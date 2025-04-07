# Calculating the relationship between Pacific Ocean variabiity and South American rainfall with correlation analysis
| Python (Xarray, NumPy) | statistics (corrleation analysis, regression analysis) |
| - | - |

![Correlations between Pacific modes and precipitation](/assets/pacific_precip_corr.png)

## Figure purpose
This figure helps to focus the viewer onto key regions of analysis. It identifies regions of South America that are most influenced by changes in sea surface temperature happening remotely in the Pacific Ocean from both model generated data and from observations measured by rain gauges which are part of the Global Precipitation Climatology Centre (GPCC) dataset, and highlights the regions with statistically signficant relationships between the Pacific Ocean and South American rainfall. 

More specifically, this figure highlights the correlations between sea surface temperature time series indices and precipitation derived from modeled data for both a low-frequency mode of variability, the Interdecadal Pacific Oscillation (IPO), and a high-frequency mode of variability, the El Niño Southern Oscillation, ENSO. The shading depicts spatial correlation values between seasonal rainfall amount at every point and the index time series. Statistically significant correlations are indicated by black stippling. The point values overlaid on the maps indicate correlations with precipitation from weather stations. Significant correlations with precipitation gauge data are indicated with a cross. In all cases, statistical significance is at the p < 0.05 level. The black square highlights a region where data were averaged to create a precipitation index.

## Data pre-processing for IPO analysis
For this analysis, the IPO time series was smoothed by 11 years to isolate the low-frequency variability. Therefore, the data correlated against the IPO time series was also smoothed prior to being correlated. 
```
# -------------- FOR GPCC Precipitation
# -------------- DETREND, SAVE AS XR, APPLY 11 YR SMOOTHING
# detrend data, save as xr, smooth
vari_dt = np.zeros(gpcc_pr.shape)
for i in range(len(gpcc_lat)):
    for j in range(len(gpcc_lon)):
        var_tmp = gpcc_pr[:,i,j]
        nas = np.isnan(gpcc_pr[:,i,j])
        if not sum(nas) == 110:
            m, b, r_val, p_val, std_err = stats.linregress(gpcc_pr[:,i,j][~nas].time,gpcc_pr[:,i,j][~nas])
            x = gpcc_pr[:,i,j][~nas].time
            trend_line = m*x+b
            vari_detrended = gpcc_pr[:,i,j][~nas] - trend_line
            vari_dt[:,i,j] = vari_detrended.reindex(time=yrs,fill_value=np.nan)
        else: vari_dt[:,i,j] == gpcc_pr[:,i,j]
        
#------------------- format as an xr Data Array -------------------
var = xr.DataArray(vari_dt, 
    coords={'time': yrs,'latitude': gpcc_lat,'longitude': gpcc_lon}, 
    dims=["time","lat", "lon"], name = 'GPCC Prect')     
#------------------- set up for smoothing -------------------
var_11yr = xr.DataArray(np.zeros([len(yrs), len(gpcc_lat), len(gpcc_lon)]), 
    coords={'time': yrs,'latitude': gpcc_lat,'longitude': gpcc_lon}, 
    dims=["time","lat", "lon"], name = 'GPCC Prect')   
        
#------------------- 11 year smoothing to match TPI -------------------
nt = len(var.time)
filter_11yr = 11
mirror = int((filter_11yr-1)/2) # end point
copy = np.zeros((nt+2*mirror))
for i in range(len(gpcc_lat)):
    for j in range(len(gpcc_lon)):
        copy[mirror:-mirror] = var[:,i,j].copy()
        copy[0:mirror] = var[1:mirror+1,i,j].copy()[::-1]
        copy[-mirror:] = var[-1-mirror:-1,i,j].copy()[::-1]
        avg_mask_11yr = np.ones(filter_11yr) / np.real(filter_11yr)
        var_11yr[:,i,j] = np.convolve(copy, avg_mask_11yr, 'same')[mirror:-mirror]
        
vars_all_dt.append(var)
vars_all_11yr.append(var_11yr)
```

# Correlation analysis and statistical significance for IPO and ENSO
The below code highlights the correlation and determines the hatching for locations of statistical significance in the final plotting. Note that this code evaluates multiple variables in a loop, not just precipitation. 
```
cc_ipo = []
hatch_ipo = []
for var in vars_all_11yr[:-1]:
        # build array
    cc, p = (xr.DataArray(np.zeros([len(lat), len(lon)]), coords={"latitude":lat,"longitude":lon}, dims=["lat","lon"],name=var.name)), (xr.DataArray(np.zeros([len(lat), len(lon)]), coords={"lat":lat,"lon":lon}, dims=["lat","lon"],name=var.name)) 
    for x in range(len(lat)):
        for y in range(len(lon)):
                # calculate statistical significance and p value with adjusted df
            cc[x,y], p[x,y] = t_p_test(ipo_index_had, var[:,x,y], 11)
    cc_ipo.append(cc)
    
        # create hatching for stippling over statistical significance
    [m,n] = np.where(p < 0.05)
    hatch=np.zeros(cc.shape)
    hatch[m, n] = 1
    hatch_ipo.append(hatch)


cc_enso = []
hatch_enso = []
for var in vars_all_dt[:-1]:
        # build array
    cc, p = (xr.DataArray(np.zeros([len(lat), len(lon)]), coords={"latitude":lat,"longitude":lon}, dims=["lat","lon"],name=var.name)), (xr.DataArray(np.zeros([len(lat), len(lon)]), coords={"lat":lat,"lon":lon}, dims=["lat","lon"],name=var.name)) 
    for x in range(len(lat)):
        for y in range(len(lon)):
                # calculate statistical significance and p value with adjusted df
            nas = np.isnan(var[:,x,y])
            cc[x,y], p[x,y] = pearsonr(n34ind_djf_had_xr[~nas], var[:,x,y][~nas])
    cc_enso.append(cc)
        # create hatching for stippling over statistical significance
    [m,n] = np.where(p < 0.05)
    hatch=np.zeros(cc.shape)
    hatch[m, n] = 1
    hatch_enso.append(hatch)
``` 

The below code evaluates the correlations between Pacific Ocean sea surface temperature modes and the GPCC rain gauge data and statistical significance of those correlations
```
gpcc_r_ipo = {}
gpcc_p_ipo = {}
for i,r in enumerate(recs):
    lati = geo_idx(lat_pts[i],gpcc_lat)
    loni = geo_idx(lon_pts[i],gpcc_lon)
    gpcc_r_ipo[r] = t_p_test(ipo_index_had.sel(time=yrs),vars_all_11yr[3][:,lati,loni],11)[0]
    gpcc_p_ipo[r] = t_p_test(ipo_index_had.sel(time=yrs),vars_all_11yr[3][:,lati,loni],11)[1]
    
gpcc_r_enso = {}
gpcc_p_enso = {}
for i,r in enumerate(recs_enso):
    lati = geo_idx(lat_pts_enso[i],gpcc_lat)
    loni = geo_idx(lon_pts_enso[i],gpcc_lon)
    gpcc_r_enso[r] = pearsonr(n34ind_djf_had_xr.sel(time=yrs),vars_all_dt[3][:,lati,loni])[0]
    gpcc_p_enso[r] = pearsonr(n34ind_djf_had_xr.sel(time=yrs),vars_all_dt[3][:,lati,loni])[1]
```
