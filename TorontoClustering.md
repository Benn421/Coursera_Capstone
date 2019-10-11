## Toronto Clustering

we begin by installing the different libraries we will use


```python
#!conda install -c anaconda beautifulsoup4
import requests
from urllib.request import urlopen
import pandas as pd
import numpy as np
import time
from bs4 import BeautifulSoup
import seaborn as sns
import matplotlib.pyplot as  plt
```


```python
# Here we define the website we will be scraping 
url = "https://en.wikipedia.org/wiki/list_of_postal_codes_of_Canada:_M"
html = urlopen(url)
```

we will be using the Beautiful Soup library for our scarping needs


```python
soup = BeautifulSoup(html, 'html.parser')
type(soup)
```




    bs4.BeautifulSoup




```python
title = soup.title
print(title)
```

    <title>List of postal codes of Canada: M - Wikipedia</title>



```python
text = soup.get_text()
```


```python
all_links = soup.find_all("a")
for link in all_links:
    link.get("href")
```


```python
rows = soup.find_all('tr')
```


```python
for row in rows:
    row_td = row.find_all('td')
type(row_td)
```




    bs4.element.ResultSet




```python
str_cells = str(row_td)
cleantext = BeautifulSoup(str_cells, 'html.parser').get_text()
```


```python
import re
list_rows = []
for row in rows:
    cells =row.find_all('td')
    str_cells = str(cells)
    clean = re.compile('<.*?>')
    clean2 = (re.sub(clean, '',str_cells))
    list_rows.append(clean2)
type(clean2)
```




    str



#### Finally we turn the data into a DataFrame.


```python
df = pd.DataFrame(list_rows)
df.head(5)
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
      <th>0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>[]</td>
    </tr>
    <tr>
      <td>1</td>
      <td>[M1A, Not assigned, Not assigned\n]</td>
    </tr>
    <tr>
      <td>2</td>
      <td>[M2A, Not assigned, Not assigned\n]</td>
    </tr>
    <tr>
      <td>3</td>
      <td>[M3A, North York, Parkwoods\n]</td>
    </tr>
    <tr>
      <td>4</td>
      <td>[M4A, North York, Victoria Village\n]</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Here we remove. the , from the dataframe
df1=df[0].str.split(',', expand=True)
```


```python
#Here we remove. the [ from the first column of the dataframe
df1[0] = df1[0].str.strip('[')
```


```python
#Here we remove. the ] from the first column of the dataframe
df1[2] = df1[2].str.strip(']')
```


```python
#No we drop the first row
df1.drop([0], axis=0, inplace=True)
```


```python
# we also dont need anything past the third column
df1.drop([4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30], axis=1, inplace=True)
df1.head()
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned\n</td>
      <td>None</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned\n</td>
      <td>None</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods\n</td>
      <td>None</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village\n</td>
      <td>None</td>
    </tr>
    <tr>
      <td>5</td>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront\n</td>
      <td>None</td>
    </tr>
  </tbody>
</table>
</div>



#### in this short section we will extract the headers for the columns


```python
col_labels = soup.find_all('th')
```


```python
all_header = []
col_str = str(col_labels)
cleantext2 = BeautifulSoup(col_str, "html.parser").get_text()
all_header.append(cleantext2)
print(all_header)
```

    ['[Postcode, Borough, Neighbourhood\n, Canadian postal codes\n]']



```python
# Herre we create a new datarame from the extracted texts
df2 = pd.DataFrame(all_header)
df2.head()
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
      <th>0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>[Postcode, Borough, Neighbourhood\n, Canadian ...</td>
    </tr>
  </tbody>
</table>
</div>




```python
# now we get ride of the commas as we did before
df3 = df2[0].str.split(',', expand=True)
df3.head()
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>[Postcode</td>
      <td>Borough</td>
      <td>Neighbourhood\n</td>
      <td>Canadian postal codes\n]</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Here we remove. the [ from the first column of the dataframe
df3[0] = df3[0].str.strip('[')
df3.head()
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Postcode</td>
      <td>Borough</td>
      <td>Neighbourhood\n</td>
      <td>Canadian postal codes\n]</td>
    </tr>
  </tbody>
</table>
</div>




```python
# now we concat the two dataframes
frames = [df3, df1]
df4 = pd.concat(frames)
df4.head(5)
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Postcode</td>
      <td>Borough</td>
      <td>Neighbourhood\n</td>
      <td>Canadian postal codes\n]</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned\n</td>
      <td>None</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned\n</td>
      <td>None</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods\n</td>
      <td>None</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village\n</td>
      <td>None</td>
    </tr>
  </tbody>
</table>
</div>




```python
# we also see that we don;t need the last column so lets drop it
df4.drop([3], axis=1, inplace=True)
df4.head()
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Postcode</td>
      <td>Borough</td>
      <td>Neighbourhood\n</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned\n</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned\n</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods\n</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village\n</td>
    </tr>
  </tbody>
</table>
</div>




```python
# let's also get ride of that pesky /n in the neighbourhood column
df4[2] = df4[2].replace('\n','', regex=True)
df4.head()
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Postcode</td>
      <td>Borough</td>
      <td>Neighbourhood</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
  </tbody>
</table>
</div>




```python
#finally lets rename the columns
df5=df4.rename(columns=df4.iloc[0])
df5.head()
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
      <th>Postcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Postcode</td>
      <td>Borough</td>
      <td>Neighbourhood</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Now we can drop the first row
df5.drop([0], axis=0, inplace=True)
df5.head()
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
      <th>Postcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <td>5</td>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
    </tr>
  </tbody>
</table>
</div>



#### let's clean the data a bit


```python
# we rename the columns 
df5.rename(columns={'Postcode':'Postalcode', ' Borough':'Borough', ' Neighbourhood': 'Neighbourhood'}, inplace=True)
df5.head()
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <td>5</td>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
    </tr>
  </tbody>
</table>
</div>




```python
df5.columns = list(map(str, df5.columns))

all(isinstance(column, str) for column in df5.columns)
```




    True




```python
# Tail Has 5 rows that are useless 
#so lets get ride of the.
df5.tail()
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>289</td>
      <td>]</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <td>290</td>
      <td>\n</td>
      <td>\n]</td>
      <td>None</td>
    </tr>
    <tr>
      <td>291</td>
      <td>\n\n\nNL\n\nNS\n\nPE\n\nNB\n\nQC\n\nON\n\nMB\n...</td>
      <td>NL\n</td>
      <td>NS</td>
    </tr>
    <tr>
      <td>292</td>
      <td>NL\n</td>
      <td>NS\n</td>
      <td>PE</td>
    </tr>
    <tr>
      <td>293</td>
      <td>A\n</td>
      <td>B\n</td>
      <td>C</td>
    </tr>
  </tbody>
</table>
</div>




```python
df5.drop([293, 292, 291, 290, 289], axis=0, inplace=True)
df5.tail()
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>284</td>
      <td>M8Z</td>
      <td>Etobicoke</td>
      <td>Mimico NW</td>
    </tr>
    <tr>
      <td>285</td>
      <td>M8Z</td>
      <td>Etobicoke</td>
      <td>The Queensway West</td>
    </tr>
    <tr>
      <td>286</td>
      <td>M8Z</td>
      <td>Etobicoke</td>
      <td>Royal York South West</td>
    </tr>
    <tr>
      <td>287</td>
      <td>M8Z</td>
      <td>Etobicoke</td>
      <td>South of Bloor</td>
    </tr>
    <tr>
      <td>288</td>
      <td>M9Z</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Shape looks better
df5.shape
```




    (288, 3)




```python
# Now lets organize the data set
# First we set Borough as not equal to Not assigned
df6 = df5[df5.Borough != ' Not assigned']
df6.head()
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>3</td>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <td>5</td>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
    </tr>
    <tr>
      <td>6</td>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park</td>
    </tr>
    <tr>
      <td>7</td>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Heights</td>
    </tr>
  </tbody>
</table>
</div>




```python
#we look at the shape again
df6.shape
```




    (211, 3)




```python
# Now we set Neighbourhood "NOt assigned" to equal to Borough
df6.Neighbourhood[df6.Neighbourhood == ' Not assigned'] = df6.Borough
```


```python
# Now we group the columns based on the previous criterias we set
# we also join Neighbourhoods that have same Borough & Postalcode
df6 = df6.groupby(['Postalcode','Borough'])['Neighbourhood'].apply(', '.join).reset_index()
df6.head()
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge,  Malvern</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Highland Creek,  Rouge Hill,  Port Union</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood,  Morningside,  West Hill</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
    </tr>
  </tbody>
</table>
</div>




```python
#we look at the shape again
df6.shape
```




    (103, 3)




```python
# Now we import the Latitude and Lonitude 
url2="http://cocl.us/Geospatial_data"
dfg=pd.read_csv(url2)
dfg.head(2)
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
      <th>Postal Code</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Now we rename the imported Postalcode to match the dataframe we previous made
dfg.rename(columns={'Postal Code':'Postalcode'}, inplace=True)
dfg.head(2)
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
      <th>Postalcode</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Now we merge the two Data frames with the Postalcode parameter
df7 = pd.merge(df6, dfg, on='Postalcode')
df7.head(10)
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge,  Malvern</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Highland Creek,  Rouge Hill,  Port Union</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood,  Morningside,  West Hill</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <td>5</td>
      <td>M1J</td>
      <td>Scarborough</td>
      <td>Scarborough Village</td>
      <td>43.744734</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <td>6</td>
      <td>M1K</td>
      <td>Scarborough</td>
      <td>East Birchmount Park,  Ionview,  Kennedy Park</td>
      <td>43.727929</td>
      <td>-79.262029</td>
    </tr>
    <tr>
      <td>7</td>
      <td>M1L</td>
      <td>Scarborough</td>
      <td>Clairlea,  Golden Mile,  Oakridge</td>
      <td>43.711112</td>
      <td>-79.284577</td>
    </tr>
    <tr>
      <td>8</td>
      <td>M1M</td>
      <td>Scarborough</td>
      <td>Cliffcrest,  Cliffside,  Scarborough Village ...</td>
      <td>43.716316</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <td>9</td>
      <td>M1N</td>
      <td>Scarborough</td>
      <td>Birch Cliff,  Cliffside West</td>
      <td>43.692657</td>
      <td>-79.264848</td>
    </tr>
  </tbody>
</table>
</div>




```python
#we look at the shape again
df7.shape
```




    (103, 5)




```python
print('The dataframe has {} boroughs and {} neighbourhoods.'.format(
        len(df7['Borough'].unique()),
        df7.shape[0]
    )
)
```

    The dataframe has 11 boroughs and 103 neighbourhoods.



```python
import numpy as np # library to handle data in a vectorized manner

import pandas as pd # library for data analsysis
pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', None)

import json # library to handle JSON files

#!conda install -c conda-forge geopy --yes 
from geopy.geocoders import Nominatim # convert an address into latitude and longitude values

import requests # library to handle requests
from pandas.io.json import json_normalize # tranform JSON file into a pandas dataframe

# Matplotlib and associated plotting modules
import matplotlib.cm as cm
import matplotlib.colors as colors

# import k-means from clustering stage
from sklearn.cluster import KMeans

#!conda install -c conda-forge folium=0.5.0 --yes
import folium # map rendering library

print('Libraries imported.')
```

    Libraries imported.



```python
address = "27 King's College Cir, Toronto, ON M5S 1A1; Canada"

geolocator = Nominatim()
location = geolocator.geocode(address)
latitude = location.latitude
longitude = location.longitude
print("The geograpical coordinate of 27 King's College Cir, Toronto, ON M5S 1A1; Canada are {}, {}.".format(latitude, longitude))
```

    /home/jupyterlab/conda/envs/python/lib/python3.6/site-packages/ipykernel_launcher.py:3: DeprecationWarning: Using Nominatim with the default "geopy/1.20.0" `user_agent` is strongly discouraged, as it violates Nominatim's ToS https://operations.osmfoundation.org/policies/nominatim/ and may possibly cause 403 and 429 HTTP errors. Please specify a custom `user_agent` with `Nominatim(user_agent="my-application")` or by overriding the default `user_agent`: `geopy.geocoders.options.default_user_agent = "my-application"`. In geopy 2.0 this will become an exception.
      This is separate from the ipykernel package so we can avoid doing imports until


    The geograpical coordinate of 27 King's College Cir, Toronto, ON M5S 1A1; Canada are 43.6607225, -79.3958255.


#### Now we plot a map and add markers to the map (borough & Neighbourhood)


```python
map = folium.Map(location=[latitude, longitude], zoom_start=11)
# Now we add markers to the map (borough & Neighbourhood)
for lat, lng, borough, neighbourhood in zip(df7['Latitude'], df7['Longitude'], df7['Borough'], df7['Neighbourhood']):
    label = '{}, {}'.format(neighbourhood, borough)
    label = folium.Popup(label, parse_html=True)
    folium.CircleMarker(
    [lat, lng],
    radius=4.5,
    popup=label,
    color='red',
    fill=True,
    fill_color='yellow',
    fill_opacity=0.7,
    parse_html=False).add_to(map)
map
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1IiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNScsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjYwNzIyNSwtNzkuMzk1ODI1NV0sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB6b29tOiAxMSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIG1heEJvdW5kczogYm91bmRzLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgbGF5ZXJzOiBbXSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHdvcmxkQ29weUp1bXA6IGZhbHNlLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgY3JzOiBMLkNSUy5FUFNHMzg1NwogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfYmU4NjNkYjY5Yjg1NDJhNjk4NzJiYzdkNWQ0NjM4ZjEgPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICdodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZycsCiAgICAgICAgICAgICAgICB7CiAgImF0dHJpYnV0aW9uIjogbnVsbCwKICAiZGV0ZWN0UmV0aW5hIjogZmFsc2UsCiAgIm1heFpvb20iOiAxOCwKICAibWluWm9vbSI6IDEsCiAgIm5vV3JhcCI6IGZhbHNlLAogICJzdWJkb21haW5zIjogImFiYyIKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZjNjI1Mjk5MjEzNTQ4ZDVhOTI1MzMxMjg4NjRmYjQxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuODA2Njg2Mjk5OTk5OTk2LC03OS4xOTQzNTM0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iNmE0M2MwOWIzZTY0ZjRhYmU4MjhkZDhjODdjOGY4ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85MThiNTU5MWY0ZTQ0NTYwYWRmMWJmODE5YTk4ZjU4YiA9ICQoJzxkaXYgaWQ9Imh0bWxfOTE4YjU1OTFmNGU0NDU2MGFkZjFiZjgxOWE5OGY1OGIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBSb3VnZSwgIE1hbHZlcm4sICBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjZhNDNjMDliM2U2NGY0YWJlODI4ZGQ4Yzg3YzhmOGUuc2V0Q29udGVudChodG1sXzkxOGI1NTkxZjRlNDQ1NjBhZGYxYmY4MTlhOThmNThiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzZjNjI1Mjk5MjEzNTQ4ZDVhOTI1MzMxMjg4NjRmYjQxLmJpbmRQb3B1cChwb3B1cF9iNmE0M2MwOWIzZTY0ZjRhYmU4MjhkZDhjODdjOGY4ZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83NGY5ZDdmZjUyNzM0NDViYjA4ZDVhNjgxNDkyYmJjNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc4NDUzNTEsLTc5LjE2MDQ5NzA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlkZTVjMjMyYmVmMDQ2NGJiNDE1NTZmMDkzZDdlYzU0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2UzYTIwNWJmZDM5NDQ1ZWViOWU5NjBlNmRmODIzN2ZlID0gJCgnPGRpdiBpZD0iaHRtbF9lM2EyMDViZmQzOTQ0NWVlYjllOTYwZTZkZjgyMzdmZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IEhpZ2hsYW5kIENyZWVrLCAgUm91Z2UgSGlsbCwgIFBvcnQgVW5pb24sICBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWRlNWMyMzJiZWYwNDY0YmI0MTU1NmYwOTNkN2VjNTQuc2V0Q29udGVudChodG1sX2UzYTIwNWJmZDM5NDQ1ZWViOWU5NjBlNmRmODIzN2ZlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc0ZjlkN2ZmNTI3MzQ0NWJiMDhkNWE2ODE0OTJiYmM2LmJpbmRQb3B1cChwb3B1cF85ZGU1YzIzMmJlZjA0NjRiYjQxNTU2ZjA5M2Q3ZWM1NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81MmM0YTdmMzEyNDA0MzBlYjZjZjAzYTE2OWFjNjFhMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc2MzU3MjYsLTc5LjE4ODcxMTVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWNhZjFhYzc3MTM5NDlhNWFlY2RlM2I2NDE5ZjllY2EgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTI5M2Y1YTliM2Q2NDkzZmE5NjEwM2ViMzhiNzBiY2QgPSAkKCc8ZGl2IGlkPSJodG1sX2UyOTNmNWE5YjNkNjQ5M2ZhOTYxMDNlYjM4YjcwYmNkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gR3VpbGR3b29kLCAgTW9ybmluZ3NpZGUsICBXZXN0IEhpbGwsICBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWNhZjFhYzc3MTM5NDlhNWFlY2RlM2I2NDE5ZjllY2Euc2V0Q29udGVudChodG1sX2UyOTNmNWE5YjNkNjQ5M2ZhOTYxMDNlYjM4YjcwYmNkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzUyYzRhN2YzMTI0MDQzMGViNmNmMDNhMTY5YWM2MWEzLmJpbmRQb3B1cChwb3B1cF9hY2FmMWFjNzcxMzk0OWE1YWVjZGUzYjY0MTlmOWVjYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iZGMxN2M5NDMwYTg0MTU2OWExN2VhYWM5YThiMmFjMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc3MDk5MjEsLTc5LjIxNjkxNzQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzVhYTNiNjllYTZiMzRjZTFiOWE0ZDA3NDhjN2M1ZGViID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzZlNjk1ZDNlOWFhMTRiMGRhMGJiNzJkN2U4NDkxZjM3ID0gJCgnPGRpdiBpZD0iaHRtbF82ZTY5NWQzZTlhYTE0YjBkYTBiYjcyZDdlODQ5MWYzNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFdvYnVybiwgIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81YWEzYjY5ZWE2YjM0Y2UxYjlhNGQwNzQ4YzdjNWRlYi5zZXRDb250ZW50KGh0bWxfNmU2OTVkM2U5YWExNGIwZGEwYmI3MmQ3ZTg0OTFmMzcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYmRjMTdjOTQzMGE4NDE1NjlhMTdlYWFjOWE4YjJhYzEuYmluZFBvcHVwKHBvcHVwXzVhYTNiNjllYTZiMzRjZTFiOWE0ZDA3NDhjN2M1ZGViKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2E0ZGU5ODM4YmE0MjQyYTI5ZTM0YjI1MTNhMzI5OGJiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzczMTM2LC03OS4yMzk0NzYwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iY2QwMWU3OTg5N2M0NTk2ODZiMDZkMWY2NWM0OTc4ZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iZjEzYzVlMmQzNDI0NzZkOTkxNTAxNTIwOThlZTEyYSA9ICQoJzxkaXYgaWQ9Imh0bWxfYmYxM2M1ZTJkMzQyNDc2ZDk5MTUwMTUyMDk4ZWUxMmEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBDZWRhcmJyYWUsICBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmNkMDFlNzk4OTdjNDU5Njg2YjA2ZDFmNjVjNDk3OGQuc2V0Q29udGVudChodG1sX2JmMTNjNWUyZDM0MjQ3NmQ5OTE1MDE1MjA5OGVlMTJhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E0ZGU5ODM4YmE0MjQyYTI5ZTM0YjI1MTNhMzI5OGJiLmJpbmRQb3B1cChwb3B1cF9iY2QwMWU3OTg5N2M0NTk2ODZiMDZkMWY2NWM0OTc4ZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kMzVhNjNkNTA1ZTM0MGRiODFlYjhiMDc4OGY4YWJkMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc0NDczNDIsLTc5LjIzOTQ3NjA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk4M2JlYzJhM2NmMjQ4MDViNTdmNDJhNDRjZmY2NjY0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzk3ZmI4YWVjZTAyYzRhZDdiYmNmNGEwYTBjM2Q1MTVhID0gJCgnPGRpdiBpZD0iaHRtbF85N2ZiOGFlY2UwMmM0YWQ3YmJjZjRhMGEwYzNkNTE1YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFNjYXJib3JvdWdoIFZpbGxhZ2UsICBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTgzYmVjMmEzY2YyNDgwNWI1N2Y0MmE0NGNmZjY2NjQuc2V0Q29udGVudChodG1sXzk3ZmI4YWVjZTAyYzRhZDdiYmNmNGEwYTBjM2Q1MTVhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2QzNWE2M2Q1MDVlMzQwZGI4MWViOGIwNzg4ZjhhYmQyLmJpbmRQb3B1cChwb3B1cF85ODNiZWMyYTNjZjI0ODA1YjU3ZjQyYTQ0Y2ZmNjY2NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kZWIzNWUzMjZhNTU0ZTFiOTNhNmU3NGM4NzRlNGViZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyNzkyOTIsLTc5LjI2MjAyOTQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2ZhMDNmYzdkNGRjYTQ1YmVhMzI0YTgwMTFlMzg1ZGZhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzY5YjE3YjUxYmMzYzRlNGU4MTIyZGUxMjYwZjM0OGVkID0gJCgnPGRpdiBpZD0iaHRtbF82OWIxN2I1MWJjM2M0ZTRlODEyMmRlMTI2MGYzNDhlZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IEVhc3QgQmlyY2htb3VudCBQYXJrLCAgSW9udmlldywgIEtlbm5lZHkgUGFyaywgIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mYTAzZmM3ZDRkY2E0NWJlYTMyNGE4MDExZTM4NWRmYS5zZXRDb250ZW50KGh0bWxfNjliMTdiNTFiYzNjNGU0ZTgxMjJkZTEyNjBmMzQ4ZWQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZGViMzVlMzI2YTU1NGUxYjkzYTZlNzRjODc0ZTRlYmUuYmluZFBvcHVwKHBvcHVwX2ZhMDNmYzdkNGRjYTQ1YmVhMzI0YTgwMTFlMzg1ZGZhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzkwNzYzNDFmNjE0NjRmYWE5MDIyMjIyNzhhODQ5YWY5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzExMTExNzAwMDAwMDA0LC03OS4yODQ1NzcyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzBmNjQyODE2NmMyYTRjMDlhYjQ4OGVhMTZjZDY3NzEwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2I0NTMxZmVlZjNkMDQ2NTJiZjEzYjEzNTdhNzlmM2NkID0gJCgnPGRpdiBpZD0iaHRtbF9iNDUzMWZlZWYzZDA0NjUyYmYxM2IxMzU3YTc5ZjNjZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IENsYWlybGVhLCAgR29sZGVuIE1pbGUsICBPYWtyaWRnZSwgIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wZjY0MjgxNjZjMmE0YzA5YWI0ODhlYTE2Y2Q2NzcxMC5zZXRDb250ZW50KGh0bWxfYjQ1MzFmZWVmM2QwNDY1MmJmMTNiMTM1N2E3OWYzY2QpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTA3NjM0MWY2MTQ2NGZhYTkwMjIyMjI3OGE4NDlhZjkuYmluZFBvcHVwKHBvcHVwXzBmNjQyODE2NmMyYTRjMDlhYjQ4OGVhMTZjZDY3NzEwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzdmNmExYTU0ZTE3OTQ4NmJhZDliMTRhYjAxYmIwYjYwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzE2MzE2LC03OS4yMzk0NzYwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kNDkwN2NhZDZhYTU0MjhmOWU2MDk2OTgxNDA1OTIzMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81ZjhmZmEyZmFhOTM0Yzc4YjZiN2ZhNWIzYzk4NDkwYyA9ICQoJzxkaXYgaWQ9Imh0bWxfNWY4ZmZhMmZhYTkzNGM3OGI2YjdmYTViM2M5ODQ5MGMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBDbGlmZmNyZXN0LCAgQ2xpZmZzaWRlLCAgU2NhcmJvcm91Z2ggVmlsbGFnZSBXZXN0LCAgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Q0OTA3Y2FkNmFhNTQyOGY5ZTYwOTY5ODE0MDU5MjMzLnNldENvbnRlbnQoaHRtbF81ZjhmZmEyZmFhOTM0Yzc4YjZiN2ZhNWIzYzk4NDkwYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83ZjZhMWE1NGUxNzk0ODZiYWQ5YjE0YWIwMWJiMGI2MC5iaW5kUG9wdXAocG9wdXBfZDQ5MDdjYWQ2YWE1NDI4ZjllNjA5Njk4MTQwNTkyMzMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODg5Njc5ODhjYjNiNDYyOWFmMjViMTRmYTYxZDVhNzkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTI2NTcwMDAwMDAwMDQsLTc5LjI2NDg0ODFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNWU4ODNjNWQ1MDY0NDkzYTlhYmRlYTE1NDRkODFkYTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOWJiM2QxZTU5YjdkNDc2ZGE4ZjdiYTkxYTA3Y2FjZGYgPSAkKCc8ZGl2IGlkPSJodG1sXzliYjNkMWU1OWI3ZDQ3NmRhOGY3YmE5MWEwN2NhY2RmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gQmlyY2ggQ2xpZmYsICBDbGlmZnNpZGUgV2VzdCwgIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81ZTg4M2M1ZDUwNjQ0OTNhOWFiZGVhMTU0NGQ4MWRhMy5zZXRDb250ZW50KGh0bWxfOWJiM2QxZTU5YjdkNDc2ZGE4ZjdiYTkxYTA3Y2FjZGYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODg5Njc5ODhjYjNiNDYyOWFmMjViMTRmYTYxZDVhNzkuYmluZFBvcHVwKHBvcHVwXzVlODgzYzVkNTA2NDQ5M2E5YWJkZWExNTQ0ZDgxZGEzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzAzYTM5OGYzYzhlODRjNDdhNTM1OTYwZWFiMGIzNzA5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzU3NDA5NiwtNzkuMjczMzA0MDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWQ3YWE4NzA3MjA1NDhjZjg0ZjAwOWRhY2E5OTE1NjkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWZjNDY1ZGU5Y2QwNDMyMWEzYTdjYTM5NTc2OGZhMjkgPSAkKCc8ZGl2IGlkPSJodG1sXzVmYzQ2NWRlOWNkMDQzMjFhM2E3Y2EzOTU3NjhmYTI5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gRG9yc2V0IFBhcmssICBTY2FyYm9yb3VnaCBUb3duIENlbnRyZSwgIFdleGZvcmQgSGVpZ2h0cywgIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hZDdhYTg3MDcyMDU0OGNmODRmMDA5ZGFjYTk5MTU2OS5zZXRDb250ZW50KGh0bWxfNWZjNDY1ZGU5Y2QwNDMyMWEzYTdjYTM5NTc2OGZhMjkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDNhMzk4ZjNjOGU4NGM0N2E1MzU5NjBlYWIwYjM3MDkuYmluZFBvcHVwKHBvcHVwX2FkN2FhODcwNzIwNTQ4Y2Y4NGYwMDlkYWNhOTkxNTY5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzcwNzAzMGYyYzNlNjQ0NmQ4ZTZmMTJkNjk3NDU5ZDEwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzUwMDcxNTAwMDAwMDA0LC03OS4yOTU4NDkxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzQwZWU4M2RkMDdlMjQ1MWJhNDIxN2E2ODQzMTMyOGU4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzcxMmI5ZGIwM2JlOTRhYzk5YmY5MWJjMDQ4MWFkMjNlID0gJCgnPGRpdiBpZD0iaHRtbF83MTJiOWRiMDNiZTk0YWM5OWJmOTFiYzA0ODFhZDIzZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IE1hcnl2YWxlLCAgV2V4Zm9yZCwgIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80MGVlODNkZDA3ZTI0NTFiYTQyMTdhNjg0MzEzMjhlOC5zZXRDb250ZW50KGh0bWxfNzEyYjlkYjAzYmU5NGFjOTliZjkxYmMwNDgxYWQyM2UpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzA3MDMwZjJjM2U2NDQ2ZDhlNmYxMmQ2OTc0NTlkMTAuYmluZFBvcHVwKHBvcHVwXzQwZWU4M2RkMDdlMjQ1MWJhNDIxN2E2ODQzMTMyOGU4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzE5MTUzMWJmM2NmOTQ5MjM5ODdmYmY1YTFkMDI3MDY1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzk0MjAwMywtNzkuMjYyMDI5NDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDgxODJhZGU2Y2RmNDc4MWFhZmIzZGNhM2Q5MzNmNzcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYmEyZTI2MTA4ZGI1NDFkYjgyNDE3NjVkMjE1MzgyNTkgPSAkKCc8ZGl2IGlkPSJodG1sX2JhMmUyNjEwOGRiNTQxZGI4MjQxNzY1ZDIxNTM4MjU5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gQWdpbmNvdXJ0LCAgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzA4MTgyYWRlNmNkZjQ3ODFhYWZiM2RjYTNkOTMzZjc3LnNldENvbnRlbnQoaHRtbF9iYTJlMjYxMDhkYjU0MWRiODI0MTc2NWQyMTUzODI1OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xOTE1MzFiZjNjZjk0OTIzOTg3ZmJmNWExZDAyNzA2NS5iaW5kUG9wdXAocG9wdXBfMDgxODJhZGU2Y2RmNDc4MWFhZmIzZGNhM2Q5MzNmNzcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmYyOWI5NDA0MWVjNGI2YWIyNGFiODU3NDQ4MDgzYjcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43ODE2Mzc1LC03OS4zMDQzMDIxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzA4YTM3M2JkODE0ODQ4YmRiNDNkNTMyNTJmNzJlMzYyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzdkZGE4MmY5MGE1MjRhNWQ4MWMwZGYwN2FiMTllODFhID0gJCgnPGRpdiBpZD0iaHRtbF83ZGRhODJmOTBhNTI0YTVkODFjMGRmMDdhYjE5ZTgxYSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IENsYXJrcyBDb3JuZXJzLCAgU3VsbGl2YW4sICBUYW0gTyYjMzk7U2hhbnRlciwgIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wOGEzNzNiZDgxNDg0OGJkYjQzZDUzMjUyZjcyZTM2Mi5zZXRDb250ZW50KGh0bWxfN2RkYTgyZjkwYTUyNGE1ZDgxYzBkZjA3YWIxOWU4MWEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZmYyOWI5NDA0MWVjNGI2YWIyNGFiODU3NDQ4MDgzYjcuYmluZFBvcHVwKHBvcHVwXzA4YTM3M2JkODE0ODQ4YmRiNDNkNTMyNTJmNzJlMzYyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2ZlYmQwNzU3ZWNiZTRjYTBhNGMyODM0MzFiMDVlYzZhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuODE1MjUyMiwtNzkuMjg0NTc3Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jZTQ5NmU0N2M0ZjY0ODlhOThjNzdiMGI4ZDg4NzhhNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85M2VlNWEzNDY0NmU0NGFmYWUzZWFkM2I3NjJjNjZmMCA9ICQoJzxkaXYgaWQ9Imh0bWxfOTNlZTVhMzQ2NDZlNDRhZmFlM2VhZDNiNzYyYzY2ZjAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBBZ2luY291cnQgTm9ydGgsICBMJiMzOTtBbW9yZWF1eCBFYXN0LCAgTWlsbGlrZW4sICBTdGVlbGVzIEVhc3QsICBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfY2U0OTZlNDdjNGY2NDg5YTk4Yzc3YjBiOGQ4ODc4YTQuc2V0Q29udGVudChodG1sXzkzZWU1YTM0NjQ2ZTQ0YWZhZTNlYWQzYjc2MmM2NmYwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2ZlYmQwNzU3ZWNiZTRjYTBhNGMyODM0MzFiMDVlYzZhLmJpbmRQb3B1cChwb3B1cF9jZTQ5NmU0N2M0ZjY0ODlhOThjNzdiMGI4ZDg4NzhhNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85YTg5YmI1NzQyY2Q0ZDAyYmU2ZTg5YjE0ODQ2ZTFmNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc5OTUyNTIwMDAwMDAwNSwtNzkuMzE4Mzg4N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kNmM3MGI5N2QzMjQ0OTFlODJmYWQ0OWU1ZWJlNTcwYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mYjhjMjNiZDQzNjQ0NzIwYTg4YjA2MzRjNTBlZTBiNCA9ICQoJzxkaXYgaWQ9Imh0bWxfZmI4YzIzYmQ0MzY0NDcyMGE4OGIwNjM0YzUwZWUwYjQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBMJiMzOTtBbW9yZWF1eCBXZXN0LCAgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Q2YzcwYjk3ZDMyNDQ5MWU4MmZhZDQ5ZTVlYmU1NzBhLnNldENvbnRlbnQoaHRtbF9mYjhjMjNiZDQzNjQ0NzIwYTg4YjA2MzRjNTBlZTBiNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85YTg5YmI1NzQyY2Q0ZDAyYmU2ZTg5YjE0ODQ2ZTFmNi5iaW5kUG9wdXAocG9wdXBfZDZjNzBiOTdkMzI0NDkxZTgyZmFkNDllNWViZTU3MGEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOGFmODJjZTEzNWQ2NDdjMzg4MDcxNTNmN2ZiYTQzNmIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My44MzYxMjQ3MDAwMDAwMDYsLTc5LjIwNTYzNjA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2ViYTQ4NTQ0YWJhMjQ4YjdiNTAwNTliNTA4MjQ0M2E0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzU2MWFhYzY4MDE2MzRjNWQ5ZGJiOWU2MjEwN2U4MDg1ID0gJCgnPGRpdiBpZD0iaHRtbF81NjFhYWM2ODAxNjM0YzVkOWRiYjllNjIxMDdlODA4NSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFVwcGVyIFJvdWdlLCAgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2ViYTQ4NTQ0YWJhMjQ4YjdiNTAwNTliNTA4MjQ0M2E0LnNldENvbnRlbnQoaHRtbF81NjFhYWM2ODAxNjM0YzVkOWRiYjllNjIxMDdlODA4NSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84YWY4MmNlMTM1ZDY0N2MzODgwNzE1M2Y3ZmJhNDM2Yi5iaW5kUG9wdXAocG9wdXBfZWJhNDg1NDRhYmEyNDhiN2I1MDA1OWI1MDgyNDQzYTQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWJjOTZkYTRiMDBjNGU2YmIxOGJjNzIxMjcxZGZhOGIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My44MDM3NjIyLC03OS4zNjM0NTE3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Q3NDJhOWMwZGFlMzQxZTk5OThkOThhZGFjOTNkOGJhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzVlYWFlNDA4MjAyMDQwMzI5ZWFkNjU0MmYyMjU2Y2ZhID0gJCgnPGRpdiBpZD0iaHRtbF81ZWFhZTQwODIwMjA0MDMyOWVhZDY1NDJmMjI1NmNmYSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IEhpbGxjcmVzdCBWaWxsYWdlLCAgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDc0MmE5YzBkYWUzNDFlOTk5OGQ5OGFkYWM5M2Q4YmEuc2V0Q29udGVudChodG1sXzVlYWFlNDA4MjAyMDQwMzI5ZWFkNjU0MmYyMjU2Y2ZhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFiYzk2ZGE0YjAwYzRlNmJiMThiYzcyMTI3MWRmYThiLmJpbmRQb3B1cChwb3B1cF9kNzQyYTljMGRhZTM0MWU5OTk4ZDk4YWRhYzkzZDhiYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jZGZmYTgzYWYyOTk0NTRiOWIyMTBjYzE3MDhjZjNmYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc3ODUxNzUsLTc5LjM0NjU1NTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZGZkOGUxNDhmM2YxNDk1NGI1Y2I3MTU5M2U1OWI4OTIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYmJmM2MzZGIzZGNjNDIyNDg2NDM1NzEyNTk4Yzc3N2IgPSAkKCc8ZGl2IGlkPSJodG1sX2JiZjNjM2RiM2RjYzQyMjQ4NjQzNTcxMjU5OGM3NzdiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gRmFpcnZpZXcsICBIZW5yeSBGYXJtLCAgT3Jpb2xlLCAgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZGZkOGUxNDhmM2YxNDk1NGI1Y2I3MTU5M2U1OWI4OTIuc2V0Q29udGVudChodG1sX2JiZjNjM2RiM2RjYzQyMjQ4NjQzNTcxMjU5OGM3NzdiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2NkZmZhODNhZjI5OTQ1NGI5YjIxMGNjMTcwOGNmM2ZiLmJpbmRQb3B1cChwb3B1cF9kZmQ4ZTE0OGYzZjE0OTU0YjVjYjcxNTkzZTU5Yjg5Mik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wZDg0NzdhNDBiMGI0MTc1Yjc3OWFmOTA0OTRhNmE2YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc4Njk0NzMsLTc5LjM4NTk3NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hYWEyYTRmYjg2YzI0MDIyODc1YzhlNDY0ZTQxZDdkYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84YTBmMGI4OTgxNmM0MzE3YWJlOTZhNWYxMDBmOTAyZCA9ICQoJzxkaXYgaWQ9Imh0bWxfOGEwZjBiODk4MTZjNDMxN2FiZTk2YTVmMTAwZjkwMmQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBCYXl2aWV3IFZpbGxhZ2UsICBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hYWEyYTRmYjg2YzI0MDIyODc1YzhlNDY0ZTQxZDdkYS5zZXRDb250ZW50KGh0bWxfOGEwZjBiODk4MTZjNDMxN2FiZTk2YTVmMTAwZjkwMmQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMGQ4NDc3YTQwYjBiNDE3NWI3NzlhZjkwNDk0YTZhNmMuYmluZFBvcHVwKHBvcHVwX2FhYTJhNGZiODZjMjQwMjI4NzVjOGU0NjRlNDFkN2RhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzcwMDhlZjBkM2EyOTRkOTU5MzkwZWM2NmQ5ZjdiMWYyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzU3NDkwMiwtNzkuMzc0NzE0MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzMwN2E3ODkyNDQxNGZlNDg1NjQ4YWZjZmZmNGYyZWQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDNiZTBhYTA1NTM4NGVlMzliNTdiNTgyYWQ3OWYzM2UgPSAkKCc8ZGl2IGlkPSJodG1sX2QzYmUwYWEwNTUzODRlZTM5YjU3YjU4MmFkNzlmMzNlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gU2lsdmVyIEhpbGxzLCAgWW9yayBNaWxscywgIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzczMDdhNzg5MjQ0MTRmZTQ4NTY0OGFmY2ZmZjRmMmVkLnNldENvbnRlbnQoaHRtbF9kM2JlMGFhMDU1Mzg0ZWUzOWI1N2I1ODJhZDc5ZjMzZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83MDA4ZWYwZDNhMjk0ZDk1OTM5MGVjNjZkOWY3YjFmMi5iaW5kUG9wdXAocG9wdXBfNzMwN2E3ODkyNDQxNGZlNDg1NjQ4YWZjZmZmNGYyZWQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmJmNTgxMDEzNTIwNDZjOGFmOWYwNzI5YTA3YTYzMGMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43ODkwNTMsLTc5LjQwODQ5Mjc5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Q5ZTcyMDU1NmU4MzQ4MTRiMTk1MDM5OTg2NTAwZGRkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2E2MzZjNTkxOTkyMzQ0ZTdhMjBjOGNiOTc5OWVjMDRmID0gJCgnPGRpdiBpZD0iaHRtbF9hNjM2YzU5MTk5MjM0NGU3YTIwYzhjYjk3OTllYzA0ZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IE5ld3RvbmJyb29rLCAgV2lsbG93ZGFsZSwgIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Q5ZTcyMDU1NmU4MzQ4MTRiMTk1MDM5OTg2NTAwZGRkLnNldENvbnRlbnQoaHRtbF9hNjM2YzU5MTk5MjM0NGU3YTIwYzhjYjk3OTllYzA0Zik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mYmY1ODEwMTM1MjA0NmM4YWY5ZjA3MjlhMDdhNjMwYy5iaW5kUG9wdXAocG9wdXBfZDllNzIwNTU2ZTgzNDgxNGIxOTUwMzk5ODY1MDBkZGQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNmI0ZTQ0NTUyNGNmNGZjMzg1OTU1MGUwMGFmMjlhMWYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43NzAxMTk5LC03OS40MDg0OTI3OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kY2RhNWIzMGViZGQ0ODgwODIxYzAyZjFkMDBiMzBmMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wZjcyYThkN2EwNTQ0MDVlYTUzMDkxZjY2YWMxNTBjYSA9ICQoJzxkaXYgaWQ9Imh0bWxfMGY3MmE4ZDdhMDU0NDA1ZWE1MzA5MWY2NmFjMTUwY2EiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBXaWxsb3dkYWxlIFNvdXRoLCAgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZGNkYTViMzBlYmRkNDg4MDgyMWMwMmYxZDAwYjMwZjIuc2V0Q29udGVudChodG1sXzBmNzJhOGQ3YTA1NDQwNWVhNTMwOTFmNjZhYzE1MGNhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzZiNGU0NDU1MjRjZjRmYzM4NTk1NTBlMDBhZjI5YTFmLmJpbmRQb3B1cChwb3B1cF9kY2RhNWIzMGViZGQ0ODgwODIxYzAyZjFkMDBiMzBmMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83NDgzYzExYTU3ZTk0ZGM1OTBiNDcxOTQ3NDYzODdiMCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1Mjc1ODI5OTk5OTk5NiwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wYWUwNjM1MWMxZGM0Zjk5YTNhMzU3M2RhMDM2ODU2NyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xNDdkZDgxOWU3MTQ0MmU3YWVhMzc5NDU4MTNkNjc2ZiA9ICQoJzxkaXYgaWQ9Imh0bWxfMTQ3ZGQ4MTllNzE0NDJlN2FlYTM3OTQ1ODEzZDY3NmYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBZb3JrIE1pbGxzIFdlc3QsICBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wYWUwNjM1MWMxZGM0Zjk5YTNhMzU3M2RhMDM2ODU2Ny5zZXRDb250ZW50KGh0bWxfMTQ3ZGQ4MTllNzE0NDJlN2FlYTM3OTQ1ODEzZDY3NmYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzQ4M2MxMWE1N2U5NGRjNTkwYjQ3MTk0NzQ2Mzg3YjAuYmluZFBvcHVwKHBvcHVwXzBhZTA2MzUxYzFkYzRmOTlhM2EzNTczZGEwMzY4NTY3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzYxZDU0OTkzZWQ4OTQzMWM4MWQ3OTczZDI4MjhkODJjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzgyNzM2NCwtNzkuNDQyMjU5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83OGRkMDAzNzhkZDU0ZmU3YjM4NmI5Y2ExYWFlM2M0MyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iOTZiZWZiMWY0NTQ0ZjRhOTg1ZmQ2N2UzM2IyNzgxMSA9ICQoJzxkaXYgaWQ9Imh0bWxfYjk2YmVmYjFmNDU0NGY0YTk4NWZkNjdlMzNiMjc4MTEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBXaWxsb3dkYWxlIFdlc3QsICBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83OGRkMDAzNzhkZDU0ZmU3YjM4NmI5Y2ExYWFlM2M0My5zZXRDb250ZW50KGh0bWxfYjk2YmVmYjFmNDU0NGY0YTk4NWZkNjdlMzNiMjc4MTEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjFkNTQ5OTNlZDg5NDMxYzgxZDc5NzNkMjgyOGQ4MmMuYmluZFBvcHVwKHBvcHVwXzc4ZGQwMDM3OGRkNTRmZTdiMzg2YjljYTFhYWUzYzQzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2YzYjA5NDYzZjM2NjQ0OTFiNjBmMjUyZmE1YmVlZDlhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzUzMjU4NiwtNzkuMzI5NjU2NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80NzJlZWI1NzJhNWY0ZjAyOTQ1MTczY2M5NjlmOTk0NSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zODU0N2E1NGIyMzc0MzViYjYzMTdjYWEyNTk0OTlhNSA9ICQoJzxkaXYgaWQ9Imh0bWxfMzg1NDdhNTRiMjM3NDM1YmI2MzE3Y2FhMjU5NDk5YTUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBQYXJrd29vZHMsICBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80NzJlZWI1NzJhNWY0ZjAyOTQ1MTczY2M5NjlmOTk0NS5zZXRDb250ZW50KGh0bWxfMzg1NDdhNTRiMjM3NDM1YmI2MzE3Y2FhMjU5NDk5YTUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjNiMDk0NjNmMzY2NDQ5MWI2MGYyNTJmYTViZWVkOWEuYmluZFBvcHVwKHBvcHVwXzQ3MmVlYjU3MmE1ZjRmMDI5NDUxNzNjYzk2OWY5OTQ1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRlOTMzNWQ2ZjljMDRmN2RhOWE5Mjk0NTQ3YWRjNjYxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzQ1OTA1Nzk5OTk5OTk2LC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTJmYjRkMzcxYjhjNGJmOTg1ZTc1NGQ5ZTA3NDFiYWQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMmQ3ZTg0ZDAwZTg1NGRjNWFiMDQzN2U0YzQxNjMxYWYgPSAkKCc8ZGl2IGlkPSJodG1sXzJkN2U4NGQwMGU4NTRkYzVhYjA0MzdlNGM0MTYzMWFmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gRG9uIE1pbGxzIE5vcnRoLCAgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTJmYjRkMzcxYjhjNGJmOTg1ZTc1NGQ5ZTA3NDFiYWQuc2V0Q29udGVudChodG1sXzJkN2U4NGQwMGU4NTRkYzVhYjA0MzdlNGM0MTYzMWFmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzRlOTMzNWQ2ZjljMDRmN2RhOWE5Mjk0NTQ3YWRjNjYxLmJpbmRQb3B1cChwb3B1cF8xMmZiNGQzNzFiOGM0YmY5ODVlNzU0ZDllMDc0MWJhZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80OWI4MTBlMjJiODk0N2QzOTQ5ZGI1MmUwOTczN2RmNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyNTg5OTcwMDAwMDAxLC03OS4zNDA5MjNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzJlNWFjZmNlZWM5NDkzYmExY2Y3NTVmMTIwYWY5MzUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjk5NzEzNzgzY2Q3NGY0OThjZjY4YTFmMDgzMmNmOWYgPSAkKCc8ZGl2IGlkPSJodG1sX2I5OTcxMzc4M2NkNzRmNDk4Y2Y2OGExZjA4MzJjZjlmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gRmxlbWluZ2RvbiBQYXJrLCAgRG9uIE1pbGxzIFNvdXRoLCAgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzJlNWFjZmNlZWM5NDkzYmExY2Y3NTVmMTIwYWY5MzUuc2V0Q29udGVudChodG1sX2I5OTcxMzc4M2NkNzRmNDk4Y2Y2OGExZjA4MzJjZjlmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQ5YjgxMGUyMmI4OTQ3ZDM5NDlkYjUyZTA5NzM3ZGY0LmJpbmRQb3B1cChwb3B1cF9jMmU1YWNmY2VlYzk0OTNiYTFjZjc1NWYxMjBhZjkzNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84NjY3NTE0YjkyZjY0YjE0YmYzNTdmZDQ0YWQzMzEyZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1NDMyODMsLTc5LjQ0MjI1OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTgyMmMzYzViMWQzNDI4YWE5NDc4YmUwOGFjN2Y1N2UgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjcwNThhNDkwNTFlNGQyYzgwNjJhNzJmNjc1MTM1YTIgPSAkKCc8ZGl2IGlkPSJodG1sX2I3MDU4YTQ5MDUxZTRkMmM4MDYyYTcyZjY3NTEzNWEyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gQmF0aHVyc3QgTWFub3IsICBEb3duc3ZpZXcgTm9ydGgsICBXaWxzb24gSGVpZ2h0cywgIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2U4MjJjM2M1YjFkMzQyOGFhOTQ3OGJlMDhhYzdmNTdlLnNldENvbnRlbnQoaHRtbF9iNzA1OGE0OTA1MWU0ZDJjODA2MmE3MmY2NzUxMzVhMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84NjY3NTE0YjkyZjY0YjE0YmYzNTdmZDQ0YWQzMzEyZC5iaW5kUG9wdXAocG9wdXBfZTgyMmMzYzViMWQzNDI4YWE5NDc4YmUwOGFjN2Y1N2UpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzg0NmYyMmMyZDUzNDI0OGE3MDBkNjFlNzk4Yjg5MTYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Njc5ODAzLC03OS40ODcyNjE5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82MGVlM2IxM2M5N2U0M2ExOTU2N2ZlMTcxYjJjZjI4YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hYjRiNzVmMzQxYjE0YzYyYmNjNjU4ZDViYjE3YThlNSA9ICQoJzxkaXYgaWQ9Imh0bWxfYWI0Yjc1ZjM0MWIxNGM2MmJjYzY1OGQ1YmIxN2E4ZTUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBOb3J0aHdvb2QgUGFyaywgIFlvcmsgVW5pdmVyc2l0eSwgIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzYwZWUzYjEzYzk3ZTQzYTE5NTY3ZmUxNzFiMmNmMjhiLnNldENvbnRlbnQoaHRtbF9hYjRiNzVmMzQxYjE0YzYyYmNjNjU4ZDViYjE3YThlNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83ODQ2ZjIyYzJkNTM0MjQ4YTcwMGQ2MWU3OThiODkxNi5iaW5kUG9wdXAocG9wdXBfNjBlZTNiMTNjOTdlNDNhMTk1NjdmZTE3MWIyY2YyOGIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODgwYjQwN2VkYWJlNGE3NDg3ODZiYTM4YmNlMzMxYTcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mzc0NzMyMDAwMDAwMDQsLTc5LjQ2NDc2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzM2NzlmODM5OWFhYTQzODViYjZkZTllMWE3YTg1MWQ0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzc4NTQ3ZmRhYjcwYTQ5MDBhODZmOGI4ZjEzYmNmOGY4ID0gJCgnPGRpdiBpZD0iaHRtbF83ODU0N2ZkYWI3MGE0OTAwYTg2ZjhiOGYxM2JjZjhmOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IENGQiBUb3JvbnRvLCAgRG93bnN2aWV3IEVhc3QsICBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zNjc5ZjgzOTlhYWE0Mzg1YmI2ZGU5ZTFhN2E4NTFkNC5zZXRDb250ZW50KGh0bWxfNzg1NDdmZGFiNzBhNDkwMGE4NmY4YjhmMTNiY2Y4ZjgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODgwYjQwN2VkYWJlNGE3NDg3ODZiYTM4YmNlMzMxYTcuYmluZFBvcHVwKHBvcHVwXzM2NzlmODM5OWFhYTQzODViYjZkZTllMWE3YTg1MWQ0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU1ZDlkMmJhYmQ0MTQyOGE4Y2Y5NTZmODc5NzQ3M2RmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzM5MDE0NiwtNzkuNTA2OTQzNl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yM2Q4ODg1NmMyNmQ0NDIyOWNhMTQzMDYzNmE0NmYwMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wNjhmMjU5YjczOWQ0ODkxYjZhZjMyMTBlYzFhMmRiOCA9ICQoJzxkaXYgaWQ9Imh0bWxfMDY4ZjI1OWI3MzlkNDg5MWI2YWYzMjEwZWMxYTJkYjgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBEb3duc3ZpZXcgV2VzdCwgIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzIzZDg4ODU2YzI2ZDQ0MjI5Y2ExNDMwNjM2YTQ2ZjAyLnNldENvbnRlbnQoaHRtbF8wNjhmMjU5YjczOWQ0ODkxYjZhZjMyMTBlYzFhMmRiOCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81NWQ5ZDJiYWJkNDE0MjhhOGNmOTU2Zjg3OTc0NzNkZi5iaW5kUG9wdXAocG9wdXBfMjNkODg4NTZjMjZkNDQyMjljYTE0MzA2MzZhNDZmMDIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYjZjM2I5NzJiODI5NDY4Mjg0MzNhZDg2MTg4Njk3MmYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mjg0OTY0LC03OS40OTU2OTc0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wMTgyYzkzMjc5MmQ0YmQ4YTAzMDczYjhhMDg3YzE5YyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hNzAxMjRjYmNlMDU0MGFmYjA0ZTk1NjYyMjI4MmRhMiA9ICQoJzxkaXYgaWQ9Imh0bWxfYTcwMTI0Y2JjZTA1NDBhZmIwNGU5NTY2MjIyODJkYTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBEb3duc3ZpZXcgQ2VudHJhbCwgIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzAxODJjOTMyNzkyZDRiZDhhMDMwNzNiOGEwODdjMTljLnNldENvbnRlbnQoaHRtbF9hNzAxMjRjYmNlMDU0MGFmYjA0ZTk1NjYyMjI4MmRhMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iNmMzYjk3MmI4Mjk0NjgyODQzM2FkODYxODg2OTcyZi5iaW5kUG9wdXAocG9wdXBfMDE4MmM5MzI3OTJkNGJkOGEwMzA3M2I4YTA4N2MxOWMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzRiODAzMzc1MGYzNDJlN2EwNWU2NGU3M2RjYTYzYjggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43NjE2MzEzLC03OS41MjA5OTk0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81OTYzNWFkZDRhY2Q0Mjk3YjZiYjgxYTEzMDBhZDBiYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jZjQxY2ZlZmZmMWY0YTcyYTQxYzZkMjc3ZThlNjdmMiA9ICQoJzxkaXYgaWQ9Imh0bWxfY2Y0MWNmZWZmZjFmNGE3MmE0MWM2ZDI3N2U4ZTY3ZjIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBEb3duc3ZpZXcgTm9ydGh3ZXN0LCAgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTk2MzVhZGQ0YWNkNDI5N2I2YmI4MWExMzAwYWQwYmIuc2V0Q29udGVudChodG1sX2NmNDFjZmVmZmYxZjRhNzJhNDFjNmQyNzdlOGU2N2YyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc0YjgwMzM3NTBmMzQyZTdhMDVlNjRlNzNkY2E2M2I4LmJpbmRQb3B1cChwb3B1cF81OTYzNWFkZDRhY2Q0Mjk3YjZiYjgxYTEzMDBhZDBiYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84YzI0NmE1ZjIyMTE0YTM1YmFhZTliZWVjNmU4ZjdjNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyNTg4MjI5OTk5OTk5NSwtNzkuMzE1NTcxNTk5OTk5OThdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjY5ZWZiYWFiNGQ1NDg5OGJiMzFiMDJmMDg0MmFjMjggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWQxYzIxNTJlYzRjNDJhZmFiN2JjOWIxNjZhYTY1YjUgPSAkKCc8ZGl2IGlkPSJodG1sXzFkMWMyMTUyZWM0YzQyYWZhYjdiYzliMTY2YWE2NWI1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gVmljdG9yaWEgVmlsbGFnZSwgIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzI2OWVmYmFhYjRkNTQ4OThiYjMxYjAyZjA4NDJhYzI4LnNldENvbnRlbnQoaHRtbF8xZDFjMjE1MmVjNGM0MmFmYWI3YmM5YjE2NmFhNjViNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84YzI0NmE1ZjIyMTE0YTM1YmFhZTliZWVjNmU4ZjdjNi5iaW5kUG9wdXAocG9wdXBfMjY5ZWZiYWFiNGQ1NDg5OGJiMzFiMDJmMDg0MmFjMjgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTIwMzI3YzhiZjVmNDMzNDg2NTk3NjY0YTgzNzkyN2YgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDYzOTcyLC03OS4zMDk5MzddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODBmZjJlNzRmYjNlNDAzNTk1OGY4N2RkYWVhY2Y2MTIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjM3NWNlMjhkYWJiNDFiN2E3M2E4NGRjMzgzZmUxNTggPSAkKCc8ZGl2IGlkPSJodG1sXzIzNzVjZTI4ZGFiYjQxYjdhNzNhODRkYzM4M2ZlMTU4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gV29vZGJpbmUgR2FyZGVucywgIFBhcmt2aWV3IEhpbGwsICBFYXN0IFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgwZmYyZTc0ZmIzZTQwMzU5NThmODdkZGFlYWNmNjEyLnNldENvbnRlbnQoaHRtbF8yMzc1Y2UyOGRhYmI0MWI3YTczYTg0ZGMzODNmZTE1OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85MjAzMjdjOGJmNWY0MzM0ODY1OTc2NjRhODM3OTI3Zi5iaW5kUG9wdXAocG9wdXBfODBmZjJlNzRmYjNlNDAzNTk1OGY4N2RkYWVhY2Y2MTIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZGEzYjU3NjljMjMyNDQwMmJmZGZlNjVkNjkwMzA4N2EgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTUzNDM5MDAwMDAwMDUsLTc5LjMxODM4ODddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzQzZDBlODJhNWQ5NDViYjkwNDM3OGExYTY0ZDUyOWIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzEzMzI0NTgwZjIzNDE4ZWE4MjNhYTE0YmIwYzJlYmMgPSAkKCc8ZGl2IGlkPSJodG1sXzcxMzMyNDU4MGYyMzQxOGVhODIzYWExNGJiMGMyZWJjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gV29vZGJpbmUgSGVpZ2h0cywgIEVhc3QgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzQzZDBlODJhNWQ5NDViYjkwNDM3OGExYTY0ZDUyOWIuc2V0Q29udGVudChodG1sXzcxMzMyNDU4MGYyMzQxOGVhODIzYWExNGJiMGMyZWJjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RhM2I1NzY5YzIzMjQ0MDJiZmRmZTY1ZDY5MDMwODdhLmJpbmRQb3B1cChwb3B1cF9jNDNkMGU4MmE1ZDk0NWJiOTA0Mzc4YTFhNjRkNTI5Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iOGU1YjExNDBlZDI0YzY3OWI5M2VlNjM4ZjIyYTkyYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3NjM1NzM5OTk5OTk5LC03OS4yOTMwMzEyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzhhMDZlZjM0ZDQ2NDQxODRiMGNjNTlhZDhhMTk2YTBhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzAwMjVlMTJlNWE1NDQ2MWFhODQxZWVlM2I4MDg1ZTc5ID0gJCgnPGRpdiBpZD0iaHRtbF8wMDI1ZTEyZTVhNTQ0NjFhYTg0MWVlZTNiODA4NWU3OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFRoZSBCZWFjaGVzLCAgRWFzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84YTA2ZWYzNGQ0NjQ0MTg0YjBjYzU5YWQ4YTE5NmEwYS5zZXRDb250ZW50KGh0bWxfMDAyNWUxMmU1YTU0NDYxYWE4NDFlZWUzYjgwODVlNzkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYjhlNWIxMTQwZWQyNGM2NzliOTNlZTYzOGYyMmE5MmMuYmluZFBvcHVwKHBvcHVwXzhhMDZlZjM0ZDQ2NDQxODRiMGNjNTlhZDhhMTk2YTBhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzI5OTNiNzcwYTMxNDQ4ZmU4NThiYzYzODNiMDg4NzUyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzA5MDYwNCwtNzkuMzYzNDUxN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kZGI4YWY2ZTgzZWM0MzVhYmU2OTk0Y2M4NWI3YzhiYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81ODM4YTQ4YmU5MTc0MTA2OTIwNjRmOWE3OTZmYzdlMiA9ICQoJzxkaXYgaWQ9Imh0bWxfNTgzOGE0OGJlOTE3NDEwNjkyMDY0ZjlhNzk2ZmM3ZTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBMZWFzaWRlLCAgRWFzdCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kZGI4YWY2ZTgzZWM0MzVhYmU2OTk0Y2M4NWI3YzhiYS5zZXRDb250ZW50KGh0bWxfNTgzOGE0OGJlOTE3NDEwNjkyMDY0ZjlhNzk2ZmM3ZTIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjk5M2I3NzBhMzE0NDhmZTg1OGJjNjM4M2IwODg3NTIuYmluZFBvcHVwKHBvcHVwX2RkYjhhZjZlODNlYzQzNWFiZTY5OTRjYzg1YjdjOGJhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I3ZGRlMTBkMzE3YzQyNzJhMWY2Y2M0MzVmNzdjM2I2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzA1MzY4OSwtNzkuMzQ5MzcxOTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjM0OGM2NDNhMmY1NGQwYjg0MTg0NTNlYjFmZmRjMmYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZWM2MTBjMDU1ZjYwNDI4MWE4ZDcwYzEwZjdkNzBhNzkgPSAkKCc8ZGl2IGlkPSJodG1sX2VjNjEwYzA1NWY2MDQyODFhOGQ3MGMxMGY3ZDcwYTc5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gVGhvcm5jbGlmZmUgUGFyaywgIEVhc3QgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjM0OGM2NDNhMmY1NGQwYjg0MTg0NTNlYjFmZmRjMmYuc2V0Q29udGVudChodG1sX2VjNjEwYzA1NWY2MDQyODFhOGQ3MGMxMGY3ZDcwYTc5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2I3ZGRlMTBkMzE3YzQyNzJhMWY2Y2M0MzVmNzdjM2I2LmJpbmRQb3B1cChwb3B1cF8yMzQ4YzY0M2EyZjU0ZDBiODQxODQ1M2ViMWZmZGMyZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hOTY1YTdmYjk4ZmE0YWM4OThiNWM4NzVhYWJkYjA4MSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY4NTM0NywtNzkuMzM4MTA2NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yNzdjZDc0OWYyYzY0M2QyOWUyNGYwNTFiZmVkY2VjOCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mMzZmMjAyYzMwY2M0NjA4YTMwNmNiYWQ4MzBjMmUwYSA9ICQoJzxkaXYgaWQ9Imh0bWxfZjM2ZjIwMmMzMGNjNDYwOGEzMDZjYmFkODMwYzJlMGEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBFYXN0IFRvcm9udG8sICBFYXN0IFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzI3N2NkNzQ5ZjJjNjQzZDI5ZTI0ZjA1MWJmZWRjZWM4LnNldENvbnRlbnQoaHRtbF9mMzZmMjAyYzMwY2M0NjA4YTMwNmNiYWQ4MzBjMmUwYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hOTY1YTdmYjk4ZmE0YWM4OThiNWM4NzVhYWJkYjA4MS5iaW5kUG9wdXAocG9wdXBfMjc3Y2Q3NDlmMmM2NDNkMjllMjRmMDUxYmZlZGNlYzgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfY2M0YTUwN2Y4ZDgyNDg2MWIyN2VmM2E3ODkxN2YwY2YgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Nzk1NTcxLC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzFiOWIyOTExMmVjNDFhYWE5NzgwOGIzNDhhMzc5OWIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTFhMjZlYTVhM2UyNDJjMmJiNzQ1MDhlMzdmMmI0ZjQgPSAkKCc8ZGl2IGlkPSJodG1sXzExYTI2ZWE1YTNlMjQyYzJiYjc0NTA4ZTM3ZjJiNGY0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gVGhlIERhbmZvcnRoIFdlc3QsICBSaXZlcmRhbGUsICBFYXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzcxYjliMjkxMTJlYzQxYWFhOTc4MDhiMzQ4YTM3OTliLnNldENvbnRlbnQoaHRtbF8xMWEyNmVhNWEzZTI0MmMyYmI3NDUwOGUzN2YyYjRmNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jYzRhNTA3ZjhkODI0ODYxYjI3ZWYzYTc4OTE3ZjBjZi5iaW5kUG9wdXAocG9wdXBfNzFiOWIyOTExMmVjNDFhYWE5NzgwOGIzNDhhMzc5OWIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMGE5ZmIzYmU2YTJhNDhhY2FiM2EzYjcwODNkMmIxMWMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njg5OTg1LC03OS4zMTU1NzE1OTk5OTk5OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wNTE2YjVkNjg3YTk0YWYyYWMxNGRjM2M3MzBhOTNjMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xZTJhZWEzZDY2M2I0NzJhYjUxYzY0MGUyYjZjODhjZCA9ICQoJzxkaXYgaWQ9Imh0bWxfMWUyYWVhM2Q2NjNiNDcyYWI1MWM2NDBlMmI2Yzg4Y2QiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBUaGUgQmVhY2hlcyBXZXN0LCAgSW5kaWEgQmF6YWFyLCAgRWFzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wNTE2YjVkNjg3YTk0YWYyYWMxNGRjM2M3MzBhOTNjMC5zZXRDb250ZW50KGh0bWxfMWUyYWVhM2Q2NjNiNDcyYWI1MWM2NDBlMmI2Yzg4Y2QpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMGE5ZmIzYmU2YTJhNDhhY2FiM2EzYjcwODNkMmIxMWMuYmluZFBvcHVwKHBvcHVwXzA1MTZiNWQ2ODdhOTRhZjJhYzE0ZGMzYzczMGE5M2MwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzEwMmZkZGFjMGI3MzQ4MGNiMzQ1ZTM4OGE4OWE1ZWE5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU5NTI1NSwtNzkuMzQwOTIzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Q0NTcxMGYyMzI2YzQyN2U4NzkzNTRhMTVhYmZjYWEwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzY3N2QzMzRmODJhMzRiYzQ4NjI5MzVhNzQwMjc2ODlmID0gJCgnPGRpdiBpZD0iaHRtbF82NzdkMzM0ZjgyYTM0YmM0ODYyOTM1YTc0MDI3Njg5ZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFN0dWRpbyBEaXN0cmljdCwgIEVhc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDQ1NzEwZjIzMjZjNDI3ZTg3OTM1NGExNWFiZmNhYTAuc2V0Q29udGVudChodG1sXzY3N2QzMzRmODJhMzRiYzQ4NjI5MzVhNzQwMjc2ODlmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzEwMmZkZGFjMGI3MzQ4MGNiMzQ1ZTM4OGE4OWE1ZWE5LmJpbmRQb3B1cChwb3B1cF9kNDU3MTBmMjMyNmM0MjdlODc5MzU0YTE1YWJmY2FhMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wOTc1ZjhiOTFhNWQ0NWU3YWNjNTA4MTE4MTg2ODg2NCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyODAyMDUsLTc5LjM4ODc5MDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNTdhMTZkZGRmNmU5NDIyN2FhODc2ZjVhM2FkOWI3Y2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMDY1M2RlMzQzYjhkNDM3ZWI5N2E4ODk3MTYyMjRlNGUgPSAkKCc8ZGl2IGlkPSJodG1sXzA2NTNkZTM0M2I4ZDQzN2ViOTdhODg5NzE2MjI0ZTRlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gTGF3cmVuY2UgUGFyaywgIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTdhMTZkZGRmNmU5NDIyN2FhODc2ZjVhM2FkOWI3Y2Muc2V0Q29udGVudChodG1sXzA2NTNkZTM0M2I4ZDQzN2ViOTdhODg5NzE2MjI0ZTRlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA5NzVmOGI5MWE1ZDQ1ZTdhY2M1MDgxMTgxODY4ODY0LmJpbmRQb3B1cChwb3B1cF81N2ExNmRkZGY2ZTk0MjI3YWE4NzZmNWEzYWQ5YjdjYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80MmQ3NjE2MmM4Yzc0MmQxYTczNjY5ZjdlODYxMWE1YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxMjc1MTEsLTc5LjM5MDE5NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNGM3OGJlYjQ1NGJkNDA3YmJiZTAwMTA4N2U4OGFkYTQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTUwNjRiOWUzMmZkNDg0MmI1MjY5YjJiYWVmY2EyZjkgPSAkKCc8ZGl2IGlkPSJodG1sXzU1MDY0YjllMzJmZDQ4NDJiNTI2OWIyYmFlZmNhMmY5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gRGF2aXN2aWxsZSBOb3J0aCwgIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNGM3OGJlYjQ1NGJkNDA3YmJiZTAwMTA4N2U4OGFkYTQuc2V0Q29udGVudChodG1sXzU1MDY0YjllMzJmZDQ4NDJiNTI2OWIyYmFlZmNhMmY5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQyZDc2MTYyYzhjNzQyZDFhNzM2NjlmN2U4NjExYTVjLmJpbmRQb3B1cChwb3B1cF80Yzc4YmViNDU0YmQ0MDdiYmJlMDAxMDg3ZTg4YWRhNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84MjQxNDUxZTY4ODE0NGUzYWU0YTY5MTZlMTczYmMxYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxNTM4MzQsLTc5LjQwNTY3ODQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI1NGMwYjYyNTgyZTQzMmNhYzk2ZmU0NTI2MGEzOWJiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Q3YTk3NGFlNzAwOTQ1YzJiMjNmY2Y1ZmNiN2E2Mzc0ID0gJCgnPGRpdiBpZD0iaHRtbF9kN2E5NzRhZTcwMDk0NWMyYjIzZmNmNWZjYjdhNjM3NCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IE5vcnRoIFRvcm9udG8gV2VzdCwgIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjU0YzBiNjI1ODJlNDMyY2FjOTZmZTQ1MjYwYTM5YmIuc2V0Q29udGVudChodG1sX2Q3YTk3NGFlNzAwOTQ1YzJiMjNmY2Y1ZmNiN2E2Mzc0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzgyNDE0NTFlNjg4MTQ0ZTNhZTRhNjkxNmUxNzNiYzFiLmJpbmRQb3B1cChwb3B1cF8yNTRjMGI2MjU4MmU0MzJjYWM5NmZlNDUyNjBhMzliYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iYjdhMGMzZDllZTU0YzJmYWJjOThmYTZmNmVjNGY2ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcwNDMyNDQsLTc5LjM4ODc5MDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmY4ZDFkMjUzYzUxNGRjM2IwMDUzOWY0YzgwZWFkY2IgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTU1YmI2ZWJkYTRlNGJkY2I5NjA3ZDBiMzIyYjU5NTQgPSAkKCc8ZGl2IGlkPSJodG1sXzU1NWJiNmViZGE0ZTRiZGNiOTYwN2QwYjMyMmI1OTU0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gRGF2aXN2aWxsZSwgIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmY4ZDFkMjUzYzUxNGRjM2IwMDUzOWY0YzgwZWFkY2Iuc2V0Q29udGVudChodG1sXzU1NWJiNmViZGE0ZTRiZGNiOTYwN2QwYjMyMmI1OTU0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JiN2EwYzNkOWVlNTRjMmZhYmM5OGZhNmY2ZWM0ZjZlLmJpbmRQb3B1cChwb3B1cF9iZjhkMWQyNTNjNTE0ZGMzYjAwNTM5ZjRjODBlYWRjYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kYmE4ZGZmZWU3YTc0MzM4YjNiMjFhNDU4YjU3NTkyNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY4OTU3NDMsLTc5LjM4MzE1OTkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2U3OTkzNTU4NzgzOTQ0ZTg5NDI4NWQ1NzE1ODczNDExID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzEyYTdlNzkwMDYxMjQ2MGFhNzJhM2ViYzlmMThkZGQ0ID0gJCgnPGRpdiBpZD0iaHRtbF8xMmE3ZTc5MDA2MTI0NjBhYTcyYTNlYmM5ZjE4ZGRkNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IE1vb3JlIFBhcmssICBTdW1tZXJoaWxsIEVhc3QsICBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2U3OTkzNTU4NzgzOTQ0ZTg5NDI4NWQ1NzE1ODczNDExLnNldENvbnRlbnQoaHRtbF8xMmE3ZTc5MDA2MTI0NjBhYTcyYTNlYmM5ZjE4ZGRkNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kYmE4ZGZmZWU3YTc0MzM4YjNiMjFhNDU4YjU3NTkyNC5iaW5kUG9wdXAocG9wdXBfZTc5OTM1NTg3ODM5NDRlODk0Mjg1ZDU3MTU4NzM0MTEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMGRiMmRiNTcyMWU5NGU3OWJlZDgzMjA0NTA1N2VmN2UgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODY0MTIyOTk5OTk5OSwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81NjM1NzUxZDk1MmU0YTUzOWIyZDljYjVhN2JkYTQ3NyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xNGIxN2U2YjkyZmE0ODMyYmE4ZDBhYTk3Y2FkMjZlMiA9ICQoJzxkaXYgaWQ9Imh0bWxfMTRiMTdlNmI5MmZhNDgzMmJhOGQwYWE5N2NhZDI2ZTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBEZWVyIFBhcmssICBGb3Jlc3QgSGlsbCBTRSwgIFJhdGhuZWxseSwgIFNvdXRoIEhpbGwsICBTdW1tZXJoaWxsIFdlc3QsICBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzU2MzU3NTFkOTUyZTRhNTM5YjJkOWNiNWE3YmRhNDc3LnNldENvbnRlbnQoaHRtbF8xNGIxN2U2YjkyZmE0ODMyYmE4ZDBhYTk3Y2FkMjZlMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wZGIyZGI1NzIxZTk0ZTc5YmVkODMyMDQ1MDU3ZWY3ZS5iaW5kUG9wdXAocG9wdXBfNTYzNTc1MWQ5NTJlNGE1MzliMmQ5Y2I1YTdiZGE0NzcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNWQ4ZDdlNjk4NDA0NDAxZjk1YmQwM2JhZmY3MDEzOWYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Nzk1NjI2LC03OS4zNzc1Mjk0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85NTc3ZDQwMmNkYWQ0Y2E1YjIxNDc3M2FkM2U3NWNhZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mNjQ3NDFkYWI3Yjk0ODVjYTYwNzAzMjNhZjE1YmU1NSA9ICQoJzxkaXYgaWQ9Imh0bWxfZjY0NzQxZGFiN2I5NDg1Y2E2MDcwMzIzYWYxNWJlNTUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBSb3NlZGFsZSwgIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk1NzdkNDAyY2RhZDRjYTViMjE0NzczYWQzZTc1Y2FkLnNldENvbnRlbnQoaHRtbF9mNjQ3NDFkYWI3Yjk0ODVjYTYwNzAzMjNhZjE1YmU1NSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81ZDhkN2U2OTg0MDQ0MDFmOTViZDAzYmFmZjcwMTM5Zi5iaW5kUG9wdXAocG9wdXBfOTU3N2Q0MDJjZGFkNGNhNWIyMTQ3NzNhZDNlNzVjYWQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTBmMTMxYjYyZTE5NGRlZGI3ZjU3NDA1ZTI1YzYwNzYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njc5NjcsLTc5LjM2NzY3NTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfY2E1NjQ0MTA4YzkzNDA2OGI4MTZiZGFhMjYxMDVlZDYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmRhYWNjYmVlZGNiNDgzYTlmZGQxZGEyYTg2YTAzZjkgPSAkKCc8ZGl2IGlkPSJodG1sX2ZkYWFjY2JlZWRjYjQ4M2E5ZmRkMWRhMmE4NmEwM2Y5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gQ2FiYmFnZXRvd24sICBTdC4gSmFtZXMgVG93biwgIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2NhNTY0NDEwOGM5MzQwNjhiODE2YmRhYTI2MTA1ZWQ2LnNldENvbnRlbnQoaHRtbF9mZGFhY2NiZWVkY2I0ODNhOWZkZDFkYTJhODZhMDNmOSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hMGYxMzFiNjJlMTk0ZGVkYjdmNTc0MDVlMjVjNjA3Ni5iaW5kUG9wdXAocG9wdXBfY2E1NjQ0MTA4YzkzNDA2OGI4MTZiZGFhMjYxMDVlZDYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZGYxZjMxOTg4YzExNDc4N2I5ZWZjYWJmZDliNGJlNjQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjU4NTk5LC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mMDEwYmUwZmI1MWI0ZDBlOWQzZDY5OWZiOWZlOWRiMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80MDFkZTg2MTQyOWI0NWRiOWJhOGY2YTBmOGUwZjcwNSA9ICQoJzxkaXYgaWQ9Imh0bWxfNDAxZGU4NjE0MjliNDVkYjliYThmNmEwZjhlMGY3MDUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBDaHVyY2ggYW5kIFdlbGxlc2xleSwgIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2YwMTBiZTBmYjUxYjRkMGU5ZDNkNjk5ZmI5ZmU5ZGIwLnNldENvbnRlbnQoaHRtbF80MDFkZTg2MTQyOWI0NWRiOWJhOGY2YTBmOGUwZjcwNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kZjFmMzE5ODhjMTE0Nzg3YjllZmNhYmZkOWI0YmU2NC5iaW5kUG9wdXAocG9wdXBfZjAxMGJlMGZiNTFiNGQwZTlkM2Q2OTlmYjlmZTlkYjApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmIwMDVhOTgyODcwNGRiMmFhNDU1Mzk4YjkwNTZhMTYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTQyNTk5LC03OS4zNjA2MzU5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzU2Yjk0MTBjYzNkZDQ2MjdiMjU1NDQ2N2QxZmFhMDFhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzAyMjU0MGEzMjg0ZjQzYjQ5N2M5NWIyYWFjMjhjMjEwID0gJCgnPGRpdiBpZD0iaHRtbF8wMjI1NDBhMzI4NGY0M2I0OTdjOTViMmFhYzI4YzIxMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IEhhcmJvdXJmcm9udCwgIFJlZ2VudCBQYXJrLCAgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTZiOTQxMGNjM2RkNDYyN2IyNTU0NDY3ZDFmYWEwMWEuc2V0Q29udGVudChodG1sXzAyMjU0MGEzMjg0ZjQzYjQ5N2M5NWIyYWFjMjhjMjEwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2ZiMDA1YTk4Mjg3MDRkYjJhYTQ1NTM5OGI5MDU2YTE2LmJpbmRQb3B1cChwb3B1cF81NmI5NDEwY2MzZGQ0NjI3YjI1NTQ0NjdkMWZhYTAxYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wNmMxYzJmY2E5NmE0NjE4ODRhYmI0NWY1NTU3ZWY5OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1NzE2MTgsLTc5LjM3ODkzNzA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzIwZmZiMGM4MmNhNjQ2MTQ5MzQ1YTFmNDdkYmRjNmY0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2U0MDQxZTFmYmI2NzRkNDM4YTc0ZTc0YTRhMmM4ODU5ID0gJCgnPGRpdiBpZD0iaHRtbF9lNDA0MWUxZmJiNjc0ZDQzOGE3NGU3NGE0YTJjODg1OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFJ5ZXJzb24sICBHYXJkZW4gRGlzdHJpY3QsICBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yMGZmYjBjODJjYTY0NjE0OTM0NWExZjQ3ZGJkYzZmNC5zZXRDb250ZW50KGh0bWxfZTQwNDFlMWZiYjY3NGQ0MzhhNzRlNzRhNGEyYzg4NTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDZjMWMyZmNhOTZhNDYxODg0YWJiNDVmNTU1N2VmOTkuYmluZFBvcHVwKHBvcHVwXzIwZmZiMGM4MmNhNjQ2MTQ5MzQ1YTFmNDdkYmRjNmY0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M3OGY5NTVkYTg1MjQ1OWE4OWJjODg3NDA2ODIxNTAwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUxNDkzOSwtNzkuMzc1NDE3OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80ODU3NjBjNDY3NDE0N2IxYTdkOGQ2OTNiMzA2MGNjYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83ZmUxNGRlN2EwZmU0YzQzOTFmZmJmZDIzZmIzOWFlMyA9ICQoJzxkaXYgaWQ9Imh0bWxfN2ZlMTRkZTdhMGZlNGM0MzkxZmZiZmQyM2ZiMzlhZTMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBTdC4gSmFtZXMgVG93biwgIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQ4NTc2MGM0Njc0MTQ3YjFhN2Q4ZDY5M2IzMDYwY2NhLnNldENvbnRlbnQoaHRtbF83ZmUxNGRlN2EwZmU0YzQzOTFmZmJmZDIzZmIzOWFlMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jNzhmOTU1ZGE4NTI0NTlhODliYzg4NzQwNjgyMTUwMC5iaW5kUG9wdXAocG9wdXBfNDg1NzYwYzQ2NzQxNDdiMWE3ZDhkNjkzYjMwNjBjY2EpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDA0MDExOGI1ZmYzNDJmMjg3NTFlODQ3ZDY0OWE4YWIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDQ3NzA3OTk5OTk5OTYsLTc5LjM3MzMwNjRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNGM0YmU3MzU2MWIwNDkyNzhmMjkwYWE2ODEzMmQ5YTUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfN2VhZTRlNTVmOGNmNDRkMDk0Njc3MjUxNzViMmFjMmQgPSAkKCc8ZGl2IGlkPSJodG1sXzdlYWU0ZTU1ZjhjZjQ0ZDA5NDY3NzI1MTc1YjJhYzJkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gQmVyY3p5IFBhcmssICBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80YzRiZTczNTYxYjA0OTI3OGYyOTBhYTY4MTMyZDlhNS5zZXRDb250ZW50KGh0bWxfN2VhZTRlNTVmOGNmNDRkMDk0Njc3MjUxNzViMmFjMmQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZDA0MDExOGI1ZmYzNDJmMjg3NTFlODQ3ZDY0OWE4YWIuYmluZFBvcHVwKHBvcHVwXzRjNGJlNzM1NjFiMDQ5Mjc4ZjI5MGFhNjgxMzJkOWE1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzdhN2U1NTY2NzM5ZDRlYWNhYTRjNDY2NWE5MmE1ZmZiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU3OTUyNCwtNzkuMzg3MzgyNl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kY2NjNTQ1NzFkNTA0NWUzYjI1ZTIzNmVkMWRkMzE3NCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80YmE5OGM1YWZkYWE0Y2M2YmI5NDEyNDFkNDRmMGZhMSA9ICQoJzxkaXYgaWQ9Imh0bWxfNGJhOThjNWFmZGFhNGNjNmJiOTQxMjQxZDQ0ZjBmYTEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBDZW50cmFsIEJheSBTdHJlZXQsICBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kY2NjNTQ1NzFkNTA0NWUzYjI1ZTIzNmVkMWRkMzE3NC5zZXRDb250ZW50KGh0bWxfNGJhOThjNWFmZGFhNGNjNmJiOTQxMjQxZDQ0ZjBmYTEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfN2E3ZTU1NjY3MzlkNGVhY2FhNGM0NjY1YTkyYTVmZmIuYmluZFBvcHVwKHBvcHVwX2RjY2M1NDU3MWQ1MDQ1ZTNiMjVlMjM2ZWQxZGQzMTc0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzlhODNiODk2MGU1NDQ3MDNhMmFkYzU4YmRkNWVlZWE2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUwNTcxMjAwMDAwMDEsLTc5LjM4NDU2NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOThhODljNjM2YjA4NDZiZTliZDQyZjYwZDdiZTc4YWUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODU2YzZiMzQ5YmFkNDE5Mjg0OTQ4YTIwYzEwY2Y1YWUgPSAkKCc8ZGl2IGlkPSJodG1sXzg1NmM2YjM0OWJhZDQxOTI4NDk0OGEyMGMxMGNmNWFlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gQWRlbGFpZGUsICBLaW5nLCAgUmljaG1vbmQsICBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85OGE4OWM2MzZiMDg0NmJlOWJkNDJmNjBkN2JlNzhhZS5zZXRDb250ZW50KGh0bWxfODU2YzZiMzQ5YmFkNDE5Mjg0OTQ4YTIwYzEwY2Y1YWUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOWE4M2I4OTYwZTU0NDcwM2EyYWRjNThiZGQ1ZWVlYTYuYmluZFBvcHVwKHBvcHVwXzk4YTg5YzYzNmIwODQ2YmU5YmQ0MmY2MGQ3YmU3OGFlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU5MzVmOTI2MzU0MTQ1MWM4OTZlNGZiNjYxNjdmMTU2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQwODE1NywtNzkuMzgxNzUyMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDM5MTVjYTA0NDhiNDMyM2JhYTU2YTViYjQ3NTQyNzUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzVlNTc3NGQ4M2M3NDljZjkzOTVmN2YzZWNmZjZhNmMgPSAkKCc8ZGl2IGlkPSJodG1sX2M1ZTU3NzRkODNjNzQ5Y2Y5Mzk1ZjdmM2VjZmY2YTZjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gSGFyYm91cmZyb250IEVhc3QsICBUb3JvbnRvIElzbGFuZHMsICBVbmlvbiBTdGF0aW9uLCAgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDM5MTVjYTA0NDhiNDMyM2JhYTU2YTViYjQ3NTQyNzUuc2V0Q29udGVudChodG1sX2M1ZTU3NzRkODNjNzQ5Y2Y5Mzk1ZjdmM2VjZmY2YTZjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzU5MzVmOTI2MzU0MTQ1MWM4OTZlNGZiNjYxNjdmMTU2LmJpbmRQb3B1cChwb3B1cF8wMzkxNWNhMDQ0OGI0MzIzYmFhNTZhNWJiNDc1NDI3NSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81N2QxN2UzZjMxODg0NzJhOGUyYWEwNmQzZGY5MjkzOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NzE3NjgsLTc5LjM4MTU3NjQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzU2ZWNlZTI1NmQyMTRmNTZiNGI2ZmEyMzNiODIzYWY5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhjYmIxYmY3ZjcyMzQ1YjhhZmJmNjQyYzdiOGQ4M2FmID0gJCgnPGRpdiBpZD0iaHRtbF84Y2JiMWJmN2Y3MjM0NWI4YWZiZjY0MmM3YjhkODNhZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IERlc2lnbiBFeGNoYW5nZSwgIFRvcm9udG8gRG9taW5pb24gQ2VudHJlLCAgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTZlY2VlMjU2ZDIxNGY1NmI0YjZmYTIzM2I4MjNhZjkuc2V0Q29udGVudChodG1sXzhjYmIxYmY3ZjcyMzQ1YjhhZmJmNjQyYzdiOGQ4M2FmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzU3ZDE3ZTNmMzE4ODQ3MmE4ZTJhYTA2ZDNkZjkyOTM4LmJpbmRQb3B1cChwb3B1cF81NmVjZWUyNTZkMjE0ZjU2YjRiNmZhMjMzYjgyM2FmOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83NWM4ZjI2YmZlOTE0YzBmYTQ4YjFlMzVhNmQxZjdhZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODE5ODUsLTc5LjM3OTgxNjkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzNmNzJjYmZjNWM4MTQ5NTY4NjRkNWFjMDM1NmFlNjA1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzczNTc4ZDVkOTAzYzRmMTU5OGUzODczN2UxNjdjYWM2ID0gJCgnPGRpdiBpZD0iaHRtbF83MzU3OGQ1ZDkwM2M0ZjE1OThlMzg3MzdlMTY3Y2FjNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IENvbW1lcmNlIENvdXJ0LCAgVmljdG9yaWEgSG90ZWwsICBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zZjcyY2JmYzVjODE0OTU2ODY0ZDVhYzAzNTZhZTYwNS5zZXRDb250ZW50KGh0bWxfNzM1NzhkNWQ5MDNjNGYxNTk4ZTM4NzM3ZTE2N2NhYzYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzVjOGYyNmJmZTkxNGMwZmE0OGIxZTM1YTZkMWY3YWQuYmluZFBvcHVwKHBvcHVwXzNmNzJjYmZjNWM4MTQ5NTY4NjRkNWFjMDM1NmFlNjA1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzY1NWExYWQ5MzdjZDQ2Y2ZiNjYxYzFlZjY4MWI1ZDBiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzMzMjgyNSwtNzkuNDE5NzQ5N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80NjcwYmZlZWZmNTA0ZmQ2OGQxZDU3YWEwOGU2ZWU5OCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82ZDViMDExYjRlMTc0NThiODkzYTZmNTc2OTQ3NzMyZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNmQ1YjAxMWI0ZTE3NDU4Yjg5M2E2ZjU3Njk0NzczMmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBCZWRmb3JkIFBhcmssICBMYXdyZW5jZSBNYW5vciBFYXN0LCAgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNDY3MGJmZWVmZjUwNGZkNjhkMWQ1N2FhMDhlNmVlOTguc2V0Q29udGVudChodG1sXzZkNWIwMTFiNGUxNzQ1OGI4OTNhNmY1NzY5NDc3MzJlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY1NWExYWQ5MzdjZDQ2Y2ZiNjYxYzFlZjY4MWI1ZDBiLmJpbmRQb3B1cChwb3B1cF80NjcwYmZlZWZmNTA0ZmQ2OGQxZDU3YWEwOGU2ZWU5OCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xMDM4M2QxYzY3YTA0YTg3OWFhMGE1ODljNWE2ZGUxMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxMTY5NDgsLTc5LjQxNjkzNTU5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2ZlMGU3MzVkODdhMTQ2ZWQ5NDc0NzZjOTExMDFjMGNmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzVjY2JlMGY1ZTJiMDQ0MDJhNDEwM2NlOTU5Zjc0ZGZkID0gJCgnPGRpdiBpZD0iaHRtbF81Y2NiZTBmNWUyYjA0NDAyYTQxMDNjZTk1OWY3NGRmZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFJvc2VsYXduLCAgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mZTBlNzM1ZDg3YTE0NmVkOTQ3NDc2YzkxMTAxYzBjZi5zZXRDb250ZW50KGh0bWxfNWNjYmUwZjVlMmIwNDQwMmE0MTAzY2U5NTlmNzRkZmQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTAzODNkMWM2N2EwNGE4NzlhYTBhNTg5YzVhNmRlMTIuYmluZFBvcHVwKHBvcHVwX2ZlMGU3MzVkODdhMTQ2ZWQ5NDc0NzZjOTExMDFjMGNmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzAzMzUzNTdmMmQ5MDRhZTFiNDFmOWQwYzYyODA5ZTQ1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjk2OTQ3NiwtNzkuNDExMzA3MjAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYTk2MTAxNTZjODZmNDAyZWE1ZGUwZDJkYjg3OGUzZWQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWRmY2IwMWFmZmRjNGQ3N2JlZDRjM2U5MDU4MDYxY2EgPSAkKCc8ZGl2IGlkPSJodG1sXzFkZmNiMDFhZmZkYzRkNzdiZWQ0YzNlOTA1ODA2MWNhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gRm9yZXN0IEhpbGwgTm9ydGgsICBGb3Jlc3QgSGlsbCBXZXN0LCAgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hOTYxMDE1NmM4NmY0MDJlYTVkZTBkMmRiODc4ZTNlZC5zZXRDb250ZW50KGh0bWxfMWRmY2IwMWFmZmRjNGQ3N2JlZDRjM2U5MDU4MDYxY2EpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDMzNTM1N2YyZDkwNGFlMWI0MWY5ZDBjNjI4MDllNDUuYmluZFBvcHVwKHBvcHVwX2E5NjEwMTU2Yzg2ZjQwMmVhNWRlMGQyZGI4NzhlM2VkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzMxNTRmNzU3MjVkMTQxMzNiYzRmZmEyYWFmNDM2ZmJmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjcyNzA5NywtNzkuNDA1Njc4NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTc1N2EwOWVlMzU3NGE4ZmE4YjQ1ZDQ5ODBkMmRlNjAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTZmYmMyYjgxMWRmNGUyZWJhYzkxMmMxMDBjOTFhMTkgPSAkKCc8ZGl2IGlkPSJodG1sX2U2ZmJjMmI4MTFkZjRlMmViYWM5MTJjMTAwYzkxYTE5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gVGhlIEFubmV4LCAgTm9ydGggTWlkdG93biwgIFlvcmt2aWxsZSwgIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTc1N2EwOWVlMzU3NGE4ZmE4YjQ1ZDQ5ODBkMmRlNjAuc2V0Q29udGVudChodG1sX2U2ZmJjMmI4MTFkZjRlMmViYWM5MTJjMTAwYzkxYTE5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMxNTRmNzU3MjVkMTQxMzNiYzRmZmEyYWFmNDM2ZmJmLmJpbmRQb3B1cChwb3B1cF85NzU3YTA5ZWUzNTc0YThmYThiNDVkNDk4MGQyZGU2MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iZjJmYjgxOGRmMjY0NTY2YTRhNmQ0YzMxY2JhZjFjMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjY5NTYsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTg0ZTliYzk3YWQ1NGViYjkzZmI1MGY5MWM5ZWM1MzcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjYxOTFlNTMxNjhjNDgyMThiMzMzNzg4MDg3MDFjY2EgPSAkKCc8ZGl2IGlkPSJodG1sX2Y2MTkxZTUzMTY4YzQ4MjE4YjMzMzc4ODA4NzAxY2NhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gSGFyYm9yZCwgIFVuaXZlcnNpdHkgb2YgVG9yb250bywgIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE4NGU5YmM5N2FkNTRlYmI5M2ZiNTBmOTFjOWVjNTM3LnNldENvbnRlbnQoaHRtbF9mNjE5MWU1MzE2OGM0ODIxOGIzMzM3ODgwODcwMWNjYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iZjJmYjgxOGRmMjY0NTY2YTRhNmQ0YzMxY2JhZjFjMi5iaW5kUG9wdXAocG9wdXBfMTg0ZTliYzk3YWQ1NGViYjkzZmI1MGY5MWM5ZWM1MzcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOGU2Zjg4NDVmMTgzNDFkMTg2NjQwNmQ4M2FkMGZkZjUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTMyMDU3LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2MyMWExZWNmMzhiNzRjNzFiOWZhNTgyNjljY2IwODhjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzI4Zjk0MGUxMGYyMTRjY2JiZGIzNjY3OGVlZWI5MzQzID0gJCgnPGRpdiBpZD0iaHRtbF8yOGY5NDBlMTBmMjE0Y2NiYmRiMzY2NzhlZWViOTM0MyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IENoaW5hdG93biwgIEdyYW5nZSBQYXJrLCAgS2Vuc2luZ3RvbiBNYXJrZXQsICBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jMjFhMWVjZjM4Yjc0YzcxYjlmYTU4MjY5Y2NiMDg4Yy5zZXRDb250ZW50KGh0bWxfMjhmOTQwZTEwZjIxNGNjYmJkYjM2Njc4ZWVlYjkzNDMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOGU2Zjg4NDVmMTgzNDFkMTg2NjQwNmQ4M2FkMGZkZjUuYmluZFBvcHVwKHBvcHVwX2MyMWExZWNmMzhiNzRjNzFiOWZhNTgyNjljY2IwODhjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzhkYThlNjhkMDY0MzQwZGFiZTMxMjlhZGJmNzI2Y2QzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjI4OTQ2NywtNzkuMzk0NDE5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zNWMyY2I3ZGNlOTY0NGNkYTkyNDZlM2NjODAwMTMyOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84ODJhY2EwMDAzNzE0YTA0YjMyNjk1OGNkY2M2ZGY3OCA9ICQoJzxkaXYgaWQ9Imh0bWxfODgyYWNhMDAwMzcxNGEwNGIzMjY5NThjZGNjNmRmNzgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBDTiBUb3dlciwgIEJhdGh1cnN0IFF1YXksICBJc2xhbmQgYWlycG9ydCwgIEhhcmJvdXJmcm9udCBXZXN0LCAgS2luZyBhbmQgU3BhZGluYSwgIFJhaWx3YXkgTGFuZHMsICBTb3V0aCBOaWFnYXJhLCAgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMzVjMmNiN2RjZTk2NDRjZGE5MjQ2ZTNjYzgwMDEzMjkuc2V0Q29udGVudChodG1sXzg4MmFjYTAwMDM3MTRhMDRiMzI2OTU4Y2RjYzZkZjc4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzhkYThlNjhkMDY0MzQwZGFiZTMxMjlhZGJmNzI2Y2QzLmJpbmRQb3B1cChwb3B1cF8zNWMyY2I3ZGNlOTY0NGNkYTkyNDZlM2NjODAwMTMyOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iYjJhMDMxZGU4YWI0NTI1OTVkN2Y5N2M2OTljZDNhMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NjQzNTIsLTc5LjM3NDg0NTk5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzFkOTViNzdmMTY0ZjQxYzY4MWYzOWUxZTYxMzYzMmZlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzAyM2UzYTZjNTcxZDQ2YzQ5MjU4NWE0MjVlYjNkZmRjID0gJCgnPGRpdiBpZD0iaHRtbF8wMjNlM2E2YzU3MWQ0NmM0OTI1ODVhNDI1ZWIzZGZkYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFN0biBBIFBPIEJveGVzIDI1IFRoZSBFc3BsYW5hZGUsICBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xZDk1Yjc3ZjE2NGY0MWM2ODFmMzllMWU2MTM2MzJmZS5zZXRDb250ZW50KGh0bWxfMDIzZTNhNmM1NzFkNDZjNDkyNTg1YTQyNWViM2RmZGMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYmIyYTAzMWRlOGFiNDUyNTk1ZDdmOTdjNjk5Y2QzYTEuYmluZFBvcHVwKHBvcHVwXzFkOTViNzdmMTY0ZjQxYzY4MWYzOWUxZTYxMzYzMmZlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Y4NmVkYmQ0NmZkNDRhOWZhZmIwMWJmNTE4N2U1YTRkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ4NDI5MiwtNzkuMzgyMjgwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83NjdkMjZiOGY2NmU0MmVjYmRlYzk5MDYzNWI2MDdiYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jZGM2NTQwNDY0OGY0Yjk1YTc2YjlkY2ZkZTg0ODE4MSA9ICQoJzxkaXYgaWQ9Imh0bWxfY2RjNjU0MDQ2NDhmNGI5NWE3NmI5ZGNmZGU4NDgxODEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBGaXJzdCBDYW5hZGlhbiBQbGFjZSwgIFVuZGVyZ3JvdW5kIGNpdHksICBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83NjdkMjZiOGY2NmU0MmVjYmRlYzk5MDYzNWI2MDdiYS5zZXRDb250ZW50KGh0bWxfY2RjNjU0MDQ2NDhmNGI5NWE3NmI5ZGNmZGU4NDgxODEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjg2ZWRiZDQ2ZmQ0NGE5ZmFmYjAxYmY1MTg3ZTVhNGQuYmluZFBvcHVwKHBvcHVwXzc2N2QyNmI4ZjY2ZTQyZWNiZGVjOTkwNjM1YjYwN2JhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzI2ODZmYjlkODFiYzQ3YWJiMTRiYzc4NzMxZTA0NjQ0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzE4NTE3OTk5OTk5OTk2LC03OS40NjQ3NjMyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83MzM2NmU5MWM3MjM0ODQxYjc2MGQ1Y2Y2ZWVkYjAxMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80OWQxZjBmYTdmN2Q0ZGIxYjEwZTMwMjQ0OGI1NjU0YiA9ICQoJzxkaXYgaWQ9Imh0bWxfNDlkMWYwZmE3ZjdkNGRiMWIxMGUzMDI0NDhiNTY1NGIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBMYXdyZW5jZSBIZWlnaHRzLCAgTGF3cmVuY2UgTWFub3IsICBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83MzM2NmU5MWM3MjM0ODQxYjc2MGQ1Y2Y2ZWVkYjAxMS5zZXRDb250ZW50KGh0bWxfNDlkMWYwZmE3ZjdkNGRiMWIxMGUzMDI0NDhiNTY1NGIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjY4NmZiOWQ4MWJjNDdhYmIxNGJjNzg3MzFlMDQ2NDQuYmluZFBvcHVwKHBvcHVwXzczMzY2ZTkxYzcyMzQ4NDFiNzYwZDVjZjZlZWRiMDExKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2YzMzFiYWUyZGYwMDQ2ZWViNWNiYTkyODYwZDIxYTc4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzA5NTc3LC03OS40NDUwNzI1OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85OTlkZGUyYmM3MTU0YjY0YmFlYTk1MDk4YWE5NDE5OSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84NTg2Y2MyYmYyNWY0MTEzOWIwOGU0ZjU3NTk4ZDhlNyA9ICQoJzxkaXYgaWQ9Imh0bWxfODU4NmNjMmJmMjVmNDExMzliMDhlNGY1NzU5OGQ4ZTciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBHbGVuY2Fpcm4sICBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85OTlkZGUyYmM3MTU0YjY0YmFlYTk1MDk4YWE5NDE5OS5zZXRDb250ZW50KGh0bWxfODU4NmNjMmJmMjVmNDExMzliMDhlNGY1NzU5OGQ4ZTcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjMzMWJhZTJkZjAwNDZlZWI1Y2JhOTI4NjBkMjFhNzguYmluZFBvcHVwKHBvcHVwXzk5OWRkZTJiYzcxNTRiNjRiYWVhOTUwOThhYTk0MTk5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzhlNjc2MGM1ZTQ0OTQxODVhZGE1Y2EyMjQwZjM5ZmMwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjkzNzgxMywtNzkuNDI4MTkxNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjE5MTkyNWNjZmUwNDg0ZjgyZjhkM2E0OTcwZDE5NDUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDVkNDI5ODNkNWE3NGI5NmE2Y2I1OGYzNTQyNGE5N2UgPSAkKCc8ZGl2IGlkPSJodG1sX2Q1ZDQyOTgzZDVhNzRiOTZhNmNiNThmMzU0MjRhOTdlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gSHVtZXdvb2QtQ2VkYXJ2YWxlLCAgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjE5MTkyNWNjZmUwNDg0ZjgyZjhkM2E0OTcwZDE5NDUuc2V0Q29udGVudChodG1sX2Q1ZDQyOTgzZDVhNzRiOTZhNmNiNThmMzU0MjRhOTdlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzhlNjc2MGM1ZTQ0OTQxODVhZGE1Y2EyMjQwZjM5ZmMwLmJpbmRQb3B1cChwb3B1cF9iMTkxOTI1Y2NmZTA0ODRmODJmOGQzYTQ5NzBkMTk0NSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82ZjFhYTdhODE3YjA0NGUzOTQwYmNmYzEzYjdiNDM4NyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY4OTAyNTYsLTc5LjQ1MzUxMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lMmI1YWRlNWY4NmU0MDI3YWEzMmM3MzYyNGZiOGQxOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80NDBkMDc3YmZlYmM0MjJlYjU2NDgyMWFiYjI5MGNlOCA9ICQoJzxkaXYgaWQ9Imh0bWxfNDQwZDA3N2JmZWJjNDIyZWI1NjQ4MjFhYmIyOTBjZTgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBDYWxlZG9uaWEtRmFpcmJhbmtzLCAgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTJiNWFkZTVmODZlNDAyN2FhMzJjNzM2MjRmYjhkMTkuc2V0Q29udGVudChodG1sXzQ0MGQwNzdiZmViYzQyMmViNTY0ODIxYWJiMjkwY2U4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzZmMWFhN2E4MTdiMDQ0ZTM5NDBiY2ZjMTNiN2I0Mzg3LmJpbmRQb3B1cChwb3B1cF9lMmI1YWRlNWY4NmU0MDI3YWEzMmM3MzYyNGZiOGQxOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82MmMxZTJmMTNiZTg0ZWFlYmVmYWIyZTI5N2RjNmJjZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTU0MiwtNzkuNDIyNTYzN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lMDU4MDUyZmQyODE0OTQ0YTNhZTU4ZTViMzQ0MGNkNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wOTg5ZGMzZTI2YmY0MTkyOWY2MDY5NTZhMzgxZjYxYSA9ICQoJzxkaXYgaWQ9Imh0bWxfMDk4OWRjM2UyNmJmNDE5MjlmNjA2OTU2YTM4MWY2MWEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBDaHJpc3RpZSwgIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2UwNTgwNTJmZDI4MTQ5NDRhM2FlNThlNWIzNDQwY2Q3LnNldENvbnRlbnQoaHRtbF8wOTg5ZGMzZTI2YmY0MTkyOWY2MDY5NTZhMzgxZjYxYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82MmMxZTJmMTNiZTg0ZWFlYmVmYWIyZTI5N2RjNmJjZS5iaW5kUG9wdXAocG9wdXBfZTA1ODA1MmZkMjgxNDk0NGEzYWU1OGU1YjM0NDBjZDcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2JhMmUwNGJjOTdjNGE0NDk2YTQ0Y2U5NzBjNmZiMzQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjkwMDUxMDAwMDAwMSwtNzkuNDQyMjU5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81ZGJlMDA1OGFlZjM0ZTg3YmMyZDM3MDk3ODE1YjJiMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kYjBmZDgxNjI0MTA0ZWEyOWJiNWI5M2JmZDE4MzEyNSA9ICQoJzxkaXYgaWQ9Imh0bWxfZGIwZmQ4MTYyNDEwNGVhMjliYjViOTNiZmQxODMxMjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBEb3ZlcmNvdXJ0IFZpbGxhZ2UsICBEdWZmZXJpbiwgIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNWRiZTAwNThhZWYzNGU4N2JjMmQzNzA5NzgxNWIyYjIuc2V0Q29udGVudChodG1sX2RiMGZkODE2MjQxMDRlYTI5YmI1YjkzYmZkMTgzMTI1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzNiYTJlMDRiYzk3YzRhNDQ5NmE0NGNlOTcwYzZmYjM0LmJpbmRQb3B1cChwb3B1cF81ZGJlMDA1OGFlZjM0ZTg3YmMyZDM3MDk3ODE1YjJiMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84ZWRlZWRlYjI0ODE0NDQzYTE4Y2Y4N2RlZjg0MmY3YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NzkyNjcwMDAwMDAwNiwtNzkuNDE5NzQ5N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wMmI3ZjA0ZTQ1NjA0YWE3ODQ4ZjBhYjk3MjM0NjA4MiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iZWJhYzkyNjVlMjM0MDkxYjE4YmNlZDg0YTZlNDJhMyA9ICQoJzxkaXYgaWQ9Imh0bWxfYmViYWM5MjY1ZTIzNDA5MWIxOGJjZWQ4NGE2ZTQyYTMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBMaXR0bGUgUG9ydHVnYWwsICBUcmluaXR5LCAgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wMmI3ZjA0ZTQ1NjA0YWE3ODQ4ZjBhYjk3MjM0NjA4Mi5zZXRDb250ZW50KGh0bWxfYmViYWM5MjY1ZTIzNDA5MWIxOGJjZWQ4NGE2ZTQyYTMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOGVkZWVkZWIyNDgxNDQ0M2ExOGNmODdkZWY4NDJmN2EuYmluZFBvcHVwKHBvcHVwXzAyYjdmMDRlNDU2MDRhYTc4NDhmMGFiOTcyMzQ2MDgyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzE5NjhiN2MyMDhmZDQ4N2ZiNGVmMTJjODI0NTM1NzlhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjM2ODQ3MiwtNzkuNDI4MTkxNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTMxMjEzOTM1NGE4NDI5Y2JiNTEwMWJhMDEwMmU4MTEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzA2MmQ1YWRiNGI3NGZjYzkxMjdmODc3MmFkYzQwODMgPSAkKCc8ZGl2IGlkPSJodG1sX2MwNjJkNWFkYjRiNzRmY2M5MTI3Zjg3NzJhZGM0MDgzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gQnJvY2t0b24sICBFeGhpYml0aW9uIFBsYWNlLCAgUGFya2RhbGUgVmlsbGFnZSwgIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTMxMjEzOTM1NGE4NDI5Y2JiNTEwMWJhMDEwMmU4MTEuc2V0Q29udGVudChodG1sX2MwNjJkNWFkYjRiNzRmY2M5MTI3Zjg3NzJhZGM0MDgzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzE5NjhiN2MyMDhmZDQ4N2ZiNGVmMTJjODI0NTM1NzlhLmJpbmRQb3B1cChwb3B1cF9lMzEyMTM5MzU0YTg0MjljYmI1MTAxYmEwMTAyZTgxMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82NDIwNWZiMmVjYWE0MTJlOTI1MzBlMmZkZjMxYTZmMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxMzc1NjIwMDAwMDAwNiwtNzkuNDkwMDczOF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81M2Y5MTg3NzNkYWI0ZDc3OGJmZmNmYTg0MmZiZTNjNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lOWIzNDRkODNiYTA0OGVkYjMzODFkNDAyMWI0ZjVkNCA9ICQoJzxkaXYgaWQ9Imh0bWxfZTliMzQ0ZDgzYmEwNDhlZGIzMzgxZDQwMjFiNGY1ZDQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBEb3duc3ZpZXcsICBOb3J0aCBQYXJrLCAgVXB3b29kIFBhcmssICBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81M2Y5MTg3NzNkYWI0ZDc3OGJmZmNmYTg0MmZiZTNjNi5zZXRDb250ZW50KGh0bWxfZTliMzQ0ZDgzYmEwNDhlZGIzMzgxZDQwMjFiNGY1ZDQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjQyMDVmYjJlY2FhNDEyZTkyNTMwZTJmZGYzMWE2ZjIuYmluZFBvcHVwKHBvcHVwXzUzZjkxODc3M2RhYjRkNzc4YmZmY2ZhODQyZmJlM2M2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzhhMTdmMDFhNWIxYTQwOWM5ZDRiMGYxNTY5M2I0N2JkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjkxMTE1OCwtNzkuNDc2MDEzMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzc5NTRiNTIyNDg5NDViN2FkZjE1NDVlODJhZWE5MzAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMDNkNzgzYjNkZDI2NDkwYTljYWYyYTFiNDg5ZGRkZjYgPSAkKCc8ZGl2IGlkPSJodG1sXzAzZDc4M2IzZGQyNjQ5MGE5Y2FmMmExYjQ4OWRkZGY2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gRGVsIFJheSwgIEtlZWxlc2RhbGUsICBNb3VudCBEZW5uaXMsICBTaWx2ZXJ0aG9ybiwgIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2M3OTU0YjUyMjQ4OTQ1YjdhZGYxNTQ1ZTgyYWVhOTMwLnNldENvbnRlbnQoaHRtbF8wM2Q3ODNiM2RkMjY0OTBhOWNhZjJhMWI0ODlkZGRmNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84YTE3ZjAxYTViMWE0MDljOWQ0YjBmMTU2OTNiNDdiZC5iaW5kUG9wdXAocG9wdXBfYzc5NTRiNTIyNDg5NDViN2FkZjE1NDVlODJhZWE5MzApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZGU1NzM1OTliNDkxNGZmNTgxODFlNzQyMTUxZDYwNGIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzMxODUyOTk5OTk5OSwtNzkuNDg3MjYxOTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZWQwZjNkNDE3YjBiNDFhYWE0OWE4ZmM1NDE3MTI5OTIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTY5YzFhZmY0MTM1NDVjMDg2N2Q3OWEyMTVhMWU1YmYgPSAkKCc8ZGl2IGlkPSJodG1sXzk2OWMxYWZmNDEzNTQ1YzA4NjdkNzlhMjE1YTFlNWJmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gVGhlIEp1bmN0aW9uIE5vcnRoLCAgUnVubnltZWRlLCAgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZWQwZjNkNDE3YjBiNDFhYWE0OWE4ZmM1NDE3MTI5OTIuc2V0Q29udGVudChodG1sXzk2OWMxYWZmNDEzNTQ1YzA4NjdkNzlhMjE1YTFlNWJmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RlNTczNTk5YjQ5MTRmZjU4MTgxZTc0MjE1MWQ2MDRiLmJpbmRQb3B1cChwb3B1cF9lZDBmM2Q0MTdiMGI0MWFhYTQ5YThmYzU0MTcxMjk5Mik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83Y2M0YjA0YzZjODI0NzA5OWRiYWM3MDNiOTVmOThhNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MTYwODMsLTc5LjQ2NDc2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2JjNTdlYjBhOWFiOTRjMTVhNTUwNjVhYzYyYjhkNjdmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2E4ZTQ4ZGFkMDZiMjRmOWU4NzlkYTY3MGQ0NzQzM2YxID0gJCgnPGRpdiBpZD0iaHRtbF9hOGU0OGRhZDA2YjI0ZjllODc5ZGE2NzBkNDc0MzNmMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IEhpZ2ggUGFyaywgIFRoZSBKdW5jdGlvbiBTb3V0aCwgIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmM1N2ViMGE5YWI5NGMxNWE1NTA2NWFjNjJiOGQ2N2Yuc2V0Q29udGVudChodG1sX2E4ZTQ4ZGFkMDZiMjRmOWU4NzlkYTY3MGQ0NzQzM2YxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzdjYzRiMDRjNmM4MjQ3MDk5ZGJhYzcwM2I5NWY5OGE1LmJpbmRQb3B1cChwb3B1cF9iYzU3ZWIwYTlhYjk0YzE1YTU1MDY1YWM2MmI4ZDY3Zik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kMmI0OTk0ZGFjNTI0ZDY3ODU1YzE0ZjJlNjVlMDBhZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODk1OTcsLTc5LjQ1NjMyNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83NzZmMWQxNWJmZTc0NzRkOTVkMGI2MmVjNWRjODE5MiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84MjU0YzRjYjdmZTU0ZmJlYjVlNzZmYTZkMGQ3M2E3ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfODI1NGM0Y2I3ZmU1NGZiZWI1ZTc2ZmE2ZDBkNzNhN2QiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBQYXJrZGFsZSwgIFJvbmNlc3ZhbGxlcywgIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzc2ZjFkMTViZmU3NDc0ZDk1ZDBiNjJlYzVkYzgxOTIuc2V0Q29udGVudChodG1sXzgyNTRjNGNiN2ZlNTRmYmViNWU3NmZhNmQwZDczYTdkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2QyYjQ5OTRkYWM1MjRkNjc4NTVjMTRmMmU2NWUwMGFlLmJpbmRQb3B1cChwb3B1cF83NzZmMWQxNWJmZTc0NzRkOTVkMGI2MmVjNWRjODE5Mik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iZTYzNTRjNGM1OWE0ZGQyYWMzYTg3OWZiOTNkMGQ5NiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MTU3MDYsLTc5LjQ4NDQ0OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTE4M2Y2ZDhkYmVmNDZkYzlhNTYzNjAyMjk3M2U5NWEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmU4NGUyN2Q1MGEwNDc5MzhiYjMzNWI5NzczZmZmMzQgPSAkKCc8ZGl2IGlkPSJodG1sXzZlODRlMjdkNTBhMDQ3OTM4YmIzMzViOTc3M2ZmZjM0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gUnVubnltZWRlLCAgU3dhbnNlYSwgIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTE4M2Y2ZDhkYmVmNDZkYzlhNTYzNjAyMjk3M2U5NWEuc2V0Q29udGVudChodG1sXzZlODRlMjdkNTBhMDQ3OTM4YmIzMzViOTc3M2ZmZjM0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JlNjM1NGM0YzU5YTRkZDJhYzNhODc5ZmI5M2QwZDk2LmJpbmRQb3B1cChwb3B1cF85MTgzZjZkOGRiZWY0NmRjOWE1NjM2MDIyOTczZTk1YSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83YjlmNzE4ZTU5YmY0MzA3OTljYTQwMDhmMzlhNWFlMCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjMwMTUsLTc5LjM4OTQ5MzhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2I1NTM2NDk3ZGIwNDNhYmI1OTEzMWZmYzk4Y2NlYTggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjI0YTc2ZTFkMjJlNGIxOGIzMDFlNDkyOTJmZDY5M2QgPSAkKCc8ZGl2IGlkPSJodG1sXzIyNGE3NmUxZDIyZTRiMThiMzAxZTQ5MjkyZmQ2OTNkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gUXVlZW4mIzM5O3MgUGFyaywgIFF1ZWVuJiMzOTtzIFBhcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzNiNTUzNjQ5N2RiMDQzYWJiNTkxMzFmZmM5OGNjZWE4LnNldENvbnRlbnQoaHRtbF8yMjRhNzZlMWQyMmU0YjE4YjMwMWU0OTI5MmZkNjkzZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83YjlmNzE4ZTU5YmY0MzA3OTljYTQwMDhmMzlhNWFlMC5iaW5kUG9wdXAocG9wdXBfM2I1NTM2NDk3ZGIwNDNhYmI1OTEzMWZmYzk4Y2NlYTgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTVhMzNiN2JjODM0NDU0ZWFhMDcyNDZhYzMwYTQ0NGIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzY5NjU2LC03OS42MTU4MTg5OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ODhmZWRjNmQ0YTk0NmYyOTkxZTIyNzU5ZDBmMjAxNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83NmY3Y2FlYTM0YmI0YWE0YTM4OTc4MDRlMDA1NDM2NCA9ICQoJzxkaXYgaWQ9Imh0bWxfNzZmN2NhZWEzNGJiNGFhNGEzODk3ODA0ZTAwNTQzNjQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBDYW5hZGEgUG9zdCBHYXRld2F5IFByb2Nlc3NpbmcgQ2VudHJlLCAgTWlzc2lzc2F1Z2E8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzc4OGZlZGM2ZDRhOTQ2ZjI5OTFlMjI3NTlkMGYyMDE1LnNldENvbnRlbnQoaHRtbF83NmY3Y2FlYTM0YmI0YWE0YTM4OTc4MDRlMDA1NDM2NCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hNWEzM2I3YmM4MzQ0NTRlYWEwNzI0NmFjMzBhNDQ0Yi5iaW5kUG9wdXAocG9wdXBfNzg4ZmVkYzZkNGE5NDZmMjk5MWUyMjc1OWQwZjIwMTUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTQ5ODhiNzk3MDE5NDY5YjkwZTg3Nzk1YjU0OWIxMjQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjI3NDM5LC03OS4zMjE1NThdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjE3ZjkzYzBlNDQzNGNhNmEzMjU3YzFlNDc3ZDUxNGMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfN2NjZGRkZjMxMGVhNGQwZDg5N2JkMmYyM2JlZmVhMzQgPSAkKCc8ZGl2IGlkPSJodG1sXzdjY2RkZGYzMTBlYTRkMGQ4OTdiZDJmMjNiZWZlYTM0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gQnVzaW5lc3MgUmVwbHkgTWFpbCBQcm9jZXNzaW5nIENlbnRyZSA5NjkgRWFzdGVybiwgIEVhc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjE3ZjkzYzBlNDQzNGNhNmEzMjU3YzFlNDc3ZDUxNGMuc2V0Q29udGVudChodG1sXzdjY2RkZGYzMTBlYTRkMGQ4OTdiZDJmMjNiZWZlYTM0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E0OTg4Yjc5NzAxOTQ2OWI5MGU4Nzc5NWI1NDliMTI0LmJpbmRQb3B1cChwb3B1cF9iMTdmOTNjMGU0NDM0Y2E2YTMyNTdjMWU0NzdkNTE0Yyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xMDcwOGUwNDYxY2E0ZTZkOWE5YjdlYWI4ZDE2YWY1ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYwNTY0NjYsLTc5LjUwMTMyMDcwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzg4NDIxMWNhMDdhZTQxMzg4MzE5N2NlODIwOGM5NTkzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2UyOGE0MjE1MjgyZTRhZTViN2YxZjU0ZjU4N2ZjZWVjID0gJCgnPGRpdiBpZD0iaHRtbF9lMjhhNDIxNTI4MmU0YWU1YjdmMWY1NGY1ODdmY2VlYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IEh1bWJlciBCYXkgU2hvcmVzLCAgTWltaWNvIFNvdXRoLCAgTmV3IFRvcm9udG8sICBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg4NDIxMWNhMDdhZTQxMzg4MzE5N2NlODIwOGM5NTkzLnNldENvbnRlbnQoaHRtbF9lMjhhNDIxNTI4MmU0YWU1YjdmMWY1NGY1ODdmY2VlYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xMDcwOGUwNDYxY2E0ZTZkOWE5YjdlYWI4ZDE2YWY1ZS5iaW5kUG9wdXAocG9wdXBfODg0MjExY2EwN2FlNDEzODgzMTk3Y2U4MjA4Yzk1OTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZWYyY2YwNzRjMWQ5NDgxYTljNmMzMzg2Y2NiZjc3YjcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MDI0MTM3MDAwMDAwMSwtNzkuNTQzNDg0MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmY0N2IxOWY1NGE3NDAxZGIxMjU4MWRjOTRmNTgxNTYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjUxMjZiYzRmNjk5NDhmM2FlNDFjNTIwNjk0NWNjYjUgPSAkKCc8ZGl2IGlkPSJodG1sXzY1MTI2YmM0ZjY5OTQ4ZjNhZTQxYzUyMDY5NDVjY2I1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gQWxkZXJ3b29kLCAgTG9uZyBCcmFuY2gsICBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2JmNDdiMTlmNTRhNzQwMWRiMTI1ODFkYzk0ZjU4MTU2LnNldENvbnRlbnQoaHRtbF82NTEyNmJjNGY2OTk0OGYzYWU0MWM1MjA2OTQ1Y2NiNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lZjJjZjA3NGMxZDk0ODFhOWM2YzMzODZjY2JmNzdiNy5iaW5kUG9wdXAocG9wdXBfYmY0N2IxOWY1NGE3NDAxZGIxMjU4MWRjOTRmNTgxNTYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOGNkMmM2ODRkNTdkNDA2NjhmZjljY2MyYzlmODE5NTEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTM2NTM2MDAwMDAwMDUsLTc5LjUwNjk0MzZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzQzMTkxNTc5YzdjNDM1OTgzMDUyNDU1MmI3NjBhYzUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTdiMjk2YmNiOGJiNDU2ZjllZGE5ODQ3ODU5NmQyZDIgPSAkKCc8ZGl2IGlkPSJodG1sXzE3YjI5NmJjYjhiYjQ1NmY5ZWRhOTg0Nzg1OTZkMmQyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gVGhlIEtpbmdzd2F5LCAgTW9udGdvbWVyeSBSb2FkLCAgT2xkIE1pbGwgTm9ydGgsICBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzc0MzE5MTU3OWM3YzQzNTk4MzA1MjQ1NTJiNzYwYWM1LnNldENvbnRlbnQoaHRtbF8xN2IyOTZiY2I4YmI0NTZmOWVkYTk4NDc4NTk2ZDJkMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84Y2QyYzY4NGQ1N2Q0MDY2OGZmOWNjYzJjOWY4MTk1MS5iaW5kUG9wdXAocG9wdXBfNzQzMTkxNTc5YzdjNDM1OTgzMDUyNDU1MmI3NjBhYzUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzY2YjdiNGVjYWQ0NGI1OWIxMGRkM2UwNjQzODYxZTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzYyNTc5LC03OS40OTg1MDkwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zM2M1ZGEzODI0MTM0MzRmOWYxNTljNWUwNTk3ZWY5NCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85NTUwNThmYzYzZGE0ODUxODc3NmNkNjFmY2I4OGRhNiA9ICQoJzxkaXYgaWQ9Imh0bWxfOTU1MDU4ZmM2M2RhNDg1MTg3NzZjZDYxZmNiODhkYTYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBIdW1iZXIgQmF5LCAgS2luZyYjMzk7cyBNaWxsIFBhcmssICBLaW5nc3dheSBQYXJrIFNvdXRoIEVhc3QsICBNaW1pY28gTkUsICBPbGQgTWlsbCBTb3V0aCwgIFRoZSBRdWVlbnN3YXkgRWFzdCwgIFJveWFsIFlvcmsgU291dGggRWFzdCwgIFN1bm55bGVhLCAgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zM2M1ZGEzODI0MTM0MzRmOWYxNTljNWUwNTk3ZWY5NC5zZXRDb250ZW50KGh0bWxfOTU1MDU4ZmM2M2RhNDg1MTg3NzZjZDYxZmNiODhkYTYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzY2YjdiNGVjYWQ0NGI1OWIxMGRkM2UwNjQzODYxZTQuYmluZFBvcHVwKHBvcHVwXzMzYzVkYTM4MjQxMzQzNGY5ZjE1OWM1ZTA1OTdlZjk0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzQ2MTZmNjVhOWRjNTRkMzRhOTI1Yzc2NDcwZjdkZTllID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjI4ODQwOCwtNzkuNTIwOTk5NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODQwY2ZkMDBiZTlhNDAwNmI2NDE0ZjQxMmI0NmVhNTUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWMxZjg4NjE3ZGEzNGI4MjhlY2MyMDNmNTZhMDQ2ODkgPSAkKCc8ZGl2IGlkPSJodG1sXzFjMWY4ODYxN2RhMzRiODI4ZWNjMjAzZjU2YTA0Njg5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gS2luZ3N3YXkgUGFyayBTb3V0aCBXZXN0LCAgTWltaWNvIE5XLCAgVGhlIFF1ZWVuc3dheSBXZXN0LCAgUm95YWwgWW9yayBTb3V0aCBXZXN0LCAgU291dGggb2YgQmxvb3IsICBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg0MGNmZDAwYmU5YTQwMDZiNjQxNGY0MTJiNDZlYTU1LnNldENvbnRlbnQoaHRtbF8xYzFmODg2MTdkYTM0YjgyOGVjYzIwM2Y1NmEwNDY4OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80NjE2ZjY1YTlkYzU0ZDM0YTkyNWM3NjQ3MGY3ZGU5ZS5iaW5kUG9wdXAocG9wdXBfODQwY2ZkMDBiZTlhNDAwNmI2NDE0ZjQxMmI0NmVhNTUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmVlYWYxZTUxMGZjNGMyZWE4ZWE5Yzk3YjdhMWZiMTIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njc4NTU2LC03OS41MzIyNDI0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jNjA5M2ExYjliYzI0ODY4OTI1YTFlMTI5MWU4OGU1YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kYWRiZmU2OGI1NjM0ZTFlYmVjMzkwZDQyMGQyZDlhZiA9ICQoJzxkaXYgaWQ9Imh0bWxfZGFkYmZlNjhiNTYzNGUxZWJlYzM5MGQ0MjBkMmQ5YWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBJc2xpbmd0b24gQXZlbnVlLCAgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jNjA5M2ExYjliYzI0ODY4OTI1YTFlMTI5MWU4OGU1Yi5zZXRDb250ZW50KGh0bWxfZGFkYmZlNjhiNTYzNGUxZWJlYzM5MGQ0MjBkMmQ5YWYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZmVlYWYxZTUxMGZjNGMyZWE4ZWE5Yzk3YjdhMWZiMTIuYmluZFBvcHVwKHBvcHVwX2M2MDkzYTFiOWJjMjQ4Njg5MjVhMWUxMjkxZTg4ZTViKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJkNWZiOTkzNmNjMDRhMzJhOTUwYmQxNzI0YTVhNzM1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUwOTQzMiwtNzkuNTU0NzI0NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfN2Q5MzdjZWJlYTUzNDg0YjhjOGFkZmFhYTY5OTRmNjUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmNiZDAxZTJiMmQxNDVkN2FjMzlkYWI2NTk1NDAzYzggPSAkKCc8ZGl2IGlkPSJodG1sXzZjYmQwMWUyYjJkMTQ1ZDdhYzM5ZGFiNjU5NTQwM2M4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gQ2xvdmVyZGFsZSwgIElzbGluZ3RvbiwgIE1hcnRpbiBHcm92ZSwgIFByaW5jZXNzIEdhcmRlbnMsICBXZXN0IERlYW5lIFBhcmssICBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzdkOTM3Y2ViZWE1MzQ4NGI4YzhhZGZhYWE2OTk0ZjY1LnNldENvbnRlbnQoaHRtbF82Y2JkMDFlMmIyZDE0NWQ3YWMzOWRhYjY1OTU0MDNjOCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yZDVmYjk5MzZjYzA0YTMyYTk1MGJkMTcyNGE1YTczNS5iaW5kUG9wdXAocG9wdXBfN2Q5MzdjZWJlYTUzNDg0YjhjOGFkZmFhYTY5OTRmNjUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzE4MTM5OWY5Yzk3NGRmZDhkMGY0YTU5MTFhZTMwNmYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDM1MTUyLC03OS41NzcyMDA3OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81NzM3MzQxMmY4ZjI0NGZhYTVjYTMyOGRjY2Q0MWVkMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85MGExNzVjYWRmZWQ0NGUyYTcwMThhNzg3ZjY1YzcxYyA9ICQoJzxkaXYgaWQ9Imh0bWxfOTBhMTc1Y2FkZmVkNDRlMmE3MDE4YTc4N2Y2NWM3MWMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBCbG9vcmRhbGUgR2FyZGVucywgIEVyaW5nYXRlLCAgTWFya2xhbmQgV29vZCwgIE9sZCBCdXJuaGFtdGhvcnBlLCAgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81NzM3MzQxMmY4ZjI0NGZhYTVjYTMyOGRjY2Q0MWVkMC5zZXRDb250ZW50KGh0bWxfOTBhMTc1Y2FkZmVkNDRlMmE3MDE4YTc4N2Y2NWM3MWMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzE4MTM5OWY5Yzk3NGRmZDhkMGY0YTU5MTFhZTMwNmYuYmluZFBvcHVwKHBvcHVwXzU3MzczNDEyZjhmMjQ0ZmFhNWNhMzI4ZGNjZDQxZWQwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU1ODA3ZjIwM2I5ODRmNzZhMDRkZjkwOWZkOTYyOTYzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzU2MzAzMywtNzkuNTY1OTYzMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAieWVsbG93IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfN2NlYTkwMGRjNDY4NDE3MGFkOTg1YWFmZTZiN2Y1MjUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTJlMzkzYzY1MDg2NGJjMjkyZWJlOGQ5NjYxMjM4YTcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjIyNjU1MDlmOTM5NDBmMTk5ODA2OWI3NzExMmM1ZTcgPSAkKCc8ZGl2IGlkPSJodG1sXzYyMjY1NTA5ZjkzOTQwZjE5OTgwNjliNzcxMTJjNWU3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gSHVtYmVyIFN1bW1pdCwgIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzEyZTM5M2M2NTA4NjRiYzI5MmViZThkOTY2MTIzOGE3LnNldENvbnRlbnQoaHRtbF82MjI2NTUwOWY5Mzk0MGYxOTk4MDY5Yjc3MTEyYzVlNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81NTgwN2YyMDNiOTg0Zjc2YTA0ZGY5MDlmZDk2Mjk2My5iaW5kUG9wdXAocG9wdXBfMTJlMzkzYzY1MDg2NGJjMjkyZWJlOGQ5NjYxMjM4YTcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTkzNjM4ZDgxMDM2NDM5OWFhMDc4ODI3Mjg0MGEwOGYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MjQ3NjU5LC03OS41MzIyNDI0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJyZWQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJ5ZWxsb3ciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF83Y2VhOTAwZGM0Njg0MTcwYWQ5ODVhYWZlNmI3ZjUyNSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82MmVjMGRkOWU0MzQ0YTYzOWVlYWZkOTk3MTFlYTMyNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84MmY5ZWQ0NTE3YmY0YjNkOWZkZjY0NzE0OTc3NWI5YyA9ICQoJzxkaXYgaWQ9Imh0bWxfODJmOWVkNDUxN2JmNGIzZDlmZGY2NDcxNDk3NzViOWMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBFbWVyeSwgIEh1bWJlcmxlYSwgIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzYyZWMwZGQ5ZTQzNDRhNjM5ZWVhZmQ5OTcxMWVhMzI0LnNldENvbnRlbnQoaHRtbF84MmY5ZWQ0NTE3YmY0YjNkOWZkZjY0NzE0OTc3NWI5Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lOTM2MzhkODEwMzY0Mzk5YWEwNzg4MjcyODQwYTA4Zi5iaW5kUG9wdXAocG9wdXBfNjJlYzBkZDllNDM0NGE2MzllZWFmZDk5NzExZWEzMjQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjIyNzBlMWY4ZDI5NDMyZjgwNmE4N2VmZjcyOTFkMzMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDY4NzYsLTc5LjUxODE4ODQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzE4ZTg5NjEyMmFhMDRmODE5NTc4ODg3MTFiNDdjY2I0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzNiNTZjODkzODQxNDQzZWRiODBhNmQyYTIyYzEyNTIxID0gJCgnPGRpdiBpZD0iaHRtbF8zYjU2Yzg5Mzg0MTQ0M2VkYjgwYTZkMmEyMmMxMjUyMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFdlc3RvbiwgIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE4ZTg5NjEyMmFhMDRmODE5NTc4ODg3MTFiNDdjY2I0LnNldENvbnRlbnQoaHRtbF8zYjU2Yzg5Mzg0MTQ0M2VkYjgwYTZkMmEyMmMxMjUyMSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mMjI3MGUxZjhkMjk0MzJmODA2YTg3ZWZmNzI5MWQzMy5iaW5kUG9wdXAocG9wdXBfMThlODk2MTIyYWEwNGY4MTk1Nzg4ODcxMWI0N2NjYjQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOGI2ZmJlMDJkYWUzNDNkMTg0MjE4OTgxMWZhOTM1MzEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTYzMTksLTc5LjUzMjI0MjQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzkwNzQ1NTE0ZWQzMjRhY2JiNTI2MjYzYjAyMGQ3MGY0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzNmNDRiYTkxMjU2ZjQwOTg4YzUwYjk4YzI2MmY4YmM5ID0gJCgnPGRpdiBpZD0iaHRtbF8zZjQ0YmE5MTI1NmY0MDk4OGM1MGI5OGMyNjJmOGJjOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFdlc3Rtb3VudCwgIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTA3NDU1MTRlZDMyNGFjYmI1MjYyNjNiMDIwZDcwZjQuc2V0Q29udGVudChodG1sXzNmNDRiYTkxMjU2ZjQwOTg4YzUwYjk4YzI2MmY4YmM5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzhiNmZiZTAyZGFlMzQzZDE4NDIxODk4MTFmYTkzNTMxLmJpbmRQb3B1cChwb3B1cF85MDc0NTUxNGVkMzI0YWNiYjUyNjI2M2IwMjBkNzBmNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wMzgzMGNkMDI0ODc0OTdlYmRjMzcxODAxY2E3MTE4NSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY4ODkwNTQsLTc5LjU1NDcyNDQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzQ5OGZkNWI0NDIzOTRlYzhiZWM1ZGUxMDQ2MzJiYmUxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzU2NjYwMWQxYzg3ZjRkYWM5M2NkM2JkYjdhNmU5ZjVmID0gJCgnPGRpdiBpZD0iaHRtbF81NjY2MDFkMWM4N2Y0ZGFjOTNjZDNiZGI3YTZlOWY1ZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IEtpbmdzdmlldyBWaWxsYWdlLCAgTWFydGluIEdyb3ZlIEdhcmRlbnMsICBSaWNodmlldyBHYXJkZW5zLCAgU3QuIFBoaWxsaXBzLCAgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80OThmZDViNDQyMzk0ZWM4YmVjNWRlMTA0NjMyYmJlMS5zZXRDb250ZW50KGh0bWxfNTY2NjAxZDFjODdmNGRhYzkzY2QzYmRiN2E2ZTlmNWYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDM4MzBjZDAyNDg3NDk3ZWJkYzM3MTgwMWNhNzExODUuYmluZFBvcHVwKHBvcHVwXzQ5OGZkNWI0NDIzOTRlYzhiZWM1ZGUxMDQ2MzJiYmUxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M4MDViM2IwNWZlOTQxODViY2FiZjZmYzE3MjQ3Y2ZjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzM5NDE2Mzk5OTk5OTk2LC03OS41ODg0MzY5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzZmNzUwYmVhNjBlNTRkMzI5N2M5MjExNGVkMjY3YTJmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzg0OGI4ZGMxZWRlNzQ1NzBhZjQ3MjJlNGVlNTUwMDQ2ID0gJCgnPGRpdiBpZD0iaHRtbF84NDhiOGRjMWVkZTc0NTcwYWY0NzIyZTRlZTU1MDA0NiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IEFsYmlvbiBHYXJkZW5zLCAgQmVhdW1vbmQgSGVpZ2h0cywgIEh1bWJlcmdhdGUsICBKYW1lc3Rvd24sICBNb3VudCBPbGl2ZSwgIFNpbHZlcnN0b25lLCAgU291dGggU3RlZWxlcywgIFRoaXN0bGV0b3duLCAgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82Zjc1MGJlYTYwZTU0ZDMyOTdjOTIxMTRlZDI2N2EyZi5zZXRDb250ZW50KGh0bWxfODQ4YjhkYzFlZGU3NDU3MGFmNDcyMmU0ZWU1NTAwNDYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzgwNWIzYjA1ZmU5NDE4NWJjYWJmNmZjMTcyNDdjZmMuYmluZFBvcHVwKHBvcHVwXzZmNzUwYmVhNjBlNTRkMzI5N2M5MjExNGVkMjY3YTJmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzM2ZGMwNzIzN2Q4NDQ1Mzk4MzIzNDUxNDkwYWNjY2JmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzA2NzQ4Mjk5OTk5OTk0LC03OS41OTQwNTQ0XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogInllbGxvdyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNC41LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdjZWE5MDBkYzQ2ODQxNzBhZDk4NWFhZmU2YjdmNTI1KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzdjNTNhNmFlYWU3MzRmNDE4M2YxYjUxMDc0NmNmMDc1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RlNTYwYTY4MGMwYzQ1OTg4MDFjYzIxMjZlZjA0ZDlmID0gJCgnPGRpdiBpZD0iaHRtbF9kZTU2MGE2ODBjMGM0NTk4ODAxY2MyMTI2ZWYwNGQ5ZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IE5vcnRod2VzdCwgIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2M1M2E2YWVhZTczNGY0MTgzZjFiNTEwNzQ2Y2YwNzUuc2V0Q29udGVudChodG1sX2RlNTYwYTY4MGMwYzQ1OTg4MDFjYzIxMjZlZjA0ZDlmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM2ZGMwNzIzN2Q4NDQ1Mzk4MzIzNDUxNDkwYWNjY2JmLmJpbmRQb3B1cChwb3B1cF83YzUzYTZhZWFlNzM0ZjQxODNmMWI1MTA3NDZjZjA3NSk7CgogICAgICAgICAgICAKICAgICAgICAKPC9zY3JpcHQ+" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>




```python
# we specify which borough we want to focus on
Toronto = df7[df7.Borough == ' West Toronto'].reset_index(drop=True)
Toronto.head(10)
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>M6H</td>
      <td>West Toronto</td>
      <td>Dovercourt Village,  Dufferin</td>
      <td>43.669005</td>
      <td>-79.442259</td>
    </tr>
    <tr>
      <td>1</td>
      <td>M6J</td>
      <td>West Toronto</td>
      <td>Little Portugal,  Trinity</td>
      <td>43.647927</td>
      <td>-79.419750</td>
    </tr>
    <tr>
      <td>2</td>
      <td>M6K</td>
      <td>West Toronto</td>
      <td>Brockton,  Exhibition Place,  Parkdale Village</td>
      <td>43.636847</td>
      <td>-79.428191</td>
    </tr>
    <tr>
      <td>3</td>
      <td>M6P</td>
      <td>West Toronto</td>
      <td>High Park,  The Junction South</td>
      <td>43.661608</td>
      <td>-79.464763</td>
    </tr>
    <tr>
      <td>4</td>
      <td>M6R</td>
      <td>West Toronto</td>
      <td>Parkdale,  Roncesvalles</td>
      <td>43.648960</td>
      <td>-79.456325</td>
    </tr>
    <tr>
      <td>5</td>
      <td>M6S</td>
      <td>West Toronto</td>
      <td>Runnymede,  Swansea</td>
      <td>43.651571</td>
      <td>-79.484450</td>
    </tr>
  </tbody>
