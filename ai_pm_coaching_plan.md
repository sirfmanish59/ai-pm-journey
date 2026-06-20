# AI-native PM coaching plan
**Goal:** Develop coding and technical skills to operate as an AI-native PM at Anthropic, Google Cloud AI, or OpenAI  
**Started:** June 2026  
**Target completion:** 8 weeks  
**Background:** Meta PM (CAPI/Pixel/SDK infra), targeting senior AI PM roles

---

## Progress summary

| Metric | Status |
|--------|--------|
| Current week | 2 |
| Weeks completed | 1 |
| Certifications completed | 1 |
| Day streak | In progress |

---

## Certifications & prior learning

### ‚úÖ Supervised Machine Learning: Regression and Classification ‚Äî Andrew Ng (Coursera)
**Completed:** August 16, 2025  
**Issued by:** DeepLearning.AI and Stanford University (via Coursera)  
**Credential:** https://coursera.org/verify/U2EKSVHXR6UJ  
**Maps to:** Week 1 (model fundamentals)  
**What this unlocks:** Solid understanding of gradient descent, loss functions, linear/logistic regression, the optimization loop. This is the mathematical foundation of how LLMs learn ‚Äî you're ahead of most PMs here.  
**Partial credit awarded:** Week 1 tasks 1‚Äì2 can be considered done. Remaining: write the 1-page LLM explainer for a non-technical CAPI customer.

---

## Phase 1 ‚Äî Foundation (weeks 1‚Äì2)

### Week 1 ‚Äî How LLMs actually work
**Goal:** Build an honest mental model ‚Äî tokens, context, attention ‚Äî not marketing copy.

**Status:** ‚úÖ Complete

| Task | Status |
|------|--------|
| Read Anthropic's model card for Claude | ‚ Done ‚ debriefed on alignment assessment, model welfare, red teaming, real-time monitoring, post-release model modification |
| Read Anthropic's system card / responsible scaling policy | ‚ Done (covered in model card debrief session) |
| Gradient descent, loss functions, regression fundamentals | ‚ Credit via Andrew Ng cert |

**Key concepts internalized:**

**Alignment assessment** ‚Äî does the model actually want what we want it to want? Measured through red teaming, behavioral probing, and consistency testing. PM framing: product-market fit testing for ethical behavior across the full distribution of real-world inputs, including adversarial ones.

**Model welfare** ‚Äî Anthropic doesn't claim Claude is sentient but doesn't claim it isn't either. They take the uncertainty seriously enough to avoid training patterns that produce persistent negative functional states. Shapes product constraints you wouldn't expect ‚Äî some things Anthropic won't do to Claude not for user safety reasons but because of uncertainty about the model itself.

**Red teaming** ‚Äî structured adversarial attempts to find failure modes before release. Two flavors: internal (Anthropic safety team, feeds into training decisions) and external (outside researchers, domain experts in biosecurity, cybersecurity etc.). PM analogy: pre-launch adversarial user research where the users are trying to cause harm. Output is a calibration of robustness, not just a bug list.

**Static weights vs. patchable layers** ‚Äî model weights are frozen at release, you can't hotfix a specific behavior. What CAN change post-release: system prompt (instant), guardrail classifiers (independent of weights), targeted fine-tune runs (expensive, risky, used sparingly). True capability improvements always require a new model. This is why pre-release evals matter so much.

**Real-time monitoring** ‚Äî partial and lagged, not a live SOC dashboard. Anomaly detection at infrastructure level + classifier flagging + red team programs + user/researcher reports. The model's own robustness is the primary defense. Real-time comprehensive monitoring at LLM scale is an unsolved PM/engineering problem ‚Äî genuine opportunity space.

**RSP / ASL levels as product gates** ‚Äî ASL-2, ASL-3, ASL-4 are release gating thresholds. A new model must be evaluated against these before commercial release. As an Anthropic PM, your roadmap can be blocked not by engineering capacity but by an ASL-3 determination. Completely different kind of product constraint than anything at Meta. The RSP is also a regulatory pre-emption strategy ‚Äî making voluntary commitments so specific and public they become de facto industry standards.

**Why capability docs are sparse in the model card** ‚Äî intentional. Model card is a safety/accountability document for policymakers and risk teams, not a product brochure. Detailed capability docs create two problems: roadmap for misuse, and they date fast. Capabilities live in API docs, cookbook, changelog ‚Äî updated continuously. Internal capability documentation at Anthropic is far more detailed than anything public.

