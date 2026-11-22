# CrashLogic: Structured Accident Report Generation and an Audit of Temporal Grounding

**Authors:** to be added  
**Status:** Research draft. The experimental results are historical or exploratory; the benchmark is not yet independently reviewed or frozen.

## Abstract

A useful account of a road collision should identify the participants and describe when and how the event unfolded. We study structured report generation from dashcam video using CrashLogic, an adaptation of Qwen2.5-VL-7B, and a human-labeled reporting layer over clips derived from the Car Crash Dataset (CCD). The supplied workbook contains 1,500 rows; processing produced 1,498 video records. On 113 clips from a candidate-quarantined diagnostic validation partition, a matched comparison gave severity accuracy of 0.5310 for the historical adapted model and 0.0088 for zero-shot Qwen. Reference-based omission cost was 0.0782 and 0.3590, respectively. These findings describe the measured cohort, not independent generalization: the original project split has confirmed source-footage overlap, and the diagnostic partition is not certified clean.

We also examine whether video features support temporal localization. A constant 2–4 second window selected from training annotations achieved mean temporal intersection over union (tIoU) of 0.5649 on the diagnostic validation cohort. The highest observed fine-tuned visual model, TimeSformer with attention and a bidirectional recurrent head, reached 0.4690. Frozen-feature controls and feature-space interventions give mixed results. A separate report-generation pilot produced text from VideoMAE or TimeSformer features using a shared small language model; performance varied across visual, shuffled-feature and text-only conditions. Together, the experiments show improved reference agreement after adaptation while identifying limits in the current evidence for temporal grounding. Independent review, a source-grouped benchmark freeze, human evaluation and external validation remain necessary.

## 1. Introduction

Dashcam footage can preserve details that are difficult to recover from a brief accident description. A model that turns such footage into a structured report could help organize information about vehicles, impact locations and event timing. Its usefulness, however, depends on more than fluency. A plausible report may contain the wrong participants, an unsupported explanation or a timestamp that reflects a common pattern in the dataset rather than the actual collision.

CrashX addresses structured reporting using CCD-derived footage and a workbook containing severity, impact, vehicle, weather, timing and narrative fields. CrashLogic adapts a pretrained vision-language model to produce those fields as text. We evaluate saved reporting experiments and test visual temporal models against controls that do not need the original frame order.

The work has three contributions within the available evidence. First, it organizes a human-labeled annotation layer for report generation and field-level evaluation over existing footage. Second, it examines historical and matched diagnostic comparisons between zero-shot and adapted report generation. Third, it audits source overlap, text-score implementation and timestamp priors, and tests exploratory video backbones with position, random and shuffled-feature controls. These are contributions to the analysis and evaluation of the resource; the present evidence does not establish a new raw-video collection, a clean generalization benchmark or a proven temporal-grounding method.

## 2. Background and related work

Bao, Yu and Kong introduced CCD in work on traffic accident anticipation [1]. Their task predicts the likelihood of a future accident from video. CRASH likewise studies accident anticipation using object and context information and temporal attention [2]. CrashX report generation and interval localization ask different questions, so their average precision and time-to-accident results cannot be directly compared with our report scores or tIoU. We have not reproduced either system on a common, reviewed reporting benchmark.

The reporting model builds on Qwen2.5-VL [3] and parameter-efficient adaptation through LoRA and QLoRA [4, 5]. VideoMAE learns video representations using masked video pretraining [6], while TimeSformer uses attention over space and time [7]. We use these backbones to examine localization and, in a separate pilot, to condition report generation. The upstream pretraining of the backbones differs and is part of the comparison.

## 3. Dataset and annotation policy

### 3.1 Source footage and reporting fields

The raw videos are derived from CCD; the CrashX contribution is the supplied reporting annotation and its use in model training and evaluation. The workbook has 1,500 rows and the processed corpus has 1,498 records. The historical project partition contains 1,198 training, 150 validation and 150 test records. It is not the original CCD evaluation protocol. The reasons for every workbook-to-record exclusion still require a reviewed record.

