# Model Summary
What's the purpose?</br>
To se how phsics scores are predicted from variables including gender, intelligence, emotional intelligence, verbal SAT scores, and math SAT scores.

# Prepare data
## Install and attach packages
```
PacMan <- function(pkg){new.pkg <- pkg[!(pkg %in% installed.packages()[, "Package"])] 
if (length(new.pkg))         
  install.packages(new.pkg, dependencies = TRUE)     
sapply(pkg, library, character.only = TRUE)}
pkgs <- c("foreign", "ppcor", "leaps", "caret")
PacMan(pkgs)
```

## Set working directory
```
setwd("/Users/chichi/Desktop/school work/R/w6")
```

## Import data
Code:
```
Physics <- read.csv("predictphysics.csv")
names(Physics)
View(Physics)
```
Output:
```
 [1] "idno"     "age"      "gender"   "ei"       "iq"      
 [6] "vsat"     "msat"     "create"   "abstract" "physics" 
```

# Standard Regression
Notes:<br/>
The standard regression enters all of the variables of interest in one model. In this case we are interested in how physics scores (physics), are predicted from gender, intelligence, (iq), emotional intelligence (ei), verbal SAT scores, (vsat), and math SAT scores, (msat). The output from the model can be accessed 

Code:
```
Phys_RegMod <- lm(physics~gender+iq+ei+vsat+msat,data=Physics)
print(summary.lm(Phys_RegMod), digits=5)
```
Output:
```
Call:
lm(formula = physics ~ gender + iq + ei + vsat + msat, data = Physics)

Residuals:
      Min        1Q    Median        3Q       Max 
-18.83699  -5.37648  -0.62882   5.54833  21.76855 

Coefficients:
              Estimate Std. Error t value  Pr(>|t|)    
(Intercept) -45.200616   9.124582 -4.9537 1.610e-06 ***
gender       -7.452708   1.334900 -5.5830 8.126e-08 ***
iq            0.130124   0.069945  1.8604   0.06439 .  
ei           -0.031818   0.071782 -0.4433   0.65808    
vsat         -0.093475   0.009694 -9.6425 < 2.2e-16 ***
msat          0.357249   0.018651 19.1546 < 2.2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 7.8703 on 189 degrees of freedom
  (5 observations deleted due to missingness)
Multiple R-squared:  0.80479,	Adjusted R-squared:  0.79962 
F-statistic: 155.83 on 5 and 189 DF,  p-value: < 2.22e-16
```

[Intercept](https://i.imgur.com/ItBQeFR.jpg)<br/>
[Statistical significance](https://www.simplypsychology.org/p-value.html)

Notes:<br/> 
The overall model was statistically significant: **R-squared= .80**, **Adjusted R-squared =.80**, **F (5,189) = 155.83**, **p <.001**. 
Three of the predictor variables contributed significantly to the overall model including “msat”, “vsat” and gender. 
The output here is **[unstandardized coefficients](https://www.facebook.com/258972167562965/photos/何謂標準化迴歸係數standardized-regression-cofficient此例子使用身高去預測體重做一個簡單的統計結果依變項dependentwei/342608869199294/)**.

Code:
```
anova(Phys_RegMod)
```
Output:
```
Analysis of Variance Table

Response: physics
           Df  Sum Sq Mean Sq F value    Pr(>F)    
gender      1  8100.8  8100.8 130.782 < 2.2e-16 ***
iq          1  8938.3  8938.3 144.302 < 2.2e-16 ***
ei          1  3466.7  3466.7  55.967 2.699e-12 ***
vsat        1  5030.6  5030.6  81.216 2.228e-16 ***
msat        1 22726.1 22726.1 366.897 < 2.2e-16 ***
Residuals 189 11706.9    61.9                      
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

## Brief check on assumptions
### Linearity
Code:
```
library(car)
x11()
crPlots(Phys_RegMod)
```
![e6d0ff05-0a74-41d0-8b86-28c3de9c3e86](https://user-images.githubusercontent.com/57466154/156632856-6a1526ef-251b-4021-a4c2-a325fcf8695a.png)

### Histograms
Code:
```
x11()
hist(Phys_RegMod$residuals,xlab="Residuals",main="")
```
![c86eaa2e-1dfb-44b7-b05f-b9480621e672](https://user-images.githubusercontent.com/57466154/156633132-898f9a16-99c6-466b-bf05-0ec187078463.png)

### Q_Q Plot
Code:
```
x11()
qqnorm(Phys_RegMod$residuals)
qqline(Phys_RegMod$residuals,col="red")
```
![cd384f64-f36a-40e5-8c23-094de3e20c3d](https://user-images.githubusercontent.com/57466154/156633396-a52c5506-08d1-48fb-aa16-8b9604f98722.png)

# Standardized coefficients
### if we want stadardized coefficients

Code:
```
install.packages("lm.beta")
library(lm.beta)
lm.beta(Phys_RegMod)
```
Output:
```
Call:
lm(formula = physics ~ gender + iq + ei + vsat + msat, data = Physics)

Standardized Coefficients::
(Intercept)      gender          iq          ei        vsat 
 0.00000000 -0.21248631  0.10869986 -0.02218276 -0.56308992 
       msat 
 0.88118041 
```

# Semipartial correlations
Notes: <br/>
We can use the **spcor.test function** from the ppcor package. This function requires the data to be formatted differently **with missing data (NA) removed (na.omit).** The select argument allows us to focus on the variables included in the model so cases are not removed unless they have missing data on the variable of interest. Additionally, we need to reformat the gender variable so it is classified as numeric. 

## Prepare data
Code:
```
Physics2 <- na.omit(subset.data.frame(Physics, 
    select=c("physics", "gender", "iq", "ei", 
             "vsat", "msat")))

Physics2$gender <- as.numeric(Physics2$gender)
class("gender")
class(Physics2$gender)
```
Note: <br/> 
Check to ensure that gender has been transformed to a numeric variable.

Output:
```
> class("gender")
[1] "character"

> class(Physics2$gender)
[1] "numeric"
```

## Semipartial correlations 
Notes: <br/>
After the data are properly formatted, the semipartial correlation can be generated for each variable (e.g. gender) with the outcome variable. (i.e. physics). The last part of the function call notes the other variables included in the model: Physics2.[,c(”iq”,”ei”,”msat”,”vsat”)]. The output from the spcor.test function can be used to calculate the squared (^2) semipartial correlation. 

Code:gender
```
sr_gender <- spcor.test(
  Physics2$physics, Physics2$gender, 
  Physics2[c("iq","ei","msat","vsat")])
sr_gender$estimate^2
```
Output:
```
[1] 0.03219449
```

Code:VSAT
```
sr_VSAT <- spcor.test(
  Physics2$physics, Physics2$vsat, 
  Physics2[,c("gender", "iq", "ei", "msat")])
sr_VSAT$estimate^2
```
Output:
```
[1] 0.09603616
```

Code:MSAT
```
sr_MSAT <- spcor.test(
  Physics2$physics, Physics2$msat, 
  Physics2[,c("gender", "iq", "ei", "vsat")])
sr_MSAT$estimate^2
```
Output:
```
[1] 0.3789615
```

Note:  <br/>
The squared semipartial correlations (sr2) indicate the proportion of variance uniquely associated with each of the three predictors: gender (sr2) = .03, vsat(sr2)=.10, and msat (sr2)= .38. These findings suggest **msat was the strongest predictor of physics scores**. 

# Plot residuals and predicted values
Notes: <br/>
The plot for the standardized predicted values and standadridzed residuals can be obtained as follows: 

Code:
```
RegPred <- predict(Phys_RegMod)
StdPred <- (RegPred-mean(RegPred))/
  sd(RegPred)
StdResid <- rstandard(Phys_RegMod)
plot(StdPred, StdResid)
abline(0,0)
abline(h=0,col="red")
```
![825654b4-a5bf-4ac7-9fe2-dbdb2e9e44e0](https://user-images.githubusercontent.com/57466154/156628029-f6c21e7b-039a-422f-9104-ac9a82e21215.png)

# User-determined regression
Note: <br/>
A user -determined regression provides another **approach to modeling the associations between the predictors and outcome**. User -determined regressions involves a series of regressions with predictors added **based on factors the researcher decided were relevant**. 

In this case, we will run five regressions models with a predictor added at each step. The order of entry will be gender, intelligence (iq), emotional intelligence (ei), verbal SAT score (vsat), and math SAT score (msat). 

Code:
```
Phys_RegMod1 <- lm(physics~gender, data=Physics)
summary(Phys_RegMod1)
```
Output:
```
Call:
lm(formula = physics ~ gender, data = Physics)

Residuals:
    Min      1Q  Median      3Q     Max 
-43.594 -10.889   0.106  10.815  41.106 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)   98.876      3.707   26.68  < 2e-16 ***
gender       -12.891      2.348   -5.49 1.25e-07 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 16.39 on 193 degrees of freedom
  (5 observations deleted due to missingness)
Multiple R-squared:  0.1351,	Adjusted R-squared:  0.1306 
F-statistic: 30.14 on 1 and 193 DF,  p-value: 1.253e-07
```

Code:
```
Phys_RegMod2 <- lm(physics~gender+iq,data=Physics)
summary(Phys_RegMod2)
```
Output:
```
Call:
lm(formula = physics ~ gender + iq, data = Physics)

Residuals:
    Min      1Q  Median      3Q     Max 
-46.067  -9.293   0.828  10.931  32.999 

Coefficients:
             Estimate Std. Error t value Pr(>|t|)    
(Intercept)  54.55308    7.78293   7.009 3.96e-11 ***
gender      -14.36467    2.15430  -6.668 2.69e-10 ***
iq            0.46489    0.07353   6.323 1.76e-09 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 14.95 on 192 degrees of freedom
  (5 observations deleted due to missingness)
Multiple R-squared:  0.2841,	Adjusted R-squared:  0.2767 
F-statistic:  38.1 on 2 and 192 DF,  p-value: 1.159e-14
```

Code:
```
Phys_RegMod3 <- lm(physics~gender+iq+ei, data=Physics)
summary(Phys_RegMod3)
```
Output:
```
Call:
lm(formula = physics ~ gender + iq + ei, data = Physics)

Residuals:
    Min      1Q  Median      3Q     Max 
-41.117  -9.579   0.040  10.996  28.109 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) 90.47218   11.52689   7.849 2.93e-13 ***
gender      -9.70001    2.36335  -4.104 6.00e-05 ***
iq           0.45951    0.07069   6.500 6.82e-10 ***
ei          -0.39383    0.09615  -4.096 6.20e-05 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 14.37 on 191 degrees of freedom
  (5 observations deleted due to missingness)
Multiple R-squared:  0.3419,	Adjusted R-squared:  0.3316 
F-statistic: 33.08 on 3 and 191 DF,  p-value: < 2.2e-16
```

Code:
```
Phys_RegMod4 <-  lm(physics~gender+iq+ei+vsat,data=Physics)
summary(Phys_RegMod4)
```
Output:
```
Call:
lm(formula = physics ~ gender + iq + ei + vsat, data = Physics)

Residuals:
    Min      1Q  Median      3Q     Max 
-36.596  -9.120   0.653   8.823  30.188 

Coefficients:
             Estimate Std. Error t value Pr(>|t|)    
(Intercept)  53.38711   12.88747   4.143 5.16e-05 ***
gender      -11.82230    2.24974  -5.255 3.95e-07 ***
iq            0.85763    0.10047   8.537 4.37e-15 ***
ei            0.04448    0.12259   0.363    0.717    
vsat         -0.08731    0.01657  -5.269 3.71e-07 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 13.46 on 190 degrees of freedom
  (5 observations deleted due to missingness)
Multiple R-squared:  0.4258,	Adjusted R-squared:  0.4137 
F-statistic: 35.23 on 4 and 190 DF,  p-value: < 2.2e-16
```

Code:
```
Phys_RegMod5 <- lm(physics~gender+iq+ei+vsat+msat,data=Physics)
summary(Phys_RegMod5)
```
Output:
```
Call:
lm(formula = physics ~ gender + iq + ei + vsat + msat, data = Physics)

Residuals:
     Min       1Q   Median       3Q      Max 
-18.8370  -5.3765  -0.6288   5.5483  21.7685 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept) -45.200616   9.124582  -4.954 1.61e-06 ***
gender       -7.452708   1.334900  -5.583 8.13e-08 ***
iq            0.130124   0.069945   1.860   0.0644 .  
ei           -0.031818   0.071782  -0.443   0.6581    
vsat         -0.093475   0.009694  -9.643  < 2e-16 ***
msat          0.357249   0.018651  19.155  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 7.87 on 189 degrees of freedom
  (5 observations deleted due to missingness)
