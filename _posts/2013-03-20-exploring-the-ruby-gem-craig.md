---
layout: post
title: "Exploring the Ruby gem Craig"
date: 2013-03-20
categories: ruby craigslist
comments: true
---

Using Ruby to search Craigslist
=================
I want to buy a motorcycle on [Craigslist.org](craigslist.org), and I want to get an idea of how the used motorcycle market behaves.
To that end, I want to make a few specific searches on Craigslist, once a day for a few weeks, and record their results for later analysis.
I would normally do this sort of scraping exercise in Ruby with [Nokogiri](http://nokogiri.org/) or in Python with lxml,
but this is Craigslist, and there are two Ruby gems made just for scraping Craigslist.
The first is called [Craigslist](https://github.com/gregstallings/craigslist), and the second, a derivative,
is called [Craig](https://github.com/danneu/craig).
In this post I'll discuss the first gem, craigslist. I'm running Linux Ubuntu 12.10, with Ruby 1.9.3.

Install craigslist
-------------------
The first step is to install the craigslist gem:
```
$ gem install craigslist
```
Some people might get a 'missing requirements' error mentioning 'libxslt'. If so, install some missing dev packages, and then try again:
```
$ sudo apt-get install libxslt-dev libxml2-dev
$ gem install craigslist
```
Now see if it works by firing up Interactive Ruby (Irb):

```
$ irb
>> require 'craigslist'
=> true
>> Craigslist.cities
=> ["atlanta", "austin", "boston", "chicago", "dallas", "denver", "detroit", "houston", "las_vegas", "los_angeles", "miami", "minneapolis", "new_york", "orange_co", "philadelphia", "phoenix", "portland", "raleigh", "sacramento", "san_diego", "seattle", "sf_bayarea", "wash_dc"]
```
If it returned a list of cities, as above, then it's working.
Use craigslist
-----------

Let's see what categories are available (I'm requiring [pretty print](http://www.ruby-doc.org/stdlib-2.0/libdoc/pp/rdoc/PP.html#method-c-pp)
to format the output):

```
>> require 'pp'
>> Craigslist.categories
>> pp Craigslist.categories
["accounting_finance",
 "activities",
 "admin_office",
 "antiques",
 "appliances",
 "apts_housing",
 "arch_engineering",
 "art_media_design",
 "artists",
 "arts_crafts",
 "auto_parts",
 "automotive",
 "baby_kids",
 "barter",
 "beauty",
 "beauty_hlth",
 "bikes",
 "biotech_science",
 "boats",
 "books",
 "business",
 "business_mgmt",
 "cars_trucks",
 "casual_encounters",
 "cds_dvd_vhs",
 "cell_phones",
 "childcare",
 "classes",
 "clothes_acc",
 "collectibles",
 "community",
 "computer",
 "computer",
 "computer",
 "creative",
 "creative",
 "crew",
 "customer_service",
 "cycle",
 "domestic",
 "education",
 "electronics",
 "etc",
 "event",
 "event",
 "events",
 "farm_garden",
 "farm_garden",
 "financial",
 "food_bev_hosp",
 "for_sale",
 "free",
 "furniture",
 "garage_sale",
 "general",
 "general",
 "general_labor",
 "gigs",
 "government",
 "groups",
 "household",
 "household",
 "housing",
 "housing_swap",
 "housing_wanted",
 "human_resources",
 "internet_engineers",
 "jewelry",
 "jobs",
 "labor",
 "labor_move",
 "legal",
 "legal_paralegal",
 "lessons",
 "local news",
 "lost_found",
 "manufacturing",
 "marine",
 "marketing_pr_ad",
 "materials",
 "medical_health",
 "men_seeking_men",
 "men_seeking_women",
 "misc_romance",
 "missed_connections",
 "motorcycles",
 "music_instr",
 "musicians",
 "nonprofit_sector",
 "office_commercial",
 "parking_storage",
 "personals",
 "pet",
 "pets",
 "photo_video",
 "politics",
 "rants_and_raves",
 "real_estate",
 "real_estate",
 "real_estate_for_sale",
 "resumes",
 "retail_wholesale",
 "rideshare",
 "rooms_shared",
 "rvs",
 "sales_biz_dev",
 "salon_spa_fitness",
 "security",
 "services",
 "skilld_trade",
 "skilled_trade_craft",
 "sm_biz_ads",
 "software_qa_dba",
 "sporting",
 "strictly_platonic",
 "sublets_temporary",
 "systems_network",
 "talent",
 "technical_support",
 "therapeutic",
 "tickets",
 "tools",
 "toys_games",
 "transport",
 "travel_vac",
 "tv_film_video",
 "vacation_rentals",
 "video_gaming",
 "volunteers",
 "wanted",
 "web_info_design",
 "women_seek_women",
 "women_seeking_men",
 "write_ed_tr8",
 "writing",
 "writing_editing"]
```

This is a huge list, and it's worth taking a moment to compare it to the Craigslist.org homepage to see what we're looking at.

![Craigslist page screenshot]({{ site.url }}{{ site.baseurl }}/assets/craigslist_motorcycle_listing.png)

Take just the "A's" from the categories list returned by the craigslist gem and see where everything comes from.

```
"accounting_finance",
 "activities",
 "admin_office",
 "antiques",
 "appliances",
 "apts_housing",
 "arch_engineering",
 "art_media_design",
 "artists",
 "arts_crafts",
 "auto_parts",
 "automotive",
```

Notice that just among the "A's", `accounting_finance`, `admin_office`, `arch_engineering`, and `art_media_design`
come from the "jobs" heading, `activities` comes from the "community" heading, `antiques`, `appliances`, `arts_crafts`,
and `auto_parts` come from the "for sale" heading, and `automotive` comes from the "services" heading. There are a few things to notice:

 * The headings themselves (from the Craigslist.org homepage) are available as categories in the craigslist ruby gem.
 * The categories returned by the craigslist Ruby gem are not spelled, spaced, capitalized, or ordered exactly as they appear on the Craigslist.org homepage.
 * Nothing under "discussion forums" is available through the Ruby gem. 

Search with craigslist
----------------------
Now let's do some searching through the "motorcycles" category.

```
>> pp Craigslist.sf_bayarea.motorcycles.last(3)
[{"text"=>"Alpinestar Jacket US36 EU46",
  "href"=>"http://sfbay.craigslist.org/sby/mcy/3692451503.html",
  "price"=>"$300",
  "has_img"=>false,
  "has_pic"=>true},
 {"text"=>"'03 anniversary heirtage softtail",
  "href"=>"http://sfbay.craigslist.org/nby/mcy/3674061914.html",
  "price"=>"$10000",
  "has_img"=>false,
  "has_pic"=>true},
 {"text"=>"1983 CR 480 DIRT BIKE",
  "href"=>"http://sfbay.craigslist.org/scz/mcy/3638375348.html",
  "price"=>"$1000",
  "has_img"=>false,
  "has_pic"=>true}]
```

This search returned the most recent 3 items from the SF Bay Area motorcycles listing.
However, if we look at the corresponding Craigslist.org page, we see something slightly different.
The items returned by the craigslist gem are present, but they're not the most recent items.
The below image shows a screenshot of the Craigslist.org page, with the items returned by the craigslist gem surrounded by a black rectangle.

![screenshot of craigslist showing motorcycle results]({{ site.url }}{{ site.baseurl }}/assets/craigslist_motorcycle_listing.png)

I don't know why these don't correspond exactly, but the fact that they don't is something to be aware of.

craigslist Source Code
---------------------
The craigslist gem Github page has some documentation.
But to get a better understanding of what we can do with craigslist, we have to look at the source code.
The easiest and clearest way to do this to to [read the source code on Github](https://github.com/gregstallings/craigslist).
Here are a few useful pages to look at.

[craigslist/spec/craigslist_spec.rb](craigslist/spec/craigslist_spec.rb) This page is where the author wrote out some tests.
This is the easiest code to follow because he writes out in English what each test should do.
These tests illustrate all of the methods available in the craigslist gem.

[craigslist/lib/craigslist/categories.rb](https://github.com/gregstallings/craigslist/blob/master/lib/craigslist/categories.rb)
This file contains a Ruby hash called CATEGORIES that gives the associations between the categories as they are written in the craigslist gem and the categories as they appear as Craigslist.org URLs.
For example, by looking in this file you can find the category for "motorcycles" (under the "for_sale" heading)
and see that the corresponding Craigslist.org URL is 'mca'.
craigslist/lib/craigslist.rb This file holds the main logic of the craigslist gem.
A glance at the method names shows where the cities, city?, categories, and last() methods come from.
Under CATEGORIES on line 49, we can see the build_uri() and more_results() methods, which are called by last().
A glance through last() shows that it builds a Craigslist.org-style URL (e.g. sfbay.craigslist.org/mca)
and then uses [Nokogiri](http://nokogiri.org/) to parse and return the results.
(To see how Nokogiri works, check the documentation)

That's it. By reading through the source code, we see that the main and only functionality of the craigslist gem is to take a
city and category and use the `last()` method to return some number of recent results from the corresponding Craigslist.org page.
No further query methods or refinements are available.
For that we turn to another gem, this one called craig, which is the subject of the next blog post.

Summary
--------
The craigslist gem has the capability to take a city and Craigslist.org category as arguments, and return a well-structured
Ruby array of text scraped from the corresponding Craigslist.org page.
The main and virtually only functionality is as follows:

```
$ irb
>> require 'craigslist'
=> true
>> Craigslist.sf_bayarea.motorcycles.last(100)
```

It is a robust but limited gem.
Query refinements are not possible, and further processing or analysis of the returned values is done outside the craigslist gem.
Thanks are due to [Greg Stallings](https://github.com/gregstallings) for his work on the gem, and for releasing it as open source.
