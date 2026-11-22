# Low-Resource Neural Machine Translation: English → Assamese

EXP 10 NLP mini project. Fine-tunes the pretrained **NLLB-200 distilled 600M** model on an open English–Assamese (`en-as`) parallel corpus and compares BLEU/chrF before and after fine-tuning.

> **Status:** code, notebook and instructions are complete. The model has **not been trained yet** and the results table below is intentionally empty. Scores must come from a real Kaggle GPU run and are not estimated.

## Dataset

- **Used:** [AI4Bharat Samanantar](https://huggingface.co/datasets/ai4bharat/samanantar), config `as` (~141k English–Assamese pairs, licence **CC BY-NC 4.0**, academic/non-commercial use). It is downloaded automatically by `src/preprocess.py --hf-dataset samanantar`.
- **Alternative:** [OPUS-100](https://huggingface.co/datasets/Helsinki-NLP/opus-100) `as-en` (`--hf-dataset opus100`, licence not specified).
- The WMT 2026 Indic MT data suggested in the project guide was not used because its test data is distributed to registered participants only. Cite Samanantar (Ramesh et al., 2021, "Samanantar: The Largest Publicly Available Parallel Corpora Collection for 11 Indic Languages") and OPUS-100 (Zhang et al., 2020) if used.
- Data files are not stored in this repository.

## Preprocessing (`src/preprocess.py`)

1. Download the open dataset (`--hf-dataset`), or load aligned text files (`--src-file/--tgt-file`) or a TSV/CSV/JSONL with English and Assamese columns.
2. Unicode NFC normalisation, removal of control/invisible characters, whitespace collapsing. Assamese script and punctuation are preserved.
3. Remove empty/missing translations, over-long pairs, pairs with extreme length ratio (misalignment guard) and duplicate pairs.
4. Optionally keep a random subset (`--max-rows`) and/or remove any pair that also appears in a test/reference file (`--exclude`).
5. Shuffle with seed 42 and split 80:10:10 into `train.csv`, `validation.csv`, `test.csv`; counts are written to `preprocess_stats.csv`.
6. Tokenisation uses the NLLB tokenizer with `src_lang=eng_Latn`, `tgt_lang=asm_Beng`, max length 128.

## Model and training (`src/train.py`)

- Model: `facebook/nllb-200-distilled-600M` (licence **CC-BY-NC-4.0**: academic, non-commercial use only).
- Starting settings: 2 epochs, batch size 4 (grad. accumulation 4), learning rate 2e-5, max length 128, seed 42, fp16 on GPU, validation after every epoch, best checkpoint by validation loss.
- Platform: Kaggle Notebook with GPU. Reduce `--batch-size` or `--max-length` if memory runs out; test with `--max-train-rows 200` first.
- Actual settings and training time: **to be filled after the Kaggle run** (`training_time_minutes.txt` and `training_log.csv` are written to the output folder).

## Evaluation (`src/evaluate.py`)

BLEU and chrF via SacreBLEU on held-out test pairs (the 10% split, never used in training), for the pretrained and the fine-tuned model on exactly the same examples (`--max-samples`). Output: `results/metrics.csv` and `results/sample_translations.csv` (English input, reference, model output).

| Model | BLEU | chrF |
|-------|------|------|
| Pretrained (before fine-tuning) | not yet evaluated | not yet evaluated |
| Fine-tuned | not yet evaluated | not yet evaluated |

Sample translations and a short error analysis go here after the run.

## How to run

On Kaggle: open `notebooks/exp10_nmt_kaggle.ipynb`, enable GPU and Internet, and run the cells in order (the dataset downloads automatically). The last cell is the interactive English → Assamese demo.

Locally (GPU recommended):

```bash
pip install -r requirements.txt
python src/preprocess.py --hf-dataset samanantar --max-rows 40000 --out-dir data/processed
python src/evaluate.py --model facebook/nllb-200-distilled-600M --label "Pretrained (before fine-tuning)" --max-samples 1000
python src/train.py --data-dir data/processed --output-dir nllb-en-as
python src/evaluate.py --model nllb-en-as --label "Fine-tuned" --max-samples 1000
```

Large model checkpoints and dataset files are not committed; see `.gitignore`.

## Repository layout

```
README.md  requirements.txt
notebooks/exp10_nmt_kaggle.ipynb
src/{preprocess,train,evaluate}.py
results/{metrics.csv,sample_translations.csv}
```
