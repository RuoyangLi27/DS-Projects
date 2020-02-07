```

# run lasso
#install.packages("glmnet")
library(glmnet)
Mx<- model.matrix(Obama_margin_percent ~ .^2, data=election_data_train)[,-1]
My<- election_data_train$Obama_margin_percent
lasso <- glmnet(Mx,My)
lassoCV <- cv.glmnet(Mx,My)

num.features <- ncol(Mx)
num.n <- nrow(Mx)
num.churn <- sum(My)
w <- (num.churn/num.n)*(1-(num.churn/num.n))

features.min <- support(lasso$beta[,which.min(lassoCV$cvm)])
length(features.min)
features.1se <- support(lasso$beta[,which.min( (lassoCV$lambda-lassoCV$lambda.1se)^2)])

data.min <- data.frame(Mx[,features.min],My)
data.1se <- data.frame(Mx[,features.1se],My)

n <- nrow(election_data_train)
nfold <- 10
OOS <- data.frame(m.null=rep(NA,nfold), m.lr=rep(NA,nfold), L.min=rep(NA,nfold), L.1se=rep(NA,nfold), 
                  PL.min=rep(NA,nfold), PL.1se=rep(NA,nfold))
foldid <- rep(1:nfold,each=ceiling(n/nfold))[sample(1:n)]

### k-fold cross validation
for(k in 1:nfold){ 
  train <- which(foldid!=k) # train on all but fold `k'
  
  ### Null model
  m.null <-glm(Obama_margin_percent~0, data=election_data_train, subset=train)
  pred.null <- predict(m.null, newdata=election_data_train[-train,], type="response")
  OOS$m.null[k] <- R2(y=election_data_train$Obama_margin_percent[-train], pred=pred.null)
  
  ### Linear regression
  m.lr <-lm(Obama_margin_percent ~ ., data=election_data_train, subset=train)
  pred.lr <- predict(m.lr, newdata=election_data_train[-train,], type="response")
  OOS$m.lr[k] <- R2(y=election_data_train$Obama_margin_percent[-train], pred=pred.lr)
  
  ### lasso and post-lasso prediction
  rmin <- glm(My~., data=data.min, subset=train)
  if ( length(features.1se) == 0){  r1se <- glm(Obama_margin_percent~1, data=election_data_train, subset=train) 
  } else {r1se <- glm(My~., data=data.1se, subset=train)
  }
  
  ### lasso estimate
  lassomin  <- glmnet(Mx[train,],My[train], lambda = lassoCV$lambda.min)
  lasso1se  <- glmnet(Mx[train,],My[train], lambda = lassoCV$lambda.1se)
  
  predlassomin <- predict(lassomin, newx=Mx[-train,], type="response")
  predlasso1se  <- predict(lasso1se, newx=Mx[-train,], type="response")
  
  OOS$L.min[k] <- R2(y=My[-train], pred=predlassomin)
  OOS$L.1se[k] <- R2(y=My[-train], pred=predlasso1se)
  
  ### post-lasso estimate
  predmin <- predict(rmin, newdata=data.min[-train,], type="response")
  pred1se  <- predict(r1se, newdata=data.1se[-train,], type="response")
  
  OOS$PL.min[k] <- R2(y=My[-train], pred=predmin)
  OOS$PL.1se[k] <- R2(y=My[-train], pred=pred1se)
  
  print(paste("Iteration",k,"of",nfold,"completed"))
  
}
R2performance <- colMeans(OOS)
m.OOS <- as.matrix(OOS)
rownames(m.OOS) <- c(1:nfold)
barplot(t(as.matrix(OOS)), beside=TRUE, legend=TRUE, args.legend=c(xjust=0.01, yjust=1.5),
        ylab= bquote( "Out of Sample " ~ R^2), xlab="Fold", names.arg = c(1:10))

newMx<-model.matrix( ~ .^2, data=election_data_test)[,-1]
predlassomin<-predict(rmin,newdata=data.frame(newMx),type="response") 

#******************

par(mar=c(4, 4, 1, 1))
election_data_train$Obama_margin <- election_data_train$Obama - election_data_train$Clinton
election_data_train$Obama_margin_percent <- 100*election_data_train$Obama_margin/election_data_train$TotalVote
election_data_train$Obama_wins <- ifelse(election_data$Obama_margin >0, 1,0)
names(election_data_train)
sample=election_data_train[,c(21,25)]
sample


library(mclust)
m_clust <- Mclust(as.matrix(sample), G=1:20) #èç±»æ°ç®ä»1ä¸ç´è¯å°20
summary(m_clust)
plot(m_clust, "BIC")