The workbook supplies fields for severity, impact, vehicles and count, weather, start and end times, an explanation, ambiguity notes and camera view. There are 1,498 nonblank explanations, with a median length of 93 whitespace-delimited words. The workbook is described as human-labeled, but the available records do not establish annotator identities, expertise, annotation time or agreement. A narrative that states a cause is an annotation, not independent proof of physical causation.

### 3.2 Source overlap and diagnostic partitions

A source audit identified confirmed related footage across historical partitions, including a train-test pair. The all-pairs candidate scan left 1,643 candidate pairs awaiting independent review; similarity flags are not duplicate judgments. Two diagnostic partitions were prepared to support investigation, but neither is certified source-independent. The candidate-quarantined diagnostic split contains 1,202 training, 122 validation and 119 test records before interval eligibility filtering.

For the reported diagnostic interval experiments, 1,101 training and 113 validation clips have positive-duration annotations and were usable in the relevant pipelines. Training cases with equal start and end comprise 36 positive point annotations and 65 `0–0` cases. The current policy keeps point events separate and leaves `0–0` cases unresolved. It does not infer a no-event label from a zero timestamp or alter the original workbook. Duration and frame-count checks establish that an interval fits in a video, not that it correctly marks the collision.

## 4. Models and experimental procedure

### 4.1 CrashLogic report generation

CrashLogic adapts Qwen2.5-VL-7B with 4-bit QLoRA and produces structured fields as text. The original run used eight sampled frames, rank 16, LoRA scaling parameter 32, dropout 0.05 and a learning rate of 0.0002 for five epochs. Its saved trainer state records 750 completed steps. Inspection of the preserved adapter found both language and visual-block LoRA tensors, including nonzero visual adapter weights. It is therefore inaccurate to describe that saved adapter as having a completely frozen visual encoder. The exact loading history of every preserved prediction remains under review.

We examine greedy decoding and temporal contrastive decoding (TCD), which combines logits obtained from original and temporally reordered frames. The historical adapter was also evaluated with other reversal strengths, a shuffled negative and a full-negative ablation. Those are inference conditions of the same saved adapter, not separate model-training runs. TCD has numerical leads on some metrics, but the saved paired analysis does not establish an advantage over greedy decoding.

### 4.2 Matched diagnostic reporting

For a controlled inference comparison on the 113 diagnostic validation clips, zero-shot Qwen and the historical adapted Qwen used the same prompt, 16 sampled frames, frame-size setting and greedy decoding. The historical adapter’s tensor values were verified after a compatibility mapping for the installed software. This comparison does not make the adapter source-independent: it was trained before the footage review.

### 4.3 Video-backbone localization

We compared frozen Qwen visual features, VideoMAE features and TimeSformer features on a common eight-bin temporal representation derived from 16 sampled frames. For each backbone, trained heads included a multilayer perceptron (MLP), a bidirectional long short-term memory network (BiLSTM), multi-head attention, and attention followed by BiLSTM. These are supervised probes: the backbone is frozen, but the head learns from training intervals. A trained head on a frozen backbone is not a zero-shot model.

Position-only, random-feature and shuffled-feature heads test whether scores can be obtained without the original ordered visual representation. Constant, mean and median timestamp windows are selected using training intervals only. The constant prior chooses the observed training interval with the highest mean training tIoU. VideoMAE and TimeSformer backbone fine-tuning used a validation-selected head. Because the same diagnostic validation set was used for head or checkpoint selection and reporting, the resulting estimates may be optimistic.

### 4.4 Video-to-report pilot

A separate pilot uses frozen VideoMAE or TimeSformer features, a learned feature projection and the same Qwen2.5-0.5B-Instruct decoder architecture with a trained LoRA adapter. Each condition trained for two epochs with seed 42. Visual, shuffled-feature and text-only variants were run for each backbone. The text-only and shuffled models are trained controls, not zero-shot systems. This small-decoder pilot differs from the 7B CrashLogic reporting system and is not an architecture-controlled comparison with it.

The first pilot emitted a warning because an attention mask was not explicitly supplied during generation. No padding was intentionally added within an individual prompt, but the warning’s effect has not been independently verified. A 1.5B-decoder follow-up with explicit masks is in progress; it is excluded from the completed-result tables until all conditions and scoring finish.

