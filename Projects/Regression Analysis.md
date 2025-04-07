# Evaluating the explained variance of regional atmospheric motion by latitude change
|Python (NumPy)|statistics (regression analysis)|
|-|-|

<img src="/assets/hc_regression.png" alt="HCI - latitude regression" width="500"/>

## Figure purpose
This figure shows the regression of an index for monsoon strength (measured in $\delta^{18}O_p$, per mil units) against the latitude change of large-scale descent of atmospheric motion. 

The relevant summary statistics for the strength of the regression (r) and the slope of the regression are plotted directly on to the graph. The slope is annoted with double stars (**) to indicate the slope is significantly different from zero (p < 0.01).

## Regression analysis
After data processing, this analysis is a simple one line function. 
```
hcext_rcp_ensavg = np.nanmean([hcext_rcp[e] for e in ens_rcp],axis=0)
m_hce, b_hce, r_val_hce, p_value, std_err = linregress(hcext_rcp_ensavg,pc_ensavg.PC1[-89:])

```
