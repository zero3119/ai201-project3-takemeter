# TakeMeter Baseline Results

## Overall Baseline Performance

- **Baseline accuracy:** 0.677
- **Evaluated on:** 31 / 31 parseable responses
- **Unparseable responses:** 0

## Per-Class Metrics

| Label | Precision | Recall | F1-score | Support |
|---|---:|---:|---:|---:|
| analysis | 1.00 | 0.43 | 0.60 | 7 |
| recommendation | 1.00 | 0.50 | 0.67 | 8 |
| reaction | 0.44 | 1.00 | 0.62 | 8 |
| question | 1.00 | 0.75 | 0.86 | 8 |
| accuracy |  |  | 0.68 | 31 |
| macro avg | 0.86 | 0.67 | 0.68 | 31 |
| weighted avg | 0.86 | 0.68 | 0.69 | 31 |

## Notes

The zero-shot baseline reached **67.7% accuracy** on the test set. All 31 responses were parseable, which means the prompt returned valid labels consistently.

The baseline performed best on **question**, with an F1-score of **0.86**. It struggled more with **analysis** and **recommendation**, especially in recall. The model also overpredicted **reaction**, shown by reaction having perfect recall but lower precision.
