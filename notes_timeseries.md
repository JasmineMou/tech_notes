# Holt-Winters model
https://grisha.org/blog/2016/01/29/triple-exponential-smoothing-forecasting/


## basics
- a general review
https://www.datascience.com/blog/time-series-forecasting-machine-learning-differences

### time series' 4 components
https://en.wikipedia.org/wiki/Seasonal_adjustment#timeseriescomponents

+ 4 components
- trend T_t: a pattern exists involving a long-term increase or decrease in data.
- seasonal S_t: a periodic pattern exists due to the calendar (ex. the quarter, month, or day of the week). dft from cyclic pattern, the seasonal pattern is always of same length. 
- cyclic C_t: a pattern exists where the data exhibits rises and falls that are not of fixed period (duration usually of at least 2 years).
	seasonal vs cyclic
	-- length: seasonal - fixed, known; cyclic - variable, unknown, usually longer than seasonality.
	-- average length of cycle is longer than the length of seasonal pattern.
	-- magnitude of cycle is more variable than magnitude of seasonal pattern. 
	-- the timing of peaks and throughs is predictable w/ seasonal data, but unpredictable in the long term w/ cyclic data. 
	season is repeating cycle.
- error E_t

+ relationship btw the decomposition of the 4 components
- additive decomposition:
-- formula: Y_t = S_t + T_t + C_t + E_t, where Y_t is the data at time t.
-- when to use additive decomposition: if the magnitude of seasonal fluctuation does not vary with level.

- multiplicative decomposition:
-- formula: Y_t = S_t * T_t * C_t * E_t. 
-- when to use multiplicative decomposition: if seasonal fluctuations are proportional to level of series; more prevalent with economic series.

- logs turn multiplicative decomposition into additive decomposition
log(Y_t) = log(S_t * T_t * C_t * E_t) = log(S_t) + log(T_t) + log(C_t) + log(E_t)


### seasonality
https://machinelearningmastery.com/time-series-seasonality-with-python/

### stationarity & trend
A time series where the seasonal component has been removed is called seasonal stationary. 
A time series with a clear seasonal component is referred to as non-stationary.

decomposition to handle stationary & trend, ACF & PACF plots: 
https://www.analyticsvidhya.com/blog/2016/02/time-series-forecasting-codes-python/


## (S)ARIMA model

- ARIMA

-- concept
ARIMA: AutoRegressive Integrated Moving Average
	AR: autoregression. the use of the dependent relationship btw an observation & some # of lagged observations.
	I: integreted. the use of differencing of raw observations (subtracting an obs from an obs at the previous time step) in order to make time series stationary (ex. to remove trend and seasonal structures that negatively affect the regression model).
	MA: moving average. the use of the dependency btw an obs and a residual error from a moving avg model applied to lagged obs.
ARIMA's parameters
	p: the lag order, the # of lag observations included in the model. 
	d: the degree of differencing, the # of times that the raw observations are differenced.
	q: the order of moving average, the size of the moving average window.

-- ARIMA can handle data with trend, but doesn't support time series with a seasonal component. 
	It expects data either not seasonal or has the seasonal component removed (ex. seasonally adjusted via methods such as seasonal differencing).

-- configuration
https://machinelearningmastery.com/arima-for-time-series-forecasting-with-python/
	Configuring an ARIMA Model
	The classical approach for fitting an ARIMA model is to follow the Box-Jenkins Methodology.
	This is a process that uses time series analysis and diagnostics to discover good parameters for the ARIMA model.
	In summary, the steps of this process are as follows:
	+ Model Identification. Use plots and summary statistics to identify *trends, seasonality, and autoregression elements* to get an idea of the amount of differencing and the size of the lag that will be required.
	+ Parameter Estimation. Use a fitting procedure to find the coefficients of the regression model.
	+ Model Checking. Use plots and statistical tests of the residual errors to determine the amount and type of temporal structure not captured by the model.
	The process is repeated until either a desirable level of fit is achieved on the in-sample or out-of-sample observations (e.g. training or test datasets).


- SARIMA

-- concept
SARIMA: Seasonal AutoRegressive Integrated Moving Average
	It adds 3 hyperparameters to specify the AR, I, MA for the seasonal component of the series, and an additional parameter for the period of the seasonality.
SARIMA's parameters
	Trend elements:
		p: trend autoregressive order.
		d: trend difference order.
		q: trend moving average order.
	Seasonal elements:
		P: seasonal autoregressive order.
		D: seasonal difference order.
		Q: seasonal moving average order.
		m: the # of time steps for a single seasonal period.

-- configuration
https://machinelearningmastery.com/sarima-for-time-series-forecasting-in-python/
	trend elements can be chosen through analysis of ACF and PACF plots to check correlations of recent time steps.
