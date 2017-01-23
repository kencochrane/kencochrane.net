+++
title = "Getting WinCVS out of flat mode"
date = 2005-05-05T12:58:00Z
updated = 2005-05-05T13:04:57Z
categories = ["blog"]
topics = ["blog"]
slug = "getting-wincvs-out-of-flat-mode"
url = "blog/2005/05/getting-wincvs-out-of-flat-mode/"
+++

If you accidentally browse up to the root directory or some other level that is too large for WinCVS to initially deal with and you have the flat mode browser view toggle button of WinCVS on, WinCVS will freeze indefinitely.  If killing it in Task Manager and restarting doesn't fix your problem, you need to hack the registry.<br /> <br />Launch regedit (Start -> Run   "regedit") and browse to:<br /> <br />        HKEY_CURRENT_USER/Software/WinCvs/wincvs/CVS settings<br /> <br />In CVS settings, find the variable  P_DisplayRecursive in the right hand window.  Right-click on P_DisplayRecursive and select Modify from the right-click menu.  in the window that gets displayed, change the Value data set with only two numbers in it from the 01 to 00.  Then click Ok at the bottom of the window, shut down Registry Editor, start up WinCVS again, and you should now have the flat mode button toggled off.<br /><br />Some companies don't let their employees run regedit, but do allow employees to run .reg files. If so, put the following into a file called Unflatten.reg, run it, it should work for you:<br /><blockquote><br />Windows Registry Editor Version 5.00 /S<br />[HKEY_CURRENT_USER\Software\WinCvs\wincvs\CVS settings]<br />"P_DisplayRecursive"=hex:00 </blockquote>
