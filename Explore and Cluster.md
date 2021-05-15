# Explore and cluster the neighborhoods in Toronto

Import the coordinates


```python
import urllib.request
import pandas as pd
import requests
from bs4 import BeautifulSoup
#get the data and create the first dataframe
url="https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M"
req = requests.get(url)
soup = BeautifulSoup(req.content, 'html.parser')

table_contents=[]
table=soup.find('table')
for row in table.findAll('td'):
    cell = {}
    if row.span.text=='Not assigned':
        pass
    else:
        cell['PostalCode'] = row.p.text[:3]
        cell['Borough'] = (row.span.text).split('(')[0]
        cell['Neighborhood'] = (((((row.span.text).split('(')[1]).strip(')')).replace(' /',',')).replace(')',' ')).strip(' ')
        table_contents.append(cell)


df=pd.DataFrame(table_contents)
df['Borough']=df['Borough'].replace({'Downtown TorontoStn A PO Boxes25 The Esplanade':'Downtown Toronto Stn A',
                                             'East TorontoBusiness reply mail Processing Centre969 Eastern':'East Toronto Business',
                                             'EtobicokeNorthwest':'Etobicoke Northwest','East YorkEast Toronto':'East York/East Toronto',
                                             'MississaugaCanada Post Gateway Processing Centre':'Mississauga'})
```


```python
df.head()
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor, Lawrence Heights</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Queen's Park</td>
      <td>Ontario Provincial Government</td>
    </tr>
  </tbody>
</table>
</div>




```python
#get the coordinates
df1=pd.read_csv("C:\\Users\\gkiou\\Downloads\\Geospatial_Coordinates.csv")
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
      <th>Postal Code</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
  </tbody>
</table>
</div>




```python
df1.rename(columns={'Postal Code' : 'PostalCode'},inplace=True)
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
      <th>PostalCode</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create the dataframe with the coordinates by merging df1 & df2
df2=pd.merge(left=df,right=df1,on='PostalCode')
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
      <td>43.725882</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor, Lawrence Heights</td>
      <td>43.718518</td>
      <td>-79.464763</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Queen's Park</td>
      <td>Ontario Provincial Government</td>
      <td>43.662301</td>
      <td>-79.389494</td>
    </tr>
  </tbody>
</table>
</div>




```python
import matplotlib.cm as cm
import matplotlib.colors as colors

#import k-means from clustering stage
from sklearn.cluster import KMeans

!conda install -c conda-forge folium=0.5.0 --yes # uncomment this line if you haven't completed the Foursquare API lab
import folium # map rendering library
!conda install -c conda-forge geopy --yes # uncomment this line if you haven't completed the Foursquare API lab
from geopy.geocoders import Nominatim # convert an address into latitude and longitude values

import requests # library to handle requests
```

    usage: conda-script.py [-h] [-V] command ...
    conda-script.py: error: unrecognized arguments: # uncomment this line if you haven't completed the Foursquare API lab
    usage: conda-script.py [-h] [-V] command ...
    conda-script.py: error: unrecognized arguments: # uncomment this line if you haven't completed the Foursquare API lab
    


```python
address = 'Toronto, ON'

geolocator = Nominatim(user_agent="toronto_explorer")
location = geolocator.geocode(address)
tor_lat = location.latitude
tor_lon  = location.longitude
print('The geograpical coordinate of Toronto are {}, {}.'.format(tor_lat, tor_lon))
```

    The geograpical coordinate of Toronto are 43.6534817, -79.3839347.
    


```python
map_toronto = folium.Map(location=[tor_lat, tor_lon], zoom_start=10)

# add markers to map
for lat, lng, label in zip(df2['Latitude'], df2['Longitude'], df2['Neighborhood']):
    label = folium.Popup(label, parse_html=True)
    folium.CircleMarker(
        [lat, lng],
        radius=5,
        popup=label,
        color='blue',
        fill=True,
        fill_color='#3186cc',
        fill_opacity=0.7,
        parse_html=False).add_to(map_toronto)  
    
