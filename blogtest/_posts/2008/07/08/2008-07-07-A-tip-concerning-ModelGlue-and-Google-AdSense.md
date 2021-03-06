---
layout: post
title: "A tip concerning Model-Glue and Google AdSense"
date: "2008-07-08T07:07:00+06:00"
categories: [coldfusion]
tags: []
banner_image: 
permalink: /2008/07/08/A-tip-concerning-ModelGlue-and-Google-AdSense
guid: 2921
---

Here is something I've run into with Model-Glue and AdSense. I'd bet it would also apply to other frameworks and similar advertising engines.
<!--more-->
A typical web site may include Google AdSense code on it's public facing pages. Imagine one such page, an Entry View, with a URL something like so:

http://foo/index.cfm?event=entry&id=5

When you view this page in your browser, AdSense's embedded JavaScript code will actually <i>re-request</i> your page again. This is how it handles the contextual ads. 

Now imagine your page has a comment box where users can leave feedback. You ask for a name, some feedback, and a captcha challenge. 

Your back end code is going to verity these inputs, and if things fail, add a result called Invalid. Your XML for the AddComment code would then perhaps have something like so:

<code>
&lt;result name="invalid" do="Entry" redirect="true"&gt;
</code>

That's all you need really. If your comment form included the ID value of the entry, when Model-Glue sends you along to the Entry event again, it will pass along all of the previous values from the event. So to Model-Glue, the ID value is still there.

But if you look in your browser, you will see something like:

http://foo/index.cfm?event=entry

Ok so the point of all this is is to point out that at <i>this</i> point now, AdSense will attempt to create the contextual ads by requesting the URL it sees - which in this case is just the event value, no ID. So while everything worked fine and dandy for you personally, AdSense is going to request an event without the proper ID value. 

The fix for this is simple enough - just append ID:

<code>
&lt;result name="invalid" do="entry" redirect="true" append="id"&gt;
</code>

I bring this up because when I first ran into it - I couldn't understand why I wasn't seeing errors myself, but the error emails would flood in. What I didn't get was that the emails were being generated by the AdSense request, not my own request.