## 5. Metrics and terminology

| Term | Meaning in this study |
| --- | --- |
| **tIoU** | Temporal intersection over union: overlap duration divided by the union of predicted and reference intervals. |
| **THR@0.25 / THR@0.50** | Fraction of evaluated clips whose predicted interval reaches the stated tIoU threshold. |
| **H / O** | Reference-based conflict and omission costs; lower values are favorable under the project’s rules. |
| **BLEU, ROUGE, METEOR** | Text-overlap measures computed against report references; each captures a different notion of similarity. |
| **Cider+PTB** | A separately recomputed Cider score using the PTB tokenizer and documented corpus settings. |
| **BERTScore** | Contextual embedding similarity between generated and reference text. |
| **NLI** | Natural language inference: a text cross-encoder estimates entailment and contradiction. The reported NLI score is entailment minus contradiction. |

NLI is reported for systems that produce text. The localization-only heads and timestamp priors output intervals, so NLI is not applicable to them. NLI and the text metrics compare generated language with references; they do not independently inspect the video or verify every factual claim.

Six historical primary scores saved under the label CIDEr exactly reproduce a term-frequency n-gram cosine proxy. We label that quantity **legacy TF-cosine** and keep it separate from Cider+PTB, which was recomputed with the specified implementation. Historical text-metric and NLI checkpoints were not uniformly pinned.

## 6. Results

### 6.1 Matched zero-shot and adapted reporting

| Metric | Zero-shot Qwen | Historical adapted CrashLogic |
| --- | ---: | ---: |
| Severity accuracy ↑ | 0.0088 | **0.5310** |
| Mean tIoU ↑ | 0.0142 | **0.4204** |
| Conflict cost H ↓ | 0.2631 | **0.1876** |
| Omission cost O ↓ | 0.3590 | **0.0782** |
| BLEU-4 ↑ | 0.0157 | **0.1387** |
| ROUGE-L ↑ | 0.1646 | **0.3366** |
| METEOR ↑ | 0.2079 | **0.3689** |
| Cider+PTB ↑ | 0.0027 | **0.0951** |
| BERTScore ↑ | 0.5085 | **0.6879** |
| Explanation NLI score ↑ | **0.0865** | 0.0671 |
| Full-report NLI score ↑ | 0.1184 | **0.3615** |

*Table 1. Matched greedy reporting on 113 diagnostic validation clips. The adapter was trained on an earlier unreviewed split. Scores are reference-based and do not establish independent generalization.*

Adaptation improves the displayed severity, overlap, cost and text-similarity estimates on this cohort. NLI gives a more qualified picture: the adapted model has the higher full-report score, while zero-shot Qwen has the higher explanation score and lower contradiction probability. A descriptive paired video bootstrap gives a severity difference of 0.5221 with a 95% interval of [0.4336, 0.6106]. It does not account for source dependence, seed variation or multiple comparisons and is not treated here as confirmatory evidence.

### 6.2 Historical reporting and decoder comparisons

On the original 150-clip test split, saved severity accuracy was 0.0800 for zero-shot Qwen2.5-VL-7B, 0.4600 for greedy CrashLogic and 0.4867 for CrashLogic with TCD. The corresponding omission costs were 0.4622, 0.1067 and 0.1022. These historical scores use a split with confirmed source overlap and historical conditions that were not uniformly matched.

No decoder leads on every measure. In the seven-condition historical ablation, the full-negative variant has the highest observed BLEU-4 (0.1475), while the shuffled-negative variant has the highest observed ROUGE-L (0.3402) and BERTScore (0.6880). TCD at α = 0.5 has the highest observed severity accuracy (0.4867) and lowest omission cost (0.1022). The 95% paired video-bootstrap intervals for TCD minus greedy include zero for severity, tIoU and omission cost. The evidence does not establish TCD superiority. Complete model, decoder, architecture and NLI tables are provided in the appendix.

### 6.3 Original-split timing-prior comparison from the earlier CrashX manuscript

The supplied earlier manuscript reports the following original-split comparison. These numbers come from the same CrashX research artifacts and are included for continuity; they are not independent published baselines or a new experiment. The original partition contains confirmed source-footage overlap.