map_toronto
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=%3C%21DOCTYPE%20html%3E%0A%3Chead%3E%20%20%20%20%0A%20%20%20%20%3Cmeta%20http-equiv%3D%22content-type%22%20content%3D%22text/html%3B%20charset%3DUTF-8%22%20/%3E%0A%20%20%20%20%3Cscript%3EL_PREFER_CANVAS%20%3D%20false%3B%20L_NO_TOUCH%20%3D%20false%3B%20L_DISABLE_3D%20%3D%20false%3B%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//cdn.jsdelivr.net/npm/leaflet%401.2.0/dist/leaflet.js%22%3E%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js%22%3E%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js%22%3E%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js%22%3E%3C/script%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//cdn.jsdelivr.net/npm/leaflet%401.2.0/dist/leaflet.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//maxcdn.bootstrapcdn.com/font-awesome/4.6.3/css/font-awesome.min.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//rawgit.com/python-visualization/folium/master/folium/templates/leaflet.awesome.rotate.css%22/%3E%0A%20%20%20%20%3Cstyle%3Ehtml%2C%20body%20%7Bwidth%3A%20100%25%3Bheight%3A%20100%25%3Bmargin%3A%200%3Bpadding%3A%200%3B%7D%3C/style%3E%0A%20%20%20%20%3Cstyle%3E%23map%20%7Bposition%3Aabsolute%3Btop%3A0%3Bbottom%3A0%3Bright%3A0%3Bleft%3A0%3B%7D%3C/style%3E%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%3Cstyle%3E%20%23map_dd7e120904a94a4d86f6968cdb4c5b17%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20position%20%3A%20relative%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20width%20%3A%20100.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20height%3A%20100.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20left%3A%200.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20top%3A%200.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%3C/style%3E%0A%20%20%20%20%20%20%20%20%0A%3C/head%3E%0A%3Cbody%3E%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%3Cdiv%20class%3D%22folium-map%22%20id%3D%22map_dd7e120904a94a4d86f6968cdb4c5b17%22%20%3E%3C/div%3E%0A%20%20%20%20%20%20%20%20%0A%3C/body%3E%0A%3Cscript%3E%20%20%20%20%0A%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20bounds%20%3D%20null%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20map_dd7e120904a94a4d86f6968cdb4c5b17%20%3D%20L.map%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%27map_dd7e120904a94a4d86f6968cdb4c5b17%27%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7Bcenter%3A%20%5B43.6534817%2C-79.3839347%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20zoom%3A%2010%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20maxBounds%3A%20bounds%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20layers%3A%20%5B%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20worldCopyJump%3A%20false%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20crs%3A%20L.CRS.EPSG3857%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7D%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20tile_layer_7a1c313d52a5479ba39a40adf51bfbcb%20%3D%20L.tileLayer%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%27https%3A//%7Bs%7D.tile.openstreetmap.org/%7Bz%7D/%7Bx%7D/%7By%7D.png%27%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22attribution%22%3A%20null%2C%0A%20%20%22detectRetina%22%3A%20false%2C%0A%20%20%22maxZoom%22%3A%2018%2C%0A%20%20%22minZoom%22%3A%201%2C%0A%20%20%22noWrap%22%3A%20false%2C%0A%20%20%22subdomains%22%3A%20%22abc%22%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_d92eaf8936ae44548d6d60c2891d6af7%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7532586%2C-79.3296565%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_7dd1d70705cc4c7cb5b743594eb21a30%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_5c64bf93cb4b4ab595eca5e74032eb31%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_5c64bf93cb4b4ab595eca5e74032eb31%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EParkwoods%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_7dd1d70705cc4c7cb5b743594eb21a30.setContent%28html_5c64bf93cb4b4ab595eca5e74032eb31%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_d92eaf8936ae44548d6d60c2891d6af7.bindPopup%28popup_7dd1d70705cc4c7cb5b743594eb21a30%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_215f32f8b8234618836e6738eeb17959%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.725882299999995%2C-79.31557159999998%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_557a9bc3831a460b88ccb2b917d7d5b0%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_2d175c388fd4415ea06e2a9a535fd2a2%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_2d175c388fd4415ea06e2a9a535fd2a2%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EVictoria%20Village%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_557a9bc3831a460b88ccb2b917d7d5b0.setContent%28html_2d175c388fd4415ea06e2a9a535fd2a2%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_215f32f8b8234618836e6738eeb17959.bindPopup%28popup_557a9bc3831a460b88ccb2b917d7d5b0%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_ba0838be33e5460b88450b6752d1bc4a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6542599%2C-79.3606359%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_2ad8247e7d6149c99c9a3859e68240d6%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_405e39728a1b45fdbe052d4bea070f73%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_405e39728a1b45fdbe052d4bea070f73%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERegent%20Park%2C%20Harbourfront%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_2ad8247e7d6149c99c9a3859e68240d6.setContent%28html_405e39728a1b45fdbe052d4bea070f73%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_ba0838be33e5460b88450b6752d1bc4a.bindPopup%28popup_2ad8247e7d6149c99c9a3859e68240d6%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_0824391bf5d34380a4ee38f06aec56c8%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.718517999999996%2C-79.46476329999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_91979e3d2d1f4b278bcaf584798d09d0%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_4a580ab195c149e29ba4c5aa414e9416%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_4a580ab195c149e29ba4c5aa414e9416%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ELawrence%20Manor%2C%20Lawrence%20Heights%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_91979e3d2d1f4b278bcaf584798d09d0.setContent%28html_4a580ab195c149e29ba4c5aa414e9416%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_0824391bf5d34380a4ee38f06aec56c8.bindPopup%28popup_91979e3d2d1f4b278bcaf584798d09d0%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_640372c533624d5a8184213dd628f5b2%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6623015%2C-79.3894938%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_4a61e63d1a894becb1174b4d0fb20aa2%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_e52acfe90ee94212abb5c7707981e062%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_e52acfe90ee94212abb5c7707981e062%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EOntario%20Provincial%20Government%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_4a61e63d1a894becb1174b4d0fb20aa2.setContent%28html_e52acfe90ee94212abb5c7707981e062%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_640372c533624d5a8184213dd628f5b2.bindPopup%28popup_4a61e63d1a894becb1174b4d0fb20aa2%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_a403981fa9844eae8a7ba40859379475%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6678556%2C-79.53224240000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_44ed3a9ca2294a6698663bac5fe76313%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_010c117f57b04e86aa0c8efcdb6d9957%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_010c117f57b04e86aa0c8efcdb6d9957%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EIslington%20Avenue%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_44ed3a9ca2294a6698663bac5fe76313.setContent%28html_010c117f57b04e86aa0c8efcdb6d9957%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_a403981fa9844eae8a7ba40859379475.bindPopup%28popup_44ed3a9ca2294a6698663bac5fe76313%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_34b350dfdd524391b2b9b23baf1b7286%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.806686299999996%2C-79.19435340000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_e18ab7ff96cf489e97674d15b1bbf7bb%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_e9e6505cfa3940eeabc329c7b0f375cf%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_e9e6505cfa3940eeabc329c7b0f375cf%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EMalvern%2C%20Rouge%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_e18ab7ff96cf489e97674d15b1bbf7bb.setContent%28html_e9e6505cfa3940eeabc329c7b0f375cf%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_34b350dfdd524391b2b9b23baf1b7286.bindPopup%28popup_e18ab7ff96cf489e97674d15b1bbf7bb%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_d9f927da693e4d0bbd4d10eba1a74d7c%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.745905799999996%2C-79.352188%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_0dd593e22afc4cf5a095754b98a5c543%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_7c71b6b2bb624961bf427f5029c76a2b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_7c71b6b2bb624961bf427f5029c76a2b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDon%20Mills%20North%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_0dd593e22afc4cf5a095754b98a5c543.setContent%28html_7c71b6b2bb624961bf427f5029c76a2b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_d9f927da693e4d0bbd4d10eba1a74d7c.bindPopup%28popup_0dd593e22afc4cf5a095754b98a5c543%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_1a6e8235bb1e44b1918813908cefe51d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7063972%2C-79.309937%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_e087057a8b34442fb81a99fec6aeaf44%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d54ead95b38f470d835ba58348c316e2%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d54ead95b38f470d835ba58348c316e2%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EParkview%20Hill%2C%20Woodbine%20Gardens%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_e087057a8b34442fb81a99fec6aeaf44.setContent%28html_d54ead95b38f470d835ba58348c316e2%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_1a6e8235bb1e44b1918813908cefe51d.bindPopup%28popup_e087057a8b34442fb81a99fec6aeaf44%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_31da77d07b474c02aa712f4ecee9f566%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6571618%2C-79.37893709999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_bf36005929394dd78b3fe147d410114b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_97133a10a252405c963e34567a7fb5f6%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_97133a10a252405c963e34567a7fb5f6%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EGarden%20District%2C%20Ryerson%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_bf36005929394dd78b3fe147d410114b.setContent%28html_97133a10a252405c963e34567a7fb5f6%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_31da77d07b474c02aa712f4ecee9f566.bindPopup%28popup_bf36005929394dd78b3fe147d410114b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_dd8b78028ed24d969b1628770bba0622%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.709577%2C-79.44507259999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_a0dd5e436cde4d349bb76df44153381a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_afd54c65a85640e9933e20e5e8383e38%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_afd54c65a85640e9933e20e5e8383e38%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EGlencairn%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_a0dd5e436cde4d349bb76df44153381a.setContent%28html_afd54c65a85640e9933e20e5e8383e38%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_dd8b78028ed24d969b1628770bba0622.bindPopup%28popup_a0dd5e436cde4d349bb76df44153381a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_719063558e3e4cd8be1249c597faf785%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6509432%2C-79.55472440000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_568fc89951ca45e5b88a49a8e95c040a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_9b4d258231f74cf184130d8ffb6314c7%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_9b4d258231f74cf184130d8ffb6314c7%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWest%20Deane%20Park%2C%20Princess%20Gardens%2C%20Martin%20Grove%2C%20Islington%2C%20Cloverdale%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_568fc89951ca45e5b88a49a8e95c040a.setContent%28html_9b4d258231f74cf184130d8ffb6314c7%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_719063558e3e4cd8be1249c597faf785.bindPopup%28popup_568fc89951ca45e5b88a49a8e95c040a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_ec0c680b23674af9b971e3780ff28821%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7845351%2C-79.16049709999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_1495726321cc45199196117eb3fae54b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d1af5d9a909c42a986c24093cf043b5d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d1af5d9a909c42a986c24093cf043b5d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERouge%20Hill%2C%20Port%20Union%2C%20Highland%20Creek%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_1495726321cc45199196117eb3fae54b.setContent%28html_d1af5d9a909c42a986c24093cf043b5d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_ec0c680b23674af9b971e3780ff28821.bindPopup%28popup_1495726321cc45199196117eb3fae54b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_a569b964bba643759b2bd86a9f5a0cce%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.72589970000001%2C-79.340923%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f72bb254a5a948b1b5acb2729a500861%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_63e59631226d47c28d382d757a5d4a43%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_63e59631226d47c28d382d757a5d4a43%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDon%20Mills%20South%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f72bb254a5a948b1b5acb2729a500861.setContent%28html_63e59631226d47c28d382d757a5d4a43%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_a569b964bba643759b2bd86a9f5a0cce.bindPopup%28popup_f72bb254a5a948b1b5acb2729a500861%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_149c41d7cd574c2493fd59b7d8f346d1%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.695343900000005%2C-79.3183887%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_fcbbdbc1bca6459c8c3d9c3501f47d4a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_fbc77df6371a43a2a4d4001d3e34d2b8%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_fbc77df6371a43a2a4d4001d3e34d2b8%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWoodbine%20Heights%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_fcbbdbc1bca6459c8c3d9c3501f47d4a.setContent%28html_fbc77df6371a43a2a4d4001d3e34d2b8%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_149c41d7cd574c2493fd59b7d8f346d1.bindPopup%28popup_fcbbdbc1bca6459c8c3d9c3501f47d4a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_2e9d34b4a4344628affaa7f401b730cc%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6514939%2C-79.3754179%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d99c35710e0a4f13a5c73f4728332274%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_be87be332b7e4be5bfb051ffafaa20db%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_be87be332b7e4be5bfb051ffafaa20db%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ESt.%20James%20Town%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d99c35710e0a4f13a5c73f4728332274.setContent%28html_be87be332b7e4be5bfb051ffafaa20db%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_2e9d34b4a4344628affaa7f401b730cc.bindPopup%28popup_d99c35710e0a4f13a5c73f4728332274%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_fb02408603434597a4e7ee9e8ce7e62b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6937813%2C-79.42819140000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_268238cc7eda4ea19714af59ef9da134%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_f1a9f13d5e7148c5a8c7edf6ba0b1f19%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_f1a9f13d5e7148c5a8c7edf6ba0b1f19%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHumewood-Cedarvale%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_268238cc7eda4ea19714af59ef9da134.setContent%28html_f1a9f13d5e7148c5a8c7edf6ba0b1f19%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_fb02408603434597a4e7ee9e8ce7e62b.bindPopup%28popup_268238cc7eda4ea19714af59ef9da134%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_1ddd8616443441949ee74d665b5ad146%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6435152%2C-79.57720079999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_fae4f5a238b146b4a737888234a651ce%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_3ac7ac49b50b48e1a243ef9aaf5ec64c%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_3ac7ac49b50b48e1a243ef9aaf5ec64c%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EEringate%2C%20Bloordale%20Gardens%2C%20Old%20Burnhamthorpe%2C%20Markland%20Wood%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_fae4f5a238b146b4a737888234a651ce.setContent%28html_3ac7ac49b50b48e1a243ef9aaf5ec64c%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_1ddd8616443441949ee74d665b5ad146.bindPopup%28popup_fae4f5a238b146b4a737888234a651ce%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_53ed6213b87641ddaf21c81946742928%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7635726%2C-79.1887115%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_b12ec635d42a4833b26f47a2463cbf30%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_c031a3f4a557471e88c047b904d62d9c%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_c031a3f4a557471e88c047b904d62d9c%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EGuildwood%2C%20Morningside%2C%20West%20Hill%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_b12ec635d42a4833b26f47a2463cbf30.setContent%28html_c031a3f4a557471e88c047b904d62d9c%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_53ed6213b87641ddaf21c81946742928.bindPopup%28popup_b12ec635d42a4833b26f47a2463cbf30%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_2aa47c918c764a53a18e231c1ff1f119%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.67635739999999%2C-79.2930312%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8d5a7e8c2c624d6b9fb2043d2038fd41%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_6d30dc91e2044c9091bb1722429c3c63%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_6d30dc91e2044c9091bb1722429c3c63%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThe%20Beaches%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8d5a7e8c2c624d6b9fb2043d2038fd41.setContent%28html_6d30dc91e2044c9091bb1722429c3c63%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_2aa47c918c764a53a18e231c1ff1f119.bindPopup%28popup_8d5a7e8c2c624d6b9fb2043d2038fd41%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f2443505c42e45059b2224ff72169e94%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.644770799999996%2C-79.3733064%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_fc590704636947d6991e40d8e81f2a62%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d2b31c374cbf4e8b80a3d7394c91d30e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d2b31c374cbf4e8b80a3d7394c91d30e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBerczy%20Park%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_fc590704636947d6991e40d8e81f2a62.setContent%28html_d2b31c374cbf4e8b80a3d7394c91d30e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f2443505c42e45059b2224ff72169e94.bindPopup%28popup_fc590704636947d6991e40d8e81f2a62%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_c6f6cd4f785f447b94182c2c940d704a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6890256%2C-79.453512%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d610aed32e1549578f8f09daf165162b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_3a3199d0fba745bd8d63be93c6b75a4a%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_3a3199d0fba745bd8d63be93c6b75a4a%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaledonia-Fairbanks%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d610aed32e1549578f8f09daf165162b.setContent%28html_3a3199d0fba745bd8d63be93c6b75a4a%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_c6f6cd4f785f447b94182c2c940d704a.bindPopup%28popup_d610aed32e1549578f8f09daf165162b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_dc32c62167bb4bdf8a42c8a8dea2d7af%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7709921%2C-79.21691740000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_c4dbb7f3c87b4479816cb73bff809211%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_4469006a3df5437bbd7938acb8d5af54%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_4469006a3df5437bbd7938acb8d5af54%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWoburn%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_c4dbb7f3c87b4479816cb73bff809211.setContent%28html_4469006a3df5437bbd7938acb8d5af54%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_dc32c62167bb4bdf8a42c8a8dea2d7af.bindPopup%28popup_c4dbb7f3c87b4479816cb73bff809211%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_eb783faaf6bb4e87bd31f23e0c07dd67%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7090604%2C-79.3634517%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_e255da5e7d3645d589dbcb1288dd9128%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_5fef239b30b14070802d59452ee00d77%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_5fef239b30b14070802d59452ee00d77%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ELeaside%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_e255da5e7d3645d589dbcb1288dd9128.setContent%28html_5fef239b30b14070802d59452ee00d77%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_eb783faaf6bb4e87bd31f23e0c07dd67.bindPopup%28popup_e255da5e7d3645d589dbcb1288dd9128%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_fd723010149849419428e4d81bc728d7%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6579524%2C-79.3873826%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_5bbf325e09714607ac31e3f2a8c6ed63%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_8b49baad02764ce38935aef0ef2676f6%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_8b49baad02764ce38935aef0ef2676f6%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECentral%20Bay%20Street%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_5bbf325e09714607ac31e3f2a8c6ed63.setContent%28html_8b49baad02764ce38935aef0ef2676f6%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_fd723010149849419428e4d81bc728d7.bindPopup%28popup_5bbf325e09714607ac31e3f2a8c6ed63%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_fc10d5f8235740acac52fea912cdf5e2%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.669542%2C-79.4225637%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d146eadb10c14a38914bab4aa5188f50%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_05dc47e8cf4343b49368e1c6cce1f4dd%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_05dc47e8cf4343b49368e1c6cce1f4dd%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EChristie%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d146eadb10c14a38914bab4aa5188f50.setContent%28html_05dc47e8cf4343b49368e1c6cce1f4dd%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_fc10d5f8235740acac52fea912cdf5e2.bindPopup%28popup_d146eadb10c14a38914bab4aa5188f50%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_d7d5a392b33c4723948a7bc3db269533%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.773136%2C-79.23947609999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_4e3cd021b5504c7d967dc4412953ad09%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_8aaf4a8ce81a4dbcbf3cbae63e049025%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_8aaf4a8ce81a4dbcbf3cbae63e049025%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECedarbrae%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_4e3cd021b5504c7d967dc4412953ad09.setContent%28html_8aaf4a8ce81a4dbcbf3cbae63e049025%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_d7d5a392b33c4723948a7bc3db269533.bindPopup%28popup_4e3cd021b5504c7d967dc4412953ad09%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_bb5b24212c3040d0accbf259e9e2e8ec%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.8037622%2C-79.3634517%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_bed92bab134748c38fcf3567aa024356%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_dce2f3fe0a6c42afa8ad71b05ef1100b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_dce2f3fe0a6c42afa8ad71b05ef1100b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHillcrest%20Village%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_bed92bab134748c38fcf3567aa024356.setContent%28html_dce2f3fe0a6c42afa8ad71b05ef1100b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_bb5b24212c3040d0accbf259e9e2e8ec.bindPopup%28popup_bed92bab134748c38fcf3567aa024356%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_07fa881cc3924674a0060611bf9b14f8%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7543283%2C-79.4422593%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_7ec0eeee8fb749e3a4d0ee4b1bb4c459%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_19065a8084604204a55b350d4c87a4c9%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_19065a8084604204a55b350d4c87a4c9%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBathurst%20Manor%2C%20Wilson%20Heights%2C%20Downsview%20North%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_7ec0eeee8fb749e3a4d0ee4b1bb4c459.setContent%28html_19065a8084604204a55b350d4c87a4c9%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_07fa881cc3924674a0060611bf9b14f8.bindPopup%28popup_7ec0eeee8fb749e3a4d0ee4b1bb4c459%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_a06554e70bcd4fcdb93b5bf1ab064a1b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7053689%2C-79.34937190000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_aab5c02af81c4d1e98969b6eb5851b78%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_478d464c366e4db7854f23e978726033%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_478d464c366e4db7854f23e978726033%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThorncliffe%20Park%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_aab5c02af81c4d1e98969b6eb5851b78.setContent%28html_478d464c366e4db7854f23e978726033%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_a06554e70bcd4fcdb93b5bf1ab064a1b.bindPopup%28popup_aab5c02af81c4d1e98969b6eb5851b78%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_ee390e403dcc43b4bb9d54982278c314%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.65057120000001%2C-79.3845675%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_0817be41431c4c94b1d72971a807cd47%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_21a0cfd3a7d04b5cbb3022b6a6ec76e3%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_21a0cfd3a7d04b5cbb3022b6a6ec76e3%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERichmond%2C%20Adelaide%2C%20King%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_0817be41431c4c94b1d72971a807cd47.setContent%28html_21a0cfd3a7d04b5cbb3022b6a6ec76e3%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_ee390e403dcc43b4bb9d54982278c314.bindPopup%28popup_0817be41431c4c94b1d72971a807cd47%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8973219fe41742a4b22fdadfd95c3382%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.66900510000001%2C-79.4422593%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_95cb9f2e158d446592c7ecfe53edbacf%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_50bdf6a3e8c74c8e9c26285c4ce3208f%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_50bdf6a3e8c74c8e9c26285c4ce3208f%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDufferin%2C%20Dovercourt%20Village%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_95cb9f2e158d446592c7ecfe53edbacf.setContent%28html_50bdf6a3e8c74c8e9c26285c4ce3208f%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8973219fe41742a4b22fdadfd95c3382.bindPopup%28popup_95cb9f2e158d446592c7ecfe53edbacf%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_6c1a26de96e34d2880664ccd640c0494%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7447342%2C-79.23947609999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_56451c828f314ef79947e1fb8900ca5f%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_aac036481bb348f98c7b75b7759261c2%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_aac036481bb348f98c7b75b7759261c2%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EScarborough%20Village%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_56451c828f314ef79947e1fb8900ca5f.setContent%28html_aac036481bb348f98c7b75b7759261c2%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_6c1a26de96e34d2880664ccd640c0494.bindPopup%28popup_56451c828f314ef79947e1fb8900ca5f%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f128aba433924f53b4110392814cbf19%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7785175%2C-79.3465557%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_3166f6f679994de5b90d4f8129ea4b47%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_e008378c1cc748b1bf33670aae005887%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_e008378c1cc748b1bf33670aae005887%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EFairview%2C%20Henry%20Farm%2C%20Oriole%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_3166f6f679994de5b90d4f8129ea4b47.setContent%28html_e008378c1cc748b1bf33670aae005887%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f128aba433924f53b4110392814cbf19.bindPopup%28popup_3166f6f679994de5b90d4f8129ea4b47%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_1e70f2b153ae4e9bbed3fd44e44fdecd%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7679803%2C-79.48726190000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_9d07f1986e554b5e9459eb2acfe0059d%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_0f70e76b9b66412fb18984d7b5a7d575%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_0f70e76b9b66412fb18984d7b5a7d575%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ENorthwood%20Park%2C%20York%20University%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_9d07f1986e554b5e9459eb2acfe0059d.setContent%28html_0f70e76b9b66412fb18984d7b5a7d575%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_1e70f2b153ae4e9bbed3fd44e44fdecd.bindPopup%28popup_9d07f1986e554b5e9459eb2acfe0059d%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8c6d8bed8d0d48b98f216909d8938440%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.685347%2C-79.3381065%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8787141ee16e4a5382c8d317337554f1%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a6fda5cc967a4a1eb1e3e301cc58a7c5%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a6fda5cc967a4a1eb1e3e301cc58a7c5%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThe%20Danforth%20%20East%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8787141ee16e4a5382c8d317337554f1.setContent%28html_a6fda5cc967a4a1eb1e3e301cc58a7c5%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8c6d8bed8d0d48b98f216909d8938440.bindPopup%28popup_8787141ee16e4a5382c8d317337554f1%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_9f2a2c1aa59f4b78813ee93f9b3195d6%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6408157%2C-79.38175229999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_4bde61aadc994114b0f2a171198749ca%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_b0ae1b2ce3f74035940e9ec2d5ddfb92%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_b0ae1b2ce3f74035940e9ec2d5ddfb92%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHarbourfront%20East%2C%20Union%20Station%2C%20Toronto%20Islands%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_4bde61aadc994114b0f2a171198749ca.setContent%28html_b0ae1b2ce3f74035940e9ec2d5ddfb92%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_9f2a2c1aa59f4b78813ee93f9b3195d6.bindPopup%28popup_4bde61aadc994114b0f2a171198749ca%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_b93c2918d6a440379a28e23a31261411%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.647926700000006%2C-79.4197497%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f988d2b0943443f29b0f6c65ad273183%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a25274a2d3df45f68be515bb5e3dc76f%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a25274a2d3df45f68be515bb5e3dc76f%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ELittle%20Portugal%2C%20Trinity%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f988d2b0943443f29b0f6c65ad273183.setContent%28html_a25274a2d3df45f68be515bb5e3dc76f%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_b93c2918d6a440379a28e23a31261411.bindPopup%28popup_f988d2b0943443f29b0f6c65ad273183%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f4ef6005502643bbabebada445644a54%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7279292%2C-79.26202940000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_a739a495ae4d408d8173ae4522b74d96%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_7877618b10a1465489f58fa8fbca0759%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_7877618b10a1465489f58fa8fbca0759%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EKennedy%20Park%2C%20Ionview%2C%20East%20Birchmount%20Park%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_a739a495ae4d408d8173ae4522b74d96.setContent%28html_7877618b10a1465489f58fa8fbca0759%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f4ef6005502643bbabebada445644a54.bindPopup%28popup_a739a495ae4d408d8173ae4522b74d96%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_38b5a09bee444c0dbe71f98d26ea3081%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7869473%2C-79.385975%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_b251b83f32a344d7a43c5f1fd36f4889%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_ec94df325ac941ae9f1172f682735461%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_ec94df325ac941ae9f1172f682735461%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBayview%20Village%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_b251b83f32a344d7a43c5f1fd36f4889.setContent%28html_ec94df325ac941ae9f1172f682735461%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_38b5a09bee444c0dbe71f98d26ea3081.bindPopup%28popup_b251b83f32a344d7a43c5f1fd36f4889%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_e661b0b68fb74c749a9427ea9d878d56%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.737473200000004%2C-79.46476329999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_262da686b57546ad950691e49597415f%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_952bf35b56014deab6fa1dde407688eb%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_952bf35b56014deab6fa1dde407688eb%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDownsview%20East%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_262da686b57546ad950691e49597415f.setContent%28html_952bf35b56014deab6fa1dde407688eb%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_e661b0b68fb74c749a9427ea9d878d56.bindPopup%28popup_262da686b57546ad950691e49597415f%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_e679ad9e46df4f38874ea397c5fa9326%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6795571%2C-79.352188%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_b922e712624e45ae84998b31e5a54b19%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d7624fb9e5a7459ea50c7d93f7e2e286%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d7624fb9e5a7459ea50c7d93f7e2e286%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThe%20Danforth%20West%2C%20Riverdale%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_b922e712624e45ae84998b31e5a54b19.setContent%28html_d7624fb9e5a7459ea50c7d93f7e2e286%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_e679ad9e46df4f38874ea397c5fa9326.bindPopup%28popup_b922e712624e45ae84998b31e5a54b19%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_e492796ab38545d996f2994553d0e221%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6471768%2C-79.38157640000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_823297596300444a8e35360626739d71%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d18844b3209c4a5b828df99943ef6a4d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d18844b3209c4a5b828df99943ef6a4d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EToronto%20Dominion%20Centre%2C%20Design%20Exchange%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_823297596300444a8e35360626739d71.setContent%28html_d18844b3209c4a5b828df99943ef6a4d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_e492796ab38545d996f2994553d0e221.bindPopup%28popup_823297596300444a8e35360626739d71%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_072726f0d570414893897198f04bd913%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6368472%2C-79.42819140000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8bb63cb9e5ab4200a87a89b0688f90ba%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_62bb18b515114b329d6458b1fbe03b16%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_62bb18b515114b329d6458b1fbe03b16%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBrockton%2C%20Parkdale%20Village%2C%20Exhibition%20Place%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8bb63cb9e5ab4200a87a89b0688f90ba.setContent%28html_62bb18b515114b329d6458b1fbe03b16%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_072726f0d570414893897198f04bd913.bindPopup%28popup_8bb63cb9e5ab4200a87a89b0688f90ba%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_7a052e521ef64617a3709f6d6bf3ecc4%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.711111700000004%2C-79.2845772%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_9ee496df00ad49048604dd48d0f0be8e%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_00081b78affd4b3baf586a61cdb63ea8%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_00081b78affd4b3baf586a61cdb63ea8%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EGolden%20Mile%2C%20Clairlea%2C%20Oakridge%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_9ee496df00ad49048604dd48d0f0be8e.setContent%28html_00081b78affd4b3baf586a61cdb63ea8%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_7a052e521ef64617a3709f6d6bf3ecc4.bindPopup%28popup_9ee496df00ad49048604dd48d0f0be8e%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_70cdeeba75f046cbae2f714fdb153e9a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7574902%2C-79.37471409999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_23b12de61ff54b5da6c299f0c7637fc7%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_09d780308e3e473f9c8d07b94447e22c%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_09d780308e3e473f9c8d07b94447e22c%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EYork%20Mills%2C%20Silver%20Hills%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_23b12de61ff54b5da6c299f0c7637fc7.setContent%28html_09d780308e3e473f9c8d07b94447e22c%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_70cdeeba75f046cbae2f714fdb153e9a.bindPopup%28popup_23b12de61ff54b5da6c299f0c7637fc7%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_0c78debb4eb64e6693ad992c2ef0e0b7%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7390146%2C-79.5069436%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_cb28149b82734ea1833d5b571a7ae53d%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a1896c2abb8a41fd9ae3f1b10d1942b6%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a1896c2abb8a41fd9ae3f1b10d1942b6%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDownsview%20West%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_cb28149b82734ea1833d5b571a7ae53d.setContent%28html_a1896c2abb8a41fd9ae3f1b10d1942b6%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_0c78debb4eb64e6693ad992c2ef0e0b7.bindPopup%28popup_cb28149b82734ea1833d5b571a7ae53d%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_e72131bc996a4a4186c7b8a267dc864a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6689985%2C-79.31557159999998%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d9ddaff4312242c09f06aa94872a0e83%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_350e665b01b94941be56f58561c02241%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_350e665b01b94941be56f58561c02241%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EIndia%20Bazaar%2C%20The%20Beaches%20West%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d9ddaff4312242c09f06aa94872a0e83.setContent%28html_350e665b01b94941be56f58561c02241%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_e72131bc996a4a4186c7b8a267dc864a.bindPopup%28popup_d9ddaff4312242c09f06aa94872a0e83%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_92e04b8a7aa54221b90cbb7cf80e4c93%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6481985%2C-79.37981690000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8794ef57896c4098a2a69e844db9ea2e%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_6c2128e292a8430da8a13a9274d103b6%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_6c2128e292a8430da8a13a9274d103b6%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECommerce%20Court%2C%20Victoria%20Hotel%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8794ef57896c4098a2a69e844db9ea2e.setContent%28html_6c2128e292a8430da8a13a9274d103b6%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_92e04b8a7aa54221b90cbb7cf80e4c93.bindPopup%28popup_8794ef57896c4098a2a69e844db9ea2e%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_b9fac4c4587c42939347b0ba9b597d9b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.713756200000006%2C-79.4900738%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d55b7f3354e2468095e12e2e24a18a2a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_25bab36bd00a44bdbfa7dce5cb637b5d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_25bab36bd00a44bdbfa7dce5cb637b5d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ENorth%20Park%2C%20Maple%20Leaf%20Park%2C%20Upwood%20Park%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d55b7f3354e2468095e12e2e24a18a2a.setContent%28html_25bab36bd00a44bdbfa7dce5cb637b5d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_b9fac4c4587c42939347b0ba9b597d9b.bindPopup%28popup_d55b7f3354e2468095e12e2e24a18a2a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_67b7a756ba354c67b03849ae57c57565%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7563033%2C-79.56596329999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_984b4b825b474d6c87a4a109d8807037%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_930b063ea9334311881f3e1b08f9dc70%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_930b063ea9334311881f3e1b08f9dc70%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHumber%20Summit%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_984b4b825b474d6c87a4a109d8807037.setContent%28html_930b063ea9334311881f3e1b08f9dc70%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_67b7a756ba354c67b03849ae57c57565.bindPopup%28popup_984b4b825b474d6c87a4a109d8807037%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_72f4a2117b3c47c3989dd08ae74f5c1b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.716316%2C-79.23947609999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_b96def446bca478a982c848aa16ce50f%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d8c2ce8452714897a5cdcb163a1b3573%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d8c2ce8452714897a5cdcb163a1b3573%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECliffside%2C%20Cliffcrest%2C%20Scarborough%20Village%20West%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_b96def446bca478a982c848aa16ce50f.setContent%28html_d8c2ce8452714897a5cdcb163a1b3573%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_72f4a2117b3c47c3989dd08ae74f5c1b.bindPopup%28popup_b96def446bca478a982c848aa16ce50f%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_27835b469deb4b8ca4173a95c4d9fcf5%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.789053%2C-79.40849279999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_33a40f19910f4d7c8ca725c5434ed74c%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_3cec6489934244a3914eac30f9fc4a7b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_3cec6489934244a3914eac30f9fc4a7b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWillowdale%2C%20Newtonbrook%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_33a40f19910f4d7c8ca725c5434ed74c.setContent%28html_3cec6489934244a3914eac30f9fc4a7b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_27835b469deb4b8ca4173a95c4d9fcf5.bindPopup%28popup_33a40f19910f4d7c8ca725c5434ed74c%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4811ef10e7ce4d5588379069f033523a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7284964%2C-79.49569740000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_76452281adb941fc8d1139c6d157e7f6%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_84ec011883f04fd8bcc4c5df3290741f%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_84ec011883f04fd8bcc4c5df3290741f%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDownsview%20Central%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_76452281adb941fc8d1139c6d157e7f6.setContent%28html_84ec011883f04fd8bcc4c5df3290741f%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4811ef10e7ce4d5588379069f033523a.bindPopup%28popup_76452281adb941fc8d1139c6d157e7f6%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_344fce771642454091d80512a9012b80%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6595255%2C-79.340923%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_e42ca9ef264645b2b16cc33c7a95f9cc%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_488121da54d741a6a0bc050afb1d7c5e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_488121da54d741a6a0bc050afb1d7c5e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EStudio%20District%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_e42ca9ef264645b2b16cc33c7a95f9cc.setContent%28html_488121da54d741a6a0bc050afb1d7c5e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_344fce771642454091d80512a9012b80.bindPopup%28popup_e42ca9ef264645b2b16cc33c7a95f9cc%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_19a40b6d8c634c9181cc2ca3fd20f54f%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7332825%2C-79.4197497%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_81dd174e21df46cdb1966c4c4662b52e%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_3f11809cf3644fa9b8d8c2161a7f16ec%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_3f11809cf3644fa9b8d8c2161a7f16ec%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBedford%20Park%2C%20Lawrence%20Manor%20East%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_81dd174e21df46cdb1966c4c4662b52e.setContent%28html_3f11809cf3644fa9b8d8c2161a7f16ec%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_19a40b6d8c634c9181cc2ca3fd20f54f.bindPopup%28popup_81dd174e21df46cdb1966c4c4662b52e%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_60c1ec5bab5f4252b05d1a93aa7a5fb9%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6911158%2C-79.47601329999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_6bda35127aaa42c3b0df5d5040bafe5d%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_49cb5c27610b4ead8455731d4fd927d7%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_49cb5c27610b4ead8455731d4fd927d7%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDel%20Ray%2C%20Mount%20Dennis%2C%20Keelsdale%20and%20Silverthorn%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_6bda35127aaa42c3b0df5d5040bafe5d.setContent%28html_49cb5c27610b4ead8455731d4fd927d7%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_60c1ec5bab5f4252b05d1a93aa7a5fb9.bindPopup%28popup_6bda35127aaa42c3b0df5d5040bafe5d%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_fb2cf7f62dca40bcbd21c221940cd6f7%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7247659%2C-79.53224240000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_c2da7540a2124c1890e186ab515dff73%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_5a4050f2f6c449668082221ee91dc5a4%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_5a4050f2f6c449668082221ee91dc5a4%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHumberlea%2C%20Emery%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_c2da7540a2124c1890e186ab515dff73.setContent%28html_5a4050f2f6c449668082221ee91dc5a4%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_fb2cf7f62dca40bcbd21c221940cd6f7.bindPopup%28popup_c2da7540a2124c1890e186ab515dff73%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_980b4e127ea2479180ea58ba1b1df8dd%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.692657000000004%2C-79.2648481%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_0364fcaa003543618ad066c392df4470%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_b5681a5850014c53aed0b148131ffdc8%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_b5681a5850014c53aed0b148131ffdc8%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBirch%20Cliff%2C%20Cliffside%20West%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_0364fcaa003543618ad066c392df4470.setContent%28html_b5681a5850014c53aed0b148131ffdc8%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_980b4e127ea2479180ea58ba1b1df8dd.bindPopup%28popup_0364fcaa003543618ad066c392df4470%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_39b7d8ca88374b81b4145fd9d776f3cc%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7701199%2C-79.40849279999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_457c962540b84facbe162fc3eb5e7615%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d967e411241e46339557005c41fe9d1e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d967e411241e46339557005c41fe9d1e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWillowdale%20South%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_457c962540b84facbe162fc3eb5e7615.setContent%28html_d967e411241e46339557005c41fe9d1e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_39b7d8ca88374b81b4145fd9d776f3cc.bindPopup%28popup_457c962540b84facbe162fc3eb5e7615%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_2911d7c28c864e2999a261a5c4d26b31%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7616313%2C-79.52099940000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_fae8fa7a529847c69ff06e842e3450ab%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_5369dea498654487802ebadb4e7982bb%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_5369dea498654487802ebadb4e7982bb%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDownsview%20Northwest%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_fae8fa7a529847c69ff06e842e3450ab.setContent%28html_5369dea498654487802ebadb4e7982bb%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_2911d7c28c864e2999a261a5c4d26b31.bindPopup%28popup_fae8fa7a529847c69ff06e842e3450ab%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8ba99dd9e4d94c2a816a4c214ca6b132%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7280205%2C-79.3887901%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_bbf16d693c3c4f61acee376309164625%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_6912cf847b4245f491603f7559678d53%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_6912cf847b4245f491603f7559678d53%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ELawrence%20Park%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_bbf16d693c3c4f61acee376309164625.setContent%28html_6912cf847b4245f491603f7559678d53%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8ba99dd9e4d94c2a816a4c214ca6b132.bindPopup%28popup_bbf16d693c3c4f61acee376309164625%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8bb6902fe3884aa5acca4650e734480d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7116948%2C-79.41693559999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_7632e53e3c1348aea3fca65b2f5be860%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_b6ab6df74f6c44eca95ce958272bce20%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_b6ab6df74f6c44eca95ce958272bce20%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERoselawn%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_7632e53e3c1348aea3fca65b2f5be860.setContent%28html_b6ab6df74f6c44eca95ce958272bce20%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8bb6902fe3884aa5acca4650e734480d.bindPopup%28popup_7632e53e3c1348aea3fca65b2f5be860%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_0b9d8374ce5e4303afd8c72d3b4bcd4d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.67318529999999%2C-79.48726190000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_3b98594245874a5aaef6310e4acf85b7%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_e67c9f83654d4fe2ae26848d15350160%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_e67c9f83654d4fe2ae26848d15350160%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERunnymede%2C%20The%20Junction%20North%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_3b98594245874a5aaef6310e4acf85b7.setContent%28html_e67c9f83654d4fe2ae26848d15350160%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_0b9d8374ce5e4303afd8c72d3b4bcd4d.bindPopup%28popup_3b98594245874a5aaef6310e4acf85b7%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_e80233500d324c6882db2e7ac8f695f3%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.706876%2C-79.51818840000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_45a59612e04a4d1c8f38a4fe385e71d3%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_e2cc70a4570d4b579ba36dca2bfeca0f%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_e2cc70a4570d4b579ba36dca2bfeca0f%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWeston%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_45a59612e04a4d1c8f38a4fe385e71d3.setContent%28html_e2cc70a4570d4b579ba36dca2bfeca0f%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_e80233500d324c6882db2e7ac8f695f3.bindPopup%28popup_45a59612e04a4d1c8f38a4fe385e71d3%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_37cab51bbb29401aaaed3a4de7e22bde%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7574096%2C-79.27330400000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_34bb6efbda3f4bffa1fea73370a95d0b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_6df744193e2840768cc7052c756529e5%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_6df744193e2840768cc7052c756529e5%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDorset%20Park%2C%20Wexford%20Heights%2C%20Scarborough%20Town%20Centre%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_34bb6efbda3f4bffa1fea73370a95d0b.setContent%28html_6df744193e2840768cc7052c756529e5%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_37cab51bbb29401aaaed3a4de7e22bde.bindPopup%28popup_34bb6efbda3f4bffa1fea73370a95d0b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8194d172fb85489787c152f599c7a147%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.752758299999996%2C-79.4000493%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_5a04bf36b78449a080b9d057f8ba65ec%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_3a59ccf132af4d859a9fc08151106945%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_3a59ccf132af4d859a9fc08151106945%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EYork%20Mills%20West%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_5a04bf36b78449a080b9d057f8ba65ec.setContent%28html_3a59ccf132af4d859a9fc08151106945%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8194d172fb85489787c152f599c7a147.bindPopup%28popup_5a04bf36b78449a080b9d057f8ba65ec%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f6f18ee778264c1f990997140c4cb27d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7127511%2C-79.3901975%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f53620eeb30d4b93be0f9a44b884b73c%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_dcd0e74c7fdb40ed8cba3e25f6bfdf4b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_dcd0e74c7fdb40ed8cba3e25f6bfdf4b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDavisville%20North%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f53620eeb30d4b93be0f9a44b884b73c.setContent%28html_dcd0e74c7fdb40ed8cba3e25f6bfdf4b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f6f18ee778264c1f990997140c4cb27d.bindPopup%28popup_f53620eeb30d4b93be0f9a44b884b73c%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_3ead2a6477b0438784c5305d778e81ca%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6969476%2C-79.41130720000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_441dd0746d804cc382abf7721928f2f5%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_182b844411e44935b1e537974dc4e6e3%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_182b844411e44935b1e537974dc4e6e3%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EForest%20Hill%20North%20%26amp%3B%20West%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_441dd0746d804cc382abf7721928f2f5.setContent%28html_182b844411e44935b1e537974dc4e6e3%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_3ead2a6477b0438784c5305d778e81ca.bindPopup%28popup_441dd0746d804cc382abf7721928f2f5%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_0f2b50e25b954a91b329c3fb488b8c7d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6616083%2C-79.46476329999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_5aa278f6ec534e8ba27c94d670aecd9e%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_1f7b06e2440746598b4df639e0b72370%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_1f7b06e2440746598b4df639e0b72370%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHigh%20Park%2C%20The%20Junction%20South%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_5aa278f6ec534e8ba27c94d670aecd9e.setContent%28html_1f7b06e2440746598b4df639e0b72370%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_0f2b50e25b954a91b329c3fb488b8c7d.bindPopup%28popup_5aa278f6ec534e8ba27c94d670aecd9e%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_bdaac9f70b8943fda1e20733e2cfca94%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.696319%2C-79.53224240000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_73d1a1dce5a741bb899f57b961b7b61b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_ab3eda331b454d3e8480f845aeaf36a5%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_ab3eda331b454d3e8480f845aeaf36a5%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWestmount%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_73d1a1dce5a741bb899f57b961b7b61b.setContent%28html_ab3eda331b454d3e8480f845aeaf36a5%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_bdaac9f70b8943fda1e20733e2cfca94.bindPopup%28popup_73d1a1dce5a741bb899f57b961b7b61b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_56af6554c4964e22bcb6ab47a660d425%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.750071500000004%2C-79.2958491%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_4a14477cc7e34435b475b05df990c890%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_fe27a20e4d1e494ab686b6bea80bdfe9%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_fe27a20e4d1e494ab686b6bea80bdfe9%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWexford%2C%20Maryvale%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_4a14477cc7e34435b475b05df990c890.setContent%28html_fe27a20e4d1e494ab686b6bea80bdfe9%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_56af6554c4964e22bcb6ab47a660d425.bindPopup%28popup_4a14477cc7e34435b475b05df990c890%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_b47db5d020934a6e9c5abc6913f4794b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7827364%2C-79.4422593%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_2580b25d0b9649478f2b4531b3a42986%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_0919aa005786407884729699588fa188%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_0919aa005786407884729699588fa188%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWillowdale%20West%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_2580b25d0b9649478f2b4531b3a42986.setContent%28html_0919aa005786407884729699588fa188%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_b47db5d020934a6e9c5abc6913f4794b.bindPopup%28popup_2580b25d0b9649478f2b4531b3a42986%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_d5199818423e4f3da3fd7c04b66dc1f3%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7153834%2C-79.40567840000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_80dcafb9b77542bb9b94c62a1bdadd2a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_1abf8425a118460697a26c51429b00df%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_1abf8425a118460697a26c51429b00df%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ENorth%20Toronto%20West%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_80dcafb9b77542bb9b94c62a1bdadd2a.setContent%28html_1abf8425a118460697a26c51429b00df%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_d5199818423e4f3da3fd7c04b66dc1f3.bindPopup%28popup_80dcafb9b77542bb9b94c62a1bdadd2a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_471fe6d91d244f8a93652ad423efdeec%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6727097%2C-79.40567840000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_1415cb3d7ae249eebfbff4e4542d8c5c%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_9b112f60d5a149c7a9103c0f97756215%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_9b112f60d5a149c7a9103c0f97756215%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThe%20Annex%2C%20North%20Midtown%2C%20Yorkville%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_1415cb3d7ae249eebfbff4e4542d8c5c.setContent%28html_9b112f60d5a149c7a9103c0f97756215%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_471fe6d91d244f8a93652ad423efdeec.bindPopup%28popup_1415cb3d7ae249eebfbff4e4542d8c5c%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_6fe64449a77f4114857f5a256347dacb%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6489597%2C-79.456325%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_322b533abb884b9fb2bd9cf59f3d84f4%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_59c3cd8f2c214acbacbe061635f02ef2%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_59c3cd8f2c214acbacbe061635f02ef2%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EParkdale%2C%20Roncesvalles%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_322b533abb884b9fb2bd9cf59f3d84f4.setContent%28html_59c3cd8f2c214acbacbe061635f02ef2%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_6fe64449a77f4114857f5a256347dacb.bindPopup%28popup_322b533abb884b9fb2bd9cf59f3d84f4%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_72a02463f2dd4bacaf1b05ff2095662b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6369656%2C-79.61581899999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f1540a2c8b3747259ca76b2d2d780575%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_06e7a605b3dc436581f9c3ab0702ad4d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_06e7a605b3dc436581f9c3ab0702ad4d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EEnclave%20of%20L4W%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f1540a2c8b3747259ca76b2d2d780575.setContent%28html_06e7a605b3dc436581f9c3ab0702ad4d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_72a02463f2dd4bacaf1b05ff2095662b.bindPopup%28popup_f1540a2c8b3747259ca76b2d2d780575%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_a8a4e4da77704510b8e3a37e1c5443a0%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6889054%2C-79.55472440000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_2e3d819a08284b23b8ca3188ac6dd9c8%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d5cae8a9b60249d08d94781604c20308%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d5cae8a9b60249d08d94781604c20308%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EKingsview%20Village%2C%20St.%20Phillips%2C%20Martin%20Grove%20Gardens%2C%20Richview%20Gardens%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_2e3d819a08284b23b8ca3188ac6dd9c8.setContent%28html_d5cae8a9b60249d08d94781604c20308%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_a8a4e4da77704510b8e3a37e1c5443a0.bindPopup%28popup_2e3d819a08284b23b8ca3188ac6dd9c8%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_b8df9a5bf41f4f2aa2457f52318dec2e%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7942003%2C-79.26202940000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_9d2244ed6be443d3b36531e364fa73b7%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_f64458f6515149d5a3f98a6f81895378%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_f64458f6515149d5a3f98a6f81895378%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EAgincourt%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_9d2244ed6be443d3b36531e364fa73b7.setContent%28html_f64458f6515149d5a3f98a6f81895378%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_b8df9a5bf41f4f2aa2457f52318dec2e.bindPopup%28popup_9d2244ed6be443d3b36531e364fa73b7%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8ab85347d52e4335b2df8fdcfe4f96e8%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7043244%2C-79.3887901%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_208351c7749c4d97ba428e2d77d0f7cf%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_0c1e9ec4e10f404caa03a70fd9f9f72b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_0c1e9ec4e10f404caa03a70fd9f9f72b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDavisville%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_208351c7749c4d97ba428e2d77d0f7cf.setContent%28html_0c1e9ec4e10f404caa03a70fd9f9f72b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8ab85347d52e4335b2df8fdcfe4f96e8.bindPopup%28popup_208351c7749c4d97ba428e2d77d0f7cf%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_b39f6d5dba2446d1b7312f62eaee04b1%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6626956%2C-79.4000493%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_e5cea97206dd4af6b7e3f176aa19081d%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_c07f169bfd54478691da083045693632%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_c07f169bfd54478691da083045693632%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EUniversity%20of%20Toronto%2C%20Harbord%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_e5cea97206dd4af6b7e3f176aa19081d.setContent%28html_c07f169bfd54478691da083045693632%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_b39f6d5dba2446d1b7312f62eaee04b1.bindPopup%28popup_e5cea97206dd4af6b7e3f176aa19081d%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_420231bb381a4a3886f6ed40aa52bd8d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6515706%2C-79.4844499%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_cbafc01d42324133863fe80ebb9c766e%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_f77b92df67c94ce18519d382caa3199a%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_f77b92df67c94ce18519d382caa3199a%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERunnymede%2C%20Swansea%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_cbafc01d42324133863fe80ebb9c766e.setContent%28html_f77b92df67c94ce18519d382caa3199a%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_420231bb381a4a3886f6ed40aa52bd8d.bindPopup%28popup_cbafc01d42324133863fe80ebb9c766e%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_1dd0ed02cb9f400186be0ba371e773b2%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7816375%2C-79.3043021%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_830eafed85064fc993a3e7af78943c0e%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_be73e1617d5142aa8a67602913000534%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_be73e1617d5142aa8a67602913000534%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EClarks%20Corners%2C%20Tam%20O%26%2339%3BShanter%2C%20Sullivan%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_830eafed85064fc993a3e7af78943c0e.setContent%28html_be73e1617d5142aa8a67602913000534%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_1dd0ed02cb9f400186be0ba371e773b2.bindPopup%28popup_830eafed85064fc993a3e7af78943c0e%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8fe73b400fd84961948ba0c4ec1c82f6%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6895743%2C-79.38315990000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_9a6ac24b14c141e1baac11d98b343945%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_c6c0d1ad6ce94b53bffae19f15b8209f%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_c6c0d1ad6ce94b53bffae19f15b8209f%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EMoore%20Park%2C%20Summerhill%20East%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_9a6ac24b14c141e1baac11d98b343945.setContent%28html_c6c0d1ad6ce94b53bffae19f15b8209f%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8fe73b400fd84961948ba0c4ec1c82f6.bindPopup%28popup_9a6ac24b14c141e1baac11d98b343945%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_70166299efaf4f288ae5d3318ef8de62%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6532057%2C-79.4000493%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_0e42e71696a34c0fbebf7085f33224c9%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_90ffd3a5ca4d4ecc9c07dee0bea825c8%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_90ffd3a5ca4d4ecc9c07dee0bea825c8%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EKensington%20Market%2C%20Chinatown%2C%20Grange%20Park%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_0e42e71696a34c0fbebf7085f33224c9.setContent%28html_90ffd3a5ca4d4ecc9c07dee0bea825c8%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_70166299efaf4f288ae5d3318ef8de62.bindPopup%28popup_0e42e71696a34c0fbebf7085f33224c9%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f5f55fde3418417eb28930968db63f3a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.8152522%2C-79.2845772%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_086aafcfe6fd4204ba7c4a6b79f48df5%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_c7c2caee5177465f813f42cbfa462731%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_c7c2caee5177465f813f42cbfa462731%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EMilliken%2C%20Agincourt%20North%2C%20Steeles%20East%2C%20L%26%2339%3BAmoreaux%20East%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_086aafcfe6fd4204ba7c4a6b79f48df5.setContent%28html_c7c2caee5177465f813f42cbfa462731%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f5f55fde3418417eb28930968db63f3a.bindPopup%28popup_086aafcfe6fd4204ba7c4a6b79f48df5%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_9ff1f2b9aad049f49b5ee50a76344834%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.68641229999999%2C-79.4000493%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_3a3abbd4ab0f43cba8f92e66e4c7a9b3%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_afc955df6af04371920dd6452edbe8a4%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_afc955df6af04371920dd6452edbe8a4%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ESummerhill%20West%2C%20Rathnelly%2C%20South%20Hill%2C%20Forest%20Hill%20SE%2C%20Deer%20Park%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_3a3abbd4ab0f43cba8f92e66e4c7a9b3.setContent%28html_afc955df6af04371920dd6452edbe8a4%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_9ff1f2b9aad049f49b5ee50a76344834.bindPopup%28popup_3a3abbd4ab0f43cba8f92e66e4c7a9b3%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_86e4df87f37e424c93e85c6792b74a69%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6289467%2C-79.3944199%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_e6b90b35832c450ab345c99267cae214%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_f602a4a6274a4214a78fd9984a7f15e1%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_f602a4a6274a4214a78fd9984a7f15e1%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECN%20Tower%2C%20King%20and%20Spadina%2C%20Railway%20Lands%2C%20Harbourfront%20West%2C%20Bathurst%20Quay%2C%20South%20Niagara%2C%20Island%20airport%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_e6b90b35832c450ab345c99267cae214.setContent%28html_f602a4a6274a4214a78fd9984a7f15e1%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_86e4df87f37e424c93e85c6792b74a69.bindPopup%28popup_e6b90b35832c450ab345c99267cae214%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_56db50e177694f1db521d2ebc90f623d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6056466%2C-79.50132070000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_243c3175793b4aaf82aba5b5b6d37ae1%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_286c20c237bf41448bb30e67ef7e36fd%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_286c20c237bf41448bb30e67ef7e36fd%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ENew%20Toronto%2C%20Mimico%20South%2C%20Humber%20Bay%20Shores%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_243c3175793b4aaf82aba5b5b6d37ae1.setContent%28html_286c20c237bf41448bb30e67ef7e36fd%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_56db50e177694f1db521d2ebc90f623d.bindPopup%28popup_243c3175793b4aaf82aba5b5b6d37ae1%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_bd194120cb324a8fbdb70ea373ca550b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.739416399999996%2C-79.5884369%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_dd4c0808b62a4a118e92ef40bab1fdab%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_e527b4311c5a4ccea0b9595a00d74b43%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_e527b4311c5a4ccea0b9595a00d74b43%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ESouth%20Steeles%2C%20Silverstone%2C%20Humbergate%2C%20Jamestown%2C%20Mount%20Olive%2C%20Beaumond%20Heights%2C%20Thistletown%2C%20Albion%20Gardens%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_dd4c0808b62a4a118e92ef40bab1fdab.setContent%28html_e527b4311c5a4ccea0b9595a00d74b43%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_bd194120cb324a8fbdb70ea373ca550b.bindPopup%28popup_dd4c0808b62a4a118e92ef40bab1fdab%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_3529974ff5494fff8c87a0cf5678a481%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.799525200000005%2C-79.3183887%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_3b5ea15707fd4f13b788138de23fd8f2%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a0b728eebe0e44d297ea52dbb6406c6a%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a0b728eebe0e44d297ea52dbb6406c6a%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ESteeles%20West%2C%20L%26%2339%3BAmoreaux%20West%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_3b5ea15707fd4f13b788138de23fd8f2.setContent%28html_a0b728eebe0e44d297ea52dbb6406c6a%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_3529974ff5494fff8c87a0cf5678a481.bindPopup%28popup_3b5ea15707fd4f13b788138de23fd8f2%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_9be386a5936d44e391ff8cc0866e92dd%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6795626%2C-79.37752940000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8a7c93cc215c4536a7dbf2d852aa19b6%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d8148f41d9f949c8835122df5ca43b28%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d8148f41d9f949c8835122df5ca43b28%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERosedale%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8a7c93cc215c4536a7dbf2d852aa19b6.setContent%28html_d8148f41d9f949c8835122df5ca43b28%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_9be386a5936d44e391ff8cc0866e92dd.bindPopup%28popup_8a7c93cc215c4536a7dbf2d852aa19b6%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_5f029d00b6094338aba99b82f2490686%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6464352%2C-79.37484599999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_065170579ab1432fad0c3f4e5bd8aeec%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_2de3f72b668844309d92e297ee54a4ee%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_2de3f72b668844309d92e297ee54a4ee%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EEnclave%20of%20M5E%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_065170579ab1432fad0c3f4e5bd8aeec.setContent%28html_2de3f72b668844309d92e297ee54a4ee%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_5f029d00b6094338aba99b82f2490686.bindPopup%28popup_065170579ab1432fad0c3f4e5bd8aeec%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_b2774454cc834bf7a57c09b6eeb8c646%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.60241370000001%2C-79.54348409999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_034c0436b9c5412ab8552186ae5e09be%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_47f5be48cd794a94b027db725204d187%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_47f5be48cd794a94b027db725204d187%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EAlderwood%2C%20Long%20Branch%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_034c0436b9c5412ab8552186ae5e09be.setContent%28html_47f5be48cd794a94b027db725204d187%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_b2774454cc834bf7a57c09b6eeb8c646.bindPopup%28popup_034c0436b9c5412ab8552186ae5e09be%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_c67b49f2ae5d481ca99169d2939c4f9b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.706748299999994%2C-79.5940544%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8bc2d7b08b8c4e74985e00d55d2f598a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_0db4e1dfd186426cae0c216a18b6772b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_0db4e1dfd186426cae0c216a18b6772b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EClairville%2C%20Humberwood%2C%20Woodbine%20Downs%2C%20West%20Humber%2C%20Kipling%20Heights%2C%20Rexdale%2C%20Elms%2C%20Tandridge%2C%20Old%20Rexdale%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8bc2d7b08b8c4e74985e00d55d2f598a.setContent%28html_0db4e1dfd186426cae0c216a18b6772b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_c67b49f2ae5d481ca99169d2939c4f9b.bindPopup%28popup_8bc2d7b08b8c4e74985e00d55d2f598a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_93f6fa04a5c640e6843426e352333010%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.836124700000006%2C-79.20563609999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_777d2aa686eb4b9195014348a06f7512%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_9865555ffeb94350a6fd53c42b5c09a8%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_9865555ffeb94350a6fd53c42b5c09a8%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EUpper%20Rouge%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_777d2aa686eb4b9195014348a06f7512.setContent%28html_9865555ffeb94350a6fd53c42b5c09a8%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_93f6fa04a5c640e6843426e352333010.bindPopup%28popup_777d2aa686eb4b9195014348a06f7512%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_37b6e69ab6cb4ee18943dc3f5f8a5023%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.667967%2C-79.3676753%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_efb663fa69ed4ad0aed9bcf7ea41ae43%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_9d23123c410c461aaf06a978c4f306ad%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_9d23123c410c461aaf06a978c4f306ad%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ESt.%20James%20Town%2C%20Cabbagetown%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_efb663fa69ed4ad0aed9bcf7ea41ae43.setContent%28html_9d23123c410c461aaf06a978c4f306ad%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_37b6e69ab6cb4ee18943dc3f5f8a5023.bindPopup%28popup_efb663fa69ed4ad0aed9bcf7ea41ae43%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_89ef7d735fdd4581a02abb1e4b7fa264%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6484292%2C-79.3822802%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_12f5ed82302a4c3aa1f27d357e3ea4e1%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_fbb65c75c9e34f78aa1ec75cf2c0ba0a%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_fbb65c75c9e34f78aa1ec75cf2c0ba0a%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EFirst%20Canadian%20Place%2C%20Underground%20city%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_12f5ed82302a4c3aa1f27d357e3ea4e1.setContent%28html_fbb65c75c9e34f78aa1ec75cf2c0ba0a%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_89ef7d735fdd4581a02abb1e4b7fa264.bindPopup%28popup_12f5ed82302a4c3aa1f27d357e3ea4e1%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_75005d9933cc4fdfafec384a88f4236d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.653653600000005%2C-79.5069436%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_62ba364403c54353b1599bb54c989756%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_24dcf4392c3e40f9a0de0233600a427b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_24dcf4392c3e40f9a0de0233600a427b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThe%20Kingsway%2C%20Montgomery%20Road%2C%20Old%20Mill%20North%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_62ba364403c54353b1599bb54c989756.setContent%28html_24dcf4392c3e40f9a0de0233600a427b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_75005d9933cc4fdfafec384a88f4236d.bindPopup%28popup_62ba364403c54353b1599bb54c989756%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_0b4a564327a74ddfa9faafc39c7e0ac7%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6658599%2C-79.38315990000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_64109070822c4f09b6942f9d0618b694%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_726e6a01cafa433f9eb7584482294e9b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_726e6a01cafa433f9eb7584482294e9b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EChurch%20and%20Wellesley%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_64109070822c4f09b6942f9d0618b694.setContent%28html_726e6a01cafa433f9eb7584482294e9b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_0b4a564327a74ddfa9faafc39c7e0ac7.bindPopup%28popup_64109070822c4f09b6942f9d0618b694%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_b84c60de3ee04bfd99e5cb6059b5b9d5%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6627439%2C-79.321558%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_ddbf01a63f4b46138bb90b4b6e8c66f3%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_bf96c4da96154d8f8c7a94fdfb060f60%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_bf96c4da96154d8f8c7a94fdfb060f60%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EEnclave%20of%20M4L%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_ddbf01a63f4b46138bb90b4b6e8c66f3.setContent%28html_bf96c4da96154d8f8c7a94fdfb060f60%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_b84c60de3ee04bfd99e5cb6059b5b9d5.bindPopup%28popup_ddbf01a63f4b46138bb90b4b6e8c66f3%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_7f777a6b18df4067913d5c0dea588de8%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6362579%2C-79.49850909999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_9ea6c1ee545d468b9e7ce9f68ad10389%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_4e4b02b1e1114814b62fdbd2fc679a8d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_4e4b02b1e1114814b62fdbd2fc679a8d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EOld%20Mill%20South%2C%20King%26%2339%3Bs%20Mill%20Park%2C%20Sunnylea%2C%20Humber%20Bay%2C%20Mimico%20NE%2C%20The%20Queensway%20East%2C%20Royal%20York%20South%20East%2C%20Kingsway%20Park%20South%20East%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_9ea6c1ee545d468b9e7ce9f68ad10389.setContent%28html_4e4b02b1e1114814b62fdbd2fc679a8d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_7f777a6b18df4067913d5c0dea588de8.bindPopup%28popup_9ea6c1ee545d468b9e7ce9f68ad10389%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_57efe0503bcf43549d268ac9b7ec02a6%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6288408%2C-79.52099940000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22blue%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%233186cc%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_dd7e120904a94a4d86f6968cdb4c5b17%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_b2131e8741df493a9d35d5b0626855b8%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_56e4f4de521d461689d1248d6d237e40%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_56e4f4de521d461689d1248d6d237e40%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EMimico%20NW%2C%20The%20Queensway%20West%2C%20South%20of%20Bloor%2C%20Kingsway%20Park%20South%20West%2C%20Royal%20York%20South%20West%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_b2131e8741df493a9d35d5b0626855b8.setContent%28html_56e4f4de521d461689d1248d6d237e40%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_57efe0503bcf43549d268ac9b7ec02a6.bindPopup%28popup_b2131e8741df493a9d35d5b0626855b8%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%3C/script%3E onload="this.contentDocument.open();this.contentDocument.write(    decodeURIComponent(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>




