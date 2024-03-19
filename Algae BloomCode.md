#Calculating Algae Bloom from Sentinel 2 in R

---
title: "Calculating Algae Bloom from Sentinel 2 in R"
Author: "Kingsle Kanjin"
date: "2024-03-14"
---
library(raster)
library(RStoolbox)
library(XML)
library(dplyr)
library(readr)
library(magrittr)
library(stringr)
library(sf)
library(cluster)
Warning: package ‘cluster’ was built under R version 4.3.3
# Set working directory
setwd("E:\\Sentinel202306")
# Define file paths (replace placeholders with actual file paths)
red_band_file <- "E:\\Sentinel202306\\R10m\\T17TLG_20231006T162049_B04_10m.jp2"
red_edge1_band_file <- "E:\\Sentinel202306\\R20m\\T17TLG_20231006T162049_B05_20m.jp2"
# Read Sentinel-2 bands
red_band <- raster(red_band_file)
red_edge1_band <- raster(red_edge1_band_file)
# Resample Red Edge 1 band to match the resolution of the Red band
red_edge1_resampled <- resample(red_edge1_band, red_band, method = "bilinear")
# Define file path for saving resampled raster
resampled_file <- "E://Sentinel202306/red_edge1_resampled.tif"

# Write resampled raster to disk
writeRaster(red_edge1_resampled, filename = resampled_file, format = "GTiff", overwrite = TRUE)
# Define threshold for water classification
water_threshold <- 0.1  # Adjust threshold as needed

# Classify Red band into water and non-water areas based on threshold
red_band_classified <- red_band <= water_threshold

# Convert classified image to polygon
red_polygon <- rasterToPolygons(red_band_classified, dissolve = TRUE)

# Keep only the water polygon
water_polygon <- red_polygon[red_band_classified[], ]
plot(red_classified)
plot(water_polygon)
# Use water polygon to clip Red band image
red_band_clipped <- mask(red_band, water_polygon)
rededge1_band_clipped <- mask(red_edge1_resampled, water_polygon)
# Calculate NDCI
ndci <- (rededge1_band_clipped - red_band_clipped) / (rededge1_band_clipped + red_band_clipped)
# Visualize NDCI results
plot(ndci, main = "Normalized Difference Chlorophyll Index (NDCI)", 
     col = rev(terrain.colors(100)), legend = FALSE)
ndci_palette <- colorRampPalette(c("red", "yellow", "green", "darkgreen"))

plot(ndci, 
     col=ndci_palette(100), 
     axes=FALSE)
hist(ndci)
writeRaster(ndci,'ndci.tif',options=c('TFW=YES'),overwrite=TRUE)

