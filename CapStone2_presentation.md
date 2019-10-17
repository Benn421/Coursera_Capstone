# Picking the right Neighborhood for opening a Bakery-Coffee shop
## IBM Capstone Project
### By: Benyam Tadesse

### 1. Introduction

An investor wants to expand his  Bakery-Coffee shop in New York City but the investor isn't from New York City and doesn't know much about the different boroughs and/or neighborhoods in the city. In knowing this we will provide the relevant data for the investor, which will mostly be neighborhood venue data that lists the least amount of coffee shops and/or bakeries. We will also want to figure out which borough has the least amount of crime data reported in that specific borough which will likely have an affect on the demographics, this information will also likely help with real estate pricing and also give potential customers a better vibe to make them feel safer but also keep potential competitors at bay.


in this report we're trying to give a simplistic understanding of the different boroughs crime rates in New York City, so that the investor can get a better understanding of the structures (demographics) of the boroughs. We will also explore certain neighborhoods from that preferred borough, we will leverage the Foursquare API for this part of the task. In layman terms we will answer which borough is better or worse in terms of crime data, after which figure out which neighborhoods are preferred in that specific borough and a list of most common venues in these neighborhoods using the Foursquare API.

### 2. Data Description

#### We will be using three different datasets & the Foursquare API in this report.
The first dataset we will be analyzing is the New York City crime data. This data is comprised of crime reports from all 5 boroughs in fiscal year ending in 2018. The dataset contains longitude and latitude of where the crime occurred, it also includes various other information but we will only be using the location data. This data was accessible from the City of New York public API found here: 
- https://data.cityofnewyork.us/resource/qgea-i56i.json.
```python
df1 =  df['Borough'].value_counts()
df1
```
  - Borough        -- Count
  - Brooklyn       -- 293
  - Manhattan      -- 251
  - Bronx          -- 224
  - Queens         -- 191
  - Staten Island  -- 39
<br>Name: Borough, dtype: int64 <br>

The second dataset we will be looking at is New York City borough border map Geojson dataset, this map will be used to create a Choropleth map. A Choropleth map is a thematic map in which areas are shaded in proportion to the measurement of the statistical variable being displayed on the map, the variable data will be the crime data collected. This dataset can be downloaded from the City of New York site found here:
- https://data.cityofnewyork.us/widgets/tqmj-j8zm

The third Dataset we will be using is the New York City neighborhoods geographical coordinates, we will learn that New York City has 5 boroughs and 306 neighborhoods. Thus the reason for us to figure out which borough we want to focus our analyses on. The New York City neighborhoods geographical coordinates data will be utilized using Foursquare API. this Dataset was provided by NYU and can be downloaded from this site:
- https://geo.nyu.edu/catalog/nyu_2451_34572.

Finally we will be accessing the Foursquare call API to to get venue location data.

### 3. Methodology Section

The main component of this report will consist of performing a exploratory data analysis (EDA) on the New York City crime data while including the Geojson data of the 5 boroughs to superimpose the combined data into a Choropleth Folium map. After which we will utilize the foursquare API to get venue data on the borough selected after performing EDA, this will help us figure out which neighborhood has what type of venues in the area for potential investors.

We will begin with the New York City crime data by finding unique values of the number of times a crime is committed in each borough, by using the **.value_count()** pandas function. Doing this will give use a clear picture of the 5 boroughs, after which we will utilize this informations in our *Choropleth* map. A *Choropleth* map is a thematic map in which areas are shaded in proportion to the measurement of the statistical variable being displayed on the map, such as the **.value_count()** data we gathered. The *Choropleth* map will provide an essential way to visualize how the measurement varies across the 5 boroughs.

