# TakeMeter

TakeMeter is a text classification project that labels Reddit television posts and comments by their main purpose. The project uses four labels: `analysis`, `reaction`, `recommendation`, and `question`.

## Community Choice and Reasoning

The community I chose is `r/television`. This community is a good fit for a text classification task because posts and comments naturally fall into different kinds of discussion. Some users analyze shows and TV industry decisions, some react emotionally to TV news or episodes, some recommend shows to other users, and some ask questions or request viewing advice. Because these comment types appear often in the community, the labels are useful for organizing television discussion.

## Project Goal

The goal of this project is to fine-tune `distilbert-base-uncased` to classify Reddit television posts into one of four categories. I also compare the fine-tuned model to a zero-shot Groq baseline so I can measure whether training on my labeled examples improves performance.

## Label Taxonomy

| Label | Definition |
|---|---|
| `analysis` | A post or comment that makes a reasoned point about a show, episode, character, actor, writing, pacing, themes, ratings, streaming strategy, or TV industry topic. |
| `reaction` | A post or comment that mainly expresses an emotional response, opinion, hype, frustration, surprise, sadness, disappointment, or agreement without much explanation. |
| `recommendation` | A post or comment mainly suggesting, praising, or encouraging people to watch or avoid a show. |
| `question` | A post or comment mainly asking for information, opinions, clarification, or viewing advice. This includes indirect questions where the user expresses uncertainty and is likely looking for others to respond. |

### Examples per Label

#### `analysis`

- “Around 2018, a lot of people realized they could monetize their anger, and this is where the culture wars bullshit began on YouTube. It has only gotten worse.”
- “Season 3 so far has been just as good as the first two seasons, albeit different in tone and storytelling.”

#### `reaction`

- “Pretty cool they also got Shaheer Zamata. Would love if they got some current SNL cast members like Andrew Dismukes or Sarah Sherman.”
- “At this point I’m committed. There’s only one season left, so we will find out something I hope. If not, I’m just here for the ride.”

#### `recommendation`

- “I think you’d enjoy the Interview with the Vampire series. The first two seasons are on Netflix and season 3, rebranded as Vampire Lestat, is airing now on AMC.”
- “Highly recommend you watch Evil. It sounds exactly like what you want to see and even has the classic monster-of-the-week episodes together with a really interesting overarching plot.”

#### `question`

- “What is your favorite pre-2000 TV episode?”
- “How has the season been so far? Absolutely loved the first two, but I subscribe to way too many streaming services to shell out for AMC+ unless this is amazing.”

## Dataset

The dataset contains Reddit television posts and comments labeled using the four categories above. The final labeled CSV contains three columns:

```csv
text,label,notes
```

The `notes` column is used for difficult or borderline examples. The notebook automatically split the dataset into train, validation, and test sets using a 70% / 15% / 15% split.

### Data Source

The examples came from Reddit television discussions, mainly from `r/television`. I collected posts and comments that represented the four main discussion purposes: analysis, reaction, recommendation, and question.

### Labeling Process

I labeled each example based on the main purpose of the post rather than only looking for keywords. If a post mainly explained something, I labeled it `analysis`. If it mainly expressed emotion or a quick opinion, I labeled it `reaction`. If it encouraged someone to watch, avoid, try, or continue a show, I labeled it `recommendation`. If it asked for information, opinions, clarification, or viewing advice, I labeled it `question`.

I used AI assistance to help organize and pre-label some examples, but I reviewed the labels myself and corrected difficult cases. Borderline cases were documented in the `notes` column and in `planning.md`.

### Label Distribution

| Label | Count | Percent |
|---|---:|---:|
| analysis | 50 | 24.8% |
| reaction | 51 | 25.2% |
| recommendation | 51 | 25.2% |
| question | 50 | 24.8% |
| **Total** | **202** | **100%** |

No label accounts for more than 70% of the dataset, so there is no major class imbalance problem.

## Difficult-to-Label Examples

