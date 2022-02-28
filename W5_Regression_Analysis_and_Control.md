# Install and attach packages
```
PacMan <- function(pkg){new.pkg <- pkg[!(pkg %in% installed.packages()[, "Package"])] 
if (length(new.pkg))         
  install.packages(new.pkg, dependencies = TRUE)     
sapply(pkg, library, character.only = TRUE)}
pkgs <- c("foreign", "scatterplot3d", "rgl")
PacMan(pkgs)
```

# Set working directory
```
setwd("C:/Users/.../Desktop/LearningR/ Reg test")
```

# Import Age, Weight, BloodPressure data
# Rename, view, summary
Code:
```
AgeWtBp <- read.spss("………/ageweightbp.sav", 
                       to.data.frame=TRUE)
names(AgeWtBp)
view(AgeWtBp)
summary(AgeWtBp)
```
Output:
```
     Age            Weight      BloodPressure  
 Min.   :25.00   Min.   : 66.0   Min.   : 37.0  
 1st Qu.:43.25   1st Qu.:123.5   1st Qu.:133.8  
 Median :60.50   Median :170.5   Median :190.5  
 Mean   :58.30   Mean   :162.0   Mean   :177.3  
 3rd Qu.:71.75   3rd Qu.:194.2   3rd Qu.:222.2  
 Max.   :85.00   Max.   :244.0   Max.   :288.0  
```
 
# Correlations
Code:
```
cor(AgeWtBp)
```
Output:
```
                    Age    Weight BloodPressure
Age           1.0000000 0.5630766     0.7816219
Weight        0.5630766 1.0000000     0.6723903
BloodPressure 0.7816219 0.6723903     1.0000000
```


# Scatterplots and 3D scatterplots

Note:
Visualizing the association between variables as we add additional predictors becomes difficult. 
However, R can provide three dimensional scatterplots to visualize two predictors and an outcome. 
These scatterplots can be constructed using the scatterplot3d function from the scatterplot3d package.

In presenting a model, a tilde symbol (~) is used to indicate the outcome variable is regressed on 
our predictor variables, which are separated with (+) signs.

