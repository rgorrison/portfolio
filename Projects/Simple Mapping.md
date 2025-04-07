# Mapping a database of paleoclimate records for preliminary analysis
|Python | data management|
|-|-|

Paleoclimate Record Map            |  Close-up of South Atlantic Paleoclimate Records
:-------------------------:|:-------------------------:
![Proxy locations](/assets/RecAvail_SAm_15ka3ka.png)   |  ![Proxy locations](/assets/RecAvail_SAtl_15ka3ka.png)

## Figure purpose

The goal of these figures was to illustrate the geographic dispersion of paleoclimate records containing data during the Holocene (approximately 15,000 years before present until today) to outline an approach to collaborative study. This preliminary figure was not intended for publication, but rather to ground and propel forward a strategic discussion. 

These simple maps shows a diverse network of sites from which paleoclimate records have been generated. This figure uses two layers: first, distinct symbols illustrate the source from which information is derived (bogs, speleothems (caves), corals, ice cores, lake cores, salt flats, and ocean cores); and second, the time period available for each record is parsed using distinct colors (black for long records and green for shorter records).

The close-up figure on the right hand side helps to parse the region of the South Atlantic where there is a high density of records available.  

## Database hard coding
Each record is hardcoded based on type and within each type based on label, latitude, and longitude. Below is the code for the Record Map.
```
# Continuous/complete records by type
recs_bg = ['1']
lat_pts_bg = [-52.13,]
lon_pts_bg = [-71.88,]

recs_sp = ['2']
lat_pts_sp = [-27.22,]
lon_pts_sp = [-49.16,]

recs_cor = ['3,4']
lat_pts_cor = [13.083333, ]
lon_pts_cor = [-59.533333, ]

recs_ic = ['5','6','7']
lat_pts_ic = [-9,-18,-16.616667]
lon_pts_ic = [-77.5,-69,-67.766667]

recs_lk = ['8','9','10, 11','12','13','14','15','16a,b']
lat_pts_lk = [-0.87,3.87,-17.77,-2.76667,-51.95,-16.13,-15.93,-9.8]
lon_pts_lk = [-89.45,-74,-57.72,-79.23333,-70.3833,-69.15,-69.25,-77.3]

recs_oc = ['17','18b','18c','18d','18e','19','20','21','22','23a', '23b',
           '23c', '24', '245','25b','26', '27', '28a','28b','29a','29b','29c',
           '29d', '30a','30b','30c','30d','30e','30f','30g','30h','30i','30j',
           '31', '32', '33', '34', '35', '36', '37', '38', '39', '40','41a',
           '41b', '41c','42', '43', '44']
lat_pts_oc = [2.251389, -0.47, -2.37, -3.38, -1.22, 5.75,12.09, -27.516667,
              -27.89, 7.58, 7.93, 8.23, 4.20415, 7.85583, 7.85583, 11.33,
              -27.52, -27.57, -27.4833, -27.7, -28.1333, -27.5667, -27.4833,
              -26.68, -27.7, -28.64, -28.13, -27.57, -27.27, -27.76, -28.36,
              -27.48, -27.35, -27.7667, -1.252167, -27.51, 10.7, 10.7, 10.74667,
              10.712167, 10.712167, 10.712167, 11.56667, -41.000167, -36.219167,
              -36.159833, -32.75, 11.491, 5.9]
lon_pts_oc = [-90.950278,-82.07, -84.65, -83.52, -89.68, -49.1, -61.24, -46.466667,
              -46.04, -53.91, -53.68, -53.23, -43.4889, -83.60833, -83.60694,
              -66.63, -46.47, -46.18, -46.3333, -46.4833, -46.0667, -46.1833, 
              -46.3333, -46.5, -46.49, -45.54, -46.07, -46.18, -46.47, -46.63,
              -45.84, -46.33, -46.63, -46.0333, -89.6845, -46.47, -65.95, -65.95,
              -65.18833, -65.169667, -65.169667, -65.169667, -78.41667, -74.44983, 
              -73.68167, -73.56633, -72.033333, -79.378, -44.2]

recs_sf = ['45a,b']
lat_pts_sf = [-20.25,]
lon_pts_sf = [-67.5,]

# Partial records by type
recs_sp_part = ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12', 
                '13', '14', '15',]
lat_pts_sp_part = [-4.0667, -21.083, -5.592222, -14.4227, -16.15, -13.216667, 
                   5.7, -5.833333, -24.53, -5.94, -12.633611, -11.27, -52.68, 
                   -16, 10.6,]
lon_pts_sp_part = [-55.45, -56.583, -37.684444, -44.3656, -44.6, -41.05, -77.9, 
                   -77.400000, -48.73, -77.31, -41.026389, -75.79, -73.38, -47, 
                   -84.8]

recs_lk_part = ['6',]
lat_pts_lk_part = [-50.0495,]
lon_pts_lk_part = [-25.2404]
```

