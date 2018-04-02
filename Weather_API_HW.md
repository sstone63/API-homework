

```python
#import dependencies 
import random
import pandas as pd
from citipy import citipy
import requests
import matplotlib.pyplot as plt
from api_keys import weather_api

#create empty lists

latitude = []
longitude = []
city_name = []
country_code = []
count = 0

#generate random latitude, longitude, and city values

while count < 600:
    lat = round((random.randint(-90, 90)+random.random()),7)
    lng = round((random.randint(-180, 180)+random.random()),7)
    city = citipy.nearest_city(lat, lng).city_name
    country = citipy.nearest_city(lat, lng).country_code

    if city not in city_name:
        
        city_name.append(city)
        latitude.append(lat)
        longitude.append(lng)
        country_code.append(country)
        count+=1
        
#place list values into dataframe
    
city_df = pd.DataFrame({
    "City": city_name,
    "Country": country_code,
    "Latitude": latitude, 
    "Longitude": longitude
    })  

city_df.head(20)
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
      <th>City</th>
      <th>Country</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>taolanaro</td>
      <td>mg</td>
      <td>-54.834351</td>
      <td>55.925823</td>
    </tr>
    <tr>
      <th>1</th>
      <td>hay river</td>
      <td>ca</td>
      <td>60.242743</td>
      <td>-114.867577</td>
    </tr>
    <tr>
      <th>2</th>
      <td>east london</td>
      <td>za</td>
      <td>-42.159847</td>
      <td>35.521581</td>
    </tr>
    <tr>
      <th>3</th>
      <td>busselton</td>
      <td>au</td>
      <td>-57.928855</td>
      <td>84.321859</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ushuaia</td>
      <td>ar</td>
      <td>-58.977452</td>
      <td>-54.078239</td>
    </tr>
    <tr>
      <th>5</th>
      <td>mount isa</td>
      <td>au</td>
      <td>-17.686491</td>
      <td>141.631644</td>
    </tr>
    <tr>
      <th>6</th>
      <td>vaini</td>
      <td>to</td>
      <td>-55.986610</td>
      <td>-168.762286</td>
    </tr>
    <tr>
      <th>7</th>
      <td>turayf</td>
      <td>sa</td>
      <td>30.299511</td>
      <td>38.350086</td>
    </tr>
    <tr>
      <th>8</th>
      <td>vila velha</td>
      <td>br</td>
      <td>-24.287043</td>
      <td>-31.512798</td>
    </tr>
    <tr>
      <th>9</th>
      <td>wanning</td>
      <td>cn</td>
      <td>18.330063</td>
      <td>111.755520</td>
    </tr>
    <tr>
      <th>10</th>
      <td>hilo</td>
      <td>us</td>
      <td>8.684600</td>
      <td>-160.794041</td>
    </tr>
    <tr>
      <th>11</th>
      <td>chokurdakh</td>
      <td>ru</td>
      <td>81.440747</td>
      <td>148.139111</td>
    </tr>
    <tr>
      <th>12</th>
      <td>hermanus</td>
      <td>za</td>
      <td>-74.470894</td>
      <td>2.052740</td>
    </tr>
    <tr>
      <th>13</th>
      <td>mataura</td>
      <td>pf</td>
      <td>-28.820677</td>
      <td>-151.490236</td>
    </tr>
    <tr>
      <th>14</th>
      <td>port alfred</td>
      <td>za</td>
      <td>-77.555484</td>
      <td>51.496544</td>
    </tr>
    <tr>
      <th>15</th>
      <td>udachnyy</td>
      <td>ru</td>
      <td>68.775283</td>
      <td>113.464728</td>
    </tr>
    <tr>
      <th>16</th>
      <td>illoqqortoormiut</td>
      <td>gl</td>
      <td>83.336680</td>
      <td>-29.030990</td>
    </tr>
    <tr>
      <th>17</th>
      <td>ballangen</td>
      <td>no</td>
      <td>67.665586</td>
      <td>17.027465</td>
    </tr>
    <tr>
      <th>18</th>
      <td>kodiak</td>
      <td>us</td>
      <td>53.756937</td>
      <td>-154.570412</td>
    </tr>
    <tr>
      <th>19</th>
      <td>kapoeta</td>
      <td>sd</td>
      <td>4.837192</td>
      <td>33.928207</td>
    </tr>
  </tbody>
</table>
</div>




```python
#generate url links
count = 0
print("Commencing Data Retrieval")
print("--------------------------")

#iterate through dataframe
for index,row in city_df.iterrows():
    base_url = "http://api.openweathermap.org/data/2.5/weather?"
    
    city = row["City"]
    country = row["Country"]
    city_search = f"{city},{country}"
    
    city_weather = requests.get(base_url + "appid=" + weather_api + "&q=" + city_search + "&units=imperial")
    city_weather_json = city_weather.json()
    print("Now fetching city " + str(count) + " of " + str(len(city_df)) + ", " + f"{city}")
    print(city_weather.url)
    #increase count by 1 after every url link is generated
    count += 1
    
    
    #generate column values based on json key returns
    try: 
        city_temp = city_weather_json["main"]["temp_max"]
        city_humidity = city_weather_json["main"]["humidity"]
        city_clouds = city_weather_json["clouds"]["all"]
        city_wind_speed = city_weather_json["wind"]["speed"]
        
        city_df.set_value(index, "Temperature", city_temp)
        city_df.set_value(index, "Humidity", city_humidity)
        city_df.set_value(index, "Cloud Cover %", city_clouds)
        city_df.set_value(index, "Wind Speed (MPH)", city_wind_speed)
        
    
    #error exceptions for missing values
    except (KeyError, IndexError):
        print("Error with city data. Skipping...")
        
print("--------------------------")
print("Data Retrieval Complete")
print("--------------------------")