Multiple R-squared:  0.8048,	Adjusted R-squared:  0.7996 
F-statistic: 155.8 on 5 and 189 DF,  p-value: < 2.2e-16
```

# Evaluate change at each step

Code:
```
anova(Phys_RegMod1)
anova(Phys_RegMod1, Phys_RegMod2)
anova(Phys_RegMod2, Phys_RegMod3)
anova(Phys_RegMod3, Phys_RegMod4)
anova(Phys_RegMod4, Phys_RegMod5)
```
Output:
```
> anova(Phys_RegMod1)
Analysis of Variance Table

Response: physics
           Df Sum Sq Mean Sq F value    Pr(>F)    
gender      1   8101  8100.8  30.143 1.253e-07 ***
Residuals 193  51869   268.7                      
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
> anova(Phys_RegMod1, Phys_RegMod2)
Analysis of Variance Table

Model 1: physics ~ gender
Model 2: physics ~ gender + iq
  Res.Df   RSS Df Sum of Sq      F    Pr(>F)    
1    193 51869                                  
2    192 42930  1    8938.3 39.975 1.762e-09 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

> anova(Phys_RegMod2, Phys_RegMod3)
Analysis of Variance Table

Model 1: physics ~ gender + iq
Model 2: physics ~ gender + iq + ei
  Res.Df   RSS Df Sum of Sq      F    Pr(>F)    
