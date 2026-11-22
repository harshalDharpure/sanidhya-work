# CrashX: complete saved comparison results and NLI

Status: historical original-split results and exploratory diagnostic-validation results. The original split has confirmed source-footage overlap; the candidate-quarantined split is not a certified clean benchmark. No result establishes clean generalization or conference readiness.

NLI compares generated text against reference text. Every available reporting NLI score is included below. Timestamp-only temporal heads and priors produce no language, so NLI is **N/A**, not zero. NLI is not a direct video factuality test.

Historical original split uses 150 saved test clips; diagnostic localization uses 1,101 positive-duration training clips and 113 validation clips. These cohorts, settings and score definitions must not be mixed. NLI historical checkpoints are not uniformly pinned; matched diagnostic NLI is pinned.

## Paper highlights: strongest supported findings

These are candidates for a paper, **not claims of acceptance, state-of-the-art performance, independent generalization or proven factuality**. Each result must retain its cohort and limitations. Report the negative findings and full control comparisons alongside positive results.

| Finding | Evidence | Appropriate paper wording | Required limitation |
| --- | --- | --- | --- |
| **Matched reporting improvement** | On 113 diagnostic validation clips, severity accuracy is **0.5310 vs 0.0088** and omission cost is **0.0782 vs 0.3590** for the historical adapted Qwen versus zero-shot Qwen under matched inference settings. | "On the diagnostic validation cohort, the adapted model achieved higher reference severity agreement and lower omission cost than zero-shot Qwen." | Candidate-quarantined split is uncertified; historical adapter predates review and source overlap remains unresolved. |
| **Matched text agreement** | Adapted vs zero-shot: BLEU-4 **0.1387 vs 0.0157**, Cider+PTB **0.0951 vs 0.0027**, BERTScore **0.6879 vs 0.5085**. | "The adapted reports had higher reference text-similarity scores under the specified scorers." | Similarity is not expert-verified factuality; Cider+PTB differs from the historical TF-cosine proxy. |
| **Full-report NLI, with a mixed result** | Full-report NLI score **0.3615 vs 0.1184** for adapted vs zero-shot; explanation NLI **0.0671 vs 0.0865**. | "The adapted model had a higher full-report NLI score, while its explanation NLI score was lower." | NLI compares reference and generated text; contradiction increased in the adapted condition and does not verify the video. |
| **Source and metric audit** | Confirmed source-footage overlap includes train-test contamination; all six saved primary CIDEr-labeled values reproduce a legacy TF-cosine proxy. | "We document source overlap and distinguish the historical text-similarity proxy from a separate Cider+PTB replay." | Review is incomplete; source candidates are not automatically duplicates. |
| **Temporal-prior finding** | The train-selected 2–4 s prior achieves diagnostic validation tIoU **0.5649**, above the best tested fine-tuned visual estimate **0.4690**. | "A training-selected constant window outperformed the evaluated visual temporal heads on the diagnostic validation cohort." | Split is uncertified; fine-tuned checkpoints were selected on this validation set. This is evidence against claiming demonstrated visual grounding. |
| **Decoder uncertainty** | Historical TCD-minus-greedy paired intervals include zero for severity, tIoU and omission cost. | "TCD produced numerical changes, but the paired analysis did not establish an advantage over greedy decoding." | Original split is contaminated; clip bootstrap does not account for source dependence or seed variability. |

The matched severity difference is **+0.5221** with a descriptive 95% paired video-bootstrap interval **[0.4336, 0.6106]**. The omission-cost difference is **-0.2808** with interval **[-0.3089, -0.2513]**. These intervals do not account for source dependence, training-seed variation or multiple comparisons and should not be presented as confirmatory statistical proof.

## 1. Matched reporting on diagnostic validation

Same 113 clips, Qwen backbone, prompt, frame sampling and greedy decoding; the adapted condition uses a verified historical adapter. Historical training occurred before source review, and source independence is unresolved.

