+++
author = ""
date = "2014-06-05"
draft = false
image = ""
menu = ""
share = true
comments = true
slug = "caution_with_java_autoboxing"
tags = ["Java", "Programming"]
title = "Caution with Java Autoboxing"
+++

In Java **Boxing** means when you convert a primitive type to a reference type. So when you have some thing like

<pre>
boolean myBool = new Boolean("true");
</pre>

`Boolean` object is converted to primitive type `boolean`. So how this happens under the hood? Java compiler uses the 
method `booleanValue()` in `Boolean` class to get the primitive value. This is where you have to be careful; there is a possibility
of NPE when the `Boolean` object is null. 

Recently I had a NPE due to this. I was creating a POJO from mongo `DBObject`. POJO had a boolean value and I was setting
its value using a setter. 

<pre>
public void setExpirableApp(boolean expirableApp) {
        this.expirableApp = expirableApp;
}
</pre>

I was calling the setter as follows.

<pre>
app.setExpirableApp((Boolean) dbObject.get("expirable-app"))
</pre>

This is where I encounter NPE. At first glance everything looked fine. Problem happened when there is no field for `"expirable-app"`
in the DB. In that case `(Boolean) dbObject.get("expirable-app")` would return `null`. In the setter this is tried to convert to
`boolean` which throws NPE due to above explained auto-boxing. So need to be careful when handling scenarios like this.