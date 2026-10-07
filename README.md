# Information Diffusion to Price Action Model

## 1. Overview

This project investigates how publicly released information is incorporated into asset prices and whether the market response differs depending on the nature of the information event.

The core approach is an **event-study framework** based on Cumulative Abnormal Returns (CAR). For each information event, the model estimates the expected relationship between the individual security and the broader market, calculates abnormal returns around the event, and compares the resulting event CAR with CARs observed during non-event periods.

The analysis is performed using two measures of abnormal returns:

1. **Market-adjusted returns**
2. **Beta-adjusted returns**

The resulting CAR paths are then analyzed both at the individual-event level and in aggregate across different categories of events, such as FOMC **rate hikes, holds, and cuts**.

---

## 2. Model Structure

The overall workflow consists of the following stages:

1. Identify an information event and its timestamp.
2. Construct a pre-event estimation window.
3. Estimate the stock's market beta.
4. Calculate abnormal returns around the event.
5. Calculate Cumulative Abnormal Returns (CAR).
6. Construct a non-event CAR benchmark.
7. Compare event and non-event CAR paths.
8. Aggregate CARs across similar event types.
9. Analyze the resulting price-response patterns.

---

## 3. Beta Calculation

Before calculating beta-adjusted abnormal returns, a beta is estimated separately for each stock-event pair.

The beta calculation is implemented through the `beta_calculation` function.

### 3.1 Estimation Window

For each event, the model uses **30 trading days of data preceding the event** to estimate the stock's relationship with the market.

The estimation window is designed to represent the normal relationship between the security and the market before the information event occurs.

An exclusion rule is applied when necessary to prevent contamination of the estimation window by another event. In particular, if a previous event falls within the relevant lookback period, that period can be excluded from the beta estimation so that the estimated beta is not influenced by another information shock.

### 3.2 Beta Estimation

The model uses returns from:

- The individual stock being analyzed
- The S&P 500 (`SPX500`) as the market benchmark

Beta is calculated using the covariance between stock returns and market returns divided by the variance of market returns:

\[
\beta_i =
\frac{\operatorname{Cov}(R_i,R_m)}
{\operatorname{Var}(R_m)}
\]

where:

- \(R_i\) = return of stock \(i\)
- \(R_m\) = S&P 500 return
- \(\beta_i\) = estimated market sensitivity of stock \(i\)

This produces an event-specific beta that is subsequently used to calculate beta-adjusted abnormal returns.

---

## 4. Cumulative Abnormal Returns

The primary measure used to evaluate the market response to an information event is **Cumulative Abnormal Return (CAR)**.

Two approaches are used to calculate abnormal returns.

### 4.1 Mean-Adjusted CAR

The first approach uses the historical mean return of the stock as the benchmark for its expected return.

The abnormal return is therefore defined as the difference between the observed return and the stock's expected/mean return:

\[
AR_t = R_t - E[R_t]
\]

The abnormal returns are then accumulated over the event window:

\[
CAR_{[t_1,t_2]}
=
\sum_{t=t_1}^{t_2} AR_t
\]

This provides a measure of how much the stock deviated from its normal return behavior around the event.

### 4.2 Beta-Adjusted CAR

The second approach explicitly accounts for the stock's exposure to the broader market.

Using the previously estimated beta, the market component of the stock's return is removed to obtain an abnormal return:

\[
AR_t =
R_{i,t} - \beta_i R_{m,t}
\]

where:

- \(R_{i,t}\) = stock return at time \(t\)
- \(R_{m,t}\) = S&P 500 return at time \(t\)
- \(\beta_i\) = beta estimated from the pre-event window

The abnormal returns are then accumulated:

\[
CAR_{[t_1,t_2]}
=
\sum_{t=t_1}^{t_2} AR_t
\]

The beta-adjusted approach therefore attempts to isolate the component of the stock's price movement that cannot be explained by its normal sensitivity to the market.

---

## 5. Event CAR

For each information event, the model calculates CAR over the defined event window.

The event window captures the price behavior immediately surrounding the release of the information.

The resulting CAR is stored as a time series, allowing the model to examine not only the final cumulative response but also **how the response develops through time**.

This produces an individual CAR path for every event.

---

## 6. Non-Event CAR Benchmark

To determine whether the observed price movement around an information event is unusual, the model also constructs **non-event CAR paths**.

The purpose of the non-event benchmark is to establish what a comparable period of market behavior looks like in the absence of the information event.

Event CARs can then be compared against these non-event CARs to determine whether the observed response appears distinct from normal price fluctuations.

This comparison is particularly important because a positive or negative CAR by itself does not necessarily imply that the information event caused the observed movement.

---

## 7. CAR Path Visualization

For each event, the model plots the cumulative abnormal return through the event window.

These plots allow the analysis to examine:

- The direction of the price response
- The magnitude of the response
- The speed at which information is incorporated into prices
- Whether the response occurs immediately or gradually
- Whether the initial response reverses
- Whether abnormal returns persist after the information release

Both **mean-adjusted** and **beta-adjusted** CAR paths are examined.

---

## 8. Aggregation by Event Type

After calculating CAR for individual events, events are grouped according to their information type.

For the FOMC dataset, events are categorized into:

- **Rate hikes**
- **Rate holds**
- **Rate cuts**

CAR paths are then averaged within each category.

This produces an **average CAR trajectory** for each type of monetary-policy event.

The aggregated analysis allows the model to identify systematic differences in how markets respond to different forms of information rather than relying exclusively on individual event observations.

---

## 9. Market-Adjusted vs. Beta-Adjusted Analysis

The model compares the two abnormal-return methodologies directly.

For each event category, the analysis plots:

- Mean-adjusted CAR
- Beta-adjusted CAR
- Non-event CAR

This allows the robustness of the observed information-response pattern to be examined under different definitions of expected return.

If similar patterns appear under both adjustment methodologies, the observed response provides stronger evidence of a systematic relationship between the information event and subsequent price behavior.

---

## 10. Overall Analytical Pipeline

The complete process can therefore be summarized as:

**Information Event**

↓

**30-Day Pre-Event Estimation Window**

↓

**Stock & S&P 500 Returns**

↓

**Beta Estimation**

↓

**Event-Window Returns**

↓

**Abnormal Return Calculation**

↓

**Mean-Adjusted / Beta-Adjusted CAR**

↓

**Non-Event CAR Benchmark**

↓

**Individual Event CAR Paths**

↓

**Aggregation by Event Type**

↓

**Comparison of Event vs. Non-Event CAR**

↓

**Analysis of Information Diffusion into Prices**

---

## 11. Objective

The objective of the model is not simply to determine whether an information event is associated with a positive or negative return.

Instead, the analysis aims to investigate the **dynamics of information diffusion into prices**:

> How quickly, strongly, and persistently does new information become incorporated into security prices, and does this response differ across different types of information events?

The event-level CAR paths and their aggregated counterparts provide the primary framework for answering this question.
