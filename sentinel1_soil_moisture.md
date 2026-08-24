# Soil Moisture Analysis Project

## Overview

This project investigates soil moisture patterns using satellite-derived data. The objective is to process, analyze, and visualize soil moisture observations to better understand spatial and temporal variations within the study area.

## Objectives

- To acquire and preprocess Sentinel-1 SAR imagery for selected pre- and post-flood periods.
- To analyse VV and VH backscatter to identify spatial variations in surface moisture conditions.
- To classify areas into relative wet, moderate, and dry conditions.
- To produce soil moisture maps that support flood-impact assessment and environmental monitoring.

## Data Source

**Platform:** Digital Earth Pacific (DEP)

**Dataset:** Sentinel-1 Ground Range Detected (GRD) Synthetic Aperture Radar (SAR) Imagery

**Provider:** European Space Agency (ESA)

**Access Method:** Element 84 STAC Catalog

**Analysis Period:** 1 July 2024 to 30 June 2025

**Bands Used:**
- VV Polarization
- VH Polarization


## Tools and Libraries Used

### Environment
- Jupyter Notebook
- Digital Earth Pacific (DEP)

### Python Libraries

- **GeoPandas (`geopandas`)**
  - Used for handling and analyzing geospatial vector data.

- **Pandas (`pandas`)**
  - Used for data manipulation and tabular data analysis.

- **NumPy (`numpy`)**
  - Used for numerical computations and array operations.

- **Matplotlib (`matplotlib`)**
  - Used for data visualization and plotting.

- **ODC-STAC (`odc.stac`)**
  - Used to load and process Earth observation datasets from STAC catalogs.

- **PySTAC Client (`pystac_client`)**
  - Used to connect to and query the STAC catalog.

- **Xarray (`xarray`)**
  - Used to manage and analyze multidimensional satellite data.

- **SciPy (`scipy.ndimage`)**
  - Used for image processing and raster filtering operations.

- **LinearSegmentedColormap (`matplotlib.colors`)**
  - Used to create custom colour maps for visualizing soil moisture and flood-related outputs.

## Workflow

### Soil Moisture Mapping Workflow Using Sentinel-1 SAR Data

#### 1. Study Area Definition
- Import the Yalelevu/Namosau Area of Interest (AOI) from a GeoJSON file.
- Reproject the AOI to WGS84 (EPSG:4326).
- Extract the bounding box for satellite data retrieval.


#### 2. Sentinel-1 Data Acquisition
- Connect to the Element84 STAC catalog.
- Search and retrieve Sentinel-1 Ground Range Detected (GRD) imagery.
- Define the analysis period:
  - **1 July 2024 – 30 June 2025**
- Filter imagery based on:
  - IW acquisition mode
  - Ascending orbit
  - VV and VH polarizations


#### 3. VV Backscatter Preparation
- Load Sentinel-1 imagery using ODC-STAC.
- Extract the VV polarization band.
- Convert VV backscatter from linear units to decibels (dB):

\[
VV_{dB} = 10 \log_{10}(VV)
\]

- Compute the temporal mean VV backscatter image.

#### 4. Water Body Detection and Masking
- Analyze the VV backscatter distribution.
- Apply a threshold to identify open water areas.
- Generate a water mask to separate water from land.
- Exclude water pixels from further soil moisture analysis.


#### 5. Speckle Noise Reduction
- Apply a 5 × 5 Lee Filter to the VV backscatter image.
- Reduce SAR speckle noise while preserving landscape features.


#### 6. Preliminary Wet–Dry Assessment
- Calculate the 25th and 75th percentiles of land VV values.
- Classify pixels into:
  - Wet
  - Moderate
  - Dry
- Generate an initial soil moisture condition map.


#### 7. Digital Elevation Model (DEM) Processing
- Load the Copernicus DEM (30 m).
- Create an elevation mask:
  - Retain areas below 30 m elevation.
  - Remove higher terrain.
- Focus the analysis on flood-prone lowland areas.



#### 8. Soil Moisture Index (SMI) Calculation
- Determine wet and dry reference backscatter values from the VV distribution.
- Calculate the Soil Moisture Index (SMI):

\[
SMI = \frac{VV - VV_{dry}}{VV_{wet} - VV_{dry}}
\]

Where:

- \(VV\) = observed backscatter
- \(VV_{dry}\) = dry reference backscatter
- \(VV_{wet}\) = wet reference backscatter



#### 9. SMI Normalization
- Normalize SMI values to a range between 0 and 1.

| SMI Value | Interpretation |
|------------|----------------|
| 0 | Driest conditions |
| 1 | Wettest conditions |



#### 10. DEM-Based Masking
- Apply the elevation mask to the SMI layer.
- Exclude upland areas from the final analysis.
- Retain only lowland regions susceptible to flooding and waterlogging.



#### 11. Soil Moisture Classification
Classify SMI values into three categories:

| Class | SMI Range |
|---------|-----------|
| Dry | < 0.3 |
| Moderate | 0.3 – 0.7 |
| Wet | > 0.7 |

- Calculate the percentage area occupied by each class.



## 12. Visualization and Interpretation
Generate the following outputs:

- Mean VV backscatter map
- Water mask map
- Wet–moderate–dry classification map
- Soil Moisture Index (SMI) map
- DEM-masked SMI map
- Soil moisture percentage statistics

Interpret the spatial distribution of soil moisture conditions across the study area.


## 13. Export Results
- Assign the coordinate reference system (EPSG:4326).
- Export the final DEM-masked SMI layer as a GeoTIFF.
- Save outputs for further GIS analysis and reporting.


# Workflow Summary

```text
Study Area Definition
        ↓
Sentinel-1 Data Acquisition
        ↓
VV Backscatter Extraction
        ↓
dB Conversion
        ↓
Water Masking
        ↓
Lee Filtering
        ↓
Wet/Dry Reference Determination
        ↓
DEM Processing
        ↓
SMI Calculation
        ↓
SMI Classification
        ↓
Visualization
        ↓
GeoTIFF Export
```


## What Worked

Several components of the workflow were successfully implemented and produced useful results:

- Sentinel-1 SAR imagery was successfully acquired and processed for the study area.
- VV backscatter data were converted to decibel (dB) values, allowing easier interpretation of surface moisture conditions.
- Water bodies were identified and masked, reducing the influence of open water on soil moisture calculations.
- The Lee Filter effectively reduced speckle noise while preserving major landscape features.
- DEM masking successfully isolated low-lying areas (<30 m elevation), focusing the analysis on flood-prone regions.
- The Soil Moisture Index (SMI) was successfully calculated and normalized between 0 and 1.
- Soil moisture classes (Dry, Moderate, and Wet) were generated and visualized across the study area.
- Area statistics for each soil moisture class were calculated, providing quantitative information on moisture distribution.
- Final soil moisture maps were exported as GeoTIFF files for further analysis in GIS software.

These steps demonstrated that Sentinel-1 VV backscatter can be used to characterize relative soil moisture conditions within the Yalelevu study area and identify spatial patterns of wetter and drier land surfaces.

## What Didn't Work

## Intended Analysis

The objective of this project was to compare soil moisture conditions before and after a flood event.

The planned workflow was:

1. Extract pre-flood soil moisture data.
2. Extract post-flood soil moisture data.
3. Calculate the difference between the two datasets.
4. Use the resulting values to calculate the Soil Moisture Index (SMI).
5. Visualize and interpret the changes associated with the flood event.

## Challenges Encountered

Although the preprocessing steps were completed, I was unable to produce the expected output from the difference analysis and Soil Moisture Index calculations.

The resulting maps and values did not align with the anticipated flood-related soil moisture patterns. Possible reasons include:

- Incorrect preprocessing of the input datasets.
- Spatial or temporal mismatches between the pre-flood and post-flood data.
- Issues with data quality or missing values.
- Errors in the Soil Moisture Index calculation methodology.
- Challenges in interpreting the raster outputs.

As a result, further analysis and validation are required before reliable conclusions can be drawn from the analysis.

## Lessons Learned

This project highlighted the importance of:

- Verifying input datasets before analysis.
- Checking spatial and temporal alignment of data.
- Validating intermediate outputs throughout the workflow.
- Testing the calculation methodology on smaller datasets before applying it to the full analysis.

## Next Steps

- Review preprocessing and data alignment procedures.
- Verify the Soil Moisture Index formula used in the analysis.
- Perform quality checks on the difference layer.
- Compare results with known flood observations or reference datasets.
- Refine the workflow and rerun the analysis.


## Challenges and Limitations

Several challenges and limitations were encountered during the soil moisture mapping process:

- Sentinel-1 backscatter is influenced by multiple surface characteristics, including soil moisture, vegetation cover, surface roughness, and land management practices. As a result, changes in backscatter cannot always be attributed solely to soil moisture variations.

- The water masking procedure relied on a fixed VV backscatter threshold. Threshold selection can be subjective and may not accurately capture all water bodies under varying environmental conditions.

- Despite applying a Lee Filter, some residual speckle noise remained in the SAR imagery, which may affect local soil moisture estimates.

- The Soil Moisture Index (SMI) represents relative moisture conditions rather than actual volumetric soil moisture measurements. Therefore, results should be interpreted as indicators of wetter and drier areas rather than absolute soil moisture values.

- The wet and dry reference values used in the SMI calculation were derived from image statistics, which may not fully represent true field conditions.

- DEM masking excluded higher elevation areas from the analysis. While this helped focus on flood-prone lowlands, it may have removed potentially relevant areas exhibiting different moisture conditions.

- No field-based soil moisture measurements were available for validation. Consequently, the accuracy of the derived soil moisture maps could not be quantitatively assessed.

- Temporal variations in rainfall, vegetation growth, and agricultural activities throughout the study period may have influenced backscatter responses and introduced uncertainty into the soil moisture estimates.

- The classification thresholds used to define Dry, Moderate, and Wet conditions were based on relative SMI values and may require adjustment for application in other locations or time periods.

### Future Improvements

- Incorporate ground-based soil moisture measurements for validation.
- Compare Sentinel-1-derived soil moisture with rainfall and flood records.
- Integrate Sentinel-2 vegetation indices to better separate vegetation effects from soil moisture signals.
- Explore advanced change-detection and machine learning approaches for improved soil moisture estimation.
- Assess seasonal and post-flood soil moisture dynamics using multiple time periods rather than annual averages.


## Lessons Learned

- Understanding dataset metadata is critical before analysis.
- Xarray is effective for working with large climate datasets.
- Visual inspection helps identify preprocessing issues early.
- Documentation throughout the project improves reproducibility.



## Author

Vasemaca Naraukawa

Earth and Ocean Observation Intern