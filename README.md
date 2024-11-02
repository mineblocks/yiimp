##  Supports merged mining! Supports simultaneous payment of pow and multiple auxpow coins!

#How to install Yiimp? Use the Yiimp installation script.

- https://github.com/msy2008/yiimp_install_scrypt

#The stratum code built into Yiimp will not be continuously developed. Do you plan to reinstall Yiimp after improving the stratum code? Please use the independently developed stratum code.

- https://github.com/msy2008/stratum-full

## This Yiimp code is already able to support automatic payment with AUXPOW coins!, you need to create a table file in the database

- Enter MariaDB database
```
mysql
```
- List all databases
```
SHOW DATABASES;
```
- Use yiimpfrontend database
```
USE yiimpfrontend;
```
- Show all tables in yiimpfrontend database
```
SHOW TABLES;
```
- Query the accounts table structure
```
DESCRIBE accounts;
```
- Query all data stored in the accounts table
```
SELECT * FROM accounts;
```
- Create a new table with the same structure as the accounts table (assuming the new table is named accountsdogm):
```
CREATE TABLE accountsdogm LIKE accounts;
```
- Create a new record in the accountsdogm table and specify id as 1, coinid as 1426, username as D9U6r6eZaxJ4iDWipuHy4ws7pzKnd797D7, and coinsymbol as an empty string.
- Please enter the data according to the actual situation of your mining pool！
```
INSERT INTO accountsdogm (id, coinid, username, coinsymbol) VALUES (1, 1426, 'D9U6r6eZaxJ4iDWipuHy4ws7pzKnd797D7', '');
```
- Query all data stored in the accountsdogm table
```
SELECT * FROM accountsdogm;
```
- Update the username field value of the record with id 1 in the accountsdogm table to DHLtLzrowM63oURS1Z7Lw3YGbypBDNakVy
- Please update the data according to the actual situation of your mining pool！
```
UPDATE accountsdogm SET username = 'DHLtLzrowM63oURS1Z7Lw3YGbypBDNakVy' WHERE id = 1;
```
## In the accountsdogm and accounts table, the id field is the UID, you should associate the miners UID!

***********************************
## Old and I've never used it!
#yiimp - yaamp fork

WARNINGS
- Use at your own risks.
- Usage of this software requires abilities with sysadmin, database admin, coin daemons, and sometimes a bit of programming. Running a production pool can literally be more work than a full-time job.

Required:

	linux, mysql, php, memcached, a webserver (lighttpd or nginx recommended)


Config for nginx:

	location / {
		try_files $uri @rewrite;
	}

	location @rewrite {
		rewrite ^/(.*)$ /index.php?r=$1;
	}

	location ~ \.php$ {
		fastcgi_pass unix:/var/run/php5-fpm.sock;
		fastcgi_index index.php;
		include fastcgi_params;
	}


If you use apache, it should be something like that (already set in web/.htaccess):

	RewriteEngine on

	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^(.*) index.php?r=$1 [QSA]


If you use lighttpd, use the following config:

	$HTTP["host"] =~ "yiimp.ccminer.org" {
	        server.document-root = "/var/yaamp/web"
	        url.rewrite-if-not-file = (
			"^(.*)/([0-9]+)$" => "index.php?r=$1&id=$2",
			"^(.*)\?(.*)" => "index.php?r=$1&$2",
	                "^(.*)" => "index.php?r=$1",
	                "." => "index.php"
	        )

		url.access-deny = ( "~", ".dat", ".log" )
	}


For the database, import the initial dump present in the sql/ folder

Then, apply the migration scripts to be in sync with the current git, they are sorted by date of change.

Your database need at least 2 users, one for the web site (php) and one for the stratum connections (password set in config/algo.conf).



The recommended install folder for the stratum engine is /var/stratum. Copy all the .conf files, run.sh, the stratum binary and the blocknotify binary to this folder. 

Some scripts are expecting the web folder to be /var/web. You can use directory symlinks...


Add your exchange API public and secret keys in these two separated files:

	/etc/yiimp/keys.php - fixed path in code
	web/serverconfig.php - use sample as base...

You can find sample config files in web/serverconfig.sample.php and web/keys.sample.php

This web application includes some command line tools, add bin/ folder to your path and type "yiimp" to list them, "yiimp checkup" can help to test your initial setup.
Future scripts and maybe the "cron" jobs will then use this yiic console interface.

You need at least three backend shells (in screen) running these scripts:

	web/main.sh
	web/loop2.sh
	web/block.sh

Start one stratum per algo using the run.sh script with the algo as parameter. For example, for x11:

	run.sh x11

Edit each .conf file with proper values.

Look at rc.local, it starts all three backend shells and all stratum processes. Copy it to the /etc folder so that all screen shells are started at boot up.

All your coin's config files need to blocknotify their corresponding stratum using something like:

	blocknotify=blocknotify yaamp.com:port coinid %s

On the website, go to http://server.com/site/adminRights to login as admin. You have to change it to something different in the code (web/yaamp/modules/site/SiteController.php). A real admin login may be added later, but you can setup a password authentification with your web server, sample for lighttpd:

	htpasswd -c /etc/yiimp/admin.htpasswd <adminuser>

and in the lighttpd config file:

	# Admin access
	$HTTP["url"] =~ "^/site/adminRights" {
	        auth.backend = "htpasswd"
	        auth.backend.htpasswd.userfile = "/etc/yiimp/admin.htpasswd"
	        auth.require = (
	                "/" => (
	                        "method" => "basic",
	                        "realm" => "Yiimp Administration",
	                        "require" => "valid-user"
	                )
	        )
	}

And finally remove the IP filter check in SiteController.php



There are logs generated in the /var/stratum folder and /var/log/stratum/debug.log for the php log.

More instructions coming as needed.


There a lot of unused code in the php branch. Lot come from other projects I worked on and I've been lazy to clean it up before to integrate it to yaamp. It's mostly based on the Yii framework which implements a lightweight MVC.

	http://www.yiiframework.com/


Credits:

Thanks to globalzon to have released the initial Yaamp source code.

--

Thanks to tpruvot to have released the initial Yiimp source code.
