Recipes for running servers with SMF.

In this page:

mongrel_cluster
===

This SMF profile is for running Rails apps with mongrel_cluster. It allows you to create an instance for each different Rails app you are running.

Make a copy of the instance tags for each mongrel_cluster app and fill in the details (replace INSTANCE_NAME, /PATH/TO/RAILS/APP, USERNAME, and GROUP).

Start the mongrel cluster instance with



```
$ sudo svcadm enable mongrel/cluster:INSTANCE_NAME
```







```
Recipe
<?xml version='1.0'?>
<!DOCTYPE service_bundle SYSTEM '/usr/share/lib/xml/dtd/service_bundle.dtd.1'>
<service_bundle type='manifest' name='mongrel/cluster'>
<service name='network/mongrel/cluster' type='service' version='0'>
<dependency
name='fs'
grouping='require_all'
restart_on='none'
type='service'>
<service_fmri value='svc:/system/filesystem/local'/>
</dependency>
<dependency
name='net'
grouping='require_all'
restart_on='none'
type='service'>
<service_fmri value='svc:/network/loopback'/>
<!-- uncomment the following line if you are on an L+ Accelerator since /home is mounted through nfs -->
<!-- <service_fmri value='svc:/network/nfs/client'/> -->
</dependency>
<dependent
name='mongrel_multi-user'
restart_on='none'
grouping='optional_all'>
<service_fmri value='svc:/milestone/multi-user'/>
</dependent>
<exec_method
name='start'
type='method'
exec='/opt/csw/bin/mongrel_rails cluster::start'
timeout_seconds='60'>
</exec_method>
<exec_method
name='stop'
type='method'
exec=':kill'
timeout_seconds='60'>
</exec_method>
<!--
Define instances
-->
<instance name='INSTANCE_NAME' enabled='false'>
<method_context working_directory='/PATH/TO/RAILS/APP'>
<method_credential user='USERNAME' group='GROUP' />
<method_environment>
<envvar name="PATH" value="/usr/bin:/bin:/opt/csw/bin" />
</method_environment>
</method_context>
</instance>
<instance name='SECOND_INSTANCE_NAME' enabled='false'>
<method_context working_directory='/PATH/TO/OTHER/RAILS/APP'>
<method_credential user='USERNAME2' group='GROUP2' />
<method_environment>
<envvar name="PATH" value="/usr/bin:/bin:/opt/csw/bin" />
</method_environment>
</method_context>
</instance>
</service>
</service_bundle>
```



CouchDB
===

This recipe assumes that you have installed CouchDB and are running it with the couchdb user/group.

This manifest is for CouchDB 0.7.3/0.8.0 and assumes that you have ICU installed in /opt/local and SpiderMonkey installed in /opt/local/spidermonkey. Change the LD_LIBRARY_PATH envar name to suit your configuration.



```
Recipe
<?xml version='1.0'?>

<!DOCTYPE service_bundle SYSTEM '/usr/share/lib/xml/dtd/service_bundle.dtd.1'>

<service_bundle type='manifest' name='export'>
<service name='application/database/couch' type='service' version='0'>
<create_default_instance enabled='false'/>
<single_instance/>
<dependency name='network' grouping='require_all' restart_on='none' type='service'>
<service_fmri value='svc:/milestone/network:default'/>
</dependency>
<dependency name='filesystem-local' grouping='require_all' restart_on='none' type='service'>
<service_fmri value='svc:/system/filesystem/local:default'/>
</dependency>
<exec_method name='start' type='method' exec='/opt/local/bin/couchdb -b -o /opt/local/var/run/couchdb/couchdb.stdout -e /opt/local/var/run/couchdb/couchdb.stderr -p /opt/local/var/run/couchdb/couchdb.pid' timeout_seconds='300'>
<method_context>
<method_credential user='couchdb' group='couchdb'/>
<method_environment>
<envvar name="HOME" value="/opt/local/var/lib/couchdb" />
<envvar name="LD_LIBRARY_PATH" value="/opt/local/lib:/opt/local/spidermonkey/lib" />
</method_environment>
</method_context>
</exec_method>
<exec_method name='stop' type='method' exec='/opt/local/bin/couchdb -d -p /opt/local/var/run/couchdb/couchdb.pid' timeout_seconds='300'>
<method_context>
<method_credential user='couchdb' group='couchdb'/>
<method_environment>
<envvar name="HOME" value="/opt/local/var/lib/couchdb" />
<envvar name="LD_LIBRARY_PATH" value="/opt/local/lib:/opt/local/spidermonkey/lib" />
</method_environment>
</method_context>
</exec_method>
<stability value='Evolving'/>
<template>
<common_name>
<loctext xml:lang='C'>Apache CouchDB</loctext>
</common_name>
<documentation>
<manpage title='couchdb' section='1M'/>
<doc_link name='incubator.apache.org' uri='http://incubator.apache.org/couchdb/'/>
</documentation>
</template>
</service>
</service_bundle>
```



Nginx
===



```
Recipe
<?xml version='1.0'?>
<!DOCTYPE service_bundle SYSTEM '/usr/share/lib/xml/dtd/service_bundle.dtd.1'>
<service_bundle type='manifest' name='export'>
<service name='network/nginx' type='service' version='0'>
<create_default_instance enabled='true'/>
<single_instance/>
<dependency name='fs' grouping='require_all' restart_on='none' type='service'>
<service_fmri value='svc:/system/filesystem/local'/>
</dependency>
<dependency name='net' grouping='require_all' restart_on='none' type='service'>
<service_fmri value='svc:/network/loopback'/>
</dependency>
<dependent name='nginx' restart_on='none' grouping='optional_all'>
<service_fmri value='svc:/milestone/multi-user'/>
</dependent>
<exec_method name='start' type='method' exec='/opt/local/nginx/sbin/nginx -c /opt/local/nginx/conf/nginx.conf' timeout_seconds='60'>
<method_context working_directory='/var/log'>
<method_credential user='root' group='root'/>
<method_environment>
<envvar name='PATH' value='/usr/bin:/bin:/opt/csw/bin:/opt/local/bin'/>
</method_environment>
</method_context>
</exec_method>
<exec_method name='stop' type='method' exec=':kill' timeout_seconds='60'>
<method_context/>
</exec_method>
</service>
</service_bundle>

```






----
資料來源：[Joyent Wiki](http://wiki.joyent.com/display/www/Documentation+Home)