**Token economics and BPE vs. LZW** ‚Äî Claude uses BPE (Byte Pair Encoding): vocabulary built statically during training by merging most frequent character pairs across a massive corpus. Common English words = 1 token. JSON punctuation (`"`, `:`, `{`, `,`) = inefficient, often 1 token each, structural overhead with no semantic value. JSON costs ~30‚Äì40% more tokens than equivalent plain English for the same information. LZW (which Manish implemented as a CS undergrad in 2000) builds its dictionary dynamically at runtime ‚Äî gets more efficient the longer/more repetitive the input. BPE is frozen and general-purpose; LZW adapts to your specific data. Prompt caching is Anthropic's closest analog to LZW's "once I've seen this pattern, don't reprocess it" ‚Äî but only at the level of entire cached prefixes, not fine-grained token patterns. Future direction: model-native compressed representations may make tokenization obsolete the way broadband made WinZip irrelevant for most use cases.

**CAPI implication:** Format of events sent to the model directly affects unit economics. Compact schema-plus-values vs. verbose JSON = potentially 30‚Äì40% cost reduction at 10M events/day. Model the same CAPI event in 3 formats and compare token counts before running dollar calculations in Week 2.

---

**Tokenizer tools**
- Visual (color-coded token chunks, best for learning): https://lunary.ai/anthropic-tokenizer
- Full Claude model support (Sonnet, Opus, Haiku, Claude 4): https://www.claudetokenizer.com
- Try pasting a CAPI JSON payload and compare to plain English equivalent ‚Äî the gap is immediately visible

---

**Personal context logged**
- Built LZW compression algorithm in 2000 as a CS undergrad ‚Äî still remembers it in full. Gives genuine intuition for token economics that most PMs don't have.
- Prefers talking through concepts and having notes captured here rather than paper notes ‚Äî use this file as the running notes layer.

---

### Week 2 ‚Äî APIs, inference, and cost math
**Goal:** Understand what you're actually buying when you call an LLM API.

**Status:** ‚¨ú Not started

| Task | Status |
|------|--------|
| Sign up for Anthropic API, call claude-sonnet-4-6 via curl with a basic prompt | ‚¨ú To do |
| Build a Jupyter notebook varying max_tokens, temperature, system prompt ‚Äî compare outputs | ‚¨ú To do |
| Calculate cost per 1000 calls for haiku vs sonnet vs opus at your CAPI use case volume | ‚¨ú To do |

**Build:** `inference_explorer.ipynb` ‚Äî parameterized calls, token counts logged.  
**Apply to your context:** Model what CAPI advertiser signal enrichment would cost at 10M events/day at different model tiers.

---

## Phase 2 ‚Äî Applied (weeks 3‚Äì4)

### Week 3 ‚Äî RAG and knowledge retrieval
**Goal:** Understand why RAG exists, when it beats fine-tuning, and how to spec it for engineers.

**Status:** ‚¨ú Not started

| Task | Status |
|------|--------|
| Read the original RAG paper abstract + LangChain RAG tutorial (skim code, understand concepts) | ‚¨ú To do |
| Build a tiny RAG app: embed 10 Meta help center docs, store in ChromaDB, query with a question | ‚¨ú To do |
| Write a 1-page spec: 'How would a CAPI knowledge assistant work using RAG?' | ‚¨ú To do |

**Build:** `simple_rag.ipynb` using ChromaDB + Anthropic API.  
**Apply to your context:** When would you use RAG vs just stuffing docs into the context window? Write a decision tree.

---

### Week 4 ‚Äî Agents, tool use & orchestration
**Goal:** Understand agentic loops, tool calling, failure modes ‚Äî the core of the 2025‚Äì26 market.

**Status:** ‚¨ú Not started

| Task | Status |
|------|--------|
| Read Anthropic's tool use docs end-to-end ‚Äî implement a 2-tool agent (web search + calculator) | ‚¨ú To do |
| Study one real agent failure: find a public post-mortem of an LLM agent going wrong in production | ‚¨ú To do |
| Write a spec for a 'CAPI diagnostic agent' that can detect and explain signal gaps | ‚¨ú To do |

**Build:** `capi_agent.ipynb` ‚Äî agent with 2 tools that can answer CAPI health questions.  
**Apply to your context:** Map your agentic CAPI vision onto Anthropic's tool use primitives. Where are the gaps?

---

## Phase 3 ‚Äî Craft (weeks 5‚Äì6)

### Week 5 ‚Äî Prompt engineering ‚Äî systematic, not intuitive
**Goal:** Move from 'I tweak prompts until they work' to 'I know why this prompt structure works'.

**Status:** ‚¨ú Not started

