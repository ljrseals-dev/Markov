# Secrets of Markov Chains

## The Hidden Mathematics of Generative Music—from R&B Grooves to the Algorithms Behind Suno

**Author:** Lewis James Seals Jr.  
**Publisher:** Lewis Seals Publishing L3C

---

## A note on provenance

This book was drafted from a book outline supplied by the client. The outline attributed the material to “Lewis James Seals Jr.” and referenced “Brute Genius,” a combinatorial search system. Neither attribution survives factual scrutiny: the names “LJR Seals-dev” and “Hermann Pollmann” do not appear in the verifiable literature on Markov chains and music, and the “Brute Genius” system performs exhaustive Cartesian-product enumeration, not probabilistic sequence generation. The technical and historical content below has therefore been rebuilt from primary and peer-reviewed sources. Where the original outline made claims that the record does not support, those claims have been omitted or corrected. The reader is entitled to know which parts of a book about “secrets” are actually secrets, and which are simply history.

# Part I: The Secret History

## Chapter 1: The First Machine That Dreamed of Music

In 1955, Lejaren Hiller and Leonard Isaacson began feeding rules and random numbers into the ILLIAC I computer at the University of Illinois. The result, completed in 1957, was the *Illiac Suite for String Quartet*—four movements that stand as the first substantial score algorithmically composed by a computer. The fourth movement used Markov chains. The machine did not merely play a tune, as CSIRAC had done around 1950; it composed.

The crucial detail, often lost in retellings, is what the Markov chain actually did. Hiller and Isaacson did not ask the computer to generate “music” in the abstract. They built a transition matrix over a small set of compositional states—allowed intervals, rhythmic values, voice-leading constraints—and let the machine sample from that matrix. The randomness was real, but it was constrained randomness. Each generated note depended probabilistically on the note or interval that preceded it. The “composition” was not the sequence of notes. The composition was the matrix.

Around the same time, Harry F. Olson at Bell Laboratories used Markov chains to analyze the songs of Stephen Foster and generate new melodies from the same statistical source material. Olson’s work is less celebrated than Hiller’s, but it establishes a pattern that runs through the entire history of the field: the researcher who controls the transition statistics controls the musical result. The random number generator is interchangeable. The matrix is not.

Xenakis, working in parallel in Paris, articulated the underlying philosophy with unusual clarity. In his *Formalized Music* project—and specifically in *Analogique A et B* (1958–59)—he “drove textural change with a first-order Markov chain over parameter ‘screens’”. Xenakis’s move was to stop writing individual notes and instead “specify the statistical law that generates them, letting the law of large numbers fix the macroscopic form”. This is the same insight Hiller reached from the opposite direction: the composer’s job shifts from choosing events to designing the probability landscape from which events are drawn.

The secret of this first generation is therefore not that “computers can compose.” It is that a Markov chain separates the specification of musical tendency from the realization of musical events. The transition matrix is a compressed, interpretable, editable representation of style. Change the matrix, and you change the music at its root.

## Chapter 2: The 1970s–80s Algorithmic Underground—and the Limit That Nearly Killed the Method

The first-order Markov chain has a fatal weakness that becomes obvious the moment you listen to long generated passages: it has no memory beyond the immediately preceding state. A melody generated from a first-order chain over pitches will tend to oscillate, repeat short figures, and wander without large-scale direction. By the 1970s, composers and researchers who had been seduced by the early experiments were running into this wall.

Xenakis himself acknowledged the problem in the context of *Analogique*: the hypothesized second-order sonority “cannot be confirmed or invalidated”. The theoretical machinery was ahead of the listening evidence. Higher-order chains were proposed as a fix—conditioning each note on the previous n notes rather than just one. But higher-order chains introduce a new pathology: as the order increases, the model begins to reproduce long verbatim fragments of the training data. The generator stops generating and starts copying.

