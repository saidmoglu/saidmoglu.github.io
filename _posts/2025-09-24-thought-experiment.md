---
layout: post
title: LLMs Are Just Next Token Predictors
subtitle: But Consider This Thought Experiment
thumbnail-img: /assets/img/thought-exp/me-lecun.png
share-img: /assets/img/thought-exp/me-lecun.png
tags: [LLM, GenAI, Predictor]
---


I often hear the claim that large language models (LLMs) are "just next token predictors." The next‑token predictor criticism argues that LLMs operate by generating one token at a time based purely on statistical patterns in their training data, without genuine comprehension, reasoning, planning, or world‑modeling. This token‑by‑token mechanism, according to critics, lacks the structural and contextual grounding that makes human thinking what it is.

- Emily Bender (Linguistics Professor) coined the term “stochastic parrots” to describe LLMs as pattern‑matching machines that mimic language rather than understand it.
- Yann LeCun (Chief AI Scientist at Meta) strongly critiques LLMs as fundamentally limited: He argues they lack understanding of the physical world, persistent memory, genuine reasoning, and the ability to perform complex planning. He’s said LLMs are “just token generators” and predicted their paradigm will be outdated in 3–5 years. In several interviews, he’s stated LLMs are not a path toward AGI or human‑level intelligence.

While it’s true that LLMs are “just” predicting the next token, I think this talking point is misleading if it’s used to dismiss what they can do, or might be able to do in the future. Here’s a thought experiment to explain my view:

### A communication protocol
![Me and Yann LeCun](../assets/img/thought-exp/me-lecun.png){: width="400px" .mx-auto.d-block :}
Imagine Yann LeCun is in a remote location and I communicate with him using a really basic protocol: First, I send my question. Instead of replying with his whole answer, he sends me only the first token. I then send back the question plus the first token he sent, and he gives me the next token. This goes on, one token at a time, until the answer is complete.

The end result is that I get a full answer, just like with an LLM (which also generates responses one token at a time). Clearly, this does not mean LeCun is “just a next token predictor.” He’s still a human, thinking deeply, drawing on knowledge, reasoning, and lived experience. The method of delivering his answer (one token at a time) tells us nothing about how the answer was constructed or how much “understanding” he brought to it.

You might point out: “But LeCun isn’t picking each token from statistical patterns alone. He knows the entire reply from the start, and the token-at-a-time protocol is just a cumbersome constraint. Also, humans have memory and context well beyond what comes in each message.” Fair enough. So here’s a twist:

### The extended thought experiment
![Millions and Lecun](../assets/img/thought-exp/millions-lecun.png){: width="400px" .mx-auto.d-block :}
LeCun is now messaging millions of people, asynchronously, and can’t keep notes or see message history or sender identity. Messages from one correspondent might be separated by months or years. Each time someone messages, he only sees the current message (i.e., the original query and the tokens he sent back so far), and he has to generate the next token for that conversation with what’s in front of him. He can't remember what he thought his response would be previous times, in fact he gives up on trying to keep track of any of it. 

I claim that LeCun is effectively functioning as an LLM. He’s stateless - no memory of earlier interactions. No persistent context except what’s in the current message. Still, it’s Yann LeCun replying—so if the provided context is enough to understand the question, his replies will still be coherent, informed, and insightful, built on his real human intelligence.

The key idea: producing answers one token at a time, based only on current context, does not in itself mean the process is shallow or “mechanical.” The depth depends on how the tokens are chosen, not just that they come one by one.

### The limits of the analogy
Of course, there’s a big difference: LLMs don’t have real-world experience, grounding in physical reality, or persistent memory beyond their training data. All the rich cognition in the LeCun experiment comes from his background as a person living in and understanding the world. LLMs so far simulate this from a sea of data, not direct experience. There’s also the question of whether LLMs really “model” the world or just simulate pattern-matching.

But my point is—being a “next token predictor” is a description of an output process, not a diagnosis of what’s happening inside. It doesn’t actually tell us if an agent is understanding, reasoning, or planning. It just describes an interface. You can have a trivial model that does next token prediction, or you could (in theory) have a really sophisticated and grounded intelligence that outputs the same way.

### Conclusion
The whole “just a next token predictor” line is technically true. But it doesn’t prove anything by itself about understanding, reasoning, or intelligence—human or artificial. What matters is the quality of the internal process and the sources the system draws on to generate the next token, not the act of predicting tokens itself.

I’m not saying today’s LLMs are equivalent to humans, that they are potentially capable of matching or exceeding humans in terms of cognitive capabilities, or that they have real world models or deep reasoning equal to ours. But we should be careful about dismissing them just because of how they output text. The proof—or limitation—of intelligence is in what’s inside the system, not the mechanics of token generation.