</table>
</div>




```python
# We set the location specifications
address = 'West Toronto, Toronto'

geolocator = Nominatim()
location = geolocator.geocode(address)
latitude = location.latitude
longitude = location.longitude
print('The geograpical coordinate of West Toronto are {}, {}.'.format(latitude, longitude))
```

    /home/jupyterlab/conda/envs/python/lib/python3.6/site-packages/ipykernel_launcher.py:4: DeprecationWarning: Using Nominatim with the default "geopy/1.20.0" `user_agent` is strongly discouraged, as it violates Nominatim's ToS https://operations.osmfoundation.org/policies/nominatim/ and may possibly cause 403 and 429 HTTP errors. Please specify a custom `user_agent` with `Nominatim(user_agent="my-application")` or by overriding the default `user_agent`: `geopy.geocoders.options.default_user_agent = "my-application"`. In geopy 2.0 this will become an exception.
      after removing the cwd from sys.path.


    The geograpical coordinate of West Toronto are 43.653963, -79.387207.



```python
# create map of West Toronto using latitude and longitude values
map_toronto = folium.Map(location=[latitude, longitude], zoom_start=12)

# add markers to map
for lat, lng, label in zip(Toronto['Latitude'], Toronto['Longitude'], Toronto['Neighbourhood']):
    label = folium.Popup(label, parse_html=True)
    folium.CircleMarker(
        [lat, lng],
        radius=4.5,
        popup=label,
        color='red',
        fill=True,
        fill_color='black',
        fill_opacity=0.7,
        parse_html=False).add_to(map_toronto)  
    
map_toronto
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfOGUwMzk0MzcwY2I1NGFkY2JmYWYyMzcwMTA3NDQyZGIgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzhlMDM5NDM3MGNiNTRhZGNiZmFmMjM3MDEwNzQ0MmRiIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF84ZTAzOTQzNzBjYjU0YWRjYmZhZjIzNzAxMDc0NDJkYiA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF84ZTAzOTQzNzBjYjU0YWRjYmZhZjIzNzAxMDc0NDJkYicsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjUzOTYzLC03OS4zODcyMDddLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgem9vbTogMTIsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBtYXhCb3VuZHM6IGJvdW5kcywKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGxheWVyczogW10sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB3b3JsZENvcHlKdW1wOiBmYWxzZSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGNyczogTC5DUlMuRVBTRzM4NTcKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciB0aWxlX2xheWVyX2ZlYmMyZDNhMTY1NDQ5YjJiYmM1NzUwYzA4NmQzN2U0ID0gTC50aWxlTGF5ZXIoCiAgICAgICAgICAgICAgICAnaHR0cHM6Ly97c30udGlsZS5vcGVuc3RyZWV0bWFwLm9yZy97en0ve3h9L3t5fS5wbmcnLAogICAgICAgICAgICAgICAgewogICJhdHRyaWJ1dGlvbiI6IG51bGwsCiAgImRldGVjdFJldGluYSI6IGZhbHNlLAogICJtYXhab29tIjogMTgsCiAgIm1pblpvb20iOiAxLAogICJub1dyYXAiOiBmYWxzZSwKICAic3ViZG9tYWlucyI6ICJhYmMiCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhlMDM5NDM3MGNiNTRhZGNiZmFmMjM3MDEwNzQ0MmRiKTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hZDFmZjk3YzQzN2M0MjM4OTRmYmMzM2YyMzdkMjQ1YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTAwNTEwMDAwMDAxLC03OS40NDIyNTkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogInJlZCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA0LjUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGUwMzk0MzcwY2I1NGFkY2JmYWYyMzcwMTA3NDQyZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOGRiYTMyYTgxOGNhNDM5ODg0MDVhOWNkMDBkMzYzYzYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzhkYWY2MjA1ZDViNGUxN2I0ZDFmMWI5ZjFjNjEzNjkgPSAkKCc8ZGl2IGlkPSJodG1sXzc4ZGFmNjIwNWQ1YjRlMTdiNGQxZjFiOWYxYzYxMzY5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gRG92ZXJjb3VydCBWaWxsYWdlLCAgRHVmZmVyaW48L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzhkYmEzMmE4MThjYTQzOTg4NDA1YTljZDAwZDM2M2M2LnNldENvbnRlbnQoaHRtbF83OGRhZjYyMDVkNWI0ZTE3YjRkMWYxYjlmMWM2MTM2OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hZDFmZjk3YzQzN2M0MjM4OTRmYmMzM2YyMzdkMjQ1YS5iaW5kUG9wdXAocG9wdXBfOGRiYTMyYTgxOGNhNDM5ODg0MDVhOWNkMDBkMzYzYzYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYThiYWVjMjZjZDgxNDMyZTlmYTM4Y2M2YWRlZDE2MTEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDc5MjY3MDAwMDAwMDYsLTc5LjQxOTc0OTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZTAzOTQzNzBjYjU0YWRjYmZhZjIzNzAxMDc0NDJkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82ZGE0MWY4NGI0OTI0MjAxYjA4MDhhMGJiYTQ4Mjg0YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNzkwNzcxZWQ4Njc0Y2UwYjlmNTA1NzAwMzg1ODc2YyA9ICQoJzxkaXYgaWQ9Imh0bWxfMzc5MDc3MWVkODY3NGNlMGI5ZjUwNTcwMDM4NTg3NmMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBMaXR0bGUgUG9ydHVnYWwsICBUcmluaXR5PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82ZGE0MWY4NGI0OTI0MjAxYjA4MDhhMGJiYTQ4Mjg0Yi5zZXRDb250ZW50KGh0bWxfMzc5MDc3MWVkODY3NGNlMGI5ZjUwNTcwMDM4NTg3NmMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYThiYWVjMjZjZDgxNDMyZTlmYTM4Y2M2YWRlZDE2MTEuYmluZFBvcHVwKHBvcHVwXzZkYTQxZjg0YjQ5MjQyMDFiMDgwOGEwYmJhNDgyODRiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2FmOTg4ZWM0ZTIzZTQ2MmJhYzdiOTk4Y2JlM2M1ZWJmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjM2ODQ3MiwtNzkuNDI4MTkxNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZTAzOTQzNzBjYjU0YWRjYmZhZjIzNzAxMDc0NDJkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yNThjZTMzMGI4ZTM0MTM5OWU1Yjk5NTU5NWY2ZTc5NyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84YmRjYTI2YmMyZjk0YWE5ODZiNDljYzE1MmYxNGU2YSA9ICQoJzxkaXYgaWQ9Imh0bWxfOGJkY2EyNmJjMmY5NGFhOTg2YjQ5Y2MxNTJmMTRlNmEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBCcm9ja3RvbiwgIEV4aGliaXRpb24gUGxhY2UsICBQYXJrZGFsZSBWaWxsYWdlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yNThjZTMzMGI4ZTM0MTM5OWU1Yjk5NTU5NWY2ZTc5Ny5zZXRDb250ZW50KGh0bWxfOGJkY2EyNmJjMmY5NGFhOTg2YjQ5Y2MxNTJmMTRlNmEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYWY5ODhlYzRlMjNlNDYyYmFjN2I5OThjYmUzYzVlYmYuYmluZFBvcHVwKHBvcHVwXzI1OGNlMzMwYjhlMzQxMzk5ZTViOTk1NTk1ZjZlNzk3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2YzYWVhZTkyOTIzYTQ1MWZiODhmYWY0YjY2OGM5ZmQ4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjYxNjA4MywtNzkuNDY0NzYzMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZTAzOTQzNzBjYjU0YWRjYmZhZjIzNzAxMDc0NDJkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mMzUwZDQ3ZjliODM0Y2EwODk5ZmE2Y2U1NTkyZjk0NSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zYjRiZmQ3N2ZhOGM0MjAyYjQ1NjA0ZmI0OGJkY2U2OSA9ICQoJzxkaXYgaWQ9Imh0bWxfM2I0YmZkNzdmYThjNDIwMmI0NTYwNGZiNDhiZGNlNjkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBIaWdoIFBhcmssICBUaGUgSnVuY3Rpb24gU291dGg8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2YzNTBkNDdmOWI4MzRjYTA4OTlmYTZjZTU1OTJmOTQ1LnNldENvbnRlbnQoaHRtbF8zYjRiZmQ3N2ZhOGM0MjAyYjQ1NjA0ZmI0OGJkY2U2OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mM2FlYWU5MjkyM2E0NTFmYjg4ZmFmNGI2NjhjOWZkOC5iaW5kUG9wdXAocG9wdXBfZjM1MGQ0N2Y5YjgzNGNhMDg5OWZhNmNlNTU5MmY5NDUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTRiOWZjODkxYTE3NDdjZmFhNmYzNjU3OGFmOTliODUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg5NTk3LC03OS40NTYzMjVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZTAzOTQzNzBjYjU0YWRjYmZhZjIzNzAxMDc0NDJkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kODA2NDMxMDVkYTU0OTUyOGIzMmZiYTRmYzM0Y2UxNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yMzQzMTNkOGU2MTI0YzNlYmJhNWU0ZTYwNjMxMzI2YSA9ICQoJzxkaXYgaWQ9Imh0bWxfMjM0MzEzZDhlNjEyNGMzZWJiYTVlNGU2MDYzMTMyNmEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBQYXJrZGFsZSwgIFJvbmNlc3ZhbGxlczwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDgwNjQzMTA1ZGE1NDk1MjhiMzJmYmE0ZmMzNGNlMTQuc2V0Q29udGVudChodG1sXzIzNDMxM2Q4ZTYxMjRjM2ViYmE1ZTRlNjA2MzEzMjZhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2U0YjlmYzg5MWExNzQ3Y2ZhYTZmMzY1NzhhZjk5Yjg1LmJpbmRQb3B1cChwb3B1cF9kODA2NDMxMDVkYTU0OTUyOGIzMmZiYTRmYzM0Y2UxNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81ZDExYjE5ZDQ4NDk0MDRkYTAwZDFhNzdmYmQ3NTZiYSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MTU3MDYsLTc5LjQ4NDQ0OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAicmVkIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDQuNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZTAzOTQzNzBjYjU0YWRjYmZhZjIzNzAxMDc0NDJkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jMzczZDliMTJhOWY0ODM2OWUzMTgxMDQwN2IyMTQyMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iNWE1M2UxMmVmY2Q0OWQzOTQ3MTRiMzNiYTE5ZmI4MCA9ICQoJzxkaXYgaWQ9Imh0bWxfYjVhNTNlMTJlZmNkNDlkMzk0NzE0YjMzYmExOWZiODAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBSdW5ueW1lZGUsICBTd2Fuc2VhPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jMzczZDliMTJhOWY0ODM2OWUzMTgxMDQwN2IyMTQyMi5zZXRDb250ZW50KGh0bWxfYjVhNTNlMTJlZmNkNDlkMzk0NzE0YjMzYmExOWZiODApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNWQxMWIxOWQ0ODQ5NDA0ZGEwMGQxYTc3ZmJkNzU2YmEuYmluZFBvcHVwKHBvcHVwX2MzNzNkOWIxMmE5ZjQ4MzY5ZTMxODEwNDA3YjIxNDIyKTsKCiAgICAgICAgICAgIAogICAgICAgIAo8L3NjcmlwdD4=" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>




```python
CLIENT_ID = '2RB3EBPU40AQE5YLHJSQZMLTJTTTZRT0XXK1RRSIJQ4XWOA1' # your Foursquare ID
CLIENT_SECRET = 'Z0NAH1VT2W5QTZIONKO3TZ5YOUPSIH2VJPZ5ARS1O5YWZZVW' # your Foursquare Secret
VERSION = '20180605' # Foursquare API version

