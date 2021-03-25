---
title: "Configuring Cloudera Navigator to use external authentication"
date: "2015-02-02"
categories:
  - "hadoop"
  - "infosec"
---

[Cloudera](http://www.cloudera.com), author of one of the most popular Hadoop
distributions, has created a great tool for Hadoop security monitoring and
auditing, called
[Cloudera Navigator](http://www.cloudera.com/content/cloudera/en/products-and-services/cloudera-enterprise/cloudera-navigator.html).
I find its initial configuration process a little bit tricky, so I wanted to
document it in this post. Cloudera's original document on how to do this is
located here:
[http://www.cloudera.com/content/cloudera/en/documentation/core/latest/topics/cn\_sg\_external\_auth.html](http://www.cloudera.com/content/cloudera/en/documentation/core/latest/topics/cn_sg_external_auth.html)

I currently use the latest version of Cloudera Hadoop distribution with Cloudera
Manager 5.3.1 (trial enterprise license) and Navigator 2.2.1. It openly shows
its full version and build in a tool-tip on its logo and in 'About' section
right at the login page (so in case there's a vulnerability published in future,
hackers won't need to spend time finding out target's version ;-) ):

![1]({{ site.baseurl }}/assets/2015-02-02/1.png)

In my home lab's cluster I use Active Directory authentication, and my goal is
to make Navigator work with it, too. If you don't use Active Directory or LDAP
in your Hadoop installs, don't worry about unavailability of 'Administration'
page - it's only about configuring groups in LDAP/Active Directory:

![2]({{ site.baseurl }}/assets/2015-02-02/2.png)

However, it seems that in this version of Cloudera Manager (5.3.1) 'Navigator
Administrator' role presents but is not used, and only 'Full Administrators' can
login to Navigator. Also, please note user names are case sensitive in Cloudera
Manager and Cloudera Navigator.

Navigator's external authentication is configured via the following steps:

1. Configure Navigator's 'Authentication Backend Order' to enable Cloudera
   Manager authentication
2. Configure LDAP parameters for Cloudera Navigator
3. Restart Cloudera Navigator
4. Login with Cloudera Manager's local Full Administrator user
5. Use 'Administration' page in Cloudera Navigator to configure external admins
   group
6. Logout and test if you can now login with an external admin user
7. \[Optional\] Configure Cloudera Navigator to use only external authentication
   and restart it again.

My cluster is already configured to use LDAP authentication for Cloudera
Manager, so I need to create a new local admin user to login into Navigator. You
can skip this step if you are still using the default 'admin' user (in my
installs I convert it into an 'emergency admin').

First, I create a new local 'Full Administrator' user in Cloudera Manager (once
again, as I said above, in this version of Navigator, Cloudera Manager's role
'Navigator Administrator' does not seem to be used at all, and can't login to
Navigator):

![3]({{ site.baseurl }}/assets/2015-02-02/3.png)

Then I go to 'Cloudera Management Service' -> Configuration -> Navigator
Metadata Server Default Group -> External Authentication and configure LDAP. My
home lab uses Active Directory server with bind authentication enabled. I highly
recommend you to use LDAPS over LDAP (and HTTPS over HTTP for login pages), but
I don't have that since it's not critical for a lab environment:

Authentication Backend Order -> Cloudera Manager then External External
Authentication Type -> Active Directory (you can choose LDAP) LDAP URL ->
`ldap://winserver.lab.local` (`ldaps://` here would be better. this is address
of my AD server) LDAP Bind User Distinguished Name -> hadoop-bind (if you use a
bind user, **don't add a domain part** the user name - Navigator will do this
for you) LDAP Bind Password -> `********` (yep, eight stars it is) Active
Directory NT Domain - `LAB.LOCAL` (my domain name) LDAP User Search Base ->
`DC=LAB,DC=LOCAL` (OU to where to look for users) LDAP Group Search Base ->
`DC=LAB,DC=LOCAL` (OU to where to look for groups)

![4]({{ site.baseurl }}/assets/2015-02-02/4.png)

Then I save configuration and restart Navigator (I usually do this by going to
'Instances', marking both Navigator roles and restarting just them, not the
entire Cloudera Management Services):

![5]({{ site.baseurl }}/assets/2015-02-02/5.png)

When Navigator is back online, I login to it with my newly created Full
Administator user:

![6]({{ site.baseurl }}/assets/2015-02-02/6.png)

'Administration' page is now active:

![7]({{ site.baseurl }}/assets/2015-02-02/7.png)

I use 'Find a group...' text box to locate my lab admins group. Please note that
Navigator will send a query to your LDAP server every time you press a button,
and by default, a wildcard is used, which is not the recommended behavior for a
LDAP client (imagine a production LDAP with thousands of groups). You can
configure this query at Navigator's External Authentication page in Cloudera
Manager.

![8]({{ site.baseurl }}/assets/2015-02-02/8.png)

And click 'Manage role assignment to configure it as Navigator admins:

![9]({{ site.baseurl }}/assets/2015-02-02/9.png)

Then I save it, logout from Navigator and test the new role assignments with my
Active Directory user 'labadmin1' which is member of 'LabAdmins' group newly
configured Navigator admins:

![10]({{ site.baseurl }}/assets/2015-02-02/10.png)

![11]({{ site.baseurl }}/assets/2015-02-02/11.png)

Done. I then login back to Cloudera Manager, remove my local Full Admin user
'NavAdmin' (since I don't need it anymore) and configure Navigator to use
External authentication only. Now members of my group LabAdmins in Active
Directory can login to Navigator to configure other roles as required.
