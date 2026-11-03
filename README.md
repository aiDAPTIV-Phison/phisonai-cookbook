English | [繁體中文](README.zh-TW.md)

# llm-notebooks

Three Jupyter notebooks that show, side by side, how single-GPU QLoRA fine-tuning changes the behavior of an LLM. Each notebook runs the same inputs before and after fine-tuning, so the difference is easy to see.

| Notebook | Base model | Dataset | What it shows |
|---|---|---|---|
| [notebooks/llm-classification-ft.ipynb](notebooks/llm-classification-ft.ipynb) | `meta-llama/Llama-Guard-3-8B` | `ai4privacy/pii-masking-65k` (training), `ai4privacy/pii-masking-200k` (validation) | Teaches a guard model to detect multiple PII (personal data) violations in the same text, and compares F1, precision, recall and the confusion matrix before and after fine-tuning. |
| [notebooks/llm-style-ft.ipynb](notebooks/llm-style-ft.ipynb) | `Qwen/Qwen3-4B-Instruct-2507` | `lianghsun/tw-ptt-keyboard-warrior-chat` | Teaches a chat model the speaking style of users on Taiwan's PTT forum, and compares replies to the same prompts before and after fine-tuning. |
| [notebooks/llm-ocr-ft.ipynb](notebooks/llm-ocr-ft.ipynb) | `Qwen/Qwen3-VL-4B-Instruct` | `deepcopy/MathWriting-human` | Teaches a vision-language model to turn images of handwritten math into LaTeX, compares exact match and character error rate (CER) on test data before and after fine-tuning, and shows examples that were wrong before and right after. |

The notebooks' explanations are written in English first, followed by the original Traditional Chinese.

## What this project does not include

- It is not a production training pipeline. Steps and data sizes are deliberately small (for example `MAX_STEPS = 30` in the style notebook) so the notebooks finish quickly and show the workflow; they do not aim for the best accuracy.
- It does not ship model weights or datasets. Both are downloaded from Hugging Face at run time and are subject to their own licenses.
- It is not a ready-to-deploy safety filter. The guard notebook only demonstrates the effect of fine-tuning.

## Requirements

- Python 3.10 or later
- One CUDA GPU (the notebooks load models with `bitsandbytes` 4-bit quantization)
- A Hugging Face account that has accepted the terms of these gated resources:
  - <https://huggingface.co/meta-llama/Llama-Guard-3-8B>
  - <https://huggingface.co/datasets/lianghsun/tw-ptt-keyboard-warrior-chat>
  - The model and dataset used by `llm-ocr-ft.ipynb` are public and need no extra approval

## Quick start

```bash
git clone https://github.com/aiDAPTIV-Phison/phisonai-cookbook.git
cd phisonai-cookbook
python -m venv .venv && source .venv/bin/activate
pip install jupyterlab
hf auth login
jupyter lab notebooks/
```

The first cell of each notebook installs what it needs with `%pip install`:

- Classification: `transformers trl peft bitsandbytes accelerate datasets huggingface_hub scikit-learn matplotlib tqdm`
- Style: `transformers trl peft bitsandbytes accelerate datasets huggingface_hub`
- OCR: `transformers trl peft bitsandbytes accelerate datasets pillow`

Run the cells in order. Training outputs (adapters, merged models, charts) are written to an `outputs/` folder under the directory where you started Jupyter.

## Environment variables

| Name | Required | Purpose |
|---|---|---|
| `HF_TOKEN` | No | Hugging Face access token. If it is not set, the notebooks use the token cached by `hf auth login`. |

## More information

For common questions (access errors on gated models, out-of-memory errors, switching the base model, and so on) see [docs/FAQ.md](docs/FAQ.md). Please read the [Code of Conduct](CODE_OF_CONDUCT.md) before opening an issue or pull request.

## License

This project is licensed under the [Apache License 2.0](LICENSE). Models and datasets are not covered by this license; each remains under its own original terms.
