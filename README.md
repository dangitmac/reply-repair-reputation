# Can a Reply Repair a Reputation?

## Service Recovery in Hawaii Google Reviews 
## 🌴 🌺 🌊 🏝️ 🐬 🌸 🏄‍♀️ 🥥 🐚 🫧 🌋 🍍 🐢

**Mac Dang**

This project explores whether responding to a 1-star Google review is associated with what happens next for a business, and was made for UCSD's DSC 80's final course project in Summer Session '26.

## Introduction

The Hawaii Google Maps Reviews dataset contains **1,504,347 customer reviews** from **21,507 businesses**. It includes customer star ratings, written reviews, timestamps, and business responses. These features make it possible to study how businesses react to extremely negative feedback and what happens immediately afterward.

### Data Source

This project uses the Hawaii subset of the McAuley Lab Google Local Data (2021), including the Hawaii 10-core review data and accompanying Hawaii business metadata. The dataset contains **1,504,347 reviews** and metadata for **21,507 businesses**.

### References

Li, J., Shang, J., & McAuley, J. (2022). *UCTopic: Unsupervised Contrastive Learning for Phrase Representations and Topic Mining.* ACL.

Yan, A., He, Z., Li, J., Zhang, T., & McAuley, J. (2023). *Personalized Showcases: Generating Multi-Modal Explanations for Recommendations.* SIGIR.

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

### Missingness Dependency: Pictures

To test whether missingness in the `text` column depends on whether a review contains pictures, I performed a permutation test.

**Null Hypothesis:** The missingness of `text` does not depend on whether the review contains pictures. Any difference in missing-text rates between reviews with and without pictures is due to random chance.

**Alternative Hypothesis:** The missingness of `text` does depend on whether the review contains pictures.

I used the **absolute difference in the proportion of missing text** between reviews with pictures and reviews without pictures as the test statistic.

The observed difference was approximately **0.362, or 36.2 percentage points**. The permutation test produced a **p-value of approximately 0.002**.

At a significance level of **0.05, I reject the null hypothesis**. There is strong evidence that the missingness of `text` depends on whether a review contains pictures. Reviews without pictures were substantially more likely to have missing written text.

### Missingness Dependency: Weekend Status

I also tested whether the missingness of `text` depends on whether a review was posted on a weekend or a weekday.

**Null Hypothesis:** The missingness of `text` does not depend on whether the review was posted on a weekend or weekday. Any observed difference in missing-text rates is due to random chance.

**Alternative Hypothesis:** The missingness of `text` does depend on whether the review was posted on a weekend or weekday.

I used the **absolute difference in the proportion of missing text** between weekend and weekday reviews as the test statistic.

The observed difference in missing-text rates was approximately **0.020, or 2.0 percentage points**. The permutation test produced a **p-value of approximately 0.078**.

At a significance level of **0.05**, I fail to reject the null hypothesis. There is not sufficient evidence that the missingness of `text` depends on whether a review was posted on a weekend or weekday.

### Missingness Visualization

Reviews without pictures had a much higher rate of missing written text than reviews with pictures. This suggests that the presence of pictures is strongly associated with whether a reviewer also provides written feedback.

<iframe src="{{ '/assets/missingness-pictures.html' | relative_url }}" width="800" height="600" frameborder="0"></iframe>

## Hypothesis Testing

I tested whether businesses that respond to a 1-star review before the next customer review tend to receive a higher rating on that next review.

**Null Hypothesis:** Among 1-star reviews that have a subsequent review, the mean rating of the next review is the same whether or not the business responds before that next review. Any observed difference is due to random chance.

**Alternative Hypothesis:** Among 1-star reviews that have a subsequent review, the mean rating of the next review is higher when the business responds before that next review.

**Test Statistic:** Mean next rating for the responded-before-next group minus mean next rating for the no-response-before-next group.

I used a **one-sided permutation test** with a significance level of **0.05**. A one-sided test is appropriate because my research question specifically asks whether responding is associated with a **higher** subsequent rating.

The observed difference in mean next rating was approximately **0.095 stars**, with the responded-before-next group having the higher mean. None of the 1000 permutations produced a difference at least this large. Using the permutation-test correction, the empirical **p-value was approximately 0.001**.

At a significance level of **0.05**, I reject the null hypothesis. The results provide strong evidence that, among 1-star reviews with a subsequent review, businesses that respond before the next review tend to have a higher mean next rating than businesses that do not respond before the next review.

Because this is observational data, this result shows an **association** and does not establish that the business response caused the higher subsequent rating.

## Framing a Prediction Problem

### Prediction Problem

I will predict whether a business will respond to a 1-star review before the next customer review is posted.

This is a **binary classification** problem. The response variable is `responded_before_next`, where `True` means the business responded before the next review and `False` means it did not.

