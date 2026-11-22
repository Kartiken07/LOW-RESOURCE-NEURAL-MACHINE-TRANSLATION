# Low-Resource NMT (English to Assamese): Team Handoff for Naman and Mohit

## Status

The project code is finished and on GitHub; the one thing left is to train the model and record real results.

Repository: [Kartiken07/LOW-RESOURCE-NEURAL-MACHINE-TRANSLATION](https://github.com/Kartiken07/LOW-RESOURCE-NEURAL-MACHINE-TRANSLATION), branch `main`.

| Item | State |
| --- | --- |
| `src/preprocess.py`, `src/train.py`, `src/evaluate.py` | Pushed. Preprocessing logic was tested on sample data; training and evaluation have not yet been run on a GPU |
| `notebooks/exp10_nmt_kaggle.ipynb` | Pushed. Runs the whole pipeline on Kaggle and ends with an English to Assamese demo |
| `README.md`, `requirements.txt` | Pushed. The results table says "not yet evaluated" |
| `results/metrics.csv`, `results/sample_translations.csv` | Pushed as empty templates. No scores exist yet |
| Dataset | Not stored in the repo on purpose. It downloads automatically from Hugging Face when the notebook runs (AI4Bharat Samanantar, English to Assamese, about 141,000 pairs) |
| Trained model | Not trained yet |

The WMT 2026 files from the project guide were replaced by this open dataset because the WMT test data needs a registration password.

## Assignment

Naman or Mohit owns training the model on Kaggle and pushing the results to GitHub; the other one can take the report and presentation. Pick the owner between you and write the name next to the task.

| Task | Owner (Naman or Mohit) | Done when |
| --- | --- | --- |
| Train and evaluate on Kaggle | | `metrics.csv` has real BLEU and chrF for the pretrained and fine-tuned model |
| Push results and README numbers to GitHub | | Results files and the filled README table are on the repo under the owner's own GitHub account |
| Short project report | | Includes actual settings, training time, scores and 3 to 5 sample translations |
| Classroom presentation and live demo | | Demo cell translates a typed English sentence into Assamese |

The project guide (the Word file) asks for one pretrained model (NLLB-200 distilled 600M), one language pair, and a before and after comparison. Do not add other models or languages.

## Requirements

The owner needs three things before starting: a Kaggle account with a GPU, write access to the GitHub repo, and Git set up under their own name.

1. **Kaggle account.** Sign in at kaggle.com and verify your phone number (Settings, Phone Verification); Kaggle gives free GPUs only to verified accounts. In the notebook, turn on the GPU accelerator (T4 or P100) and set Internet to On.
2. **GitHub access.** Ask the repo owner (Kartiken07) to add your GitHub account as a collaborator on the repo. If that is not possible, fork the repo and open a pull request.
3. **Git on your computer.** Install Git and set your own name and the email address linked to your GitHub account, so the commits show your account in the repo's Contributors list.
4. **Licences.** The NLLB-200 model is CC-BY-NC-4.0 and the Samanantar dataset is CC BY-NC 4.0. Use them for this academic project only, not commercially.
5. **Keep secrets out.** Never put a GitHub token or password in a notebook, a commit, or a chat. If you use a personal access token to push, make it from your own account and revoke it when you are done.

## Train and evaluate on Kaggle

Run the repo's notebook top to bottom on a Kaggle GPU; it downloads the data, trains, evaluates and demos in one pass. The code has not yet been run on a GPU, so if a cell fails, fix it and mention the fix in the report.

1. Download `notebooks/exp10_nmt_kaggle.ipynb` from the repo. In Kaggle choose New Notebook, then File, Import Notebook, and upload it.
2. In the notebook settings set Accelerator to GPU (T4 x2 or P100) and Internet to On.
3. Run the cells in order. The first cell prints whether a GPU is available; stop and fix the settings if it says no GPU.
4. Preprocessing keeps 40,000 random pairs (`MAX_ROWS = 40000`) split 80:10:10 with seed 42. Raise or remove it only if time allows.
5. Run the smoke test cell first (200 training rows, 1 epoch). If it works, the full run will not crash on setup.
6. Run the baseline evaluation of the pretrained model, with `N_EVAL = 1000`. Use the same number for the fine-tuned evaluation so both models are scored on the same examples.
7. Run the full training: 2 epochs, batch size 4, gradient accumulation 4, learning rate 2e-5, max length 128, seed 42. If you get an out-of-memory error, lower the batch size to 2 or the max length to 96.
8. Run the fine-tuned evaluation, then the demo cell. Type an English sentence and check that Assamese text comes back.
9. Write down the actual settings and the training time (the training cell prints it and saves it in `nllb-en-as/training_time_minutes.txt`).

From the Kaggle output, download `results/metrics.csv`, `results/sample_translations.csv` and `nllb-en-as/training_log.csv`.

Shortcut: the repo also has `notebooks/run_all_kaggle.ipynb`, which runs preprocessing, baseline, fine-tuning and evaluation in one go. Import it, set GPU and Internet On, then use Save Version, Save & Run All; the run is about 1.5 hours and continues on Kaggle's servers if you close the browser. Afterwards `notebooks/show_results.ipynb` prints the metrics, training time and sample translations for the README.

Do not push the trained model folder to GitHub; it is large (roughly 2.4 GB). Keep it in Kaggle, or upload it to Kaggle Models or Hugging Face if the team wants to share it.

## Push results to GitHub

Commit the results and the filled-in README under your own GitHub account, so you appear as the contributor. Run this on your computer after downloading the result files from Kaggle:

```bash
git clone https://github.com/Kartiken07/LOW-RESOURCE-NEURAL-MACHINE-TRANSLATION.git
cd LOW-RESOURCE-NEURAL-MACHINE-TRANSLATION
git config user.name  "<your name>"
git config user.email "<email linked to your GitHub account>"

# copy the three files downloaded from Kaggle
cp /path/to/metrics.csv results/metrics.csv
cp /path/to/sample_translations.csv results/sample_translations.csv
mkdir -p results && cp /path/to/training_log.csv results/training_log.csv

# edit README.md: fill the results table, actual settings, training time, sample translations
git add results README.md
git commit -m "Add training results: BLEU/chrF and sample translations"
git push origin main
```

If the push is refused, you do not have write access yet: ask the repo owner to add you as a collaborator, or push to a branch (`git push origin HEAD:my-results`) and open a pull request.

What to put in the README: the BLEU and chrF numbers copied exactly from `metrics.csv` for both models, the settings you really used, the training time, and 3 to 5 sample translations with a sentence on typical errors. Do not estimate or round up any score.

Check the result on GitHub: your account should appear in the commit history, and the repo should show the results files and the updated README.

## Checklist and troubleshooting

The project is done when every box below is ticked.

- [ ] Kaggle run completed on a GPU with the settings noted
- [ ] Pretrained and fine-tuned models scored on the same test examples (BLEU and chrF)
- [ ] `results/metrics.csv` and `results/sample_translations.csv` hold real values
- [ ] README results table, settings, training time and sample translations filled in
- [ ] Commits pushed under the owner's own GitHub account
- [ ] No trained model files, data files or tokens in the repo
- [ ] Demo cell translates a typed English sentence into Assamese
- [ ] Short report and presentation prepared

| Problem | Fix |
| --- | --- |
| "No GPU" printed in the first cell | Turn on the GPU accelerator in notebook settings; verify your phone number in Kaggle |
| Dataset or model fails to download | Set Internet to On in notebook settings |
| CUDA out of memory | Lower batch size to 2 or max length to 96, then rerun the training cell |
| Session times out before training ends | Lower `MAX_ROWS` (for example 20000) or epochs, rerun, and note the change in the README |
| Push is rejected | Ask the repo owner for collaborator access, or push a branch and open a pull request |
| Dataset config error in `preprocess.py` | Try `--hf-dataset opus100` (OPUS-100 English to Assamese) as the alternative dataset |
