# Fall Detection: Results Tables

| Current specialist-free Tier 1 + Tier 2 | Recordings | Accuracy | Precision | Recall | F1 | Specificity | ADL alarms/hour |
|---|---:|---:|---:|---:|---:|---:|---:|
| MobiFall | 630 | 55.71% | 51.01% | 79.17% | 62.04% | 35.96% | 139.04 |
| UP-Fall | 559 | 83.18% | 77.66% | 88.63% | 82.78% | 78.62% | 35.74 |
| EVIORA-HAR | 1,950 | 93.08% | 82.34% | 83.80% | 83.06% | 95.43% | 16.21 |
| KFall | Source development | Not available | — | — | — | — | — |
| SisFall | Source development | Not available | — | — | — | — | — |

| Dataset | Method | Accuracy | Precision | Recall | F1 | Specificity |
|---|---|---:|---:|---:|---:|---:|
| MobiFall | Tier 1 only | 51.59% | 47.40% | 53.82% | 50.41% | 49.71% |
| MobiFall | Fixed numerical review | 57.46% | 52.18% | 82.99% | 64.08% | 35.96% |
| MobiFall | Learned Tier 2 | 55.71% | 51.01% | 79.17% | 62.04% | 35.96% |
| UP-Fall | Tier 1 only | 81.22% | 78.85% | 80.39% | 79.61% | 81.91% |
| UP-Fall | Fixed numerical review | 82.65% | 75.99% | 90.59% | 82.65% | 75.99% |
| UP-Fall | Learned Tier 2 | 83.18% | 77.66% | 88.63% | 82.78% | 78.62% |
| EVIORA-HAR | Tier 1 only | 90.82% | 93.20% | 58.99% | 72.25% | 98.91% |
| EVIORA-HAR | Fixed numerical review | 94.51% | 82.88% | 91.90% | 87.15% | 95.18% |
| EVIORA-HAR | Learned Tier 2 | 93.08% | 82.34% | 83.80% | 83.06% | 95.43% |

| Current learned Tier 2 | True positives | False negatives | False positives | True negatives | Correct / total |
|---|---:|---:|---:|---:|---:|
| MobiFall | 228 | 60 | 219 | 123 | 351 / 630 |
| UP-Fall | 226 | 29 | 65 | 239 | 465 / 559 |
| EVIORA-HAR | 331 | 64 | 71 | 1,484 | 1,815 / 1,950 |

| Current learned Tier 2 | ADL alarm edges | ADL hours | ADL alarms/hour | Tier 2 interventions | Recordings with Tier 2 intervention |
|---|---:|---:|---:|---:|---:|
| MobiFall | 325 | 2.34 | 139.04 | 4,706 | 374 / 630 (59.37%) |
| UP-Fall | 133 | 3.72 | 35.74 | 711 | 139 / 559 (24.87%) |
| EVIORA-HAR | 74 | 4.56 | 16.21 | 658 | 338 / 1,950 (17.33%) |

| Earlier architecture with CNN–LSTM specialist | Method | Detected falls / eligible | Fall recall | ADL alarms/hour | Accuracy | F1 |
|---|---|---:|---:|---:|---:|---:|
| KFall | Tier 1 only | 2,328 / 2,341 | 99.44% | 2.43 | Not reported | Not reported |
| KFall | Fixed confidence gate | 2,332 / 2,341 | 99.62% | 2.02 | Not reported | Not reported |
| KFall | Fixed one-hop wait | 2,330 / 2,341 | 99.53% | 2.63 | Not reported | Not reported |
| KFall | Learned controller | 2,333 / 2,341 | 99.66% | 2.23 | Not reported | Not reported |
| SisFall | Tier 1 only | 1,747 / 1,798 | 97.16% | 1.37 | Not reported | Not reported |
| SisFall | Fixed confidence gate | 1,762 / 1,798 | 98.00% | 2.21 | Not reported | Not reported |
| SisFall | Fixed one-hop wait | 1,747 / 1,798 | 97.16% | 1.75 | Not reported | Not reported |
| SisFall | Learned controller | 1,762 / 1,798 | 98.00% | 2.05 | Not reported | Not reported |

| Evaluation qualification | Result |
|---|---|
| Current external results | Recording-level; an alarm anywhere in a fall recording counts as detection. |
| EVIORA-HAR coverage | 1,950 of 1,954 identified recordings included; four had insufficient usable sensor data. |
| Earlier KFall result | Timed event-matching endpoint. |
| Earlier SisFall result | Whole-trial endpoint; an alarm before the fall can count. |
| Architecture comparison | Earlier KFall/SisFall results and current specialist-free results use different architectures and evaluation endpoints. |
