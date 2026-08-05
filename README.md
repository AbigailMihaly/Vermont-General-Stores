# Lost Gathering Places: How Vermont's General Store Landscape Has Changed
## Exploring store closures and other patterns in recent decades. ** A note for any public viewer that this project is incomplete and not for reference/publication due to data constraints **

This project aims to answer the question" Is Vermont losing general stores?" and to dig into those trends.

### Data

I analyzed bulk company data from Vermont’s Business Services Division. Bizfilings.govermont.gov/bulk-download provides signed in users with the ability to download xls files showing information about businesses, including their name, when they first filed with VT’s Business Services Division, current status with the state, and more.

I also used [data](https://geodata.vermont.gov/datasets/VCGI::vt-data-town-boundaries-1/about) from from Vermont's Center for Geographic Information to create a list of Vermont towns to use in my sorting of General Stores, detailed below.

Summary:Depict Vermont's village, town, county, RPC, administrative, and state boundary lines.

I analyzed three aspects of this data: how many stores were active in the state in each year, how many stores closed over time, and how many stores opened over time. I encountered significant challenges with this data, detailed below.

## Jupyter Notebooks

The "VT_stores_sorting_excel_data.ipynb" sorts the data for general stores.

The "VT-stores_analyzing_excel_data.ipynb" analyzes the data and produces the three .csv files for the charts in the story.

This repo also includes a notebook titled "VT_stores_scraping.ipynb". I first gathered the stores data via browser automation with Playwright, before I found the xls files that are downloadable once a user creates an account. That process is detailed in this repo. The biggest challenge was circumventing the recaptcha, which I did via the "nopecha" browser extension/API. Installation information for nopecha is included in the notebook, and can also be found [here](https://jonathansoma.com/everything/scraping/solving-captchas-in-playwright-with-nopecha/) and [here](https://developers.nopecha.com/guides/extension/).

## Analysis

### Choosing “General Stores”

Before I could do any specific analysis, I needed to isolate so-called “General Stores.”
—> The xls files are organized by datatype. I downloaded and then imported into python data across all types of domestic companies in the state, as I am interested mom-and-pop, locally owned establishments.
—> I then filtered the data for just general stores. There is no universal definition for what makes a “general store”, but many have similar names. I chose to define them as any store with a name including “General Store” or “Country Store,” like the Country Store of Weston in Weston, Vermont, or the term “store” + a Vermont city name, such as the Grafton Village Store, or the Village Store of Monkton.
—> Lastly, I read through the list and made a list of a few specific non-general stores to exclude, such as Dollar General stores. I also sorted out any stores with terms like “Book Store” and “Hardware Store” that may have made the list because of the “store” + Vermont city name qualification. 
I could then analyze the filtered data.

### Data Limitations

I quickly ran into some major limitations: David Hall, head of the Vermont Business Services Division, warned me that many companies’ filings are not up to date, and others may never have filed with the BSD at all. That means that, for example, a store listed as “inactive - expired” may still be active but not be up-to-date with BSD paperwork. On the other hand, a store listed as inactive may no longer be in service, but may never have filed formal paperwork to change their status to “terminated” or “dissolved.” The majority of the stores fall into one of these inactive categories. Of the 1239 general stores in my data, 824 were listed as “inactive - expired,” and 105 “inactive - administratively terminated.” 82 were “active - registered” and 70 “active - in good standing”; while 59 were “dissolved” and 26 “terminated.” Other statuses include “Inactive - Cessated” (47s stores), “Active - NOT In Good Standing” (23), “Inactive - Dissolved” (1) and “Inactive - Administratively Revoked” (1) and “Active registered - Reregistration Pending” (1).

I still wanted to move forward with this project for Lede — while recognizing I’d need to find additional data, possibly including driving to/calling stores to crosscheck. In order to use the data I have within its bounds, I first chose to analyze those stores that are listed as terminated/dissolved; as well as incorporation dates. That meant that I could set aside those with inactive statuses for now.

### Calculating Active Stores Over Time 

I also did an analysis of how many different stores were active over time, with the understanding that this is extremely imperfect data, and were I to publish, I would want to crosscheck any that appear inactive.

To do this analysis, I reshaped the data to create one row per store. That involved normalizing the business and town names to account for differences such as “North” in one filing and “N.” in another. I then used the pandas .groupby function to group together any rows that had both the same business and town name (to ensure this didn’t group together any stores that may have the same store name but be in different towns). This could have introduced some errors, and if I choose to dedicate more time to this project, I’d like to revisit this. If a store closed and then reopened under the same or a similar name, should it be counted as the same store? What about if a store changes its name but keeps running?

I then created a new data frame with the business’ name, and other information. Columns included "DateOfIncorporation” that pulled whatever the earliest date of incorporation among the rows with the business and town names; as well as a “last_date” column. That enabled me to create a new column with the dates that store were active — the dates between the date of incorporation and the last date. The last_date column included whatever the most relevant date provided. If the data included a termination or dissolution date, then last_date is that date. However if that information is missing, I used the last annual report date as a proxy. Only if that, too, was missing, did I use the expiration date, which is the date that their latest registration expired, as the value to fill in the last_date column.
Importantly, the majority of the rows — 449 of the 802 stores — fall into that last category. As discussed earlier, David Hall warned that many stores may simply be out of date on their filings; this analysis therefore serves only as a preliminary analysis so that I might better understand potential trends for the purpose of future reporting. Another note: I had to filter out five rows with no dates to serve as a last_date at all.

Now that I had a start and end date for each store (if the store is still active, I put the date as August. 3, 2026 — the date I last updated this analysis), I could create a new column with the timeframe during which that store was active, and graph that information.

I am also choosing to trim this data beginning in 1980 or 85 depending on the visualization, and only graph the section after that. There may be lower levels of reporting or a different reporting laws decades ago, and since I am interested in the effect that retail changes like chain stores had on the state, beginning in the early 2000s, its an easy caveat to exclude the 1960s-80s.

## Visualizations

.html page constructed using [J Soma's stack template](https://github.com/jsoma/page-templates/tree/main).
D3 visualizations constructed from a base of charts found at [d3-graph-gallery](https://d3-graph-gallery.com/graph/barplot_ordered.html).

To create the map, I geocoded each town name and mapped with datawrapper. I then created a gif with canva.
