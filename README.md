# Final-Project
---
title: "Predicting Exercise Manner Using Accelerometer Data"
author: "Your Name"
date: "`r Sys.Date()`"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Introduction
Wearable devices can collect movement data through accelerometers, allowing analysis of how exercises are performed. This project predicts the manner in which an exercise is executed using sensor data.

## Data Processing
```{r data-processing}
library(dplyr)
library(ggplot2)
library(caret)

# Load data
train_data <- read.csv("pml-training.csv")
test_data <- read.csv("pml-testing.csv")

# Remove columns with too many missing values
train_data <- train_data[, colSums(is.na(train_data)) == 0]
test_data <- test_data[, colSums(is.na(test_data)) == 0]

# Remove non-informative columns
train_data <- train_data %>% select(-c(X, user_name, cvtd_timestamp, raw_timestamp_part_1, raw_timestamp_part_2, new_window, num_window))
test_data <- test_data %>% select(-c(X, user_name, cvtd_timestamp, raw_timestamp_part_1, raw_timestamp_part_2, new_window, num_window))
```

## Model Training
```{r model-training}
set.seed(42)
trainIndex <- createDataPartition(train_data$classe, p = 0.8, list = FALSE)
train_set <- train_data[trainIndex, ]
valid_set <- train_data[-trainIndex, ]

# Train Random Forest Model
rf_model <- train(classe ~ ., data = train_set, method = "rf", trControl = trainControl(method = "cv", number = 5))

# Validate Model
predictions <- predict(rf_model, valid_set)
accuracy <- confusionMatrix(predictions, valid_set$classe)
accuracy
```

## Results
```{r results}
# Predict on test set
test_predictions <- predict(rf_model, test_data)
write.csv(data.frame(problem_id = test_data$problem_id, classe = test_predictions), "predictions.csv", row.names = FALSE)
```

## Conclusion
This model achieves high accuracy using Random Forest, demonstrating its effectiveness in classifying exercise performance based on accelerometer data.