### Difficult Example 1

**Post:** “Question for people who have read the books and watched the show: I DNF’d the books because I couldn't stand how Holden was written. Some of the other characters were pretty bad as well. If I couldn't stand how they were written in the books, am I likely to feel the same about them in the show?”

**Possible labels:** `question` or `analysis`  
**Decision:** `question`

**Reason:** The post includes analysis of why the user disliked the books, but the main purpose is asking other people for advice about whether the show has the same issue.

### Difficult Example 2

**Post:** “Enjoy the ride, beratna. The adaptation is peak sci-fi and is the benchmark for all sci-fi/space opera series, imo.”

**Possible labels:** `reaction`, `analysis`, or `recommendation`  
**Decision:** `recommendation`

**Reason:** The post praises the show and gives a small opinion about its quality, but “enjoy the ride” makes it function like a recommendation to keep watching.

### Difficult Example 3

**Post:** “What’s the fucking point of caring about shows anymore”

**Possible labels:** `question` or `reaction`  
**Decision:** `reaction`

**Reason:** This is written like a question, but the user is not really asking for an answer. They are expressing frustration about shows being canceled.

## Fine-Tuning Approach

The fine-tuned model is based on:

```text
distilbert-base-uncased
```

The model was trained in Google Colab using a free T4 GPU.

### Final Training Settings

| Setting | Value |
|---|---:|
| Epochs | 5 |
| Learning rate | 2e-5 |
| Batch size | 16 |
| Weight decay | 0.01 |
| Warmup steps | 0 |

### Hyperparameter Decision

The starter notebook originally used `warmup_steps=50`, but my dataset was small and produced a small number of training steps. In the first fine-tuning run, the model performed close to random guessing because the warmup period was too long for the amount of training. I changed `warmup_steps` from 50 to 0 so the model could use the intended learning rate immediately.

I also increased training from 3 epochs to 5 epochs. Since the dataset had only 202 total examples, the model needed a few more passes through the training examples. I kept the learning rate, batch size, weight decay, and base model the same.

## Baseline Model

Before fine-tuning, I ran a zero-shot Groq baseline on the locked test set. The baseline used the same label definitions from `planning.md`. The prompt asked the model to output only one valid label name.

### Baseline Prompt

```text
You are classifying Reddit posts and comments from r/television.
The task is to assign each post to exactly one category based on its main purpose.

analysis: A post or comment that makes a reasoned point about a show, episode, character, actor, writing, pacing, themes, ratings, streaming strategy, or TV industry topic.
Example: "Around 2018, a lot of people realized they could monetize their anger, and this is where the culture wars bullshit began on YouTube. It has only gotten worse."

reaction: A post or comment that mainly expresses an emotional response, opinion, hype, frustration, surprise, sadness, disappointment, or agreement without much explanation.
Example: "Fucking my queen Taylor Tomlinson!!!! Ron Funches!!!!! Gianmarco is coming back!!!! Reggie Watts!!! Maria Bamford!!!!!!!!!!!!!!!!!"

recommendation: A post or comment mainly suggesting, praising, or encouraging people to watch or avoid a show.
Example: "Highly recommend you watch Evil. It sounds exactly like what you want to see and even has the classic monster-of-the-week episodes together with a really interesting overarching plot."

question: A post or comment mainly asking for information, opinions, clarification, or viewing advice. This includes indirect questions where the user expresses uncertainty and is likely looking for others to respond.
Example: "How has the season been so far? Absolutely loved the first two, but I subscribe to way too many streaming services to shell out for AMC+ unless this is amazing."

Important rules:
- Choose exactly one label.
- Label based on the main purpose of the post.
- If a post asks for information, opinions, clarification, or viewing advice, choose question.
- If a post mainly tells someone to watch, avoid, try, or keep watching something, choose recommendation.
- If a post mainly explains why something works or does not work, choose analysis.
- If a post mainly expresses a feeling or quick opinion without much explanation, choose reaction.
- Rhetorical questions that mainly express frustration should usually be reaction, not question.

Respond with ONLY the label name.
Do not explain your reasoning.
Do not include punctuation.
Do not include extra words.

Valid labels:
analysis
reaction
recommendation
question
```

