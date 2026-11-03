English | [繁體中文](FAQ.zh-TW.md)

# FAQ

## Q: I get a 401 or 403 error when loading the model or dataset. What should I do?
- ID: `notebooks-access-001`
- Public: Yes
- Applies to: `llm-notebooks`
- Category: Installation
- Keywords:
  - `401`
  - `403`
  - `gated repo`
  - `access to model`
- Approved answer:
  1. `Llama-Guard-3-8B and the tw-ptt-keyboard-warrior-chat dataset are gated resources. While logged in, open the model or dataset page on Hugging Face, accept the terms, and wait for access to be granted.`
  2. `Then log in from the runtime environment with hf auth login, or set the HF_TOKEN environment variable to a token from the same account, and run the cell again.`
  3. `The Qwen3-VL-4B-Instruct model and the MathWriting-human dataset used by llm-ocr-ft.ipynb are public, so they do not cause this error and need no separate approval.`
- Escalate to Jira when:
  - Access has been granted on the Hugging Face page and the error persists after logging in again
  - The error message does not match the above

## Q: Training stops with a CUDA out of memory error. What should I do?
- ID: `notebooks-runtime-001`
- Public: Yes
- Applies to: `llm-notebooks`
- Category: Runtime
- Keywords:
  - `CUDA out of memory`
  - `OutOfMemoryError`
  - `max_length`
- Approved answer:
  1. `Restart the kernel to free GPU memory held by earlier runs, then run the cells in order from the first one.`
  2. `Classification and style notebooks: lower max_length in the training settings cell (default 1024). The per-GPU batch size in these two notebooks is already 1, so shortening the sequence length is the main remaining setting that reduces memory.`
  3. `OCR notebook: max_length is set to None here (no truncation, so image tokens are not cut), so memory cannot be reduced by shortening the sequence. Instead, lower per_device_train_batch_size from the default 2 to 1 and raise gradient_accumulation_steps by the same factor (for example from 8 to 16) to keep the same effective batch size.`
  4. `If it still fails, use a GPU with more memory. The classification notebook loads an 8B-parameter model and needs more memory than the style / ocr notebooks, which load 4B-parameter models.`
- Escalate to Jira when:
  - Settings were adjusted as above (shorter max_length, or lower per_device_train_batch_size) on a GPU with enough memory and memory still runs out
  - The error message does not match the above

## Q: After running the install cell I get an ImportError or a bitsandbytes error. What should I do?
- ID: `notebooks-install-001`
- Public: Yes
- Applies to: `llm-notebooks`
- Category: Installation
- Keywords:
  - `ImportError`
  - `bitsandbytes`
  - `CUDA`
  - `%pip install`
- Approved answer:
  1. `After the %pip install cell finishes, restart the kernel and then run the remaining cells. Packages installed into a running kernel sometimes only load after a restart.`
  2. `Run torch.cuda.is_available() in a cell to confirm a CUDA GPU is visible. The notebooks use bitsandbytes 4-bit quantization, which needs a CUDA GPU.`
- Escalate to Jira when:
  - torch.cuda.is_available() returns True but bitsandbytes still fails to load
  - The error message does not match the above

## Q: The answers after fine-tuning look only slightly different, or not better. Did something go wrong?
- ID: `notebooks-results-001`
- Public: Yes
- Applies to: `llm-notebooks`
- Category: Usage
- Keywords:
  - `results`
  - `MAX_STEPS`
  - `not converged`
- Approved answer:
  1. `This is expected with the default settings. The notebooks train for only a few steps (for example MAX_STEPS = 30 in the style notebook) to show the workflow, not to train to convergence.`
  2. `For a clearer effect, raise MAX_STEPS; in the classification notebook you can also raise TRAIN_LIMIT. Training time grows proportionally.`
- Escalate to Jira when:
  - Results do not change at all after raising MAX_STEPS substantially
  - The error message does not match the above

## Q: Can I use a different base model or dataset?
- ID: `notebooks-usage-001`
- Public: Yes
- Applies to: `llm-notebooks`
- Category: Usage
- Keywords:
  - `MODEL_ID`
  - `BASE_MODEL_ID`
  - `target_modules`
  - `dataset`
- Approved answer:
  1. `The style notebook: yes. Change MODEL_ID and make sure the LoRA target_modules names match the layer names of the new model.`
  2. `The classification notebook is built around Llama Guard 3's prompt format and safe/unsafe output. With a model other than Llama Guard 3, the code that builds the prompt and parses the output must be adjusted too.`
  3. `When switching datasets, first convert the data into the columns the notebook expects; for example, the style notebook needs a messages column made of role/content pairs.`
- Escalate to Jira when:
  - The model or dataset was switched as described above and training still fails
  - The error message does not match the above

## Q: Where are the trained adapters and outputs saved? Are they uploaded anywhere?
- ID: `notebooks-usage-002`
- Public: Yes
- Applies to: `llm-notebooks`
- Category: Usage
- Keywords:
  - `outputs`
  - `adapter`
  - `save`
- Approved answer:
  1. `Outputs are written to the outputs folder under the directory where you started Jupyter. The style notebook saves the LoRA adapter, and its optional merge step also saves the full merged model; the classification notebook saves the merged model.`
  2. `Nothing is uploaded to Hugging Face or anywhere else, and this repository does not provide trained weights.`
- Escalate to Jira when:
  - No files appear in the outputs folder after running the save cell
  - The error message does not match the above