print('Your credentails:')
print('CLIENT_ID: ' + CLIENT_ID)
print('CLIENT_SECRET:' + CLIENT_SECRET)
```

    Your credentails:
    CLIENT_ID: 2RB3EBPU40AQE5YLHJSQZMLTJTTTZRT0XXK1RRSIJQ4XWOA1
    CLIENT_SECRET:Z0NAH1VT2W5QTZIONKO3TZ5YOUPSIH2VJPZ5ARS1O5YWZZVW



```python
Toronto.loc[0, 'Neighbourhood']
```




    ' Dovercourt Village,  Dufferin'




```python
neighborhood_lat = Toronto.loc[0, 'Latitude'] # neighborhood latitude value
neighborhood_lng = Toronto.loc[0, 'Longitude'] # neighborhood longitude value

neighborhood_name = Toronto.loc[0, 'Neighbourhood'] # neighborhood name

print('Latitude and longitude values of {} are {}, {}.'.format(neighborhood_name, 
                                                               neighborhood_lat, 
                                                               neighborhood_lng))
```

    Latitude and longitude values of  Dovercourt Village,  Dufferin are 43.66900510000001, -79.4422593.



```python
# type your answer here
LIMIT = 100 # limit of number of venues returned by Foursquare API
radius = 500 # define radius
# create URL
url = 'https://api.foursquare.com/v2/venues/explore?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&limit={}'.format(
    CLIENT_ID, 
    CLIENT_SECRET, 
    VERSION, 
    neighborhood_lat, 
    neighborhood_lng, 
    radius, 
    LIMIT)
