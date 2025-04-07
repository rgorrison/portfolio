# Generating multi-layered maps to crystalize a complicated spatial story

| Python (Xarray) | data cleaning | 
|-|-|

Here I will illustrate two examples of multi-layered spatial storytelling. These images provide context for scientific investigations that seek to understand how networks of paleoclimate records document variability of key variables within an averaged background climate state including rainfall, wind patterns, and key dynamic features of the landscape. Presenting these multi-factored entities together well as a comprehensive visual is critical to setting the stage for further analysis.  

These figures concisely illustrate multiple layers of information, doing so using *color-blind accesible* color schemes and careful key selection. 

# 1: Dual purpose icons
![Proxy locations](/assets/proxy_locs.png)

## Figure purpose
This map shows the locations of two databases of paleoclimate records: one dataset used to assess variability of a multidecadal index of Pacific Ocean sea surface temperatures (the Interdecadal Pacific Oscillation, or IPO) and the other for analysis of an interannual index of Pacific Ocean sea surface temperatures (the El Niño Southern Oscillation, or ENSO). The databases contain records from diverse sources (speleothems (caves), ice cores, lakes, tree-cores, and observational stations from the IAEA network) which are highlighted on the map. Also indicated on this map is the background climate state to serve as a reference for extensive analysis of the information contained within each database describing rainfall, wind patterns, and the location of the South Atlantic Convergence Zone (SACZ). 

## Database cleaning
The IAEA stations (also called GNIP stations) used in this analysis had to meet certain criteria: have more than seven discrete years of observations, have at least three years of data in each phase (positive and negative) of the IPO, and the data outliers that were non-realistic data points needed to be removed. The below code illustrates how the raw data was cleaned, organized, and ultimately filtered for inclusion in the above figure. 
```
pth_gnip = '../gnip/'
f_gnip = 'Wiser_SAm_djf_pwt.nc'
wiser_djf_all = xr.open_dataset(pth_gnip + f_gnip)
wiser_djf_yrsrt = wiser_djf_all.sortby(wiser_djf_all.time)

# gnip data: [:-253] # 1980 -- 1999
wiser_djf = wiser_djf_yrsrt.d18o_pwt[:-253] 
wiser_djf_lat = wiser_djf_yrsrt.latitude[:-253] 
wiser_djf_lon = wiser_djf_yrsrt.longitude[:-253] 
# wiser_djf_alt = wiser_djf_yrsrt.altitude[:-253] 

    # process and average it all out for plotting: 
wiser_pd = wiser_djf.to_dataframe()
wiser_pd['lat'] = wiser_djf_lat
wiser_pd['lon'] = wiser_djf_lon
# wiser_pd['alt'] = wiser_djf_alt
iaea_clim = wiser_pd.groupby(['lat', 'lon']).mean()

    #climatology
iaea_d18o = iaea_clim.d18o_pwt.values
iaea_lat = [iaea_clim.index[i][0] for i in range(len(iaea_clim))]
iaea_lon = [iaea_clim.index[i][1] for i in range(len(iaea_clim))]
# iaea_alt = [iaea_clim.index[i][2] for i in range(len(iaea_clim))]

grouped_d18 = wiser_pd.groupby(['lat', 'lon'])

# -------------------------- SELECT GNIP STATIONS
#   # group gnip data to select sites with more than 7 years of (discontinuous) data
grouped_long7 = []
grouped_d18 = wiser_pd.groupby(['lat', 'lon'])
for key, item in grouped_d18:
    if (len(grouped_d18.get_group(key))) > 7:
         grouped_long7.append(grouped_d18.get_group(key))
            
    # select stations with at least three years of data in each phase 
inds_pwpc = [8,9,10,12,13,14,15,17]
grouped_pwpc = [grouped_long7[i] for i in inds_pwpc]

    # remove outlies above/below +/- 2 std
grouped_pwpc_2 = []
for i in range(len(grouped_pwpc)):
    plus_2std = np.mean(grouped_pwpc[i].d18o_pwt)+(np.std(grouped_pwpc[i].d18o_pwt)*2)
    minus_2std = np.mean(grouped_pwpc[i].d18o_pwt)-(np.std(grouped_pwpc[i].d18o_pwt)*2)
    tmp = grouped_pwpc[i].drop(grouped_pwpc[i][grouped_pwpc[i]['d18o_pwt'] < minus_2std].index)
    grouped_pwpc_2.append(tmp.drop(tmp[tmp['d18o_pwt'] > plus_2std].index))
```