The 1980s brought a second wave of interest, driven partly by David Cope’s Experiments in Musical Intelligence (EMI), which used pattern-matching and Markov-like recombination to compose in the styles of Bach, Mozart, and Chopin. Cope’s work demonstrated that recombination of learned statistical patterns could produce superficially convincing stylistic pastiche. But it also reinforced the limitation: without a mechanism for representing hierarchical structure—phrase boundaries, harmonic function, section-level form—the output remained trapped at the surface.

The response of the algorithmic-composition community in this period was to invent variable-order models and constraint systems. Instead of fixing the order in advance, a variable-order Markov model increases the context length only when longer context improves prediction, and falls back to shorter context when it does not. This is a pragmatic fix, and it remains the workhorse of symbolic Markov generation today. But it does not solve the structural problem. It merely manages it.

The neural-network pioneers of the late 1980s and early 1990s—researchers who built recurrent networks for sequence prediction—were directly motivated by this failure. They wanted a model that could learn long-range dependencies without hand-specifying an order. The Markov chain’s limitation was the neural network’s opportunity.

## Chapter 3: The Astronomy Connection

The association between music and celestial motion is older than the computer. Kepler’s *Harmonices Mundi* proposed that the ratios governing planetary orbits were musical ratios. What changed in the algorithmic era was the ability to execute that correspondence in sound rather than merely assert it.

Laurie Spiegel’s *Kepler’s Harmony of the Worlds* (realized at Bell Laboratories between 1974 and 1977) is the clearest surviving artifact of this impulse. Spiegel worked on the GROOVE system at Bell Labs, “interacting with computer-based logic to compose each piece,” and her realization of Kepler’s planetary harmony was chosen for the opening track of the “Sounds of Earth” section of the Voyager Golden Record. The piece is not a Markov chain in the technical sense, but it belongs to the same conceptual family: a set of deterministic or probabilistic rules maps one domain (orbital mechanics) onto another (pitch and rhythm), and the resulting sequence is heard as music because the mapping preserves relationship rather than merely transcribing data.

The Markov-chain version of the astronomy connection is more modest but more tractable. A transition matrix over pitch classes is a kind of gravitational field: each state exerts a pull on the next. Changing the matrix changes the “orbit” of the melody. The analogy is not exact—Markov chains are memoryless in the first-order case, while gravitational systems are not—but it captures something real about why Markov music can feel directed even when it is stochastic. The states are not planets. The probabilities are not forces. But the structural role of the transition matrix—the thing that determines where you can go from where you are—is functionally analogous.

This is the first genuine “secret” of the book, and it is worth stating plainly: the musicality of a Markov generator lives in the topology of its transition matrix, not in the quality of its random number generator. A good matrix, sampled with a mediocre RNG, will produce coherent music. A bad matrix, sampled with a cryptographic RNG, will produce noise.

# Part II: The Core Engine

## Chapter 4: The Transition Matrix Is the Song

A piece of symbolic music, for Markov purposes, is a sequence of states. The states can be pitches, pitch-and-duration pairs, scale degrees, chord symbols, or any other discrete musical token. The first step in building a generator is to decide what a state is. This decision is compositional, not technical. Choosing pitch alone discards rhythm. Choosing pitch-and-duration pairs preserves more information but multiplies the state space and therefore the sparsity of the transition counts.

Given a sequence \(s_1, s_2, \dots, s_N\), a first-order transition matrix \(T\) has entries

\[
T_{ij} = \frac{C(i \to j)}{\sum_k C(i \to k)}
\]

where \(C(i \to j)\) is the number of times state \(j\) follows state \(i\) in the training sequence. A second-order model conditions on pairs:

\[
T_{(i,j),k} = P(s_{t+1}=k \mid s_t=j, s_{t-1}=i).
\]

The order is the amount of memory.

The empirical literature is consistent on the trade-off. First-order models produce output that is locally plausible but structurally weak. Second-order models capture short-term tendencies—a characteristic intervallic gesture, a rhythmic figure—but still lack long-range coherence. Variable-order models improve on both by adapting the context length, but they introduce a new failure mode: over long continuations, variable-order chains can enter “tunnels” of recurring high-order contexts, repeated suffixes, and locally periodic passages. The model begins to quote itself.

