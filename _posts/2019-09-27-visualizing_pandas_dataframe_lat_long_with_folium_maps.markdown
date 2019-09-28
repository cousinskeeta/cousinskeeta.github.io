---
layout: post
title:      "Visualizing Pandas DataFrame Lat/Long with Folium Maps"
date:       2019-09-28 01:25:35 +0000
permalink:  visualizing_pandas_dataframe_lat_long_with_folium_maps
---


At the time of writing this blog post, I am almost two months in at the Flatiron School. 

We have learned how to use python and github. We have learned how to import libraries and much more! Our first project is building a multilinear regression modelto predict housing prices in King County, but this post will focus on ploting lat and longs from pandas dataframes. 

We were introduced to folium in our prep-course, prior to starting the class. I used a similar  technique but made some changes for my purposes.

I started with a dataframe that already included Lat and Long data points. 

Here is a sample of my code:

# 1. Plotting Latitude and Longiture with Folium Maps
### Importing Folium and Numpy Libraries

    import folium 
    import numpy as np

### Defining Mid-points of the Map 

    lat = data['lat'].mean() 
    lon = data['lon'].mean() 

### Defining Variables to Store all Lats and all Lons

    lat = data['lat']
    lon = data['lon']

### Initiating map with Mid-points

    map = folium.Map(location=[lat, lon], zoom_start=10.25)

### Pairing Lats and Longs: (lats, lons)

     lats_lons = list(zip(lats,lons)) 

### Function to Create and Add Each Marker to the Map

    def make_add(coordinates, the_map):
        """Creates markers for all `coordinates` passed, and adds onto `the_map`.  """
    
        markers = {}
        for i, ea in enumerate(coordinates):
            markers[i] = folium.CircleMarker(location = ea,radius=.05, tiles='stamentoner')
            markers[i].add_to(the_map) 

### Initializing the Function

    markers = make_add(lats_lons, map) 

### Calling the Map

    map 

### Saving the Map

    map.save('map.html') 

### Docustring for Created Function: make_add() 

    make_add.__doc__


# 2. Plotting Lats/Longs using HeatMap Feature
### Importing `plugins` from `folium` to use HeatMap

    import folium.plugins as plugins 
		
### Initiating map with mid points

    new_map = folium.Map(location=[lat, lon], zoom_start=10.25)

### Calling the HeatMap

    new_map

### Creating HeatMap: loc=Lats/Lons; hue= View 

    locations = list(zip(lats,lons,new_kd.view_cat)) 
    hm = plugins.HeatMap(data=locations, radius=8 , blur=4.8, overlay=True) 
										 
### Adding HeatMap Overlay to Map

    hm.add_to(new_map) 

### Saving HeatMap as Interactive HTML

    new_map.save('heatmap.html')


Let me know if this works for you, or if you found any errors in my code. Like/Share/Comment!


Thanks for reading!

