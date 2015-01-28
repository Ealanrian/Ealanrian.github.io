---
layout: post
title: Migrating magento
date: 2011-04-08 22:32
author: admin
comments: true
categories: [Magento, Magento, Tech, Webdevelopment, Webdevelopment]
---
This is a quick guide on how to migrate Magento.
After the break there are the steps to be taken and after that a note for virtual drives.<!--more-->

The steps:

- upload site structure to new location
- import database into new database server
- modifiy {sitefolder}/app/etc/local.xml to contain the new database info
- in the database modify:
in the table: core_config_data
- web/unsecure/base_url set the value to the new url
- web/secure/base_url set the value to the new url
- web/unsecure/base_skin_url set the path to the skin for example: 	http://localhost/{site_name}/skin/
- web/unsecure/base_media_url set the path to the skin for example: 	http://localhost/{site_name}/media/
- web/unsecure/base_js_url set the path to the skin for example: 	http://localhost/{site_name}/media/

- now clear the cache folder {site_name}/var/cache
- browse to the site
These steps will do if you have a normal environment.  If you are using an environment with virtual home drives for example a development environment you should also modify the Rewrite base in the HTaccess file
for me this one is on line 113 of the .htaccess  in new installations this line is commented out and looks like #RewriteBase /magento/ this should be replaced by RewriteBase /~{homedir_name}/{site_name}. note that this URL should also be used in the URL's set in the database. so http://localhost/{site_name} would become http://servername/~{homedir_name}/{site_name}
