
# Pyber Analysis

In reviewing life to date (LTD) driving records for Pyber, the data indicate opportunity for growth in the suburban market. Surverying average fares across three city types (urban, suburban, and rural), average fare trends lower the more "urban" a city is. Though fares trend higher the more rural a city is, rural cities capture such a small market share (5.3% of rides) that increasing business would likely be a larger effort that would take more time to see returns. Suburban business, however, makes up a substantial portion of the fare business (30.5%) on fewer rides (26.3%). Compared to the urban business (62.7% of fares on 68.7% of rides), the suburban market stands out as more profitable. Viewed in this light, it might be advisable to focus marketing efforts toward growing suburban business.


```python
%matplotlib inline
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

# File to Load
city_file = "data/city_data.csv"
ride_file = "data/ride_data.csv"

# Read the city file
city_raw = pd.read_csv(city_file)
city_raw.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Richardfort</td>
      <td>38</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Williamsstad</td>
      <td>59</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Port Angela</td>
      <td>67</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Rodneyfort</td>
      <td>34</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>4</th>
      <td>West Robert</td>
      <td>39</td>
      <td>Urban</td>
    </tr>
  </tbody>
</table>
</div>




```python
#read the ride file
ride_raw = pd.read_csv(ride_file)
ride_raw.head()
```




<div>
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
      <td>Lake Jonathanshire</td>
      <td>2018-01-14 10:14:22</td>
      <td>13.83</td>
      <td>5739410935873</td>
    </tr>
    <tr>
      <th>1</th>
      <td>South Michelleport</td>
      <td>2018-03-04 18:24:09</td>
      <td>30.24</td>
      <td>2343912425577</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Port Samanthamouth</td>
      <td>2018-02-24 04:29:00</td>
      <td>33.44</td>
      <td>2005065760003</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Rodneyfort</td>
      <td>2018-02-10 23:22:03</td>
      <td>23.44</td>
      <td>5149245426178</td>
    </tr>
    <tr>
      <th>4</th>
      <td>South Jack</td>
      <td>2018-03-06 04:28:35</td>
      <td>34.58</td>
      <td>3908451377344</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Combine the data into a single dataset
py_merge = pd.merge(ride_raw, city_raw, how="outer", on="city")

# Display the data table for preview
py_merge.head()
```




<div>
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
      <td>Lake Jonathanshire</td>
      <td>2018-01-14 10:14:22</td>
      <td>13.83</td>
      <td>5739410935873</td>
      <td>5</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Lake Jonathanshire</td>
      <td>2018-04-07 20:51:11</td>
      <td>31.25</td>
      <td>4441251834598</td>
      <td>5</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Lake Jonathanshire</td>
      <td>2018-03-09 23:45:55</td>
      <td>19.89</td>
      <td>2389495660448</td>
      <td>5</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Lake Jonathanshire</td>
      <td>2018-04-07 18:09:21</td>
      <td>24.28</td>
      <td>7796805191168</td>
      <td>5</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Lake Jonathanshire</td>
      <td>2018-01-02 14:14:50</td>
      <td>13.89</td>
      <td>424254840012</td>
      <td>5</td>
      <td>Urban</td>
    </tr>
  </tbody>
</table>
</div>



## Bubble Plot of Ride Sharing Data


```python
#build grouped DataFrame for clean data to populate plot
pyber_summ = pd.DataFrame(py_merge.groupby(['city', 'type', 'driver_count']).agg({'ride_id': 'nunique', 'fare':'mean'})).reset_index()
pyber_summ['type_key'] = ""
pyber_summ.loc[pyber_summ['type'].str.contains('Urban'), 'type_key'] = 'lightcoral'
pyber_summ.loc[pyber_summ['type'].str.contains('Suburban'), 'type_key'] = 'lightskyblue'
pyber_summ.loc[pyber_summ['type'].str.contains('Rural'), 'type_key'] = 'gold'
pyber_summ.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>type</th>
      <th>driver_count</th>
      <th>ride_id</th>
      <th>fare</th>
      <th>type_key</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Amandaburgh</td>
      <td>Urban</td>
      <td>12</td>
      <td>18</td>
      <td>24.641667</td>
      <td>lightcoral</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Barajasview</td>
      <td>Urban</td>
      <td>26</td>
      <td>22</td>
      <td>25.332273</td>
      <td>lightcoral</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Barronchester</td>
      <td>Suburban</td>
      <td>11</td>
      <td>16</td>
      <td>36.422500</td>
      <td>lightskyblue</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Bethanyland</td>
      <td>Suburban</td>
      <td>22</td>
      <td>18</td>
      <td>32.956111</td>
      <td>lightskyblue</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Bradshawfurt</td>
      <td>Rural</td>
      <td>7</td>
      <td>10</td>
      <td>40.064000</td>
      <td>gold</td>
    </tr>
  </tbody>
</table>
</div>




```python
#create a DataFrame per city type on which to generate scatter plots
urban = pyber_summ.loc[pyber_summ['type'] == 'Urban']
suburban = pyber_summ.loc[pyber_summ['type'] == 'Suburban']
rural = pyber_summ.loc[pyber_summ['type'] == 'Rural']
```


```python
#build the scatter plot utilizing each DataFrame, in order to provide legend data
plt.figure(figsize = (15, 10))
plt.scatter(urban['ride_id'], urban['fare'], s=urban['driver_count']*30, c=urban['type_key'], edgecolors='k', alpha=0.7)
plt.scatter(suburban['ride_id'], suburban['fare'], s=suburban['driver_count']*30, c=suburban['type_key'], edgecolors='k', alpha=0.7)
plt.scatter(rural['ride_id'], rural['fare'], s=rural['driver_count']*30, c=rural['type_key'], edgecolors='k', alpha=0.7)

