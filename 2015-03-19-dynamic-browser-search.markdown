---
layout: post
title: "Dynamic browser search from a yanked text in VIM from a vagrant box"
date: 2015-03-19 08:00:00 -0700
comments: true
author: Fellipe Brito
categories: [vim, search, bash, vagrant]
---

_by [@fellipebrito](https://github.com/fellipebrito)_

_"Me and Ms. Mouse... we have a thing"_ - I hate to use the mouse. Not only because I want to show off my abilities with the keyboard everytime a new fellow developer comes over my desk but mainly because it slows me down. I've invested some time in the last two years to get my hands as far as away from the mouse as possible. Even more than slow me down, the movement from keyboard to mouse and back to the keyboard can be harmful to your wrists, but it is another story.

I like to use the term "I'm in the matrix" when I'm coding. Because this is how I feel, and also because everyone else understands I'm with my terminal in full-screen mode and notifications turned off. The problem is that "write code" is not the main task of a developers' life. We do not like to admit, but we spend a lot of time looking for an answer to dumb questions and reading through APIs that we've already read before but we just did not memorize it. This is another thing that bothers me: _<open terminal> <tmux> <vim> <start coding> <how do I do... question> <alt + tab> <chrome> <google/github/stackoverflow> <ctrl + v> <get answers>_ and the idea that I do that at least 20 times each day.

There are so many steps - mouse interactions and keystrokes - in the whole process I described above that if I only try to make a simple math ( ( keystrokes + mouse clicks ) * 20 ) I will start to get mad at me! It is A LOT of time lost, and as once Bruce Lee said:
>_"If you love life, don't waste time, for time is what life is made up of."_

### what-bothers-me.markdown

I've seen some folks that have this practice of creating a simple markdown file somewhere so they can keep a track of everything that bothers them but is not a "must to be fixed now" thing. I like this practice, so I have an alias on my VIM that opens this file for me with two keystrokes. It was in this file that I saved the quote _"I hate to use the mouse to copy text from vim and then alt-tab to Chrome every time I want to look for something"_. It was there, saved as a small thorn in my finger. Every time I went through this process I felt the pain. Two days ago I watched 25% of a youtube video where the developer had an idea for his emacs. So before I finish watching, I thought: "Ok, I will try to fix this issue myself, and later on I come back to see how he did it". So here we go.

### tl;dr?! - Ok, code starts here...

#### Expect
First of all, you must have expect installed in your unix. _"OH, come on, you do not know what is expect?"_. Dude, go for it here: [Using Expect Scripts to Automate Tasks](http://www.admin-magazine.com/Articles/Automating-with-Expect-Scripts)

#### Automate SSH access to your computer from vagrant
My personal computer is dumb. It does not have GitHub keys, Ruby, MySQL or any other "dev stuff". The only tool this computer has is a vagrant box (or some boxes). There, inside my vagrant is where I'm happy. The problem is that this vagrant box does not have access to my browser, file preview and other apps that I use.

My plan was simple: I'd like to **yank a text on vim, execute few keystrokes and then an automated script would open my browser and search for this text in google**.

If you want to have an executable file, you should start it with a header that gives it some information about how it should be executed.

```bash
#!/usr/bin/expect -f
# Use: browser_search.sh <query-to-be-searched>

set QUERY [lindex $argv 0]
```

Now that I have the query I want to search as my argument, and matrix know it should use expect -f, it is time to ssh back into my main computer:
```bash
spawn ssh username@hostname
expect "Password: "
send "your-password\r"
expect "$ "
```

Look how amazing *expect* is!

Ok, now that I'm on my main computer (a MacOS) it is a matter of open my default browser and access Google:
```bash
send "open https://www.google.com/#q=${QUERY}\r"
expect "$ "
send "exit\r"
```

The whole script is on this [gist](https://gist.github.com/fellipebrito/e35f976c3d952d6f4714)

You can copy/download/modify it and then move into your /bin/<file_name>.sh directory. Do not forget to give it 755 permission so it looks like that:
```bash
-rwxr-xr-x 1 root 259 Mar 18 19:21 /bin/browser_search.sh*
```

#### Vim alias - how to copy yanked lines in VIM's command line

Now that I have this script I have one more challenge, I want to yank a text and with two or three keystrokes send it to the terminal?

```
map <Leader>bs :!browser_search.sh <C-r>"<CR>
```

Here the `map <Leader>bs ` is defining my keystroke.

`:!browser_search.sh ` tells VIM that it should execute this command on the command line.

`<C-r>"` is the command to paste the text is yanked on the command line. And finally the `<CR>` is the <enter> key.

It works very well, and my plan in the future is to improve it so I can search not only on Google but define if I want to search directly in one specific place.

[![ScreenShot](http://gifyu.com/images/dynamic-browser-search.gif)](http://gifyu.com/images/dynamic-browser-search.gif)
