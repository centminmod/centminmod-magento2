# Magento 2.2.2 Install Guide For Centmin Mod Nginx LEMP Stacks

## Table Of Contents

* [Introduction](https://github.com/centminmod/centminmod-magento2#introduction)
* [Centmin Mod 123.09beta01 LEMP Stack Install](https://github.com/centminmod/centminmod-magento2#centmin-mod-12309beta01-lemp-stack-install)
* [Magento 2.2.2 Installation](https://github.com/centminmod/centminmod-magento2#magento-222-installation)
* [Magento 2 Page Speed Tests](https://github.com/centminmod/centminmod-magento2#magento-2-page-speed-tests)
* [Magento 2 Redis Benchmarks](https://github.com/centminmod/centminmod-magento2#magento-2-redis-benchmarks)
* [Magento Docs & Info Links](https://github.com/centminmod/centminmod-magento2#magento-docs--info-links)

## Introduction

This Magento 2.2.2 installation guide is written by George Liu (eva2000) and is **provided as is without any support** and was written specifically for Magento 2.2.2 installs on [Centmin Mod 123.09beta01](https://centminmod.com/) or higher branch LEMP stack installations running Nginx 1.13 branch with PHP-FPM 7.1.x latest (Magento 2.2.2 doesn't support PHP 7.2 yet) and MariaDB 10.1.31 MySQL server. Magento 2.2.2 install below will be via Composer download and native Magento binary command tool and is 100% pure SSH command line install. FTP/SFTP is not used at all. The below Magento install steps could be used to write an automated script to install Magento 2.2.2 on Centmin Mod LEMP stacks purely from SSH command line. Maybe something planned on offer to [Centmin Mod Premium Members](https://community.centminmod.com/premium/).

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

check memory_limit

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

![](/screenshots/magento-222-admin-01.png)

![](/screenshots/magento-222-admin-01b.png)

![](/screenshots/magento-222-admin-03.png)

![](/screenshots/magento-222-admin-04.png)

![](/screenshots/magento-222-admin-05.png)

![](/screenshots/magento-222-admin-06b.png)

Performance Dashboard Extension

![](/screenshots/magento-222-admin-07.png)

![](/screenshots/magento-222-admin-08.png)

## Magento 2 Page Speed Tests

Quick [WebPageTest.org test](https://www.webpagetest.org). Check out my guide on [How to use webpagetest.org for page load speed testing](https://community.centminmod.com/threads/how-to-use-webpagetest-org-for-page-load-speed-testing.13859/).

Default Magento 2.2.2 theme has up to 168 HTTP requests of which 144 are Javascript assets even with CSS and JS merged files enabled ! Javascript bundling is disabled by default.

![](/screenshots/wpt-magento-222-centminmod-nginx-default-theme-cable-dulles-01.png)

![](/screenshots/wpt-magento-222-centminmod-nginx-default-theme-cable-dulles-02.png)

![](/screenshots/wpt-magento-222-centminmod-nginx-default-theme-cable-dulles-03.png)

![](/screenshots/wpt-magento-222-centminmod-nginx-default-theme-cable-dulles-04.png)

## Magento 2 Redis Benchmarks

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

## Magento Docs & Info Links

### community

* https://community.magento.com/
* https://magento.stackexchange.com/
* https://www.reddit.com/r/Magento/

### magento 2.2 docs

* http://devdocs.magento.com/guides/v2.2/release-notes/ReleaseNotes2.2.2CE.html
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
* https://github.com/colinmollenhour/Cm_Cache_Backend_Redis
* https://github.com/colinmollenhour/magento-cache-benchmark

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
* https://www.cloudways.com/blog/custom-cli-command-magento-2/

### Magento 2 Extensions

* https://www.weltpixel.com/magento-2-lazy-loading-enhanced.html
* https://marketplace.magento.com/innovo-module-cache-improve.html
* https://amasty.com/magento-full-page-cache.html
* https://github.com/AmastyLtd/improved-search