1    192 42930                                  
2    191 39464  1    3466.7 16.778 6.201e-05 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

> anova(Phys_RegMod3, Phys_RegMod4)
Analysis of Variance Table

Model 1: physics ~ gender + iq + ei
Model 2: physics ~ gender + iq + ei + vsat
  Res.Df   RSS Df Sum of Sq      F    Pr(>F)    
1    191 39464                                  
2    190 34433  1    5030.6 27.759 3.706e-07 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

> anova(Phys_RegMod4, Phys_RegMod5)
Analysis of Variance Table

Model 1: physics ~ gender + iq + ei + vsat
Model 2: physics ~ gender + iq + ei + vsat + msat
  Res.Df   RSS Df Sum of Sq     F    Pr(>F)    
1    190 34433                                 
2    189 11707  1     22726 366.9 < 2.2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

```

# Data-driven regression
Notes:  <br/>
Data-driven regression uses statistical information, (changes in model fit) to determine if predictor variables should be added to the model, (forward regression) or eliminated from the model, (backward regression).
In forward selection regression, variables are added based on their ability  to improve model fit. **Variables that improve model fit the most are added at earlier steps.** 

In contrast, **backward elimination regression removes variables from the model based on reduction in model fit estimates with variables being removed if their removal does not significantly decrease the model fit.**  

We will focus on forward regression in this example. (method-“leapfForward”). The train function from the caret package provides one method to complete this type of analysis. To ensure that we only use the variables of interest, we can use the train function with the Physics 2 data frame. The final argument in this function call notes the maximum number of predictors we want to incorporate into the model: tuneGrid=data.frame(nvmax=1:5). The code indicates we want to consider the best models of size 1 to 5, although the function can potentially stop with fewer predictors if it determines fewer predictors are needed for the best fit model. Once we run the model, we can view the results. 

Code:
```
stepMod <- train(physics~., data=Physics2,  method="leapForward", tuneGrid=data.frame(nvmax=1:5))
stepMod$results
```
Output:
```
  nvmax      RMSE  Rsquared       MAE    RMSESD RsquaredSD     MAESD
1     1 13.149313 0.4586543 10.601984 0.8825284 0.07602055 0.7801972
2     2  8.806319 0.7531807  7.316227 0.4570971 0.04695153 0.4674057
3     3  8.089706 0.7910136  6.586665 0.5290556 0.04385017 0.4926305
4     4  8.007152 0.7952428  6.517152 0.5213524 0.04457078 0.4340065
5     5  7.998359 0.7956454  6.498476 0.5233443 0.04521706 0.4426511
```
Notes: The Rsquared values, provides a way to see how the model is improving as we add predictors. 

Code:
```
summary(stepMod$finalModel)
```
Output:
```
Subset selection object
5 Variables  (and intercept)
       Forced in Forced out
gender     FALSE      FALSE
iq         FALSE      FALSE
ei         FALSE      FALSE
vsat       FALSE      FALSE
msat       FALSE      FALSE
1 subsets of each size up to 4
Selection Algorithm: forward
         gender iq  ei  vsat msat
