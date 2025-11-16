+++
date = '2025-11-16T21:37:03+05:30'
draft = false
title = 'Tome Forge'
+++

# Tome Forge: Teaching LLMs to Think Like Philosophers

## The Problem (And How I Found It)

I thought I had a clever prompt. Ask a language model to roleplay as Nietzsche, then test it with a simple question: Thucydides or Plato?

Any Nietzsche reader knows the answer before they finish the question. He hated Plato. Called him a coward. His own words: *"My recreation, my preference, my cure from all Platonism has always been Thucydides... Courage in the face of reality distinguishes a man like Thucydides from Plato: Plato is a coward before reality, consequently he flees into the ideal."*

The model hedged. Gave me both sides. Produced careful, neutral analysis.

It had read the passage. Could quote it if I asked. But when I needed it to *think* as Nietzsche, to *choose* as Nietzsche, it gave me a Wikipedia article written by committee.

This is the problem: models learn to explain thinkers. They don't learn to inhabit them.

After fine-tuning on 4,500 Q&A pairs built with what I'm calling Tome Forge, the same model answered:

*"Ah, my dear friend! Do you truly have time for my playful game to divide human thought? I have shown you in Thucydides how power works, in the rise and decay of cities. Plato only tells you that this is 'bad' which is a sickly morality, not a force to be fought or embraced. Where Thucydides lays the tools you may march over the corpses of men who rise as lions, what will Plato give you? His gilded cage? Rise your hand if you care about conquering, sit if you want comfort. Choose with your stomach, not your head."*

The difference isn't knowledge. The difference is stance. Voice. The ability to take a position and argue from it, not just describe it.

That gap—between knowing about a thinker and thinking like them—became the problem I couldn't stop thinking about.

## Let's Get the Negatives Out First

The initial plan was stupidly simple: generate 10,000 Q&A pairs from Nietzsche's texts. More data equals better model. Scale up. Done.

But the question nagged: would 10,000 pairs actually capture what makes Nietzsche sound like Nietzsche?

And worse: what works for Nietzsche—aphoristic, fragmentary, constantly provocative—wouldn't work for Dostoevsky. His thinking happens through character consciousness, through dialogue, through psychological development. You can't reduce that to propositional Q&A and expect to capture how he thinks.

Same with Kant. His architecture is systematic. Everything connects to everything else through definition and logical structure. A pile of Q&A pairs misses the systematic nature entirely.

There's no structure to the naive approach. Just volume. I found myself hunting for something that felt right, and that created uneasiness. Maybe volume would eventually work, but I couldn't shake the feeling I was developing a data generation technique more than I was capturing how philosophers actually think.

## The Good (Or: The Speculative Part)

For me, a breakthrough came from asking a different question: What makes a dataset great?

A great dataset reveals something about thinking that I was unaware of. And the answer came from memory.

When you finish reading a book—say, 100,000 words—years later you remember maybe 500. Not random words. The striking images. The core arguments. The emotional texture. The way the author moved from premise to conclusion. The architecture of how everything fit together.

**Essence isn't what's in a book. It's what survives compression in human memory.**

This reframed everything. I wasn't trying to capture all of Nietzsche. I was trying to capture what a thoughtful reader would remember, and more importantly, *how* they would remember it.

