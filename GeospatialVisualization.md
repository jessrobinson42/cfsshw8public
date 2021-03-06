Geospatial Visualization
================
Jess Robinson
May 27, 2019

``` r
#settings
knitr::opts_chunk$set(fig.width=10, fig.height=8, fig.path='Figs/',
                       warning=FALSE, message=FALSE)
```

``` r
#load libraries
library(tidyverse)
library(readr)
library(here)
library(knitr)
library(sf)
library(viridis)

set.seed(1234)
theme_set(theme_minimal())
```

Load and Clean Data
-------------------

``` r
#load shapefile
communityareas <- here("Boundaries", "geo_export_42f4e02e-8121-4598-8172-df4c6ef9896c.shp") %>%
  st_read()
```

    ## Reading layer `geo_export_42f4e02e-8121-4598-8172-df4c6ef9896c' from data source `/Users/Jessrobinson42/Desktop/CFSS/HW08/Boundaries/geo_export_42f4e02e-8121-4598-8172-df4c6ef9896c.shp' using driver `ESRI Shapefile'
    ## Simple feature collection with 77 features and 9 fields
    ## geometry type:  MULTIPOLYGON
    ## dimension:      XY
    ## bbox:           xmin: -87.94011 ymin: 41.64454 xmax: -87.52414 ymax: 42.02304
    ## epsg (SRID):    4326
    ## proj4string:    +proj=longlat +ellps=WGS84 +no_defs

``` r
#load datasets
hiv_data <- read_csv(here("HIV_data.csv"))
clinics <- read_csv(here("clinics.csv"))
```

Join Data
---------

``` r
hiv_area <- communityareas %>%
  left_join(hiv_data, by = c("community" = "Geo_ID"))
```

Map Data
--------

``` r
#make choropleth
ggplot(data = hiv_area) + 
  geom_sf(aes(fill = Crude_Rate, color = Crude_Rate)) +
    scale_color_viridis(option = "magma") + 
   scale_fill_viridis(option = "magma") + 
#add point data
  geom_point(data = clinics, aes(x = Longitude, y = Latitude), 
             shape = 5, color = "Black", stroke = 4) + 
# add legends 
   labs(title = "HIV Inicidence and Location of Public Health Clinics",
       subtitle = "Chicago, IL",
       color = "Crude Rate", 
       fill = "Crude Rate",
       caption = "Source: Chicago Data Portal and Chicago Health Atlas")
```

![](Figs/unnamed-chunk-3-1.png)

The above map shows the location of public health clinics and of HIV incidence rates by community area within Chicago during 2015. The public health clinic location data comes from the Chicago data portal, while the HIV incidence data comes from the Chicago Health Atlas. The community area boundaries for this map also come from the Chicago Data Portal. On the map, you can see the crude rate of HIV incidence, calculated as number of new cases by population, represented in different shades. Meanwhile, the clinics are represented by diamond-shaped points on the map. The metric of HIV incidence rather than HIV prevalence was chosen in order to represent the spread of disease, thereby suggesting potential areas for intervention.

From this graph, we can see which areas of the city of Chicago currently have high rates of HIV incidence. Most notably, the community areas Uptown, Edgewater in the northeast of Chicago and Grand Boulevard, Greater Grand Crossing, and Chatham on the south side of Chicago have some of the highest rates of HIV incidence. Meanwhile, most of the rest of the north side in general has relatively low rates of HIV incidence. One limitation here is that there is often great diversity in any given community area. From this map we can also see that there are some of the most public health clinics in the center of Chicago, both on the near west side and the near south side, as well as along the northeast coast, with less on the northwest side and far south side. However, another limitation of this map is that the dataset is that the Chicago Health Atlas may not have an accurate listing of all public health clinics.

Despite these limitation, there are nonetheless some policy implications which can be drawn from this map. In particular, this map suggests that the need for more public health clinics in community areas with high HIV incidences. In particular, this map suggests that the community areas of Greater Grand Crossing, Chatham, and Edgewater with some of the highest rates of HIV incidence in the city as of 2015, might benefit from an public health clinics.