| Original-split method | Validation tIoU ↑ | Test tIoU ↑ | Test THR@0.25 ↑ | Test THR@0.50 ↑ |
| --- | ---: | ---: | ---: | ---: |
| Train-selected constant, 2–4 s | 0.5289 | **0.5178** | 0.8733 | 0.7667 |
| Training mean, 2.083–3.514 s | 0.4157 | 0.4047 | 0.8333 | 0.3800 |
| Training median, 2–4 s | 0.5289 | **0.5178** | 0.8733 | 0.7667 |
| Historical CrashLogic greedy | Not reported here | 0.3728 | Not reported here | Not reported here |
| Historical CrashLogic + TCD, α=0.5 | Not reported here | 0.3939 | Not reported here | Not reported here |

*Table 2. Original-split timing comparison reproduced from the earlier CrashX manuscript and saved audit. Priors use training annotations only. The original scores retain the historical treatment of equal-boundary annotations; they are not directly interchangeable with the positive-duration-only diagnostic results below.*

The 2–4 second prior exceeds both adapted reporting models on the original test split without examining frames. This makes the prior a necessary baseline, but the source overlap and annotation ambiguity prevent a clean-generalization conclusion.

### 6.4 Diagnostic temporal localization against timing priors

| Diagnostic validation method | Mean tIoU ↑ | THR@0.25 ↑ | THR@0.50 ↑ |
| --- | ---: | ---: | ---: |
| Training-selected constant, 2–4 s | **0.5649** | **0.9646** | **0.7522** |
| TimeSformer + attention + BiLSTM, fine-tuned | 0.4690 | 0.8053 | 0.5044 |
| VideoMAE + MLP, fine-tuned | 0.4577 | 0.8053 | 0.4336 |
| VideoMAE frozen + MLP | 0.4352 | 0.7257 | 0.4336 |
| TimeSformer frozen + attention + BiLSTM | 0.4327 | 0.7522 | 0.4602 |
| Qwen frozen + BiLSTM | 0.4081 | 0.7434 | 0.3274 |

*Table 3. Exploratory results on 113 diagnostic validation clips. Fine-tuned checkpoints were selected on this validation set. The constant window was selected using training annotations and requires no video frames.*

The constant prior exceeds every listed visual model. Several trained visual heads also score below their position-only or shuffled-feature controls. These observations do not prove that the models ignore video, but they prevent a claim that the experiments demonstrate visual temporal grounding. Feature-space temporal shifts and event-versus-context replacement give mixed results. For TimeSformer with attention and BiLSTM, the event-minus-context degradation difference is -0.0090 on 100 eligible clips. These interventions do not replace pixel-level causal tests.

### 6.5 Video-to-report generation pilot

| Reporting condition | Severity accuracy ↑ | BLEU-4 ↑ | Cider+PTB ↑ | BERTScore ↑ | Explanation NLI score ↑ | Full-report NLI score ↑ |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| VideoMAE visual features | 0.4513 | 0.1041 | 0.0292 | 0.6796 | -0.1974 | 0.2349 |
| VideoMAE shuffled features | 0.4513 | 0.0968 | 0.0370 | 0.6846 | -0.1353 | **0.3256** |
| VideoMAE text-only | 0.2743 | 0.1265 | 0.0424 | 0.6957 | -0.0694 | 0.2041 |
| TimeSformer visual features | **0.4779** | 0.1173 | **0.0510** | 0.6837 | **-0.0535** | 0.3126 |
| TimeSformer shuffled features | 0.4513 | 0.0963 | 0.0499 | 0.6706 | -0.0870 | 0.2030 |
| TimeSformer text-only | 0.2743 | **0.1278** | 0.0431 | **0.6990** | -0.0629 | 0.2125 |

*Table 4. Two-epoch frozen-backbone pilot using the shared Qwen 0.5B decoder architecture on 113 diagnostic validation clips. The generation warning described in Section 4.4 applies to every condition. NLI compares generated and reference text.*

