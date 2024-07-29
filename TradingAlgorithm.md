---
author: Jeff Chen
date: 2024 T2
output:
  html_document:
    df_print: paged
  pdf_document: default
  word_document: default
title: ACTL1101 Assignment Part A
---

`{r setup, include=FALSE} knitr::opts_chunk$set(echo = TRUE)`

## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by
incorporating financial metrics to evaluate its profitability. This
exercise simulates a real-world scenario where you, as part of a
financial technology team, need to present an improved version of a
trading algorithm that not only executes trades but also calculates and
reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have
been tasked by the Trading Strategies Team to modify your trading
algorithm. This modification should include tracking the costs and
proceeds of trades to facilitate a deeper evaluation of the algorithm's
profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to
include costs, proceeds, and return on investments metrics to assess the
profitability of your trading algorithm.

## Objectives

1.  **Load and Prepare Data:** Open and run the starter code to create a
    DataFrame with stock closing data.

2.  **Implement Trading Algorithm:** Create a simple trading algorithm
    based on daily price changes.

3.  **Customize Trading Period:** Choose your entry and exit dates.

4.  **Report Financial Performance:** Analyze and report the total
    profit or loss (P/L) and the ROI of the trading strategy.

5.  **Implement a Trading Strategy:** Implement a trading strategy and
    analyze the total updated P/L and ROI.

6.  **Discussion:** Summarise your finding.

## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section
to generate a DataFrame containing AMD stock closing data. This will
serve as the basis for your trading decisions. First, create a data
frame named `amd_df` with the given closing prices and corresponding
dates.

\`\`\`{r load-data}

# Load data from CSV file

amd_df \<- read.csv("AMD.csv")

# Convert the date column to Date type and Adjusted Close as numeric

amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)

amd_df \<- amd_df\[, c("date", "close")\]


    ##Plotting the Data Plot the closing prices over time to visualize the price movement.

    ```{r plot}
    plot(amd_df$date, amd_df$close,'l')

## Step 2: Trading Algorithm

Implement the trading algorithm as per the instructions. You should
initialize necessary variables, and loop through the dataframe to
execute trades based on the set conditions.

-   Initialize Columns: Start by ensuring dataframe has columns
    'trade_type', 'costs_proceeds' and 'accumulated_shares'.
-   Change the algorithm by modifying the loop to include the cost and
    proceeds metrics for buys of 100 shares. Make sure that the
    algorithm checks the following conditions and executes the strategy
    for each one:
    -   If the previous price = 0, set 'trade_type' to 'buy', and set
        the 'costs_proceeds' column to the current share price
        multiplied by a `share_size` value of 100. Make sure to take the
        negative value of the expression so that the cost reflects money
        leaving an account. Finally, make sure to add the bought shares
        to an `accumulated_shares` variable.
    -   Otherwise, if the price of the current day is less than that of
        the previous day, set the 'trade_type' to 'buy'. Set the
        'costs_proceeds' to the current share price multiplied by a
        `share_size` value of 100.
    -   You will not modify the algorithm for instances where the
        current day's price is greater than the previous day's price or
        when it is equal to the previous day's price.
    -   If this is the last day of trading, set the 'trade_type' to
        'sell'. In this case, also set the 'costs_proceeds' column to
        the total number in the `accumulated_shares` variable multiplied
        by the price of the last day.