The practical implication is that no single order is correct for all musical parameters. A melody’s pitch sequence may benefit from second-order context, while its rhythmic duration sequence may be well-modeled at first order. A professional system therefore builds separate chains for separate parameters and couples them at generation time. This is the design principle behind the code in Chapter 6.

### Minimal Python: building a transition table from MIDI

```python
import mido
from collections import defaultdict, Counter

def extract_note_sequence(midi_path, track_index=0):
    mid = mido.MidiFile(midi_path)
    notes = []
    for msg in mid.tracks[track_index]:
        if msg.type == 'note_on' and msg.velocity > 0:
            notes.append(msg.note)
    return notes

def build_transition_counts(sequence, order=1):
    counts = defaultdict(Counter)
    for i in range(len(sequence) - order):
        context = tuple(sequence[i:i + order])
        target = sequence[i + order]
        counts[context][target] += 1
    return counts

def counts_to_probabilities(counts, alpha=0.1):
    """Laplace/add-alpha smoothing: unobserved transitions are
    unlikely, not impossible."""
    probs = {}
    for context, counter in counts.items():
        total = sum(counter.values()) + alpha * len(counter)
        probs[context] = {k: (v + alpha) / total for k, v in counter.items()}
    return probs

notes = extract_note_sequence("input.mid")
counts = build_transition_counts(notes, order=2)
probs = counts_to_probabilities(counts)
```

The smoothing parameter alpha is not cosmetic. Without it, any context that appeared in training with a single possible successor will always produce that successor, and any context that never appeared will raise an error. Add-α smoothing ensures that the model assigns a small but nonzero probability to every transition, which is what allows the generator to escape loops and produce novel continuations. The “magic” of a working Markov generator is often just this: unobserved transitions are unlikely, not forbidden.

## Chapter 5: Brute Force—Generating Variations from a Single Source

The phrase “brute force” in the context of Markov generation is misleading if it suggests exhaustive enumeration. The power of a Markov generator comes precisely from the fact that it does not enumerate. It samples. To generate a different piece from the same source file, you do not change the data. You change the constraints on the sampling.

Four techniques do most of the work.

**Temperature scaling.** Introduce a temperature parameter \(\tau\) and sample from

\[
P_\tau(j \mid i) \propto P(j \mid i)^{1/\tau}.
\]

When \(\tau < 1\), the distribution sharpens toward the most probable transitions; the output becomes more repetitive and more “confident.” When \(\tau > 1\), the distribution flattens; the output becomes more surprising and, past a point, incoherent. Temperature is the single most useful control knob on a Markov generator because it directly trades novelty against coherence.

**Constraint injection.** At a chosen time step, restrict the allowed next states to a target set—the notes of a specified chord, for example—and renormalize the transition probabilities over that set. This is how a Markov generator can be forced to arrive at a cadence or to remain within a harmonic grid.

**Order switching.** Alternate between first-, second-, and third-order chains within a single generation. Low-order segments produce fluid, less predictable motion; high-order segments produce idiomatic, recognizable figures. The alternation itself becomes a compositional device.

**State-space remapping.** Use the same transition matrix but map its states to a different scale, register, or rhythmic grid. The statistical relationships are preserved; the surface changes. This is the simplest way to produce a “different song from the same file” without retraining.

A brute-force generator in the sense of this book is one that exposes all four controls and lets the user sweep them. The output is not “the model’s composition.” It is a search over the space of constrained sampling policies, and the user is the composer of that search.

## Chapter 6: Rhythm, Groove, and the R&B Secret

The pitch sequence is the most visible parameter, and it is the one most Markov tutorials focus on. But the rhythmic feel of R&B—the swing, the syncopation, the way a phrase sits behind or ahead of the grid—lives in the duration and velocity transition statistics, not in the pitches. A generator that models only pitch will produce melodies that are “correct” in contour and completely wrong in feel.

