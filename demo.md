# IllumiCurveNet reproduction results

## Pretrained GPU inference

The authors’ `test.py` completed successfully with the bundled `snapshots/model-best.pth` checkpoint. The input was the raw lunar image embedded in Figure 1 of the [paper](research_paper.pdf). The run used PyTorch 2.11.0 and TorchVision 0.26.0 with CUDA 13.0.

| Measurement | Raw input | Enhanced output |
| --- | ---: | ---: |
| Image dimensions | 400 × 400 | 400 × 400 |
| Mean grayscale intensity (0–255) | 10.23 | 68.96 |

[Raw input](illumi-curve-net-main/data/test_data/paper-figure-1/raw.png) · [Enhanced output](illumi-curve-net-main/data/result/paper-figure-1/raw.png)

![Side-by-side comparison with raw input on the left and enhanced output on the right](illumi-curve-net-main/data/result/paper-figure-1/before-after.png)

## Dataset verification

- **Training data.** Downloaded the authors’ [Kaggle archive](https://www.kaggle.com/datasets/ashishprajapati1306/illumicurvenet-training-data) and extracted it into [data/train_data](illumi-curve-net-main/data/train_data). All 2,002 files passed Pillow verification and were 512 × 512 RGB JPEGs. Section IV-A of the paper identifies SICE Part 1 as the training source.
- **Lunar test data.** The paper used 40 images from ZSPL-PSR, with 20 images per lunar pole at 400 × 400 pixels. The [official repository](https://github.com/dl-zfq/ZSPL-PSR) contains code and a checkpoint. The exact test images remain unavailable in this workspace.

## Benchmark and training status

| Metric (lower is better) | IllumiCurveNet score reported by the authors | Local reproduction |
| --- | ---: | --- |
| PIQE | 36.40 | Pending |
| NIQE | 8.38 | Pending |
| BRISQUE | 36.55 | Pending |

The scores above are from the [project README](illumi-curve-net-main/README.md). Local verification covers pretrained inference on one image. Training has yet to run, and benchmark reproduction is pending the exact lunar test set.
