---
title: "A plain conversation about application security"
date: "2019-11-01"
original_date: "2019-05-14"
categories:
  - "infosec"
---

This is an extract from a real conversation with a colleague at one of my
previous jobs. Even though their name is not mentioned here, I asked for their
permission before publishing this:

**Colleague**:

> I think we talked a bit when you were in your previous role about the way
> traditional app sec works here. Where it's similar to how it's been in my
> other experiences. What kind of stuff have you figured out about moving that
> into the whole CI/CD process? Anything cool or interesting yet?

**me**:

> The most important thing I know about application security, and you know that
> too - is that it will fail if software engineers are not interested. So if
> there is no buy-in from that side, another problem needs to be addressed
> first. If a software engineer is not interested in learning about bugs they
> may have in their code, it's a problem of a different category. A culture
> problem.
>
> And then, the next thing I learned is that I can be a security/pentest expert
> whatsoever and know everything about software vulnerabilities, but:
>
> a) I am not the one who owns the risk - business does. And they are the party
> to make decisions on how to address it.
>
> b) I don't have access to the code repository, neither I have enough skills to
> fix the issue myself. Only the software developer can do that. So again. I
> can't really tell I own the risk or its resolution

**Colleague**:

> Do you ever do code reviews with people to look into security issues? I see
> that code reviews are a big part of a lot of App Sec programs, but I'm only
> familiar with them as a team mate building something with my team.

**me**:

> Of course, code reviews are an essential aspect of my daily work.
>
> First of all, code reviews are costly. Companies can't just spend their entire
> budgets on experts who can perform code reviews. Plus, those reviews take a
> lot of time which is also a very limited resource. That's actually how
> DAST/SAST automation came into play: we don't want those experts who review
> the code manually to spend their time on items that can be detected
> automatically. At the same time, SAST/DAST is also expensive and takes a long
> time. Not as long as a human review, but still. So that's how tools like
> dependency checks and linting came into play.
>
> So every code pipeline should have Lint (e.g. SonarQube)->Dependency check
> (.e.g Snyk) -> SAST (e.g. Veracode) -> DAST (Burp/OWASP ZAP/sqlmap/other
> tools) with just manual reviews for the most critical and risky projects
>
> And there's an important comment here that OWASP and some other large
> companies had a couple of years of research spent to see how effective
> security scanning tools are, with a verdict that if you have everything set up
> perfectly, you only get 4-10% findings. The rest is mostly human review, and
> again humans are all different. So that's the expectation that we all should
> be aware of.

**Colleague**:

> Wow, this is really insightful. So with these automated tools that's where you
> can start hooking things into the SDLC. Like how my team hooked into SonarQube
> in dev and found a bunch of things to fix and measured our test coverage.

**me**:

> That's why the entire CI/CD and consequential DevOps comes into play: we know
> that even if we invest A LOT, we still will have security issues in
> production. And the best thing we can do is ensure we can quickly react to any
> findings we have, get systems back online fast, and have patches applied in no
> time.
>
> Yeah, the first thing I would be looking to have is what you described:
> SonarQube, test coverage and pull requests (code review) process
>
> And every modern programming language will have a set of awesome linting
> tools. Even markdown does :)
>
> ...But still, application security is a matter of culture. Because what is
> security? The best definition I ever heard was 'secure system is the system
> that behaves only in the manner it is expected to behave, and nothing else.'
>
> So it's not, e.g., enough to just have sonarqube. It's about fixing everything
> as well. And not even waiting until you push code into the repository. You
> want your IDE to lint as much as it can (that's the reason I am paying for
> Intellij IDEA Ultimate - it's linters are the best in the industry, and it has
> Sonar integration)

**Colleague**:

> So modern app sec is really just translating defense-in-depth principals to
> every step in the app dev process. Which makes sense but maybe wasn't as easy
> before.

**me**:

> Well, this is a good point. Because it's also important to mention that
> security controls are no longer developers' responsibility in most cases.
> Usually, modern programming frameworks, or platforms, will take care of
> security controls. So I'd say application security is about knowing your
> framework and using it properly. If a developer has to implement security
> controls, usually it's already all wrong.
>
> So yeah, it's about security architecture design now, and defense-in-depth is
> the major aspect of it. Together with threat modeling that considers the
> entire context of the app.
>
> Looking at your question again, I'll try to rephrase. It's the combination of
> two:
>
> 1. secure design that uses common security solutions and not reinventing the
>    wheel (i.e., not implementing security controls from scratch but rely on
>    well known frameworks/platforms)
>
> 2. Using automation as much as possible to prevent \[security\] bugs from
>    getting into code.
>
> 3. Would be to ensure communication and collaboration
>
> (sorry for the large texts, this is the passion and research of my life you
> just triggered ;) )

