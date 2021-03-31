---
title: "Shell script - waiting until specified day/time"
date: "2016-08-09"
categories:
  - "linux"
  - "programming"
---

I participated in a project in which job scheduling logic was completely
implemented in shell script (it was a small project initially, and then it has
grown in time). One of tasks was to enable scheduler script to wait until a
specified time to run a job. Surprisingly, search in Internet did not bring
anything that would look simple and elegant enough. There were bunch of quite
complex solutions which did not really address my requirements and would be too
hard to maintain for support teams in future... I came up with my own solution
for this after reading Linux manuals, which I wanted to share.

It appears, 'date' utility is very flexible in terms of definition and
representation of time. For example, user can supply target date/time in a
string, using words like 'Today 9am" or "Thursday 12pm" etc. In combination with
'sleep' command, I was able to achieve the desired functionality with required
precision (seconds). I ended up writing this simple bash function:

```shell
#-[ wait_until ]------------------------------------------
# Waits until a given day/time
# $1 - target time/date in 'date' command format, e.g. "Today 13:00"
wait_until()
{
echo "Waiting until $1..."

TIME_TARGET=$( date -d "$1" +%s )
TIME_NOW=$( date +%s )

if [ $TIME_NOW -lt $TIME_TARGET ]; then

    TIME_WAIT=$( expr $TIME_TARGET - $TIME_NOW )
    echo "Waiting time: ${TIME_WAIT} seconds"
    sleep ${TIME_WAIT}s

else
    echo "Target time is already in past. No waiting required..."
fi
}
```

Use is very simple:


```shell
wait_until "Today 1pm"
#... some code ...
wait_until "Tomorrow 6am"
#... some code ...
```
etc...

Hope it helps someone who will be looking for this solution in Internet, just
like I did before I had to write it :)