```python
CLIENT_ID = '24X5BG4TZOBYQQUIMFNJ0T0004WE21TAUWAFUPWITH0TJZM3' # your Foursquare ID
CLIENT_SECRET = 'GKOPTZRZYZTIB42HVML1DRMN5IKCGFFTJYWSBLLWATXUUNFC' # your Foursquare Secret
VERSION = '20180605' # Foursquare API version
LIMIT = 100 # A default Foursquare API limit value

print('Your credentails:')
print('CLIENT_ID: ' + CLIENT_ID)
print('CLIENT_SECRET:' + CLIENT_SECRET)
```

    Your credentails:
    CLIENT_ID: 24X5BG4TZOBYQQUIMFNJ0T0004WE21TAUWAFUPWITH0TJZM3
    CLIENT_SECRET:GKOPTZRZYZTIB42HVML1DRMN5IKCGFFTJYWSBLLWATXUUNFC
    

Get the first neighborhood's name


```python
df2['Neighborhood'].iloc[0]
```




    'Parkwoods'



Get the neighborhood's longitude and latitude


```python
neighborhood_latitude = df2.loc[0, 'Latitude'] # neighborhood latitude value
neighborhood_longitude = df2.loc[0, 'Longitude'] # neighborhood longitude value

neighborhood_name = df2.loc[0, 'Neighborhood'] # neighborhood name

print('Latitude and longitude values of {} are {}, {}.'.format(neighborhood_name, 
                                                               neighborhood_latitude, 
                                                               neighborhood_longitude))
```

    Latitude and longitude values of Parkwoods are 43.7532586, -79.3296565.
    

Top 100 venues that are in Parkwoods within a radius of 500  meters


```python
LIMIT=100
radius=500

url = 'https://api.foursquare.com/v2/venues/explore?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&limit={}'.format(
    CLIENT_ID, 
    CLIENT_SECRET, 
    VERSION, 
    neighborhood_latitude, 
    neighborhood_longitude, 
    radius, 
    LIMIT)
url
```




    'https://api.foursquare.com/v2/venues/explore?&client_id=24X5BG4TZOBYQQUIMFNJ0T0004WE21TAUWAFUPWITH0TJZM3&client_secret=GKOPTZRZYZTIB42HVML1DRMN5IKCGFFTJYWSBLLWATXUUNFC&v=20180605&ll=43.7532586,-79.3296565&radius=500&limit=100'




```python
results = requests.get(url).json()
results
```




    {'meta': {'code': 200, 'requestId': '609fc6648220f7718b469761'},
     'response': {'warning': {'text': "There aren't a lot of results near you. Try something more general, reset your filters, or expand the search area."},
      'headerLocation': 'Parkwoods - Donalda',
      'headerFullLocation': 'Parkwoods - Donalda, Toronto',
      'headerLocationGranularity': 'neighborhood',
      'totalResults': 2,
      'suggestedBounds': {'ne': {'lat': 43.757758604500005,
        'lng': -79.32343823984928},
       'sw': {'lat': 43.7487585955, 'lng': -79.33587476015072}},
      'groups': [{'type': 'Recommended Places',
        'name': 'recommended',
        'items': [{'reasons': {'count': 0,
           'items': [{'summary': 'This spot is popular',
             'type': 'general',
             'reasonName': 'globalInteractionReason'}]},
          'venue': {'id': '4e8d9dcdd5fbbbb6b3003c7b',
           'name': 'Brookbanks Park',
           'location': {'address': 'Toronto',
            'lat': 43.751976046055574,
            'lng': -79.33214044722958,
            'labeledLatLngs': [{'label': 'display',
              'lat': 43.751976046055574,
              'lng': -79.33214044722958}],
            'distance': 245,
            'cc': 'CA',
            'city': 'Toronto',
            'state': 'ON',
            'country': 'Canada',
            'formattedAddress': ['Toronto', 'Toronto ON', 'Canada']},
           'categories': [{'id': '4bf58dd8d48988d163941735',
             'name': 'Park',
             'pluralName': 'Parks',
             'shortName': 'Park',
             'icon': {'prefix': 'https://ss3.4sqi.net/img/categories_v2/parks_outdoors/park_',
              'suffix': '.png'},
             'primary': True}],
           'photos': {'count': 0, 'groups': []},
           'venuePage': {'id': '600917367'}},
          'referralId': 'e-0-4e8d9dcdd5fbbbb6b3003c7b-0'},
         {'reasons': {'count': 0,
           'items': [{'summary': 'This spot is popular',
             'type': 'general',
             'reasonName': 'globalInteractionReason'}]},
          'venue': {'id': '4cb11e2075ebb60cd1c4caad',
           'name': 'Variety Store',
           'location': {'address': '29 Valley Woods Road',
            'lat': 43.75197441585782,
            'lng': -79.33311418516017,
            'labeledLatLngs': [{'label': 'display',
              'lat': 43.75197441585782,
              'lng': -79.33311418516017}],
            'distance': 312,
            'cc': 'CA',
            'city': 'Toronto',
            'state': 'ON',
            'country': 'Canada',
            'formattedAddress': ['29 Valley Woods Road', 'Toronto ON', 'Canada']},
           'categories': [{'id': '4bf58dd8d48988d1f9941735',
             'name': 'Food & Drink Shop',
             'pluralName': 'Food & Drink Shops',
             'shortName': 'Food & Drink',
             'icon': {'prefix': 'https://ss3.4sqi.net/img/categories_v2/shops/foodanddrink_',
              'suffix': '.png'},
             'primary': True}],
           'photos': {'count': 0, 'groups': []}},
          'referralId': 'e-0-4cb11e2075ebb60cd1c4caad-1'}]}]}}




```python
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
import pandas as pd
venues = results['response']['groups'][0]['items']
    
nearby_venues = pd.json_normalize(venues) # flatten JSON

# filter columns
filtered_columns = ['venue.name', 'venue.categories', 'venue.location.lat', 'venue.location.lng']
nearby_venues =nearby_venues.loc[:, filtered_columns]

# filter the category for each row
nearby_venues['venue.categories'] = nearby_venues.apply(get_category_type, axis=1)

# clean columns
nearby_venues.columns = [col.split(".")[-1] for col in nearby_venues.columns]

nearby_venues.head()
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
      <th>0</th>
      <td>Brookbanks Park</td>
      <td>Park</td>
      <td>43.751976</td>
      <td>-79.332140</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Variety Store</td>
      <td>Food &amp; Drink Shop</td>
      <td>43.751974</td>
      <td>-79.333114</td>
    </tr>
  </tbody>
</table>
</div>




```python
print('{} venues were returned by Foursquare.'.format(nearby_venues.shape[0]))
```

    2 venues were returned by Foursquare.
    


```python
def getNearbyVenues(names, latitudes, longitudes, radius=1000):
    
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
    nearby_venues.columns = ['Neighborhood', 
                  'Neighborhood Latitude', 
                  'Neighborhood Longitude', 
                  'Venue', 
                  'Venue Latitude', 
                  'Venue Longitude', 
                  'Venue Category']
    
    return(nearby_venues)
```

Creating the dataframe toronto_venues


```python
toronto_venues = getNearbyVenues(names=df2['Neighborhood'],
                                   latitudes=df2['Latitude'],
                                   longitudes=df2['Longitude']
                                  )
```

    Parkwoods
    Victoria Village
    Regent Park, Harbourfront
    Lawrence Manor, Lawrence Heights
    Ontario Provincial Government
    Islington Avenue
    Malvern, Rouge
    Don Mills North
    Parkview Hill, Woodbine Gardens
    Garden District, Ryerson
    Glencairn
    West Deane Park, Princess Gardens, Martin Grove, Islington, Cloverdale
    Rouge Hill, Port Union, Highland Creek
    Don Mills South
    Woodbine Heights
    St. James Town
    Humewood-Cedarvale
    Eringate, Bloordale Gardens, Old Burnhamthorpe, Markland Wood
    Guildwood, Morningside, West Hill
    The Beaches
    Berczy Park
    Caledonia-Fairbanks
    Woburn
    Leaside
    Central Bay Street
    Christie
    Cedarbrae
    Hillcrest Village
    Bathurst Manor, Wilson Heights, Downsview North
    Thorncliffe Park
    Richmond, Adelaide, King
    Dufferin, Dovercourt Village
    Scarborough Village
    Fairview, Henry Farm, Oriole
    Northwood Park, York University
    The Danforth  East
    Harbourfront East, Union Station, Toronto Islands
    Little Portugal, Trinity
    Kennedy Park, Ionview, East Birchmount Park
    Bayview Village
    Downsview East
    The Danforth West, Riverdale
    Toronto Dominion Centre, Design Exchange
    Brockton, Parkdale Village, Exhibition Place
    Golden Mile, Clairlea, Oakridge
    York Mills, Silver Hills
    Downsview West
    India Bazaar, The Beaches West
    Commerce Court, Victoria Hotel
    North Park, Maple Leaf Park, Upwood Park
    Humber Summit
    Cliffside, Cliffcrest, Scarborough Village West
    Willowdale, Newtonbrook
    Downsview Central
    Studio District
    Bedford Park, Lawrence Manor East
    Del Ray, Mount Dennis, Keelsdale and Silverthorn
    Humberlea, Emery
    Birch Cliff, Cliffside West
    Willowdale South
    Downsview Northwest
    Lawrence Park
    Roselawn
    Runnymede, The Junction North
    Weston
    Dorset Park, Wexford Heights, Scarborough Town Centre
    York Mills West
    Davisville North
    Forest Hill North & West
    High Park, The Junction South
    Westmount
    Wexford, Maryvale
    Willowdale West
    North Toronto West
    The Annex, North Midtown, Yorkville
    Parkdale, Roncesvalles
    Enclave of L4W
    Kingsview Village, St. Phillips, Martin Grove Gardens, Richview Gardens
    Agincourt
    Davisville
    University of Toronto, Harbord
    Runnymede, Swansea
    Clarks Corners, Tam O'Shanter, Sullivan
    Moore Park, Summerhill East
    Kensington Market, Chinatown, Grange Park
    Milliken, Agincourt North, Steeles East, L'Amoreaux East
    Summerhill West, Rathnelly, South Hill, Forest Hill SE, Deer Park
    CN Tower, King and Spadina, Railway Lands, Harbourfront West, Bathurst Quay, South Niagara, Island airport
    New Toronto, Mimico South, Humber Bay Shores
    South Steeles, Silverstone, Humbergate, Jamestown, Mount Olive, Beaumond Heights, Thistletown, Albion Gardens
    Steeles West, L'Amoreaux West
    Rosedale
    Enclave of M5E
    Alderwood, Long Branch
    Clairville, Humberwood, Woodbine Downs, West Humber, Kipling Heights, Rexdale, Elms, Tandridge, Old Rexdale
    Upper Rouge
    St. James Town, Cabbagetown
    First Canadian Place, Underground city
    The Kingsway, Montgomery Road, Old Mill North
    Church and Wellesley
    Enclave of M4L
    Old Mill South, King's Mill Park, Sunnylea, Humber Bay, Mimico NE, The Queensway East, Royal York South East, Kingsway Park South East
    Mimico NW, The Queensway West, South of Bloor, Kingsway Park South West, Royal York South West
    


```python
print(toronto_venues.shape)
toronto_venues.head()
```

    (4924, 7)
    




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
      <th>Neighborhood Latitude</th>
      <th>Neighborhood Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
      <td>Allwyn's Bakery</td>
      <td>43.759840</td>
      <td>-79.324719</td>
      <td>Caribbean Restaurant</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
      <td>Brookbanks Park</td>
      <td>43.751976</td>
      <td>-79.332140</td>
      <td>Park</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
      <td>Tim Hortons</td>
      <td>43.760668</td>
      <td>-79.326368</td>
      <td>Café</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
      <td>Bruno's valu-mart</td>
      <td>43.746143</td>
      <td>-79.324630</td>
      <td>Grocery Store</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
      <td>High Street Fish &amp; Chips</td>
      <td>43.745260</td>
      <td>-79.324949</td>
      <td>Fish &amp; Chips Shop</td>
    </tr>
  </tbody>
</table>
</div>




```python
toronto_venues.groupby("Neighborhood").count()
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
      <th>Neighborhood Latitude</th>
      <th>Neighborhood Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
    <tr>
      <th>Neighborhood</th>
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
      <th>Agincourt</th>
      <td>45</td>
      <td>45</td>
      <td>45</td>
      <td>45</td>
      <td>45</td>
      <td>45</td>
    </tr>
    <tr>
      <th>Alderwood, Long Branch</th>
      <td>27</td>
      <td>27</td>
      <td>27</td>
      <td>27</td>
      <td>27</td>
      <td>27</td>
    </tr>
    <tr>
      <th>Bathurst Manor, Wilson Heights, Downsview North</th>
      <td>32</td>
      <td>32</td>
      <td>32</td>
      <td>32</td>
      <td>32</td>
      <td>32</td>
    </tr>
    <tr>
      <th>Bayview Village</th>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
    </tr>
    <tr>
      <th>Bedford Park, Lawrence Manor East</th>
      <td>43</td>
      <td>43</td>
      <td>43</td>
      <td>43</td>
      <td>43</td>
      <td>43</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Willowdale, Newtonbrook</th>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>Woburn</th>
      <td>8</td>
      <td>8</td>
      <td>8</td>
      <td>8</td>
      <td>8</td>
      <td>8</td>
    </tr>
    <tr>
      <th>Woodbine Heights</th>
      <td>28</td>
      <td>28</td>
      <td>28</td>
      <td>28</td>
      <td>28</td>
      <td>28</td>
    </tr>
    <tr>
      <th>York Mills West</th>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
      <td>17</td>
    </tr>
    <tr>
      <th>York Mills, Silver Hills</th>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
<p>102 rows × 6 columns</p>
</div>



How many unique categories can be curated from all the returned venues


```python
print('There are {} uniques categories.'.format(len(toronto_venues['Venue Category'].unique())))
```

    There are 332 uniques categories.
    

# Analyze Each Neighborhood


