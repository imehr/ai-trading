
# 3.1 Basic Strategy Development

## Content for each prompt:

### Prompt 1: "Explain the concept of trend following in trading and how to identify trends in cryptocurrency markets. Provide examples of trend-following indicators (e.g., moving averages, MACD) and demonstrate how to implement a simple trend-following strategy in Python. Include a backtesting example using historical data and a visual representation of the strategy's performance (e.g., equity curve, drawdown chart)."

**Trend Following Explained:**

Trend following is a trading strategy that assumes that prices tend to move in trends, and that these trends can persist for some time. The goal is to identify the beginning of a trend and ride it until it reverses. It's a reactive strategy, meaning it doesn't try to predict the future, but rather reacts to what the market is doing.

**Identifying Trends in Crypto:**

Cryptocurrency markets are known for their volatility and strong trends. Here's how to identify them:

*   **Visual Inspection:** Look at price charts. An uptrend is characterized by higher highs and higher lows, while a downtrend has lower highs and lower lows.
*   **Trend Lines:** Draw lines connecting the lows in an uptrend or the highs in a downtrend. A break of these lines can signal a trend change.
*   **Moving Averages (MAs):** MAs smooth out price data and can help identify the direction of a trend.
    *   **Simple Moving Average (SMA):** The average price over a specified period.
    *   **Exponential Moving Average (EMA):** Gives more weight to recent prices, making it more responsive to new data.
*   **MACD (Moving Average Convergence Divergence):** A momentum indicator that shows the relationship between two moving averages. A bullish crossover (MACD line crossing above the signal line) can indicate an uptrend, and a bearish crossover (MACD line crossing below the signal line) can indicate a downtrend.

**Python Implementation of a Simple Trend-Following Strategy:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf

# Function to fetch historical data
def fetch_data(symbol, start_date, end_date):
    data = yf.download(symbol, start=start_date, end=end_date)
    return data

# Function to calculate moving averages
def calculate_ma(data, short_window, long_window):
    data['SMA_short'] = data['Close'].rolling(window=short_window).mean()
    data['SMA_long'] = data['Close'].rolling(window=long_window).mean()
    return data

# Function to generate trading signals
def generate_signals(data):
    data['Signal'] = 0.0
    data['Signal'][data['SMA_short'] > data['SMA_long']] = 1.0
    data['Signal'][data['SMA_short'] < data['SMA_long']] = -1.0
    data['Position'] = data['Signal'].diff()
    return data

# Function to backtest the strategy
def backtest_strategy(data, initial_capital=10000):
    portfolio = initial_capital
    data['Returns'] = data['Close'].pct_change()
    data['Strategy_Returns'] = data['Returns'] * data['Signal'].shift(1)
    data['Cumulative_Returns'] = (1 + data['Strategy_Returns']).cumprod()
    data['Portfolio_Value'] = initial_capital * data['Cumulative_Returns']
    return data

# Function to plot the equity curve and drawdown
def plot_performance(data, symbol):
    fig, axs = plt.subplots(2, 1, figsize=(12, 8), sharex=True)

    # Plot Equity Curve
    axs[0].plot(data.index, data['Portfolio_Value'], label='Portfolio Value')
    axs[0].set_title(f'Equity Curve for {symbol}')
    axs[0].set_ylabel('Portfolio Value')
    axs[0].legend()
    axs[0].grid(True)

    # Calculate Drawdown
    data['Max_Portfolio'] = data['Portfolio_Value'].cummax()
    data['Drawdown'] = (data['Portfolio_Value'] - data['Max_Portfolio']) / data['Max_Portfolio']
    axs[1].plot(data.index, data['Drawdown'], label='Drawdown', color='red')
    axs[1].set_title(f'Drawdown for {symbol}')
    axs[1].set_ylabel('Drawdown')
    axs[1].legend()
    axs[1].grid(True)

    plt.xlabel('Date')
    plt.tight_layout()
    plt.show()

# Main function
def main():
    symbol = 'BTC-USD'
    start_date = '2023-01-01'
    end_date = '2024-01-01'
    short_window = 50
    long_window = 200

    # Fetch data
    data = fetch_data(symbol, start_date, end_date)

    # Calculate moving averages
    data = calculate_ma(data, short_window, long_window)

    # Generate trading signals
    data = generate_signals(data)

    # Backtest the strategy
    data = backtest_strategy(data)

    # Plot performance
    plot_performance(data, symbol)

if __name__ == "__main__":
    main()
```

**Explanation:**

1.  **Data Fetching:** Uses `yfinance` to download historical price data for Bitcoin.
2.  **Moving Averages:** Calculates short-term (50-day) and long-term (200-day) simple moving averages.
3.  **Signal Generation:** Generates buy signals when the short-term MA crosses above the long-term MA and sell signals when the short-term MA crosses below the long-term MA.
4.  **Backtesting:** Simulates trading based on the generated signals and calculates the portfolio value over time.
5.  **Plotting:** Visualizes the equity curve and drawdown of the strategy.

**Visual Representation:**

The code generates two plots:

*   **Equity Curve:** Shows how the portfolio value changes over time.
*   **Drawdown Chart:** Shows the percentage decline from a peak in the portfolio value.

This example provides a basic framework for a trend-following strategy. You can modify the parameters (e.g., moving average periods) and add other indicators to improve its performance.

### Prompt 2: "Introduce mean reversion as a trading strategy and explain how it can be applied to cryptocurrencies. Discuss different mean reversion indicators (e.g., Bollinger Bands, RSI) and provide a Python code example for a mean reversion strategy. Include a section on the statistical basis of mean reversion and its limitations."

**Mean Reversion Explained:**

Mean reversion is a trading strategy that assumes that prices tend to revert to their average value over time. It's based on the idea that prices that have deviated significantly from their mean are likely to return to it. This strategy is often used in range-bound markets.

**Applying Mean Reversion to Cryptocurrencies:**

While cryptocurrencies are known for their volatility, they can exhibit periods of range-bound trading where mean reversion strategies can be effective. However, it's crucial to be aware of the potential for sudden breakouts or breakdowns that can invalidate mean reversion assumptions.

**Mean Reversion Indicators:**

*   **Bollinger Bands:** These bands are plotted at standard deviations above and below a moving average. When prices touch the upper band, they are considered overbought and likely to revert to the mean. When prices touch the lower band, they are considered oversold and likely to revert to the mean.
*   **Relative Strength Index (RSI):** This momentum oscillator measures the speed and change of price movements. An RSI above 70 is considered overbought, and an RSI below 30 is considered oversold, suggesting a potential reversion to the mean.

**Python Implementation of a Mean Reversion Strategy:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf
import talib

# Function to fetch historical data
def fetch_data(symbol, start_date, end_date):
    data = yf.download(symbol, start=start_date, end=end_date)
    return data

# Function to calculate Bollinger Bands
def calculate_bollinger_bands(data, window, num_std):
    data['SMA'] = data['Close'].rolling(window=window).mean()
    data['StdDev'] = data['Close'].rolling(window=window).std()
    data['Upper_Band'] = data['SMA'] + (data['StdDev'] * num_std)
    data['Lower_Band'] = data['SMA'] - (data['StdDev'] * num_std)
    return data

# Function to calculate RSI
def calculate_rsi(data, window):
    data['RSI'] = talib.RSI(data['Close'], timeperiod=window)
    return data

# Function to generate trading signals
def generate_signals(data, rsi_oversold=30, rsi_overbought=70):
    data['Signal'] = 0.0
    data['Signal'][(data['Close'] < data['Lower_Band']) | (data['RSI'] < rsi_oversold)] = 1.0  # Buy signal
    data['Signal'][(data['Close'] > data['Upper_Band']) | (data['RSI'] > rsi_overbought)] = -1.0 # Sell signal
    data['Position'] = data['Signal'].diff()
    return data

# Function to backtest the strategy
def backtest_strategy(data, initial_capital=10000):
    portfolio = initial_capital
    data['Returns'] = data['Close'].pct_change()
    data['Strategy_Returns'] = data['Returns'] * data['Signal'].shift(1)
    data['Cumulative_Returns'] = (1 + data['Strategy_Returns']).cumprod()
    data['Portfolio_Value'] = initial_capital * data['Cumulative_Returns']
    return data

# Function to plot the equity curve and drawdown
def plot_performance(data, symbol):
    fig, axs = plt.subplots(2, 1, figsize=(12, 8), sharex=True)

    # Plot Equity Curve
    axs[0].plot(data.index, data['Portfolio_Value'], label='Portfolio Value')
    axs[0].set_title(f'Equity Curve for {symbol}')
    axs[0].set_ylabel('Portfolio Value')
    axs[0].legend()
    axs[0].grid(True)

    # Calculate Drawdown
    data['Max_Portfolio'] = data['Portfolio_Value'].cummax()
    data['Drawdown'] = (data['Portfolio_Value'] - data['Max_Portfolio']) / data['Max_Portfolio']
    axs[1].plot(data.index, data['Drawdown'], label='Drawdown', color='red')
    axs[1].set_title(f'Drawdown for {symbol}')
    axs[1].set_ylabel('Drawdown')
    axs[1].legend()
    axs[1].grid(True)

    plt.xlabel('Date')
    plt.tight_layout()
    plt.show()

# Main function
def main():
    symbol = 'BTC-USD'
    start_date = '2023-01-01'
    end_date = '2024-01-01'
    window = 20
    num_std = 2
    rsi_window = 14
    rsi_oversold = 30
    rsi_overbought = 70

    # Fetch data
    data = fetch_data(symbol, start_date, end_date)

    # Calculate Bollinger Bands
    data = calculate_bollinger_bands(data, window, num_std)

    # Calculate RSI
    data = calculate_rsi(data, rsi_window)

    # Generate trading signals
    data = generate_signals(data, rsi_oversold, rsi_overbought)

    # Backtest the strategy
    data = backtest_strategy(data)

    # Plot performance
    plot_performance(data, symbol)

if __name__ == "__main__":
    main()
```

**Explanation:**

1.  **Data Fetching:** Uses `yfinance` to download historical price data for Bitcoin.
2.  **Bollinger Bands:** Calculates the moving average and upper/lower bands based on standard deviations.
3.  **RSI:** Calculates the Relative Strength Index using the `talib` library.
4.  **Signal Generation:** Generates buy signals when the price is below the lower Bollinger Band or the RSI is oversold, and sell signals when the price is above the upper Bollinger Band or the RSI is overbought.
5.  **Backtesting:** Simulates trading based on the generated signals and calculates the portfolio value over time.
6.  **Plotting:** Visualizes the equity curve and drawdown of the strategy.

**Statistical Basis of Mean Reversion:**

Mean reversion is based on the statistical concept that extreme values tend to be followed by values closer to the mean. This is often observed in financial markets due to factors like:

*   **Market Psychology:** Overreactions to news or events can push prices too far from their average, leading to a correction.
*   **Arbitrage:** Traders may take advantage of price discrepancies, pushing prices back towards their mean.

**Limitations of Mean Reversion:**

*   **Trended Markets:** Mean reversion strategies can perform poorly in strongly trending markets, where prices may not revert to the mean for extended periods.
*   **False Signals:** Indicators can generate false signals, leading to losses.
*   **Parameter Optimization:** The effectiveness of mean reversion strategies often depends on the choice of parameters (e.g., Bollinger Band periods, RSI levels), which may need to be optimized for different markets and timeframes.

### Prompt 3: "Explain momentum trading and how to identify momentum in cryptocurrency prices. Discuss different momentum indicators (e.g., ROC, RSI) and provide a Python code example for a momentum-based strategy. Include a comparison of momentum and trend-following strategies, highlighting their differences and potential synergies."

**Momentum Trading Explained:**

Momentum trading is a strategy that capitalizes on the tendency of prices to continue moving in the same direction once a trend has been established. It's based on the idea that strong price movements are likely to persist, and traders aim to profit from these movements.

**Identifying Momentum in Crypto Prices:**

Momentum can be identified by:

*   **Price Velocity:** Rapid price increases or decreases indicate strong momentum.
*   **Volume:** High trading volume accompanying price movements can confirm momentum.
*   **Momentum Indicators:**
    *   **Rate of Change (ROC):** Measures the percentage change in price over a specified period. A high ROC indicates strong upward momentum, while a low ROC indicates strong downward momentum.
    *   **Relative Strength Index (RSI):** While also used in mean reversion, RSI can indicate momentum when it is consistently above 50 (bullish momentum) or below 50 (bearish momentum).

**Python Implementation of a Momentum-Based Strategy:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf
import talib

# Function to fetch historical data
def fetch_data(symbol, start_date, end_date):
    data = yf.download(symbol, start=start_date, end=end_date)
    return data

# Function to calculate Rate of Change (ROC)
def calculate_roc(data, window):
    data['ROC'] = talib.ROC(data['Close'], timeperiod=window)
    return data

# Function to calculate RSI
def calculate_rsi(data, window):
    data['RSI'] = talib.RSI(data['Close'], timeperiod=window)
    return data

# Function to generate trading signals
def generate_signals(data, roc_threshold=5, rsi_threshold=60):
    data['Signal'] = 0.0
    data['Signal'][(data['ROC'] > roc_threshold) & (data['RSI'] > rsi_threshold)] = 1.0  # Buy signal
    data['Signal'][(data['ROC'] < -roc_threshold) & (data['RSI'] < (100 - rsi_threshold))] = -1.0 # Sell signal
    data['Position'] = data['Signal'].diff()
    return data

# Function to backtest the strategy
def backtest_strategy(data, initial_capital=10000):
    portfolio = initial_capital
    data['Returns'] = data['Close'].pct_change()
    data['Strategy_Returns'] = data['Returns'] * data['Signal'].shift(1)
    data['Cumulative_Returns'] = (1 + data['Strategy_Returns']).cumprod()
    data['Portfolio_Value'] = initial_capital * data['Cumulative_Returns']
    return data

# Function to plot the equity curve and drawdown
def plot_performance(data, symbol):
    fig, axs = plt.subplots(2, 1, figsize=(12, 8), sharex=True)

    # Plot Equity Curve
    axs[0].plot(data.index, data['Portfolio_Value'], label='Portfolio Value')
    axs[0].set_title(f'Equity Curve for {symbol}')
    axs[0].set_ylabel('Portfolio Value')
    axs[0].legend()
    axs[0].grid(True)

    # Calculate Drawdown
    data['Max_Portfolio'] = data['Portfolio_Value'].cummax()
    data['Drawdown'] = (data['Portfolio_Value'] - data['Max_Portfolio']) / data['Max_Portfolio']
    axs[1].plot(data.index, data['Drawdown'], label='Drawdown', color='red')
    axs[1].set_title(f'Drawdown for {symbol}')
    axs[1].set_ylabel('Drawdown')
    axs[1].legend()
    axs[1].grid(True)

    plt.xlabel('Date')
    plt.tight_layout()
    plt.show()

# Main function
def main():
    symbol = 'BTC-USD'
    start_date = '2023-01-01'
    end_date = '2024-01-01'
    roc_window = 10
    roc_threshold = 5
    rsi_window = 14
    rsi_threshold = 60

    # Fetch data
    data = fetch_data(symbol, start_date, end_date)

    # Calculate ROC
    data = calculate_roc(data, roc_window)

    # Calculate RSI
    data = calculate_rsi(data, rsi_window)

    # Generate trading signals
    data = generate_signals(data, roc_threshold, rsi_threshold)

    # Backtest the strategy
    data = backtest_strategy(data)

    # Plot performance
    plot_performance(data, symbol)

if __name__ == "__main__":
    main()
```

**Explanation:**

1.  **Data Fetching:** Uses `yfinance` to download historical price data for Bitcoin.
2.  **ROC Calculation:** Calculates the Rate of Change using the `talib` library.
3.  **RSI Calculation:** Calculates the Relative Strength Index using the `talib` library.
4.  **Signal Generation:** Generates buy signals when both ROC and RSI are above their respective thresholds, and sell signals when both ROC and RSI are below their respective thresholds.
5.  **Backtesting:** Simulates trading based on the generated signals and calculates the portfolio value over time.
6.  **Plotting:** Visualizes the equity curve and drawdown of the strategy.

**Comparison of Momentum and Trend-Following Strategies:**

| Feature          | Momentum Trading                                                          Okay, I understand. I will continue generating content for the next prompt in the curriculum, which is "3.2 Advanced Technical Analysis". I will generate content for each prompt listed under this section, and save the generated content in a new file called `3.2 Advanced Technical Analysis.md`.
```language:output_01/curriculumn_developed_by_gemini/3.2 Advanced Technical Analysis.md
# 3.2 Advanced Technical Analysis

