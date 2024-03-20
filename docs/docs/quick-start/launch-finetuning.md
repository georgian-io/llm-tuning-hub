---
sidebar_position: 1
---

# Finetuning

## Launch Finetuning Job

The toolkit has everything you need to get started. This guide will walk you through the initial setup, explain the key components of the configuration, and offer advice on customizing your fine-tuning job. Let's dive in!

First, make sure you have read the [installation guide](getting-started.md#installation) and installed all the dependencies. Then, To launch a LoRA fine-tuning job, run the following command in your terminal:

```bash
python3 toolkit.py
```

## Default Config

This command initiates the fine-tuning process using the settings specified in the default YAML configuration file `config.yaml`.

```yaml
save_dir: "./experiment/"

ablation:
  use_ablate: false

# Data Ingestion -------------------
data:
  file_type: "huggingface" # one of 'json', 'csv', 'huggingface'
  path: "yahma/alpaca-cleaned"
  prompt:
    >- # prompt, make sure column inputs are enclosed in {} brackets and that they match your data
    Below is an instruction that describes a task. 
    Write a response that appropriately completes the request. 
    ### Instruction: {instruction}
    ### Input: {input}
    ### Output:
  prompt_stub:
    >- # Stub to add for training at the end of prompt, for test set or inference, this is omitted; make sure only one variable is present
    {output}
  test_size: 0.1 # Proportion of test as % of total; if integer then # of samples
  train_size: 0.9 # Proportion of train as % of total; if integer then # of samples
  train_test_split_seed: 42

# Model Definition -------------------
model:
  hf_model_ckpt: "NousResearch/Llama-2-7b-hf"
  quantize: true
  bitsandbytes:
    load_in_4bit: true
    bnb_4bit_compute_dtype: "bf16"
    bnb_4bit_quant_type: "nf4"

# LoRA Params -------------------
lora:
  task_type: "CAUSAL_LM"
  r: 32
  lora_alpha: 16
  lora_dropout: 0.1
  target_modules:
    - q_proj
    - v_proj
    - k_proj
    - o_proj
    - up_proj
    - down_proj
    - gate_proj

# Training -------------------
training:
  training_args:
    num_train_epochs: 5
    per_device_train_batch_size: 4
    optim: "paged_adamw_32bit"
    learning_rate: 2.0e-4
    bf16: true # Set to true for mixed precision training on Newer GPUs
    tf32: true
  sft_args:
    max_seq_length: 1024

inference:
  max_new_tokens: 1024
  do_sample: True
  top_p: 0.9
  temperature: 0.8
```

🎉 Congradulations! You've ran the first fine-tuning job using this toolkit!