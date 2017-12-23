---
layout: post
title: "Twitch Chat Ruby Gem"
modified:
categories: articles
excerpt:
tags: [twitch, chat, ruby, gem, irc]
comments: true
share: true
---

Actually, it doesn't exist yet. But Twitch using IRC. So, you can use IRC client for twitch chat.

I have found this IRC client gem: [https://github.com/r7kamura/zircon](https://github.com/r7kamura/zircon)

And wrote small script for twitch chat: [Gist](https://gist.github.com/4b037da377326d6725dc)

Your password should be an OAuth token. You can get your OAuth token here: [http://www.twitchapps.com/tmi/](http://www.twitchapps.com/tmi/)


### Usage

- Get script

```sh
$ wget https://gist.githubusercontent.com/ecylmz/4b037da377326d6725dc/raw/927943bd12210a03cca4dee0dcd7f3247607a22d/twitch_zircon.rb
```

- Install dependencies

```sh
$ gem install zircon colorize
```

- Run the script

```sh
$ ruby twitch_zircon.rb
```

### Screenshot

<figure>
        <img src="http://ecylmz.com/file/ruby_twitch.png">
</figure>