## Content for each prompt:

### Prompt 1: "Explain how to develop custom technical indicators in Python. Provide a step-by-step guide to creating a new indicator from scratch, including defining the logic, implementing the calculation, and visualizing the results. Include examples of custom indicators based on unique trading ideas or market insights."

**Developing Custom Technical Indicators in Python:**

Creating custom technical indicators allows traders to tailor their analysis to specific market conditions or trading ideas. Here's a step-by-step guide:

1.  **Define the Logic:**
    *   Start with a clear idea of what you want the indicator to measure.
    *   Formulate the mathematical logic behind the indicator. This might involve combining existing indicators, using price action, or incorporating other market data.
    *   Consider the parameters that the indicator will use (e.g., lookback periods, smoothing factors).

2.  **Implement the Calculation in Python:**
    *   Use libraries like `pandas`, `numpy`, and `talib` to perform calculations efficiently.
    *   Create a function that takes price data as input and returns the indicator values.
    *   Ensure the code is well-documented and easy to understand.

3.  **Visualize the Results:**
    *   Use libraries like `matplotlib` or `plotly` to plot the indicator alongside price data.
    *   This helps in understanding how the indicator behaves and identifying potential trading signals.

**Example: Custom Indicator - "Price Momentum Oscillator (PMO)"**

Let's create a custom indicator that combines price momentum with volume. The logic is as follows:

*   Calculate the difference between the current closing price and the closing price `n` periods ago.
*   Multiply this difference by the volume.
*   Smooth the result using a moving average.

**Python Implementation:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf

# Function to fetch historical data
def fetch_data(symbol, start_date, end_date):
    data = yf.download(symbol, start=start_date, end=end_date)
    return data

# Function to calculate the Price Momentum Oscillator (PMO)
def calculate_pmo(data, lookback_period, smoothing_period):
    # Calculate price difference
    data['Price_Diff'] = data['Close'].diff(lookback_period)

    # Multiply by volume
    data['PMO_Raw'] = data['Price_Diff'] * data['Volume']

    # Calculate moving average
    data['PMO'] = data['PMO_Raw'].rolling(window=smoothing_period).mean()

    return data

# Function to plot the indicator
def plot_indicator(data, symbol):
    fig, axs = plt.subplots(2, 1, figsize=(12, 8), sharex=True)

    # Plot Price
    axs[0].plot(data.index, data['Close'], label='Close Price')
    axs[0].set_title(f'Price and PMO for {symbol}')
    axs[0].set_ylabel('Price')
    axs[0].legend()
    axs[0].grid(True)

    # Plot PMO
    axs[1].plot(data.index, data['PMO'], label='PMO', color='purple')
    axs[1].set_ylabel('PMO')
    axs[1].legend()
    axs[1].grid(True)

    plt.xlabel('Date')
    plt.tight_layout()
    plt.show()

# Main function
def main():
    symbol = 'BTC-USD'
    start_date = '2023-01-01'
    end_date = '2024-01-01'
    lookback_period = 10
    smoothing_period = 20

    # Fetch data
    data = fetch_data(symbol, start_date, end_date)

    # Calculate PMO
    data = calculate_pmo(data, lookback_period, smoothing_period)

    # Plot the indicator
    plot_indicator(data, symbol)

if __name__ == "__main__":
    main()
