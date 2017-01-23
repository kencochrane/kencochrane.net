+++
title = "iText HTMLWorker Style guide"
date = 2008-03-09T17:40:00Z
updated = 2008-03-09T17:55:03Z
tags = ["Java", "HTMLWorker", "Style", "iText", "PDF"]
categories = ["blog"]
topics = ["blog"]
slug = "itext-htmlworker-style-guide"
url = "blog/2008/03/itext-htmlworker-style-guide/"
+++

iText provides a way to convert HTML snippets to PDF using the HTMLWorker class. Unfortunately there isn't much documentation on what you can do for styles. In order to figure this out I went through the source code. I'm putting it here so that I won't have to do that again, and so that others will be able to gain something from my pain.<br /><br />This is from iText 2.0.8<br /><br />HTMLWorker supported tags <blockquote>"ol ul li a pre font span br p div body table td th tr i b u sub sup em strong s strike h1 h2 h3 h4 h5 h6 img"</blockquote><br /><table border="1"><br /><tbody><tr><th>Tag</th><th>Attribute [iText methods]</th><th>Values</th></tr><br /><br /><tr><td>p</td><td>align [setAlignment()]</td><td>center, right, justify</td></tr><br /><tr><td>p</td><td>leading [setLeading()]</td><td>float or float, float</td></tr><br /><tr><td>p</td><td>before [setSpacingBefore()]</td><td>float</td></tr><br /><tr><td>p</td><td>after [setSpacingAfter()]</td><td>float</td></tr><br /><tr><td>p</td><td>extraparaspace [setExtraParagraphSpace()]</td><td>float</td></tr><br /><br /></tbody></table><br /><br />If you find any more let me know and I'll add them to the list.<br /><br />Ken
