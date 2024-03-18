#Calculating Algae Bloom from Sentinel 2 in R

---
title: "Calculating Algae Bloom from Sentinel 2 in R"
Author: "Kingsle Kanjin"
date: "2024-03-14"
---

```{r Installatioms of sen2r package}
install.packages("remotes")
remotes::install_github("ranghetti/sen2r")
install.packages("sen2r")
```


```{r,warning=FALSE, results='hide', message=FALSE}

library(raster)
library(sp)
library(readr)
library(magrittr)
library(stringr)
library(grDevices)
library(sen2r)

```


```{r}
red_band_path <- "path of red band data"
nir_band_path <- "path of nir data"

```

```{r}
# Load the bands as raster layers
red_band <- raster(red_band_path)
nir_band <- raster(nir_band_path)
```

```{r}
# Calculate NDVI
ndvi <- (nir_band - red_band) / (nir_band + red_band)
```


```{r}
#Calculating FAI: FAI is specific to floating algae on water surfaces
fai <- nir_band - (red_band + ((nir_band - red_band) * ((850 - 665) / (1610 - 665))))
```


```{r}
#Masking Non-Water Areas
# Example pseudo code for masking (adjust based on your data and method)
water_mask <- raster("path/to/water_mask.tif")
ndvi_water <- mask(ndvi, water_mask)
fai_water <- mask(fai, water_mask)
```

```{r}
#Visualize
plot(ndvi, main="NDVI for Algae Bloom Detection")
#plot(fai_water, main="FAI for Algae Bloom Detection")