```python
toronto_onehot = pd.get_dummies(toronto_venues[['Venue Category']], prefix="", prefix_sep="")

# add neighborhood column back to dataframe
toronto_onehot['Neighborhood'] = toronto_venues['Neighborhood'] 

# move neighborhood column to the first column
fixed_columns = [toronto_onehot.columns[-1]] + list(toronto_onehot.columns[:-1])
toronto_onehot = toronto_onehot[fixed_columns]

toronto_onehot.head()
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
      <th>Zoo</th>
      <th>ATM</th>
      <th>Accessories Store</th>
      <th>Adult Boutique</th>
      <th>Afghan Restaurant</th>
      <th>Airport</th>
      <th>Airport Lounge</th>
      <th>American Restaurant</th>
      <th>Amphitheater</th>
      <th>Animal Shelter</th>
      <th>...</th>
      <th>Video Game Store</th>
      <th>Video Store</th>
      <th>Vietnamese Restaurant</th>
      <th>Warehouse Store</th>
      <th>Water Park</th>
      <th>Whisky Bar</th>
      <th>Wine Bar</th>
      <th>Wings Joint</th>
      <th>Women's Store</th>
      <th>Yoga Studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
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
      <td>...</td>
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
    <tr>
      <th>1</th>
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
      <td>...</td>
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
    <tr>
      <th>2</th>
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
      <td>...</td>
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
    <tr>
      <th>3</th>
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
      <td>...</td>
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
    <tr>
      <th>4</th>
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
      <td>...</td>
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
<p>5 rows × 332 columns</p>
</div>




```python
toronto_onehot.shape
```




    (4924, 332)



 Group rows by neighborhood and by taking the mean of the frequency of occurrence of each category


```python
toronto_grouped=toronto_onehot.groupby('Neighborhood').mean().reset_index()
toronto_grouped
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
      <th>Zoo</th>
      <th>ATM</th>
      <th>Accessories Store</th>
      <th>Adult Boutique</th>
      <th>Afghan Restaurant</th>
      <th>Airport</th>
      <th>Airport Lounge</th>
      <th>American Restaurant</th>
      <th>Amphitheater</th>
      <th>...</th>
      <th>Video Game Store</th>
      <th>Video Store</th>
      <th>Vietnamese Restaurant</th>
      <th>Warehouse Store</th>
      <th>Water Park</th>
      <th>Whisky Bar</th>
      <th>Wine Bar</th>
      <th>Wings Joint</th>
      <th>Women's Store</th>
      <th>Yoga Studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Agincourt</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alderwood, Long Branch</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Bathurst Manor, Wilson Heights, Downsview North</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Bayview Village</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Bedford Park, Lawrence Manor East</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.023256</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.023256</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.023256</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>97</th>
      <td>Willowdale, Newtonbrook</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>98</th>
      <td>Woburn</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>99</th>
      <td>Woodbine Heights</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>100</th>
      <td>York Mills West</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>101</th>
      <td>York Mills, Silver Hills</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>102 rows × 332 columns</p>
</div>




```python
toronto_grouped.shape
```




    (102, 332)



Print each neighborhood along with the top 5 most common venues


```python
num_top_venues = 5

for hood in toronto_grouped['Neighborhood']:
    print("----"+hood+"----")
    temp = toronto_grouped[toronto_grouped['Neighborhood'] == hood].T.reset_index()
    temp.columns = ['venue','freq']
    temp = temp.iloc[1:]
    temp['freq'] = temp['freq'].astype(float)
    temp = temp.round({'freq': 2})
    print(temp.sort_values('freq', ascending=False).reset_index(drop=True).head(num_top_venues))
    print('\n')
```

    ----Agincourt----
                      venue  freq
    0    Chinese Restaurant  0.16
    1         Shopping Mall  0.07
    2                Bakery  0.04
    3  Caribbean Restaurant  0.04
    4        Sandwich Place  0.04
    
    
    ----Alderwood, Long Branch----
                   venue  freq
    0     Discount Store  0.11
    1  Convenience Store  0.07
    2               Park  0.07
    3        Pizza Place  0.07
    4           Pharmacy  0.07
    
    
    ----Bathurst Manor, Wilson Heights, Downsview North----
             venue  freq
    0  Pizza Place  0.06
    1     Pharmacy  0.06
    2  Coffee Shop  0.06
    3         Park  0.06
    4         Bank  0.06
    
    
    ----Bayview Village----
                     venue  freq
    0                 Bank  0.12
    1  Japanese Restaurant  0.12
    2        Grocery Store  0.12
    3          Gas Station  0.12
    4         Intersection  0.06
    
    
    ----Bedford Park, Lawrence Manor East----
                      venue  freq
    0    Italian Restaurant  0.07
    1           Coffee Shop  0.07
    2            Restaurant  0.05
    3  Fast Food Restaurant  0.05
    4      Sushi Restaurant  0.05
    
    
    ----Berczy Park----
                     venue  freq
    0          Coffee Shop  0.10
    1                 Café  0.07
    2                Hotel  0.05
    3                 Park  0.05
    4  Japanese Restaurant  0.04
    
    
    ----Birch Cliff, Cliffside West----
                       venue  freq
    0                   Park  0.15
    1        College Stadium  0.08
    2  General Entertainment  0.08
    3                    Gym  0.08
    4               Gym Pool  0.08
    
    
    ----Brockton, Parkdale Village, Exhibition Place----
             venue  freq
    0         Café  0.07
    1   Restaurant  0.06
    2  Coffee Shop  0.05
    3          Bar  0.05
    4       Bakery  0.04
    
    
    ----CN Tower, King and Spadina, Railway Lands, Harbourfront West, Bathurst Quay, South Niagara, Island airport----
                  venue  freq
    0              Café  0.12
    1   Harbor / Marina  0.12
    2       Coffee Shop  0.12
    3    Scenic Lookout  0.06
    4  Sushi Restaurant  0.06
    
    
    ----Caledonia-Fairbanks----
                    venue  freq
    0                Park  0.11
    1         Pizza Place  0.11
    2            Pharmacy  0.11
    3              Bakery  0.06
    4  Falafel Restaurant  0.06
    
    
    ----Cedarbrae----
                   venue  freq
    0             Bakery  0.09
    1  Indian Restaurant  0.06
    2               Bank  0.06
    3      Grocery Store  0.06
    4        Gas Station  0.06
    
    
    ----Central Bay Street----
                     venue  freq
    0          Coffee Shop  0.10
    1                 Café  0.05
    2  Japanese Restaurant  0.03
    3     Sushi Restaurant  0.03
    4                 Park  0.03
    
    
    ----Christie----
                   venue  freq
    0  Korean Restaurant  0.13
    1               Café  0.07
    2        Coffee Shop  0.06
    3      Grocery Store  0.06
    4        Pizza Place  0.03
    
    
    ----Church and Wellesley----
                     venue  freq
    0          Coffee Shop  0.10
    1  Japanese Restaurant  0.04
    2     Sushi Restaurant  0.04
    3   Italian Restaurant  0.03
    4                 Park  0.03
    
    
    ----Clairville, Humberwood, Woodbine Downs, West Humber, Kipling Heights, Rexdale, Elms, Tandridge, Old Rexdale----
                       venue  freq
    0                  Hotel  0.33
    1    Rental Car Location  0.33
    2            Coffee Shop  0.33
    3              Nightclub  0.00
    4  Outdoors & Recreation  0.00
    
    
    ----Clarks Corners, Tam O'Shanter, Sullivan----
                      venue  freq
    0                  Bank  0.06
    1              Pharmacy  0.06
    2  Fast Food Restaurant  0.06
    3           Coffee Shop  0.06
    4           Pizza Place  0.06
    
    
    ----Cliffside, Cliffcrest, Scarborough Village West----
                venue  freq
    0     Pizza Place  0.23
    1           Beach  0.15
    2  Ice Cream Shop  0.15
    3            Park  0.08
    4      Restaurant  0.08
    
    
    ----Commerce Court, Victoria Hotel----
                    venue  freq
    0         Coffee Shop  0.10
    1                Café  0.06
    2               Hotel  0.05
    3          Restaurant  0.04
    4  Seafood Restaurant  0.04
    
    
    ----Davisville----
                    venue  freq
    0  Italian Restaurant  0.09
    1         Coffee Shop  0.08
    2    Sushi Restaurant  0.06
    3                Café  0.04
    4          Restaurant  0.04
    
    
    ----Davisville North----
                    venue  freq
    0         Coffee Shop  0.12
    1  Italian Restaurant  0.06
    2          Restaurant  0.04
    3                Café  0.04
    4                 Gym  0.03
    
    
    ----Del Ray, Mount Dennis, Keelsdale and Silverthorn----
                        venue  freq
    0  Furniture / Home Store  0.18
    1           Grocery Store  0.12
    2            Intersection  0.12
    3       Convenience Store  0.06
    4      Italian Restaurant  0.06
    
    
    ----Don Mills North----
                     venue  freq
    0  Japanese Restaurant  0.10
    1         Burger Joint  0.07
    2          Pizza Place  0.07
    3          Coffee Shop  0.07
    4                 Bank  0.03
    
    
    ----Don Mills South----
                  venue  freq
    0        Restaurant  0.14
    1               Gym  0.07
    2  Sushi Restaurant  0.05
    3       Supermarket  0.05
    4       Coffee Shop  0.05
    
    
    ----Dorset Park, Wexford Heights, Scarborough Town Centre----
                   venue  freq
    0  Electronics Store  0.08
    1         Restaurant  0.06
    2           Pharmacy  0.06
    3        Coffee Shop  0.06
    4             Bakery  0.06
    
    
    ----Downsview Central----
                       venue  freq
    0  Vietnamese Restaurant  0.50
    1             Food Truck  0.25
    2         Baseball Field  0.25
    3                    Zoo  0.00
    4              Nightclub  0.00
    
    
    ----Downsview East----
                    venue  freq
    0         Coffee Shop  0.12
    1  Turkish Restaurant  0.08
    2              Bakery  0.04
    3                Park  0.04
    4        Liquor Store  0.04
    
    
    ----Downsview Northwest----
               venue  freq
    0          Hotel  0.11
    1  Grocery Store  0.09
    2       Pharmacy  0.06
    3    Gas Station  0.06
    4    Coffee Shop  0.06
    
    
    ----Downsview West----
               venue  freq
    0           Park  0.17
    1    Pizza Place  0.17
    2           Bank  0.17
    3  Shopping Mall  0.17
    4  Grocery Store  0.17
    
    
    ----Dufferin, Dovercourt Village----
                    venue  freq
    0                Café  0.10
    1                Park  0.09
    2         Coffee Shop  0.09
    3  Italian Restaurant  0.06
    4                 Bar  0.04
    
    
    ----Enclave of L4W----
                           venue  freq
    0                Coffee Shop  0.11
    1                Bus Station  0.06
    2  Middle Eastern Restaurant  0.06
    3                      Hotel  0.06
    4          Indian Restaurant  0.04
    
    
    ----Enclave of M4L----
                    venue  freq
    0                Park  0.10
    1             Brewery  0.06
    2         Coffee Shop  0.06
    3         Pizza Place  0.06
    4  Italian Restaurant  0.04
    
    
    ----Enclave of M5E----
                     venue  freq
    0          Coffee Shop  0.11
    1                 Café  0.07
    2                 Park  0.04
    3  Japanese Restaurant  0.04
    4   Seafood Restaurant  0.04
    
    
    ----Eringate, Bloordale Gardens, Old Burnhamthorpe, Markland Wood----
                venue  freq
    0     Coffee Shop  0.11
    1  Sandwich Place  0.06
    2     Pizza Place  0.06
    3        Pharmacy  0.06
    4            Café  0.06
    
    
    ----Fairview, Henry Farm, Oriole----
                      venue  freq
    0           Coffee Shop  0.11
    1        Clothing Store  0.09
    2  Fast Food Restaurant  0.05
    3   Japanese Restaurant  0.05
    4        Sandwich Place  0.05
    
    
    ----First Canadian Place, Underground city----
                     venue  freq
    0          Coffee Shop  0.09
    1                 Café  0.06
    2                Hotel  0.05
    3  Japanese Restaurant  0.03
    4              Theater  0.03
    
    
    ----Forest Hill North & West----
                      venue  freq
    0                  Park  0.08
    1                  Café  0.06
    2                  Bank  0.06
    3           Coffee Shop  0.06
    4  Gym / Fitness Center  0.04
    
    
    ----Garden District, Ryerson----
                     venue  freq
    0          Coffee Shop  0.10
    1            Gastropub  0.06
    2  Japanese Restaurant  0.06
    3                 Café  0.06
    4   Italian Restaurant  0.03
    
    
    ----Glencairn----
                      venue  freq
    0  Fast Food Restaurant  0.10
    1         Grocery Store  0.10
    2           Coffee Shop  0.06
    3           Pizza Place  0.06
    4           Gas Station  0.06
    
    
    ----Golden Mile, Clairlea, Oakridge----
              venue  freq
    0  Intersection  0.10
    1   Coffee Shop  0.10
    2        Bakery  0.07
    3          Park  0.07
    4      Pharmacy  0.03
    
    
    ----Guildwood, Morningside, West Hill----
                      venue  freq
    0           Pizza Place  0.12
    1            Restaurant  0.12
    2  Fast Food Restaurant  0.08
    3                  Bank  0.08
    4            Sports Bar  0.04
    
    
    ----Harbourfront East, Union Station, Toronto Islands----
                     venue  freq
    0          Coffee Shop  0.08
    1                 Café  0.07
    2                Hotel  0.05
    3  Japanese Restaurant  0.04
    4                 Park  0.04
    
    
    ----High Park, The Junction South----
                    venue  freq
    0                Café  0.08
    1                 Bar  0.06
    2         Coffee Shop  0.05
    3     Thai Restaurant  0.04
    4  Italian Restaurant  0.03
    
    
    ----Hillcrest Village----
                   venue  freq
    0               Park  0.09
    1           Pharmacy  0.09
    2        Coffee Shop  0.09
    3  Recreation Center  0.05
    4  Korean Restaurant  0.05
    
    
    ----Humber Summit----
                   venue  freq
    0  Electronics Store  0.22
    1             Bakery  0.11
    2               Bank  0.11
    3               Park  0.11
    4      Shopping Mall  0.11
    
    
    ----Humberlea, Emery----
                   venue  freq
    0        Golf Course  0.14
    1  Convenience Store  0.14
    2             Bakery  0.14
    3               Park  0.14
    4   Storage Facility  0.14
    
    
    ----Humewood-Cedarvale----
                   venue  freq
    0  Convenience Store  0.07
    1         Bagel Shop  0.07
    2      Grocery Store  0.07
    3        Coffee Shop  0.07
    4        Pizza Place  0.07
    
    
    ----India Bazaar, The Beaches West----
                   venue  freq
    0  Indian Restaurant  0.09
    1        Coffee Shop  0.05
    2         Restaurant  0.04
    3               Park  0.04
    4      Grocery Store  0.04
    
    
    ----Islington Avenue----
                   venue  freq
    0           Pharmacy  0.17
    1               Bank  0.08
    2  Convenience Store  0.08
    3        Golf Course  0.08
    4      Shopping Mall  0.08
    
    
    ----Kennedy Park, Ionview, East Birchmount Park----
                      venue  freq
    0    Chinese Restaurant  0.12
    1           Coffee Shop  0.12
    2  Fast Food Restaurant  0.08
    3           Pizza Place  0.08
    4   Rental Car Location  0.04
    
    
    ----Kensington Market, Chinatown, Grange Park----
                               venue  freq
    0                           Café  0.11
    1                            Bar  0.05
    2                    Coffee Shop  0.05
    3  Vegetarian / Vegan Restaurant  0.05
    4                    Art Gallery  0.03
    
    
    ----Kingsview Village, St. Phillips, Martin Grove Gardens, Richview Gardens----
                    venue  freq
    0            Pharmacy  0.14
    1  Chinese Restaurant  0.07
    2        Intersection  0.07
    3         Gas Station  0.07
    4          Beer Store  0.07
    
    
    ----Lawrence Manor, Lawrence Heights----
                        venue  freq
    0  Furniture / Home Store  0.07
    1              Restaurant  0.07
    2    Fast Food Restaurant  0.07
    3             Coffee Shop  0.07
    4          Clothing Store  0.07
    
    
    ----Lawrence Park----
                      venue  freq
    0           College Gym  0.12
    1             Bookstore  0.12
    2                  Café  0.12
    3  Gym / Fitness Center  0.12
    4          College Quad  0.12
    
    
    ----Leaside----
                        venue  freq
    0             Coffee Shop  0.07
    1  Furniture / Home Store  0.05
    2     Sporting Goods Shop  0.05
    3           Grocery Store  0.05
    4       Electronics Store  0.05
    
    
    ----Little Portugal, Trinity----
                               venue  freq
    0                           Café  0.08
    1                            Bar  0.07
    2                     Restaurant  0.06
    3  Vegetarian / Vegan Restaurant  0.04
    4             Italian Restaurant  0.04
    
    
    ----Malvern, Rouge----
                      venue  freq
    0  Fast Food Restaurant  0.10
    1                 Trail  0.10
    2                Bakery  0.05
    3      Greek Restaurant  0.05
    4                  Bank  0.05
    
    
    ----Milliken, Agincourt North, Steeles East, L'Amoreaux East----
                    venue  freq
    0  Chinese Restaurant  0.15
    1                Park  0.06
    2           BBQ Joint  0.06
    3        Intersection  0.06
    4          Playground  0.06
    
    
    ----Mimico NW, The Queensway West, South of Bloor, Kingsway Park South West, Royal York South West----
                      venue  freq
    0            Restaurant  0.08
    1  Gym / Fitness Center  0.07
    2           Yoga Studio  0.03
    3                Bakery  0.03
    4    Italian Restaurant  0.03
    
    
    ----Moore Park, Summerhill East----
                    venue  freq
    0       Grocery Store  0.07
    1  Italian Restaurant  0.07
    2     Thai Restaurant  0.07
    3                Park  0.07
    4         Coffee Shop  0.07
    
    
    ----New Toronto, Mimico South, Humber Bay Shores----
                venue  freq
    0            Park  0.11
    1  Breakfast Spot  0.05
    2   Grocery Store  0.05
    3      Restaurant  0.05
    4     Pizza Place  0.05
    
    
    ----North Park, Maple Leaf Park, Upwood Park----
                    venue  freq
    0         Coffee Shop  0.18
    1        Intersection  0.09
    2  Chinese Restaurant  0.09
    3         Gas Station  0.09
    4  Athletics & Sports  0.09
    
    
    ----North Toronto West----
                     venue  freq
    0   Italian Restaurant  0.07
    1          Coffee Shop  0.07
    2                Diner  0.04
    3   Mexican Restaurant  0.04
    4  Sporting Goods Shop  0.04
    
    
    ----Northwood Park, York University----
                           venue  freq
    0                Pizza Place  0.12
    1                Coffee Shop  0.12
    2                 Restaurant  0.08
    3     Furniture / Home Store  0.08
    4  Middle Eastern Restaurant  0.04
    
    
    ----Old Mill South, King's Mill Park, Sunnylea, Humber Bay, Mimico NE, The Queensway East, Royal York South East, Kingsway Park South East----
                             venue  freq
    0                         Park  0.25
    1                     Bus Stop  0.12
    2  Eastern European Restaurant  0.12
    3                Shopping Mall  0.12
    4         Gym / Fitness Center  0.12
    
    
    ----Ontario Provincial Government----
                     venue  freq
    0          Coffee Shop  0.09
    1  Japanese Restaurant  0.05
    2     Sushi Restaurant  0.05
    3                 Park  0.04
    4          Pizza Place  0.03
    
    
    ----Parkdale, Roncesvalles----
                  venue  freq
    0       Coffee Shop  0.04
    1               Bar  0.04
    2    Breakfast Spot  0.04
    3            Bakery  0.04
    4  Sushi Restaurant  0.04
    
    
    ----Parkview Hill, Woodbine Gardens----
                      venue  freq
    0               Brewery  0.10
    1  Gym / Fitness Center  0.10
    2           Coffee Shop  0.10
    3           Pizza Place  0.10
    4    Rock Climbing Spot  0.05
    
    
    ----Parkwoods----
               venue  freq
    0           Park  0.10
    1       Pharmacy  0.07
    2       Bus Stop  0.07
    3  Shopping Mall  0.07
    4  Grocery Store  0.03
    
    
    ----Regent Park, Harbourfront----
             venue  freq
    0  Coffee Shop  0.15
    1   Restaurant  0.04
    2         Park  0.04
    3          Pub  0.04
    4      Theater  0.04
    
    
    ----Richmond, Adelaide, King----
                     venue  freq
    0          Coffee Shop  0.09
    1                 Café  0.07
    2                Hotel  0.05
    3  Japanese Restaurant  0.04
    4              Theater  0.04
    
    
    ----Rosedale----
               venue  freq
    0    Coffee Shop  0.14
    1           Park  0.10
    2  Grocery Store  0.10
    3      Juice Bar  0.05
    4           Bank  0.05
    
    
    ----Roselawn----
                  venue  freq
    0  Sushi Restaurant  0.12
    1               Spa  0.08
    2          Pharmacy  0.08
    3              Café  0.08
    4              Bank  0.08
    
    
    ----Rouge Hill, Port Union, Highland Creek----
                    venue  freq
    0                Park   0.2
    1          Playground   0.2
    2        Burger Joint   0.2
    3  Italian Restaurant   0.2
    4      Breakfast Spot   0.2
    
    
    ----Runnymede, Swansea----
             venue  freq
    0  Coffee Shop  0.10
    1         Café  0.09
    2  Pizza Place  0.05
    3       Bakery  0.05
    4          Pub  0.04
    
    
    ----Runnymede, The Junction North----
                   venue  freq
    0        Coffee Shop  0.11
    1            Brewery  0.07
    2           Bus Line  0.07
    3        Gas Station  0.05
    4  Convenience Store  0.05
    
    
    ----Scarborough Village----
                      venue  freq
    0        Ice Cream Shop  0.15
    1     Convenience Store  0.08
    2            Restaurant  0.08
    3         Bowling Alley  0.08
    4  Fast Food Restaurant  0.08
    
    
    ----South Steeles, Silverstone, Humbergate, Jamestown, Mount Olive, Beaumond Heights, Thistletown, Albion Gardens----
                     venue  freq
    0          Pizza Place  0.21
    1        Grocery Store  0.16
    2  Japanese Restaurant  0.05
    3       Sandwich Place  0.05
    4             Pharmacy  0.05
    
    
    ----St. James Town----
                    venue  freq
    0         Coffee Shop  0.10
    1                Café  0.08
    2  Seafood Restaurant  0.04
    3          Restaurant  0.04
    4           Gastropub  0.04
    
    
    ----St. James Town, Cabbagetown----
                     venue  freq
    0                 Park  0.06
    1                Diner  0.06
    2            Gastropub  0.06
    3  Japanese Restaurant  0.06
    4          Coffee Shop  0.06
    
    
    ----Steeles West, L'Amoreaux West----
                    venue  freq
    0  Chinese Restaurant  0.12
    1        Intersection  0.09
    2         Coffee Shop  0.09
    3              Bakery  0.06
    4                Bank  0.06
    
    
    ----Studio District----
                       venue  freq
    0            Coffee Shop  0.07
    1                Brewery  0.05
    2                  Diner  0.05
    3    American Restaurant  0.05
    4  Vietnamese Restaurant  0.05
    
    
    ----Summerhill West, Rathnelly, South Hill, Forest Hill SE, Deer Park----
                    venue  freq
    0         Coffee Shop  0.09
    1    Sushi Restaurant  0.06
    2     Thai Restaurant  0.05
    3  Italian Restaurant  0.05
    4                Park  0.05
    
    
    ----The Annex, North Midtown, Yorkville----
                               venue  freq
    0                           Café  0.06
    1             Italian Restaurant  0.05
    2                    Coffee Shop  0.05
    3                            Gym  0.04
    4  Vegetarian / Vegan Restaurant  0.04
    
    
    ----The Beaches----
                     venue  freq
    0          Coffee Shop  0.09
    1                  Pub  0.06
    2                Beach  0.05
    3  Japanese Restaurant  0.04
    4          Pizza Place  0.04
    
    
    ----The Danforth  East----
                      venue  freq
    0                  Café  0.09
    1           Coffee Shop  0.07
    2      Greek Restaurant  0.06
    3  Fast Food Restaurant  0.03
    4              Beer Bar  0.03
    
    
    ----The Danforth West, Riverdale----
                  venue  freq
    0  Greek Restaurant  0.11
    1       Coffee Shop  0.07
    2              Café  0.06
    3               Pub  0.05
    4    Ice Cream Shop  0.03
    
    
    ----The Kingsway, Montgomery Road, Old Mill North----
              venue  freq
    0   Coffee Shop  0.08
    1          Park  0.06
    2          Bank  0.04
    3  Dessert Shop  0.04
    4           Pub  0.04
    
    
    ----Thorncliffe Park----
                    venue  freq
    0         Coffee Shop  0.09
    1   Indian Restaurant  0.08
    2       Grocery Store  0.06
    3  Turkish Restaurant  0.04
    4                Park  0.04
    
    
    ----Toronto Dominion Centre, Design Exchange----
                     venue  freq
    0          Coffee Shop  0.09
    1                 Café  0.07
    2                Hotel  0.05
    3  Japanese Restaurant  0.04
    4              Theater  0.03
    
    
    ----University of Toronto, Harbord----
                               venue  freq
    0                           Café  0.10
    1                         Bakery  0.05
    2  Vegetarian / Vegan Restaurant  0.04
    3                    Coffee Shop  0.04
    4                     Restaurant  0.04
    
    
    ----Victoria Village----
                       venue  freq
    0            Coffee Shop   0.2
    1            Golf Course   0.1
    2   Gym / Fitness Center   0.1
    3  Portuguese Restaurant   0.1
    4    Sporting Goods Shop   0.1
    
    
    ----West Deane Park, Princess Gardens, Martin Grove, Islington, Cloverdale----
                    venue  freq
    0                Park  0.20
    1               Hotel  0.13
    2         Pizza Place  0.13
    3   Fish & Chips Shop  0.07
    4  Mexican Restaurant  0.07
    
    
    ----Westmount----
                venue  freq
    0     Pizza Place  0.17
    1     Gas Station  0.11
    2  Ice Cream Shop  0.06
    3     Coffee Shop  0.06
    4  Breakfast Spot  0.06
    
    
    ----Weston----
                     venue  freq
    0        Train Station  0.12
    1    Convenience Store  0.06
    2                Diner  0.06
    3         Soccer Field  0.06
    4  Fried Chicken Joint  0.06
    
    
    ----Wexford, Maryvale----
                           venue  freq
    0  Middle Eastern Restaurant  0.10
    1              Grocery Store  0.10
    2                Pizza Place  0.10
    3               Burger Joint  0.07
    4           Asian Restaurant  0.03
    
    
    ----Willowdale South----
                      venue  freq
    0           Coffee Shop  0.08
    1       Bubble Tea Shop  0.06
    2     Korean Restaurant  0.06
    3      Ramen Restaurant  0.06
    4  Fast Food Restaurant  0.05
    
    
    ----Willowdale West----
                             venue  freq
    0                  Supermarket  0.14
    1                     Pharmacy  0.14
    2                  Coffee Shop  0.07
    3                         Park  0.07
    4  Eastern European Restaurant  0.07
    
    
    ----Willowdale, Newtonbrook----
                           venue  freq
    0          Korean Restaurant  0.13
    1                       Café  0.10
    2  Middle Eastern Restaurant  0.07
    3                Pizza Place  0.07
    4                Coffee Shop  0.07
    
    
    ----Woburn----
                      venue  freq
    0           Coffee Shop  0.25
    1                  Park  0.25
    2     Mobile Phone Shop  0.12
    3     Indian Restaurant  0.12
    4  Fast Food Restaurant  0.12
    
    
    ----Woodbine Heights----
                venue  freq
    0            Park  0.14
    1     Coffee Shop  0.11
    2     Pizza Place  0.07
    3  Sandwich Place  0.07
    4            Café  0.07
    
    
    ----York Mills West----
                   venue  freq
    0         Restaurant  0.24
    1               Park  0.18
    2        Coffee Shop  0.12
    3  Convenience Store  0.06
    4  French Restaurant  0.06
    
    
    ----York Mills, Silver Hills----
                      venue  freq
    0                  Park  0.75
    1                  Pool  0.25
    2                   Zoo  0.00
    3             Newsagent  0.00
    4  Other Great Outdoors  0.00
    
    
    


```python
def return_most_common_venues(row, num_top_venues):
    row_categories = row.iloc[1:]
    row_categories_sorted = row_categories.sort_values(ascending=False)
    
    return row_categories_sorted.index.values[0:num_top_venues]
```

 Create the new dataframe and display the top 10 venues for each neighborhood