| Task | Status |
|------|--------|
| Read Anthropic's prompt engineering docs in full (1‚Äì2 hours) | ‚¨ú To do |
| Take one real CAPI use case and write 3 prompt variants: zero-shot, few-shot, chain-of-thought | ‚¨ú To do |
| Run all 3 variants against 20 test inputs ‚Äî measure which wins and why | ‚¨ú To do |

**Build:** `prompt_variants.csv` with inputs, outputs, and scoring rubric.  
**Apply to your context:** Write the system prompt for an Anthropic 'Conversions API integration assistant' ‚Äî production-grade.

---

### Week 6 ‚Äî Evals ‚Äî your most differentiating skill
**Goal:** Be the PM who can design, run, and interpret evals ‚Äî not just ask for them.

**Status:** ‚¨ú Not started

| Task | Status |
|------|--------|
| Read 'How to evaluate LLM outputs' ‚Äî Eugene Yan and Hamel Husain recommended | ‚¨ú To do |
| Build a 30-item golden dataset for your CAPI assistant prompt from week 5 | ‚¨ú To do |
| Write an LLM-as-judge prompt that scores outputs ‚Äî target 80%+ agreement with manual scores | ‚¨ú To do |

**Build:** `eval_loop.ipynb` ‚Äî automated scoring pipeline against golden set.  
**Apply to your context:** How would you use evals to safely ship a model upgrade at Anthropic? Write the rollout checklist.

---

## Phase 4 ‚Äî Capstone (weeks 7‚Äì8)

### Week 7 ‚Äî Safety, alignment & responsible deployment
**Goal:** Go beyond 'don't hallucinate' ‚Äî understand the landscape a PM at Anthropic actually navigates.

**Status:** ‚¨ú Not started

| Task | Status |
|------|--------|
| Read Anthropic's Responsible Scaling Policy and Claude's model card | ‚¨ú To do |
| Read 2 red-teaming or jailbreak case studies ‚Äî understand the attacker mental model | ‚¨ú To do |
| Write a 'Trust & Safety spec' for your CAPI assistant: what it should refuse, how it handles edge cases | ‚¨ú To do |

**Build:** 1-page document: CAPI assistant safety policy.  
**Apply to your context:** How would you balance capability and safety in a new Anthropic API feature? What's the PM's role?

---

### Week 8 ‚Äî Synthesis: the AI PM portfolio piece
**Goal:** Produce one artifact that proves you think at the model-infrastructure layer.

**Status:** ‚¨ú Not started

| Task | Status |
|------|--------|
| Write a 3-page product brief: 'AI-native Conversions API ‚Äî rebuilt for agentic advertisers' | ‚¨ú To do |
| Record a 5-minute Loom walking through your evals pipeline and what you learned | ‚¨ú To do |
| Apply one insight from this plan to outreach to Shavi Goel, David Dugan, or Benji Shomair | ‚¨ú To do |

**Build:** Product brief PDF + Loom video = your AI PM portfolio.  
**Apply to your context:** Reframe your resume narrative using one concrete deliverable from this 8-week plan.

---

## Daily habits

| Habit | Description |
|-------|-------------|
| Read | 1 AI paper, doc, or changelog (15 min) |
| Reflect | Write 1 sentence about what I learned |
| Build | Run or review 1 API call / notebook cell |
| Apply | Apply a concept to my Meta/CAPI context |

---

## Coaching notes

- **Your CAPI/Pixel/SDK background** means developer empathy is already yours ‚Äî the hardest thing to teach most PMs.
- **Week 4 (agents)** is where your infra instincts become a superpower ‚Äî agentic orchestration is the same reliability problem as webhook pipelines, with LLM steps in the middle.
- **Week 6 (evals)** is the career-defining one. A real eval pipeline you can describe in an interview is a major differentiator at Anthropic or Google Cloud AI.
- **Andrew Ng ML cert** puts you ahead on the math layer ‚Äî you understand *why* the model learns, not just what it does.

---

## Update log

| Date | Update |
|------|--------|
| June 19, 2026 | Week 1 complete. Full debrief logged ‚Äî alignment assessment, model welfare, red teaming, monitoring, static weights, RSP/ASL levels, token economics, BPE vs LZW. Tokenizer links saved. Personal context: built LZW in 2000 as CS undergrad. Moving to Week 2. |
| Aug 16, 2025 | Completed: Andrew Ng ‚Äî Supervised Machine Learning: Regression and Classification. Issued by DeepLearning.AI + Stanford via Coursera. Credential: coursera.org/verify/U2EKSVHXR6UJ. Partial credit applied to Week 1. |

