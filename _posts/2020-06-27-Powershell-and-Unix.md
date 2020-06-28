---
layout: post  
title:  "Two Flavors of Pipes"
date:   2020-06-27 19:19:21 +0700   
--- 

On June 1, I started my journey at DocuSign. While starting remote has been a bit weird, I have had an incredible experience so far, shipped some code, learned some things, and even made a fool of myself a couple times. I am extremely grateful that I am able to find employment in these crazy times, especially meaningful and interesting employment. There is lots of C# at DocuSign, so I have been migrating away from C++ & Linux focused development into new, uncharted waters. A side effect of this is moving from Unix --> Powershell for some of the automated scripting. Just before I started at DocuSign, I gained a newfound appreciation for Unix (and Unix pipes) after reading Brian Kernighan's latest book [Unix: A History and Memoir](https://www.cs.princeton.edu/~bwk/). Never used pipes? Well, today is your lucky day! This post will show you how to use pipes in Unix and then how a similar effect can be created in powershell.

## Pipes

<p align="center">
  <img src="https://media.giphy.com/media/9PzaDXcbo9a45a5Y22/giphy.gif" />
</p>

Ah yes, so what is a pipe? This is a good place to start. Pipes, or more formally a pipeline, is basically a mechanism for chaining multiple processes together by feeding the output of one into the input of the next. A slightly more computer-sciency way of saying this is *"a set of processes chained together by their standard streams, so that the output text of each process (stdout) is passed directly as input (stdin) to the next one"* - ([Wikipedia](https://en.wikipedia.org/wiki/Pipeline_(Unix))). So yes, a pipeline is probably almost exactly what you thought it was. 

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/b/b9/Douglas_McIlroy.jpeg/220px-Douglas_McIlroy.jpeg" />
</p>

This concept that was first brought into the world of computing by [Douglas McIlroy](https://en.wikipedia.org/wiki/Douglas_McIlroy) at Bell Labs (and subsequently implemented into Unix by the mythical [Ken Thompson](https://en.wikipedia.org/wiki/Ken_Thompson). Here is a fantastic quote about the invention of the pipe: *"His ideas were implemented in 1973 when ("in one feverish night", wrote McIlroy) Ken Thompson added the pipe() system call and pipes to the shell and several utilities in Version 3 Unix. "The next day", McIlroy continued, "saw an unforgettable orgy of one-liners as everybody joined in the excitement of plumbing.""* ([Wikipedia](https://en.wikipedia.org/wiki/Pipeline_(Unix))).


## Pipes in Unix

Ok, time for an example. For anyone running a version of Unix, MacOS, Ubuntu, any Linux derivative, etc. this one is for you. I will be creating an example very similar to Brian Kernighan's favorite example - we will be determining the misspelled words in a text file.

For starters, we have a textfile (text.txt):

```
I like to write commands using pipes and pipes are what I like to wriite
```

First, we want to read from the file. We can do this uisng `cat`:

```
> cat text.txt
I like to write commands using pipes and pipes are what I like to write
```

Then we want to lowercase all the words since our dictionary only has lowercase letters. To do this on the text from our text file, we use the `|` to pipe the output from our first command to the input of our second command:
```
> cat text.txt | sed -e 's/.*/\L&/g'
i like to write commands using pipes and pipes are what i like to write
```

The `sed` command takes a regular expression, matches every occurance of this expression, and replaces these matches with the second expression. In this case our find expression is `.*` which says to match on everything and our replace expression is `L&` which turns all ascii characters to lowercase.

Now, we want to put every word on its own line. Using the `sed` command again, we match against whitespace `\s` and we replace with a newline `\n`:
```
> cat text.txt | sed -e 's/.*/\L&/g' | sed -e 's/\s/\n/g'
i
like
to
write
commands
using
pipes
and
pipes
are
what
i
like
to
write
```

The next step is to alphabetize our list and then remove duplicates. Removing duplicates allows us to perform fewer checks against our dictionary in the next step:
```
> cat text.txt | sed -e 's/.*/\L&/g' | sed -e 's/\s/\n/g' | sort | uniq
and
are
commands
i
like
pipes
to
using
what
wriite
write
```

Finally, it is time to check for misspelled words. To do this, we want to check to see if each word is in the dictionary, if it is not, it must be misspelled. Assuming the computer has a textfile called dictionary, we will use the `grep` command to do just that. The grep command is a unix command for searching for lines of text that match a certain pattern (regex again!!). By using this with some flags, `v` which returns the inversion, `f` a path to a file to match against, and `x` matching only against whole lines, we get the desired effect:
```
> cat text.txt | sed -e 's/.*/\L&/g' | sed -e 's/\s/\n/g' | sort | uniq | grep -xvf dictionary
wriite
```

Yep, so next time you're in a Zoom party with friends on a Friday night, share your screen and impress them with this.

<p align="center">
  <img src="https://media.giphy.com/media/nbNVIe8Y0AEyit5qV0/giphy.gif" />
</p>

## Pipes in Powershell

Ok, so you run Windows but still want to impress your friends. How does the above translate? Are there pipes in the powershell? Questions, questions, questions. 

It turns out there are pipes, and have been for quite a while. However, they come with one small caveat... **when piping in the powershell, output is formatted as objects, not just unformatted streams of text**. This actually turns out to be quite useful! Let's dive in and see how we can recreate the above.

To start with, we could use cat, but that seems like cheating... let's use a powershell cmdlet! [Cmdlets](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/cmdlet-overview?view=powershell-7#:~:text=A%20cmdlet%20is%20a%20lightweight,them%20programmatically%20through%20PowerShell%20APIs.) are powershell methods. Many useful ones are included with Windows and you can of course write your own as well.
```
> Get-Content text.txt
I like to write commands using pipes and pipes are what I like to wriite
```

Ok, so far so good. Now let's expose what I meant by *passing objects instead of unstructured text*. When we use the `Get-Content` command here, we get a string. We can access this string in the next pipe via the special `$_` variable (variables in powershell are denoted by the `$`). Thus, we can actually combine the splitting of lines by whitespace and the lowercase conversion in one go (in a much friendlier, readable way... no regex!):
```
> Get-Content text.txt | % {$_.Split(" ").ToLower()}
i
like
to
write
commands
using
pipes
and
pipes
are
what
i
like
to
wriite
```

Now it is time to sort and remove duplicates. Windows ships some similar commands to what we used in Unix: `Sort` and `Get-Unique`:
```
>  Get-Content text.txt | % {$_.Split(" ").ToLower()} | Sort | Get-Unique
and
are
commands
i
like
pipes
to
using
what
wriite
write
```

Wow - this is almost a 1:1 translation so far and we even avoided regex! Ok, now it gets a little messy. Unlike `grep` above, `Compare-Object` does not show one-sided differences. As you may imagine, this is rather unfortunate when comparing against a large dictionary; it returns each difference with a `SideIndicator`, `<=` meaning only in left and `=>` meaning only in right. In our case we want the words which are unique to the input so we filter `=>`:
```
>  Get-Content text.txt | % {$_.Split(" ").ToLower()} | Sort | Get-Unique | Compare-Object (Get-Content) dictionary) | Where-Object {$_.SideIndictor -eq "=>}
InputObject SideIndicator
----------- -------------
wriite       =>
```

Ah, so we have what we want, but now it is in this wonky table. IMO, this is an unfortunate side-effect of powershell's object approach. However, it is not that hard to transform the output into the desired format:
```
> >  Get-Content text.txt | % {$_.Split(" ").ToLower()} | Sort | Get-Unique | Compare-Object (Get-Content) dictionary) | Where-Object {$_.SideIndictor -eq "=>} | Select-Object "InputObject" | Format-Table -HideTableHeaders
wriite
```

My apologies, I realize that this one-liner is now flowing into the abyss off the right side of the code box. That being said, I actually believe this one-liner is much more readable than the unix one, albeit that takes away some of the fantastical magic that comes with it when you try to impress your friends.

## Conclusion

So, besides this being an interesting exercise, what's the point? My point is that after two summers deep in the world of cryptocurrency in San Francisco, I was indoctrinated into a certain mindset. This mindset said things like:
* closed source evil
* Microsoft bad, Linux good
* do you use Rust? Why don't you use Rust?

This is of course a minor exaggeration. That being said, I am learning (or rather relearning if you talk to my 11th grade English professor) it is almost always best to take a nuanced approach - by considering the tradeoffs of a decision and both/all points of view, you end up with a much stronger, more comprehensive final outcome. In software this means using Linux when appropriate and Windows when it is not. In astrophotography, it means taking some wide-angle shots and some close-ups. In collegiate track and field 400m training, it means 1000m workouts and 50m speed work (yes 1k's are no fun for short sprinters, but hey, I cut 4 sec off my 400 pr in one season, thanks [Dave](https://www.gocolbymules.com/sports/mtrack/coaches/Dave_Cusano?view=bio)). So, since I'll probably be living in both Windows and Unix systems for the next X years of my software engineering career, it'll pay off to understand both. 
