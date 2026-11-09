# Coastal Substrate Dataset (South Korea, Sentinel-2)

Pixel-level labels of coastal substrate (sand, gravel, mud and rock) for 743 Sentinel-2 Level-2A image tiles from five coastal regions of South Korea. The labels were transcribed from the 1:10,000 coastal substrate survey maps of the Korea Hydrographic and Oceanographic Agency (KHOA). Each tile is 256 × 256 pixels at 10 m (2.56 km × 2.56 km) with nine spectral bands, and each tile has two label rasters: the raw map legend codes and a five-class target (background, sand, gravel, mud, rock). For every map sheet, the Sentinel-2 scene was chosen to be cloud-free over the mapped substrate and, in tidal regions, close to low tide.

## Source data

- Labels: KHOA 1:10,000 coastal substrate survey maps (연안해역 재질분포도), survey years 2015–2020. Each map sheet was rendered, its legend colours were matched to classes and the sheet was georeferenced from its printed corner coordinates; the sheets were rasterised at 2 m and reduced to the 10 m tile grid.
- Images: Sentinel-2 Level-2A surface reflectance, read from the public Sentinel-2 cloud-optimised GeoTIFFs on AWS through the Element 84 Earth Search STAC API. Acquisitions from 2017 to 2020.

## Scene selection

One Sentinel-2 scene was chosen for each map sheet:

1. Candidates are same-day, same-orbit mosaics of Level-2A granules covering the sheet. A mosaic pixel is valid only if all nine bands are greater than 0.
2. Cloud screening uses OmniCloudMask on B04, B03 and B8A. Thick cloud, thin cloud and cloud shadow, dilated by 3 pixels, count as bad. A candidate passes if bad pixels are at most 2 % of the sheet extent and at most 0.5 % of the mapped substrate.
3. Candidates are taken from the survey year of the sheet first; the window is widened to ±1, ±2 and ±3 years only when no candidate passes.
4. Among passing candidates, tidal regions (West Sea and South Sea, South-east coast) take the scene with the lowest tide level at acquisition, predicted from the KHOA harmonic constants (M2, S2, K1, O1) of the regional tide station. The East Sea takes the scene with the least cloud over the mapped substrate.
5. Scenes from eight acquisition dates that passed the cloud screening but showed a bright sea surface were excluded after visual inspection.

## Water mask

Water is defined on the image itself at acquisition time: NDWI = (B03 − B08) / (B03 + B08) ≥ 0. In `labels_raw` such pixels have code 254; in `labels_target5` they are background (0). Pixels where any image band is 0, or whose map code is unknown, are 255 (ignore) in both label rasters.

## Tiles

| Property | Value |
|---|---|
| Tiles | 743 |
| Size | 256 × 256 pixels, 10 m, 2.56 km × 2.56 km, non-overlapping |
| CRS | EPSG:32652 (WGS 84 / UTM zone 52N) |
| Image bands | B04, B03, B02, B08, B05, B06, B07, B11, B12 (uint16, reflectance = DN / 10000, nodata 0); B05, B06, B07, B11 and B12 were resampled from 20 m to 10 m by bilinear interpolation |
| Labels | uint8, nodata 255 |

| Region | Tiles | Area (km²) | Survey years | Acquisition dates | Background px | Sand px | Gravel px | Mud px | Rock px | Ignore px |
|---|---:|---:|---|---:|---:|---:|---:|---:|---:|---:|
| West Sea (Taean) | 32 | 209.7 | 2017 | 1 | 1,496,624 | 113,343 | 7,973 | 1,534 | 49,845 | 427,833 |
| South Sea western | 95 | 622.6 | 2015, 2017, 2018 | 4 | 4,769,913 | 29,782 | 4,607 | 147,848 | 55,623 | 1,218,147 |
| South Sea central | 249 | 1,631.8 | 2018, 2019 | 8 | 12,156,025 | 24,462 | 76,775 | 364,249 | 103,676 | 3,593,277 |
| South-east coast | 211 | 1,382.8 | 2019, 2020 | 6 | 10,745,827 | 70,141 | 101,622 | 73,558 | 82,753 | 2,754,195 |
| East Sea | 156 | 1,022.4 | 2015, 2016, 2017 | 5 | 6,989,797 | 56,422 | 3,572 | 3,319 | 52,053 | 3,118,453 |
| Total | 743 | 4,869.3 | 2015, 2016, 2017, 2018, 2019, 2020 | 18 | 36,158,186 | 294,150 | 194,549 | 590,508 | 343,950 | 11,111,905 |

