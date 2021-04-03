## Prerequisites

1.  An Ubuntu 16.04 server and root user with full privileges. 
2.  A LAMP stack (Linux, Apache, MySQL, PHP) on Ubuntu 16.04. 
3. A Redis Server. 

###### STEPS TO FOLLOW :
1. Logging in as root user

First of all, Please make sure that you have a root user and SSH installed in your system and login to SSH from Termainal using bellow command
```
  ssh root@your_server_ip 
Eg: ssh root@192.168.151.88
```

2. How To Install Linux, Apache, MySQL, PHP (LAMP) stack on Ubuntu 16.04

Step 1 — Installing Apache 
       
	 sudo apt update
	 sudo apt install apache2
	 
Step 2 — Installing MySQL

	sudo apt install mysql-server
	
Step 3 — Installing PHP

	sudo apt install php libapache2-mod-php php-mysql
	
C. How To Install and Secure Redis on Ubuntu 16.04 

Step 1 — Installing and Configuring Redis

	sudo apt update
	sudo apt install redis-server
	
To test that Redis is functioning correctly, connect to the server using redis-cli, 
Redis’s command-line client:

	redis-cli
	
In the prompt that follows, test connectivity with the ping command:

	127.0.0.1:6379> ping
	
it show output pong ,This output confirms that the server connection is active

Step 2 — Configuring a Redis Password

	sudo nano /etc/redis/redis.conf
	
Scroll to the SECURITY section and look for a commented directive that reads:
```
. . .
# requirepass foobared
. . .
```

Uncomment it by removing the #, and change foobared to a secure password:
After setting the password, save and close the file, then restart Redis:

	sudo systemctl restart redis.service

Step 3 — Installing the Redis Library for PHP

	sudo apt update
	sudo apt install -y php-redis
	
Next, restart the Apache server to load the php-redis library:

	sudo systemctl restart apache2
	
Step 4 — Building a PHP Web Resource for Rate Limiting

In this step, you need to copy the "test.php"  file into the root directory (/var/www/html/) of your web server. 
This file will be accessible to the public and users can type its address in a web browser to run it. 
Later you can test  the resource using the curl command.

To begin, open the /var/www/html/test.php file:

	sudo nano /var/www/html/test.php
	
Next, Remember to enter the appropriate value for REDIS_PASSWORD you created before
```
<?php

$redis = new Redis();
$redis->connect('127.0.0.1', 6379);
$redis->auth('REDIS_PASSWORD');

?>
```

When you’ve finished editing the /var/www/html/test.php file, save and close it.

Step 5 — Testing Redis Rate Limiting

In this step, you’ll use the curl command to request the web resource.
 To fully check the script, you’ll request the resource five times in a single command.
 It is possible to do this by including a placeholder URL parameter at the end of the test.php file. Here, you use the value ?[1-5] at the end of your request to execute the curl commands five times.
 
 Now run the below command from Terminal :
 ```
 curl -H "Accept: text/plain" -H "Content-Type: text/plain" -X GET http://localhost/test.php?[1-5]
```

After running the code, you will receive output similar to the following:
```
[1/5]: http://localhost/test.php?1 --> <stdout>
--_curl_--http://localhost/test.php?1
Welcome 127.0.0.1 total calls made 1 in 10 seconds
[2/5]: http://localhost/test.php?2 --> <stdout>
--_curl_--http://localhost/test.php?2
Welcome 127.0.0.1 total calls made 2 in 10 seconds
[3/5]: http://localhost/test.php?3 --> <stdout>
--_curl_--http://localhost/test.php?3
Welcome 127.0.0.1 total calls made 3 in 10 seconds
[4/5]: http://localhost/test.php?4 --> <stdout>
--_curl_--http://localhost/test.php?4
User 127.0.0.1 limit exceeded.
[5/5]: http://localhost/test.php?5 --> <stdout>
--_curl_--http://localhost/test.php?5
User 127.0.0.1 limit exceeded.

```	

## Conclusion

As you’ll note, the first three requests ran without a problem. However, your script has rate limited the fourth and fifth requests. This confirms that the Redis server is rate limiting users’ requests.
This tutorial implemented a PHP script for rate limiting with Redis on an Ubuntu 20.04 server to prevent your web application from malicious overuse. 




