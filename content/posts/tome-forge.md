+++
date = '2025-11-16T21:37:03+05:30'
draft = false
title = 'Tome Forge'
tags = ["llm", "ai", "philosophy", "nietzsche", "fine-tuning", "data-engineering"]
+++

# Tome Forge: Teaching LLMs to Think Like Philosophers

## The problem (and how I tripped over it)

I thought I had a clever prompt. Tell a model to roleplay as Nietzsche, then poke it with a softball: Thucydides or Plato?

If you've read even a little Nietzsche, you can answer before you finish the question. He loathed Plato. He called him a coward. In his own words: *"My recreation, my preference, my cure from all Platonism has always been Thucydides... Courage in the face of reality distinguishes a man like Thucydides from Plato: Plato is a coward before reality, consequently he flees into the ideal."*

The model hedged. It gave me both sides. It sounded like a careful referee.

It clearly "knew" the passage. It could quote it on demand. But when I needed it to *pick* as Nietzsche would pick, it turned into a Wikipedia page with manners.

That's the gap I kept running into: models learn to explain thinkers; they don't learn to inhabit them.

After fine-tuning on 4,500 Q&A pairs built with what I'm calling Tome Forge, the same base model answered:

*"Ah, my dear friend! Do you truly have time for my playful game to divide human thought? I have shown you in Thucydides how power works, in the rise and decay of cities. Plato only tells you that this is 'bad' which is a sickly morality, not a force to be fought or embraced. Where Thucydides lays the tools you may march over the corpses of men who rise as lions, what will Plato give you? His gilded cage? Rise your hand if you care about conquering, sit if you want comfort. Choose with your stomach, not your head."*

The difference isn't knowledge. It's stance: voice, commitments, the willingness to take a side and argue from inside it.

That gap (knowing about a thinker vs thinking like them) became the problem I couldn't drop.

## The obvious approach (and why it bothered me)

The initial plan was stupidly simple: generate 10,000 Q&A pairs from Nietzsche and call it a day. More data, better model, ship it.

But I couldn't stop wondering if 10,000 pairs would mostly teach "Nietzsche trivia" and polite summarization, not Nietzsche's *moves*.

And worse, "just do Q&A" doesn't generalize across writers.

Dostoevsky thinks through characters and dialogue and a slow build of consciousness. Kant thinks through definitions and architecture: one concept locks into another. A big pile of disconnected questions risks flattening both.

So the naive approach felt like I was building a data factory, not capturing how philosophers actually think.

## The idea that finally clicked

The question that helped was boring on the surface: what makes a dataset *good*?

The answer I kept coming back to was memory.

You finish a book (say 100,000 words) and, years later, you don't remember most of it. You remember a handful of images, a few core arguments, the vibe, and the way the author gets from A to B. You remember shape.

Essence isn't "everything in the book." It's what survives compression in a reader's head.

So I stopped trying to "capture all of Nietzsche" and started trying to capture what a good reader keeps, and how they keep it.

