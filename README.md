# LLM01 Prompt Seed Dataset Collection

This repository contains the **data artifacts** prepared for the LLM01 (Prompt Injection) stage of the Mutation Engine pipeline.

The collection was built by selecting dataset-wise prompt seed samples, normalizing them into a unified schema, validating the normalized outputs, merging them into a single prompt-seed collection, and then transforming the merged set into a Mutation Engine-ready `mutation_seed_v1` format.

This repository is intended to store the **data outputs** of the pipeline.  
The corresponding preprocessing, validation, merge, and conversion scripts are maintained separately in the main project repository.

---

## Overview

The goal of this dataset collection is to provide a curated and validated set of **LLM01-oriented prompt seeds** for mutation-based experiments.

The pipeline used in this repository is:

```text
dataset-wise sampled raw
→ normalized_sample
→ normalized_sample validation
→ merged prompt seed dataset
→ merged dataset validation
→ mutation_seed_v1 conversion
→ mutation seed validation
```

The final dataset is designed to be used as input to a Mutation Engine that mutates attacker-controlled prompt inputs while preserving dataset provenance and validation metadata.

## Source Datasets
The final collection contains seeds sampled from the following five datasets:
- AdvBench
- HackAPrompt
- DoNotAnswer
- BeaverTails
- LLMail
These datasets were used as prompt-seed sources for LLM01-oriented experiments, with bucket_tags fixed to:
```
["LLM01_PROMPT_INJECTION"]
```

## Final Dataset Size
The validated merged prompt-seed collection contains 100 rows in total.
#### Dataset-wise counts
- AdvBench: 15
- HackAPrompt: 35
- DoNotAnswer: 20
- BeaverTails: 15
- LLMail: 15
#### Total
- 100 prompt seeds

## Repository Structure
```
sample/
  normalized_sample/
    normalized_sample_advbench.jsonl
    normalized_sample_hackaprompt.jsonl
    normalized_sample_donotanswer.jsonl
    normalized_sample_beavertails.jsonl
    normalized_sample_llmail.jsonl

  merged/
    prompt_seed_dataset_llm01.jsonl
    prompt_seed_dataset_llm01_summary.json
    prompt_seed_dataset_llm01_validation_summary.json

    mutation_seeds_llm01.jsonl
    mutation_seeds_llm01_summary.json
    mutation_seeds_llm01_validation_summary.json
```

## Main Files
#### 1. Dataset-wise normalized sample files
These files contain dataset-specific sampled raw data converted into a unified normalized schema.
```
normalized_sample_advbench.jsonl
normalized_sample_hackaprompt.jsonl
normalized_sample_donotanswer.jsonl
normalized_sample_beavertails.jsonl
normalized_sample_llmail.jsonl
```
#### 2. Merged prompt seed dataset
- prompt_seed_dataset_llm01.jsonl
This is the validated merged collection of normalized prompt seeds.

#### 3. Mutation Engine-ready seed dataset
- mutation_seeds_llm01.jsonl
This file is the final output intended for Mutation Engine input.
It extends the merged prompt seed dataset with mutation-specific fields such as:
```
attack_surface
mutation_target_text
mutation_target_role
is_mutable
source_format
```
## Validation Status
The merged prompt seed dataset passed structural and count-based validation.

### Prompt seed dataset validation summary
```
status: ok
total_rows: 100
unique_record_ids: 100
```
### Dataset counts
```
advbench: 15
hackaprompt: 35
do_not_answer: 20
beavertails: 15
llmail: 15
```
The corresponding validation summary is stored in:
- sample/merged/prompt_seed_dataset_llm01_validation_summary.json

### Mutation seed dataset validation
The mutation seed dataset was also validated after conversion into mutation_seed_v1-style format.
The corresponding summary is stored in:
```
sample/merged/mutation_seeds_llm01_validation_summary.json
```
### Unified Normalized Schema
The normalized prompt-seed dataset preserves common provenance and content fields across datasets.
Key top-level fields include:
```
dataset_name
subset_or_split
record_id
input_primary
input_secondary
context_text
candidate_output
reference_output
label_binary
category_primary
category_multilabel_json
metadata_json
raw_json
bucket_tags
```
### Notes
- record_id is preserved from tagged sample artifacts when available.
- sample_index and sample_label are stored in metadata_json.
- raw_json preserves the sampled source row used during normalization.

## Mutation Seed Conversion Rules
The merged prompt seed dataset was converted into a Mutation Engine-ready format using the following rules.

###  Common fields
```
bucket_tags = ["LLM01_PROMPT_INJECTION"]
mutation_target_role = "attacker_controlled_input"
is_mutable = true
source_format = "mutation_seed_v1"
```
### Attack surface mapping
```
llmail → email_body
all other datasets → prompt_text
```
### Mutation target mapping
For the current LLM01 collection:
```mutation_target_text = input_primary```
This means the mutation target is the attacker-controlled prompt-side text selected during normalization.

## Important Implementation Notes
### 1. Fixed LLM01 bucket assignment

Although the original source datasets contain heterogeneous safety categories and task types, this collection fixes bucket_tags to LLM01_PROMPT_INJECTION because the current experimental setup uses all selected samples as prompt-side mutation seeds for the LLM01 pipeline.

### 2. Nested raw_json

Some records preserve the tagged sample row inside raw_json, which may itself contain another nested raw_json from earlier preprocessing stages. This is intentional for provenance retention, though it results in a heavier record structure.

### 3. Sample-focused artifact set

This repository stores the sampled and validated subset artifacts, not the full original corpora.

### Intended Use
This dataset collection is intended for:
```
Mutation Engine input preparation
LLM01 prompt mutation experiments
Scanner benchmarking and evaluator testing
Controlled seed-based adversarial prompt generation
```
It is not intended to be treated as a canonical reproduction of the full source datasets.

### Provenance
This dataset collection was produced through a staged preprocessing pipeline that included:
1. dataset-wise sample selection
2. schema normalization
3. normalized dataset validation
4. merged prompt-seed construction
5. mutation seed conversion
6. mutation seed validation

The scripts used to generate these artifacts are maintained separately in the main project repository.

### Citation / Reference
If this dataset collection is used in internal reports, benchmarking logs, or experimental write-ups, it is recommended to reference it as:
```
LLM01 Prompt Seed Dataset Collection for Mutation Engine experiments, built from AdvBench, HackAPrompt, DoNotAnswer, BeaverTails, and LLMail, validated and converted into mutation_seed_v1 format.
```