## Selected plotting code
The icon construction makes use of keyword arguments within the plotting script, with the 'fillsyle' argument determining if the 'markerfacecoloralt' argument is utilized for the paleoclimate record icon plotting. 
```
# kwargs for plotting:
filled_marker_style_IE = dict(linestyle = 'None',
                              markeredgecolor='grey', 
                              markersize=14,
                              markeredgewidth = 1.5,
                              markerfacecolor='darkmagenta', # IPO only locations
                              markerfacecoloralt='darkorange'  # IAEA, IPO, and ENSO locations
                              )

# -------------------------- Plotting proxy records
for i,(r,lat_val,lon_val) in enumerate(zip(recs_ipo,lat_pts_ipo, lon_pts_ipo)):
    if i == 0: # 0'P00-H1'
        prx_s_f = ax_sam.plot(lon_val,lat_val-0.5, marker='X', labels = 'speleothem', fillstyle='full', **filled_marker_style_IE, transform=ccrs.PlateCarree())
        txt = ax_sam.text(lon_val + 1, lat_val-1.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    elif i == 1: # 1'CR1'
        prx_s = ax_sam.plot(lon_val + 1,lat_val, marker='X', labels = 'speleothem', fillstyle='full', **filled_marker_style_IE, transform=ccrs.PlateCarree())
        txt = ax_sam.text(lon_val + 2, lat_val - 1, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    elif i == 4: # 4'DV2+TR5\n+LD12'
        prx_s = ax_sam.plot(lon_val,lat_val, marker='X', labels = 'speleothem', fillstyle='full', **filled_marker_style_IE, transform=ccrs.PlateCarree())
        txt = ax_sam.text(lon_val + 1, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    elif i > 1 and i <= 5: # 2'JAR4', 3'SBE3', 5'PIM4'
        prx_s = ax_sam.plot(lon_val,lat_val, marker='X', labels = 'speleothem', fillstyle='full', **filled_marker_style_IE, transform=ccrs.PlateCarree())
        txt = ax_sam.text(lon_val + 1, lat_val - 1, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    elif i == 6: #6'MFZ' 
        prx_s = ax_sam.plot(lon_val,lat_val, marker='X', labels = 'speleothem', fillstyle='left', **filled_marker_style_IE, transform=ccrs.PlateCarree())
        txt = ax_sam.text(lon_val - 6, lat_val - 2, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    elif i == 7: #7'Quelccaya' 
        prx_i = ax_sam.plot(lon_val,lat_val, marker='d', labels = 'ice core', fillstyle='left', **filled_marker_style_IE, transform=ccrs.PlateCarree())
        txt = ax_sam.text(lon_val + 1, lat_val - 1, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    elif i == 8: #8 'Pumacocha'
        prx_l_f = ax_sam.plot(lon_val,lat_val, marker='o', labels = 'lake core', fillstyle='full', **filled_marker_style_IE, transform=ccrs.PlateCarree())
        txt = ax_sam.text(lon_val + 1, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    elif i == 10: # 10'Cuy'
        prx_t = ax_sam.plot(lon_val,lat_val-1, marker='^', labels = 'tree cellulose', fillstyle='left', **filled_marker_style_IE, transform=ccrs.PlateCarree())
        txt = ax_sam.text(lon_val + 1, lat_val - 2, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    else: # 9'UTU', 11'Bol'
        prx_t = ax_sam.plot(lon_val,lat_val, marker='^', labels = 'tree cellulose', fillstyle='left', **filled_marker_style_IE, transform=ccrs.PlateCarree())
        txt = ax_sam.text(lon_val + 1, lat_val - 1, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
```

# 2: Adding close-ups for information-dense regions

![Proxy locations with close up](/assets/proxy_locs_close.jpg)

## Figure purpose
This map shows the locations of multiple types of paleoclimate records (speleothems (cave records) and pollen), zooming in to rescale a section of the map that has a tight clustering of records. Also shown in this map is the background climate state, including rainfall and wind patterns.