I chose this response variable because predicting whether a negative review will receive a timely business response could help identify which reviews are most likely to receive service recovery attention.

### Evaluation Metric

I will evaluate the model using the **F1-score**. The responded-before-next group makes up only about **6%** of the observations, so the classes are highly imbalanced. Accuracy could therefore be misleading because a model could achieve high accuracy simply by predicting the majority class most of the time.

F1-score balances **precision and recall**, making it more appropriate for evaluating how well the model identifies the relatively uncommon reviews that actually receive a response.

### Time of Prediction

The model uses only information that is available when the 1-star review is posted, such as characteristics of the review and its timestamp.

For this project, evaluation is performed retrospectively on the subset of 1-star reviews for which a later customer review is observed, because `responded_before_next` requires a subsequent review in order to define the outcome.

Therefore, this model should be interpreted as a **conditional retrospective prediction model** rather than as a fully deployable real-time system for every newly posted 1-star review.

I do not use the next customer rating, the time of the next review, or any other future information as model features.

## Baseline Model

### Baseline Model Features

For my baseline model, I used two features that are available immediately after the 1-star review is posted:

- **Review text length:** a **quantitative** feature created from `text`. Missing review text was treated as having a length of 0, and this feature was standardized before modeling.
- **Whether the review contains pictures:** a **binary nominal** feature created from `pics`, where the feature indicates whether pictures were included with the review.

I used a **logistic regression classifier** because the response variable is binary. All feature transformations and model training were performed together in a single `sklearn` Pipeline.

### Baseline Model Performance

The baseline model achieved an **F1-score of 0.000 on the training set and 0.000 on the unseen test set**.

Although the response rate was about 6%, the model predicted **zero observations** as belonging to the positive class. As a result, it completely failed to identify reviews that received a business response before the next review.

I do not consider this baseline model effective. Its performance shows that the two baseline features alone are not enough to identify the minority response class, so the final model will address the class imbalance and incorporate additional relevant features.

## Final Model

### Final Model Improvements

My baseline model used review text length and whether the review contained pictures. For the final model, I kept those features and added two new features based on when the review was posted:

- **Hour of day:** a quantitative feature derived from the review timestamp. Businesses may be more likely to notice and respond to reviews posted during hours when staff are actively monitoring their online presence.
- **Weekend status:** a binary nominal feature indicating whether the review was posted on Saturday or Sunday. Response behavior may differ on weekends because staffing and business operations can differ from weekdays.

I continued using **logistic regression**, but addressed the strong class imbalance by tuning the model's class weighting.

### Hyperparameter Tuning

I used **GridSearchCV with 5-fold cross-validation** and F1-score as the scoring metric.

I tuned:

- **`C`**, the logistic regression regularization parameter, using values `0.01`, `0.1`, `1`, and `10`.
- **`class_weight`**, comparing no class weighting with `balanced`.

The best-performing combination was:

- **`C = 0.1`**
- **`class_weight = 'balanced'`**

The best cross-validation F1-score was approximately **0.120**.

### Final Model Performance

The final model achieved an **F1-score of approximately 0.125 on the training set and 0.134 on the unseen test set**.

This improved substantially over the baseline model's test F1-score of **0.000**, which occurred because the baseline predicted no observations as belonging to the positive class.

The final model is still limited by the difficulty of predicting a relatively rare response event, but it is more useful than the baseline because it is able to identify some reviews that receive a business response. The similar training and testing F1-scores also suggest that the model is not severely overfitting.

## Fairness Analysis

### Fairness Question

I examined whether my final model performs differently for reviews with written feedback compared with reviews that contain no written text.

- **Group X:** Reviews with no written text.
- **Group Y:** Reviews with written text.

I used **F1-score** as the evaluation metric because it is the same metric used to evaluate my prediction model and is appropriate for the strongly imbalanced response variable.

### Hypotheses

**Null Hypothesis:** The final model performs equally well for reviews with and without written text. Any observed difference in F1-score between the two groups is due to random chance.

**Alternative Hypothesis:** The final model has a lower F1-score for reviews with no written text than for reviews with written text.

**Test Statistic:** F1-score for reviews with written text minus F1-score for reviews with no written text.

I used a significance level of **0.05**.

### Fairness Analysis Results

The final model had an F1-score of **0.000** for reviews with no written text and approximately **0.139** for reviews with written text, giving an observed difference of approximately **0.139**.

Using 1000 permutations, the permutation test produced a p-value of approximately **0.001**.

At a significance level of **0.05**, I reject the null hypothesis. The results provide strong evidence that the model performs worse for reviews with no written text than for reviews that contain written feedback.

This indicates a meaningful performance disparity between the two groups. One possible explanation is that review text length is one of the model's features, so reviews without written feedback provide the model with less information for identifying whether a business will respond.