Area is the tile area (tiles × 6.5536 km²). Pixel counts are from `labels_target5`.

## Class codes

`labels_raw` (KHOA legend codes):

| Code | Class | KHOA legend | KHOA legend (Korean) | Target5 | Pixels |
|---:|---|---|---|---:|---:|
| 1 | sand | sand beach; sea sand | 사빈; 해역 모래 | 1 | 294,150 |
| 2 | gravel | gravel (land); riprap; gravel (sea) | 육역 자갈; 사석; 해역 자갈 | 2 | 194,549 |
| 3 | mud | mud (land); mud (sea); sandy mud (sea; 2015 sheets) | 육역 뻘; 해역 뻘; 해역 모래뻘(2015 도엽) | 3 | 590,508 |
| 4 | rock | rock (land); bedrock (sea) | 암석; 해역 암반 | 4 | 343,950 |
| 5 | river | river | 하천 | 0 | 205,446 |
| 6 | road | road | 도로 | 0 | 2,099,122 |
| 7 | tetrapod | tetrapod (TTP) | TTP | 0 | 18,993 |
| 8 | soil | soil | 토양 | 0 | 462,236 |
| 9 | building | building | 건물 | 0 | 557,409 |
| 10 | farmland | farmland | 농경지 | 0 | 2,657,301 |
| 11 | forest | forest | 산림 | 0 | 8,711,971 |
| 254 | water | not a legend class: NDWI >= 0 on the image at acquisition |  | 0 | 21,445,708 |
| 255 | ignore | not a legend class: unlabelled or invalid pixel |  | 255 | 11,111,905 |

`labels_target5`:

| Code | Class | Pixels |
|---:|---|---:|
| 0 | background | 36,158,186 |
| 1 | sand | 294,150 |
| 2 | gravel | 194,549 |
| 3 | mud | 590,508 |
| 4 | rock | 343,950 |
| 255 | ignore | 11,111,905 |

The same table is in `metadata/class_codes.csv`.

## Files

```
dataset/images/<tile_id>.tif          9-band image
dataset/labels_raw/<tile_id>.tif      KHOA legend codes, 254 water, 255 ignore
dataset/labels_target5/<tile_id>.tif  0 background, 1 sand, 2 gravel, 3 mud, 4 rock, 255 ignore
```

The rasters are in the release asset. This repository holds the metadata:

| File | Content |
|---|---|
| `metadata/tiles.csv` | Tile id, grid row and column, CRS, geotransform, region, survey years, contributing map sheets with their pixel counts, pixels per class, cloud indicators (OmniCloudMask and Sentinel-2 SCL percentages over the tile) |
| `metadata/tile_scenes.csv` | Sentinel-2 granule ids and acquisition times of each tile, with the pixels from each granule |
| `metadata/scene_selection.csv` | Selected scene of each map sheet, its tide level, cloud measures and candidate counts |
| `metadata/class_codes.csv` | Pixel values of both label rasters and their classes |
| `metadata/checksums.csv` | Path, size and sha256 of every raster in the release asset |

## Download and verification

Download `coastal_substrate_dataset_v1.0.tar.gz` and `SHA256SUMS` from the v1.0 release (599,004,464 bytes), then:

```
sha256sum -c SHA256SUMS
tar -xzf coastal_substrate_dataset_v1.0.tar.gz
tail -n +2 metadata/checksums.csv | awk -F, '{print $3"  "$1}' | sha256sum -c --quiet && echo OK
```

The last command runs in a folder that holds both `dataset/` and this repository's `metadata/`; it checks all 2,229 rasters.

## Licence

- Data: Creative Commons Attribution 4.0 International (CC BY 4.0), see `DATA_LICENSE.md`.
- The labels are derived from KHOA coastal substrate survey maps and are redistributed with the permission of KHOA.
- Contains modified Copernicus Sentinel data [2017–2020].

## Citation

Jung, W., Chun, M., Lee, J., Kim, Y., Han, J. H. (2026). Coastal Substrate Dataset (South Korea, Sentinel-2), v1.0. https://github.com/Jung-woojin/coastal-substrate-dataset

A manuscript describing the dataset is in preparation.