url # display URL
```




    'https://api.foursquare.com/v2/venues/explore?&client_id=2RB3EBPU40AQE5YLHJSQZMLTJTTTZRT0XXK1RRSIJQ4XWOA1&client_secret=Z0NAH1VT2W5QTZIONKO3TZ5YOUPSIH2VJPZ5ARS1O5YWZZVW&v=20180605&ll=43.66900510000001,-79.4422593&radius=500&limit=100'




```python
results = requests.get(url).json()
```


```python
# function that extracts the category of the venue
def get_category_type(row):
    try:
        categories_list = row['categories']
    except:
        categories_list = row['venue.categories']
        
    if len(categories_list) == 0:
        return None
    else:
        return categories_list[0]['name']
```


```python
venues = results['response']['groups'][0]['items']
    
nearby_venues = json_normalize(venues) # flatten JSON

# filter columns
filtered_columns = ['venue.name', 'venue.categories', 'venue.location.lat', 'venue.location.lng']
nearby_venues =nearby_venues.loc[:, filtered_columns]

# filter the category for each row
nearby_venues['venue.categories'] = nearby_venues.apply(get_category_type, axis=1)

# clean columns
nearby_venues.columns = [col.split(".")[-1] for col in nearby_venues.columns]

nearby_venues.head(1)
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
      <th>name</th>
      <th>categories</th>
      <th>lat</th>
      <th>lng</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>The Greater Good Bar</td>
      <td>Bar</td>
      <td>43.669409</td>
      <td>-79.439267</td>
    </tr>
  </tbody>
