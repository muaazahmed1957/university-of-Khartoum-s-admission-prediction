# University of Khartoum Admission Cutoff Prediction

A small proof-of-concept machine learning project that predicts the yearly
admission cutoff score at the University of Khartoum (UofK) for **Medicine**
and **Dentistry**, using the average score of the top 100 students in the
Sudanese Secondary Certificate.

> **This is a small experiment, not a production model.** It was built on a
> very small, self-collected dataset to test whether the idea can work. The
> results suggest it is promising and would benefit from more data.

## Motivation

Every year, families and students mostly rely on the previous year's
admission results to decide which programs to apply for. But cutoffs can
change a lot from one year to the next, as happened in 2020, and many
students were surprised by their results.

The long-term goal of this project is to give students a data-based estimate
of the coming cutoff, so they can choose programs they have a realistic
chance of being accepted into. The current version is only a first test of
whether this is possible.

## Scope

The project covers two programs at UofK:

- Medicine
- Dentistry

I chose them because they usually require the highest scores for admission
at UofK, so the performance of the top students in the country is a
relevant signal for predicting their cutoffs.

## Data

No public dataset exists for this problem, so I collected the data myself,
which was difficult. The data is **yearly**: one row per year, with the
national exam statistics, the average score of the top 100 students, and the
UofK cutoff for each program.

- **Feature used:** the average of the top 100 scores each year. More
  features could be derived from the top-100 scores, but this project uses
  only the average.
- **Target:** the UofK admission cutoff for Medicine and for Dentistry.
- **Training years:** 2016-2022 (7 years).
- **Excluded years:** 2023-2024, because the number of accepted students
  dropped by about half due to the war in Sudan.
- **Test year:** 2025.

The dataset is very small and **is not publicly available**, so it is not
included in this repository.

## Method

For each program, a simple linear regression was trained on the top-100
average. The feature was chosen because of its high correlation with the
cutoff in the training years:

| Program   | Correlation with top-100 average |
|-----------|----------------------------------|
| Medicine  | 0.92                             |
| Dentistry | 0.86                             |

## Results

Predictions for 2025, compared with the actual cutoffs. The baseline simply
predicts the average cutoff of the training years, to check that the model
adds value beyond that.

| Program   | Training years  | Predicted | Actual | Model error | Baseline error |
|-----------|-----------------|-----------|--------|-------------|----------------|
| Medicine  | all 2016-2022   | 94.78     | 94.7   | 0.08        | 0.86           |
| Medicine  | without 2020    | 94.48     | 94.7   | 0.22        | 1.17           |
| Dentistry | all 2016-2022   | 93.23     | 92.3   | 0.93        | 0.06           |
| Dentistry | without 2020    | 92.43     | 92.3   | 0.13        | 0.45           |

**Medicine** was predicted closely and beats the baseline with or without
2020.

**Dentistry** is more sensitive to the training data. With all years
included, the model is less accurate than the baseline. 2020 is an outlier
year (the highest top-100 average in the data, 97.0, together with unusually
high cutoffs), and a single year like this can strongly change the fitted
line. Without 2020 the model predicts 92.43 against an actual 92.3 and beats
the baseline.

Both versions are reported so the effect of that one year is visible.

## Limitations

- Only 7 training years and a single test year (2025), so these results show
  that the approach is feasible, not that it is reliable.
- A single unusual year, such as 2020, can noticeably change the results.
- Only one feature (the top-100 average) is used.
- Admission cutoffs also depend on factors that are not in the data, such as
  seat capacity, quotas, and policy changes.
- The dataset is not public, so the notebook cannot be re-run from this
  repository alone.

## Future work

- Collect more years and more data
- With permission from the relevant ministry, collect the planned number of
  seats per program, since seat capacity is likely an important factor in
  the cutoff that is missing from the current data
- With the same permission, use the scores of all students (anonymized)
  instead of only the top 100, which would allow richer features such as the
  full score distribution
- Build more features from the scores (for example the median, minimum, and
  spread)
- Predict a range with an uncertainty estimate instead of a single number,
  so students can judge how risky a choice is
- Add other programs and universities
- Use leave-one-year-out validation and compare with simple baselines

## Tools

Python, Pandas, Scikit-learn, Matplotlib, Seaborn, Google Colab
