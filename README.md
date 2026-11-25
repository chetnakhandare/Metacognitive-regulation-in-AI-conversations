pip install -r requirements.txt
huggingface-cli login            # LMSYS-Chat-1M is gated: accept its licence first

python 01_build_sample.py --out data/turns.parquet                 # Section 4.2
python 02_lexicon_code.py --turns data/turns.parquet --out data/labels_lexicon.csv \
                          --gold-sheet 120 --gold-out data/gold_sheet.csv   # Section 4.3/4.4
# two coders fill in gold_sheet.csv independently -> data/coder_a.csv, data/coder_b.csv
python 03_reliability.py --coder-a data/coder_a.csv --coder-b data/coder_b.csv
python 04_train_classifier.py --gold data/gold_adjudicated.csv --context \
                              --predict data/turns.parquet --pred-out data/labels_model.csv
python 04b_llm_annotate.py --gold data/gold_adjudicated.csv --out data/labels_llm.csv   # optional
python 05_sequence_analysis.py --labels data/labels_model.csv --out results/        # Section 4.5
python 06_residual_topics.py --turns data/turns.parquet --labels data/labels_model.csv
