# Masters Thesis R-codes
# Thesis Analysis: Does Corruption Deters FDI inflow in Low-Income Countries in Africa?
# This repository contains R code for analyzing panel data on **FDI, GDP, Inflation, Exchange Rates, Corruption, Political Stability, and Trade** using descriptive statistics, visualizations, and panel regression models (Pooled OLS, Random Effects, Fixed Effects). Specification tests (Hausman, LM, Chow) are also included.

## Preliminaries
getwd() 

# Load necessary packages
library(dplyr)
library(ggplot2)
library(scales) 
library(plm)
library(lmtest)
library(sandwich)
library(stargazer)
library(readr)

# Upload dataset
panel_data <- read_csv("panel_data.csv")
View(panel_data)

# Converting into panel data
final_dataset1 <- panel_data %>%
  mutate(Country = as.factor(Country),
         Year = as.factor(Year))

final_dataset1 <- final_dataset1[, c("Country", "Year", "GDP", "FDI", "Inflation", 
                                     "Exchange_rate", "Corruption_con", "Pol_sta", "Trade")]
View(final_dataset1)

# Summary checks
sum(!is.na(final_dataset1))
nrow(final_dataset1)
summary(final_dataset1$FDI)
ls()
length(unique(final_dataset1$Country))

# Summary
discr <- summary(final_dataset1[, c("FDI", "GDP", "Inflation", "Exchange_rate", 
                                    "Corruption_con", "Trade", "Pol_sta")])
View(discr)

# Descriptive statistics
library(psych)
vars <- c("FDI", "GDP", "Inflation", "Exchange_rate", "Corruption_con", "Trade", "Pol_sta")
summary_sta = describe(final_dataset1[, vars])

library(stargazer)
stargazer(summary_sta, type = "latex", summary = FALSE)

# Correlation matrix
cor_matrix1 <- cor(final_dataset1[, c("FDI", "GDP", "Inflation", "Exchange_rate", 
                                      "Corruption_con", "Pol_sta", "Trade")], 
                   use = "pairwise.complete.obs")
print(cor_matrix1)

library(ggcorrplot)
ggcorrplot(cor_matrix1, lab = TRUE, colors = c("red", "white", "blue"))

# Boxplot of FDI by country
ggplot(final_dataset1, aes(x = reorder(Country, FDI, median), y = FDI / 1e9, fill = Country)) +
  geom_boxplot(alpha = 0.6, outlier.size = 1.5, outlier.shape = 21) +  
  stat_summary(fun = mean, geom = "text", aes(label = round(..y.., 2)), vjust = -1, size = 4) +
  scale_y_continuous(labels = function(x) paste0(x, "B")) +
  labs(title = "FDI Boxplot by Country", x = "Country", y = "FDI (Billion $ Scale)") +
  theme_minimal()

# Boxplot of Corruption Control by country
ggplot(final_dataset1, aes(x = Country, y = Corruption_con, fill = Country)) +
  geom_boxplot(alpha = 0.6) +
  stat_summary(fun = mean, geom = "text", aes(label = round(..y.., 1)), vjust = -1, size = 4) +
  labs(title = "Boxplot of Corruption Perception Index by Country", x = "Country", y = "CPI Score") +
  theme_minimal()

# FDI mean across countries
ggplot(final_dataset1, aes(x = Country, y = FDI)) +
  stat_summary(fun = mean, geom = "point", size = 3) +
  stat_summary(fun.data = mean_cl_normal, geom = "errorbar", width = 0.2) +
  scale_y_continuous(labels = label_number(scale = 1e-9, suffix = "B", accuracy = 0.1),
                     breaks = seq(-6e9, 3e9, by = 1.5e9)) +
  labs(title = "Heterogeneity in FDI Across Countries",
       subtitle = "Mean FDI with 95% Confidence Intervals",
       x = "", y = "FDI (Billions USD)") +
  theme_minimal() +
  geom_hline(yintercept = 0, linetype = "dashed", color = "red")

