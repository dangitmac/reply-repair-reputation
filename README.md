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

## Assessment of Missingness

## Hypothesis Testing

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis
