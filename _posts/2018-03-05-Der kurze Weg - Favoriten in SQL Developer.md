--- 
layout: post
title: Der kurze Weg - Favoriten in SQL Developer
---

## Das Problem
<p>Wie kommt man im SQL Developer <em>schnell</em> zu einem bestimmten, oft verwendeten Objekt?</p>
<img src="{{site.url}}/images/ricardo-gomez-angel-520343-unsplash_3-0e2ec18e.png" style="float: right; margin: 15px;"/>

<p>Stellen Sie sich zum Beispiel Folgendes vor:<br>Ein Projekt mit mit diversen Entwicklerdatenbanken, Integrationsdatenbanken und Produktivdatenbanken, auf denen man ab und zu nach dem Rechten sehen will. Alle Datenbanken haben dieselbe Struktur, und es gibt eine Handvoll Tabellen, auf die man immer wieder zugreifen muss, zum Beispiel: eine Steuerungstabelle, eine Konfigurationstabelle, eine Versionskontrolltabelle, mehrere Log-Views. Diese Objekte sind über mehrere Schemas verteilt.</p>
<p>Der Connection Tree und der Schema Browser sind normalerweise ausreichend, um in der Datenbank zu navigieren, aber gerade für häufig besuchte Objekte summieren sich die Klicks, bis alle Fenster wieder so beisammen hat, wie man sie braucht.</p>

## Eine Lösung

Ich war es irgendwann leid, nach jedem Neustart im Connection Tree herumzuscrollen, und habe mir eine kleine "Bookmarksammlung" gebaut. Hierfür kann man einen Benutzerdefinierten Bericht verwenden, mit dessen Hilfe man einzelne Objekte direkt anspringen kann.     
Das kommt zwar noch nicht an meine Idealvorstellung von <a href="https://apex.oracle.com/pls/apex/f?p=43135:7:::NO:RP,7:P7_ID:361" target="_blank">abgespeicherten Workspaces</a> heran, spart mir aber bei der täglichen Arbeit schon jede Menge Zeit.    

Zum Anlegen eines Benutzerdefinierten Berichts klickt man rechts auf 'User-Defined Reports' im Reports-Fenster und wählt 'New Report' aus:

<img src="{{site.url}}/images/Favoriten-Report in SQL Developer - User Defined Reports-782509d9.png" style="margin: 15px;"/>


Das Definitionsfenster öffnet sich. Die Minimalangaben, die der neue Bericht benötigt, sind ein Name und eine SQL-Abfrage       
(Beispielcode am Ende der Seite):

<img src="{{site.url}}/images/Favoriten-Report in SQL Developer - Definition-fb2d3a26.png" style="margin: 15px;"/>


Nach dem Abspeichern findet sich der neue Bericht unter 'User-defined Reports'. Wie jeder Bericht öffnet er sich im Hauptfenster durch einen einfachen Klick und fragt dann zunächst ab, über welche Connection er die Abfrage starten soll.    
Je nach gewählter Umgebung werden alle Objekte angezeigt, auf die man mit der jeweiligen Connection Zugriff hat.    
    
Den geöffneten Bericht fixiere ich mit dem kleinen roten Pin (links oben), damit der Fensterinhalt nicht beim Öffnen eines neuen Berichts ersetzt wird. Dann verschiebe ich ihn ins selbe Panel wie den Connection Tree:

<img src="{{site.url}}/images/Favoriten-Report in SQL Developer - Ergebnis-de009c5b.png" style="margin: 15px;"/>

Über einen Rechtsklick auf eine Zeile bekommt man die Option 'Go To' angeboten, die das Objekt direkt im Object Viewer öffnet.    

Will man die Umgebung wechseln, kann man dies im Bericht selbst über die Drop-Down-Liste mit den Connections tun. Die Abfrage wird dann erneut ausgeführt und bietet einem die dort verfügbaren Objekte an.

## Der Trick

Wie funktioniert das Ganze nun?    
Der zusätzliche Menüpunkt 'Go to...' wird genau dann generiert, wenn die Spaltenaliase    
&nbsp;&nbsp;&nbsp;&nbsp;**sdev_link_owner**, **sdev_link_name** und **sdev_link_type**     
im Statement verwendet werden. Die Spalten mit diesen Aliasen werden selbst nicht im Report angezeigt.

## Code für den Report zum Kopieren und Anpassen:

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
 