### Baseline Results

| Metric | Value |
|---|---:|
| Accuracy | 0.677 |
| Parseable responses | 31 / 31 |
| Unparseable responses | 0 |

### Baseline Per-Class Metrics

| Label | Precision | Recall | F1-score | Support |
|---|---:|---:|---:|---:|
| analysis | 1.00 | 0.43 | 0.60 | 7 |
| recommendation | 1.00 | 0.50 | 0.67 | 8 |
| reaction | 0.44 | 1.00 | 0.62 | 8 |
| question | 1.00 | 0.75 | 0.86 | 8 |
| accuracy |  |  | 0.68 | 31 |
| macro avg | 0.86 | 0.67 | 0.68 | 31 |
| weighted avg | 0.86 | 0.68 | 0.69 | 31 |

## Fine-Tuned Model Evaluation

After fine-tuning `distilbert-base-uncased`, the model was evaluated on the same locked test set.

### Overall Comparison

| Model | Accuracy |
|---|---:|
| Zero-shot Groq baseline | 0.677 |
| Fine-tuned DistilBERT | 0.742 |

The fine-tuned model improved over the baseline by about 6.45 percentage points.

### Fine-Tuned Per-Class Metrics

| Label | Precision | Recall | F1-score | Support |
|---|---:|---:|---:|---:|
| analysis | 0.60 | 0.86 | 0.71 | 7 |
| recommendation | 1.00 | 0.88 | 0.93 | 8 |
| reaction | 0.60 | 0.38 | 0.46 | 8 |
| question | 0.78 | 0.88 | 0.82 | 8 |
| accuracy |  |  | 0.74 | 31 |
| macro avg | 0.74 | 0.75 | 0.73 | 31 |
| weighted avg | 0.75 | 0.74 | 0.73 | 31 |

### Fine-Tuned Confusion Matrix

Rows are the true labels. Columns are the predicted labels.

| True Label | Predicted analysis | Predicted recommendation | Predicted reaction | Predicted question |
|---|---:|---:|---:|---:|
| analysis | 6 | 0 | 1 | 0 |
| recommendation | 0 | 7 | 0 | 1 |
| reaction | 4 | 0 | 3 | 1 |
| question | 0 | 0 | 1 | 7 |

## Error Analysis

The fine-tuned model performed best on `recommendation`, `question`, and `analysis`. The main weakness was still `reaction`. The most important failure pattern was:

```text
reaction → analysis
```

Out of 8 true reaction examples, 4 were predicted as `analysis`. This means that the model often treated detailed emotional reactions as reasoned explanations.

### Wrong Prediction 1

**Text:** It’s incredibly silly and the last few seasons are unwatchable, but the first few were quite enjoyable taking the silliness into account. The spinoffs have all been pretty entertaining, if you accept ...

### Wrong Prediction 2

**Text:** Breaking bad for sure

**True label:** recommendation
**Predicted label:** question
**Confidence:** 0.52

**Analysis:** This likely happened because very short recommendation answers can look incomplete without thread context. If the post only names a show or gives a short answer, the model may not understand that it is responding to a recommendation request.

### Wrong Prediction 3

**Text:** I didnâ€™t care for this one at all. Please find me a movie or show with a female spy who is a good spy and not crazy and not bumping against the men in the field. I havenâ€™t seen much of the show â€œAliasâ€ so maybe that is it.

**True label:** question  
**Predicted label:** reaction

**Confidence:** . 46

**Analysis:** This kind of error can happen when a question is surrounded qith context about the situation and might be seen as more of a reaction.

## Patterns Found With AI Assistance

I used an AI tool to review the wrong predictions and look for common themes. The tool suggested that several errors involved rhetorical questions, short low-context posts, and comments that contained both opinion and reasoning. I re-read the wrong predictions myself and found that the strongest pattern was reaction posts being predicted as analysis.

