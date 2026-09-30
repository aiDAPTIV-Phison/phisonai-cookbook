# FAQ

## Q: 載入模型或資料集時出現 401 或 403 錯誤，該怎麼辦？
- ID: `notebooks-access-001`
- Public: Yes
- Applies to: `main`
- Category: Installation
- Keywords:
  - `401`
  - `403`
  - `gated repo`
  - `access to model`
- Approved answer:
  1. `Llama-Guard-3-8B 與 tw-ptt-keyboard-warrior-chat 資料集都是受限（gated）資源。請在登入狀態下開啟該模型或資料集在 Hugging Face 上的頁面，接受授權條款，並等待存取權限核准。`
  2. `接著在執行環境用 hf auth login 登入，或將 HF_TOKEN 環境變數設為同一個帳號的 token，然後重新執行該 cell。`
  3. `llm-ocr-ft.ipynb 用的 Qwen3-VL-4B-Instruct 與 MathWriting-human 資料集都是公開資源，不會出現這個錯誤，也不需要另外申請授權。`
- Escalate to Jira when:
  - 已在 Hugging Face 頁面取得存取權限，重新登入後錯誤仍然存在
  - 錯誤訊息與上述不符

## Q: 訓練時停在 CUDA out of memory 錯誤，該怎麼辦？
- ID: `notebooks-runtime-001`
- Public: Yes
- Applies to: `main`
- Category: Runtime
- Keywords:
  - `CUDA out of memory`
  - `OutOfMemoryError`
  - `max_length`
- Approved answer:
  1. `重新啟動 kernel，釋放先前執行佔用的 GPU 記憶體，再從第一個 cell 開始依序執行。`
  2. `classification 與 style notebook：調低訓練設定 cell 中的 max_length（預設為 1024）。這兩本的每張 GPU batch size 已經是 1，所以縮短序列長度是剩下主要可以降低的設定。`
  3. `ocr notebook：這本的 max_length 設為 None（不截斷，避免切掉圖片 token），無法用縮短序列長度的方式降低記憶體。請改把 per_device_train_batch_size 從預設的 2 調低到 1，並把 gradient_accumulation_steps 等比例調高（例如從 8 調成 16），維持相同的有效 batch size。`
  4. `若仍然失敗，請改用記憶體更大的 GPU。classification notebook 載入 8B 參數的模型，需要的記憶體比載入 4B 參數模型的 style / ocr notebook 更多。`
- Escalate to Jira when:
  - 已依上述方式調整（縮短 max_length，或調低 per_device_train_batch_size）且使用記憶體充足的 GPU，仍然發生記憶體不足
  - 錯誤訊息與上述不符

## Q: 執行完安裝 cell 後出現 ImportError 或 bitsandbytes 相關錯誤，該怎麼辦？
- ID: `notebooks-install-001`
- Public: Yes
- Applies to: `main`
- Category: Installation
- Keywords:
  - `ImportError`
  - `bitsandbytes`
  - `CUDA`
  - `%pip install`
- Approved answer:
  1. `%pip install 的 cell 執行完成後，請重新啟動 kernel，再執行其餘的 cell。在執行中的 kernel 內安裝的套件，有時要重啟後才會被載入。`
  2. `在 cell 中執行 torch.cuda.is_available() 確認 CUDA GPU 是否可見。notebook 透過 bitsandbytes 做 4-bit 量化，需要 CUDA GPU。`
- Escalate to Jira when:
  - torch.cuda.is_available() 回傳 True，但 bitsandbytes 仍然無法載入
  - 錯誤訊息與上述不符

## Q: 微調後的回答看起來差異很小，或沒有變好，是哪裡出錯了嗎？
- ID: `notebooks-results-001`
- Public: Yes
- Applies to: `main`
- Category: Usage
- Keywords:
  - `results`
  - `MAX_STEPS`
  - `not converged`
- Approved answer:
  1. `使用預設設定時這是預期的結果。notebook 只訓練少量步數（例如 style notebook 的 MAX_STEPS = 30），目的是展示流程，不是訓練到收斂。`
  2. `想看到更明顯的效果，請調高 MAX_STEPS；classification notebook 也可以一併調高 TRAIN_LIMIT。訓練時間會隨之等比例增加。`
- Escalate to Jira when:
  - 大幅調高 MAX_STEPS 後，結果仍完全沒有變化
  - 錯誤訊息與上述不符

## Q: 可以換成別的基底模型或資料集嗎？
- ID: `notebooks-usage-001`
- Public: Yes
- Applies to: `main`
- Category: Usage
- Keywords:
  - `MODEL_ID`
  - `BASE_MODEL_ID`
  - `target_modules`
  - `dataset`
- Approved answer:
  1. `style notebook 可以：修改 MODEL_ID，並確認 LoRA 的 target_modules 名稱與新模型的層名稱相符。`
  2. `classification notebook 是圍繞 Llama Guard 3 的 prompt 格式與 safe/unsafe 輸出設計的，換成 Llama Guard 3 以外的模型時，需要一併調整組 prompt 與解析輸出的程式碼。`
  3. `換資料集時，需先轉成 notebook 預期的欄位格式，例如 style notebook 需要由 role/content 組成的 messages 欄位。`
- Escalate to Jira when:
  - 已依上述方式更換模型或資料集，訓練仍然失敗
  - 錯誤訊息與上述不符

## Q: 訓練好的 adapter 與輸出存在哪裡？有被上傳到任何地方嗎？
- ID: `notebooks-usage-002`
- Public: Yes
- Applies to: `main`
- Category: Usage
- Keywords:
  - `outputs`
  - `adapter`
  - `save`
- Approved answer:
  1. `輸出會寫到啟動 Jupyter 的目錄下的 outputs 資料夾。style notebook 會存 LoRA adapter，選用的合併步驟會另存完整的合併模型；classification notebook 會存合併後的模型。`
  2. `不會上傳到 Hugging Face 或其他任何地方，本儲存庫也不提供訓練好的權重。`
- Escalate to Jira when:
  - 執行儲存的 cell 後，outputs 資料夾中沒有出現檔案
  - 錯誤訊息與上述不符