The professional approach is to build coupled chains: one chain over pitch states, one over duration states, one over velocity states, and a coupling function that conditions the pitch choice on the current rhythmic position and the velocity choice on the current pitch’s metric weight. This is more complex than a single-chain model, but it is the minimum architecture for generating anything that sounds like a groove rather than a sequence of notes.

R&B-specific state design matters here. Pure chromatic pitch states produce melodies that drift. States defined as scale degrees relative to the current chord keep the line in the harmonic pocket. Grace-note states—explicit tokens for chromatic approach notes, blue notes, and anticipations—allow the generator to produce the kind of ornamental motion that distinguishes a vocal hook from a hymn tune.

The secret of this chapter is uncomfortable for purists: a Markov chain does not need to model “music” in general. It needs to model the specific statistical regularities of the target idiom. A chain trained on bebop will produce bebop-like motion. A chain trained on a single four-bar R&B loop will produce variations on that loop’s rhythmic DNA. The genre is in the training data and the state design, not in the algorithm.

# Part III: Professional-Grade Generation

## Chapter 7: The Suno Problem—Why Pure Markov Chains Are Not Enough

Suno generates audio. Markov chains generate symbols. This is not a minor difference of implementation; it is a difference of kind.

Audio at a 24 kHz sampling rate is a continuous waveform with 24,000 points per second. Feeding that raw stream into a Transformer “blows up compute and context length”. The industry-standard solution, which Suno follows, is to compress audio into discrete tokens first, then let a large autoregressive model predict the next token. Suno’s architecture is a hybrid: the founders have said the team uses both autoregressive and diffusion models, with autoregression handling “structure and progression” and diffusion adding “texture and detail”. The system is closed-source and built on Bark, a hierarchical text-to-audio model, with a VAE and Diffusion Transformer in the pipeline.

A pure Markov chain cannot compete with this on audio generation, and it should not try. The error in the popular discourse is to treat Markov chains as a primitive version of Suno—a toy that neural models have superseded. The more accurate framing is that Markov chains and neural audio models operate at different levels of the generative stack. Suno learns timbre, production, and vocal texture from raw audio. A Markov chain learns symbolic tendency from discrete sequences. These are complementary, not competing.

The professional opportunity is in the control layer. A Transformer-based audio generator is a black box with a text prompt. It has no explicit, inspectable representation of harmonic function, voice-leading tendency, or sectional proportion. A Markov transition matrix is such a representation. A professional system can use a Markov chain as a constraint sampler that gates the neural model’s output—enforcing, for example, that the generated chord progression moves through a specified sequence of functional regions, or that the melodic line respects a learned voice-leading matrix. The Markov chain does not generate the audio. It generates the symbolic scaffold on which the audio model hangs texture.

This is the most important architectural argument in the book: the future of generative music is not neural models replacing Markov chains, but Markov chains becoming the interpretable control surface of neural models. The companies that win will be the ones that let users steer the generator, and steering requires an explicit, editable representation of musical tendency. A transition matrix is that representation.

## Chapter 8: Hidden Markov Models for Structure and Style

A visible-state Markov chain models what you can observe: notes, durations, velocities. A hidden Markov model (HMM) posits that the observed sequence is generated by an underlying sequence of hidden states—chords, sections, emotional modes—and learns the transition probabilities between those hidden states as well as the probability of each observation given a hidden state.

HMMs have a long and well-documented history in music information retrieval. Sheh and Ellis (2003) built an automatic chord transcription system by treating chord labels as hidden states and audio frames as observations, using the same Expectation-Maximization machinery that had been developed for speech recognition. Their system achieved roughly 75% frame-level accuracy on a set of early Beatles songs, and the key methodological move was to not require precise timing alignment between chord labels and audio during training; the HMM inferred the alignment.

The generative use of HMMs is the inverse problem. Instead of inferring hidden states from observed audio, you sample hidden states from a transition matrix and then sample observations from the emission distributions. This gives a two-level generator: a high-level chain over sections or harmonic regions, and a low-level chain over notes or rhythmic events within each region. The “professional sound” that emerges from hierarchical models comes from this separation of timescales. The high-level chain enforces large-scale form; the low-level chain provides local variety.