| Condition | Severity ↑ | tIoU ↑ | H ↓ | O ↓ | BLEU ↑ | ROUGE ↑ | METEOR ↑ | Cider+PTB ↑ | BERTScore ↑ |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| zero_shot | 0.0088 | 0.0142 | 0.2631 | 0.3590 | 0.0157 | 0.1646 | 0.2079 | 0.0027 | 0.5085 |
| historical_finetuned | **0.5310** | **0.4204** | **0.1876** | **0.0782** | **0.1387** | **0.3366** | **0.3689** | **0.0951** | **0.6879** |

| Condition | Exp entail ↑ | Exp contradict ↓ | Exp score ↑ | Full entail ↑ | Full contradict ↓ | Full score ↑ |
| --- | --- | --- | --- | --- | --- | --- |
| zero_shot | 0.1411 | **0.0546** | **0.0865** | 0.1753 | **0.0569** | 0.1184 |
| historical_finetuned | **0.2469** | 0.1797 | 0.0671 | **0.4721** | 0.1106 | **0.3615** |

The fine-tuned condition has higher full-report NLI score, while zero-shot has higher explanation NLI score and lower contradiction in both comparisons. No single factuality winner follows.

## 2. Historical six-model reporting comparison

Original-split results; settings are not fully matched across historical models. H and O are reference-based conflict and omission costs.

| Condition | Severity ↑ | Impact J ↑ | Vehicle J ↑ | Weather J ↑ | H ↓ | O ↓ | tIoU ↑ |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ZeroShot-Qwen2.5-VL-7B | 0.0800 | 0.2226 | 0.3492 | 0.1567 | 0.2274 | 0.4622 | 0.0125 |
| ZeroShot-Qwen2.5-VL-3B | 0.1867 | 0.2567 | 0.3756 | 0.1867 | 0.2848 | 0.4133 | 0.0096 |
| ZeroShot-Qwen2-VL-2B | 0.0867 | 0.1441 | 0.2323 | 0.1400 | 0.5638 | 0.6067 | 0.0000 |
| ZeroShot-LLaVA-Video-7B | 0.3467 | 0.2645 | 0.4182 | 0.2933 | 0.4013 | 0.3344 | 0.0239 |
| CrashLogic-7B-Greedy | 0.4600 | 0.5162 | 0.5698 | 0.8800 | 0.2227 | 0.1067 | 0.3728 |
| CrashLogic-7B-SEASON-a0.5 | 0.4867 | 0.5206 | 0.5426 | 0.8733 | 0.2120 | 0.1022 | 0.3939 |

| Condition | BLEU ↑ | ROUGE ↑ | METEOR ↑ | BERTScore ↑ | Legacy TF-cos ↑ |
| --- | --- | --- | --- | --- | --- |
| ZeroShot-Qwen2.5-VL-7B | 0.0156 | 0.1600 | 0.2027 | 0.4863 | 0.3376 |
| ZeroShot-Qwen2.5-VL-3B | 0.0192 | 0.1795 | 0.2340 | 0.5184 | 0.3944 |
| ZeroShot-Qwen2-VL-2B | 0.0182 | 0.1201 | 0.1317 | 0.4184 | 0.2215 |
| ZeroShot-LLaVA-Video-7B | 0.0257 | 0.1970 | 0.2747 | 0.5467 | 0.4493 |
| CrashLogic-7B-Greedy | 0.1424 | 0.3356 | 0.3710 | 0.6860 | 0.5080 |
| CrashLogic-7B-SEASON-a0.5 | 0.1417 | 0.3388 | 0.3690 | 0.6856 | 0.5154 |

For these six historical conditions, the saved field named CIDEr exactly matches TF-cosine; it is not standard CIDEr. The separately recomputed Cider+PTB values are listed in the v9 paper caption table.

