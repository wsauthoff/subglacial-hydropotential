[![DOI](https://zenodo.org/badge/986576162.svg)](https://doi.org/10.5281/zenodo.16243278)

# Subglacial hydropotential

## About the dataset

This repository contains code and metadata related to the generation of a 500-m gridded Antarctic subglacial hydropotential dataset, derived from variables in the BedMachine Antarctica dataset (Morlighem et al., 2020; Morlighem, 2022).

![Antarctic subglacial hydropotential map](output/subglacial_hydropotential_Antarctica.png)

## Data access

Dataset is downloadable from [Zenodo](https://doi.org/10.5281/zenodo.16243279) in the following formats:

Filename | Format | Notes |
|--------|--------|-------------|
|subglacial_hydropotential_Antarctica.nc| NetCDF4 | Chunked and compressed |
|subglacial_hydropotential_Antarctica.zarr.zip| Zarr | Cloud-optimized, consolidated metadata |

Both versions include:
- Variable: `hydropotential` [kPa], gridded at 500 m resolution
- Coordinates: `x`, `y` in EPSG:3031 (Polar Stereographic South)
- Fill value for missing data: `NaN` (e.g., grid cells not considered under grounded ice)

## Subglacial hydropotential (θₕ)
Subglacial hydropotential (hydraulic potential) represents the total force per unit area exerted on subglacial water by gravitational potential energy and ice overburden pressure. Hydropotential is a key control on basal water flow direction and magnitude beneath ice sheets.

This dataset provides a stationary field of hydropotential computed as (following Shreve, 1972):

**θg** = ρ<sub>water</sub> ⋅ g ⋅ z<sub>bed</sub>  
*(gravitational potential)*

**θₕ** = ρ<sub>water</sub> ⋅ g ⋅ z<sub>bed</sub> + (ρ<sub>ice</sub> ⋅ g ⋅ h<sub>ice</sub>)  
*(combine terms; add ice overburden)*

**θₕ** = g ⋅ [ρ<sub>water</sub> ⋅ z<sub>bed</sub> + ρ<sub>ice</sub> ⋅ (z<sub>surface</sub> − z<sub>bed</sub>)] − N  
*(factor g from expression; add effective pressure)*

> Effective pressure is the ice overburden pressure minus the basal water pressure; this variable often poorly assumed to be zero because of lack of observations

**θₕ** = g ⋅ [ρ<sub>water</sub> ⋅ z<sub>bed</sub> + (ρ<sub>ice</sub> ⋅ z<sub>surface</sub>) − (ρ<sub>ice</sub> ⋅ z<sub>bed</sub>)]  
*(ignore effective pressure; distribute ρ<sub>ice</sub>)*

**θₕ** = g ⋅ [ρ<sub>water</sub> ⋅ z<sub>bed</sub> − (ρ<sub>ice</sub> ⋅ z<sub>bed</sub>) + (ρ<sub>ice</sub> ⋅ z<sub>surface</sub>)]  
*(fareorder terms)*

**θₕ** = g ⋅ [(ρ<sub>water</sub> − ρ<sub>ice</sub>) ⋅ z<sub>bed</sub> + (ρ<sub>ice</sub> ⋅ z<sub>surface</sub>)]  
*(combine z<sub>bed</sub> terms)*

**θₕ** = g ⋅ [(997 − 917 kg/m³) ⋅ z<sub>bed</sub> + (917 kg/m³ ⋅ z<sub>surface</sub>)]  
*(input values of assumed ρ<sub>water</sub> and ρ<sub>ice</sub>)*

**θₕ** = g ⋅ [(80 kg/m³ ⋅ z<sub>bed</sub>) + (917 kg/m³ ⋅ z<sub>surface</sub>)]  
*(simplify)*

**θₕ** = g ⋅ [(80 kg/m³ ⋅ z<sub>bed</sub>) + (917 kg/m³ ⋅ (z<sub>surface</sub> - FAC))]  
*(subtract firn air content (FAC) from ice surface elevation to accurately quantify the mass of overlying ice)*

where:
- θg, θₕ = potential, graviational and hydro-
- ρ_water, ρ_ice = densities of freshwater and ice
- g = 9.8 m/s² (acceleration due to gravity)
- z<sub>bed</sub> = bed elevation (from BedMachine Antarctica)
- z<sub>surface</sub> = ice surface elevation (ice surface model used by BedMachine Antarctica)
- FAC = firn air content (FAC model used by BedMachine Antarctica)

### Unit Analysis

Using kg/m³ for density:

m/s² * (kg/m³ * m + kg/m³ * m)

→ m/s² * (kg/m² + kg/m²)

→ m/s² * kg/m²

→ kg / (m·s²) = Pa

Pa = N/m² = force / area (pressure)

Calculated hydropotential values are expressed in kilopascals (kPa) for human readable.

## Usage example (Python)

```python
import xarray as xr
import zipfile

# For NetCDF
# Open dataset with xarray and plot
ds_nc = xr.open_dataset("subglacial_hydropotential_Antarctica.nc")
hp = ds_nc["hydropotential"]
hp.plot()

# For Zarr
# Extract the zip file
with zipfile.ZipFile('output/subglacial_hydropotential_Antarctica.zarr.zip', 'r') as zip_ref:
    zip_ref.extractall('output/subglacial_hydropotential_Antarctica.zarr')

# Open dataset with xarray and plot
ds_zarr = xr.open_zarr("subglacial_hydropotential_Antarctica.zarr", consolidated=True)
hp = ds_zarr["hydropotential"]
hp.plot()
```

## Citation

Please cite the dataset as:

    Sauthoff, W., & Siegfried, M. R. (2025). Antarctic subglacial hydropotential [Data set]. Zenodo. https://doi.org/10.5281/zenodo.16243278

## License

* Code: Licensed under GPL-3.0 (see LICENSE-CODE)
* Data: Licensed under CC-BY-SA-4.0 (see LICENSE-DATA)

## References

Morlighem, M., Rignot, E., Binder, T., Blankenship, D., Drews, R., Eagles, G., et al. (2020). Deep glacial troughs and stabilizing ridges unveiled beneath the margins of the Antarctic ice sheet. *Nature Geoscience*, 13(2), 132–137. https://doi.org/10.1038/s41561-019-0510-8

Morlighem, M. (2022). MEaSUREs BedMachine Antarctica. (NSIDC-0756, Version 3). [Data Set]. Boulder, Colorado USA. NASA National Snow and Ice Data Center Distributed Active Archive Center. https://doi.org/10.5067/FPSU0V1MWUB6. Date Accessed 05-19-2025.

Shreve, R. L. (1972). Movement of Water in Glaciers. *Journal of Glaciology*, 11(62), 205–214. https://doi.org/10.3189/S002214300002219X
