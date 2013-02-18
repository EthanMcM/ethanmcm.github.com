---
layout: post
title: "Bang for Your Torrent Buck"
description: "How much does seeding a torrent really cost?"
category: Entertainment
tags: [torrent, movie, music, porn, r, software, games]
---
{% include JB/setup %}
I’ve often wondered how much each download of a torrent costs the owner of the respective product. For instance, how much does a download of the average movie cost the studio who produced and released it? This question can be phrased from a few different perspectives. The seeder, or uploader of the torrent, is shorting the true owner of the torrent some amount of money. The leecher, or downloader, is taking that money and the owner of the product/torrent is losing the money.

### Data
To take a better look at this problem, I’ve looked at two data sets. Both data sets contain the torrents from the website, ThePirateBay (TPB). In 2012, TPB was the most popular torrent site on the web and has continued to maintain its spot at the top in 2013. One data set contains torrents uploaded as recently as 2012 and has over a million observations while the other has roughly 600,000 observations. Each has its upsides; the first data set has the names of the torrents while the second data set has the categories of the torrents but lacks the names and also only contains data as recent as 2008. I used both in my analysis, but relied heavily on the 2012 data. The only categories that I pulled from the 2008 data were albums, audiobooks and comic books.

To those new to torrenting, you can pretty much download everything. TPB has made it pretty convenient too by categorizing these torrents into a number of helpful sections and subsections. For example, I can easily peruse a section on audio books or take a look at the bevy of different porn movie clips…for science. This is not only a dream come true for the average porn junkie, but also very helpful in answering the aforementioned question. In other words, it’s now possible to see the downloading traffic for a specific category.

### Methodology
Okay, so we have the data source, now what do we do with it? Rather than detail each component and nuance involved in answering this question for all categories, it’s best to give an example of how I did it for a specific category. Let’s take a look at the movie industry. 

When people download movies online through torrenting websites, they can choose the quality of the movie, much like when a person purchases a movie legally and decides between a Blu-ray copy and a DVD. In fact, downloaders are normally presented with these two options as well as various forms of camera recordings taken inside a movie theater. (This last option is normally the most viable for torrenters of movies that have yet to be released on home theater.) So when I put together the equation for movies, I incorporated Blu-ray, DVD, and CAM recordings. Here’s how I computed the Blu-ray piece.

The actual numerical inputs in the equation above is specific to Blu-ray movie torrents but the general components on the right hand side stay the same from category to category. Each movie torrent has a corresponding number of seeders and leechers which is important in calculating the ratio in the summation. This ratio indicates how much a torrent is “shared” because of one seed. I averaged the summed ratios which gave me an overall metric for the specific category. The price component is a weighted average of data from the corresponding torrent category, in this case Blu-rays. I pulled the pricing data from amazon. Ideally, I could match torrents by their name to the products on amazon and get a price quote but this is easier said than done. Instead, I looked up pricing data for a type of product, binned these figures into equally spaced bins (ex: $.01 -$5.00, $5.01 - $10.00, etc.) and created a weighted average based on the number of products in each bin.

So far so good? K, the last part is figuring out how to distinguish between a Blu-ray movie torrent and a DVD torrent. I used the stringr package in r within a function I created which also calculates the aforementioned ratio. I’ve included a sample bit of code below to detail this procedure.

    #Blu-ray
    bluray <- c("bluray", "brrip", "bray", "bdrip", "blu-ray",
            "blu ray", "blu.ray", "bd-rip","bd rip", "br rip",
            "br-rip", "b ray", "b-ray")
    tvtags <- c("tv", "all[. ()]episodes", "season", "seasons", 
                          "s[0-9][0-9]", "e[0-9][0-9]-e[0-9][0-9]",
            "s[0-9][0-9]e[0-9][0-9][. ()]")
    fs <- function(data){
    if(any(str_detect(as.character(data), bluray) == T) & 
       any(str_detect(as.character(data), tvtags)) != T){
    tpb_torrents2$sl[which(tpb_torrents2$Name == data)[1]]
    }
    else{
    NA
    }  
    }
    
    br <- laply(tpb_torrents2$Name, fs, .progress = "text")
    br <- br[!is.na(br)]
    sum(br)*brpr/length(br)

That’s the basic structure. I applied this to the 16 categories and came up with some pretty cool results.

### Results
I’ve written too much already so I’ll try to keep it brief. I made an infographic which should explain a lot of my findings but I made a list of important points for the things that aren’t immediately clear (or on the infographic).

- The seed cost doesn’t differ that greatly from the market price average for each category. All categories range from 1.23 to 2.61 times their market price average.
- The seed ratio really tells how much traffic each category has. From the data, PC Games have the most traffic on average whereas Mac Software has the least.
- It’s interesting that individual songs are not as prolific in the music category as albums. How much of this is due to sites like sharebeast.com or hulkshare.com or even people just streaming these songs on YouTube (assuming these people don’t go and download the single on iTunes)?
- Porn too. While there is quite the selection of porn, its seed ratio is lower than the mean (1.60). So where do people go for their free porn?
- The ideal way to come up with the seed costs would be to match each torrent to the specific product and then create torrent prices using its own price data. It’s good to dream.

Last but not least, I tried doing some regression analysis on the data but it didn’t turn out all that well. Using the 2012 complete data set didn’t produce anything worthwhile and when I used the summary data, I came to the rather obvious conclusion that market price greatly affects torrent price. I’ve included a scatterplot of the summarized data which didn’t seem to have anything eye popping to me. I might need to brush up on my regression.

And that’s it for now. More coming soon. 
**–Ethan**