| Condition | Exp entail ↑ | Exp contradict ↓ | Exp score ↑ | Full entail ↑ | Full contradict ↓ | Full score ↑ |
| --- | --- | --- | --- | --- | --- | --- |
| ZeroShot-Qwen2.5-VL-7B | 0.1392 | 0.0920 | 0.0472 | 0.1285 | 0.0521 | 0.0764 |
| ZeroShot-Qwen2.5-VL-3B | 0.1449 | 0.0556 | 0.0893 | 0.1522 | 0.0412 | 0.1110 |
| ZeroShot-Qwen2-VL-2B | 0.0920 | 0.0988 | -0.0068 | 0.0990 | 0.0939 | 0.0051 |
| ZeroShot-LLaVA-Video-7B | 0.1573 | 0.0607 | 0.0965 | 0.1532 | 0.0614 | 0.0919 |
| CrashLogic-7B-Greedy | 0.2235 | 0.2286 | -0.0051 | 0.4022 | 0.1555 | 0.2467 |
| CrashLogic-7B-SEASON-a0.5 | 0.2493 | 0.1926 | 0.0567 | 0.3948 | 0.1604 | 0.2344 |

### Historical CIDEr backend replay

| Condition | TF-cosine | Cider without PTB | Cider+PTB |
| --- | ---: | ---: | ---: |
| ZeroShot-Qwen2.5-VL-7B | 0.3376 | 0.0022 | 0.0021 |
| ZeroShot-Qwen2.5-VL-3B | 0.3944 | 0.0019 | 0.0012 |
| ZeroShot-Qwen2-VL-2B | 0.2215 | 0.0056 | 0.0060 |
| ZeroShot-LLaVA-Video-7B | 0.4493 | 0.0005 | 0.0005 |
| CrashLogic-7B-Greedy | 0.5080 | 0.0679 | 0.0862 |
| CrashLogic-7B-SEASON-a0.5 | 0.5154 | 0.0981 | 0.1197 |

## 3. Historical original-adapter decoder variants

Same saved adapter; inference decoding changes. Saved legacy CIDEr-labeled values in additional ablations have not all been independently replayed and are labeled legacy score.

| Decoder | Severity ↑ | Impact J ↑ | tIoU ↑ | H ↓ | O ↓ | BLEU ↑ | ROUGE ↑ | METEOR ↑ | Legacy score | BERT ↑ |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CrashLogic-7B-Greedy | 0.4600 | 0.5162 | 0.3728 | 0.2227 | 0.1067 | 0.1424 | 0.3356 | 0.3710 | 0.5080 | 0.6860 |
| CrashLogic-7B-SEASON | 0.4667 | 0.5112 | 0.3750 | 0.2273 | 0.1122 | 0.1405 | 0.3382 | 0.3649 | 0.5074 | 0.6840 |
| CrashLogic-7B-SEASON-a0.5 | 0.4867 | 0.5206 | 0.3939 | 0.2120 | 0.1022 | 0.1417 | 0.3388 | 0.3690 | 0.5154 | 0.6856 |
| CrashLogic-7B-SEASON-a1.5 | 0.4267 | 0.4787 | 0.3544 | 0.2486 | 0.1244 | 0.1333 | 0.3270 | 0.3573 | 0.4987 | 0.6806 |
| CrashLogic-7B-SEASON-a2.0 | 0.3933 | 0.4603 | 0.3558 | 0.2531 | 0.1278 | 0.1330 | 0.3193 | 0.3441 | 0.4912 | 0.6758 |
| CrashLogic-7B-SEASON-shuffle | 0.4467 | 0.4971 | 0.3950 | 0.2332 | 0.1144 | 0.1432 | 0.3402 | 0.3663 | 0.5112 | 0.6880 |
| CrashLogic-7B-SEASON-Full | 0.4467 | 0.4947 | 0.3572 | 0.2397 | 0.1189 | 0.1475 | 0.3319 | 0.3645 | 0.5126 | 0.6853 |