```python
import numpy as np
num_top_venues = 10

indicators = ['st', 'nd', 'rd']

# create columns according to number of top venues
columns = ['Neighborhood']
for ind in np.arange(num_top_venues):
    try:
        columns.append('{}{} Most Common Venue'.format(ind+1, indicators[ind]))
    except:
        columns.append('{}th Most Common Venue'.format(ind+1))

# create a new dataframe
neighborhoods_venues_sorted = pd.DataFrame(columns=columns)
neighborhoods_venues_sorted['Neighborhood'] = toronto_grouped['Neighborhood']

for ind in np.arange(toronto_grouped.shape[0]):
    neighborhoods_venues_sorted.iloc[ind, 1:] = return_most_common_venues(toronto_grouped.iloc[ind, :], num_top_venues)

neighborhoods_venues_sorted.head()
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
      <th>0</th>
      <td>Agincourt</td>
      <td>Chinese Restaurant</td>
      <td>Shopping Mall</td>
      <td>Caribbean Restaurant</td>
      <td>Bakery</td>
      <td>Sandwich Place</td>
      <td>Breakfast Spot</td>
      <td>Malay Restaurant</td>
      <td>Lounge</td>
      <td>Seafood Restaurant</td>
      <td>Café</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alderwood, Long Branch</td>
      <td>Discount Store</td>
      <td>Pizza Place</td>
      <td>Convenience Store</td>
      <td>Pharmacy</td>
      <td>Park</td>
      <td>Playground</td>
      <td>Donut Shop</td>
      <td>Shopping Mall</td>
      <td>Dance Studio</td>
      <td>Intersection</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Bathurst Manor, Wilson Heights, Downsview North</td>
      <td>Pizza Place</td>
      <td>Pharmacy</td>
      <td>Bank</td>
      <td>Park</td>
      <td>Coffee Shop</td>
      <td>Mediterranean Restaurant</td>
      <td>Mobile Phone Shop</td>
      <td>Shopping Mall</td>
      <td>Sandwich Place</td>
      <td>Supermarket</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Bayview Village</td>
      <td>Grocery Store</td>
      <td>Bank</td>
      <td>Gas Station</td>
      <td>Japanese Restaurant</td>
      <td>Restaurant</td>
      <td>Park</td>
      <td>Trail</td>
      <td>Shopping Mall</td>
      <td>Skating Rink</td>
      <td>Chinese Restaurant</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Bedford Park, Lawrence Manor East</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Bank</td>
      <td>Sushi Restaurant</td>
      <td>Fast Food Restaurant</td>
      <td>Sandwich Place</td>
      <td>Restaurant</td>
      <td>Pub</td>
      <td>Baby Store</td>
      <td>Intersection</td>
    </tr>
  </tbody>
</table>
</div>



# Cluster Neighborhoods


```python
kclusters = 5

toronto_grouped_clustering = toronto_grouped.drop('Neighborhood', 1)

# run k-means clustering
kmeans = KMeans(n_clusters=kclusters, random_state=0).fit(toronto_grouped_clustering)

# check cluster labels generated for each row in the dataframe
kmeans.labels_[0:10] 
```




    array([1, 1, 1, 1, 4, 4, 4, 4, 4, 1])




```python
# add clustering labels
neighborhoods_venues_sorted.insert(0, 'Cluster Labels', kmeans.labels_)

toronto_merged = df2

# merge manhattan_grouped with toronto_data to add latitude/longitude for each neighborhood
toronto_merged = toronto_merged.join(neighborhoods_venues_sorted.set_index('Neighborhood'), on='Neighborhood')

toronto_merged.head() # check the last columns!
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
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
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
      <td>1.0</td>
      <td>Park</td>
      <td>Bus Stop</td>
      <td>Pharmacy</td>
      <td>Shopping Mall</td>
      <td>Pizza Place</td>
      <td>Fish &amp; Chips Shop</td>
      <td>Supermarket</td>
      <td>Caribbean Restaurant</td>
      <td>Discount Store</td>
      <td>Tennis Court</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
      <td>43.725882</td>
      <td>-79.315572</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Portuguese Restaurant</td>
      <td>Hockey Arena</td>
      <td>Sporting Goods Shop</td>
      <td>Pizza Place</td>
      <td>Men's Store</td>
      <td>Gym / Fitness Center</td>
      <td>Lounge</td>
      <td>Golf Course</td>
      <td>Electronics Store</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Theater</td>
      <td>Café</td>
      <td>Restaurant</td>
      <td>Park</td>
      <td>Pub</td>
      <td>Diner</td>
      <td>Sushi Restaurant</td>
      <td>Breakfast Spot</td>
      <td>Italian Restaurant</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor, Lawrence Heights</td>
      <td>43.718518</td>
      <td>-79.464763</td>
      <td>4.0</td>
      <td>Furniture / Home Store</td>
      <td>Fast Food Restaurant</td>
      <td>Restaurant</td>
      <td>Clothing Store</td>
      <td>Coffee Shop</td>
      <td>Vietnamese Restaurant</td>
      <td>Accessories Store</td>
      <td>Sushi Restaurant</td>
      <td>Fried Chicken Joint</td>
      <td>Dessert Shop</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Queen's Park</td>
      <td>Ontario Provincial Government</td>
      <td>43.662301</td>
      <td>-79.389494</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Sushi Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Park</td>
      <td>Pizza Place</td>
      <td>Restaurant</td>
      <td>Dance Studio</td>
      <td>Breakfast Spot</td>
      <td>Thai Restaurant</td>
      <td>Middle Eastern Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



Check for missing values


```python
toronto_merged['Cluster Labels'].isna().sum()


```




    1



Remove missing values


```python
toronto_merged.dropna(inplace=True)
```


```python
toronto_merged.shape
```




    (102, 16)