</table>
</div>




```python
print('{} venues were returned by Foursquare.'.format(nearby_venues.shape[0]))
```

    15 venues were returned by Foursquare.



```python
def getNearbyVenues(names, latitudes, longitudes, radius=500):
    
    venues_list=[]
    for name, lat, lng in zip(names, latitudes, longitudes):
        print(name)
            
        # create the API request URL
        url = 'https://api.foursquare.com/v2/venues/explore?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&limit={}'.format(
            CLIENT_ID, 
            CLIENT_SECRET, 
            VERSION, 
            lat, 
            lng, 
            radius, 
            LIMIT)
            
        # make the GET request
        results = requests.get(url).json()["response"]['groups'][0]['items']
        
        # return only relevant information for each nearby venue
        venues_list.append([(
            name, 
            lat, 
            lng, 
            v['venue']['name'], 
            v['venue']['location']['lat'], 
            v['venue']['location']['lng'],  
            v['venue']['categories'][0]['name']) for v in results])

    nearby_venues = pd.DataFrame([item for venue_list in venues_list for item in venue_list])
    nearby_venues.columns = ['Neighbourhood', 
                  'Neighbourhood Latitude', 
                  'Neighbourhood Longitude', 
                  'Venue', 
                  'Venue Latitude', 
                'Venue Longitude', 
                  'Venue Category']
    
    return(nearby_venues)
```


