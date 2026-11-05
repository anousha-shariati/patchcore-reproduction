# Reproducing PatchCore for Industrial Anomaly Detection

Code, data and results for my COMP8240 (Applications of Data Science, Macquarie University) project: a reproduction of **PatchCore** — Roth et al., *Towards Total Recall in Industrial Anomaly Detection*, CVPR 2022 ([paper](https://arxiv.org/abs/2106.08265), [official code](https://github.com/amazon-science/patchcore-inspection)).

The project:
1. reproduces the paper's main results on **MVTec AD** with the authors' official code (and checks them with the Anomalib implementation);
2. tests the method on new data: an existing dataset, **BTAD**, and a **tealight dataset** I photographed and annotated for this project;
3. brings an LLM (Gemini) into the evaluation, as a second annotator and as a judge of PatchCore's anomaly maps.

## Main results

| Experiment | Image AUROC | Pixel AUROC |
|---|---|---|
| MVTec AD, paper (PatchCore-10%) | 99.0 | 98.1 |
| MVTec AD, official code (my run) | 99.2 | 98.1 |
| MVTec AD, Anomalib 2.6.2 (my run) | 98.3 | 98.0 |
| BTAD, mean of 3 products | 92.7 | 97.4 |
| Tealights (constructed dataset) | 100.0 | 97.0 |

Annotation agreement between my tealight masks and Gemini's (pixel-level Cohen's kappa): 0.70 for empty tealights, 0.43 for used ones.

## Repository structure

```
notebooks/          the Colab notebooks, in the order they were run, with all results and figures in their outputs
data/tealights/     the constructed tealight dataset, in MVTec AD layout (CC BY 4.0)
data/gemini/        Gemini's annotations: its raw answers, the image key and its masks
```

| Notebook | Step |
|---|---|
| `1_mvtec_anomalib.ipynb` | MVTec AD, all 15 categories, with Anomalib 2.6.2 |
| `2_official_repo.ipynb` | MVTec AD with the authors' official code, and comparison with the paper and with Anomalib |
| `3_btad.ipynb` | BTAD (existing new dataset) with the official code |
| `4_tealights_preprocessing.ipynb` | Tealight dataset: HEIC to PNG, automatic cropping, MVTec layout |
| `5_tealight_annotation.ipynb` | Masks from my annotations, Gemini as a second annotator, agreement (IoU, Dice, pixel kappa) |
| `6_tealights_patchcore.ipynb` | PatchCore (official code) on the tealights, per defect type, with per-image scores |
| `7_llm_judge.ipynb` | PatchCore scored against Gemini's masks, and Gemini as a judge of the anomaly maps |

**Notes on the notebooks**
- Notebook 1 was saved without its outputs. Its results are read from the results file it produced and shown, next to the official-code results, in notebook 2.
- Notebook 4, Part 2 (annotation) is an earlier version. The final masks were built in notebook 5, Part 1, from `data/tealights/annotations_coco.json`.
- Gemini (Gemini 3.6 Flash, web app, 4 November 2026) was used by hand: the notebooks prepare the images and prompts, and its answers were pasted back into notebooks 5 and 7, so they can be re-read without access to the model.

## Environment

Google Colab with a Tesla T4 GPU, Python 3.13, PyTorch 2.11, torchvision 0.26, faiss-cpu 1.15. Official code at commit `fcaa92f`. Each notebook also saves an `environment.json`.

Changes made to the official code (applied by the notebooks at run time, the authors' files are not included here):
- `--faiss_on_gpu` removed from the command, since the requirements install faiss-cpu (affects only speed);
- two lines in `bin/run_patchcore.py` that saved anomaly-map images were fixed to use the ImageNet mean and std (affects only the saved images);
- for the tealights, the script was extended to also save every image's anomaly score and anomaly map.

## How to run

1. Open a notebook in Google Colab and select a T4 GPU (Runtime → Change runtime type). Notebooks 5 and 7 do not need a GPU.
2. The notebooks read and write files in Google Drive under `MyDrive/COMP8240_PatchCore/`. To re-run them, copy `data/tealights/` to `MyDrive/COMP8240_PatchCore/tealights_mvtec/tealight/` (and `annotations_coco.json` to `MyDrive/COMP8240_PatchCore/tealights_mvtec/`), or change the `BASE` path at the top of each notebook.
3. MVTec AD and BTAD are **not** included here; the notebooks download them from their original sources.
4. Run the notebooks in order. Notebooks 6 and 7 need the outputs of the earlier tealight notebooks.

## Data and licences

- **Tealight dataset** (`data/tealights/`): 154 photographs of tealights (59 nominal, 49 used, 46 empty) cropped to 512 x 512, with masks for the 95 defective images. Only the cropped PNGs are included, which carry no location metadata. Licensed under **CC BY 4.0** (see `data/tealights/LICENSE`).
- **MVTec AD** (CC BY-NC-SA 4.0) and **BTAD** are used under their own terms and are downloaded from their original sources, not redistributed.
- The official PatchCore code that the notebooks run is Apache-2.0, by its authors.

## Citation

Roth, K., Pemula, L., Zepeda, J., Schölkopf, B., Brox, T. and Gehler, P. (2022). Towards Total Recall in Industrial Anomaly Detection. *CVPR 2022*.
