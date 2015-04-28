# Introduction #

First and foremost, you'll need to download and install the latest [Google Web Toolkit](http://code.google.com/webtoolkit/).

Next, you'll want to download [gwt-oauth2-0.1-alpha.jar](https://code.google.com/p/gwt-oauth2/downloads/detail?name=gwt-oauth2-0.1-alpha.jar), add it to the classpath for your GWT project, and add the following line to your Module.gwt.xml:

```
<inherits name="com.google.api.gwt.oauth2.OAuth2" />
```

And that's it!

Now that you've installed the library into your GWT application, you're ready to start [writing code to allow users to grant access to their data](GettingStarted.md).