
# WeatherPy

---------------

## Analysis

- Temperature gets higher and humidity gets lower closer to the equator.
- The wind speeds are higher farther away from the equator.
- Humidity tends to be lower in negative latitudes and higher in positive latitudes.


```python
import pandas as pd
import requests as req
import csv
import matplotlib.pyplot as plt
import random
from citipy import citipy 
import seaborn as sns
import time as time
pd.options.mode.chained_assignment = None
api_key = "25bc90a1196e6f153eece0bc0b0fc9eb"
```

## Generate Latitudes and Longitudes

-----------


```python
data = pd.DataFrame(columns = ['Lat',"Lng","City","Max Temp","Humidity","Clouds","Wind Speed"])
lat = []
lng = []
for x in range(0,1200):
    lat.append(random.uniform(-90,91))
    lng.append(random.uniform(-181,181))

data['Lat'] = lat
data['Lng'] = lng
data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lng</th>
      <th>City</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>90.704185</td>
      <td>-12.237284</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.842379</td>
      <td>-64.942033</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>62.401443</td>
      <td>-38.909656</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.912685</td>
      <td>18.473937</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20.332541</td>
      <td>143.497201</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



## Generate Cities

-------------


```python
cities = []
for index, row in data.iterrows():
    city = citipy.nearest_city(row["Lat"], row["Lng"])
    cities.append(city.city_name)

data['City'] = cities
data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lng</th>
      <th>City</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>90.704185</td>
      <td>-12.237284</td>
      <td>illoqqortoormiut</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.842379</td>
      <td>-64.942033</td>
      <td>santa isabel do rio negro</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>62.401443</td>
      <td>-38.909656</td>
      <td>tasiilaq</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.912685</td>
      <td>18.473937</td>
      <td>mbandaka</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20.332541</td>
      <td>143.497201</td>
      <td>shimoda</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
new_data = data.drop_duplicates("City", keep="first")
len(new_data)
```




    499



## Perform API Calls

----------------


```python
temp = []
humidity = []
wind = []
clouds = []

counter = 0
url = "https://api.openweathermap.org/data/2.5/weather?q="
units = "imperial"

for index, row in new_data.iterrows():
    counter +=1
    city = row["City"]
    target_url = url + city + "&appid=" + api_key + "&units=" + units
    print("Now Processing Record " + str(counter))
    print("The city is "+row["City"])
    print(target_url)
    print("__________________________________________________________________________________________")
    info = req.get(target_url).json()
    temp.append(info['main']['temp'])
    humidity.append(info['main']['humidity'])
    clouds.append(info['clouds']['all'])
    wind.append(info['wind']['speed'])
    time.sleep(1)

