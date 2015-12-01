## Change features to all unique, and use as variable names
featuresU <- make.names(features$V2, unique = TRUE, allow_ = TRUE)
names(X_test)[1:561] <- paste(featuresU)
names(X_train)[1:561] <- paste(featuresU)

## Combine Test and Train
Combine <- union(X_test, X_train)
ys <- bind_rows(y_test, y_train)
subs <- bind_rows(subject_test, subject_train)

## Extract mean and std columns
Avgs <- select(Combine, contains("mean"))
Devs <- select(Combine, contains("std"))
Combine2 <- bind_cols(Avgs, Devs)

## Add the subject and activity variables
Combine <- bind_cols(Combine, ys, subs)
Combine2 <- bind_cols(Combine2, ys, subs)
names(Combine)[562] <- paste("ActivityLabel")
names(Combine2)[87] <- paste("ActivityLabel")
names(Combine)[563] <- paste("ActivityName")
names(Combine2)[88] <- paste("ActivityName")
names(Combine)[564] <- paste("Subject")
names(Combine2)[89] <- paste("Subject")

## Join the labels to the activity numbers
Combine <- left_join(Combine, activity_labels, "V1")
Combine2 <- left_join(Combine2, activity_labels, "V1")

## Group the data by subject and activty, then take average
Combine3 <-Combine2 %>%
  group_by(Subject, ActivityName) %>% 
  summarise_each(funs(mean))
