# Can a Reply Repair a Reputation?

## Service Recovery in Hawaii Google Reviews

**Mac Dang**

This project explores whether responding to a 1-star Google review is associated with what happens next for a business.

## Introduction

The Hawaii Google Maps Reviews dataset contains **1,504,347 customer reviews** from **21,507 businesses**. It includes customer star ratings, written reviews, timestamps, and business responses. These features make it possible to study how businesses react to extremely negative feedback and what happens immediately afterward.

My project focuses on the following question:

**After a 1-star Google review, is a business's next customer review rated higher when the business responds before that next review than when it does not?**

This question matters because online reviews can strongly influence how customers perceive a business. If responding to negative feedback is associated with better ratings afterward, businesses may have an incentive to actively engage with dissatisfied customers. Since this is observational data, however, my analysis looks for an **association** rather than claiming that a business response directly causes a higher future rating.

The main columns relevant to this analysis are:

| Column | Description |
| --- | --- |
| `gmap_id` | Identifies the business being reviewed. |
| `rating` | The customer's rating from 1 to 5 stars. |
| `time` | The timestamp indicating when the customer review was posted. |
| `text` | The written feedback included with the review, if any. |
| `resp` | The business's response, including its response time and text, if a response was posted. |

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

Before analyzing the reviews, I cleaned and reorganized the data so that each 1-star review could be connected to what happened immediately afterward.

I performed the following cleaning steps:

- Converted the review timestamps from Unix timestamps into readable datetime values.
- Extracted the timestamp of each business response from the `resp` column.
- Created an indicator showing whether a business responded to a review.
- Treated missing review text as having a text length of 0.
- Sorted reviews chronologically within each business using `gmap_id` and review time.
- Used the chronological ordering to identify the rating and time of the next customer review for each business.
- Created `responded_before_next`, which indicates whether the business responded to the 1-star review before the next customer review was posted.
- Restricted the main analysis to 1-star reviews that actually had a subsequent customer review.

These steps were important because simply knowing that a business eventually responded is not enough for my research question. The response had to occur **before** the next customer review in order to compare what happened next for businesses that responded versus those that did not.

### Cleaned Data Preview

Below are the first five rows of the cleaned dataset used for the main analysis.

| gmap_id | rating | review_datetime | text_length | next_rating | response_group |
|:---|---:|:---|---:|---:|:---|
| 0x0:0x9edcb14b0cf1ec04 | 1 | 2017-05-03 19:46:01.266000 | 145 | 5 | No response before next review |
| 0x0:0x9edcb14b0cf1ec04 | 1 | 2018-05-17 02:19:44.124000 | 0 | 1 | Responded before next review |
| 0x0:0x9edcb14b0cf1ec04 | 1 | 2018-06-11 23:31:36.087000 | 38 | 1 | No response before next review |
| 0x0:0x9edcb14b0cf1ec04 | 1 | 2018-06-12 01:45:24.827000 | 128 | 1 | Responded before next review |
| 0x0:0x9edcb14b0cf1ec04 | 1 | 2019-03-24 02:15:12.444000 | 236 | 5 | No response before next review |

The cleaned table links each 1-star review to the next customer rating and indicates whether the business responded before that next review occurred.

### Univariate Analysis

The distribution of subsequent ratings is concentrated toward the positive end of the scale. In particular, 5-star reviews make up the largest share of next reviews following a 1-star review, showing that one extremely negative review does not necessarily mean the next customer will also report a poor experience.

<iframe src="{{ '/assets/next-rating-distribution.html' | relative_url }}" width="800" height="600" frameborder="0"></iframe>

### Bivariate Analysis

Businesses that responded before the next review had a slightly more positive subsequent rating distribution. In particular, about **56%** of the next reviews were 5 stars for the responded group, compared with about **50%** for the no-response group. This suggests a small positive association between responding to a 1-star review and the rating of the following review.

<iframe src="{{ '/assets/response-vs-next-rating.html' | relative_url }}" width="900" height="550" frameborder="0"></iframe>

### Interesting Aggregates

Grouping the 1-star reviews by whether the business responded before the next review gives the following summary:

| response_group | number_of_reviews | mean_next_rating | median_next_rating |
|:---|---:|---:|---:|
| No response before next review | 30199 | 4.10 | 4 |
| Responded before next review | 1946 | 4.20 | 5 |

The responded-before-next group had a slightly higher **mean next rating of 4.20 compared with 4.10** for the no-response group. Its median next rating was also higher, at **5 stars compared with 4 stars**. This supports the pattern seen in the bivariate plot, although the two groups are very different in size.

## Assessment of Missingness

### MNAR Analysis

I believe the missingness in the `text` column could be **MNAR (Missing Not At Random)**. Whether a reviewer chooses to write text may depend on information that is not fully observed in the dataset, such as how strongly they felt about their experience or whether they felt a written explanation was necessary.

For example, some users may leave only a star rating when they do not have much additional feedback to provide. Additional information about why each reviewer chose to leave the text field blank, or about their general tendency to write detailed reviews, could help explain the missingness. If that information were observed and accounted for, the missingness could potentially be considered MAR instead of MNAR.

### Missingness Dependency: Rating

To test whether missingness in the `text` column depends on the review's star rating, I performed a permutation test.

**Null Hypothesis:** The missingness of `text` does not depend on the review's star rating. Any difference in average rating between reviews with and without text is due to random chance.

**Alternative Hypothesis:** The missingness of `text` does depend on the review's star rating.

I used the **absolute difference in mean rating** between reviews with missing text and reviews with non-missing text as the test statistic.

The observed difference was approximately **0.0228 stars**. None of the 500 permutations produced a difference at least this large, corresponding to an empirical p-value of **less than 0.002**.

At a significance level of **0.05**, I reject the null hypothesis. There is evidence that whether review text is missing is associated with the review's star rating.

## Hypothesis Testing

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis
