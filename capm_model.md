---
author: Jeff Chen
date: 2024 T2
output:
  html_document:
    df_print: paged
  pdf_document: default
title: ACTL1101 Assignment Part B
---

`{r setup, include=FALSE} knitr::opts_chunk$set(echo = TRUE) library(quantmod) library(ggplot2) library(tidyverse)`

# CAPM Analysis

## Introduction

In this assignment, you will explore the foundational concepts of the
Capital Asset Pricing Model (CAPM) using historical data for AMD and the
S&P 500 index. This exercise is designed to provide a hands-on approach
to understanding how these models are used in financial analysis to
assess investment risks and returns.

## Background

The CAPM provides a framework to understand the relationship between
systematic risk and expected return, especially for stocks. This model
is critical for determining the theoretically appropriate required rate
of return of an asset, assisting in decisions about adding assets to a
diversified portfolio.

## Objectives

1.  **Load and Prepare Data:** Import and prepare historical price data
    for AMD and the S&P 500 to ensure it is ready for detailed analysis.
2.  **CAPM Implementation:** Focus will be placed on applying the CAPM
    to examine the relationship between AMD's stock performance and the
    overall market as represented by the S&P 500.
3.  **Beta Estimation and Analysis:** Calculate the beta of AMD, which
    measures its volatility relative to the market, providing insights
    into its systematic risk.
4.  **Results Interpretation:** Analyze the outcomes of the CAPM
    application, discussing the implications of AMD's beta in terms of
    investment risk and potential returns.

## Instructions

### Step 1: Data Loading

-   We are using the `quantmod` package to directly load financial data
    from Yahoo Finance without the need to manually download and read
    from a CSV file.
-   `quantmod` stands for "Quantitative Financial Modelling Framework".
    It was developed to aid the quantitative trader in the development,
    testing, and deployment of statistically based trading models.
-   Make sure to install the `quantmod` package by running
    `install.packages("quantmod")` in the R console before proceeding.

\`\`\`{r load-data} \# Set start and end dates start_date \<-
as.Date("2019-05-20") end_date \<- as.Date("2024-05-20")

# Load data for AMD, S&P 500, and the 1-month T-Bill (DTB4WK)

amd_data \<- getSymbols("AMD", src = "yahoo", from = start_date, to =
end_date, auto.assign = FALSE) gspc_data \<- getSymbols("\^GSPC", src =
"yahoo", from = start_date, to = end_date, auto.assign = FALSE) rf_data
\<- getSymbols("DTB4WK", src = "FRED", from = start_date, to = end_date,
auto.assign = FALSE)

# Convert Adjusted Closing Prices and DTB4WK to data frames

amd_df \<- data.frame(Date = index(amd_data), AMD =
as.numeric(Cl(amd_data))) gspc_df \<- data.frame(Date =
index(gspc_data), GSPC = as.numeric(Cl(gspc_data))) rf_df \<-
data.frame(Date = index(rf_data), RF = as.numeric(rf_data\[,1\])) \#
Accessing the first column of rf_data

# Merge the AMD, GSPC, and RF data frames on the Date column

df \<- merge(amd_df, gspc_df, by = "Date") df \<- merge(df, rf_df, by =
"Date")


    #### Data Processing 
    ```{r data}
    colSums(is.na(df))
    # Fill N/A RF data
    df <- df %>%
      fill(RF, .direction = "down") 

### Step 2: CAPM Analysis

The Capital Asset Pricing Model (CAPM) is a financial model that
describes the relationship between systematic risk and expected return
for assets, particularly stocks. It is widely used to determine a
theoretically appropriate required rate of return of an asset, to make
decisions about adding assets to a well-diversified portfolio.

#### The CAPM Formula

The formula for CAPM is given by:

\[ E(R_i) = R_f + `\beta`{=tex}\_i (E(R_m) - R_f) \]

Where:

-   ( E(R_i) ) is the expected return on the capital asset,
-   ( R_f ) is the risk-free rate,
-   ( `\beta`{=tex}\_i ) is the beta of the security, which represents
    the systematic risk of the security,
-   ( E(R_m) ) is the expected return of the market.

#### CAPM Model Daily Estimation

