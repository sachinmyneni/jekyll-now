I had been interested in data related projects for a while. 
Most of the time I dig through our build database and try to make interesting pictures for folks to see. 
Visualizations, that sit on top of good data are a fantastic way to bring clarity. 

[Spotcrime.com](http://www.spotcrime.com) was one of the sites I came across when I moved away from one of the safest cities in the country to a not-so-great place. 
8 years since my move I realize that I am not in that bad a place either. 

But I am still curious about crime rates and how they change so I decided to try to scrape Spotcrime.

I was watching Kimberly Fessel's talk on PyCon2020 about web scraping as I started this project. Some of her tips were very useful and some I learnt the hard way.

This is what my data looked like:
'''
- __state__
     - Area/County/City
       - Date
         - Report_1  Date/Time  Crime  Address  Link_to_Details
         - Report_2  Date/Time  Crime  Address  Link_to_Details
'''

Here is my problem: Each state has anywhere from 5-20 reporting areas. Each area can have a handful to 1000s of reported dates. As I peeked into some areas I saw reports going back a couple of decades! This is going to take some time.

1. I started off with a random 1-10 second gap between scrapes. I did not want to get banned from this site. They do a public service and I did not want to "attack" the site with too many requests. This alone means the scraping will take a very long time (if everything goes well).
1. Save your data: Errors, exceptions, outages, bugs happen. Save your data to a CSV file or a database as you scrape. You don't want to spend a few days on a project like this only to lose what you've downloaded so far. It is a terrible waste.
1. I first started off with simple depth-first tree search: state->place->date. 
   After I worked out all the kinks I left the script to run overnight.. 
   Next morning I discovered it had failed on a link: timeout. This is OK. 
   I had saved each date's data into a CSV (date,crime,address,place,state). 
   So all I had to do is skip the scrape if date+place+state already existed in the CSV. 
   Initially, each time an error occured it took only several minutes for the script to skip all the data collected so far. 
   I am running on a 2009 MacMini (8GB RAM, 2GHz Core2Duo). 
   This is where performance of the machine matters as I am looking through the dataframe for a match.
   But soon it slowed down to 1-match-per-second as the number of rows grew. This became untenable.. 
1. My workaround is to not start with the same set of state-place each time. 
   I changed the dictionary lookup into a random.sample(list(dict),1) and did not use a specific seed. 
   So far each time it started off with a different state and different city. 
   As of the writing of this page I am at around 885,000 lines in the CSV file (Huntington, West Virginia, 2010)
1. WIP: The next plan is to break down the CSV by state so that scanning for existing records does not take a long time in future restarts.

