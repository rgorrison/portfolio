# Evaluting the time-evolution of dynamic relationships
| Python (Pandas) | Statistics (time series analysis, correlation analysis)|
|-|-|

<img src="/assets/running_correlation.png" alt="Running correlations and AMO" width="800"/>


## Graph purpose

This graph contains three layers of information meant to identify which variables are most closely correlated to stable oxygen isotope variability ($\delta^{18}O_{p}$) and possible contributions to those relationships. Confidence levels are indicated for the 95%-significance level (dashed lines) and 99%-significance level (dotted lines).The layers can be discretized as follows:

  1) Running correlations for smoothed indices related to or impacting monsoon activity (where $\omega_{500}$ represents vertical motion in the atmosphere; $\delta^{18}O_{p}$ represnts stable oxygen isotopes in precipitation; N3.4 is an index of El Niño variability; and P is precipitation). 
  2) The index of Atlantic Multidecadal Variability (AMO) is plotted to illustrate that during the positive phase of the AMO, the N3.4 correlation with $\delta^{18}O_{p}$ dips below significant levels, indicating a decoupling of that relationship.
  3) Red and blue shading highlight positive and negative phases of the Interdecadal Pacific Oscillation, respectively.

Below are selections of Python code for key analysis and plotting aspects of this project. 

## Correlation analysis
I converted processed data files to Pandas dataframe series to ease data processing:
```
w500_neb_df = pd.Series(w500_neb_a)
d18_wam_df = pd.Series(d18_wam_a)
p_wam_df = pd.Series(p_wam_a)
n34_df = pd.Series(n34ind_djf_had_xr)
```

I mirrored the end by 62 years in order to calculate running averages in 31 year climatology windows:
```
# mirror ends to calculate running averages: 

#------------------- w500_neb -------------------
var = w500_neb_df
nt = len(icam_yrs_ovr_enso)
filter_62yr = 62
mirror = int((filter_62yr-1)/2) # end point
copy = np.zeros((nt+2*mirror))
copy[mirror:-mirror] = var.copy()
copy[0:mirror] = var[1:mirror+1].copy()[::-1]
copy[-mirror:] = var[-1-mirror:-1].copy()[::-1]
w500_neb_mirend = pd.Series(copy)

#------------------- d18_wam -------------------
var = d18_wam_df
nt = len(icam_yrs_ovr_enso)
filter_62yr = 62
mirror = int((filter_62yr-1)/2) # end point
copy = np.zeros((nt+2*mirror))
copy[mirror:-mirror] = var.copy()
copy[0:mirror] = var[1:mirror+1].copy()[::-1]
copy[-mirror:] = var[-1-mirror:-1].copy()[::-1]
d18_wam_mirend = pd.Series(copy)

#------------------- p_wam -------------------
var = p_wam_df
nt = len(icam_yrs_ovr_enso)
filter_62yr = 62
mirror = int((filter_62yr-1)/2) # end point
copy = np.zeros((nt+2*mirror))
copy[mirror:-mirror] = var.copy()
copy[0:mirror] = var[1:mirror+1].copy()[::-1]
copy[-mirror:] = var[-1-mirror:-1].copy()[::-1]
p_wam_mirend = pd.Series(copy)

#------------------- n34 -------------------
var = n34_df
nt = len(icam_yrs_ovr_enso)
filter_62yr = 62
mirror = int((filter_62yr-1)/2) # end point
copy = np.zeros((nt+2*mirror))
copy[mirror:-mirror] = var.copy()
copy[0:mirror] = var[1:mirror+1].copy()[::-1]
copy[-mirror:] = var[-1-mirror:-1].copy()[::-1]
n34_mirend = pd.Series(copy)
```

I calculated Pearson rolling correlations using the Pandas .corr function: 
```
# Corr(d18o, w500)
d18_w500_corr = d18_wam_mirend.rolling(31).corr(w500_neb_mirend)

# Corr(d18o, P)
d18_p_corr = d18_wam_mirend.rolling(31).corr(p_wam_mirend)

# Corr(n34, d18o)
n34_d18_corr = n34_mirend.rolling(31).corr(d18_wam_mirend)
```