## Plotting
Each list of different record types is plotting with a loop. Again, this is the plotting for the Record Map. 
```
# PLOTTING
# pplot.rc['ytick.labelsize'] = 'large'

fig, axs = pplot.subplots(proj='pcarree', figsize=(8,10))
#fig.suptitle('Proxy Network', fontsize=24)

axs.set_ylabel('Latitude', fontsize='medium')
axs.set_xlabel('Longitude', fontsize='medium')
axs.format(land=False, labels=False, lonlines=10, latlines=10, 
           gridminor=True, 
)

axs.set_aspect('auto',adjustable=None)  
axs.coastlines()
axs.add_feature(cf.BORDERS)  
axs.set_extent([330,265,15,-60], crs=ccrs.PlateCarree())
axs.set_xticks([-80,-70,-60,-50,-40,-30], crs=ccrs.PlateCarree())   
axs.set_yticks([-60,-50,-40,-30,-20,-10,0,10,15], crs=ccrs.PlateCarree())
lon_fmt = LongitudeFormatter(number_format='.0f')
lat_fmt = LatitudeFormatter(number_format='.0f')
axs.xaxis.set_major_formatter(lon_fmt)
axs.yaxis.set_major_formatter(lat_fmt)
axs.set_ylabel(r'Latitude', fontsize='medium')
axs.set_xlabel(r'Longitude', fontsize='medium')

# PARTIAL
for r,lat_val,lon_val in zip(recs_sp_part,lat_pts_sp_part, lon_pts_sp_part):
    prx_sp = plt.plot(lon_val,lat_val, marker='d',linestyle = 'None', color='g', mec='w', markersize=10, labels = '- speleothem', transform=ccrs.PlateCarree())
    txt = plt.text(lon_val + 0.5, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    txt.set_path_effects([PathEffects.withStroke(linewidth=1, foreground='w')])
    
for r,lat_val,lon_val in zip(recs_lk_part,lat_pts_lk_part, lon_pts_lk_part):
    prx_lk = plt.plot(lon_val,lat_val, marker='o',linestyle = 'None', color='g', mec='w', markersize=10, labels = '- lake core', transform=ccrs.PlateCarree())
    txt = plt.text(lon_val + 0.5, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    txt.set_path_effects([PathEffects.withStroke(linewidth=1, foreground='w')])
    
#COMPLETE
for r,lat_val,lon_val in zip(recs_bg,lat_pts_bg, lon_pts_bg):
    prx_bg = plt.plot(lon_val,lat_val, marker='P',linestyle = 'None', color='k', mec='w', markersize=10, labels = '- bog', transform=ccrs.PlateCarree())
    txt = plt.text(lon_val + 0.5, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    txt.set_path_effects([PathEffects.withStroke(linewidth=1, foreground='w')])
    
for r,lat_val,lon_val in zip(recs_sp,lat_pts_sp, lon_pts_sp):
    prx_sp = plt.plot(lon_val,lat_val, marker='d',linestyle = 'None', color='k', mec='w', markersize=10, labels = '- speleothem', transform=ccrs.PlateCarree())
    txt = plt.text(lon_val + 0.5, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    txt.set_path_effects([PathEffects.withStroke(linewidth=1, foreground='w')])
    
for r,lat_val,lon_val in zip(recs_cor,lat_pts_cor, lon_pts_cor):
    prx_cor = plt.plot(lon_val,lat_val, marker='^',linestyle = 'None', color='k', mec='w', markersize=10, labels = '- coral', transform=ccrs.PlateCarree())
    txt = plt.text(lon_val + 0.5, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    txt.set_path_effects([PathEffects.withStroke(linewidth=1, foreground='w')])
    
for r,lat_val,lon_val in zip(recs_ic,lat_pts_ic, lon_pts_ic):
    prx_ic = plt.plot(lon_val,lat_val, marker='*',linestyle = 'None', color='k', mec='w', markersize=10, labels = '- ice core', transform=ccrs.PlateCarree())
    txt = plt.text(lon_val + 0.5, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    txt.set_path_effects([PathEffects.withStroke(linewidth=1, foreground='w')])
        
for r,lat_val,lon_val in zip(recs_lk,lat_pts_lk, lon_pts_lk):
    prx_lk = plt.plot(lon_val,lat_val, marker='o',linestyle = 'None', color='k', mec='w', markersize=10, labels = '- lake core', transform=ccrs.PlateCarree())
    txt = plt.text(lon_val + 0.5, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    txt.set_path_effects([PathEffects.withStroke(linewidth=1, foreground='w')])
    
for r,lat_val,lon_val in zip(recs_oc,lat_pts_oc, lon_pts_oc):
    prx_oc = plt.plot(lon_val,lat_val, marker='v',linestyle = 'None', color='k', mec='w', markersize=10, labels = '- ocean core', transform=ccrs.PlateCarree())
    txt = plt.text(lon_val + 0.5, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    txt.set_path_effects([PathEffects.withStroke(linewidth=1, foreground='w')])
        
for r,lat_val,lon_val in zip(recs_sf, lat_pts_sf, lon_pts_sf):
    prx_sf = plt.plot(lon_val,lat_val, marker='s',linestyle = 'None', color='k', mec='w', markersize=10, labels = '- salt flat', transform=ccrs.PlateCarree())
    txt = plt.text(lon_val + 0.5, lat_val + 0.5, r, size=10, horizontalalignment='left', transform=ccrs.PlateCarree())
    txt.set_path_effects([PathEffects.withStroke(linewidth=1, foreground='w')])

axs.set_title('Available records for Holocene climate, ~15k - 0 cal yr BP\nblack: continuous record; green; partial record')

prx_all = prx_bg + prx_sp + prx_cor + prx_ic + prx_lk + prx_sf + prx_oc
lbls = [l.get_label() for l in prx_all]
axs.legend(prx_all, lbls, loc='ll', ncol=1, color='k',fancybox=True, fontsize='medium')
axs.set_ylabel('Latitude')
axs.set_xlabel('Longitude')
# axs.legend.set_facecolor('white')

fig.savefig('../RecAvail_SAm_15ka3ka.png', facecolor='w')
plt.show()
```