| Decoder | Exp entail ↑ | Exp contradict ↓ | Exp score ↑ | Full entail ↑ | Full contradict ↓ | Full score ↑ |
| --- | --- | --- | --- | --- | --- | --- |
| CrashLogic-7B-Greedy | 0.2235 | 0.2286 | -0.0051 | 0.4022 | 0.1555 | 0.2467 |
| CrashLogic-7B-SEASON | 0.2759 | 0.1856 | 0.0903 | 0.3862 | 0.1492 | 0.2370 |
| CrashLogic-7B-SEASON-a0.5 | 0.2493 | 0.1926 | 0.0567 | 0.3948 | 0.1604 | 0.2344 |
| CrashLogic-7B-SEASON-a1.5 | 0.2084 | 0.2306 | -0.0222 | 0.4104 | 0.1876 | 0.2228 |
| CrashLogic-7B-SEASON-a2.0 | 0.2183 | 0.2021 | 0.0162 | 0.3390 | 0.1906 | 0.1484 |
| CrashLogic-7B-SEASON-shuffle | 0.2159 | 0.2408 | -0.0249 | 0.3861 | 0.1815 | 0.2046 |
| CrashLogic-7B-SEASON-Full | 0.2713 | 0.2076 | 0.0638 | 0.3821 | 0.1780 | 0.2042 |

The historical paired video-bootstrap TCD-minus-greedy 95% intervals include zero for tIoU, severity and omission cost. TCD superiority is not established.

## 4. Historical architecture-v4 fine-tuning

Visual = language plus visual-merger adapters. Language-only configurations use a single seed. These runs are not matched to original TCD settings.

| Run | BLEU ↑ | ROUGE ↑ | METEOR ↑ | Legacy score | BERT ↑ | tIoU ↑ | H ↓ | O ↓ |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| language16-s42 | 0.1318 | 0.3492 | 0.3564 | 0.5081 | 0.6892 | 0.3872 | 0.2240 | 0.1033 |
| language8-s42 | 0.1301 | 0.3443 | 0.3536 | 0.5022 | 0.6881 | 0.3406 | 0.2333 | 0.1167 |
| visual16-s123 | 0.1353 | 0.3535 | 0.3596 | 0.5059 | 0.6942 | 0.3872 | 0.2267 | 0.1089 |
| visual16-s2026 | 0.1355 | 0.3512 | 0.3634 | 0.5089 | 0.6929 | 0.3872 | 0.2173 | 0.1022 |
| visual16-s42 | 0.1290 | 0.3469 | 0.3524 | 0.5065 | 0.6887 | 0.3861 | 0.2240 | 0.1022 |

| Run | Exp entail ↑ | Exp contradict ↓ | Exp score ↑ | Full entail ↑ | Full contradict ↓ | Full score ↑ |
| --- | --- | --- | --- | --- | --- | --- |
| language16-s42 | 0.1912 | 0.1630 | 0.0283 | 0.3127 | 0.1539 | 0.1589 |
| language8-s42 | 0.2347 | 0.1524 | 0.0823 | 0.3951 | 0.1221 | 0.2730 |
| visual16-s123 | 0.1683 | 0.2125 | -0.0442 | 0.3748 | 0.1528 | 0.2220 |
| visual16-s2026 | 0.2236 | 0.1732 | 0.0503 | 0.4002 | 0.1311 | 0.2691 |
| visual16-s42 | 0.1896 | 0.1825 | 0.0071 | 0.3118 | 0.1652 | 0.1465 |

## 5. Diagnostic frozen-backbone localization: every head and control

Frozen visual backbones with trained heads are supervised probes, not zero-shot reporting models. NLI is N/A for every row. The listed THR values belong to the visual head; control columns show control tIoU.