new_data["Max Temp"] = temp
new_data["Humidity"] = humidity
new_data["Wind Speed"] = wind
new_data["Clouds"] = clouds
```

    Now Processing Record 1
    The city is illoqqortoormiut
    https://api.openweathermap.org/data/2.5/weather?q=illoqqortoormiut&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 2
    The city is santa isabel do rio negro
    https://api.openweathermap.org/data/2.5/weather?q=santa isabel do rio negro&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 3
    The city is tasiilaq
    https://api.openweathermap.org/data/2.5/weather?q=tasiilaq&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 4
    The city is mbandaka
    https://api.openweathermap.org/data/2.5/weather?q=mbandaka&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 5
    The city is shimoda
    https://api.openweathermap.org/data/2.5/weather?q=shimoda&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 6
    The city is lompoc
    https://api.openweathermap.org/data/2.5/weather?q=lompoc&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 7
    The city is mahalapye
    https://api.openweathermap.org/data/2.5/weather?q=mahalapye&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 8
    The city is le havre
    https://api.openweathermap.org/data/2.5/weather?q=le havre&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 9
    The city is busselton
    https://api.openweathermap.org/data/2.5/weather?q=busselton&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 10
    The city is qaanaaq
    https://api.openweathermap.org/data/2.5/weather?q=qaanaaq&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 11
    The city is mataura
    https://api.openweathermap.org/data/2.5/weather?q=mataura&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 12
    The city is upernavik
    https://api.openweathermap.org/data/2.5/weather?q=upernavik&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 13
    The city is whitehorse
    https://api.openweathermap.org/data/2.5/weather?q=whitehorse&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 14
    The city is mar del plata
    https://api.openweathermap.org/data/2.5/weather?q=mar del plata&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 15
    The city is praia
    https://api.openweathermap.org/data/2.5/weather?q=praia&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 16
    The city is chaozhou
    https://api.openweathermap.org/data/2.5/weather?q=chaozhou&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 17
    The city is vaitupu
    https://api.openweathermap.org/data/2.5/weather?q=vaitupu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 18
    The city is ushuaia
    https://api.openweathermap.org/data/2.5/weather?q=ushuaia&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 19
    The city is cayenne
    https://api.openweathermap.org/data/2.5/weather?q=cayenne&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 20
    The city is pisco
    https://api.openweathermap.org/data/2.5/weather?q=pisco&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 21
    The city is punta arenas
    https://api.openweathermap.org/data/2.5/weather?q=punta arenas&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 22
    The city is rikitea
    https://api.openweathermap.org/data/2.5/weather?q=rikitea&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 23
    The city is jalu
    https://api.openweathermap.org/data/2.5/weather?q=jalu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 24
    The city is port alfred
    https://api.openweathermap.org/data/2.5/weather?q=port alfred&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 25
    The city is kyra
    https://api.openweathermap.org/data/2.5/weather?q=kyra&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 26
    The city is taolanaro
    https://api.openweathermap.org/data/2.5/weather?q=taolanaro&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 27
    The city is kapaa
    https://api.openweathermap.org/data/2.5/weather?q=kapaa&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 28
    The city is hobart
    https://api.openweathermap.org/data/2.5/weather?q=hobart&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 29
    The city is turkmenabat
    https://api.openweathermap.org/data/2.5/weather?q=turkmenabat&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 30
    The city is carnarvon
    https://api.openweathermap.org/data/2.5/weather?q=carnarvon&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 31
    The city is gizo
    https://api.openweathermap.org/data/2.5/weather?q=gizo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 32
    The city is jacqueville
    https://api.openweathermap.org/data/2.5/weather?q=jacqueville&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 33
    The city is leningradskiy
    https://api.openweathermap.org/data/2.5/weather?q=leningradskiy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 34
    The city is kodiak
    https://api.openweathermap.org/data/2.5/weather?q=kodiak&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 35
    The city is inverell
    https://api.openweathermap.org/data/2.5/weather?q=inverell&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 36
    The city is barrow
    https://api.openweathermap.org/data/2.5/weather?q=barrow&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 37
    The city is sentyabrskiy
    https://api.openweathermap.org/data/2.5/weather?q=sentyabrskiy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 38
    The city is la tuque
    https://api.openweathermap.org/data/2.5/weather?q=la tuque&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 39
    The city is arraial do cabo
    https://api.openweathermap.org/data/2.5/weather?q=arraial do cabo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 40
    The city is butaritari
    https://api.openweathermap.org/data/2.5/weather?q=butaritari&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 41
    The city is albany
    https://api.openweathermap.org/data/2.5/weather?q=albany&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 42
    The city is luganville
    https://api.openweathermap.org/data/2.5/weather?q=luganville&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 43
    The city is kavieng
    https://api.openweathermap.org/data/2.5/weather?q=kavieng&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 44
    The city is longyearbyen
    https://api.openweathermap.org/data/2.5/weather?q=longyearbyen&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 45
    The city is formoso do araguaia
    https://api.openweathermap.org/data/2.5/weather?q=formoso do araguaia&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 46
    The city is baghdad
    https://api.openweathermap.org/data/2.5/weather?q=baghdad&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 47
    The city is koslan
    https://api.openweathermap.org/data/2.5/weather?q=koslan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 48
    The city is labuhan
    https://api.openweathermap.org/data/2.5/weather?q=labuhan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 49
    The city is port elizabeth
    https://api.openweathermap.org/data/2.5/weather?q=port elizabeth&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 50
    The city is floro
    https://api.openweathermap.org/data/2.5/weather?q=floro&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 51
    The city is narsaq
    https://api.openweathermap.org/data/2.5/weather?q=narsaq&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 52
    The city is tabou
    https://api.openweathermap.org/data/2.5/weather?q=tabou&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 53
    The city is darnah
    https://api.openweathermap.org/data/2.5/weather?q=darnah&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 54
    The city is jardim
    https://api.openweathermap.org/data/2.5/weather?q=jardim&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 55
    The city is terra santa
    https://api.openweathermap.org/data/2.5/weather?q=terra santa&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 56
    The city is hilo
    https://api.openweathermap.org/data/2.5/weather?q=hilo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 57
    The city is ostrovnoy
    https://api.openweathermap.org/data/2.5/weather?q=ostrovnoy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 58
    The city is eaglesfield
    https://api.openweathermap.org/data/2.5/weather?q=eaglesfield&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 59
    The city is richards bay
    https://api.openweathermap.org/data/2.5/weather?q=richards bay&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 60
    The city is satitoa
    https://api.openweathermap.org/data/2.5/weather?q=satitoa&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 61
    The city is vaini
    https://api.openweathermap.org/data/2.5/weather?q=vaini&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 62
    The city is vila
    https://api.openweathermap.org/data/2.5/weather?q=vila&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 63
    The city is bethanien
    https://api.openweathermap.org/data/2.5/weather?q=bethanien&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 64
    The city is belushya guba
    https://api.openweathermap.org/data/2.5/weather?q=belushya guba&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 65
    The city is bandar-e torkaman
    https://api.openweathermap.org/data/2.5/weather?q=bandar-e torkaman&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 66
    The city is flin flon
    https://api.openweathermap.org/data/2.5/weather?q=flin flon&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 67
    The city is saint-philippe
    https://api.openweathermap.org/data/2.5/weather?q=saint-philippe&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 68
    The city is nassau
    https://api.openweathermap.org/data/2.5/weather?q=nassau&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 69
    The city is manjacaze
    https://api.openweathermap.org/data/2.5/weather?q=manjacaze&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 70
    The city is mayo
    https://api.openweathermap.org/data/2.5/weather?q=mayo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 71
    The city is mecca
    https://api.openweathermap.org/data/2.5/weather?q=mecca&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 72
    The city is bredasdorp
    https://api.openweathermap.org/data/2.5/weather?q=bredasdorp&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 73
    The city is mineiros
    https://api.openweathermap.org/data/2.5/weather?q=mineiros&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 74
    The city is snezhnogorsk
    https://api.openweathermap.org/data/2.5/weather?q=snezhnogorsk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 75
    The city is mahajanga
    https://api.openweathermap.org/data/2.5/weather?q=mahajanga&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 76
    The city is eskasem
    https://api.openweathermap.org/data/2.5/weather?q=eskasem&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 77
    The city is voh
    https://api.openweathermap.org/data/2.5/weather?q=voh&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 78
    The city is atuona
    https://api.openweathermap.org/data/2.5/weather?q=atuona&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 79
    The city is santa rosa
    https://api.openweathermap.org/data/2.5/weather?q=santa rosa&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 80
    The city is xinmin
    https://api.openweathermap.org/data/2.5/weather?q=xinmin&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 81
    The city is sembe
    https://api.openweathermap.org/data/2.5/weather?q=sembe&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 82
    The city is hasaki
    https://api.openweathermap.org/data/2.5/weather?q=hasaki&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 83
    The city is touros
    https://api.openweathermap.org/data/2.5/weather?q=touros&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 84
    The city is haines junction
    https://api.openweathermap.org/data/2.5/weather?q=haines junction&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 85
    The city is nikolskoye
    https://api.openweathermap.org/data/2.5/weather?q=nikolskoye&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 86
    The city is college
    https://api.openweathermap.org/data/2.5/weather?q=college&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 87
    The city is glendive
    https://api.openweathermap.org/data/2.5/weather?q=glendive&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 88
    The city is khatanga
    https://api.openweathermap.org/data/2.5/weather?q=khatanga&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 89
    The city is ribeira grande
    https://api.openweathermap.org/data/2.5/weather?q=ribeira grande&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 90
    The city is mys shmidta
    https://api.openweathermap.org/data/2.5/weather?q=mys shmidta&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 91
    The city is kununurra
    https://api.openweathermap.org/data/2.5/weather?q=kununurra&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 92
    The city is padang
    https://api.openweathermap.org/data/2.5/weather?q=padang&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 93
    The city is chuy
    https://api.openweathermap.org/data/2.5/weather?q=chuy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 94
    The city is attawapiskat
    https://api.openweathermap.org/data/2.5/weather?q=attawapiskat&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 95
    The city is grand gaube
    https://api.openweathermap.org/data/2.5/weather?q=grand gaube&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 96
    The city is new norfolk
    https://api.openweathermap.org/data/2.5/weather?q=new norfolk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 97
    The city is torbay
    https://api.openweathermap.org/data/2.5/weather?q=torbay&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 98
    The city is miri
    https://api.openweathermap.org/data/2.5/weather?q=miri&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 99
    The city is bereda
    https://api.openweathermap.org/data/2.5/weather?q=bereda&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 100
    The city is paamiut
    https://api.openweathermap.org/data/2.5/weather?q=paamiut&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 101
    The city is ormara
    https://api.openweathermap.org/data/2.5/weather?q=ormara&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 102
    The city is evensk
    https://api.openweathermap.org/data/2.5/weather?q=evensk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 103
    The city is umzimvubu
    https://api.openweathermap.org/data/2.5/weather?q=umzimvubu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 104
    The city is kaitangata
    https://api.openweathermap.org/data/2.5/weather?q=kaitangata&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 105
    The city is georgetown
    https://api.openweathermap.org/data/2.5/weather?q=georgetown&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 106
    The city is grand river south east
    https://api.openweathermap.org/data/2.5/weather?q=grand river south east&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 107
    The city is rio tuba
    https://api.openweathermap.org/data/2.5/weather?q=rio tuba&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 108
    The city is saint george
    https://api.openweathermap.org/data/2.5/weather?q=saint george&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 109
    The city is northam
    https://api.openweathermap.org/data/2.5/weather?q=northam&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 110
    The city is havre-saint-pierre
    https://api.openweathermap.org/data/2.5/weather?q=havre-saint-pierre&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 111
    The city is sur
    https://api.openweathermap.org/data/2.5/weather?q=sur&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 112
    The city is cape town
    https://api.openweathermap.org/data/2.5/weather?q=cape town&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 113
    The city is yellowknife
    https://api.openweathermap.org/data/2.5/weather?q=yellowknife&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 114
    The city is khorinsk
    https://api.openweathermap.org/data/2.5/weather?q=khorinsk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 115
    The city is tuatapere
    https://api.openweathermap.org/data/2.5/weather?q=tuatapere&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 116
    The city is alappuzha
    https://api.openweathermap.org/data/2.5/weather?q=alappuzha&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 117
    The city is bethel
    https://api.openweathermap.org/data/2.5/weather?q=bethel&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 118
    The city is izvestkovyy
    https://api.openweathermap.org/data/2.5/weather?q=izvestkovyy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 119
    The city is jamestown
    https://api.openweathermap.org/data/2.5/weather?q=jamestown&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 120
    The city is castro
    https://api.openweathermap.org/data/2.5/weather?q=castro&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 121
    The city is coquimbo
    https://api.openweathermap.org/data/2.5/weather?q=coquimbo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 122
    The city is baykit
    https://api.openweathermap.org/data/2.5/weather?q=baykit&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 123
    The city is san quintin
    https://api.openweathermap.org/data/2.5/weather?q=san quintin&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 124
    The city is zarinsk
    https://api.openweathermap.org/data/2.5/weather?q=zarinsk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 125
    The city is hermanus
    https://api.openweathermap.org/data/2.5/weather?q=hermanus&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 126
    The city is avarua
    https://api.openweathermap.org/data/2.5/weather?q=avarua&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 127
    The city is lethem
    https://api.openweathermap.org/data/2.5/weather?q=lethem&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 128
    The city is fomboni
    https://api.openweathermap.org/data/2.5/weather?q=fomboni&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 129
    The city is sterlibashevo
    https://api.openweathermap.org/data/2.5/weather?q=sterlibashevo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 130
    The city is lavrentiya
    https://api.openweathermap.org/data/2.5/weather?q=lavrentiya&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 131
    The city is san patricio
    https://api.openweathermap.org/data/2.5/weather?q=san patricio&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 132
    The city is codrington
    https://api.openweathermap.org/data/2.5/weather?q=codrington&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 133
    The city is esperance
    https://api.openweathermap.org/data/2.5/weather?q=esperance&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 134
    The city is te anau
    https://api.openweathermap.org/data/2.5/weather?q=te anau&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 135
    The city is rio gallegos
    https://api.openweathermap.org/data/2.5/weather?q=rio gallegos&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 136
    The city is tarko-sale
    https://api.openweathermap.org/data/2.5/weather?q=tarko-sale&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 137
    The city is pevek
    https://api.openweathermap.org/data/2.5/weather?q=pevek&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 138
    The city is saint-pierre
    https://api.openweathermap.org/data/2.5/weather?q=saint-pierre&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 139
    The city is bluff
    https://api.openweathermap.org/data/2.5/weather?q=bluff&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 140
    The city is ancud
    https://api.openweathermap.org/data/2.5/weather?q=ancud&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 141
    The city is shingu
    https://api.openweathermap.org/data/2.5/weather?q=shingu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 142
    The city is amderma
    https://api.openweathermap.org/data/2.5/weather?q=amderma&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 143
    The city is lagos
    https://api.openweathermap.org/data/2.5/weather?q=lagos&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 144
    The city is kurchum
    https://api.openweathermap.org/data/2.5/weather?q=kurchum&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 145
    The city is hithadhoo
    https://api.openweathermap.org/data/2.5/weather?q=hithadhoo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 146
    The city is samana
    https://api.openweathermap.org/data/2.5/weather?q=samana&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 147
    The city is palabuhanratu
    https://api.openweathermap.org/data/2.5/weather?q=palabuhanratu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 148
    The city is port blair
    https://api.openweathermap.org/data/2.5/weather?q=port blair&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 149
    The city is katsuura
    https://api.openweathermap.org/data/2.5/weather?q=katsuura&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 150
    The city is mumford
    https://api.openweathermap.org/data/2.5/weather?q=mumford&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 151
    The city is ilulissat
    https://api.openweathermap.org/data/2.5/weather?q=ilulissat&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 152
    The city is trincomalee
    https://api.openweathermap.org/data/2.5/weather?q=trincomalee&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 153
    The city is esmeralda
    https://api.openweathermap.org/data/2.5/weather?q=esmeralda&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 154
    The city is gurlan
    https://api.openweathermap.org/data/2.5/weather?q=gurlan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 155
    The city is severo-kurilsk
    https://api.openweathermap.org/data/2.5/weather?q=severo-kurilsk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 156
    The city is tiksi
    https://api.openweathermap.org/data/2.5/weather?q=tiksi&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 157
    The city is hamilton
    https://api.openweathermap.org/data/2.5/weather?q=hamilton&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 158
    The city is hobyo
    https://api.openweathermap.org/data/2.5/weather?q=hobyo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 159
    The city is chiredzi
    https://api.openweathermap.org/data/2.5/weather?q=chiredzi&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 160
    The city is cabo san lucas
    https://api.openweathermap.org/data/2.5/weather?q=cabo san lucas&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 161
    The city is nizhniy chir
    https://api.openweathermap.org/data/2.5/weather?q=nizhniy chir&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 162
    The city is victoria
    https://api.openweathermap.org/data/2.5/weather?q=victoria&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 163
    The city is valparaiso
    https://api.openweathermap.org/data/2.5/weather?q=valparaiso&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 164
    The city is mount gambier
    https://api.openweathermap.org/data/2.5/weather?q=mount gambier&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 165
    The city is road town
    https://api.openweathermap.org/data/2.5/weather?q=road town&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 166
    The city is ponta do sol
    https://api.openweathermap.org/data/2.5/weather?q=ponta do sol&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 167
    The city is charters towers
    https://api.openweathermap.org/data/2.5/weather?q=charters towers&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 168
    The city is komsomolskiy
    https://api.openweathermap.org/data/2.5/weather?q=komsomolskiy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 169
    The city is san andres
    https://api.openweathermap.org/data/2.5/weather?q=san andres&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 170
    The city is zafra
    https://api.openweathermap.org/data/2.5/weather?q=zafra&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 171
    The city is yumen
    https://api.openweathermap.org/data/2.5/weather?q=yumen&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 172
    The city is keti bandar
    https://api.openweathermap.org/data/2.5/weather?q=keti bandar&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 173
    The city is pacific grove
    https://api.openweathermap.org/data/2.5/weather?q=pacific grove&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 174
    The city is mutoko
    https://api.openweathermap.org/data/2.5/weather?q=mutoko&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 175
    The city is maningrida
    https://api.openweathermap.org/data/2.5/weather?q=maningrida&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 176
    The city is ulladulla
    https://api.openweathermap.org/data/2.5/weather?q=ulladulla&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 177
    The city is east london
    https://api.openweathermap.org/data/2.5/weather?q=east london&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 178
    The city is hit
    https://api.openweathermap.org/data/2.5/weather?q=hit&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 179
    The city is wasilla
    https://api.openweathermap.org/data/2.5/weather?q=wasilla&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 180
    The city is namuac
    https://api.openweathermap.org/data/2.5/weather?q=namuac&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 181
    The city is puerto ayora
    https://api.openweathermap.org/data/2.5/weather?q=puerto ayora&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 182
    The city is port-cartier
    https://api.openweathermap.org/data/2.5/weather?q=port-cartier&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 183
    The city is nemuro
    https://api.openweathermap.org/data/2.5/weather?q=nemuro&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 184
    The city is okhotsk
    https://api.openweathermap.org/data/2.5/weather?q=okhotsk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 185
    The city is bur gabo
    https://api.openweathermap.org/data/2.5/weather?q=bur gabo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 186
    The city is berlevag
    https://api.openweathermap.org/data/2.5/weather?q=berlevag&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 187
    The city is vardo
    https://api.openweathermap.org/data/2.5/weather?q=vardo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 188
    The city is felanitx
    https://api.openweathermap.org/data/2.5/weather?q=felanitx&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 189
    The city is camana
    https://api.openweathermap.org/data/2.5/weather?q=camana&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 190
    The city is taoudenni
    https://api.openweathermap.org/data/2.5/weather?q=taoudenni&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 191
    The city is daru
    https://api.openweathermap.org/data/2.5/weather?q=daru&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 192
    The city is thompson
    https://api.openweathermap.org/data/2.5/weather?q=thompson&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 193
    The city is abu samrah
    https://api.openweathermap.org/data/2.5/weather?q=abu samrah&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 194
    The city is sitka
    https://api.openweathermap.org/data/2.5/weather?q=sitka&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 195
    The city is eisiskes
    https://api.openweathermap.org/data/2.5/weather?q=eisiskes&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 196
    The city is daura
    https://api.openweathermap.org/data/2.5/weather?q=daura&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 197
    The city is bengkulu
    https://api.openweathermap.org/data/2.5/weather?q=bengkulu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 198
    The city is horni jiretin
    https://api.openweathermap.org/data/2.5/weather?q=horni jiretin&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 199
    The city is bilibino
    https://api.openweathermap.org/data/2.5/weather?q=bilibino&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 200
    The city is khonuu
    https://api.openweathermap.org/data/2.5/weather?q=khonuu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 201
    The city is kaeo
    https://api.openweathermap.org/data/2.5/weather?q=kaeo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 202
    The city is lamar
    https://api.openweathermap.org/data/2.5/weather?q=lamar&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 203
    The city is riyadh
    https://api.openweathermap.org/data/2.5/weather?q=riyadh&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 204
    The city is acarau
    https://api.openweathermap.org/data/2.5/weather?q=acarau&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 205
    The city is saleaula
    https://api.openweathermap.org/data/2.5/weather?q=saleaula&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 206
    The city is fare
    https://api.openweathermap.org/data/2.5/weather?q=fare&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 207
    The city is tuktoyaktuk
    https://api.openweathermap.org/data/2.5/weather?q=tuktoyaktuk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 208
    The city is olavarria
    https://api.openweathermap.org/data/2.5/weather?q=olavarria&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 209
    The city is pochutla
    https://api.openweathermap.org/data/2.5/weather?q=pochutla&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 210
    The city is hundested
    https://api.openweathermap.org/data/2.5/weather?q=hundested&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 211
    The city is amos
    https://api.openweathermap.org/data/2.5/weather?q=amos&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 212
    The city is nabire
    https://api.openweathermap.org/data/2.5/weather?q=nabire&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 213
    The city is dikson
    https://api.openweathermap.org/data/2.5/weather?q=dikson&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 214
    The city is suntar
    https://api.openweathermap.org/data/2.5/weather?q=suntar&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 215
    The city is saldanha
    https://api.openweathermap.org/data/2.5/weather?q=saldanha&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 216
    The city is puqi
    https://api.openweathermap.org/data/2.5/weather?q=puqi&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 217
    The city is ust-barguzin
    https://api.openweathermap.org/data/2.5/weather?q=ust-barguzin&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 218
    The city is aborlan
    https://api.openweathermap.org/data/2.5/weather?q=aborlan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 219
    The city is angoram
    https://api.openweathermap.org/data/2.5/weather?q=angoram&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 220
    The city is barentsburg
    https://api.openweathermap.org/data/2.5/weather?q=barentsburg&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 221
    The city is aklavik
    https://api.openweathermap.org/data/2.5/weather?q=aklavik&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 222
    The city is xunchang
    https://api.openweathermap.org/data/2.5/weather?q=xunchang&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 223
    The city is columbus
    https://api.openweathermap.org/data/2.5/weather?q=columbus&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 224
    The city is grindavik
    https://api.openweathermap.org/data/2.5/weather?q=grindavik&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 225
    The city is ahipara
    https://api.openweathermap.org/data/2.5/weather?q=ahipara&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 226
    The city is the valley
    https://api.openweathermap.org/data/2.5/weather?q=the valley&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 227
    The city is bambous virieux
    https://api.openweathermap.org/data/2.5/weather?q=bambous virieux&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 228
    The city is longyan
    https://api.openweathermap.org/data/2.5/weather?q=longyan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 229
    The city is mahebourg
    https://api.openweathermap.org/data/2.5/weather?q=mahebourg&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 230
    The city is solovetskiy
    https://api.openweathermap.org/data/2.5/weather?q=solovetskiy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 231
    The city is marsassoum
    https://api.openweathermap.org/data/2.5/weather?q=marsassoum&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 232
    The city is kahului
    https://api.openweathermap.org/data/2.5/weather?q=kahului&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 233
    The city is bonnyville
    https://api.openweathermap.org/data/2.5/weather?q=bonnyville&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 234
    The city is passo de camaragibe
    https://api.openweathermap.org/data/2.5/weather?q=passo de camaragibe&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 235
    The city is yiyang
    https://api.openweathermap.org/data/2.5/weather?q=yiyang&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 236
    The city is rugeley
    https://api.openweathermap.org/data/2.5/weather?q=rugeley&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 237
    The city is north bend
    https://api.openweathermap.org/data/2.5/weather?q=north bend&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 238
    The city is noto
    https://api.openweathermap.org/data/2.5/weather?q=noto&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 239
    The city is kruisfontein
    https://api.openweathermap.org/data/2.5/weather?q=kruisfontein&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 240
    The city is zatoka
    https://api.openweathermap.org/data/2.5/weather?q=zatoka&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 241
    The city is shubarkuduk
    https://api.openweathermap.org/data/2.5/weather?q=shubarkuduk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 242
    The city is foz
    https://api.openweathermap.org/data/2.5/weather?q=foz&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 243
    The city is butterworth
    https://api.openweathermap.org/data/2.5/weather?q=butterworth&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 244
    The city is nizhneyansk
    https://api.openweathermap.org/data/2.5/weather?q=nizhneyansk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 245
    The city is chicama
    https://api.openweathermap.org/data/2.5/weather?q=chicama&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 246
    The city is obidos
    https://api.openweathermap.org/data/2.5/weather?q=obidos&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 247
    The city is guerrero negro
    https://api.openweathermap.org/data/2.5/weather?q=guerrero negro&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 248
    The city is geraldton
    https://api.openweathermap.org/data/2.5/weather?q=geraldton&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 249
    The city is hambantota
    https://api.openweathermap.org/data/2.5/weather?q=hambantota&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 250
    The city is bulgan
    https://api.openweathermap.org/data/2.5/weather?q=bulgan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 251
    The city is algiers
    https://api.openweathermap.org/data/2.5/weather?q=algiers&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 252
    The city is perene
    https://api.openweathermap.org/data/2.5/weather?q=perene&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 253
    The city is aflu
    https://api.openweathermap.org/data/2.5/weather?q=aflu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 254
    The city is tura
    https://api.openweathermap.org/data/2.5/weather?q=tura&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 255
    The city is mehamn
    https://api.openweathermap.org/data/2.5/weather?q=mehamn&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 256
    The city is monrovia
    https://api.openweathermap.org/data/2.5/weather?q=monrovia&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 257
    The city is la palma
    https://api.openweathermap.org/data/2.5/weather?q=la palma&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 258
    The city is ishigaki
    https://api.openweathermap.org/data/2.5/weather?q=ishigaki&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 259
    The city is norman wells
    https://api.openweathermap.org/data/2.5/weather?q=norman wells&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 260
    The city is belmonte
    https://api.openweathermap.org/data/2.5/weather?q=belmonte&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 261
    The city is talnakh
    https://api.openweathermap.org/data/2.5/weather?q=talnakh&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 262
    The city is galveston
    https://api.openweathermap.org/data/2.5/weather?q=galveston&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 263
    The city is port hardy
    https://api.openweathermap.org/data/2.5/weather?q=port hardy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 264
    The city is sioux lookout
    https://api.openweathermap.org/data/2.5/weather?q=sioux lookout&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 265
    The city is bay roberts
    https://api.openweathermap.org/data/2.5/weather?q=bay roberts&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 266
    The city is elsterwerda
    https://api.openweathermap.org/data/2.5/weather?q=elsterwerda&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 267
    The city is vanimo
    https://api.openweathermap.org/data/2.5/weather?q=vanimo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 268
    The city is rawson
    https://api.openweathermap.org/data/2.5/weather?q=rawson&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 269
    The city is airai
    https://api.openweathermap.org/data/2.5/weather?q=airai&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 270
    The city is moree
    https://api.openweathermap.org/data/2.5/weather?q=moree&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 271
    The city is amot
    https://api.openweathermap.org/data/2.5/weather?q=amot&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 272
    The city is luanda
    https://api.openweathermap.org/data/2.5/weather?q=luanda&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 273
    The city is goderich
    https://api.openweathermap.org/data/2.5/weather?q=goderich&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 274
    The city is high level
    https://api.openweathermap.org/data/2.5/weather?q=high level&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 275
    The city is prince rupert
    https://api.openweathermap.org/data/2.5/weather?q=prince rupert&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 276
    The city is beringovskiy
    https://api.openweathermap.org/data/2.5/weather?q=beringovskiy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 277
    The city is waddan
    https://api.openweathermap.org/data/2.5/weather?q=waddan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 278
    The city is sao filipe
    https://api.openweathermap.org/data/2.5/weather?q=sao filipe&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 279
    The city is kloulklubed
    https://api.openweathermap.org/data/2.5/weather?q=kloulklubed&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 280
    The city is deputatskiy
    https://api.openweathermap.org/data/2.5/weather?q=deputatskiy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 281
    The city is westport
    https://api.openweathermap.org/data/2.5/weather?q=westport&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 282
    The city is iqaluit
    https://api.openweathermap.org/data/2.5/weather?q=iqaluit&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 283
    The city is amuntai
    https://api.openweathermap.org/data/2.5/weather?q=amuntai&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 284
    The city is kirakira
    https://api.openweathermap.org/data/2.5/weather?q=kirakira&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 285
    The city is cheuskiny
    https://api.openweathermap.org/data/2.5/weather?q=cheuskiny&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 286
    The city is port lincoln
    https://api.openweathermap.org/data/2.5/weather?q=port lincoln&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 287
    The city is pontianak
    https://api.openweathermap.org/data/2.5/weather?q=pontianak&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 288
    The city is faanui
    https://api.openweathermap.org/data/2.5/weather?q=faanui&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 289
    The city is alugan
    https://api.openweathermap.org/data/2.5/weather?q=alugan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 290
    The city is half moon bay
    https://api.openweathermap.org/data/2.5/weather?q=half moon bay&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 291
    The city is lebu
    https://api.openweathermap.org/data/2.5/weather?q=lebu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 292
    The city is kawalu
    https://api.openweathermap.org/data/2.5/weather?q=kawalu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 293
    The city is orikum
    https://api.openweathermap.org/data/2.5/weather?q=orikum&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 294
    The city is chokurdakh
    https://api.openweathermap.org/data/2.5/weather?q=chokurdakh&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 295
    The city is tevriz
    https://api.openweathermap.org/data/2.5/weather?q=tevriz&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 296
    The city is huntingdon
    https://api.openweathermap.org/data/2.5/weather?q=huntingdon&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 297
    The city is vao
    https://api.openweathermap.org/data/2.5/weather?q=vao&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 298
    The city is dingle
    https://api.openweathermap.org/data/2.5/weather?q=dingle&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 299
    The city is charlestown
    https://api.openweathermap.org/data/2.5/weather?q=charlestown&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 300
    The city is pangnirtung
    https://api.openweathermap.org/data/2.5/weather?q=pangnirtung&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 301
    The city is wageningen
    https://api.openweathermap.org/data/2.5/weather?q=wageningen&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 302
    The city is jiwani
    https://api.openweathermap.org/data/2.5/weather?q=jiwani&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 303
    The city is taybad
    https://api.openweathermap.org/data/2.5/weather?q=taybad&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 304
    The city is karsava
    https://api.openweathermap.org/data/2.5/weather?q=karsava&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 305
    The city is karratha
    https://api.openweathermap.org/data/2.5/weather?q=karratha&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 306
    The city is utrecht
    https://api.openweathermap.org/data/2.5/weather?q=utrecht&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 307
    The city is pervomayskoye
    https://api.openweathermap.org/data/2.5/weather?q=pervomayskoye&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 308
    The city is maldonado
    https://api.openweathermap.org/data/2.5/weather?q=maldonado&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 309
    The city is kemijarvi
    https://api.openweathermap.org/data/2.5/weather?q=kemijarvi&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 310
    The city is souillac
    https://api.openweathermap.org/data/2.5/weather?q=souillac&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 311
    The city is altoona
    https://api.openweathermap.org/data/2.5/weather?q=altoona&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 312
    The city is tiarei
    https://api.openweathermap.org/data/2.5/weather?q=tiarei&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 313
    The city is solnechnyy
    https://api.openweathermap.org/data/2.5/weather?q=solnechnyy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 314
    The city is portland
    https://api.openweathermap.org/data/2.5/weather?q=portland&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 315
    The city is artyk
    https://api.openweathermap.org/data/2.5/weather?q=artyk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 316
    The city is lolua
    https://api.openweathermap.org/data/2.5/weather?q=lolua&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 317
    The city is mackenzie
    https://api.openweathermap.org/data/2.5/weather?q=mackenzie&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 318
    The city is sabang
    https://api.openweathermap.org/data/2.5/weather?q=sabang&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 319
    The city is chulumani
    https://api.openweathermap.org/data/2.5/weather?q=chulumani&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 320
    The city is kutum
    https://api.openweathermap.org/data/2.5/weather?q=kutum&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 321
    The city is xianyang
    https://api.openweathermap.org/data/2.5/weather?q=xianyang&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 322
    The city is great yarmouth
    https://api.openweathermap.org/data/2.5/weather?q=great yarmouth&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 323
    The city is slave lake
    https://api.openweathermap.org/data/2.5/weather?q=slave lake&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 324
    The city is marzuq
    https://api.openweathermap.org/data/2.5/weather?q=marzuq&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 325
    The city is fallon
    https://api.openweathermap.org/data/2.5/weather?q=fallon&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 326
    The city is pyaozerskiy
    https://api.openweathermap.org/data/2.5/weather?q=pyaozerskiy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 327
    The city is bar harbor
    https://api.openweathermap.org/data/2.5/weather?q=bar harbor&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 328
    The city is itaqui
    https://api.openweathermap.org/data/2.5/weather?q=itaqui&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 329
    The city is saint anthony
    https://api.openweathermap.org/data/2.5/weather?q=saint anthony&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 330
    The city is ternate
    https://api.openweathermap.org/data/2.5/weather?q=ternate&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 331
    The city is mchinji
    https://api.openweathermap.org/data/2.5/weather?q=mchinji&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 332
    The city is george
    https://api.openweathermap.org/data/2.5/weather?q=george&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 333
    The city is san vicente
    https://api.openweathermap.org/data/2.5/weather?q=san vicente&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 334
    The city is avera
    https://api.openweathermap.org/data/2.5/weather?q=avera&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 335
    The city is constitucion
    https://api.openweathermap.org/data/2.5/weather?q=constitucion&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 336
    The city is tautira
    https://api.openweathermap.org/data/2.5/weather?q=tautira&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 337
    The city is cherskiy
    https://api.openweathermap.org/data/2.5/weather?q=cherskiy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 338
    The city is saskylakh
    https://api.openweathermap.org/data/2.5/weather?q=saskylakh&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 339
    The city is laguna
    https://api.openweathermap.org/data/2.5/weather?q=laguna&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 340
    The city is iseyin
    https://api.openweathermap.org/data/2.5/weather?q=iseyin&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 341
    The city is vila franca do campo
    https://api.openweathermap.org/data/2.5/weather?q=vila franca do campo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 342
    The city is laurel
    https://api.openweathermap.org/data/2.5/weather?q=laurel&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 343
    The city is lumphat
    https://api.openweathermap.org/data/2.5/weather?q=lumphat&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 344
    The city is kabalo
    https://api.openweathermap.org/data/2.5/weather?q=kabalo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 345
    The city is ust-kulom
    https://api.openweathermap.org/data/2.5/weather?q=ust-kulom&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 346
    The city is provideniya
    https://api.openweathermap.org/data/2.5/weather?q=provideniya&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 347
    The city is camapua
    https://api.openweathermap.org/data/2.5/weather?q=camapua&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 348
    The city is kamenskoye
    https://api.openweathermap.org/data/2.5/weather?q=kamenskoye&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 349
    The city is ayna
    https://api.openweathermap.org/data/2.5/weather?q=ayna&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 350
    The city is aranos
    https://api.openweathermap.org/data/2.5/weather?q=aranos&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 351
    The city is lonevag
    https://api.openweathermap.org/data/2.5/weather?q=lonevag&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 352
    The city is bokoro
    https://api.openweathermap.org/data/2.5/weather?q=bokoro&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 353
    The city is kabinda
    https://api.openweathermap.org/data/2.5/weather?q=kabinda&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 354
    The city is carbonia
    https://api.openweathermap.org/data/2.5/weather?q=carbonia&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 355
    The city is esik
    https://api.openweathermap.org/data/2.5/weather?q=esik&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 356
    The city is port-gentil
    https://api.openweathermap.org/data/2.5/weather?q=port-gentil&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 357
    The city is stargard szczecinski
    https://api.openweathermap.org/data/2.5/weather?q=stargard szczecinski&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 358
    The city is talaya
    https://api.openweathermap.org/data/2.5/weather?q=talaya&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 359
    The city is zlatoustovsk
    https://api.openweathermap.org/data/2.5/weather?q=zlatoustovsk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 360
    The city is norsup
    https://api.openweathermap.org/data/2.5/weather?q=norsup&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 361
    The city is bargal
    https://api.openweathermap.org/data/2.5/weather?q=bargal&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 362
    The city is qui nhon
    https://api.openweathermap.org/data/2.5/weather?q=qui nhon&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 363
    The city is anamur
    https://api.openweathermap.org/data/2.5/weather?q=anamur&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 364
    The city is kondopoga
    https://api.openweathermap.org/data/2.5/weather?q=kondopoga&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 365
    The city is saint joseph
    https://api.openweathermap.org/data/2.5/weather?q=saint joseph&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 366
    The city is loudi
    https://api.openweathermap.org/data/2.5/weather?q=loudi&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 367
    The city is sabha
    https://api.openweathermap.org/data/2.5/weather?q=sabha&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 368
    The city is turkan
    https://api.openweathermap.org/data/2.5/weather?q=turkan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 369
    The city is tunduru
    https://api.openweathermap.org/data/2.5/weather?q=tunduru&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 370
    The city is sola
    https://api.openweathermap.org/data/2.5/weather?q=sola&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 371
    The city is baillif
    https://api.openweathermap.org/data/2.5/weather?q=baillif&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 372
    The city is liverpool
    https://api.openweathermap.org/data/2.5/weather?q=liverpool&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 373
    The city is butte
    https://api.openweathermap.org/data/2.5/weather?q=butte&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 374
    The city is tabas
    https://api.openweathermap.org/data/2.5/weather?q=tabas&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 375
    The city is saint-augustin
    https://api.openweathermap.org/data/2.5/weather?q=saint-augustin&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 376
    The city is champerico
    https://api.openweathermap.org/data/2.5/weather?q=champerico&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 377
    The city is redcar
    https://api.openweathermap.org/data/2.5/weather?q=redcar&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 378
    The city is adrar
    https://api.openweathermap.org/data/2.5/weather?q=adrar&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 379
    The city is cidreira
    https://api.openweathermap.org/data/2.5/weather?q=cidreira&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 380
    The city is sarkand
    https://api.openweathermap.org/data/2.5/weather?q=sarkand&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 381
    The city is rungata
    https://api.openweathermap.org/data/2.5/weather?q=rungata&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 382
    The city is coihaique
    https://api.openweathermap.org/data/2.5/weather?q=coihaique&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 383
    The city is sambava
    https://api.openweathermap.org/data/2.5/weather?q=sambava&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 384
    The city is moindou
    https://api.openweathermap.org/data/2.5/weather?q=moindou&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 385
    The city is chifeng
    https://api.openweathermap.org/data/2.5/weather?q=chifeng&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 386
    The city is moba
    https://api.openweathermap.org/data/2.5/weather?q=moba&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 387
    The city is camacha
    https://api.openweathermap.org/data/2.5/weather?q=camacha&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 388
    The city is takoradi
    https://api.openweathermap.org/data/2.5/weather?q=takoradi&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 389
    The city is weinan
    https://api.openweathermap.org/data/2.5/weather?q=weinan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 390
    The city is fort oglethorpe
    https://api.openweathermap.org/data/2.5/weather?q=fort oglethorpe&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 391
    The city is saquena
    https://api.openweathermap.org/data/2.5/weather?q=saquena&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 392
    The city is nanortalik
    https://api.openweathermap.org/data/2.5/weather?q=nanortalik&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 393
    The city is yokadouma
    https://api.openweathermap.org/data/2.5/weather?q=yokadouma&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 394
    The city is karatau
    https://api.openweathermap.org/data/2.5/weather?q=karatau&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 395
    The city is assiniboia
    https://api.openweathermap.org/data/2.5/weather?q=assiniboia&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 396
    The city is maragogi
    https://api.openweathermap.org/data/2.5/weather?q=maragogi&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 397
    The city is neuquen
    https://api.openweathermap.org/data/2.5/weather?q=neuquen&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 398
    The city is oeiras
    https://api.openweathermap.org/data/2.5/weather?q=oeiras&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 399
    The city is lagoa
    https://api.openweathermap.org/data/2.5/weather?q=lagoa&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 400
    The city is macaubas
    https://api.openweathermap.org/data/2.5/weather?q=macaubas&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 401
    The city is nadapuram
    https://api.openweathermap.org/data/2.5/weather?q=nadapuram&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 402
    The city is kibara
    https://api.openweathermap.org/data/2.5/weather?q=kibara&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 403
    The city is teahupoo
    https://api.openweathermap.org/data/2.5/weather?q=teahupoo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 404
    The city is lorengau
    https://api.openweathermap.org/data/2.5/weather?q=lorengau&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 405
    The city is aleksandrov gay
    https://api.openweathermap.org/data/2.5/weather?q=aleksandrov gay&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 406
    The city is marfino
    https://api.openweathermap.org/data/2.5/weather?q=marfino&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 407
    The city is ayr
    https://api.openweathermap.org/data/2.5/weather?q=ayr&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 408
    The city is jinzhou
    https://api.openweathermap.org/data/2.5/weather?q=jinzhou&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 409
    The city is broken hill
    https://api.openweathermap.org/data/2.5/weather?q=broken hill&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 410
    The city is brae
    https://api.openweathermap.org/data/2.5/weather?q=brae&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 411
    The city is cortez
    https://api.openweathermap.org/data/2.5/weather?q=cortez&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 412
    The city is cumberland
    https://api.openweathermap.org/data/2.5/weather?q=cumberland&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 413
    The city is azangaro
    https://api.openweathermap.org/data/2.5/weather?q=azangaro&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 414
    The city is wahran
    https://api.openweathermap.org/data/2.5/weather?q=wahran&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 415
    The city is vila velha
    https://api.openweathermap.org/data/2.5/weather?q=vila velha&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 416
    The city is henties bay
    https://api.openweathermap.org/data/2.5/weather?q=henties bay&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 417
    The city is usinsk
    https://api.openweathermap.org/data/2.5/weather?q=usinsk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 418
    The city is banda aceh
    https://api.openweathermap.org/data/2.5/weather?q=banda aceh&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 419
    The city is bud
    https://api.openweathermap.org/data/2.5/weather?q=bud&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 420
    The city is burnie
    https://api.openweathermap.org/data/2.5/weather?q=burnie&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 421
    The city is kudahuvadhoo
    https://api.openweathermap.org/data/2.5/weather?q=kudahuvadhoo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 422
    The city is kamakwie
    https://api.openweathermap.org/data/2.5/weather?q=kamakwie&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 423
    The city is luderitz
    https://api.openweathermap.org/data/2.5/weather?q=luderitz&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 424
    The city is tambovka
    https://api.openweathermap.org/data/2.5/weather?q=tambovka&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 425
    The city is wanning
    https://api.openweathermap.org/data/2.5/weather?q=wanning&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 426
    The city is duz
    https://api.openweathermap.org/data/2.5/weather?q=duz&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 427
    The city is san carlos de bariloche
    https://api.openweathermap.org/data/2.5/weather?q=san carlos de bariloche&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 428
    The city is playas
    https://api.openweathermap.org/data/2.5/weather?q=playas&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 429
    The city is puerto rico
    https://api.openweathermap.org/data/2.5/weather?q=puerto rico&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 430
    The city is pangody
    https://api.openweathermap.org/data/2.5/weather?q=pangody&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 431
    The city is korla
    https://api.openweathermap.org/data/2.5/weather?q=korla&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 432
    The city is parelhas
    https://api.openweathermap.org/data/2.5/weather?q=parelhas&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 433
    The city is huarmey
    https://api.openweathermap.org/data/2.5/weather?q=huarmey&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 434
    The city is dharchula
    https://api.openweathermap.org/data/2.5/weather?q=dharchula&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 435
    The city is sorland
    https://api.openweathermap.org/data/2.5/weather?q=sorland&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 436
    The city is yulara
    https://api.openweathermap.org/data/2.5/weather?q=yulara&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 437
    The city is ruwi
    https://api.openweathermap.org/data/2.5/weather?q=ruwi&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 438
    The city is roebourne
    https://api.openweathermap.org/data/2.5/weather?q=roebourne&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 439
    The city is libreville
    https://api.openweathermap.org/data/2.5/weather?q=libreville&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 440
    The city is kharan
    https://api.openweathermap.org/data/2.5/weather?q=kharan&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 441
    The city is champagnole
    https://api.openweathermap.org/data/2.5/weather?q=champagnole&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 442
    The city is nelson bay
    https://api.openweathermap.org/data/2.5/weather?q=nelson bay&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 443
    The city is caravelas
    https://api.openweathermap.org/data/2.5/weather?q=caravelas&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 444
    The city is maykain
    https://api.openweathermap.org/data/2.5/weather?q=maykain&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 445
    The city is yerbogachen
    https://api.openweathermap.org/data/2.5/weather?q=yerbogachen&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 446
    The city is sinaloa
    https://api.openweathermap.org/data/2.5/weather?q=sinaloa&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 447
    The city is svobodnyy
    https://api.openweathermap.org/data/2.5/weather?q=svobodnyy&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 448
    The city is biak
    https://api.openweathermap.org/data/2.5/weather?q=biak&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 449
    The city is verkhnevilyuysk
    https://api.openweathermap.org/data/2.5/weather?q=verkhnevilyuysk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 450
    The city is west wendover
    https://api.openweathermap.org/data/2.5/weather?q=west wendover&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 451
    The city is plouzane
    https://api.openweathermap.org/data/2.5/weather?q=plouzane&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 452
    The city is banjar
    https://api.openweathermap.org/data/2.5/weather?q=banjar&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 453
    The city is buala
    https://api.openweathermap.org/data/2.5/weather?q=buala&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 454
    The city is shenjiamen
    https://api.openweathermap.org/data/2.5/weather?q=shenjiamen&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 455
    The city is saint-joseph
    https://api.openweathermap.org/data/2.5/weather?q=saint-joseph&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 456
    The city is sorvag
    https://api.openweathermap.org/data/2.5/weather?q=sorvag&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 457
    The city is mildura
    https://api.openweathermap.org/data/2.5/weather?q=mildura&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 458
    The city is santa lucia
    https://api.openweathermap.org/data/2.5/weather?q=santa lucia&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 459
    The city is dali
    https://api.openweathermap.org/data/2.5/weather?q=dali&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 460
    The city is maracaibo
    https://api.openweathermap.org/data/2.5/weather?q=maracaibo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 461
    The city is nancha
    https://api.openweathermap.org/data/2.5/weather?q=nancha&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 462
    The city is itarema
    https://api.openweathermap.org/data/2.5/weather?q=itarema&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 463
    The city is ruatoria
    https://api.openweathermap.org/data/2.5/weather?q=ruatoria&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 464
    The city is sokoni
    https://api.openweathermap.org/data/2.5/weather?q=sokoni&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 465
    The city is alice springs
    https://api.openweathermap.org/data/2.5/weather?q=alice springs&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 466
    The city is kuah
    https://api.openweathermap.org/data/2.5/weather?q=kuah&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 467
    The city is galbshtadt
    https://api.openweathermap.org/data/2.5/weather?q=galbshtadt&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 468
    The city is roald
    https://api.openweathermap.org/data/2.5/weather?q=roald&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 469
    The city is kavaratti
    https://api.openweathermap.org/data/2.5/weather?q=kavaratti&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 470
    The city is olafsvik
    https://api.openweathermap.org/data/2.5/weather?q=olafsvik&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 471
    The city is maumere
    https://api.openweathermap.org/data/2.5/weather?q=maumere&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 472
    The city is cockburn town
    https://api.openweathermap.org/data/2.5/weather?q=cockburn town&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 473
    The city is tezu
    https://api.openweathermap.org/data/2.5/weather?q=tezu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 474
    The city is bawku
    https://api.openweathermap.org/data/2.5/weather?q=bawku&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 475
    The city is sibanicu
    https://api.openweathermap.org/data/2.5/weather?q=sibanicu&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 476
    The city is marsala
    https://api.openweathermap.org/data/2.5/weather?q=marsala&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 477
    The city is clyde river
    https://api.openweathermap.org/data/2.5/weather?q=clyde river&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 478
    The city is san cosme y damian
    https://api.openweathermap.org/data/2.5/weather?q=san cosme y damian&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 479
    The city is oromocto
    https://api.openweathermap.org/data/2.5/weather?q=oromocto&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 480
    The city is speightstown
    https://api.openweathermap.org/data/2.5/weather?q=speightstown&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 481
    The city is azrow
    https://api.openweathermap.org/data/2.5/weather?q=azrow&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 482
    The city is vangaindrano
    https://api.openweathermap.org/data/2.5/weather?q=vangaindrano&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 483
    The city is linxia
    https://api.openweathermap.org/data/2.5/weather?q=linxia&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 484
    The city is santa cruz
    https://api.openweathermap.org/data/2.5/weather?q=santa cruz&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 485
    The city is konongo
    https://api.openweathermap.org/data/2.5/weather?q=konongo&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 486
    The city is comodoro rivadavia
    https://api.openweathermap.org/data/2.5/weather?q=comodoro rivadavia&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 487
    The city is klaksvik
    https://api.openweathermap.org/data/2.5/weather?q=klaksvik&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 488
    The city is vilyuysk
    https://api.openweathermap.org/data/2.5/weather?q=vilyuysk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 489
    The city is grand island
    https://api.openweathermap.org/data/2.5/weather?q=grand island&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 490
    The city is kasama
    https://api.openweathermap.org/data/2.5/weather?q=kasama&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 491
    The city is samarai
    https://api.openweathermap.org/data/2.5/weather?q=samarai&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 492
    The city is mochicahui
    https://api.openweathermap.org/data/2.5/weather?q=mochicahui&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 493
    The city is riohacha
    https://api.openweathermap.org/data/2.5/weather?q=riohacha&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 494
    The city is aquiraz
    https://api.openweathermap.org/data/2.5/weather?q=aquiraz&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 495
    The city is inta
    https://api.openweathermap.org/data/2.5/weather?q=inta&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 496
    The city is srednekolymsk
    https://api.openweathermap.org/data/2.5/weather?q=srednekolymsk&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 497
    The city is bandarbeyla
    https://api.openweathermap.org/data/2.5/weather?q=bandarbeyla&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 498
    The city is murray bridge
    https://api.openweathermap.org/data/2.5/weather?q=murray bridge&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________
    Now Processing Record 499
    The city is porto velho
    https://api.openweathermap.org/data/2.5/weather?q=porto velho&appid=25bc90a1196e6f153eece0bc0b0fc9eb&units=imperial
    __________________________________________________________________________________________



```python
new_data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lng</th>
      <th>City</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>90.704185</td>
      <td>-12.237284</td>
      <td>illoqqortoormiut</td>
      <td>59.59</td>
      <td>82</td>
      <td>0</td>
      <td>4.70</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.842379</td>
      <td>-64.942033</td>
      <td>santa isabel do rio negro</td>
      <td>76.96</td>
      <td>90</td>
      <td>80</td>
      <td>3.94</td>
    </tr>
    <tr>
      <th>2</th>
      <td>62.401443</td>
      <td>-38.909656</td>
      <td>tasiilaq</td>
      <td>41.00</td>
      <td>86</td>
      <td>80</td>
      <td>12.75</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.912685</td>
      <td>18.473937</td>
      <td>mbandaka</td>
      <td>73.18</td>
      <td>98</td>
      <td>32</td>
      <td>2.04</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20.332541</td>
      <td>143.497201</td>
      <td>shimoda</td>
      <td>77.85</td>
      <td>74</td>
      <td>40</td>
      <td>23.04</td>
    </tr>
  </tbody>