-   **Calculate Returns**: First, we calculate the daily returns for AMD
    and the S&P 500 from their adjusted closing prices. This should be
    done by dividing the difference in prices between two consecutive
    days by the price at the beginning of the period. $$
    \text{Daily Return} = \frac{\text{Today's Price} - \text{Previous Trading Day's Price}}{\text{Previous Trading Day's Price}}
    $$

\`\`\`{r return}

df$dr_amd <- NA df$dr_snp \<- NA

for(i in 1:nrow(df)){ if(i!=1){\
df$dr_amd[i] = (df$AMD\[i\]-df$AMD[i-1])/df$AMD\[i-1\]
df$dr_snp[i] = (df$GSPC\[i\]-df$GSPC[i-1])/df$GSPC\[i-1\] } }
df$dr_amd = as.numeric(df$dr_amd) df$dr_snp = as.numeric(df$dr_snp)


    - **Calculate Risk-Free Rate**: Calculate the daily risk-free rate by conversion of annual risk-free Rate. This conversion accounts for the compounding effect over the days of the year and is calculated using the formula:
    $$
    \text{Daily Risk-Free Rate} = \left(1 + \frac{\text{Annual Rate}}{100}\right)^{\frac{1}{360}} - 1
    $$

    ```{r riskfree}
    for(i in 1:nrow(df)){
      df$daily_rf[i] = (1+df$RF[i]/100)^(1/360) - 1
    }

-   **Calculate Excess Returns**: Compute the excess returns for AMD and
    the S&P 500 by subtracting the daily risk-free rate from their
    respective returns.

\`\`\`{r excess return} df$er_amd <- NA df$er_snp \<- NA

for(i in 1:nrow(df)){ df$er_amd[i] = df$dr_amd\[i\] -
df$daily_rf[i]  df$er_snp\[i\] = df$dr_snp[i] - df$daily_rf\[i\] }



    - **Perform Regression Analysis**: Using linear regression, we estimate the beta (\(\beta\)) of AMD relative to the S&P 500. Here, the dependent variable is the excess return of AMD, and the independent variable is the excess return of the S&P 500. Beta measures the sensitivity of the stock's returns to fluctuations in the market.

    ```{r lm}
    capm_lr <- lm(df$er_amd ~ df$er_snp)
    summary(capm_lr)
    beta_lr = coef(capm_lr)[2]
    print(beta_lr)

#### Interpretation

What is your (`\beta`{=tex})? Is AMD more volatile or less volatile than
the market?

\*\*Answer: The beta value we found above is 1.569999, implying that AMD
is more volatile than the S&P index.

From our CAPM analysis, we get that:

E\[AMD Return\] = RF + beta\*(E\[S&P\] - RF).

This means that movements in the market will be amplified by a factor of
1.569999 when considering movements in AMD stock, which implies a higher
level of volatility and greater price swings across the market cycle.

The greater volatility of AMD stock may signal to investors to balance
their portfolio with less volatile stocks.\*\*

#### Plotting the CAPM Line

Plot the scatter plot of AMD vs. S&P 500 excess returns and add the CAPM
regression line.

\`\`\`{r plot} ggplot(data.frame(SNP500_ER =
df$er_snp, AMD_ER = df$er_amd), aes(x = SNP500_ER, y = AMD_ER)) +
geom_point(color = "black", shape = 4) + geom_smooth(method = "lm", col
= "magenta") + labs(title = "CAPM Anslysis of AMD vs S&P500 Returns", x
= "S&P500 Excess Returns", y = "AMD Excess Returns") + theme_minimal()


    ### Step 3: Predictions Interval
    Suppose the current risk-free rate is 5.0%, and the annual expected return for the S&P 500 is 13.3%. Determine a 90% prediction interval for AMD's annual expected return.

    *Hint: Calculate the daily standard error of the forecast ($s_f$), and assume that the annual standard error for prediction is $s_f \times \sqrt{252}$. Use the simple return average method to convert daily stock returns to annual returns if needed.*


    **Answer:**

    ```{r pi}
    RFRate = 0.05
    SNPExpectedR = 0.133
    AMDExpectedR = RFRate + beta_lr*(SNPExpectedR - RFRate)

    s_e = sqrt(sum(residuals(capm_lr)^2)/(length(df$dr_snp)-2))

    SST = sum((na.omit(df$dr_snp)-mean(na.omit(df$dr_snp))^2))
    s_f = s_e*sqrt(1+1/length(na.omit(df$dr_snp))+((SNPExpectedR - RFRate - mean(na.omit(df$dr_snp)))^2)/SST)

    t_value = qt((1+0.9)/2, df = length(df$dr_snp)-2)
    lower_bound = AMDExpectedR - t_value*s_f*sqrt(252)
    upper_bound = AMDExpectedR + t_value*s_f*sqrt(252)

    cat("The Prediction Interval is [", round(lower_bound, 5), ",", round(upper_bound, 5), "] \n")