1  ( 1 ) " "    " " " " " "  "*" 
2  ( 1 ) " "    " " " " "*"  "*" 
3  ( 1 ) "*"    " " " " "*"  "*" 
4  ( 1 ) "*"    "*" " " "*"  "*" 
```

# Regression model coefficients
Note: <br/>
The unstandardized coefficients for a particular model can be obtained with the coef function. 

Code:
```
round(coef(stepMod$finalModel, 4), 3)
round(coef(stepMod$finalModel, 4), 4)
round(coef(stepMod$finalModel, 4), 5)
```
Output:
```
> round(coef(stepMod$finalModel, 4), 3)
(Intercept)      gender          iq        vsat        msat 
    -47.871      -7.732       0.145      -0.096       0.357 
> round(coef(stepMod$finalModel, 4), 4)
(Intercept)      gender          iq        vsat        msat 
   -47.8707     -7.7317      0.1446     -0.0964      0.3568 
> round(coef(stepMod$finalModel, 4), 5)
(Intercept)      gender          iq        vsat        msat 
  -47.87074    -7.73174     0.14456    -0.09638     0.35679 
```

# Evaluating Regression Models
### AIC
Note:  <br/>
AIC stands for Akaike’s Information Criteria, a metric developed by the Japanese Statistician, Hirotugu Akaike, 1970. The basic idea of AIC is to penalize the inclusion of additional variables to a model. It adds a penalty that increases the error when including additional terms. 
**The lower the AIC, the better the model.**

Code: AIC for the Standard Regression Model
```
AIC(Phys_RegMod)
```
Output:
```
[1] 1365.898
```

Code: AIC for the User determined Regression Model
```
AIC(Phys_RegMod2) #For 2 Predictor Variables.
AIC(Phys_RegMod5) #For 5 Predictor Variables.
```
Output:
```
> AIC(Phys_RegMod2)
[1] 1613.281

> AIC(Phys_RegMod5)
[1] 1365.898
```

Code: AIC for the User determined Regression Model
```
AIC(Phys_RegMod5)
```
Output:
```
[1] 1365.898
```

### BIC
Note:  <br/>
BIC (or Bayesian information criteria) is a variant of AIC with a stronger penalty for including additional variables to the model.

Code: BIC for Standard Regression model
```
BIC(Phys_RegMod) 
```
Output:
```
[1] 1388.809
```

Code: BIC for User determined Regression Model
```
BIC(Phys_RegMod2)
BIC(Phys_RegMod5)
```
Output:
```
> BIC(Phys_RegMod2)
[1] 1626.373

> BIC(Phys_RegMod5)
[1] 1388.809
```

# Getting all model evaluation matrix with one command

Code: 
```
library(broom)
library(magrittr) 
library(dplyr)

summary(Phys_RegMod)$adj.r.squared

glance(Phys_RegMod) %>%
  dplyr::select(adj.r.squared, sigma, AIC, BIC, p.value)
  
glance(Phys_RegMod2) %>%
  dplyr::select(adj.r.squared, sigma, AIC, BIC, p.value)

glance(Phys_RegMod5) %>%
  dplyr::select(adj.r.squared, sigma, AIC, BIC, p.value)
```
Output:
```
> summary(Phys_RegMod)$adj.r.squared
[1] 0.7996207

> glance(Phys_RegMod) %>%
+   dplyr::select(adj.r.squared, sigma, AIC, BIC, p.value)
# A tibble: 1 × 5
  adj.r.squared sigma   AIC   BIC  p.value
          <dbl> <dbl> <dbl> <dbl>    <dbl>
1         0.800  7.87 1366. 1389. 4.59e-65
> glance(Phys_RegMod2) %>%
+   dplyr::select(adj.r.squared, sigma, AIC, BIC, p.value)
# A tibble: 1 × 5
  adj.r.squared sigma   AIC   BIC  p.value
          <dbl> <dbl> <dbl> <dbl>    <dbl>
1         0.277  15.0 1613. 1626. 1.16e-14
> glance(Phys_RegMod5) %>%
+   dplyr::select(adj.r.squared, sigma, AIC, BIC, p.value)
# A tibble: 1 × 5
  adj.r.squared sigma   AIC   BIC  p.value
          <dbl> <dbl> <dbl> <dbl>    <dbl>
1         0.800  7.87 1366. 1389. 4.59e-65
```