TimeSformer visual features give the highest observed severity accuracy and Cider+PTB in this pilot, while its text-only control has higher BLEU-4 and BERTScore. VideoMAE shuffled features have the highest full-report NLI score. All six explanation NLI scores are negative, indicating that the scorer assigned higher contradiction than entailment on average. The results vary by metric and do not identify a universal winner or establish that visual evidence caused the generated claims.

## 7. Discussion

The clearest reporting result is the matched diagnostic comparison between zero-shot and adapted Qwen: the adapted model has higher reference severity agreement and lower omission cost under the measured setup. It is useful evidence about the saved system, but the source-overlap audit limits what it says about new recordings. The result should be accompanied by the mixed NLI breakdown rather than summarized as verified factual reliability.

The localization study raises a separate concern. A common 2–4 second interval is a strong training-selected baseline for these annotations. A method can obtain substantial overlap by exploiting this timing pattern. A visual method should therefore be compared with timing-only, position-only and order-disrupting controls, and should respond to controlled changes in the event evidence. The current models fall short of that combined standard.

The annotation workbook is a potentially useful reporting resource, but its value to other researchers depends on a clear account of annotation provenance and reliability. The number of structured fields and the length of explanations do not by themselves establish annotation accuracy or novelty relative to another resource.

## 8. Limitations and next steps

The original split contains confirmed source overlap, the diagnostic splits are not certified final benchmarks, and independent source and timestamp adjudication is incomplete. The 1,643 pending source candidates are review requests, not confirmed duplicates. Some timestamps have unresolved point or `0–0` semantics, and an interval that fits a video can still mark the wrong event.

The visual backbone comparisons use different upstream pretraining. The reported fine-tuned heads and checkpoints were selected on the validation set used for reporting. The pilot uses one seed and has a generation warning; its feature interventions may create out-of-distribution inputs. Historical text and NLI model revisions are not uniformly pinned. No completed human-rating study or external evaluation is available.

The next milestone is independent review and adjudication, followed by a versioned source-grouped split with recorded exclusions, hashes and reviewer signoff. Models and hyperparameters should then be selected using training and validation data, locked, and evaluated once on the reviewed test set. A report-quality study and evaluation on external footage would be needed to support stronger reliability and generalization claims.

## 9. Conclusion

CrashLogic produces better reference agreement than the evaluated zero-shot Qwen condition in the matched diagnostic reporting comparison. The temporal experiments show that a training-selected timestamp window remains a stronger baseline than the tested visual models. These results support further study of structured accident reporting while leaving source-independent generalization, visual grounding and factual reliability open.

## References

1. Bao, W., Yu, Q. and Kong, Y. *Uncertainty-based Traffic Accident Anticipation with Spatio-Temporal Relational Learning.* ACM Multimedia, 2020. https://arxiv.org/abs/2008.00334
2. Liao, H. et al. *CRASH: Crash Recognition and Anticipation System Harnessing with Context-Aware and Temporal Focus Attentions.* 2024. https://arxiv.org/abs/2407.17757
3. Bai, S. et al. *Qwen2.5-VL Technical Report.* 2025. https://arxiv.org/abs/2502.13923
4. Hu, E. J. et al. *LoRA: Low-Rank Adaptation of Large Language Models.* 2021. https://arxiv.org/abs/2106.09685
5. Dettmers, T. et al. *QLoRA: Efficient Finetuning of Quantized LLMs.* 2023. https://arxiv.org/abs/2305.14314
6. Tong, Z. et al. *VideoMAE: Masked Autoencoders are Data-Efficient Learners for Self-Supervised Video Pre-Training.* 2022. https://arxiv.org/abs/2203.12602
7. Bertasius, G., Wang, H. and Torresani, L. *Is Space-Time Attention All You Need for Video Understanding?* 2021. https://arxiv.org/abs/2102.05095

---

# Appendix: complete result tables

The following appendix preserves the complete saved comparison tables. Historical original-split and diagnostic validation results are labeled separately; their numbers should not be combined into a single ranking.

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


## Appendix: pending work and evidence

The complete results source and artifact paths are documented in `CRASHX_ALL_RESULTS_AND_NLI_20261122.md`. The stronger-decoder follow-up has not completed and is excluded from this draft.
