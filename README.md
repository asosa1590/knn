# knn

(20 points) Create a k-nn model to try to predict how a person commutes to work. (Worry a bit about the zero values of TRANWORK since that typically means they’re not working.) How useful is this model at predicting? What are some of the important variables in this prediction?

```

>load("/cloud/project/acs2017_ny_data.RData")
> attach(acs2017_ny)
> detach(acs2017_ny
+ )
> dat_NYC <-subset(acs2017_ny, (acs2017_ny$in_NYC == 1)&(acs2017_ny$AGE > 20) & (acs2017_ny$AGE < 66))
> attach(dat_NYC)
> View(acs2017_ny)
> Travel <- TRANWORK

# I defined TRANWORK as a Travel

> norm_varb <- function(X_in) {
+     (X_in - min(X_in, na.rm = TRUE))/( max(X_in, na.rm = TRUE) - min(X_in, na.rm = TRUE) )
+ }
> is.na(TRANWORK) <- which(TRANWORK=0)
Error in which(TRANWORK = 0) : unused argument (TRANWORK = 0)
> is.na(TRANTIME) <- which(TRANWORK == 0)
> is.na(Travel) <- which(Travel == 0)
> norm_varb(UHRSWORK)

> data_use_prelim <- data.frame(norm_varb(UHRSWORK),FTOTINC)

## I compared TRANWORK ( method of transportation) to hours worked (UHRSWORK) and family total income work hours  (FTOTINC)

> good_obs_data_use <- complete.cases(data_use_prelim,Travel)
> dat_use <- subset(data_use_prelim,good_obs_data_use)

> y_use <- subset(Travel,good_obs_data_use)
> set.seed(12345)
> NN_obs <- sum(good_obs_data_use == 1)
> select1 <- (runif(NN_obs) < 0.8)
> train_data <- subset(dat_use,select1)
> test_data <- subset(dat_use,(!select1))
> cl_data <- y_use[select1]
> true_data <- y_use[!select1]


> summary(cl_data)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  10.00   10.00   33.00   29.23   33.00   70.00 
> prop.table(summary(cl_data))
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
0.05399 0.05399 0.17816 0.15780 0.17816 0.37791 
> summary(train_data)
 norm_varb.UHRSWORK.    FTOTINC       
 Min.   :0.0101      Min.   :  -5900  
 1st Qu.:0.3535      1st Qu.:  45000  
 Median :0.4040      Median :  83000  
 Mean   :0.4037      Mean   : 118891  
 3rd Qu.:0.4545      3rd Qu.: 145000  
 Max.   :1.0000      Max.   :2030000  
 
 > for (indx in seq(1, 9, by= 2)) {
+     pred_borough <- knn(train_data, test_data, cl_data, k = indx, l = 0, prob = FALSE, use.all = TRUE)
+     num_correct_labels <- sum(pred_borough == true_data)
+     correct_rate <- num_correct_labels/length(true_data)
+     print(c(indx,correct_rate))
+ }
[1] 1.0000000 0.3936877
[1] 3.0000000 0.3908638
[1] 5.0000000 0.4157807
[1] 7.0000000 0.4169435
[1] 9.0000000 0.4151163