| Backbone | Head | Visual tIoU ↑ | Position tIoU | Random tIoU | Shuffled tIoU | Visual THR@.25 ↑ | Visual THR@.50 ↑ | NLI |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| videomae | mlp | 0.4352 | 0.4828 | 0.3144 | 0.3717 | 0.7257 | 0.4336 | N/A |
| videomae | bilstm | 0.4065 | 0.4828 | 0.3994 | 0.4451 | 0.7257 | 0.4071 | N/A |
| videomae | attention | 0.3957 | 0.3770 | 0.3333 | 0.3592 | 0.7345 | 0.3363 | N/A |
| videomae | attention_bilstm | 0.4278 | 0.4828 | 0.3890 | 0.4468 | 0.6814 | 0.4956 | N/A |
| timesformer | mlp | 0.4021 | 0.4828 | 0.3144 | 0.3656 | 0.6726 | 0.3717 | N/A |
| timesformer | bilstm | 0.4251 | 0.4828 | 0.3994 | 0.4284 | 0.7699 | 0.4336 | N/A |
| timesformer | attention | 0.3896 | 0.3770 | 0.3333 | 0.3707 | 0.6903 | 0.3186 | N/A |
| timesformer | attention_bilstm | 0.4327 | 0.4828 | 0.3890 | 0.4156 | 0.7522 | 0.4602 | N/A |
| qwen | mlp | 0.3412 | 0.4828 | 0.3211 | 0.3608 | 0.6195 | 0.3097 | N/A |
| qwen | bilstm | 0.4081 | 0.4828 | 0.3810 | 0.4218 | 0.7434 | 0.3274 | N/A |
| qwen | attention | 0.3372 | 0.3770 | 0.2935 | 0.3275 | 0.6637 | 0.2212 | N/A |
| qwen | attention_bilstm | 0.3995 | 0.4828 | 0.3748 | 0.4218 | 0.7257 | 0.3363 | N/A |

## 6. Diagnostic train-only priors and backbone fine-tuning

Fine-tuned checkpoints were selected on the same validation set used for the reported score. Their estimates are subject to selection optimism. NLI is N/A because these models output intervals.

| Method | Window / checkpoint | tIoU ↑ | THR@.25 ↑ | THR@.50 ↑ | NLI |
| --- | --- | --- | --- | --- | --- |
| constant | [2.0, 4.0] | **0.5649** | **0.9646** | **0.7522** | N/A |
| mean | [2.1708, 3.7348] | 0.4663 | 0.9469 | 0.2920 | N/A |
| median | [2.0, 4.0] | 0.5649 | 0.9646 | 0.7522 | N/A |
| videomae fine-tuned | selected epoch 4 | 0.4577 | 0.8053 | 0.4336 | N/A |
| timesformer fine-tuned | selected epoch 6 | 0.4690 | 0.8053 | 0.5044 | N/A |

The train-selected constant prior (0.5649) exceeds all tested frozen and fine-tuned visual estimates. This fails the stated visual-grounding criterion.

## 7. Diagnostic feature-space counterfactual checks

Shift gain is improvement over an unchanged prediction in circular endpoint error. Event/context replacement results are tIoU degradation. The shift uses 113 clips; the matched replacement test uses 100 eligible clips and excludes 13. Feature replacement is not a pixel-level causal test.

| Backbone | Head | Shift gain bins | Event drop | Context drop | Event − context | Eligible | NLI |
| --- | --- | --- | --- | --- | --- | --- | --- |
| videomae | mlp | 1.3982 | 0.1485 | -0.0468 | 0.1952 | 100.0000 | N/A |
| videomae | bilstm | 1.1106 | 0.1249 | -0.0417 | 0.1666 | 100.0000 | N/A |
| videomae | attention | 1.3938 | 0.0889 | -0.0734 | 0.1623 | 100.0000 | N/A |
| videomae | attention_bilstm | 1.2434 | 0.1536 | -0.0199 | 0.1735 | 100.0000 | N/A |
| timesformer | mlp | 0.8363 | 0.0830 | -0.0299 | 0.1129 | 100.0000 | N/A |
| timesformer | bilstm | 0.2301 | 0.0104 | 0.0105 | -0.0001 | 100.0000 | N/A |
| timesformer | attention | 0.5973 | 0.0587 | -0.0189 | 0.0776 | 100.0000 | N/A |
| timesformer | attention_bilstm | 0.2920 | -0.0136 | -0.0046 | -0.0090 | 100.0000 | N/A |
| qwen | mlp | 1.0442 | 0.1511 | -0.0507 | 0.2018 | 100.0000 | N/A |
| qwen | bilstm | 0.7965 | 0.0504 | -0.0161 | 0.0666 | 100.0000 | N/A |
| qwen | attention | 1.1770 | 0.1243 | -0.0473 | 0.1716 | 100.0000 | N/A |
| qwen | attention_bilstm | 0.3850 | 0.0175 | -0.0110 | 0.0285 | 100.0000 | N/A |

