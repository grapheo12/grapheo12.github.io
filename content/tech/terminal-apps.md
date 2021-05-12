+++
title = "Cut bloat, move fast with Terminal apps"
date = 2020-01-21

[taxonomies]
tags = ["terminal"]
+++

The traditional GUI based applications for general purpose use are
typically built for the laymen. But we ain't no laymen, are we? We are
developers: productivity is the key to our sustainance.

<!-- more -->

Majority of the GUI based applications come with some extra features
that we don't even care to use. The first step towards choosing such
bloat-free app, in my opinion, is to do away with the GUI itself and
focus on simple apps that only do what they are supposed to do.

Now I am not talking about doing everything in a shell. Some tasks like
Web Browsing, Image and Video playing, editing and recording are better
done using graphical applications.

Apart from that, pretty much everything can be done through the
terminal.

## Major advantages of using the terminal

1.  **Lightweight interface**: Most terminal apps either take input from
    the argument variables or provide *Ncurses* base TUI to play with.
    Thses are way lighter on the RAM than traditional GUI frameworks
    like QT or GTK.
2.  **No mouse involved**: Terminal apps rely solely on the keyboard for
    input. So the time wasted to find an option or menu by clicking is
    reduced. Most apps come with man-pages to help you if you get stuck.
3.  **Low dependency**: These app rely on very few dependencies. Hence
    the download size is quite low. Apps can work standalone.

## Some terminal apps you SHOULD use

Let me introduce some cool terminal apps that easily win over their GUI
counterparts.

### Vim

For most people, Vim is an obscure text editor. But for Vim users, it's
a way of life. I am writing this article in Vim. The learning curve is
steep. But once you get used to it, it takes over your mind.

**Vi** comes installed in almost all Linux distros. Vim is an
improved version of Vi. It's highly configurable through the
**.vimrc**. You can even install themes and plugins to add on
to your experience.

Vim is a modal text editor. Meaning: when it opens files, it is meant
for reading and minor editing (*Normal* mode). You switch to *Insert*
mode to type text. There are other modes, eg *Visual* mode for
selection, *Visual (Block)* mode for block selection and editing etc.

### Ranger

It is a terminal based File Manager. It's extremely fast and
configurable.

The best thing about ranger is its ability to do bulk operations on
files. For example, bulk rename, bulk delete etc.

And the second best thing is that it supports Vim keybindings. :-)

### Cmus

Cmus is a terminal music player. It too uses Vim-like keybindings.

It is meant for blazing fast processing of music data spanning many GBs.
Even being a terminal app, it integrates with the GNOME taskbar very
well.

### nmtui

It is a Terminal utility for the **NetworkManager** service.

If you are using a minimal Window manager, in my opinion,
**nmtui** is far better tool to configure connections than the
**nm-applet**.

### amixer

It is the sound management utility provided by ALSA. It is extremely
intuitive and easy to use.

## Ending Notes


I am slowly discovering this world of free and open source utilities.
Hence the list is quite short here. But I intend to add more as I use.

Of particular mention, is the Suckless project, that aims at making
bloat-free software. I did not include their products as I have not used
them.

This post is heavily inspired by DistroTube and Luke Smith. Do check out
their YouTube channels.