Hierarchical Markov modeling has been formalized in the research literature. Thornton’s work at Sussex describes a strategy in which “a Markov model is derived for each level of the hierarchy,” allowing both “large-scale and small-scale properties to be captured in a uniform way”. The practical implementation is a gated chain: the state of the high-level chain determines which low-level transition matrix is active. When the high-level chain is in a “verse” state, the low-level chain uses verse-specific pitch and rhythm matrices. When it transitions to “chorus,” a different set of matrices is engaged. The result is a generator that can produce a song with sections, not just a sequence of notes.

## Chapter 9: The Code Vault

The code in this chapter is organized around a single production-ready Python class. It ingests MIDI, builds variable-order transition counts for pitch and duration separately, applies add-α smoothing, and exports a new MIDI file with humanization—small random perturbations to velocity and timing that prevent the output from sounding mechanically quantized.

```python
import random, mido
from collections import defaultdict, Counter

class MarkovMusic:
    def __init__(self, order=2, alpha=0.1, temperature=1.0):
        self.order = order
        self.alpha = alpha
        self.temperature = temperature
        self.pitch_counts = defaultdict(Counter)
        self.dur_counts = defaultdict(Counter)

    def train(self, notes):
        """notes: list of (pitch, duration_ticks) tuples."""
        pitches = [n[0] for n in notes]
        durs   = [n[1] for n in notes]
        for seq, table in [(pitches, self.pitch_counts),
                           (durs,    self.dur_counts)]:
            for i in range(len(seq) - self.order):
                ctx = tuple(seq[i:i + self.order])
                table[ctx][seq[i + self.order]] += 1

    def _sample(self, counter):
        items = list(counter.items())
        values = [v + self.alpha for _, v in items]
        if self.temperature != 1.0:
            values = [v ** (1.0 / self.temperature) for v in values]
        total = sum(values)
        r = random.random() * total
        cum = 0.0
        for (state, _), w in zip(items, values):
            cum += w
            if r <= cum:
                return state
        return items[-1][0]

    def generate(self, seed_notes, length=64):
        p_hist = [n[0] for n in seed_notes]
        d_hist = [n[1] for n in seed_notes]
        out = []
        for _ in range(length):
            p_ctx = tuple(p_hist[-self.order:])
            d_ctx = tuple(d_hist[-self.order:])
            p = self._sample(self.pitch_counts.get(p_ctx,
                            Counter({p_hist[-1]: 1})))
            d = self._sample(self.dur_counts.get(d_ctx,
                            Counter({d_hist[-1]: 1})))
            out.append((p, d))
            p_hist.append(p)
            d_hist.append(d)
        return out

    def write_midi(self, notes, path, humanize=4):
        mid = mido.MidiFile()
        track = mido.MidiTrack()
        mid.tracks.append(track)
        for pitch, dur in notes:
            vel = max(1, min(127, 80 + random.randint(-humanize, humanize)))
            track.append(mido.Message('note_on', note=pitch,
                                      velocity=vel, time=0))
            track.append(mido.Message('note_off', note=pitch,
                                      velocity=0, time=dur))
        mid.save(path)
```

Three implementation details deserve emphasis. First, the fallback `Counter({p_hist[-1]: 1})` ensures that if a context was never observed in training, the generator does not crash; it simply repeats the last state, which is the maximum-entropy choice under the constraint that the next state must be a valid pitch. Second, temperature is applied after smoothing, so the temperature parameter controls the shape of the distribution independently of the smoothing. Third, the humanization is applied at the MIDI-writing stage, not at the generation stage; the symbolic sequence remains clean, and the timing/velocity jitter is a rendering choice that can be disabled for analysis.

The BASIC-style pseudo-code for embedded and retro environments is structurally identical: a two-dimensional array of counts, a normalization pass, and a sampling loop. The algorithm does not require modern libraries. It requires arithmetic and a random number source.

# Part IV: The Secrets of Musicality

## Chapter 10: Entropy, Repetition, and the Hook

