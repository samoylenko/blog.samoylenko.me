---
title: "A conversation about TLS certificates in application security"
date: "2019-09-11"
categories:
  - "infosec"
---

A conversation about TLS certificates in application security, from a chat with
a colleague while working on an actual project. Published with permission.

> Alright, so here's that fun part most people in security don't know about.
>
> For us, using TLS was sort of natural because we are a security team. But many
> software engineers avoid it, especially at the beginning of their career. And
> then when they finally got to the point when they actually _want_ to start
> using TLS, that usually means that they got pretty advanced in what they do.
>
> Often that also means that they learned to do most things by just googling
> them and copy-pasting solutions from StackOverflow. Nobody ever reads manuals,
> so eventually dealing with certs becomes mostly 'hacking' certs because, you
> know, certs are hard. Reading takes time, so whoever comes up with a quick
> solution from StackOverflow, everybody is going to use it without even knowing
> it's wrong. If it works - don't touch it, right?
>
> Nobody will spend time learning certs properly because it's complex and takes
> lots of time, which can be spent playing with stuff like React and other cool
> technologies.
>
> And you know that getting legit certs that are accepted by browsers is a long
> process, and most of the time, people use self-signed certs just like you and
> me yesterday. That's the natural order of things. And not everyone is signing
> certs with a global CA root certificate included by default in browser and
> operating system trust stores, so things actually get more complicated for
> many companies.
>
> Many companies I worked with use self-signed certs for internal TLS, saving
> tons of money this way. You know, now that I am typing it, I think that maybe,
> the whole reason our company uses a global CA, and there are those discussions
> in our Slack's `#certificates` channel about those humongous costs is
> precisely for the same reasons that people didn't know how to tackle the
> self-signed problem this large text is about :)
>
> Every time an engineer is implementing something major in their company, they
> get the problem you got, "signed by unknown CA" type errors. And you know how
> bad this can end: you can see that `-tls-skip-verify` we are using in our
> scripts.
>
> So to the solutions. There are two primary solutions I know:
>
> 1. The best and proper solution is to add the root cert to the operating
>    system's trust store. Every OS has a place where it stores root
>    certificates it trusts. Everybody knows it, but because every OS does it
>    differently, people just never get to learn how to add those certs
>    correctly.
>
>    In Windows, that would be right-clicking the certificate and selecting 'add
>    to Root CA'-something (I don't remember that precisely). In Linux, it's
>    just putting your root CA public cert to a particular folder and then
>    running a command to process it and add to the system :)
>
>    Here's an example for RedHat based OS (Includes CentOS, Fedora etc):
>
>    ```shell
>    ln -s /etc/vault.d/tls/cert.pem /etc/pki/ca-trust/source/anchors/vault-self-trust.pem
>    update-ca-trust export
>    ```
>
>    Surprisingly, in the same way, using something like CygWin or GitBash has
>    the same trick - those two are also like an OS inside your OS :) The only
>    problem with that - it requires you to be an admin.
>
>    E.g., in Debian-based Linux distros (Ubuntu, Mint etc.) that would be a bit
>    different: the directory for new certs to place is `/etc/ssl/certs`. And
>    command to run is `update-ca-certificates --fresh`. You can always find
>    these commands by googling something like 'redhat add root certificate' :)
>    Most people google just the error they get from `curl` or their browser.
>
>    Now, why is this way the best way to do it - the crucial part here, it
>    doesn't just update your OS certs. The colossal problem you never want to
>    solve, and instead, you want to delegate it to the operating system
>    consists of the following: all programming frameworks and languages such as
>    Java, .NET, Ruby, JavaScript, Python (_all of them_) do have their own,
>    separate certificate storages :)
>
>    Most server operating systems will have hooks triggered when you update
>    your OS Root certs with the commands above - hooks that automatically
>    rebuild certificate storages for the frameworks and languages. If those are
>    correctly installed using package managers - the whole reason why you don't
>    want to compile stuff in the modern world and put it to `/opt` or
>    `/usr/local` and instead only use official repositories. It also explains
>    why major Linux distros are not up to date with recent software versions -
>    that packaging takes time).
>
>    And of course, in the modern corporate world, Java and Python are among the
>    most popular languages, so those are pretty much guaranteed to be
>    automatically updated with certs if you are using a good Linux distro and
>    installed them correctly (using the system package manager and good repos)
>
> 2. The second way is very hard. And you probably already guessed that it's
>    about adding a Root CA certificate to a specific language or framework you
>    are using. You never want to do that, but sometimes you got to, especially
>    when you're not an admin at the machine you are using (no root). Again, in
>    the corporate world, this solution is not recommended.
>
>    For example, Java uses trust stores, encrypted files that are deployed
>    together with your java app. You explicitly add that trust store to JVM
>    parameters when it's run. So things can get complicated there.
>
>    While useful in dev, those are really bad scenarios - you can imagine
>    nothing good happens when there's a discrepancy between OS root CA trust
>    and App's root CA trust.

That's it :) thank you for reading :))
