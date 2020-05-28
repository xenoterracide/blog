+++
title = "Gradle With Gdub"
date = 2020-05-28T13:22:49-05:00
description = "run gradle with gdub instead of gradle or ./gradlew"
tags = ["gradle", "gdub", "java", "development", "build-tools", "monorepo"]

# noLicense = true # Do not show license in this post
# playerName = "..." # Audio title or name
# playerArtist = "..." # Audio artist
# playerURL = "..." # Audio URL, support aac, mp3, wav and ogg
# playerCover = "..." # Audio cover
+++

So I found this cool tool the other day when researching how to do Gradle multi-module (monorepo). The tool is called `gdub` and what it does, is it behaves more or less like `git`, instead of having to be in the directory with `./gradlew` all you have to do is be in the directory, or a sub directory, and it will fall back to `gradle` on the path if `./gradlew` isn't present. You can use it like this

```sh
gw build
```

or replace build with any gradle command or option. Visit the [website](http://www.gdub.rocks) for more information.