```python
# We extract the neighbourhood location and venue 
WestT_venues = getNearbyVenues(names=Toronto['Neighbourhood'],
                                   latitudes=Toronto['Latitude'],
                                   longitudes=Toronto['Longitude']
                                  )
```

     Dovercourt Village,  Dufferin
     Little Portugal,  Trinity
     Brockton,  Exhibition Place,  Parkdale Village
     High Park,  The Junction South
     Parkdale,  Roncesvalles
     Runnymede,  Swansea



```python
#Print out the shape and first 5 rows of the dataframe
print(WestT_venues.shape)
WestT_venues.head(1)
```

    (176, 7)





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
      <th>Neighbourhood</th>
      <th>Neighbourhood Latitude</th>
      <th>Neighbourhood Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Dovercourt Village,  Dufferin</td>
      <td>43.669005</td>
      <td>-79.442259</td>
      <td>The Greater Good Bar</td>
      <td>43.669409</td>
      <td>-79.439267</td>
      <td>Bar</td>
    </tr>
  </tbody>
</table>
</div>




```python
#now we grop by the Neighborhoods and count the venues in the neighborhoods
WestT_venues.groupby('Neighbourhood').count()
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
      <th>Neighbourhood Latitude</th>
      <th>Neighbourhood Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
    <tr>
      <th>Neighbourhood</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Brockton,  Exhibition Place,  Parkdale Village</td>
      <td>23</td>
      <td>23</td>
      <td>23</td>
      <td>23</td>
      <td>23</td>
      <td>23</td>
    </tr>
    <tr>
      <td>Dovercourt Village,  Dufferin</td>
      <td>15</td>
      <td>15</td>
      <td>15</td>
      <td>15</td>
      <td>15</td>
      <td>15</td>
    </tr>
    <tr>
      <td>High Park,  The Junction South</td>
      <td>25</td>
      <td>25</td>
      <td>25</td>
      <td>25</td>
      <td>25</td>
      <td>25</td>
    </tr>
    <tr>
      <td>Little Portugal,  Trinity</td>
      <td>68</td>
      <td>68</td>
      <td>68</td>
      <td>68</td>
      <td>68</td>
      <td>68</td>
    </tr>
    <tr>
      <td>Parkdale,  Roncesvalles</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
    </tr>
    <tr>
      <td>Runnymede,  Swansea</td>
      <td>31</td>
      <td>31</td>
      <td>31</td>
      <td>31</td>
      <td>31</td>
      <td>31</td>
    </tr>
  </tbody>
</table>
</div>




```python
print('There are {} uniques categories.'.format(len(WestT_venues['Venue Category'].unique())))
```

    There are 89 uniques categories.



```python
# one hot encoding
west_toronto = pd.get_dummies(WestT_venues[['Venue Category']], prefix="", prefix_sep="")

# add neighborhood column back to dataframe
west_toronto['Neighbourhood'] = WestT_venues['Neighbourhood'] 

# move neighborhood column to the first column
fixed_columns = [west_toronto.columns[-1]] + list(west_toronto.columns[:-1])
west_toronto = west_toronto[fixed_columns]

