
<!-- README.md is generated from README.Rmd. Please edit that file -->

<!-- badges: start -->

<!-- badges: end -->

Script to estimate statistical power using Kendall Test for trend on
log-normal *E. coli* concentrations. Full script is in the `scripts`
folder.

Estimate and fit distribution to the empirical data:

``` r

library(fitdistrplus)

plotdist(dist.data$ResultMeasureValue, histo = TRUE, demp = TRUE)
```

![](https://raw.githubusercontent.com/mps9506/mk-power/master/plotdist.png)<!-- -->

Try some distributions. Lognormal looks appropriate and matches
literature descriptions.
![](https://raw.githubusercontent.com/mps9506/mk-power/master/fitdist.png)<!-- -->

Grab the logmean and logsd

``` r
fln <- fitdist(dist.data$ResultMeasureValue, "lnorm")
summary(fln) ## fln looks most reasonable by plot and AIC

## get the sample distribution parameters
mu <- fln$estimate["meanlog"]
sd <- fln$estimate["sdlog"]
```

Generate samples, test, and repeat:

``` r
## number of resamples
r <- 10000

## percent change to detect
percent_change = -20

samples_per_year <- c(2,3,4,5,6,7,8,10)


output <- tibble(n_years = rep(7, length(samples_per_year)),
                 samples_per_year = samples_per_year) %>%
  mutate(power = furrr::future_map2(n_years, samples_per_year,
                                    ~power_ken(r, mu, percent_change,
                                               .x, .y)))
```

![](https://raw.githubusercontent.com/mps9506/mk-power/master/example.png)<!-- -->
