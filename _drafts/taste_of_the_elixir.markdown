---
layout: post
title: "A Taste of the Elixir (lang)"
date:   2015-07-01 00:00:00
categories: Elixir
author: Yoad Snapir
---

Installed Chocolatey.

Installed Elixir using Chocolatey.
From an administrator cmd ran:

`cinst elixir`

which downloaded and installed both Erlang and Elixir. (Chocolatey is so awesome.. Windows users were missing out so much all these years without a "yum" or an "apt-get" for that platform).

On the same command window, assuming the path is already OK to go, I followed the "Getting Started" guide and ran:

`iex`

And got the polite:

    'iex' is not recognized as an internal or external command,
    operable program or batch file.

Yuck, so i guess the guide is not up-to-date since checking the chocolatey bin directory shows that the exe is actually called `ielixir`. Here goes:

`ielixir`

And got:

    Interactive Elixir (1.0.5) - press Ctrl+C to exit (type h() ENTER for help)
    iex(1)>

Thanks.

    iex(1)> 1 / 1
    1.0
ok.
What about:

    iex(2)> 1 / 0
    ** (ArithmeticError) bad argument in arithmetic expression
        :erlang./(1, 0)
sure enough.  
What is this strange `:erlang./(1,0)` thing? The underlying function syntax of Erlang perhaps? Lets see.

    iex(7)> :erlang.*(2,2)
    4
Ah.. ok. What about:

    iex(7)> :erlang.*(2,3,3)
    ** (UndefinedFunctionError) undefined function: :erlang.*/3
    :erlang.*(2, 3, 3)
Gotcha.

Trying to stay away from the "hello world" examples.. (don't have time to actually learn anything right now) Let's throw at it a desperate cry for help:

    iex(8)> help
    ** (RuntimeError) undefined function: help/0

Is everyting a function for you?! guess so.

Till next time...





    

