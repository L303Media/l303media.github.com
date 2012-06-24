---
layout: post
category : lessons
tags : [intro, beginner, apache, tutorial]
---
{% include JB/setup %}

# Basic Mac OS X Apache Configuration
## Config File Locations
<ul>
  <li>Core httpd.conf located at /etc/apache2/httpd.conf</li>
  <li>SSL conf located at /etc/apache2/extra/httpd-ssl.conf</li>
  <li>Custom conf files located at /private/etc/apache2/other</li>
</ul>

## Enabling multiple domains on localhost
To enable port 80 and VirtualHosts on port 80, add the following lines to core config:
<pre>
Listen 80
NameVirtualHost *:80
...
&#60;VirtualHost *:80&#62;
    DocumentRoot /Your/Document/Root
    ServerName your.local.domain
&#60;/VirtualHost&#62;
</pre>

The asterisk in the NameVirtualHost directive instructs Apache to enable port 80 for any VirtualHost declared.

This basic VirtualHost entry is needed to provide a default DocumentRoot.  Otherwise, a later VirtualHost declaration will become the default (i.e. the VirtualHost used to service requests for domains that do not have a defined VirtualHost).  Add the following line at the end of your core configuration file:

<pre>
  Include /private/etc/apache2/other/*.conf
</pre>

This will include any custom configuration files located in the directory shown above.  It is best practice to then create separate files for any additional VirtualHosts (i.e. per project).  For example, files named /private/etc/apache2/other/project1.conf and /private/etc/apache2/other/project2.conf would contain VirtualHosts for their respective projects.  These files should contain the following, at a minimum:

<pre>
  &#60;VirtualHost *:80&#62;
   ServerName local.project1.com
   ServerAlias local.this.project1.com
   ServerAdmin webmaster@example.com

   DocumentRoot "/Project/One/Document/Root"
   DirectoryIndex index.html
   CustomLog /path/to/logs/home_access.log combined
   ErrorLog /path/to/logs/home_error.log
 &#60;/VirtualHost&#62;
</pre>

The "ServerName" directive is responsible for ensuring relevant traffic is served using this VirtualHost configuration; any requests to domains not explicitly defined in a VirtualHost will be served from the default VirtualHost defined in the core configuration.  

To configure your local machine to recognize these domains, the /etc/hosts file will need to be edited.  A line should be added to locally resolve each ServerName referenced in VirtualHost sections:
<pre>
  127.0.0.1&#160;&#160;&#160;&#160;local.project1.com
  127.0.0.1&#160;&#160;&#160;&#160;local.project2.com
  ...
</pre>
After adding these entries execute the following command from the CLI to refresh the local loopback entries:
<pre>
  dscacheutil -flushcache
</pre>