after accomplishing the *Choropleth* map wee will begin to segment & cluster the neighborhoods in Queens, we will also get the location data by using *Geopy* library. after gathering the location data we utilization the Foursquare API to explore and segment the neighborhoods in Queens. segmenting is division into separate parts or sections there are 4 main types of segmentation Geographic, Demographic, Psychographic, & Behavioral we will be using geographic segmentation in this report. Extracting venues category data from all the neighborhoods in Queens is the next method we will execute, also we will print out the top 5 most common venues in that neighborhood. Finally we reach the *k*-means algorithm part of the process, *k*-means algorithm is an unsupervised/ partitioning clustering algorithm that:
- 1. Cluster the data into *k* groups where *k* is predefined
- 2. Select *k* points at random as cluster centers.
- 3. Assign objects to their closed cluster center according to the *Euclidean distance* function.
- 4. Calculate the centroid or mean of all objects in each cluster.
- 5. Final step is to repeat the steps until the same points are assigned  to each cluster in consecutive rounds.

*k*-means divides the data into non-overlapping subsets (clusters) without any cluster-internal structure, the objective of *k*-means is to form clusters in such a way that similar samples go into a cluster, in our case common venue categories are put in the same cluster. We can reach this by following these steps:
- Cluster Neighborhoods
- We run *k*-means to cluster the neighborhood into 5 clusters.
- create a new dataframe that includes the cluster as well as the top 10 venues for each neighborhood.
- visualize the resulting clusters
- Examine Clusters
- Now we examine each cluster and determine the discriminating venue categories that distinguish each cluster.

### Examine Clusters
Now we examine each cluster and determine the discriminating venue categories that distinguish each cluster.

##### Cluster 1