```python
map_clusters = folium.Map(location=[tor_lat, tor_lon], zoom_start=11)

# set color scheme for the clusters
x = np.arange(kclusters)
ys = [i + x + (i*x)**2 for i in range(kclusters)]
colors_array = cm.rainbow(np.linspace(0, 1, len(ys)))
rainbow = [colors.rgb2hex(i) for i in colors_array]

# add markers to the map
markers_colors = []
for lat, lon, poi, cluster in zip(toronto_merged['Latitude'], toronto_merged['Longitude'], toronto_merged['Neighborhood'], toronto_merged['Cluster Labels']):
    label = folium.Popup(str(poi) + ' Cluster ' + str(cluster), parse_html=True)
    folium.CircleMarker(
        [lat, lon],
        radius=5,
        popup=label,
       color = rainbow[int(cluster)-1],
        fill=True,
        fill_color=rainbow[int(cluster)-1],
        fill_opacity=0.7).add_to(map_clusters)
       
map_clusters
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=%3C%21DOCTYPE%20html%3E%0A%3Chead%3E%20%20%20%20%0A%20%20%20%20%3Cmeta%20http-equiv%3D%22content-type%22%20content%3D%22text/html%3B%20charset%3DUTF-8%22%20/%3E%0A%20%20%20%20%3Cscript%3EL_PREFER_CANVAS%20%3D%20false%3B%20L_NO_TOUCH%20%3D%20false%3B%20L_DISABLE_3D%20%3D%20false%3B%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//cdn.jsdelivr.net/npm/leaflet%401.2.0/dist/leaflet.js%22%3E%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js%22%3E%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js%22%3E%3C/script%3E%0A%20%20%20%20%3Cscript%20src%3D%22https%3A//cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js%22%3E%3C/script%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//cdn.jsdelivr.net/npm/leaflet%401.2.0/dist/leaflet.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//maxcdn.bootstrapcdn.com/font-awesome/4.6.3/css/font-awesome.min.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css%22/%3E%0A%20%20%20%20%3Clink%20rel%3D%22stylesheet%22%20href%3D%22https%3A//rawgit.com/python-visualization/folium/master/folium/templates/leaflet.awesome.rotate.css%22/%3E%0A%20%20%20%20%3Cstyle%3Ehtml%2C%20body%20%7Bwidth%3A%20100%25%3Bheight%3A%20100%25%3Bmargin%3A%200%3Bpadding%3A%200%3B%7D%3C/style%3E%0A%20%20%20%20%3Cstyle%3E%23map%20%7Bposition%3Aabsolute%3Btop%3A0%3Bbottom%3A0%3Bright%3A0%3Bleft%3A0%3B%7D%3C/style%3E%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%3Cstyle%3E%20%23map_c2f03717382c44779ba918667c1562dc%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20position%20%3A%20relative%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20width%20%3A%20100.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20height%3A%20100.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20left%3A%200.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20top%3A%200.0%25%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%3C/style%3E%0A%20%20%20%20%20%20%20%20%0A%3C/head%3E%0A%3Cbody%3E%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%3Cdiv%20class%3D%22folium-map%22%20id%3D%22map_c2f03717382c44779ba918667c1562dc%22%20%3E%3C/div%3E%0A%20%20%20%20%20%20%20%20%0A%3C/body%3E%0A%3Cscript%3E%20%20%20%20%0A%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20bounds%20%3D%20null%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20map_c2f03717382c44779ba918667c1562dc%20%3D%20L.map%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%27map_c2f03717382c44779ba918667c1562dc%27%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7Bcenter%3A%20%5B43.6534817%2C-79.3839347%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20zoom%3A%2011%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20maxBounds%3A%20bounds%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20layers%3A%20%5B%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20worldCopyJump%3A%20false%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20crs%3A%20L.CRS.EPSG3857%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7D%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20tile_layer_806322c171004c69a3e4da2d758c3602%20%3D%20L.tileLayer%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%27https%3A//%7Bs%7D.tile.openstreetmap.org/%7Bz%7D/%7Bx%7D/%7By%7D.png%27%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22attribution%22%3A%20null%2C%0A%20%20%22detectRetina%22%3A%20false%2C%0A%20%20%22maxZoom%22%3A%2018%2C%0A%20%20%22minZoom%22%3A%201%2C%0A%20%20%22noWrap%22%3A%20false%2C%0A%20%20%22subdomains%22%3A%20%22abc%22%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_7bcbd870e0e4461282a8448d58723e2d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7532586%2C-79.3296565%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_64cf6e04619c41d789963f034624ba79%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_e6c1d20117d241c9b01185cfb7a6e233%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_e6c1d20117d241c9b01185cfb7a6e233%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EParkwoods%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_64cf6e04619c41d789963f034624ba79.setContent%28html_e6c1d20117d241c9b01185cfb7a6e233%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_7bcbd870e0e4461282a8448d58723e2d.bindPopup%28popup_64cf6e04619c41d789963f034624ba79%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8f5789b09999432ea036505e66f96884%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.725882299999995%2C-79.31557159999998%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_77f64608b8a84f2db1eed8f8ff976494%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_6425a61d0c46452583928d64184251de%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_6425a61d0c46452583928d64184251de%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EVictoria%20Village%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_77f64608b8a84f2db1eed8f8ff976494.setContent%28html_6425a61d0c46452583928d64184251de%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8f5789b09999432ea036505e66f96884.bindPopup%28popup_77f64608b8a84f2db1eed8f8ff976494%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_cd666071811347cbbaf152c9f5572bb0%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6542599%2C-79.3606359%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_54fb3ad9fe76400685055d7db6c4b737%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_93d36dec7922418abf5c10e093000e8e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_93d36dec7922418abf5c10e093000e8e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERegent%20Park%2C%20Harbourfront%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_54fb3ad9fe76400685055d7db6c4b737.setContent%28html_93d36dec7922418abf5c10e093000e8e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_cd666071811347cbbaf152c9f5572bb0.bindPopup%28popup_54fb3ad9fe76400685055d7db6c4b737%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_009e4f577bc1424fa2e74bac816dba16%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.718517999999996%2C-79.46476329999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_25f9b9d637f3444592499c82e8ccf9d8%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_5d1b4c1677d548249efed4a5b4aef938%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_5d1b4c1677d548249efed4a5b4aef938%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ELawrence%20Manor%2C%20Lawrence%20Heights%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_25f9b9d637f3444592499c82e8ccf9d8.setContent%28html_5d1b4c1677d548249efed4a5b4aef938%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_009e4f577bc1424fa2e74bac816dba16.bindPopup%28popup_25f9b9d637f3444592499c82e8ccf9d8%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4a3a95e99805420bbb08a00e7b4f8909%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6623015%2C-79.3894938%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_eb69e369c2594b6b82b5bc881ca3986e%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a58ceae9876e4b5c86bd6fb84e7946f1%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a58ceae9876e4b5c86bd6fb84e7946f1%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EOntario%20Provincial%20Government%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_eb69e369c2594b6b82b5bc881ca3986e.setContent%28html_a58ceae9876e4b5c86bd6fb84e7946f1%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4a3a95e99805420bbb08a00e7b4f8909.bindPopup%28popup_eb69e369c2594b6b82b5bc881ca3986e%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_5afd7301872a4450837d096624a5aeb1%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6678556%2C-79.53224240000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d1d2e482284b45b8bf773be2b4400c1b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a7f48eddded84819aeb9ef1b991dbd26%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a7f48eddded84819aeb9ef1b991dbd26%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EIslington%20Avenue%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d1d2e482284b45b8bf773be2b4400c1b.setContent%28html_a7f48eddded84819aeb9ef1b991dbd26%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_5afd7301872a4450837d096624a5aeb1.bindPopup%28popup_d1d2e482284b45b8bf773be2b4400c1b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_5647d14ce54642b88586d0743d68c8ed%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.806686299999996%2C-79.19435340000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_91201b95e2384adfaefcf6688e5860f4%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_96a998186b004548893c49cd6eeebe91%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_96a998186b004548893c49cd6eeebe91%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EMalvern%2C%20Rouge%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_91201b95e2384adfaefcf6688e5860f4.setContent%28html_96a998186b004548893c49cd6eeebe91%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_5647d14ce54642b88586d0743d68c8ed.bindPopup%28popup_91201b95e2384adfaefcf6688e5860f4%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_1e4ebeb732be4b1a99d0d2365543e383%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.745905799999996%2C-79.352188%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_7b814d70eec24b17bf4408d004be418e%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_cf66fb141fcc43d3ae81412b1d721b41%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_cf66fb141fcc43d3ae81412b1d721b41%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDon%20Mills%20North%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_7b814d70eec24b17bf4408d004be418e.setContent%28html_cf66fb141fcc43d3ae81412b1d721b41%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_1e4ebeb732be4b1a99d0d2365543e383.bindPopup%28popup_7b814d70eec24b17bf4408d004be418e%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4b731f6f69794b989ab9d618650f992f%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7063972%2C-79.309937%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_89603704eefc42dba19faf6ba8b7de41%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_af3f7487d0754b6685a4db2f339f6ba5%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_af3f7487d0754b6685a4db2f339f6ba5%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EParkview%20Hill%2C%20Woodbine%20Gardens%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_89603704eefc42dba19faf6ba8b7de41.setContent%28html_af3f7487d0754b6685a4db2f339f6ba5%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4b731f6f69794b989ab9d618650f992f.bindPopup%28popup_89603704eefc42dba19faf6ba8b7de41%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_87a4b047b9874bd0b4989f9601263466%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6571618%2C-79.37893709999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_c6c78fd2688641218373775fe3a5a353%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_3988deaf66434e5e9aee25c069b0386c%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_3988deaf66434e5e9aee25c069b0386c%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EGarden%20District%2C%20Ryerson%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_c6c78fd2688641218373775fe3a5a353.setContent%28html_3988deaf66434e5e9aee25c069b0386c%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_87a4b047b9874bd0b4989f9601263466.bindPopup%28popup_c6c78fd2688641218373775fe3a5a353%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_e781a35d931748059f0ca43a85f5eb37%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.709577%2C-79.44507259999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_dbdf3efdc5c94abcac519f50e2a70541%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_fd2061fcf0874d5dace57c8e8fff544b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_fd2061fcf0874d5dace57c8e8fff544b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EGlencairn%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_dbdf3efdc5c94abcac519f50e2a70541.setContent%28html_fd2061fcf0874d5dace57c8e8fff544b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_e781a35d931748059f0ca43a85f5eb37.bindPopup%28popup_dbdf3efdc5c94abcac519f50e2a70541%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4be0ae3d615045b5a243179e319d48d4%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6509432%2C-79.55472440000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ff0000%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ff0000%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_00a49500fa7340f08f80a589ac0ee74a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_c7a7fa97116847d38aba8ddefb15253b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_c7a7fa97116847d38aba8ddefb15253b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWest%20Deane%20Park%2C%20Princess%20Gardens%2C%20Martin%20Grove%2C%20Islington%2C%20Cloverdale%20Cluster%200.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_00a49500fa7340f08f80a589ac0ee74a.setContent%28html_c7a7fa97116847d38aba8ddefb15253b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4be0ae3d615045b5a243179e319d48d4.bindPopup%28popup_00a49500fa7340f08f80a589ac0ee74a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f24cf59f5bfa4622895673a7df2c392d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7845351%2C-79.16049709999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ff0000%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ff0000%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_fc297fa915f74174bf674bc579e1bcb5%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_29a35492f45c4bb7a7bc70f6412ae382%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_29a35492f45c4bb7a7bc70f6412ae382%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERouge%20Hill%2C%20Port%20Union%2C%20Highland%20Creek%20Cluster%200.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_fc297fa915f74174bf674bc579e1bcb5.setContent%28html_29a35492f45c4bb7a7bc70f6412ae382%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f24cf59f5bfa4622895673a7df2c392d.bindPopup%28popup_fc297fa915f74174bf674bc579e1bcb5%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_ff48555080c74c80a963d8bbb5533f63%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.72589970000001%2C-79.340923%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_c06a71bc979e48e390adcc417203093e%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_f5586a787fa245319bbfe718af0b028b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_f5586a787fa245319bbfe718af0b028b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDon%20Mills%20South%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_c06a71bc979e48e390adcc417203093e.setContent%28html_f5586a787fa245319bbfe718af0b028b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_ff48555080c74c80a963d8bbb5533f63.bindPopup%28popup_c06a71bc979e48e390adcc417203093e%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_7ca6f6445ba44845883a5da7cc70754d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.695343900000005%2C-79.3183887%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_45026c17bfb443668eea10eb6b7a4329%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_c8e227d7c8174ad9931d573c1a2b2ec8%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_c8e227d7c8174ad9931d573c1a2b2ec8%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWoodbine%20Heights%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_45026c17bfb443668eea10eb6b7a4329.setContent%28html_c8e227d7c8174ad9931d573c1a2b2ec8%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_7ca6f6445ba44845883a5da7cc70754d.bindPopup%28popup_45026c17bfb443668eea10eb6b7a4329%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_2e8ed7e38943430dabc030006942b327%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6514939%2C-79.3754179%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_ceec137979e64382bf7dc075f0e02ecf%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_160d0ac9e82e443ba5d633985c2b643a%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_160d0ac9e82e443ba5d633985c2b643a%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ESt.%20James%20Town%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_ceec137979e64382bf7dc075f0e02ecf.setContent%28html_160d0ac9e82e443ba5d633985c2b643a%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_2e8ed7e38943430dabc030006942b327.bindPopup%28popup_ceec137979e64382bf7dc075f0e02ecf%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4c32f55a4596421a9d199aa060d2747d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6937813%2C-79.42819140000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_3b4ec64cc5444f788581d2aed7f7dd7c%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_c1cfafb937774d5b86db4168c156e6ec%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_c1cfafb937774d5b86db4168c156e6ec%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHumewood-Cedarvale%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_3b4ec64cc5444f788581d2aed7f7dd7c.setContent%28html_c1cfafb937774d5b86db4168c156e6ec%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4c32f55a4596421a9d199aa060d2747d.bindPopup%28popup_3b4ec64cc5444f788581d2aed7f7dd7c%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_282cb9680f6f4daa8a15e417dc4cbb83%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6435152%2C-79.57720079999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_34c1e64a8e144f71a44d1a677fbdf358%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_0efe9b47ecb84ca4b378db192436afec%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_0efe9b47ecb84ca4b378db192436afec%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EEringate%2C%20Bloordale%20Gardens%2C%20Old%20Burnhamthorpe%2C%20Markland%20Wood%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_34c1e64a8e144f71a44d1a677fbdf358.setContent%28html_0efe9b47ecb84ca4b378db192436afec%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_282cb9680f6f4daa8a15e417dc4cbb83.bindPopup%28popup_34c1e64a8e144f71a44d1a677fbdf358%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_b9031129ad8e4c2caf60c2a52c84f769%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7635726%2C-79.1887115%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_09ab1d34956440e4a98d380354a1e2db%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_ddf352dc38724060a0a68ba2066954cf%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_ddf352dc38724060a0a68ba2066954cf%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EGuildwood%2C%20Morningside%2C%20West%20Hill%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_09ab1d34956440e4a98d380354a1e2db.setContent%28html_ddf352dc38724060a0a68ba2066954cf%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_b9031129ad8e4c2caf60c2a52c84f769.bindPopup%28popup_09ab1d34956440e4a98d380354a1e2db%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_08aef18d88454682a6f5380661e1774c%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.67635739999999%2C-79.2930312%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d12b26aa728649fd9f3eca92d6f93eb8%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_54537687e8c8469b93b378f731f0fba2%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_54537687e8c8469b93b378f731f0fba2%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThe%20Beaches%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d12b26aa728649fd9f3eca92d6f93eb8.setContent%28html_54537687e8c8469b93b378f731f0fba2%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_08aef18d88454682a6f5380661e1774c.bindPopup%28popup_d12b26aa728649fd9f3eca92d6f93eb8%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_03bde9ea6d0e46baa6eda5e5784e3092%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.644770799999996%2C-79.3733064%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f522ecc05f0346ad9b1ac7e933b8a17b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_ca4e146b1e0047478a324d7cff6507e8%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_ca4e146b1e0047478a324d7cff6507e8%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBerczy%20Park%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f522ecc05f0346ad9b1ac7e933b8a17b.setContent%28html_ca4e146b1e0047478a324d7cff6507e8%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_03bde9ea6d0e46baa6eda5e5784e3092.bindPopup%28popup_f522ecc05f0346ad9b1ac7e933b8a17b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8be23cd004c1453b88b5989480ddd0b5%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6890256%2C-79.453512%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f988db82e692479cb7d185fb75b4f207%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_25763246edb3412cbafed4b1ac1c4c9d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_25763246edb3412cbafed4b1ac1c4c9d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECaledonia-Fairbanks%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f988db82e692479cb7d185fb75b4f207.setContent%28html_25763246edb3412cbafed4b1ac1c4c9d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8be23cd004c1453b88b5989480ddd0b5.bindPopup%28popup_f988db82e692479cb7d185fb75b4f207%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_7041d86748354e0fa868b66a0948425d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7709921%2C-79.21691740000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_a3584084cb624de385eb96cff220eae5%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_94c2da46949a48d0abdf97701c8e8eac%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_94c2da46949a48d0abdf97701c8e8eac%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWoburn%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_a3584084cb624de385eb96cff220eae5.setContent%28html_94c2da46949a48d0abdf97701c8e8eac%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_7041d86748354e0fa868b66a0948425d.bindPopup%28popup_a3584084cb624de385eb96cff220eae5%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_69604f1af8764ad683603c3d3ec5765d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7090604%2C-79.3634517%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_234f82b1f8574457864258ba05027101%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_dea60502afd64747b6d94a03c6dcf5e8%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_dea60502afd64747b6d94a03c6dcf5e8%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ELeaside%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_234f82b1f8574457864258ba05027101.setContent%28html_dea60502afd64747b6d94a03c6dcf5e8%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_69604f1af8764ad683603c3d3ec5765d.bindPopup%28popup_234f82b1f8574457864258ba05027101%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_684dd25738c9475da6299c94c298e807%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6579524%2C-79.3873826%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_4bc34fdd076548908b7c23a81f8ecafc%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_035ce5dcfb7c42c4b66e5e76204de708%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_035ce5dcfb7c42c4b66e5e76204de708%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECentral%20Bay%20Street%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_4bc34fdd076548908b7c23a81f8ecafc.setContent%28html_035ce5dcfb7c42c4b66e5e76204de708%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_684dd25738c9475da6299c94c298e807.bindPopup%28popup_4bc34fdd076548908b7c23a81f8ecafc%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_846b174e13c149ada7631fcc3214811e%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.669542%2C-79.4225637%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_79de0835875b471f85baf0a02401e0e9%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a68e6a247bff44b8b9b2d4b230a0dc65%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a68e6a247bff44b8b9b2d4b230a0dc65%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EChristie%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_79de0835875b471f85baf0a02401e0e9.setContent%28html_a68e6a247bff44b8b9b2d4b230a0dc65%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_846b174e13c149ada7631fcc3214811e.bindPopup%28popup_79de0835875b471f85baf0a02401e0e9%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_6b7e7cb3fe8d477f86456cc29f3868b1%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.773136%2C-79.23947609999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f741a26726004044a5ec2efaa7a31587%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_33eeb3d86923433d91550c69c7c28368%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_33eeb3d86923433d91550c69c7c28368%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECedarbrae%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f741a26726004044a5ec2efaa7a31587.setContent%28html_33eeb3d86923433d91550c69c7c28368%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_6b7e7cb3fe8d477f86456cc29f3868b1.bindPopup%28popup_f741a26726004044a5ec2efaa7a31587%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_ebc9e31fd7bf4c0bb8e8f4beec073e15%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.8037622%2C-79.3634517%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8ee243c872834d66add3ce8d3d64fac4%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_784d5bd1732b42c0a5425fd50ef212a6%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_784d5bd1732b42c0a5425fd50ef212a6%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHillcrest%20Village%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8ee243c872834d66add3ce8d3d64fac4.setContent%28html_784d5bd1732b42c0a5425fd50ef212a6%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_ebc9e31fd7bf4c0bb8e8f4beec073e15.bindPopup%28popup_8ee243c872834d66add3ce8d3d64fac4%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f8059cd4763041738a5671c6e53f8b35%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7543283%2C-79.4422593%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_1f9bcfc89d9f45898d75c8daf63e7d39%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_84285f7b4a084b13af9f66207944e0f9%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_84285f7b4a084b13af9f66207944e0f9%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBathurst%20Manor%2C%20Wilson%20Heights%2C%20Downsview%20North%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_1f9bcfc89d9f45898d75c8daf63e7d39.setContent%28html_84285f7b4a084b13af9f66207944e0f9%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f8059cd4763041738a5671c6e53f8b35.bindPopup%28popup_1f9bcfc89d9f45898d75c8daf63e7d39%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8892bee106774d6d93673ca30db571cd%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7053689%2C-79.34937190000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_5b890abb457a463f98c585b3c92c2bd8%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a333b1af8ca54944b7442ec3fea96988%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a333b1af8ca54944b7442ec3fea96988%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThorncliffe%20Park%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_5b890abb457a463f98c585b3c92c2bd8.setContent%28html_a333b1af8ca54944b7442ec3fea96988%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8892bee106774d6d93673ca30db571cd.bindPopup%28popup_5b890abb457a463f98c585b3c92c2bd8%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_0a4c531f49b140ae8376802fb054c457%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.65057120000001%2C-79.3845675%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_3e66848a035946309d16db207de30cbe%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_662b789d0a90412384b1cd1d19087bf5%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_662b789d0a90412384b1cd1d19087bf5%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERichmond%2C%20Adelaide%2C%20King%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_3e66848a035946309d16db207de30cbe.setContent%28html_662b789d0a90412384b1cd1d19087bf5%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_0a4c531f49b140ae8376802fb054c457.bindPopup%28popup_3e66848a035946309d16db207de30cbe%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_3aa588253f6d44929d66deaef4f424a0%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.66900510000001%2C-79.4422593%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_671895884e324863a645e7f6e13f5ef5%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_59b776ef2b8f40dfb945956c87e0b107%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_59b776ef2b8f40dfb945956c87e0b107%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDufferin%2C%20Dovercourt%20Village%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_671895884e324863a645e7f6e13f5ef5.setContent%28html_59b776ef2b8f40dfb945956c87e0b107%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_3aa588253f6d44929d66deaef4f424a0.bindPopup%28popup_671895884e324863a645e7f6e13f5ef5%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_995afe5f26d24d588d3e40a586327001%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7447342%2C-79.23947609999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8a274012555a400a96216f0b1286fd69%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_17e378a149e547a6b89d2cf7c27ead3e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_17e378a149e547a6b89d2cf7c27ead3e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EScarborough%20Village%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8a274012555a400a96216f0b1286fd69.setContent%28html_17e378a149e547a6b89d2cf7c27ead3e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_995afe5f26d24d588d3e40a586327001.bindPopup%28popup_8a274012555a400a96216f0b1286fd69%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_ff346477c4634644a74e212b221f826a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7785175%2C-79.3465557%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_0b2090b3ac844a0d86b3f50a3f0e36fd%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_f97d3edf998e4277a169860291c215cb%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_f97d3edf998e4277a169860291c215cb%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EFairview%2C%20Henry%20Farm%2C%20Oriole%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_0b2090b3ac844a0d86b3f50a3f0e36fd.setContent%28html_f97d3edf998e4277a169860291c215cb%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_ff346477c4634644a74e212b221f826a.bindPopup%28popup_0b2090b3ac844a0d86b3f50a3f0e36fd%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_1f906525922d4939ab08bc180559609e%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7679803%2C-79.48726190000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_1094fc4956d94b7492425df65f6526f7%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_1873f04dd1ad4f269e0470390ff6ced1%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_1873f04dd1ad4f269e0470390ff6ced1%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ENorthwood%20Park%2C%20York%20University%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_1094fc4956d94b7492425df65f6526f7.setContent%28html_1873f04dd1ad4f269e0470390ff6ced1%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_1f906525922d4939ab08bc180559609e.bindPopup%28popup_1094fc4956d94b7492425df65f6526f7%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_208195643c22415fbef7d7b8300be523%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.685347%2C-79.3381065%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8e16853b5c904ff1ba526674a8e2d8e3%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_bc16bb86193541258e64a6ec4bd63c4b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_bc16bb86193541258e64a6ec4bd63c4b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThe%20Danforth%20%20East%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8e16853b5c904ff1ba526674a8e2d8e3.setContent%28html_bc16bb86193541258e64a6ec4bd63c4b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_208195643c22415fbef7d7b8300be523.bindPopup%28popup_8e16853b5c904ff1ba526674a8e2d8e3%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f34977ae2ee94dc499c29801e6147856%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6408157%2C-79.38175229999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_98f1b656527c4cf2893df87190b1a026%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_7c9b288458f24879b4ecdec5e20e4644%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_7c9b288458f24879b4ecdec5e20e4644%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHarbourfront%20East%2C%20Union%20Station%2C%20Toronto%20Islands%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_98f1b656527c4cf2893df87190b1a026.setContent%28html_7c9b288458f24879b4ecdec5e20e4644%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f34977ae2ee94dc499c29801e6147856.bindPopup%28popup_98f1b656527c4cf2893df87190b1a026%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_9732b19e80d54ffda0d1b6ec77e30694%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.647926700000006%2C-79.4197497%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_853d74693c364a09953999fe0ec866ce%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_31760698777b44e6a16ebd9b3c7c6cfa%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_31760698777b44e6a16ebd9b3c7c6cfa%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ELittle%20Portugal%2C%20Trinity%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_853d74693c364a09953999fe0ec866ce.setContent%28html_31760698777b44e6a16ebd9b3c7c6cfa%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_9732b19e80d54ffda0d1b6ec77e30694.bindPopup%28popup_853d74693c364a09953999fe0ec866ce%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_bfdfa8abc2e64450a5dfc5fb12308f02%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7279292%2C-79.26202940000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_4c94ecb22a7c46728ab53a6602b79178%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_581742fe17394d84971c74bab7bec9d2%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_581742fe17394d84971c74bab7bec9d2%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EKennedy%20Park%2C%20Ionview%2C%20East%20Birchmount%20Park%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_4c94ecb22a7c46728ab53a6602b79178.setContent%28html_581742fe17394d84971c74bab7bec9d2%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_bfdfa8abc2e64450a5dfc5fb12308f02.bindPopup%28popup_4c94ecb22a7c46728ab53a6602b79178%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_eefb9c6198014af3b68ce8b0256095eb%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7869473%2C-79.385975%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_75416d68bcb541479edcc5375a54adb0%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_27e2df1feddd4dffa1f0823aad3f6962%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_27e2df1feddd4dffa1f0823aad3f6962%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBayview%20Village%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_75416d68bcb541479edcc5375a54adb0.setContent%28html_27e2df1feddd4dffa1f0823aad3f6962%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_eefb9c6198014af3b68ce8b0256095eb.bindPopup%28popup_75416d68bcb541479edcc5375a54adb0%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_c3e810c905654fedb0084ee6611c33aa%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.737473200000004%2C-79.46476329999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_753ad06992fb4119b2e650e8667a8d00%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_de8fde90328e4b469d7e3e45508c992f%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_de8fde90328e4b469d7e3e45508c992f%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDownsview%20East%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_753ad06992fb4119b2e650e8667a8d00.setContent%28html_de8fde90328e4b469d7e3e45508c992f%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_c3e810c905654fedb0084ee6611c33aa.bindPopup%28popup_753ad06992fb4119b2e650e8667a8d00%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_780fa8a566ce48d09c2f8f8a75da0bad%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6795571%2C-79.352188%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f34fa0c1a3994b5dad9abd1699a7dd35%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_cc7d9ff481ea424e8121f1b5f8cfdd78%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_cc7d9ff481ea424e8121f1b5f8cfdd78%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThe%20Danforth%20West%2C%20Riverdale%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f34fa0c1a3994b5dad9abd1699a7dd35.setContent%28html_cc7d9ff481ea424e8121f1b5f8cfdd78%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_780fa8a566ce48d09c2f8f8a75da0bad.bindPopup%28popup_f34fa0c1a3994b5dad9abd1699a7dd35%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_3c97c0b8f47040ba94321b1a7648e6fc%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6471768%2C-79.38157640000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f6bccd86e6ef44d6b925e3168d5f9dbd%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_4ead294f32a24efb9f2bdb17675f976e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_4ead294f32a24efb9f2bdb17675f976e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EToronto%20Dominion%20Centre%2C%20Design%20Exchange%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f6bccd86e6ef44d6b925e3168d5f9dbd.setContent%28html_4ead294f32a24efb9f2bdb17675f976e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_3c97c0b8f47040ba94321b1a7648e6fc.bindPopup%28popup_f6bccd86e6ef44d6b925e3168d5f9dbd%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_98321787782b4cfa980efe5a7ce9aeca%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6368472%2C-79.42819140000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_618f7fd997594a8499f1a3c4042f0057%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_710233e440fc464eb75d784a73f966c0%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_710233e440fc464eb75d784a73f966c0%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBrockton%2C%20Parkdale%20Village%2C%20Exhibition%20Place%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_618f7fd997594a8499f1a3c4042f0057.setContent%28html_710233e440fc464eb75d784a73f966c0%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_98321787782b4cfa980efe5a7ce9aeca.bindPopup%28popup_618f7fd997594a8499f1a3c4042f0057%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4f9d5362f6464ef684c3947b70f5655e%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.711111700000004%2C-79.2845772%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_69d3ea89d12d45ca81dcb721d37b373c%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_b28dafe291084b329972f6071381d162%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_b28dafe291084b329972f6071381d162%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EGolden%20Mile%2C%20Clairlea%2C%20Oakridge%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_69d3ea89d12d45ca81dcb721d37b373c.setContent%28html_b28dafe291084b329972f6071381d162%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4f9d5362f6464ef684c3947b70f5655e.bindPopup%28popup_69d3ea89d12d45ca81dcb721d37b373c%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_353c542f88574f90a7664f26d5509ede%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7574902%2C-79.37471409999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ff0000%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ff0000%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_550bdd4940f1430da113d878c9137b2a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a84b8fe136034ba087b64c97d10a40b8%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a84b8fe136034ba087b64c97d10a40b8%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EYork%20Mills%2C%20Silver%20Hills%20Cluster%200.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_550bdd4940f1430da113d878c9137b2a.setContent%28html_a84b8fe136034ba087b64c97d10a40b8%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_353c542f88574f90a7664f26d5509ede.bindPopup%28popup_550bdd4940f1430da113d878c9137b2a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_52360a77a8c84b41ad988151ae5cbd3c%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7390146%2C-79.5069436%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_2197dd26f20e486ba7eb7a5fb2633b75%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_6c1fb50d31e74dfba75a53824f50d676%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_6c1fb50d31e74dfba75a53824f50d676%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDownsview%20West%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_2197dd26f20e486ba7eb7a5fb2633b75.setContent%28html_6c1fb50d31e74dfba75a53824f50d676%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_52360a77a8c84b41ad988151ae5cbd3c.bindPopup%28popup_2197dd26f20e486ba7eb7a5fb2633b75%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_9adcba25a41947f1878bef30a4879e07%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6689985%2C-79.31557159999998%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_251187c28a72412294dcc7a81604b7eb%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_b37845482ae042a387029445fbdb7b12%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_b37845482ae042a387029445fbdb7b12%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EIndia%20Bazaar%2C%20The%20Beaches%20West%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_251187c28a72412294dcc7a81604b7eb.setContent%28html_b37845482ae042a387029445fbdb7b12%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_9adcba25a41947f1878bef30a4879e07.bindPopup%28popup_251187c28a72412294dcc7a81604b7eb%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_5c2b5553f1f543c3b2e7f216bb0b42c4%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6481985%2C-79.37981690000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_4209b9cdfbfe49fa9df62d2a15ef131d%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_3f84dd7232664699acd5e29f3e1d5b35%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_3f84dd7232664699acd5e29f3e1d5b35%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECommerce%20Court%2C%20Victoria%20Hotel%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_4209b9cdfbfe49fa9df62d2a15ef131d.setContent%28html_3f84dd7232664699acd5e29f3e1d5b35%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_5c2b5553f1f543c3b2e7f216bb0b42c4.bindPopup%28popup_4209b9cdfbfe49fa9df62d2a15ef131d%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_abcbf2f5434a44abb18228b78dc7a21d%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.713756200000006%2C-79.4900738%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_67081628462c4dc8b8030d928461a5bd%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_11b8ec98f6364c888dec04deab36af5e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_11b8ec98f6364c888dec04deab36af5e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ENorth%20Park%2C%20Maple%20Leaf%20Park%2C%20Upwood%20Park%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_67081628462c4dc8b8030d928461a5bd.setContent%28html_11b8ec98f6364c888dec04deab36af5e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_abcbf2f5434a44abb18228b78dc7a21d.bindPopup%28popup_67081628462c4dc8b8030d928461a5bd%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_c28b2e90d7d84059b99e15d635f3a861%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7563033%2C-79.56596329999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ff0000%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ff0000%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_2aef426360764a89bda239bf26f1d313%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_0bb619618e37491a9627d4f995c9e366%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_0bb619618e37491a9627d4f995c9e366%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHumber%20Summit%20Cluster%200.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_2aef426360764a89bda239bf26f1d313.setContent%28html_0bb619618e37491a9627d4f995c9e366%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_c28b2e90d7d84059b99e15d635f3a861.bindPopup%28popup_2aef426360764a89bda239bf26f1d313%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_90691b40a94440d895d8f523e6c0f979%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.716316%2C-79.23947609999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_a4e76d3d1b194c99ba3cd4ebc1d4e590%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_514714060d6143cfbeb2f8ff10bf5797%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_514714060d6143cfbeb2f8ff10bf5797%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECliffside%2C%20Cliffcrest%2C%20Scarborough%20Village%20West%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_a4e76d3d1b194c99ba3cd4ebc1d4e590.setContent%28html_514714060d6143cfbeb2f8ff10bf5797%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_90691b40a94440d895d8f523e6c0f979.bindPopup%28popup_a4e76d3d1b194c99ba3cd4ebc1d4e590%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_cd1ae7e80c504fa3a2e150850df05031%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.789053%2C-79.40849279999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f57fe2aacec640e09ce5c1900082f43f%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_7a9f6a99cfc44266847202b6294c683d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_7a9f6a99cfc44266847202b6294c683d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWillowdale%2C%20Newtonbrook%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f57fe2aacec640e09ce5c1900082f43f.setContent%28html_7a9f6a99cfc44266847202b6294c683d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_cd1ae7e80c504fa3a2e150850df05031.bindPopup%28popup_f57fe2aacec640e09ce5c1900082f43f%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_63b85e6c421b42c0b30ca99b5a50a3da%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7284964%2C-79.49569740000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%2380ffb4%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%2380ffb4%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f67172f577174545a11df7b536ba3855%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_6c8b532c7b7c419786b4f3378818ac63%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_6c8b532c7b7c419786b4f3378818ac63%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDownsview%20Central%20Cluster%203.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f67172f577174545a11df7b536ba3855.setContent%28html_6c8b532c7b7c419786b4f3378818ac63%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_63b85e6c421b42c0b30ca99b5a50a3da.bindPopup%28popup_f67172f577174545a11df7b536ba3855%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_52fb1f3d12834512a5da570c7e77c93b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6595255%2C-79.340923%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_5070131ae89e4203b51570982835d735%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_83bf64e160d94d57950efa9d1d102c45%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_83bf64e160d94d57950efa9d1d102c45%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EStudio%20District%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_5070131ae89e4203b51570982835d735.setContent%28html_83bf64e160d94d57950efa9d1d102c45%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_52fb1f3d12834512a5da570c7e77c93b.bindPopup%28popup_5070131ae89e4203b51570982835d735%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f7e9e4a083534cf4b0e2af9b517faed1%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7332825%2C-79.4197497%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_162c4627c4cc4a22a6a58b1b621745c9%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_42b7bc4effa44536b72bc80744311f05%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_42b7bc4effa44536b72bc80744311f05%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBedford%20Park%2C%20Lawrence%20Manor%20East%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_162c4627c4cc4a22a6a58b1b621745c9.setContent%28html_42b7bc4effa44536b72bc80744311f05%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f7e9e4a083534cf4b0e2af9b517faed1.bindPopup%28popup_162c4627c4cc4a22a6a58b1b621745c9%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_206ae644d87f42478407c68bc2f51ca6%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6911158%2C-79.47601329999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d5e6d29cee144a138cc3494ece8f8bce%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_e9d6077584f54b3da8af55b21d1a1063%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_e9d6077584f54b3da8af55b21d1a1063%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDel%20Ray%2C%20Mount%20Dennis%2C%20Keelsdale%20and%20Silverthorn%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d5e6d29cee144a138cc3494ece8f8bce.setContent%28html_e9d6077584f54b3da8af55b21d1a1063%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_206ae644d87f42478407c68bc2f51ca6.bindPopup%28popup_d5e6d29cee144a138cc3494ece8f8bce%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8004bdc355724200b2ff1222e514f820%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7247659%2C-79.53224240000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ff0000%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ff0000%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_68b7056dfe394c75976e30275eeeabb0%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_721146ec79b042f9a8a096e559645a30%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_721146ec79b042f9a8a096e559645a30%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHumberlea%2C%20Emery%20Cluster%200.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_68b7056dfe394c75976e30275eeeabb0.setContent%28html_721146ec79b042f9a8a096e559645a30%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8004bdc355724200b2ff1222e514f820.bindPopup%28popup_68b7056dfe394c75976e30275eeeabb0%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_adf1732a3f71499b828d67ed3e048923%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.692657000000004%2C-79.2648481%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d5701a5dffba43c4ba8352363616818b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_1f4b8e3f383844fdb90f0db533129a3d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_1f4b8e3f383844fdb90f0db533129a3d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EBirch%20Cliff%2C%20Cliffside%20West%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d5701a5dffba43c4ba8352363616818b.setContent%28html_1f4b8e3f383844fdb90f0db533129a3d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_adf1732a3f71499b828d67ed3e048923.bindPopup%28popup_d5701a5dffba43c4ba8352363616818b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_a4d613e2d7424be192c20af42f385aaa%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7701199%2C-79.40849279999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_837639533fee41e6b07a4495ff54b5bf%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_8a8bcfafdf5549ff88fdec8a31e1841c%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_8a8bcfafdf5549ff88fdec8a31e1841c%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWillowdale%20South%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_837639533fee41e6b07a4495ff54b5bf.setContent%28html_8a8bcfafdf5549ff88fdec8a31e1841c%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_a4d613e2d7424be192c20af42f385aaa.bindPopup%28popup_837639533fee41e6b07a4495ff54b5bf%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4fc5c74c34f64b4d9c6a1f808bf8fd0f%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7616313%2C-79.52099940000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_43744e7166554869a3f6409996ac50cd%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_43b807ebdb4e4dc38a4d8d6de5c409da%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_43b807ebdb4e4dc38a4d8d6de5c409da%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDownsview%20Northwest%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_43744e7166554869a3f6409996ac50cd.setContent%28html_43b807ebdb4e4dc38a4d8d6de5c409da%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4fc5c74c34f64b4d9c6a1f808bf8fd0f.bindPopup%28popup_43744e7166554869a3f6409996ac50cd%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_06d08d2fca23462ca34a3462c25d0450%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7280205%2C-79.3887901%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_7173fe7c3ef447a4875099c83b08ba4a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_4a7f27a3f4a64fe5aa71f00191091b4a%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_4a7f27a3f4a64fe5aa71f00191091b4a%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ELawrence%20Park%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_7173fe7c3ef447a4875099c83b08ba4a.setContent%28html_4a7f27a3f4a64fe5aa71f00191091b4a%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_06d08d2fca23462ca34a3462c25d0450.bindPopup%28popup_7173fe7c3ef447a4875099c83b08ba4a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8efb069bb97d46e6b25687f66bc05996%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7116948%2C-79.41693559999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_1eff4a0e772742188a43f283ee9d80ad%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_c0788fd8f04142a0a53506e67adf1bcd%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_c0788fd8f04142a0a53506e67adf1bcd%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERoselawn%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_1eff4a0e772742188a43f283ee9d80ad.setContent%28html_c0788fd8f04142a0a53506e67adf1bcd%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8efb069bb97d46e6b25687f66bc05996.bindPopup%28popup_1eff4a0e772742188a43f283ee9d80ad%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_c7ab4977d08f4643a844aee9090bcbd2%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.67318529999999%2C-79.48726190000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_57885da7bd284bbd81c1278d535e15b2%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_b23217a00d75470b911fd06d44c0153e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_b23217a00d75470b911fd06d44c0153e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERunnymede%2C%20The%20Junction%20North%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_57885da7bd284bbd81c1278d535e15b2.setContent%28html_b23217a00d75470b911fd06d44c0153e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_c7ab4977d08f4643a844aee9090bcbd2.bindPopup%28popup_57885da7bd284bbd81c1278d535e15b2%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_98def019802a4857ace158aa78b1352b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.706876%2C-79.51818840000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_9301d08c25c842909fde3971dc337f99%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a0d88f39c24c4945ba90ef943f94d4ce%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a0d88f39c24c4945ba90ef943f94d4ce%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWeston%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_9301d08c25c842909fde3971dc337f99.setContent%28html_a0d88f39c24c4945ba90ef943f94d4ce%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_98def019802a4857ace158aa78b1352b.bindPopup%28popup_9301d08c25c842909fde3971dc337f99%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_7ff3d09d77b54ed585d89891680fb702%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7574096%2C-79.27330400000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_404b3b4e167e4590a951f3469dc7450d%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_a775e552df6a42358f8967fa967bda96%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_a775e552df6a42358f8967fa967bda96%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDorset%20Park%2C%20Wexford%20Heights%2C%20Scarborough%20Town%20Centre%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_404b3b4e167e4590a951f3469dc7450d.setContent%28html_a775e552df6a42358f8967fa967bda96%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_7ff3d09d77b54ed585d89891680fb702.bindPopup%28popup_404b3b4e167e4590a951f3469dc7450d%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_7f1216e9985c476b9f1be0c28f4df7b1%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.752758299999996%2C-79.4000493%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_8364e66331e748dcb23ee9d0a7a0d9be%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_98bb441c89a448dc8f7078da25b9e990%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_98bb441c89a448dc8f7078da25b9e990%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EYork%20Mills%20West%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_8364e66331e748dcb23ee9d0a7a0d9be.setContent%28html_98bb441c89a448dc8f7078da25b9e990%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_7f1216e9985c476b9f1be0c28f4df7b1.bindPopup%28popup_8364e66331e748dcb23ee9d0a7a0d9be%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_1094b14d2deb400bba6cc6ff8eca543b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7127511%2C-79.3901975%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_28a520f491d349df9c774ae46fa56565%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_986b9a40a6c548f9baa659b41a671eed%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_986b9a40a6c548f9baa659b41a671eed%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDavisville%20North%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_28a520f491d349df9c774ae46fa56565.setContent%28html_986b9a40a6c548f9baa659b41a671eed%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_1094b14d2deb400bba6cc6ff8eca543b.bindPopup%28popup_28a520f491d349df9c774ae46fa56565%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_d26d6d21477c4444979c49259c7633c4%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6969476%2C-79.41130720000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d6f9a479ed2148dd88f6d4f632c83e53%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_dcad4ebab2514f239efd8eb15b372bae%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_dcad4ebab2514f239efd8eb15b372bae%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EForest%20Hill%20North%20%26amp%3B%20West%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d6f9a479ed2148dd88f6d4f632c83e53.setContent%28html_dcad4ebab2514f239efd8eb15b372bae%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_d26d6d21477c4444979c49259c7633c4.bindPopup%28popup_d6f9a479ed2148dd88f6d4f632c83e53%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_3e302059a23440afb8e539bea573f9f2%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6616083%2C-79.46476329999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_ce21599275d44b29b3ece6dab5bd75ea%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_2b4758d41d8c46479475a0816fc0b34e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_2b4758d41d8c46479475a0816fc0b34e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EHigh%20Park%2C%20The%20Junction%20South%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_ce21599275d44b29b3ece6dab5bd75ea.setContent%28html_2b4758d41d8c46479475a0816fc0b34e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_3e302059a23440afb8e539bea573f9f2.bindPopup%28popup_ce21599275d44b29b3ece6dab5bd75ea%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8fa2e63c75af4235893224abfea605bc%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.696319%2C-79.53224240000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_9e46fe88847b45f3b6ca3fd2f94bc64c%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_0afe99d2ec414013a48eba4e79b898c4%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_0afe99d2ec414013a48eba4e79b898c4%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWestmount%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_9e46fe88847b45f3b6ca3fd2f94bc64c.setContent%28html_0afe99d2ec414013a48eba4e79b898c4%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8fa2e63c75af4235893224abfea605bc.bindPopup%28popup_9e46fe88847b45f3b6ca3fd2f94bc64c%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_7d088ecd182a4fd08e6de9e9d2c73abf%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.750071500000004%2C-79.2958491%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_f158858ca6cf43eb8fa0ca579b8cda70%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_0bb5e0ae27484954a9fd03fb4db0da6d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_0bb5e0ae27484954a9fd03fb4db0da6d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWexford%2C%20Maryvale%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_f158858ca6cf43eb8fa0ca579b8cda70.setContent%28html_0bb5e0ae27484954a9fd03fb4db0da6d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_7d088ecd182a4fd08e6de9e9d2c73abf.bindPopup%28popup_f158858ca6cf43eb8fa0ca579b8cda70%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_e2fe5cceb4234bb393ee53f143fb60af%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7827364%2C-79.4422593%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_c1f2cbe43e7444fc931290f98fbb2235%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_bf11e62e6eec403b9f1ad8fe3971aa2b%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_bf11e62e6eec403b9f1ad8fe3971aa2b%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EWillowdale%20West%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_c1f2cbe43e7444fc931290f98fbb2235.setContent%28html_bf11e62e6eec403b9f1ad8fe3971aa2b%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_e2fe5cceb4234bb393ee53f143fb60af.bindPopup%28popup_c1f2cbe43e7444fc931290f98fbb2235%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_5c5d4c11cc104485bbcbfaf857a35640%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7153834%2C-79.40567840000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_646332615ed845ed90ff4a0e2540b126%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_7beeb73e8d114019bbd820ca12ee8b7d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_7beeb73e8d114019bbd820ca12ee8b7d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ENorth%20Toronto%20West%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_646332615ed845ed90ff4a0e2540b126.setContent%28html_7beeb73e8d114019bbd820ca12ee8b7d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_5c5d4c11cc104485bbcbfaf857a35640.bindPopup%28popup_646332615ed845ed90ff4a0e2540b126%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_8aa2bdb356ca4d89813da81f67ed6d35%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6727097%2C-79.40567840000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_53cc021ca1c34938913b3f3769dd84e8%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_d41c818d8bc34157abbeb2511b872d6f%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_d41c818d8bc34157abbeb2511b872d6f%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThe%20Annex%2C%20North%20Midtown%2C%20Yorkville%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_53cc021ca1c34938913b3f3769dd84e8.setContent%28html_d41c818d8bc34157abbeb2511b872d6f%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_8aa2bdb356ca4d89813da81f67ed6d35.bindPopup%28popup_53cc021ca1c34938913b3f3769dd84e8%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_5d59fabc3903431ea19eb740bb048a2a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6489597%2C-79.456325%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_afdaae76c5b24ef0b86d13dca389c0a7%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_f06949569afc49f8850b6b691caaea57%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_f06949569afc49f8850b6b691caaea57%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EParkdale%2C%20Roncesvalles%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_afdaae76c5b24ef0b86d13dca389c0a7.setContent%28html_f06949569afc49f8850b6b691caaea57%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_5d59fabc3903431ea19eb740bb048a2a.bindPopup%28popup_afdaae76c5b24ef0b86d13dca389c0a7%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_60fb3e68fc7940ee84fa37c0e310c11b%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6369656%2C-79.61581899999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_6445ec56c39d41bdb1e712fd65ac594b%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_44576e1b672046cd9b44110cd6b02135%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_44576e1b672046cd9b44110cd6b02135%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EEnclave%20of%20L4W%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_6445ec56c39d41bdb1e712fd65ac594b.setContent%28html_44576e1b672046cd9b44110cd6b02135%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_60fb3e68fc7940ee84fa37c0e310c11b.bindPopup%28popup_6445ec56c39d41bdb1e712fd65ac594b%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_108020f4aae34ecfa178bb581ebe6ecb%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6889054%2C-79.55472440000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_c867a5491555438e8cdd6b45d1286172%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_b14a9c4bb69d416abe6b6290856c4257%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_b14a9c4bb69d416abe6b6290856c4257%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EKingsview%20Village%2C%20St.%20Phillips%2C%20Martin%20Grove%20Gardens%2C%20Richview%20Gardens%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_c867a5491555438e8cdd6b45d1286172.setContent%28html_b14a9c4bb69d416abe6b6290856c4257%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_108020f4aae34ecfa178bb581ebe6ecb.bindPopup%28popup_c867a5491555438e8cdd6b45d1286172%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_0eaac2c4a17d4c528d8d7e90e38c32e7%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7942003%2C-79.26202940000002%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_4a4a38254ac74cd89a8f4c1ac39df7fa%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_9f8647e82ce4469289700b9668933d95%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_9f8647e82ce4469289700b9668933d95%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EAgincourt%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_4a4a38254ac74cd89a8f4c1ac39df7fa.setContent%28html_9f8647e82ce4469289700b9668933d95%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_0eaac2c4a17d4c528d8d7e90e38c32e7.bindPopup%28popup_4a4a38254ac74cd89a8f4c1ac39df7fa%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_847808ccd69b4928ba860b57f10293a8%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7043244%2C-79.3887901%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_329c29a552a34c0b977fb8da3377e085%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_592ca74a252844b6b12961f0bf89f9c6%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_592ca74a252844b6b12961f0bf89f9c6%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EDavisville%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_329c29a552a34c0b977fb8da3377e085.setContent%28html_592ca74a252844b6b12961f0bf89f9c6%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_847808ccd69b4928ba860b57f10293a8.bindPopup%28popup_329c29a552a34c0b977fb8da3377e085%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_a1c1b794ab9f4f769faed2627c2faf7a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6626956%2C-79.4000493%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_546ed6a08bc847dd87b80a2e7bb9cad6%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_58f5c4ef00d64a2bb1bf35dd9d14e2eb%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_58f5c4ef00d64a2bb1bf35dd9d14e2eb%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EUniversity%20of%20Toronto%2C%20Harbord%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_546ed6a08bc847dd87b80a2e7bb9cad6.setContent%28html_58f5c4ef00d64a2bb1bf35dd9d14e2eb%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_a1c1b794ab9f4f769faed2627c2faf7a.bindPopup%28popup_546ed6a08bc847dd87b80a2e7bb9cad6%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_7eb6690718494e9cab704e1888196d09%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6515706%2C-79.4844499%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_145caca8ebe44f59a100e55a69c0c0c3%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_3a37832ab3e64256b57a97de7522633e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_3a37832ab3e64256b57a97de7522633e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERunnymede%2C%20Swansea%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_145caca8ebe44f59a100e55a69c0c0c3.setContent%28html_3a37832ab3e64256b57a97de7522633e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_7eb6690718494e9cab704e1888196d09.bindPopup%28popup_145caca8ebe44f59a100e55a69c0c0c3%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_05663f474d8d4a19b0c5cbd2b0648b42%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.7816375%2C-79.3043021%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_3ca5afe1de264ba3bf415d9a2778dc65%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_96803d49d5e24004bc59c8b3edfc354c%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_96803d49d5e24004bc59c8b3edfc354c%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EClarks%20Corners%2C%20Tam%20O%26%2339%3BShanter%2C%20Sullivan%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_3ca5afe1de264ba3bf415d9a2778dc65.setContent%28html_96803d49d5e24004bc59c8b3edfc354c%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_05663f474d8d4a19b0c5cbd2b0648b42.bindPopup%28popup_3ca5afe1de264ba3bf415d9a2778dc65%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_2d941bd4a66143528223b060d841e7b1%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6895743%2C-79.38315990000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_43ab70ab8f774d3d915d790729743ec7%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_9d7cb588aec048f3995ae5fe9198573d%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_9d7cb588aec048f3995ae5fe9198573d%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EMoore%20Park%2C%20Summerhill%20East%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_43ab70ab8f774d3d915d790729743ec7.setContent%28html_9d7cb588aec048f3995ae5fe9198573d%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_2d941bd4a66143528223b060d841e7b1.bindPopup%28popup_43ab70ab8f774d3d915d790729743ec7%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_b8d780b546b94c8ab76a6291d4e101fb%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6532057%2C-79.4000493%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_5bc56f42ce544fe28e012c9e63b7ed2a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_911199d1ec0c4c709741a1209d84cbcd%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_911199d1ec0c4c709741a1209d84cbcd%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EKensington%20Market%2C%20Chinatown%2C%20Grange%20Park%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_5bc56f42ce544fe28e012c9e63b7ed2a.setContent%28html_911199d1ec0c4c709741a1209d84cbcd%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_b8d780b546b94c8ab76a6291d4e101fb.bindPopup%28popup_5bc56f42ce544fe28e012c9e63b7ed2a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4cbfadba211f4b7cbd0d8a3e2b2f9c14%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.8152522%2C-79.2845772%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_e3d3584804a54447a8490cf031337850%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_1ca922ccb8344a8295e62c2b7e160031%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_1ca922ccb8344a8295e62c2b7e160031%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EMilliken%2C%20Agincourt%20North%2C%20Steeles%20East%2C%20L%26%2339%3BAmoreaux%20East%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_e3d3584804a54447a8490cf031337850.setContent%28html_1ca922ccb8344a8295e62c2b7e160031%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4cbfadba211f4b7cbd0d8a3e2b2f9c14.bindPopup%28popup_e3d3584804a54447a8490cf031337850%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_9cc4757e5c474230a81564cde1173b25%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.68641229999999%2C-79.4000493%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_5e3b9c0c435c4c328f7dd9480cfadaf1%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_0404e2e45e544ef1898911e33f52dda8%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_0404e2e45e544ef1898911e33f52dda8%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ESummerhill%20West%2C%20Rathnelly%2C%20South%20Hill%2C%20Forest%20Hill%20SE%2C%20Deer%20Park%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_5e3b9c0c435c4c328f7dd9480cfadaf1.setContent%28html_0404e2e45e544ef1898911e33f52dda8%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_9cc4757e5c474230a81564cde1173b25.bindPopup%28popup_5e3b9c0c435c4c328f7dd9480cfadaf1%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_1323a19cf1794aa1b3c610b940816b5a%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6289467%2C-79.3944199%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_008ba1bfa5d6463e866813a48eef8141%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_35409d9c7c0f419d964bf2928aa98d57%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_35409d9c7c0f419d964bf2928aa98d57%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ECN%20Tower%2C%20King%20and%20Spadina%2C%20Railway%20Lands%2C%20Harbourfront%20West%2C%20Bathurst%20Quay%2C%20South%20Niagara%2C%20Island%20airport%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_008ba1bfa5d6463e866813a48eef8141.setContent%28html_35409d9c7c0f419d964bf2928aa98d57%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_1323a19cf1794aa1b3c610b940816b5a.bindPopup%28popup_008ba1bfa5d6463e866813a48eef8141%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_9261886dbf514e3db6c4a32feee64b04%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6056466%2C-79.50132070000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_9867503d68c549f5a0cdaa857e028508%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_7301730a248c4846a3133021790d62d4%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_7301730a248c4846a3133021790d62d4%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ENew%20Toronto%2C%20Mimico%20South%2C%20Humber%20Bay%20Shores%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_9867503d68c549f5a0cdaa857e028508.setContent%28html_7301730a248c4846a3133021790d62d4%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_9261886dbf514e3db6c4a32feee64b04.bindPopup%28popup_9867503d68c549f5a0cdaa857e028508%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_b161b9af482947488d245022811c3f64%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.739416399999996%2C-79.5884369%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_b5b2060f760c436a88045e302040d012%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_3da32bf285c744c8b449344e6b2febf2%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_3da32bf285c744c8b449344e6b2febf2%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ESouth%20Steeles%2C%20Silverstone%2C%20Humbergate%2C%20Jamestown%2C%20Mount%20Olive%2C%20Beaumond%20Heights%2C%20Thistletown%2C%20Albion%20Gardens%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_b5b2060f760c436a88045e302040d012.setContent%28html_3da32bf285c744c8b449344e6b2febf2%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_b161b9af482947488d245022811c3f64.bindPopup%28popup_b5b2060f760c436a88045e302040d012%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_d2524a2e7bad451d90d0eaf9b2833554%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.799525200000005%2C-79.3183887%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_9d78d33e516d4f6a9027717ec057a67a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_ed5717ad3bdd4233ad19c010889841e5%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_ed5717ad3bdd4233ad19c010889841e5%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ESteeles%20West%2C%20L%26%2339%3BAmoreaux%20West%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_9d78d33e516d4f6a9027717ec057a67a.setContent%28html_ed5717ad3bdd4233ad19c010889841e5%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_d2524a2e7bad451d90d0eaf9b2833554.bindPopup%28popup_9d78d33e516d4f6a9027717ec057a67a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_914004a43e3f4900a0711471daa8cb60%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6795626%2C-79.37752940000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_901ce95a85834f26b978aa6a6bfc3a9a%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_287a1b2105ed4717a8856276b1bbec9a%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_287a1b2105ed4717a8856276b1bbec9a%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ERosedale%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_901ce95a85834f26b978aa6a6bfc3a9a.setContent%28html_287a1b2105ed4717a8856276b1bbec9a%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_914004a43e3f4900a0711471daa8cb60.bindPopup%28popup_901ce95a85834f26b978aa6a6bfc3a9a%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_c83a7c26cad3424aa6867b073e532ccc%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6464352%2C-79.37484599999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_c457238490cb4d9bb946279dbec4e282%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_77a692a5ea0c48349a5569c58784a8a1%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_77a692a5ea0c48349a5569c58784a8a1%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EEnclave%20of%20M5E%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_c457238490cb4d9bb946279dbec4e282.setContent%28html_77a692a5ea0c48349a5569c58784a8a1%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_c83a7c26cad3424aa6867b073e532ccc.bindPopup%28popup_c457238490cb4d9bb946279dbec4e282%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_0331a122c93a4fb0baac88c40a74c836%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.60241370000001%2C-79.54348409999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%238000ff%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%238000ff%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_7c2c183046aa4bbdb2fbf68671745f3d%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_c40abf78517f417c8faf227bfc0d0b81%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_c40abf78517f417c8faf227bfc0d0b81%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EAlderwood%2C%20Long%20Branch%20Cluster%201.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_7c2c183046aa4bbdb2fbf68671745f3d.setContent%28html_c40abf78517f417c8faf227bfc0d0b81%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_0331a122c93a4fb0baac88c40a74c836.bindPopup%28popup_7c2c183046aa4bbdb2fbf68671745f3d%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_ce45eebb76414ef485ccfe0c68276bd1%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.706748299999994%2C-79.5940544%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%2300b5eb%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%2300b5eb%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_1477a3260c3549caaef62b4bc4bc4897%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_1f8f73184b0045979d63518c5749500e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_1f8f73184b0045979d63518c5749500e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EClairville%2C%20Humberwood%2C%20Woodbine%20Downs%2C%20West%20Humber%2C%20Kipling%20Heights%2C%20Rexdale%2C%20Elms%2C%20Tandridge%2C%20Old%20Rexdale%20Cluster%202.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_1477a3260c3549caaef62b4bc4bc4897.setContent%28html_1f8f73184b0045979d63518c5749500e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_ce45eebb76414ef485ccfe0c68276bd1.bindPopup%28popup_1477a3260c3549caaef62b4bc4bc4897%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4809a07538964b21b5670d375546cce2%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.667967%2C-79.3676753%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_57e87c2a08a04595a00c82906b5cbcea%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_aa704c2af4ad41f2983f42661c67dd29%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_aa704c2af4ad41f2983f42661c67dd29%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3ESt.%20James%20Town%2C%20Cabbagetown%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_57e87c2a08a04595a00c82906b5cbcea.setContent%28html_aa704c2af4ad41f2983f42661c67dd29%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4809a07538964b21b5670d375546cce2.bindPopup%28popup_57e87c2a08a04595a00c82906b5cbcea%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_f3fc70b648d04cc689538759bea96fad%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6484292%2C-79.3822802%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_3ffac42f762e4b4b8b2a21d6f02f3bcd%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_78a86088166943c2a5fc2ca8c5380a70%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_78a86088166943c2a5fc2ca8c5380a70%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EFirst%20Canadian%20Place%2C%20Underground%20city%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_3ffac42f762e4b4b8b2a21d6f02f3bcd.setContent%28html_78a86088166943c2a5fc2ca8c5380a70%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_f3fc70b648d04cc689538759bea96fad.bindPopup%28popup_3ffac42f762e4b4b8b2a21d6f02f3bcd%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_3e8a752a9049422ba17f2f61e11699f0%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.653653600000005%2C-79.5069436%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d82265c2dcca4f26b6ef9af2002ad23c%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_20cc7d3fd7cc427c937942a2cb7a6f4e%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_20cc7d3fd7cc427c937942a2cb7a6f4e%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EThe%20Kingsway%2C%20Montgomery%20Road%2C%20Old%20Mill%20North%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d82265c2dcca4f26b6ef9af2002ad23c.setContent%28html_20cc7d3fd7cc427c937942a2cb7a6f4e%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_3e8a752a9049422ba17f2f61e11699f0.bindPopup%28popup_d82265c2dcca4f26b6ef9af2002ad23c%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_36a053c383254dbfb6f161931d15ad77%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6658599%2C-79.38315990000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_b5863607d4d5437881955426f46828ba%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_250ee460027742569effae69c6edfb60%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_250ee460027742569effae69c6edfb60%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EChurch%20and%20Wellesley%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_b5863607d4d5437881955426f46828ba.setContent%28html_250ee460027742569effae69c6edfb60%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_36a053c383254dbfb6f161931d15ad77.bindPopup%28popup_b5863607d4d5437881955426f46828ba%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_4e3d395ecf3547a2b46ed74d650b882e%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6627439%2C-79.321558%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_0dcf1a3e744a4c4ba63707fce0721dfa%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_fde9f3795cbf43be88571107cee28125%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_fde9f3795cbf43be88571107cee28125%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EEnclave%20of%20M4L%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_0dcf1a3e744a4c4ba63707fce0721dfa.setContent%28html_fde9f3795cbf43be88571107cee28125%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_4e3d395ecf3547a2b46ed74d650b882e.bindPopup%28popup_0dcf1a3e744a4c4ba63707fce0721dfa%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_555c852b8d3a4185b13f0eed0317bac4%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6362579%2C-79.49850909999999%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ff0000%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ff0000%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_93810b75aa504fa7b4fac3964b5446cd%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_55cffcf815794048b792aa3b3c625dcc%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_55cffcf815794048b792aa3b3c625dcc%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EOld%20Mill%20South%2C%20King%26%2339%3Bs%20Mill%20Park%2C%20Sunnylea%2C%20Humber%20Bay%2C%20Mimico%20NE%2C%20The%20Queensway%20East%2C%20Royal%20York%20South%20East%2C%20Kingsway%20Park%20South%20East%20Cluster%200.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_93810b75aa504fa7b4fac3964b5446cd.setContent%28html_55cffcf815794048b792aa3b3c625dcc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_555c852b8d3a4185b13f0eed0317bac4.bindPopup%28popup_93810b75aa504fa7b4fac3964b5446cd%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20circle_marker_6d77bdbf231e43a3ba88b78c2965da94%20%3D%20L.circleMarker%28%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5B43.6288408%2C-79.52099940000001%5D%2C%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%22bubblingMouseEvents%22%3A%20true%2C%0A%20%20%22color%22%3A%20%22%23ffb360%22%2C%0A%20%20%22dashArray%22%3A%20null%2C%0A%20%20%22dashOffset%22%3A%20null%2C%0A%20%20%22fill%22%3A%20true%2C%0A%20%20%22fillColor%22%3A%20%22%23ffb360%22%2C%0A%20%20%22fillOpacity%22%3A%200.7%2C%0A%20%20%22fillRule%22%3A%20%22evenodd%22%2C%0A%20%20%22lineCap%22%3A%20%22round%22%2C%0A%20%20%22lineJoin%22%3A%20%22round%22%2C%0A%20%20%22opacity%22%3A%201.0%2C%0A%20%20%22radius%22%3A%205%2C%0A%20%20%22stroke%22%3A%20true%2C%0A%20%20%22weight%22%3A%203%0A%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%29.addTo%28map_c2f03717382c44779ba918667c1562dc%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20var%20popup_d9e00b0f894d47d79ca82f6c8c0a2324%20%3D%20L.popup%28%7BmaxWidth%3A%20%27300%27%7D%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20var%20html_dda800b1d65149369c45a0f825f53f4a%20%3D%20%24%28%27%3Cdiv%20id%3D%22html_dda800b1d65149369c45a0f825f53f4a%22%20style%3D%22width%3A%20100.0%25%3B%20height%3A%20100.0%25%3B%22%3EMimico%20NW%2C%20The%20Queensway%20West%2C%20South%20of%20Bloor%2C%20Kingsway%20Park%20South%20West%2C%20Royal%20York%20South%20West%20Cluster%204.0%3C/div%3E%27%29%5B0%5D%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20popup_d9e00b0f894d47d79ca82f6c8c0a2324.setContent%28html_dda800b1d65149369c45a0f825f53f4a%29%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20circle_marker_6d77bdbf231e43a3ba88b78c2965da94.bindPopup%28popup_d9e00b0f894d47d79ca82f6c8c0a2324%29%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%0A%3C/script%3E onload="this.contentDocument.open();this.contentDocument.write(    decodeURIComponent(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



# Examine Clusters

Cluster 1


```python
Cluster_1=toronto_merged.loc[toronto_merged['Cluster Labels'] == 0, toronto_merged.columns[[1] + list(range(5, toronto_merged.shape[1]))]]
Cluster_1
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
      <th>11</th>
      <td>Etobicoke</td>
      <td>0.0</td>
      <td>Park</td>
      <td>Hotel</td>
      <td>Pizza Place</td>
      <td>Mexican Restaurant</td>
      <td>Gym</td>
      <td>Theater</td>
      <td>Clothing Store</td>
      <td>Restaurant</td>
      <td>Grocery Store</td>
      <td>Bank</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Scarborough</td>
      <td>0.0</td>
      <td>Breakfast Spot</td>
      <td>Playground</td>
      <td>Park</td>
      <td>Burger Joint</td>
      <td>Italian Restaurant</td>
      <td>Yoga Studio</td>
      <td>Escape Room</td>
      <td>Dry Cleaner</td>
      <td>Dumpling Restaurant</td>
      <td>Eastern European Restaurant</td>
    </tr>
    <tr>
      <th>45</th>
      <td>North York</td>
      <td>0.0</td>
      <td>Park</td>
      <td>Pool</td>
      <td>Yoga Studio</td>
      <td>Escape Room</td>
      <td>Doner Restaurant</td>
      <td>Donut Shop</td>
      <td>Dry Cleaner</td>
      <td>Dumpling Restaurant</td>
      <td>Eastern European Restaurant</td>
      <td>Electronics Store</td>
    </tr>
    <tr>
      <th>50</th>
      <td>North York</td>
      <td>0.0</td>
      <td>Electronics Store</td>
      <td>Bakery</td>
      <td>Bank</td>
      <td>Park</td>
      <td>Italian Restaurant</td>
      <td>Arts &amp; Crafts Store</td>
      <td>Pharmacy</td>
      <td>Shopping Mall</td>
      <td>Yoga Studio</td>
      <td>Dry Cleaner</td>
    </tr>
    <tr>
      <th>57</th>
      <td>North York</td>
      <td>0.0</td>
      <td>Bakery</td>
      <td>Discount Store</td>
      <td>Convenience Store</td>
      <td>Golf Course</td>
      <td>Park</td>
      <td>Storage Facility</td>
      <td>Gas Station</td>
      <td>Yoga Studio</td>
      <td>Electronics Store</td>
      <td>Donut Shop</td>
    </tr>
    <tr>
      <th>101</th>
      <td>Etobicoke</td>
      <td>0.0</td>
      <td>Park</td>
      <td>Gym / Fitness Center</td>
      <td>Shopping Mall</td>
      <td>Eastern European Restaurant</td>
      <td>Ice Cream Shop</td>
      <td>Italian Restaurant</td>
      <td>Bus Stop</td>
      <td>Yoga Studio</td>
      <td>Donut Shop</td>
      <td>Dry Cleaner</td>
    </tr>
  </tbody>
