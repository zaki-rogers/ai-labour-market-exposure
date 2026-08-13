# AI Labour Market Exposure
Exploratory analysis of Claude.ai usage across U.S. occupations, using the Anthropic Economic Index, compared against BLS employment and wage data

## Overview
This project analyses how Claude.ai Free and Pro usage is distributed across the U.S. labour market, using occupation and task-level data from the Anthropic Economic Index (February 2025 release), combined with Bureau of Labor Statistics (BLS) employment and wage data. The central question: does AI usage concentrate in particular occupations and wage levels disproportionately to their actual share of employment, and what does that pattern suggest about the kind of work AI is currently being used for?

The analysis independently reconstructs the Index's occupation-level methodology from the raw task-level data, then extends it with original comparisons against employment share and wage data to test this question.

## Data Sources
All data was downloaded from the [Anthropic Economic Index dataset on Hugging Face](https://huggingface.co/datasets/Anthropic/EconomicIndex/tree/main/release_2025_02_10) (February 2025 release)

- `onet_task_statements.csv`, `SOC_Structure.csv`: O*NET/SOC occupation and task taxonomy data, bundled into the release for reproducibility.
- `onet_task_mappings.csv`, `automation_vs_augmentation.csv`: Claude.ai usage data, broken down by task and by automation/augmentation interaction type respectively.
- `bls_employment_may_2023.csv`, `wage_data.csv`: BLS employment and wage statistics, also bundled into the release.

The `data/` folder is not tracked in this repository (see `.gitignore`). Download the six files above from the link and place them in a local `data/` folder before running the notebook.

## Methodology
O*NET-SOC occupation codes are mapped to SOC major groups to categorise occupations, then joined to task-level usage data. Because a single task can belong to multiple occupations, usage percentages are corrected for this fan-out before aggregating to the category and occupation level. The aggregated usage data is then merged against BLS employment figures to compute a representation gap between usage share and employment share, and against BLS wage data to examine the relationship between usage and salary. Separately, the automation vs augmentation interaction type data is compared directly against the published breakdown to verify the underlying classification.

## Key Findings
- **Representation gap**: Computer and Mathematical Occupations show by far the largest positive gap between usage share and employment share. Transportation and Material Moving, Food Preparation and Serving Related, and Sales and Related Occupations show the largest negative gaps. This pattern tracks the nature of the work itself: the most over-represented categories are largely text- and knowledge-based, the kind of task a conversational AI tool can meaningfully assist with. The most under-represented categories involve physical, hands-on labour that a text interface has little to offer.
- **Usage vs wage**: Usage doesn't rise steadily with salary. It peaks in a specific band, roughly $75,000-$110,000, dominated by software and tech-adjacent occupations like Computer Programmers, Software Developers, and Web Developers. Usage drops off sharply both below and above this band; occupations earning over $150,000 show almost no usage at all. This is why the linear correlation is weak (~0.09): usage tracks occupation type, not pay level, and only happens to overlap with high pay within this specific band of technical roles.
- **Automation vs Augmentation**: After renormalising to match the paper's methodology, Augmentation-style interactions (task iteration, learning, validation) make up roughly 57% of classified usage, versus 43% for Automation-style interactions (directive, feedback loop). Task iteration alone is the single largest category at 31%, and validation the smallest at under 3%. This suggests people use Claude more often to iteratively refine work already in progress than to fully hand off a task or simply check finished work.

## Limitations
- A single task row with a placeholder `"none"` value was dropped, as it carried no usable information and caused a division error in the usage-scaling step.
- Occupation-level comparisons cover only the 756 occupations with at least one Claude.ai usage-tracked task. Occupations in the O*NET/BLS reference data with no matching usage figures aren't represented. This limit comes from usage-data coverage, not the wage merge itself, which matched all 756 occupations cleanly.
- Four performing-arts occupations (actors, dancers, singers, musicians) report BLS wages as hourly rather than annual figures. These were converted to annual estimates using standard full-time hours (40 x 52), a simplifying assumption rather than an exact figure.
- The usage vs wage correlation is weak under a linear measure. This doesn't rule out a non-linear relationship, which the scatter plot is better suited to reveal than the correlation coefficient alone.
- The six recorded automation/augmentation interaction types, including `none`, sum to only 84.2%, leaving roughly 15.8% of usage unaccounted for by any category. The source of this gap isn't resolved by the available data.
- The underlying dataset only captures Claude.ai Free and Pro conversations. API, Team, and Enterprise usage aren't included, so findings reflect consumer-side usage patterns rather than the full picture of how Claude is used across the economy.
- Claude is marketed specifically as a strong coding model, so the dominance of Computer and Mathematical Occupations in the representation gap may partly reflect product positioning rather than purely reflecting which occupations AI is generally best suited to help with.

## Project Structure
```bash
ai-labour-market-exposure/
├── data/
│   ├── automation_vs_augmentation.csv
│   ├── bls_employment_may_2023.csv
│   ├── onet_task_mappings.csv
│   ├── onet_task_statements.csv
│   ├── SOC_Structure.csv
│   └── wage_data.csv
├── eda.ipynb
├── requirements.txt
├── LICENSE
├── README.md
└── .gitignore
​```

## Setup
```bash
git clone https://github.com/zaki-rogers/ai-labour-market-exposure.git
cd ai-labour-market-exposure
pip install -r requirements.txt
```

Download the datasets listed under [Data Sources](#data-sources) into a local `data/` folder, then open and run `eda.ipynb`.

## License
Released under the [MIT License](LICENSE). Anthropic Economic Index data is released separately under CC-BY and is not redistributed in this repository.