# Data Package Creator Writeup Notes

![](Data%20Package%20Creator%20Writeup%20Notes/Screenshot%202022-01-26%20at%2009.45.34.png)

Constantly under the impression that I’m six months behind on lab work, I am capital Q - **Queen** - of bad data practices. My computer is a graveyard of poorly labeled .csv files, featuring illustrative headers such as “redo,” “negative pressure why?” and “weird - see notes.”  I was vaguely aware of the existence of data packages, but like learning Italian or traveling more, implementing them in my workflow got slotted in the category of “would be nice if I had the time.”  That clemency, however, was not extended to my research lifeblood - molecular spectroscopy databases, you disorganised beauties you - nor to collaborators who often invoked the following feeling:  

![norm_normal_file_format_2x](https://user-images.githubusercontent.com/4370045/151969473-28d367b8-29ba-47fb-b58a-e3fa3ea502c9.png)


Particularly in fields where measurables aren’t tangible macro concepts (see: population) but abstract and insular conventions with many varied representations, clear descriptors of multivariate data are a _must_ in order for that data to be easily used and reproduced.  This is where data packages come in; they bundle up your data with a human and machine readable file containing, at minimum, standardised information regarding structure and contents. In this lil’ post here, we’re going to walk through this process together by packaging data together with its metadata, and then validating the data using Frictionless tools.

## Get that data! 
I’ve recently gotten involved with a project monitoring agricultural regeneration through remote sensing.  One important soil health parameter is soil moisture, relevant on its own, but additionally in how it ties into drought prediction and food security monitoring. The data is measured by [SMAP](https://smap.jpl.nasa.gov/), a passive microwave orbiting observatory from the acronym loving institute, NASA. As a government agency, NASA doesn’t license the use of NASA materials or sign licensing agreements, but instead asks kindly not to use their data for weird merchandising stuff; the SMAP data products are therefore [openly available ](https://earthdata.nasa.gov/earth-observation-data/data-use-policy) from the [National Snow and Ice Data Center](https://nsidc.org/data/smap).  You can select a relevant region and time series and  bulk download the data via HTTPS.  For our team, the data then undergoes external preprocessing and a downconversion process, via external partners, before coming to us, looking like:

!![9272701B-1D90-49AA-95FF-AC0EBF185B70](https://user-images.githubusercontent.com/4370045/151969571-5d5e1b13-0eac-45e5-a51a-31b8a1afc354.png)

Not bad so far! At an initial glance, we can see that the date column is in a reasonable format, there is a header containing units, and it doesn’t seem like there are missing values. Let’s put it through the data packaging process and see what happens. 


## Pack it up!
Frictionless has a browser tool for creating data packages  but for a slightly closer look under the hood we’re going to play with the Frictionless Tools a bit. The function _describe_, for example, can be used to describe data and generate metadata, which contains information such as type (integer, string, etc), constrains (min, max etc.) relations, and so forth.  Yes - much of this can be inferred by investigating the tabular data itself, or is included in the csv header, but having properly described data saves a lot of upfront workflow effort, and potentially many grim hours of starring at unlabelled gridded numbers. In the command line  _describe_, spits out a YAML file where it infers several data parameters.

<img width="645" alt="Screenshot 2022-01-24 at 18 46 05" src="https://user-images.githubusercontent.com/4370045/151969603-3cf6fa77-cc52-4c6d-9ecc-ad57c9aace0d.png">

It is, in this case, admittedly a little barebones. So I opened the file and tried and flush things out, going off suggestions given via the Frictionless Standards ([Data Package | Frictionless Standards](https://specs.frictionlessdata.io//data-package/) ) for data packages. It didn’t take me long to figure out that I had no idea what the column names meant. Some measures of volumetric soil moisture, given in m^3/m^3, for sure. But the full naming convention eluded me so I went on the hunt. NASA has loose bits of documentation all over like such as: 

!<img width="710" alt="Screenshot 2022-01-17 at 09 25 10" src="https://user-images.githubusercontent.com/4370045/151969660-3247931e-8f70-4126-8277-c5f59ab93454.png">

SM was likely soil moisture and SMAP - the name of the satellite.  One hour into the worlds driest scavenger hunt I determined L to indicate the satellite band and DESC to mean it was measured during a descending orbit. At this point I sucked up my pride and emailed the data provider who cleared up what turned out to be internal naming conventions. AVG_b20d, for example, is the soil moisture average over a window of 20 days. This is done due to the volatility of daily observation.  A meta rallying cry for metadata! I added the information into the YAML file, and made some other changes. 

<img width="762" alt="Screenshot 2022-01-24 at 19 31 00" src="https://user-images.githubusercontent.com/4370045/151969688-5e9bf5ec-e3ed-43b8-b221-e12c06e2f121.png">

Cool. I decided it was time to try and validate the data, to see if there were any apparent problems I wasn’t picking up visually. 

<img width="908" alt="Screenshot 2022-01-24 at 19 33 13" src="https://user-images.githubusercontent.com/4370045/151969717-29720d14-7348-4161-930b-bf83ea85a42b.png">

Oops! In my metadata zeal, I tried to manually change change the dialect delimiter specification. Fixed it and everything looked golden.

<img width="537" alt="Screenshot 2022-01-24 at 19 33 44" src="https://user-images.githubusercontent.com/4370045/151969742-d7dffcac-b0a4-4802-9f21-c3e194ad3bc6.png">

This exercise, however got me thinking. Lots of information is still missing to properly make conclusions about soil moisture, such a shape file or some other geographic specifier, an indicator giving the soil type, clarity on if this is a measurement of the top soil or an estimation of the root zone layer, etc.  The data in better shape now than when we started, but could still use additional specifiers to facilitate clear and reproducible analysis. At minimum, a data package contains a metadata file.  Even better, metadata + the data itself. Better yet, you add a README.md file! Learn more here: https://specs.frictionlessdata.io/. 



