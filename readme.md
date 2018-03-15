# Magento 2.2.2 Install Guide For Centmin Mod Nginx LEMP Stacks

## Table Of Contents

* [Introduction](https://github.com/centminmod/centminmod-magento2#introduction)
* [Centmin Mod 123.09beta01 LEMP Stack Install](https://github.com/centminmod/centminmod-magento2#centmin-mod-12309beta01-lemp-stack-install)
* [Magento 2.2.2 Installation](https://github.com/centminmod/centminmod-magento2#magento-222-installation)
* [Magento 2 Upgrade](https://github.com/centminmod/centminmod-magento2#magento-2-upgrade)
* [Magento 2 Page Speed Tests](https://github.com/centminmod/centminmod-magento2#magento-2-page-speed-tests)
* [Magento 2 Redis Benchmarks](https://github.com/centminmod/centminmod-magento2#magento-2-redis-benchmarks)
  * [Magento 2 File Based Caching Benchmarks](https://github.com/centminmod/centminmod-magento2#benchmarks-with-redis-caching-disabled)
* [Magento 2 Varnish Cache Config & Benchmarks](https://github.com/centminmod/centminmod-magento2#magento-2-varnish-cache)
* [Magento Docs & Info Links](https://github.com/centminmod/centminmod-magento2#magento-docs--info-links)
* [Magento 2 Bugs](https://github.com/centminmod/centminmod-magento2#magento-2-bugs)

## Introduction

This Magento 2.2.2 installation guide is written by George Liu (eva2000) and is **provided as is without any support** and was written specifically for Magento 2.2.2 installs on [Centmin Mod 123.09beta01](https://centminmod.com/) or higher branch LEMP stack installations running Nginx 1.13 branch with PHP-FPM 7.1.x latest (Magento 2.2.2 doesn't support PHP 7.2 yet) and MariaDB 10.1.31 MySQL server. Magento 2.2.2 install below will be via Composer download and native Magento binary command tool and is 100% pure SSH command line install. FTP/SFTP is not used at all. The below Magento install steps could be used to write an automated script to install Magento 2.2.2 on Centmin Mod LEMP stacks purely from SSH command line. Maybe something planned on offer to [Centmin Mod Premium Members](https://community.centminmod.com/premium/). Update: added a [Magento 2 Bugs section](https://github.com/centminmod/centminmod-magento2#magento-2-bugs) of which one bug already affects Magento 2.2.2 causing high MySQL / PHP related loads.

**Disclaimer:** 

I have never used Magento before so this is first attempt at installing Magento 2.2.2 after a quick read of the official Magento 2.2.2 documentation and Magento related info links outlined below in [Magento Docs & Info Links](https://github.com/centminmod/centminmod-magento2#magento-docs--info-links) section. I haven't gone beyond the initial Magento installation, so have no experience with managing and administrating Magento 2.2 application nor have I have any experience with Magento themes etc. It's purely a first time attempt outline for Magento 2.2.2. Hence, why I decided to setup this Magento install guide on a Github repository instead of the [official Centmin Mod Community forums](https://community.centminmod.com/) so I can accept contributions and corrections to the below guide via Git pull requests.

All below installation steps assume you have thoroughly read those listed [official Magento 2.2.2 documentation and links](https://github.com/centminmod/centminmod-magento2#magento-docs--info-links). So if you do not understand any of the SSH commands used, start reading the listed documentation and links below.

**Github Repository**

* https://github.com/centminmod/centminmod-magento2

![](/screenshots/magento-222-admin-02.png)

## Centmin Mod 123.09beta01 LEMP Stack Install

First you need to install Centmin Mod 123.09beta01 LEMP stack on a fresh virgin CentOS 7.4 64bit server using the `betainstaller71.sh` installer which will install Nginx 1.13 branch with MariaDB 10.1.31 MySQL and latest version of PHP 7.1.x branch as PHP-FPM which as of this moment is PHP 7.1.15. Details are [here](https://community.centminmod.com/threads/centmin-mod-09-beta-branch-testing.4128/).

As root user in SSH session run the installer via curl:

```
yum -y update; curl -O https://centminmod.com/betainstaller71.sh && chmod 0700 betainstaller71.sh && bash betainstaller71.sh
```
Install times vary depending on the server hardware specs, resources available and speed of server i.e. more cpu cores and higher cpu clock speed = faster install times. You can check out other Centmin Mod users' posted install times [here](https://community.centminmod.com/threads/post-your-centmin-mod-123-09beta01-install-time-stats.8866/).

After install it is highly recommended to bookmark and read at least these 3 links:

* [Getting Started Guide](https://centminmod.com/getstarted.html)
* [How to Boost Centmin Mod LEMP Stack Performance](https://centminmod.com/perf/)
* [Guide To Learning About Centmin Mod](https://community.centminmod.com/threads/guide-to-learning-more-about-centmin-mod.10838/)

Centmin Mod LEMP stack installer takes care of all possible PHP extension requirements for many web applications including, Wordpress, Xenforo, Invision Board, vBulletin, Piwik, and Magento.

List of PHP extensions loaded with PHP 7.1.5 with lzf and lz4 added post initial install

```
php -m
[PHP Modules]
bcmath
bz2
calendar
Core
ctype
curl
date
dom
enchant
exif
filter
ftp
gd
geoip
gettext
gmp
hash
iconv
igbinary
imagick
imap
intl
json
ldap
libxml
lz4
lzf
mailparse
mbstring
mcrypt
memcache
memcached
mysqli
mysqlnd
openssl
pcntl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
pspell
readline
redis
Reflection
session
shmop
SimpleXML
snmp
soap
sockets
SPL
sqlite3
standard
sysvmsg
sysvsem
sysvshm
tidy
tokenizer
xml
xmlreader
xmlrpc
xmlwriter
xsl
Zend OPcache
zip
zlib

[Zend Modules]
Zend OPcache
```

## Magento 2.2.2 Installation

Magento 2.2.2 install is done purely via SSH command line and the test KVM VPS server is of following specs:

 - 4 CPU Intel 2.40Ghz cpu 
 - 4GB memory 
 - 80GB disk 
 - CentOS 7.4 64bit 
 - Centmin Mod 123.09beta01 installed via [betainstaller71.sh](https://community.centminmod.com/threads/centmin-mod-09-beta-branch-testing.4128/)
 - Nginx 1.13.9
 - PHP-FPM 7.1.15 + Zend Opcache
 - MariaDB 10.1.31 MySQL Server
 - CSF Firewall

The cpu is very old as it doesn't have SSE4.2 which was added back in [Intel Nehalem](https://en.wikipedia.org/wiki/SSE4#SSE4.2) architecture back in 2008 - making this cpu at least 10+ yrs old.

```
lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                4
On-line CPU(s) list:   0-3
Thread(s) per core:    1
Core(s) per socket:    1
Socket(s):             4
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 13
Model name:            QEMU Virtual CPU version 1.5.3
Stepping:              3
CPU MHz:               2499.998
BogoMIPS:              4999.99
Hypervisor vendor:     KVM
Virtualization type:   full
L1d cache:             32K
L1i cache:             32K
L2 cache:              4096K
NUMA node0 CPU(s):     0-3
Flags:                 fpu de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pse36 clflush mmx fxsr sse sse2 syscall nx lm rep_good nopl pni cx16 hypervisor lahf_lm
```

### Step 1. Get Magento Authentication Keys

To install Magento via Composer, you need to register an account to get the Authentication Keys from [Magento Markplace](https://marketplace.magento.com/) and log in and go to Account Profile, Market Place tab and Access Keys to generate a set of Authentictaion Keys. In step 2 next, you will use the public key to assign to SSH variable `magentouser_key`and private key to assign to SSH variable `magentouser_pass`.

### Step 2. Install Magento required components and optional components for optimal performance

#### composer

```
magentouser_key=yourmagento_authention_publickey
magentouser_pass=yourmagento_authention_privatekey
curl -sS https://getcomposer.org/installer | php
mv -f composer.phar /usr/local/bin/composer
composer config -g repositories.magento '{ "type": "composer", "url": "https://repo.magento.com/" }'
composer config -g http-basic.repo.magento.com $magentouser_key $magentouser_pass
```

#### n98-magerun2 install https://github.com/netz98/n98-magerun2

```
wget -O /usr/local/bin/n98-magerun2 https://files.magerun.net/n98-magerun2.phar
chmod +x /usr/local/bin/n98-magerun2
wget -O /etc/bash_completion.d/n98-magerun2.phar.bash https://github.com/netz98/n98-magerun2/raw/develop/res/autocompletion/bash/n98-magerun2.phar.bash
```

#### redis server

Redis server is installed from Remi YUM repository using [custom Redis installer](https://github.com/centminmod/centminmod-redis) written for Centmin Mod LEMP stacks and implements the basic Redis server install steps outlined [here](https://community.centminmod.com/threads/how-to-install-redis-server-on-centmin-mod-lemp-stack.4546/).

```
mkdir -p /root/tools
cd /root/tools
git clone https://github.com/centminmod/centminmod-redis
cd centminmod-redis
if [ ! -f /usr/bin/redis-server ]; then ./redis-install.sh install; fi
service redis restart
```

Modifying /etc/redis.conf config with

```
maxmemory 512mb
maxmemory-policy allkeys-lru
maxmemory-samples 10
appendonly yes
```

If you are only using redis server for caching and not persistent storage in any web apps on server also comment out and disable snapshotting altogther

```
#save 900 1
#save 300 10
#save 60 10000
```

You can also remove the Redis TCP overhead in /etc/redis.conf by also enabling unix socket mode while keep TCP ports for Redis enabled too. Below setups up unixsocket at `/var/run/redis/redis.sock`

```
# Specify the path for the Unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
#
unixsocket /var/run/redis/redis.sock
unixsocketperm 777
```

restart redis server

```
service redis restart
```

check redis server info via unixsocket

```
redis-cli -s /var/run/redis/redis.sock info server
# Server
redis_version:4.0.8
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:32fcc4a764b07c42
redis_mode:standalone
os:Linux 3.10.0-229.el7.x86_64 x86_64
arch_bits:64
multiplexing_api:epoll
atomicvar_api:atomic-builtin
gcc_version:4.8.5
process_id:1618
run_id:21de92709a7ed5f299425ddeb1bf0dd5a7399331
tcp_port:6379
uptime_in_seconds:139
uptime_in_days:0
hz:10
lru_clock:10971220
executable:/usr/bin/redis-server
config_file:/etc/redis.conf
```

#### lz4 compression

Need to install newer version of lz4 1.8.2 instead of YUM provided 1.7.3 and install lzf and lz4 PHP extensions.

```
cd /svr-setup || cd /usr/src ; rm -rf lz4; 
git clone --depth=1 https://github.com/lz4/lz4;
cd lz4; make clean; if [ -f /opt/rh/devtoolset-7/enable ]; then source /opt/rh/devtoolset-7/enable; fi
make -j$(nproc);
make install;
lz4 --help;
```
Installing lz4 PHP extension - you may need to reinstall this if you upgrade PHP to major branch i.e. from PHP 7.1.x to 7.2.x
```
cd /svr-setup;
git clone --recursive --depth=1 https://github.com/kjdev/php-ext-lz4.git;
cd php-ext-lz4;
make clean;
phpize;
./configure --with-php-config=/usr/local/bin/php-config;
make -j$(nproc);
make install;
PHPEXTDIRD=$(cat /usr/local/bin/php-config | awk '/^extension_dir/ {extdir=$1} END {gsub(/\047|extension_dir|=|)/,"",extdir); print extdir}');
echo "extension=${PHPEXTDIRD}/lz4.so" > /etc/centminmod/php.d/lz4.ini;
fpmrestart
php --ri lz4
```
Installing lzf PHP extension - you may need to reinstall this if you upgrade PHP to major branch i.e. from PHP 7.1.x to 7.2.x
```
cd /svr-setup;
wget https://pecl.php.net/get/LZF-1.6.6.tgz;
tar vzxf LZF-1.6.6.tgz;
cd LZF-1.6.6;
make clean;
phpize;
./configure --with-php-config=/usr/local/bin/php-config;
make -j$(nproc);
make install;
PHPEXTDIRD=$(cat /usr/local/bin/php-config | awk '/^extension_dir/ {extdir=$1} END {gsub(/\047|extension_dir|=|)/,"",extdir); print extdir}');
echo "extension=${PHPEXTDIRD}/lzf.so" > /etc/centminmod/php.d/lzf.ini;
fpmrestart
php --ri lzf
```

#### tuning zend opcache

* magento [recommends](http://devdocs.magento.com/guides/v2.2/config-guide/prod/prod_perf-optimize.html) that opcache.enable_cli=1 is enabled
* tune and edit custom php.ini settings via custom separate individual .ini settings files outlined at https://centminmod.com/phpfpm.html
* create custom .ini settings file at /etc/centminmod/php.d/zzz-zendopcache.ini which overrides default /etc/centminmod/php.d/zendopcache.ini
* allocate 512MB of memory to Zend Opcache but don't set opcache.validate_timestamps=0 and leave at default opcache.validate_timestamps=1
* raise opcache.max_accelerated_files to 100,000
* set memory_limit to 1024MB

```
echo -e "memory_limit=1024M\nopcache.max_accelerated_files=100000\nopcache.memory_consumption=512\nopcache.enable_cli=1\nsession.gc_maxlifetime=86400" > /etc/centminmod/php.d/zzz-zendopcache.ini
fpmrestart
php --ini
php --ri "Zend Opcache"
php --ri session
```

check memory_limit which shows local and global values

```
php -i | grep memory_limit
memory_limit => 1024M => 1024M
```

check Zend Opcache settings

```
php --ri "Zend Opcache"

Zend OPcache

Opcode Caching => Up and Running
Optimization => Enabled
SHM Cache => Enabled
File Cache => Disabled
Startup => OK
Shared memory model => mmap
Cache hits => 0
Cache misses => 0
Used memory => 25161376
Free memory => 511709536
Wasted memory => 0
Interned Strings Used memory => 413712
Interned Strings Free memory => 7974896
Cached scripts => 0
Cached keys => 0
Max keys => 130987
OOM restarts => 0
Hash keys restarts => 0
Manual restarts => 0

Directive => Local Value => Master Value
opcache.enable => On => On
opcache.use_cwd => On => On
opcache.validate_timestamps => On => On
opcache.validate_permission => Off => Off
opcache.validate_root => Off => Off
opcache.inherited_hack => On => On
opcache.dups_fix => Off => Off
opcache.revalidate_path => Off => Off
opcache.log_verbosity_level => 1 => 1
opcache.memory_consumption => 512 => 512
opcache.interned_strings_buffer => 8 => 8
opcache.max_accelerated_files => 100000 => 100000
opcache.max_wasted_percentage => 5 => 5
opcache.consistency_checks => 0 => 0
opcache.force_restart_timeout => 180 => 180
opcache.revalidate_freq => 180 => 180
opcache.file_update_protection => 2 => 2
opcache.preferred_memory_model => no value => no value
opcache.blacklist_filename => no value => no value
opcache.max_file_size => 0 => 0
opcache.protect_memory => 0 => 0
opcache.save_comments => 1 => 1
opcache.fast_shutdown => 0 => 0
opcache.optimization_level => 0x7FFFBFFF => 0x7FFFBFFF
opcache.opt_debug_level => 0 => 0
opcache.enable_file_override => On => On
opcache.enable_cli => On => On
opcache.error_log => no value => no value
opcache.restrict_api => no value => no value
opcache.lockfile_path => /tmp => /tmp
opcache.file_cache => no value => no value
opcache.file_cache_only => 0 => 0
opcache.file_cache_consistency_checks => 1 => 1
opcache.huge_code_pages => Off => Off
```

### Step 3. Download & Install Magento

Below steps will download Magento 2.2.2 via Composer and use Magento native `bin/magento` binary to install Magento 2.2.2 on Centmin Mod LEMP stack based CentOS 7.4 64bit server enabling the following optimisations:

* Install Redis 4.0.8+ server
* Install [n98-magerun2](https://github.com/netz98/n98-magerun2) command line too
* Install lzf and lz4 PHP extensions
* Install Magento 2.2.2 with randomised backend Admin url address
* Enable Magento 2 default, full page cache and session caching using Redis caching with full page cache and session caching using faster lz4 compression type instead of default gzip type
* Flat based catalogue product and categories
* Async indexing
* Async sending of sales emails
* magento_umask set to 022
* Create Centmin Mod Nginx HTTP/2 HTTPS based vhost site with Magento 2 nginx configuration rules modified for Centmin Mod Nginx structured layout

First use `nv` command outlined [here](https://centminmod.com/nginx_domain_dns_setup.html) to create a Centmin Mod Nginx HTTP/2 HTTPS vhost site with a new `magento2` system user and default `vhostname='magento.domain.com'`variable in SSH session which will be referenced in below steps as `$vhostname` variable.

```
# setup centmin mod nginx http/2 https vhost site
vhostname='magento.domain.com'
adduser magento2
adduser_pass="$(pwgen -1cnys 34)"
echo "magento2 user pass: $adduser_pass"
echo "$adduser_pass" | passwd magento2 --stdin
usermod -a -G nginx magento2
id magento2
groups magento2
echo "$(curl -4s ipinfo.io/ip) $vhostname" >> /etc/hosts
```
For Nginx vhost setup via `nv` command using self-signed SSL certificate based HTTP/2 HTTPS:

```
nv -d $vhostname -s y -u "ftpu$(pwgen -1cnys 31)"
```
Or instead use free Letsencrypt SSL certificate via `nv` command using Centmin Mod [addons/acmetool.sh](https://centminmod.com/acmetool/) integration script. Just make sure that your default `vhostname='magento.domain.com'` variable domain i.e. `magento.domain.com` has a valid and propagated DNS A record pointing to the Centmin Mod LEMP server's primary IP address. If you are using a non-subdomain name, make sure both non-www and www version of the name both have valid and propagated DNS A records pointing to the Centmin Mod LEMP server's primary IP address **BEFORE** you run the below `nv` command to automatically create a Centmin Mod Nginx HTTP/2 HTTPS vhost site and obtain a free Letsencrypt SSL certificate. The valid DNS A records are need for Letsencrypt domain validation purposes.


```
echo "LETSENCRYPT_DETECT='y'" >> /etc/centminmod/custom_config.inc
nv -d $vhostname -s lelive -u "ftpu$(pwgen -1cnys 31)"
```


Change into `magento.domain.com` vhost directory one level above the usual web root at `public`, which is `/home/nginx/domains/$vhostname` and move and make a backup copy of the original web root as `public.orig` directory. Then use Composer to download the Magento 2.2.2 source code to a newly created `public` directory and set the appropriate user/group owner permissions on files, directories and the Magento binary.

Update: Magento 2.2.3 is latest now so you would change `magento/project-community-edition=2.2.2` to `magento/project-community-edition=2.2.3` instead.

```
# downloading magento 2.2 code via composer
cd /home/nginx/domains/$vhostname
mv public public.orig
time composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition=2.2.2 public
time composer require predis/predis
#time composer require ext-phpiredis
cd public
time find var generated vendor pub/static pub/media app/etc -type f -exec chmod u+w {} \;
time find var vendor generated pub/static pub/media app/etc -type d -exec chmod u+w {} \;
chown -R nginx:nginx "/home/nginx/domains/${vhostname}/public"
chmod u+x bin/magento
```
Now the actual Magento 2.2.2 installing via SSH command line using the Magento binary `$WEBROOT/bin/magento` where `WEBROOT` variable and other installation settings are setup in SSH session variables outlined below. Adjust the variables values as needed.

You can use `$WEBROOT/bin/magento list` command to see all available command options.

```
php $WEBROOT/bin/magento list
Magento CLI version 2.2.2

Usage:
  command [options] [arguments]

Options:
  -h, --help            Display this help message
  -q, --quiet           Do not output any message
  -V, --version         Display this application version
      --ansi            Force ANSI output
      --no-ansi         Disable ANSI output
  -n, --no-interaction  Do not ask any interactive question
  -v|vv|vvv, --verbose  Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  help                                     Displays help for a command
  list                                     Lists commands
 admin
  admin:user:create                        Creates an administrator
  admin:user:unlock                        Unlock Admin Account
 app
  app:config:dump                          Create dump of application
  app:config:import                        Import data from shared configuration files to appropriate data storage
 cache
  cache:clean                              Cleans cache type(s)
  cache:disable                            Disables cache type(s)
  cache:enable                             Enables cache type(s)
  cache:flush                              Flushes cache storage used by cache type(s)
  cache:status                             Checks cache status
 catalog
  catalog:images:resize                    Creates resized product images
  catalog:product:attributes:cleanup       Removes unused product attributes.
 config
  config:sensitive:set                     Set sensitive configuration values
  config:set                               Change system configuration
  config:show                              Shows configuration value for given path. If path is not specified, all saved values will be shown
 cron
  cron:install                             Generates and installs crontab for current user
  cron:remove                              Removes tasks from crontab
  cron:run                                 Runs jobs by schedule
 customer
  customer:hash:upgrade                    Upgrade customer's hash according to the latest algorithm
 deploy
  deploy:mode:set                          Set application mode.
  deploy:mode:show                         Displays current application mode.
 dev
  dev:di:info                              Provides information on Dependency Injection configuration for the Command.
  dev:query-log:disable                    Disable DB query logging
  dev:query-log:enable                     Enable DB query logging
  dev:source-theme:deploy                  Collects and publishes source files for theme.
  dev:template-hints:disable               Disable frontend template hints. A cache flush might be required.
  dev:template-hints:enable                Enable frontend template hints. A cache flush might be required.
  dev:tests:run                            Runs tests
  dev:urn-catalog:generate                 Generates the catalog of URNs to *.xsd mappings for the IDE to highlight xml.
  dev:xml:convert                          Converts XML file using XSL style sheets
 i18n
  i18n:collect-phrases                     Discovers phrases in the codebase
  i18n:pack                                Saves language package
  i18n:uninstall                           Uninstalls language packages
 indexer
  indexer:info                             Shows allowed Indexers
  indexer:reindex                          Reindexes Data
  indexer:reset                            Resets indexer status to invalid
  indexer:set-mode                         Sets index mode type
  indexer:show-mode                        Shows Index Mode
  indexer:status                           Shows status of Indexer
 info
  info:adminuri                            Displays the Magento Admin URI
  info:backups:list                        Prints list of available backup files
  info:currency:list                       Displays the list of available currencies
  info:dependencies:show-framework         Shows number of dependencies on Magento framework
  info:dependencies:show-modules           Shows number of dependencies between modules
  info:dependencies:show-modules-circular  Shows number of circular dependencies between modules
  info:language:list                       Displays the list of available language locales
  info:timezone:list                       Displays the list of available timezones
 maintenance
  maintenance:allow-ips                    Sets maintenance mode exempt IPs
  maintenance:disable                      Disables maintenance mode
  maintenance:enable                       Enables maintenance mode
  maintenance:status                       Displays maintenance mode status
 module
  module:disable                           Disables specified modules
  module:enable                            Enables specified modules
  module:status                            Displays status of modules
  module:uninstall                         Uninstalls modules installed by composer
 sampledata
  sampledata:deploy                        Deploy sample data modules
  sampledata:remove                        Remove all sample data packages from composer.json
  sampledata:reset                         Reset all sample data modules for re-installation
 setup
  setup:backup                             Takes backup of Magento Application code base, media and database
  setup:config:set                         Creates or modifies the deployment configuration
  setup:cron:run                           Runs cron job scheduled for setup application
  setup:db-data:upgrade                    Installs and upgrades data in the DB
  setup:db-schema:upgrade                  Installs and upgrades the DB schema
  setup:db:status                          Checks if DB schema or data requires upgrade
  setup:di:compile                         Generates DI configuration and all missing classes that can be auto-generated
  setup:install                            Installs the Magento application
  setup:performance:generate-fixtures      Generates fixtures
  setup:rollback                           Rolls back Magento Application codebase, media and database
  setup:static-content:deploy              Deploys static view files
  setup:store-config:set                   Installs the store configuration. Deprecated since 2.2.0. Use config:set instead
  setup:uninstall                          Uninstalls the Magento application
  setup:upgrade                            Upgrades the Magento application, DB data, and schema
 store
  store:list                               Displays the list of stores
  store:website:list                       Displays the list of websites
 theme
  theme:uninstall                          Uninstalls theme
 varnish
  varnish:vcl:generate                     Generates Varnish VCL and echos it to the command line
```

Define your custom variables for Magento 2.2.2 install. I use `pwgen` password generator command to randomise some parts of the values for database name `DBNAME`, database username `DBUSER`, database user password `DBPASS`, Magento admin user password `ADMINPASS` and the backend Admin url `BACKEND_FRONTNAME`.


```
# defining variables
WEBROOT="/home/nginx/domains/${vhostname}/public"
IP=$(curl -4s ipinfo.io/ip)
DBPREFIX='mags_'
DBNAME="magento2$(pwgen -1cnys 4)"
DBUSER="magento2$(pwgen -1cnys 9)"
DBPASS=$(pwgen -1cnys 21)
ADMINUSER=adminuser
ADMINPASS="adminpass$(pwgen -1cnys 18)"
ADMINFNAME=first
ADMINLNAME=last
ADMINEMAIL=your@emaildomain.com
CURRENCY=USD
TIMEZONE='America/Chicago'
BACKEND_FRONTNAME="admin_au$(pwgen -1cnys 8)"
```
Use Centmin Mod [addons/mysqladmin_shell.sh](https://community.centminmod.com/threads/mysqladmin_shell-sh-shell-based-addon.543/) script to create the MySQL database, database user and database password based on the above set SSH session variables.
```
# create mysql database
echo "/usr/local/src/centminmod/addons/mysqladmin_shell.sh createuserdb $DBNAME $DBUSER $DBPASS"
/usr/local/src/centminmod/addons/mysqladmin_shell.sh createuserdb $DBNAME $DBUSER $DBPASS
```
Now using php run the `$WEBROOT/bin/magento` binary to install and setup Magento 2.2.2
```
# magento installation
time php $WEBROOT/bin/magento setup:install --base-url=https:/${vhostname}/ \
--db-host=localhost --db-prefix=$DBPREFIX --db-name=$DBNAME --db-user=$DBUSER --db-password=$DBPASS \
--admin-firstname=$ADMINFNAME --admin-lastname=$ADMINLNAME --admin-email=$ADMINEMAIL \
--admin-user=$ADMINUSER --admin-password=$ADMINPASS --language=en_US \
--backend-frontname=$BACKEND_FRONTNAME \
--currency=$CURRENCY --timezone=$TIMEZONE --use-rewrites=1

# setup whitelisted IP for maintenance mode
MYIP=$(last -i | grep "still logged in" | awk '{print $3}' | uniq)
php $WEBROOT/bin/magento maintenance:allow-ips $MYIP

# fix base-url if mistakes occur
php $WEBROOT/bin/magento setup:store-config:set --base-url="https://${vhostname}/"
php $WEBROOT/bin/magento setup:store-config:set --base-url-secure="https://${vhostname}/"
php $WEBROOT/bin/magento setup:store-config:set --use-secure=1
php $WEBROOT/bin/magento setup:store-config:set --use-secure-admin=1
php $WEBROOT/bin/magento cache:flush

# list database tables
echo "SELECT CONCAT(table_schema,'.',table_name) AS 'Table Name', CONCAT(ROUND(table_rows,2),' Rows') AS 'Number of Rows',ENGINE AS 'Storage Engine',CONCAT(ROUND(data_length/(1024*1024),2),'MB') AS 'Data Size',
CONCAT(ROUND(index_length/(1024*1024),2),'MB') AS 'Index Size' ,CONCAT(ROUND((data_length+index_length)/(1024*1024),2),'MB') AS'Total', ROW_FORMAT, TABLE_COLLATION FROM information_schema.TABLES WHERE table_schema LIKE '$DBNAME';" | mysql -t

# mysql engine breakdown stats
echo "SELECT engine, count(*) tables, concat(round(sum(table_rows))) rows, concat(round(sum(data_length)/(1024*1024),2),'MB') data, concat(round(sum(index_length)/(1024*1024),2),'MB') idx, concat(round(sum(data_length+index_length)/(1024*1024),2),'MB') total_size, round(sum(index_length)/sum(data_length),2) idxfrac FROM information_schema.TABLES WHERE table_schema LIKE '$DBNAME' GROUP BY engine ORDER BY sum(data_length+index_length) DESC LIMIT 10;" | mysql -t

# setup default, full page and session based redis caching in redis databases 12, 13 and 14 respectively instead of default
# if using redis unix socket set to 
# redisserver='/var/run/redis/redis.sock'
redisserver=127.0.0.1
# lz4 or gzip
compresstype=lz4
port=6379
php $WEBROOT/bin/magento setup:config:set --cache-backend=redis --cache-backend-redis-server=$redisserver --cache-backend-redis-db=12 --cache-backend-redis-port=$port

php $WEBROOT/bin/magento setup:config:set --page-cache=redis --page-cache-redis-server=$redisserver --page-cache-redis-db=13 --page-cache-redis-compress-data=$compresstype --page-cache-redis-port=$port

php $WEBROOT/bin/magento setup:config:set --session-save=redis --session-save-redis-host=$redisserver --session-save-redis-log-level=3 --session-save-redis-db=14 --session-save-redis-compression-lib=$compresstype --session-save-redis-max-concurrency=4 --session-save-redis-first-lifetime=600 --session-save-redis-bot-first-lifetime=60 --session-save-redis-bot-lifetime=7200 --session-save-redis-min-lifetime=60 --session-save-redis-max-lifetime=2592000 --session-save-redis-break-after-frontend=60 --session-save-redis-break-after-adminhtml=60 --session-save-redis-port=$port

#n98-magerun2 config:set catalog/frontend/grid_per_page_values "12,24,36"
#n98-magerun2 config:set catalog/frontend/grid_per_page "12"
n98-magerun2 config:set catalog/frontend/flat_catalog_category 1
n98-magerun2 config:set catalog/frontend/flat_catalog_product 1
n98-magerun2 config:set system/full_page_cache/caching_application 1
n98-magerun2 config:set system/full_page_cache/ttl 86400
n98-magerun2 config:set dev/grid/async_indexing 1
n98-magerun2 config:set sales_email/general/async_sending 1

php $WEBROOT/bin/magento cache:status
php $WEBROOT/bin/magento cache:flush
php $WEBROOT/bin/magento indexer:reindex cataloginventory_stock
php $WEBROOT/bin/magento indexer:reindex
php $WEBROOT/bin/magento cache:enable

# cronjob setup
crontab -l > cronjob.backups
ls -lah cronjob.backups
php $WEBROOT/bin/magento cron:install
crontab -l > cronjob.magento
sed -i 's|#~ MAGENTO START|\n#~ MAGENTO START|' cronjob.magento
crontab cronjob.magento
crontab -l

# set umask http://devdocs.magento.com/guides/v2.2/install-gde/install/post-install-umask.html
echo "022" > $WEBROOT/magento_umask

# optimisations for production mode only
php $WEBROOT/bin/magento deploy:mode:show
php $WEBROOT/bin/magento deploy:mode:set production
# optimisations
n98-magerun2 config:set dev/js/merge_files 1
n98-magerun2 config:set dev/css/merge_css_files 1
php $WEBROOT/bin/magento indexer:set-mode schedule design_config_grid customer_grid catalog_product_flat catalog_category_flat catalog_category_product catalog_product_category catalog_product_price catalog_product_attribute cataloginventory_stock catalogrule_rule catalogrule_product catalogsearch_fulltext
php $WEBROOT/bin/magento module:disable Shopial_Facebook
php $WEBROOT/bin/magento setup:static-content:deploy
php $WEBROOT/bin/magento setup:di:compile

echo "admin url = https://${vhostname}/${BACKEND_FRONTNAME}"
```

Optional install [Peformance Dashboard Extension](https://github.com/magehost/performance-dashboard)

```
composer require magehost/performance-dashboard
php $WEBROOT/bin/magento module:enable MageHost_PerformanceDashboard
php $WEBROOT/bin/magento setup:upgrade
php $WEBROOT/bin/magento setup:di:compile
php $WEBROOT/bin/magento setup:static-content:deploy --area adminhtml
composer dump-autoload -o
chown -R nginx:nginx "/home/nginx/domains/${vhostname}/public"
```


Optional sample data you can install but you need to switch to `developer` mode first to install the sample data. Don't install sample data if you intend to run a production live web site.

```
# install sample data
# http://devdocs.magento.com/guides/v2.2/install-gde/trouble/tshoot_sample-data.html#trouble-samp-prod

# dev mode
php $WEBROOT/bin/magento deploy:mode:show
php $WEBROOT/bin/magento deploy:mode:set developer
rm -rf generated/code/* generated/metadata/*
php $WEBROOT/bin/magento cache:status
php $WEBROOT/bin/magento cache:flush
php $WEBROOT/bin/magento indexer:reindex cataloginventory_stock
php $WEBROOT/bin/magento indexer:reindex
php $WEBROOT/bin/magento cache:enable
redis-cli -n 12 FLUSHALL
redis-cli -n 13 FLUSHALL
redis-cli -n 14 FLUSHALL
php $WEBROOT/bin/magento sampledata:deploy
php $WEBROOT/bin/magento setup:upgrade

# optimisations
n98-magerun2 config:set dev/js/merge_files 0
n98-magerun2 config:set dev/css/merge_css_files 0
php $WEBROOT/bin/magento setup:static-content:deploy
php $WEBROOT/bin/magento setup:di:compile
composer dump-autoload -o
# ensure correct Centmin Mod Nginx permissions
chown -R nginx:nginx "/home/nginx/domains/${vhostname}/public"
```

Magento 2 logrotate configuration

Configuring logrotate for Magento 2 by setting up a `/etc/logrotate.d/magento2` logrotate config file with following contents

```
/home/nginx/domains/magento.domain.com/public/var/log/*.log {
  su root root
  size 10M
  missingok
  rotate 50
  compress
  delaycompress
  notifempty
  dateext
}
```

Manually test logrotate

```
logrotate -df /etc/logrotate.d/magento2
```

### Step 4. Nginx Vhost 

As per https://community.centminmod.com/threads/updated-magento-2-nginx-directives-for-magento-2-15.10975/ with adjusted modifications derived from the reading of other info links.

In `/usr/local/nginx/conf/nginx.conf` add within `http {`content add a HSTS header map

```
http {

## Map HSTS header
map $scheme $mag_hstsheader { https  "max-age=31556926; includeSubDomains"; }
```

* Make a copy of `/usr/local/nginx/conf/php.conf` named as `/usr/local/nginx/conf/php_magento.conf` and then disable and comment out with hash in front of `fastcgi_split_path_info` directive in `/usr/local/nginx/conf/php_magento.conf.
* Make a copy of Centmin Mod Nginx HTTP/2 HTTPS vhost config file `/usr/local/nginx/conf/conf.d/magento.domain.com.ssl.conf ` named as `/usr/local/nginx/conf/conf.d/magento.domain.com.ssl.conf.original`
* Disable via renaming the Centmin Mod Nginx non-HTTPS vhost config file `/usr/local/nginx/conf/conf.d/magento.domain.com.conf` renamed as `/usr/local/nginx/conf/conf.d/magento.domain.com.conf.disabled`

```
cp -a /usr/local/nginx/conf/php.conf /usr/local/nginx/conf/php_magento.conf
sed -i 's|fastcgi_split_path_info|#fastcgi_split_path_info|' /usr/local/nginx/conf/php_magento.conf
cp -a /usr/local/nginx/conf/conf.d/magento.domain.com.ssl.conf /usr/local/nginx/conf/conf.d/magento.domain.com.ssl.conf.original
mv /usr/local/nginx/conf/conf.d/magento.domain.com.conf /usr/local/nginx/conf/conf.d/magento.domain.com.conf.disabled
```
Edit Centmin Mod HTTP/2 HTTPS Nginx vhost config file `/usr/local/nginx/conf/conf.d/magento.domain.com.ssl.conf` and force HTTP to HTTPS redirect by uncommenting the 5 lines for `return 302` redirect using nano linux text editor
```
nano /usr/local/nginx/conf/conf.d/magento.domain.com.ssl.conf
```
Enable 302 redirect of non-HTTPS requests to HTTPS version of site domain
```
# Centmin Mod Getting Started Guide
# must read http://centminmod.com/getstarted.html
# For HTTP/2 SSL Setup
# read http://centminmod.com/nginx_configure_https_ssl_spdy.html

# redirect from www to non-www  forced SSL
# uncomment, save file and restart Nginx to enable
# if unsure use return 302 before using return 301
 server {
       listen   80;
       server_name magento.domain.com www.magento.domain.com;
       return 302 https://$server_name$request_uri;
 }

```
Below is the just the Magento 2.2.2 related Nginx rules to be placed in `/usr/local/nginx/conf/conf.d/magento.domain.com.ssl.conf` Nginx vhost config file.
```
set $MAGE_ROOT /home/nginx/domains/magento.domain.com/public;
root $MAGE_ROOT/pub;
include /usr/local/nginx/conf/503include-main.conf;

index index.php;
autoindex off;
charset UTF-8;
error_page 404 403 = /errors/404.php;
#add_header "X-UA-Compatible" "IE=Edge";

# PHP entry point for setup application
location ~* ^/setup($|/) {
    root $MAGE_ROOT;
    location ~ ^/setup/index.php {
        include /usr/local/nginx/conf/php.conf;
    }

    location ~ ^/setup/(?!pub/). {
        deny all;
    }

    location ~ ^/setup/pub/ {
        add_header X-Frame-Options "SAMEORIGIN";
    }
}

# PHP entry point for update application
location ~* ^/update($|/) {
    root $MAGE_ROOT;

    location ~ ^/update/index.php {
        fastcgi_split_path_info ^(/update/index.php)(/.+)$;
        include /usr/local/nginx/conf/php_magento.conf;
    }

    # Deny everything but index.php
    location ~ ^/update/(?!pub/). {
        deny all;
    }

    location ~ ^/update/pub/ {
        add_header X-Frame-Options "SAMEORIGIN";
    }
}

location / {
    include /usr/local/nginx/conf/503include-only.conf;
    try_files $uri $uri/ /index.php$is_args$args;
}

location /pub/ {
    location ~ ^/pub/media/(downloadable|customer|import|theme_customization/.*\.xml) {
        deny all;
    }
    alias $MAGE_ROOT/pub/;
    add_header X-Frame-Options "SAMEORIGIN";
}

location /static/ {
    # Uncomment the following line in production mode
    expires max;

    # Remove signature of the static files that is used to overcome the browser cache
    location ~ ^/static/version {
        rewrite ^/static/(version\d*/)?(.*)$ /static/$2 last;
    }
    location ~* \.(ico|jpg|jpeg|png|gif|svg|js|css)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires 30d;

        if (!-f $request_filename) {
            rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
        }
    }
    location ~* \.(swf|eot|ttf|otf|woff|woff2)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires +1y;

        if (!-f $request_filename) {
            rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
        }
    }
    location ~* \.(zip|gz|gzip|bz2|csv|xml)$ {
        add_header Cache-Control "no-store";
        add_header X-Frame-Options "SAMEORIGIN";
        expires    off;

        if (!-f $request_filename) {
           rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
        }
    }
    if (!-f $request_filename) {
        rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
    }
    add_header X-Frame-Options "SAMEORIGIN";
}

location /media/ {
    try_files $uri $uri/ /get.php$is_args$args;

    location ~ ^/media/theme_customization/.*\.xml {
        deny all;
    }
    location ~* \.(ico|jpg|jpeg|png|gif|svg|js|css)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires 30d;
        try_files $uri $uri/ /get.php$is_args$args;
    }
    location ~* \.(swf|eot|ttf|otf|woff|woff2)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires +1y;
        try_files $uri $uri/ /get.php$is_args$args;
    }
    location ~* \.(zip|gz|gzip|bz2|csv|xml)$ {
        add_header Cache-Control "no-store";
        add_header X-Frame-Options "SAMEORIGIN";
        expires    off;
        try_files $uri $uri/ /get.php$is_args$args;
    }
    add_header X-Frame-Options "SAMEORIGIN";
}

location /media/customer/ {
    deny all;
}

location /media/downloadable/ {
    deny all;
}

location /media/import/ {
    deny all;
}

# Deny cron and files with the obvious names. favorite entry points for hackers and script kiddie
location ~* ^/(cron|phpminiadmin|pma|sqlyog|adminer.+)\.php { deny all; }

# Deny auth and composer
location ~ (auth|package|composer)\.(json|lock)$ { deny all; }

# PHP entry point for main application
location ~ (index|get|static|report|404|503|health_check)\.php$ {
    try_files $uri =404;
    add_header X-Processing-Time $request_time always;
    add_header X-Request-ID $request_id always;
    add_header Strict-Transport-Security $mag_hstsheader always;
    add_header X-UA-Compatible 'IE=Edge,chrome=1';
    add_header Link "<$scheme://$http_host$request_uri>; rel=\"canonical\"" always;
    fastcgi_buffers 1024 4k;

    fastcgi_param  PHP_FLAG  "session.auto_start=off \n suhosin.session.cryptua=off";
    fastcgi_param  PHP_VALUE "memory_limit=4096M \n max_execution_time=3600";
    fastcgi_read_timeout 600s;
    fastcgi_connect_timeout 600s;
    include /usr/local/nginx/conf/503include-only.conf;
    include /usr/local/nginx/conf/php.conf;
}

# Banned locations (only reached if the earlier PHP entry point regexes don't match)
location ~* (\.php$|\.htaccess$|\.git) {
    deny all;
}

  include /usr/local/nginx/conf/pre-staticfiles-local-magento.domain.com.conf;
  include /usr/local/nginx/conf/pre-staticfiles-global.conf;
  #include /usr/local/nginx/conf/staticfiles.conf;
  #include /usr/local/nginx/conf/php.conf;
  #include /usr/local/nginx/conf/drop.conf;
  #include /usr/local/nginx/conf/errorpage.conf;
  include /usr/local/nginx/conf/vts_server.conf;
```

The resulting full Centmin Mod Nginx HTTP/2 HTTPS based Magento 2.2.2 vhost config file `/usr/local/nginx/conf/conf.d/magento.domain.com.ssl.conf` for self-signed SSL certificate based site example is below:

```
# Centmin Mod Getting Started Guide
# must read http://centminmod.com/getstarted.html
# For HTTP/2 SSL Setup
# read http://centminmod.com/nginx_configure_https_ssl_spdy.html

# redirect from www to non-www  forced SSL
# uncomment, save file and restart Nginx to enable
# if unsure use return 302 before using return 301
 server {
       listen   80;
       server_name magento.domain.com www.magento.domain.com;
       return 302 https://$server_name$request_uri;
 }

server {
  listen 443 ssl http2;
  server_name magento.domain.com www.magento.domain.com;

  ssl_dhparam /usr/local/nginx/conf/ssl/magento.domain.com/dhparam.pem;
  ssl_certificate      /usr/local/nginx/conf/ssl/magento.domain.com/magento.domain.com.crt;
  ssl_certificate_key  /usr/local/nginx/conf/ssl/magento.domain.com/magento.domain.com.key;
  include /usr/local/nginx/conf/ssl_include.conf;

  # cloudflare authenticated origin pull cert community.centminmod.com/threads/13847/
  #ssl_client_certificate /usr/local/nginx/conf/ssl/cloudflare/magento.domain.com/origin.crt;
  #ssl_verify_client on;
  http2_max_field_size 16k;
  http2_max_header_size 32k;
  # mozilla recommended
  ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS;
  ssl_prefer_server_ciphers   on;
  #add_header Alternate-Protocol  443:npn-spdy/3;

  # before enabling HSTS line below read centminmod.com/nginx_domain_dns_setup.html#hsts
  #add_header Strict-Transport-Security "max-age=31536000; includeSubdomains;";
  #add_header X-Frame-Options SAMEORIGIN;
  #add_header X-Xss-Protection "1; mode=block" always;
  #add_header X-Content-Type-Options "nosniff" always;
  #add_header Referrer-Policy "strict-origin-when-cross-origin";
  #spdy_headers_comp 5;
  ssl_buffer_size 1369;
  ssl_session_tickets on;
  
  # enable ocsp stapling
  #resolver 8.8.8.8 8.8.4.4 valid=10m;
  #resolver_timeout 10s;
  #ssl_stapling on;
  #ssl_stapling_verify on;
  #ssl_trusted_certificate /usr/local/nginx/conf/ssl/magento.domain.com/magento.domain.com-trusted.crt;  

# ngx_pagespeed & ngx_pagespeed handler
#include /usr/local/nginx/conf/pagespeed.conf;
#include /usr/local/nginx/conf/pagespeedhandler.conf;
#include /usr/local/nginx/conf/pagespeedstatslog.conf;

  # limit_conn limit_per_ip 16;
  # ssi  on;

  access_log /home/nginx/domains/magento.domain.com/log/access.log combined buffer=256k flush=5m;
  error_log /home/nginx/domains/magento.domain.com/log/error.log;

  #include /usr/local/nginx/conf/autoprotect/magento.domain.com/autoprotect-magento.domain.com.conf;
set $MAGE_ROOT /home/nginx/domains/magento.domain.com/public;
root $MAGE_ROOT/pub;
include /usr/local/nginx/conf/503include-main.conf;

index index.php;
autoindex off;
charset UTF-8;
error_page 404 403 = /errors/404.php;
#add_header "X-UA-Compatible" "IE=Edge";

# PHP entry point for setup application
location ~* ^/setup($|/) {
    root $MAGE_ROOT;
    location ~ ^/setup/index.php {
        include /usr/local/nginx/conf/php.conf;
    }

    location ~ ^/setup/(?!pub/). {
        deny all;
    }

    location ~ ^/setup/pub/ {
        add_header X-Frame-Options "SAMEORIGIN";
    }
}

# PHP entry point for update application
location ~* ^/update($|/) {
    root $MAGE_ROOT;

    location ~ ^/update/index.php {
        fastcgi_split_path_info ^(/update/index.php)(/.+)$;
        include /usr/local/nginx/conf/php_magento.conf;
    }

    # Deny everything but index.php
    location ~ ^/update/(?!pub/). {
        deny all;
    }

    location ~ ^/update/pub/ {
        add_header X-Frame-Options "SAMEORIGIN";
    }
}

location / {
    include /usr/local/nginx/conf/503include-only.conf;
    try_files $uri $uri/ /index.php$is_args$args;
}

location /pub/ {
    location ~ ^/pub/media/(downloadable|customer|import|theme_customization/.*\.xml) {
        deny all;
    }
    alias $MAGE_ROOT/pub/;
    add_header X-Frame-Options "SAMEORIGIN";
}

location /static/ {
    # Uncomment the following line in production mode
    expires max;

    # Remove signature of the static files that is used to overcome the browser cache
    location ~ ^/static/version {
        rewrite ^/static/(version\d*/)?(.*)$ /static/$2 last;
    }
    location ~* \.(ico|jpg|jpeg|png|gif|svg|js|css)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires 30d;

        if (!-f $request_filename) {
            rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
        }
    }
    location ~* \.(swf|eot|ttf|otf|woff|woff2)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires +1y;

        if (!-f $request_filename) {
            rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
        }
    }
    location ~* \.(zip|gz|gzip|bz2|csv|xml)$ {
        add_header Cache-Control "no-store";
        add_header X-Frame-Options "SAMEORIGIN";
        expires    off;

        if (!-f $request_filename) {
           rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
        }
    }
    if (!-f $request_filename) {
        rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
    }
    add_header X-Frame-Options "SAMEORIGIN";
}

location /media/ {
    try_files $uri $uri/ /get.php$is_args$args;

    location ~ ^/media/theme_customization/.*\.xml {
        deny all;
    }
    location ~* \.(ico|jpg|jpeg|png|gif|svg|js|css)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires 30d;
        try_files $uri $uri/ /get.php$is_args$args;
    }
    location ~* \.(swf|eot|ttf|otf|woff|woff2)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires +1y;
        try_files $uri $uri/ /get.php$is_args$args;
    }
    location ~* \.(zip|gz|gzip|bz2|csv|xml)$ {
        add_header Cache-Control "no-store";
        add_header X-Frame-Options "SAMEORIGIN";
        expires    off;
        try_files $uri $uri/ /get.php$is_args$args;
    }
    add_header X-Frame-Options "SAMEORIGIN";
}

location /media/customer/ {
    deny all;
}

location /media/downloadable/ {
    deny all;
}

location /media/import/ {
    deny all;
}

# Deny cron and files with the obvious names. favorite entry points for hackers and script kiddie
location ~* ^/(cron|phpminiadmin|pma|sqlyog|adminer.+)\.php { deny all; }

# Deny auth and composer
location ~ (auth|package|composer)\.(json|lock)$ { deny all; }

# PHP entry point for main application
location ~ (index|get|static|report|404|503|health_check)\.php$ {
    try_files $uri =404;
    add_header X-Processing-Time $request_time always;
    add_header X-Request-ID $request_id always;
    add_header Strict-Transport-Security $mag_hstsheader always;
    add_header X-UA-Compatible 'IE=Edge,chrome=1';
    add_header Link "<$scheme://$http_host$request_uri>; rel=\"canonical\"" always;
    fastcgi_buffers 1024 4k;

    fastcgi_param  PHP_FLAG  "session.auto_start=off \n suhosin.session.cryptua=off";
    fastcgi_param  PHP_VALUE "memory_limit=4096M \n max_execution_time=3600";
    fastcgi_read_timeout 600s;
    fastcgi_connect_timeout 600s;
    include /usr/local/nginx/conf/503include-only.conf;
    include /usr/local/nginx/conf/php.conf;
}

# Banned locations (only reached if the earlier PHP entry point regexes don't match)
location ~* (\.php$|\.htaccess$|\.git) {
    deny all;
}

  include /usr/local/nginx/conf/pre-staticfiles-local-magento.domain.com.conf;
  include /usr/local/nginx/conf/pre-staticfiles-global.conf;
  #include /usr/local/nginx/conf/staticfiles.conf;
  #include /usr/local/nginx/conf/php.conf;
  #include /usr/local/nginx/conf/drop.conf;
  #include /usr/local/nginx/conf/errorpage.conf;
  include /usr/local/nginx/conf/vts_server.conf;
}
```

End result is Centmin Mod Nginx 1.13 HTTP/2 HTTPS based Magento 2.2.2 installation

system info

```
n98-magerun2 sys:info --skip-root-check

                              
  Magento System Information  
                              

+------------------+-----------------------------------------------+
| name             | value                                         |
+------------------+-----------------------------------------------+
| Name             | Magento                                       |
| Version          | 2.2.2                                         |
| Edition          | Community                                     |
| Root             | /home/nginx/domains/magento.domain.com/public |
| Application Mode | production                                    |
| Session          | redis                                         |
| Crypt Key        | d45f000b87ce82******************              |
| Install Date     | Sun, 11 Mar 2018 20:40:12 +0000               |
| Cache Backend    | Cm_Cache_Backend_Redis                        |
| Vendors          | Magento, Dotdigitalgroup, Shopial, Temando    |
| Attribute Count  | 134                                           |
| Customer Count   | 0                                             |
| Category Count   | 2                                             |
| Product Count    | 0                                             |
+------------------+-----------------------------------------------+
```

cache list

```
n98-magerun2 cache:list --skip-root-check

                       
  Magento Cache Types  
                       

+------------------------+--------------------------------+---------+
| Name                   | Type                           | Enabled |
+------------------------+--------------------------------+---------+
| config                 | Configuration                  | 1       |
| layout                 | Layouts                        | 1       |
| block_html             | Blocks HTML output             | 1       |
| collections            | Collections Data               | 1       |
| reflection             | Reflection Data                | 1       |
| db_ddl                 | Database DDL operations        | 1       |
| eav                    | EAV types and attributes       | 1       |
| customer_notification  | Customer Notification          | 1       |
| config_integration     | Integrations Configuration     | 1       |
| config_integration_api | Integrations API Configuration | 1       |
| full_page              | Page Cache                     | 1       |
| translate              | Translations                   | 1       |
| config_webservice      | Web Services Configuration     | 1       |
+------------------------+--------------------------------+---------+
```

system check

```
n98-magerun2 sys:check --skip-root-check

                                                                
                            SETTINGS                            
                                                                

✔ Secure BaseURL: https://magento.domain.com/ of Store: default - OK
✔ Unsecure BaseURL: https://magento.domain.com/ of Store: default - OK
✔ Empty cookie Domain (secure) of Store: default - OK
✔ Empty cookie Domain (unsecure) of Store: default - OK

                                                                
                           FILESYSTEM                           
                                                                

✔ Folder pub/media found.
✔ Folder var found.
✖ Folder var/cache not found! Usage: Used for caching
✔ Folder var/session found.
✔ File app/etc/env.php found.

                                                                
                              PHP                               
                                                                

✔ Required PHP Module simplexml found.
✔ Required PHP Module mcrypt found.
✔ Required PHP Module hash found.
✔ Required PHP Module gd found.
✔ Required PHP Module dom found.
✔ Required PHP Module iconv found.
✔ Required PHP Module curl found.
✔ Required PHP Module soap found.
✔ Required PHP Module pdo found.
✔ Required PHP Module pdo_mysql found.
✔ Required PHP Module intl found.
✔ Required PHP Module openssl found.
✔ Bytecode Cache Zend OPcache found.

                                                                
                             MYSQL                              
                                                                

✔ MySQL Version 10.1.31-MariaDB found.
✔ Required MySQL Storage Engine InnoDB found.
```

database info

```
n98-magerun2 db:info --skip-root-check  
+------------------------+----------------------------------------------------------------------------------------------------+
| Name                   | Value                                                                                              |
+------------------------+----------------------------------------------------------------------------------------------------+
| host                   | localhost                                                                                          |
| dbname                 | magento2*****                                                                                      |
| username               | magento2a6********                                                                                 |
| password               | jIPa748zeZ***********                                                                              |
| model                  | mysql4                                                                                             |
| engine                 | innodb                                                                                             |
| initStatements         | SET NAMES utf8;                                                                                    |
| active                 | 1                                                                                                  |
| prefix                 | mags_                                                                                              |
| PDO-Connection-String  | mysql:host=localhost;port=3306;dbname=magento2****                                                 |
| JDBC-Connection-String | jdbc:mysql://localhost:3306/magento2****?username=magento2a6*******&password=jjIPa748zeZ********** |
| MySQL-Cli-String       | mysql -h'localhost' -u'magento2a6*******' --password='jjIPa748zeZ**********' 'magento2****'        |
+------------------------+----------------------------------------------------------------------------------------------------+
```

database server status

```
n98-magerun2 db:status --skip-root-check
+--------------------------------+--------------+-----------------------------------------------------------------------------+
| Variable Name                  | Value        | Description                                                                 |
+--------------------------------+--------------+-----------------------------------------------------------------------------+
| Aborted_connects               |            0 | Total number of failed attempts to connect to MySQL.                        |
| Created_tmp_disk_tables        |          406 | Number of temporary tables that have been created on disk instead of        |
|                                |              | in-memory. Lower is better.                                                 |
| Handler_read_first             |         6422 | Number of times a table handler made a request to read the first row of a   |
|                                |              | table index.                                                                |
| Handler_read_rnd_next          |      7280977 | Number of requests to read the next row in the data file. This value is     |
|                                |              | high if you are doing a lot of table scans. Generally this suggests that    |
|                                |              | your tables are not properly indexed or that your queries are not written   |
|                                |              | to take advantage of the indexes you have.                                  |
| Innodb_buffer_pool_pages_dirty |            0 | Indicates the number of InnoDB buffer pool data pages that have been        |
|                                |              | changed in memory, but the changes are not yet written (flushed) to the     |
|                                |              | InnoDB data files                                                           |
| Innodb_buffer_pool_wait_free   |            0 | Number of times MySQL has to wait for memory pages to be flushed.           |
| Key_reads                      |            9 | Number of filesystem accesses MySQL performed to fetch database indexes.    |
| Max_used_connections           |           12 | Max number of connections MySQL has had open at the same time since the     |
|                                |              | server was last restarted.                                                  |
| Open_tables                    |          398 | Number of tables that are currently open.                                   |
| Select_full_join               |           11 | Number of full joins MySQL has performed to satisfy client queries.         |
| Slow_queries                   |            0 | Number of queries that have taken longer than usual to execute.             |
| Threads_connected              |            1 | Total number of clients that have currently open connections to the server. |
| Uptime                         | 19 hours ago | Time since the server was last restarted.                                   |
| Full table scans               | 69.86%       | HINT: "Handler_read_rnd_next" is reset to zero when reached the value of    |
|                                |              | 2^32 (4G).                                                                  |
| InnoDB Buffer Pool hit         | 100.00%      | An InnoDB Buffer Pool hit ratio below 99.9% is a weak indicator that your   |
|                                |              | InnoDB Buffer Pool could be increased.                                      |
+--------------------------------+--------------+-----------------------------------------------------------------------------+
```

MariaDB server variables of note

```
n98-magerun2 db:variables --skip-root-check
+---------------------------------+--------+
| Variable Name                   | Value  |
+---------------------------------+--------+
| have_query_cache                |    YES |
| innodb_additional_mem_pool_size |     8M |
| innodb_buffer_pool_size         |   192M |
| innodb_log_buffer_size          |     8M |
| innodb_log_file_size            |   128M |
| innodb_thread_concurrency       |      0 |
| join_buffer_size                |   128K |
| key_buffer_size                 |    32M |
| max_allowed_packet              |    64M |
| max_connections                 |    300 |
| max_heap_table_size             |   128M |
| open_files_limit                | 524288 |
| query_cache_size                |    64M |
| query_cache_type                |     ON |
| read_buffer_size                |   128K |
| read_rnd_buffer_size            |   256K |
| sort_buffer_size                |   256K |
| table_definition_cache          |   8192 |
| table_open_cache                |   4096 |
| thread_cache_size               |   128B |
| tmp_table_size                  |   128M |
+---------------------------------+--------+
```

Show Magento 2 database tables

```
n98-magerun2 db:query "show tables;" --skip-root-check   
Tables_in_magento24MUU
mags_admin_passwords
mags_admin_system_messages
mags_admin_user
mags_admin_user_session
mags_adminnotification_inbox
mags_authorization_role
mags_authorization_rule
mags_cache
mags_cache_tag
mags_captcha_log
mags_catalog_category_entity
mags_catalog_category_entity_datetime
mags_catalog_category_entity_decimal
mags_catalog_category_entity_int
mags_catalog_category_entity_text
mags_catalog_category_entity_varchar
mags_catalog_category_flat_cl
mags_catalog_category_flat_store_1
mags_catalog_category_product
mags_catalog_category_product_cl
mags_catalog_category_product_index
mags_catalog_category_product_index_replica
mags_catalog_category_product_index_tmp
mags_catalog_compare_item
mags_catalog_eav_attribute
mags_catalog_product_attribute_cl
mags_catalog_product_bundle_option
mags_catalog_product_bundle_option_value
mags_catalog_product_bundle_price_index
mags_catalog_product_bundle_selection
mags_catalog_product_bundle_selection_price
mags_catalog_product_bundle_stock_index
mags_catalog_product_category_cl
mags_catalog_product_entity
mags_catalog_product_entity_datetime
mags_catalog_product_entity_decimal
mags_catalog_product_entity_gallery
mags_catalog_product_entity_int
mags_catalog_product_entity_media_gallery
mags_catalog_product_entity_media_gallery_value
mags_catalog_product_entity_media_gallery_value_to_entity
mags_catalog_product_entity_media_gallery_value_video
mags_catalog_product_entity_text
mags_catalog_product_entity_tier_price
mags_catalog_product_entity_varchar
mags_catalog_product_flat_1
mags_catalog_product_flat_cl
mags_catalog_product_frontend_action
mags_catalog_product_index_eav
mags_catalog_product_index_eav_decimal
mags_catalog_product_index_eav_decimal_idx
mags_catalog_product_index_eav_decimal_replica
mags_catalog_product_index_eav_decimal_tmp
mags_catalog_product_index_eav_idx
mags_catalog_product_index_eav_replica
mags_catalog_product_index_eav_tmp
mags_catalog_product_index_price
mags_catalog_product_index_price_bundle_idx
mags_catalog_product_index_price_bundle_opt_idx
mags_catalog_product_index_price_bundle_opt_tmp
mags_catalog_product_index_price_bundle_sel_idx
mags_catalog_product_index_price_bundle_sel_tmp
mags_catalog_product_index_price_bundle_tmp
mags_catalog_product_index_price_cfg_opt_agr_idx
mags_catalog_product_index_price_cfg_opt_agr_tmp
mags_catalog_product_index_price_cfg_opt_idx
mags_catalog_product_index_price_cfg_opt_tmp
mags_catalog_product_index_price_downlod_idx
mags_catalog_product_index_price_downlod_tmp
mags_catalog_product_index_price_final_idx
mags_catalog_product_index_price_final_tmp
mags_catalog_product_index_price_idx
mags_catalog_product_index_price_opt_agr_idx
mags_catalog_product_index_price_opt_agr_tmp
mags_catalog_product_index_price_opt_idx
mags_catalog_product_index_price_opt_tmp
mags_catalog_product_index_price_replica
mags_catalog_product_index_price_tmp
mags_catalog_product_index_tier_price
mags_catalog_product_index_website
mags_catalog_product_link
mags_catalog_product_link_attribute
mags_catalog_product_link_attribute_decimal
mags_catalog_product_link_attribute_int
mags_catalog_product_link_attribute_varchar
mags_catalog_product_link_type
mags_catalog_product_option
mags_catalog_product_option_price
mags_catalog_product_option_title
mags_catalog_product_option_type_price
mags_catalog_product_option_type_title
mags_catalog_product_option_type_value
mags_catalog_product_price_cl
mags_catalog_product_relation
mags_catalog_product_super_attribute
mags_catalog_product_super_attribute_label
mags_catalog_product_super_link
mags_catalog_product_website
mags_catalog_url_rewrite_product_category
mags_cataloginventory_stock
mags_cataloginventory_stock_cl
mags_cataloginventory_stock_item
mags_cataloginventory_stock_status
mags_cataloginventory_stock_status_idx
mags_cataloginventory_stock_status_replica
mags_cataloginventory_stock_status_tmp
mags_catalogrule
mags_catalogrule_customer_group
mags_catalogrule_group_website
mags_catalogrule_group_website_replica
mags_catalogrule_product
mags_catalogrule_product_cl
mags_catalogrule_product_price
mags_catalogrule_product_price_replica
mags_catalogrule_product_replica
mags_catalogrule_rule_cl
mags_catalogrule_website
mags_catalogsearch_fulltext_cl
mags_catalogsearch_fulltext_scope1
mags_checkout_agreement
mags_checkout_agreement_store
mags_cms_block
mags_cms_block_store
mags_cms_page
mags_cms_page_store
mags_core_config_data
mags_cron_schedule
mags_customer_address_entity
mags_customer_address_entity_datetime
mags_customer_address_entity_decimal
mags_customer_address_entity_int
mags_customer_address_entity_text
mags_customer_address_entity_varchar
mags_customer_dummy_cl
mags_customer_eav_attribute
mags_customer_eav_attribute_website
mags_customer_entity
mags_customer_entity_datetime
mags_customer_entity_decimal
mags_customer_entity_int
mags_customer_entity_text
mags_customer_entity_varchar
mags_customer_form_attribute
mags_customer_grid_flat
mags_customer_group
mags_customer_log
mags_customer_visitor
mags_design_change
mags_design_config_dummy_cl
mags_design_config_grid_flat
mags_directory_country
mags_directory_country_format
mags_directory_country_region
mags_directory_country_region_name
mags_directory_currency_rate
mags_downloadable_link
mags_downloadable_link_price
mags_downloadable_link_purchased
mags_downloadable_link_purchased_item
mags_downloadable_link_title
mags_downloadable_sample
mags_downloadable_sample_title
mags_eav_attribute
mags_eav_attribute_group
mags_eav_attribute_label
mags_eav_attribute_option
mags_eav_attribute_option_swatch
mags_eav_attribute_option_value
mags_eav_attribute_set
mags_eav_entity
mags_eav_entity_attribute
mags_eav_entity_datetime
mags_eav_entity_decimal
mags_eav_entity_int
mags_eav_entity_store
mags_eav_entity_text
mags_eav_entity_type
mags_eav_entity_varchar
mags_eav_form_element
mags_eav_form_fieldset
mags_eav_form_fieldset_label
mags_eav_form_type
mags_eav_form_type_entity
mags_email_automation
mags_email_campaign
mags_email_catalog
mags_email_contact
mags_email_importer
mags_email_order
mags_email_review
mags_email_rules
mags_email_template
mags_email_wishlist
mags_flag
mags_gift_message
mags_googleoptimizer_code
mags_import_history
mags_importexport_importdata
mags_indexer_state
mags_integration
mags_layout_link
mags_layout_update
mags_mview_state
mags_newsletter_problem
mags_newsletter_queue
mags_newsletter_queue_link
mags_newsletter_queue_store_link
mags_newsletter_subscriber
mags_newsletter_template
mags_oauth_consumer
mags_oauth_nonce
mags_oauth_token
mags_oauth_token_request_log
mags_password_reset_request_event
mags_paypal_billing_agreement
mags_paypal_billing_agreement_order
mags_paypal_cert
mags_paypal_payment_transaction
mags_paypal_settlement_report
mags_paypal_settlement_report_row
mags_persistent_session
mags_product_alert_price
mags_product_alert_stock
mags_quote
mags_quote_address
mags_quote_address_item
mags_quote_id_mask
mags_quote_item
mags_quote_item_option
mags_quote_payment
mags_quote_shipping_rate
mags_rating
mags_rating_entity
mags_rating_option
mags_rating_option_vote
mags_rating_option_vote_aggregated
mags_rating_store
mags_rating_title
mags_release_notification_viewer_log
mags_report_compared_product_index
mags_report_event
mags_report_event_types
mags_report_viewed_product_aggregated_daily
mags_report_viewed_product_aggregated_monthly
mags_report_viewed_product_aggregated_yearly
mags_report_viewed_product_index
mags_reporting_counts
mags_reporting_module_status
mags_reporting_orders
mags_reporting_system_updates
mags_reporting_users
mags_review
mags_review_detail
mags_review_entity
mags_review_entity_summary
mags_review_status
mags_review_store
mags_sales_bestsellers_aggregated_daily
mags_sales_bestsellers_aggregated_monthly
mags_sales_bestsellers_aggregated_yearly
mags_sales_creditmemo
mags_sales_creditmemo_comment
mags_sales_creditmemo_grid
mags_sales_creditmemo_item
mags_sales_invoice
mags_sales_invoice_comment
mags_sales_invoice_grid
mags_sales_invoice_item
mags_sales_invoiced_aggregated
mags_sales_invoiced_aggregated_order
mags_sales_order
mags_sales_order_address
mags_sales_order_aggregated_created
mags_sales_order_aggregated_updated
mags_sales_order_grid
mags_sales_order_item
mags_sales_order_payment
mags_sales_order_status
mags_sales_order_status_history
mags_sales_order_status_label
mags_sales_order_status_state
mags_sales_order_tax
mags_sales_order_tax_item
mags_sales_payment_transaction
mags_sales_refunded_aggregated
mags_sales_refunded_aggregated_order
mags_sales_sequence_meta
mags_sales_sequence_profile
mags_sales_shipment
mags_sales_shipment_comment
mags_sales_shipment_grid
mags_sales_shipment_item
mags_sales_shipment_track
mags_sales_shipping_aggregated
mags_sales_shipping_aggregated_order
mags_salesrule
mags_salesrule_coupon
mags_salesrule_coupon_aggregated
mags_salesrule_coupon_aggregated_order
mags_salesrule_coupon_aggregated_updated
mags_salesrule_coupon_usage
mags_salesrule_customer
mags_salesrule_customer_group
mags_salesrule_label
mags_salesrule_product_attribute
mags_salesrule_website
mags_search_query
mags_search_synonyms
mags_sendfriend_log
mags_sequence_creditmemo_0
mags_sequence_creditmemo_1
mags_sequence_invoice_0
mags_sequence_invoice_1
mags_sequence_order_0
mags_sequence_order_1
mags_sequence_shipment_0
mags_sequence_shipment_1
mags_session
mags_setup_module
mags_shipping_tablerate
mags_signifyd_case
mags_sitemap
mags_store
mags_store_group
mags_store_website
mags_tax_calculation
mags_tax_calculation_rate
mags_tax_calculation_rate_title
mags_tax_calculation_rule
mags_tax_class
mags_tax_order_aggregated_created
mags_tax_order_aggregated_updated
mags_temando_checkout_address
mags_temando_order
mags_temando_shipment
mags_theme
mags_theme_file
mags_translation
mags_ui_bookmark
mags_url_rewrite
mags_variable
mags_variable_value
mags_vault_payment_token
mags_vault_payment_token_order_payment_link
mags_weee_tax
mags_widget
mags_widget_instance
mags_widget_instance_page
mags_widget_instance_page_layout
mags_wishlist
mags_wishlist_item
mags_wishlist_item_option
```

Verifying cache and cache_tag database tables contents with above defaulted database prefix = `mags_ ` when SSH session variable `DBPREFIX='mags_'` was set

```
n98-magerun2 db:query "SELECT * from mags_cache limit 0,10;" --skip-root-check

n98-magerun2 db:query "SELECT * from mags_cache_tag limit 0,10;" --skip-root-check
```

Magento 2 database engine breakdown stats

```
echo "SELECT engine, count(*) tables, concat(round(sum(table_rows))) rows, concat(round(sum(data_length)/(1024*1024),2),'MB') data, concat(round(sum(index_length)/(1024*1024),2),'MB') idx, concat(round(sum(data_length+index_length)/(1024*1024),2),'MB') total_size, round(sum(index_length)/sum(data_length),2) idxfrac FROM information_schema.TABLES WHERE table_schema LIKE '$DBNAME' GROUP BY engine ORDER BY sum(data_length+index_length) DESC LIMIT 10;" | mysql -t
+--------+--------+------+--------+---------+------------+---------+
| engine | tables | rows | data   | idx     | total_size | idxfrac |
+--------+--------+------+--------+---------+------------+---------+
| InnoDB |    326 | 5535 | 5.59MB | 11.88MB | 17.47MB    |    2.12 |
| MEMORY |     14 | 0    | 0.00MB | 0.00MB  | 0.00MB     |    NULL |
+--------+--------+------+--------+---------+------------+---------+
```

default Magento modules' status

```
php $WEBROOT/bin/magento module:status
List of enabled modules:
Magento_Store
MageHost_PerformanceDashboard
Magento_Directory
Magento_AdvancedPricingImportExport
Magento_Config
Magento_Backend
Magento_Theme
Magento_Eav
Magento_Backup
Magento_Customer
Magento_AdminNotification
Magento_BundleImportExport
Magento_CacheInvalidate
Magento_Indexer
Magento_Cms
Magento_Security
Magento_CatalogImportExport
Magento_Rule
Magento_Cron
Magento_Catalog
Magento_Search
Magento_CatalogUrlRewrite
Magento_Widget
Magento_Quote
Magento_SalesSequence
Magento_Payment
Magento_CmsUrlRewrite
Magento_User
Magento_ConfigurableImportExport
Magento_Msrp
Magento_CatalogInventory
Magento_Contact
Magento_Cookie
Magento_Newsletter
Magento_CurrencySymbol
Magento_Sales
Magento_Integration
Magento_CustomerImportExport
Magento_Deploy
Magento_Developer
Magento_Dhl
Magento_Authorization
Magento_Downloadable
Magento_ImportExport
Magento_Bundle
Magento_Email
Magento_EncryptionKey
Magento_Fedex
Magento_GiftMessage
Magento_Checkout
Magento_GoogleAnalytics
Magento_Ui
Magento_GroupedImportExport
Magento_GroupedProduct
Magento_DownloadableImportExport
Magento_CatalogRule
Magento_InstantPurchase
Magento_Analytics
Magento_LayeredNavigation
Magento_Marketplace
Magento_MediaStorage
Magento_ConfigurableProduct
Magento_Multishipping
Magento_NewRelicReporting
Magento_Reports
Magento_OfflinePayments
Magento_SalesRule
Magento_PageCache
Magento_Vault
Magento_Paypal
Magento_Persistent
Magento_ProductAlert
Magento_ProductVideo
Magento_CheckoutAgreements
Magento_QuoteAnalytics
Magento_ReleaseNotification
Magento_Review
Magento_RequireJs
Magento_Shipping
Magento_ReviewAnalytics
Magento_Robots
Magento_Rss
Magento_CatalogRuleConfigurable
Magento_Captcha
Magento_SalesAnalytics
Magento_SalesInventory
Magento_OfflineShipping
Magento_ConfigurableProductSales
Magento_UrlRewrite
Magento_CatalogSearch
Magento_CustomerAnalytics
Magento_SendFriend
Magento_Wishlist
Magento_Signifyd
Magento_Sitemap
Magento_Authorizenet
Magento_Swagger
Magento_Swatches
Magento_SwatchesLayeredNavigation
Magento_Tax
Magento_TaxImportExport
Magento_GoogleAdwords
Magento_Translation
Magento_GoogleOptimizer
Magento_Ups
Magento_SampleData
Magento_CatalogAnalytics
Magento_Usps
Magento_Variable
Magento_Braintree
Magento_Version
Magento_Webapi
Magento_WebapiSecurity
Magento_Weee
Magento_CatalogWidget
Dotdigitalgroup_Email
Magento_WishlistAnalytics
Shopial_Facebook
Temando_Shipping

List of disabled modules:
None
```

Asynchronous Indexing

```
php $WEBROOT/bin/magento indexer:show-mode
Design Config Grid:                                Update by Schedule
Customer Grid:                                     Update by Schedule
Product Flat Data:                                 Update by Schedule
Category Flat Data:                                Update by Schedule
Category Products:                                 Update by Schedule
Product Categories:                                Update by Schedule
Product Price:                                     Update by Schedule
Product EAV:                                       Update by Schedule
Stock:                                             Update by Schedule
Catalog Rule Product:                              Update by Schedule
Catalog Product Rule:                              Update by Schedule
Catalog Search:                                    Update by Schedule
```

static file location

```
ls -lah pub/static/frontend/Magento/luma/en_US/css/
total 392K
drwxr-sr-x  2 root nginx 4.0K Mar 14 08:09 .
drwxr-sr-x 68 root nginx 4.0K Mar 14 08:09 ..
-rw-r--r--  1 root nginx 5.7K Mar 14 08:09 email.css
-rw-r--r--  1 root nginx 2.0K Mar 14 08:09 email-fonts.css
-rw-r--r--  1 root nginx 8.8K Mar 14 08:09 email-inline.css
-rw-r--r--  1 root nginx 1.2K Mar 14 08:09 print.css
-rw-r--r--  1 root nginx  64K Mar 14 08:09 styles-l.css
-rw-r--r--  1 root nginx 290K Mar 14 08:09 styles-m.css
```

```
ls -lah pub/static/_cache/merged/
total 2.4M
drwxr-sr-x 2 nginx nginx 4.0K Mar 14 12:13 .
drwxr-sr-x 3 nginx nginx 4.0K Mar 14 08:10 ..
-rw-r--r-- 1 nginx nginx 297K Mar 14 08:10 9de8797d0c615276fc794278a0af8df2.css
-rw-r--r-- 1 nginx nginx 647K Mar 14 12:13 9e492d36090b18d041a5555bf3f0fd98.css
-rw-r--r-- 1 nginx nginx 131K Mar 14 11:25 a814ec60c7b703d594ceda92980506f6.js
-rw-r--r-- 1 nginx nginx 104K Mar 14 08:10 c0b5661e7cfc4d3e29c8fc8495e4d56e.js
-rw-r--r-- 1 nginx nginx  99K Mar 14 11:09 ddce5b0a41e6dbae4b6f458053932955.js
-rw-r--r-- 1 nginx nginx 483K Mar 14 11:09 e6a662994948b56fa4f39e1f141aaed3.css
-rw-r--r-- 1 nginx nginx 669K Mar 14 11:25 fe7035d3b7b90d9b17c968ffd74cf349.css
```

```
ls -lah pub/static/frontend/Magento/luma/en_US/jquery            
total 1.2M
drwxr-sr-x  6 root nginx 4.0K Mar 14 08:08 .
drwxr-sr-x 68 root nginx 4.0K Mar 14 08:09 ..
drwxr-sr-x  5 root nginx 4.0K Mar 14 08:08 colorpicker
drwxr-sr-x  4 root nginx 4.0K Mar 14 08:08 editableMultiselect
drwxr-sr-x  6 root nginx 4.0K Mar 14 08:08 fileUploader
-rw-r--r--  1 root nginx 1.5K Mar 14 08:08 jquery.ba-hashchange.min.js
-rw-r--r--  1 root nginx 2.2K Mar 14 08:08 jquery.cookie.js
-rw-r--r--  1 root nginx 5.7K Mar 14 08:08 jquery.details.js
-rw-r--r--  1 root nginx 5.0K Mar 14 08:08 jquery.hoverIntent.js
-rw-r--r--  1 root nginx 6.1K Mar 14 08:08 jquery.metadata.js
-rw-r--r--  1 root nginx  17K Mar 14 08:08 jquery-migrate.js
-rw-r--r--  1 root nginx  95K Mar 14 08:08 jquery.min.js
-rw-r--r--  1 root nginx  24K Mar 14 08:08 jquery.mobile.custom.js
-rw-r--r--  1 root nginx 1.4K Mar 14 08:08 jquery.parsequery.js
-rw-r--r--  1 root nginx 6.9K Mar 14 08:08 jquery.storageapi.min.js
-rw-r--r--  1 root nginx 7.8K Mar 14 08:08 jquery.tabs.js
-rw-r--r--  1 root nginx 441K Mar 14 08:08 jquery-ui-1.9.2.js
-rw-r--r--  1 root nginx 427K Mar 14 08:08 jquery-ui.js
-rw-r--r--  1 root nginx  71K Mar 14 08:08 jquery-ui-timepicker-addon.js
-rw-r--r--  1 root nginx  53K Mar 14 08:08 jquery.validate.js
drwxr-sr-x  3 root nginx 4.0K Mar 14 08:08 jstree
```

![](/screenshots/magento-222-admin-01.png)

![](/screenshots/magento-222-admin-01b.png)

![](/screenshots/magento-222-admin-03.png)

![](/screenshots/magento-222-admin-04.png)

![](/screenshots/magento-222-admin-05.png)

![](/screenshots/magento-222-admin-06b.png)

Performance Dashboard Extension

![](/screenshots/magento-222-admin-07.png)

![](/screenshots/magento-222-admin-08.png)

## Magento 2 Upgrade

Upgrading Magento 2 can also be done via command line as per instructions outlined in [official documentation](http://devdocs.magento.com/guides/v2.2/comp-mgr/cli/cli-upgrade.html).

### Upgrading to Magento 2.2.3 CE.

Also added checks if Varnish & Redis cache are in play as per setup in this guide to restart Varnish Cache and flush Redis caches.

```
vhostname=magento.domain.com
WEBROOT="/home/nginx/domains/${vhostname}/public"
cd $WEBROOT
php $WEBROOT/bin/magento maintenance:enable
composer require magento/product-community-edition 2.2.3 --no-update
composer update
rm -rf $WEBROOT/var/cache/*
rm -rf $WEBROOT/var/page_cache/*
rm -rf $WEBROOT/generated/code/*
php $WEBROOT/bin/magento setup:upgrade
php $WEBROOT/bin/magento setup:static-content:deploy -f
php $WEBROOT/bin/magento setup:di:compile
#composer dump-autoload -o
php $WEBROOT/bin/magento cache:clean
php $WEBROOT/bin/magento cache:flush
php $WEBROOT/bin/magento cache:status
php $WEBROOT/bin/magento cache:disable
php $WEBROOT/bin/magento cache:enable
php $WEBROOT/bin/magento cache:flush full_page
php $WEBROOT/bin/magento maintenance:disable
php $WEBROOT/bin/magento deploy:mode:set production
chown -R nginx:nginx "/home/nginx/domains/${vhostname}/public"
service nginx restart
if [ -f "$(which varnishadm)" ]; then service varnish restart; fi
if [ -f "$(which redis-cli)" ]; then redis-cli -n 12 FLUSHALL; redis-cli -n 13 FLUSHALL; redis-cli -n 14 FLUSHALL; fi
```

specific commands' example output

```
composer update
Do not run Composer as root/super user! See https://getcomposer.org/root for details
Loading composer repositories with package information
Updating dependencies (including require-dev)
Package operations: 0 installs, 30 updates, 0 removals
  - Updating tedivm/jshrink (v1.2.0 => v1.3.0): Loading from cache
  - Updating magento/framework (101.0.2 => 101.0.3): Loading from cache
  - Updating magento/module-config (101.0.2 => 101.0.3): Loading from cache
  - Updating magento/module-backend (100.2.2 => 100.2.3): Loading from cache
  - Updating magento/module-ui (101.0.2 => 101.0.3): Loading from cache
  - Updating magento/module-variable (100.2.1 => 100.2.2): Loading from cache
  - Updating magento/module-catalog (102.0.2 => 102.0.3): Loading from cache
  - Updating magento/module-eav (101.0.1 => 101.0.2): Loading from cache
  - Updating magento/module-customer (101.0.2 => 101.0.3): Loading from cache
  - Updating magento/module-reports (100.2.2 => 100.2.3): Loading from cache
  - Updating magento/module-tax (100.2.2 => 100.2.3): Loading from cache
  - Updating magento/module-shipping (100.2.2 => 100.2.3): Loading from cache
  - Updating magento/module-directory (100.2.1 => 100.2.2): Loading from cache
  - Updating magento/module-theme (100.2.2 => 100.2.3): Loading from cache
  - Updating magento/module-cms (102.0.2 => 102.0.3): Loading from cache
  - Updating magento/module-checkout (100.2.2 => 100.2.3): Loading from cache
  - Updating magento/module-downloadable (100.2.1 => 100.2.2): Loading from cache
  - Updating magento/module-newsletter (100.2.1 => 100.2.2): Loading from cache
  - Updating magento/module-review (100.2.2 => 100.2.3): Loading from cache
  - Updating magento/module-import-export (100.2.2 => 100.2.3): Loading from cache
  - Updating magento/module-backup (100.2.1 => 100.2.2): Loading from cache
  - Updating magento/module-usps (100.2.0 => 100.2.1): Loading from cache
  - Updating magento/module-configurable-product (100.2.2 => 100.2.3): Loading from cache
  - Updating braintree/braintree_php (3.25.0 => 3.27.0): Loading from cache
  - Updating magento/module-braintree (100.2.2 => 100.2.3): Loading from cache
  - Updating ramsey/uuid (3.7.1 => 3.7.3): Loading from cache
  - Updating tubalmartin/cssmin (v4.1.0 => v4.1.1): Loading from cache
  - Updating colinmollenhour/cache-backend-redis (1.10.2 => 1.10.4): Loading from cache
  - Updating magento/magento2-base (2.2.2 => 2.2.3): Loading from cache
Package sjparkinson/static-review is abandoned, you should avoid using it. Use phpro/grumphp instead.
Writing lock file
Generating autoload files
```

```
php $WEBROOT/bin/magento setup:static-content:deploy -f

Deploy using quick strategy
frontend/Magento/blank/en_US            2108/2108           ============================ 100% %  13 secs             
adminhtml/Magento/backend/en_US         2105/2105           ============================ 100% %  15 secs             
frontend/Magento/luma/en_US             2124/2124           ============================ 100% %  15 secs

Execution time: 65.690301895142
```

```
php $WEBROOT/bin/magento setup:di:compile
Compilation was started.
Interception cache generation... 7/7 [============================] 100% 1 min 196.0 MiB
Generated code and dependency injection configuration successfully.
```

### Verify Magento Upgrade

Unfortunately, running into a problem in that all reported Magento 2 caches are reported as disabled in admin area GUI but command line says they're all enabled (set to 1) ? After a few cache flushes and switching between production and developer mode, and finally back to production mode, then the admin are GUI showed all caches as enabled ?

```
vhostname=magento.domain.com
WEBROOT="/home/nginx/domains/${vhostname}/public"
cd $WEBROOT
php $WEBROOT/bin/magento indexer:show-mode
php $WEBROOT/bin/magento deploy:mode:show
n98-magerun2 cache:list --skip-root-check
n98-magerun2 sys:check --skip-root-check
n98-magerun2 sys:info --skip-root-check
```

```
n98-magerun2 cache:list --skip-root-check

                       
  Magento Cache Types  
                       

+------------------------+--------------------------------+---------+
| Name                   | Type                           | Enabled |
+------------------------+--------------------------------+---------+
| config                 | Configuration                  | 1       |
| layout                 | Layouts                        | 1       |
| block_html             | Blocks HTML output             | 1       |
| collections            | Collections Data               | 1       |
| reflection             | Reflection Data                | 1       |
| db_ddl                 | Database DDL operations        | 1       |
| eav                    | EAV types and attributes       | 1       |
| customer_notification  | Customer Notification          | 1       |
| config_integration     | Integrations Configuration     | 1       |
| config_integration_api | Integrations API Configuration | 1       |
| full_page              | Page Cache                     | 1       |
| translate              | Translations                   | 1       |
| config_webservice      | Web Services Configuration     | 1       |
+------------------------+--------------------------------+---------+
```

```
n98-magerun2 cache:status --skip-root-check
Current status:
                        config: 1
                        layout: 1
                    block_html: 1
                   collections: 1
                    reflection: 1
                        db_ddl: 1
                           eav: 1
         customer_notification: 1
            config_integration: 1
        config_integration_api: 1
                     full_page: 1
                     translate: 1
             config_webservice: 1
```

```
n98-magerun2 sys:info --skip-root-check

                              
  Magento System Information  
                              

+------------------+-----------------------------------------------+
| name             | value                                         |
+------------------+-----------------------------------------------+
| Name             | Magento                                       |
| Version          | 2.2.3                                         |
| Edition          | Community                                     |
| Root             | /home/nginx/domains/magento.domain.com/public |
| Application Mode | production                                    |
| Session          | redis                                         |
| Crypt Key        | d45f000b87ce82******************              |
| Install Date     | Sun, 11 Mar 2018 20:40:12 +0000               |
| Cache Backend    | Cm_Cache_Backend_Redis                        |
| Vendors          | Magento, MageHost, Dotdigitalgroup, Temando   |
| Attribute Count  | 134                                           |
| Customer Count   | 0                                             |
| Category Count   | 2                                             |
| Product Count    | 0                                             |
+------------------+-----------------------------------------------+
```

## Magento 2 Page Speed Tests

Quick [WebPageTest.org test](https://www.webpagetest.org). Check out my guide on [How to use webpagetest.org for page load speed testing](https://community.centminmod.com/threads/how-to-use-webpagetest-org-for-page-load-speed-testing.13859/).

Default Magento 2.2.2 theme has up to 168 HTTP requests of which 144 are Javascript assets even with CSS and JS merged files enabled ! Javascript bundling is disabled by default.

![](/screenshots/wpt-magento-222-centminmod-nginx-default-theme-cable-dulles-01.png)

![](/screenshots/wpt-magento-222-centminmod-nginx-default-theme-cable-dulles-02.png)

![](/screenshots/wpt-magento-222-centminmod-nginx-default-theme-cable-dulles-03.png)

![](/screenshots/wpt-magento-222-centminmod-nginx-default-theme-cable-dulles-04.png)

## Magento 2 Redis Benchmarks

Update: added a [Magento 2 Bugs](https://github.com/centminmod/centminmod-magento2#magento-2-bugs) of which one bug already affects Magento 2.2.2 causing high MySQL / PHP related loads so may of affected benchmarks too. I have 428 pending cron sheduled jobs.

```
n98-magerun2 db:query "SELECT * FROM mags_cron_schedule WHERE status='pending';" --skip-root-check | wc -l
428
```

Redis server can be configured to default TCP mode or you can take advantage of using both TCP mode + Unix Socket modes so configure web apps that support Unix Socket to use that whike keaving web apps that only support TCP mode to use that. From my experience which Unix Sockets maybe faster, they don't scale in terms of higher concurrency load levels as TCP connections.

Quick benchmarks are below of Redis caching TCP vs Unix Socket using my custom forked [wrk-cmm](https://github.com/centminmod/wrk/tree/centminmod) load testing tool. Pay particular attention to latency distribution at 99% percentile mark and requests/sec and thread stats for Time To First Byte (TTFB) differences. Below wrk-cmm tests were lightl testing with 2 threads and 2 concurrent users over 10 second duration and testing for gzip encoded compressed HTTP requests.

Current Magento System Info

```
n98-magerun2 sys:info --skip-root-check

                              
  Magento System Information  
                              

+------------------+-----------------------------------------------+
| name             | value                                         |
+------------------+-----------------------------------------------+
| Name             | Magento                                       |
| Version          | 2.2.2                                         |
| Edition          | Community                                     |
| Root             | /home/nginx/domains/magento.domain.com/public |
| Application Mode | production                                    |
| Session          | redis                                         |
| Crypt Key        | d45f000b87ce82*******************             |
| Install Date     | Sun, 11 Mar 2018 20:40:12 +0000               |
| Cache Backend    | Cm_Cache_Backend_Redis                        |
| Vendors          | Magento, MageHost, Dotdigitalgroup, Temando   |
| Attribute Count  | 134                                           |
| Customer Count   | 0                                             |
| Category Count   | 2                                             |
| Product Count    | 0                                             |
+------------------+-----------------------------------------------+
```

Redis TCP Mode

```
redisserver=127.0.0.1
# lz4 or gzip
compresstype=lz4
port=6379
php $WEBROOT/bin/magento setup:config:set --cache-backend=redis --cache-backend-redis-server=$redisserver --cache-backend-redis-db=12 --cache-backend-redis-port=$port

php $WEBROOT/bin/magento setup:config:set --page-cache=redis --page-cache-redis-server=$redisserver --page-cache-redis-db=13 --page-cache-redis-compress-data=$compresstype --page-cache-redis-port=$port

php $WEBROOT/bin/magento setup:config:set --session-save=redis --session-save-redis-host=$redisserver --session-save-redis-log-level=3 --session-save-redis-db=14 --session-save-redis-compression-lib=$compresstype --session-save-redis-max-concurrency=4 --session-save-redis-first-lifetime=600 --session-save-redis-bot-first-lifetime=60 --session-save-redis-bot-lifetime=7200 --session-save-redis-min-lifetime=60 --session-save-redis-max-lifetime=2592000 --session-save-redis-break-after-frontend=60 --session-save-redis-break-after-adminhtml=60 --session-save-redis-port=$port

php $WEBROOT/bin/magento setup:static-content:deploy
php $WEBROOT/bin/magento setup:di:compile
composer dump-autoload -o
# ensure correct Centmin Mod Nginx permissions
chown -R nginx:nginx "/home/nginx/domains/${vhostname}/public"
```

```
domain=https://magento.domain.com
wrk-cmm -t2 -c2 -d10s --breakout -H 'Accept-Encoding: gzip' -s scripts/setup.lua --latency $domain
thread 1 created
thread 2 created
Running 10s test @ https://magento.domain.com
  2 threads and 2 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    31.15ms   10.16ms 158.71ms   97.59%
    Connect    14.00ms    5.32ms  17.76ms  100.00%
    TTFB       30.83ms   10.10ms 158.42ms   97.89%
    TTLB      316.32us  266.73us   4.59ms   97.25%
    Req/Sec    32.83      5.93    40.00     62.63%
  Latency Distribution
     50%   29.45ms
     75%   31.67ms
     90%   35.71ms
     99%   80.05ms
  655 requests in 10.06s, 4.64MB read
Requests/sec:     65.14
Transfer/sec:    472.44KB
thread 1 made 330 requests and got 328 responses
thread 2 made 328 requests and got 327 responses
```

Redis Unix socket Mode

```
redisserver=/var/run/redis/redis.sock
# lz4 or gzip
compresstype=lz4
port=0
php $WEBROOT/bin/magento setup:config:set --cache-backend=redis --cache-backend-redis-server=$redisserver --cache-backend-redis-db=12 --cache-backend-redis-port=$port

php $WEBROOT/bin/magento setup:config:set --page-cache=redis --page-cache-redis-server=$redisserver --page-cache-redis-db=13 --page-cache-redis-compress-data=$compresstype --page-cache-redis-port=$port

php $WEBROOT/bin/magento setup:config:set --session-save=redis --session-save-redis-host=$redisserver --session-save-redis-log-level=3 --session-save-redis-db=14 --session-save-redis-compression-lib=$compresstype --session-save-redis-max-concurrency=4 --session-save-redis-first-lifetime=600 --session-save-redis-bot-first-lifetime=60 --session-save-redis-bot-lifetime=7200 --session-save-redis-min-lifetime=60 --session-save-redis-max-lifetime=2592000 --session-save-redis-break-after-frontend=60 --session-save-redis-break-after-adminhtml=60 --session-save-redis-port=$port

php $WEBROOT/bin/magento setup:static-content:deploy
php $WEBROOT/bin/magento setup:di:compile
composer dump-autoload -o
# ensure correct Centmin Mod Nginx permissions
chown -R nginx:nginx "/home/nginx/domains/${vhostname}/public"
```

```
domain=https://magento.domain.com
wrk-cmm -t2 -c2 -d10s --breakout -H 'Accept-Encoding: gzip' -s scripts/setup.lua --latency $domain
thread 1 created
thread 2 created
Running 10s test @ https://magento.domain.com
  2 threads and 2 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    29.77ms    9.12ms 134.42ms   96.97%
    Connect     9.30ms    4.76ms  12.67ms  100.00%
    TTFB       29.46ms    9.08ms 134.08ms   96.97%
    TTLB      306.88us  212.53us   2.83ms   97.51%
    Req/Sec    34.26      6.07    40.00     97.47%
  Latency Distribution
     50%   28.09ms
     75%   30.42ms
     90%   33.51ms
     99%   75.62ms
  684 requests in 10.05s, 4.84MB read
Requests/sec:     68.04
Transfer/sec:    493.39KB
thread 1 made 342 requests and got 340 responses
thread 2 made 345 requests and got 344 responses
```

Magento 2 Redis caching still has to go through Nginx and PHP-FPM to be processed so there is still PHP-FPM load to deal with it seems. So looks like [Varnish Caching for Magento 2 full page caching](http://devdocs.magento.com/guides/v2.2/config-guide/varnish/config-varnish.html) would be a better option. Varnsih Cache added HTTP/2 support in 5.0 but isn't enabled out of box and Varnish HTTP/2 support is still work in progress with Varnish Cache 5.1, 5.2+ [details](https://varnish-cache.org/docs/5.2/whats-new/changes-5.1.html#progress-on-http-2-support).

During wrk-cmm load testing run top stats.

```
top -c
top - 10:04:41 up  1:14,  2 users,  load average: 1.15, 0.94, 0.82
Tasks: 119 total,  20 running,  99 sleeping,   0 stopped,   0 zombie
%Cpu0  : 81.6 us, 16.4 sy,  0.0 ni,  0.3 id,  0.0 wa,  0.0 hi,  1.6 si,  0.0 st
%Cpu1  : 79.5 us, 18.5 sy,  0.0 ni,  0.3 id,  0.0 wa,  0.0 hi,  1.7 si,  0.0 st
%Cpu2  : 77.6 us, 20.1 sy,  0.0 ni,  0.3 id,  0.0 wa,  0.0 hi,  2.0 si,  0.0 st
%Cpu3  : 76.9 us, 20.8 sy,  0.0 ni,  0.0 id,  0.0 wa,  0.0 hi,  2.3 si,  0.0 st
KiB Mem :  3881428 total,  2639780 free,   517272 used,   724376 buff/cache
KiB Swap:  4194300 total,  4194300 free,        0 used.  3022348 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                     
  760 redis     20   0  159424   7524   1552 R  32.6  0.2   0:25.46 redis-server                                                
11543 nginx     10 -10  157724  45128   2300 S  20.0  1.2   0:12.43 nginx                                                       
13033 nginx     20   0  918384  27584  14580 R  20.0  0.7   0:01.47 php-fpm                                                     
13027 nginx     20   0  918384  27576  14572 R  19.0  0.7   0:01.56 php-fpm                                                     
13035 nginx     20   0  918384  27580  14576 R  18.7  0.7   0:01.59 php-fpm                                                     
13032 nginx     20   0  918384  27580  14576 R  18.4  0.7   0:01.52 php-fpm                                                     
13021 nginx     20   0  918480  27688  14588 R  18.1  0.7   0:02.20 php-fpm                                                     
13031 nginx     20   0  918384  27580  14576 R  18.1  0.7   0:01.48 php-fpm                                                     
13026 nginx     20   0  918384  27580  14576 R  17.7  0.7   0:01.85 php-fpm                                                     
13029 nginx     20   0  918384  27576  14572 R  17.7  0.7   0:01.68 php-fpm                                                     
13030 nginx     20   0  918384  27580  14576 R  17.7  0.7   0:01.58 php-fpm                                                     
13036 nginx     20   0  918384  27580  14576 S  17.7  0.7   0:01.48 php-fpm                                                     
13022 nginx     20   0  918384  27580  14576 R  17.4  0.7   0:02.20 php-fpm                                                     
13023 nginx     20   0  918420  27620  14584 R  17.4  0.7   0:02.07 php-fpm                                                     
13025 nginx     20   0  918384  27576  14576 R  17.4  0.7   0:02.26 php-fpm                                                     
13034 nginx     20   0  918384  27580  14576 R  17.4  0.7   0:01.49 php-fpm                                                     
13024 nginx     20   0  918468  27684  14588 R  17.1  0.7   0:02.28 php-fpm                                                     
13028 nginx     20   0  918384  27576  14572 R  16.5  0.7   0:01.61 php-fpm                                                     
11545 nginx     10 -10  153628  44512   2284 S  16.1  1.1   0:08.60 nginx                                                       
11547 nginx     10 -10  157724  44672   2224 R  14.8  1.2   0:10.76 nginx                                                       
11544 nginx     10 -10  153628  44512   2264 R  10.6  1.1   0:09.73 nginx                                                       
13018 root      20   0  307660   8072   2360 S   6.8  0.2   0:00.57 wrk-cmm
```

#### Benchmarks with Redis caching disabled

Benchmarks with Redis caching disabled and switching caching to file based so only slightly less performance that Redis caching in terms of average throughput at 60 requests/s versus 65-68 requests/s with Redis caching. But 99% percentile latency response times were much slower with file based caching at 292 ms vs 75-80ms with Redis caching. Thread TTFB latency max was also much higher with file based caching at 357+ ms versus Redis cached thread TTFB latency at 134-158 ms.

```
wrk-cmm -t2 -c2 -d10s --breakout -H 'Accept-Encoding: gzip' -s scripts/setup.lua --latency $domain
thread 1 created
thread 2 created
Running 10s test @ https://magento.domain.com
  2 threads and 2 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    39.78ms   43.07ms 357.86ms   95.21%
    Connect    15.02ms    1.14ms  15.83ms  100.00%
    TTFB       39.35ms   42.99ms 357.27ms   95.22%
    TTLB      426.65us  153.00us   3.21ms   90.85%
    Req/Sec    31.85      7.32    40.00     64.74%
  Latency Distribution
     50%   29.49ms
     75%   31.51ms
     90%   39.24ms
     99%  292.20ms
  612 requests in 10.05s, 4.33MB read
Requests/sec:     60.92
Transfer/sec:    441.84KB
thread 1 made 315 requests and got 313 responses
thread 2 made 300 requests and got 299 responses
```

Set session-save to files based

```
php $WEBROOT/bin/magento setup:config:set --session-save=files
```

manually remove app/etc/env.php entries for Redis backend and full page caching

```
php $WEBROOT/bin/magento cache:flush
php $WEBROOT/bin/magento deploy:mode:set production
chown -R nginx:nginx "/home/nginx/domains/magento.domain.com/public"
```

```
n98-magerun2 sys:info --skip-root-check

                              
  Magento System Information  
                              

+------------------+-----------------------------------------------+
| name             | value                                         |
+------------------+-----------------------------------------------+
| Name             | Magento                                       |
| Version          | 2.2.2                                         |
| Edition          | Community                                     |
| Root             | /home/nginx/domains/magento.domain.com/public |
| Application Mode | production                                    |
| Session          | files                                         |
| Crypt Key        | d45f000b87ce82*******************             |
| Install Date     | Sun, 11 Mar 2018 20:40:12 +0000               |
| Cache Backend    | Cm_Cache_Backend_File                         |
| Vendors          | Magento, MageHost, Dotdigitalgroup, Temando   |
| Attribute Count  | 134                                           |
| Customer Count   | 0                                             |
| Category Count   | 2                                             |
| Product Count    | 0                                             |
+------------------+-----------------------------------------------+
```

file based full page caching

```
ls -lahrt var/page_cache/mage--3/
total 48K
drwxrwsr-x. 5 nginx nginx 4.0K Mar 13 13:29 ..
drwxrwxrwx  2 nginx nginx 4.0K Mar 13 13:29 .
-rw-rw----  1 nginx nginx  38K Mar 13 13:29 mage---495_FD9FFAEA25F37BB543E29CD5BBAB6A81BF0BA03
```

file based session caching

```
ls -lhrt var/session/ | tail -10 
-rw------- 1 nginx nginx 157 Mar 13 13:25 sess_lfqapeotn2u03plhj6h0kdqeb6
-rw------- 1 nginx nginx 157 Mar 13 13:26 sess_i6uamdosufdodse1l4olkrr5q0
-rw------- 1 nginx nginx 157 Mar 13 13:27 sess_9dc14co3me6eq7lkbjtgaf03im
-rw------- 1 nginx nginx 157 Mar 13 13:28 sess_kf1bgo6hgaletafdia83or4b4f
-rw------- 1 nginx nginx 157 Mar 13 13:29 sess_5cd5jvoouvn7hv9giro95t5h5s
-rw------- 1 nginx nginx 607 Mar 13 13:29 sess_fu48njb7u7eu4qj6qvjof8ktsv
-rw------- 1 nginx nginx 157 Mar 13 13:30 sess_2s8os32p7rmd74n7rn0e4doenf
-rw------- 1 nginx nginx 157 Mar 13 13:31 sess_ri4j18hb9vdvbav1pj4meg3l8p
-rw------- 1 nginx nginx 157 Mar 13 13:32 sess_rf82e1tn0ppp2597rbm3304v2i
-rw------- 1 nginx nginx 157 Mar 13 13:33 sess_lvupdn2j6m49mib8ra8nraft01
```

comparing `app/etc/env.php` file based caching versus `app/etc/env.php.redisset` with Redis session, backend and full page caching

```
diff -u app/etc/env.php app/etc/env.php.redisset
--- app/etc/env.php     2018-03-13 13:25:50.794181470 +0000
+++ app/etc/env.php.redisset    2018-03-13 05:30:56.948206383 +0000
@@ -37,7 +37,28 @@
   'MAGE_MODE' => 'production',
   'session' => 
   array (
-    'save' => 'files',
+    'save' => 'redis',
+    'redis' => 
+    array (
+      'host' => '127.0.0.1',
+      'port' => '6379',
+      'password' => '',
+      'timeout' => '2.5',
+      'persistent_identifier' => '',
+      'database' => '14',
+      'compression_threshold' => '2048',
+      'compression_library' => 'lz4',
+      'log_level' => '3',
+      'max_concurrency' => '4',
+      'break_after_frontend' => '60',
+      'break_after_adminhtml' => '60',
+      'first_lifetime' => '600',
+      'bot_first_lifetime' => '60',
+      'bot_lifetime' => '7200',
+      'disable_locking' => '0',
+      'min_lifetime' => '60',
+      'max_lifetime' => '2592000',
+    ),
   ),
   'cache_types' => 
   array (
@@ -60,6 +81,33 @@
   array (
     'date' => 'Sun, 11 Mar 2018 20:40:12 +0000',
   ),
+  'cache' => 
+  array (
+    'frontend' => 
+    array (
+      'default' => 
+      array (
+        'backend' => 'Cm_Cache_Backend_Redis',
+        'backend_options' => 
+        array (
+          'server' => '127.0.0.1',
+          'database' => '12',
+          'port' => '6379',
+        ),
+      ),
+      'page_cache' => 
+      array (
+        'backend' => 'Cm_Cache_Backend_Redis',
+        'backend_options' => 
+        array (
+          'server' => '127.0.0.1',
+          'database' => '13',
+          'port' => '6379',
+          'compress_data' => 'lz4',
+        ),
+      ),
+    ),
+  ),
   'system' => 
   array (
     'default' =>  
```

comparing `app/etc/env.php.redisset` with Redis session, backend and full page caching versus `app/etc/env.php` file based caching

```
diff -u app/etc/env.php.redisset app/etc/env.php
--- app/etc/env.php.redisset    2018-03-13 05:30:56.948206383 +0000
+++ app/etc/env.php     2018-03-13 13:25:50.794181470 +0000
@@ -37,28 +37,7 @@
   'MAGE_MODE' => 'production',
   'session' => 
   array (
-    'save' => 'redis',
-    'redis' => 
-    array (
-      'host' => '127.0.0.1',
-      'port' => '6379',
-      'password' => '',
-      'timeout' => '2.5',
-      'persistent_identifier' => '',
-      'database' => '14',
-      'compression_threshold' => '2048',
-      'compression_library' => 'lz4',
-      'log_level' => '3',
-      'max_concurrency' => '4',
-      'break_after_frontend' => '60',
-      'break_after_adminhtml' => '60',
-      'first_lifetime' => '600',
-      'bot_first_lifetime' => '60',
-      'bot_lifetime' => '7200',
-      'disable_locking' => '0',
-      'min_lifetime' => '60',
-      'max_lifetime' => '2592000',
-    ),
+    'save' => 'files',
   ),
   'cache_types' => 
   array (
@@ -81,33 +60,6 @@
   array (
     'date' => 'Sun, 11 Mar 2018 20:40:12 +0000',
   ),
-  'cache' => 
-  array (
-    'frontend' => 
-    array (
-      'default' => 
-      array (
-        'backend' => 'Cm_Cache_Backend_Redis',
-        'backend_options' => 
-        array (
-          'server' => '127.0.0.1',
-          'database' => '12',
-          'port' => '6379',
-        ),
-      ),
-      'page_cache' => 
-      array (
-        'backend' => 'Cm_Cache_Backend_Redis',
-        'backend_options' => 
-        array (
-          'server' => '127.0.0.1',
-          'database' => '13',
-          'port' => '6379',
-          'compress_data' => 'lz4',
-        ),
-      ),
-    ),
-  ),
   'system' => 
   array (
     'default' => 
```

## Magento 2 Varnish Cache

Magento folks recommend that Varnish Cache be used for full page caching instead of Redis full page caching as outlined at [Varnish Cache based full page caching](http://devdocs.magento.com/guides/v2.2/config-guide/varnish/config-varnish.html). We'll need to configure Varnish Cache 5.2.x with HTTP/2 support proxying to a non-HTTPS `magento.domain.com` backend. For terminating HTTPS connections using Centmin Mod Nginx HTTP/2 HTTPS as reverse proxy in front of Varnish Cache as Varnish Cache can't fully handle HTTPS itself. The work flow looks like this:

```
Visitor > Nginx HTTP/2 HTTPS Proxy > Varnish Cache HTTP/2 Enabled > Nginx non-HTTPS backend
```

### Step 1. Installing Varnish Cache on Centmin Mod LEMP Stack

Centmin Mod 123.09beta01 branch automatically adds the official Varnish Cache 4.1 branch YUM repo. So this needs to be disabled first.

```
yum-config-manager --disable varnishcache_varnish41 varnishcache_varnish41-source
```

Then Varnish Cache 5 YUM repository needs to be installed and setup as per outlined instructions [here](https://packagecloud.io/varnishcache/varnish5/install#bash-rpm).

```
curl -s https://packagecloud.io/install/repositories/varnishcache/varnish5/script.rpm.sh | sudo bash
```

List the Varnish Cache YUM package

```
yum list varnish --disablerepo=epel

Loaded plugins: fastestmirror, priorities, versionlock
Loading mirror speeds from cached hostfile
 * base: mirror.nodesdirect.com
 * extras: www.gtlib.gatech.edu
 * rpmforge: mirror.us.leaseweb.net
 * updates: mirror.trouble-free.net
27 packages excluded due to repository priority protections
Available Packages
varnish.x86_64        5.2.1-1.el7      varnishcache_varnish5
```

Install and setup Varnish Cache with HTTP/2 support enabled so it can read HTTP/2 in clear plain text with Varnish Cache listening off default 6081 port and setup Varnish backend port on Nginx non-HTTPS backend port 8686 which will be setup further below. I will be using Magento 2.2.2 exported Varnish default.vcl config file with slight modifications ([here](https://github.com/centminmod/centminmod-magento2/raw/master/vcl/varnish5/varnish-custom.vcl)) which I usually make to my Varnish Cache setups i.e. support Brotli compression when detected etc.

```
yum -y install varnish --disablerepo=epel
mkdir -p /etc/systemd/system/varnish.service.d
echo -en "[Service]\nLimitNOFILE=262144\nLimitSTACK=262144\n" > /etc/systemd/system/varnish.service.d/limit.conf
\cp -af /etc/varnish/varnish.params /etc/varnish/varnish.params.orig
\cp -af /etc/varnish/default.vcl /etc/varnish/default.vcl.orig
wget -O /etc/varnish/default.vcl https://github.com/centminmod/centminmod-magento2/raw/master/vcl/varnish5/varnish-custom.vcl
sed -i 's|port = "8080";|port = "8686";|' /etc/varnish/default.vcl
sed -i 's|/pub/health_check.php|/health_check.php|' /etc/varnish/default.vcl
sed -i '/DAEMON_OPTS/d' /etc/varnish/varnish.params
echo "DAEMON_OPTS=\"-p feature=+http2 -p thread_pools=2 -p thread_pool_add_delay=0.02 -p listen_depth=4096 -p lru_interval=2 -p cli_timeout=10 -p thread_pool_min=500 -p thread_pool_max=1500 -p thread_pool_timeout=300 -p tcp_fastopen=on -p http_resp_hdr_len=65536 -p http_resp_size=98304 -p workspace_backend=262144 -p workspace_client=262144 -p workspace_session=262144 -p workspace_thread=2048\"" >> /etc/varnish/varnish.params
cat /etc/systemd/system/varnish.service.d/limit.conf
cat /etc/varnish/varnish.params
systemctl daemon-reload
systemctl start varnish
systemctl status varnish
journalctl -u varnish --no-pager
```

```
varnishd -V
varnishd (varnish-5.2.1 revision 67e562482)
Copyright (c) 2006 Verdens Gang AS
Copyright (c) 2006-2015 Varnish Software AS
```

Below setup assumes we have configured Varnish Cache 5.x listening on port 6081 default using the Magento geenerated and exported Varnish 5 default.vcl. Which will eventually communicate with backend port for Nginx created vhost file `magento.domain.com.backend.conf` on port 8686 and defined in `/usr/local/nginx/conf/proxycache_map.conf` upstream.


within /etc/varnish/default.vcl 

```
backend default {
    .host = "localhost";
    .port = "8686";
```

### Step 2. Magento side adjustments

```
vhostname=magento.domain.com
WEBROOT="/home/nginx/domains/${vhostname}/public"
php $WEBROOT/bin/magento setup:store-config:set --base-url="http://${vhostname}/"
php $WEBROOT/bin/magento setup:store-config:set --base-url-secure="https://${vhostname}/"
php $WEBROOT/bin/magento cache:flush
```

check settings

```
n98-magerun2 sys:store:config:base-url:list --skip-root-check

                              
  Magento Stores - Base URLs  
                              

+----+---------+----------------------------+-----------------------------+
| id | code    | unsecure_baseurl           | secure_baseurl              |
+----+---------+----------------------------+-----------------------------+
| 1  | default | http://magento.domain.com/ | https://magento.domain.com/ |
+----+---------+----------------------------+-----------------------------+
```

```
php $WEBROOT/bin/magento config:show
web/seo/use_rewrites - 1
web/unsecure/base_url - http://magento.domain.com/
web/secure/base_url - https://magento.domain.com/
web/secure/use_in_adminhtml - 1
web/secure/use_in_frontend - 1
general/locale/code - en_US
general/locale/timezone - America/Chicago
general/region/display_all - 1
general/region/state_required - AT,BR,CA,CH,EE,ES,FI,HR,LT,LV,RO,US
currency/options/base - USD
currency/options/default - USD
currency/options/allow - USD
catalog/category/root_id - 2
catalog/frontend/flat_catalog_category - 1
catalog/frontend/flat_catalog_product - 1
analytics/subscription/enabled - 1
analytics/general/token - kE4qU4IDX5qXxDRCbtmZSu_mC1m0dGW011J1wMlAIfuCAz_jkWROOWuzB6_Cqd3lDHqYHP-0OU8CywoI3tWozA
system/full_page_cache/caching_application - 1
system/full_page_cache/ttl - 86400
system/full_page_cache/varnish/access_list - localhost
system/full_page_cache/varnish/backend_host - localhost
system/full_page_cache/varnish/backend_port - 8080
system/full_page_cache/varnish/grace_period - 300
dev/grid/async_indexing - 1
dev/js/merge_files - 1
dev/css/merge_css_files - 1
dev/debug/debug_logging - 0
sales_email/general/async_sending - 1
```

### Step 3. Create the Nginx non-HTTPS backend

This can be done by using the previously disabled Nginx vhost file `magento.domain.com.conf.disabled` and making a backend copy to edit

```
cd /usr/local/nginx/conf/conf.d
cp -a magento.domain.com.conf.disabled magento.domain.com.backend.conf
```

Modifying the vhost file to replicate the Magento rules and use a custom backend port 8686 which Varnish Cache will proxy and editing custom access_log and error_log paths. 

Also need to adjust `$scheme` to `https`

```
add_header Link "<$scheme://$http_host$request_uri>; rel=\"canonical\"" always;
```

```
add_header Link "<https://$http_host$request_uri>; rel=\"canonical\"" always;
```

fully adjusted `/usr/local/nginx/conf/conf.d/magento.domain.com.backend.conf` contents below:

```
server {
  listen 8686 backlog=2048 reuseport;
  server_name magento.domain.com;

# ngx_pagespeed & ngx_pagespeed handler
#include /usr/local/nginx/conf/pagespeed.conf;
#include /usr/local/nginx/conf/pagespeedhandler.conf;
#include /usr/local/nginx/conf/pagespeedstatslog.conf;

  #add_header X-Frame-Options SAMEORIGIN;
  #add_header X-Xss-Protection "1; mode=block" always;
  #add_header X-Content-Type-Options "nosniff" always;
  #add_header Referrer-Policy "strict-origin-when-cross-origin";

  # limit_conn limit_per_ip 16;
  # ssi  on;

  access_log /home/nginx/domains/magento.domain.com/log/access.backend.log combined buffer=256k flush=5m;
  error_log /home/nginx/domains/magento.domain.com/log/error.backend.log;

  #include /usr/local/nginx/conf/autoprotect/magento.domain.com/autoprotect-magento.domain.com.conf;
set $MAGE_ROOT /home/nginx/domains/magento.domain.com/public;
root $MAGE_ROOT/pub;
include /usr/local/nginx/conf/503include-main.conf;

index index.php;
autoindex off;
charset UTF-8;
error_page 404 403 = /errors/404.php;
#add_header "X-UA-Compatible" "IE=Edge";

# PHP entry point for setup application
location ~* ^/setup($|/) {
    root $MAGE_ROOT;
    location ~ ^/setup/index.php {
        include /usr/local/nginx/conf/php.conf;
    }

    location ~ ^/setup/(?!pub/). {
        deny all;
    }

    location ~ ^/setup/pub/ {
        add_header X-Frame-Options "SAMEORIGIN";
    }
}

# PHP entry point for update application
location ~* ^/update($|/) {
    root $MAGE_ROOT;

    location ~ ^/update/index.php {
        fastcgi_split_path_info ^(/update/index.php)(/.+)$;
        include /usr/local/nginx/conf/php_magento.conf;
    }

    # Deny everything but index.php
    location ~ ^/update/(?!pub/). {
        deny all;
    }

    location ~ ^/update/pub/ {
        add_header X-Frame-Options "SAMEORIGIN";
    }
}

location / {
    include /usr/local/nginx/conf/503include-only.conf;
    try_files $uri $uri/ /index.php$is_args$args;
}

location /pub/ {
    location ~ ^/pub/media/(downloadable|customer|import|theme_customization/.*\.xml) {
        deny all;
    }
    alias $MAGE_ROOT/pub/;
    add_header X-Frame-Options "SAMEORIGIN";
}

location /static/ {
    # Uncomment the following line in production mode
    expires max;

    # Remove signature of the static files that is used to overcome the browser cache
    location ~ ^/static/version {
        rewrite ^/static/(version\d*/)?(.*)$ /static/$2 last;
    }
    location ~* \.(ico|jpg|jpeg|png|gif|svg|js|css)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires 30d;

        if (!-f $request_filename) {
            rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
        }
    }
    location ~* \.(swf|eot|ttf|otf|woff|woff2)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires +1y;

        if (!-f $request_filename) {
            rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
        }
    }
    location ~* \.(zip|gz|gzip|bz2|csv|xml)$ {
        add_header Cache-Control "no-store";
        add_header X-Frame-Options "SAMEORIGIN";
        expires    off;

        if (!-f $request_filename) {
           rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
        }
    }
    if (!-f $request_filename) {
        rewrite ^/static/?(.*)$ /static.php?resource=$1 last;
    }
    add_header X-Frame-Options "SAMEORIGIN";
}

location /media/ {
    try_files $uri $uri/ /get.php$is_args$args;

    location ~ ^/media/theme_customization/.*\.xml {
        deny all;
    }
    location ~* \.(ico|jpg|jpeg|png|gif|svg|js|css)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires 30d;
        try_files $uri $uri/ /get.php$is_args$args;
    }
    location ~* \.(swf|eot|ttf|otf|woff|woff2)$ {
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        add_header X-Frame-Options "SAMEORIGIN";
        expires +1y;
        try_files $uri $uri/ /get.php$is_args$args;
    }
    location ~* \.(zip|gz|gzip|bz2|csv|xml)$ {
        add_header Cache-Control "no-store";
        add_header X-Frame-Options "SAMEORIGIN";
        expires    off;
        try_files $uri $uri/ /get.php$is_args$args;
    }
    add_header X-Frame-Options "SAMEORIGIN";
}

location /media/customer/ {
    deny all;
}

location /media/downloadable/ {
    deny all;
}

location /media/import/ {
    deny all;
}

# Deny cron and files with the obvious names. favorite entry points for hackers and script kiddie
location ~* ^/(cron|phpminiadmin|pma|sqlyog|adminer.+)\.php { deny all; }

# Deny auth and composer
location ~ (auth|package|composer)\.(json|lock)$ { deny all; }

# PHP entry point for main application
location ~ (index|get|static|report|404|503|health_check)\.php$ {
    try_files $uri =404;
    add_header X-Processing-Time $request_time always;
    add_header X-Request-ID $request_id always;
    add_header Strict-Transport-Security $mag_hstsheader always;
    add_header X-UA-Compatible 'IE=Edge,chrome=1';
    add_header Link "<$scheme://$http_host$request_uri>; rel=\"canonical\"" always;
    fastcgi_buffers 1024 4k;

    fastcgi_param  PHP_FLAG  "session.auto_start=off \n suhosin.session.cryptua=off";
    fastcgi_param  PHP_VALUE "memory_limit=4096M \n max_execution_time=3600";
    fastcgi_read_timeout 600s;
    fastcgi_connect_timeout 600s;
    include /usr/local/nginx/conf/503include-only.conf;
    include /usr/local/nginx/conf/php.conf;
}

# Banned locations (only reached if the earlier PHP entry point regexes don't match)
location ~* (\.php$|\.htaccess$|\.git) {
    deny all;
}

  include /usr/local/nginx/conf/pre-staticfiles-local-magento.domain.com.conf;
  include /usr/local/nginx/conf/pre-staticfiles-global.conf;
  #include /usr/local/nginx/conf/staticfiles.conf;
  #include /usr/local/nginx/conf/php.conf;
  #include /usr/local/nginx/conf/drop.conf;
  #include /usr/local/nginx/conf/errorpage.conf;
  include /usr/local/nginx/conf/vts_server.conf;
}
```

Restart Nginx server

```
service nginx restart
```

or via Centmin Mod command shortcut

```
ngxrestart
```

curl header test for `http://magento.domain.com:8686/`

```
curl -Ik http://magento.domain.com:8686/ 
HTTP/1.1 200 OK
Date: Wed, 14 Mar 2018 07:48:57 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
Vary: Accept-Encoding
Pragma: no-cache
Cache-Control: max-age=0, must-revalidate, no-cache, no-store
Expires: Tue, 14 Mar 2017 07:48:57 GMT
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
Server: nginx centminmod
X-Powered-By: centminmod
X-Processing-Time: 1.500
X-Request-ID: 90934314e90e859262f427cc05505057
X-UA-Compatible: IE=Edge,chrome=1
Link: <http://magento.domain.com:8686/>; rel="canonical"
```

### Step 4. Modifying Nginx HTTP/2 HTTPS vhost to be a reverse proxy to Varnish Cache backup on port 6081 listening port

Make a backup copying of live Nginx HTTP/2 HTTPS vhost file `magento.domain.com.ssl.conf` as `magento.domain.com.ssl.conf.backup-b4proxy`

```
cd /usr/local/nginx/conf/conf.d
cp -a magento.domain.com.ssl.conf magento.domain.com.ssl.conf.backup-b4proxy
```

Modifying `magento.domain.com.ssl.conf` vhost file to be a reverse proxy in front of Varnish Cache proxying to backend Varnish Cache listening port 6081

```
# Centmin Mod Getting Started Guide
# must read http://centminmod.com/getstarted.html
# For HTTP/2 SSL Setup
# read http://centminmod.com/nginx_configure_https_ssl_spdy.html

# redirect from www to non-www  forced SSL
# uncomment, save file and restart Nginx to enable
# if unsure use return 302 before using return 301
 server {
       listen   80;
       server_name magento.domain.com www.magento.domain.com;
       return 302 https://$server_name$request_uri;
 }

server {
  listen 443 ssl http2 backlog=2048 reuseport;
  server_name magento.domain.com www.magento.domain.com;

  ssl_dhparam /usr/local/nginx/conf/ssl/magento.domain.com/dhparam.pem;
  ssl_certificate      /usr/local/nginx/conf/ssl/magento.domain.com/magento.domain.com.crt;
  ssl_certificate_key  /usr/local/nginx/conf/ssl/magento.domain.com/magento.domain.com.key;
  include /usr/local/nginx/conf/ssl_include.conf;

  # cloudflare authenticated origin pull cert community.centminmod.com/threads/13847/
  #ssl_client_certificate /usr/local/nginx/conf/ssl/cloudflare/magento.domain.com/origin.crt;
  #ssl_verify_client on;
  http2_max_field_size 16k;
  http2_max_header_size 32k;
  # mozilla recommended
  ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS;
  ssl_prefer_server_ciphers   on;
  #add_header Alternate-Protocol  443:npn-spdy/3;

  # before enabling HSTS line below read centminmod.com/nginx_domain_dns_setup.html#hsts
  #add_header Strict-Transport-Security "max-age=31536000; includeSubdomains;";
  #add_header X-Frame-Options SAMEORIGIN;
  #add_header X-Xss-Protection "1; mode=block" always;
  #add_header X-Content-Type-Options "nosniff" always;
  #add_header Referrer-Policy "strict-origin-when-cross-origin";
  #spdy_headers_comp 5;
  ssl_buffer_size 1369;
  ssl_session_tickets on;
  
  # enable ocsp stapling
  #resolver 8.8.8.8 8.8.4.4 valid=10m;
  #resolver_timeout 10s;
  #ssl_stapling on;
  #ssl_stapling_verify on;
  #ssl_trusted_certificate /usr/local/nginx/conf/ssl/magento.domain.com/magento.domain.com-trusted.crt;  

# ngx_pagespeed & ngx_pagespeed handler
#include /usr/local/nginx/conf/pagespeed.conf;
#include /usr/local/nginx/conf/pagespeedhandler.conf;
#include /usr/local/nginx/conf/pagespeedstatslog.conf;

  # limit_conn limit_per_ip 16;
  # ssi  on;

  access_log /home/nginx/domains/magento.domain.com/log/access.log combined buffer=256k flush=5m;
  error_log /home/nginx/domains/magento.domain.com/log/error.log;

set $size_cachebypass 0;

if ($query_string ~* nocache) {
    set $size_cachebypass 1;
}

location / {
    include /usr/local/nginx/conf/proxycache_magento.domain.com.conf;
    proxy_pass http://proxy_backend;
    #proxy_bind $split_ip;
    #proxy_bind $remote_addr transparent;
}

location /check {
    return 200 "Hello from $hostname. You connected from $remote_addr:$remote_port to $server_addr:$server_port\n";
}
}
```

create `/usr/local/nginx/conf/proxycache_magento.domain.com.conf` with contents below setting `proxy_cache cache_magento.domain.com` as defined within the soon to be created `/usr/local/nginx/conf/proxycache-includes.conf` include file's `proxy_cache_path`. Microcaching is setup with `proxy_cache_valid` for HTTP 200, 302 and 404 status codes set to TTLS = 3 seconds.

```
add_header X-Cache-Status $upstream_cache_status;
proxy_cache cache_magento.domain.com;
proxy_cache_key $scheme$proxy_host$uri$is_args$args;
proxy_cache_lock on;
proxy_cache_lock_age 3s;
proxy_cache_lock_timeout 3s;
proxy_cache_revalidate on;
proxy_cache_valid 200 302 3s;
proxy_cache_valid 404 3s;
proxy_cache_min_uses 1;
proxy_ignore_headers Set-Cookie;
proxy_hide_header Set-Cookie;
proxy_no_cache $http_pragma $http_authorization $mag_nocacheuri $mag_mobiledevices $size_cachebypass;
proxy_cache_bypass $http_pragma $http_authorization $mag_nocacheuri $mag_mobiledevices $size_cachebypass;
proxy_cache_use_stale error timeout invalid_header updating http_500 http_502 http_503 http_504;

proxy_connect_timeout 59s;
proxy_send_timeout 600;
proxy_read_timeout 600;
proxy_buffer_size 8k;
proxy_buffers 4112 8k;
proxy_busy_buffers_size 8192k;
proxy_temp_file_write_size 8192k;
proxy_pass_header Set-Cookie;
proxy_redirect off;
proxy_http_version 1.1;
proxy_set_header Accept-Encoding '';
proxy_ignore_headers Cache-Control Expires;
proxy_set_header Referer $http_referer;
proxy_set_header Host $host;
proxy_hide_header X-Powered-By;
proxy_hide_header Vary;
proxy_set_header Cookie $http_cookie;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-Host $host;
proxy_set_header X-Forwarded-Server $host;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504 http_404;
```

### Step 5. Setup Nginx proxy include files to enable proxying

At the bottom of `/usr/local/nginx/conf/nginx.conf` just before the last include file before the closing curly bracket `}` add the 2 include files


```
 include /usr/local/nginx/conf/proxycache_map.conf;
 include /usr/local/nginx/conf/proxycache-includes.conf;
```

so it looks like below

```
 include /usr/local/nginx/conf/proxycache_map.conf;
 include /usr/local/nginx/conf/proxycache-includes.conf;
 include /usr/local/nginx/conf/conf.d/*.conf;
}
```

create these 2 files

* `/usr/local/nginx/conf/proxycache_map.conf`
* `/usr/local/nginx/conf/proxycache-includes.conf`

contents of `/usr/local/nginx/conf/proxycache_map.conf` initially sets up Nginx HTTP/2 HTTPS to reverse proxy to Nginx non-HTTPS backend on port 8686 `server localhost:8686 weight=1` to confirm Nginx reverse proxy works first and has varnish cache backend commented out and disabled for now `server localhost:6081 weight=1`

```
upstream proxy_backend {
    zone upstream_dynamic 512k;
    keepalive 256;
    #least_conn;
    # proxy to varnish cache backend
    #server localhost:6081 weight=1;
    # proxy to nginx backend
    server localhost:8686 weight=1;
}
    
map $request_uri $mag_nocacheuri {
       default              0;
    ~^/customer             1;
    ~^/register             1;
    ~^/login                1;
    ~^/validate-field       1;
    ~^/captcha              1;
    ~^/lost-password        1;
    ~^/cart/                1;
    ~^/checkout/            1;
    ~^/add-to-cart=         1;
    ~^/wp-admin/            1;
    ~^/my-account/          1;
    ~^/members/             1;
    ~^/signup/              1;
    ~^/join/                1;
    ~^/two-step             1;
}

map $http_user_agent $mag_mobiledevices {
    default                                      0;
    ~*(iPad|iPhone|Android|IEMobile|Blackberry)  1;
    "~*Firefox.*Mobile"                          1;
    "~*ipod.*mobile"                             1;
    "~*Opera\ Mini"                              1;
    "~*Opera\ Mobile"                            1;
    "~*Mobile"                                   1;
    "~*Tablet"                                   1;
    "~*Kindle"                                   1;
    "~*Windows\ Phone"                           1;
}

split_clients "$remote_addr$remote_port" $split_ip {
    4%  127.0.0.2;
    4%  127.0.0.3;
    4%  127.0.0.4;
    4%  127.0.0.5;
    4%  127.0.0.6;
    4%  127.0.0.7;
    4%  127.0.0.8;
    4%  127.0.0.9;
    4%  127.0.0.10;
    4%  127.0.0.11;
    4%  127.0.0.12;
    4%  127.0.0.13;
    4%  127.0.0.14;
    4%  127.0.0.15;
    4%  127.0.0.16;
    4%  127.0.0.17;
    4%  127.0.0.18;
    4%  127.0.0.19;
    4%  127.0.0.20;
    4%  127.0.0.21;
    4%  127.0.0.22;
    4%  127.0.0.23;
    4%  127.0.0.24;
    4%  127.0.0.25;
    *   127.0.0.26;
}
```

contents of `/usr/local/nginx/conf/proxycache-includes.conf` as we will setup some micro caching too on Nginx HTTP/2 HTTPS reverse proxy for just TTLS = 3 seconds

```
proxy_cache_path /home/nginx/proxycache/0-magento.domain.com-proxycache levels=1:2 keys_zone=cache_magento.domain.com:300m max_size=1200m inactive=2m manager_files=200 manager_threshold=200 manager_sleep=50 loader_files=200 loader_threshold=200 loader_sleep=50;
```

create `proxy_cache_path` directory

```
vhostname=magento.domain.com
mkdir -p "/home/nginx/proxycache/0-$vhostname-proxycache"
chown -R nginx:nginx "/home/nginx/proxycache/0-$vhostname-proxycache"
chmod 0770 "/home/nginx/proxycache/0-$vhostname-proxycache"
```

Restart Nginx server

```
service nginx restart
```

or via Centmin Mod command shortcut

```
ngxrestart
```

confirm Nginx reverse proxy works first with Nginx backend on port 8686 defined in `/usr/local/nginx/conf/proxycache_map.conf` upstream

```
curl -Ik https://magento.domain.com/
HTTP/1.1 200 OK
Date: Wed, 14 Mar 2018 08:36:23 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
Vary: Accept-Encoding
Pragma: no-cache
Cache-Control: max-age=0, must-revalidate, no-cache, no-store
Expires: Tue, 14 Mar 2017 08:16:40 GMT
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
X-Processing-Time: 2.297
X-Request-ID: ae327d25a26333a06778001b9f9ad1f5
X-UA-Compatible: IE=Edge,chrome=1
Link: <https://magento.domain.com/>; rel="canonical"
Server: nginx centminmod
X-Powered-By: centminmod
X-Cache-Status: HIT
```

confirm HTTP to HTTPS redirect works

```
curl -Ik http://magento.domain.com/
HTTP/1.1 302 Moved Temporarily
Date: Wed, 14 Mar 2018 08:49:58 GMT
Content-Type: text/html
Content-Length: 154
Connection: keep-alive
Location: https://magento.domain.com/
Server: nginx centminmod
X-Powered-By: centminmod
```

### Step 6. Switch Magento To Use Varnish Cache

As per [Magento 2.2.2 documentation](http://devdocs.magento.com/guides/v2.2/config-guide/varnish/config-varnish-magento.html), Varnish Cache needs to be set up for Magento full page caching.

This can be done via cmd line

```
vhostname=magento.domain.com
WEBROOT="/home/nginx/domains/${vhostname}/public"
IP=$(curl -4s ipinfo.io/ip)
cd $WEBROOT
n98-magerun2 config:set system/full_page_cache/caching_application 2 --skip-root-check
n98-magerun2 config:set system/full_page_cache/ttl 86400 --skip-root-check
n98-magerun2 config:set system/full_page_cache/varnish/access_list "localhost, 127.0.0.1, $IP" --skip-root-check
n98-magerun2 config:set system/full_page_cache/varnish/backend_port 8686 --skip-root-check
n98-magerun2 config:show system/full_page_cache/caching_application --skip-root-check
n98-magerun2 config:show system/full_page_cache/varnish/access_list --skip-root-check
n98-magerun2 config:show system/full_page_cache/varnish/backend_port --skip-root-check
php $WEBROOT/bin/magento cache:flush
```

Verify values set

```
n98-magerun2 config:show system/full_page_cache/caching_application --skip-root-check
n98-magerun2 config:show system/full_page_cache/ttl --skip-root-check
n98-magerun2 config:show system/full_page_cache/varnish/ --skip-root-check
```

```
n98-magerun2 config:show system/full_page_cache/caching_application --skip-root-check
2

n98-magerun2 config:show system/full_page_cache/ttl --skip-root-check
86400

n98-magerun2 config:show system/full_page_cache/varnish/ --skip-root-check
system/full_page_cache/varnish/access_list - localhost, 127.0.0.1, server_ipaddress
system/full_page_cache/varnish/backend_host - localhost
system/full_page_cache/varnish/backend_port - 8686
system/full_page_cache/varnish/grace_period - 300
```

This is what it looks like in Magento Admin area > Stores > Advanced > System > Full Page Cache

![](/screenshots/magento-222-admin-varnish-cache-01.png)

Above initial Nginx HTTP/2 HTTPS reverse proxy setup uses upstream defined as the Nginx backend on port 8686 defined in `/usr/local/nginx/conf/proxycache_map.conf` upstream. This needs to switched to using Varnish Cache on port 6081 as backend within upstream definition

So change and disable `server localhost:8686 weight=1` from

```
upstream proxy_backend {
    zone upstream_dynamic 512k;
    keepalive 256;
    #least_conn;
    # proxy to varnish cache backend
    #server localhost:6081 weight=1;
    # proxy to nginx backend
    server localhost:8686 weight=1;
}
```

to using Varnish Cache server as backend via `server localhost:6081 weight=1`

```
upstream proxy_backend {
    zone upstream_dynamic 512k;
    keepalive 256;
    #least_conn;
    # proxy to varnish cache backend
    server localhost:6081 weight=1;
    # proxy to nginx backend
    #server localhost:8686 weight=1;
}
```

Restart Nginx server

```
service nginx restart
```

or via Centmin Mod command shortcut

```
ngxrestart
```

Verify Magento 2.2.2 Varnish Caching with HTTP/2 setup

```
varnishadm param.show feature 
feature
        Value is: +http2
        Default is: none

        Enable/Disable various minor features.
           none                       Disable all features.

        Use +/- prefix to enable/disable individual feature:
           short_panic                Short panic message.
           wait_silo                  Wait for persistent silo.
           no_coredump                No coredumps.
           esi_ignore_https           Treat HTTPS as HTTP in
                                      ESI:includes
           esi_disable_xml_check      Don't check of body looks like
                                      XML
           esi_ignore_other_elements  Ignore non-esi XML-elements
           esi_remove_bom             Remove UTF-8 BOM
           https_scheme               Also split https URIs
           http2                      Support HTTP/2 protocol

```

```
varnishadm param.show
accept_filter              -
acceptor_sleep_decay       0.9 (default)
acceptor_sleep_incr        0.000 [seconds] (default)
acceptor_sleep_max         0.050 [seconds] (default)
auto_restart               on [bool] (default)
backend_idle_timeout       60.000 [seconds] (default)
ban_cutoff                 0 [bans] (default)
ban_dups                   on [bool] (default)
ban_lurker_age             60.000 [seconds] (default)
ban_lurker_batch           1000 (default)
ban_lurker_holdoff         0.010 [seconds] (default)
ban_lurker_sleep           0.010 [seconds] (default)
between_bytes_timeout      60.000 [seconds] (default)
cc_command                 exec gcc -std=gnu99 -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches   -m64 -mtune=generic -Wall -Werror -Wno-error=unused-result -pthread -fpic -shared -Wl,-x -o %o %s (default)
cli_buffer                 8k [bytes] (default)
cli_limit                  48k [bytes] (default)
cli_timeout                10.000 [seconds]
clock_skew                 10 [seconds] (default)
clock_step                 1.000 [seconds] (default)
connect_timeout            3.500 [seconds] (default)
critbit_cooloff            180.000 [seconds] (default)
debug                      none (default)
default_grace              10.000 [seconds] (default)
default_keep               0.000 [seconds] (default)
default_ttl                120.000 [seconds] (default)
feature                    +http2
fetch_chunksize            16k [bytes] (default)
fetch_maxchunksize         0.25G [bytes] (default)
first_byte_timeout         60.000 [seconds] (default)
gzip_buffer                32k [bytes] (default)
gzip_level                 6 (default)
gzip_memlevel              8 (default)
h2_rx_window_increment     1M [bytes] (default)
h2_rx_window_low_water     10M [bytes] (default)
http_gzip_support          on [bool] (default)
http_max_hdr               64 [header lines] (default)
http_range_support         on [bool] (default)
http_resp_hdr_len          64k [bytes]
http_resp_size             96k [bytes]
http_resp_hdr_len          48000b [bytes]
http_resp_size             32k [bytes] (default)
idle_send_timeout          60.000 [seconds] (default)
listen_depth               4096 [connections]
lru_interval               2.000 [seconds] (default)
max_esi_depth              5 [levels] (default)
max_restarts               4 [restarts] (default)
max_retries                4 [retries] (default)
nuke_limit                 50 [allocations] (default)
pcre_match_limit           10000 (default)
pcre_match_limit_recursion 20 (default)
ping_interval              3 [seconds] (default)
pipe_timeout               60.000 [seconds] (default)
pool_req                   10,100,10 (default)
pool_sess                  10,100,10 (default)
pool_vbo                   10,100,10 (default)
prefer_ipv6                off [bool] (default)
rush_exponent              3 [requests per request] (default)
send_timeout               600.000 [seconds] (default)
shm_reclen                 255b [bytes] (default)
shortlived                 10.000 [seconds] (default)
sigsegv_handler            on [bool] (default)
syslog_cli_traffic         on [bool] (default)
tcp_fastopen               on [bool]
tcp_keepalive_intvl        30.000 [seconds] (default)
tcp_keepalive_probes       3 [probes] (default)
tcp_keepalive_time         240.000 [seconds] (default)
thread_pool_add_delay      0.020 [seconds]
thread_pool_destroy_delay  1.000 [seconds] (default)
thread_pool_fail_delay     0.200 [seconds] (default)
thread_pool_max            1500 [threads]
thread_pool_min            500 [threads]
thread_pool_reserve        0 [threads] (default)
thread_pool_stack          48k [bytes] (default)
thread_pool_timeout        300.000 [seconds] (default)
thread_pools               2 [pools] (default)
thread_queue_limit         20 (default)
thread_stats_rate          10 [requests] (default)
timeout_idle               5.000 [seconds] (default)
timeout_linger             0.050 [seconds] (default)
vcc_allow_inline_c         off [bool] (default)
vcc_err_unref              on [bool] (default)
vcc_unsafe_path            on [bool] (default)
vcl_cooldown               600.000 [seconds] (default)
vcl_dir                    /etc/varnish:/usr/share/varnish/vcl (default)
vcl_path                   /etc/varnish:/usr/share/varnish/vcl (default)
vmod_dir                   /usr/lib64/varnish/vmods (default)
vmod_path                  /usr/lib64/varnish/vmods (default)
vsl_buffer                 4k [bytes] (default)
vsl_mask                   -VCL_trace,-WorkThread,-Hash,-VfpAcct (default)
vsl_reclen                 255b [bytes] (default)
vsl_space                  80M [bytes] (default)
vsm_free_cooldown          60.000 [seconds] (default)
vsm_space                  1M [bytes] (default)
workspace_backend          0.25M [bytes]
workspace_client           0.25M [bytes]
workspace_session          0.25M [bytes]
workspace_thread           2k [bytes] (default)
```

```
curl -Ik https://magento.domain.com/
HTTP/1.1 200 OK
Date: Wed, 14 Mar 2018 12:19:42 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
Vary: Accept-Encoding
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
X-Processing-Time: 0.099
X-Request-ID: 942205ca8c4b080ab7d4b1b422eca918
X-UA-Compatible: IE=Edge,chrome=1
Pragma: no-cache
Expires: -1
Cache-Control: no-store, no-cache, must-revalidate, max-age=0
Server: nginx centminmod
X-Powered-By: centminmod
X-Cache-Status: HIT
```

```
curl -Ik http://magento.domain.com:6081/
HTTP/1.1 200 OK
Date: Wed, 14 Mar 2018 12:12:44 GMT
Content-Type: text/html; charset=UTF-8
Vary: Accept-Encoding
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
X-Processing-Time: 1.101
X-Request-ID: 7897e4a2c619b4b62117de8dde990825
X-UA-Compatible: IE=Edge,chrome=1
Pragma: no-cache
Expires: -1
Cache-Control: no-store, no-cache, must-revalidate, max-age=0
Accept-Ranges: bytes
Connection: keep-alive
```

Quick benchmarks with Redis caching for sessions and backend and Varnish Cache for full page caching and a huge boost in performance compared to using Redis caching for full page caching jumping from 65-68 requests/sec and TTFB thread latency max at between 134-158ms.

first run without cache warm up with 569 requests/s and TTFB thread latency max at 581ms

```
domain=https://magento.domain.com
wrk-cmm -t2 -c2 -d10s --breakout -H 'Accept-Encoding: gzip' -s scripts/setup.lua --latency $domain
thread 1 created
thread 2 created
Running 10s test @ https://magento.domain.com
  2 threads and 2 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    11.03ms   53.21ms 581.69ms   97.69%
    Connect    12.42ms    9.14ms  28.15ms   55.00%
    TTFB       10.53ms   54.13ms 581.03ms   97.63%
    TTLB      709.65us    0.90ms  26.59ms   96.59%
    Req/Sec   294.81    119.88   620.00     64.95%
  Latency Distribution
     50%    2.97ms
     75%    3.94ms
     90%    7.20ms
     99%  356.80ms
  5736 requests in 10.08s, 39.96MB read
Requests/sec:    569.02
Transfer/sec:      3.96MB
thread 1 made 2680 requests and got 2678 responses
thread 2 made 3059 requests and got 3058 responses
```

second run with warmed up cache with 1,011 requests/second and TTFB thread latency max at 12.54ms

```
wrk-cmm -t2 -c2 -d10s --breakout -H 'Accept-Encoding: gzip' -s scripts/setup.lua --latency $domain
thread 1 created
thread 2 created
Running 10s test @ https://magento.domain.com
  2 threads and 2 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.99ms    0.90ms  13.00ms   91.47%
    Connect     7.28ms    5.85ms  17.71ms   52.00%
    TTFB        1.46ms  679.66us  12.54ms   81.05%
    TTLB      505.80us  583.70us   6.74ms   91.19%
    Req/Sec   508.07    126.29   720.00     66.50%
  Latency Distribution
     50%    1.49ms
     75%    2.49ms
     90%    2.76ms
     99%    5.49ms
  10124 requests in 10.01s, 70.53MB read
Requests/sec:   1011.06
Transfer/sec:      7.04MB
thread 1 made 5153 requests and got 5151 responses
```

Varnish cache stats after benchmark tests

```
varnishstat -1 | column -t
MGT.uptime                          238               1.00      Management     process      uptime
MGT.child_start                     1                 0.00      Child          process      started
MGT.child_exit                      0                 0.00      Child          process      normal         exit
MGT.child_stop                      0                 0.00      Child          process      unexpected     exit
MGT.child_died                      0                 0.00      Child          process      died           (signal)
MGT.child_dump                      0                 0.00      Child          process      core           dumped
MGT.child_panic                     0                 0.00      Child          process      panic
MAIN.summs                          748               3.13      stat           summ         operations
MAIN.uptime                         239               1.00      Child          process      uptime
MAIN.sess_conn                      166               0.69      Sessions       accepted
MAIN.sess_drop                      0                 0.00      Sessions       dropped
MAIN.sess_fail                      0                 0.00      Session        accept       failures
MAIN.client_req_400                 0                 0.00      Client         requests     received,      subject     to           400       errors
MAIN.client_req_417                 0                 0.00      Client         requests     received,      subject     to           417       errors
MAIN.client_req                     166               0.69      Good           client       requests       received
MAIN.cache_hit                      12                0.05      Cache          hits
MAIN.cache_hitpass                  0                 0.00      Cache          hits         for            pass.
MAIN.cache_hitmiss                  0                 0.00      Cache          hits         for            miss.
MAIN.cache_miss                     2                 0.01      Cache          misses
MAIN.backend_conn                   8                 0.03      Backend        conn.        success
MAIN.backend_unhealthy              0                 0.00      Backend        conn.        not            attempted
MAIN.backend_busy                   0                 0.00      Backend        conn.        too            many
MAIN.backend_fail                   0                 0.00      Backend        conn.        failures
MAIN.backend_reuse                  153               0.64      Backend        conn.        reuses
MAIN.backend_recycle                161               0.67      Backend        conn.        recycles
MAIN.backend_retry                  0                 0.00      Backend        conn.        retry
MAIN.fetch_head                     0                 0.00      Fetch          no           body           (HEAD)
MAIN.fetch_length                   159               0.67      Fetch          with         Length
MAIN.fetch_chunked                  2                 0.01      Fetch          chunked
MAIN.fetch_eof                      0                 0.00      Fetch          EOF
MAIN.fetch_bad                      0                 0.00      Fetch          bad          T-E
MAIN.fetch_none                     0                 0.00      Fetch          no           body
MAIN.fetch_1xx                      0                 0.00      Fetch          no           body           (1xx)
MAIN.fetch_204                      0                 0.00      Fetch          no           body           (204)
MAIN.fetch_304                      0                 0.00      Fetch          no           body           (304)
MAIN.fetch_failed                   0                 0.00      Fetch          failed       (all           causes)
MAIN.fetch_no_thread                0                 0.00      Fetch          failed       (no            thread)
MAIN.pools                          2                 .         Number         of           thread         pools
MAIN.threads                        1000              .         Total          number       of             threads
MAIN.threads_limited                0                 0.00      Threads        hit          max
MAIN.threads_created                1000              4.18      Threads        created
MAIN.threads_destroyed              0                 0.00      Threads        destroyed
MAIN.threads_failed                 0                 0.00      Thread         creation     failed
MAIN.thread_queue_len               0                 .         Length         of           session        queue
MAIN.busy_sleep                     0                 0.00      Number         of           requests       sent        to           sleep     on        busy  objhdr
MAIN.busy_wakeup                    0                 0.00      Number         of           requests       woken       after        sleep     on        busy  objhdr
MAIN.busy_killed                    0                 0.00      Number         of           requests       killed      after        sleep     on        busy  objhdr
MAIN.sess_queued                    0                 0.00      Sessions       queued       for            thread
MAIN.sess_dropped                   0                 0.00      Sessions       dropped      for            thread
MAIN.req_dropped                    0                 0.00      Requests       dropped
MAIN.n_object                       2                 .         object         structs      made
MAIN.n_vampireobject                0                 .         unresurrected  objects
MAIN.n_objectcore                   4                 .         objectcore     structs      made
MAIN.n_objecthead                   4                 .         objecthead     structs      made
MAIN.n_backend                      1                 .         Number         of           backends
MAIN.n_expired                      0                 .         Number         of           expired        objects
MAIN.n_lru_nuked                    0                 .         Number         of           LRU            nuked       objects
MAIN.n_lru_moved                    6                 .         Number         of           LRU            moved       objects
MAIN.losthdr                        0                 0.00      HTTP           header       overflows
MAIN.s_sess                         166               0.69      Total          sessions     seen
MAIN.s_pipe                         0                 0.00      Total          pipe         sessions       seen
MAIN.s_pass                         159               0.67      Total          pass-ed      requests       seen
MAIN.s_fetch                        161               0.67      Total          backend      fetches        initiated
MAIN.s_synth                        0                 0.00      Total          synthethic   responses      made
MAIN.s_req_hdrbytes                 169119            707.61    Request        header       bytes
MAIN.s_req_bodybytes                0                 0.00      Request        body         bytes
MAIN.s_resp_hdrbytes                67866             283.96    Response       header       bytes
MAIN.s_resp_bodybytes               2616180           10946.36  Response       body         bytes
MAIN.s_pipe_hdrbytes                0                 0.00      Pipe           request      header         bytes
MAIN.s_pipe_in                      0                 0.00      Piped          bytes        from           client
MAIN.s_pipe_out                     0                 0.00      Piped          bytes        to             client
MAIN.sess_closed                    166               0.69      Session        Closed
MAIN.sess_closed_err                0                 0.00      Session        Closed       with           error
MAIN.sess_readahead                 0                 0.00      Session        Read         Ahead
MAIN.sess_herd                      0                 0.00      Session        herd
MAIN.sc_rem_close                   0                 0.00      Session        OK           REM_CLOSE
MAIN.sc_req_close                   166               0.69      Session        OK           REQ_CLOSE
MAIN.sc_req_http10                  0                 0.00      Session        Err          REQ_HTTP10
MAIN.sc_rx_bad                      0                 0.00      Session        Err          RX_BAD
MAIN.sc_rx_body                     0                 0.00      Session        Err          RX_BODY
MAIN.sc_rx_junk                     0                 0.00      Session        Err          RX_JUNK
MAIN.sc_rx_overflow                 0                 0.00      Session        Err          RX_OVERFLOW
MAIN.sc_rx_timeout                  0                 0.00      Session        Err          RX_TIMEOUT
MAIN.sc_tx_pipe                     0                 0.00      Session        OK           TX_PIPE
MAIN.sc_tx_error                    0                 0.00      Session        Err          TX_ERROR
MAIN.sc_tx_eof                      0                 0.00      Session        OK           TX_EOF
MAIN.sc_resp_close                  0                 0.00      Session        OK           RESP_CLOSE
MAIN.sc_overload                    0                 0.00      Session        Err          OVERLOAD
MAIN.sc_pipe_overflow               0                 0.00      Session        Err          PIPE_OVERFLOW
MAIN.sc_range_short                 0                 0.00      Session        Err          RANGE_SHORT
MAIN.sc_req_http20                  0                 0.00      Session        Err          REQ_HTTP20
MAIN.sc_vcl_failure                 0                 0.00      Session        Err          VCL_FAILURE
MAIN.shm_records                    25737             107.69    SHM            records
MAIN.shm_writes                     1512              6.33      SHM            writes
MAIN.shm_flushes                    132               0.55      SHM            flushes      due            to          overflow
MAIN.shm_cont                       32                0.13      SHM            MTX          contention
MAIN.shm_cycles                     0                 0.00      SHM            cycles       through        buffer
MAIN.backend_req                    161               0.67      Backend        requests     made
MAIN.n_vcl                          1                 .         Number         of           loaded         VCLs        in           total
MAIN.n_vcl_avail                    1                 .         Number         of           VCLs           available
MAIN.n_vcl_discard                  0                 .         Number         of           discarded      VCLs
MAIN.vcl_fail                       0                 0.00      VCL            failures
MAIN.bans                           1                 .         Count          of           bans
MAIN.bans_completed                 1                 .         Number         of           bans           marked      'completed'
MAIN.bans_obj                       0                 .         Number         of           bans           using       obj.*
MAIN.bans_req                       0                 .         Number         of           bans           using       req.*
MAIN.bans_added                     1                 0.00      Bans           added
MAIN.bans_deleted                   0                 0.00      Bans           deleted
MAIN.bans_tested                    0                 0.00      Bans           tested       against        objects     (lookup)
MAIN.bans_obj_killed                0                 0.00      Objects        killed       by             bans        (lookup)
MAIN.bans_lurker_tested             0                 0.00      Bans           tested       against        objects     (lurker)
MAIN.bans_tests_tested              0                 0.00      Ban            tests        tested         against     objects      (lookup)
MAIN.bans_lurker_tests_tested       0                 0.00      Ban            tests        tested         against     objects      (lurker)
MAIN.bans_lurker_obj_killed         0                 0.00      Objects        killed       by             bans        (lurker)
MAIN.bans_lurker_obj_killed_cutoff  0                 0.00      Objects        killed       by             bans        for          cutoff    (lurker)
MAIN.bans_dups                      0                 0.00      Bans           superseded   by             other       bans
MAIN.bans_lurker_contention         0                 0.00      Lurker         gave         way            for         lookup
MAIN.bans_persisted_bytes           16                .         Bytes          used         by             the         persisted    ban       lists
MAIN.bans_persisted_fragmentation   0                 .         Extra          bytes        in             persisted   ban          lists     due       to    fragmentation
MAIN.n_purges                       0                 .         Number         of           purge          operations  executed
MAIN.n_obj_purged                   0                 .         Number         of           purged         objects
MAIN.exp_mailed                     2                 0.01      Number         of           objects        mailed      to           expiry    thread
MAIN.exp_received                   2                 0.01      Number         of           objects        received    by           expiry    thread
MAIN.hcb_nolock                     14                0.06      HCB            Lookups      without        lock
MAIN.hcb_lock                       2                 0.01      HCB            Lookups      with           lock
MAIN.hcb_insert                     2                 0.01      HCB            Inserts
MAIN.esi_errors                     0                 0.00      ESI            parse        errors         (unlock)
MAIN.esi_warnings                   0                 0.00      ESI            parse        warnings       (unlock)
MAIN.vmods                          1                 .         Loaded         VMODs
MAIN.n_gzip                         158               0.66      Gzip           operations
MAIN.n_gunzip                       165               0.69      Gunzip         operations
MAIN.n_test_gunzip                  0                 0.00      Test           gunzip       operations
LCK.backend.creat                   3                 0.01      Created        locks
LCK.backend.destroy                 0                 0.00      Destroyed      locks
LCK.backend.locks                   473               1.98      Lock           Operations
LCK.backend_tcp.creat               1                 0.00      Created        locks
LCK.backend_tcp.destroy             0                 0.00      Destroyed      locks
LCK.backend_tcp.locks               636               2.66      Lock           Operations
LCK.ban.creat                       1                 0.00      Created        locks
LCK.ban.destroy                     0                 0.00      Destroyed      locks
LCK.ban.locks                       183               0.77      Lock           Operations
LCK.busyobj.creat                   163               0.68      Created        locks
LCK.busyobj.destroy                 161               0.67      Destroyed      locks
LCK.busyobj.locks                   1726              7.22      Lock           Operations
LCK.cli.creat                       1                 0.00      Created        locks
LCK.cli.destroy                     0                 0.00      Destroyed      locks
LCK.cli.locks                       91                0.38      Lock           Operations
LCK.exp.creat                       1                 0.00      Created        locks
LCK.exp.destroy                     0                 0.00      Destroyed      locks
LCK.exp.locks                       13                0.05      Lock           Operations
LCK.hcb.creat                       1                 0.00      Created        locks
LCK.hcb.destroy                     0                 0.00      Destroyed      locks
LCK.hcb.locks                       4                 0.02      Lock           Operations
LCK.lru.creat                       2                 0.01      Created        locks
LCK.lru.destroy                     0                 0.00      Destroyed      locks
LCK.lru.locks                       8                 0.03      Lock           Operations
LCK.mempool.creat                   5                 0.02      Created        locks
LCK.mempool.destroy                 0                 0.00      Destroyed      locks
LCK.mempool.locks                   2108              8.82      Lock           Operations
LCK.objhdr.creat                    5                 0.02      Created        locks
LCK.objhdr.destroy                  0                 0.00      Destroyed      locks
LCK.objhdr.locks                    2023              8.46      Lock           Operations
LCK.pipestat.creat                  1                 0.00      Created        locks
LCK.pipestat.destroy                0                 0.00      Destroyed      locks
LCK.pipestat.locks                  0                 0.00      Lock           Operations
LCK.sess.creat                      166               0.69      Created        locks
LCK.sess.destroy                    166               0.69      Destroyed      locks
LCK.sess.locks                      502               2.10      Lock           Operations
LCK.vbe.creat                       1                 0.00      Created        locks
LCK.vbe.destroy                     0                 0.00      Destroyed      locks
LCK.vbe.locks                       134               0.56      Lock           Operations
LCK.vcapace.creat                   1                 0.00      Created        locks
LCK.vcapace.destroy                 0                 0.00      Destroyed      locks
LCK.vcapace.locks                   0                 0.00      Lock           Operations
LCK.vcl.creat                       1                 0.00      Created        locks
LCK.vcl.destroy                     0                 0.00      Destroyed      locks
LCK.vcl.locks                       355               1.49      Lock           Operations
LCK.vxid.creat                      1                 0.00      Created        locks
LCK.vxid.destroy                    0                 0.00      Destroyed      locks
LCK.vxid.locks                      15                0.06      Lock           Operations
LCK.waiter.creat                    2                 0.01      Created        locks
LCK.waiter.destroy                  0                 0.00      Destroyed      locks
LCK.waiter.locks                    504               2.11      Lock           Operations
LCK.wq.creat                        3                 0.01      Created        locks
LCK.wq.destroy                      0                 0.00      Destroyed      locks
LCK.wq.locks                        3419              14.31     Lock           Operations
LCK.wstat.creat                     1                 0.00      Created        locks
LCK.wstat.destroy                   0                 0.00      Destroyed      locks
LCK.wstat.locks                     387               1.62      Lock           Operations
MEMPOOL.busyobj.live                0                 .         In             use
MEMPOOL.busyobj.pool                10                .         In             Pool
MEMPOOL.busyobj.sz_wanted           65536             .         Size           requested
MEMPOOL.busyobj.sz_actual           65504             .         Size           allocated
MEMPOOL.busyobj.allocs              161               0.67      Allocations
MEMPOOL.busyobj.frees               161               0.67      Frees
MEMPOOL.busyobj.recycle             161               0.67      Recycled       from         pool
MEMPOOL.busyobj.timeout             1                 0.00      Timed          out          from           pool
MEMPOOL.busyobj.toosmall            0                 0.00      Too            small        to             recycle
MEMPOOL.busyobj.surplus             0                 0.00      Too            many         for            pool
MEMPOOL.busyobj.randry              0                 0.00      Pool           ran          dry
MEMPOOL.req0.live                   0                 .         In             use
MEMPOOL.req0.pool                   10                .         In             Pool
MEMPOOL.req0.sz_wanted              65536             .         Size           requested
MEMPOOL.req0.sz_actual              65504             .         Size           allocated
MEMPOOL.req0.allocs                 89                0.37      Allocations
MEMPOOL.req0.frees                  89                0.37      Frees
MEMPOOL.req0.recycle                89                0.37      Recycled       from         pool
MEMPOOL.req0.timeout                1                 0.00      Timed          out          from           pool
MEMPOOL.req0.toosmall               0                 0.00      Too            small        to             recycle
MEMPOOL.req0.surplus                0                 0.00      Too            many         for            pool
MEMPOOL.req0.randry                 0                 0.00      Pool           ran          dry
MEMPOOL.sess0.live                  0                 .         In             use
MEMPOOL.sess0.pool                  10                .         In             Pool
MEMPOOL.sess0.sz_wanted             512               .         Size           requested
MEMPOOL.sess0.sz_actual             480               .         Size           allocated
MEMPOOL.sess0.allocs                83                0.35      Allocations
MEMPOOL.sess0.frees                 83                0.35      Frees
MEMPOOL.sess0.recycle               83                0.35      Recycled       from         pool
MEMPOOL.sess0.timeout               1                 0.00      Timed          out          from           pool
MEMPOOL.sess0.toosmall              0                 0.00      Too            small        to             recycle
MEMPOOL.sess0.surplus               0                 0.00      Too            many         for            pool
MEMPOOL.sess0.randry                0                 0.00      Pool           ran          dry
LCK.sma.creat                       2                 0.01      Created        locks
LCK.sma.destroy                     0                 0.00      Destroyed      locks
LCK.sma.locks                       905               3.79      Lock           Operations
SMA.s0.c_req                        7                 0.03      Allocator      requests
SMA.s0.c_fail                       0                 0.00      Allocator      failures
SMA.s0.c_bytes                      42019             175.81    Bytes          allocated
SMA.s0.c_freed                      32768             137.10    Bytes          freed
SMA.s0.g_alloc                      5                 .         Allocations    outstanding
SMA.s0.g_bytes                      9251              .         Bytes          outstanding
SMA.s0.g_space                      268426205         .         Bytes          available
SMA.Transient.c_req                 448               1.87      Allocator      requests
SMA.Transient.c_fail                0                 0.00      Allocator      failures
SMA.Transient.c_bytes               3105627           12994.26  Bytes          allocated
SMA.Transient.c_freed               3105627           12994.26  Bytes          freed
SMA.Transient.g_alloc               0                 .         Allocations    outstanding
SMA.Transient.g_bytes               0                 .         Bytes          outstanding
SMA.Transient.g_space               0                 .         Bytes          available
MEMPOOL.req1.live                   0                 .         In             use
MEMPOOL.req1.pool                   10                .         In             Pool
MEMPOOL.req1.sz_wanted              65536             .         Size           requested
MEMPOOL.req1.sz_actual              65504             .         Size           allocated
MEMPOOL.req1.allocs                 84                0.35      Allocations
MEMPOOL.req1.frees                  84                0.35      Frees
MEMPOOL.req1.recycle                84                0.35      Recycled       from         pool
MEMPOOL.req1.timeout                0                 0.00      Timed          out          from           pool
MEMPOOL.req1.toosmall               0                 0.00      Too            small        to             recycle
MEMPOOL.req1.surplus                0                 0.00      Too            many         for            pool
MEMPOOL.req1.randry                 0                 0.00      Pool           ran          dry
MEMPOOL.sess1.live                  0                 .         In             use
MEMPOOL.sess1.pool                  10                .         In             Pool
MEMPOOL.sess1.sz_wanted             512               .         Size           requested
MEMPOOL.sess1.sz_actual             480               .         Size           allocated
MEMPOOL.sess1.allocs                83                0.35      Allocations
MEMPOOL.sess1.frees                 83                0.35      Frees
MEMPOOL.sess1.recycle               83                0.35      Recycled       from         pool
MEMPOOL.sess1.timeout               1                 0.00      Timed          out          from           pool
MEMPOOL.sess1.toosmall              0                 0.00      Too            small        to             recycle
MEMPOOL.sess1.surplus               0                 0.00      Too            many         for            pool
MEMPOOL.sess1.randry                0                 0.00      Pool           ran          dry
VBE.boot.default.happy              4503599627370495  .         Happy          health       probes
VBE.boot.default.bereq_hdrbytes     172088            720.03    Request        header       bytes
VBE.boot.default.bereq_bodybytes    0                 0.00      Request        body         bytes
VBE.boot.default.beresp_hdrbytes    76081             318.33    Response       header       bytes
VBE.boot.default.beresp_bodybytes   531365            2223.28   Response       body         bytes
VBE.boot.default.pipe_hdrbytes      0                 0.00      Pipe           request      header         bytes
VBE.boot.default.pipe_out           0                 0.00      Piped          bytes        to             backend
VBE.boot.default.pipe_in            0                 0.00      Piped          bytes        from           backend
VBE.boot.default.conn               0                 .         Concurrent     connections  to             backend
VBE.boot.default.req                161               0.67      Backend        requests     sent
```

#### Varnish Cache Flushing

To flush Magento full page cache within Varnish Cache command:

```
php $WEBROOT/bin/magento cache:flush full_page
```

```
php $WEBROOT/bin/magento cache:flush full_page
Flushed cache types:
full_page
```

## Magento Docs & Info Links

### community

* https://community.magento.com/
* https://magento.stackexchange.com/
* https://www.reddit.com/r/Magento/

### magento 2.2 docs

* http://devdocs.magento.com/guides/v2.2/release-notes/ReleaseNotes2.2.2CE.html
* http://devdocs.magento.com/guides/v2.2/comp-mgr/cli/cli-upgrade.html
* http://devdocs.magento.com/guides/v2.2/comp-mgr/bk-compman-upgrade-guide.html
* http://devdocs.magento.com/guides/v2.2/comp-mgr/trouble/cman/maint-mode.html
* http://devdocs.magento.com/guides/v2.2/config-guide/bootstrap/magento-modes.html
* http://devdocs.magento.com/guides/v2.2/config-guide/cli/config-cli-subcommands-mode.html
* http://devdocs.magento.com/guides/v2.2/install-gde/install-quick-ref.html
* http://devdocs.magento.com/guides/v2.2/install-gde/prereq/connect-auth.html
* http://devdocs.magento.com/guides/v2.2/install-gde/system-requirements2.html
* http://devdocs.magento.com/guides/v2.2/install-gde/prereq/integrator_install_ce.html
* http://devdocs.magento.com/guides/v2.2/config-guide/bk-config-guide.html
* http://devdocs.magento.com/guides/v2.2/install-gde/install/post-install-config.html
* http://devdocs.magento.com/guides/v2.2/install-gde/install/cli/install-cli-uninstall.html#instgde-install-magento-update
* http://devdocs.magento.com/guides/v2.2/install-gde/install/get-help.html
* http://devdocs.magento.com/guides/v2.2/config-guide/deployment/single-machine.html
* http://devdocs.magento.com/guides/v2.2/install-gde/install/post-install-umask.html
* http://devdocs.magento.com/guides/v2.2/config-guide/cli/config-cli-subcommands-static-view.html

### magento caching

* http://devdocs.magento.com/guides/v2.2/config-guide/redis/redis-pg-cache.html
* http://devdocs.magento.com/guides/v2.2/config-guide/varnish/config-varnish.html
* http://devdocs.magento.com/guides/v2.2/config-guide/varnish/config-varnish-configure.html
* http://devdocs.magento.com/guides/v2.2/config-guide/varnish/config-varnish-magento.html
* http://devdocs.magento.com/guides/v2.2/config-guide/varnish/use-multiple-varnish-cache.html
* http://devdocs.magento.com/guides/v2.2/config-guide/varnish/use-varnish-cache.html
* http://devdocs.magento.com/guides/v2.2/config-guide/varnish/use-varnish-cache-how.html
* http://devdocs.magento.com/guides/v2.2/config-guide/varnish/tshoot-varnish-503.html
* https://github.com/colinmollenhour/Cm_Cache_Backend_Redis
* https://github.com/colinmollenhour/magento-cache-benchmark
* https://support.hypernode.com/knowledgebase/varnish-on-magento2/

### magento search

* https://github.com/Smile-SA/elasticsuite

### magento sample data sets

* https://github.com/IvanChepurnyi/load-test-magento2-bootstrap ([setup.sh](https://github.com/IvanChepurnyi/load-test-magento2-bootstrap/blob/master/setup.sh))
* https://github.com/IvanChepurnyi/load-test-magento1-bootstrap

### nginx configs

* http://devdocs.magento.com/guides/v2.2/install-gde/prereq/nginx.html
* https://github.com/andrewhowdencom/magento2-1/blob/2.2.1-preview/nginx.conf.sample
* https://github.com/magento/magento2/pull/11690/files
* https://github.com/magenx/Magento-nginx-config

### backups

* http://devdocs.magento.com/guides/v2.2/install-gde/install/cli/install-cli-backup.html
* https://github.com/MagePsycho/magento2-db-code-backup-bash-script

### migration info & tools

* http://devdocs.magento.com/guides/v2.2/migration/bk-migration-guide.html
* https://serverguy.com/magento/migrate-magento-1-magento-2-guide/
* https://www.cloudways.com/blog/migrate-from-magento-1-to-magento-2/
* https://github.com/magento/data-migration-tool
* https://github.com/magento/code-migration

### admin tools

* https://github.com/netz98/n98-magerun2
* https://github.com/netz98/n98-magerun2/wiki
* http://magerun.net/
* https://www.atwix.com/magento-2/n98-magerun2-tool-overview/

### other links

* https://community.centminmod.com/threads/updated-magento-2-nginx-directives-for-magento-2-15.10975/
* https://www.goivvy.com/blog/speed-up-magento
* https://www.goivvy.com/blog/magento-2-upgrade

### other installers

* https://github.com/MagePsycho/magento2-installer-bash-script
* https://github.com/Zorennnyj/m2_install

### benchmarks & performance

* http://devdocs.magento.com/guides/v2.2/config-guide/cli/config-cli-subcommands-perf-data.html
* https://community.magento.com/t5/Hosting-Performance/bd-p/Hosting-Performance
* https://medium.com/@IvanChepurnyi/do-not-blindly-trust-benchmarks-analyse-them-d56168f77434
* https://www.keycdn.com/blog/speed-up-magento/
* https://community.magento.com/t5/Hosting-Performance/CSS-and-JS-loading-WAY-too-slow/m-p/61742#M235
* http://blog.mageworx.com/2018/02/5-ways-to-improve-magento-2-mobile-speed/
* https://servebolt.com/performance-magento-2-demo-store-new-york/
* https://amasty.com/blog/magento-1-2-performance-speed-php-5-php-7/

### magento profiler

* http://devdocs.magento.com/guides/v2.2/config-guide/bootstrap/mage-profiler.html
* https://amasty.com/knowledge-base/how-to-enable-magento-2-profiler.html
* https://aionhill.com/8-effective-ways-to-really-boost-magento-page-speed

### maintenance mode

* http://devdocs.magento.com/guides/v2.2/install-gde/install/cli/install-cli-subcommands-maint.html

```
MYIP=$(last -i | grep "still logged in" | awk '{print $3}' | uniq)
php $WEBROOT/bin/magento maintenance:allow-ips --none
php $WEBROOT/bin/magento maintenance:allow-ips $MYIP
php $WEBROOT/bin/magento maintenance:enable --ip=$MYIP
php $WEBROOT/bin/magento maintenance:status

php $WEBROOT/bin/magento maintenance:disable --ip=$MYIP
php $WEBROOT/bin/magento maintenance:status
```

### Magento 2 Blogs

* https://www.cloudways.com/blog/magento/magento-2/

### Magento 2 CLI Commands

* http://devdocs.magento.com/guides/v2.2/config-guide/cli/config-cli-subcommands-index.html
* http://devdocs.magento.com/guides/v2.2/config-guide/cli/config-cli-subcommands-config-mgmt-set.html
* http://devdocs.magento.com/guides/v2.2/config-guide/prod/config-reference-sens.html
* https://www.cloudways.com/blog/custom-cli-command-magento-2/

### Magento 2 Extensions

* https://www.weltpixel.com/magento-2-lazy-loading-enhanced.html
* https://marketplace.magento.com/innovo-module-cache-improve.html
* https://amasty.com/magento-full-page-cache.html
* https://github.com/AmastyLtd/improved-search

## Magento 2 Bugs

* [Varnish related issues listed](https://github.com/magento/magento2/issues?page=4&q=varnish+sort%3Aupdated-desc)
* https://github.com/magento/magento2/issues/11002
* https://github.com/magento/magento2/issues/6401
* https://github.com/magento/magento2/issues/9156