#add aesthetics and clarifying details
plt.grid(color="0.75", linestyle="-")
plt.title("Pyber Ride Sharing Data")
plt.xlabel("Total Number of Rides (Per City)")
plt.ylabel("Average Fare ($)")
plt.text(41, 35, "Note: \n Bubble size correlates to number of drivers in city.")

#add and format legend
leg = plt.legend(['Urban', 'Suburban', 'Rural'], title='City Types')
leg.legendHandles[0]._sizes = [100]
leg.legendHandles[1]._sizes = [100]
leg.legendHandles[2]._sizes = [100]

#save figure
plt.savefig('ride_share_avgs.jpg')
# show plot
plt.show()

```


![png](output_8_0.png)


## Total Fares by City Type


```python
# create DataFrame storing fares on which the pie chart might be built
fare_sum = pd.DataFrame(py_merge.groupby('type').agg({'fare':'sum'})).reset_index()
fare_sum['type_key'] = ""
fare_sum.loc[fare_sum['type'].str.contains('Urban'), 'type_key'] = 'lightcoral'
fare_sum.loc[fare_sum['type'].str.contains('Suburban'), 'type_key'] = 'lightskyblue'
fare_sum.loc[fare_sum['type'].str.contains('Rural'), 'type_key'] = 'gold'

# Build Pie Chart
plt.pie(fare_sum['fare'],
        labels=fare_sum['type'],
        colors=fare_sum['type_key'], 
        autopct="%1.1f%%", 
        shadow=True,
        explode=[0, 0, .1], 
        startangle=145)
plt.title("% Total Fares by City Type")

#save figure
plt.savefig('fares_by_city_type.jpg')

# show plot
plt.show()
```


![png](output_10_0.png)


## Total Rides by City Type


```python
# create DataFrame storing ride id counts on which the pie chart might be built
ride_sum = pd.DataFrame(py_merge.groupby(['type']).agg({'ride_id':'nunique'})).reset_index()
ride_sum['type_key'] = ""
ride_sum.loc[ride_sum['type'].str.contains('Urban'), 'type_key'] = 'lightcoral'
ride_sum.loc[ride_sum['type'].str.contains('Suburban'), 'type_key'] = 'lightskyblue'
ride_sum.loc[ride_sum['type'].str.contains('Rural'), 'type_key'] = 'gold'

# Build Pie Chart
plt.pie(ride_sum['ride_id'],
        labels=ride_sum['type'],
        colors=ride_sum['type_key'], 
        autopct="%1.1f%%", 
        shadow=True,
        explode=[0, 0, .1], 
        startangle=145)
plt.title("% Total Rides by City Type")

#save figure
plt.savefig('fares_by_city_type.jpg')

# show plot
plt.show()
```


![png](output_12_0.png)


## Total Drivers by City Type


```python
# create DataFrame storing driver counts on which the pie chart might be built
driver_sum = pd.DataFrame(city_raw.groupby('type').agg({'driver_count':'sum'})).reset_index()
driver_sum['type_key'] = ""
driver_sum.loc[driver_sum['type'].str.contains('Urban'), 'type_key'] = 'lightcoral'
driver_sum.loc[driver_sum['type'].str.contains('Suburban'), 'type_key'] = 'lightskyblue'
driver_sum.loc[driver_sum['type'].str.contains('Rural'), 'type_key'] = 'gold'

# Build Pie Chart
plt.pie(driver_sum['driver_count'],
        labels=ride_sum['type'],
        colors=ride_sum['type_key'], 
        autopct="%1.1f%%", 
        shadow=True,
        explode=[0, 0, .1], 
        startangle=145)
plt.title("% Total Drivers by City Type")

#save figure
plt.savefig('drivers_by_city_type.jpg')

# show plot
plt.show()
```


![png](output_14_0.png)

