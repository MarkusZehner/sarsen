# Sarsen

Algorithms and utilities for SAR sensors. Enables cloud-native SAR processing via *Xarray* and *Dask*.

This Open Source project is sponsored by B-Open - https://www.bopen.eu.

Sarsen current features:

- provides algorithms to terrain-correct satellite SAR data
  - geometric terrain correction (geocoding)
    - *fast mode*: to terrain-correct images
    - *accurate mode*: for interferometric processing
  - radiometric terrain correction (gamma flattening)
- accesses SAR data via [*xarray-sentinel*](https://github.com/bopen/xarray-sentinel):
  - supports all Sentinel-1 data products as [distributed by ESA](https://scihub.copernicus.eu/dhus/#/home):
    - Sentinel-1 Single Look Complex (SLC) SM/IW/EW
    - Sentinel-1 Ground Range Detected (GRD) SM/IW/EW
  - reads uncompressed and compressed SAFE data products on the local computer or
    on a network via [*fsspec*](https://filesystem-spec.readthedocs.io) - **depends on rasterio>=1.3a3**
- accesses DEM data via [*rioxarray*](https://corteva.github.io/rioxarray):
  - reads local and remote data in virtually any raster format via
    [*rasterio*](https://rasterio.readthedocs.io) / [*GDAL*](https://gdal.org)
- supports larger-than-memory and distributed data access and processing via [*dask*](https://dask.org)

Overall the software is in the **alpha** phase and the usual caveats apply.

## Non-objectives / Caveat emptor

- No attempt is done to support UTC leap seconds. Observations that include a leap second may crash the code or
  silently return wrong results.

## Development focus

Focus until this release has been on algorithmic correctness and comparison with existing tools.
From now on the focus will shift to improving performance and scalability.

## Install

The easiest way to install *sarsen* is in a *conda* environment.
You may create a new environment, activate it, install the package and its dependencies
with the following commands:

```shell
    conda create -n SARSEN
    conda activate SARSEN
    conda install -c conda-forge fsspec dask rioxarray scipy xarray xmlschema
    pip install sarsen
```

## Command line usage

The `sarsen` command line tool corrects SAR data based on a selected DEM and may produce
geometrically terrain-corrected images (GTC) or radiometrically terrain-corrected images (RTC).
The terrain corrected images will have the same pixels as the input DEM, that should be resampled
to the target projection and spacing in advance, for example using `gdalwarp`.

Geometric terrain correct is performed with:

```
  $ sarsen gtc S1B_IW_GRDH_1SDV_20211217T141304_20211217T141329_030066_039705_9048.SAFE IW/VV South-of-Redmond-10m_UTM.tif
```

Performing geometric and radiometric terrain correction is more resoure demanding,
currently it is possible to produce an RTC images of 50km x 50km at a 10m resolution on a 32Gb machine:

```
  $ sarsen rtc S1B_IW_GRDH_1SDV_20211217T141304_20211217T141329_030066_039705_9048.SAFE IW/VV South-of-Redmond-10m_UTM.tif
```

## Python API

The python API has entry points to the same commands and it also gives access to several lower level
algorithms, but internal APIs should not be considered stable:

```python-repl
>>> from sarsen import apps
>>> gtc = apps.backward_geocode_sentinel1(
  "S1B_IW_GRDH_1SDV_20211217T141304_20211217T141329_030066_039705_9048.SAFE",
  measurement_group="IW/VV",
  dem_urlpath="South-of-Redmond-10m_UTM.tif",
)
```

## Contributing

The main repository is hosted on GitHub,
testing, bug reports and contributions are highly welcomed and appreciated:

https://github.com/bopen/sarsen

Lead developer:

- [Alessandro Amici](https://github.com/alexamici) - [B-Open](https://bopen.eu)

Main contributors:

- [Aureliana Barghini](https://github.com/aurghs) - [B-Open](https://bopen.eu)

See also the list of [contributors](https://github.com/bopen/sarsen/contributors) who participated in this project.

## Sponsoring

[B-Open](https://bopen.eu) commits to maintain the project long term and we are happy to accept sponsorships to develop new features.

We wish to express our gratitude to the project sponsors:

- [Microsoft](https://microsoft.com) has sponsored the support for *GRD* products and the *gamma flattening* algorithm.

## License

```
Copyright 2016-2022 B-Open Solutions srl

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