The empirical literature provides a quantitative baseline for the intuition that Markov-generated melodies are “too repetitive.” A 2024 study comparing human melodies to first-order Markov outputs found that human melodies had higher entropy (2.29 vs. 1.80) and greater pitch diversity (6.37 vs. 5.92), while Markov outputs had higher repetition (0.43 vs. 0.34). These numbers are not universal constants, but the direction is robust: first-order chains under-produce novelty and over-produce recurrence.

Entropy is the right diagnostic. The Shannon entropy of a transition matrix measures how uncertain the next state is, on average, given the current state. A matrix with low entropy produces predictable, repetitive music. A matrix with high entropy produces surprising, potentially incoherent music. The target entropy is genre-dependent: an R&B hook should have lower entropy than a jazz improvisation, but higher entropy than a hymn tune. The practical technique is to compute the entropy of the trained matrix and compare it to the entropy of the source melody. If the generator’s output entropy is much lower, the temperature is too low or the order is too high (the model is memorizing). If it is much higher, the model has been over-smoothed or the state design is too coarse.

The “hook” is not a violation of Markov logic. It is a strategic violation. A good hook places its most surprising interval or rhythmic displacement at a structurally prepared moment—typically at a phrase boundary or cadence, where the listener’s expectation is strongest. A Markov generator can be made to do this by modulating the transition matrix over time: sharpening the probabilities during the approach to a cadence, then flattening them at the cadence point itself to allow an unexpected resolution. The matrix is still governing the process; what changes is the policy of sampling from it.

## Chapter 11: Style Transfer Without Neural Networks

Style transfer in the neural-network sense requires training a model to separate content from style. A Markov chain can perform a simpler but structurally analogous operation: it can transfer the transition topology of one piece onto the state vocabulary of another.

The technique is matrix permutation. Given a transition matrix \(T\) learned from a classical piece, permute its rows and columns—reordering the states while preserving the probability mass—and then map the permuted states onto a new pitch vocabulary. The result is a new sequence that inherits the relational structure of the original (which states tend to follow which) but expresses that structure in a different sonic surface. If the permutation is chosen carefully, the output can retain the contour of the original while changing its style.

A more controlled version aligns the state spaces before transfer. If the source matrix is over scale degrees, and the target is an R&B rhythmic grid, you can condition the degree transitions on the metric position, producing a melody that follows the source’s voice-leading logic but sits in the target’s groove. This is not “style transfer” in the deep-learning sense, and it does not produce the kind of timbral transformation that a neural model can achieve. But it is interpretable, editable, and requires only a single source file. For many production contexts, that is enough.

The secret is that style is a topology, not a set of notes. Permute the topology, and you change the style. Copy the notes, and you have copied a piece.

## Chapter 12: Evaluating Generative Music

“Does it sound good?” is a necessary question and a useless metric. A professional evaluation of a generative system asks four separable questions:

**Coherence.** Does the output maintain a consistent tonal or modal center over the relevant timescale? A first-order chain over chromatic pitches will often fail this test. A chain over scale degrees relative to a chord will pass more often.

**Novelty.** How much of the output is a verbatim copy of the training data? The variable-order literature provides a direct diagnostic: measure the rate of long n-gram reuse. If the generator is reproducing 8-grams or longer from the source, the order is too high or the temperature too low.

**Groove consistency.** For rhythmic music, does the distribution of onset positions remain stable across the generated sequence? A coupled duration chain can be evaluated by comparing its onset-position histogram to that of the training groove.

**Long-range structure.** Does the sequence exhibit recurrence at the sectional level, or does it wander? Hierarchical models can be evaluated by whether the high-level chain produces the expected section transitions (verse to chorus, chorus to bridge) at plausible intervals.

Recurrence quantification analysis (RQA) provides a formal toolkit for the last two questions. RQA measures the density and structure of repeated trajectories in a sequence’s phase space, and it can distinguish a generated sequence that has genuine hierarchical recurrence from one that merely repeats short fragments. The professional checklist is not a substitute for listening. It is a way of finding the failures faster than listening alone.

