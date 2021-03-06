
<!-- README.md is generated from README.Rmd. Please edit that file -->

# animal-tracks

The goal of animal-tracks is to provide some animal tracks data sets in
ready to use data frame form.

Create ellie and walrus from existing R packages.

``` r
## bsam package
## two (id) elephant seals in data frame  (id, date, lc, lon, lat)
ellie <- bsam::ellie 

## trip package
## several (Deployment) walrus tracks (X_AED170_70 Y_AED170_70, DataDT)
trip::walrus818 
library(trip)
walrus <- tibble::as_tibble(as.data.frame(walrus818))


saveRDS(ellie, "ellie.rds")
saveRDS(walrus, "walrus.rds")
```

The ellie\_IMOS data set was obtained from IMOS, see
ref.

``` r
## IMOS elephant seal data, used in Jonsen et al. 2018 doi:https://doi.org/10.1101/314690
## 24 (id) seals in data frame (id, date, lc, lon, lat, trip)
## `trip` denotes an ice-bound or pelagic foraging trip, all originating from Isles Kerguelen
ellie_IMOS <- tibble::as_tibble(readRDS("ellie_IMOS.RDS"))
```

## KML

The `ellie_IMOS` data set is in KML form in this subfolder “/kml/”.

## SST

There is a reduced resolution OISST data set matching the ellie\_IMOS
tracks in sst/. This is 5-day sampled from 1-day, and reduced to 0.5
degree resolution from 0.25.

``` r
library(raster)
sst <- readRDS("sst/ellie-sst.rds")

## beware, 100Mb of expanded raster here
## turn into data.frame with  lon, lat, and date (dimindex)
library(tabularaster)
library(dplyr)
tabularaster::as_tibble(sst, cell = FALSE, xy = TRUE) %>% 
 dplyr::filter(!is.na(cellvalue)) %>% 
 dplyr::rename(sst = cellvalue, date = dimindex)
# A tibble: 3,315,200 x 4
#     sst date                    x     y
#   <dbl> <dttm>              <dbl> <dbl>
# 1  11.8 2013-02-14 19:43:56  -1.5 -42.8
# 2  11.8 2013-02-14 19:43:56  -1   -42.8
# 3  11.9 2013-02-14 19:43:56  -0.5 -42.8
# 4  12.1 2013-02-14 19:43:56   0   -42.8
# 5  12.2 2013-02-14 19:43:56   0.5 -42.8
# ...
```

## ICE

NSIDC 25km ice concentration reduced to ~100km contours of the 15%
concentration daily for the ellie\_IMOS data.

``` r
library(sf)
#> Linking to GEOS 3.6.2, GDAL 2.3.0, proj.4 4.9.3
ice_cont <- readRDS("ice/ellie-ice-contour15pc.rds")
plot(ice_cont$geometry, col = rgb(0, 0, 0, 0.05))
axis(1)
axis(2)
```

![](README_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
range(ice_cont$date)
#> [1] "2013-02-14 19:43:56 GMT" "2015-11-06 19:43:56 GMT"
```