```

    Commencing Data Retrieval
    --------------------------
    Now fetching city 0 of 600, taolanaro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=taolanaro,mg&units=imperial
    Error with city data. Skipping...
    Now fetching city 1 of 600, hay river
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hay%20river,ca&units=imperial
    Now fetching city 2 of 600, east london
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=east%20london,za&units=imperial
    Now fetching city 3 of 600, busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=busselton,au&units=imperial
    Now fetching city 4 of 600, ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ushuaia,ar&units=imperial
    Now fetching city 5 of 600, mount isa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mount%20isa,au&units=imperial
    Now fetching city 6 of 600, vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vaini,to&units=imperial
    Now fetching city 7 of 600, turayf
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=turayf,sa&units=imperial
    Now fetching city 8 of 600, vila velha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vila%20velha,br&units=imperial
    Now fetching city 9 of 600, wanning
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=wanning,cn&units=imperial
    Now fetching city 10 of 600, hilo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hilo,us&units=imperial
    Now fetching city 11 of 600, chokurdakh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chokurdakh,ru&units=imperial
    Now fetching city 12 of 600, hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hermanus,za&units=imperial
    Now fetching city 13 of 600, mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mataura,pf&units=imperial
    Error with city data. Skipping...
    Now fetching city 14 of 600, port alfred
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20alfred,za&units=imperial
    Now fetching city 15 of 600, udachnyy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=udachnyy,ru&units=imperial
    Now fetching city 16 of 600, illoqqortoormiut
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=illoqqortoormiut,gl&units=imperial
    Error with city data. Skipping...
    Now fetching city 17 of 600, ballangen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ballangen,no&units=imperial
    Now fetching city 18 of 600, kodiak
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kodiak,us&units=imperial
    Now fetching city 19 of 600, kapoeta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kapoeta,sd&units=imperial
    Error with city data. Skipping...
    Now fetching city 20 of 600, saint george
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint%20george,bm&units=imperial
    Now fetching city 21 of 600, saint-philippe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-philippe,re&units=imperial
    Now fetching city 22 of 600, hobart
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hobart,au&units=imperial
    Now fetching city 23 of 600, katsuura
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=katsuura,jp&units=imperial
    Now fetching city 24 of 600, butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=butaritari,ki&units=imperial
    Now fetching city 25 of 600, yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yellowknife,ca&units=imperial
    Now fetching city 26 of 600, barentsburg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barentsburg,sj&units=imperial
    Error with city data. Skipping...
    Now fetching city 27 of 600, dunedin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dunedin,nz&units=imperial
    Now fetching city 28 of 600, bredasdorp
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bredasdorp,za&units=imperial
    Now fetching city 29 of 600, kaitangata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kaitangata,nz&units=imperial
    Now fetching city 30 of 600, georgetown
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=georgetown,sh&units=imperial
    Now fetching city 31 of 600, carsamba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=carsamba,tr&units=imperial
    Now fetching city 32 of 600, souillac
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=souillac,mu&units=imperial
    Now fetching city 33 of 600, hithadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hithadhoo,mv&units=imperial
    Now fetching city 34 of 600, narsaq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=narsaq,gl&units=imperial
    Now fetching city 35 of 600, atherton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=atherton,au&units=imperial
    Now fetching city 36 of 600, bluff
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bluff,nz&units=imperial
    Now fetching city 37 of 600, magadan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=magadan,ru&units=imperial
    Now fetching city 38 of 600, tomball
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tomball,us&units=imperial
    Now fetching city 39 of 600, punta arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=punta%20arenas,cl&units=imperial
    Now fetching city 40 of 600, mar del plata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mar%20del%20plata,ar&units=imperial
    Now fetching city 41 of 600, vestmannaeyjar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vestmannaeyjar,is&units=imperial
    Now fetching city 42 of 600, longyearbyen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=longyearbyen,sj&units=imperial
    Now fetching city 43 of 600, aitape
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aitape,pg&units=imperial
    Now fetching city 44 of 600, beloeil
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=beloeil,ca&units=imperial
    Now fetching city 45 of 600, vryburg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vryburg,za&units=imperial
    Now fetching city 46 of 600, kumba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kumba,cm&units=imperial
    Now fetching city 47 of 600, laguna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=laguna,br&units=imperial
    Error with city data. Skipping...
    Now fetching city 48 of 600, port elizabeth
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20elizabeth,za&units=imperial
    Now fetching city 49 of 600, nikolskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nikolskoye,ru&units=imperial
    Now fetching city 50 of 600, saldanha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saldanha,za&units=imperial
    Now fetching city 51 of 600, basco
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=basco,ph&units=imperial
    Now fetching city 52 of 600, hualmay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hualmay,pe&units=imperial
    Now fetching city 53 of 600, tarakan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tarakan,id&units=imperial
    Now fetching city 54 of 600, port moresby
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20moresby,pg&units=imperial
    Now fetching city 55 of 600, mantua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mantua,cu&units=imperial
    Now fetching city 56 of 600, draguignan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=draguignan,fr&units=imperial
    Now fetching city 57 of 600, laibin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=laibin,cn&units=imperial
    Now fetching city 58 of 600, desbiens
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=desbiens,ca&units=imperial
    Now fetching city 59 of 600, cape town
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cape%20town,za&units=imperial
    Now fetching city 60 of 600, riverton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=riverton,nz&units=imperial
    Now fetching city 61 of 600, roma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=roma,au&units=imperial
    Now fetching city 62 of 600, san patricio
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20patricio,mx&units=imperial
    Now fetching city 63 of 600, skelleftea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=skelleftea,se&units=imperial
    Now fetching city 64 of 600, inhambane
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=inhambane,mz&units=imperial
    Now fetching city 65 of 600, thompson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=thompson,ca&units=imperial
    Now fetching city 66 of 600, faanui
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=faanui,pf&units=imperial
    Now fetching city 67 of 600, saint-pierre
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-pierre,pm&units=imperial
    Now fetching city 68 of 600, myskhako
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=myskhako,ru&units=imperial
    Now fetching city 69 of 600, albany
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=albany,au&units=imperial
    Now fetching city 70 of 600, buenos aires
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=buenos%20aires,cr&units=imperial
    Now fetching city 71 of 600, rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rikitea,pf&units=imperial
    Now fetching city 72 of 600, ilinskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ilinskiy,ru&units=imperial
    Now fetching city 73 of 600, carnarvon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=carnarvon,au&units=imperial
    Now fetching city 74 of 600, lolua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lolua,tv&units=imperial
    Error with city data. Skipping...
    Now fetching city 75 of 600, bilibino
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bilibino,ru&units=imperial
    Now fetching city 76 of 600, aksarka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aksarka,ru&units=imperial
    Now fetching city 77 of 600, new norfolk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=new%20norfolk,au&units=imperial
    Now fetching city 78 of 600, luderitz
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=luderitz,na&units=imperial
    Now fetching city 79 of 600, morros
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=morros,br&units=imperial
    Now fetching city 80 of 600, arraial do cabo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=arraial%20do%20cabo,br&units=imperial
    Now fetching city 81 of 600, las conchas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=las%20conchas,hn&units=imperial
    Error with city data. Skipping...
    Now fetching city 82 of 600, nepomuk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nepomuk,cz&units=imperial
    Now fetching city 83 of 600, opuwo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=opuwo,na&units=imperial
    Now fetching city 84 of 600, seoul
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=seoul,kr&units=imperial
    Now fetching city 85 of 600, codrington
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=codrington,ag&units=imperial
    Error with city data. Skipping...
    Now fetching city 86 of 600, tautira
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tautira,pf&units=imperial
    Now fetching city 87 of 600, banda aceh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=banda%20aceh,id&units=imperial
    Now fetching city 88 of 600, mayumba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mayumba,ga&units=imperial
    Now fetching city 89 of 600, shenjiamen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shenjiamen,cn&units=imperial
    Now fetching city 90 of 600, kruisfontein
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kruisfontein,za&units=imperial
    Now fetching city 91 of 600, staraya toropa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=staraya%20toropa,ru&units=imperial
    Now fetching city 92 of 600, waingapu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=waingapu,id&units=imperial
    Now fetching city 93 of 600, betsiamites
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=betsiamites,ca&units=imperial
    Now fetching city 94 of 600, amderma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=amderma,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 95 of 600, nizhneyansk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nizhneyansk,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 96 of 600, port lincoln
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20lincoln,au&units=imperial
    Now fetching city 97 of 600, marovoay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marovoay,mg&units=imperial
    Now fetching city 98 of 600, fare
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fare,pf&units=imperial
    Now fetching city 99 of 600, pevek
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pevek,ru&units=imperial
    Now fetching city 100 of 600, meyungs
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=meyungs,pw&units=imperial
    Error with city data. Skipping...
    Now fetching city 101 of 600, kidal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kidal,ml&units=imperial
    Now fetching city 102 of 600, nizhniy kuranakh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nizhniy%20kuranakh,ru&units=imperial
    Now fetching city 103 of 600, moroni
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=moroni,km&units=imperial
    Now fetching city 104 of 600, yumen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yumen,cn&units=imperial
    Now fetching city 105 of 600, san cristobal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20cristobal,ec&units=imperial
    Now fetching city 106 of 600, dharchula
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dharchula,in&units=imperial
    Now fetching city 107 of 600, takoradi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=takoradi,gh&units=imperial
    Now fetching city 108 of 600, dikson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dikson,ru&units=imperial
    Now fetching city 109 of 600, tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tasiilaq,gl&units=imperial
    Now fetching city 110 of 600, mutsu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mutsu,jp&units=imperial
    Now fetching city 111 of 600, chuy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chuy,uy&units=imperial
    Now fetching city 112 of 600, khatanga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=khatanga,ru&units=imperial
    Now fetching city 113 of 600, jamestown
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jamestown,sh&units=imperial
    Now fetching city 114 of 600, norman wells
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=norman%20wells,ca&units=imperial
    Now fetching city 115 of 600, bonavista
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bonavista,ca&units=imperial
    Now fetching city 116 of 600, saint-francois
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-francois,gp&units=imperial
    Now fetching city 117 of 600, airai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=airai,pw&units=imperial
    Error with city data. Skipping...
    Now fetching city 118 of 600, burica
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=burica,pa&units=imperial
    Error with city data. Skipping...
    Now fetching city 119 of 600, sault sainte marie
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sault%20sainte%20marie,ca&units=imperial
    Now fetching city 120 of 600, kaohsiung
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kaohsiung,tw&units=imperial
    Now fetching city 121 of 600, fairbanks
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fairbanks,us&units=imperial
    Now fetching city 122 of 600, upernavik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=upernavik,gl&units=imperial
    Now fetching city 123 of 600, kavieng
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kavieng,pg&units=imperial
    Now fetching city 124 of 600, samusu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=samusu,ws&units=imperial
    Error with city data. Skipping...
    Now fetching city 125 of 600, castro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=castro,cl&units=imperial
    Now fetching city 126 of 600, tuktoyaktuk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tuktoyaktuk,ca&units=imperial
    Now fetching city 127 of 600, otjimbingwe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=otjimbingwe,na&units=imperial
    Now fetching city 128 of 600, cidreira
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cidreira,br&units=imperial
    Now fetching city 129 of 600, timra
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=timra,se&units=imperial
    Now fetching city 130 of 600, atuona
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=atuona,pf&units=imperial
    Now fetching city 131 of 600, tiksi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tiksi,ru&units=imperial
    Now fetching city 132 of 600, attawapiskat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=attawapiskat,ca&units=imperial
    Error with city data. Skipping...
    Now fetching city 133 of 600, salalah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=salalah,om&units=imperial
    Now fetching city 134 of 600, riviere-du-loup
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=riviere-du-loup,ca&units=imperial
    Now fetching city 135 of 600, kotra
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kotra,in&units=imperial
    Now fetching city 136 of 600, jalu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jalu,ly&units=imperial
    Now fetching city 137 of 600, puerto ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=puerto%20ayora,ec&units=imperial
    Now fetching city 138 of 600, ketchikan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ketchikan,us&units=imperial
    Now fetching city 139 of 600, tadine
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tadine,nc&units=imperial
    Now fetching city 140 of 600, bom jesus
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bom%20jesus,br&units=imperial
    Now fetching city 141 of 600, vila franca do campo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vila%20franca%20do%20campo,pt&units=imperial
    Now fetching city 142 of 600, lorengau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lorengau,pg&units=imperial
    Now fetching city 143 of 600, ewo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ewo,cg&units=imperial
    Now fetching city 144 of 600, mackay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mackay,au&units=imperial
    Now fetching city 145 of 600, dondo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dondo,mz&units=imperial
    Now fetching city 146 of 600, bowen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bowen,au&units=imperial
    Now fetching city 147 of 600, mucurapo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mucurapo,tt&units=imperial
    Now fetching city 148 of 600, the valley
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=the%20valley,ai&units=imperial
    Now fetching city 149 of 600, mahebourg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mahebourg,mu&units=imperial
    Now fetching city 150 of 600, saskylakh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saskylakh,ru&units=imperial
    Now fetching city 151 of 600, aykhal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aykhal,ru&units=imperial
    Now fetching city 152 of 600, diphu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=diphu,in&units=imperial
    Now fetching city 153 of 600, port augusta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20augusta,au&units=imperial
    Now fetching city 154 of 600, port-gentil
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port-gentil,ga&units=imperial
    Now fetching city 155 of 600, tigil
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tigil,ru&units=imperial
    Now fetching city 156 of 600, qaanaaq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=qaanaaq,gl&units=imperial
    Now fetching city 157 of 600, wakkanai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=wakkanai,jp&units=imperial
    Now fetching city 158 of 600, esperance
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=esperance,au&units=imperial
    Now fetching city 159 of 600, skibbereen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=skibbereen,ie&units=imperial
    Now fetching city 160 of 600, puerto penasco
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=puerto%20penasco,mx&units=imperial
    Now fetching city 161 of 600, caravelas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=caravelas,br&units=imperial
    Now fetching city 162 of 600, necochea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=necochea,ar&units=imperial
    Now fetching city 163 of 600, bacolod
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bacolod,ph&units=imperial
    Now fetching city 164 of 600, bur gabo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bur%20gabo,so&units=imperial
    Error with city data. Skipping...
    Now fetching city 165 of 600, coihaique
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=coihaique,cl&units=imperial
    Now fetching city 166 of 600, houma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=houma,us&units=imperial
    Now fetching city 167 of 600, kirakira
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kirakira,sb&units=imperial
    Now fetching city 168 of 600, severo-kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=severo-kurilsk,ru&units=imperial
    Now fetching city 169 of 600, ola
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ola,ru&units=imperial
    Now fetching city 170 of 600, kerki
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kerki,tm&units=imperial
    Error with city data. Skipping...
    Now fetching city 171 of 600, sentyabrskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sentyabrskiy,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 172 of 600, ancud
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ancud,cl&units=imperial
    Now fetching city 173 of 600, pokanayevka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pokanayevka,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 174 of 600, ojinaga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ojinaga,mx&units=imperial
    Now fetching city 175 of 600, torbay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=torbay,ca&units=imperial
    Now fetching city 176 of 600, hayden
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hayden,us&units=imperial
    Now fetching city 177 of 600, klaksvik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=klaksvik,fo&units=imperial
    Now fetching city 178 of 600, tambovka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tambovka,ru&units=imperial
    Now fetching city 179 of 600, pisco
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pisco,pe&units=imperial
    Now fetching city 180 of 600, barrow
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barrow,us&units=imperial
    Now fetching city 181 of 600, kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kapaa,us&units=imperial
    Now fetching city 182 of 600, cherrapunji
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cherrapunji,in&units=imperial
    Now fetching city 183 of 600, kismayo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kismayo,so&units=imperial
    Error with city data. Skipping...
    Now fetching city 184 of 600, sisimiut
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sisimiut,gl&units=imperial
    Now fetching city 185 of 600, victoria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=victoria,sc&units=imperial
    Now fetching city 186 of 600, pangnirtung
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pangnirtung,ca&units=imperial
    Now fetching city 187 of 600, zhezkazgan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zhezkazgan,kz&units=imperial
    Now fetching city 188 of 600, samarai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=samarai,pg&units=imperial
    Now fetching city 189 of 600, pimenta bueno
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pimenta%20bueno,br&units=imperial
    Now fetching city 190 of 600, swinoujscie
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=swinoujscie,pl&units=imperial
    Now fetching city 191 of 600, nome
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nome,us&units=imperial
    Now fetching city 192 of 600, ustka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ustka,pl&units=imperial
    Now fetching city 193 of 600, silvania
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=silvania,br&units=imperial
    Now fetching city 194 of 600, honiara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=honiara,sb&units=imperial
    Now fetching city 195 of 600, myre
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=myre,no&units=imperial
    Now fetching city 196 of 600, perur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=perur,in&units=imperial
    Now fetching city 197 of 600, cayenne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cayenne,gf&units=imperial
    Now fetching city 198 of 600, tumannyy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tumannyy,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 199 of 600, grand river south east
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grand%20river%20south%20east,mu&units=imperial
    Error with city data. Skipping...
    Now fetching city 200 of 600, kahului
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kahului,us&units=imperial
    Now fetching city 201 of 600, lazaro cardenas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lazaro%20cardenas,mx&units=imperial
    Now fetching city 202 of 600, port hardy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20hardy,ca&units=imperial
    Now fetching city 203 of 600, camiri
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=camiri,bo&units=imperial
    Now fetching city 204 of 600, faya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=faya,td&units=imperial
    Error with city data. Skipping...
    Now fetching city 205 of 600, matagami
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=matagami,ca&units=imperial
    Now fetching city 206 of 600, xining
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=xining,cn&units=imperial
    Now fetching city 207 of 600, yicheng
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yicheng,cn&units=imperial
    Now fetching city 208 of 600, buraydah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=buraydah,sa&units=imperial
    Now fetching city 209 of 600, margate
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=margate,za&units=imperial
    Now fetching city 210 of 600, lebu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lebu,cl&units=imperial
    Now fetching city 211 of 600, azimur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=azimur,ma&units=imperial
    Error with city data. Skipping...
    Now fetching city 212 of 600, tacoronte
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tacoronte,es&units=imperial
    Now fetching city 213 of 600, kawalu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kawalu,id&units=imperial
    Now fetching city 214 of 600, beloha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=beloha,mg&units=imperial
    Now fetching city 215 of 600, waipawa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=waipawa,nz&units=imperial
    Now fetching city 216 of 600, avarua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=avarua,ck&units=imperial
    Now fetching city 217 of 600, naze
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=naze,jp&units=imperial
    Now fetching city 218 of 600, gizo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gizo,sb&units=imperial
    Now fetching city 219 of 600, mamlyutka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mamlyutka,kz&units=imperial
    Now fetching city 220 of 600, sur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sur,om&units=imperial
    Now fetching city 221 of 600, jasper
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jasper,us&units=imperial
    Now fetching city 222 of 600, constitucion
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=constitucion,mx&units=imperial
    Now fetching city 223 of 600, la palma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=la%20palma,pa&units=imperial
    Now fetching city 224 of 600, elena
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=elena,bg&units=imperial
    Now fetching city 225 of 600, hobyo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hobyo,so&units=imperial
    Now fetching city 226 of 600, ribeira grande
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ribeira%20grande,pt&units=imperial
    Now fetching city 227 of 600, vanimo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vanimo,pg&units=imperial
    Now fetching city 228 of 600, prince rupert
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=prince%20rupert,ca&units=imperial
    Now fetching city 229 of 600, karratha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=karratha,au&units=imperial
    Now fetching city 230 of 600, nyurba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nyurba,ru&units=imperial
    Now fetching city 231 of 600, sitka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sitka,us&units=imperial
    Now fetching city 232 of 600, beni suef
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=beni%20suef,eg&units=imperial
    Now fetching city 233 of 600, evensk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=evensk,ru&units=imperial
    Now fetching city 234 of 600, manado
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=manado,id&units=imperial
    Now fetching city 235 of 600, gigmoto
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gigmoto,ph&units=imperial
    Now fetching city 236 of 600, kasongo-lunda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kasongo-lunda,cd&units=imperial
    Now fetching city 237 of 600, barbar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barbar,sd&units=imperial
    Error with city data. Skipping...
    Now fetching city 238 of 600, oshakati
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=oshakati,na&units=imperial
    Now fetching city 239 of 600, ponta do sol
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ponta%20do%20sol,cv&units=imperial
    Now fetching city 240 of 600, cabo san lucas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cabo%20san%20lucas,mx&units=imperial
    Now fetching city 241 of 600, alakurtti
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=alakurtti,ru&units=imperial
    Now fetching city 242 of 600, mrirt
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mrirt,ma&units=imperial
    Error with city data. Skipping...
    Now fetching city 243 of 600, chapais
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chapais,ca&units=imperial
    Now fetching city 244 of 600, barra do corda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barra%20do%20corda,br&units=imperial
    Now fetching city 245 of 600, killarney
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=killarney,ca&units=imperial
    Now fetching city 246 of 600, kailua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kailua,us&units=imperial
    Now fetching city 247 of 600, taksimo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=taksimo,ru&units=imperial
    Now fetching city 248 of 600, nguiu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nguiu,au&units=imperial
    Error with city data. Skipping...
    Now fetching city 249 of 600, tuy hoa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tuy%20hoa,vn&units=imperial
    Now fetching city 250 of 600, taoudenni
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=taoudenni,ml&units=imperial
    Now fetching city 251 of 600, belen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=belen,us&units=imperial
    Now fetching city 252 of 600, sao filipe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sao%20filipe,cv&units=imperial
    Now fetching city 253 of 600, platteville
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=platteville,us&units=imperial
    Now fetching city 254 of 600, grand gaube
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grand%20gaube,mu&units=imperial
    Now fetching city 255 of 600, clyde river
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=clyde%20river,ca&units=imperial
    Now fetching city 256 of 600, henties bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=henties%20bay,na&units=imperial
    Now fetching city 257 of 600, charters towers
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=charters%20towers,au&units=imperial
    Now fetching city 258 of 600, flinders
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=flinders,au&units=imperial
    Now fetching city 259 of 600, anchovy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=anchovy,jm&units=imperial
    Now fetching city 260 of 600, bengkulu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bengkulu,id&units=imperial
    Error with city data. Skipping...
    Now fetching city 261 of 600, yar-sale
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yar-sale,ru&units=imperial
    Now fetching city 262 of 600, fukue
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fukue,jp&units=imperial
    Now fetching city 263 of 600, los llanos de aridane
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=los%20llanos%20de%20aridane,es&units=imperial
    Now fetching city 264 of 600, poum
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=poum,nc&units=imperial
    Now fetching city 265 of 600, homer
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=homer,us&units=imperial
    Now fetching city 266 of 600, leningradskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=leningradskiy,ru&units=imperial
    Now fetching city 267 of 600, waspan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=waspan,ni&units=imperial
    Error with city data. Skipping...
    Now fetching city 268 of 600, krasnoselkup
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=krasnoselkup,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 269 of 600, bambous virieux
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bambous%20virieux,mu&units=imperial
    Now fetching city 270 of 600, boguchany
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=boguchany,ru&units=imperial
    Now fetching city 271 of 600, belmopan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=belmopan,bz&units=imperial
    Now fetching city 272 of 600, soyo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=soyo,ao&units=imperial
    Now fetching city 273 of 600, malabo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=malabo,gq&units=imperial
    Now fetching city 274 of 600, san carlos de bariloche
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20carlos%20de%20bariloche,ar&units=imperial
    Now fetching city 275 of 600, chegem
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chegem,ru&units=imperial
    Now fetching city 276 of 600, paradwip
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=paradwip,in&units=imperial
    Error with city data. Skipping...
    Now fetching city 277 of 600, san rafael
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20rafael,ar&units=imperial
    Now fetching city 278 of 600, moose factory
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=moose%20factory,ca&units=imperial
    Now fetching city 279 of 600, makakilo city
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=makakilo%20city,us&units=imperial
    Now fetching city 280 of 600, mahadday weyne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mahadday%20weyne,so&units=imperial
    Error with city data. Skipping...
    Now fetching city 281 of 600, texarkana
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=texarkana,us&units=imperial
    Now fetching city 282 of 600, brae
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=brae,gb&units=imperial
    Now fetching city 283 of 600, lakatoro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lakatoro,vu&units=imperial
    Now fetching city 284 of 600, manthani
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=manthani,in&units=imperial
    Now fetching city 285 of 600, inderborskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=inderborskiy,kz&units=imperial
    Error with city data. Skipping...
    Now fetching city 286 of 600, provideniya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=provideniya,ru&units=imperial
    Now fetching city 287 of 600, dingle
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dingle,ie&units=imperial
    Now fetching city 288 of 600, pochutla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pochutla,mx&units=imperial
    Now fetching city 289 of 600, te anau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=te%20anau,nz&units=imperial
    Now fetching city 290 of 600, otaru
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=otaru,jp&units=imperial
    Now fetching city 291 of 600, belushya guba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=belushya%20guba,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 292 of 600, ilulissat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ilulissat,gl&units=imperial
    Now fetching city 293 of 600, mys shmidta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mys%20shmidta,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 294 of 600, ahuimanu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ahuimanu,us&units=imperial
    Now fetching city 295 of 600, ponta delgada
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ponta%20delgada,pt&units=imperial
    Now fetching city 296 of 600, talnakh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=talnakh,ru&units=imperial
    Now fetching city 297 of 600, esmeralda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=esmeralda,cu&units=imperial
    Now fetching city 298 of 600, longlac
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=longlac,ca&units=imperial
    Error with city data. Skipping...
    Now fetching city 299 of 600, aksu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aksu,cn&units=imperial
    Now fetching city 300 of 600, ellisras
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ellisras,za&units=imperial
    Now fetching city 301 of 600, tabiauea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tabiauea,ki&units=imperial
    Error with city data. Skipping...
    Now fetching city 302 of 600, bonthe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bonthe,sl&units=imperial
    Now fetching city 303 of 600, quatre cocos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=quatre%20cocos,mu&units=imperial
    Now fetching city 304 of 600, zhigansk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zhigansk,ru&units=imperial
    Now fetching city 305 of 600, zabol
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zabol,ir&units=imperial
    Now fetching city 306 of 600, gao
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gao,ml&units=imperial
    Now fetching city 307 of 600, benin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=benin,ng&units=imperial
    Error with city data. Skipping...
    Now fetching city 308 of 600, saleaula
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saleaula,ws&units=imperial
    Error with city data. Skipping...
    Now fetching city 309 of 600, bukachacha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bukachacha,ru&units=imperial
    Now fetching city 310 of 600, cherskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cherskiy,ru&units=imperial
    Now fetching city 311 of 600, rio gallegos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rio%20gallegos,ar&units=imperial
    Now fetching city 312 of 600, maues
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maues,br&units=imperial
    Now fetching city 313 of 600, banjar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=banjar,id&units=imperial
    Now fetching city 314 of 600, barroso
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barroso,br&units=imperial
    Now fetching city 315 of 600, itarema
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=itarema,br&units=imperial
    Now fetching city 316 of 600, maragogi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maragogi,br&units=imperial
    Now fetching city 317 of 600, manono
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=manono,cd&units=imperial
    Now fetching city 318 of 600, duobao
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=duobao,cn&units=imperial
    Now fetching city 319 of 600, sergeyevka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sergeyevka,kz&units=imperial
    Now fetching city 320 of 600, panguna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=panguna,pg&units=imperial
    Now fetching city 321 of 600, vaitupu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vaitupu,wf&units=imperial
    Error with city data. Skipping...
    Now fetching city 322 of 600, carinhanha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=carinhanha,br&units=imperial
    Now fetching city 323 of 600, saravan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saravan,la&units=imperial
    Error with city data. Skipping...
    Now fetching city 324 of 600, bethel
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bethel,us&units=imperial
    Now fetching city 325 of 600, kodinsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kodinsk,ru&units=imperial
    Now fetching city 326 of 600, straumen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=straumen,no&units=imperial
    Now fetching city 327 of 600, sioux lookout
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sioux%20lookout,ca&units=imperial
    Now fetching city 328 of 600, kedgwick
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kedgwick,ca&units=imperial
    Now fetching city 329 of 600, marawi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marawi,sd&units=imperial
    Now fetching city 330 of 600, bossangoa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bossangoa,cf&units=imperial
    Now fetching city 331 of 600, sao joao da barra
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sao%20joao%20da%20barra,br&units=imperial
    Now fetching city 332 of 600, parkes
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=parkes,au&units=imperial
    Now fetching city 333 of 600, ipixuna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ipixuna,br&units=imperial
    Now fetching city 334 of 600, san quintin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20quintin,mx&units=imperial
    Error with city data. Skipping...
    Now fetching city 335 of 600, requena
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=requena,pe&units=imperial
    Error with city data. Skipping...
    Now fetching city 336 of 600, stoyba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=stoyba,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 337 of 600, la esperanza
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=la%20esperanza,co&units=imperial
    Now fetching city 338 of 600, edson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=edson,ca&units=imperial
    Now fetching city 339 of 600, srednekolymsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=srednekolymsk,ru&units=imperial
    Now fetching city 340 of 600, northam
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=northam,au&units=imperial
    Now fetching city 341 of 600, malakal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=malakal,sd&units=imperial
    Error with city data. Skipping...
    Now fetching city 342 of 600, gezing
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gezing,in&units=imperial
    Error with city data. Skipping...
    Now fetching city 343 of 600, abashiri
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=abashiri,jp&units=imperial
    Now fetching city 344 of 600, nyuksenitsa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nyuksenitsa,ru&units=imperial
    Now fetching city 345 of 600, severodvinsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=severodvinsk,ru&units=imperial
    Now fetching city 346 of 600, howard springs
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=howard%20springs,au&units=imperial
    Now fetching city 347 of 600, san onofre
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20onofre,co&units=imperial
    Now fetching city 348 of 600, yenagoa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yenagoa,ng&units=imperial
    Now fetching city 349 of 600, ahipara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ahipara,nz&units=imperial
    Now fetching city 350 of 600, fort nelson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fort%20nelson,ca&units=imperial
    Now fetching city 351 of 600, tall kayf
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tall%20kayf,iq&units=imperial
    Error with city data. Skipping...
    Now fetching city 352 of 600, tsihombe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tsihombe,mg&units=imperial
    Error with city data. Skipping...
    Now fetching city 353 of 600, atar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=atar,mr&units=imperial
    Now fetching city 354 of 600, ankara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ankara,tr&units=imperial
    Now fetching city 355 of 600, kalmar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kalmar,se&units=imperial
    Now fetching city 356 of 600, paso de patria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=paso%20de%20patria,py&units=imperial
    Error with city data. Skipping...
    Now fetching city 357 of 600, bintulu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bintulu,my&units=imperial
    Now fetching city 358 of 600, daru
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=daru,pg&units=imperial
    Now fetching city 359 of 600, isangel
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=isangel,vu&units=imperial
    Now fetching city 360 of 600, warqla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=warqla,dz&units=imperial
    Error with city data. Skipping...
    Now fetching city 361 of 600, vestmanna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vestmanna,fo&units=imperial
    Now fetching city 362 of 600, beringovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=beringovskiy,ru&units=imperial
    Now fetching city 363 of 600, athni
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=athni,in&units=imperial
    Now fetching city 364 of 600, castelo branco
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=castelo%20branco,pt&units=imperial
    Now fetching city 365 of 600, campo largo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=campo%20largo,br&units=imperial
    Now fetching city 366 of 600, nemuro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nemuro,jp&units=imperial
    Now fetching city 367 of 600, armacao dos buzios
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=armacao%20dos%20buzios,br&units=imperial
    Error with city data. Skipping...
    Now fetching city 368 of 600, estelle
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=estelle,us&units=imperial
    Now fetching city 369 of 600, lavrentiya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lavrentiya,ru&units=imperial
    Now fetching city 370 of 600, santana do acarau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=santana%20do%20acarau,br&units=imperial
    Now fetching city 371 of 600, gobabis
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gobabis,na&units=imperial
    Now fetching city 372 of 600, sangar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sangar,ru&units=imperial
    Now fetching city 373 of 600, komsomolskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=komsomolskiy,ru&units=imperial
    Now fetching city 374 of 600, taltal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=taltal,cl&units=imperial
    Now fetching city 375 of 600, poso
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=poso,id&units=imperial
    Now fetching city 376 of 600, buala
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=buala,sb&units=imperial
    Now fetching city 377 of 600, nanortalik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nanortalik,gl&units=imperial
    Now fetching city 378 of 600, husavik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=husavik,is&units=imperial
    Now fetching city 379 of 600, camacari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=camacari,br&units=imperial
    Now fetching city 380 of 600, yurginskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yurginskoye,ru&units=imperial
    Now fetching city 381 of 600, navabad
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=navabad,tj&units=imperial
    Error with city data. Skipping...
    Now fetching city 382 of 600, batagay-alyta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=batagay-alyta,ru&units=imperial
    Now fetching city 383 of 600, pontes e lacerda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pontes%20e%20lacerda,br&units=imperial
    Now fetching city 384 of 600, moyale
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=moyale,et&units=imperial
    Now fetching city 385 of 600, zvishavane
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zvishavane,zw&units=imperial
    Now fetching city 386 of 600, vardo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vardo,no&units=imperial
    Now fetching city 387 of 600, filadelfia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=filadelfia,py&units=imperial
    Now fetching city 388 of 600, todos santos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=todos%20santos,mx&units=imperial
    Now fetching city 389 of 600, lalomanu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lalomanu,ws&units=imperial
    Error with city data. Skipping...
    Now fetching city 390 of 600, frunzivka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=frunzivka,ua&units=imperial
    Now fetching city 391 of 600, umm durman
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=umm%20durman,sd&units=imperial
    Error with city data. Skipping...
    Now fetching city 392 of 600, tuggurt
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tuggurt,dz&units=imperial
    Error with city data. Skipping...
    Now fetching city 393 of 600, baley
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=baley,ru&units=imperial
    Now fetching city 394 of 600, concepcion
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=concepcion,py&units=imperial
    Now fetching city 395 of 600, semey
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=semey,kz&units=imperial
    Now fetching city 396 of 600, lasa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lasa,cn&units=imperial
    Error with city data. Skipping...
    Now fetching city 397 of 600, riyadh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=riyadh,sa&units=imperial
    Now fetching city 398 of 600, barranca
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barranca,pe&units=imperial
    Now fetching city 399 of 600, grimari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grimari,cf&units=imperial
    Error with city data. Skipping...
    Now fetching city 400 of 600, manokwari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=manokwari,id&units=imperial
    Now fetching city 401 of 600, bathsheba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bathsheba,bb&units=imperial
    Now fetching city 402 of 600, lata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lata,sb&units=imperial
    Error with city data. Skipping...
    Now fetching city 403 of 600, les cayes
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=les%20cayes,ht&units=imperial
    Now fetching city 404 of 600, chara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chara,ru&units=imperial
    Now fetching city 405 of 600, geraldton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=geraldton,au&units=imperial
    Now fetching city 406 of 600, malwan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=malwan,in&units=imperial
    Error with city data. Skipping...
    Now fetching city 407 of 600, shelburne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shelburne,ca&units=imperial
    Now fetching city 408 of 600, palencia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=palencia,es&units=imperial
    Now fetching city 409 of 600, tobermory
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tobermory,gb&units=imperial
    Error with city data. Skipping...
    Now fetching city 410 of 600, new richmond
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=new%20richmond,ca&units=imperial
    Now fetching city 411 of 600, rungata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rungata,ki&units=imperial
    Error with city data. Skipping...
    Now fetching city 412 of 600, grand-lahou
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grand-lahou,ci&units=imperial
    Now fetching city 413 of 600, tishchenskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tishchenskoye,ru&units=imperial
    Now fetching city 414 of 600, ust-kamchatsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ust-kamchatsk,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 415 of 600, dzhusaly
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dzhusaly,kz&units=imperial
    Error with city data. Skipping...
    Now fetching city 416 of 600, cockburn town
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cockburn%20town,tc&units=imperial
    Now fetching city 417 of 600, andros town
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=andros%20town,bs&units=imperial
    Now fetching city 418 of 600, da nang
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=da%20nang,vn&units=imperial
    Error with city data. Skipping...
    Now fetching city 419 of 600, port-de-paix
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port-de-paix,ht&units=imperial
    Error with city data. Skipping...
    Now fetching city 420 of 600, labuhan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=labuhan,id&units=imperial
    Now fetching city 421 of 600, pousat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pousat,kh&units=imperial
    Error with city data. Skipping...
    Now fetching city 422 of 600, gorazde
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gorazde,ba&units=imperial
    Now fetching city 423 of 600, paysandu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=paysandu,uy&units=imperial
    Now fetching city 424 of 600, jumla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jumla,np&units=imperial
    Now fetching city 425 of 600, okhotsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=okhotsk,ru&units=imperial
    Now fetching city 426 of 600, touros
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=touros,br&units=imperial
    Now fetching city 427 of 600, bandarbeyla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bandarbeyla,so&units=imperial
    Now fetching city 428 of 600, lagunas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lagunas,pe&units=imperial
    Now fetching city 429 of 600, morant bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=morant%20bay,jm&units=imperial
    Now fetching city 430 of 600, palanga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=palanga,lt&units=imperial
    Now fetching city 431 of 600, carnot
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=carnot,cf&units=imperial
    Now fetching city 432 of 600, pacific grove
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pacific%20grove,us&units=imperial
    Now fetching city 433 of 600, yomitan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yomitan,jp&units=imperial
    Error with city data. Skipping...
    Now fetching city 434 of 600, umm kaddadah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=umm%20kaddadah,sd&units=imperial
    Now fetching city 435 of 600, balabac
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=balabac,ph&units=imperial
    Now fetching city 436 of 600, altay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=altay,cn&units=imperial
    Now fetching city 437 of 600, auki
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=auki,sb&units=imperial
    Now fetching city 438 of 600, yanggu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yanggu,cn&units=imperial
    Now fetching city 439 of 600, lagoa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lagoa,pt&units=imperial
    Now fetching city 440 of 600, kangaatsiaq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kangaatsiaq,gl&units=imperial
    Now fetching city 441 of 600, nabire
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nabire,id&units=imperial
    Now fetching city 442 of 600, senador guiomard
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=senador%20guiomard,br&units=imperial
    Now fetching city 443 of 600, conde
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=conde,br&units=imperial
    Now fetching city 444 of 600, sirjan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sirjan,ir&units=imperial
    Now fetching city 445 of 600, champerico
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=champerico,gt&units=imperial
    Now fetching city 446 of 600, zlate moravce
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zlate%20moravce,sk&units=imperial
    Now fetching city 447 of 600, tymovskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tymovskoye,ru&units=imperial
    Now fetching city 448 of 600, ust-nera
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ust-nera,ru&units=imperial
    Now fetching city 449 of 600, sao geraldo do araguaia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sao%20geraldo%20do%20araguaia,br&units=imperial
    Now fetching city 450 of 600, fredericksburg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fredericksburg,us&units=imperial
    Now fetching city 451 of 600, sant julia de loria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sant%20julia%20de%20loria,ad&units=imperial
    Now fetching city 452 of 600, chapleau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chapleau,ca&units=imperial
    Now fetching city 453 of 600, padang
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=padang,id&units=imperial
    Now fetching city 454 of 600, namatanai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=namatanai,pg&units=imperial
    Now fetching city 455 of 600, iguape
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=iguape,br&units=imperial
    Now fetching city 456 of 600, yulara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yulara,au&units=imperial
    Now fetching city 457 of 600, rawson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rawson,ar&units=imperial
    Now fetching city 458 of 600, ilam
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ilam,np&units=imperial
    Now fetching city 459 of 600, villazon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=villazon,bo&units=imperial
    Error with city data. Skipping...
    Now fetching city 460 of 600, tolaga bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tolaga%20bay,nz&units=imperial
    Now fetching city 461 of 600, christchurch
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=christchurch,nz&units=imperial
    Now fetching city 462 of 600, mundo nuevo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mundo%20nuevo,mx&units=imperial
    Now fetching city 463 of 600, ormara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ormara,pk&units=imperial
    Now fetching city 464 of 600, elvas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=elvas,pt&units=imperial
    Now fetching city 465 of 600, clearwater
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=clearwater,us&units=imperial
    Now fetching city 466 of 600, sonqor
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sonqor,ir&units=imperial
    Now fetching city 467 of 600, sacramento
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sacramento,br&units=imperial
    Now fetching city 468 of 600, mildura
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mildura,au&units=imperial
    Now fetching city 469 of 600, havre-saint-pierre
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=havre-saint-pierre,ca&units=imperial
    Now fetching city 470 of 600, mogadishu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mogadishu,so&units=imperial
    Now fetching city 471 of 600, kisangani
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kisangani,cd&units=imperial
    Now fetching city 472 of 600, ambon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ambon,id&units=imperial
    Now fetching city 473 of 600, manyana
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=manyana,bw&units=imperial
    Now fetching city 474 of 600, rantepao
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rantepao,id&units=imperial
    Now fetching city 475 of 600, pizarro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pizarro,co&units=imperial
    Now fetching city 476 of 600, tilichiki
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tilichiki,ru&units=imperial
    Now fetching city 477 of 600, kuche
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kuche,cn&units=imperial
    Error with city data. Skipping...
    Now fetching city 478 of 600, lodwar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lodwar,ke&units=imperial
    Now fetching city 479 of 600, hasaki
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hasaki,jp&units=imperial
    Now fetching city 480 of 600, topolobampo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=topolobampo,mx&units=imperial
    Now fetching city 481 of 600, vao
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vao,nc&units=imperial
    Now fetching city 482 of 600, san jose
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20jose,gt&units=imperial
    Now fetching city 483 of 600, montrose
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=montrose,us&units=imperial
    Now fetching city 484 of 600, dali
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dali,cn&units=imperial
    Now fetching city 485 of 600, grand centre
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grand%20centre,ca&units=imperial
    Error with city data. Skipping...
    Now fetching city 486 of 600, sao bento
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sao%20bento,br&units=imperial
    Error with city data. Skipping...
    Now fetching city 487 of 600, louisbourg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=louisbourg,ca&units=imperial
    Error with city data. Skipping...
    Now fetching city 488 of 600, sfantu gheorghe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sfantu%20gheorghe,ro&units=imperial
    Now fetching city 489 of 600, sinnamary
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sinnamary,gf&units=imperial
    Now fetching city 490 of 600, leninskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=leninskiy,ru&units=imperial
    Now fetching city 491 of 600, porosozero
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=porosozero,ru&units=imperial
    Now fetching city 492 of 600, galveston
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=galveston,us&units=imperial
    Now fetching city 493 of 600, morrelganj
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=morrelganj,bd&units=imperial
    Error with city data. Skipping...
    Now fetching city 494 of 600, shcholkine
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shcholkine,ua&units=imperial
    Error with city data. Skipping...
    Now fetching city 495 of 600, esna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=esna,eg&units=imperial
    Error with city data. Skipping...
    Now fetching city 496 of 600, lerwick
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lerwick,gb&units=imperial
    Now fetching city 497 of 600, iracoubo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=iracoubo,gf&units=imperial
    Now fetching city 498 of 600, ust-maya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ust-maya,ru&units=imperial
    Now fetching city 499 of 600, athabasca
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=athabasca,ca&units=imperial
    Now fetching city 500 of 600, vostok
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vostok,ru&units=imperial
    Now fetching city 501 of 600, shingu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shingu,jp&units=imperial
    Now fetching city 502 of 600, williams lake
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=williams%20lake,ca&units=imperial
    Now fetching city 503 of 600, alexandria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=alexandria,us&units=imperial
    Now fetching city 504 of 600, abu kamal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=abu%20kamal,sy&units=imperial
    Now fetching city 505 of 600, aklavik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aklavik,ca&units=imperial
    Now fetching city 506 of 600, berlevag
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=berlevag,no&units=imperial
    Now fetching city 507 of 600, bereda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bereda,so&units=imperial
    Error with city data. Skipping...
    Now fetching city 508 of 600, chunan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chunan,tw&units=imperial
    Error with city data. Skipping...
    Now fetching city 509 of 600, high level
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=high%20level,ca&units=imperial
    Now fetching city 510 of 600, datong
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=datong,cn&units=imperial
    Now fetching city 511 of 600, iqaluit
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=iqaluit,ca&units=imperial
    Now fetching city 512 of 600, litovko
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=litovko,ru&units=imperial
    Now fetching city 513 of 600, swellendam
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=swellendam,za&units=imperial
    Now fetching city 514 of 600, veshkayma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=veshkayma,ru&units=imperial
    Now fetching city 515 of 600, ihosy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ihosy,mg&units=imperial
    Now fetching city 516 of 600, malartic
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=malartic,ca&units=imperial
    Now fetching city 517 of 600, morondava
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=morondava,mg&units=imperial
    Now fetching city 518 of 600, ukiah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ukiah,us&units=imperial
    Now fetching city 519 of 600, hami
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hami,cn&units=imperial
    Now fetching city 520 of 600, namibe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=namibe,ao&units=imperial
    Now fetching city 521 of 600, valle hermoso
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=valle%20hermoso,mx&units=imperial
    Now fetching city 522 of 600, bafq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bafq,ir&units=imperial
    Now fetching city 523 of 600, port said
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20said,eg&units=imperial
    Now fetching city 524 of 600, boyolangu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=boyolangu,id&units=imperial
    Now fetching city 525 of 600, tarn taran
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tarn%20taran,in&units=imperial
    Now fetching city 526 of 600, marsa matruh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marsa%20matruh,eg&units=imperial
    Now fetching city 527 of 600, nizwa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nizwa,om&units=imperial
    Now fetching city 528 of 600, maqrin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maqrin,tn&units=imperial
    Error with city data. Skipping...
    Now fetching city 529 of 600, rudsar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rudsar,ir&units=imperial
    Now fetching city 530 of 600, sakakah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sakakah,sa&units=imperial
    Error with city data. Skipping...
    Now fetching city 531 of 600, coshocton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=coshocton,us&units=imperial
    Now fetching city 532 of 600, umzimvubu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=umzimvubu,za&units=imperial
    Error with city data. Skipping...
    Now fetching city 533 of 600, bose
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bose,cn&units=imperial
    Error with city data. Skipping...
    Now fetching city 534 of 600, peniche
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=peniche,pt&units=imperial
    Now fetching city 535 of 600, qui nhon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=qui%20nhon,vn&units=imperial
    Error with city data. Skipping...
    Now fetching city 536 of 600, berbera
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=berbera,so&units=imperial
    Now fetching city 537 of 600, alamogordo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=alamogordo,us&units=imperial
    Now fetching city 538 of 600, nguru
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nguru,ng&units=imperial
    Now fetching city 539 of 600, moranbah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=moranbah,au&units=imperial
    Now fetching city 540 of 600, petropavlovsk-kamchatskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=petropavlovsk-kamchatskiy,ru&units=imperial
    Now fetching city 541 of 600, russell
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=russell,nz&units=imperial
    Now fetching city 542 of 600, dossor
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dossor,kz&units=imperial
    Now fetching city 543 of 600, asau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=asau,tv&units=imperial
    Error with city data. Skipping...
    Now fetching city 544 of 600, san andres
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20andres,co&units=imperial
    Now fetching city 545 of 600, wanaka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=wanaka,nz&units=imperial
    Now fetching city 546 of 600, taseyevo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=taseyevo,ru&units=imperial
    Now fetching city 547 of 600, mandalgovi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mandalgovi,mn&units=imperial
    Now fetching city 548 of 600, biltine
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=biltine,td&units=imperial
    Now fetching city 549 of 600, nalut
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nalut,ly&units=imperial
    Now fetching city 550 of 600, shieli
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shieli,kz&units=imperial
    Now fetching city 551 of 600, olafsvik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=olafsvik,is&units=imperial
    Error with city data. Skipping...
    Now fetching city 552 of 600, ulladulla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ulladulla,au&units=imperial
    Now fetching city 553 of 600, jining
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jining,cn&units=imperial
    Now fetching city 554 of 600, korla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=korla,cn&units=imperial
    Now fetching city 555 of 600, santa cecilia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=santa%20cecilia,br&units=imperial
    Now fetching city 556 of 600, scarborough
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=scarborough,gb&units=imperial
    Now fetching city 557 of 600, nortelandia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nortelandia,br&units=imperial
    Now fetching city 558 of 600, pskov
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pskov,ru&units=imperial
    Now fetching city 559 of 600, booue
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=booue,ga&units=imperial
    Now fetching city 560 of 600, urucara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=urucara,br&units=imperial
    Now fetching city 561 of 600, richards bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=richards%20bay,za&units=imperial
    Now fetching city 562 of 600, coari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=coari,br&units=imperial
    Now fetching city 563 of 600, litoral del san juan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=litoral%20del%20san%20juan,co&units=imperial
    Error with city data. Skipping...
    Now fetching city 564 of 600, barra patuca
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barra%20patuca,hn&units=imperial
    Now fetching city 565 of 600, varnamo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=varnamo,se&units=imperial
    Now fetching city 566 of 600, san nicolas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20nicolas,ph&units=imperial
    Now fetching city 567 of 600, road town
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=road%20town,vg&units=imperial
    Now fetching city 568 of 600, luanda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=luanda,ao&units=imperial
    Now fetching city 569 of 600, tinogboc
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tinogboc,ph&units=imperial
    Now fetching city 570 of 600, kenitra
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kenitra,ma&units=imperial
    Now fetching city 571 of 600, praia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=praia,cv&units=imperial
    Now fetching city 572 of 600, vilyuysk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vilyuysk,ru&units=imperial
    Now fetching city 573 of 600, sibolga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sibolga,id&units=imperial
    Now fetching city 574 of 600, hervey bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hervey%20bay,au&units=imperial
    Now fetching city 575 of 600, ingham
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ingham,au&units=imperial
    Now fetching city 576 of 600, goderich
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=goderich,sl&units=imperial
    Error with city data. Skipping...
    Now fetching city 577 of 600, broome
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=broome,au&units=imperial
    Now fetching city 578 of 600, hewitt
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hewitt,us&units=imperial
    Now fetching city 579 of 600, tual
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tual,id&units=imperial
    Now fetching city 580 of 600, varna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=varna,ru&units=imperial
    Now fetching city 581 of 600, rocha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rocha,uy&units=imperial
    Now fetching city 582 of 600, tanout
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tanout,ne&units=imperial
    Now fetching city 583 of 600, ngukurr
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ngukurr,au&units=imperial
    Error with city data. Skipping...
    Now fetching city 584 of 600, palabuhanratu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=palabuhanratu,id&units=imperial
    Error with city data. Skipping...
    Now fetching city 585 of 600, urdoma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=urdoma,ru&units=imperial
    Now fetching city 586 of 600, sorvag
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sorvag,fo&units=imperial
    Error with city data. Skipping...
    Now fetching city 587 of 600, bara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bara,sd&units=imperial
    Error with city data. Skipping...
    Now fetching city 588 of 600, talaya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=talaya,ru&units=imperial
    Now fetching city 589 of 600, igarka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=igarka,ru&units=imperial
    Now fetching city 590 of 600, kudahuvadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kudahuvadhoo,mv&units=imperial
    Now fetching city 591 of 600, hurghada
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hurghada,eg&units=imperial
    Error with city data. Skipping...
    Now fetching city 592 of 600, mahates
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mahates,co&units=imperial
    Now fetching city 593 of 600, yamada
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yamada,jp&units=imperial
    Now fetching city 594 of 600, xingcheng
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=xingcheng,cn&units=imperial
    Now fetching city 595 of 600, hambantota
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hambantota,lk&units=imperial
    Now fetching city 596 of 600, diu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=diu,in&units=imperial
    Now fetching city 597 of 600, kalmunai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kalmunai,lk&units=imperial
    Now fetching city 598 of 600, kenora
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kenora,ca&units=imperial
    Now fetching city 599 of 600, zhicheng
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zhicheng,cn&units=imperial
    --------------------------
    Data Retrieval Complete
    --------------------------



```python
#drop null values, rename columns, and export dataframe to csv
city_weather_df = city_df.dropna()
city_weather_df = city_weather_df.rename(columns={"Temperature": "Temperature (F)", "Humidity": "Humidity %"})
city_weather_df.head(20)