## Plotting close-up
To create a close-up of part of the main plot, a box is drawn around the region to correspond to data plotted on a secondary plotting axis for the mini plot. The data within the region is plotted there for a second time. 
```
# -------------------------- Make inset box, add data
     # draw a box around the region on large map
sesa = ax.add_patch(patches.Rectangle(xy=[309.75, -17.8], width=7.5, height=8.25,
                            facecolor='none', linestyle = '-', edgecolor='k', lw=1.5,
                            transform=ccrs.PlateCarree()))
# size = .2 # Figure Standardized coordinates (0~1)
#     # establish inset axis location
iax = plt.axes([0.115, 0.237, 0.3, 0.3], projection=ccrs.PlateCarree())
i_ext=[129.75, 137.25, -9.75, -18]
iax.set_extent(i_ext, crs=ccrs.PlateCarree())
iax.contourf(chirps_lon+180, chirps_lat,precip_climo,levels=levels, cmap = new_cmap,
                   transform=ccrs.PlateCarree())
# skipi = 8
# Q_i = iax.quiver(uv_lon[::skipi]+180, uv_lat[::skipi], u.data[::skipi, ::skipi], v.data[::skipi, ::skipi], 
#            minlength = .7, width = 0.004, transform=ccrs.PlateCarree(), alpha = 0.7)

for i,(r,lat_val,lon_val) in enumerate(zip(caves[0:4],c_lat[0:4], c_lon[0:4])):
    if i == 0:
        prx_c = iax.plot(lon_val+180,lat_val, marker='^', label = 'speleothem', fillstyle='full', markerfacecolor = 'black', linestyle = 'None',
                              markeredgecolor='red', markersize=14, markeredgewidth = 1.5, transform=ccrs.PlateCarree(), zorder=10)
        txt = iax.text(lon_val+180 - 0.5, lat_val - 0.3, r, size=10, horizontalalignment='right', 
            bbox=dict(facecolor='none', edgecolor='red', pad=3.0), transform=ccrs.PlateCarree()) # add red around proxy for this study
    elif i == 1:
        prx_c = iax.plot(lon_val+180,lat_val, marker='^', label = 'speleothem', fillstyle='full', markerfacecolor = 'black', **filled_marker_style, transform=ccrs.PlateCarree())
        txt = iax.text(lon_val+180 - 0.5, lat_val - 0.25, r, size=10, horizontalalignment='right', transform=ccrs.PlateCarree())
    elif i == 2:
        prx_c = iax.plot(lon_val+180,lat_val, marker='^', label = 'speleothem', fillstyle='full', markerfacecolor = 'black', **filled_marker_style, transform=ccrs.PlateCarree())
        txt = iax.text(lon_val+180, lat_val - 0.5, r, size=10, horizontalalignment='center', transform=ccrs.PlateCarree())
    else:
        prx_c = iax.plot(lon_val+180,lat_val, marker='^', label = 'speleothem', fillstyle='full', markerfacecolor = 'black', **filled_marker_style, transform=ccrs.PlateCarree())
        txt = iax.text(lon_val+180, lat_val - 0.5, r, size=10, horizontalalignment='center', transform=ccrs.PlateCarree())
for i,(r,lat_val,lon_val) in enumerate(zip(pollen,p_lat, p_lon)):
    if i == 0:
        prx_p = iax.plot(lon_val+180,lat_val, marker='o', label = 'pollen', fillstyle='full', markerfacecolor = 'yellow', **filled_marker_style, transform=ccrs.PlateCarree())
        txt = iax.text(lon_val+180 - 2.0, lat_val + 0.3, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    elif i == 1:
        prx_p = iax.plot(lon_val+180,lat_val, marker='o', label = 'pollen', fillstyle='full', markerfacecolor = 'yellow', **filled_marker_style, transform=ccrs.PlateCarree())
        txt = iax.text(lon_val+180, lat_val - 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    elif i == 2:
        prx_p = iax.plot(lon_val+180,lat_val, marker='o', label = 'pollen', fillstyle='full', markerfacecolor = 'yellow', **filled_marker_style, transform=ccrs.PlateCarree())
        txt = iax.text(lon_val+180, lat_val - 0.5, r, size=10, horizontalalignment='center', transform=ccrs.PlateCarree())
    elif i == 3:
        prx_p = iax.plot(lon_val+180,lat_val, marker='o', label = 'pollen', fillstyle='full', markerfacecolor = 'yellow', **filled_marker_style, transform=ccrs.PlateCarree())
        txt = iax.text(lon_val+180 + 1.2, lat_val - 0.5, r, size=10, horizontalalignment='center', transform=ccrs.PlateCarree())
    else:
        prx_p = iax.plot(lon_val+180,lat_val, marker='o', label = 'pollen', fillstyle='full', markerfacecolor = 'yellow', **filled_marker_style, transform=ccrs.PlateCarree())
        txt = iax.text(lon_val+180, lat_val - 0.5, r, size=10, horizontalalignment='center', transform=ccrs.PlateCarree())

```