I also rejected one overly broad pattern: not all mistakes were caused by short text. Some of the most important mistakes were long comments, especially reaction comments that included examples, names, or explanations. The real issue was not just length, but the model confusing detailed emotional reactions with analysis.

## Sample Classifications

| Text | Predicted Label | Confidence | Correct? | Notes |
|---|---|---:|---|---|
| I mean, yeah. Taylor Sheridan isnt that good of a writer. He wrote a couple of good movies, got high on his own supply, and Paramount threw an ungodly amount of money at him to make boomer bait shows that can be clipfarmed by the dumbest members of your family to repost on Facebook. | analysis | 0.289 | Yes | This prediction is reasonable because the post explains a point about the show rather than just reacting emotionally. |
| Excellent show, still worth watching. | recommendation | 0.295 | Yes | This prediction is reasonable if the post recommends, praises, or encourages watching something. |
|  Yeah, I’m glad the show help me discover Ryan Bingham. The show also helped me discover Whiskey Myers and Shane Smith and the Saints. | `analysis` | 0.294 | No | It is more of a reaction since it is more of a personal comment |

## Reflection: Intended Labels vs. Learned Behavior

My intended label definitions were based on the main purpose of each Reddit post. I wanted the model to distinguish between posts that explain something, react emotionally, recommend something, or ask a question.

The fine-tuned model learned some of these boundaries well. It performed especially well on `recommendation` and `question`, and it improved over the zero-shot baseline overall. This suggests that the model learned some useful patterns from the labeled dataset.

However, the model still struggled with `reaction`. It often predicted `analysis` when a reaction post included specific details, examples, or references to shows and actors. This means the model partially learned to associate detail and explanation with `analysis`, even when the main purpose was emotional reaction. A longer emotional reaction can look like analysis, so the model did not fully capture the intended “main purpose” rule.

To improve the model, I would add more reaction examples that are longer and more detailed. I would also add more hard examples where reaction and analysis are close, so the model can better learn the difference between emotional opinion and reasoned explanation.

## Spec Reflection

One way the spec helped guide my implementation was by requiring a baseline before fine-tuning. This made the fine-tuned model’s accuracy meaningful because I had something to compare it against. Without the baseline, I would not know whether 0.742 accuracy was good or bad.

One way my implementation diverged from the initial notebook setup was the fine-tuning hyperparameters. The original fine-tuning settings used 50 warmup steps, but my small dataset created too few total training steps for that setting to work well. This caused the first fine-tuned model to perform poorly. I changed `warmup_steps` from 50 to 0 and increased training from 3 epochs to 5 epochs. I made this change because the original settings were not appropriate for the size of my dataset.

## AI Usage

I used AI assistance in several parts of this project.

First, I used an AI tool to help pre-label and organize Reddit posts into the four categories. The AI tool helped identify likely examples of `analysis`, `reaction`, `recommendation`, and `question`. I reviewed the labels myself and corrected difficult cases instead of accepting the AI output automatically.

Second, I used AI assistance to write and refine the label definitions and edge case rules in `planning.md`. The AI helped me phrase the label boundaries more clearly, especially for cases where posts could be both recommendation and analysis, or question and reaction.

Third, I used AI assistance to review the fine-tuned model’s wrong predictions. The AI suggested possible failure patterns, such as rhetorical questions, low-context short posts, and detailed reactions being confused with analysis. I checked those suggestions myself and kept the patterns that matched the actual examples.

## Demo Video Plan

In the demo video, I will show the fine-tuned model classifying 3–5 posts. I will include the predicted label and confidence score for each example. I will explain one correct prediction and one incorrect prediction. Then I will briefly walk through the evaluation report, including the baseline accuracy, fine-tuned accuracy, confusion matrix, and the main failure pattern involving `reaction` being confused with `analysis`.

## LINK TO VIDEO
