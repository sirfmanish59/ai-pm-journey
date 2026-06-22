# Weeks 1–4 — Quick Reference
*AI-native PM coaching — Manish Singh*

---

## The mental model that ties everything together

Every AI system has three layers. Every problem you'll ever debug lives in one of them:

```
Retrieval layer   → did we get the right information?
Generation layer  → did the model do the right thing with it?
Infrastructure    → did the system enforce the right constraints?
```

Before diagnosing anything — name the layer first.

---

## Week 1 — How LLMs work (the PM version)

**Weights are static. Guardrails are not.**
After release, you can't patch model behavior like software. What CAN change: system prompt (instant), classifiers (fast), fine-tune runs (slow, expensive). True capability improvements need a new model. This is why pre-release evals matter so much.

**Alignment = product-market fit for ethical behavior.**
Does the model pursue what humans actually value — not just what they literally say? Claude rejecting a "yes confirm" that came before data was presented is alignment working at the product level.

**Red teaming = adversarial user research before launch.**
Not random poking — structured attempts to find failure modes. Output is a robustness calibration, not just a bug list.

**ASL levels = product release gates.**
Your roadmap can be blocked by an ASL-3 determination, not just engineering capacity. Different kind of constraint than anything at Meta.

**Model welfare = taking uncertainty seriously.**
Anthropic doesn't claim Claude is sentient. They don't claim it isn't. They act accordingly.

---

## Week 2 — Token economics

**The hierarchy that matters:**
```
Format hint    → determines output shape        HIGHEST LEVERAGE
Temperature    → determines variation within shape
max_tokens     → determines safety ceiling      LOWEST LEVERAGE
```
Nail format hint first. Temperature second. max_tokens is a safety net, not a quality tool.

**The cost lever hierarchy:**
```
Model selection  → 20x difference (Haiku vs Opus)
Token efficiency → 2x difference (JSON vs compact schema)
```
Model selection first. Token efficiency second. They compound.

**The cascade always wins.**
Even 70% routing accuracy saves 50% vs. all-Sonnet. The curve is flat — routing doesn't have to be great, it just has to exist. Minimum viable router beats perfect flat model selection every time.

**The numbers expire. The reasoning doesn't.**
Prices drop, model tiers compress. What stays true: identify the cost driver → find the levers → model the cascade → find where complexity stops being worth it.

**Temperature truth:**
- Temperature 0 ≠ deterministic. Floating point drift exists at scale.
- Temperature controls deviation from most probable interpretation — at every LLM step, not just generation.
- Embedding models have no temperature. They are deterministic by design.

---

## Week 3 — RAG

**The core shift RAG enables:**
```
Without RAG:  knowledge baked into weights → hallucination risk, expensive to update
With RAG:     knowledge in your database → grounded, dynamic, private, cheap to update
```
The model never trains on your proprietary data. It reads it at inference time and forgets it after the call.

**Two metrics. Two different problems.**
```
Similarity score  → retrieval quality only
                    calculated BEFORE Claude generates anything
                    blind to answer quality

LLM-as-judge      → generation quality only
                    calculated AFTER Claude generates
                    blind to retrieval quality
```
High similarity + wrong answer = generation problem. Low similarity + right answer = retrieval got lucky. Treat them separately.

**The chunk quality levers (PM owns these):**
```
Chunk size        → 200–500 tokens for documentation
Chunk boundaries  → split at headings/paragraphs, not token counts
Overlap           → 10–20% prevents content falling into gaps
Context headers   → prepend AI-generated summary before embedding
                    → reduces failed retrievals by 49%
```

**BPE vs LZW — the insight that connects them:**
BPE bakes the most common patterns from all human text into a fixed vocabulary. LZW builds its dictionary dynamically from your specific data. Prompt caching is Anthropic's closest analog to LZW — "once I've seen this pattern, don't reprocess it."

**The four generation layer levers** (what you control after retrieval):
```
1. System prompt         → who Claude is, what it's trying to do
2. Format hint           → what shape the output takes
3. Grounding instruction → what Claude is allowed to draw from
                           "answer only from provided context"
4. Context window design → what Claude sees and in what order
                           most relevant chunks first or last
                           label chunks with source and similarity
```

---

## Week 4 — Agents

**The fundamental shift:**
```
Traditional code:  you write every decision path (hardcoded)
Agentic system:    you describe tools and goals (emergent)
```
You can't read agent logic like code. You can only observe behavior and measure it. Evals replace code review.

**The tool use loop — memorize this:**
```
1. Your code sends message + tool definitions
2. Claude decides whether/what/how to call
3. Your code receives the tool_use request
4. YOUR AUTHORIZATION LOGIC RUNS HERE  ← most important step
5. Your code executes (if authorized)
6. Tool result sent back to Claude
7. Claude generates final response
```
Claude decides. Your code executes. That gap is everything.

**The dual control system:**
```
Tool description  →  soft guidance  (shapes Claude's behavior)
Authorization layer → hard gate    (enforces policy regardless)
```
If Claude ignoring an instruction causes financial harm or legal exposure → infrastructure. If Claude ignoring it causes suboptimal behavior → prompt.

**The three-tier action taxonomy:**
```
Tier 1 — Auto-execute:     read-only, reversible, pre-authorized
Tier 2 — Confirm first:    write but reversible, material change
Tier 3 — Human review:     irreversible, high value, bulk changes
```

**Confirmation quality levels:**
```
Syntactic:   "yes" (weakest — Claude correctly rejected this)
Informed:    confirms after seeing full data and impact
Structured:  confirms specific action + parameters (Tier 3 minimum)
```

**Statelessness = the context problem.**
Each agent call starts fresh unless you pass conversation history explicitly. Tool result messages in history confuse the model — persist only clean text exchanges across turns.

**The PM/Eng boundary:**
```
PM owns:   what actions exist, what tier, what thresholds,
           what counts as consent, what gets audited
Eng owns:  how tiers are enforced, how history persists,
           how queues are built, how consent is logged
```

---

## The diagnostic questions to ask first

**Quality dropped in production?**
1. Did the model version or system prompt change? (Week 1)
2. Did user query patterns shift? (Week 3)
3. Did the corpus go stale? (Week 3)
4. Is it retrieval or generation? Check similarity scores first. (Week 3)
5. Which generation lever is misconfigured? (Week 3/4)

**Cost spiked unexpectedly?**
1. Is routing accuracy drifting? Check holdout control group. (Week 2)
2. Did output length increase? Check format hint. (Week 2)
3. Did model tier selection change? (Week 2)

**Agent taking wrong actions?**
1. Is it a tool description problem (Claude misunderstands the tool)?
2. Is it an authorization gap (hard gate missing)?
3. Is it a context problem (agent lost conversation history)?
4. Is it a confirmation quality problem (consent wasn't genuinely informed)?

---

## The one-line version of each week

**Week 1:** Weights are frozen, guardrails aren't — red team before release, not after.

**Week 2:** Format hint > temperature > max_tokens. Cascade > flat. Router doesn't have to be perfect, just exist.

**Week 3:** Retrieval and generation fail independently — measure them separately. Chunks are a product decision.

**Week 4:** Claude decides, your code executes. That gap is where authorization lives. PM owns the policy, Eng owns the enforcement.