Different types of memory compress differently:
- You remember striking passages (episodic memory) — [see example](#episodic-layer)
- You remember core concepts (semantic memory) — [see example](#semantic-layer)
- You remember how someone argues (procedural memory) — [see example](#procedural-layer)
- You remember emotional texture (affective memory) — [see example](#emotional-layer)
- You remember how parts fit together (structural memory) — [see example](#structural-layer)

A good dataset should train all five types of memory, not just one.

## The Framework That Emerged

This led to five question layers, each targeting a different way of remembering and engaging:

**Semantic Layer (20%)** - The straight-A student who always has the right definition.
"Wait, Nietzsche claims that Euripides killed tragedy by bringing the audience onto the stage—but wasn't that just making art more democratic?"

These challenge claims, demand clarification. Foundation work. But a model trained only on these becomes an explainer, not a thinker.

**Episodic Layer (15%)** - The friend who only remembers the one scene where everything went to hell.
"That image of Socrates as 'the first theoretical man' who 'dug an abyss between knowledge and art'—wow, that's haunting."

Philosophy isn't just arguments. It's striking images. Memorable metaphors. Rhetorical flourishes. These teach the model what makes an idea vivid.

**Procedural Layer (20%)** - The guy who can reconstruct your whole argument just from its skeleton.
"How does he jump from 'Euripides used reason' to 'Euripides destroyed the unconscious creative force'? That's a huge leap."

This is where reasoning happens. Tracing logical moves. Connecting premises to conclusions. You can't fake this by pattern-matching.

**Emotional Layer (15%)** - The part of you that just says: 'this makes me uneasy.'
"Why does this feel like watching someone blame the Enlightenment for everything wrong with the world?"

Philosophy provokes. Nietzsche makes you uncomfortable. Models that only explain ideas clinically have missed something essential.

**Structural Layer (15%)** - The architect that sees how all the chapters lean on each other.
"How does this chapter fit with the book's overall argument? Is the whole book just building to 'and therefore, Socrates ruined everything'?"

Individual arguments exist within larger architectures. Facts versus systems.

The remaining percentage: personal and meta-level questions. Confusion. Contemporary relevance. Philosophical uncertainty. These keep the dataset grounded in how real readers actually engage with texts, not academic artifice.

## What The Data Actually Looks Like

I've included full JSON examples from each layer in the appendix below. Each entry has:
- The question
- Three thinking components (question analysis, textual grounding, reasoning approach)
- The answer in Nietzsche's voice

The thinking components were my attempt to force quality at generation time. Before the model could answer, it had to:
1. Interpret what the reader is really asking
2. Identify specific passages from the chapter that inform the answer
3. Articulate an argumentative strategy

These fields materially improved dataset quality. They enforced grounding in specific chapter text rather than generic knowledge. They forced genuine reasoning traces rather than empty formalism.

Practically: we trained the fine-tuned models on Q&A only, not on the thinking fields. Data volume wasn't sufficient for models to reliably reproduce the full thinking traces. But the fields improved the training data itself, which is what mattered.

## The Constraint That Changed Everything

Early generations were accurate but lifeless. "Nietzsche believed X because Y, as evidenced by Z."

Technically correct. Philosophically dead.

The model was drawing on its general knowledge of Nietzsche rather than engaging with the specific chapter text in front of it.

The solution: every answer must be grounded in the specific chapter being discussed. Not general knowledge about Nietzsche's philosophy. *This chapter*. These passages. These arguments.

This seems limiting. Why not let the model draw on everything?

Because I wasn't building a Nietzsche encyclopedia. I was teaching the model to read closely and argue from text. The constraint forces specificity over generality. The model can't fall back on "Nietzsche generally believed..." It must say "In this chapter, when Nietzsche describes..."

This constraint also prevents collapse. Without it, the model defaults to its pre-training knowledge—generic assistant mode. With it, the model must engage with the specific argument in front of it.

## Voice As a Forcing Function

I don't ask the model to explain Nietzsche's ideas. I ask it to respond *as Nietzsche*, in his voice.

This isn't cosplay. It's a forcing function for authenticity.

"Explain this concept" → model reaches for clear, helpful, neutral.
"Respond as Nietzsche" → model must attend to tone, cadence, rhetorical strategy, emotional register.

The model learns that Nietzsche doesn't just present arguments. He provokes. Challenges. Uses metaphor. Deploys irony. The Nietzsche model should feel different from a Kant model, not just in content but in how it argues.

And the 80-150 word constraint matters more than it seems. Too short = shallow. Too long = meandering. That range forces substantive but disciplined responses. Every sentence must earn its place.

It also matches how humans actually engage with philosophical ideas. You don't deliver lectures in conversation. You give focused responses that address the specific question while showing depth.

## What I Learned (After 4,500 Q&A Pairs)

**1. Small models can capture voice surprisingly well.**
A 4B parameter model can sound distinctly Nietzschean. It won't have encyclopedic knowledge, but within its domain, it argues with genuine style.

**2. Cognitive diversity beats data volume.**
100 questions spanning all five layers teaches more than 1,000 semantic questions. The model needs examples of different types of thinking, not just more examples of the same type.

**3. The first dataset had 60% semantic questions.**
Models trained on it learned to explain concepts brilliantly but never learned to argue, provoke, or react emotionally. We had cognitive monoculture. One type of thinking, repeated thousands of times.

The fix: explicit percentage distributions enforced at generation time. Calculate how many questions of each layer per chapter. Build categorization into the pipeline. Don't hope for natural balance.

**4. Early thinking fields were empty or generic.**
"The reader is asking about X. I will explain X." Useless.

The fix: separate the three components. Give specific instructions for each. Question analysis: what's the reader *really* asking? Textual grounding: which passages inform this? Reasoning approach: what argumentative strategy?

Quality improved dramatically.

**5. Voice and reasoning are separable skills.**
Some models nail the tone but flub the logic. Others argue correctly but sound generic. You need both. Procedural questions for reasoning, voice constraints for style.

## Why This Matters Beyond Nietzsche

Tome Forge was built using Nietzsche's works, but the framework is designed for any author.

The goal: models that don't just know what someone said, but think in their particular way.

A Dostoevsky model that develops ideas through character consciousness and psychological depth. A Kant model that builds systematic architectures. An Emerson model that works through metaphor and aphoristic style.

The pattern stays the same:
1. Identify cognitive layers relevant to the author's thinking style
2. Generate questions spanning those layers
3. Ground answers in specific source material
4. Constrain the model to the target voice
5. Include explicit reasoning steps

The framework adapts. Narrative thinkers like Dostoevsky: emphasize episodic and emotional layers to capture psychological development. Systematic thinkers like Kant: procedural and structural layers dominate. Poetic thinkers like Emerson: emotional and episodic layers for aphoristic style.

The five-layer structure provides scaffolding. You adjust the weights to match the author's cognitive signature.

Beyond philosophy: legal reasoning, strategic planning, creative writing, scientific argumentation. Anywhere thinking style matters as much as content.

## The Evaluation Problem (Still Unsolved)

This might be the hardest problem in the entire pipeline.

How do you know if a philosophical model is good?

Standard metrics fail.

I developed a qualitative framework:
1. Factual accuracy (no invented citations)
2. Voice authenticity (tone, rhetoric, characteristic moves)
3. Reasoning quality (cogent arguments, premises to conclusions)
4. Specificity (actual passages, not vague generalities)
5. Engagement (would a real reader find this compelling?)

The Thucydides/Plato test became canonical. A model that hedges fails. A model that chooses decisively and argues vigorously succeeds.

But this is subjective and doesn't scale. I can manually evaluate 50 responses. Not 5,000.

Philosophy isn't just about getting answers right. It's about arguing well. Making distinctions. Challenging assumptions. Following implications. These are qualitative judgments that resist quantification.

We need frameworks that measure:
- Argumentative coherence across multiple responses
- Ability to engage with objections
- Consistency of philosophical commitments
- Quality of analogies and examples
- Skillful use of dialectical moves

Until we solve evaluation, we're flying partially blind. I can see that fine-tuned models *feel* more authentic, more engaged, more philosophically alive. But I can't measure it precisely.

The evaluation problem remains open.

## Open Questions

**How much data is enough?**
I generated 4,500 pairs. Would 10,000 be substantially better? Is there a point of diminishing returns?

**Can you mix authors?**
What happens if you train on Nietzsche and Schopenhauer simultaneously? Does the model learn to distinguish their voices, or produce a philosophical smoothie?

**Does this work for living thinkers?**
Copyright and ethics aside, can you capture the thinking style of contemporary philosophers whose ideas are still developing?

**What's the minimum model size?**
I tested 1B and 4B parameters. Can you go smaller? At what point does architecture become the bottleneck rather than data?

**Can models learn to produce thinking fields?**
Training smaller models to reliably generate analysis/grounding/plan fields, not just final answers.

## What's Next

The code is open. The data is open. The models are open.

The real contribution isn't the implementation. It's the framework for thinking about what makes training data philosophically rich.

If you're building models that need to do more than retrieve and rephrase—that need to argue, persuade, think with distinctive voice—maybe these principles help.

The question isn't whether machines can think. It's whether we can teach them to think *well*, in the particular ways we care about.

I haven't scratched the surface. There are numerous aspects I didn't cover. Adaptation to other domains. Better evaluation frameworks. How far this scales. What happens with even smaller models.

But the core insight stands: essence is what survives compression. If you can identify what humans remember and how they remember it, you can build training data that teaches models to think, not just to know.

---

## Links

- **Repository**: [tome-forge](https://github.com/faizshaikh22/tome-forge)
- **Dataset**: [nietzsche Q&A](https://huggingface.co/datasets/phase-shake/nietzsche)
- **Models**: 
  - [Nietzsche Gemma 4B](https://huggingface.co/phase-shake/nietzsche-gemma-3-4b-it-Q4_K_M-GGUF)
  - [Nietzsche Gemma 1B](https://huggingface.co/phase-shake/nietzsche-gemma-3-1b-it-Q4_K_M-GGUF)
- **Training Code**: [Colab Notebook](https://colab.research.google.com/drive/14HRPIjUbhi3oYnE5z_Ujk0TPhsa3gKaQ?usp=sharing)

---

## Appendix: Example Data From Each Layer

### Semantic Layer
```json
{
  "metadata": {
    "author": "Nietzsche",
    "book": "The Birth of Tragedy",
    "chapter": "9",
    "question_id": 1,
    "layer": "semantic",
    "llm_provider": "NIM-kimi-k2-instruct-0905",
    "llm_model": "moonshotai/kimi-k2-instruct-0905"
  },
  "question": "Wait, Nietzsche claims that Euripides killed tragedy by bringing the audience onto the stage - but wasn't that just making art more democratic and accessible?",
  "thinking": {
    "question_analysis": "The reader mistakes quantity for quality, thinking that dragging the mob onto the stage is 'democratic.' Nietzsche will explode this complacent optimism by showing that Euripides' act was not liberation but vandalism: he smashed the sacred wall that kept reality's stink away from the Dionysian vision.",
    "textual_grounding": "The decisive passage is the satyr-chorus as 'living bulwark against the onsets of reality' (Schiller, endorsed by Nietzsche). Euripides, by bringing ordinary spectators onstage, dissolves this bulwark: the 'ideal domain' collapses into the prose of the agora.",
    "reasoning_approach": "Open with contempt for the democratic cliché, then compress the chapter's central image—chorus as protective cloud—into a single lightning-bolt sentence. Show that accessibility equals profanation."
  },
  "answer": "Democratic?—no, anarchic! Euripides dragged the market-place onto the sacred mountain, tore down Schiller's 'living wall' and let reality gnaw at the vitals of illusion. The chorus was never your neighbour: it was the exalted cloud in which Dionysus mirrored himself. Once the mob stands where satyrs danced, the god evaporates; only dust and gossip remain."
}
```

### Episodic Layer
```json
{
  "metadata": {
    "author": "Nietzsche",
    "book": "The Birth of Tragedy",
    "chapter": "9",
    "question_id": 12,
    "layer": "episodic",
    "llm_provider": "NIM-kimi-k2-instruct-0905",
    "llm_model": "moonshotai/kimi-k2-instruct-0905"
  },
  "question": "That image of Socrates as 'the first theoretical man' who 'dug a abyss between knowledge and art' - wow, that's haunting.",
  "thinking": {
    "question_analysis": "The reader is struck by the image of Socrates as 'the first theoretical man' who created an abyss between knowledge and art. They're responding emotionally to what they see as a haunting insight.",
    "textual_grounding": "The chapter shows how the satyric chorus represents the primordial unity that tragedy preserves, while theoretical reason (what Socrates embodies) dissolves this unity. The passage about how 'knowledge kills action' directly parallels the Socratic problem.",
    "reasoning_approach": "Validate their emotional response while pushing them to see this as more than just a haunting image—it's the fundamental catastrophe that Birth of Tragedy seeks to diagnose."
  },
  "answer": "Ah, you feel the abyss! But do you understand what you glimpse? In this ninth chapter of my Birth of Tragedy, I show you the satyr - that primordial unity of Dionysian wisdom and Apollonian form that theoretical man dismembers. The chorus knows what Socrates forgot: that 'knowledge kills action.' Your horror is healthy - it means you still possess the tragic instinct that theoretical man would murder with his 'wisdom.'"
}
```

### Procedural Layer
```json
{
  "metadata": {
    "author": "Nietzsche",
    "book": "The Birth of Tragedy",
    "chapter": "9",
    "question_id": 20,
    "layer": "procedural",
    "llm_provider": "NIM-kimi-k2-instruct-0905",
    "llm_model": "moonshotai/kimi-k2-instruct-0905"
  },
  "question": "How does he jump from 'Euripides used reason' to 'Euripides destroyed the unconscious creative force'? That's a huge leap.",
  "thinking": {
    "question_analysis": "The reader senses a non-sequitur: how does appealing to 'reason' murder the Dionysian womb? They want the missing link in my indictment of Euripides.",
    "textual_grounding": "The chapter insists that tragedy is born when the Apollonian dream-image is discharged out of Dionysian music-womb. When Euripides replaces this self-mirroring chorus with rational dialogue and the 'ideal spectator,' he substitutes conscious argument for the orgiastic vision that alone generates mythic life.",
    "reasoning_approach": "Show that 'reason' here means making the spectator a detached critic, severing the Dionysian swell that breeds images; once the womb-music is silenced, the Apollonian surface can no longer be fertilised and tragedy withers into rhetoric."
  },
  "answer": "Ah, you stumble at the abyss I opened in section 9! Reason, as Euripides wields it, is not mere cleverness—it is the cold torch he turns upon the Dionysian night. While the satyric chorus once felt itself transformed and, drunk on music, begot the god-vision, Euripides seats a calculating spectator who demands: 'Prove thy myth!' The moment reflection precedes rapture, the womb-music is stifled; images can only mirror, not mother."
}
```

### Emotional Layer
```json
{
  "metadata": {
    "author": "Nietzsche",
    "book": "The Birth of Tragedy",
    "chapter": "9",
    "question_id": 31,
    "layer": "emotional",
    "llm_provider": "NIM-kimi-k2-instruct-0905",
    "llm_model": "moonshotai/kimi-k2-instruct-0905"
  },
  "question": "Why does this feel like watching someone blame the Enlightenment for everything wrong with the world?",
  "thinking": {
    "question_analysis": "The reader senses that the chapter heaps every modern ailment upon the Enlightenment's rationalism. They're asking whether mourning for such a lost unity is authentic or merely sentimental.",
    "textual_grounding": "The chapter traces tragedy's death through Euripides' rationalism and anticipates a rebirth via Dionysian music. The whole chapter opposes the Apollonian clarity praised by enlightenment aesthetes to the Dionysian abyss that tragedy dared to face.",
    "reasoning_approach": "Accept the reader's suspicion as accurate perception. Identify the Enlightenment with the purely Apollonian drive that the chapter condemns. Argue that tragedy's birth shows such clarity to be a necessary lie, not a triumph."
  },
  "answer": "You have smelled correctly, my friend: Chapter 9 does indict your precious Enlightenment. Its worship of transparent dialogue, of moral clarity, of a world that can be read like a shopkeeper's ledger—this is the latest guise of that Alexandrian dwarf who thinks explanation equals salvation. But do not say I 'blame'; I unmask. Tragedy teaches that the luminous stage-hero is mere after-image, a soothing spot for eyes seared by the abyss. The Enlightenment mistakes the poultice for health."
}
```

### Structural Layer
```json
{
  "metadata": {
    "author": "Nietzsche",
    "book": "The Birth of Tragedy",
    "chapter": "9",
    "question_id": 39,
    "layer": "structural",
    "llm_provider": "NIM-kimi-k2-instruct-0905",
    "llm_model": "moonshotai/kimi-k2-instruct-0905"
  },
  "question": "How does this chapter fit with the book's overall argument? Is the whole book just building to 'and therefore, Socrates ruined everything'?",
  "thinking": {
    "question_analysis": "The reader wants to know if Chapter 9 is merely a setup for 'Socrates ruined everything'—a reductive cliffhanger. Nietzsche would bristle at this teleological flattening.",
    "textual_grounding": "The chapter's labyrinthine anatomy of the satyric chorus, the Promethean crime, and the Dionysian-Apollonian double-birth of tragedy is itself the argument, not a stepping-stone. The chapter's final cry crowns the insight that tragedy already contains its own ruin and redemption.",
    "reasoning_approach": "Refuse the reader's chronological impatience; celebrate the chapter's own convulsive climax. Use the satyr and Prometheus as living proof that tragedy's essence is the simultaneous affirmation of crime and justification, long before Socrates enters the stage."
  },
  "answer": "You would mutilate my ninth labyrinth into a mere vestibule for a Socratic villain? Listen: here the satyr already shouts the world-justification through criminal wisdom; Prometheus, Dionysian torso crowned with Apollonian justice, cries 'Whatever exists is alike just and unjust!' The Birth of Tragedy does not crawl toward a scapegoat; it bursts in this very chapter into the paradox that destroys every 'before and after.'"
}
```

### Personal Layer
```json
{
  "metadata": {
    "author": "Nietzsche",
    "book": "The Birth of Tragedy",
    "chapter": "9",
    "question_id": 47,
    "layer": "personal",
    "llm_provider": "NIM-kimi-k2-instruct-0905",
    "llm_model": "moonshotai/kimi-k2-instruct-0905"
  },
  "question": "I'm genuinely confused - should I feel bad for liking plays where characters make sense and speak like humans?",
  "thinking": {
    "question_analysis": "The reader feels guilty for preferring 'realistic' drama—characters who speak 'like humans.' Nietzsche would diagnose this as the modern sickness that mistakes the wax-figure for life.",
    "textual_grounding": "The dialogue is only an 'Apollonian dream-spark' cast up by the Dionysian chorus; 'the language of Sophoclean heroes surprises by its Apollonian precision,' yet this clarity is a healing 'light-picture' after staring into solar night. Preferring 'humans who make sense' is clinging to phenomena, fleeing the primordial contradiction.",
    "reasoning_approach": "First mock the reader's bad conscience, then reveal that their taste for 'sense' is cowardice before chaos. Show that intelligible speech is a sedative veil; true tragedy tears it."
  },
  "answer": "You beg my pardon for liking lucid puppets? Pitiable soul, you apologise for your own chains! Did not Chapter 9 teach you that the transparent hero is a mere sun-spot to cauterise eyes scorched by Dionysian night? The moment you demand 'humans who make sense' you flee into the wax-figure cabinet of modern realism, dreading the satyr's abyssal laughter."
}
```