https://www.kaggle.com/poiupoiu/how-to-use-sarimax

- grid search SARIMA model
https://www.digitalocean.com/community/tutorials/a-guide-to-time-series-forecasting-with-arima-in-python-3 
https://machinelearningmastery.com/how-to-grid-search-sarima-model-hyperparameters-for-time-series-forecasting-in-python/
	

- coding
+ a great tutorial on statsmodel.ARIMA: 
https://www.digitalocean.com/community/tutorials/a-guide-to-time-series-forecasting-with-arima-in-python-3  
+ ARIMA library: 
http://www.statsmodels.org/devel/generated/statsmodels.tsa.statespace.sarimax.SARIMAX.html

- other
correlation: https://www.datascience.com/blog/introduction-to-correlation-learn-data-science-tutorials
	correlation of recent time steps helps decide the trend parameter in the model.
	test: ACF, PACF plots.




## ETS model
ETS: Error, Trend, Seasonality
	single exponential smoothing: level.
		alpha: smoothing factor for the level. to control the rate at which the influence of the observations at prior time steps decay exponentially.
			range: [0,1]. 
			near 1: fast learning, the most recent data has more influence. 
			near 0: slow learning, more of the history will be taken into account.
	double exponential smoothing: level plus trend.
		alpha. 
		beta: smoothing factor for the trend. to control the decay of the influence of the *change* in trend. 
		trend type: additive or multiplicative
			additive: DES with a linear trend. classically referred to as Holt's linear trend model.
			multiplicative: DES with an exponential trend.
		phi: damping coefficient. to control the rate of dampening. 
			dampening: reducing the size of the trend over future time steps down to a straight line (no trend), as for longer range (multi-step) forecasts, the trend may continue on unrealistically. 
		dampen type: additive or multiplicative.
	triple exponential smoothing: level plus trend plus seasonality.
		alpha.
		beta.
		trend type.
		phi. 
		dampen type.
		gamma: smoothing factor for the seasonality. to control the influence on the seasonal component. 
		seasonality type:
			additive: TES with a linear seasonality. 
			multiplicative: TES with an exponential seasonality
		period: time steps in seasonal period. to ensure that the seasonality is modeled correctly, the number of time steps in a seasonal period (Period) must be specified. 
	
https://machinelearningmastery.com/exponential-smoothing-for-time-series-forecasting-in-python/


- hyperparameter selections
time series decomposition plot
	https://artofdatablog.wordpress.com/2017/11/24/parameters-selection-for-time-series-model-ets/
	https://machinelearningmastery.com/how-to-grid-search-triple-exponential-smoothing-for-time-series-forecasting-in-python/
AIC in model selection
	https://stats.stackexchange.com/questions/84076/negative-values-for-aic-in-general-mixed-model


- math formulas
http://course1.winona.edu/bdeppa/FIN%20335/Handouts/Exponential_Smoothing__part_3_.html
https://medium.com/datadriveninvestor/how-to-build-exponential-smoothing-models-using-python-simple-exponential-smoothing-holt-and-da371189e1a1
https://www.stat.berkeley.edu/~arturof/Teaching/STAT248/lab10_part1.html

## how to update trained time series model with new data
- statsmodel SARIMAX()
https://groups.google.com/forum/#!msg/pystatsmodels/F9hGcFCcjEQ/QbuUm4eqBQAJ 
https://github.com/statsmodels/statsmodels/issues/3623#issuecomment-300969821

https://github.com/statsmodels/statsmodels/issues/2788#issuecomment-175209448
code:
	modelA = sm.tsa.SARIMAX(dataset_A, order=(3,0,3))
	resA = modelA.fit()
	modelB = sm.tsa.SARIMAX(dataset_B, order=(3,0,3))
	resB = modelB.fit()
https://github.com/statsmodels/statsmodels/issues/3623#issuecomment-300969821
smooth: http://www.statsmodels.org/devel/generated/statsmodels.tsa.statespace.sarimax.SARIMAX.smooth.html#statsmodels.tsa.statespace.sarimax.SARIMAX.smooth
update: http://www.statsmodels.org/devel/generated/statsmodels.tsa.statespace.sarimax.SARIMAX.update.html#statsmodels.tsa.statespace.sarimax.SARIMAX.update

- statsmodel ETS
	modelA = ExponentialSmoothing(dataset_A, ...)
	resA = modelA.fit()
	modelB = ExponentialSmoothing(dataset_B, ...)
	pred = modelB.predict(resA.params)


## time series clustering
https://stats.stackexchange.com/questions/66976/how-to-cluster-time-series
http://www1.cs.columbia.edu/~jopa/Papers/PaparrizosSIGMOD2015.pdf
http://www.stat.uchicago.edu/~xiangzhu/XZhu_anl13.pdf
https://blog.newrelic.com/product-news/optimizing-k-means-clustering/

