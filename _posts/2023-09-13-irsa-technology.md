---
title: iRSA - The Technology
date: 2023-09-12
categories: [Vue, APIs, statistics, development]
tags: [iRSA]
---
In this post about the technology behind iRSA, I am going to describe what components will be used to build the first iteration of iRSA and how they will fit together in a light textual summary of the architecture of the application.
## Front End
### Frameworks
I'm not primarily a front-end developer, but I am familiar with Javascript/CSS/HTML.  Because of this I'm looking for something that's relatively easy to ramp up with and can provide mostly ready-to-use UI components that fit the vision for this site.  I've spent some time digging through the options available and settled on Vue feeling the most accessible for me, and having the most capable supporting frameworks available that enable the seamless distribution of mobile apps from the same code base.

At this point I'm leaning towards using PrimeVue for it's component library and theming alongside capacitor for mobile deployment.  A close second place would be Quasar for it's ability to provide a full deployment path for mobile in addition to web; though it's material UI design is significantly dated at this point and was the primary driving force behind going with another option.
### Needs and Desires
#### No SSR
It is a hard requirement for this project to avoid SSR.  This will only increase the complexity of the project, site response time, and expense of hosting, without providing much benefit for the end user.  There is no sense in providing data on-demand, and lagging slightly behind official results is acceptable, there is no indication that on-demand results will increase the desirability of the app at all.
#### Minimal Page Load
In order to minimize page load time as much as possible, we will want to prioritize fetching and displaying data over rendering images.  This goes side-by-side with the desire to calculate data in lieu of providing actual data points; the back end will provide coefficients for each car and track combination, while the front end will calculate laptimes on-the-fly in javascript as opposed to fetching raw data each time the page or sub-pages are loaded.  This allows us to create a much wider range of statistics without bloating the initial json object too much.
Unfortunately this pales in comparison to the amount of data transfer we will need for delivering series logos, and track images.  The same process that fetches and compiles data will also resize logos to a size more friendly for the web and the intended display size of those images.

## Back End
Given the projects reliance on an entirely static site, this means that we'll need to occasionally render data to push to buckets for consumption by the site.  This should be an entirely automated process where the presence or absense of a series depends entirely on the data provided by the iRacing API, we should never assume that something does or does not exist; and instead trust our data source.  Because of this we will be interested in performing a full update of all race metadata on every API pull.  This will prove necessary particularly for newer series with uncommon properties such as Ringmeister, or the 13th week series which change tracks daily.
### Data Fetching and Transposition
As a rule of thumb, we'll want to make as few requests to the iRacing data API as possible.  We are guests and we should only take what we need.  Because of this we will want to immediately transpose and cache the data in a format that we can use more efficiently while also keeping track of the data we have already pulled to reduce duplicate efforts.
#### What is exposed?
The iRacing data API is generally not very flexible in allowing us to query for only the data we want.  It seems that the API itself was built with very specific functionality in mind; so some of the work we do will end up being quite expensive and require careful tracking of our rate limit.  There is quite a lot of data at our disposal here; but ultimately we will need to present it in a very different format to our web page to ensure visitors have a quick and responsive experience.
#### What data do we need?
##### Active Series
We already discussed the need to fetch information on each active series every time we pull data.  For that we'll need the stats_series endpoint
* <https://members-ng.iracing.com/data/series/stats_series>
  * This tells us almost everything we need to know.  An active series is `"active": "true"`, but we unfortunately can't filter by this.
* <https://members-ng.iracing.com/data/carclass/get>
  * Again we can't filter here, but once we've pulled down the complete list of classes we can discern individual car IDs.

##### Laptimes
We will need to keep track of individual laptimes.  This includes qualifying, average, and fast laps.  This is the bread and butter of the data that we offer.
We may also be interested in recording whether this driver completed most of the race or dropped out early.
* <https://members-ng.iracing.com/data/results/search_series>
  * What interests us about this endpoint is we can filter down a list of subsession IDs by date range.  We'll want to only fetch data that we don't have yet; these fetches can be very expensive and will be the majority of our calls to the iRacing API
* <https://members-ng.iracing.com/data/results/get>
  * Once we have subsession IDs of interest we can make individual queries to this endpoint.  This will allow us to build out our results by driver
  * Unfortunately qualifying times are not provided in race results, so we'll need to make a separate query for qualifying sessions to document those times

##### Incidents
Calculating the incident rate by series for each week could also be a very interesting metric.  This can give users a good indication of whether or not they should stay away if they're interested in maintaining their safety rating.  We can calculate incidents per corner with the equation $i/(lc)$ where $i$ = incidents, $l$ = laps completed, $c$ = corners per lap.  All the data necessary for this is available in the race results.


##### Car, Class, and Series
In order to reduce on data duplication in cache, we will want to subdivide data by car, class, and series.  We can build this into our bucket structure like so:
`<year>/<season>/<week>/<series_id>/<class>/<car>/<results_data>`

##### Participation
This is an easy one and we get this data for free by simply counting the number of results that we've cached for each race.  Tracking the irating of each result will also help us calculate participation by iRating, helping the user determine how they might stack up against the bulk of the competition.

---

All of this is to say, the data used to populate the website will be fetched from a bucket by the client during page load.  The front end will serve to filter and present this data in a digestable way.  The metadata loaded on the home page will be a highly trimmed down version of more detailed data available on sub-pages for each series.  We'll want to implement some filtering options to ensure we're only showing what the user is interested in, but other than that this concept is fairly simple.  Once the MVP is complete we will look into remembering user preferences for filtering, and even allowing users to configure their iRacing account to fetch their profile data and filter for races their account is eligible for.
