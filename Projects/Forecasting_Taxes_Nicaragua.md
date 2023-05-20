---
layout: default
---
# Forecasting Nicaragua's government taxes Income using SARIMA model

The present project was aimed to build a model in order to predict Nicaragua's central government income taxes. 
The data source for this project is [Banco Central de Nicaragua](https://view.officeapps.live.com/op/view.aspx?src=https%3A%2F%2Fwww.bcn.gob.ni%2Fsites%2Fdefault%2Ffiles%2Festadisticas%2Ffinanzas_publicas%2F7-7.xlsx&wdOrigin=BROWSELINK) (2003-2015) and [Ministerio de Hacienda y Credito Publico](https://view.officeapps.live.com/op/view.aspx?src=http%3A%2F%2Fwww.hacienda.gob.ni%2Fhacienda%2Ffinanzaspublicas%2Fefp%2F1-7.xlsx&wdOrigin=BROWSELINK) (2016-2022) taxes income databases.

One of the most used models to predict economics indicators using just Time Series data is the SARIMA model. I won't talk about Time Series forecasting and models because there is plenty bibliography about this, but the most important thing to know is that almost all economics data needs to make some kind of transformation in order to be able to fit models and make right predictions. This goes to every data that can be collected on time (sales, demand, inventory).

To build this model to predict the Nicaragua's government income taxes I get the monthly data from January 2003 to December 2022. The idea is to predict a whole year into the future.

## I. First part: Build the TS, and the EDA to check for Stationarity

As I mentioned before, in order to build a good model to predict the values of a TS, this needs to be Stationary, i.e. no trend, constant mean, variance, and autocorrelation structure over the time. In order to know this is necessary to get some plots and run some statistical test to understand the characteristic of the TS, because it would be a main Key when building the model.
