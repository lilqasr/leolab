---
layout: default
---
# Forecasting Nicaragua's government taxes Income using SARIMA model with Python

The present project was aimed to build a model in order to predict Nicaragua's central government income taxes. 
The data source for this project is [Banco Central de Nicaragua](https://view.officeapps.live.com/op/view.aspx?src=https%3A%2F%2Fwww.bcn.gob.ni%2Fsites%2Fdefault%2Ffiles%2Festadisticas%2Ffinanzas_publicas%2F7-7.xlsx&wdOrigin=BROWSELINK) (2003-2015) and [Ministerio de Hacienda y Credito Publico](https://view.officeapps.live.com/op/view.aspx?src=http%3A%2F%2Fwww.hacienda.gob.ni%2Fhacienda%2Ffinanzaspublicas%2Fefp%2F1-7.xlsx&wdOrigin=BROWSELINK) (2016-2022) taxes income databases.

One of the most used models to predict economics indicators using just Time Series data is the SARIMA model. I won't talk about Time Series forecasting and models because there is plenty bibliography about this, but the most important thing to know is that almost all economics data needs to make some kind of transformation in order to be able to fit models and make right predictions. This goes to every data that can be collected on time (sales, demand, inventory).

To build this model to predict the Nicaragua's government income taxes I get the monthly data from January 2003 to December 2022. The idea is to predict a whole year into the future.

## I. Build the TS, and the EDA to check for Stationarity

As I mentioned before, in order to build a good model to predict the values of a TS, this needs to be Stationary, i.e. no trend, constant mean, variance, and autocorrelation structure over the time. In order to know this is necessary to get some plots and run some statistical test to understand the characteristic of the TS, because it would be a main Key when building the model.

<img width="700" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/e6376276-b5f7-46f5-8409-06843e0650f6">

So, the first thing to do is make a simple line plot where the TS can be study. In the above graph, we can see that my TS has clearly a positive Trend, and it shows a non constant variance of the values, meaning that the dispersion of values gets higher as time goes on. These characteristics of this TS lead to apply some type of transformation in order to become to a constante variance, and as I said before, eliminate the trend.

Since we have a TS with a increasing variance, the best thing to do is apply natural logarithms.

<img width="700" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/799efd5d-bef0-4eb4-bc7e-8162b9c478f3">

As you can see this first transformation looks like stabilize a little our TS, but still has a trend and it looks like the variance is not constant yet. Another thing that this TS has is a seasonality. Almost all of the economics indicators collected, particularlly in TS data, have some kind of seasonality or Cycles. We can detect this by putting attention on the lines plot where a peak is repeated every x time, in this case looks like every year. Another way is by making a boxplot with every month of the year and see the median of every one of them.

<img width="700" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/59207616-2305-4f93-9fda-91aba8009030">

The above chart show us that the median of the 12 months is higher in March and November. This give us an idea the seasonality on our TS.

Considering the above, our TS is going to need a seasonality differencing and maybe an additional lag one difference. 

The next plot shows all the types of transformations made for this TS. This give us a first idea of how is going to be built the model.

<img width="966" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/c0e88efe-eda5-4d3e-8f0b-4dd40f37f629">

As we can see, when we apply just the first difference to our logTS, the values looks like stationary. In fact, when running an ADF test, that is a statistical test to check if the data is stationary, the results give us that the TS is already stationary.

<img width="445" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/46db6662-bfa5-4b14-a9cf-b837583b8f28">

If we apply a seasonal difference (12 months) to the logTS, apparently is not enough to have a Stationary Series.
<img width="463" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/9acccf0f-d7f2-48d5-b507-fd8f5cc292a0">

I would need to apply a first difference to the seasonal difference in order to make stationary the Series, just like this:
<img width="516" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/32c73495-045e-4095-8147-9dc342726e5b">

## II. Build the model

Once I check how many transformations would need the TS, next step is to find the parameters for the arima model. I already know it should be a SARIMA model because the TS has Seasonality. So, in order to know the parameters (p,d,q, and P, D, Q), it's necessary to check the Autocorrelation and Partial Autocorrelations Plot, because this would give us an idea of how to choose them manually.

<figure>
<img width="1039" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/3b2697c2-c560-4cb7-840e-4bea4bb337da">
<figcaption align = "center"><b> Seasonal Diff TS, ACF and PACF.</b> </figcaption>
</figure>

<figure>
<img width="1047" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/70b47a78-6522-4599-9504-cb45825ed46f">
<figcaption align = "center"><b> Seasonal Diff + LAG 1 DIFF TS, ACF and PACF.</b> </figcaption>
</figure>

According with the ACF and PACF plots, we problably have a seasonal MA(1) model (Seasonal Diff + LAG 1 DIFF TS graph has only 1 really significant lag (12)), and a non seasonal MA(1). We could have a
(0,1,1)(0,1,1) SARIMA Model. In addition, Python has an ARIMA package that find the best parameters for the ARIMA or SARIMA model. We are going to check which one is the best and make some statistical test to evaluate them.

```Python
automodel = pm.auto_arima(np.log(cleancgtaxesnic.Amount),
                           error_action='ignore', m=12, D=1, d=1,
                           start_P=0,
                           start_q=0, start_Q=0, start_p=0,
                           suppress_warnings=True, seasonal=True,
                           stepwise=True, trace=True)
print(automodel.aic())
```

<img width="500" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/7f4ebef9-f435-4b31-a8d3-0b16b6580c7d">

So, since I choose an SARIMA (0,1,1)(0,1,1,12) model, I also checked if there was a better parameters for the order of differencing I Choose. The autoarima model gave me another choice: a (1,1,1)(0,1,1,12) model, that has a similar AIC to the one I selected (-351.918 and -351.755).

Even though, these were the parameters chose taking in count a seasonal difference and a lag 1 difference, in the EDA we saw that when I just made a lag one difference, the TS became stationary. Considering this, I ran an auto arima again letting it to choose the order of differencing.

```Python
automodel2 = pm.auto_arima(np.log(cleancgtaxesnic.Amount),
                           error_action='ignore', m=12,start_P=0,
                           start_q=0, start_Q=0, start_p=0,
                           suppress_warnings=True, seasonal=True,
                           stepwise=True, trace=True)
print(automodel2.aic())
```
<img width="500" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/8c89dc3a-e077-496f-a8bb-aa6e8ec7bd6a">

In this case, the parameters choose was different, giving just the lag 1 difference, and not the seasonal one: ARIMA(2,1,2)(1,0,1)[12] intercept

So, we are going to work with this two models, and see which one is better to predict.

## II. Run the models

First of all, Both of the models looks like all the p-values of the coefficients are significants.

Model 1
<img width="500" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/e0c94d26-c0b2-4e18-8a1b-9f78bd474589">

Model 2
<img width="500" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/21e63fc0-500b-48ff-abc7-96934bd61626">

Second part, is to evaluate the residuals. We see optimals behaviors, but is necessary to run some test.

Model 1
<img width="870" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/fbe4b863-5eef-46b9-ad7f-cc49d3d24fda">

Model 2
<img width="870" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/8c4c5bb4-6bf5-4c75-aabe-c2ae569795a8">


Tests:
- Normality (Jarque-Bera)
- Serial correlation (Ljung-Box)
- Heteroskedasticity
- Tests for change in variance between residuals.
- Durbin Watson

Model 1
<img width="400" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/bb8c738a-9c79-4cd5-bb4a-1158c416eede">

Model 2
<img width="400" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/7cb7c332-dda0-4b77-be21-31fc4b79a22e">

All results, except for Normality test, says that the residuals are good: no serial correlations, and no heterokedasticity. The normal distribution could be because of some extreme (outliers) values because of the crisis in Nicaragua (political crisis and COVID-19 what produced a economic crisis).

## III. Evaluate the models

In order to evaluate the models, I calculated the RMSE of them and apparently the second model, the one with only a lag 1 difference (basically an ARIMA model), gave me the best result:

RMSE MODEL1 IS:  0.4944248649824081
RMSE MODEL2 IS:  0.4397149482406612

## IV. Predictions

### MODEL 1

The chart below shows the observed values vs the predictions (for the last 2 years). It seems to capture pretty well the behaviour of the TS values.

<img width="800" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/792a2448-1c5c-4d91-8d33-9eec1a2ece45">

The chart below shows the forecast for the next 24 months. It also seems that captured the seasonality showed in the past values.
<img width="800" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/20783130-41b2-4ac0-9895-2c432c3dfd5a">

### MODEL 2

According to this chart, looks like did not capture very well the seasonality behaviour, but it fitted pretty well the rest of the actual values.
<img width="800" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/dabc284b-dd44-4c38-803d-7122d1a74ce1">

The forecasting seems pretty well because captured the seasonality behaviour for the future values.
<img width="800" alt="image" src="https://github.com/lilqasr/leolab/assets/112327873/7497a18b-e68e-4d13-9e84-86b0354b4c4a">

## V. Last evaluations

Previously, we saw that the best model according with the RMSE is the second one. If we evaluate the predictions with the MAPE, it confirms the results:

Mean Absolute Percentage Error for MODEL 1 is 0.0116 == means that the model is about 98.84% accurate to predict the future values
Mean Absolute Percentage Error for MODEL 2 is 0.0105 == means that the model is about 98.95% accurate to predict the future values

The difference is a little. Now, we are going to check if the values for the first three months of 2023 are correct, according with the official data. 





