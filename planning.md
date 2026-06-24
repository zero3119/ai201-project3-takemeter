# TakeMeter

## Recommended Label Set

---

### Analysis

A post/comment that makes a reasoned point about a show, episode, character, actor, writing, pacing, themes, ratings, streaming strategy, or TV industry topic.

**Example**

It has always been bad, but to give you an honest answer.

Around 2018, a lot of people realized they could monetize their anger, and this is where the culture wars bullshit began on YouTube.

It has only gotten worse.

**Example**

Season 3 so far has been just as good as the first two seasons, albeit different in tone and storytelling.

**Post that might apply to multiple labels**

First two episodes were good, different from the first two seasons, but still familiar. This episode was fantastic though. Maybe the best of the whole series.

---

### Reaction

Mainly expresses an emotional response, opinion, hype, frustration, surprise, sadness, etc., without much explanation.

**Example**

Pretty cool they also got Shaheer Zamata. Would love if they got some current SNL cast members like Andrew Dismukes or Sarah Sherman.

**Example**

Fucking my queen Taylor Tomlinson!!!! Ron Funches!!!!! Gianmarco is coming back!!!! Reggie Watts!!! Maria Bamford!!!!!!!!!!!!!!!!! I could do this about everyone on this list it's insane.

I feel like word has spread in the standup community that it's a fun gig and they pay well. Gianmarco said the pay was extremely good compared to doing a gig of standup, so people are open to it. Plus, they can all watch the first season to see what it's about. This is amazing news.

I would love for them to do some Dropout standup specials. I wonder if they are considering it.

**Post that might apply to multiple labels**

At this point I’m committed. There’s only one season left, so we will find out something I hope. If not, I’m just here for the ride. Feels like *Lost* all over again.

---

### Recommendation

A post/comment mainly suggesting, praising, or encouraging people to watch or avoid a show.

**Example**

I think you’d enjoy the *Interview with the Vampire* series. The first two seasons are on Netflix and season 3, rebranded as *Vampire Lestat*, is airing now on AMC.

**Example**

Highly recommend you watch *Evil*. It sounds exactly like what you want to see and even has the classic monster-of-the-week episodes together with a really interesting overarching plot.

**Post that might apply to multiple labels**

It’s my favorite show that I’ve stopped recommending. Four seasons in and I still have almost no idea what the fuck is going on.

---

### Question

A post or comment mainly asking for information, opinions, clarification, or viewing advice. This includes indirect questions where the user expresses uncertainty and is likely looking for others to respond.

**Example**

So anyone miss supernatural shows, specifically stuff like *Buffy*, *Charmed*, *Angel*, *Originals*, *Vampire Diaries*, *Supernatural*, or *X-Files*? It genuinely feels like this genre is dead right now. Why do you think that is, and would you love to see a comeback?

**Example**

What is your favorite pre-2000 TV episode?

**Post that might apply to multiple labels**

How has the season been so far? Absolutely loved the first two, but I subscribe to way too many streaming services to shell out for AMC+ unless this is amazing.

---

## Description

My community is a television subreddit. It is a place where people discuss TV shows, recommendations, news, and anything related to that area of entertainment. The labels that I chose were analysis, question, recommendation, and reaction. These labels encapsulate this subreddit pretty accurately since everything is about how people react to shows, ask questions about shows and what is happening, recommend TV shows that they like, and try to figure out theories about shows.

## Edge Cases

### Case One

One hard edge case for this project will be very short comments, because they often do not provide enough context to clearly show whether the user is reacting, recommending, asking a question, or analyzing something.

**Example**

“This show is amazing.”

**Solution**

In my taxonomy, I would label that as reaction because its main purpose is expressing emotion or opinion.

### Case Two

Another difficult case will be comments that could fit two labels at once.

**Example**

“You should watch this because the writing is great” has both a recommendation and a small amount of reasoning.

**Solution**

I will handle these cases by labeling the comment based on its main purpose. If the comment mainly tells someone to watch or avoid a show, I will label it recommendation. If it mainly explains why something works or does not work, I will label it analysis. If it mainly asks for advice or information, I will label it question. If it mainly expresses a quick feeling or opinion without much explanation, I will label it reaction.

### Documented Examples

While labeling the dataset, some posts were difficult because they could fit more than one category. I labeled each one based on the main purpose of the post instead of just looking for keywords.