## Plotting
I utilize section headings and line breaks to increase legibility in complex plots: 
```
# 31 yr running correlation of upstream, amount effects and the enso, amo link. 
fig, ax1 = plt.subplots(figsize=(8,5))

# Pos IPO phases
plt.axvspan(1897.5, 1905.5, alpha=0.3, color='#D62728',zorder=2)  
plt.axvspan(1906.5, 1907.5, alpha=0.3, color='#D62728',zorder=2)
plt.axvspan(1921.5, 1937.5, alpha=0.3, color='#D62728',zorder=2)
plt.axvspan(1980.5, 1999.5, alpha=0.3, color='#D62728',zorder=2)

# Neg IPO phases
plt.axvspan(1881.5, 1882.5, alpha=0.3, color='#1F77B4',zorder=2)
plt.axvspan(1887.5, 1895.5, alpha=0.3, color='#1F77B4',zorder=2)
plt.axvspan(1911.5, 1913.5, alpha=0.3, color='#1F77B4',zorder=2)
plt.axvspan(1944.5, 1960.5, alpha=0.3, color='#1F77B4',zorder=2)
plt.axvspan(1964.5, 1976.5, alpha=0.3, color='#1F77B4',zorder=2)

ax2 = ax1.twinx()
    
    # Plot correlation lines, AMO index
    # w500, d18o - upstream convection
ax1.plot(icam_yrs_ovr_enso,d18_w500_corr[30:-30],linewidth=2., color='#FF7F0E', zorder=10)
    # P, d18o - amount effect
ax1.plot(icam_yrs_ovr_enso,d18_p_corr[30:-30],linewidth=2., color='#BCBD22', zorder=10)
    # n34, d18o - pacific link
ax1.plot(icam_yrs_ovr_enso,n34_d18_corr[30:-30],linewidth=2., color='#E377C2', zorder=10)
    # AMO index
ax2.plot(icam_yrs_ovr_enso[14:], amo_ann_had, linewidth=2., color='#000000',zorder=10)

    # axis formatting
ax1.set_xlabel('Years (CE)')
ax1.set_ylim(-2,1.2)
ax1.set_yticks([-0.6,-0.4,-0.2, 0,0.2,0.4,0.,0.6,0.8,1.0])
ax1.set_ylabel('Correlation Coefficient')
ax1.yaxis.set_label_coords(-0.1,0.72)
ax2.set_ylim(-0.35,1.)
ax2.set_yticks([-0.25,-0.125, 0,0.125,0.25])
ax2.set_ylabel('AMO index')
ax2.yaxis.set_label_coords(1.15,0.25)
ax1.grid(False)
ax2.grid(False)

#  Label lines
plt.text(0.415, 0.94, r'Upstream convective control; $\it{corr(\omega500, \delta^{18}O_p)}$',color='#FF7F0E',backgroundcolor='w',size=10,zorder=2,transform=ax1.transAxes)
plt.text(0.78, 0.678, r'$\it{corr(N 3.4,\; \delta^{18}O_p)}$',color='#E377C2',backgroundcolor='w',size=10,zorder=2,transform=ax1.transAxes)
plt.text(0.56, 0.42, r'Local amount control; $\it{corr(P, \delta^{18}O_p)}$',color='#BCBD22',backgroundcolor='w',size=10,zorder=2,transform=ax1.transAxes)
plt.text(0.52, 0.05, r'AMO index; $\it{Trenberth\ and\ Shea\ (2006)}$',color='black',backgroundcolor='w',size=10,zorder=2,transform=ax1.transAxes)

# set significance lines
ax1.axhline(y=0.4556,linewidth=1.8, color='black',linestyle='dotted')   # p > 0.001
ax1.axhline(y=0.355,linewidth=1.8, color='black',linestyle='dashed')   # p > 0.05
ax1.axhline(y=-0.4556,linewidth=1.8, color='black',linestyle='dotted')   # p > 0.001
ax1.axhline(y=-0.355,linewidth=1.8, color='black',linestyle='dashed')   # p > 0.05

plt.xlim(1880,2000)
plt.tight_layout()
plt.savefig('/network/rit/home/ro553136/orrison/Plots/Pac_SAm/Fig5_run_corr_amo.jpg',format='JPEG',dpi=300)
plt.show()
```