```python
Queens_merged.loc[Queens_merged['Cluster Labels'] == 0, Queens_merged.columns[[0] + list(range(1, Queens_merged.shape[1]))]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Boro</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>43</td>
      <td>Breezy Point</td>
      <td>40.557401</td>
      <td>-73.925512</td>
      <td>Queens</td>
      <td>0</td>
      <td>Beach</td>
      <td>Board Shop</td>
      <td>Monument / Landmark</td>
      <td>Trail</td>
      <td>Women's Store</td>
      <td>Event Space</td>
      <td>Eastern European Restaurant</td>
      <td>Egyptian Restaurant</td>
      <td>Electronics Store</td>
      <td>Empanada Restaurant</td>
    </tr>
    <tr>
      <td>49</td>
      <td>Rockaway Beach</td>
      <td>40.582802</td>
      <td>-73.822361</td>
      <td>Queens</td>
      <td>0</td>
      <td>Beach</td>
      <td>Ice Cream Shop</td>
      <td>Latin American Restaurant</td>
      <td>Deli / Bodega</td>
      <td>BBQ Joint</td>
      <td>Seafood Restaurant</td>
      <td>Food Truck</td>
      <td>Arepa Restaurant</td>
      <td>Pizza Place</td>
      <td>Moving Target</td>
    </tr>
    <tr>
      <td>61</td>
      <td>Belle Harbor</td>
      <td>40.576156</td>
      <td>-73.854018</td>
      <td>Queens</td>
      <td>0</td>
      <td>Beach</td>
      <td>Spa</td>
      <td>Deli / Bodega</td>
      <td>Mexican Restaurant</td>
      <td>Chinese Restaurant</td>
      <td>Donut Shop</td>
      <td>Bakery</td>
      <td>Bagel Shop</td>
      <td>Italian Restaurant</td>
      <td>Boutique</td>
    </tr>
    <tr>
      <td>62</td>
      <td>Rockaway Park</td>
      <td>40.580343</td>
      <td>-73.841534</td>
      <td>Queens</td>
      <td>0</td>
      <td>Beach</td>
      <td>Donut Shop</td>
      <td>Pizza Place</td>
      <td>Bank</td>
      <td>Bagel Shop</td>
      <td>Smoke Shop</td>
      <td>Seafood Restaurant</td>
      <td>Bus Stop</td>
      <td>Board Shop</td>
      <td>French Restaurant</td>
    </tr>
    <tr>
      <td>75</td>
      <td>Roxbury</td>
      <td>40.567376</td>
      <td>-73.892138</td>
      <td>Queens</td>
      <td>0</td>
      <td>Beach</td>
      <td>Bar</td>
      <td>Fast Food Restaurant</td>
      <td>Pub</td>
      <td>Deli / Bodega</td>
      <td>Baseball Field</td>
      <td>Irish Pub</td>
      <td>Trail</td>
      <td>Electronics Store</td>
      <td>Dry Cleaner</td>
    </tr>
    <tr>
      <td>78</td>
      <td>Hammels</td>
      <td>40.587338</td>
      <td>-73.805530</td>
      <td>Queens</td>
      <td>0</td>
      <td>Beach</td>
      <td>Southern / Soul Food Restaurant</td>
      <td>Diner</td>
      <td>Bus Stop</td>
      <td>Bus Station</td>
      <td>Dog Run</td>
      <td>Shoe Store</td>
      <td>Gym / Fitness Center</td>
      <td>Café</td>
      <td>Food Truck</td>
    </tr>
  </tbody>
</table>
</div>



##### Cluster 2


```python
Queens_merged.loc[Queens_merged['Cluster Labels'] == 1, Queens_merged.columns[[0] + list(range(1, Queens_merged.shape[1]))]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Boro</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>63</td>
      <td>Somerville</td>
      <td>40.597711</td>
      <td>-73.796648</td>
      <td>Queens</td>
      <td>1</td>
      <td>Park</td>
      <td>Women's Store</td>
      <td>Empanada Restaurant</td>
      <td>Dry Cleaner</td>
      <td>Dumpling Restaurant</td>
      <td>Eastern European Restaurant</td>
      <td>Egyptian Restaurant</td>
      <td>Electronics Store</td>
      <td>Event Space</td>
      <td>Donut Shop</td>
    </tr>
    <tr>
      <td>79</td>
      <td>Bayswater</td>
      <td>40.611322</td>
      <td>-73.765968</td>
      <td>Queens</td>
      <td>1</td>
      <td>Playground</td>
      <td>Park</td>
      <td>Women's Store</td>
      <td>Empanada Restaurant</td>
      <td>Dry Cleaner</td>
      <td>Dumpling Restaurant</td>
      <td>Eastern European Restaurant</td>
      <td>Egyptian Restaurant</td>
      <td>Electronics Store</td>
      <td>Event Space</td>
    </tr>
  </tbody>
</table>
</div>



##### Cluster 3


```python
Queens_merged.loc[Queens_merged['Cluster Labels'] == 2, Queens_merged.columns[[0] + list(range(1, Queens_merged.shape[1]))]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Boro</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>50</td>
      <td>Neponsit</td>
      <td>40.572037</td>
      <td>-73.857547</td>
      <td>Queens</td>
      <td>2</td>
      <td>Beach</td>
      <td>Women's Store</td>
      <td>Event Space</td>
      <td>Dumpling Restaurant</td>
      <td>Eastern European Restaurant</td>
      <td>Egyptian Restaurant</td>
      <td>Electronics Store</td>
      <td>Empanada Restaurant</td>
      <td>Falafel Restaurant</td>
      <td>Flower Shop</td>
    </tr>
  </tbody>
</table>
</div>




```python
Queens_merged.loc[Queens_merged['Cluster Labels'] == 3, Queens_merged.columns[[0] + list(range(1, Queens_merged.shape[1]))]]
```

##### Cluster 5


```python
Queens_merged.loc[Queens_merged['Cluster Labels'] == 4, Queens_merged.columns[[0] + list(range(1, Queens_merged.shape[1]))]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Boro</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>64</td>
      <td>Brookville</td>
      <td>40.660003</td>
      <td>-73.751753</td>
      <td>Queens</td>
      <td>4</td>
      <td>Deli / Bodega</td>
      <td>Women's Store</td>
      <td>Falafel Restaurant</td>
      <td>Dumpling Restaurant</td>
      <td>Eastern European Restaurant</td>
      <td>Egyptian Restaurant</td>
      <td>Electronics Store</td>
      <td>Empanada Restaurant</td>
      <td>Event Space</td>
      <td>Farm</td>
    </tr>
  </tbody>
</table>
</div>



### 5. Discussion, Conclusion

This report we observed the crime data of the 5 boroughs, we saw that Brooklyn, Manhattan, & The Bronx had high crime rates with Brooklyn as the highest. This information made it easier to choose which borough we'd put our focus into which will be Queens, Queens is an optimal choose mainly due to the fact that Staten Island had such low crime rate that real estate prices would be astronomical and/or rent prices so Queens was the best choice for financial reasons.

After segmenting & clustering Queens neighborhood venue data from the FourSquare API, we can see that the neighborhoods venues give use an understanding of the demographics of the neighborhoods or at the least what the people in that neighborhoods prefer. By looking at the top 10 most common venues we can get a sense of the neighborhood, by doing this we can pick a preferred neighborhood and/or neighborhoods for the potential investor. Prime example of this is Jackson Heights, Jackson Heights has 5 of its top 10 most common venues as spanish restaurants, this tells use that its mostly a spanish community. Another thing we will notice is that Queens has a lot of Dei/Bodega it's the number 1 most common venue in a number of neighborhoods. Now for us to pick which neighborhoods is adequate for our investor we will look for neighborhoods with venues that require high foot traffic such as metro stations, gyms, parks, & malls. One neighborhood that meets these criteria is 
Beechhurst, Beechhurst top 3 most common venues are Chinese Restaurant,	Yoga Studio, Shopping Mall which are not considered competitor to the market the potential investor wants to get into, not to mention 2 of its top 10 most common venues are Gym, & Gym/Fitness Center. With all these high foot traffic venues opening a Bakery-Coffee shop would be optimal for the potential investor.

In conclusion, we see that that there are 3 potential neighborhoods that we can open a Bakery-Coffee shop in, these 3 neighborhoods with high foot traffic are:
- Beechhurst: Chinese Restaurant, Yoga Studio, Shopping Mall, Donut Shop, Supermarket, Gym, Gym/Fitness Center, Dessert Shop, Italian Restaurant, Deli/Bodega
- Forest Hills: Gym/Fitness Center, Gym, Yoga Studio, Pharmacy, Pizza Place, Park, Thai Restaurant, Convenience Store, Farmers Market, Food Truck
- Hollis: Park, Shopping Mall, Sandwich Place, Fried Chicken Joint, Baseball Field, Discount Store, Asian Restaurant, Bakery, Electronics Store, Grocery

from what wee can see these 3 neighborhoods have one thing in common and that is the communities are highly active whether it's gym's or baseball fields to shopping malls, this indicates that these neighborhoods are high foot traffic areas. High foot traffic helps our potential investors investment with exposure to the market without having to put out adds or chase potential customers. 

In comparison here are a couple of neighborhoods with coffee shops or bakeries as one of their top 5 most common venues:
- Arverne: Surf Spot, Metro Station, Sandwich Place, Coffee Shop, Pizza Place, Board Shop, Bus Stop, Bed & Breakfast, Beach, Donut Shop
- Lefrak City: Cosmetics Shop, Department Store, Bakery, Pharmacy, Supplement Shop, Restaurant, Mexican Restaurant, Dry Cleaner, Furniture/Home Store, Fruit & Vegetable Store

We see that Arverne second most common venue is a Metro Stations, this will cause that neighborhood to have high foot traffic due to commuters so its optimal location for coffee shops. Lefrak City shopping venues such as cosmetics and department stores, this will also have modest foot traffic so having a bakery is understandable (Note this will depend on their menus options). 

From what we've gather Beechhurst is the optimal neighborhood, mainly due to the fact that Forest Hills tenth most common venue are Food Truck and that might take potential customers away. Hollis has Bakeries as the eighth most common venue this is in direct competition with the investor business.
