---
title: "hadoop.security.auth_to_local examples"
date: "2015-04-15"
categories:
  - "hadoop"
  - "infosec"
---

In my previous post "An important Hadoop security configuration parameter you
may have missed" I was talking about importance of the
**hadoop.security.auth_to_local** configuration parameter and promised to
provide some solutions using this parameter.

I want to focus on a couple of practical use examples in this post, and if you
want to learn more about this, here are links to the existing documentation:

- By Hortonworks:  
  [http://hortonworks.com/blog/fine-tune-your-apache-hadoop-security-settings/](http://hortonworks.com/blog/fine-tune-your-apache-hadoop-security-settings/)  
  (IMHO the best documentation to date for Hadoop)
- By Cloudera:  
  [http://www.cloudera.com/content/cloudera/en/documentation/cdh4/v4-3-1/CDH4-Security-Guide/cdh4sg\_topic\_19.htm](http://www.cloudera.com/content/cloudera/en/documentation/cdh4/v4-3-1/CDH4-Security-Guide/cdh4sg_topic_19.html)
- This parameter is actually similar to Kerberos' `auth_to_local`:  
  [http://web.mit.edu/kerberos/krb5-1.12/doc/admin/conf\_files/krb5\_conf.html](http://web.mit.edu/kerberos/krb5-1.12/doc/admin/conf_files/krb5_conf.html)

The overall idea is there are two major types of principals coming in your
Hadoop ecosystems:

1. Three-parts principals: `<user>/<host/service>@<domain>`, e.g.
   `hdfs/node1.my.domain@MY.DOMAIN`
2. Two-parts principals: `<user>@<DOMAIN>`, e.g. `bob@MY.DOMAIN`

Rules for these two types are defined separately. Hadoop needs to convert these
principals into usernames, and this is where `auth_to_local` rules are used. By
default these rules just remove everything but the <user> part for the current
(default) domain. I spoke about the negative consequences of leaving this
default in my previous post. In short, they are:

1. Users are not uniquely mapped to their UIDs (aka 'users overloading') - e.g.
   if user `bob@MY.DOMAIN` has a confidential data stored in Hadoop cluster with
   no access to anyone but Bob, still there can be another user created with
   principal `bob/<anything>@MY.DOMAIN` which will be translated by default into
   'bob' user, too, and will get access to all Bob's data.

2. Superusers (`hdfs`, `yarn`, `hive`, `mapred`, `hue` etc.) are shared between
   clusters in the same domain (e.g. if at all of your clusters, namenode runs
   as local 'hdfs' user, then `hdfs@<DOMAIN>` user is de-facto the superuser at
   all your clusters). Also, e.g. datanode user at Cluster1 is still a legal
   user (and in Cloudera distribution, superuser) at Cluster2 and all other
   clusters in the same domain (and if it users `hdfs/<node>@<DOMAIN>`
   principal, then it's also an HDFS superuser at all your clusters).

3. Any user of type `<user>@<DOMAIN>` with the `<user>` part being equal to the
   OS user which has started a Hadoop process is de-facto the superuser of this
   process (e.g. `hdfs@<DOMAIN>` becomes superuser at all clusters in the same
   domain in most installations that I saw, same with hive@<DOMAIN>,
   hue@<DOMAIN> etc.)

To solve these issues, the following `auth_to_local` rules can be applied:

1. Rule to prevent any unwanted users coming in to this cluster:

   ```
   RULE:\[2:$1/$2@$0\](^.\*$)s/^.\*$/nobody/
   ```

   What this rule does: it converts any Kerberos principal of type
   `<user>/<something>@<DOMAIN>` into a `nobody` user. It works like a firewall
   rule '**Block All**' and **must be placed at the end** of your
   **auth\_to\_local** rules.

2. Since the rule above blocks all 3-part principals, which are generally used
   by Hadoop service users (e.g. HDFS NN / DN or YARN RM / NM etc), we now need
   to whitelist these users. For each of your services, create a line(s) using
   the following template:

   ```
   RULE:\[2:$1/$2@$0\](^<service>/<node>@<domain>$)s/^.\*$/<service>/
   ```

   Example for a 8-nodes cluster with YARN and HDFS, will have the following
   rules:

   ```
   RULE:\[2:$1/$2@$0\](^hdfs/node\[1-8\].my.domain@MY.DOMAIN$)s/^.\*$/hdfs/
   RULE:\[2:$1/$2@$0\](^yarn/node\[1-8\].my.domain@MY.DOMAIN$)s/^.\*$/yarn/
   RULE:\[2:$1/$2@$0\](^yarn/yarnm@MY.DOMAIN$)s/^.\*$/yarn/
   RULE:\[2:$1/$2@$0\](^HTTP/node\[1-8\].my.domain@MY.DOMAIN$)s/^.\*$/HTTP/
   ```

   Note the `yarn/yarnrm` principal - it's used by YARN RM to renew auth tokens.
   You also can replace the regex `node[1-8]` with a number of lines for each
   node at your cluster, this is useful if regex can't be used to list all
   cluster nodes, e.g.:

   ```
   RULE:\[2:$1/$2@$0\](^hdfs/node1.my.domain@MY.DOMAIN$)s/^.\*$/hdfs/
   RULE:\[2:$1/$2@$0\](^hdfs/node2.my.domain@MY.DOMAIN$)s/^.\*$/hdfs/ ...
   RULE:\[2:$1/$2@$0\](^hdfs/node8.my.domain@MY.DOMAIN$)s/^.\*$/hdfs/
   RULE:\[2:$1/$2@$0\](^hdfs/node14.my.domain@MY.DOMAIN$)s/^.\*$/hdfs/
   RULE:\[2:$1/$2@$0\](^hdfs/node22.my.domain@MY.DOMAIN$)s/^.\*$/hdfs/
   ```

3. Please also note that majority of Hadoop ecosystem daemons will accept any
   user with the same name as the local OS user that runs this principal. as a
   **superuser**. So users below are de-facto the default superusers for the
   corresponding services in most default Hadoop installs:

   * `hdfs@<DOMAIN>`
   * `yarn@<DOMAIN>`
   * `hive@<DOMAIN>`
   * etc.

   These users never exist in my Hadoop cluster setups: all my superusers are
   managed through groups. In clusters managed by me, those only will be human
   users, members of Admin groups (ping me if you want more information on how
   to setup this, but it's widely available in the Internet and at Apache
   website), and some specific technical users (for example, in Cloudera CDH
   which I use, it is required that Hue user is part of the HDFS and Hive admin
   group etc). Since technical users come in as 3-part Kerberos principals in
   form of `<user>/<node>@<DOMAIN>`, they will be converted into corresponding
   superusers by the rules above, e.g. `hdfs/node1.my.doman@MY.DOMAIN` -> hdfs
   (same username as NN daemons runs as, and therefore is HDFS superuser). But
   we want to prevent a scenario when someone creates a user `hdfs@MY.DOMAIN`,
   which automatically becomes the HDFS superuser at all clusters of the same
   domain, so we need to block those users using the rules below:

   ```
   RULE:\[1:$1@$0\](^hue@.\*$)s/^.\*$/nobody/
   RULE:\[1:$1@$0\](^sentry@.\*$)s/^.\*$/nobody/
   RULE:\[1:$1@$0\](^hive@.\*$)s/^.\*$/nobody/
   RULE:\[1:$1@$0\](^oozie@.\*$)s/^.\*$/nobody/
   RULE:\[1:$1@$0\](^yarn@.\*$)s/^.\*$/nobody/
   RULE:\[1:$1@$0\](^mapred@.\*$)s/^.\*$/nobody/
   RULE:\[1:$1@$0\](^hdfs@.\*$)s/^.\*$/nobody/
   RULE:\[1:$1@$0\](^zookeeper@.\*)s/^.\*$/nobody/
   RULE:\[1:$1@$0\](^httpfs@.\*$)s/^.\*$/nobody/
   RULE:\[1:$1@$0\](^HTTP@.\*$)s/^.\*$/nobody/
   etc
   ```

   These rules should include all the local OS users that start services at your
   cluster, and in my case, the example below includes the default configuration
   of a CDH cluster which has Hue, Sentry, Hive, Oozie, YARN, HDFS and ZooKeeper
   installed.

That's pretty much it. The 3 solutions above will help ensure that you can have
any number of secure clusters in the same domain managed by different teams or
one team as required.

Since I use Cloudera distribution and direct integration of my Hadoop clusters
with Active Directory (via Kerberos and LDAP group mappings), I slightly change
the rules from #2 above to rename Cloudera Manager's automatically generated
default principals into usernames that are unique for each Hadoop cluster I
have. I then can easily implement any kind of granular access lists and group
membership by just creating these users in Active Directory and assigning them
to the required groups and ACLS:

Cluster1 (nodes 1-4):

```
RULE:\[2:$1/$2@$0\](^hdfs/node\[1-4\].my.domain@MY.DOMAIN$)s/^.\*$/cl1\_hdfs/
RULE:\[2:$1/$2@$0\](^yarn/node\[1-4\].my.domain@MY.DOMAIN$)s/^.\*$/cl2\_yarn/
RULE:\[2:$1/$2@$0\](^yarn/yarnm@MY.DOMAIN$)s/^.\*$/cl1\_yarn/
RULE:\[2:$1/$2@$0\](^HTTP/node\[1-4\].my.domain@MY.DOMAIN$)s/^.\*$/HTTP/
```

Cluster2 (nodes 5-8):

```
RULE:\[2:$1/$2@$0\](^hdfs/node\[5-8\].my.domain@MY.DOMAIN$)s/^.\*$/cl2\_hdfs/
RULE:\[2:$1/$2@$0\](^yarn/node\[5-8\].my.domain@MY.DOMAIN$)s/^.\*$/cl2\_yarn/
RULE:\[2:$1/$2@$0\](^yarn/yarnm@MY.DOMAIN$)s/^.\*$/cl2\_yarn/
RULE:\[2:$1/$2@$0\](^HTTP/node\[5-8\].my.domain@MY.DOMAIN$)s/^.\*$/HTTP/
```

etc.

Please note that HTTP principal is part of Kerberos convention, and must remain
`HTTP` at all times.

Please feel free to ping me at any time with any questions you might have, or if
you need help securing your Hadoop clusters.