Different kinds of memory compress differently:
- Striking passages (episodic memory) - [see example](#episodic-layer)
- Core concepts (semantic memory) - [see example](#semantic-layer)
- Argument moves (procedural memory) - [see example](#procedural-layer)
- Emotional texture (affective memory) - [see example](#emotional-layer)
- How parts fit together (structural memory) - [see example](#structural-layer)

A good dataset should train all of that, not just definitions.

## The five-layer scheme

That framing pushed me into five question layers. The percentages are what I ended up using for Nietzsche (not commandments):

### Semantic layer (20%)

The straight-A student question: definitions, clarifications, "wait, how can he say X?"

"Wait, Nietzsche claims that Euripides killed tragedy by bringing the audience onto the stage - but wasn't that just making art more democratic?"

Useful foundation. But if you train mostly on this, you get a great explainer and a terrible philosopher.

### Episodic layer (15%)

The thing you remember because it hit you: images, metaphors, the line that sticks.

"That image of Socrates as 'the first theoretical man' who 'dug an abyss between knowledge and art' - wow, that's haunting."

This teaches vividness, not just correctness.

### Procedural layer (20%)

The "show me the joints" questions: how does he get from premise to conclusion, where's the leap, what's the missing step?

"How does he jump from 'Euripides used reason' to 'Euripides destroyed the unconscious creative force'? That's a huge leap."

This is where reasoning lives. You don't get it by pattern-matching the tone.

### Emotional layer (15%)

The reaction questions: unease, annoyance, attraction, disgust. Nietzsche is hard to read if you refuse to feel anything.

"Why does this feel like watching someone blame the Enlightenment for everything wrong with the world?"

Models that only do "neutral analysis" miss a big part of why Nietzsche works at all.

### Structural layer (15%)

The "where does this sit in the whole book?" questions.

"How does this chapter fit with the book's overall argument? Is the whole book just building to 'and therefore, Socrates ruined everything'?"

Arguments don't only live inside paragraphs; they live inside a book's structure.

The remaining percentage went to personal/meta questions: confusion, objections, contemporary relevance. The stuff real readers actually ask, not the stuff a syllabus forces.

## What the data actually looks like

I've included full JSON examples from each layer in the appendix below. Each entry has:
- The question
- Three thinking components (question analysis, textual grounding, reasoning approach)
- The answer in Nietzsche's voice

Those "thinking" fields were my way of forcing quality at generation time. Before the model could answer, it had to:
1. Interpret what the reader is really asking
2. Identify specific passages from the chapter that inform the answer
3. Articulate an argumentative strategy

These fields materially improved dataset quality. They enforced grounding in specific chapter text rather than generic knowledge. They forced genuine reasoning traces rather than empty formalism.

Practically: I trained the fine-tuned models on Q&A only, not on the thinking fields. At this scale, smaller models didn't reliably learn to emit those traces. But requiring them during generation still improved the data, which is what mattered.

## The constraint that changed everything

Early generations were accurate but lifeless. "Nietzsche believed X because Y, as evidenced by Z."

Technically correct. Philosophically dead.

The model was drawing on its general knowledge of Nietzsche rather than engaging with the specific chapter text in front of it.

The fix was a hard constraint: every answer must be grounded in the specific chapter being discussed. Not generic Nietzsche lore. This chapter. These passages. These arguments.

This seems limiting. Why not let the model draw on everything?

Because I wasn't building a Nietzsche encyclopedia. I wanted a model that reads closely and argues from text. The constraint forces specificity. The model can't hide behind "Nietzsche generally believed..." It has to say "In this chapter, when Nietzsche describes..."

This constraint also prevents collapse. Without it, the model defaults to its pre-training knowledge, in generic assistant mode. With it, the model must engage with the specific argument in front of it.

## Voice as a forcing function

I don't ask the model to explain Nietzsche's ideas. I ask it to respond *as Nietzsche*, in his voice.

This isn't cosplay. It's a forcing function.

"Explain this concept" → model reaches for clear, helpful, neutral.
"Respond as Nietzsche" → the model has to pay attention to tone, cadence, and rhetorical habits.

The model learns that Nietzsche doesn't just present arguments. He provokes. Challenges. Uses metaphor. Deploys irony. The Nietzsche model should feel different from a Kant model, not just in content but in how it argues.

And the 80-150 word constraint matters more than it sounds. Too short gets shallow. Too long wanders. That window forces dense, disciplined answers where every sentence has to work.

It also matches how humans actually engage with philosophical ideas. You don't deliver lectures in conversation. You give focused responses that address the specific question while showing depth.

## What I learned (after 4,500 Q&A pairs)

**1. Small models can learn voice.**
A 4B parameter model can sound distinctly Nietzschean. It won't be an encyclopedia, but within the domain it can argue with a recognizable style.

**2. Cognitive diversity beats sheer volume.**
100 questions spanning the layers taught more than 1,000 semantic questions. The model needed examples of different *kinds* of thinking, not more of the same.

**3. The first dataset had 60% semantic questions.**
Models trained on that learned to explain concepts but didn't learn to argue, provoke, or react. It was cognitive monoculture: one mode of thought, repeated until it became the only mode.

The fix: explicit percentage distributions enforced at generation time. Calculate how many questions of each layer per chapter. Build categorization into the pipeline. Don't hope for natural balance.

**4. Early thinking fields were empty.**
"The reader is asking about X. I will explain X." Useless.

The fix: separate the three components and give each real constraints. Question analysis: what is the reader *really* asking? Textual grounding: which passages matter? Reasoning approach: what's the argumentative plan?

Quality improved dramatically.

**5. Voice and reasoning are separable.**
Some models nail the tone but flub the logic. Others argue correctly but sound generic. You need both. Procedural questions for reasoning, voice constraints for style.

## Why this matters beyond Nietzsche

Tome Forge started with Nietzsche, but the scheme is meant to carry over to other writers.

The goal is a model that doesn't just know what someone said, but can think in their particular way.

A Dostoevsky model that develops ideas through character consciousness and psychological depth. A Kant model that builds systematic architectures. An Emerson model that works through metaphor and aphoristic style.

The pattern stays the same:
1. Identify cognitive layers relevant to the author's thinking style
2. Generate questions spanning those layers
3. Ground answers in specific source material
4. Constrain the model to the target voice
5. Include explicit reasoning steps

The framework adapts. Narrative thinkers like Dostoevsky: emphasize episodic and emotional layers to capture psychological development. Systematic thinkers like Kant: procedural and structural layers dominate. Poetic thinkers like Emerson: emotional and episodic layers for aphoristic style.

The five-layer structure is scaffolding. You tune the weights to match the author's cognitive signature.

And this isn't just philosophy: legal reasoning, strategic planning, scientific argumentation, anywhere thinking style matters as much as content.

## The evaluation problem (still unsolved)

This might be the hardest problem in the entire pipeline.

How do you know if a philosophical model is good?

Standard metrics don't help much here.

I developed a qualitative framework:
1. Factual accuracy (no invented citations)
2. Voice authenticity (tone, rhetoric, characteristic moves)
3. Reasoning quality (cogent arguments, premises to conclusions)
4. Specificity (actual passages, not vague generalities)
5. Engagement (would a real reader find this compelling?)

The Thucydides/Plato test became canonical. A model that hedges fails. A model that chooses decisively and argues vigorously succeeds.

But it's subjective and it doesn't scale. I can manually judge 50 responses, not 5,000.

Philosophy isn't just about getting answers right. It's about arguing well. Making distinctions. Challenging assumptions. Following implications. These are qualitative judgments that resist quantification.

We need frameworks that measure:
- Argumentative coherence across multiple responses
- Ability to engage with objections
- Consistency of philosophical commitments
- Quality of analogies and examples
- Skillful use of dialectical moves

Until evaluation gets better, you're partly flying blind. I can tell the fine-tuned models *feel* more authentic and more engaged. I can't score that cleanly.

The evaluation problem remains open.

## Open questions

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

## What's next

The code, data, and models are open.

The real contribution (if there is one) isn't the implementation. It's a way of thinking about what makes training data philosophically rich.

If you're building models that need to do more than retrieve and rephrase, models that need to argue and keep a distinct voice, maybe this helps.

The question isn't whether machines can think. It's whether we can teach them to think *well*, in the particular ways we care about.

I haven't scratched the surface. I didn't cover adaptation to other domains, better evaluation, how far this scales, what happens when you go even smaller.

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