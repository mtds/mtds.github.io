---
layout: post
title:  "My screen terminal config"
date:   2016-09-29 14:56:12 +0200
categories: linux screen
---

[screen], the full-screen window manager, has build up a reputation of being a little bit tricky to configure. There are tons of posts around the Internet about how to configure it, so I will just add my config file to the list.

Here we are:

{% gist mtds/61187b329d28aa81e0db1a24942dbc89 %}

I believe the *hardstatus* (hardware status string) needs a little bit more of explanations. The following is a complete list of the parameters used in the string:

* %{= wK} : set colors to bright white (W) on bright black (K) and keep current text styles (=)
* %H      : hostname
* [       : opening bracket character
* %`      : print output of 'backtick' command (defined elsewhere in .screenrc)
* ]       : closing bracket character
* %{= wW} : set colors to white (w) on bright black (K) and keep current text styles (=)
* \|       : bar character
* ${-}    : restore colors to previous colors / undo last color change
* %-Lw    : list windows before current window (L [optional] = "include flags")
* %{= bW} : set colors to bright white (W) on blue (b) and keep current text styles (=)
* %f      : window flags
* %t      : window title
* %{-}    : restore colors to previous colors / undo last color change
* %+Lw    : list windows after current window (L [optional] = "include flags")
* %=      : expand to fill all space (used here to make remaining content flush right)
* %C      : current time (12-hr; 24-hr is %c)
* %a      : am/pm (lowercase; uppercase is %A)
* %Y      : current year
* \-       : hyphen character
* %m      : current month (0-padded; %M for "Jan" etc.)
* \-       : hyphen character
* %d      : current date (0-padded)

[screen string escapes] are explained here in full detail.

[screen string escapes]: http://www.gnu.org/software/screen/manual/html_node/String-Escapes.html
[screen]: https://www.gnu.org/software/screen/
