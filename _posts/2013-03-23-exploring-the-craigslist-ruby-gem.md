---
layout: post
title: "Exploring the Craigstlist Ruby gem"
date: 2013-03-23
categories: ruby craigslist
comments: true
---

In [my last post](#) I looked at Greg Stallings's Ruby gem craigslist.
In this post I'll look at [Dan Neumann](https://github.com/danneu/)'s Ruby gem [craig](https://github.com/danneu/craig), which builds upon Greg's work.
Both of these gems allow a user to easily scrape search results from [Craigslist.org](http://craigslist.org/).
Thanks are due to Greg and Dan for making their work public.
I'm running Ubuntu Linux 12.10 with Ruby 1.9.

Install craigslist
---------------

Installation is like any other gem. In a terminal type:

```
$ gem install craig
```

Now enter an Interactive Ruby session and see if it worked.

```
$ irb --simple-prompt
>> require 'craig'
=> true
```

If it returned 'true', then installation was successful.

Use it to find some motorcycles
---------------
I want to see what motorcycles are for sale in the SF Bay Area.
To see how craig expects to see 'SF Bay Area' and 'motorcycles', I look at the source code.
Check [craig/lib/cities.rb](https://github.com/danneu/craig/blob/master/lib/craig/cities.rb) to see how your 
city is spelled/puntuated, and [craig/lib/categories.rb](https://github.com/danneu/craig/blob/master/lib/craig/categories.rb) to 
see what categories are available.
In `categories.rb` I searched for 'motorcycle', and found this line:

```
'motorcycles' => 'mca'
```

This means that craig will expect just the word motorcycles, and will translate it to 'mca' for Craigslist.org.
In cities.rb I searched 'francisco' and found this line:

```
"san_francisco_bay_area" => "sfbay"
```

This means that craig will expect `:san_francisco_bay_area` and will translate it to 'sfbay'.
With this in mind, I can construct my search phrase:

```
motorcycles = Craig.query(:san_francisco_bay_area, :for_sale => :motorcycles)
```

This returns a Ruby array, which I hold it in a variable called motorcycles.
Since it's an array, I can select an individual array item with indexing.

```
>> motorcycles[2]
=> {
  :url => "http://sfbay.craigslist.org/eby/mcy/3710305817.html"
  :title => "HARLEY 2012 FXWG 8 miles"
  :seller => :owner
  :price => 14000
  :posted_at => #<Date: 2013-03-28 ((2456380j,0s,0n),+0s,2299161j)>
  :location => ""
  :id => 3710305817
  :has_map? => false
  :has_image? => true
}
>> motorcycles[2].title
=> "HARLEY 2012 FXWG 8 miles"
>> motorcycles[2].price
=> 14000
```

The first line returns a complete listing.
Each listing element can accessed by appending it with a dot to the listing object, as demonstrated above.
For this Harley example, note that this is not scraping or accessing the actual Craigslist ad page for this Harley.
Instead, it's just scraping the search results page.
If we wanted to get more information about this Harley, we'd have to use the URL (motorcycles[2].url) and look at it (or scrape it!) ourselves.

Differences Compared to Ruby Gem craigslist
-------------------
So what's the difference between this gem, and the Ruby gem [craigslist](https://github.com/gregstallings/craigslist)?
Both gems have one main function which returns a list of craigslist search results.
In craigslist this function is `.last()`, and in craig this function is `.query()`.
Both gems return a Ruby array which can be looped over to search through specific attributes.
The syntax of craig (using Ruby symbols) may be a bit cleaner than that of craigslist (using Ruby hashes).
Lastly, the documentation of craig goes a bit further than craigslist in showing what can be done with the scraping results.
However, it should be noted that searching through returned results is not a capability of either gem; looping and filtering is done in pure Ruby.

Summary
------------------
The main takeaway is that both gems allow a user to do a basic Craigslist.org search,
without parameters such as a price, and both gems return an array of results.
These gems only return search results, and neither is capable of drilling down to the level of individual Craigslist.org listings.
