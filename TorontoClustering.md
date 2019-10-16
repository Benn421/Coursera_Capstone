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




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;+" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>




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




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64," style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>




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




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64," style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



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