</table>
</div>



Checking which venue appears the most times


```python
Cluster_1['1st Most Common Venue'].describe()
```




    count        6
    unique       4
    top       Park
    freq         3
    Name: 1st Most Common Venue, dtype: object



Cluster 2


```python
Cluster_2=toronto_merged.loc[toronto_merged['Cluster Labels'] == 1, toronto_merged.columns[[1] + list(range(5, toronto_merged.shape[1]))]]
Cluster_2
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
      <th>0</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Park</td>
      <td>Bus Stop</td>
      <td>Pharmacy</td>
      <td>Shopping Mall</td>
      <td>Pizza Place</td>
      <td>Fish &amp; Chips Shop</td>
      <td>Supermarket</td>
      <td>Caribbean Restaurant</td>
      <td>Discount Store</td>
      <td>Tennis Court</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Etobicoke</td>
      <td>1.0</td>
      <td>Pharmacy</td>
      <td>Bank</td>
      <td>Playground</td>
      <td>Convenience Store</td>
      <td>Café</td>
      <td>Shopping Mall</td>
      <td>Golf Course</td>
      <td>Bakery</td>
      <td>Grocery Store</td>
      <td>Skating Rink</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Trail</td>
      <td>Fast Food Restaurant</td>
      <td>Greek Restaurant</td>
      <td>Caribbean Restaurant</td>
      <td>Bank</td>
      <td>Bakery</td>
      <td>Supermarket</td>
      <td>Chinese Restaurant</td>
      <td>Restaurant</td>
      <td>Paper / Office Supplies Store</td>
    </tr>
    <tr>
      <th>8</th>
      <td>East York</td>
      <td>1.0</td>
      <td>Coffee Shop</td>
      <td>Brewery</td>
      <td>Pizza Place</td>
      <td>Gym / Fitness Center</td>
      <td>Construction &amp; Landscaping</td>
      <td>Bank</td>
      <td>Bakery</td>
      <td>Soccer Stadium</td>
      <td>Gastropub</td>
      <td>Café</td>
    </tr>
    <tr>
      <th>10</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Fast Food Restaurant</td>
      <td>Grocery Store</td>
      <td>Coffee Shop</td>
      <td>Pizza Place</td>
      <td>Gas Station</td>
      <td>Metro Station</td>
      <td>Sandwich Place</td>
      <td>Sushi Restaurant</td>
      <td>Latin American Restaurant</td>
      <td>Discount Store</td>
    </tr>
    <tr>
      <th>16</th>
      <td>York</td>
      <td>1.0</td>
      <td>Pizza Place</td>
      <td>Convenience Store</td>
      <td>Coffee Shop</td>
      <td>Grocery Store</td>
      <td>Bagel Shop</td>
      <td>Hockey Arena</td>
      <td>Restaurant</td>
      <td>Field</td>
      <td>Soccer Stadium</td>
      <td>Playground</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Etobicoke</td>
      <td>1.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Shopping Plaza</td>
      <td>Shopping Mall</td>
      <td>Intersection</td>
      <td>Gas Station</td>
      <td>Farmers Market</td>
      <td>Pet Store</td>
      <td>Grocery Store</td>
      <td>Pharmacy</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Restaurant</td>
      <td>Pizza Place</td>
      <td>Bank</td>
      <td>Fast Food Restaurant</td>
      <td>Filipino Restaurant</td>
      <td>Sports Bar</td>
      <td>Beer Store</td>
      <td>Sandwich Place</td>
      <td>Food &amp; Drink Shop</td>
      <td>Convenience Store</td>
    </tr>
    <tr>
      <th>21</th>
      <td>York</td>
      <td>1.0</td>
      <td>Pharmacy</td>
      <td>Park</td>
      <td>Pizza Place</td>
      <td>Mexican Restaurant</td>
      <td>Discount Store</td>
      <td>Japanese Restaurant</td>
      <td>Bus Stop</td>
      <td>Bank</td>
      <td>Coffee Shop</td>
      <td>Food Truck</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Park</td>
      <td>Coffee Shop</td>
      <td>Mobile Phone Shop</td>
      <td>Fast Food Restaurant</td>
      <td>Chinese Restaurant</td>
      <td>Indian Restaurant</td>
      <td>Yoga Studio</td>
      <td>Escape Room</td>
      <td>Dry Cleaner</td>
      <td>Dumpling Restaurant</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Bakery</td>
      <td>Grocery Store</td>
      <td>Gas Station</td>
      <td>Coffee Shop</td>
      <td>Bank</td>
      <td>Indian Restaurant</td>
      <td>Yoga Studio</td>
      <td>Fried Chicken Joint</td>
      <td>Sporting Goods Shop</td>
      <td>Fast Food Restaurant</td>
    </tr>
    <tr>
      <th>27</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Pharmacy</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Residential Building (Apartment / Condo)</td>
      <td>Recreation Center</td>
      <td>Korean Restaurant</td>
      <td>Bank</td>
      <td>Bakery</td>
      <td>Tennis Court</td>
      <td>Chinese Restaurant</td>
    </tr>
    <tr>
      <th>28</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Pizza Place</td>
      <td>Pharmacy</td>
      <td>Bank</td>
      <td>Park</td>
      <td>Coffee Shop</td>
      <td>Mediterranean Restaurant</td>
      <td>Mobile Phone Shop</td>
      <td>Shopping Mall</td>
      <td>Sandwich Place</td>
      <td>Supermarket</td>
    </tr>
    <tr>
      <th>29</th>
      <td>East York</td>
      <td>1.0</td>
      <td>Coffee Shop</td>
      <td>Indian Restaurant</td>
      <td>Grocery Store</td>
      <td>Shopping Mall</td>
      <td>Burger Joint</td>
      <td>Park</td>
      <td>Pizza Place</td>
      <td>Afghan Restaurant</td>
      <td>Supermarket</td>
      <td>Brewery</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Ice Cream Shop</td>
      <td>Bowling Alley</td>
      <td>Coffee Shop</td>
      <td>Sandwich Place</td>
      <td>Japanese Restaurant</td>
      <td>Intersection</td>
      <td>Restaurant</td>
      <td>Grocery Store</td>
      <td>Fast Food Restaurant</td>
      <td>Convenience Store</td>
    </tr>
    <tr>
      <th>34</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Pizza Place</td>
      <td>Coffee Shop</td>
      <td>Furniture / Home Store</td>
      <td>Restaurant</td>
      <td>Bar</td>
      <td>Chinese Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Automotive Shop</td>
      <td>Caribbean Restaurant</td>
      <td>Sushi Restaurant</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Coffee Shop</td>
      <td>Chinese Restaurant</td>
      <td>Pizza Place</td>
      <td>Fast Food Restaurant</td>
      <td>Hobby Shop</td>
      <td>Asian Restaurant</td>
      <td>Pharmacy</td>
      <td>Rental Car Location</td>
      <td>Department Store</td>
      <td>Discount Store</td>
    </tr>
    <tr>
      <th>39</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Grocery Store</td>
      <td>Bank</td>
      <td>Gas Station</td>
      <td>Japanese Restaurant</td>
      <td>Restaurant</td>
      <td>Park</td>
      <td>Trail</td>
      <td>Shopping Mall</td>
      <td>Skating Rink</td>
      <td>Chinese Restaurant</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Coffee Shop</td>
      <td>Intersection</td>
      <td>Park</td>
      <td>Bakery</td>
      <td>Soccer Field</td>
      <td>Convenience Store</td>
      <td>Bank</td>
      <td>Restaurant</td>
      <td>Beer Store</td>
      <td>Fast Food Restaurant</td>
    </tr>
    <tr>
      <th>46</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Bank</td>
      <td>Park</td>
      <td>Pizza Place</td>
      <td>Shopping Mall</td>
      <td>Coffee Shop</td>
      <td>Grocery Store</td>
      <td>Yoga Studio</td>
      <td>Electronics Store</td>
      <td>Donut Shop</td>
      <td>Dry Cleaner</td>
    </tr>
    <tr>
      <th>49</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Coffee Shop</td>
      <td>Gas Station</td>
      <td>Convenience Store</td>
      <td>Mediterranean Restaurant</td>
      <td>Chinese Restaurant</td>
      <td>Park</td>
      <td>Athletics &amp; Sports</td>
      <td>Bakery</td>
      <td>Intersection</td>
      <td>Dim Sum Restaurant</td>
    </tr>
    <tr>
      <th>51</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Pizza Place</td>
      <td>Beach</td>
      <td>Ice Cream Shop</td>
      <td>Restaurant</td>
      <td>Sports Bar</td>
      <td>Auto Garage</td>
      <td>Gym / Fitness Center</td>
      <td>Pharmacy</td>
      <td>Park</td>
      <td>Electronics Store</td>
    </tr>
    <tr>
      <th>56</th>
      <td>York</td>
      <td>1.0</td>
      <td>Furniture / Home Store</td>
      <td>Grocery Store</td>
      <td>Intersection</td>
      <td>Discount Store</td>
      <td>Gas Station</td>
      <td>Sandwich Place</td>
      <td>Dessert Shop</td>
      <td>Park</td>
      <td>Italian Restaurant</td>
      <td>Fast Food Restaurant</td>
    </tr>
    <tr>
      <th>60</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Hotel</td>
      <td>Grocery Store</td>
      <td>Gas Station</td>
      <td>Fast Food Restaurant</td>
      <td>Discount Store</td>
      <td>Coffee Shop</td>
      <td>Pharmacy</td>
      <td>Pizza Place</td>
      <td>Fried Chicken Joint</td>
      <td>Kitchen Supply Store</td>
    </tr>
    <tr>
      <th>63</th>
      <td>York</td>
      <td>1.0</td>
      <td>Coffee Shop</td>
      <td>Brewery</td>
      <td>Bus Line</td>
      <td>Convenience Store</td>
      <td>Pizza Place</td>
      <td>Sandwich Place</td>
      <td>Gas Station</td>
      <td>Beer Store</td>
      <td>Park</td>
      <td>Furniture / Home Store</td>
    </tr>
    <tr>
      <th>64</th>
      <td>York</td>
      <td>1.0</td>
      <td>Train Station</td>
      <td>Grocery Store</td>
      <td>Furniture / Home Store</td>
      <td>Café</td>
      <td>Sandwich Place</td>
      <td>Diner</td>
      <td>Intersection</td>
      <td>Park</td>
      <td>Restaurant</td>
      <td>Skating Rink</td>
    </tr>
    <tr>
      <th>65</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Electronics Store</td>
      <td>Pharmacy</td>
      <td>Coffee Shop</td>
      <td>Furniture / Home Store</td>
      <td>Restaurant</td>
      <td>Bakery</td>
      <td>Intersection</td>
      <td>Chinese Restaurant</td>
      <td>Fast Food Restaurant</td>
      <td>Indian Restaurant</td>
    </tr>
    <tr>
      <th>66</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Restaurant</td>
      <td>Park</td>
      <td>Coffee Shop</td>
      <td>Dog Run</td>
      <td>Chinese Restaurant</td>
      <td>Golf Course</td>
      <td>French Restaurant</td>
      <td>Grocery Store</td>
      <td>Intersection</td>
      <td>Gas Station</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Etobicoke</td>
      <td>1.0</td>
      <td>Pizza Place</td>
      <td>Gas Station</td>
      <td>Intersection</td>
      <td>Breakfast Spot</td>
      <td>Flea Market</td>
      <td>Supermarket</td>
      <td>Sandwich Place</td>
      <td>Discount Store</td>
      <td>Chinese Restaurant</td>
      <td>Golf Course</td>
    </tr>
    <tr>
      <th>71</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Pizza Place</td>
      <td>Grocery Store</td>
      <td>Middle Eastern Restaurant</td>
      <td>Burger Joint</td>
      <td>Rental Car Location</td>
      <td>Indian Restaurant</td>
      <td>Korean Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Bakery</td>
      <td>Badminton Court</td>
    </tr>
    <tr>
      <th>72</th>
      <td>North York</td>
      <td>1.0</td>
      <td>Pharmacy</td>
      <td>Supermarket</td>
      <td>Park</td>
      <td>Convenience Store</td>
      <td>Pizza Place</td>
      <td>Discount Store</td>
      <td>Butcher</td>
      <td>Eastern European Restaurant</td>
      <td>Coffee Shop</td>
      <td>Bakery</td>
    </tr>
    <tr>
      <th>77</th>
      <td>Etobicoke</td>
      <td>1.0</td>
      <td>Pharmacy</td>
      <td>Beer Store</td>
      <td>Bus Line</td>
      <td>Sandwich Place</td>
      <td>Gas Station</td>
      <td>Supplement Shop</td>
      <td>Chinese Restaurant</td>
      <td>Supermarket</td>
      <td>Intersection</td>
      <td>Coffee Shop</td>
    </tr>
    <tr>
      <th>78</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Chinese Restaurant</td>
      <td>Shopping Mall</td>
      <td>Caribbean Restaurant</td>
      <td>Bakery</td>
      <td>Sandwich Place</td>
      <td>Breakfast Spot</td>
      <td>Malay Restaurant</td>
      <td>Lounge</td>
      <td>Seafood Restaurant</td>
      <td>Café</td>
    </tr>
    <tr>
      <th>82</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Bank</td>
      <td>Pharmacy</td>
      <td>Pizza Place</td>
      <td>Convenience Store</td>
      <td>Sandwich Place</td>
      <td>Coffee Shop</td>
      <td>Fast Food Restaurant</td>
      <td>Taiwanese Restaurant</td>
      <td>Thai Restaurant</td>
      <td>Seafood Restaurant</td>
    </tr>
    <tr>
      <th>85</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Chinese Restaurant</td>
      <td>Pizza Place</td>
      <td>Playground</td>
      <td>Park</td>
      <td>BBQ Joint</td>
      <td>Intersection</td>
      <td>Korean Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Bakery</td>
      <td>Noodle House</td>
    </tr>
    <tr>
      <th>89</th>
      <td>Etobicoke</td>
      <td>1.0</td>
      <td>Pizza Place</td>
      <td>Grocery Store</td>
      <td>Sandwich Place</td>
      <td>Pharmacy</td>
      <td>Park</td>
      <td>Beer Store</td>
      <td>Auto Garage</td>
      <td>Liquor Store</td>
      <td>Discount Store</td>
      <td>Bus Line</td>
    </tr>
    <tr>
      <th>90</th>
      <td>Scarborough</td>
      <td>1.0</td>
      <td>Chinese Restaurant</td>
      <td>Coffee Shop</td>
      <td>Intersection</td>
      <td>Bank</td>
      <td>Bakery</td>
      <td>Pizza Place</td>
      <td>Fast Food Restaurant</td>
      <td>Electronics Store</td>
      <td>Sandwich Place</td>
      <td>Camera Store</td>
    </tr>
    <tr>
      <th>91</th>
      <td>Downtown Toronto</td>
      <td>1.0</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Grocery Store</td>
      <td>Playground</td>
      <td>Metro Station</td>
      <td>Breakfast Spot</td>
      <td>Convenience Store</td>
      <td>Bistro</td>
      <td>Candy Store</td>
      <td>Sandwich Place</td>
    </tr>
    <tr>
      <th>93</th>
      <td>Etobicoke</td>
      <td>1.0</td>
      <td>Discount Store</td>
      <td>Pizza Place</td>
      <td>Convenience Store</td>
      <td>Pharmacy</td>
      <td>Park</td>
      <td>Playground</td>
      <td>Donut Shop</td>
      <td>Shopping Mall</td>
      <td>Dance Studio</td>
      <td>Intersection</td>
    </tr>
  </tbody>
</table>
</div>



Checking which venue appears the most times


```python
Cluster_2['1st Most Common Venue'].describe()
```




    count              39
    unique             17
    top       Coffee Shop
    freq                8
    Name: 1st Most Common Venue, dtype: object



Cluster 3


```python
Cluster_3=toronto_merged.loc[toronto_merged['Cluster Labels'] == 2, toronto_merged.columns[[1] + list(range(5, toronto_merged.shape[1]))]]
Cluster_3
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
      <th>94</th>
      <td>Etobicoke Northwest</td>
      <td>2.0</td>
      <td>Hotel</td>
      <td>Rental Car Location</td>
      <td>Coffee Shop</td>
      <td>Yoga Studio</td>
      <td>Escape Room</td>
      <td>Doner Restaurant</td>
      <td>Donut Shop</td>
      <td>Dry Cleaner</td>
      <td>Dumpling Restaurant</td>
      <td>Eastern European Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



