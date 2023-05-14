---
title: "Myth about hard-coded 'hdfs' superuser in Hadoop"
date: "2015-01-16"
categories:
  - "hadoop"
  - "infosec"
---

I often hear about the hard-coded 'hdfs' superuser in Hadoop clusters, and
various challenges around managing it in scenarios when there is more than one
team in the same organization using Hadoop in their projects.

I think it's very important to mention that there is no hardcoded 'hdfs'
superuser in Hadoop. Name Node just gives admin rights to the system user name
which started its process. So if you are starting Name Node as root (please
don't do this), your superuser name will be 'root'. If you are starting it as
'namenode', this will make 'namenode' user a superuser.

Here's what
[HDFS Permissions Guide](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
says about this (quoting entire 'Super-User' section):

> The super-user is the user with the same identity as name node process itself.
> Loosely, if you started the name node, then you are the super-user. The
> super-user can do anything in that permissions checks never fail for the
> super-user. There is no persistent notion of who was the super-user; when the
> name node is started the process identity determines who is the super-user for
> now. The HDFS super-user does not have to be the super-user of the name node
> host, nor is it necessary that all clusters have the same super-user. Also, an
> experimenter running HDFS on a personal workstation, conveniently becomes that
> installation's super-user without any configuration.
>
> In addition, the administrator my identify a distinguished group using a
> configuration parameter. If set, members of this group are also super-users.

And that's just HDFS admin. For other components of Hadoop ecosystem, they all
have their own admin users, but some in default configurations will allow other
components' admin users manage them.

I guess this myth exists because the default system user name used to start HDFS
daemons by majority of automated Hadoop installations is 'hdfs'.

(and of course don't forget about
[**dfs.permissions.superusergroup** and **dfs.cluster.administrators**](http://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#Configuration_Parameters
"HDFS Permissions Guide"))
