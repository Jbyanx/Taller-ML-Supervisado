---
license: cc-by-4.0
task_categories:
- text-classification
- text-generation
language:
- es
tags:
- stance-detection
- spanish-corpus
- cross-topic
- argument-mining
- natural-language-processing
- citizen-participation
- perspectivism
- crowdsource-annotations
size_categories:
- 1K<n<10K
---

# Dataset Introduction

This corpus comprises **3,000 manually annotated instances** for **stance detection** in Spanish, built from real citizen comments posted on the [Decide Madrid](https://decide.madrid.es) participatory democracy platform. Each instance consists of a civic topic (*target*) — defined by its title and description — paired with a citizen comment, annotated for stance as **favor**, **against**, or **neutral** by **3 independent human annotators**.

The dataset is published in full accordance with the principles of **data perspectivism**: rather than collapsing annotations into a single ground-truth label and discarding disagreement, we release all individual judgments. This enables research on annotator disagreement, ambiguity modeling, and multi-perspective stance classification.

## Dataset Details

### Dataset Description

The **ALIA Spanish Discriminative Stance Detection Corpus** is a manually annotated dataset designed for training and evaluating stance detection models in Spanish. Given a civic topic (*target*) and a citizen comment, the task is to classify the comment's stance toward the topic into one of three categories: **favor**, **against**, or **neutral**.

The corpus is constructed from first-level comments responding directly to debates and proposals on the Decide Madrid platform. It is **cross-topic** by design: comments span 387 distinct topics covering urban mobility, environmental policy, public services, accessibility, animal welfare, taxation, and many other areas of local governance. No fixed domain taxonomy is imposed — the diversity emerges naturally from the platform's citizen-driven agenda.

Each instance was annotated by **3 independent human annotators** via crowdsourcing on [Prolific](https://www.prolific.com). The final label is provided only when a majority exists (2/3 or 3/3 agreement); instances with full disagreement (1/1/1, 5.0% of the sample) have a missing `majority_label`.

In alignment with the Data Perspectivism manifesto, we publish the full set of individual annotations, enabling researchers to model annotator disagreement and subjective perspectives rather than forcing a single ground truth.

### Dataset Sources

- **Original data source:** [Decide Madrid](https://decide.madrid.es) — Portal de Datos Abiertos del Ayuntamiento de Madrid ([datos.madrid.es](https://datos.madrid.es))
- **License of original data:** Creative Commons Attribution 4.0 International (CC BY 4.0)

### Uses

The primary purpose of this corpus is to serve as a resource for training and evaluating stance detection and argument mining models in Spanish, with applications in:

- Training and fine-tuning text classification models for stance detection in civic and political discourse.
- Evaluating cross-topic generalization: models trained on some topics can be tested on unseen topics.
- Argument mining research: identifying supportive and opposing arguments in citizen-generated text.
- Sentiment and opinion analysis in participatory democracy platforms.
- Benchmarking stance detection systems in Spanish across diverse policy domains.
- Supporting the development of AI models for civic engagement and public consultation analysis.
- Studying inter-annotator agreement, subjectivity, and learning-from-disagreement in subjective NLP tasks.

## Dataset Structure

### Data Instances

Each instance in the corpus has the following structure:

```json
{
    "id": 1,
    "target": "Bajar las motos de las aceras.",
    "description": "Esta propuesta es originaria del vecino... Los motoristas, aparte del ruido a veces intolerable de sus motos, circulan hoy dia impunemente por las aceras de nuestra ciudad...",
    "comment": "A mi no me molesta tanto que aparquen en la acera. Me molesta mucho mas que circulen por ella...",
    "annotation_1": "neutral",
    "annotation_2": "against",
    "annotation_3": "neutral",
    "majority_label": "neutral"
}
```

### Data Fields

| Field | Type | Description |
|-------|------|-------------|
| **id** | integer | Unique identifier (1 to 3,000). |
| **target** | string | Title of the debate or proposal the comment responds to. |
| **description** | string | Descriptive text of the topic (HTML-cleaned plain text from the original debate/proposal). |
| **comment** | string | Citizen comment to be classified. |
| **annotation_1** | string | Label from annotator 1. Values: `favor`, `against`, `neutral`. |
| **annotation_2** | string | Label from annotator 2. Values: `favor`, `against`, `neutral`. |
| **annotation_3** | string | Label from annotator 3. Values: `favor`, `against`, `neutral`. |
| **majority_label** | string or null | Majority-vote label derived from the three annotations. Values: `favor`, `against`, `neutral`. `null` when all three annotators disagree (1/1/1). |

### Data Statistics

The dataset contains **3,000 annotated instances**:

| Metric | Value |
|--------|-------|
| Total Instances | 3,000 |
| Unique Topics (targets) | 387 |
| Columns | 8 |
| Annotators (unique) | 180 |
| Total Annotations | 9,000 (3 per instance) |

**Inter-Annotator Agreement:**

| Metric | Value |
|--------|-------|
| Fleiss' kappa | 0.406 (Moderate) |
| Unanimous agreement (3/3) | 1,539 instances (51.3%) |
| Majority agreement (2/3) | 1,311 instances (43.7%) |
| Full disagreement (1/1/1) | 150 instances (5.0%) — *no `majority_label`* |
| Gold standard pass rate | 100% |

**Label Distribution (majority-vote labels, 2,850 instances with majority):**

| Label | Count | Percentage |
|-------|-------|------------|
| favor | 1,780 | 62.5% |
| against | 627 | 22.0% |
| neutral | 443 | 15.5% |

**Text Length Characteristics:**

| Metric | Target Description | Comment |
|--------|-------------------|---------|
| Average length | ~945 characters | ~312 characters |
| Median length | ~647 characters | ~270 characters |
| Min length | 46 characters | 80 characters |
| Max length | ~5,916 characters | ~812 characters |

**Comments per Topic:**

| Metric | Value |
|--------|-------|
| Mean | 7.8 |
| Median | 4.0 |
| Minimum | 1 |
| Maximum | 106 |

### Example Usage

To load the dataset:

```python
from datasets import load_dataset

# Load the dataset
data = load_dataset("SINAI/ALIA-es-discriminative-stance-detection", trust_remote_code=True)

# Access the training split
example = data['train'][0]
print(f"ID: {example['id']}")
print(f"Target: {example['target']}")
print(f"Description: {example['description']}")
print(f"Comment: {example['comment'][:100]}...")
print(f"Annotations: {example['annotation_1']}, {example['annotation_2']}, {example['annotation_3']}")
print(f"Majority label: {example['majority_label']}")
```

Example of filtering by stance label:

```python
from datasets import load_dataset

# Load the dataset
dataset = load_dataset("SINAI/ALIA-es-discriminative-stance-detection", trust_remote_code=True)

# Filter comments in favor
favor_comments = dataset['train'].filter(lambda x: x['majority_label'] == 'favor')

# Filter comments against
against_comments = dataset['train'].filter(lambda x: x['majority_label'] == 'against')

# Filter neutral comments
neutral_comments = dataset['train'].filter(lambda x: x['majority_label'] == 'neutral')

print(f"Favor: {len(favor_comments)}")
print(f"Against: {len(against_comments)}")
print(f"Neutral: {len(neutral_comments)}")
```

Example of using individual annotations for disagreement modeling:

```python
from datasets import load_dataset
import pandas as pd

# Load dataset
dataset = load_dataset("SINAI/ALIA-es-discriminative-stance-detection", trust_remote_code=True)
df = dataset['train'].to_pandas()

# Instances with full disagreement
disagreed = df[df['majority_label'].isna()]
print(f"Instances with full disagreement: {len(disagreed)}")

# Distribution of annotation patterns for disagreed instances
annotation_patterns = disagreed[['annotation_1', 'annotation_2', 'annotation_3']].apply(
    lambda row: ' / '.join(sorted(row)), axis=1
)
print(annotation_patterns.value_counts())
```

Example of using the dataset for model training:

```python
from datasets import load_dataset
from transformers import AutoTokenizer

# Load dataset
dataset = load_dataset("SINAI/ALIA-es-discriminative-stance-detection", trust_remote_code=True)

# Filter out instances without a majority label for standard supervised training
train_data = dataset['train'].filter(lambda x: x['majority_label'] is not None)

# Load tokenizer
tokenizer = AutoTokenizer.from_pretrained("PlanTL-GOB-ES/roberta-large-bne")

# Preprocess function: concatenate target description and comment
def preprocess(examples):
    texts = [
        f"Target: {t}\nDescription: {d}\nComment: {c}"
        for t, d, c in zip(examples['target'], examples['description'], examples['comment'])
    ]
    return tokenizer(texts, truncation=True, padding=True, max_length=512)

# Apply preprocessing
tokenized = train_data.map(preprocess, batched=True)
```

## Dataset Creation

### Source Data

The corpus integrates citizen-generated text from the Decide Madrid participatory democracy platform, published by the Ayuntamiento de Madrid through its Open Data Portal. The platform allows citizens to create debates and proposals on local policy issues, and other citizens respond with comments.

**Original data files used:**
- **`debates.csv`** — Debate topics (title, description, votes)
- **`proposals.csv`** — Citizen proposals (title, description, votes)
- **`comments.csv`** — Comments posted on debates and proposals

All source data are published by the Ayuntamiento de Madrid under CC BY 4.0.

### Data Collection and Processing

The dataset was compiled through a multi-step filtering and sampling pipeline applied to the original Decide Madrid open data:

1. **First-level comment filtering** — Only comments responding directly to a debate or proposal were retained (replies to other comments were excluded, as they express stance toward other users rather than the topic).
2. **Quality filtering** — Comments were filtered by minimum and maximum length, spam detection (URLs without substantive text), automatic welcome messages, and off-topic patterns.
3. **Topic filtering** — Topics without descriptions were excluded; only topics with substantial descriptive text were retained.
4. **Stratified sampling** — 3,000 comments were selected via proportional stratified sampling by topic, ensuring cross-topic diversity. The sampling guaranteed at least 3 comments per topic and capped highly represented topics at 50 comments.

**Quality control procedures included:**
- Removal of non-substantive comments (spam, automatic messages, jurisdictional redirects without stance)
- Deduplication of identical (topic, comment) pairs
- Stratification by comment length (short, medium, long) within each topic
- Fixed random seed (SEED=42) for reproducibility

### Annotations

This dataset contains **manual annotations** produced through a crowdsourcing campaign on Prolific.

The annotation guide used by Prolific annotators (in Spanish) is available [here](https://github.com/SINAI/ALIA-UJA/blob/dev/documentation/data/discriminative/stance_detection/guia_anotacion.md).

**Annotation design:**
- **Platform:** Prolific, using Taskflow for automatic distribution.
- **Annotators:** 180 unique native Spanish speakers resident in Spain.
- **Annotations per instance:** 3 independent annotators per comment.
- **Labels:** `favor` (in favor), `contra` (against), `neutral` (neutral) — translated to English as `favor`, `against`, `neutral` in the published file.
- **Task format:** Annotators received a Google Form with 55 questions per session (50 real instances + 5 attention-check gold standards).
- **Gold standards:** 10 instructed-response attention checks (GS01-GS10) with 100% pass rate across all annotators.

**Pilot study:**
Before launching the full study, a pilot was conducted with 100 samples and 6 annotators to validate the annotation guide, the Google Forms mechanics, the Prolific setup, and time estimates. Key findings:
- No annotator reported that the guide was unclear or unsuitable for the data.
- All annotators scored 100% on the gold standards, confirming that the instructions were understood.
- Average completion time was approximately **40 minutes per 50 instances** (~45–50 seconds per question), which was used to set the reward and time limit for the full study.
- A block size of 55 questions (50 real + 5 gold) was feasible within a single Prolific session.

These parameters were carried over unchanged into the full study of 3,000 samples across 60 blocks.

**Perspectivism and Disagreement:**
We follow the principles of data perspectivism: for subjective tasks like stance detection, there is often no single "correct" label. Rather than discarding disagreement, we publish the full set of individual annotations so that researchers can:
- Study how subjective perspectives vary across annotators.
- Train models that predict distributions over labels instead of a single hard label.
- Investigate the 150 instances with full disagreement (1/1/1) to understand ambiguity boundaries.
- Develop methods for learning-from-disagreement and modeling annotator characteristics.

**Label assignment:**
The `majority_label` is determined by simple majority voting when possible:
- **3/3 agreement (51.3%):** All 3 annotators agreed.
- **2/3 agreement (43.7%):** 2 of 3 annotators agreed.
- **1/1/1 disagreement (5.0%):** All 3 annotators chose different labels — *no `majority_label` is assigned*.

The Fleiss' kappa of 0.406 reflects moderate agreement, consistent with the inherent subjectivity of stance detection in argumentative political texts.

### Personal and Sensitive Information

The corpus derives from publicly available citizen participation data published by the Ayuntamiento de Madrid. Original comments were posted voluntarily by citizens on a public platform. During corpus construction, the following considerations were applied:

- **Names in comments:** Some comments may contain personal names mentioned by citizens in public discourse (e.g., referring to public figures or other participants). These were preserved as they appear in the original public data.
- **User identifiers:** All platform-specific user IDs and metadata were removed; only the textual content (target title, target description, comment text) is retained.
- **Geographic references:** Comments may reference neighborhoods, streets, or locations in Madrid, reflecting the local nature of the debates.

**User Responsibility:** Users are advised to apply additional privacy controls depending on the specific use case, particularly for applications involving personal data processing or re-identification risk analysis.

## Considerations for Using the Data

### Social Impact of Dataset

This corpus contributes to advancing NLP research and applications for Spanish-language civic discourse analysis:

- **Democratizing Civic AI:** Providing labeled resources for understanding citizen opinions in participatory democracy platforms.
- **Supporting Public Consultation Analysis:** Enabling automated tools for summarizing public feedback on policy proposals.
- **Research Advancement:** Offering a standardized, manually validated resource for stance detection research in Spanish.
- **National AI Strategy:** Supporting Spain's strategic objective of developing foundational AI models in Spanish through the ALIA project.
- **Transparency in Governance:** Facilitating research on how citizens express support, opposition, and neutrality toward public policies.
- **Cross-Topic Generalization Research:** Enabling studies on whether stance detection models generalize across diverse, unseen policy topics.

### Discussion of Biases

The corpus reflects inherent biases from its source platform and annotation process:

**Platform-Specific Biases:**
- **Participation bias:** Decide Madrid users are self-selected citizens with higher digital literacy and civic engagement; the corpus does not represent the full demographic spectrum of Madrid residents.
- **Topic bias:** Topics emerge from citizen initiative, not systematic policy coverage. Popular topics (e.g., urban mobility, public transport) are over-represented compared to technical administrative issues.
- **Temporal bias:** The data covers the period when Decide Madrid was active (from 2015 onward); policy priorities and language use may differ from other time periods.
- **Class imbalance:** The corpus exhibits a natural skew toward `favor` labels (~62.5%), consistent with the platform's dynamic where citizens tend to comment on topics they support.

**Language and Register Biases:**
- **Geographic variety:** European Spanish (specifically from Madrid) dominates; Latin American varieties are absent or minimal.
- **Register:** Mix of formal and informal registers, with colloquial expressions, irony, and sarcasm common in political discourse.
- **Length bias:** Comments were filtered to 80-800 characters, excluding very short reactions and very long essays.

**Annotation Biases:**
- **Annotator subjectivity:** Fleiss' kappa of 0.406 indicates legitimate variability in human judgments for this task.
- **Majority-vote limitation:** The 2/3 cases (43.7% of instances with a majority label) represent labels where one annotator disagreed; these instances may be inherently more ambiguous.
- **Neutral as catch-all:** The `neutral` category absorbs ambiguous, tangential, and truly neutral comments, which may represent heterogeneous phenomena.

### Other Known Limitations

**Data Quality:**
- **Encoding artifacts:** Some comments contain encoding issues from the original platform data (e.g., misrendered special characters).
- **HTML remnants:** Despite cleaning, some descriptions may contain residual formatting artifacts.
- **Topic-description mismatch:** In rare cases, the title and description may not perfectly align if the original author edited the post.

**Cross-Topic Generalization:**
- While the corpus is designed to be cross-topic, some topics are highly represented (up to 106 comments) while others have very few (1-3 comments). Models may overfit to frequently occurring topics.

**Temporal Coverage:**
- The original platform data spans several years, but temporal metadata is not included in the published corpus. Trends in language use or policy priorities over time cannot be analyzed.

**Label Granularity:**
- The 3-class schema (favor/against/neutral) does not capture nuanced stances (e.g., conditional support, partial opposition, sarcasm). The `neutral` label is particularly coarse.

**No Train/Test Split Provided:**
- The corpus is distributed as a single collection. Users must create their own splits, ideally ensuring that topics do not overlap between splits when evaluating cross-topic performance.

### Citation

```bibtex
@misc{ALIA-es-discriminative-stance-detection,
  title={ALIA Spanish Discriminative Stance Detection Corpus},
  author={SINAI Research Group},
  year={2026},
  note={3,000 citizen comments annotated for stance detection in Spanish. Original data from Ayuntamiento de Madrid (CC BY 4.0).},
  publisher={HuggingFace},
  howpublished={\url{https://huggingface.co/datasets/SINAI/ALIA-es-discriminative-stance-detection}},
}
```

---

### Funding
This work is funded by the Ministerio para la Transformación Digital y de la Función Pública - Funded by EU – NextGenerationEU within the framework of the project [ALIA](https://alia.gob.es).

---

**Contact:** [ALIA Project](https://www.alia.gob.es/) - [SINAI Research Group](https://sinai.ujaen.es) - [Universidad de Jaén](https://www.ujaen.es/)

**More Information:** [SINAI Research Group](https://sinai.ujaen.es) | [ALIA-UJA Project](https://github.com/sinai-uja/ALIA-UJA)
