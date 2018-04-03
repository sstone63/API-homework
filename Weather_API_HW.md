

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
      <td>sistranda</td>
      <td>no</td>
      <td>65.178455</td>
      <td>7.711470</td>
    </tr>
    <tr>
      <th>1</th>
      <td>champasak</td>
      <td>la</td>
      <td>13.705067</td>
      <td>105.683461</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tinskoy</td>
      <td>ru</td>
      <td>54.316015</td>
      <td>96.878936</td>
    </tr>
    <tr>
      <th>3</th>
      <td>beringovskiy</td>
      <td>ru</td>
      <td>61.698448</td>
      <td>179.864941</td>
    </tr>
    <tr>
      <th>4</th>
      <td>vysha</td>
      <td>ru</td>
      <td>54.064601</td>
      <td>42.408846</td>
    </tr>
    <tr>
      <th>5</th>
      <td>punta arenas</td>
      <td>cl</td>
      <td>-61.259557</td>
      <td>-113.717797</td>
    </tr>
    <tr>
      <th>6</th>
      <td>pathein</td>
      <td>mm</td>
      <td>17.460980</td>
      <td>92.935947</td>
    </tr>
    <tr>
      <th>7</th>
      <td>tazovskiy</td>
      <td>ru</td>
      <td>68.634023</td>
      <td>78.025149</td>
    </tr>
    <tr>
      <th>8</th>
      <td>fuxin</td>
      <td>cn</td>
      <td>42.450667</td>
      <td>121.631819</td>
    </tr>
    <tr>
      <th>9</th>
      <td>chunskiy</td>
      <td>ru</td>
      <td>57.230604</td>
      <td>99.697991</td>
    </tr>
    <tr>
      <th>10</th>
      <td>dikson</td>
      <td>ru</td>
      <td>90.328251</td>
      <td>81.116582</td>
    </tr>
    <tr>
      <th>11</th>
      <td>kourou</td>
      <td>gf</td>
      <td>5.371142</td>
      <td>-52.366746</td>
    </tr>
    <tr>
      <th>12</th>
      <td>thompson</td>
      <td>ca</td>
      <td>59.657071</td>
      <td>-95.465365</td>
    </tr>
    <tr>
      <th>13</th>
      <td>stornoway</td>
      <td>gb</td>
      <td>58.616829</td>
      <td>-11.051604</td>
    </tr>
    <tr>
      <th>14</th>
      <td>bethel</td>
      <td>us</td>
      <td>48.878811</td>
      <td>-164.440826</td>
    </tr>
    <tr>
      <th>15</th>
      <td>taltal</td>
      <td>cl</td>
      <td>-24.775022</td>
      <td>-81.060677</td>
    </tr>
    <tr>
      <th>16</th>
      <td>trairi</td>
      <td>br</td>
      <td>2.435668</td>
      <td>-36.012425</td>
    </tr>
    <tr>
      <th>17</th>
      <td>kapaa</td>
      <td>us</td>
      <td>33.400357</td>
      <td>-170.320733</td>
    </tr>
    <tr>
      <th>18</th>
      <td>cidreira</td>
      <td>br</td>
      <td>-57.528134</td>
      <td>-22.492667</td>
    </tr>
    <tr>
      <th>19</th>
      <td>tasiilaq</td>
      <td>gl</td>
      <td>69.071872</td>
      <td>-34.589935</td>
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
        actual_latitude = city_weather_json["coord"]["lat"]
        actual_longitude = city_weather_json["coord"]["lon"]
        
        city_df.set_value(index, "Temperature", city_temp)
        city_df.set_value(index, "Humidity", city_humidity)
        city_df.set_value(index, "Cloud Cover %", city_clouds)
        city_df.set_value(index, "Wind Speed (MPH)", city_wind_speed)
        city_df.set_value(index, "Actual Latitude", actual_latitude)
        city_df.set_value(index, "Actual Longitude", actual_longitude)
        
    
    #error exceptions for missing values
    except (KeyError, IndexError):
        print("Error with city data. Skipping...")
        
print("--------------------------")
print("Data Retrieval Complete")
print("--------------------------")