silhouette analysis: https://scikit-learn.org/stable/auto_examples/cluster/plot_kmeans_silhouette_analysis.html#sphx-glr-auto-examples-cluster-plot-kmeans-silhouette-analysis-py

Bill's example: https://dataplatform.cloud.ibm.com/analytics/notebooks/v2/193e02dd-5ed4-46a2-b508-cffff4325b85/view?access_token=28901283bfc02c5e6e30d330bf10ceda18cc666712ee0fae4358cbb41efde58d

python library with clustering: tslearn


## enable mass prediction in scale
https://machinelearningmastery.com/how-to-grid-search-sarima-model-hyperparameters-for-time-series-forecasting-in-python/
	check Parallel() to enable multiprocessing in the backend
http://www.unofficialgoogledatascience.com/2017/04/our-quest-for-robust-time-series.html
https://storage.googleapis.com/pub-tools-public-publication-data/pdf/37483.pdf

## evaluations
https://medium.com/usf-msds/choosing-the-right-metric-for-machine-learning-models-part-1-a99d7d7414e4


## time series visualization
https://machinelearningmastery.com/time-series-data-visualization-with-python/


## R coding
### R libraries
library(forecast)
library(ggplot2)

### create time series object
`ts(data, start, frequency)`
usnim_ts = ts(usnim_2002[,2], start=c(2002,1), frequency=4) 
~start=c(year,period), period corresponds to the quarter of the year: period 1 - Jan, period 2 - Apr, period 3 - Jul, period 4 - Oct. ~
~ frequency = 4 as data is quarterly ~ 

### ts visualizations
`autoplot(data, facets=T/F)` # create time plot of the data w/ or w/o facets or panels.

`ggseasonplot()` # similar to a time plot except that the data are plotted against the individual "seasons" in which the data were observed. 

`ggsubseriesplot(data)` # a subseries plot comprises mini time plots for each season, with mean for each season shown as a blue horizontal line. 

### polar coordinates
An interesting variant of a season plot uses polar coordinates, where the time axis is circular rather than horizontal; to make one, simply add a `polar` argument and set it to `TRUE`
`ggseasonplot(data, polar=T)`

### split a time series
`window(x, start, end)`  # `window()` extracts a subset from the object `x` observed btw the times `start` and `end`. 

### autocorrelation of non-seasonal time series
`gglagplot()` # plot the time series against lags of itself.
`ggAcf()` # produce ACF plots, y=ACF, x=lag. the correlations associated w/ the lag plots form is what called autocorrelation function (ACF). by observing the ACF plots, we can find the lag value that corresponds to the maximum autocorrelation. 

### autocorrelation of seasonal and cyclic time series
when data are either seaonal or cyclic, the ACF will peak around the seasonal lags or at the average cycle length.

ACF plots 
- Trends induce positive correlation in the early lags, and decreasing spikes. 
- Seasonality induce ACF peaks at the seasonal lags.
- Cyclicity induces ACF peaks at the average cycle length. 

### white noise
expectation: each autocorrelation is close to 0. 95% of all autocorrelations for white noise should lie w/i the blue lines; if not series is probably not white noise. 
- wn is a purely random ts.
- test for wn by looking at an ACF plot or by doing a Ljung-Box test.
- to check white noise:
+ `autoplot(diff(data))`
+ `ggAcf(diff(data))`
+ `Box.test(data, type="Lj")` 

### Ljung-Box test
The Ljung-Box test considers the first h autocorrelation values together. A significant test (small p-value) indicates the data are probably not white noise. A p-value greater than 0.05 suggests that the data are not significantly dft from white noise. 
`Box.test(data, lag=24, fitdf=0, type="Lj")`

### other helpful R functions
`read_excel()` # a function from readxl() package  
`frequency()` # get the no. of obs. per unit time. 


## REFS
https://campus.datacamp.com/courses/forecasting-using-r
https://www.datacamp.com/courses/manipulating-time-series-data-in-python
https://otexts.org/fpp2/
http://environmentalcomputing.net/forecasting-time-series/
https://machinelearningmastery.com/time-series-forecasting-methods-in-python-cheat-sheet/
exog vs endog: https://www.statsmodels.org/stable/endog_exog.html

## links to understand thoroughly
	http://www.chadfulton.com/files/fulton_statsmodels_2017_v1.pdf
	https://www.statsmodels.org/dev/statespace.html#statespace
	https://www.statsmodels.org/dev/_modules/statsmodels/tsa/statespace/sarimax.html
	https://www.statsmodels.org/dev/examples/notebooks/generated/statespace_sarimax_stata.html
	https://apimirror.com/statsmodels/examples/notebooks/generated/tsa_arma_1
	https://www.datascience.com/blog/time-series-forecasting-machine-learning-differences