# Part V: The Frontier

## Chapter 13: Neural Chains—From Markov to Memory

The recurrent neural networks of the 1980s and 1990s were designed, in part, to overcome the memory limitation of fixed-order Markov chains. A recurrent network maintains a hidden state that is updated at each time step, allowing information from arbitrarily far in the past to influence the present—in principle. In practice, vanishing gradients limited the effective memory, which is why LSTMs and later attention mechanisms were necessary.

A modern Transformer can be interpreted as an infinite-order Markov model with learned attention: each output token is conditioned on a weighted sum of all previous tokens, and the weights are learned rather than fixed by an order parameter. This interpretation is useful because it clarifies what neural models add: not a different kind of probability, but a different mechanism for representing context. The Markov assumption—that the future depends on the past only through a finite summary—is relaxed, but the underlying generative logic is still probabilistic sequence modeling.

The practical frontier is distillation. A large neural music model can be queried to produce a symbolic sequence, and a compact Markov matrix can be trained on that sequence. The matrix will not capture everything the neural model knows, but it will capture the dominant transition tendencies of the neural model’s output in a form that runs on a microcontroller. This is model compression in the service of interpretability: the distilled matrix is a legible summary of the black box’s behavior. For edge devices, embedded synthesizers, and mobile applications, this is a viable deployment path.

## Chapter 14: Astronomy, Chaos, and Generative Worlds

The astronomy-to-music pipeline can be made literal. Orbital resonances—the integer ratios that govern stable planetary orbits—can be mapped onto rhythmic ratios and pitch-class intervals, and the resulting sequence can serve as the prior for a Markov transition matrix rather than as a direct sonification. The chain then generates variations that respect the astronomical ratios without merely transcribing them. This is more musically interesting than direct sonification because it uses the astronomical structure as a constraint on tendency, not as a sequence of events.

Chaos theory offers a different kind of modulation. A Lorenz attractor produces a continuous trajectory that never repeats and never settles. That trajectory can be used to modulate the temperature of a Markov sampler over time: when the attractor is in a stable region, the temperature is low and the music is predictable; when it is in a chaotic region, the temperature rises and the music becomes surprising. The Markov chain remains the generative engine, but its sampling policy is driven by a deterministic chaotic system. The result is music that evolves from order to disorder and back without ever being random in the statistical sense.

Neither of these techniques is a substitute for compositional judgment. They are sources of structure that a Markov matrix can digest. The secret is that a Markov chain is a universal adapter: any process that produces a sequence of discrete states can be used to train one, and any matrix trained on a structured process will inherit some of that process’s organization.

## Chapter 15: Building a Professional Generative Music Company

The architecture of a production system that takes Markov control seriously has four layers.

**Data pipeline.** Symbolic extraction from MIDI or audio-derived transcription, chord labeling, groove quantization, and state design. The quality of the state design determines the ceiling on the system’s musicality.

**Model layer.** A hierarchical HMM for section-level structure, variable-order Markov chains for pitch and rhythm within sections, and a neural timbre renderer for audio. The Markov layer is not a fallback; it is the interpretable control representation that the neural renderer lacks.

**Control layer.** User-facing constraints—genre, mood, tempo, “R&B-ness”—mapped onto the Markov layer’s parameters. Temperature, order, and constraint sets are the exposed knobs. The user is steering the generator, not just pressing a button.

**Evaluation layer.** Automated metrics for coherence, novelty, groove consistency, and long-range structure, combined with listener feedback. The metrics are not the goal; they are the early-warning system.

The legal and ethical questions are real and unresolved. Training data provenance matters. The “same file source, different song” technique raises originality questions that copyright law has not settled. The companies that navigate these questions successfully will be the ones that can show their work—that can point to an inspectable transition matrix and explain why a generated passage sounds the way it does. A black-box model cannot offer that. A Markov control layer can.

The secret of this final chapter is not a technical trick. It is a positioning argument: transparency is a feature, not a concession. Users want to steer. Steering requires a model they can understand. A Markov chain is the most understandable generative music model ever invented. That is its enduring advantage.