# Histogram of FDI
ggplot(final_dataset1, aes(x = FDI)) + 
  geom_histogram(fill = "blue", color = "black", bins = 30) +
  labs(title = "Distribution of FDI", x = "FDI", y = "Frequency") +
  theme_minimal()

# Histogram of Corruption Control
ggplot(final_dataset1, aes(x = CPI_score)) + 
  geom_histogram(fill = "blue", color = "black", bins = 30) +
  labs(title = "Distribution of FDI", x = "FDI", y = "Frequency") +
  theme_minimal()

# FDI trends by country
ggplot(final_dataset1, aes(x = Year, y = FDI, group = Country, color = Country)) +
  geom_line(linewidth = 1) +
  scale_y_continuous(labels = scales::dollar, breaks = scales::pretty_breaks()) +
  scale_x_discrete() +
  labs(title = "FDI Trends by Country", x = "Year", y = "FDI Inflows")

# Scatter plot: FDI vs Corruption Control
ggplot(final_dataset1, aes(x = Corruption_con, y = FDI / 1e9, color = Country)) +
  geom_point(alpha = 0.7, size = 3) +
  scale_y_continuous(labels = function(x) paste0(x, "B")) +
  labs(title = "Scatter Plot of FDI vs Corruption Perception Index", 
       x = "Corruption Control Index", y = "FDI (Billion Scale)") +
  theme_minimal()

cor(final_dataset1$FDI, final_dataset1$Corruption_con, use = "complete.obs", method = "pearson")

# Ensure Year numeric
final_dataset2 <- final_dataset1
final_dataset2$Year <- as.numeric(as.character(final_dataset2$Year))

sapply(final_dataset1, function(x) length(unique(x)))

# OLS model
l_model <- lm(log(FDI) ~ log(GDP) + Inflation + Exchange_rate + Corruption_con +  
              Pol_sta + Trade + factor(Year), data = final_dataset1)
summary(l_model)

resettest(l_model, power=2:3, type="fitted")

# Pooled OLS
pooled_model <- plm(log(FDI+1) ~ log(GDP) + Inflation + Exchange_rate + Corruption_con + 
                    Pol_sta + Trade + factor(Year), 
                    data = final_dataset1, index = c("Country", "Year"), model = "pooling")
summary(pooled_model)
stargazer(pooled_model, type = "latex", summary = FALSE)

# Random Effects
random_model <- plm(log(FDI+1) ~ log(GDP) + Inflation + Exchange_rate + Corruption_con + 
                    Pol_sta + Trade + factor(Year),
                    data = final_dataset1, index = c("Country", "Year"), 
                    model = "random", random.method = "walhus")
summary(random_model)
stargazer(random_model, type = "latex", summary = FALSE)

# Fixed Effects
fixed_model <- plm(log(FDI+1) ~ log(GDP) + Inflation + Exchange_rate + Corruption_con + 
                   Pol_sta + Trade + factor(Year),
                   data = final_dataset1, index = c("Country", "Year"), model = "within")
summary(fixed_model)
stargazer(fixed_model, type = "latex", summary = FALSE)

# Specification tests
bp_test <- plmtest(pooled_model, type = "bp")
print(bp_test)

chow_test <- pFtest(fixed_model, pooled_model)
print(chow_test)

hausman_test <- phtest(fixed_model, random_model)
print(hausman_test)

library(car)
vif(random_model)

plot(random_model$residuals, ylab = "Residuals")
qqnorm(random_model$residuals)

# Interaction model
l_interact <- plm(log(FDI) ~ log(GDP) + Inflation + Exchange_rate + Corruption_con +  
                   Pol_sta + Trade + factor(Year) + Corruption_con * Pol_sta, 
                 data = final_dataset1, index = c("Country", "Year"), model = "random")
summary(l_interact)

# Quadratic model
l_nonlin <- plm(log(FDI) ~ log(GDP) + Inflation + Exchange_rate + Corruption_con +  
                 Pol_sta + Trade + factor(Year) + I(Corruption_con^2), 
               data = final_dataset1, index = c("Country", "Year"), model = "random")
summary(l_nonlin)
