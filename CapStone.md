# The Battle of Neighborhoods

## IBM Capstone Project 

## By: Benyam Tadesse

## Table of Contents

<font size = 4>

1. <a href="#item1">Introduction:- discussion on the business problem and who would be interested in this project</a>

2. <a href="#item2">Data Description:- describe the data that will be used to solve the problem and the source of the data.</a>

3. <a href="#item3">Methodology Section:- main component of the report, we will discuss & describe any exploratory data analysis that is used, any inferential statistical testing that is performed, if any, and what machine learnings were used and why.</a>

4. <a href="#item4">ResultS Section:- discuss the results</a>

5. <a href="#item5">Discussion Section:- discuss any observations noted and any recommendations based on the results.</a>
    
6. <a href="#item5">Conclusion, & Disclaimer</a>    
</font>

## 1. Introduction

An investor wants to expand his  Bakery-Coffee shop in New York City but the investor isn't from New York City and doesn't know much about the different boroughs and/or neighborhoods in the city. In knowing this we will provide the relevant data for the investor, which will mostly be neighborhood venue data that lists the least amount of coffee shops and/or bakeries. We will also want to figure out which borough has the least amount of crime data reported in that specific borough which will likely have an affect on the demographics, this information will also likely help with real estate pricing and also give potential customers a better vibe to make them feel safer but also keep potential competitors at bay.


in this report we're trying to give a simplistic understanding of the different boroughs crime rates in New York City, so that the investor can get a better understanding of the structures (demographics) of the boroughs. We will also explore certain neighborhoods from that preferred borough, we will leverage the Foursquare API for this part of the task. In layman terms we will answer which borough is better or worse in terms of crime data, after which figure out which neighborhoods are preferred in that specific borough and a list of most common venues in these neighborhoods using the Foursquare API.

## 2. Data Description 

#### We will be using three different datasets & the Foursquare API in this report.
The first dataset we will be analyzing is the New York City crime data. This data is comprised of crime reports from all 5 boroughs in fiscal year ending in 2018. The dataset contains longitude and latitude of where the crime occurred, it also includes various other information but we will only be using the location data. This data was accessible from the City of New York public API found here: 
- https://data.cityofnewyork.us/resource/qgea-i56i.json.
  - Borough        -- Count
  - Brooklyn       -- 293
  - Manhattan      -- 251
  - Bronx          -- 224
  - Queens         -- 191
  - Staten Island  -- 39

The second dataset we will be looking at is New York City borough border map, this map will be used to create a Choropleth map. A Choropleth map is a thematic map in which areas are shaded in proportion to the measurement of the statistical variable being displayed on the map, the variable data will be the crime data collected. This dataset can be downloaded from the City of New York site found here:
- https://data.cityofnewyork.us/widgets/tqmj-j8zm

The third Dataset we will be using is the New York City neighborhoods geographical coordinates, we will learn that New York City has 5 boroughs and 306 neighborhoods. Thus the reason for us to figure out which borough we want to focus our analyses on. The New York City neighborhoods geographical coordinates data will be utilized using Foursquare API. this Dataset was provided by NYU and can be downloaded from this site:
- https://geo.nyu.edu/catalog/nyu_2451_34572.

Finally we will be accessing the Foursquare call API to to get venue location data.

## 3. Methodology Section


```python

```




```python

```

## 4. Results


```python

```


```python

```


```python

```


```python

```

## 5. Discussion


```python

```


```python

```

## 6. Conclusion, &  Disclaimer


```python

```