**Colleague**:

> No apologies needed, this is all really great insight. You should give a talk
> about this at a conference.

**me**

> haha, I appreciate this :) thank you for your kind words
>
> I didn't invent any of this, really. It's what I keep hearing over and over at
> 'Paul Security Weekly' and 'The Secure Developer' podcasts (would totally
> recommend)

**Colleague**:

> A lot of this is stuff I've already heard or was vaguely aware of, but you put
> all the pieces together really well. I am a big podcast fan, so I'll check
> those out.
>
> One of the things that always intimidates me with application security is that
> people say you need to have a development background or be really good at
> "code reviews," and I've developed and I've done code reviews, but when you
> google to find more information, it sure seems vague and like everyone does
> those things differently. So I'm left wondering, when do you know if you've
> done enough development to understand what you need to, and what's this
> person's idea of a code review if everyone does them differently. Maybe just
> realizing that is the key haha

**me**

>  Excellent question :) I don't think there's really a way for someone to be a
>  code review expert of sorts. Because it's not enough to just understand
>  vulnerabilities, but the central part of the problem is to provide the right
>  solution for it. And that would depend on the specific language and framework
>  used in a particular project.
>
>  So I'd say the only item here that makes sense is reviews performed by
>  experienced developers in their space, and not by security people.
>
>  Are you a Java guy? I have a very simple but very arguable example :)
>
> ```xml
> <p th:utext="'Hello, ' + ${name} + '!'"/>
> ```
>
> (I also know this is very different from what security training has, but this
> is real life)

**Colleague**:

> Looking at the code, it seems like it takes in a parameter name, and the
> desired output is to put a <p> tag on the HTML with "Hello, \[Name\]!" in it.
>
> Okay, so the issue I'm seeing is just that it's taking in any string and
> putting it into the page. so you need input sanitization

**me**:

> And that would be the 99% answer because this is what training tells us. it is
> entirely correct from a security perspective.
>
> But in reality, first of all, input sanitization is almost impossible in this
> case.
>
> And even if you figure out some perfect regex, it will slow down the
> application by a lot, it will be a huge performance problem - just like any
> regex. That's what developers told me. And I tested it all, and they are
> right. This is a real-life example, and a typical Java legacy app would have
> 100s of these.
>
> Now I want to add a comment of my own before giving my version of the
> solution. What I also learned is that most injection vulns consist of two
> parts:
>
> `---- input ----> [routine] ----> output ---->`
>
> So two conditions need to be met for a vulnerability to exist:
>
> 1. Input needs to contain character sequences that are treated as control
>    sequences by a downstream component, e.g. database, or a subprocess.
>
> 2. And the output must contain the same chars that are interpreted as control
>    sequences by the downstream component or system that receives that output.
>
> The idea is that, e.g., during HTML rendering, like in the above case, the
> second condition would be for output to contain characters like `<>` so that
> they are rendered into control tags like `<script>`
>
> And the problem here is the `[routine]` part above can have multiple inputs.
> E.g., it can be called by some other routine, some function, be inherited,
> etc. so it's actually like this:
>
> ```
> ---- input ----> [routine] ----> output ----> 
>         input 2 /    | input 3
> ```
>
> So the lesson I learned looking for vulns resolution is that input validation
> is extremely hard to do right. And still, there is output encoding that
> actually will prevent an injection.
>
> So the real-life solution here by a senior developer is just to use output
> encoding (escaping). Which is just by changing the view:
>
> Was:
> ```xml
> <p th:utext="'Hello, ' + ${name} + '!'"/>
> ```
>
> Fixed:
> ```xml
> <p th:text="'Hello, ' + ${name} + '!'"/>
> ```
>
> Changing that tag from `utext` to `text` required knowledge of that specific
> `thymeleaf` framework on top of `Spring`
>
> In the same way, SQL injection is prevented by using prepared statements that
> de-facto escape parameters.
>
> Back to the XSS example, in real life, our company app had 800 of these. And
> developers were initially forced to filter input. And they failed after 1.5
> years doing that.

**Colleague**:

> Yeah, that's wild - I know about escaping things and I know you can do it
> easily in like PHP and other formats. It's crazy to think that every framework
> or tool is going to have a different approach, but I'm sure as you learn them
> you start to see patterns.

**me**:

> And you are absolutely right about patterns - the biggest one is '**don't
> disable a control that exists by default just because it's not convenient**'
> :)
>
> Usually, frameworks will have a secure solution available by default. That
> `utext` stuff in the example above takes time to find in docs.
