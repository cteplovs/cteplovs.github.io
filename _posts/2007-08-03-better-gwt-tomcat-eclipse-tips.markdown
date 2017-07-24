---
author: christeplovs
comments: true
date: 2007-08-03 04:24:00+00:00
layout: post
link: https://christeplovs.wordpress.com/2007/08/03/better-gwt-tomcat-eclipse-tips/
slug: better-gwt-tomcat-eclipse-tips
title: Better GWT-Tomcat-Eclipse tips
wordpress_id: 30
---

See [Xebia's blog entry](http://blog.xebia.com/2006/08/17/using-google-web-toolkit-with-eclipse-wtp/) (reproduced here, in case it goes away):  
  
  
Using Google Web Toolkit with Eclipse WTP  
Posted by Marco Mulder in the late afternoon: August 17, 2006  
  
I am writing a GWT application and want to test this application both in the GWT shell and in Tomcat. The application uses different Eclipse projects for the GWT UI and for the business service layer.  
  
I found out that this can easily be done if you use an Eclipse WTP dynamic web project instead of the standard Java project that is created by the GWT projectCreator.  
  
Using a dynamic web project has the following advantages:  
  
 * It is possible to use external jars and other Eclipse projects from services that are implemented with the RemoteServiceServlet.  
 * It is easy to test the 'compiled' JavaScript version in tomcat without having to create and deploy a war file.  
 * It is easy to export a war file that can be deployed in any application server.  
  
There is an Eclipse plugin for using GWT with WTP: Googlipse . However, this plugin does not (yet) support launching the 'compiled' JavaScript application. I will outline the steps that allow you to launch the JavaScript version of a GWT application in WTP.  
  
How to create a GWT application in a dynamic web project  
  
The recipe to create a GWT application in a dynamic web project is as follows:  
  
 * Create a dynamic Web project MyProject in Eclipse WTP  
 * Use GWT projectCreator without overwriting the .project and .classpath files:projectCreator.cmd -ignore -eclipse MyProject  
 * Use GWT applicationCreator to create a GWT application:applicationCreator.cmd -eclipse MyProject com.mycompany.client.MyApplication  
 * Add gwt-user.jar and junit.jar to the .classpath file:  
     
     
 * Search and replace "www" to "WebContent" in MyApplication.launch, MyApplication-compile.cmd and MyApplication-shell.cmd  
 * Copy gwt-servlet.jar to WebContent/WEB-INF/lib  
  
Testing the application  
  
You can use the generated launch configuration to test the application from the GWT shell. The WTP tooling ensures that libraries that are used by the web project will be on the classpath.  
  
To test the 'compiled' JavaScript application:  
  
 * Compile Java source into JavaScript using MyApplication-compile.cmd  
 * Select "Run as --> Run on Server" from the context menu of MyProject  
 * Open a browser on the URL of the application:  
   http://localhost:8080/MyProject/com.mycompany.MyApplication/MyApplication.html  
  
Implementing and invoking a service  
  
To invoke a service in a way that works both in the GWT shell and in the ‘compiled’ JavaScript application, you have to use GWT.getModuleBaseURL() when setting the endpoint.  
  
For example, if you implement a service “myservice” in package com.mycompany.server.MyServiceImpl:  
  
 * Use GWT.getModuleBaseURL() in the serviceEntryPoint:endpoint.setServiceEntryPoint(GWT.getModuleBaseURL() + "myservice");  
 * Specify the full path in MyApplication.gwt.xml:  
   /com.mycompany.MyApplication/myservice" class="com.mycompany.server.MyServiceImpl">  
 * Specify the path also in WebContent/WEB-INF/web.xml:  
`  
  
 MyService  
 com.mycompany.server.MyServiceImpl  
  
  
 MyService  
 /com.mycompany.MyApplication/myservice  
  
`