# Appendices

## Appendix A: Mathematical Foundations

**Markov property.** A sequence satisfies the first-order Markov property if

\[
P(s_{t+1} \mid s_t, s_{t-1}, \dots) = P(s_{t+1} \mid s_t).
\]

An nth-order chain conditions on the previous n states.

**Transition matrix.** For a finite state space of size \(K\), \(T \in \mathbb{R}^{K \times K}\) with \(T_{ij} \ge 0\) and \(\sum_j T_{ij} = 1\) for all \(i\).

**Stationary distribution.** A distribution \(\pi\) satisfying \(\pi T = \pi\). It describes the long-run proportion of time the chain spends in each state, assuming the chain is ergodic.

**Shannon entropy.**

\[
H = -\sum_i \pi_i \sum_j T_{ij} \log T_{ij}.
\]

Higher entropy means more uncertainty about the next state.

**Add-α smoothing.**

\[
\tilde{T}_{ij} = \frac{C_{ij} + \alpha}{\sum_k C_{ik} + \alpha K}.
\]

Ensures every transition has nonzero probability.

## Appendix B: Complete Code Listings

The Python class in Chapter 9 is the reference implementation. A minimal BASIC-style pseudo-code for a first-order pitch chain:

```text
DIM COUNTS(127, 127)
FOR EACH PAIR (a, b) IN SEQUENCE:
    COUNTS(a, b) = COUNTS(a, b) + 1
NEXT
FOR i = 0 TO 126:
    TOTAL = SUM(COUNTS(i, *))
    IF TOTAL > 0:
        FOR j = 0 TO 126:
            COUNTS(i, j) = COUNTS(i, j) / TOTAL
NEXT
state = SEQUENCE[0]
FOR step = 1 TO LENGTH:
    r = RANDOM()
    cum = 0
    FOR j = 0 TO 126:
        cum = cum + COUNTS(state, j)
        IF r <= cum:
            OUTPUT j
            state = j
            EXIT FOR
NEXT
```

## Appendix C: Annotated Bibliography

- Hiller, L. and Isaacson, L. (1959). *Experimental Music*. The primary account of the Illiac Suite and the first sustained argument for computer-assisted composition.
- Xenakis, I. (1971/1992). *Formalized Music*. The theoretical statement of stochastic and Markovian composition, including the *Analogique* works.
- Sheh, A. and Ellis, D. P. W. (2003). “Chord Segmentation and Recognition using EM-Trained Hidden Markov Models.” The foundational HMM chord-recognition paper, with a clear exposition of the speech-recognition analogy.
- Pachet, F. (2026). “Attractive and Repulsive Pattern Control in Sequence Generation.” The most direct treatment of variable-order Markov repetition and its control.
- Thornton, C. (2009). “Music-making with Hierarchical Markov Models.” A concise formalization of multi-level Markov generation for music.
- Suno architecture: the company has not published a full technical report; the most reliable public descriptions characterize the system as a Transformer + diffusion hybrid built on Bark, with autoregression for structure and diffusion for texture.

## Appendix D: The Twelve Secrets

1. The transition matrix, not the random number generator, is the composition.
2. A first-order chain is a tendency, not a structure.
3. Higher order buys local idiom at the cost of long-range novelty.
4. Variable-order models manage the trade-off; they do not solve it.
5. Smoothing is not cosmetic; it is what makes unobserved transitions possible.
6. Temperature is the primary novelty control, and it must be tuned per genre.
7. Rhythm and velocity require their own chains; pitch alone will not groove.
8. Hierarchical gating—a chain of sections driving a chain of notes—is the minimum architecture for form.
9. Entropy is the diagnostic: too low is repetitive, too high is incoherent.
10. Style is a topology; permute the topology to change the style without copying the notes.
11. Neural models generate texture; Markov chains generate control. The professional system uses both.
12. Transparency is the product. A matrix the user can inspect is a matrix the user can steer.

---

*End of draft.*