Cluster 4


```python
Cluster_4=toronto_merged.loc[toronto_merged['Cluster Labels'] == 3, toronto_merged.columns[[1] + list(range(5, toronto_merged.shape[1]))]]
Cluster_4
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
      <th>53</th>
      <td>North York</td>
      <td>3.0</td>
      <td>Vietnamese Restaurant</td>
      <td>Food Truck</td>
      <td>Baseball Field</td>
      <td>Yoga Studio</td>
      <td>Ethiopian Restaurant</td>
      <td>Donut Shop</td>
      <td>Dry Cleaner</td>
      <td>Dumpling Restaurant</td>
      <td>Eastern European Restaurant</td>
      <td>Electronics Store</td>
    </tr>
  </tbody>
</table>
</div>



Cluster 5


```python
Cluster_5=toronto_merged.loc[toronto_merged['Cluster Labels'] == 4, toronto_merged.columns[[1] + list(range(5, toronto_merged.shape[1]))]]
Cluster_5
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
      <th>1</th>
      <td>North York</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Portuguese Restaurant</td>
      <td>Hockey Arena</td>
      <td>Sporting Goods Shop</td>
      <td>Pizza Place</td>
      <td>Men's Store</td>
      <td>Gym / Fitness Center</td>
      <td>Lounge</td>
      <td>Golf Course</td>
      <td>Electronics Store</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Theater</td>
      <td>Café</td>
      <td>Restaurant</td>
      <td>Park</td>
      <td>Pub</td>
      <td>Diner</td>
      <td>Sushi Restaurant</td>
      <td>Breakfast Spot</td>
      <td>Italian Restaurant</td>
    </tr>
    <tr>
      <th>3</th>
      <td>North York</td>
      <td>4.0</td>
      <td>Furniture / Home Store</td>
      <td>Fast Food Restaurant</td>
      <td>Restaurant</td>
      <td>Clothing Store</td>
      <td>Coffee Shop</td>
      <td>Vietnamese Restaurant</td>
      <td>Accessories Store</td>
      <td>Sushi Restaurant</td>
      <td>Fried Chicken Joint</td>
      <td>Dessert Shop</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Queen's Park</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Sushi Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Park</td>
      <td>Pizza Place</td>
      <td>Restaurant</td>
      <td>Dance Studio</td>
      <td>Breakfast Spot</td>
      <td>Thai Restaurant</td>
      <td>Middle Eastern Restaurant</td>
    </tr>
    <tr>
      <th>7</th>
      <td>North York</td>
      <td>4.0</td>
      <td>Japanese Restaurant</td>
      <td>Burger Joint</td>
      <td>Pizza Place</td>
      <td>Coffee Shop</td>
      <td>Caribbean Restaurant</td>
      <td>Supermarket</td>
      <td>Bar</td>
      <td>Bank</td>
      <td>Liquor Store</td>
      <td>Restaurant</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Japanese Restaurant</td>
      <td>Gastropub</td>
      <td>Cosmetics Shop</td>
      <td>Theater</td>
      <td>Italian Restaurant</td>
      <td>Hotel</td>
      <td>American Restaurant</td>
      <td>Creperie</td>
    </tr>
    <tr>
      <th>13</th>
      <td>North York</td>
      <td>4.0</td>
      <td>Restaurant</td>
      <td>Gym</td>
      <td>Coffee Shop</td>
      <td>Supermarket</td>
      <td>Sushi Restaurant</td>
      <td>Discount Store</td>
      <td>Shopping Mall</td>
      <td>Salon / Barbershop</td>
      <td>Intersection</td>
      <td>Italian Restaurant</td>
    </tr>
    <tr>
      <th>14</th>
      <td>East York</td>
      <td>4.0</td>
      <td>Park</td>
      <td>Coffee Shop</td>
      <td>Sandwich Place</td>
      <td>Café</td>
      <td>Pizza Place</td>
      <td>Curling Ice</td>
      <td>Bus Stop</td>
      <td>Intersection</td>
      <td>Restaurant</td>
      <td>Athletics &amp; Sports</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Gastropub</td>
      <td>Cosmetics Shop</td>
      <td>Theater</td>
      <td>Italian Restaurant</td>
      <td>American Restaurant</td>
      <td>Pizza Place</td>
    </tr>
    <tr>
      <th>19</th>
      <td>East Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Pub</td>
      <td>Beach</td>
      <td>Pizza Place</td>
      <td>Breakfast Spot</td>
      <td>Japanese Restaurant</td>
      <td>Burger Joint</td>
      <td>Bar</td>
      <td>Sandwich Place</td>
      <td>Bakery</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Park</td>
      <td>Japanese Restaurant</td>
      <td>Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Creperie</td>
      <td>Gastropub</td>
      <td>Gym</td>
    </tr>
    <tr>
      <th>23</th>
      <td>East York</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Sporting Goods Shop</td>
      <td>Furniture / Home Store</td>
      <td>Electronics Store</td>
      <td>Grocery Store</td>
      <td>Sandwich Place</td>
      <td>Restaurant</td>
      <td>Brewery</td>
      <td>Bank</td>
      <td>Department Store</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Clothing Store</td>
      <td>Sushi Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Ramen Restaurant</td>
      <td>Park</td>
      <td>Bookstore</td>
      <td>Bubble Tea Shop</td>
      <td>Burrito Place</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Korean Restaurant</td>
      <td>Café</td>
      <td>Grocery Store</td>
      <td>Coffee Shop</td>
      <td>Mexican Restaurant</td>
      <td>Cocktail Bar</td>
      <td>Pizza Place</td>
      <td>Bar</td>
      <td>Indian Restaurant</td>
      <td>Vegetarian / Vegan Restaurant</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Japanese Restaurant</td>
      <td>Theater</td>
      <td>Pizza Place</td>
      <td>Art Gallery</td>
      <td>Beer Bar</td>
      <td>Seafood Restaurant</td>
      <td>Restaurant</td>
    </tr>
    <tr>
      <th>31</th>
      <td>West Toronto</td>
      <td>4.0</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Italian Restaurant</td>
      <td>Bakery</td>
      <td>Bar</td>
      <td>Sushi Restaurant</td>
      <td>Convenience Store</td>
      <td>Pharmacy</td>
      <td>Gourmet Shop</td>
    </tr>
    <tr>
      <th>33</th>
      <td>North York</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Clothing Store</td>
      <td>Restaurant</td>
      <td>Fast Food Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Bakery</td>
      <td>Sandwich Place</td>
      <td>Bank</td>
      <td>Juice Bar</td>
      <td>Fried Chicken Joint</td>
    </tr>
    <tr>
      <th>35</th>
      <td>East York/East Toronto</td>
      <td>4.0</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Greek Restaurant</td>
      <td>Beer Bar</td>
      <td>Ethiopian Restaurant</td>
      <td>Fast Food Restaurant</td>
      <td>Pizza Place</td>
      <td>Bakery</td>
      <td>Bank</td>
      <td>Thai Restaurant</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Japanese Restaurant</td>
      <td>Park</td>
      <td>Brewery</td>
      <td>Scenic Lookout</td>
      <td>Theater</td>
      <td>Gym</td>
      <td>Steakhouse</td>
    </tr>
    <tr>
      <th>37</th>
      <td>West Toronto</td>
      <td>4.0</td>
      <td>Café</td>
      <td>Bar</td>
      <td>Restaurant</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Asian Restaurant</td>
      <td>Bakery</td>
      <td>Pizza Place</td>
      <td>Cocktail Bar</td>
    </tr>
    <tr>
      <th>40</th>
      <td>North York</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Turkish Restaurant</td>
      <td>Chinese Restaurant</td>
      <td>Athletics &amp; Sports</td>
      <td>Soccer Field</td>
      <td>Pizza Place</td>
      <td>Electronics Store</td>
      <td>Liquor Store</td>
      <td>Café</td>
      <td>Latin American Restaurant</td>
    </tr>
    <tr>
      <th>41</th>
      <td>East Toronto</td>
      <td>4.0</td>
      <td>Greek Restaurant</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Pub</td>
      <td>Italian Restaurant</td>
      <td>Bank</td>
      <td>Ice Cream Shop</td>
      <td>Yoga Studio</td>
      <td>Discount Store</td>
      <td>Fast Food Restaurant</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Japanese Restaurant</td>
      <td>Cosmetics Shop</td>
      <td>Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Theater</td>
      <td>Concert Hall</td>
      <td>Farmers Market</td>
    </tr>
    <tr>
      <th>43</th>
      <td>West Toronto</td>
      <td>4.0</td>
      <td>Café</td>
      <td>Restaurant</td>
      <td>Bar</td>
      <td>Coffee Shop</td>
      <td>Bakery</td>
      <td>Furniture / Home Store</td>
      <td>Gift Shop</td>
      <td>Tibetan Restaurant</td>
      <td>Gym</td>
      <td>Arts &amp; Crafts Store</td>
    </tr>
    <tr>
      <th>47</th>
      <td>East Toronto</td>
      <td>4.0</td>
      <td>Indian Restaurant</td>
      <td>Coffee Shop</td>
      <td>Beach</td>
      <td>Grocery Store</td>
      <td>Park</td>
      <td>Fast Food Restaurant</td>
      <td>Restaurant</td>
      <td>Gym</td>
      <td>Discount Store</td>
      <td>Brewery</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Seafood Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Restaurant</td>
      <td>Concert Hall</td>
      <td>Cosmetics Shop</td>
      <td>Farmers Market</td>
      <td>Bakery</td>
    </tr>
    <tr>
      <th>52</th>
      <td>North York</td>
      <td>4.0</td>
      <td>Korean Restaurant</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Middle Eastern Restaurant</td>
      <td>Pizza Place</td>
      <td>Indian Restaurant</td>
      <td>Supermarket</td>
      <td>Bank</td>
      <td>Bakery</td>
      <td>Fried Chicken Joint</td>
    </tr>
    <tr>
      <th>54</th>
      <td>East Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Brewery</td>
      <td>Vietnamese Restaurant</td>
      <td>American Restaurant</td>
      <td>Diner</td>
      <td>Bar</td>
      <td>Bakery</td>
      <td>French Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Café</td>
    </tr>
    <tr>
      <th>55</th>
      <td>North York</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Bank</td>
      <td>Sushi Restaurant</td>
      <td>Fast Food Restaurant</td>
      <td>Sandwich Place</td>
      <td>Restaurant</td>
      <td>Pub</td>
      <td>Baby Store</td>
      <td>Intersection</td>
    </tr>
    <tr>
      <th>58</th>
      <td>Scarborough</td>
      <td>4.0</td>
      <td>Park</td>
      <td>Thai Restaurant</td>
      <td>Ice Cream Shop</td>
      <td>Diner</td>
      <td>Café</td>
      <td>General Entertainment</td>
      <td>Skating Rink</td>
      <td>Restaurant</td>
      <td>Gym Pool</td>
      <td>Gym</td>
    </tr>
    <tr>
      <th>59</th>
      <td>North York</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Bubble Tea Shop</td>
      <td>Korean Restaurant</td>
      <td>Ramen Restaurant</td>
      <td>Pizza Place</td>
      <td>Japanese Restaurant</td>
      <td>Fast Food Restaurant</td>
      <td>Sandwich Place</td>
      <td>Restaurant</td>
      <td>Sushi Restaurant</td>
    </tr>
    <tr>
      <th>61</th>
      <td>Central Toronto</td>
      <td>4.0</td>
      <td>Gym / Fitness Center</td>
      <td>Trail</td>
      <td>Bookstore</td>
      <td>Café</td>
      <td>Park</td>
      <td>College Quad</td>
      <td>College Gym</td>
      <td>Coffee Shop</td>
      <td>Donut Shop</td>
      <td>Dry Cleaner</td>
    </tr>
    <tr>
      <th>62</th>
      <td>Central Toronto</td>
      <td>4.0</td>
      <td>Sushi Restaurant</td>
      <td>Spa</td>
      <td>Italian Restaurant</td>
      <td>Pharmacy</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Bank</td>
      <td>Pet Store</td>
      <td>Dry Cleaner</td>
      <td>Skating Rink</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Central Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Café</td>
      <td>Restaurant</td>
      <td>Pizza Place</td>
      <td>Gym</td>
      <td>Sandwich Place</td>
      <td>Dessert Shop</td>
      <td>Sushi Restaurant</td>
      <td>Ramen Restaurant</td>
    </tr>
    <tr>
      <th>68</th>
      <td>Central Toronto</td>
      <td>4.0</td>
      <td>Park</td>
      <td>Café</td>
      <td>Bank</td>
      <td>Coffee Shop</td>
      <td>Burger Joint</td>
      <td>Japanese Restaurant</td>
      <td>Bakery</td>
      <td>Trail</td>
      <td>Sushi Restaurant</td>
      <td>Italian Restaurant</td>
    </tr>
    <tr>
      <th>69</th>
      <td>West Toronto</td>
      <td>4.0</td>
      <td>Café</td>
      <td>Bar</td>
      <td>Coffee Shop</td>
      <td>Thai Restaurant</td>
      <td>Park</td>
      <td>Sushi Restaurant</td>
      <td>Convenience Store</td>
      <td>Italian Restaurant</td>
      <td>Grocery Store</td>
      <td>Mexican Restaurant</td>
    </tr>
    <tr>
      <th>73</th>
      <td>Central Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Diner</td>
      <td>Sporting Goods Shop</td>
      <td>Park</td>
      <td>Skating Rink</td>
      <td>Restaurant</td>
      <td>Mexican Restaurant</td>
      <td>Café</td>
      <td>Ramen Restaurant</td>
    </tr>
    <tr>
      <th>74</th>
      <td>Central Toronto</td>
      <td>4.0</td>
      <td>Café</td>
      <td>Italian Restaurant</td>
      <td>Coffee Shop</td>
      <td>Gym</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Bakery</td>
      <td>Restaurant</td>
      <td>Grocery Store</td>
      <td>Museum</td>
      <td>Sandwich Place</td>
    </tr>
    <tr>
      <th>75</th>
      <td>West Toronto</td>
      <td>4.0</td>
      <td>Breakfast Spot</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Sushi Restaurant</td>
      <td>Bar</td>
      <td>Bakery</td>
      <td>Thai Restaurant</td>
      <td>Pub</td>
      <td>Sandwich Place</td>
      <td>Pizza Place</td>
    </tr>
    <tr>
      <th>76</th>
      <td>Mississauga</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Middle Eastern Restaurant</td>
      <td>Bus Station</td>
      <td>Hotel</td>
      <td>Fried Chicken Joint</td>
      <td>Bakery</td>
      <td>Indian Restaurant</td>
      <td>Asian Restaurant</td>
      <td>Burrito Place</td>
      <td>Mexican Restaurant</td>
    </tr>
    <tr>
      <th>79</th>
      <td>Central Toronto</td>
      <td>4.0</td>
      <td>Italian Restaurant</td>
      <td>Coffee Shop</td>
      <td>Sushi Restaurant</td>
      <td>Pizza Place</td>
      <td>Café</td>
      <td>Restaurant</td>
      <td>Middle Eastern Restaurant</td>
      <td>Bank</td>
      <td>Sandwich Place</td>
      <td>Dessert Shop</td>
    </tr>
    <tr>
      <th>80</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Café</td>
      <td>Bakery</td>
      <td>Coffee Shop</td>
      <td>Bar</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Restaurant</td>
      <td>Beer Bar</td>
      <td>Bookstore</td>
      <td>Comfort Food Restaurant</td>
      <td>Japanese Restaurant</td>
    </tr>
    <tr>
      <th>81</th>
      <td>West Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Bakery</td>
      <td>Pizza Place</td>
      <td>Sushi Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Pub</td>
      <td>Restaurant</td>
      <td>Bank</td>
      <td>Falafel Restaurant</td>
    </tr>
    <tr>
      <th>83</th>
      <td>Central Toronto</td>
      <td>4.0</td>
      <td>Grocery Store</td>
      <td>Italian Restaurant</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Thai Restaurant</td>
      <td>Gym</td>
      <td>Bank</td>
      <td>Sushi Restaurant</td>
      <td>Restaurant</td>
      <td>Pizza Place</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Café</td>
      <td>Bar</td>
      <td>Coffee Shop</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Yoga Studio</td>
      <td>Art Gallery</td>
      <td>Gym</td>
      <td>Beer Bar</td>
      <td>Vietnamese Restaurant</td>
      <td>Gaming Cafe</td>
    </tr>
    <tr>
      <th>86</th>
      <td>Central Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Sushi Restaurant</td>
      <td>Park</td>
      <td>Thai Restaurant</td>
      <td>Grocery Store</td>
      <td>Italian Restaurant</td>
      <td>Gym / Fitness Center</td>
      <td>Pizza Place</td>
      <td>Restaurant</td>
      <td>Café</td>
    </tr>
    <tr>
      <th>87</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Harbor / Marina</td>
      <td>Café</td>
      <td>Scenic Lookout</td>
      <td>Park</td>
      <td>Dance Studio</td>
      <td>Garden</td>
      <td>Sushi Restaurant</td>
      <td>Airport Lounge</td>
      <td>Airport</td>
    </tr>
    <tr>
      <th>88</th>
      <td>Etobicoke</td>
      <td>4.0</td>
      <td>Park</td>
      <td>Fast Food Restaurant</td>
      <td>Grocery Store</td>
      <td>Bakery</td>
      <td>Café</td>
      <td>Liquor Store</td>
      <td>Dessert Shop</td>
      <td>Italian Restaurant</td>
      <td>Restaurant</td>
      <td>Skating Rink</td>
    </tr>
    <tr>
      <th>92</th>
      <td>Downtown Toronto Stn A</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Park</td>
      <td>Japanese Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Gastropub</td>
      <td>Hotel</td>
      <td>Restaurant</td>
      <td>Cheese Shop</td>
      <td>Beer Bar</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Gastropub</td>
      <td>Café</td>
      <td>Diner</td>
      <td>Park</td>
      <td>Japanese Restaurant</td>
      <td>Coffee Shop</td>
      <td>Botanical Garden</td>
      <td>Taiwanese Restaurant</td>
      <td>Thai Restaurant</td>
      <td>Garden</td>
    </tr>
    <tr>
      <th>97</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Theater</td>
      <td>Cosmetics Shop</td>
      <td>Japanese Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Restaurant</td>
      <td>Thai Restaurant</td>
      <td>Italian Restaurant</td>
    </tr>
    <tr>
      <th>98</th>
      <td>Etobicoke</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Pub</td>
      <td>French Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Dessert Shop</td>
      <td>Italian Restaurant</td>
      <td>Bank</td>
      <td>Breakfast Spot</td>
      <td>Pizza Place</td>
    </tr>
    <tr>
      <th>99</th>
      <td>Downtown Toronto</td>
      <td>4.0</td>
      <td>Coffee Shop</td>
      <td>Sushi Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Park</td>
      <td>Bookstore</td>
      <td>Restaurant</td>
      <td>Caribbean Restaurant</td>
      <td>Ramen Restaurant</td>
      <td>Clothing Store</td>
    </tr>
    <tr>
      <th>100</th>
      <td>East Toronto Business</td>
      <td>4.0</td>
      <td>Park</td>
      <td>Pizza Place</td>
      <td>Coffee Shop</td>
      <td>Brewery</td>
      <td>Pet Store</td>
      <td>Italian Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Fast Food Restaurant</td>
      <td>Flea Market</td>
      <td>Beach</td>
    </tr>
    <tr>
      <th>102</th>
      <td>Etobicoke</td>
      <td>4.0</td>
      <td>Restaurant</td>
      <td>Gym / Fitness Center</td>
      <td>Yoga Studio</td>
      <td>Convenience Store</td>
      <td>Sushi Restaurant</td>
      <td>Bank</td>
      <td>Bakery</td>
      <td>Burrito Place</td>
      <td>Sandwich Place</td>
      <td>Coffee Shop</td>
    </tr>
  </tbody>
</table>
</div>



Checking which venue appears the most times


```python
Cluster_5['1st Most Common Venue'].describe()
```




    count              55
    unique             15
    top       Coffee Shop
    freq               28
    Name: 1st Most Common Venue, dtype: object