## 8. VideoMAE and TimeSformer report-generation pilot: complete results

These systems use cached **frozen** VideoMAE or TimeSformer features with the same Qwen2.5-0.5B-Instruct decoder architecture, a trained feature projection and decoder LoRA. Each condition was trained for two epochs with seed 42 on 1,101 diagnostic training examples and evaluated on the same 113 diagnostic validation examples. The text-only and shuffled conditions are supervised controls, not zero-shot models; the visual backbones were not fine-tuned in this reporting pilot. The text-only conditions still undergo independent training runs and are not independent external baselines.

**Interpretation:** These are exploratory reference-agreement measurements, not clean-test results. The diagnostic split remains uncertified. Generation emitted a warning that the attention mask was not explicitly supplied; no padding was intentionally added within an individual prompt, but the effect has not been independently verified, so interpret these scores cautiously. A 1.5B decoder follow-up uses explicit attention masks; its results were not complete when this document was updated. No statistical significance, visual-grounding success, human preference or external generalization is established.

### Structured and text-reference metrics

| Condition | Severity ↑ | tIoU ↑ | Conflict H ↓ | Omission O ↓ | BLEU-4 ↑ | ROUGE-L ↑ | METEOR ↑ | Cider+PTB ↑ | BERTScore ↑ |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| VideoMAE visual | 0.4513 | 0.4440 | 0.2761 | 0.1386 | 0.1041 | 0.3360 | 0.2842 | 0.0292 | 0.6796 |
| VideoMAE shuffled | 0.4513 | 0.4086 | 0.2637 | 0.1283 | 0.0968 | 0.3402 | 0.2784 | 0.0370 | 0.6846 |
| VideoMAE text-only | 0.2743 | 0.3909 | 0.3221 | 0.1475 | 0.1265 | 0.3611 | 0.3120 | 0.0424 | 0.6957 |
| TimeSformer visual | 0.4779 | 0.4410 | 0.2513 | 0.1224 | 0.1173 | 0.3472 | 0.3102 | 0.0510 | 0.6837 |
| TimeSformer shuffled | 0.4513 | 0.4174 | 0.2425 | 0.1106 | 0.0963 | 0.3290 | 0.2917 | 0.0499 | 0.6706 |
| TimeSformer text-only | 0.2743 | 0.3909 | 0.3221 | 0.1475 | 0.1278 | 0.3634 | 0.3174 | 0.0431 | 0.6990 |

### Full NLI breakdown for all six report-generation conditions

NLI is a reference-text cross-encoder score, not video verification. Explanation and full-report scores are each entailment probability minus contradiction probability. NLI loss is negative log entailment; lower is better. The fixed model revision is `fa2804872c3b4bd748f38c0185cc85775361e735`.