```

**Explanation:**

1.  **Data Fetching:** Uses `yfinance` to download historical price data for Bitcoin.
2.  **PMO Calculation:** Calculates the Price Momentum Oscillator as described above.
3.  **Plotting:** Visualizes the price and the PMO on the same chart.

**Examples of Custom Indicators:**

*   **Volume-Weighted Moving Average (VWMA):** A moving average that gives more weight to prices with higher volume.
*   **Chaikin Money Flow (CMF):** Measures the amount of money flowing into or out of a security over a period.
*   **Custom Volatility Indicators:** Combine different volatility measures to create a unique view of market risk.

**Key Considerations:**

*   **Backtesting:** Always backtest your custom indicators to evaluate their performance.
*   **Optimization:** Optimize the parameters of your indicators to find the best settings for different markets and timeframes.
*   **Overfitting:** Be careful not to overfit your indicators to historical data, as this can lead to poor performance in live trading.

### Prompt 2: "Discuss multi-timeframe analysis in trading and how to combine signals from different timeframes to improve trading decisions. Provide a Python code example demonstrating how to fetch and analyze data from multiple timeframes using the `pandas` library. Include a flowchart illustrating a trading strategy that incorporates multi-timeframe analysis."

**Multi-Timeframe Analysis Explained:**

Multi-timeframe analysis involves analyzing price charts across different timeframes (e.g., 15-minute, hourly, daily, weekly) to gain a more comprehensive view of market conditions. It helps traders identify trends, support/resistance levels, and potential trading opportunities that might not be visible on a single timeframe.

**Combining Signals from Different Timeframes:**

*   **Higher Timeframe for Trend:** Use a higher timeframe (e.g., daily or weekly) to identify the overall trend.
*   **Lower Timeframe for Entry:** Use a lower timeframe (e.g., hourly or 15-minute) to find precise entry and exit points.
*   **Confirmation:** Look for confluence of signals across different timeframes. For example, a buy signal on a lower timeframe that aligns with an uptrend on a higher timeframe is a stronger signal.

**Python Implementation for Multi-Timeframe Analysis:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf
import talib

# Function to fetch historical data for multiple timeframes
def fetch_multi_timeframe_data(symbol, start_date, end_date, timeframes):
    data = {}
    for timeframe in timeframes:
        if timeframe == '1d':
            data[timeframe] = yf.download(symbol, start=start_date, end=end_date)
        else:
            data[timeframe] = yf.download(symbol, start=start_date, end=end_date, interval=timeframe)
    return data

# Function to calculate moving averages
def calculate_ma(data, window):
    data['SMA'] = data['Close'].rolling(window=window).mean()
    return data

# Function to generate trading signals based on multi-timeframe analysis
def generate_multi_timeframe_signals(data, short_window, long_window):
    signals = pd.DataFrame(index=data['15m'].index)
    signals['Signal'] = 0.0

    # Calculate moving averages for daily timeframe
    data['1d'] = calculate_ma(data['1d'], short_window)
    data['1d'] = calculate_ma(data['1d'], long_window)

    # Calculate moving averages for 15-minute timeframe
    data['15m'] = calculate_ma(data['15m'], short_window)
    data['15m'] = calculate_ma(data['15m'], long_window)

    # Generate signals based on daily trend and 15-minute crossover
    signals['Signal'][(data['1d']['SMA'].iloc[-1] > data['1d']['SMA'].iloc[-1]) & (data['15m']['SMA'] > data['15m']['SMA'])] = 1.0
    signals['Signal'][(data['1d']['SMA'].iloc[-1] < data['1d']['SMA'].iloc[-1]) & (data['15m']['SMA'] < data['15m']['SMA'])] = -1.0

    signals['Position'] = signals['Signal'].diff()
    return signals

# Function to backtest the strategy
def backtest_strategy(data, signals, initial_capital=10000):
    portfolio = initial_capital
    data['15m']['Returns'] = data['15m']['Close'].pct_change()
    signals['Strategy_Returns'] = data['15m']['Returns'] * signals['Signal'].shift(1)
    signals['Cumulative_Returns'] = (1 + signals['Strategy_Returns']).cumprod()
    signals['Portfolio_Value'] = initial_capital * signals['Cumulative_Returns']
    return signals

# Function to plot the equity curve and drawdown
def plot_performance(signals, symbol):
    fig, axs = plt.subplots(2, 1, figsize=(12, 8), sharex=True)

    # Plot Equity Curve
    axs[0].plot(signals.index, signals['Portfolio_Value'], label='Portfolio Value')
    axs[0].set_title(f'Equity Curve for {symbol}')
    axs[0].set_ylabel('Portfolio Value')
    axs[0].legend()
    axs[0].grid(True)

    # Calculate Drawdown
    signals['Max_Portfolio'] = signals['Portfolio_Value'].cummax()
    signals['Drawdown'] = (signals['Portfolio_Value'] - signals['Max_Portfolio']) / signals['Max_Portfolio']
    axs[1].plot(signals.index, signals['Drawdown'], label='Drawdown', color='red')
    axs[1].set_title(f'Drawdown for {symbol}')
    axs[1].set_ylabel('Drawdown')
    axs[1].legend()
    axs[1].grid(True)

    plt.xlabel('Date')
    plt.tight_layout()
    plt.show()

# Main function
def main():
    symbol = 'BTC-USD'
    start_date = '2023-01-01'
    end_date = '2024-01-01'
    timeframes = ['15m', '1d']
    short_window = 50
    long_window = 200

    # Fetch data for multiple timeframes
    data = fetch_multi_timeframe_data(symbol, start_date, end_date, timeframes)

    # Generate trading signals
    signals = generate_multi_timeframe_signals(data, short_window, long_window)

    # Backtest the strategy
    signals = backtest_strategy(data, signals)

    # Plot performance
    plot_performance(signals, symbol)

if __name__ == "__main__":
    main()
```

**Explanation:**

1.  **Data Fetching:** Fetches data for 15-minute and daily timeframes using `yfinance`.
2.  **Moving Averages:** Calculates moving averages for both timeframes.
3.  **Signal Generation:** Generates buy signals when the daily trend is up and the 15-minute MA crosses up, and sell signals when the daily trend is down and the 15-minute MA crosses down.
4.  **Backtesting:** Simulates trading based on the generated signals and calculates the portfolio value over time.
5.  **Plotting:** Visualizes the equity curve and drawdown of the strategy.

**Flowchart of a Multi-Timeframe Trading Strategy:**

```mermaid
graph LR
    A[Start] --> B{Identify Trend on Higher Timeframe (e.g., Daily)};
    B -- Uptrend --> C{Check for Buy Signal on Lower Timeframe (e.g., 15-min)};
    B -- Downtrend --> D{Check for Sell Signal on Lower Timeframe (e.g., 15-min)};
    C -- Buy Signal --> E[Enter Long Position];
    C -- No Buy Signal --> F[Wait];
    D -- Sell Signal --> G[Enter Short Position];
    D -- No Sell Signal --> F;
    E --> H[Monitor Position];
    G --> H;
    H --> I{Exit Condition Met?};
    I -- Yes --> J[Exit Position];
    I -- No --> H;
    J --> K[End];
    F --> B;
```

