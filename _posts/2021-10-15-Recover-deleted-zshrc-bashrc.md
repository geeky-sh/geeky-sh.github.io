---
layout: post
title: What to do when .zshrc OR .bashrc is deleted.
description: Steps to recover most of the customizations made in the bashrc/zshrc
---

## Disclaimer:

There is no default way to recover ~/.zshrc in case it is deleted. One can just try to recover everything that was customized in the file. Or one can step individual steps to ensure the file is recovered on routine basis

## Context:

There are times when we mistakenly delete the ~/.zshrc file (Like running `source ~/.rvm/scripts/rvm > ~/.zshrc` instead of `source ~/.rvm/scripts/rvm >> ~/.zshrc`).

I did the above mistake once. I opened a new tab and since my system can't find `/.zshrc` configuration, all the customization which I did - like using oh-my-zsh, adding other shortcut commands, env variables, etc - was lost. And I didn't have any backup of the file. I paniked for a moment.

## Solution:

I searched online if there is a way to get back the old version of file. Unfortuately, there isn't. However there are certain way in which one can recover the custom config. The only caveat is one should have at least one old terminal session open which has the old configuration loaded. Go to that terminal and do:

1. `printenv` - This will print all the environment variables. One can get all the custom variables one would have defined. Like `AWS_CLIENT_KEY`, `LD_FLAGS`, etc. One can also get the `PATH` variable if one wants to get find any custom paths one would have defined
2. `functions` - This would print all the functions present in the session. If there are any custom functions you had written in `~/.zshrc`, one can find it here.
3. `alias -L` - This is one of the most helpful commands for me. I had created a lot aliases (shortcuts) to avoid writing long commands. Running this command prints all the `alias`es that are there in the current session. I was able to get all the custom aliases I had defined through this.

### Missing Information
- One will miss all the `source ..` commands one would have written in the deleted `~/.zshrc`. And one will have have to take extra pain to remember what was the `source ..` by looking at the environment variables in the session (`printenv`)

## Future learning to avoid losing important files.
1. It is better to keep a git repo containing one's configuration files. One just will need to keep pushing the changes regularly
2. Another approach is to write a cron job - using [cron tab](https://man7.org/linux/man-pages/man5/crontab.5.html) that would run once a day. It's job would be to create a backup file of `~/.zshrc` (like `~/.backup/.zshrc`).

Ref:
- [https://superuser.com/questions/1043315/dumping-current-zshrc-from-terminal/1046861](https://superuser.com/questions/1043315/dumping-current-zshrc-from-terminal/1046861)