```

    Commencing Data Retrieval
    --------------------------
    Now fetching city 0 of 600, sistranda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sistranda,no&units=imperial
    Now fetching city 1 of 600, champasak
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=champasak,la&units=imperial
    Now fetching city 2 of 600, tinskoy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tinskoy,ru&units=imperial
    Now fetching city 3 of 600, beringovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=beringovskiy,ru&units=imperial
    Now fetching city 4 of 600, vysha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vysha,ru&units=imperial
    Now fetching city 5 of 600, punta arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=punta%20arenas,cl&units=imperial
    Now fetching city 6 of 600, pathein
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pathein,mm&units=imperial
    Now fetching city 7 of 600, tazovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tazovskiy,ru&units=imperial
    Now fetching city 8 of 600, fuxin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fuxin,cn&units=imperial
    Now fetching city 9 of 600, chunskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chunskiy,ru&units=imperial
    Now fetching city 10 of 600, dikson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dikson,ru&units=imperial
    Now fetching city 11 of 600, kourou
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kourou,gf&units=imperial
    Now fetching city 12 of 600, thompson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=thompson,ca&units=imperial
    Now fetching city 13 of 600, stornoway
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=stornoway,gb&units=imperial
    Now fetching city 14 of 600, bethel
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bethel,us&units=imperial
    Now fetching city 15 of 600, taltal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=taltal,cl&units=imperial
    Now fetching city 16 of 600, trairi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=trairi,br&units=imperial
    Now fetching city 17 of 600, kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kapaa,us&units=imperial
    Now fetching city 18 of 600, cidreira
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cidreira,br&units=imperial
    Now fetching city 19 of 600, tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tasiilaq,gl&units=imperial
    Now fetching city 20 of 600, college
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=college,us&units=imperial
    Now fetching city 21 of 600, biak
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=biak,id&units=imperial
    Now fetching city 22 of 600, hilo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hilo,us&units=imperial
    Now fetching city 23 of 600, mossendjo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mossendjo,cg&units=imperial
    Now fetching city 24 of 600, mineiros
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mineiros,br&units=imperial
    Now fetching city 25 of 600, bluff
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bluff,nz&units=imperial
    Now fetching city 26 of 600, hobart
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hobart,au&units=imperial
    Now fetching city 27 of 600, salekhard
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=salekhard,ru&units=imperial
    Now fetching city 28 of 600, tura
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tura,ru&units=imperial
    Now fetching city 29 of 600, ovsyanka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ovsyanka,ru&units=imperial
    Now fetching city 30 of 600, comodoro rivadavia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=comodoro%20rivadavia,ar&units=imperial
    Now fetching city 31 of 600, yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yellowknife,ca&units=imperial
    Now fetching city 32 of 600, ponta do sol
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ponta%20do%20sol,cv&units=imperial
    Now fetching city 33 of 600, lima
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lima,pe&units=imperial
    Now fetching city 34 of 600, ribeira grande
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ribeira%20grande,pt&units=imperial
    Now fetching city 35 of 600, codrington
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=codrington,ag&units=imperial
    Error with city data. Skipping...
    Now fetching city 36 of 600, tygda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tygda,ru&units=imperial
    Now fetching city 37 of 600, zhangjiakou
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zhangjiakou,cn&units=imperial
    Now fetching city 38 of 600, mahadday weyne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mahadday%20weyne,so&units=imperial
    Error with city data. Skipping...
    Now fetching city 39 of 600, taolanaro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=taolanaro,mg&units=imperial
    Error with city data. Skipping...
    Now fetching city 40 of 600, flinders
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=flinders,au&units=imperial
    Now fetching city 41 of 600, georgetown
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=georgetown,sh&units=imperial
    Now fetching city 42 of 600, karpathos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=karpathos,gr&units=imperial
    Now fetching city 43 of 600, rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rikitea,pf&units=imperial
    Now fetching city 44 of 600, toora-khem
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=toora-khem,ru&units=imperial
    Now fetching city 45 of 600, illoqqortoormiut
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=illoqqortoormiut,gl&units=imperial
    Error with city data. Skipping...
    Now fetching city 46 of 600, mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mataura,pf&units=imperial
    Error with city data. Skipping...
    Now fetching city 47 of 600, sao joaquim
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sao%20joaquim,br&units=imperial
    Now fetching city 48 of 600, nizhneyansk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nizhneyansk,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 49 of 600, sobolevo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sobolevo,ru&units=imperial
    Now fetching city 50 of 600, vao
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vao,nc&units=imperial
    Now fetching city 51 of 600, pangkalanbuun
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pangkalanbuun,id&units=imperial
    Now fetching city 52 of 600, cape town
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cape%20town,za&units=imperial
    Now fetching city 53 of 600, puerto ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=puerto%20ayora,ec&units=imperial
    Now fetching city 54 of 600, shitanjing
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shitanjing,cn&units=imperial
    Now fetching city 55 of 600, klaksvik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=klaksvik,fo&units=imperial
    Now fetching city 56 of 600, barentsburg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barentsburg,sj&units=imperial
    Error with city data. Skipping...
    Now fetching city 57 of 600, zhigansk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zhigansk,ru&units=imperial
    Now fetching city 58 of 600, lahij
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lahij,ye&units=imperial
    Now fetching city 59 of 600, albany
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=albany,au&units=imperial
    Now fetching city 60 of 600, khatanga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=khatanga,ru&units=imperial
    Now fetching city 61 of 600, kommunarka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kommunarka,ru&units=imperial
    Now fetching city 62 of 600, taoudenni
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=taoudenni,ml&units=imperial
    Now fetching city 63 of 600, avarua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=avarua,ck&units=imperial
    Now fetching city 64 of 600, katsuura
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=katsuura,jp&units=imperial
    Now fetching city 65 of 600, barrow
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barrow,us&units=imperial
    Now fetching city 66 of 600, sorland
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sorland,no&units=imperial
    Now fetching city 67 of 600, butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=butaritari,ki&units=imperial
    Now fetching city 68 of 600, qaanaaq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=qaanaaq,gl&units=imperial
    Now fetching city 69 of 600, new norfolk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=new%20norfolk,au&units=imperial
    Now fetching city 70 of 600, port alfred
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20alfred,za&units=imperial
    Now fetching city 71 of 600, ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ushuaia,ar&units=imperial
    Now fetching city 72 of 600, pevek
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pevek,ru&units=imperial
    Now fetching city 73 of 600, busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=busselton,au&units=imperial
    Now fetching city 74 of 600, anadyr
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=anadyr,ru&units=imperial
    Now fetching city 75 of 600, defiance
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=defiance,us&units=imperial
    Now fetching city 76 of 600, linxia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=linxia,cn&units=imperial
    Now fetching city 77 of 600, atherton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=atherton,au&units=imperial
    Now fetching city 78 of 600, salalah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=salalah,om&units=imperial
    Now fetching city 79 of 600, port hardy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20hardy,ca&units=imperial
    Now fetching city 80 of 600, tuggurt
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tuggurt,dz&units=imperial
    Error with city data. Skipping...
    Now fetching city 81 of 600, makakilo city
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=makakilo%20city,us&units=imperial
    Now fetching city 82 of 600, hutang
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hutang,cn&units=imperial
    Now fetching city 83 of 600, east london
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=east%20london,za&units=imperial
    Now fetching city 84 of 600, tsihombe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tsihombe,mg&units=imperial
    Error with city data. Skipping...
    Now fetching city 85 of 600, canavieiras
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=canavieiras,br&units=imperial
    Now fetching city 86 of 600, matara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=matara,lk&units=imperial
    Now fetching city 87 of 600, lloret de mar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lloret%20de%20mar,es&units=imperial
    Now fetching city 88 of 600, severo-kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=severo-kurilsk,ru&units=imperial
    Now fetching city 89 of 600, yaan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yaan,cn&units=imperial
    Error with city data. Skipping...
    Now fetching city 90 of 600, nikki
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nikki,bj&units=imperial
    Now fetching city 91 of 600, vila velha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vila%20velha,br&units=imperial
    Now fetching city 92 of 600, cabo san lucas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cabo%20san%20lucas,mx&units=imperial
    Now fetching city 93 of 600, mount gambier
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mount%20gambier,au&units=imperial
    Now fetching city 94 of 600, eenhana
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=eenhana,na&units=imperial
    Now fetching city 95 of 600, saint-philippe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-philippe,re&units=imperial
    Now fetching city 96 of 600, ancud
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ancud,cl&units=imperial
    Now fetching city 97 of 600, la paz
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=la%20paz,mx&units=imperial
    Now fetching city 98 of 600, vila do maio
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vila%20do%20maio,cv&units=imperial
    Now fetching city 99 of 600, shache
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shache,cn&units=imperial
    Now fetching city 100 of 600, ilo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ilo,pe&units=imperial
    Now fetching city 101 of 600, deputatskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=deputatskiy,ru&units=imperial
    Now fetching city 102 of 600, bossembele
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bossembele,cf&units=imperial
    Error with city data. Skipping...
    Now fetching city 103 of 600, tuatapere
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tuatapere,nz&units=imperial
    Now fetching city 104 of 600, samusu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=samusu,ws&units=imperial
    Error with city data. Skipping...
    Now fetching city 105 of 600, todos santos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=todos%20santos,mx&units=imperial
    Now fetching city 106 of 600, axim
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=axim,gh&units=imperial
    Now fetching city 107 of 600, hasaki
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hasaki,jp&units=imperial
    Now fetching city 108 of 600, hithadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hithadhoo,mv&units=imperial
    Now fetching city 109 of 600, djenne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=djenne,ml&units=imperial
    Now fetching city 110 of 600, castro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=castro,cl&units=imperial
    Now fetching city 111 of 600, kavaratti
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kavaratti,in&units=imperial
    Now fetching city 112 of 600, natal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=natal,br&units=imperial
    Now fetching city 113 of 600, vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vaini,to&units=imperial
    Now fetching city 114 of 600, nanortalik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nanortalik,gl&units=imperial
    Now fetching city 115 of 600, requena
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=requena,pe&units=imperial
    Error with city data. Skipping...
    Now fetching city 116 of 600, pontes e lacerda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pontes%20e%20lacerda,br&units=imperial
    Now fetching city 117 of 600, vanimo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vanimo,pg&units=imperial
    Now fetching city 118 of 600, horasan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=horasan,tr&units=imperial
    Now fetching city 119 of 600, beaverlodge
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=beaverlodge,ca&units=imperial
    Now fetching city 120 of 600, shenjiamen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shenjiamen,cn&units=imperial
    Now fetching city 121 of 600, saint george
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint%20george,bm&units=imperial
    Now fetching city 122 of 600, lisakovsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lisakovsk,kz&units=imperial
    Now fetching city 123 of 600, charters towers
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=charters%20towers,au&units=imperial
    Now fetching city 124 of 600, masjed-e soleyman
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=masjed-e%20soleyman,ir&units=imperial
    Error with city data. Skipping...
    Now fetching city 125 of 600, benguela
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=benguela,ao&units=imperial
    Now fetching city 126 of 600, chapais
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chapais,ca&units=imperial
    Now fetching city 127 of 600, dolhesti
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dolhesti,ro&units=imperial
    Now fetching city 128 of 600, ambilobe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ambilobe,mg&units=imperial
    Now fetching city 129 of 600, samarai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=samarai,pg&units=imperial
    Now fetching city 130 of 600, saint-louis
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-louis,sn&units=imperial
    Now fetching city 131 of 600, cherskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cherskiy,ru&units=imperial
    Now fetching city 132 of 600, nikolskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nikolskoye,ru&units=imperial
    Now fetching city 133 of 600, lasa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lasa,cn&units=imperial
    Error with city data. Skipping...
    Now fetching city 134 of 600, monrovia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=monrovia,lr&units=imperial
    Now fetching city 135 of 600, guerrero negro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=guerrero%20negro,mx&units=imperial
    Now fetching city 136 of 600, hudson bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hudson%20bay,ca&units=imperial
    Now fetching city 137 of 600, alice springs
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=alice%20springs,au&units=imperial
    Now fetching city 138 of 600, olafsvik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=olafsvik,is&units=imperial
    Error with city data. Skipping...
    Now fetching city 139 of 600, upernavik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=upernavik,gl&units=imperial
    Now fetching city 140 of 600, chuy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chuy,uy&units=imperial
    Now fetching city 141 of 600, saint-joseph
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-joseph,re&units=imperial
    Now fetching city 142 of 600, kayerkan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kayerkan,ru&units=imperial
    Now fetching city 143 of 600, bambous virieux
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bambous%20virieux,mu&units=imperial
    Now fetching city 144 of 600, saldanha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saldanha,za&units=imperial
    Now fetching city 145 of 600, vestmanna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vestmanna,fo&units=imperial
    Now fetching city 146 of 600, pochutla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pochutla,mx&units=imperial
    Now fetching city 147 of 600, adzope
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=adzope,ci&units=imperial
    Now fetching city 148 of 600, goderich
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=goderich,sl&units=imperial
    Error with city data. Skipping...
    Now fetching city 149 of 600, nortelandia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nortelandia,br&units=imperial
    Now fetching city 150 of 600, belushya guba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=belushya%20guba,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 151 of 600, la reforma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=la%20reforma,mx&units=imperial
    Now fetching city 152 of 600, najran
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=najran,sa&units=imperial
    Now fetching city 153 of 600, maniitsoq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maniitsoq,gl&units=imperial
    Now fetching city 154 of 600, hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hermanus,za&units=imperial
    Now fetching city 155 of 600, abha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=abha,sa&units=imperial
    Now fetching city 156 of 600, micheweni
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=micheweni,tz&units=imperial
    Now fetching city 157 of 600, cayenne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cayenne,gf&units=imperial
    Now fetching city 158 of 600, amderma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=amderma,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 159 of 600, jamestown
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jamestown,sh&units=imperial
    Now fetching city 160 of 600, fortuna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fortuna,us&units=imperial
    Now fetching city 161 of 600, lishui
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lishui,cn&units=imperial
    Now fetching city 162 of 600, salina cruz
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=salina%20cruz,mx&units=imperial
    Now fetching city 163 of 600, mudgee
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mudgee,au&units=imperial
    Now fetching city 164 of 600, lagoa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lagoa,pt&units=imperial
    Now fetching city 165 of 600, skoghall
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=skoghall,se&units=imperial
    Now fetching city 166 of 600, halalo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=halalo,wf&units=imperial
    Error with city data. Skipping...
    Now fetching city 167 of 600, bandarbeyla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bandarbeyla,so&units=imperial
    Now fetching city 168 of 600, metro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=metro,id&units=imperial
    Now fetching city 169 of 600, mayo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mayo,ca&units=imperial
    Now fetching city 170 of 600, stolin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=stolin,by&units=imperial
    Now fetching city 171 of 600, vaitupu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vaitupu,wf&units=imperial
    Error with city data. Skipping...
    Now fetching city 172 of 600, stepnyak
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=stepnyak,kz&units=imperial
    Now fetching city 173 of 600, otradnoye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=otradnoye,ru&units=imperial
    Now fetching city 174 of 600, hami
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hami,cn&units=imperial
    Now fetching city 175 of 600, bredasdorp
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bredasdorp,za&units=imperial
    Now fetching city 176 of 600, dubti
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dubti,et&units=imperial
    Now fetching city 177 of 600, noumea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=noumea,nc&units=imperial
    Now fetching city 178 of 600, morro bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=morro%20bay,us&units=imperial
    Now fetching city 179 of 600, coihaique
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=coihaique,cl&units=imperial
    Now fetching city 180 of 600, kapit
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kapit,my&units=imperial
    Now fetching city 181 of 600, tiksi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tiksi,ru&units=imperial
    Now fetching city 182 of 600, marsa matruh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marsa%20matruh,eg&units=imperial
    Now fetching city 183 of 600, luderitz
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=luderitz,na&units=imperial
    Now fetching city 184 of 600, grand river south east
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grand%20river%20south%20east,mu&units=imperial
    Error with city data. Skipping...
    Now fetching city 185 of 600, morehead
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=morehead,pg&units=imperial
    Now fetching city 186 of 600, tupik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tupik,ru&units=imperial
    Now fetching city 187 of 600, saskylakh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saskylakh,ru&units=imperial
    Now fetching city 188 of 600, muzhi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=muzhi,ru&units=imperial
    Now fetching city 189 of 600, grand-santi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grand-santi,gf&units=imperial
    Now fetching city 190 of 600, marcona
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marcona,pe&units=imperial
    Error with city data. Skipping...
    Now fetching city 191 of 600, sanchor
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sanchor,in&units=imperial
    Now fetching city 192 of 600, miyako
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=miyako,jp&units=imperial
    Now fetching city 193 of 600, norman wells
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=norman%20wells,ca&units=imperial
    Now fetching city 194 of 600, biltine
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=biltine,td&units=imperial
    Now fetching city 195 of 600, black river
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=black%20river,jm&units=imperial
    Now fetching city 196 of 600, hauterive
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hauterive,ca&units=imperial
    Now fetching city 197 of 600, kenai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kenai,us&units=imperial
    Now fetching city 198 of 600, carnarvon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=carnarvon,au&units=imperial
    Now fetching city 199 of 600, cockburn town
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cockburn%20town,tc&units=imperial
    Now fetching city 200 of 600, ustyuzhna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ustyuzhna,ru&units=imperial
    Now fetching city 201 of 600, san jose
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20jose,gt&units=imperial
    Now fetching city 202 of 600, sept-iles
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sept-iles,ca&units=imperial
    Now fetching city 203 of 600, zaria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zaria,ng&units=imperial
    Now fetching city 204 of 600, lebu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lebu,cl&units=imperial
    Now fetching city 205 of 600, kolokani
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kolokani,ml&units=imperial
    Now fetching city 206 of 600, vostok
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vostok,ru&units=imperial
    Now fetching city 207 of 600, tessalit
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tessalit,ml&units=imperial
    Now fetching city 208 of 600, paragominas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=paragominas,br&units=imperial
    Now fetching city 209 of 600, bonavista
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bonavista,ca&units=imperial
    Now fetching city 210 of 600, hamilton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hamilton,us&units=imperial
    Now fetching city 211 of 600, longyearbyen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=longyearbyen,sj&units=imperial
    Now fetching city 212 of 600, mullaitivu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mullaitivu,lk&units=imperial
    Error with city data. Skipping...
    Now fetching city 213 of 600, ukiah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ukiah,us&units=imperial
    Now fetching city 214 of 600, kahului
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kahului,us&units=imperial
    Now fetching city 215 of 600, ahipara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ahipara,nz&units=imperial
    Now fetching city 216 of 600, kielce
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kielce,pl&units=imperial
    Now fetching city 217 of 600, atuona
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=atuona,pf&units=imperial
    Now fetching city 218 of 600, pischia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pischia,ro&units=imperial
    Now fetching city 219 of 600, alofi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=alofi,nu&units=imperial
    Now fetching city 220 of 600, havre-saint-pierre
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=havre-saint-pierre,ca&units=imperial
    Now fetching city 221 of 600, ronne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ronne,dk&units=imperial
    Now fetching city 222 of 600, padang
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=padang,id&units=imperial
    Now fetching city 223 of 600, carquefou
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=carquefou,fr&units=imperial
    Now fetching city 224 of 600, nome
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nome,us&units=imperial
    Now fetching city 225 of 600, malpe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=malpe,in&units=imperial
    Now fetching city 226 of 600, kargasok
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kargasok,ru&units=imperial
    Now fetching city 227 of 600, thayetmyo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=thayetmyo,mm&units=imperial
    Now fetching city 228 of 600, kyren
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kyren,ru&units=imperial
    Now fetching city 229 of 600, kysyl-syr
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kysyl-syr,ru&units=imperial
    Now fetching city 230 of 600, paamiut
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=paamiut,gl&units=imperial
    Now fetching city 231 of 600, victoria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=victoria,sc&units=imperial
    Now fetching city 232 of 600, grand gaube
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grand%20gaube,mu&units=imperial
    Now fetching city 233 of 600, attawapiskat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=attawapiskat,ca&units=imperial
    Error with city data. Skipping...
    Now fetching city 234 of 600, bababe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bababe,mr&units=imperial
    Error with city data. Skipping...
    Now fetching city 235 of 600, caiaponia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=caiaponia,br&units=imperial
    Now fetching city 236 of 600, ola
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ola,ru&units=imperial
    Now fetching city 237 of 600, zhanatas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zhanatas,kz&units=imperial
    Error with city data. Skipping...
    Now fetching city 238 of 600, butembo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=butembo,cd&units=imperial
    Now fetching city 239 of 600, boddam
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=boddam,gb&units=imperial
    Now fetching city 240 of 600, saint anthony
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint%20anthony,ca&units=imperial
    Error with city data. Skipping...
    Now fetching city 241 of 600, narsaq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=narsaq,gl&units=imperial
    Now fetching city 242 of 600, fort nelson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fort%20nelson,ca&units=imperial
    Now fetching city 243 of 600, x-can
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=x-can,mx&units=imperial
    Error with city data. Skipping...
    Now fetching city 244 of 600, kedrovyy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kedrovyy,ru&units=imperial
    Now fetching city 245 of 600, naze
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=naze,jp&units=imperial
    Now fetching city 246 of 600, fairbanks
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fairbanks,us&units=imperial
    Now fetching city 247 of 600, japura
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=japura,br&units=imperial
    Error with city data. Skipping...
    Now fetching city 248 of 600, jyvaskyla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jyvaskyla,fi&units=imperial
    Error with city data. Skipping...
    Now fetching city 249 of 600, ostrovnoy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ostrovnoy,ru&units=imperial
    Now fetching city 250 of 600, souillac
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=souillac,mu&units=imperial
    Now fetching city 251 of 600, ahuimanu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ahuimanu,us&units=imperial
    Now fetching city 252 of 600, baran
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=baran,in&units=imperial
    Now fetching city 253 of 600, akcaabat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=akcaabat,tr&units=imperial
    Now fetching city 254 of 600, kodiak
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kodiak,us&units=imperial
    Now fetching city 255 of 600, sinjai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sinjai,id&units=imperial
    Now fetching city 256 of 600, kamaishi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kamaishi,jp&units=imperial
    Now fetching city 257 of 600, palauig
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=palauig,ph&units=imperial
    Now fetching city 258 of 600, faanui
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=faanui,pf&units=imperial
    Now fetching city 259 of 600, dakar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dakar,sn&units=imperial
    Now fetching city 260 of 600, sao filipe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sao%20filipe,cv&units=imperial
    Now fetching city 261 of 600, bonfim
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bonfim,br&units=imperial
    Now fetching city 262 of 600, nanakuli
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nanakuli,us&units=imperial
    Now fetching city 263 of 600, henties bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=henties%20bay,na&units=imperial
    Now fetching city 264 of 600, gao
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gao,ml&units=imperial
    Now fetching city 265 of 600, leningradskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=leningradskiy,ru&units=imperial
    Now fetching city 266 of 600, sanandaj
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sanandaj,ir&units=imperial
    Now fetching city 267 of 600, galiwinku
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=galiwinku,au&units=imperial
    Error with city data. Skipping...
    Now fetching city 268 of 600, bengkulu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bengkulu,id&units=imperial
    Error with city data. Skipping...
    Now fetching city 269 of 600, kendari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kendari,id&units=imperial
    Now fetching city 270 of 600, nizwa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nizwa,om&units=imperial
    Now fetching city 271 of 600, poya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=poya,nc&units=imperial
    Now fetching city 272 of 600, warqla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=warqla,dz&units=imperial
    Error with city data. Skipping...
    Now fetching city 273 of 600, kirando
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kirando,tz&units=imperial
    Now fetching city 274 of 600, ugoofaaru
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ugoofaaru,mv&units=imperial
    Now fetching city 275 of 600, thano bula khan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=thano%20bula%20khan,pk&units=imperial
    Error with city data. Skipping...
    Now fetching city 276 of 600, zapolyarnyy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zapolyarnyy,ru&units=imperial
    Now fetching city 277 of 600, pisco
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pisco,pe&units=imperial
    Now fetching city 278 of 600, cururupu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cururupu,br&units=imperial
    Now fetching city 279 of 600, ambodifototra
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ambodifototra,mg&units=imperial
    Error with city data. Skipping...
    Now fetching city 280 of 600, harwich
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=harwich,us&units=imperial
    Now fetching city 281 of 600, maltahohe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maltahohe,na&units=imperial
    Now fetching city 282 of 600, odweyne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=odweyne,so&units=imperial
    Error with city data. Skipping...
    Now fetching city 283 of 600, arraial do cabo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=arraial%20do%20cabo,br&units=imperial
    Now fetching city 284 of 600, teluknaga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=teluknaga,id&units=imperial
    Now fetching city 285 of 600, lompoc
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lompoc,us&units=imperial
    Now fetching city 286 of 600, nhulunbuy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nhulunbuy,au&units=imperial
    Now fetching city 287 of 600, smolenka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=smolenka,ru&units=imperial
    Now fetching city 288 of 600, nikolayevka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nikolayevka,ru&units=imperial
    Now fetching city 289 of 600, port elizabeth
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20elizabeth,za&units=imperial
    Now fetching city 290 of 600, namibe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=namibe,ao&units=imperial
    Now fetching city 291 of 600, ilulissat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ilulissat,gl&units=imperial
    Now fetching city 292 of 600, touros
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=touros,br&units=imperial
    Now fetching city 293 of 600, gremyachye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gremyachye,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 294 of 600, santa barbara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=santa%20barbara,mx&units=imperial
    Now fetching city 295 of 600, mountain home
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mountain%20home,us&units=imperial
    Now fetching city 296 of 600, archidona
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=archidona,es&units=imperial
    Now fetching city 297 of 600, laguna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=laguna,br&units=imperial
    Error with city data. Skipping...
    Now fetching city 298 of 600, san cristobal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20cristobal,ec&units=imperial
    Now fetching city 299 of 600, mareeba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mareeba,au&units=imperial
    Now fetching city 300 of 600, magway
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=magway,mm&units=imperial
    Now fetching city 301 of 600, te anau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=te%20anau,nz&units=imperial
    Now fetching city 302 of 600, benjamin constant
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=benjamin%20constant,br&units=imperial
    Now fetching city 303 of 600, oia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=oia,gr&units=imperial
    Error with city data. Skipping...
    Now fetching city 304 of 600, mar del plata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mar%20del%20plata,ar&units=imperial
    Now fetching city 305 of 600, yankton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yankton,us&units=imperial
    Now fetching city 306 of 600, san quintin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20quintin,mx&units=imperial
    Error with city data. Skipping...
    Now fetching city 307 of 600, okhotsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=okhotsk,ru&units=imperial
    Now fetching city 308 of 600, khani
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=khani,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 309 of 600, pangnirtung
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pangnirtung,ca&units=imperial
    Now fetching city 310 of 600, sunrise
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sunrise,us&units=imperial
    Now fetching city 311 of 600, tierralta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tierralta,co&units=imperial
    Now fetching city 312 of 600, wharton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=wharton,us&units=imperial
    Now fetching city 313 of 600, almansa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=almansa,es&units=imperial
    Now fetching city 314 of 600, kaitangata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kaitangata,nz&units=imperial
    Now fetching city 315 of 600, chokurdakh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chokurdakh,ru&units=imperial
    Now fetching city 316 of 600, presidencia roque saenz pena
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=presidencia%20roque%20saenz%20pena,ar&units=imperial
    Now fetching city 317 of 600, shirokiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shirokiy,ru&units=imperial
    Now fetching city 318 of 600, sibolga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sibolga,id&units=imperial
    Now fetching city 319 of 600, mys shmidta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mys%20shmidta,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 320 of 600, westport
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=westport,ie&units=imperial
    Now fetching city 321 of 600, moussoro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=moussoro,td&units=imperial
    Now fetching city 322 of 600, itarema
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=itarema,br&units=imperial
    Now fetching city 323 of 600, chicama
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chicama,pe&units=imperial
    Now fetching city 324 of 600, sitka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sitka,us&units=imperial
    Now fetching city 325 of 600, abu zabad
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=abu%20zabad,sd&units=imperial
    Now fetching city 326 of 600, hailar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hailar,cn&units=imperial
    Now fetching city 327 of 600, torbay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=torbay,ca&units=imperial
    Now fetching city 328 of 600, devarkonda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=devarkonda,in&units=imperial
    Now fetching city 329 of 600, dunedin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dunedin,nz&units=imperial
    Now fetching city 330 of 600, elmira
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=elmira,us&units=imperial
    Now fetching city 331 of 600, kiunga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kiunga,pg&units=imperial
    Now fetching city 332 of 600, barawe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barawe,so&units=imperial
    Error with city data. Skipping...
    Now fetching city 333 of 600, karratha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=karratha,au&units=imperial
    Now fetching city 334 of 600, campbell river
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=campbell%20river,ca&units=imperial
    Now fetching city 335 of 600, lata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lata,sb&units=imperial
    Error with city data. Skipping...
    Now fetching city 336 of 600, pekan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pekan,my&units=imperial
    Now fetching city 337 of 600, plotnikovo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=plotnikovo,ru&units=imperial
    Now fetching city 338 of 600, umzimvubu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=umzimvubu,za&units=imperial
    Error with city data. Skipping...
    Now fetching city 339 of 600, mae hong son
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mae%20hong%20son,th&units=imperial
    Now fetching city 340 of 600, batouri
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=batouri,cm&units=imperial
    Now fetching city 341 of 600, mazamari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mazamari,pe&units=imperial
    Now fetching city 342 of 600, utiroa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=utiroa,ki&units=imperial
    Error with city data. Skipping...
    Now fetching city 343 of 600, barbar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barbar,sd&units=imperial
    Error with city data. Skipping...
    Now fetching city 344 of 600, mirnyy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mirnyy,ru&units=imperial
    Now fetching city 345 of 600, la ronge
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=la%20ronge,ca&units=imperial
    Now fetching city 346 of 600, havoysund
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=havoysund,no&units=imperial
    Now fetching city 347 of 600, svecha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=svecha,ru&units=imperial
    Now fetching city 348 of 600, jodar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jodar,es&units=imperial
    Now fetching city 349 of 600, praia da vitoria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=praia%20da%20vitoria,pt&units=imperial
    Now fetching city 350 of 600, tateyama
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tateyama,jp&units=imperial
    Now fetching city 351 of 600, tadine
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tadine,nc&units=imperial
    Now fetching city 352 of 600, balkanabat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=balkanabat,tm&units=imperial
    Now fetching city 353 of 600, terme
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=terme,tr&units=imperial
    Now fetching city 354 of 600, pervomayskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pervomayskoye,ru&units=imperial
    Now fetching city 355 of 600, houston
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=houston,ca&units=imperial
    Now fetching city 356 of 600, quatre cocos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=quatre%20cocos,mu&units=imperial
    Now fetching city 357 of 600, barcelona
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barcelona,ve&units=imperial
    Now fetching city 358 of 600, cap-aux-meules
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cap-aux-meules,ca&units=imperial
    Now fetching city 359 of 600, matagami
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=matagami,ca&units=imperial
    Now fetching city 360 of 600, bilma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bilma,ne&units=imperial
    Now fetching city 361 of 600, rosetta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rosetta,eg&units=imperial
    Now fetching city 362 of 600, tuktoyaktuk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tuktoyaktuk,ca&units=imperial
    Now fetching city 363 of 600, kupang
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kupang,id&units=imperial
    Now fetching city 364 of 600, villamontes
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=villamontes,bo&units=imperial
    Now fetching city 365 of 600, mahebourg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mahebourg,mu&units=imperial
    Now fetching city 366 of 600, nemuro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nemuro,jp&units=imperial
    Now fetching city 367 of 600, qiongshan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=qiongshan,cn&units=imperial
    Now fetching city 368 of 600, fukue
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fukue,jp&units=imperial
    Now fetching city 369 of 600, togul
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=togul,ru&units=imperial
    Now fetching city 370 of 600, tumannyy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tumannyy,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 371 of 600, harbour breton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=harbour%20breton,ca&units=imperial
    Now fetching city 372 of 600, tromso
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tromso,no&units=imperial
    Now fetching city 373 of 600, palmer
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=palmer,us&units=imperial
    Now fetching city 374 of 600, karlskrona
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=karlskrona,se&units=imperial
    Now fetching city 375 of 600, esil
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=esil,kz&units=imperial
    Now fetching city 376 of 600, altoona
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=altoona,us&units=imperial
    Now fetching city 377 of 600, nouakchott
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nouakchott,mr&units=imperial
    Now fetching city 378 of 600, murud
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=murud,in&units=imperial
    Error with city data. Skipping...
    Now fetching city 379 of 600, san andres
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20andres,co&units=imperial
    Now fetching city 380 of 600, tabiauea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tabiauea,ki&units=imperial
    Error with city data. Skipping...
    Now fetching city 381 of 600, kathmandu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kathmandu,np&units=imperial
    Now fetching city 382 of 600, san juan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20juan,ar&units=imperial
    Now fetching city 383 of 600, zvishavane
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zvishavane,zw&units=imperial
    Now fetching city 384 of 600, dingle
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dingle,ie&units=imperial
    Now fetching city 385 of 600, shonguy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shonguy,ru&units=imperial
    Now fetching city 386 of 600, viligili
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=viligili,mv&units=imperial
    Error with city data. Skipping...
    Now fetching city 387 of 600, burica
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=burica,pa&units=imperial
    Error with city data. Skipping...
    Now fetching city 388 of 600, yakeshi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yakeshi,cn&units=imperial
    Now fetching city 389 of 600, brenham
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=brenham,us&units=imperial
    Now fetching city 390 of 600, lorengau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lorengau,pg&units=imperial
    Now fetching city 391 of 600, chernyshevskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chernyshevskiy,ru&units=imperial
    Now fetching city 392 of 600, hervey bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hervey%20bay,au&units=imperial
    Now fetching city 393 of 600, tres arroyos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tres%20arroyos,ar&units=imperial
    Now fetching city 394 of 600, conceicao do araguaia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=conceicao%20do%20araguaia,br&units=imperial
    Now fetching city 395 of 600, san miguel
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20miguel,pe&units=imperial
    Now fetching city 396 of 600, urumqi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=urumqi,cn&units=imperial
    Error with city data. Skipping...
    Now fetching city 397 of 600, itoman
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=itoman,jp&units=imperial
    Now fetching city 398 of 600, plettenberg bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=plettenberg%20bay,za&units=imperial
    Now fetching city 399 of 600, warrnambool
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=warrnambool,au&units=imperial
    Now fetching city 400 of 600, karak
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=karak,pk&units=imperial
    Now fetching city 401 of 600, zurrieq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zurrieq,mt&units=imperial
    Now fetching city 402 of 600, opuwo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=opuwo,na&units=imperial
    Now fetching city 403 of 600, kanigoro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kanigoro,id&units=imperial
    Now fetching city 404 of 600, belaya gora
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=belaya%20gora,ru&units=imperial
    Now fetching city 405 of 600, kununurra
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kununurra,au&units=imperial
    Now fetching city 406 of 600, adre
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=adre,td&units=imperial
    Now fetching city 407 of 600, bilibino
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bilibino,ru&units=imperial
    Now fetching city 408 of 600, sento se
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sento%20se,br&units=imperial
    Error with city data. Skipping...
    Now fetching city 409 of 600, sampit
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sampit,id&units=imperial
    Now fetching city 410 of 600, lamu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lamu,ke&units=imperial
    Now fetching city 411 of 600, constitucion
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=constitucion,mx&units=imperial
    Now fetching city 412 of 600, kalur kot
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kalur%20kot,pk&units=imperial
    Now fetching city 413 of 600, komsomolskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=komsomolskiy,ru&units=imperial
    Now fetching city 414 of 600, hitoyoshi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hitoyoshi,jp&units=imperial
    Now fetching city 415 of 600, yenagoa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yenagoa,ng&units=imperial
    Now fetching city 416 of 600, acatic
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=acatic,mx&units=imperial
    Now fetching city 417 of 600, caravelas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=caravelas,br&units=imperial
    Now fetching city 418 of 600, bijela
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bijela,ba&units=imperial
    Now fetching city 419 of 600, homer
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=homer,us&units=imperial
    Now fetching city 420 of 600, avera
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=avera,pf&units=imperial
    Error with city data. Skipping...
    Now fetching city 421 of 600, meyungs
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=meyungs,pw&units=imperial
    Error with city data. Skipping...
    Now fetching city 422 of 600, jiddah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jiddah,sa&units=imperial
    Error with city data. Skipping...
    Now fetching city 423 of 600, yershov
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yershov,ru&units=imperial
    Now fetching city 424 of 600, koygorodok
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=koygorodok,ru&units=imperial
    Now fetching city 425 of 600, ekhabi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ekhabi,ru&units=imperial
    Now fetching city 426 of 600, sturgis
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sturgis,us&units=imperial
    Now fetching city 427 of 600, esperance
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=esperance,au&units=imperial
    Now fetching city 428 of 600, curumani
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=curumani,co&units=imperial
    Now fetching city 429 of 600, gamba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gamba,ga&units=imperial
    Now fetching city 430 of 600, saint-augustin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-augustin,ca&units=imperial
    Now fetching city 431 of 600, letlhakane
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=letlhakane,bw&units=imperial
    Now fetching city 432 of 600, kungurtug
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kungurtug,ru&units=imperial
    Now fetching city 433 of 600, bayan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bayan,kw&units=imperial
    Now fetching city 434 of 600, varnavino
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=varnavino,ru&units=imperial
    Now fetching city 435 of 600, bolungarvik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bolungarvik,is&units=imperial
    Error with city data. Skipping...
    Now fetching city 436 of 600, smithers
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=smithers,ca&units=imperial
    Now fetching city 437 of 600, clonakilty
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=clonakilty,ie&units=imperial
    Now fetching city 438 of 600, la libertad
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=la%20libertad,sv&units=imperial
    Now fetching city 439 of 600, waddan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=waddan,ly&units=imperial
    Now fetching city 440 of 600, prince rupert
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=prince%20rupert,ca&units=imperial
    Now fetching city 441 of 600, asau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=asau,tv&units=imperial
    Error with city data. Skipping...
    Now fetching city 442 of 600, bamboo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bamboo,jm&units=imperial
    Now fetching city 443 of 600, port lincoln
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20lincoln,au&units=imperial
    Now fetching city 444 of 600, faya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=faya,td&units=imperial
    Error with city data. Skipping...
    Now fetching city 445 of 600, tautira
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tautira,pf&units=imperial
    Now fetching city 446 of 600, kavieng
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kavieng,pg&units=imperial
    Now fetching city 447 of 600, simpang rengam
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=simpang%20rengam,my&units=imperial
    Error with city data. Skipping...
    Now fetching city 448 of 600, kyshtovka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kyshtovka,ru&units=imperial
    Now fetching city 449 of 600, skovorodino
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=skovorodino,ru&units=imperial
    Now fetching city 450 of 600, licata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=licata,it&units=imperial
    Now fetching city 451 of 600, mutsamudu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mutsamudu,km&units=imperial
    Error with city data. Skipping...
    Now fetching city 452 of 600, bucerias
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bucerias,mx&units=imperial
    Now fetching city 453 of 600, nampula
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nampula,mz&units=imperial
    Now fetching city 454 of 600, chulman
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chulman,ru&units=imperial
    Now fetching city 455 of 600, uray
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=uray,ru&units=imperial
    Now fetching city 456 of 600, port-gentil
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port-gentil,ga&units=imperial
    Now fetching city 457 of 600, clarence town
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=clarence%20town,bs&units=imperial
    Now fetching city 458 of 600, joshimath
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=joshimath,in&units=imperial
    Now fetching city 459 of 600, andenes
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=andenes,no&units=imperial
    Now fetching city 460 of 600, kruisfontein
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kruisfontein,za&units=imperial
    Now fetching city 461 of 600, west lafayette
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=west%20lafayette,us&units=imperial
    Now fetching city 462 of 600, zhuhai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zhuhai,cn&units=imperial
    Now fetching city 463 of 600, araouane
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=araouane,ml&units=imperial
    Now fetching city 464 of 600, teacapan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=teacapan,mx&units=imperial
    Now fetching city 465 of 600, uruzgan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=uruzgan,af&units=imperial
    Now fetching city 466 of 600, coquimbo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=coquimbo,cl&units=imperial
    Now fetching city 467 of 600, sorvag
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sorvag,fo&units=imperial
    Error with city data. Skipping...
    Now fetching city 468 of 600, charlestown
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=charlestown,us&units=imperial
    Now fetching city 469 of 600, acapulco
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=acapulco,mx&units=imperial
    Now fetching city 470 of 600, ondorhaan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ondorhaan,mn&units=imperial
    Error with city data. Skipping...
    Now fetching city 471 of 600, yumen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yumen,cn&units=imperial
    Now fetching city 472 of 600, toliary
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=toliary,mg&units=imperial
    Error with city data. Skipping...
    Now fetching city 473 of 600, termiz
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=termiz,uz&units=imperial
    Error with city data. Skipping...
    Now fetching city 474 of 600, mahajanga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mahajanga,mg&units=imperial
    Now fetching city 475 of 600, two hills
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=two%20hills,ca&units=imperial
    Now fetching city 476 of 600, nizhniy kuranakh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nizhniy%20kuranakh,ru&units=imperial
    Now fetching city 477 of 600, saint-francois
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-francois,gp&units=imperial
    Now fetching city 478 of 600, turukhansk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=turukhansk,ru&units=imperial
    Now fetching city 479 of 600, diapaga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=diapaga,bf&units=imperial
    Now fetching city 480 of 600, marsh harbour
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marsh%20harbour,bs&units=imperial
    Now fetching city 481 of 600, srednekolymsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=srednekolymsk,ru&units=imperial
    Now fetching city 482 of 600, lages
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lages,br&units=imperial
    Now fetching city 483 of 600, mircea voda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mircea%20voda,ro&units=imperial
    Now fetching city 484 of 600, akureyri
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=akureyri,is&units=imperial
    Now fetching city 485 of 600, sumbe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sumbe,ao&units=imperial
    Now fetching city 486 of 600, suntar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=suntar,ru&units=imperial
    Now fetching city 487 of 600, hurricane
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hurricane,us&units=imperial
    Now fetching city 488 of 600, nuuk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nuuk,gl&units=imperial
    Now fetching city 489 of 600, mayna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mayna,ru&units=imperial
    Now fetching city 490 of 600, wagga wagga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=wagga%20wagga,au&units=imperial
    Now fetching city 491 of 600, grootfontein
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grootfontein,na&units=imperial
    Now fetching city 492 of 600, leh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=leh,in&units=imperial
    Now fetching city 493 of 600, zaysan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zaysan,kz&units=imperial
    Now fetching city 494 of 600, alexandria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=alexandria,eg&units=imperial
    Now fetching city 495 of 600, black forest
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=black%20forest,us&units=imperial
    Now fetching city 496 of 600, otane
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=otane,nz&units=imperial
    Now fetching city 497 of 600, chakulia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chakulia,in&units=imperial
    Now fetching city 498 of 600, sentyabrskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sentyabrskiy,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 499 of 600, isangel
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=isangel,vu&units=imperial
    Now fetching city 500 of 600, boali
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=boali,cf&units=imperial
    Now fetching city 501 of 600, hobyo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hobyo,so&units=imperial
    Now fetching city 502 of 600, newport
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=newport,us&units=imperial
    Now fetching city 503 of 600, burns lake
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=burns%20lake,ca&units=imperial
    Now fetching city 504 of 600, komsomolsk-na-amure
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=komsomolsk-na-amure,ru&units=imperial
    Now fetching city 505 of 600, azare
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=azare,ng&units=imperial
    Now fetching city 506 of 600, balgazyn
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=balgazyn,ru&units=imperial
    Now fetching city 507 of 600, san pedro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20pedro,bo&units=imperial
    Now fetching city 508 of 600, melilla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=melilla,es&units=imperial
    Now fetching city 509 of 600, airai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=airai,pw&units=imperial
    Error with city data. Skipping...
    Now fetching city 510 of 600, provideniya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=provideniya,ru&units=imperial
    Now fetching city 511 of 600, skalistyy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=skalistyy,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 512 of 600, hofn
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hofn,is&units=imperial
    Now fetching city 513 of 600, manggar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=manggar,id&units=imperial
    Now fetching city 514 of 600, gizo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gizo,sb&units=imperial
    Now fetching city 515 of 600, flin flon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=flin%20flon,ca&units=imperial
    Now fetching city 516 of 600, north bend
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=north%20bend,us&units=imperial
    Now fetching city 517 of 600, balakhta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=balakhta,ru&units=imperial
    Now fetching city 518 of 600, severobaykalsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=severobaykalsk,ru&units=imperial
    Now fetching city 519 of 600, xuanzhou
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=xuanzhou,cn&units=imperial
    Now fetching city 520 of 600, tyukhtet
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tyukhtet,ru&units=imperial
    Now fetching city 521 of 600, bourg-en-bresse
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bourg-en-bresse,fr&units=imperial
    Now fetching city 522 of 600, qostanay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=qostanay,kz&units=imperial
    Now fetching city 523 of 600, kushmurun
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kushmurun,kz&units=imperial
    Error with city data. Skipping...
    Now fetching city 524 of 600, bar-le-duc
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bar-le-duc,fr&units=imperial
    Now fetching city 525 of 600, umm lajj
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=umm%20lajj,sa&units=imperial
    Now fetching city 526 of 600, durban
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=durban,za&units=imperial
    Now fetching city 527 of 600, balabac
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=balabac,ph&units=imperial
    Now fetching city 528 of 600, sakakah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sakakah,sa&units=imperial
    Error with city data. Skipping...
    Now fetching city 529 of 600, phibun mangsahan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=phibun%20mangsahan,th&units=imperial
    Now fetching city 530 of 600, borogontsy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=borogontsy,ru&units=imperial
    Now fetching city 531 of 600, les cayes
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=les%20cayes,ht&units=imperial
    Now fetching city 532 of 600, dubenskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dubenskiy,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 533 of 600, boffa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=boffa,gn&units=imperial
    Now fetching city 534 of 600, santiago del estero
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=santiago%20del%20estero,ar&units=imperial
    Now fetching city 535 of 600, maine-soroa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maine-soroa,ne&units=imperial
    Now fetching city 536 of 600, sioux falls
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sioux%20falls,us&units=imperial
    Now fetching city 537 of 600, kholodnyy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kholodnyy,ru&units=imperial
    Now fetching city 538 of 600, port hedland
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20hedland,au&units=imperial
    Now fetching city 539 of 600, sinnamary
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sinnamary,gf&units=imperial
    Now fetching city 540 of 600, maragogi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maragogi,br&units=imperial
    Now fetching city 541 of 600, mandalgovi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mandalgovi,mn&units=imperial
    Now fetching city 542 of 600, labuhan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=labuhan,id&units=imperial
    Now fetching city 543 of 600, iqaluit
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=iqaluit,ca&units=imperial
    Now fetching city 544 of 600, guarapari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=guarapari,br&units=imperial
    Now fetching city 545 of 600, fort wayne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fort%20wayne,us&units=imperial
    Now fetching city 546 of 600, kalanaur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kalanaur,in&units=imperial
    Now fetching city 547 of 600, lemesos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lemesos,cy&units=imperial
    Error with city data. Skipping...
    Now fetching city 548 of 600, gorakhpur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gorakhpur,in&units=imperial
    Now fetching city 549 of 600, pacifica
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pacifica,us&units=imperial
    Now fetching city 550 of 600, poso
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=poso,id&units=imperial
    Now fetching city 551 of 600, kogon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kogon,uz&units=imperial
    Now fetching city 552 of 600, kuandian
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kuandian,cn&units=imperial
    Now fetching city 553 of 600, vestmannaeyjar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vestmannaeyjar,is&units=imperial
    Now fetching city 554 of 600, ayotzintepec
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ayotzintepec,mx&units=imperial
    Now fetching city 555 of 600, luwuk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=luwuk,id&units=imperial
    Now fetching city 556 of 600, korem
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=korem,et&units=imperial
    Now fetching city 557 of 600, berlevag
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=berlevag,no&units=imperial
    Now fetching city 558 of 600, peque
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=peque,co&units=imperial
    Now fetching city 559 of 600, nieuw amsterdam
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nieuw%20amsterdam,sr&units=imperial
    Now fetching city 560 of 600, coos bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=coos%20bay,us&units=imperial
    Now fetching city 561 of 600, atar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=atar,mr&units=imperial
    Now fetching city 562 of 600, mbini
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mbini,gq&units=imperial
    Now fetching city 563 of 600, lolua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lolua,tv&units=imperial
    Error with city data. Skipping...
    Now fetching city 564 of 600, hastings
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hastings,us&units=imperial
    Now fetching city 565 of 600, sur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sur,om&units=imperial
    Now fetching city 566 of 600, port augusta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20augusta,au&units=imperial
    Now fetching city 567 of 600, portland
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=portland,au&units=imperial
    Now fetching city 568 of 600, waterbury
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=waterbury,us&units=imperial
    Now fetching city 569 of 600, hambantota
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hambantota,lk&units=imperial
    Now fetching city 570 of 600, petropavlovsk-kamchatskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=petropavlovsk-kamchatskiy,ru&units=imperial
    Now fetching city 571 of 600, akersberga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=akersberga,se&units=imperial
    Now fetching city 572 of 600, bam
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bam,ir&units=imperial
    Now fetching city 573 of 600, ujjain
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ujjain,in&units=imperial
    Now fetching city 574 of 600, raymondville
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=raymondville,us&units=imperial
    Now fetching city 575 of 600, muroto
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=muroto,jp&units=imperial
    Now fetching city 576 of 600, meulaboh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=meulaboh,id&units=imperial
    Now fetching city 577 of 600, pilar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pilar,ph&units=imperial
    Now fetching city 578 of 600, maridi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maridi,sd&units=imperial
    Error with city data. Skipping...
    Now fetching city 579 of 600, alandur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=alandur,in&units=imperial
    Now fetching city 580 of 600, rocha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rocha,uy&units=imperial
    Now fetching city 581 of 600, semey
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=semey,kz&units=imperial
    Now fetching city 582 of 600, san patricio
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20patricio,mx&units=imperial
    Now fetching city 583 of 600, kieta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kieta,pg&units=imperial
    Now fetching city 584 of 600, labrea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=labrea,br&units=imperial
    Error with city data. Skipping...
    Now fetching city 585 of 600, severo-yeniseyskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=severo-yeniseyskiy,ru&units=imperial
    Now fetching city 586 of 600, aswan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aswan,eg&units=imperial
    Now fetching city 587 of 600, pineville
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pineville,us&units=imperial
    Now fetching city 588 of 600, sungairaya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sungairaya,id&units=imperial
    Now fetching city 589 of 600, ampanihy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ampanihy,mg&units=imperial
    Now fetching city 590 of 600, pucara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pucara,pe&units=imperial
    Now fetching city 591 of 600, podor
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=podor,sn&units=imperial
    Error with city data. Skipping...
    Now fetching city 592 of 600, porto belo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=porto%20belo,br&units=imperial
    Now fetching city 593 of 600, derzhavinsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=derzhavinsk,kz&units=imperial
    Now fetching city 594 of 600, aquin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aquin,ht&units=imperial
    Now fetching city 595 of 600, saleaula
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saleaula,ws&units=imperial
    Error with city data. Skipping...
    Now fetching city 596 of 600, carahue
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=carahue,cl&units=imperial
    Now fetching city 597 of 600, berbera
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=berbera,so&units=imperial
    Now fetching city 598 of 600, erenhot
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=erenhot,cn&units=imperial
    Now fetching city 599 of 600, dabat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dabat,et&units=imperial
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


```python
#create latitude vs. temperature scatterplot
plt.scatter(city_weather_df["Actual Latitude"], city_weather_df["Temperature (F)"], marker="o", 
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
plt.scatter(city_weather_df["Actual Latitude"], city_weather_df["Humidity %"], marker="o",
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
plt.scatter(city_weather_df["Actual Latitude"], city_weather_df["Cloud Cover %"], marker="o",
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
plt.scatter(city_weather_df["Actual Latitude"], city_weather_df["Wind Speed (MPH)"], marker="o",
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



```python
plt.scatter(city_weather_df["Actual Longitude"], city_weather_df["Actual Latitude"], marker="o",
           color="b", edgecolor="black", alpha=.8, label="City")
plt.xlim(-200, 200)
plt.ylim(-100, 100)
plt.xlabel("Latitude")
plt.ylabel("Longitude")
plt.title("Map Points")
plt.grid(True)
plt.show()
#plt.savefig("City Latitude vs. Wind Speed 4-1-2018.png")
```


![png](output_7_0.png)

