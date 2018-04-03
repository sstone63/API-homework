

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
      <td>dukat</td>
      <td>ru</td>
      <td>64.030611</td>
      <td>154.812652</td>
    </tr>
    <tr>
      <th>1</th>
      <td>kamenskoye</td>
      <td>ru</td>
      <td>63.414318</td>
      <td>171.630188</td>
    </tr>
    <tr>
      <th>2</th>
      <td>vostok</td>
      <td>ru</td>
      <td>47.873714</td>
      <td>150.200983</td>
    </tr>
    <tr>
      <th>3</th>
      <td>gardenstown</td>
      <td>gb</td>
      <td>57.718204</td>
      <td>-2.232948</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tautira</td>
      <td>pf</td>
      <td>-15.890678</td>
      <td>-144.162622</td>
    </tr>
    <tr>
      <th>5</th>
      <td>albany</td>
      <td>au</td>
      <td>-87.374633</td>
      <td>98.054353</td>
    </tr>
    <tr>
      <th>6</th>
      <td>viligili</td>
      <td>mv</td>
      <td>0.226437</td>
      <td>76.627752</td>
    </tr>
    <tr>
      <th>7</th>
      <td>constitucion</td>
      <td>mx</td>
      <td>16.467982</td>
      <td>-120.500935</td>
    </tr>
    <tr>
      <th>8</th>
      <td>sakakah</td>
      <td>sa</td>
      <td>31.132817</td>
      <td>40.217157</td>
    </tr>
    <tr>
      <th>9</th>
      <td>vaini</td>
      <td>to</td>
      <td>-64.613750</td>
      <td>-179.916852</td>
    </tr>
    <tr>
      <th>10</th>
      <td>caibarien</td>
      <td>cu</td>
      <td>23.499015</td>
      <td>-78.678551</td>
    </tr>
    <tr>
      <th>11</th>
      <td>atuona</td>
      <td>pf</td>
      <td>-7.731670</td>
      <td>-116.788392</td>
    </tr>
    <tr>
      <th>12</th>
      <td>samarai</td>
      <td>pg</td>
      <td>-13.350414</td>
      <td>148.458550</td>
    </tr>
    <tr>
      <th>13</th>
      <td>kapaa</td>
      <td>us</td>
      <td>21.633249</td>
      <td>-164.574980</td>
    </tr>
    <tr>
      <th>14</th>
      <td>egvekinot</td>
      <td>ru</td>
      <td>66.906619</td>
      <td>-176.593118</td>
    </tr>
    <tr>
      <th>15</th>
      <td>college</td>
      <td>us</td>
      <td>75.967559</td>
      <td>-146.587976</td>
    </tr>
    <tr>
      <th>16</th>
      <td>kahului</td>
      <td>us</td>
      <td>35.769037</td>
      <td>-144.825297</td>
    </tr>
    <tr>
      <th>17</th>
      <td>khatanga</td>
      <td>ru</td>
      <td>83.288781</td>
      <td>97.374700</td>
    </tr>
    <tr>
      <th>18</th>
      <td>nouadhibou</td>
      <td>mr</td>
      <td>20.584562</td>
      <td>-16.408619</td>
    </tr>
    <tr>
      <th>19</th>
      <td>busselton</td>
      <td>au</td>
      <td>-81.082529</td>
      <td>78.072388</td>
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
        city_df.set_value(index, "Latitude", actual_latitude)
        city_df.set_value(index, "Longitude", actual_longitude)
        
    
    #error exceptions for missing values
    except (KeyError, IndexError):
        print("Error with city data. Skipping...")
        
print("--------------------------")
print("Data Retrieval Complete")
print("--------------------------")


