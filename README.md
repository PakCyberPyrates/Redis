Installing Redis and Upgrading to PHP 7.0

First let’s update the package manager, we’re using CentOS 7 so the package manager will be yum:

# yum update

Then install Redis:

# yum install redis

Type in the following command to update the packages in DirectAdmin’s custombuild directory:

# cd /usr/local/directadmin/custombuild

# ./build update

Then type in the following commands to change the current version of PHP to 7.0 and build it:

# ./build set php1_release 7.0

# ./build php d

Grab a cup of coffee because the build is going to take a long time.

Installing Redis API for PHP with igbinary Support

After DirectAdmin finishes building PHP 7.0, we’re going to install igbinary which is a replacement for the standard php serializer, it stores php data structures in a compact binary format therefore reducing the storage requirement by half:

# pecl install igbinary igbinary-devel

Run the following command to install the Redis PHP extension:

# pecl install redis

When the installer asks you if you want to enable igbinary support, type yes then press enter.

Then wait for the installer to finish and after that you need to load the extensions we just installed in PHP 7, do that by opening the /usr/local/php70/lib/php.conf.d/10-directadmin.ini file:

# nano /usr/local/php70/lib/php.conf.d/10-directadmin.ini

Now look for the path in the extension_dir variable this is where pecl should have installed both igbinary and the Redis PHP extension.

In our case the path is /usr/local/php70/lib/php/extensions/no-debug-non-zts-20151012, add two lines in the 10-directadmin.ini file just under extension_dir:

extension=/usr/local/php70/lib/php/extensions/no-debug-non-zts-20151012/redis.so

extension=/usr/local/php70/lib/php/extensions/no-debug-non-zts-20151012/igbinary.so

Our 10-directadmin.ini file looks something like this:

extension_dir=/usr/local/php70/lib/php/extensions/no-debug-non-zts-20151012

extension=/usr/local/php70/lib/php/extensions/no-debug-non-zts-20151012/redis.so


extension=/usr/local/php70/lib/php/extensions/no-debug-non-zts-20151012/igbinary.so


zend_extension=/usr/local/php70/lib/php/extensions/no-debug-non-zts-20151012/opcache.so

opcache.memory_consumption=128

opcache.interned_strings_buffer=8

opcache.max_accelerated_files=4000

opcache.revalidate_freq=60

opcache.fast_shutdown=1

opcache.enable_cli=1

opcache.validate_permission=1


Yours should look similar if you’re doing this tutorial on a clean DirectAdmin installation.
Restart Apache now to enforce the changes we just made:

# systemctl restart httpd

Now you should have Redis installed with igbinary and PHP 7.0 on your DirectAdmin VPS.

Testing Redis PHP with WordPress

If you have a WordPress installation in one or more of your DirectAdmin domains you can also test Redis by installing the W3TC(W3 Total Cache) plugin and setting the Object Cache to Redis.

Then just run the following command and visit a page on your WordPress site:

# redis-cli MONITOR

If the output is similar to this, you’ve configured WordPress to use your freshly installed Redis cache system correctly:

1505419577.020898 [0 127.0.0.1:44546] "GET" "w3tc_directadmin.test_0_object_0userloginstest"

1505419577.021066 [0 127.0.0.1:44546] "GET" "w3tc_directadmin.test_0_object_0users1"

1505419577.021222 [0 127.0.0.1:44546] "GET" "w3tc_directadmin.test_0_object_0user_meta1"

1505419577.021416 [0 127.0.0.1:44546] "GET" "w3tc_directadmin.test_0_object_0optionsauth_key"

1505419577.021801 [0 127.0.0.1:44546] "SETEX" "w3tc_directadmin.test_0_object_0optionsauth_key" "180" "a:2:{s:7:\"content\";s:64:\"jTA3D|7HW8EijHDO9l|$#.>RO<%!zuL{+XL#m.@gNyg;;!(}s@(]/c%:l,+,4k[d\";s:11:\"key_version\";i:1;}"

 

Installing PHP 7 and Redis on a DirectAdmin Virtual Server is an easy task – just follow this step by step tutorial. If you for some reason get in trouble with installing PHP 7 and Redis on a DirectAdmin VPS, please post a comment bellow and we will be happy to answer it.

P.S. Feel free to share this blog post if you liked it by using the social network shortcuts – you can also leave a comment instead, found under the share buttons.
