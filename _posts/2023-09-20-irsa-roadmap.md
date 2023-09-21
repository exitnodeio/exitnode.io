---
title: iRSA - The Roadmap
date: 2023-09-20
categories: [iRacing, Program Design, MVP]
tags: [iRSA]
---
Now that the POC for iRSA is done and we are more or less achieving what we set out to do it's important to outline the things we want to improve upon, and the feature additions that can bring value to the app as well as the relationship between priority and value add of all the future work to be done to the site.

## Reflecting
When I set out to begin this project; it had a relatively simple goal that could be filtered down to the following objectives:
* Collect statistics from the iRacing data API
  * Transform that data in a way that is usable for a CSR front end
  * Compute coefficients for laptime estimation equations
* Display a list with some images and text

The majority of this work was heavy on the back end, because webdev isn't my strongsuit, and despite this I've learned a lot about Javascript and Vue.  In only a few short weeks I've been able to smash these goals, and we are well on our way to expanding on what exists now.  Some data is computed and pushed to the frontend and not exposed; but for good reason.  My priority has been UI/UX first; I don't want users to have information overload and I want the information I do have to be conveyed in a pleasing and uncluttered manner.
I've also been focusing on maintaining a good relationship between small and large devices, as a first time web developer this is a struggle, but it's going well!  I still have plenty of work to do to make both large and small devices feel like a native experience, but this too is coming along.  Once a consistent UI is identified and complete, more granular data can be built into the app.

## Looking Forward
### Improvements
#### Styling, Styling, Styling!
The user experience here needs to be flawless.  What exists now looks good; but it still has quirks, this is what I'm aware of and actively working on improving:
* Class names are too long!  I will be working on abbreviating these in a way that is more pleasing to the eye in a table while still communicating the nature of the class.  This is the only manual maintenance so far; and if this falls behind in any way the only impact is having longer class names clipped off like we see now.
* Lap times just don't work in the main card on mobile.  There isn't enough space to fit these comfortably, and their display front and center may have to remain a feature for large screens.  They also take away from valuable real estate to communicate quick and useful information about the race like how long it is, what category, multiclass, etc.
  * This will be addressed with large dialogs on all screens; on mobile this will have to be how users retrieve lap data.
* Full screen on a desktop is quite sparse.  It wouldn't hurt to fill that space with something, but we also don't want it to be too busy.  My initial thought is two columns of cards to separate Road and Oval racing (or show more results for one if the other is filtered out)
#### Laptimes!
This is what you came here for; I think.  At least it's the reason I built this site.  Right now I think the data we have does a really good job at estimating the lap time needed for any particular irating to place roughly middle of the pack.  I want to do better.


![Image]({{site.url}}/assets/img/qual_fit.png)


The ideal for how this fit should work in the future is by using two separate fits instead of one and blending between them.  This will give users a new slider to tweak, and they will be able to set their own performance goals to affect the data exposed to them.  The way I want to achieve this is instead of calculating a mean fit as above, I will calculate an upper and lower 95% confidence interval.  Blending between them will allow users results to end up exactly on the spectrum aligned with their desire.  Crank the slider all the way up to see what laptime you might need to win every time, or all the way down if... the point is you have the option!  Not everyone wants to compete at the highest level; but likewise others may be targeting results that consistently increase their irating and this feature can help with that.
### New Features
#### Series Danger!
This was touched on in my last blog post; but the data has been collected to cover incidents per corner (IPC) for each series.  I don't have a high level of confidence in this data yet as it hasn't been my primary focus.  Ultimately I would like to outline here in the future exactly how I am going to calculate how dangerous a series is.  This won't just be a flat value based on average IPC, it will also be a function of iRating in addition to some other factors like average race distance, and safety license averages.
Ultimately exposing a number here will be fairly difficult for users to interpret; .02 IPC doesn't mean anything to most people.  This is why I'm leaning towards a calculation that can display how dangerous any given series is at any given iRating on a scale of four pips, four pips being the most dangerous, and one pip being the safest.  Of course this is quite a lot of hand-waving and the majority of this code still needs to be written, but I think there would be a huge appreciation of this kind of feature.  It's not always intuitive _why_ a series will be dangerous so it will be helpful to know that ahead of time!
#### Info, at a Glance
The accompanying information that drivers will be interested in seeing is something I absolutely want to include here; though it's not the focus.  The way iRacing achieves this across it's new UIs is often through chips, and I have a strong interest in doing the same here.  Chips are like tags, we can apply a list of them to an item and they are a clean way of exposing mutable lists of information to describe something.
#### Keeping Time
Currently this data is not collected, but will be soon.  New results are fetched on the hour, every hour.  This is more than enough for us to collect upcoming races for each series and offer the option to sort by races starting soon.
#### Anything else _you_ want!
If you made it this far, I have your eternal gratitude and I want to hear from you.  If you have suggestions, issues, or just want to chat you can contact me at <hello@exitnode.io>
