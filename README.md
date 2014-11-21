SCS Curve Number method (R-package)
===================================

This package implements an adaptation of the SCS Curve Number method according to [Hawkins (1993)](http://dx.doi.org/10.1061/(ASCE)0733-9437(1993)119:2(334)).

#### Basics
Install and load packages
```R
# Install dependent packages from CRAN:
x <- c("zoo", "EcoHydRology", "udunits2","devtools")
install.packages(x)
lapply(x, require, character.only=T); rm(x)

# Install dependent packages from github:
install_github("josephguillaume/hydromad")
install_github("cvitolo/r_CurveNumber", subdir = "curvenumber")
```

#### Load the library and some test data
Library and test dataset can be loaded as follows:
```R
library(curvenumber)
data(DATA) 
```

DATA is in mm/d but the time step is 1 hour, below is an adjustment:
```R
InputTS <- DATA/24; rm(DATA)
```

If the adjustment is not made, volumes should be re-calculated as the sum of the streamflow values multiplied by [the time interval length / your main time units]. E.g. if you use mm/day for hourly observations, the multiplier is [hour/day] = 1/24. If your main time units are the same as the time interval (mm/hr for hourly data), the multiplier is 1.
```

### Identify Rainfall-Runoff events
According to [Hawkins (1993)](http://dx.doi.org/10.1061/(ASCE)0733-9437(1993)119:2(334)), in order to calculate the curve Number, the rainfall and runoff events can be identified separately. Return periods are then matched using the Frequency Matching approach [Hjelmfelt (1980)](http://cedb.asce.org/cgi/WWWdisplay.cgi?9734). 

```R
df  <- EventIdentification(dataX = InputTS,
                           hours2extend = 6, plotOption = FALSE,
                           stepsBack = 5, timeUnits = "hours")
```

### Calculate the Curve Number
Determine the Curve Number and k coefficient and also plot CN-P behaviour to 
define the type of asymptote
```R
coef <- CalculateCN(dfTPQ = df, PQunits = "mm", plotOption = TRUE)
```

Please note that there are three types of behaviour: 
* "standard" (increasing asymptotically), 
* "complacent" (decreasing indefinitely) and 
* "violent" (increasing asymptotically).
Here, only the standard behaviour is implemented. In this case, CN (infinity) is
the value of CN that corresponds to the largest rainfall events and can be 
calculated by a nonlinear least squares curve fitting (red line).

#### Warnings
This package and functions herein are provided as is, without any guarantee.

#### Please leave your feedback
This package was developed by [Claudia Vitolo](http://www.imperial.ac.uk/people/c.vitolo) and [Nataliya Le Vine](http://www.imperial.ac.uk/people/n.le-vine). We would greatly appreciate if you could leave your feedbacks via email (cvitolodev@gmail.com).