**Key Considerations:**

*   **Timeframe Selection:** Choose timeframes that align with your trading style and goals.
*   **Signal Confluence:** Look for multiple signals across timeframes to increase the probability of success.
*   **Risk Management:** Always use appropriate risk management techniques when trading based on multi-timeframe analysis.

### Prompt 3: "Introduce volume profile analysis and how it can be used to identify key price levels and areas of high trading activity. Explain how to construct a volume profile and how to interpret its features (e.g., Point of Control, Value Area). Provide examples of how to use volume profile in trading decisions and include links to charting tools that offer volume profile analysis."

**Volume Profile Analysis Explained:**

Volume profile analysis is a charting technique that displays the volume traded at different price levels over a specified period. Unlike traditional volume bars, which show volume over time, volume profile shows volume over price. This helps traders identify key price levels where significant trading activity has occurred.

**Constructing a Volume Profile:**

1.  **Data Collection:** Gather price and volume data for the period you want to analyze.
2.  **Price Buckets:** Divide the price range into a series of price buckets or levels.
3.  **Volume Aggregation:** For each price bucket, sum the volume traded at that level.
4.  **Visualization:** Display the volume for each price bucket as a horizontal histogram on the price chart.

**Key Features of a Volume Profile:**

*   **Point of Control (POC):** The price level with the highest traded volume. It often acts as a magnet for price action.
*   **Value Area (VA):** The range of price levels where a specified percentage (usually 68% or 70%) of the total volume was traded. It represents the area of fair value.
    *   **Value Area High (VAH):** The upper boundary of the value area.
    *   **Value Area Low (VAL):** The lower boundary of the value area.
*   **High Volume Nodes (HVN):** Price levels with high volume, indicating areas of strong support or resistance.
*   **Low Volume Nodes (LVN):** Price levels with low volume, indicating areas where price may move quickly.

**Interpreting Volume Profile:**

*   **POC as Support/Resistance:** The POC can act as a support level in an uptrend and a resistance level in a downtrend.
*   **Value Area as Fair Value:** Prices tend to gravitate towards the value area.
*   **HVNs as Strong Levels:** HVNs can act as strong support or resistance levels.
*   **LVNs as Potential Breakout Areas:** Prices may move quickly through LVNs.

**Using Volume Profile in Trading Decisions:**

*   **Identify Support and Resistance:** Use HVNs and the POC to identify potential support and resistance levels.
*   **Confirm Breakouts:** Look for breakouts from the value area or HVNs to confirm trend changes.
*   **Find Entry and Exit Points:** Use the POC and value area to find potential entry and exit points.
*   **Assess Market Sentiment:** A volume profile skewed towards the top of the range may indicate bullish sentiment, while a profile skewed towards the bottom may indicate bearish sentiment.

**Examples of Volume Profile in Trading:**

*   **Long Entry:** Enter a long position when the price bounces off a HVN or the POC in an uptrend.
*   **Short Entry:** Enter a short position when the price rejects a HVN or the POC in a downtrend.
*   **Breakout Trade:** Enter a long position when the price breaks above the VAH or a short position when the price breaks below the VAL.

**Charting Tools with Volume Profile Analysis:**