```

    Commencing Data Retrieval
    --------------------------
    Now fetching city 0 of 600, dukat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dukat,ru&units=imperial
    Now fetching city 1 of 600, kamenskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kamenskoye,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 2 of 600, vostok
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vostok,ru&units=imperial
    Now fetching city 3 of 600, gardenstown
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gardenstown,gb&units=imperial
    Now fetching city 4 of 600, tautira
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tautira,pf&units=imperial
    Now fetching city 5 of 600, albany
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=albany,au&units=imperial
    Now fetching city 6 of 600, viligili
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=viligili,mv&units=imperial
    Error with city data. Skipping...
    Now fetching city 7 of 600, constitucion
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=constitucion,mx&units=imperial
    Now fetching city 8 of 600, sakakah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sakakah,sa&units=imperial
    Error with city data. Skipping...
    Now fetching city 9 of 600, vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vaini,to&units=imperial
    Now fetching city 10 of 600, caibarien
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=caibarien,cu&units=imperial
    Now fetching city 11 of 600, atuona
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=atuona,pf&units=imperial
    Now fetching city 12 of 600, samarai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=samarai,pg&units=imperial
    Now fetching city 13 of 600, kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kapaa,us&units=imperial
    Now fetching city 14 of 600, egvekinot
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=egvekinot,ru&units=imperial
    Now fetching city 15 of 600, college
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=college,us&units=imperial
    Now fetching city 16 of 600, kahului
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kahului,us&units=imperial
    Now fetching city 17 of 600, khatanga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=khatanga,ru&units=imperial
    Now fetching city 18 of 600, nouadhibou
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nouadhibou,mr&units=imperial
    Now fetching city 19 of 600, busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=busselton,au&units=imperial
    Now fetching city 20 of 600, ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ushuaia,ar&units=imperial
    Now fetching city 21 of 600, arraial do cabo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=arraial%20do%20cabo,br&units=imperial
    Now fetching city 22 of 600, shelburne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shelburne,ca&units=imperial
    Now fetching city 23 of 600, palabuhanratu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=palabuhanratu,id&units=imperial
    Error with city data. Skipping...
    Now fetching city 24 of 600, mys shmidta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mys%20shmidta,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 25 of 600, punta arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=punta%20arenas,cl&units=imperial
    Now fetching city 26 of 600, umm ruwabah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=umm%20ruwabah,sd&units=imperial
    Error with city data. Skipping...
    Now fetching city 27 of 600, lazaro cardenas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lazaro%20cardenas,mx&units=imperial
    Now fetching city 28 of 600, klaksvik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=klaksvik,fo&units=imperial
    Now fetching city 29 of 600, ketchikan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ketchikan,us&units=imperial
    Now fetching city 30 of 600, pedro carbo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pedro%20carbo,ec&units=imperial
    Now fetching city 31 of 600, belushya guba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=belushya%20guba,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 32 of 600, cherskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cherskiy,ru&units=imperial
    Now fetching city 33 of 600, progreso
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=progreso,mx&units=imperial
    Now fetching city 34 of 600, faanui
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=faanui,pf&units=imperial
    Now fetching city 35 of 600, bambous virieux
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bambous%20virieux,mu&units=imperial
    Now fetching city 36 of 600, mbanza-ngungu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mbanza-ngungu,cd&units=imperial
    Now fetching city 37 of 600, taburi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=taburi,ph&units=imperial
    Error with city data. Skipping...
    Now fetching city 38 of 600, innisfail
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=innisfail,au&units=imperial
    Now fetching city 39 of 600, bluff
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bluff,nz&units=imperial
    Now fetching city 40 of 600, dikson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dikson,ru&units=imperial
    Now fetching city 41 of 600, altay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=altay,cn&units=imperial
    Now fetching city 42 of 600, new norfolk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=new%20norfolk,au&units=imperial
    Now fetching city 43 of 600, mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mataura,pf&units=imperial
    Error with city data. Skipping...
    Now fetching city 44 of 600, aripuana
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aripuana,br&units=imperial
    Now fetching city 45 of 600, jamestown
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jamestown,sh&units=imperial
    Now fetching city 46 of 600, marovoay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marovoay,mg&units=imperial
    Now fetching city 47 of 600, alegrete
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=alegrete,br&units=imperial
    Now fetching city 48 of 600, yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yellowknife,ca&units=imperial
    Now fetching city 49 of 600, mar del plata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mar%20del%20plata,ar&units=imperial
    Now fetching city 50 of 600, hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hermanus,za&units=imperial
    Now fetching city 51 of 600, ferme-neuve
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ferme-neuve,ca&units=imperial
    Now fetching city 52 of 600, grand gaube
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grand%20gaube,mu&units=imperial
    Now fetching city 53 of 600, luderitz
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=luderitz,na&units=imperial
    Now fetching city 54 of 600, ewa beach
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ewa%20beach,us&units=imperial
    Now fetching city 55 of 600, amambai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=amambai,br&units=imperial
    Now fetching city 56 of 600, tezu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tezu,in&units=imperial
    Now fetching city 57 of 600, nikolskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nikolskoye,ru&units=imperial
    Now fetching city 58 of 600, puerto colombia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=puerto%20colombia,co&units=imperial
    Now fetching city 59 of 600, kondagaon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kondagaon,in&units=imperial
    Now fetching city 60 of 600, provideniya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=provideniya,ru&units=imperial
    Now fetching city 61 of 600, brae
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=brae,gb&units=imperial
    Now fetching city 62 of 600, grenaa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grenaa,dk&units=imperial
    Now fetching city 63 of 600, iralaya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=iralaya,hn&units=imperial
    Now fetching city 64 of 600, xuddur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=xuddur,so&units=imperial
    Now fetching city 65 of 600, lasa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lasa,cn&units=imperial
    Error with city data. Skipping...
    Now fetching city 66 of 600, warwick
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=warwick,au&units=imperial
    Now fetching city 67 of 600, mawlaik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mawlaik,mm&units=imperial
    Now fetching city 68 of 600, pevek
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pevek,ru&units=imperial
    Now fetching city 69 of 600, port hedland
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20hedland,au&units=imperial
    Now fetching city 70 of 600, katsuura
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=katsuura,jp&units=imperial
    Now fetching city 71 of 600, tiksi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tiksi,ru&units=imperial
    Now fetching city 72 of 600, noumea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=noumea,nc&units=imperial
    Now fetching city 73 of 600, dingle
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dingle,ie&units=imperial
    Now fetching city 74 of 600, thilogne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=thilogne,sn&units=imperial
    Error with city data. Skipping...
    Now fetching city 75 of 600, thompson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=thompson,ca&units=imperial
    Now fetching city 76 of 600, grindavik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grindavik,is&units=imperial
    Now fetching city 77 of 600, baboua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=baboua,cf&units=imperial
    Error with city data. Skipping...
    Now fetching city 78 of 600, mehamn
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mehamn,no&units=imperial
    Now fetching city 79 of 600, port elizabeth
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20elizabeth,za&units=imperial
    Now fetching city 80 of 600, saint george
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint%20george,bm&units=imperial
    Now fetching city 81 of 600, yendi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yendi,gh&units=imperial
    Now fetching city 82 of 600, acapulco
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=acapulco,mx&units=imperial
    Now fetching city 83 of 600, qaanaaq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=qaanaaq,gl&units=imperial
    Now fetching city 84 of 600, sorong
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sorong,id&units=imperial
    Now fetching city 85 of 600, tsihombe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tsihombe,mg&units=imperial
    Error with city data. Skipping...
    Now fetching city 86 of 600, jumla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jumla,np&units=imperial
    Now fetching city 87 of 600, ampanihy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ampanihy,mg&units=imperial
    Now fetching city 88 of 600, hilo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hilo,us&units=imperial
    Now fetching city 89 of 600, sataua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sataua,ws&units=imperial
    Error with city data. Skipping...
    Now fetching city 90 of 600, grand river south east
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=grand%20river%20south%20east,mu&units=imperial
    Error with city data. Skipping...
    Now fetching city 91 of 600, cape town
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cape%20town,za&units=imperial
    Now fetching city 92 of 600, manzhouli
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=manzhouli,cn&units=imperial
    Now fetching city 93 of 600, lebu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lebu,cl&units=imperial
    Now fetching city 94 of 600, castro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=castro,cl&units=imperial
    Now fetching city 95 of 600, aklavik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aklavik,ca&units=imperial
    Now fetching city 96 of 600, te anau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=te%20anau,nz&units=imperial
    Now fetching city 97 of 600, rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rikitea,pf&units=imperial
    Now fetching city 98 of 600, derzhavinsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=derzhavinsk,kz&units=imperial
    Now fetching city 99 of 600, didwana
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=didwana,in&units=imperial
    Now fetching city 100 of 600, twin falls
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=twin%20falls,us&units=imperial
    Now fetching city 101 of 600, sumbe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sumbe,ao&units=imperial
    Now fetching city 102 of 600, saleilua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saleilua,ws&units=imperial
    Error with city data. Skipping...
    Now fetching city 103 of 600, atocha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=atocha,bo&units=imperial
    Now fetching city 104 of 600, bandar-e torkaman
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bandar-e%20torkaman,ir&units=imperial
    Error with city data. Skipping...
    Now fetching city 105 of 600, mikhaylovka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mikhaylovka,kz&units=imperial
    Now fetching city 106 of 600, albion
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=albion,mu&units=imperial
    Now fetching city 107 of 600, taolanaro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=taolanaro,mg&units=imperial
    Error with city data. Skipping...
    Now fetching city 108 of 600, sentyabrskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sentyabrskiy,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 109 of 600, etchoropo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=etchoropo,mx&units=imperial
    Now fetching city 110 of 600, leningradskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=leningradskiy,ru&units=imperial
    Now fetching city 111 of 600, staraya poltavka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=staraya%20poltavka,ru&units=imperial
    Now fetching city 112 of 600, torbay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=torbay,ca&units=imperial
    Now fetching city 113 of 600, kuala pilah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kuala%20pilah,my&units=imperial
    Now fetching city 114 of 600, ngunguru
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ngunguru,nz&units=imperial
    Now fetching city 115 of 600, maun
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maun,bw&units=imperial
    Now fetching city 116 of 600, denpasar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=denpasar,id&units=imperial
    Now fetching city 117 of 600, usinsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=usinsk,ru&units=imperial
    Now fetching city 118 of 600, santa lucia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=santa%20lucia,es&units=imperial
    Now fetching city 119 of 600, port alfred
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20alfred,za&units=imperial
    Now fetching city 120 of 600, sur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sur,om&units=imperial
    Now fetching city 121 of 600, bac lieu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bac%20lieu,vn&units=imperial
    Error with city data. Skipping...
    Now fetching city 122 of 600, cap malheureux
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cap%20malheureux,mu&units=imperial
    Now fetching city 123 of 600, mayumba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mayumba,ga&units=imperial
    Now fetching city 124 of 600, illoqqortoormiut
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=illoqqortoormiut,gl&units=imperial
    Error with city data. Skipping...
    Now fetching city 125 of 600, tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tasiilaq,gl&units=imperial
    Now fetching city 126 of 600, east london
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=east%20london,za&units=imperial
    Now fetching city 127 of 600, havre-saint-pierre
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=havre-saint-pierre,ca&units=imperial
    Now fetching city 128 of 600, victoria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=victoria,sc&units=imperial
    Now fetching city 129 of 600, jian
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=jian,cn&units=imperial
    Now fetching city 130 of 600, saldanha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saldanha,za&units=imperial
    Now fetching city 131 of 600, tuktoyaktuk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tuktoyaktuk,ca&units=imperial
    Now fetching city 132 of 600, acarau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=acarau,br&units=imperial
    Error with city data. Skipping...
    Now fetching city 133 of 600, odweyne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=odweyne,so&units=imperial
    Error with city data. Skipping...
    Now fetching city 134 of 600, chuy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chuy,uy&units=imperial
    Now fetching city 135 of 600, fostoria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fostoria,us&units=imperial
    Now fetching city 136 of 600, chokurdakh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chokurdakh,ru&units=imperial
    Now fetching city 137 of 600, hami
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hami,cn&units=imperial
    Now fetching city 138 of 600, broome
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=broome,au&units=imperial
    Now fetching city 139 of 600, hihifo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hihifo,to&units=imperial
    Error with city data. Skipping...
    Now fetching city 140 of 600, gangotri
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gangotri,in&units=imperial
    Error with city data. Skipping...
    Now fetching city 141 of 600, olafsvik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=olafsvik,is&units=imperial
    Error with city data. Skipping...
    Now fetching city 142 of 600, carnarvon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=carnarvon,au&units=imperial
    Now fetching city 143 of 600, talnakh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=talnakh,ru&units=imperial
    Now fetching city 144 of 600, miyako
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=miyako,jp&units=imperial
    Now fetching city 145 of 600, hilton head island
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hilton%20head%20island,us&units=imperial
    Now fetching city 146 of 600, puerto ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=puerto%20ayora,ec&units=imperial
    Now fetching city 147 of 600, husavik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=husavik,is&units=imperial
    Now fetching city 148 of 600, tunduru
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tunduru,tz&units=imperial
    Error with city data. Skipping...
    Now fetching city 149 of 600, olinda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=olinda,br&units=imperial
    Now fetching city 150 of 600, sitka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sitka,us&units=imperial
    Now fetching city 151 of 600, oxbow
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=oxbow,ca&units=imperial
    Now fetching city 152 of 600, sola
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sola,vu&units=imperial
    Now fetching city 153 of 600, upernavik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=upernavik,gl&units=imperial
    Now fetching city 154 of 600, moerai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=moerai,pf&units=imperial
    Now fetching city 155 of 600, samana
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=samana,in&units=imperial
    Error with city data. Skipping...
    Now fetching city 156 of 600, batemans bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=batemans%20bay,au&units=imperial
    Now fetching city 157 of 600, utete
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=utete,tz&units=imperial
    Now fetching city 158 of 600, ancud
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ancud,cl&units=imperial
    Now fetching city 159 of 600, iqaluit
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=iqaluit,ca&units=imperial
    Now fetching city 160 of 600, mercedes
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mercedes,ar&units=imperial
    Now fetching city 161 of 600, laguna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=laguna,br&units=imperial
    Error with city data. Skipping...
    Now fetching city 162 of 600, coquimbo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=coquimbo,cl&units=imperial
    Now fetching city 163 of 600, kaitangata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kaitangata,nz&units=imperial
    Now fetching city 164 of 600, ambon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ambon,id&units=imperial
    Now fetching city 165 of 600, saskylakh
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saskylakh,ru&units=imperial
    Now fetching city 166 of 600, longyearbyen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=longyearbyen,sj&units=imperial
    Now fetching city 167 of 600, hobart
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hobart,au&units=imperial
    Now fetching city 168 of 600, lagoa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lagoa,pt&units=imperial
    Now fetching city 169 of 600, ruatoria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ruatoria,nz&units=imperial
    Error with city data. Skipping...
    Now fetching city 170 of 600, bengkulu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bengkulu,id&units=imperial
    Error with city data. Skipping...
    Now fetching city 171 of 600, arrecife
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=arrecife,es&units=imperial
    Now fetching city 172 of 600, san patricio
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20patricio,mx&units=imperial
    Now fetching city 173 of 600, fortuna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fortuna,us&units=imperial
    Now fetching city 174 of 600, tieli
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tieli,cn&units=imperial
    Now fetching city 175 of 600, bredasdorp
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bredasdorp,za&units=imperial
    Now fetching city 176 of 600, sikonge
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sikonge,tz&units=imperial
    Now fetching city 177 of 600, geraldton
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=geraldton,au&units=imperial
    Now fetching city 178 of 600, rungata
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rungata,ki&units=imperial
    Error with city data. Skipping...
    Now fetching city 179 of 600, hun
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hun,ly&units=imperial
    Now fetching city 180 of 600, barentsburg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barentsburg,sj&units=imperial
    Error with city data. Skipping...
    Now fetching city 181 of 600, tabiauea
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tabiauea,ki&units=imperial
    Error with city data. Skipping...
    Now fetching city 182 of 600, avarua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=avarua,ck&units=imperial
    Now fetching city 183 of 600, alpena
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=alpena,us&units=imperial
    Now fetching city 184 of 600, kuminskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kuminskiy,ru&units=imperial
    Now fetching city 185 of 600, ngukurr
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ngukurr,au&units=imperial
    Error with city data. Skipping...
    Now fetching city 186 of 600, praia da vitoria
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=praia%20da%20vitoria,pt&units=imperial
    Now fetching city 187 of 600, chabahar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chabahar,ir&units=imperial
    Now fetching city 188 of 600, qiongshan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=qiongshan,cn&units=imperial
    Now fetching city 189 of 600, deputatskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=deputatskiy,ru&units=imperial
    Now fetching city 190 of 600, kopyevo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kopyevo,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 191 of 600, padang
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=padang,id&units=imperial
    Now fetching city 192 of 600, ituporanga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ituporanga,br&units=imperial
    Now fetching city 193 of 600, eyrarbakki
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=eyrarbakki,is&units=imperial
    Now fetching city 194 of 600, luganville
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=luganville,vu&units=imperial
    Now fetching city 195 of 600, normandin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=normandin,ca&units=imperial
    Now fetching city 196 of 600, dong hoi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dong%20hoi,vn&units=imperial
    Now fetching city 197 of 600, aswan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aswan,eg&units=imperial
    Now fetching city 198 of 600, ostersund
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ostersund,se&units=imperial
    Now fetching city 199 of 600, shangqiu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shangqiu,cn&units=imperial
    Now fetching city 200 of 600, buta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=buta,cd&units=imperial
    Now fetching city 201 of 600, kavaratti
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kavaratti,in&units=imperial
    Now fetching city 202 of 600, ha giang
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ha%20giang,vn&units=imperial
    Now fetching city 203 of 600, barrow
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=barrow,us&units=imperial
    Now fetching city 204 of 600, burica
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=burica,pa&units=imperial
    Error with city data. Skipping...
    Now fetching city 205 of 600, attawapiskat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=attawapiskat,ca&units=imperial
    Error with city data. Skipping...
    Now fetching city 206 of 600, harper
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=harper,lr&units=imperial
    Now fetching city 207 of 600, severo-kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=severo-kurilsk,ru&units=imperial
    Now fetching city 208 of 600, hithadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hithadhoo,mv&units=imperial
    Now fetching city 209 of 600, general pico
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=general%20pico,ar&units=imperial
    Now fetching city 210 of 600, cravo norte
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cravo%20norte,co&units=imperial
    Now fetching city 211 of 600, nizhneyansk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nizhneyansk,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 212 of 600, tateyama
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tateyama,jp&units=imperial
    Now fetching city 213 of 600, amahai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=amahai,id&units=imperial
    Now fetching city 214 of 600, prince rupert
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=prince%20rupert,ca&units=imperial
    Now fetching city 215 of 600, tuatapere
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tuatapere,nz&units=imperial
    Now fetching city 216 of 600, matamoros
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=matamoros,mx&units=imperial
    Now fetching city 217 of 600, ponta do sol
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ponta%20do%20sol,cv&units=imperial
    Now fetching city 218 of 600, butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=butaritari,ki&units=imperial
    Now fetching city 219 of 600, tidore
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tidore,id&units=imperial
    Error with city data. Skipping...
    Now fetching city 220 of 600, vaitupu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vaitupu,wf&units=imperial
    Error with city data. Skipping...
    Now fetching city 221 of 600, azimur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=azimur,ma&units=imperial
    Error with city data. Skipping...
    Now fetching city 222 of 600, yumbe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yumbe,ug&units=imperial
    Now fetching city 223 of 600, sindand
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sindand,af&units=imperial
    Error with city data. Skipping...
    Now fetching city 224 of 600, zhigansk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zhigansk,ru&units=imperial
    Now fetching city 225 of 600, baykit
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=baykit,ru&units=imperial
    Now fetching city 226 of 600, tumannyy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tumannyy,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 227 of 600, suraabad
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=suraabad,az&units=imperial
    Now fetching city 228 of 600, rio grande
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rio%20grande,br&units=imperial
    Now fetching city 229 of 600, beloha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=beloha,mg&units=imperial
    Now fetching city 230 of 600, tarudant
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tarudant,ma&units=imperial
    Error with city data. Skipping...
    Now fetching city 231 of 600, nuuk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nuuk,gl&units=imperial
    Now fetching city 232 of 600, omboue
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=omboue,ga&units=imperial
    Now fetching city 233 of 600, vestmanna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vestmanna,fo&units=imperial
    Now fetching city 234 of 600, waingapu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=waingapu,id&units=imperial
    Now fetching city 235 of 600, lorengau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lorengau,pg&units=imperial
    Now fetching city 236 of 600, panique
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=panique,ph&units=imperial
    Now fetching city 237 of 600, banepa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=banepa,np&units=imperial
    Now fetching city 238 of 600, paranaiba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=paranaiba,br&units=imperial
    Now fetching city 239 of 600, georgetown
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=georgetown,sh&units=imperial
    Now fetching city 240 of 600, impfondo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=impfondo,cg&units=imperial
    Now fetching city 241 of 600, quang ngai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=quang%20ngai,vn&units=imperial
    Now fetching city 242 of 600, kindu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kindu,cd&units=imperial
    Now fetching city 243 of 600, ishigaki
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ishigaki,jp&units=imperial
    Now fetching city 244 of 600, balkhash
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=balkhash,kz&units=imperial
    Now fetching city 245 of 600, krasnoselkup
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=krasnoselkup,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 246 of 600, ginir
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ginir,et&units=imperial
    Now fetching city 247 of 600, tabialan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tabialan,ph&units=imperial
    Error with city data. Skipping...
    Now fetching city 248 of 600, esperance
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=esperance,au&units=imperial
    Now fetching city 249 of 600, bathsheba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bathsheba,bb&units=imperial
    Now fetching city 250 of 600, tupancireta
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tupancireta,br&units=imperial
    Now fetching city 251 of 600, atyrau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=atyrau,kz&units=imperial
    Now fetching city 252 of 600, oneida
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=oneida,us&units=imperial
    Now fetching city 253 of 600, kutum
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kutum,sd&units=imperial
    Now fetching city 254 of 600, doha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=doha,qa&units=imperial
    Now fetching city 255 of 600, ribeira grande
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ribeira%20grande,pt&units=imperial
    Now fetching city 256 of 600, amboasary
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=amboasary,mg&units=imperial
    Now fetching city 257 of 600, san quintin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20quintin,mx&units=imperial
    Error with city data. Skipping...
    Now fetching city 258 of 600, isangel
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=isangel,vu&units=imperial
    Now fetching city 259 of 600, zhuanghe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zhuanghe,cn&units=imperial
    Now fetching city 260 of 600, agirish
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=agirish,ru&units=imperial
    Now fetching city 261 of 600, camacha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=camacha,pt&units=imperial
    Now fetching city 262 of 600, sao joao da barra
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sao%20joao%20da%20barra,br&units=imperial
    Now fetching city 263 of 600, firovo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=firovo,ru&units=imperial
    Now fetching city 264 of 600, san cristobal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20cristobal,ec&units=imperial
    Now fetching city 265 of 600, bonavista
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bonavista,ca&units=imperial
    Now fetching city 266 of 600, bouloupari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bouloupari,nc&units=imperial
    Now fetching city 267 of 600, nishihara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nishihara,jp&units=imperial
    Now fetching city 268 of 600, shenjiamen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shenjiamen,cn&units=imperial
    Now fetching city 269 of 600, pringsewu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pringsewu,id&units=imperial
    Now fetching city 270 of 600, dali
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dali,cn&units=imperial
    Now fetching city 271 of 600, korla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=korla,cn&units=imperial
    Now fetching city 272 of 600, cabo san lucas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cabo%20san%20lucas,mx&units=imperial
    Now fetching city 273 of 600, lander
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lander,us&units=imperial
    Now fetching city 274 of 600, dalby
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dalby,au&units=imperial
    Now fetching city 275 of 600, raga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=raga,sd&units=imperial
    Error with city data. Skipping...
    Now fetching city 276 of 600, poum
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=poum,nc&units=imperial
    Now fetching city 277 of 600, ostrovnoy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ostrovnoy,ru&units=imperial
    Now fetching city 278 of 600, ibra
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ibra,om&units=imperial
    Now fetching city 279 of 600, dunedin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dunedin,nz&units=imperial
    Now fetching city 280 of 600, danilov
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=danilov,ru&units=imperial
    Now fetching city 281 of 600, kursumlija
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kursumlija,rs&units=imperial
    Now fetching city 282 of 600, nikolayevka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nikolayevka,ru&units=imperial
    Now fetching city 283 of 600, qaqortoq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=qaqortoq,gl&units=imperial
    Now fetching city 284 of 600, pisco
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pisco,pe&units=imperial
    Now fetching city 285 of 600, mangai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mangai,cd&units=imperial
    Now fetching city 286 of 600, catuday
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=catuday,ph&units=imperial
    Now fetching city 287 of 600, kodiak
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kodiak,us&units=imperial
    Now fetching city 288 of 600, norman wells
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=norman%20wells,ca&units=imperial
    Now fetching city 289 of 600, kaeo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kaeo,nz&units=imperial
    Now fetching city 290 of 600, pravda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pravda,ru&units=imperial
    Now fetching city 291 of 600, ape
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ape,lv&units=imperial
    Now fetching city 292 of 600, rodos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rodos,gr&units=imperial
    Now fetching city 293 of 600, gat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gat,ly&units=imperial
    Error with city data. Skipping...
    Now fetching city 294 of 600, serenje
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=serenje,zm&units=imperial
    Now fetching city 295 of 600, stykkisholmur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=stykkisholmur,is&units=imperial
    Now fetching city 296 of 600, ararat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ararat,au&units=imperial
    Now fetching city 297 of 600, bitkine
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bitkine,td&units=imperial
    Now fetching city 298 of 600, mogwase
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mogwase,za&units=imperial
    Now fetching city 299 of 600, nizhniy odes
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nizhniy%20odes,ru&units=imperial
    Now fetching city 300 of 600, komsomolskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=komsomolskiy,ru&units=imperial
    Now fetching city 301 of 600, tombouctou
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tombouctou,ml&units=imperial
    Now fetching city 302 of 600, seymchan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=seymchan,ru&units=imperial
    Now fetching city 303 of 600, chavakachcheri
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chavakachcheri,lk&units=imperial
    Now fetching city 304 of 600, pangnirtung
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pangnirtung,ca&units=imperial
    Now fetching city 305 of 600, chipinge
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chipinge,zw&units=imperial
    Now fetching city 306 of 600, yulara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yulara,au&units=imperial
    Now fetching city 307 of 600, oistins
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=oistins,bb&units=imperial
    Now fetching city 308 of 600, puerto madryn
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=puerto%20madryn,ar&units=imperial
    Now fetching city 309 of 600, moratuwa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=moratuwa,lk&units=imperial
    Now fetching city 310 of 600, sayansk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sayansk,ru&units=imperial
    Now fetching city 311 of 600, beringovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=beringovskiy,ru&units=imperial
    Now fetching city 312 of 600, amderma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=amderma,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 313 of 600, petropavlovsk-kamchatskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=petropavlovsk-kamchatskiy,ru&units=imperial
    Now fetching city 314 of 600, las lomas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=las%20lomas,pe&units=imperial
    Now fetching city 315 of 600, lavrentiya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lavrentiya,ru&units=imperial
    Now fetching city 316 of 600, narsaq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=narsaq,gl&units=imperial
    Now fetching city 317 of 600, manacapuru
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=manacapuru,br&units=imperial
    Now fetching city 318 of 600, buariki
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=buariki,ki&units=imperial
    Error with city data. Skipping...
    Now fetching city 319 of 600, iisalmi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=iisalmi,fi&units=imperial
    Now fetching city 320 of 600, evensk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=evensk,ru&units=imperial
    Now fetching city 321 of 600, stornoway
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=stornoway,gb&units=imperial
    Now fetching city 322 of 600, flinders
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=flinders,au&units=imperial
    Now fetching city 323 of 600, naousa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=naousa,gr&units=imperial
    Now fetching city 324 of 600, nizwa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nizwa,om&units=imperial
    Now fetching city 325 of 600, caxito
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=caxito,ao&units=imperial
    Now fetching city 326 of 600, salalah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=salalah,om&units=imperial
    Now fetching city 327 of 600, muisne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=muisne,ec&units=imperial
    Now fetching city 328 of 600, mahebourg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mahebourg,mu&units=imperial
    Now fetching city 329 of 600, cidreira
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cidreira,br&units=imperial
    Now fetching city 330 of 600, tiznit
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tiznit,ma&units=imperial
    Now fetching city 331 of 600, tokur
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tokur,ru&units=imperial
    Now fetching city 332 of 600, bethel
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bethel,us&units=imperial
    Now fetching city 333 of 600, akyab
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=akyab,mm&units=imperial
    Error with city data. Skipping...
    Now fetching city 334 of 600, qui nhon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=qui%20nhon,vn&units=imperial
    Error with city data. Skipping...
    Now fetching city 335 of 600, capreol
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=capreol,ca&units=imperial
    Now fetching city 336 of 600, portland
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=portland,au&units=imperial
    Now fetching city 337 of 600, zlocieniec
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zlocieniec,pl&units=imperial
    Now fetching city 338 of 600, mukhanovo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mukhanovo,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 339 of 600, okhotsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=okhotsk,ru&units=imperial
    Now fetching city 340 of 600, khromtau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=khromtau,kz&units=imperial
    Now fetching city 341 of 600, vryburg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vryburg,za&units=imperial
    Now fetching city 342 of 600, nanortalik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nanortalik,gl&units=imperial
    Now fetching city 343 of 600, yenagoa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yenagoa,ng&units=imperial
    Now fetching city 344 of 600, umzimvubu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=umzimvubu,za&units=imperial
    Error with city data. Skipping...
    Now fetching city 345 of 600, sioux lookout
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sioux%20lookout,ca&units=imperial
    Now fetching city 346 of 600, fereydun kenar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fereydun%20kenar,ir&units=imperial
    Now fetching city 347 of 600, tayzhina
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tayzhina,ru&units=imperial
    Now fetching city 348 of 600, sao filipe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sao%20filipe,cv&units=imperial
    Now fetching city 349 of 600, balabac
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=balabac,ph&units=imperial
    Now fetching city 350 of 600, nianzishan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nianzishan,cn&units=imperial
    Now fetching city 351 of 600, galiwinku
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=galiwinku,au&units=imperial
    Error with city data. Skipping...
    Now fetching city 352 of 600, ilo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ilo,pe&units=imperial
    Now fetching city 353 of 600, sao miguel do araguaia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sao%20miguel%20do%20araguaia,br&units=imperial
    Now fetching city 354 of 600, nome
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nome,us&units=imperial
    Now fetching city 355 of 600, vila franca do campo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vila%20franca%20do%20campo,pt&units=imperial
    Now fetching city 356 of 600, coahuayana
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=coahuayana,mx&units=imperial
    Now fetching city 357 of 600, airai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=airai,pw&units=imperial
    Error with city data. Skipping...
    Now fetching city 358 of 600, gaillac
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gaillac,fr&units=imperial
    Now fetching city 359 of 600, zama
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zama,jp&units=imperial
    Now fetching city 360 of 600, aykhal
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aykhal,ru&units=imperial
    Now fetching city 361 of 600, matagami
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=matagami,ca&units=imperial
    Now fetching city 362 of 600, vao
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vao,nc&units=imperial
    Now fetching city 363 of 600, kruisfontein
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kruisfontein,za&units=imperial
    Now fetching city 364 of 600, black river
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=black%20river,jm&units=imperial
    Now fetching city 365 of 600, tabou
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tabou,ci&units=imperial
    Now fetching city 366 of 600, singaraja
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=singaraja,id&units=imperial
    Now fetching city 367 of 600, roma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=roma,au&units=imperial
    Now fetching city 368 of 600, zandvoort
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=zandvoort,nl&units=imperial
    Now fetching city 369 of 600, kyenjojo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kyenjojo,ug&units=imperial
    Now fetching city 370 of 600, port-de-bouc
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port-de-bouc,fr&units=imperial
    Now fetching city 371 of 600, port lincoln
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20lincoln,au&units=imperial
    Now fetching city 372 of 600, kuche
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kuche,cn&units=imperial
    Error with city data. Skipping...
    Now fetching city 373 of 600, christchurch
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=christchurch,nz&units=imperial
    Now fetching city 374 of 600, vila velha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vila%20velha,br&units=imperial
    Now fetching city 375 of 600, nichinan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nichinan,jp&units=imperial
    Now fetching city 376 of 600, marystown
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marystown,ca&units=imperial
    Now fetching city 377 of 600, avera
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=avera,pf&units=imperial
    Error with city data. Skipping...
    Now fetching city 378 of 600, rawson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rawson,ar&units=imperial
    Now fetching city 379 of 600, xuanhua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=xuanhua,cn&units=imperial
    Now fetching city 380 of 600, paamiut
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=paamiut,gl&units=imperial
    Now fetching city 381 of 600, hasaki
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hasaki,jp&units=imperial
    Now fetching city 382 of 600, faya
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=faya,td&units=imperial
    Error with city data. Skipping...
    Now fetching city 383 of 600, clyde river
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=clyde%20river,ca&units=imperial
    Now fetching city 384 of 600, krivosheino
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=krivosheino,ru&units=imperial
    Now fetching city 385 of 600, conakry
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=conakry,gn&units=imperial
    Now fetching city 386 of 600, port-de-paix
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port-de-paix,ht&units=imperial
    Error with city data. Skipping...
    Now fetching city 387 of 600, price
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=price,us&units=imperial
    Now fetching city 388 of 600, bur gabo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bur%20gabo,so&units=imperial
    Error with city data. Skipping...
    Now fetching city 389 of 600, honiara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=honiara,sb&units=imperial
    Now fetching city 390 of 600, sosua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sosua,do&units=imperial
    Now fetching city 391 of 600, djambala
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=djambala,cg&units=imperial
    Now fetching city 392 of 600, fort nelson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fort%20nelson,ca&units=imperial
    Now fetching city 393 of 600, howell
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=howell,us&units=imperial
    Now fetching city 394 of 600, port-gentil
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port-gentil,ga&units=imperial
    Now fetching city 395 of 600, namatanai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=namatanai,pg&units=imperial
    Now fetching city 396 of 600, rio gallegos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rio%20gallegos,ar&units=imperial
    Now fetching city 397 of 600, sayyan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sayyan,ye&units=imperial
    Now fetching city 398 of 600, karangasem
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=karangasem,id&units=imperial
    Now fetching city 399 of 600, kavieng
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kavieng,pg&units=imperial
    Now fetching city 400 of 600, lumeje
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lumeje,ao&units=imperial
    Now fetching city 401 of 600, souillac
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=souillac,mu&units=imperial
    Now fetching city 402 of 600, karratha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=karratha,au&units=imperial
    Now fetching city 403 of 600, soavinandriana
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=soavinandriana,mg&units=imperial
    Now fetching city 404 of 600, los llanos de aridane
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=los%20llanos%20de%20aridane,es&units=imperial
    Now fetching city 405 of 600, urumqi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=urumqi,cn&units=imperial
    Error with city data. Skipping...
    Now fetching city 406 of 600, puri
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=puri,in&units=imperial
    Now fetching city 407 of 600, turayf
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=turayf,sa&units=imperial
    Now fetching city 408 of 600, maumere
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maumere,id&units=imperial
    Now fetching city 409 of 600, la ronge
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=la%20ronge,ca&units=imperial
    Now fetching city 410 of 600, san miguel
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=san%20miguel,bo&units=imperial
    Now fetching city 411 of 600, flin flon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=flin%20flon,ca&units=imperial
    Now fetching city 412 of 600, huilong
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=huilong,cn&units=imperial
    Now fetching city 413 of 600, abu samrah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=abu%20samrah,qa&units=imperial
    Error with city data. Skipping...
    Now fetching city 414 of 600, kamenka
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kamenka,ru&units=imperial
    Now fetching city 415 of 600, penzance
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=penzance,gb&units=imperial
    Now fetching city 416 of 600, marcona
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marcona,pe&units=imperial
    Error with city data. Skipping...
    Now fetching city 417 of 600, saint-georges
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-georges,gf&units=imperial
    Error with city data. Skipping...
    Now fetching city 418 of 600, whitehorse
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=whitehorse,ca&units=imperial
    Now fetching city 419 of 600, nchelenge
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nchelenge,zm&units=imperial
    Now fetching city 420 of 600, esil
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=esil,kz&units=imperial
    Now fetching city 421 of 600, belmonte
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=belmonte,br&units=imperial
    Now fetching city 422 of 600, nelson bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nelson%20bay,au&units=imperial
    Now fetching city 423 of 600, samusu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=samusu,ws&units=imperial
    Error with city data. Skipping...
    Now fetching city 424 of 600, waipawa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=waipawa,nz&units=imperial
    Now fetching city 425 of 600, cuenca
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cuenca,es&units=imperial
    Now fetching city 426 of 600, someru
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=someru,ee&units=imperial
    Now fetching city 427 of 600, carutapera
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=carutapera,br&units=imperial
    Now fetching city 428 of 600, erzin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=erzin,ru&units=imperial
    Now fetching city 429 of 600, diffa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=diffa,ne&units=imperial
    Now fetching city 430 of 600, acajutla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=acajutla,sv&units=imperial
    Now fetching city 431 of 600, strezhevoy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=strezhevoy,ru&units=imperial
    Now fetching city 432 of 600, tayu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tayu,id&units=imperial
    Now fetching city 433 of 600, port blair
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20blair,in&units=imperial
    Now fetching city 434 of 600, chapais
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chapais,ca&units=imperial
    Now fetching city 435 of 600, cumaribo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cumaribo,co&units=imperial
    Error with city data. Skipping...
    Now fetching city 436 of 600, kalia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kalia,bd&units=imperial
    Now fetching city 437 of 600, brownsville
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=brownsville,us&units=imperial
    Now fetching city 438 of 600, vasilyevo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vasilyevo,ru&units=imperial
    Now fetching city 439 of 600, nhulunbuy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nhulunbuy,au&units=imperial
    Now fetching city 440 of 600, vardo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=vardo,no&units=imperial
    Now fetching city 441 of 600, maldonado
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maldonado,uy&units=imperial
    Now fetching city 442 of 600, hailey
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hailey,us&units=imperial
    Now fetching city 443 of 600, lincoln
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lincoln,us&units=imperial
    Now fetching city 444 of 600, fukue
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fukue,jp&units=imperial
    Now fetching city 445 of 600, maceio
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=maceio,br&units=imperial
    Now fetching city 446 of 600, port keats
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=port%20keats,au&units=imperial
    Now fetching city 447 of 600, cayenne
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cayenne,gf&units=imperial
    Now fetching city 448 of 600, puerto escondido
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=puerto%20escondido,mx&units=imperial
    Now fetching city 449 of 600, annamalainagar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=annamalainagar,in&units=imperial
    Now fetching city 450 of 600, csaszartoltes
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=csaszartoltes,hu&units=imperial
    Now fetching city 451 of 600, paracuru
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=paracuru,br&units=imperial
    Now fetching city 452 of 600, boa vista
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=boa%20vista,br&units=imperial
    Now fetching city 453 of 600, rexburg
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=rexburg,us&units=imperial
    Now fetching city 454 of 600, itarema
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=itarema,br&units=imperial
    Now fetching city 455 of 600, touros
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=touros,br&units=imperial
    Now fetching city 456 of 600, mount hagen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mount%20hagen,pg&units=imperial
    Now fetching city 457 of 600, karaul
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=karaul,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 458 of 600, hambantota
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hambantota,lk&units=imperial
    Now fetching city 459 of 600, bouar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bouar,cf&units=imperial
    Now fetching city 460 of 600, novikovo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=novikovo,ru&units=imperial
    Now fetching city 461 of 600, dickinson
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dickinson,us&units=imperial
    Now fetching city 462 of 600, meyungs
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=meyungs,pw&units=imperial
    Error with city data. Skipping...
    Now fetching city 463 of 600, kamaishi
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kamaishi,jp&units=imperial
    Now fetching city 464 of 600, skorodnoye
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=skorodnoye,ru&units=imperial
    Now fetching city 465 of 600, manono
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=manono,cd&units=imperial
    Now fetching city 466 of 600, saint-philippe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-philippe,re&units=imperial
    Now fetching city 467 of 600, kendari
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kendari,id&units=imperial
    Now fetching city 468 of 600, ajaccio
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ajaccio,fr&units=imperial
    Now fetching city 469 of 600, nyeri
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nyeri,ke&units=imperial
    Now fetching city 470 of 600, homa bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=homa%20bay,ke&units=imperial
    Now fetching city 471 of 600, quatre cocos
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=quatre%20cocos,mu&units=imperial
    Now fetching city 472 of 600, finschhafen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=finschhafen,pg&units=imperial
    Now fetching city 473 of 600, torbat-e jam
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=torbat-e%20jam,ir&units=imperial
    Now fetching city 474 of 600, kadhan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kadhan,pk&units=imperial
    Now fetching city 475 of 600, dalbandin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dalbandin,pk&units=imperial
    Now fetching city 476 of 600, bardiyah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bardiyah,ly&units=imperial
    Now fetching city 477 of 600, leo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=leo,bf&units=imperial
    Now fetching city 478 of 600, ozernovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ozernovskiy,ru&units=imperial
    Now fetching city 479 of 600, plainview
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=plainview,us&units=imperial
    Now fetching city 480 of 600, bezhetsk
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bezhetsk,ru&units=imperial
    Now fetching city 481 of 600, adrar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=adrar,dz&units=imperial
    Now fetching city 482 of 600, igrim
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=igrim,ru&units=imperial
    Now fetching city 483 of 600, ilulissat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ilulissat,gl&units=imperial
    Now fetching city 484 of 600, ziro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ziro,in&units=imperial
    Now fetching city 485 of 600, plettenberg bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=plettenberg%20bay,za&units=imperial
    Now fetching city 486 of 600, tapaua
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tapaua,br&units=imperial
    Error with city data. Skipping...
    Now fetching city 487 of 600, yarim
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=yarim,ye&units=imperial
    Now fetching city 488 of 600, umm lajj
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=umm%20lajj,sa&units=imperial
    Now fetching city 489 of 600, coihaique
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=coihaique,cl&units=imperial
    Now fetching city 490 of 600, lahat
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=lahat,id&units=imperial
    Now fetching city 491 of 600, daru
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=daru,pg&units=imperial
    Now fetching city 492 of 600, toliary
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=toliary,mg&units=imperial
    Error with city data. Skipping...
    Now fetching city 493 of 600, sobolevo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sobolevo,ru&units=imperial
    Now fetching city 494 of 600, homer
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=homer,us&units=imperial
    Now fetching city 495 of 600, bani walid
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bani%20walid,ly&units=imperial
    Now fetching city 496 of 600, piedras negras
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=piedras%20negras,mx&units=imperial
    Now fetching city 497 of 600, ambilobe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ambilobe,mg&units=imperial
    Now fetching city 498 of 600, nemuro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nemuro,jp&units=imperial
    Now fetching city 499 of 600, brookings
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=brookings,us&units=imperial
    Now fetching city 500 of 600, juneau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=juneau,us&units=imperial
    Now fetching city 501 of 600, aberdeen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=aberdeen,us&units=imperial
    Now fetching city 502 of 600, haines junction
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=haines%20junction,ca&units=imperial
    Now fetching city 503 of 600, road town
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=road%20town,vg&units=imperial
    Now fetching city 504 of 600, hayden
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hayden,us&units=imperial
    Now fetching city 505 of 600, shimoda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shimoda,jp&units=imperial
    Now fetching city 506 of 600, orlik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=orlik,ru&units=imperial
    Now fetching city 507 of 600, margate
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=margate,za&units=imperial
    Now fetching city 508 of 600, shache
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=shache,cn&units=imperial
    Now fetching city 509 of 600, borama
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=borama,so&units=imperial
    Error with city data. Skipping...
    Now fetching city 510 of 600, along
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=along,in&units=imperial
    Now fetching city 511 of 600, mullaitivu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mullaitivu,lk&units=imperial
    Error with city data. Skipping...
    Now fetching city 512 of 600, dzhusaly
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dzhusaly,kz&units=imperial
    Error with city data. Skipping...
    Now fetching city 513 of 600, pierre
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pierre,us&units=imperial
    Now fetching city 514 of 600, eenhana
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=eenhana,na&units=imperial
    Now fetching city 515 of 600, ornskoldsvik
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ornskoldsvik,se&units=imperial
    Now fetching city 516 of 600, andujar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=andujar,es&units=imperial
    Now fetching city 517 of 600, merke
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=merke,kz&units=imperial
    Now fetching city 518 of 600, saint-augustin
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=saint-augustin,ca&units=imperial
    Now fetching city 519 of 600, cockburn town
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cockburn%20town,bs&units=imperial
    Now fetching city 520 of 600, sarai alamgir
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sarai%20alamgir,pk&units=imperial
    Now fetching city 521 of 600, dzilam gonzalez
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dzilam%20gonzalez,mx&units=imperial
    Now fetching city 522 of 600, linhai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=linhai,cn&units=imperial
    Now fetching city 523 of 600, burnie
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=burnie,au&units=imperial
    Now fetching city 524 of 600, namibe
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=namibe,ao&units=imperial
    Now fetching city 525 of 600, disna
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=disna,by&units=imperial
    Error with city data. Skipping...
    Now fetching city 526 of 600, xining
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=xining,cn&units=imperial
    Now fetching city 527 of 600, meiganga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=meiganga,cm&units=imperial
    Now fetching city 528 of 600, myitkyina
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=myitkyina,mm&units=imperial
    Now fetching city 529 of 600, helong
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=helong,cn&units=imperial
    Now fetching city 530 of 600, tocopilla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tocopilla,cl&units=imperial
    Now fetching city 531 of 600, salym
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=salym,ru&units=imperial
    Now fetching city 532 of 600, ovalle
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ovalle,cl&units=imperial
    Now fetching city 533 of 600, hobyo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=hobyo,so&units=imperial
    Now fetching city 534 of 600, marzuq
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marzuq,ly&units=imperial
    Error with city data. Skipping...
    Now fetching city 535 of 600, axim
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=axim,gh&units=imperial
    Now fetching city 536 of 600, cartagena del chaira
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cartagena%20del%20chaira,co&units=imperial
    Now fetching city 537 of 600, katherine
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=katherine,au&units=imperial
    Now fetching city 538 of 600, antequera
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=antequera,py&units=imperial
    Now fetching city 539 of 600, terrace
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=terrace,ca&units=imperial
    Now fetching city 540 of 600, macas
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=macas,ec&units=imperial
    Now fetching city 541 of 600, pangai
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pangai,to&units=imperial
    Now fetching city 542 of 600, wageningen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=wageningen,sr&units=imperial
    Now fetching city 543 of 600, sydney
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sydney,au&units=imperial
    Now fetching city 544 of 600, erenhot
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=erenhot,cn&units=imperial
    Now fetching city 545 of 600, alibag
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=alibag,in&units=imperial
    Now fetching city 546 of 600, bugiri
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bugiri,ug&units=imperial
    Now fetching city 547 of 600, gawler
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gawler,au&units=imperial
    Now fetching city 548 of 600, sisimiut
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sisimiut,gl&units=imperial
    Now fetching city 549 of 600, dossor
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=dossor,kz&units=imperial
    Now fetching city 550 of 600, ahipara
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ahipara,nz&units=imperial
    Now fetching city 551 of 600, pochutla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=pochutla,mx&units=imperial
    Now fetching city 552 of 600, viedma
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=viedma,ar&units=imperial
    Now fetching city 553 of 600, stoyba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=stoyba,ru&units=imperial
    Error with city data. Skipping...
    Now fetching city 554 of 600, altamira
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=altamira,br&units=imperial
    Now fetching city 555 of 600, antalaha
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=antalaha,mg&units=imperial
    Now fetching city 556 of 600, richards bay
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=richards%20bay,za&units=imperial
    Now fetching city 557 of 600, anloga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=anloga,gh&units=imperial
    Now fetching city 558 of 600, halalo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=halalo,wf&units=imperial
    Error with city data. Skipping...
    Now fetching city 559 of 600, birao
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=birao,cf&units=imperial
    Now fetching city 560 of 600, risaralda
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=risaralda,co&units=imperial
    Now fetching city 561 of 600, oriximina
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=oriximina,br&units=imperial
    Now fetching city 562 of 600, mount gambier
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mount%20gambier,au&units=imperial
    Now fetching city 563 of 600, fort frances
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fort%20frances,ca&units=imperial
    Now fetching city 564 of 600, bayan
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bayan,kw&units=imperial
    Now fetching city 565 of 600, sile
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sile,tr&units=imperial
    Now fetching city 566 of 600, westport
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=westport,ie&units=imperial
    Now fetching city 567 of 600, gamba
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gamba,ga&units=imperial
    Now fetching city 568 of 600, mnogovershinnyy
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mnogovershinnyy,ru&units=imperial
    Now fetching city 569 of 600, santa cruz cabralia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=santa%20cruz%20cabralia,br&units=imperial
    Now fetching city 570 of 600, marneuli
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=marneuli,ge&units=imperial
    Now fetching city 571 of 600, casian
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=casian,ph&units=imperial
    Now fetching city 572 of 600, bam
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bam,ir&units=imperial
    Now fetching city 573 of 600, mata grande
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mata%20grande,br&units=imperial
    Now fetching city 574 of 600, wagga wagga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=wagga%20wagga,au&units=imperial
    Now fetching city 575 of 600, bustehrad
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=bustehrad,cz&units=imperial
    Now fetching city 576 of 600, ojinaga
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=ojinaga,mx&units=imperial
    Now fetching city 577 of 600, eyemouth
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=eyemouth,gb&units=imperial
    Now fetching city 578 of 600, cabra
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=cabra,ph&units=imperial
    Now fetching city 579 of 600, nurota
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=nurota,uz&units=imperial
    Now fetching city 580 of 600, fairbanks
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=fairbanks,us&units=imperial
    Now fetching city 581 of 600, unguia
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=unguia,co&units=imperial
    Error with city data. Skipping...
    Now fetching city 582 of 600, mukhen
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=mukhen,ru&units=imperial
    Now fetching city 583 of 600, chicama
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chicama,pe&units=imperial
    Now fetching city 584 of 600, seoul
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=seoul,kr&units=imperial
    Now fetching city 585 of 600, chhapar
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=chhapar,in&units=imperial
    Now fetching city 586 of 600, sept-iles
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=sept-iles,ca&units=imperial
    Now fetching city 587 of 600, miquelon
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=miquelon,pm&units=imperial
    Now fetching city 588 of 600, asau
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=asau,tv&units=imperial
    Error with city data. Skipping...
    Now fetching city 589 of 600, canals
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=canals,es&units=imperial
    Now fetching city 590 of 600, klamath falls
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=klamath%20falls,us&units=imperial
    Now fetching city 591 of 600, tecoanapa
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=tecoanapa,mx&units=imperial
    Now fetching city 592 of 600, calama
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=calama,cl&units=imperial
    Now fetching city 593 of 600, samalaeulu
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=samalaeulu,ws&units=imperial
    Error with city data. Skipping...
    Now fetching city 594 of 600, umm kaddadah
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=umm%20kaddadah,sd&units=imperial
    Now fetching city 595 of 600, upington
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=upington,za&units=imperial
    Now fetching city 596 of 600, kardla
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=kardla,ee&units=imperial
    Now fetching city 597 of 600, gizo
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=gizo,sb&units=imperial
    Now fetching city 598 of 600, guerrero negro
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=guerrero%20negro,mx&units=imperial
    Now fetching city 599 of 600, powell river
    http://api.openweathermap.org/data/2.5/weather?appid=2351e01876e29ba8eb0641948f4776dd&q=powell%20river,ca&units=imperial
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
c=city_weather_df["Temperature (F)"]
colormap = "jet"
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



```python
plt.scatter(city_weather_df["Longitude"], city_weather_df["Latitude"], marker="o",
            edgecolor="black", alpha=.8, label="City", c=city_weather_df["Temperature (F)"], cmap="jet")
plt.xlim(-200, 200)
plt.ylim(-100, 100)
plt.xlabel("Latitude")
plt.ylabel("Longitude")
plt.title("City Temperatures by Coordinate")
plt.grid(True)
plt.show()
#plt.savefig("City Latitude vs. Wind Speed 4-1-2018.png")
```


![png](output_7_0.png)

