
## Analysis

1. There are far more drivers, as well as rides, in urban cities. Average fairs are also lower in urban citites. This could be due to the higher number of rides or greater avialability of drivers, based on the information provided. Another data point that may have provided more insight as to why the rides in urban cities have lower average fares would be the distance of each ride.  
2. Rides in rural cities have a significanlty higher average price in comparison to other city types. This could be due to driver availability as well as number of rides taken in those cities. It is also possible that rides in rural areas have a higher average distance than that of rides taken in urban and suburban areas.

3. Offering discounted rides in rural cities, and possibly even suburban cities, could increase usage. Promotion of the service in these areas could also increase usage. 


```python
import matplotlib.pyplot as plt 
import pandas as pd
import numpy as np
import seaborn as sns
```


```python
city_csv = 'Resources/city_data.csv'
ride_csv = 'Resources/ride_data.csv'

city_data = pd.read_csv(city_csv)
ride_data = pd.read_csv(ride_csv)

ride_data.head()
#city_data.head()
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
      <th>city</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sarabury</td>
      <td>2016-01-16 13:49:27</td>
      <td>38.35</td>
      <td>5403689035038</td>
    </tr>
    <tr>
      <th>1</th>
      <td>South Roy</td>
      <td>2016-01-02 18:42:34</td>
      <td>17.49</td>
      <td>4036272335942</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Wiseborough</td>
      <td>2016-01-21 17:35:29</td>
      <td>44.18</td>
      <td>3645042422587</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Spencertown</td>
      <td>2016-07-31 14:53:22</td>
      <td>6.87</td>
      <td>2242596575892</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nguyenbury</td>
      <td>2016-07-09 04:42:44</td>
      <td>6.28</td>
      <td>1543057793673</td>
    </tr>
  </tbody>
</table>
</div>




```python
merged = pd.merge(ride_data, city_data, how="left", on=["city", "city"])

merged.head()
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
      <th>city</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
      <th>driver_count</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sarabury</td>
      <td>2016-01-16 13:49:27</td>
      <td>38.35</td>
      <td>5403689035038</td>
      <td>46</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>1</th>
      <td>South Roy</td>
      <td>2016-01-02 18:42:34</td>
      <td>17.49</td>
      <td>4036272335942</td>
      <td>35</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Wiseborough</td>
      <td>2016-01-21 17:35:29</td>
      <td>44.18</td>
      <td>3645042422587</td>
      <td>55</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Spencertown</td>
      <td>2016-07-31 14:53:22</td>
      <td>6.87</td>
      <td>2242596575892</td>
      <td>68</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nguyenbury</td>
      <td>2016-07-09 04:42:44</td>
      <td>6.28</td>
      <td>1543057793673</td>
      <td>8</td>
      <td>Urban</td>
    </tr>
  </tbody>
</table>
</div>




```python
urban = merged[merged["type"] == "Urban"]
suburban = merged[merged["type"] == "Suburban"]
rural = merged[merged["type"] == "Rural"]

urban_rides = urban.groupby(["city"]).count()["ride_id"]
suburban_rides = suburban.groupby(["city"]).count()["ride_id"]
rural_rides = rural.groupby(["city"]).count()["ride_id"]

urban_fares = urban.groupby(["city"]).mean()["fare"]
suburban_fares = suburban.groupby(["city"]).mean()["fare"]
rural_fares = rural.groupby(["city"]).mean()["fare"]

urban_drivers = urban.groupby(["city"]).count()["driver_count"]
suburban_drivers = suburban.groupby(["city"]).count()["driver_count"]
rural_drivers = rural.groupby(["city"]).count()["driver_count"]
```


```python
plt.scatter(urban_rides, 
            urban_fares, 
            s=10*urban_drivers, c="coral", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Urban")

plt.scatter(suburban_rides, 
            suburban_fares, 
            s=10*suburban_drivers, c="skyblue", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Suburban")

plt.scatter(rural_rides, 
            rural_fares, 
            s=10*rural_drivers, c="gold", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Rural")

legend = plt.legend(loc="best", title="City Types")

plt.title("Pyber Ride Sharing Data (2016)")
plt.ylabel("Average Fare ($)")
plt.xlabel("Total Number of Rides (Per City)")
plt.xlim((0,40))
plt.grid(True)

plt.text(42, 35, "Note:\nCircle size correlates with driver count per city.")
plt.show()
```


![png](output_5_0.png)



```python
urban_fares = len(merged[merged["type"] == "Urban"])
percent_urban_fares = round((urban_fares/len(merged["type"])*100),2)

suburban_fares = len(merged[merged["type"] == "Suburban"])
percent_suburban_fares = round((suburban_fares/len(merged["type"])*100),2)

rural_fares = len(merged[merged["type"] == "Rural"])
percent_rural_fares = round((rural_fares/len(merged["type"])*100),2)

fares_by_type = [percent_urban_fares, percent_suburban_fares, percent_rural_fares]
labels = ["Urban","Suburban", "Rural"]

fares_by_type
```




    [67.51, 27.3, 5.19]




```python
fares = 100 * merged.groupby(["type"]).sum()["fare"] / merged["fare"].sum()

plt.pie(fares, 
        labels=["Rural", "Suburban", "Urban"], 
        colors=["gold", "lightskyblue", "lightcoral"], 
        explode=[0, 0, 0.1], 
        autopct='%1.1f%%', 
        shadow=True, startangle=150)
plt.title("% of Total Fares by City Type")

plt.show()
```


![png](output_7_0.png)



```python
rides = 100 * merged.groupby(["type"]).count()["ride_id"] / merged["ride_id"].count()

plt.pie(rides, 
        labels=["Rural", "Suburban", "Urban"], 
        colors=["gold", "lightskyblue", "lightcoral"], 
        explode=[0, 0, 0.1], 
        autopct='%1.1f%%', 
        shadow=True, startangle=150)
plt.title("% of Total Rides by City Type")

plt.show()
```


![png](output_8_0.png)



```python
drivers = 100 * city_data.groupby(["type"]).sum()["driver_count"] / city_data["driver_count"].sum()

#drivers = 100 * merged.groupby(["type"]).sum()["driver_count"] / merged["driver_count"].sum()

plt.pie(drivers, 
        labels=["Rural", "Suburban", "Urban"], 
        colors=["gold", "lightskyblue", "lightcoral"], 
        explode=[0, 0, 0.1], 
        autopct='%1.1f%%', 
        shadow=True, startangle=150)
plt.title("% of Total Drivers by City Type")

plt.show()
```


![png](output_9_0.png)