sample
plot(sample,col=3-election_data_train$Obama_wins)
sample_kmeans=kmeans(sample,4 , nstart=10)
colorcluster=1+sample_kmeans$cluster
sample_kmeans$cluster
plot(sample, col=colorcluster)
points(sample_kmeans$centers, col = 1, pch = 24, cex = 1.5, lwd=1, bg = 2:5)
sample
apply(sample,2,sd)
apply(sample,2,mean)
Ssample=scale(sample)
apply(Ssample,2,sd)
apply(Ssample,2,mean)
names(sample)

Ssample_kmeans=kmeans(Ssample,4 , nstart=10)
colorcluster=1+Ssample_kmeans$cluster
plot(Ssample, col=colorcluster, shape = 1)
points(Ssample_kmeans$centers, col = 12,ph = 24, cex = 1.5, lwd=1, bg = 2:5)
Ssample_kmeans$centers
Ssample_kmeans$size
names(election_data)
radius=election_data[,15]
plot(sample, col=colorcluster,ylab='Per capita income in 2005',xlab="Bachelor's degree or higher(percentage)")
summary(sample_kmeans)
sample_kmeans$size
sample_kmeans$centers

mean(sample$AverageIncome[sample_kmeans$clust==1])
mean(election_data_train$Obama_wins[sample_kmeans$clust==1])
mean(election_data_train$Obama_wins[sample_kmeans$clust==2])
mean(election_data_train$Obama_wins[sample_kmeans$clust==3])
mean(election_data_train$Obama_wins[sample_kmeans$clust==4])

mean(election_data_train$Black[sample_kmeans$cluster==1])
mean(election_data_train$Black[sample_kmeans$cluster==2])
mean(election_data_train$Black[sample_kmeans$cluster==3])
mean(election_data_train$Black[sample_kmeans$cluster==4])

sample$Obama

names(election_data_train)

#*******

###
#### Model with 1771 controls to measure the impact of 10% larger Hispanic demographic
y <- election_data_train$Obama_margin_percent
x <- model.matrix( Obama_margin_percent ~ .-Hispanic-Obama_wins-Obama_margin-FIPS-ElectionDate-TotalVote-Clinton-Obama, data = election_data_train )
d <- election_data_train$Hispanic
summary(orig <- glm(y ~ d+x, data=election_data_train) )$coef['d',]

####
#### Feel free to compare/contrast your results with the following simple regression model
#### 
HispanicSimple <- glm(Obama_margin_percent ~ Hispanic, data = election_data_train )
summary(HispanicSimple)
summary(simple1 <- glm(y ~ d, data=election_data_train) )$coef['d',]
####

####
#### Model with 1771 controls to measure the impact of 10% larger Black demographic
y <- election_data_train$Obama_margin_percent
x <- model.matrix( Obama_margin_percent ~ .-Black-Obama_wins-Obama_margin-FIPS-ElectionDate-TotalVote-Clinton-Obama, data = election_data_train )
d <- election_data_train$Black
summary(orig <- glm(y ~ d+x, data=election_data_train) )$coef['d',]
####
#### Feel free to compare/contrast your results with the following simple regression model
#### 
BlackSimple <- glm( Obama_margin_percent ~ Black, data = election_data_train )
summary(BlackSimple)
summary(simple2 <- glm(y ~ d, data=election_data_train) )$coef['d',]
####

### Double Selection
#step1
library(glmnet)
num.features <- ncol(x)
num.n <- nrow(x)
w <-sd(y)
lambda.theory <- 2*w*sqrt(log(num.features/0.01)/num.n)
lassoTheory <- glmnet(x,y,lambda = lambda.theory)
supp1 <- support(lassoTheory$beta)
length(supp1)
colnames(x[,supp1])
#step2
w <-sd(d)
lambda.theory <- 2*w*sqrt(log(num.features/0.05)/num.n)
lassoTheory <- glmnet(x,d,lambda = lambda.theory)
supp2<-support(lassoTheory$beta)
length(supp2)
colnames(x[,supp2])
#step3
inthemodel <- unique(c(supp1,supp2)) # unique grabs union
selectdata <- cBind(d,x[,inthemodel]) 
selectdata <- as.data.frame(as.matrix(selectdata)) # make it a data.frame
dim(selectdata) ## p about half n
#run a a linear regression of Y on d and all selected
causal_glm <- glm(y~., data=selectdata)
summary(causal_glm)$coef["d",]

```
