[![Build Status](https://travis-ci.org/Phil-Friderici/puppet-module-afs.png?branch=master)](https://travis-ci.org/Phil-Friderici/puppet-module-afs)

# puppet-module-afs #
===

Puppet Module to manage OpenAFS

The module installs and configures OpenAFS.

# Compatability #

This module provides OS default values for these OSfamilies:

 * RedHat
 * Solaris
 * Suse

For other OSfamilies support, please specify all parameters which defaults to 'USE_DEFAULTS'.


# Parameters #

afs_cellserverdb
----------------
String with content of the file $afs_config_path/CellServDB.
This file will be ignored if the default value is not changed.

- *Default*: undef


afs_cell
--------
String with content of the file $afs_config_path/ThisCell.
This file will be ignored if the default value is not changed.

- *Default*: undef


afs_config_path
---------------
Path to the OpenAFS config directory.

- *Default*: 'USE_DEFAULTS', based on OS platform


afs_cron_job_content
--------------------
String with OpenAFS cron job command. Example: '[ -x /afs_maintenance.sh ] && /afs_maintenance.sh'
Do not use multi line content when $afs_cron_job_interval is set to 'specific'.

- *Default*: undef


afs_cron_job_hour
-----------------
Integer between 0 and 23. The hour at which to run the cron job.
If set to <undef> it will become '*' at creation time.

- *Default*: undef


afs_cron_job_interval
---------------------
String to specify when to run the cron job.

Set to 'specific' to create cron jobs. It uses $afs_cron_job_minute/hour/weekday/month/monthday
to specify when to run the cron job.

On systems that support fragment files in /etc/cron.(hourly|daily|weekly|monthly) you can use
'hourly' ,'daily', 'weekly' and 'monthly' to create a file in the according directory.

This module can only create or change cron jobs, there is no housekeeping support to delete them.


afs_cron_job_minute
-------------------
Integer between 0 and 59. The minute at which to run the cron job.
ACHTUNG: If set to <undef> it will become '*' at creation time.
Default to 42 for sanity reasons.

- *Default*: 42


afs_cron_job_monthday
---------------------
Integer between 1 and 31. The day of the month on which to run the cron job.
If set to <undef> it will become '*' at creation time.

- *Default*: undef


afs_cron_job_month
------------------
Integer between 1 and 12. The month of the year in which to run the cron job.
If set to <undef> it will become '*' at creation time.

- *Default*: undef


afs_cron_job_weekday
--------------------
Integer between 0 and 7. The weekday on which to run the cron job. 0 and 7 are both for Sundays.
If set to <undef> it will become '*' at creation time.

- *Default*: undef


afs_suidcells
-------------
String with content of the file $afs_config_path/SuidCells.
This file will be ignored if the default value is not changed.

- *Default*: undef


cache_path
----------
Path to cache storage when using disk cache.
Recommended: use a dedicated partition as disk cache.

- *Default*: 'USE_DEFAULTS', based on OS platform


cache_size
----------
Cache size in kb.
'1000000' = 1GB is a good value for single user systems
'4000000' = 4GB is a good value for terminal servers
ACHTUNG!: real occupied space can be 5% larger, due to metadata

- *Default*: '1000000'


config_client_args
------------------
AFSD_ARGS / parameters to be passed to AFS daemon while starting.
Since 1.6.x the afs-client has integrated auto-tuning. So specifying more options for tuning should only be applied after monitoring the system.
Candidates for tuning: -stat, -volumes

- *Default*: '-dynroot -afsdb -daemons 6 -volumes 1000'


config_client_dkms
------------------
Boolean to control the AFS kernel module handling via DKMS or the openafs start-script.
At the moment only available on RHEL platform. It will be ignored on other platforms.

- *Default*: 'USE_DEFAULTS', based on OS platform


config_client_path
------------------
Path to the openafs-client configuration file.

- *Default*: 'USE_DEFAULTS', based on OS platform


config_client_update
--------------------
Boolean trigger for the selfupdating routine in the init script.
If set to true, the init skript checks for updated AFS packages in the available repositories and installs them.

- *Default*: false


create_symlinks
---------------
Create symlinks for convenient access to AFS structure. Path and target are taken from hash in $links.

- *Default*: false


init_script
-----------
Filename for the init script.

- *Default*: 'USE_DEFAULTS', based on OS platform


init_template
-------------
Name of the template file to be used for $init_script.

- *Default*: 'USE_DEFAULTS', based on OS platform


links
-----
Hash of path and target to create symlinks from if $create_links is true.

- *Default*: undef

Hiera example:
<pre>
afs::symlinks:
  'app':
    path:   '/app'
    target: '/afs/some/path/app'
  'env':
    path:   '/env'
    target: '/afs/some/path/env'
</pre>


package_adminfile
-----------------
Solaris specific: string with adminfile.

- *Default*: undef


package_name
------------
Array of needed OpenAFS packages.

- *Default*: 'USE_DEFAULTS', based on OS platform


package_source
--------------
Solaris specific: string with package source.

- *Default*: undef


service_provider
----------------
Solaris specific (mostly): string with provider for service.
Should be undef for Linux and 'init' for Solaris.

- *Default*: undef


# Solaris specific #
For usage on Solaris, you will need to define these variables:

$package_adminfile, $package_source and $service_provider

If you want to create a cron job, please set $afs_cron_job_interval to 'specific' and choose your values for $afs_cron_job_hour and $afs_cron_job_minute.

Hiera example:
<pre>
afs::afs_cron_job_interval: 'specific'
afs::afs_cron_job_content:  '[ -x /afs_maintenance.sh ] && /afs_maintenance.sh'
afs::afs_cron_job_hour:     '2'
afs::afs_cron_job_minute:   '42'

afs::package_adminfile:     '/path/to/adminfile/noask'
afs::package_source:        '/path/to/package/openafs-x.x.x-x-Sol10'
afs::service_provider:      'init'
</pre>
