# CALAMITA: Challenge the Abilities of LAnguage Models in ITAlian

[![](https://img.shields.io/badge/Link-Website-blue)](https://clic2024.ilc.cnr.it/calamita/)
[![](https://img.shields.io/badge/Slack-Workspace-orange)]([https://clic2024.ilc.cnr.it/calamita/](https://join.slack.com/t/calamitaworkspace/shared_invite/zt-2ocxq4h3p-h~8aoVqmkWg7ygkTtonQ4Q))


**⏰ Timeline**
- ~~17th May 2024: pre-proposal submission~~
- ~~27th May 2024: notification of pre-proposal acceptance~~
- ~~End of May 2024: distribution of challenge paper template and further instructions~~
- **(Upcoming)** 16th September 2024: data and challenge paper submission
- 14th October 2024: benchmark ready with reports for each challenge
- October-November 2024: running selected models on benchmark with analyses (the CALAMITA team will do this)
- 4th-6th December 2024: CLIC-it conference in Pisa. Co-located half-day CALAMITA workshop scheduled for December 6th.



## Submission Guidelines

This document introduces the main technical details of how we intend to run the evaluation on CALAMITA tasks. 
It also lists each team's requirements to complete its submission to the shared task. 
We tried to draft this document as comprehensively as possible, but if anything needs clarification, please [join our Slack workspace](https://join.slack.com/t/calamitaworkspace/shared_invite/zt-2ocxq4h3p-h~8aoVqmkWg7ygkTtonQ4Q) for help.

**TLDR**: we will ask you
1. a HuggingFace Dataset with your data and
2. a GitHub Gist compiled with some information to run models against your task.

> [!TIP]
> If you have your data ready and a clear evaluation in mind, preparing these requirements will take around an hour.

#### How we intend to run CALAMITA
We will follow standard evaluation practices to promote standardization and foster reproducibility. Namely, we will:
- Use standard prompting (zero- or few-shot) and compute one or more numerical scores based on the system output(s).
- Base the CALAMITA evaluation codebase on the [lm-eval-harness framework](https://github.com/EleutherAI/lm-evaluation-harness).
- Request each team some additional information to comply with it (see the next section). 

Moreover, for categorization and analysis purposes, we will fit every task in a custom taxonomy and assign each submitted task one of the labels: 1) Natural Language Understanding, 2) Commonsense and Factual Knowledge, or 3) Bias, Fairness, and Safety.

### List of requirements
To comply with our evaluation standards, each team must provide 1) data in a standardized format, 2) prompting details, and 3) details about their evaluation metric(s).    

#### 1) Data Format
We request that each team make data accessible through the Huggingface Hub. 
The platform's extreme flexibility regarding column types, licensing, and no maximum size restrictions make it convenient.
Creating and publishing a dataset on HF is a one-off 30-minute procedure. For more details, please refer to [the official guide](https://huggingface.co/docs/hub/datasets-adding).
If you need support, do not hesitate to reach out.

Some general notes:
- Datasets do not have to respect any particular format: each example constitutes a row, which is composed of features (columns). 
- If your task requires few-shot prompting, think ahead about where to sample few-shot examples (also known as “task demonstrations”). If you do not want to sample from the whole evaluation set, you can consider setting aside some data that will only be used as a pool for few-shot exemplars and dedicating a dataset configuration to it (see [this dataset](https://huggingface.co/datasets/RiTA-nlp/GeNTE_ita-eval) with a “test” configuration with held-out samples).
- Set memorable and talkative column names, which will be easier to use to compile the prompt.

> [!IMPORTANT]
> Several options exist if you cannot or prefer not to store the data on Huggingface’s servers. You can still create a dataset on Huggingface that, when we prompt it, retrieves the data from an external server (see [ITALIC](https://huggingface.co/datasets/RiTA-nlp/ITALIC) for an example). We would appreciate this solution. Otherwise, **if your data cannot be shared publicly, please submit it as a zip file when submitting the report on Softconf**.

#### 2) Prompting details

We request each team to share a [Github Gist](​​https://gist.github.com/) with us with various variables compiled. See [this example gist](https://gist.github.com/g8a9/1ba39187ed7562927c6a617067a2c04d) for reference. Specifically, we need:

- The **huggingface hub ID** of your dataset.
- The **output type**. If submitting a multiple-choice question-answering task, set it to “multiple_choice.” Otherwise, set it to “open-ended.”
- A **prompt template**. The template string can use variables between curly brackets to compile the prompt with values from your dataset columns (see the gist for an example).
  - We do not restrict the template format or length. However, be mindful that some modern LMs support 1,024 and, depending on the type of your task (e.g., it requires few-shot inputs or long outputs with several reasoning steps), long templates might be unusable with some models. Use Slack or Github Issues if you have doubts.
  - The prompt should not include any system message (e.g., “You are a helpful assistant.”). System messages are typically model-dependent and a model developers’ design choice.
  - You can specify up to one prompt template per task/gist. If your evaluation involves testing multiple prompts, please reach out on Slack.
- The **number of task demonstrations** (shots), where to take them from, and which sampling strategy. You have several options. Please refer to the fields in the example Gist to set these attributes.
  - If task demonstrations (shots) are intended to be static (i.e., we will prompt all rows with the same demonstrations), you can either write them directly in the prompt (and specify `N_SHOTS_TO_SAMPLE = 0`) or set `N_SHOTS_TO_SAMPLE` with a number greater than 0 and `SHOTS_SAMPLE_STRATEGY` to “sequential” so that we will always retrieve the same first `N_SHOTS_TO_SAMPLE`s.
  - If demonstrations must be sampled randomly for each row, set `N_SHOTS_TO_SAMPLE` accordingly and `SHOTS_SAMPLE_STRATEGY` to “random.”

#### 3) Evaluation details
Finally, use the same Gist to define the evaluation metrics we should use for your task.

- The **evaluation metric(s)**. You can specify one or more metrics. If your metric is standard, just use its name (e.g., “accuracy,” “f1_macro”, etc.); otherwise, specify the name of the function and define it in the gist.
  - When defining custom evaluation functions, feel free to use any definition (function name, number, and name of parameters) – we will take care of making it work in our codebase. If custom functions have dependencies, we will do our best to figure them out and reach out to the team if we fail. 
  - See [this “open-ended” gist](https://gist.github.com/g8a9/f5e82d38ce12831323b20dc79b0452c9) using xCOMET to evaluate translation outputs. 
  - We will use the first metric of the list as the main task metric (e.g., if we need to compute an aggregate across CALAMITA tasks). 

### How to submit your HF dataset and Gist
Follow [this link](https://docs.google.com/spreadsheets/d/1Z_RMMMTkuESjUdOxMEDVbGr2A9xHzZva-1EhTAaIoeA/edit?gid=0#gid=0) (the first time, you will have to request access to it) and add a row for each task you submit. A task is considered the combination of a dataset and a gist. Thus, **you can submit multiple tasks per dataset, each with a different gist**. 

### FAQ

**Do I need to run models or provide running any code and/or bash scripts?**

No, you don’t. We will develop a codebase to run the evaluation on your data and run it ourselves.

**Should my prompt be in Italian, English, or any other specific language?**

Any language is okay. We consider prompt and prompt language design choices and leave them up to the task proposers.   

**Should I provide decoding details?**

No, you don’t. We will standardize decoding parameters.