\`\`\`{r trading}

#For this algorithm, we will initialise the columns in the code

for (i in 1:nrow(amd_df)) { if (i==1) {
amd_df$accumulated_shares[1] <- 100 #For the initial date, we will buy shares  amd_df$trade_type\[1\]
\<- "Buy" amd_df$costs_proceeds[1] <- -100 * amd_df$close\[1\]

} else if (i == nrow(amd_df)) {
amd_df$costs_proceeds[nrow(amd_df)] <- amd_df$close\[nrow(amd_df)\] \*
amd_df$accumulated_shares[nrow(amd_df)-1]  amd_df$accumulated_shares\[nrow(amd_df)\]
\<- 0 amd_df\$trade_type\[nrow(amd_df)\] \<- "Sell" #For the final date,
we will sell all the shares we own

} else if (amd_df$close[i] >= amd_df$close\[i-1\]){ #if the price of the
current day is higher than the previous day, we will hold our stock
amd_df$accumulated_shares[i] <- amd_df$accumulated_shares\[i-1\]
amd_df$costs_proceeds[i] <- 0  amd_df$trade_type\[i\] \<- "Hold"

} else if (amd_df$close[i] < amd_df$close\[i-1\]) { #if the price of the
current day is lower than the previous day, we will buy stock
amd_df$accumulated_shares[i] <- amd_df$accumulated_shares\[i-1\] + 100
amd_df$costs_proceeds[i] <- -100 * amd_df$close\[i\]
amd_df\$trade_type\[i\] \<- "Buy" } }


    ## Step 3: Customize Trading Period

    -   Define a trading period you wanted in the past five years

    ```{r period}
    starting_date <- as.Date("2022-01-02")
    end_date <- as.Date("2024-01-02")

    # Load data from CSV file
    amd_df_modstart <- read.csv("AMD.csv")

    # Convert the date column to Date type and Adjusted Close as numeric
    amd_df_modstart$date <- as.Date(amd_df_modstart$Date)
    amd_df_modstart$close <- as.numeric(amd_df_modstart$Adj.Close)

    amd_df_modstart <- amd_df_modstart[, c("date", "close")]

    amd_df_modstart <- amd_df_modstart[amd_df_modstart$date >= starting_date & amd_df_modstart$date <= end_date,] 
    #we are choosing the rows of the data which lie between our chosen start and end dates

## Step 4: Run Your Algorithm and Analyze Results

After running your algorithm, check if the trades were executed as
expected. Calculate the total profit or loss and ROI from the trades.

-   Total Profit/Loss Calculation: Calculate the total profit or loss
    from your trades. This should be the sum of all entries in the
    'costs_proceeds' column of your dataframe. This column records the
    financial impact of each trade, reflecting money spent on buys as
    negative values and money gained from sells as positive values.
-   Invested Capital: Calculate the total capital invested. This is
    equal to the sum of the 'costs_proceeds' values for all 'buy'
    transactions. Since these entries are negative (representing money
    spent), you should take the negative sum of these values to reflect
    the total amount invested.
-   ROI Formula:
    $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

``` {r}

#columns are initialised in the code

for (i in 1:nrow(amd_df_modstart)) {
  if (i==1) {               
    amd_df_modstart$accumulated_shares[1] <- 100          #again, we are buying on the starting date
    amd_df_modstart$trade_type[1] <- "Buy"
    amd_df_modstart$costs_proceeds[1] <- -100 * amd_df$close[1]
    
 } else if (i == nrow(amd_df_modstart)) {
    amd_df_modstart$costs_proceeds[nrow(amd_df_modstart)] <- amd_df$close[nrow(amd_df_modstart)] * amd_df_modstart$accumulated_shares[nrow(amd_df_modstart)-1]
    amd_df_modstart$accumulated_shares[nrow(amd_df_modstart)] <- 0
    amd_df_modstart$trade_type[nrow(amd_df_modstart)] <- "Sell"    #again, we sell on the final date
    
 }
  else if (amd_df_modstart$close[i] >= amd_df_modstart$close[i-1]){        #if the price rises between 2 days, we choose to hold the stock
    amd_df_modstart$accumulated_shares[i] <- amd_df_modstart$accumulated_shares[i-1]
    amd_df_modstart$costs_proceeds[i] <- 0
    amd_df_modstart$trade_type[i] <- "Hold"
    
  }
  else if (amd_df_modstart$close[i] < amd_df_modstart$close[i-1]) {     #if the price rises falls, we choose to buy
    amd_df_modstart$accumulated_shares[i] <- amd_df_modstart$accumulated_shares[i-1] + 100
    amd_df_modstart$costs_proceeds[i] <- -100 * amd_df_modstart$close[i]
    amd_df_modstart$trade_type[i] <- "Buy"
  }
}

for (i in 1:nrow(amd_df_modstart)) {
  if (i == 1) {
    amd_df_modstart$total_profit_loss[1] <- amd_df_modstart$costs_proceeds[1]   #the initial total P/L is the initial cost of buying the first stock
  }
  else if (i <= nrow(amd_df_modstart)) {
    amd_df_modstart$total_profit_loss[i] <- amd_df_modstart$total_profit_loss[i-1] + amd_df_modstart$costs_proceeds[i] 
    #for each other day, the total P/L will be the sum previous day's P/L and the current day's costs or income
  }
}

for (i in 1:(nrow(amd_df_modstart)-1)) {
  amd_df_modstart$invested_capital[i] <- -amd_df_modstart$total_profit_loss[i]   
  #until we sell in the final day, all our invested capital will be equal to the loss we incur
  amd_df_modstart$invested_capital[nrow(amd_df_modstart)] <- amd_df_modstart$invested_capital[nrow(amd_df_modstart)-1]  
  #in the final day, the total invested capital will be the accumulation of the previous losses
}

for (i in 1:nrow(amd_df_modstart)) {
    amd_df_modstart$return_on_investment[i] <- (amd_df_modstart$total_profit_loss[i] / amd_df_modstart$invested_capital[i])*100
    #using the ROI formula to calculate the ROI for each day
}
```

## Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)

-   Option 1: Implement a profit-taking strategy that you sell half of
    your holdings if the price has increased by a certain percentage
    (e.g., 20%) from the average purchase price.
-   Option 2: Implement a stop-loss mechanism in the trading strategy
    that you sell half of your holdings if the stock falls by a certain
    percentage (e.g., 20%) from the average purchase price. You don't
    need to buy 100 stocks on the days that the stop-loss mechanism is
    triggered.

\`\`\`{r option} \# Option 2: Stop-loss mechanism triggered when prices
fall 5% from average purchase price #columns are initialised in the code

amd_df_stoploss \<- read.csv("AMD.csv")

amd_df_stoploss$date <- as.Date(amd_df_stoploss$Date)
amd_df_stoploss$close <- as.numeric(amd_df_stoploss$Adj.Close)
amd_df_stoploss \<- amd_df_stoploss\[, c("date", "close")\]

amd_df_stoploss \<-
amd_df_stoploss\[amd_df_stoploss$date >= starting_date & amd_df_stoploss$date
\<= end_date,\] #defining the trading period

options(scipen=999)

for (i in 1:nrow(amd_df_stoploss)) { if (i==1) {
amd_df_stoploss$shares_owned[1] <- 100  amd_df_stoploss$trade_type\[1\]
\<- "Buy" #initialisng the columns
amd_df_stoploss$costs_proceeds[1] <- -100 * amd_df$close\[1\]
amd_df_stoploss$average_purchase_price[1] <- amd_df_stoploss$close\[1\]
amd_df_stoploss$invested_capital[1] <- -amd_df_stoploss$costs_proceeds\[1\]
amd_df_stoploss$sum_prices[i=1] <- amd_df_stoploss$close\[1\] }

if (i!=1) { if (i==nrow(amd_df_stoploss)) {
amd_df_stoploss$costs_proceeds[nrow(amd_df_stoploss)] <- amd_df_stoploss$close\[nrow(amd_df_stoploss)\]
\*
amd_df_stoploss$shares_owned[nrow(amd_df_stoploss)-1]  amd_df_stoploss$shares_owned\[nrow(amd_df_stoploss)\]
\<- 0
amd_df_stoploss$trade_type[nrow(amd_df_stoploss)] <- "Sell" #again, on the final day, we sell all our stock  amd_df_stoploss$invested_capital\[nrow(amd_df_stoploss)\]
\<-
amd_df_stoploss$invested_capital[nrow(amd_df_stoploss)-1]  }  else if (amd_df_stoploss$close\[i\]
\<
amd_df_stoploss$close[i-1] & -amd_df_stoploss$close\[i\]+amd_df_stoploss$close[i-1] <= 0.05*amd_df_stoploss$average_purchase_price\[i-1\])
{
amd_df_stoploss$shares_owned[i] <- amd_df_stoploss$shares_owned\[i-1\] +
100
amd_df_stoploss$costs_proceeds[i] <- -100 * amd_df_stoploss$close\[i\]
#if our stock price falls between 2 days, we will buy, assuming that the
drop in the
amd_df_stoploss$trade_type[i] <- "Buy" #price is less than the 5% of our average price to trigger our stop-loss mechanism  amd_df_stoploss$invested_capital\[i\]
\<-
amd_df_stoploss$invested_capital[i-1] -amd_df_stoploss$costs_proceeds\[i\]
} else if (amd_df_stoploss$close[i] >= amd_df_stoploss$close\[i-1\]){
amd_df_stoploss$shares_owned[i] <- amd_df_stoploss$shares_owned\[i-1\]
amd_df_stoploss$costs_proceeds[i] <- 0  amd_df_stoploss$trade_type\[i\]
\<- "Hold" #if our stock price is greater than the previous day, we hold
our stock
amd_df_stoploss$invested_capital[i] <- amd_df_stoploss$invested_capital\[i-1\]
} else if (-amd_df_stoploss$close[i]+amd_df_stoploss$close\[i-1\] \>
0.05*amd_df_stoploss$average_purchase_price[i-1]) {  amd_df_stoploss$shares_owned\[i\]
\<-
0.5*amd_df_stoploss$shares_owned[i-1]  amd_df_stoploss$costs_proceeds\[i\]
\<- 0.5\*amd_df_stoploss$shares_owned[i-1]*amd_df_stoploss$close\[i\]
amd_df_stoploss$trade_type[i] <- "Sell" #if our stock price falls greater than 5% of our average purchase price, we sell half our stock  amd_df_stoploss$invested_capital\[i\]
\<- amd_df_stoploss\$invested_capital\[i-1\] #note: we subtract the
present day's closing price from yesterdays, since this mechanism only
triggers when the price falls, not if the price rises }

if
(amd_df_stoploss$trade_type[i] == "Buy") {  amd_df_stoploss$average_purchase_price\[i\]
\<-
(amd_df_stoploss$average_purchase_price[i-1]*amd_df_stoploss$shares_owned\[i-1\]+100\*amd_df_stoploss$close[i])/amd_df_stoploss$shares_owned\[i\]
#here we find the average purchase price, which is the weighted average
of the price of the shares prior to buying and the shares bought on that
day #note: the initial if statement means that we only update our
average price if we buy } else if
(amd_df_stoploss$trade_type[i] == "Sell" || amd_df_stoploss$trade_type\[i\]
== "Hold") {
amd_df_stoploss$sum_prices[i] <- amd_df_stoploss$sum_prices\[i-1\]
amd_df_stoploss$average_purchase_price[i] <- amd_df_stoploss$average_purchase_price\[i-1\]
#here we outline how when we sell or hold our stock, we do not update
the average price (or the sum) }

} }

for (i in 1:nrow(amd_df_stoploss)) {
amd_df_stoploss$total_profit_loss[1] <- amd_df_stoploss$costs_proceeds\[1\]
#initialising the P/L column if (i != 1) {
amd_df_stoploss$total_profit_loss[i] <- amd_df_stoploss$costs_proceeds\[i\] +
amd_df_stoploss\$total_profit_loss\[i-1\] } #we define P/L as the sum of
all the costs / incomes }

for (i in 1:nrow(amd_df_stoploss)) {
amd_df_stoploss$return_on_investment[i] <- (amd_df_stoploss$total_profit_loss\[i\]/amd_df_stoploss\$invested_capital\[i\])\*100
} #again, we use the ROI formula to find the ROI


    ## Step 6: Summarize Your Findings

    -   Did your P/L and ROI improve over your chosen period?
    -   Relate your results to a relevant market event and explain why these outcomes may have occurred.

    ```{r}
    #My chosen period ran from the 2nd of January 2022 to the 2nd of January 2024.

    #By using the stop-loss mechanism, the profit and return on investment improved over the chosen period (Jan 2nd 2022 to Jan 2nd 2024). Using the original algorithm, we make a net loss by trading, whereas using the stop-loss, we make a net profit. This is likely due to the AMD stock prices falling for most of the early 2022 to late 2023 period, which means by the original algorithm that the final selling price is less than the original buy in prices, leading to a loss. In the case of a stop-loss, we are selling as the price falls and buying at lower prices, minimising losses and enabling us to sell for a net profit when prices began to rise again.

    #By the end of the period, the original algorithm made a net loss of $514,840.10, with an ROI of -21.36%, while the algorithm with the stop-loss made a net profit of $77853.64, with an ROI of 3.71%.

    #The main reason for the decline in AMD stock prices in my chosen period was due to the difficulties in AMD, and other chipset producers such as NVIDIA, in sourcing silicon to mass-produce chips for their product lines, primarily GPUs. The general uncertainty in supply led to a reduction in investor confidence, causing a downwards trajectory for AMD stock during this period. However, prices began rising towards the end of 2023, primarily due to timely innovations in AI, leading to demand for GPUs and chipsets. On December 6, 2023, AMD CEO Lisa SU announced a new graphics processor dedicated for AI services, inspiring further interest and investment into the company which further drove prices upwards towards the end of my chosen investment period.

Sample Discussion: On Wednesday, December 6, 2023, AMD CEO Lisa Su
discussed a new graphics processor designed for AI servers, with
Microsoft and Meta as committed users. The rise in AMD shares on the
following Thursday suggests that investors believe in the chipmaker's
upward potential and market expectations; My first strategy earned X
dollars more than second strategy on this day, therefore providing a
better ROI.