1. **Post:** “Question for people who have read the books and watched the show: I DNF’d the books because I couldn't stand how Holden was written. Some of the other characters were pretty bad as well. If I couldn't stand how they were written in the books, am I likely to feel the same about them in the show?”

   **Possible labels:** Question or Analysis

   **Decision:** Question

   **Reason:** The post includes analysis of why the user disliked the books, but the main purpose is asking other people for advice about whether the show has the same issue. Since it is asking for an opinion and guidance, I labeled it as question.

2. **Post:** “Enjoy the ride, beratna. The adaptation is peak sci-fi and is the benchmark for all sci-fi/space opera series, imo.”

   **Possible labels:** Reaction, Analysis, or Recommendation

   **Decision:** Recommendation

   **Reason:** The post praises the show and gives a small opinion about its quality, but “enjoy the ride” makes it function like a recommendation to keep watching. Since the main purpose is encouraging someone to watch, I labeled it as recommendation.

3. **Post:** “What’s the fucking point of caring about shows anymore”

   **Possible labels:** Question or Reaction

   **Decision:** Reaction

   **Reason:** This is written like a question, but the user is not really asking for an answer. They are expressing frustration about shows being canceled. Because the main purpose is emotional reaction, I labeled it as reaction.



## Data Collection

**Source**

The data will be collected from a subreddit called r/television.

**Examples per label**

50

**In case of underrepresentation**

In case of underrepresentation of a label, the appropriate solution might be to find a new method of labeling the information that will better encapsulate all the responses into more even examples.

## Evaluation Metrics

I will evaluate the model using accuracy, per-label scores, and a confusion matrix. Accuracy will show how often the model gets the correct label overall, but I do not want to rely only on accuracy because one label might appear more often than the others.

The per-label scores will help me see which categories the model understands well and which ones it struggles with. The confusion matrix will help me find patterns in the mistakes, such as whether the model mixes up short reactions with recommendations or questions.

I will also compare my fine-tuned model with a zero-shot Groq baseline on the same test set. This will show whether training the model on my Reddit television data actually helped.

## Definition of Success

For this classifier to be useful, it should perform better than the zero-shot baseline and work reasonably well across all four labels: analysis, reaction, recommendation, and question. My goal is around 70% accuracy, with no category performing extremely badly.

I do not expect the model to be perfect because Reddit comments can be short, sarcastic, informal, or unclear. I would consider the model good enough if it can correctly separate most clear examples and if its mistakes make sense when I review them. I would use this as a tool to help organize or study online discussion, not as an automatic moderation system.

## AI Tool Plan

### Label Stress-Testing

**Boundary: reaction / analysis**

“I honestly hated the finale at first, but the more I think about it, the rushed pacing kind of matches how the main character was spiraling the whole season.”

**Label:** analysis

**Boundary: recommendation / analysis**

“You should definitely watch *The Leftovers* if you like shows that do not explain everything directly. The best part is how it uses grief and confusion as part of the storytelling instead of giving easy answers.”

**Label:** recommendation

**Boundary: question / recommendation**

“Is *Severance* worth watching if I usually like slow mystery shows, or is it one of those shows people only recommend because it looks stylish?”

**Label:** question

**Boundary: reaction / recommendation**

“This show is absolutely insane in the best way. I don’t even know how to describe it, but everyone needs to give at least the first episode a chance.”

**Label:** recommendation

**Boundary: question / analysis**

“Do you think the last season failed because the writers ran out of source material, or was the show already having pacing problems before that?”

**Label:** question

### Annotation Assistance

I will ask an LLM to go through a CSV file containing the posts. I will make it clear that it should only label the posts that are easily classified without human interaction. Otherwise, I will ask it to mark the post in the CSV file as “NEEDS REVISION” so that there is another proof check made by me.

### Failure Analysis

After I run the model and collect the wrong predictions, I will use an AI tool to help look for patterns in the mistakes. I will give it examples where the model predicted the wrong label and ask it to identify common issues, such as short comments, sarcastic wording, unclear intent, or labels that are often confused with each other.

I will not automatically trust the AI’s analysis. I will re-read the wrong predictions myself and check whether the patterns it found are actually present. If the AI suggests a pattern that does not match the examples, I will ignore or revise it. This will help me write a stronger evaluation section and explain why the model struggled with certain Reddit television comments.
