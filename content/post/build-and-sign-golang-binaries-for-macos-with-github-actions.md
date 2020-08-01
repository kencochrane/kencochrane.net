+++
title = "Build, notarize, and sign Golang binaries for MacOS with GitHub Actions"
date = "2020-08-01"
tags = ["github", "github-actions", "golang", "macos", "signing"]
categories = ["blog"]
topics = ["blog"]
slug = "build-and-sign-golang-binaries-for-macos-with-github-actions"
url = "2020/08/01/build-and-sign-golang-binaries-for-macos-with-github-actions"
banner = "images/github_logo.png"
banner_thumb = "images/github_mark.png"
+++

When Apple introduced MacOS Catalina, it came with some security features to make sure you were only using trusted binaries. This requires that the binaries are signed and notarized by Apple themselves, or else you get an error.

{{< figure src="/golang-macos-sign/apple-binary-not-signed-error.png" title="MacOs Catalina error for unsigned binary" >}}

There are [ways around this error](https://www.imore.com/how-open-apps-anywhere-macos-catalina-and-mojave), but that makes it difficult if you want distribute your binaries to a large group of people. It could slow down adoption.

At work, I wrote a [Golang](https://golang.org/) binary that makes it easy for people to get temporary AWS API keys using their work [Okta](https://www.okta.com/) login. I cross compile the binary so that Windows, Linux and MacOS users can all use it.

It has worked great with very limited issues until teams started upgrading to MacOS Catalina. Once they upgraded, it would give them an error saying the binary they were using was not trusted because it wasn’t signed.

To solved this issue, I did some research and got the binaries signed. But this was a manual process, and because I’m lazy, I wanted to automate this step so that I didn’t have to do this by hand every time a new release came out.

To automate this process I used GitHub actions, and it worked really well, so I’m going to share with you how I did it, with a [simple example golang app](https://github.com/kencochrane/golang-github-action-sign-macos).

This requires that you have an [Apple Developer account](https://developer.apple.com/), so if you don’t have one yet, you will need to get one.

## Getting started
In order to show you how this works, I created a [simple golang app and put all my code in a github repo](https://github.com/kencochrane/golang-github-action-sign-macos). Feel free to check it out to see the end result.

To help with the building of the Golang binary I'm using a `Makefile` and a few helper scripts and doing the build in a Docker container to keep everything nice and reproducible. The other benefit of doing it this way, is that you can build the binaries like normal on your local machine, and use the same code to build in the github action. If you did all building in github actions you might have to have two different ways to build your code, and that could lead to drifting issues in the future if the build process changed at all.

There are a few scripts that I created to make everything easier:

 - `build.sh` which will create a docker container and call `build_app.sh` which will call `make build` and that will compile and create three different binaries one for Mac, Linux, and Windows, and put them in a `/release` directory.
 - `sign.sh` This is a wrapper script that will call `make sign` which will kick off the binary signing process for MacOS using [Gon](https://github.com/mitchellh/gon).

### Gon
To make the signing process much easier I'm using [Gon](https://github.com/mitchellh/gon) written by [Mitchell Hashimoto](https://github.com/mitchellh) who is the founder at [Hashicorp](https://www.hashicorp.com/) (Terraform, Vagrant, etc). Gon is what they use to sign Terraform, etc. If it is good enough for Terraform, it is good enough for me.

#### Gon Prerequisite: Acquiring a Developer ID Certificate

Before using `gon`, you must acquire an Apple Developer ID Certificate. To do
this, you can either do it via the web or via Xcode locally on a Mac. Using
Xcode is easier if you already have it installed.

Via the web:

  1. Sign into [developer.apple.com](https://developer.apple.com) with valid
     Apple ID credentials. You may need to sign up for an Apple developer account.

  2. Navigate to the [certificates](https://developer.apple.com/account/resources/certificates/list)
     page.

  3. Click the "+" icon, select "Developer ID Application" and follow the steps.

  4. After downloading the certificate, double-click to import it into your
     keychain. If you're building on a CI machine, every CI machine must have
     this certificate in their keychain.

Via Xcode:

  1. Open Xcode and go to Xcode => Preferences => Accounts

  2. Click the "+" in the bottom left and add your Apple ID if you haven't already.

  3. Select your Apple account and click "Manage Certificates" in the bottom
     right corner.

  4. Click "+" in the bottom left corner and click "Developer ID Application".

  5. Right-click the newly created cert in the list, click "export" and
     export the file as a p12-formatted certificate. _Save this somewhere_.
     You'll never be able to download it again.

To verify you did this correctly, you can inspect your keychain:

```sh
$ security find-identity -v
  1) 4194587FE60D93D416CF3F4669FF913C7BBA4271 "Developer ID Application: Your Name (GK80BB2A7)"
     1 valid identities found
```

#### More notes on getting an Apple Developer Certificate

In order to get an Apple developer certificate I did the following.

1. Created the CSR using the Mac keychain app, and stored it locally on my machine.
  - [How to create a CSR:](https://help.apple.com/developer-account/#/devbfa00fef7)
2. In order to create a developer certificate you need to be an account holder. If you are not an account holder, you will need to find out who is, and ask them to help. I sent them by CSR, but I'm not sure if that is actually needed or not, but figured I would leave that step here for future Ken incase I ever need it.
3. In order to sign and notarize you need to have an app specific password (regular passwords that require 2FA will fail with the error "We are unable to create an authentication session. (-22016)")
4. Take your Apple Developer username and App Specific password and add them as a secret in your Github repo, and if you want to do signing and notarizing locally, you will also need to set the password in your ENV under `AC_PASSWORD`.
  - `AC_USERNAME`
  - `AC_PASSWORD`


#### Exporting the Developer Certificate
Installing the developer certificate on your local machine is good for local builds, but we want to do this in a github action, so we need to get the certificate in a format that will allow us to import it in our build machine.

1. Export the Certificate from the keychain [more details here](https://fluffy.es/keychain-cant-export-to-p12/)
- Click on "My Certificates" on left side, select the "Developer ID Application: ..." Certificate
- Click on arrow next to the name so that it shows the private key as well.
- Highlight both rows, and right click "export 2 items".
- Give it a Name (Certificate.p12), and then a good password. Your new p12 certificate will be in the location where you specified.
- Keep track of that password, it is needed for the github action (as a secret)
2. Copy the certificate in base64 format, and store as a github secret on the repo

  ``` bash
  cat Certificate.p12 | base64 | pbcopy
  ```

3. Go into your github repo and set the github secrets for the certificate and the password
- `APPLE_DEVELOPER_CERTIFICATE_P12_BASE64`
- `APPLE_DEVELOPER_CERTIFICATE_PASSWORD`

Now that you have the certificate and password as a github secret, the github action can use them to build and sign the binaries.

#### Gon Configuration

Here is the configuration we are going to use. For details on the config, go read the [gon documentation](https://github.com/mitchellh/gon/blob/master/README.md) This depends on two ENV variables. `AC_USERNAME` and `AC_PASSWORD`.

**Note**: If you try and use `@env:AC_USERNAME` in the apple_id section it won't work. Either hard code it, or leave it out and add as an ENV variable, the `@env` function doesn't work for username.

```
{
    "source" : ["./release/darwin/hello-github"],
    "bundle_id" : "com.kencochrane.hello-github",
    "apple_id": {
        "password":  "@env:AC_PASSWORD"
    },
    "sign" :{
        "application_identity" : "4194587FE60D93D416CF3F4669FF913C7BBA4271"
    },
    "dmg" :{
        "output_path":  "./release/darwin/hello-github.dmg",
        "volume_name":  "hello-github"
    },
    "zip" :{
        "output_path" : "./release/darwin/hello-github.zip"
    }
}
```

## Github Action
Now that we have everything in place, we can create a github action that will trigger when we push a new tag to our repo. That will build our binaries on a Linux Runner, and then sign and notarize on a MacOS Runner. I am building on Linux and signing on MacOS vs doing it all on MacOS because the MacOS Runner doesn't come with Docker installed and because of this you can't run Docker containers. It also lets me show you how to use the upload and download artifact feature that might become useful to you sometime in the future.

### Build
Here is our [github action workflow file](https://github.com/kencochrane/golang-github-action-sign-macos/blob/master/.github/workflows/release.yml). The first build step is pretty simple, it will checkout the code, and then run the `build.sh` script to build the binaries, then it will upload the artifacts so that they are available in the next step. It does this in an `ubuntu-latest` hosted runner.
```
build:
    name: Build the binaries
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Build
        run: |
          ./build.sh
      - name: upload binary artifacts
        uses: actions/upload-artifact@v2
        with:
          name: binaries
          path: release/
```

### Release
This step is a little more complicated, it will run on the `macos-latest` hosted runner.

It does the following.
1. checkout the code
2. download compiled binaries from the previous step
3. Import the developer certificates from the github secrets
4. Install gon via homebrew
5. signs the mac binaries using gon, referencing the ENV variables for username and password.
6. Zip up the release directory with all the binaries
7. create a github release
8. attach the release binaries to the github release, so they are all together.

If successful you will have a new github release with your signed and notarized binaries when ever you tag your code and push to github.

```
release:
    needs: build
    runs-on: macos-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v2

    - name: Download-Binaries
      uses: actions/download-artifact@v2
      with:
        name: binaries
        path: release/

    - name: Import Code-Signing Certificates
      uses: Apple-Actions/import-codesign-certs@v1
      with:
        # The certificates in a PKCS12 file encoded as a base64 string
        p12-file-base64: ${{ secrets.APPLE_DEVELOPER_CERTIFICATE_P12_BASE64 }}
        # The password used to import the PKCS12 file.
        p12-password: ${{ secrets.APPLE_DEVELOPER_CERTIFICATE_PASSWORD }}

    - name: Install gon via HomeBrew for code signing and app notarization
      run: |
        brew tap mitchellh/gon
        brew install mitchellh/gon/gon

    - name: Sign the mac binaries with Gon
      env:
        AC_USERNAME: ${{ secrets.AC_USERNAME }}
        AC_PASSWORD: ${{ secrets.AC_PASSWORD }}
      run: |
        ./sign.sh

    - name: Zip up release directory
      run: |
        zip -r release-binaries.zip release/

    - name: Create Release
      id: create_release
      uses: actions/create-release@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        tag_name: ${{ github.ref }}
        release_name: Release ${{ github.ref }}
        draft: false
        prerelease: false

    - name: Upload release binaries Zip
      id: upload-release-asset
      uses: actions/upload-release-asset@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        upload_url: ${{ steps.create_release.outputs.upload_url }}
        asset_path: release-binaries.zip
        asset_name: release-binaries.zip
        asset_content_type: application/zip
```

## Conclusion
Hopefully this blog post along with the accompanying [GitHub repo](https://github.com/kencochrane/golang-github-action-sign-macos/) will make it easier for you to use Github Actions to build your Golang binaries and then sign and notarize them for MacOS, so that your users don't get any security warnings.

If this is a topic that you find interesting and you are looking for a job, [please reach out](https://www.linkedin.com/in/kencochrane/), I'm always looking to hire smart people who want to solve interesting problems. We hire fully remote folks, so if you don't live near one of our offices around the world, you can work from home, no problem with us.
