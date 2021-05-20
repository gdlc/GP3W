
#### Libraries and loading the data (included in the BGLR R-package)

```r
 library(BGLR)
 library(glmnet)

 data(mice)
```

#### Adjusting phenotypes by sex and other effects

```r
 fm=lm(Obesity.BodyLength~GENDER+CageDensity+Litter,data=mice.pheno)
 y=residuals(fm)
```

#### Centering the genotype matrix

```r
 n=nrow(mice.pheno)
 X=scale(mice.X,center=TRUE,scale=FALSE)
```

#### Creating a testing set

```r
 tst=sample(1:n,size=150)
 yNA=y
 yNA[tst]=NA
```

#### Fitting a Bayesian variable selection model

```r
 BAYES=BGLR(y=yNA,ETA=list(list(X=X,model='BayesC')),verbose=FALSE)
 yHatBayes=fm$yHat[tst]
 COR.BGLR[i]=cor(y[tst],yHatBayes)
```

#### Fitting a Lasso regression

```r
 LASSO=glmnet(y=y[-tst],x=X[-tst,],alpha=1,lambda=0.012)
 yHatLASSO=X[tst,]%*%as.numeric(LASSO$beta)
 cor(y[tst],yHatLASSO)
```