</table>
</div>



## Latitude vs. Temperature Plot

-----------------_


```python
plt.scatter(new_data["Lat"], new_data["Max Temp"], marker ="o")
plt.title("City Latitude vs. Max Temperature")
plt.xlabel("Latitude")
plt.ylabel("Max Temperature (F)")
sns.set()
plt.savefig("lat_temp.png")
plt.show()
```


![png](output_12_0.png)


## Latitude vs. Humidity Plot

----------------


```python
plt.scatter(new_data["Lat"], new_data["Humidity"], marker ="o")
plt.title("City Latitude vs. Humidity")
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")
sns.set()
plt.savefig("lat_humidity.png")
plt.show()
```


![png](output_14_0.png)


## Latitude vs. Cloudiness Plot

-------------


```python
plt.scatter(new_data["Lat"], new_data["Clouds"], marker ="o")
plt.title("City Latitude vs. Cloudiness")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
sns.set()
plt.savefig("lat_clouds.png")
plt.show()
```


![png](output_16_0.png)


## Latitude vs. Wind Speed Plot

-----------


```python
plt.scatter(new_data["Lat"], new_data["Wind Speed"], marker ="o")
plt.title("City Latitude vs. Wind Speed")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (mph)")
sns.set()
plt.savefig("lat_wind.png")
plt.show()
```


![png](output_18_0.png)



```python

```