| Condition | Exp entail ↑ | Exp contradict ↓ | Exp NLI ↑ | Exp loss ↓ | Full entail ↑ | Full contradict ↓ | Full NLI ↑ | Full loss ↓ |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| VideoMAE visual | 0.0568 | 0.2542 | -0.1974 | 4.4150 | 0.3750 | 0.1401 | 0.2349 | 1.6420 |
| VideoMAE shuffled | 0.1078 | 0.2431 | -0.1353 | 3.9733 | 0.4577 | 0.1321 | 0.3256 | 1.3887 |
| VideoMAE text-only | 0.0962 | 0.1657 | -0.0694 | 3.2494 | 0.4118 | 0.2077 | 0.2041 | 1.8939 |
| TimeSformer visual | 0.1172 | 0.1707 | -0.0535 | 3.7623 | 0.4179 | 0.1053 | 0.3126 | 1.5615 |
| TimeSformer shuffled | 0.0748 | 0.1618 | -0.0870 | 4.2007 | 0.3115 | 0.1086 | 0.2030 | 2.0144 |
| TimeSformer text-only | 0.0999 | 0.1628 | -0.0629 | 3.2111 | 0.4187 | 0.2062 | 0.2125 | 1.8538 |

### Comparison and paper wording

- Within this six-condition pilot, TimeSformer visual has the highest observed severity accuracy (0.4779), Cider+PTB (0.0510), and explanation NLI score (-0.0535).
- VideoMAE visual has the highest observed reporting-window tIoU (0.4440). TimeSformer text-only has the highest BLEU-4 (0.1278), METEOR (0.3174), and BERTScore (0.6990). VideoMAE shuffled has the highest full-report NLI score (0.3256).
- The explanation NLI scores are negative for all six conditions, indicating that this scorer assigned greater contradiction than entailment on average. This is a model-based text metric, not a clinical or human factuality determination.
- Scores differ across metrics and conditions. Do not describe a universal winning model or equate a numerical lead with statistical significance. Report the full controls and the data and generation limitations.
- The historical Qwen 7B reporting comparisons and this shared 0.5B decoder pilot use different backbones/training regimes and must not be presented as an architecture-controlled comparison.

## 9. Stronger-decoder follow-up: in progress

The Qwen2.5-1.5B-Instruct follow-up uses explicit attention masks, three training epochs and the same diagnostic training/validation data. At the time of this update the initial TimeSformer visual condition was generating reports; the other matched controls and scoring were queued. No follow-up metrics are reported until complete. Training loss is not a test result.

## 10. Limits and remaining work

- No clean source-grouped benchmark is frozen; independent source and timestamp reviews remain outstanding.
- No new clean final-test results, human ratings or external validation are reported.
- Historical and diagnostic reporting results are descriptive; NLI cannot substitute for expert or visual verification.
- Historical adapter contains nonzero visual-block LoRA tensors, and the exact adapter-loading provenance of every historical prediction remains under review.
- Original workbook, historical outputs and previous paper packages are preserved.

## Evidence files

- Historical reporting and NLI: `/data/rahul_mishra/omkar/crashx/outputs/temporal_grounding_poc_v1/paper_cvpr2026_v9/paper/evidence/primary_metrics.json`
- Historical decoder and NLI: `/data/rahul_mishra/omkar/crashx/outputs/temporal_grounding_poc_v1/paper_cvpr2026_v9/paper/evidence/decoding_ablation.json`
- Historical architecture and NLI: `/data/rahul_mishra/omkar/crashx/outputs/temporal_grounding_poc_v1/paper_cvpr2026_v9/paper/evidence/architecture_v4.json`
- Matched diagnostic NLI: `/data/rahul_mishra/omkar/crashx/outputs/exploratory_video_20261116/matched_reporting_v2/nli_scores.json`
- Matched structured/text scores: `/data/rahul_mishra/omkar/crashx/outputs/exploratory_video_20261116/matched_reporting_v2/structured_scores.json` and `text_scores_with_resources_v2.json`
- Video-report pilot metric evidence: `/data/rahul_mishra/omkar/crashx/outputs/exploratory_video_20261116/video_report_pilot_v1/scored_results.json`
- Diagnostic backbone and interventions: `/data/rahul_mishra/omkar/crashx/outputs/exploratory_video_20261116/<backbone>/report.json` and `validation_counterfactuals.json`
- Publication tables and Cider+PTB recomputation: `/data/rahul_mishra/omkar/crashx/outputs/temporal_grounding_poc_v1/paper_cvpr2026_v9/paper/tables/`
