+++
title = "Super Charge Your Shell with ZSH"
date = "2014-11-08"
description = "Improve your Linux Shell with Zsh"
tags = ["Programming", "Linux", "Zsh"]
+++

I've being using Linux as my primary OS for about 6 years.
Since I'm doing programming all the time, I use shell all the time to perform various task from compiling to commiting code. First I used the normal Terminal comes with Ubuntu
and then for about three years I've being using Terminator. Terminator is better than normal terminal since it has split screen feature. But no matter what the frontend, underneath 
It is still the BASH. 

Resently I came accross a new Shell, ZSH !. Well it is not a new thing, but I haven't came accorss it before. I've to say that ZSH is far better than BASH. It has some really cool features. There is a very nice project called [oh my zsh](https://github.com/robbyrussell/oh-my-zsh) which is a community driven framework to manage zsh configurations. You can use `oh-my-zsh` to install ZSH as well.

To installing ZSH just execute following command. 

    curl -L http://install.ohmyz.sh | sh

It will download and setup zsh as well as scripts related to oh-my-zsh. Above itself tryies to change your default shell to zsh as well, but in my machine that part failed. In case if that happens use following to set it mannually.

    chsh -s /bin/zsh

So what makes ZSH better than BASH?, well there are many reason. I've listed some of them below.

 * Powerful context based tab completion
 * Pattern matching/globbing on alien steroids
 * Themeable prompts
 * Loadable modules
 * Good spelling correction
 * Sharing of command history among all running shells (sometimes this can be annoying as well)
 * Global aliases

 I really like the GIT integration; I can see the current branch I'm working on and wether it is dirty or not directly from the terminal.
 ![Terminal inside a GIT repo](http://sandarenu.github.io/images/zsh-git.png)






