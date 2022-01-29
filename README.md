# Citi-Bike Phenomenon


## Purpose

The purpose of this repository is to visualize Citi-Bike data in calendar year 2021 for the New York City area.  

## The Data

[New York Citi Bike](https://en.wikipedia.org/wiki/Citi_Bike) provides historical data on utilization from 2013 - 2021.  This data includes information on gender, station IDs, station locations, station names, start coordinates, end coordinates, and whether users are casual users or members.  The data is stored as single csv files in a zip file reflective of that month's data that can be downloaded on the [Citi Bike Data](https://www.citibikenyc.com/system-data) website.

### Data Scraping, Cleansing, and Merging

To get the data, code was developed to automate the process of downloading the data and generating a "Master" DataFrame. 

#### Data Scraping

The code uses the dependency [zipfiles] for unzipping each csv and populating into a DataFrame, and adding to a "master" DataFrame, and looping through the process until the number of selected csvs are fully loaded into a master DataFrame.  The first attempt was to download all of the available data on the website into one csv (3 years of rentals), but this was not possible due to storage and memory constraints.  Therefore, the data of focus was monthly data in calendar 2021.

Dependencies used for scraping included Splinter, webdriver_manager.chrome, BeautifulSoup, and Requests to open a browser, parse the HTML, and collect the source URLs needed for the data download.  IO, zipfile, and urllib.request were used to download the zipfile, unzip the zipfile, pull in the individual csv into a DataFrame.  The "df" DataFrame would be concatenated with a "master" DataFrame to continue to load the csv data into one DataFrame [master_df].  Once this was complete for one csv, the "df" DataFrame was deleted and the process would continue to loop until all ZipFile URLs were downloaded, unzipped, and the csv data was pulled into a single DataFrame.

#### Data Cleansing

As mentioned, as the csv was unzipped and loaded into a DataFrame, it was concatenated with another DataFrame.  Because the data column names in each month were not consistent such as spaces vs. "_", the DataFrames were populated correctly but left empty columns.  Those columns were dropped.  Key attributes that were needed included starting location and ending location for coordinates, member user vs. casual user, as well as date/time stamps for the start of the rental and return.  The null values were dropped from these columns.  Because other columns had numerous null values such as Gender, usertype, and Birth Year, those columns were dropped as it would be difficult to come to any substantial and/or valid conclusion. Once this was done, the datatype for the start and end date of the rental were changed from an "object" to a "date/time" using the datetime dependency.  

## Tableau Visuals

Tableau was used to visualize the data and identify phenomenon.

### Calculated Fields

Because the data was clean, there were only a few instances where data had to be manipulated in a way that removed erroneous values.  However, a few calculated fields were needed to maximize the use of the data.  For example, there were separate objects for starting longitude vs. starting latitude.  The same was true with ending longitude vs. ending latitude.  This would not be recognized by Tableau as coordinates.  A calculated field was developed to consolidate starting latitude and starting longitude into an "Origin" category using the formula `MAKEPOINT([End Lat], [End Lng])`.  The same step was performed for ending latitude and ending longitude into a "Destination" category.  This was a key first step in identifying one of the phenomenon described below.  

The final calculated field that was developed was `Number of Rentals`.  A "Ride ID" column was included in the data.  Using this column, a calculated field was developed to identify the total count of rentals by using the formula `COUNT([Ride Id])`.  This was also a key part of identifying a first phenomonen described below.

Another calculated field that was developed was `Rental Time`.  Timestamps were included in the data which reflected the time the reservation started and ended ("started_at" and "ended_at", respectively).  A calculated field was developed to identify the total time of each rental (in minutes) by using the formula `DATEDIFF('minute', [Started At], [Ended At])`.  This was a key part of identifying a second phenomenon described below.

### Sets

A few sets were generated with stations that had stations with the highest average rental time and another for the lowest average rental time.  However, this was not used in the dashboard as there were no phenomenon identified in this data.  Further, there was an unexplained error that potentially involved in an erroneous cell.  

## Conclusion (i.e., Phenomenon)

### Origin and Destination Mystery

Once the calculated field for origin and destination was developed, the visuals could now display the travel lines between the different locations.  As mentioned, this provided an idea of the route boundaries for these bikes using a city map.  The locations were plotted on two separate city maps; one for origins and the other for destinations.  The coloring was based on average rental time for these locations.  

Comparing this to the boundaries was very interesting.  All but one origin location was inside New York.  Although most of the destination locations were also in New York, there were several in New Jersey (~75).  Thinking of scenarios, this would explain where individuals were renting their bikes from within the City and going to New Jersey and turning in their bike.  One dashboard was created to contrast the two maps along with the boundary map previously described.  A secondary phenomenon in this dashboard is why the end locations had far higher averages in rental times than the starting locations accounted for. 

These visuals are reflected in its own dashboard and the first page in the Story.

### February Mystery

Visuals were put together to show the number of rentals by month and the average rental time by month.  This was further split to compare member riding habits vs. casual member habits.  Perhaps not surprisingly, the  number of rentals for each month dip in the coldest months but peak in the warmer to hotter months. 

The lowest number of rentals, including rentals by members and casual users, occurred in February.  However, February has a substantially higher average rental time by casual users (47.04 mins).  Other visuals were put together to try to explain this phenomenon, but the source could not be identified.

These visuals are reflected in its own dashboard and the second page in the Story.

## Conclusion

The only way to explain these phenomenon is with data that is outside of the data reported by Citi-Bike.  For the first phenomenon, it could be attributed to hours of operation. For the second phenomenon, it could be pricing incentives for casual users in lower usage months.  Because this data is not available, I can only identify this as phenomenons that can be explained by the data that is provided.  

# Code and Visual Repositories

#### [GitHub](https://github.com/nladkins/tableau-challenge)

#### [Tableau](https://prod-useast-b.online.tableau.com/#/site/normanadkins/workbooks/379153)