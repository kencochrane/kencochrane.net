This is the source code for my website kencochrane.net, it uses Hugo to build the site, and is deployed via github webhook to netlify.

## Running site locally
$ hugo server
Go to http://localhost:1313/ in browser

## deploying new version.
1. make commit to master
2. push to github
3. github webhook will notify netlify
4. netlify will build and update site.

## make new post
create a new file in `content/post`, add metadata to the top of the page. Write post. preview with local build, if it looks good, commit and push to github to release.
