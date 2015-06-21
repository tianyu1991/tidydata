# tidydata
library(dplyr)
library(reshape2)

#read original data
tsubject<-read.table("test/subject_test.txt")
tx<-read.table("test/X_test.txt")
ty<-read.table("test/y_test.txt")
trsubject<-read.table("train/subject_train.txt")
trx<-read.table("train/X_train.txt")
try<-read.table("train/y_train.txt")
fig<-read.table("features.txt")
alabel<-read.table("activity_labels.txt")
da<-rbind(tx,trx)

#change the data type of features
figs<-rep("0",561)
for(i in 1:561){
	figs[i]<-toString(fig[i,2])}

#change colnames
colnames(da)<-c(figs[1:561])

#Extracts only the measurements on the mean and standard deviation for each measurement. 
nemean<-da[,grep("mean()", colnames(da)) ]
nestd<-da[,grep("std()", colnames(da)), ]
da1<-cbind(nemean,nestd)
da2<-da1[,-grep("meanFreq()", colnames(da1))]

y<-rbind(ty,try)
subject<-rbind(tsubject,trsubject)
colnames(subject)<-("subject")

#Appropriately labels the data set with descriptive variable names.
da3<-cbind(subject,y,da2)
da4=merge(da3,alabel,by="V1",sort=FALSE)
da4$V1<-da4$V2
da4<-da4[,1:68]
da4<-rename(da4,activity = V1)

#creates a second, independent tidy data set 
da4$index = paste(da4$subject, da4$activity, sep = "_" )
char = as.character(names(da4[, 3:68]))
melt_data = melt(da4, id="index", measure.vars=char)
tidy_data = dcast(melt_data, index ~ variable, mean) 

save(tidy_data,file="tidy_data.txt")