*   **TradingView:** [https://www.tradingview.com/](https://www.tradingview.com/)
*   **Sierra Chart:** [https://www.sierrachart.com/](https://www.sierrachart.com/)
*   **NinjaTrader:** [https://ninjatrader.com/](https://ninjatrader.com/)
*   **GoCharting:** [https://gocharting.com/](https://gocharting.com/)

These platforms offer built-in volume profile tools that allow you to visualize and analyze volume profiles on various timeframes and assets.

**Key Considerations:**

*   **Timeframe:** Use volume profiles on different timeframes to get a comprehensive view of market activity.
*   **Context:** Always consider the overall market context when interpreting volume profiles.
*   **Confirmation:** Use volume profile analysis in conjunction with other technical indicators and price action analysis.

### Prompt 4: "Explain order flow trading and how to analyze order book data to gain insights into market sentiment and potential price movements. Discuss concepts like order book imbalances, absorption, and spoofing. Provide examples of order flow patterns and suggest tools or platforms for visualizing and analyzing order flow data."

**Order Flow Trading Explained:**

Order flow trading is a technique that focuses on analyzing the order book and trade data to understand the dynamics of supply and demand in real-time. It goes beyond traditional technical analysis by looking at the actual orders being placed and executed, providing insights into market sentiment and potential price movements.

**Analyzing Order Book Data:**

*   **Order Book:** The order book is a list of all outstanding buy and sell orders at different price levels. It shows the depth of the market and the potential liquidity at each price.
*   **Trade Data:** Trade data shows the actual transactions that have occurred, including the price, volume, and time of each trade.

**Key Concepts in Order Flow Trading:**

*   **Order Book Imbalances:** When there are significantly more buy orders than sell orders (or vice versa) at a particular price level, it can indicate a potential price move in that direction.
*   **Absorption:** When large orders are placed at a specific price level, and the market is unable to move through that level, it indicates absorption. This can suggest that the price may reverse direction.
*   **Spoofing:** When traders place large orders with no intention of executing them, they are spoofing. This is done to create a false impression of demand or supply and manipulate the market.
*   **Iceberg Orders:** Large orders that are hidden from the order book, only revealing a small portion of the order at a time. These can be used to accumulate or distribute positions without causing significant price movements.
*   **Aggression:** The speed and size of market orders being executed can indicate the level of aggression in the market.

**Order Flow Patterns:**

*   **Stacking:** When multiple orders are placed at the same price level, it can indicate strong support or resistance.
*   **Sweeping:** When market orders quickly consume all the liquidity at a price level, it can indicate a strong directional move.
*   **Clustering:** When a large number of trades occur at a specific price level, it can indicate a potential turning point.

**Using Order Flow in Trading Decisions:**

*   **Identify Support and Resistance:** Look for areas of absorption or stacking to identify potential support and resistance levels.
*   **Confirm Breakouts:** Look for sweeping or aggressive market orders to confirm breakouts.
*   **Detect Spoofing:** Be aware of large orders that are quickly canceled, as this may indicate spoofing.
*   **Anticipate Price Movements:** Use order book imbalances and absorption to anticipate potential price movements.

**Tools and Platforms for Visualizing and Analyzing Order Flow Data:**

*   **Bookmap:** [https://bookmap.com/](https://bookmap.com/)
*   **Jigsaw Trading:** [https://jigsawtrading.com/](https://jigsawtrading.com/)
*   **Sierra Chart:** [https://www.sierrachart.com/](https://www.sierrachart.com/)
*   **NinjaTrader:** [https://ninjatrader.com/](https://ninjatrader.com/)
*   **ATAS (Advanced Time and Sales):** [https://atas.net/](https://atas.net/)

These platforms provide real-time order book data, trade data, and various visualization tools to help traders analyze order flow.

**Key Considerations:**

*   **Real-Time Data:** Order flow trading requires real-time data feeds.
*   **Experience:** It takes time and practice to become proficient in order flow analysis.
*   **Context:** Always consider the overall market context when analyzing order flow.
*   **Risk Management:** Use appropriate risk management techniques when trading based on order flow analysis.

### Prompt 5: "Discuss market depth analysis and how to use Level 2 data to understand the supply and demand dynamics in a market. Explain how to interpret market depth charts and how to identify potential support and resistance levels based on order book depth. Provide examples of how market depth can be used to improve trade execution and manage risk."

**Market Depth Analysis Explained:**

Market depth analysis involves examining the order book to understand the supply and demand dynamics at different price levels. It uses Level 2 data, which provides a detailed view of the order book, including the quantity of buy and sell orders at each price. This analysis helps traders assess the liquidity and potential price movements in a market.

**Level 2 Data:**

Level 2 data provides a more granular view of the order book compared to Level 1 data, which only shows the best bid and ask prices. Level 2 data includes:

*   **Bid Prices:** The prices at which buyers are willing to purchase an asset.
*   **Bid Sizes:** The quantity of the asset that buyers are willing to purchase at each bid price.
*   **Ask Prices:** The prices at which sellers are willing to sell an asset.
*   **Ask Sizes:** The quantity of the asset that sellers are willing to sell at each ask price.

**Interpreting Market Depth Charts:**

*   **Depth of Market:** The number of orders and the quantity of assets at each price level indicate the depth of the market. A deep market has a large number of orders and a high quantity of assets at various price levels.
*   **Order Book Imbalances:** A significant imbalance between bid and ask sizes at different price levels can indicate potential price movements.
*   **Support and Resistance:** Areas with a large number of buy orders can act as support levels, while areas with a large number of sell orders can act as resistance levels.
*   **Liquidity:** Areas with a high quantity of orders indicate high liquidity, while areas with a low quantity of orders indicate low liquidity.
*   **Spoofing:** Large orders that are quickly canceled can indicate spoofing.

**Using Market Depth to Improve Trade Execution:**

*   **Slippage Reduction:** By analyzing the order book, traders can identify areas with high liquidity and place orders at prices where they are more likely to be filled without significant slippage.
*   **Optimal Order Placement:** Traders can use market depth to place limit orders at prices where they are more likely to be filled, rather than using market orders that can result in unfavorable prices.
*   **Timing Entries and Exits:** By observing the order book, traders can time their entries and exits to take advantage of short-term imbalances in supply and demand.

**Using Market Depth to Manage Risk:**

*   **Stop-Loss Placement:** Traders can use market depth to place stop-loss orders at prices where they are less likely to be triggered by short-term price fluctuations.
*   **Position Sizing:** By assessing the liquidity at different price levels, traders can adjust their position sizes to manage risk.
*   **Identifying Potential Reversals:** By observing changes in the order book, traders can identify potential reversals and adjust their positions accordingly.

**Examples of Market Depth in Trading:**

*   **Support Level:** A large number of buy orders at a specific price level can act as a support level. Traders may place buy orders near this level, expecting the price to bounce off it.
*   **Resistance Level:** A large number of sell orders at a specific price level can act as a resistance level. Traders may place sell orders near this level, expecting the price to reverse.
*   **Breakout Trade:** A sudden increase in buy orders at a resistance level can indicate a potential breakout. Traders may enter a long position when this occurs.

**Key Considerations:**

*   **Real-Time Data:** Market depth analysis requires real-time Level 2 data feeds.
*   **Experience:** It takes time and practice to become proficient in interpreting market depth charts.
*   **Context:** Always consider the overall market context when analyzing market depth.
*   **Risk Management:** Use appropriate risk management techniques when trading based on market depth analysis.

### Prompt 6: "Explain how to identify and interpret institutional order flow in cryptocurrency markets. Discuss techniques for detecting large orders and understanding their potential impact on price. Provide examples of institutional order flow patterns and suggest data sources or tools for tracking institutional activity."

**Identifying and Interpreting Institutional Order Flow:**

Institutional order flow refers to the trading activity of large financial institutions, such as hedge funds, investment banks, and asset managers. These entities often trade in large volumes and can have a significant impact on market prices. Identifying and interpreting their order flow can provide valuable insights into market sentiment and potential price movements.

**Techniques for Detecting Large Orders:**

*   **Order Book Analysis:** Look for large orders placed at specific price levels. These orders can be visible in the order book as large bid or ask sizes.
*   **Time and Sales Data:** Analyze the time and sales data for large trades. These trades can indicate institutional activity.
*   **Volume Analysis:** Look for spikes in volume that are not accompanied by significant price movements. This can indicate large orders being absorbed by the market.
*   **Block Trades:** Block trades are large private transactions that are often executed by institutions. These trades can be reported separately and can provide insights into institutional activity.
*   **Dark Pools:** Dark pools are private exchanges where large institutions can trade without revealing their orders to the public. While these trades are not visible in the order book, they can still impact market prices.

**Understanding the Impact of Institutional Order Flow:**

*   **Price Movements:** Large institutional orders can cause significant price movements, especially in less liquid markets.
*   **Trend Confirmation:** Institutional order flow can confirm existing trends. For example, large buy orders in an uptrend can indicate that institutions are also bullish.
*   **Reversal Signals:** Institutional order flow can also signal potential reversals. For example, large sell orders at a resistance level can indicate that institutions are taking profits.
*   **Market Sentiment:** Institutional order flow can provide insights into the overall market sentiment. For example, a large number of institutional buy orders can indicate bullish sentiment.

**Examples of Institutional Order Flow Patterns:**

*   **Accumulation:** Institutions may accumulate positions over time by placing large buy orders at different price levels. This can be seen as a gradual increase in volume and price.
*   **Distribution:** Institutions may distribute positions over time by placing large sell orders at different price levels. This can be seen as a gradual decrease in volume and price.
*   **Spoofing:** Institutions may use spoofing to manipulate the market by placing large orders with no intention of executing them.
*   **Iceberg Orders:** Institutions may use iceberg orders to hide their large orders from the public.

**Data Sources and Tools for Tracking Institutional Activity:**

*   **Exchange APIs:** Some cryptocurrency exchanges provide APIs that allow traders to access order book data, trade data, and block trade data.
*   **Order Flow Platforms:** Platforms like Bookmap, Jigsaw Trading, and ATAS provide tools for visualizing and analyzing order flow data.
*   **On-Chain Data:** On-chain data can provide insights into large transactions and wallet activity, which can be used to track institutional activity.
*   **Data Aggregators:** Data aggregators like Glassnode and CryptoQuant provide on-chain data and analytics that can be used to track institutional activity.
*   **News and Social Media:** Keep an eye on news and social media for reports of large institutional trades or investments.

**Key Considerations:**

*   **Real-Time Data:** Tracking institutional order flow requires real-time data feeds.
*   **Experience:** It takes time and practice to become proficient in identifying and interpreting institutional order flow.
*   **Context:** Always consider the overall market context when analyzing institutional order flow.
*   **Risk Management:** Use appropriate risk management techniques when trading based on institutional order flow analysis.

### Prompt 7: "Introduce the concept of supply and demand zones and how to identify them on a price chart. Explain how these zones can be used to anticipate potential price reversals or continuations. Provide examples of how to trade based on supply and demand zones and discuss the differences between these zones and traditional support/resistance levels."

**Supply and Demand Zones Explained:**

Supply and demand zones are areas on a price chart where significant buying or selling pressure has occurred. These zones represent areas where the market is likely to react in the future. Unlike traditional support and resistance levels, which are often based on horizontal lines, supply and demand zones are typically represented as rectangular areas that encompass a range of prices.

**Identifying Supply and Demand Zones:**

*   **Supply Zones:** These are areas where selling pressure is strong. They are typically found at the top of price rallies or near previous highs. Supply zones are often characterized by a sharp price decline after a period of consolidation.
*   **Demand Zones:** These are areas where buying pressure is strong. They are typically found at the bottom of price declines or near previous lows. Demand zones are often characterized by a sharp price increase after a period of consolidation.
*   **Price Action:** Look for areas where price has moved sharply away from a consolidation area. These areas are likely to be supply or demand zones.
*   **Volume:** Look for areas with high volume, as these areas are more likely to be significant supply or demand zones.
*   **Timeframe:** Identify supply and demand zones on different timeframes to get a comprehensive view of market activity.

**Using Supply and Demand Zones to Anticipate Price Movements:**

*   **Potential Reversals:** When price approaches a supply zone, it is likely to reverse and decline. When price approaches a demand zone, it is likely to reverse and increase.
*   **Potential Continuations:** When price breaks through a supply zone, it is likely to continue to rise. When price breaks through a demand zone, it is likely to continue to decline.
*   **Entry and Exit Points:** Use supply and demand zones to find potential entry and exit points for trades.

**Trading Based on Supply and Demand Zones:**

*   **Long Entry:** Enter a long position when price approaches a demand zone, expecting a bounce.
*   **Short Entry:** Enter a short position when price approaches a supply zone, expecting a decline.
*   **Breakout Trade:** Enter a long position when price breaks above a supply zone or a short position when price breaks below a demand zone.

**Differences Between Supply/Demand Zones and Traditional Support/Resistance Levels:**

| Feature             | Supply/Demand Zones                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          
```

