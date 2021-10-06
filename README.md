# hk-timezones v.0.0.1
Haskell Timezones - A back and frontend solution to calculate timezones conversions, written in two completely functional languages: Haskell and  Elm.


# Contents
1. About
2. Reasoning
3. Haskell
4. Elm
5. Q & A


## About

Well, first off, let me just say a heart friendly Hello to all of you who drop by my repo. It is here that I will upload all my attempts to code a Timezone Conversion Tool (Backend and Frontend). 
I'll separate the two solutions each into its own folder. I'll also document my progress and the problems I've encountered thus far.

## Reasoning

I've encountered a client on Upwork that wanted me to design and architect a solution of my own choosing in Haskell and preferably another functional language as frontend. I've quickly decided for Elm since I've worked with it in the past.
Also, by being functional in both ends, it's kind of nice to see how we can reuse types and how each of the two languages employs serialization.
The idea behind the project was that it offers plenty of opportunity for problem solving, tweaking and it's a pretty neat way to discover FP through this task. It's not too simple yet hopefully not too complicated.

So, please bear with me.

### So what is the problem with timezoning ?

![timezones map from timeanddate.com](https://c.tadst.com/gfx/600x337/timezone-map5.png?1)w

Who doesn't want us to have an easy way of converting from one timezone to another ? In short: governments. But before you break out that circled "A" t-shirt and those Molotovs, how can we reason about timezones so that we keep it to a minimal and clean perception ? We deal with: 
- non-standard timezone lengths (as [timeanddate.com](https://www.timeanddate.com/time/current-number-time-zones.html) would have us know: "If each time zone were 1 hour apart, there would be 24 in the world. But several time zones have only 30 and 45 minutes offsets, making the total number worldwide much higher."
- Daylight Saving Time : meaning we have to keep candlesticks to a minimum burn and setback our clocks to gain as much day light at evenings with its countereffect of losing more light at the early hours of the morning. I don't know what's more important, having light to brush your teeth and make your coffee or having more light outside in the evening when you're not in a hurry to go to work? But ... that's just me.
- Some places decide they don't need DST : which means sometimes, we must adjust for those of us who are living in this century and don't take part in the whole "save the candles" movement. Good news is, there are more and more of those that see the light.(pun intented).
- Time Zone Borders Vary : for every 1-hour timezone difference, there's 15 degrees of globe curvature (as seen on the above image as well) - resulting in a 1 hour [mean sun time](https://www.timeanddate.com/time/local-mean-time.html) equivalence.
- Governments : "Some geographically large (wide) countries, like India and China, use only 1 time zone, while it would have been natural to expect several, like in the US or Australia." - [timeanddate.com](https://www.timeanddate.com/time/current-number-time-zones.html). "Remember remember, the 5th of November" takes on a whole different meaning when you ask in which Timezone it happened.
- UTC is Base : "Every place on Earth is measured in terms of its distance east or west of the prime meridian (0°longitude) in Greenwich, London, United Kingdom." - [timeanddate.com](https://www.timeanddate.com/time/current-number-time-zones.html). Kinda like how all arabic written languages start from right-to-left or left-to-right depending on the land they're written in and its position of east or west of Jerusalim - where it needs to flow to. Of course this isn't ENTIRELY true as Japanese people write top-to-bottom, and it;s said that the actual change to Left-to-Right script originated in Ancient Greek . However, there's an [International Date Line](https://en.wikipedia.org/wiki/International_Date_Line) just as there's an International Write Line.
- sometimes more problems appear. If you don't believe me, check this one out: 

>I have a great example of an arbitrary time zone change. In July 2018, Japan considered observing daylight saving time during the summer of 2020 to make the Tokyo weather more bearable during the 2020 Olympics. The committee even considered offsetting the time by two hours instead of one! It is fortunate that this idea did not get implemented, as I suspect that it would have caused many more problems in Japanese infrastructure than Y2K (at least in the systems that do not run on paper and fax). Since all of Japan is in a single time zone that does not observe daily saving time, many systems here take a simple approach and do not handle time correctly; there would have been many hours of overtime as developers scrambled to fix systems on short notice, as well as many issues during that summer. -[Cross-platform timezones in Haskell](https://www.extrema.is/blog/2021/07/26/cross-platform-time-zones-in-haskell)

- and a lot of other things. so let's jump ahead just to keep this place light and neat.

To sum up, there are **Currently 38 Different Local Times in Use**. And as anything complex, it must be abstracted away in our algorithm so that we can solve our problem effectively. It's important to know that we depend on Standards, which are made by humans, and which change sometimes without warning, nor with backward compatibility. So our job will be to translate these timezones as static data types, and see how to make some operators between them. We could of course use a preexisting library that magics our tz conversions, but I think it would actually be beneficial to see how the nitty gritty works.

## Haskell

I've only just started thinking about the types in Haskell. This is an important step since they will be inter-usable and in FP the first and foremost problem is identifying good and solid types. 
