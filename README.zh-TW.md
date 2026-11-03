[English](README.md) | 繁體中文

# llm-notebooks

三本 Jupyter notebook，並排展示單張 GPU 的 QLoRA 微調會讓 LLM 的行為產生什麼變化。每本 notebook 都用同樣的輸入分別在微調前後各跑一次，讓差異一眼可見。

| Notebook | 基底模型 | 資料集 | 展示內容 |
|---|---|---|---|
| [notebooks/llm-classification-ft.ipynb](notebooks/llm-classification-ft.ipynb) | `meta-llama/Llama-Guard-3-8B` | `ai4privacy/pii-masking-65k`（訓練）、`ai4privacy/pii-masking-200k`（驗證） | 讓 guard 模型能在同一段文字中辨識多重 PII（個資）違規，並比較微調前後的 F1、precision、recall 與 confusion matrix。 |
| [notebooks/llm-style-ft.ipynb](notebooks/llm-style-ft.ipynb) | `Qwen/Qwen3-4B-Instruct-2507` | `lianghsun/tw-ptt-keyboard-warrior-chat` | 讓聊天模型學會台灣 PTT 鄉民的說話風格，並對照同一組 prompt 在微調前後的回覆。 |
| [notebooks/llm-ocr-ft.ipynb](notebooks/llm-ocr-ft.ipynb) | `Qwen/Qwen3-VL-4B-Instruct` | `deepcopy/MathWriting-human` | 讓視覺語言模型把手寫數學算式圖片轉成 LaTeX，並在測試資料上比較微調前後的 exact match 與字元錯誤率（CER），展示微調前答錯、微調後答對的例子。 |

notebook 裡的說明先寫英文，後面接原本的繁體中文。

## 本專案不包含

- 不是正式環境的訓練流程。步數與資料量刻意設得很小（例如 style notebook 的 `MAX_STEPS = 30`），目的是讓 notebook 能快速跑完、展示流程，不追求最佳準確率。
- 不提供模型權重或資料集。兩者都在執行時從 Hugging Face 下載，並適用各自的授權條款。
- 不是可直接部署的安全過濾器。guard notebook 只用來示範微調的效果。

## 環境需求

- Python 3.10 或以上
- 一張 CUDA GPU（notebook 使用 `bitsandbytes` 4-bit 量化載入模型）
- 已接受下列受限資源授權條款的 Hugging Face 帳號：
  - <https://huggingface.co/meta-llama/Llama-Guard-3-8B>
  - <https://huggingface.co/datasets/lianghsun/tw-ptt-keyboard-warrior-chat>
  - `llm-ocr-ft.ipynb` 用的模型與資料集皆為公開資源，不需要額外的授權

## 快速開始

```bash
git clone https://github.com/aiDAPTIV-Phison/phisonai-cookbook.git
cd phisonai-cookbook
python -m venv .venv && source .venv/bin/activate
pip install jupyterlab
hf auth login
jupyter lab notebooks/
```

每本 notebook 的第一個 cell 會用 `%pip install` 安裝所需套件：

- Classification：`transformers trl peft bitsandbytes accelerate datasets huggingface_hub scikit-learn matplotlib tqdm`
- Style：`transformers trl peft bitsandbytes accelerate datasets huggingface_hub`
- OCR：`transformers trl peft bitsandbytes accelerate datasets pillow`

請依序執行各個 cell。訓練產出（adapter、合併後的模型、圖表）會寫到啟動 Jupyter 的目錄下的 `outputs/` 資料夾。

## 環境變數

| 名稱 | 必填 | 用途 |
|---|---|---|
| `HF_TOKEN` | 否 | Hugging Face access token。未設定時，notebook 會改用 `hf auth login` 快取的 token。 |

## 更多說明

常見問題（受限模型的存取錯誤、記憶體不足、更換基底模型等）請見 [docs/FAQ.zh-TW.md](docs/FAQ.zh-TW.md)。開 issue 或 pull request 之前，請先閱讀 [行為準則](CODE_OF_CONDUCT.zh-TW.md)。

## 授權

本專案以 [Apache License 2.0](LICENSE) 授權。模型與資料集不在此授權範圍內，各自適用其原本的授權條款。
