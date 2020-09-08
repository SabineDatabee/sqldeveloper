--- 
layout: post
title: Taking the short way - Favourites in SQL Developer
---

<p><b>How can you navigate <em>quickly</em> to a frequently used object?</b></p>
<img src="{{site.url}}/sqldeveloper/images/ricardo-gomez-angel-520343-unsplash_3-0e2ec18e.png" style="float: right; margin: 15px;"/>

<p>Imagine the following situation:<br>A project with a few development databases, integration databases, and production databases that you would like to check upon from time to time.<br>All of these databases are set up in the same way - same schemas, same objects. A handful of tables and views are being accessed often for maintenance and monitoring: a control table, a config table, a version control table, a few log views. They reside in different schemas.</p>
<p>Connection Tree and Schema Browser are a convenient way to find your way around the database, but for these objects the amount of scrolling and clicking can become a little annoying.</p>

## A Solution

One day I was really fed up with scrolling and clicking after every restart, so I decided to build a little 'bookmark collection' with the help of a User Defined Report.
This is still not quite what I'm dreaming of (which is <a href="https://apex.oracle.com/pls/apex/f?p=43135:7:::NO:RP,7:P7_ID:361" target="_blank">Saved Workspaces</a>) but still it saves me a lot of time every day.

To create a report, go to the reports panel, right click 'User-Defined Reports' and choose 'New Report':

<img src="{{site.url}}/sqldeveloper/images/Favoriten-Report in SQL Developer - User Defined Reports-782509d9.png" style="margin: 15px;"/>

The edit report window is then opened. You only need a name and a SQL query
(you'll find the sample code at the end):

<img src="{{site.url}}/sqldeveloper/images/Favoriten-Report in SQL Developer - Definition-fb2d3a26.png" style="margin: 15px;"/>


After saving you'll find the new report under the 'User-defined Reports' node. When clicked, the report opens in the main panel and asks which connection it should use. Then it will show all objects that the current database user is allowed to see.

I pin the opened report with the little red pin (upper left corner) to prevent it from being replaced by the next opened report. Then I move it to the Connections panel:

<img src="{{site.url}}/sqldeveloper/images/Favoriten-Report in SQL Developer - Ergebnis-de009c5b.png" style="margin: 15px;"/>


When you right-click a line the context menu offers a 'Go To' option which opens the underlying object in the Object Viewer.

If you want to switch connections you can do this via the drop-down list within the report panel. The query will be executed again - this time in the new environment - and the report shows the available objects.

## The Trick

Now... how does this work?   
The new menu option 'Go to...' will be generated every time SQL Developer finds the column aliases    
&nbsp;&nbsp;&nbsp;&nbsp;**sdev_link_owner**, **sdev_link_name** and **sdev_link_type**     
inside the report's SQL statement. The aliased columns themselves will not be shown in the report.

## SQL Code for Copy, Paste & Customise:

<pre>
select initcap(object_type) "Type",
       owner                "Schema",
       object_name          "Name",
       owner                sdev_link_owner,
       object_name          sdev_link_name,
       object_type          sdev_link_type
  from all_objects
 where (owner, object_name, object_type) 
          in ((user, 'TECH_LOG','TABLE'),
              ('LOGGER','LOGGER_LOGS_5_MIN','VIEW'),
              ('LOGGER','LOGGER_PREFS','TABLE'),
              ('LOGGER','LOGGER_LOGS_60_MIN','VIEW'),
              ('DBMT','flyway_schema_history','TABLE'),
              ('CONTROL','FLOWCONTROL','TABLE'))
    or (object_name, object_type) 
          in (('TECH_LOG','TABLE'))
 order by 2,3
 </pre>
