# Input

For this test, I asked Claude to develop an R function which accepts a data
frame of model predictions and true responses. From there, we need to compute
RMSE, MAPE, and total sum of squares. I also asked it to use the tidyverse and
make the function dynamic with non-standard evaluation.

**Please write an R function which accepts a data frame as the first argument.**
**This function needs to consider actual and predicted values and find MAPE,**
**RMSE, and total sum of squares. Use the tidyverse, R namespaces, and the new**
**R pipe `|>`. Make it so we can pass column names using NSE with `{{}}`.**

# Response

Certainly! The R function I'll write will calculate the Mean Absolute Percentage Error (MAPE), Root Mean Squared Error (RMSE), and Total Sum of Squares (TSS) for a given data frame. This function will use tidyverse conventions and the latest R native pipe (|>).

To accept column names using Non-Standard Evaluation (NSE), I will utilize {{}} from rlang. This allows you to pass unquoted column names directly to the function.

Here's a sample function:

```r
library(tidyverse)

calculate_metrics <- function(data, actual, predicted) {
  # Ensure rlang is attached for NSE
  rlang::check_installed("rlang")
  
  # Calculate metrics
  data |> 
    dplyr::mutate(
      actual = {{ actual }},
      predicted = {{ predicted }},
      diff = actual - predicted,
      diff_squared = diff^2,
      abs_percent_error = abs(diff / actual)
    ) |> 
    dplyr::summarize(
      MAPE = mean(abs_percent_error, na.rm = TRUE) * 100,
      RMSE = sqrt(mean(diff_squared, na.rm = TRUE)),
      TSS = sum((actual - mean(actual, na.rm = TRUE))^2, na.rm = TRUE)
    )
}

# Example usage:
# calculate_metrics(your_dataframe, actual_column, predicted_column)
```

Explanation:

1. data: The data frame you pass to the function.
2. actual and predicted: The column names for the actual and predicted values.
3. The function calculates the difference, squared difference, and absolute percentage error for each row.
4. summarize is used to compute MAPE, RMSE, and TSS.
Remember to replace your_dataframe, actual_column, and predicted_column with your actual data frame and column names when calling calculate_metrics.

This function assumes that your actual and predicted columns are numeric and that the actual values are not zero (to avoid division by zero in MAPE calculation). Adjustments might be needed based on the specific characteristics of your data.
