# Couchbase Nagios/Check_MK Plugin
A plugin to monitor Couchbase REST APIs and forward metrics to Nagios/CheckMK.

It is intended to be a standalone Nagios/Check_MK plugin as well as a reference for how to interact with the Couchbase REST APIs when building plugins for other systems.

## Requirements
* Python requests module
* PyYAML

For Nagios/Check_MK:
* send_nsca via the nsca or nsca-ng packages

## Configuration
This plugin is configured to submit passive checks to Nagios/Check_MK via NSCA.  The set of metrics to monitor and thresholds for each metric are locally configured in the check_couchbase.yaml file.

### Minimum configuration
Make sure the following properties match your environment:
* couchbase_host
* couchbase_user
* couchbase_password
* monitor_type
* monitor_host
* monitor_port
* nagios_nsca_path
* nagios_nsca_config

Note that the user executing this script must have read access to /etc/send_nsca.cfg.

### Nagios services
You must have services configured in Nagios in order for the passive check results to be accepted.  The plugin allows you to customize the service description to match your Nagios configuration.  

Service descriptions are built in the following format:
{prefix}.{cluster name}.{label}.{metric description}

The configuration file documents how the service description is built and how to customize it.

The --dump-services flag can be used to output the Nagios service descriptions this script will use.

### Check_MK services
You must enable NSCA in your Check_MK configuration https://mathias-kettner.com/cms_wato_services.html#passive_checks

You must have host and services configured in Check_MK in order for the passive check results to be accepted. The plugin allows you to customize the service description to match your Check_MK configuration.

Custom check services built in the following format:
{prefix}.{cluster name}.{label}.{metric description}

The configuration file documents how the service description is built and how to customize it.

The --dump-services flag can be used to output the Check_MK service descriptions this script will use.

Alternatively you can create a full output of the configuration file used in CheckMK.

The --dump-checkmk flag can be used to output the Check_MK custom_checks configuration and placed in /omd/sites/<site>/etc/check_mk/conf.d/<custom_name>.mk. Then reload Check_MK with "cmk -O".

References:

Passive Checks: https://mathias-kettner.com/cms_wato_services.html#passive_checks

Configuration files: https://mathias-kettner.com/checkmk_configfiles.html#Further+configuration+files+in+conf.d

Using your own plugin: https://mathias-kettner.com/cms_wato_services.html#active_checks


### Couchbase metrics
This plugin comes pre-configured with a set of best-practice metrics.  It will be necessary to update the metric thresholds to reflect your Couchbase environment.

## Usage
``` 
usage: check_couchbase.py [options] -c CONFIG_FILE

optional arguments:
  -h, --help            show this help message and exit
  -a, --all-nodes       Return metrics for all cluster nodes
  -c CONFIG_FILE, --config CONFIG_FILE
                        Path to the check_couchbase YAML file
  -d, --dump-services   Print service descriptions and exit
  -k, --dump-checkmk    Print custom_checks configuration for Check_MK
  -n, --no-metrics      Do not send metrics to the monitoring host
  -v, --verbose         Enable debug logging to console
  -C COUCHBASE_HOST, --couchbase-host COUCHBASE_HOST
                        Override the configured Couchbase host
  -U COUCHBASE_USER, --couchbase-user COUCHBASE_USER
                        Override the configured Couchbase admin username
  -P COUCHBASE_PASSWORD, --couchbase-password COUCHBASE_PASSWORD
                        Override the configured Couchbase admin password
  -H MONITOR_HOST, --monitor-host MONITOR_HOST
                        Override the configured monitoring host
  -M MONITOR_TYPE, --monitor-type MONITOR_TYPE
                        Override the configured monitoring system type
```

This script should be executed via cron, a Nagios NRPE check, or as a command line rule in Check_MK (Classical active and passive monitoring checks).