city_weather_df.to_csv("City Weather Data.csv")






```

    508



```python
#create latitude vs. temperature scatterplot
plt.scatter(city_weather_df["Latitude"], city_weather_df["Temperature (F)"], marker="o", 
            color="b", edgecolor="black", alpha=.8, label="City")
plt.xlim(-100, 100)
plt.ylim(-25, (city_weather_df["Temperature (F)"].max()+5))
plt.xlabel("Latitude")
plt.ylabel("Temperature (F)")
plt.title("City Latitude vs. Max Temperature (F) 4-1-2018")
plt.grid(True)
plt.show()
plt.savefig("City Latitude vs. Max Temperature 4-1-2018.png")
```


![png](output_3_0.png)



```python
#create latitude vs. humidity scatterplot
plt.scatter(city_weather_df["Latitude"], city_weather_df["Humidity %"], marker="o",
           color="b", edgecolor="black", alpha=.8, label="City")
plt.xlim(-100, 100)
plt.ylim(0, (city_weather_df["Humidity %"].max()+5))
plt.xlabel("Latitude")
plt.ylabel("Humidity %")
plt.title("City Latitude vs. Humidity 4-1-2018")
plt.grid(True)
plt.show()
plt.savefig("City Latitude vs. Humidity 4-1-2018.png")
```


![png](output_4_0.png)



```python
#create latitude vs. cloud cover scatterplot
plt.scatter(city_weather_df["Latitude"], city_weather_df["Cloud Cover %"], marker="o",
           color="b", edgecolor="black", alpha=.8, label="City")
plt.xlim(-100, 100)
plt.ylim(-5, (city_weather_df["Cloud Cover %"].max()+5))
plt.xlabel("Latitude")
plt.ylabel("Cloud Cover %")
plt.title("City Latitude vs. Cloud Cover 4-1-18")
plt.grid(True)
plt.show()
plt.savefig("City Latitude vs. Cloud Cover 4-1-2018.png")
```


![png](output_5_0.png)



```python
#create latitude vs. wind speed scatterplot
plt.scatter(city_weather_df["Latitude"], city_weather_df["Wind Speed (MPH)"], marker="o",
           color="b", edgecolor="black", alpha=.8, label="City")
plt.xlim(-100, 100)
plt.ylim(-5, (city_weather_df["Wind Speed (MPH)"].max()+5))
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (MPH)")
plt.title("City Latitude vs. Wind Speed 4-1-18")
plt.grid(True)
plt.show()
plt.savefig("City Latitude vs. Wind Speed 4-1-2018.png")
```


![png](output_6_0.png)