west_toronto.head(1)
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
      <th>Neighbourhood</th>
      <th>Antique Shop</th>
      <th>Art Gallery</th>
      <th>Arts &amp; Crafts Store</th>
      <th>Asian Restaurant</th>
      <th>Bagel Shop</th>
      <th>Bakery</th>
      <th>Bank</th>
      <th>Bar</th>
      <th>Bistro</th>
      <th>Bookstore</th>
      <th>Boutique</th>
      <th>Brazilian Restaurant</th>
      <th>Breakfast Spot</th>
      <th>Brewery</th>
      <th>Burrito Place</th>
      <th>Café</th>
      <th>Cajun / Creole Restaurant</th>
      <th>Caribbean Restaurant</th>
      <th>Climbing Gym</th>
      <th>Cocktail Bar</th>
      <th>Coffee Shop</th>
      <th>Convenience Store</th>
      <th>Cuban Restaurant</th>
      <th>Cupcake Shop</th>
      <th>Deli / Bodega</th>
      <th>Dessert Shop</th>
      <th>Diner</th>
      <th>Discount Store</th>
      <th>Dog Run</th>
      <th>Eastern European Restaurant</th>
      <th>Falafel Restaurant</th>
      <th>Fast Food Restaurant</th>
      <th>Fish &amp; Chips Shop</th>
      <th>Flea Market</th>
      <th>Food</th>
      <th>French Restaurant</th>
      <th>Fried Chicken Joint</th>
      <th>Furniture / Home Store</th>
      <th>Gastropub</th>
      <th>Gift Shop</th>
      <th>Gourmet Shop</th>
      <th>Greek Restaurant</th>
      <th>Grocery Store</th>
      <th>Gym</th>
      <th>Gym / Fitness Center</th>
      <th>Ice Cream Shop</th>
      <th>Indie Movie Theater</th>
      <th>Intersection</th>
      <th>Italian Restaurant</th>
      <th>Japanese Restaurant</th>
      <th>Juice Bar</th>
      <th>Korean Restaurant</th>
      <th>Latin American Restaurant</th>
      <th>Mac &amp; Cheese Joint</th>
      <th>Malay Restaurant</th>
      <th>Men's Store</th>
      <th>Mexican Restaurant</th>
      <th>Middle Eastern Restaurant</th>
      <th>Miscellaneous Shop</th>
      <th>Movie Theater</th>
      <th>Music Venue</th>
      <th>New American Restaurant</th>
      <th>Park</th>
      <th>Performing Arts Venue</th>
      <th>Pet Store</th>
      <th>Pharmacy</th>
      <th>Pizza Place</th>
      <th>Playground</th>
      <th>Portuguese Restaurant</th>
      <th>Pub</th>
      <th>Record Shop</th>
      <th>Restaurant</th>
      <th>Salon / Barbershop</th>
      <th>Sandwich Place</th>
      <th>Smoothie Shop</th>
      <th>Southern / Soul Food Restaurant</th>
      <th>Speakeasy</th>
      <th>Sports Bar</th>
      <th>Stadium</th>
      <th>Supermarket</th>
      <th>Sushi Restaurant</th>
      <th>Tapas Restaurant</th>
      <th>Tea Room</th>
      <th>Thai Restaurant</th>
      <th>Theater</th>
      <th>Vegetarian / Vegan Restaurant</th>
      <th>Vietnamese Restaurant</th>
      <th>Wine Bar</th>
      <th>Yoga Studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Dovercourt Village,  Dufferin</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#wee show the shape of the dataframe
west_toronto.shape
```




    (176, 90)




```python
#now we grop by the Neighborhoods and get the mean of the venues in the neighborhoods
WestT_grouped = west_toronto.groupby('Neighbourhood').mean().reset_index()
```


```python
#wee show the shape of the dataframe
WestT_grouped.shape
```




    (6, 90)



#### here we loop the data to show the top 5 venues in each neighborhoods


```python
num_top_venues = 5

for hood in WestT_grouped['Neighbourhood']:
    print("----"+hood+"----")
    temp = WestT_grouped[WestT_grouped['Neighbourhood'] == hood].T.reset_index()
    temp.columns = ['venue','freq']
    temp = temp.iloc[1:]
    temp['freq'] = temp['freq'].astype(float)
    temp = temp.round({'freq': 2})
    print(temp.sort_values('freq', ascending=False).reset_index(drop=True).head(num_top_venues))
    print('\n')
```

    ---- Brockton,  Exhibition Place,  Parkdale Village----
                venue  freq
    0            Café  0.13
    1  Breakfast Spot  0.09
    2     Coffee Shop  0.09
    3       Pet Store  0.04
    4      Restaurant  0.04
    
    
    ---- Dovercourt Village,  Dufferin----
                      venue  freq
    0           Supermarket  0.13
    1              Pharmacy  0.13
    2                Bakery  0.13
    3  Gym / Fitness Center  0.07
    4                  Park  0.07
    
    
    ---- High Park,  The Junction South----
                    venue  freq
    0  Mexican Restaurant  0.08
    1                 Bar  0.08
    2     Thai Restaurant  0.08
    3                Café  0.08
    4        Antique Shop  0.04
    
    
    ---- Little Portugal,  Trinity----
                  venue  freq
    0               Bar  0.12
    1       Men's Store  0.06
    2       Coffee Shop  0.06
    3  Asian Restaurant  0.04
    4              Café  0.03
    
    
    ---- Parkdale,  Roncesvalles----
                    venue  freq
    0           Gift Shop  0.14
    1      Breakfast Spot  0.14
    2  Italian Restaurant  0.07
    3           Bookstore  0.07
    4        Dessert Shop  0.07
    
    
    ---- Runnymede,  Swansea----
                    venue  freq
    0         Coffee Shop  0.10
    1                Café  0.10
    2         Pizza Place  0.06
    3  Italian Restaurant  0.06
    4    Sushi Restaurant  0.06
    
    



```python
def return_most_common_venues(row, num_top_venues):
    row_categories = row.iloc[1:]
    row_categories_sorted = row_categories.sort_values(ascending=False)
    
    return row_categories_sorted.index.values[0:num_top_venues]
```


```python
num_top_venues = 10

indicators = ['st', 'nd', 'rd']

# create columns according to number of top venues
columns = ['Neighbourhood']
for ind in np.arange(num_top_venues):
    try:
        columns.append('{}{} Most Common Venue'.format(ind+1, indicators[ind]))
    except:
        columns.append('{}th Most Common Venue'.format(ind+1))

# create a new dataframe
neighborhoods_venues_sorted = pd.DataFrame(columns=columns)
neighborhoods_venues_sorted['Neighbourhood'] = WestT_grouped['Neighbourhood']

for ind in np.arange(WestT_grouped.shape[0]):
    neighborhoods_venues_sorted.iloc[ind, 1:] = return_most_common_venues(WestT_grouped.iloc[ind, :], num_top_venues)

neighborhoods_venues_sorted.head(1)
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
      <th>Neighbourhood</th>
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
      <td>0</td>
      <td>Brockton,  Exhibition Place,  Parkdale Village</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Breakfast Spot</td>
      <td>Pet Store</td>
      <td>Italian Restaurant</td>
      <td>Convenience Store</td>
      <td>Performing Arts Venue</td>
      <td>Climbing Gym</td>
      <td>Caribbean Restaurant</td>
      <td>Burrito Place</td>
    </tr>
  </tbody>
</table>
</div>




```python
# set number of clusters
kclusters = 5

WestT_grouped_clustering = WestT_grouped.drop('Neighbourhood', 1)

# run k-means clustering
kmeans = KMeans(n_clusters=kclusters, random_state=0).fit(WestT_grouped_clustering)

# check cluster labels generated for each row in the dataframe
kmeans.labels_[0:10] 
```




    array([3, 2, 4, 1, 0, 1], dtype=int32)




```python
# add clustering labels
neighborhoods_venues_sorted.insert(0, 'Cluster Labels', kmeans.labels_)
```


```python

westT_merged = Toronto

# merge toronto_grouped with toronto_data to add latitude/longitude for each neighborhood
westT_merged = westT_merged.join(neighborhoods_venues_sorted.set_index('Neighbourhood'), on='Neighbourhood')

westT_merged.head(1) # check the last columns!
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
      <th>Postalcode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
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
      <td>0</td>
      <td>M6H</td>
      <td>West Toronto</td>
      <td>Dovercourt Village,  Dufferin</td>
      <td>43.669005</td>
      <td>-79.442259</td>
      <td>2</td>
      <td>Pharmacy</td>
      <td>Supermarket</td>
      <td>Bakery</td>
      <td>Middle Eastern Restaurant</td>
      <td>Music Venue</td>
      <td>Park</td>
      <td>Portuguese Restaurant</td>
      <td>Café</td>
      <td>Brewery</td>
      <td>Gym / Fitness Center</td>
    </tr>
  </tbody>
</table>
</div>




```python
# create map
map_clusters = folium.Map(location=[latitude, longitude], zoom_start=12)

# set color scheme for the clusters
x = np.arange(kclusters)
ys = [i + x + (i*x)**2 for i in range(kclusters)]
colors_array = cm.rainbow(np.linspace(0, 1, len(ys)))
rainbow = [colors.rgb2hex(i) for i in colors_array]

# add markers to the map
markers_colors = []
for lat, lon, poi, cluster in zip(westT_merged['Latitude'], westT_merged['Longitude'], westT_merged['Neighbourhood'], westT_merged['Cluster Labels']):
    label = folium.Popup(str(poi) + ' Cluster ' + str(cluster), parse_html=True)
    folium.CircleMarker(
        [lat, lon],
        radius=5,
        popup=label,
        color=rainbow[cluster-1],
        fill=True,
        fill_color=rainbow[cluster-1],
        fill_opacity=0.7).add_to(map_clusters)
       
map_clusters
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfMDA1MWYwMzJjZjg2NGQ5YzhlZmIxZDMxYTAxMDYzNmUgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzAwNTFmMDMyY2Y4NjRkOWM4ZWZiMWQzMWEwMTA2MzZlIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF8wMDUxZjAzMmNmODY0ZDljOGVmYjFkMzFhMDEwNjM2ZSA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF8wMDUxZjAzMmNmODY0ZDljOGVmYjFkMzFhMDEwNjM2ZScsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjUzOTYzLC03OS4zODcyMDddLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgem9vbTogMTIsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBtYXhCb3VuZHM6IGJvdW5kcywKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGxheWVyczogW10sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB3b3JsZENvcHlKdW1wOiBmYWxzZSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGNyczogTC5DUlMuRVBTRzM4NTcKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciB0aWxlX2xheWVyXzFkMmMxMzBmMzVmZjQ0MzViMThjNzlmOTBhZTliNmNiID0gTC50aWxlTGF5ZXIoCiAgICAgICAgICAgICAgICAnaHR0cHM6Ly97c30udGlsZS5vcGVuc3RyZWV0bWFwLm9yZy97en0ve3h9L3t5fS5wbmcnLAogICAgICAgICAgICAgICAgewogICJhdHRyaWJ1dGlvbiI6IG51bGwsCiAgImRldGVjdFJldGluYSI6IGZhbHNlLAogICJtYXhab29tIjogMTgsCiAgIm1pblpvb20iOiAxLAogICJub1dyYXAiOiBmYWxzZSwKICAic3ViZG9tYWlucyI6ICJhYmMiCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzAwNTFmMDMyY2Y4NjRkOWM4ZWZiMWQzMWEwMTA2MzZlKTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83MzgyMTMxODIzNzU0YjIwYWY0ODE1YWM4YWQyNmQ3NyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTAwNTEwMDAwMDAxLC03OS40NDIyNTkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiMwMGI1ZWIiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMDBiNWViIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzAwNTFmMDMyY2Y4NjRkOWM4ZWZiMWQzMWEwMTA2MzZlKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2UxYmEwZTY3YTE0MzRmY2JiMGY3NTdiNmY4MGY2NDI0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzBiOWZmM2E1M2Q0NTRmMGRiZTIzYThiN2M3ZmE2ZDNlID0gJCgnPGRpdiBpZD0iaHRtbF8wYjlmZjNhNTNkNDU0ZjBkYmUyM2E4YjdjN2ZhNmQzZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IERvdmVyY291cnQgVmlsbGFnZSwgIER1ZmZlcmluIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTFiYTBlNjdhMTQzNGZjYmIwZjc1N2I2ZjgwZjY0MjQuc2V0Q29udGVudChodG1sXzBiOWZmM2E1M2Q0NTRmMGRiZTIzYThiN2M3ZmE2ZDNlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzczODIxMzE4MjM3NTRiMjBhZjQ4MTVhYzhhZDI2ZDc3LmJpbmRQb3B1cChwb3B1cF9lMWJhMGU2N2ExNDM0ZmNiYjBmNzU3YjZmODBmNjQyNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yYTY5MjE0OGM2OGE0MWNiYjdmNTcyZTlhZjEyY2UxZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NzkyNjcwMDAwMDAwNiwtNzkuNDE5NzQ5N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjODAwMGZmIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wMDUxZjAzMmNmODY0ZDljOGVmYjFkMzFhMDEwNjM2ZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83Nzg3ZWZmYjhmZTM0NWI3YThmZTVmNTVkMzhmZGE5MSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kODU5MWU1NDg1MmQ0YmViYjc1MmJmZWQ2YjkwMjIwZiA9ICQoJzxkaXYgaWQ9Imh0bWxfZDg1OTFlNTQ4NTJkNGJlYmI3NTJiZmVkNmI5MDIyMGYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBMaXR0bGUgUG9ydHVnYWwsICBUcmluaXR5IENsdXN0ZXIgMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzc4N2VmZmI4ZmUzNDViN2E4ZmU1ZjU1ZDM4ZmRhOTEuc2V0Q29udGVudChodG1sX2Q4NTkxZTU0ODUyZDRiZWJiNzUyYmZlZDZiOTAyMjBmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzJhNjkyMTQ4YzY4YTQxY2JiN2Y1NzJlOWFmMTJjZTFkLmJpbmRQb3B1cChwb3B1cF83Nzg3ZWZmYjhmZTM0NWI3YThmZTVmNTVkMzhmZGE5MSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83OTQ2MmM1NzJhZDI0YzRhOGQ3OTVmOGU5OTAwMjNhNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYzNjg0NzIsLTc5LjQyODE5MTQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MGZmYjQiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODBmZmI0IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzAwNTFmMDMyY2Y4NjRkOWM4ZWZiMWQzMWEwMTA2MzZlKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzMwZGRjYzJjNzM3MTRlODc5Zjc3ZTQ0YjdmNjBiM2Y0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y5NjJjNTE5MDcyMjQzMDk4OWFlYmY4NTAyZjkwZDA2ID0gJCgnPGRpdiBpZD0iaHRtbF9mOTYyYzUxOTA3MjI0MzA5ODlhZWJmODUwMmY5MGQwNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IEJyb2NrdG9uLCAgRXhoaWJpdGlvbiBQbGFjZSwgIFBhcmtkYWxlIFZpbGxhZ2UgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zMGRkY2MyYzczNzE0ZTg3OWY3N2U0NGI3ZjYwYjNmNC5zZXRDb250ZW50KGh0bWxfZjk2MmM1MTkwNzIyNDMwOTg5YWViZjg1MDJmOTBkMDYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzk0NjJjNTcyYWQyNGM0YThkNzk1ZjhlOTkwMDIzYTQuYmluZFBvcHVwKHBvcHVwXzMwZGRjYzJjNzM3MTRlODc5Zjc3ZTQ0YjdmNjBiM2Y0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzFiYzUxZTZiNWNlZTQ2YjBiMGEyNzI4M2Q4NDk1ZDI5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjYxNjA4MywtNzkuNDY0NzYzMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmYjM2MCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZmIzNjAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDA1MWYwMzJjZjg2NGQ5YzhlZmIxZDMxYTAxMDYzNmUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2Q4YzE5ZGE4NDU3NDJhY2JiMDdmZjI2ZjIwNmE5YWEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGUzN2E4NjBkMGVlNGU1NGJhMTg5MjViNmQ4ODA5ZjUgPSAkKCc8ZGl2IGlkPSJodG1sXzRlMzdhODYwZDBlZTRlNTRiYTE4OTI1YjZkODgwOWY1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij4gSGlnaCBQYXJrLCAgVGhlIEp1bmN0aW9uIFNvdXRoIENsdXN0ZXIgNDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2Q4YzE5ZGE4NDU3NDJhY2JiMDdmZjI2ZjIwNmE5YWEuc2V0Q29udGVudChodG1sXzRlMzdhODYwZDBlZTRlNTRiYTE4OTI1YjZkODgwOWY1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFiYzUxZTZiNWNlZTQ2YjBiMGEyNzI4M2Q4NDk1ZDI5LmJpbmRQb3B1cChwb3B1cF8zZDhjMTlkYTg0NTc0MmFjYmIwN2ZmMjZmMjA2YTlhYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80YzZjNzhlYjQyMDg0ZTUxODdlMzJiMGJlMTdmMTU5YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODk1OTcsLTc5LjQ1NjMyNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wMDUxZjAzMmNmODY0ZDljOGVmYjFkMzFhMDEwNjM2ZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85MTFhZGFkZjAzZjM0ZmMxOWUxYzQ5OWFkYjZjNTc2MSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wODA4YzMwNThlNDE0MzI0YmM3OTUyMzc4MzRkNGQwMiA9ICQoJzxkaXYgaWQ9Imh0bWxfMDgwOGMzMDU4ZTQxNDMyNGJjNzk1MjM3ODM0ZDRkMDIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPiBQYXJrZGFsZSwgIFJvbmNlc3ZhbGxlcyBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkxMWFkYWRmMDNmMzRmYzE5ZTFjNDk5YWRiNmM1NzYxLnNldENvbnRlbnQoaHRtbF8wODA4YzMwNThlNDE0MzI0YmM3OTUyMzc4MzRkNGQwMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80YzZjNzhlYjQyMDg0ZTUxODdlMzJiMGJlMTdmMTU5Yy5iaW5kUG9wdXAocG9wdXBfOTExYWRhZGYwM2YzNGZjMTllMWM0OTlhZGI2YzU3NjEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzM5YWQxNjFjOTEwNDBmNDkyMjIzZmJjMTc2MzZlNWIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTE1NzA2LC03OS40ODQ0NDk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiM4MDAwZmYiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjODAwMGZmIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzAwNTFmMDMyY2Y4NjRkOWM4ZWZiMWQzMWEwMTA2MzZlKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzBjNTJhNjUyMTAwZjRhM2Y5ZDU1NmM2NjlhN2U4ZTRiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzE2OGRkZjU2NWIzMzRmOTA4YTcwMTQzMTgxYWUzOTkwID0gJCgnPGRpdiBpZD0iaHRtbF8xNjhkZGY1NjViMzM0ZjkwOGE3MDE0MzE4MWFlMzk5MCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+IFJ1bm55bWVkZSwgIFN3YW5zZWEgQ2x1c3RlciAxPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wYzUyYTY1MjEwMGY0YTNmOWQ1NTZjNjY5YTdlOGU0Yi5zZXRDb250ZW50KGh0bWxfMTY4ZGRmNTY1YjMzNGY5MDhhNzAxNDMxODFhZTM5OTApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzM5YWQxNjFjOTEwNDBmNDkyMjIzZmJjMTc2MzZlNWIuYmluZFBvcHVwKHBvcHVwXzBjNTJhNjUyMTAwZjRhM2Y5ZDU1NmM2NjlhN2U4ZTRiKTsKCiAgICAgICAgICAgIAogICAgICAgIAo8L3NjcmlwdD4=" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



### Cluster 1


```python
westT_merged.loc[westT_merged['Cluster Labels'] == 0, westT_merged.columns[[1] + list(range(5, westT_merged.shape[1]))]]
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
      <th>Borough</th>
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
      <td>4</td>
      <td>West Toronto</td>
      <td>0</td>
      <td>Breakfast Spot</td>
      <td>Gift Shop</td>
      <td>Bookstore</td>
      <td>Cuban Restaurant</td>
      <td>Coffee Shop</td>
      <td>Movie Theater</td>
      <td>Dog Run</td>
      <td>Eastern European Restaurant</td>
      <td>Restaurant</td>
      <td>Dessert Shop</td>
    </tr>
  </tbody>
</table>
</div>



### Cluster 2


```python
westT_merged.loc[westT_merged['Cluster Labels'] == 1, westT_merged.columns[[1] + list(range(5, westT_merged.shape[1]))]]
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
      <th>Borough</th>
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
      <td>1</td>
      <td>West Toronto</td>
      <td>1</td>
      <td>Bar</td>
      <td>Men's Store</td>
      <td>Coffee Shop</td>
      <td>Asian Restaurant</td>
      <td>Café</td>
      <td>New American Restaurant</td>
      <td>Pizza Place</td>
      <td>French Restaurant</td>
      <td>Cocktail Bar</td>
      <td>Restaurant</td>
    </tr>
    <tr>
      <td>5</td>
      <td>West Toronto</td>
      <td>1</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Pizza Place</td>
      <td>Sushi Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Gastropub</td>
      <td>Smoothie Shop</td>
      <td>Diner</td>
      <td>Latin American Restaurant</td>
      <td>Burrito Place</td>
    </tr>
  </tbody>
</table>
</div>



### Cluster 3


```python
westT_merged.loc[westT_merged['Cluster Labels'] == 2, westT_merged.columns[[1] + list(range(5, westT_merged.shape[1]))]]
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
      <th>Borough</th>
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
      <td>0</td>
      <td>West Toronto</td>
      <td>2</td>
      <td>Pharmacy</td>
      <td>Supermarket</td>
      <td>Bakery</td>
      <td>Middle Eastern Restaurant</td>
      <td>Music Venue</td>
      <td>Park</td>
      <td>Portuguese Restaurant</td>
      <td>Café</td>
      <td>Brewery</td>
      <td>Gym / Fitness Center</td>
    </tr>
  </tbody>
</table>
</div>



### Cluster 4


```python
westT_merged.loc[westT_merged['Cluster Labels'] == 3, westT_merged.columns[[1] + list(range(5, westT_merged.shape[1]))]]
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
      <th>Borough</th>
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
      <td>2</td>
      <td>West Toronto</td>
      <td>3</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Breakfast Spot</td>
      <td>Pet Store</td>
      <td>Italian Restaurant</td>
      <td>Convenience Store</td>
      <td>Performing Arts Venue</td>
      <td>Climbing Gym</td>
      <td>Caribbean Restaurant</td>
      <td>Burrito Place</td>
    </tr>
  </tbody>
</table>
</div>



### Cluster 5


```python
westT_merged.loc[westT_merged['Cluster Labels'] == 4, westT_merged.columns[[1] + list(range(5, westT_merged.shape[1]))]]
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
      <th>Borough</th>
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
      <td>3</td>
      <td>West Toronto</td>
      <td>4</td>
      <td>Mexican Restaurant</td>
      <td>Thai Restaurant</td>
      <td>Café</td>
      <td>Bar</td>
      <td>Antique Shop</td>
      <td>Speakeasy</td>
      <td>Park</td>
      <td>Diner</td>
      <td>Discount Store</td>
      <td>Cajun / Creole Restaurant</td>
    </tr>
  </tbody>
</table>
</div>


