# Low-Resource Neural Machine Translation: English → Assamese

EXP 10 NLP mini project. Fine-tunes the pretrained **NLLB-200 distilled 600M** model on the **WMT 2026 Indic MT** English–Assamese (`en-as`) data and compares BLEU/chrF before and after fine-tuning.

> **Status:** code, notebook and instructions are complete. The model has **not been trained yet** and the results table below is intentionally empty. Scores must come from a real Kaggle GPU run and are not estimated.

## Dataset

- Source: WMT 2026 Indic MT shared task, <https://www2.statmt.org/wmt26/indic-mt-task.html> (English–Assamese, Category 1).
- Training data: the official 2026 training dataset. Test/reference data is used **only** for evaluation and must never be added to training (`preprocess.py --exclude` removes any overlap).
- Data files are not stored in this repository; download them from the task page / the links shared with the team and follow the task page's acknowledgement and citation instructions.

## Preprocessing (`src/preprocess.py`)

1. Load aligned text files (`--src-file/--tgt-file`) or a TSV/CSV/JSONL with English and Assamese columns.
2. Unicode NFC normalisation, removal of control/invisible characters, whitespace collapsing. Assamese script and punctuation are preserved.
3. Remove empty/missing translations, over-long pairs, pairs with extreme length ratio (misalignment guard) and duplicate pairs.
4. Optionally remove any pair that also appears in a test/reference file.
5. Shuffle with seed 42 and split 80:10:10 into `train.csv`, `validation.csv`, `test.csv`; counts are written to `preprocess_stats.csv`.
6. Tokenisation uses the NLLB tokenizer with `src_lang=eng_Latn`, `tgt_lang=asm_Beng`, max length 128.

## Model and training (`src/train.py`)

- Model: `facebook/nllb-200-distilled-600M` (licence **CC-BY-NC-4.0**: academic, non-commercial use only).
- Starting settings: 2 epochs, batch size 4 (grad. accumulation 4), learning rate 2e-5, max length 128, seed 42, fp16 on GPU, validation after every epoch, best checkpoint by validation loss.
- Platform: Kaggle Notebook with GPU. Reduce `--batch-size` or `--max-length` if memory runs out; test with `--max-train-rows 200` first.
- Actual settings and training time: **to be filled after the Kaggle run** (`training_time_minutes.txt` and `training_log.csv` are written to the output folder).

## Evaluation (`src/evaluate.py`)

BLEU and chrF via SacreBLEU on held-out test pairs, for the pretrained and the fine-tuned model on exactly the same examples (`--max-samples`). Output: `results/metrics.csv` and `results/sample_translations.csv` (English input, reference, model output).

| Model | BLEU | chrF |
|-------|------|------|
| Pretrained (before fine-tuning) | not yet evaluated | not yet evaluated |
| Fine-tuned | not yet evaluated | not yet evaluated |

Sample translations and a short error analysis go here after the run.

## How to run

On Kaggle: open `notebooks/exp10_nmt_kaggle.ipynb`, enable GPU and Internet, add the WMT training data as a Kaggle Dataset, set `TRAIN_FILE`/column names in section 2, and run the cells in order. The last cell is the interactive English → Assamese demo.

Locally (GPU recommended):

```bash
pip install -r requirements.txt
python src/preprocess.py --input train.tsv --src-col en --tgt-col as --out-dir data/processed
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
