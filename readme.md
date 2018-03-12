# Magento 2.2.2 Install Guide For Centmin Mod Nginx LEMP Stacks

## Table Of Contents

* [Introduction](https://github.com/centminmod/centminmod-magento2#introduction)
* [Centmin Mod 123.09beta01 LEMP Stack Install](https://github.com/centminmod/centminmod-magento2#centmin-mod-12309beta01-lemp-stack-install)
* [Magento 2.2.2 Installation](https://github.com/centminmod/centminmod-magento2#magento-222-installation)
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

To install Magento via Composer, you need to register an account to get the Authentication Keys from [Magento Markplace](https://marketplace.magento.com/) and log in and go to Account Profile, Market Place tab and Access Keys to generate a set of Authentictaion Keys. In step 2 next, tou will use the public key to assign to SSH variable `magentouser_key`and private key to assign to SSH variable `magentouser_pass`.

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
* set memory_limit to 1024MB

```
echo -e "memory_limit=1024M\nopcache.memory_consumption=512\nopcache.enable_cli=1\nsession.gc_maxlifetime=86400" > /etc/centminmod/php.d/zzz-zendopcache.ini
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
Used memory => 21488896
Free memory => 515382016
Wasted memory => 0
Interned Strings Used memory => 413712
Interned Strings Free memory => 7974896
Cached scripts => 0
Cached keys => 0
Max keys => 65407
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
opcache.max_accelerated_files => 65407 => 65407
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
* Async indexing of grids
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
redisserver=127.0.0.1
# lz4 or gzip
compresstype=lz4
php $WEBROOT/bin/magento setup:config:set --cache-backend=redis --cache-backend-redis-server=$redisserver --cache-backend-redis-db=12

php $WEBROOT/bin/magento setup:config:set --page-cache=redis --page-cache-redis-server=$redisserver --page-cache-redis-db=13 --page-cache-redis-compress-data=$compresstype

php $WEBROOT/bin/magento setup:config:set --session-save=redis --session-save-redis-host=$redisserver --session-save-redis-log-level=3 --session-save-redis-db=14 --session-save-redis-compression-lib=$compresstype --session-save-redis-max-concurrency=4 --session-save-redis-first-lifetime=600 --session-save-redis-bot-first-lifetime=60 --session-save-redis-bot-lifetime=7200 --session-save-redis-min-lifetime=60 --session-save-redis-max-lifetime=2592000 --session-save-redis-break-after-frontend=60 --session-save-redis-break-after-adminhtml=60

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
php $WEBROOT/bin/magento setup:static-content:deploy
php $WEBROOT/bin/magento setup:di:compile

echo "admin url = https://${vhostname}/${BACKEND_FRONTNAME}"
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
# ensure correct Centmin Mod Nginx permissions
chown -R nginx:nginx "/home/nginx/domains/${vhostname}/public"
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

![](/screenshots/magento-222-admin-01.png)

![](/screenshots/magento-222-admin-01b.png)

![](/screenshots/magento-222-admin-03.png)

![](/screenshots/magento-222-admin-04.png)

![](/screenshots/magento-222-admin-05.png)

![](/screenshots/magento-222-admin-06.png)

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

### magento search

* https://github.com/Smile-SA/elasticsuite

### magento sample data sets

* https://github.com/IvanChepurnyi/load-test-magento2-bootstrap ([setup.sh](https://github.com/IvanChepurnyi/load-test-magento2-bootstrap/blob/master/setup.sh))
* https://github.com/IvanChepurnyi/load-test-magento1-bootstrap

### nginx configs

* https://github.com/andrewhowdencom/magento2-1/blob/2.2.1-preview/nginx.conf.sample
* https://github.com/magento/magento2/pull/11690/files
* https://github.com/magenx/Magento-nginx-config

### backups

* http://devdocs.magento.com/guides/v2.2/install-gde/install/cli/install-cli-backup.html
* https://github.com/MagePsycho/magento2-db-code-backup-bash-script

### migration tools

* http://devdocs.magento.com/guides/v2.2/migration/bk-migration-guide.html
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

* https://community.magento.com/t5/Hosting-Performance/bd-p/Hosting-Performance
* https://medium.com/@IvanChepurnyi/do-not-blindly-trust-benchmarks-analyse-them-d56168f77434
* https://www.keycdn.com/blog/speed-up-magento/
* https://community.magento.com/t5/Hosting-Performance/CSS-and-JS-loading-WAY-too-slow/m-p/61742#M235

### magento profiler

* http://devdocs.magento.com/guides/v2.2/config-guide/bootstrap/mage-profiler.html
* https://amasty.com/knowledge-base/how-to-enable-magento-2-profiler.html

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