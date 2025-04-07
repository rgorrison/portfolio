# Calculating and plotting trends of an index representing local changes in large-scale circulation
|Python (NumPy)| statistics (regression analysis)
|-|-|

![Proxy locations](/assets/HCE_trend.png)

## Figure purpose
This figure documents the trend of a certain atmospheric diagnostic: the large-scale descent (subsidence) of air in the mid-latitudes known as Hadley circulation extent (HCE). This location has remained relatively stationary in the history period, but is projected to move southward in the future. 

This figure is derived from an ensemble of four model simulations shown by the blue lines and the bold, black line shows the average of those individual simulations. The average simulation, being most likely to capture the variability of the real world, stands out in this figure prominently. This leads the viewer to focus on the average HCE tendency while considering the context of the single model simulations of the blue lines. In red dashed lines, the trend lines over two discrete time intervals are captured and the trend in latitude per year is written at the top of the figure. The double stars (**) indicate the trend is significant at p < 0.01.  

## Initiate the plotting and load in data 
```
fig, ax = plt.subplots(1, figsize=(7,4),facecolor='w',dpi=300)
for e in ensmem:    
    enyr = ax.plot(fh_hcext[e].time,fh_hcext[e]['djf_avg'], alpha=0.3,color='b')
yrs = fh_hcext[e]['djf_avg'].time
hc_avg = np.mean([fh_hcext[e]['djf_avg'] for e in ensmem],axis=0)
ax.plot(yrs,hc_avg.data,color='k')
ax.axvline(x = 2009, color = 'r')
```

## Calculate trends and plot trend lines and significance
```
mask = ~np.isnan(yrs[0:90]) & ~np.isnan(hc_avg[0:90])
res_hist = stats.linregress(yrs[0:90][mask],hc_avg[0:90][mask])
ax.plot(yrs[0:90], res_hist.intercept + res_hist.slope*yrs[0:90], linestyle='--', color='r')
ax.text(1921, -29., "Modern HCE trend:\n "+'{0:.{1}f}'.format(res_hist.slope*10, 2) + r'$\degree\ dec.^{-1}$', 
        ha='left', va='center')

mask = ~np.isnan(yrs[90:]) & ~np.isnan(hc_avg[90:])
res = stats.linregress(yrs[90:][mask],hc_avg[90:][mask])
ax.plot(yrs[90:], res.intercept + res.slope*yrs[90:], linestyle='--', color='r')
ax.text(2010, -29.0, "Future HCE trend:\n "+'{0:.{1}f}'.format(res.slope*10, 2) + r'$\degree\ dec.^{-1}$ **',    
        ha='left', va='center')
```

## Finalize plotting structure
```
ax.set_ylabel('Hadley circulation extent\n (latitude)', fontsize = 14)
ax.set_xlabel('Years (CE)', fontsize = 14)

# Format latitudes
lat_fmt = LatitudeFormatter(number_format='.0f')
ax.yaxis.set_major_formatter(lat_fmt)
ax.yaxis.set_major_locator(ticker.MultipleLocator(5))

ax.xaxis.set_tick_params(labelsize=12)
ax.yaxis.set_tick_params(labelsize=12)
ax.text(-0.15, 1.1, 'd)', transform=ax.transAxes, fontsize=20, va='top', ha='right')

plt.xlim(1920,2100)

print('\n p (1920 -- 2000) = ' +'{0:.{1}f}'.format(res_hist.pvalue, 3))
print('\n p (2000 -- 2098) < 0.001') # pval = 4.823216692486791e-06
plt.tight_layout()
plt.savefig('../Fig3d_HC_ext_1920_2098_flip.png',dpi=300,facecolor='w')
```