Code:
```
ScatAgeWtBp <- scatterplot3d(x=AgeWtBp$Age, y=AgeWtBp$BloodPressure, 
                             z=AgeWtBp$Weight, 
                             main="Blood Pressure by Age and Weight", 
                             xlab="Age", ylab="Blood Pressure", 
                             zlab="Weight", box=TRUE, type="h")
```
![Screen Shot 2022-02-27 at 10 54 22 PM](https://user-images.githubusercontent.com/57466154/155923410-5faa5860-7694-467c-9531-df7b181d9874.png)
```
ScatPlane <- lm(BloodPressure~Age+Weight, data=AgeWtBp)
ScatAgeWtBp$plane3d(ScatPlane)
```
![Screen Shot 2022-02-27 at 10 54 53 PM](https://user-images.githubusercontent.com/57466154/155923449-88facc52-bc95-4098-8393-14242a48a6f8.png)

# 3D scatterplot - rotates
Note:
If you are presenting three dimensional graphs to an in-person audience or want to see the 
associations from multiple perspectives, the plot3d function from the rgl package can be 
used. The code for this function is similar to the code for the scatterplot3d function, 
but the graph opens in a new window and you can rotate the graph to more easily see 
certain associations. 

Code:
```
install.packages("rgl")
library(rgl) #check in packages section
plot3d(x=AgeWtBp$Age, y=AgeWtBp$BloodPressure, 
       z=AgeWtBp$Weight, xlab="Age", ylab="Blood Pressure", 
       zlab="Weight")
```
![Screen Shot 2022-02-28 at 12 19 36 PM](https://user-images.githubusercontent.com/57466154/156028672-eb19568f-c649-4727-b496-51afb97247e2.png)

# Scatterplot matrix
Notes: 
As a final option, you can graph all of the bivariate associations using the pairs function. 
In this assessment, ensure the scores for each variable are normally distributed and the 
associations between each pair of variables are linear. Additionally, the scatterplots can 
be used to assess homoscedasticity. 

Code:
```
pairs(AgeWtBp, upper.panel = NULL)
pairs(AgeWtBp[,1:3], upper.panel = NULL)
```
![Picture1](https://user-images.githubusercontent.com/57466154/156030604-9ef6b044-22c5-4aa9-baa7-a12a26867e2f.png)

# Regression
Note:
After the assumptions are checked, the regression analysis can be conducted. In R linear 
regressions are performed using the lm (linear model) function. This function uses a 
formula to indicate the model to evaluate. The outcome (y) variable goes on the left side 
of a tilde (~)symbol, and the predictor variables go on the right side of the tilde symbol 
with a plus sign (+) between each x variable: y~x +X2

Code:
```
Bp_RegMod <- 
  lm(BloodPressure~Age+Weight, 
      data=AgeWtBp)

print(summary.lm(Bp_RegMod), 
      digits=4)
```
Output:
```
Call:
lm(formula = BloodPressure ~ Age + Weight, data = AgeWtBp)

Residuals:
    Min      1Q  Median      3Q     Max 
-74.752 -20.460  -5.626  22.922  63.436 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) -28.0464    27.9846  -1.002 0.325141    
Age           2.1610     0.4749   4.551 0.000102 ***
Weight        0.4897     0.1867   2.623 0.014163 *  
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 36.69 on 27 degrees of freedom
Multiple R-squared:  0.6899,	Adjusted R-squared:  0.667 
F-statistic: 30.04 on 2 and 27 DF,  p-value: 1.363e-07
```
# Semipartial correlations
Notes: 
Semi-partial correlations remove the variance shared between the predictors. In this case, 
we can perform a regression (lm) of age on weight and save the residuals. Then we can 
correlate (cor) the residuals with blood pressure and square(^2) the correlation for the 
squared semi-partial correlation, (sr2). 

Code(Age):
```
resAge <- residuals(lm(Age~Weight,data=AgeWtBp))
round(cor(AgeWtBp$BloodPressure, resAge)^2, 2)
```
Output:
```
[1] 0.24
```
Code(Wt):
```
resWt <- residuals(lm(Weight~Age,data=AgeWtBp))
round(cor(AgeWtBp$BloodPressure,resWt)^2, 2)
```
Output:
```
[1] 0.08
```
Notes: 
The standardized predicted values and residuals can be plotted. First, save the predicted 
values (predict) from the regression model. Second, save the standardized predicted values 
by subtracting the mean and dividing by the standard deviation (sd) of the predicted 
values. Third use the rstandard function to obtain the standardized residual values. 
A line (abline) can be added at y equals 0. 

# Plot residuals and predicted values
Notes: 
If we have correct functional form, the local means will have a value around 0. Just as 
we would get names () for our datasets, we can get names() for our regression model. 
(Bp_RegMod)

Code:
```
RegPred <- predict(Bp_RegMod)
StdPred <- (RegPred-mean(RegPred))/
  sd(RegPred)
StdResid <- rstandard(Bp_RegMod)
plot(StdPred, StdResid)
abline(0,0)
```
![Picture1](https://user-images.githubusercontent.com/57466154/156032941-159b20e1-847b-4eb8-ab04-f0675ce09d61.png)

# Model Evaluation
Notes: 
Based on the above plot, it does seem that the local means are roughly 0. If we need more secure
outcome, we can also use the Ramsey RESET test. We will use the resettest() function that is part 
of the lmtest package , so we need to make sure it is installed and loaded. In the parentheses 
of the resettest argument, we specify the model name, the type of test we want, (we want to use 
the “fitted” values) and what power to test. The test compares the fit to our original model to 
the fit of a model that includes here, the squared and cubed versions of the fitted values. 

Make sure to load the lmtest package!

Code:
```
library(lmtest)
resettest(Bp_RegMod,power=2:3,type="fitted")
```
Output:
```
	RESET test

data:  Bp_RegMod
RESET = 0.037309, df1 = 2, df2 = 25, p-value = 0.9634
```

# Interpretation
Note:
The null hypothesis of this test is that there is no significant difference in the amount of 
variance explained in the outcome variable. Here we see that p > t 0.05, so we fail to reject 
the null and conclude that we have not violated the assumption of correct functional form. 

We can also get a visual sense of which predictors may not be linearly related to the outcome 
variable by using a component-plus-residual-plot. This plot what the linear relationship 
between outcome and predictor variables should be compared to the estimated relationship. Any 
variables’ plots that demonstrate nonlinearity may need to be transformed to correct 
functional form. 

To perform this test, we will use the [crPlots()](https://www.rdocumentation.org/packages/car/versions/1.2-9/topics/cr.plots) function from the car library

Code:
```
library(car)

x11()
crPlots(Bp_RegMod)
```

![Picture1](https://user-images.githubusercontent.com/57466154/156034838-331e87be-bf73-4f5a-b64e-0d1b9ef67459.png)

Note:
The dashed blue line is what the linear relationship should be, while the solid pink line is 
the estimated relationship. Both plots demonstrate linearity in this case.  

# [Heteroscedasticity](https://ppwq.net/?p=1390)
Note:
We can use the Breusch-Pagan test for determining whether heteroscedasticty exists. We can use 
the bptest() function and in the argument specify our mode and that we do not want to use 
studentized residuals(studentized=FALSE). The null hypothesis is that there is constant 
error variance (homoscedasticity). 

Code:
```
bptest(Bp_RegMod)
```
Output: 
```
	studentized Breusch-Pagan test
data:  Bp_RegMod
BP = 3.5704, df = 2, p-value = 0.1678
```

Note:
We see that the p-value is above 0.05, thus we fail to reject the null hypothesis and conclude 
that we do not have heteroscedasticity. 

# Normality
Note:
The normality assumption is critical for being able to make inferences about the population from 
our sample data. If we violate normality and make no corrections, then we are not confident that 
the results we obtain in our sample necessarily apply to the population.  We will now consider 
three different tests for normality: Histogram, QQ plot, Shapiro Wilks Test

# 1.Histogram
Note:
we ill create a histogram of the residuals for the model (Bp_RegMod) using the hist() function. 
With the histogram, we are looking for whether the residuals appear normally distributed. With 
real data, the residuals will never be perfectly normal, and so we need to use our own judgement 
of how normal they appear. 

Code:
```
x11()
hist(Bp_RegMod$residuals,xlab="Residuals",main="")
```
![Picture1](https://user-images.githubusercontent.com/57466154/156052351-20690187-7c8b-41cc-ad82-24592e44e2f0.png)

Observation: 
The residuals are not far off being normal. 

# 2.Q-Q Plot
Note:
We will use a Q-Q plot to examine the normality of the residuals. The Q-Q plot graphs whether 
our model’s residuals are the same as the theoretical values for residuals in a perfect normal 
distribution. Each point represents one quantile. When the residuals are the same, they will 
lie on the 45-degree angle. We are looking for how many, in general, of the residuals fall on
the line; we almost always get the tails trailing away from the line. Like the histogram, 
there is no hard and fast rule for normality and so it is more of a judgement call. 

We first include the qqnorm() function to get our normal Q-Q plot for our residuals from our 
model, Bp_RegMod. We then use the qqline() function to get a line that passes through the first 
and third quantiles.

Code:
```
x11()
qqnorm(Bp_RegMod$residuals)
qqline(Bp_RegMod$residuals,col="red")
```
![Picture1](https://user-images.githubusercontent.com/57466154/156052738-425bc8b0-be52-4146-8b60-e833ccc0bbc2.png)

# 3.Shapiro Wilks Test
Note:
To be certain of our conclusions, we lastly perform  a formal test for normaility. One common 
option to test normailty is the Shapiro-Wilk normality test- in R, we use the shapiro.test() 
function. This test works for sample sizes between 3- 5000 observations. The null hypothesis 
is that the residuals are normally distributed so we do  not want to reject the null. 

Code:
```
shapiro.test(Bp_RegMod$residuals)
```
Output: 
```
	Shapiro-Wilk normality test
data:  Bp_RegMod$residuals
W = 0.97455, p-value = 0.6693
```

Observation: 
Based on a p value of .06693, we cannot reject the null and so we can conclude 
that the variables are normally distributed.  


