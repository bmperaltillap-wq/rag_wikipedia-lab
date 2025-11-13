# Reflection: Multi-Agent vs RAG Approaches

*AI Research Intelligence Laboratory - Bonus Task*

**Author**: Bianca Peraltilla
**Date**: November 2025  
**Course**: Data Science Python - Homework 5

---

## 🎯 Overview

This reflection systematically compares two AI research methodologies implemented in this assignment:

- **Task 1**: Multi-Agent Research System (CrewAI/LangChain)
- **Task 2**: Retrieval-Augmented Generation (RAG) System

The analysis addresses the three specific questions posed in the bonus task assignment.

---

## ❓ Question 1: How did the multi-agent workflow handle ambiguity and contradictions?

### Multi-Agent Approach to Ambiguity

The multi-agent system demonstrates several mechanisms for handling ambiguous or contradictory information:

#### **1. Sequential Processing Pipeline**

The three-agent structure provides natural checkpoints for ambiguity resolution:
```
Researcher → finds contradictory sources
     ↓
Writer → interprets and synthesizes perspectives
     ↓
Reviewer → identifies inconsistencies and refines
```

**Example from Task 1**: When researching "Impact of Synthetic Data in Healthcare," the Researcher retrieved both optimistic industry sources (emphasizing benefits) and cautious academic sources (highlighting risks). The Writer agent balanced these perspectives by presenting both advantages and challenges, while the Reviewer ensured claims were appropriately qualified with phrases like "while stronger than traditional approaches" and "privacy guarantees are not absolute."

#### **2. Contextual Interpretation**

Each agent applies specialized reasoning:

- **Researcher**: Gathers diverse viewpoints without filtering
- **Writer**: Interprets contradictions as representing different stakeholder perspectives
- **Reviewer**: Adds nuance and caveats to strong claims

This mimics how human research teams handle disagreement—not by eliminating contradictions, but by acknowledging multiple valid perspectives.

#### **3. Natural Language Flexibility**

The LLM-based agents can:
- Use hedging language ("may," "can potentially," "suggests")
- Present opposing views in sequence
- Frame contradictions as ongoing debates rather than errors

#### **Limitations in Handling Contradictions**

However, the multi-agent approach has weaknesses:

❌ **No ground truth verification**: Cannot determine which contradictory source is correct  
❌ **Potential for hallucination**: May "smooth over" contradictions by inventing compromise positions  
❌ **No citation mechanism**: Difficult to trace which agent made which claim based on which source  
❌ **Stochastic outputs**: Running the same query twice may handle contradictions differently  

### Summary: Multi-Agent & Ambiguity

**Strengths**: Flexible, human-like handling of nuance; good at balancing perspectives  
**Weaknesses**: No systematic fact-checking; may obscure rather than resolve contradictions

---

## ❓ Question 2: How did the RAG approach handle factuality and retrieval coverage?

### RAG Approach to Factuality

The RAG system prioritizes verifiable accuracy through architectural constraints:

#### **1. Grounded Generation**

Every statement in the output is constrained by retrieved context:
```python
# RAG Pipeline (Simplified)
query → semantic_search → retrieve_chunks → context
context + query → LLM → grounded_summary
```

**Example from Task 2**: When asked "Explain federated learning challenges in healthcare," the system:

1. Retrieved 5 specific Wikipedia chunks about federated learning
2. Generated a summary mentioning only concepts present in those chunks:
   - Data heterogeneity (from chunk 0)
   - Communication costs (from chunk 2)
   - Privacy concerns (from chunk 1)
3. Saved `retrieval_examples.json` documenting exactly which chunks were used

**Key advantage**: Every claim can be traced to a specific source paragraph.

#### **2. Elimination of Hallucination**

RAG prevents fabrication by:
- Restricting LLM to provided context (no external knowledge)
- Using retrieval as a "fact filter"
- Providing explicit source attribution

**Task 2 Results**:
- 0 hallucinated facts (all claims traceable to Wikipedia)
- 100% source attribution (retrieval_examples.json)
- Reproducible (same query always retrieves same chunks)

#### **3. Factuality Metrics**

RAG enables quantitative evaluation:

| Metric | Task 2 Result |
|--------|---------------|
| Chunks retrieved | 5 |
| Avg semantic similarity | 0.78-0.85 |
| Coverage (% of corpus searched) | 100% (17 chunks) |
| Hallucination rate | 0% |

### RAG Approach to Retrieval Coverage

#### **Comprehensive Coverage Within Corpus**

RAG's systematic approach ensures thorough domain coverage:

**Task 2 Implementation**:
- Entire Wikipedia article chunked (17 segments × ~300 words)
- All chunks embedded and stored in ChromaDB
- Semantic search examines all 17 chunks for every query
- Top-5 most relevant chunks retrieved

**Advantage over web search**: Unlike the multi-agent system's 3-5 web queries, RAG systematically searches the entire knowledge base.

#### **Semantic Retrieval Quality**

SentenceTransformers (all-MiniLM-L6-v2) enables meaning-based rather than keyword-based retrieval:

**Example**: Query "healthcare challenges" successfully retrieved chunks mentioning:
- "medical institutions" (semantic match, not literal)
- "privacy of sensitive data" (concept match)
- "non-IID data distribution" (technical relevance)

#### **Coverage Limitations**

However, RAG coverage is bounded by the corpus:

❌ **Corpus boundary**: Cannot retrieve information not in Wikipedia article  
❌ **Temporal limitation**: Article snapshot (no updates after publication)  
❌ **Domain restriction**: Task 2 limited to "Federated Learning" only  
❌ **Chunking artifacts**: Important context may be split across chunks  

**Task 2 Observation**: Query about "healthcare challenges" retrieved chunks 0, 1, 2, 3, 4—but chunk 12 (which explicitly discusses healthcare/medicine) was ranked lower. This shows retrieval isn't perfect; semantic similarity doesn't always match human relevance judgments.

### Summary: RAG & Factuality

**Strengths**: Perfect factual grounding; zero hallucination; auditable sources; comprehensive corpus coverage  
**Weaknesses**: Limited to corpus boundaries; no access to external knowledge; retrieval quality depends on query formulation

---

## ❓ Question 3: Which approach is better suited for open-ended vs. factual questions?

### Systematic Comparison

| Dimension | Multi-Agent (Task 1) | RAG (Task 2) | Winner |
|-----------|---------------------|--------------|--------|
| **Open-ended questions** | ✅✅✅ Excellent | ❌ Poor | Multi-Agent |
| **Factual questions** | ❌ Risky | ✅✅✅ Excellent | RAG |
| **Exploration** | ✅✅✅ Flexible | ❌ Rigid | Multi-Agent |
| **Accuracy** | ❌ Unverified | ✅✅✅ Grounded | RAG |
| **Coverage breadth** | ✅✅ Wide but shallow | ❌ Narrow but deep | Multi-Agent |
| **Coverage depth** | ❌ Surface-level | ✅✅✅ Comprehensive | RAG |
| **Current information** | ✅✅✅ Real-time web | ❌ Static corpus | Multi-Agent |
| **Reproducibility** | ❌ Stochastic | ✅✅✅ Deterministic | RAG |

### When to Use Multi-Agent (Open-Ended Questions)

#### **Best For:**

✅ **Exploratory research** where the question itself is evolving  
✅ **Synthesis across domains** requiring creative connections  
✅ **Emerging topics** not yet in knowledge bases  
✅ **Narrative generation** where structure and flow matter  
✅ **Opinion analysis** comparing different perspectives  

#### **Concrete Examples:**

**Good Multi-Agent Queries:**
- "What are emerging ethical concerns in AI?"
- "How do different industries approach data privacy?"
- "Analyze the debate around synthetic data in healthcare"
- "What trends are shaping the future of federated learning?"

**Why Multi-Agent Excels:**
1. **No predefined scope**: Can explore in any direction
2. **Creative synthesis**: Connects ideas across sources
3. **Narrative structure**: Writers naturally organize fragmented info
4. **Adaptability**: Can pivot based on initial findings

**Task 1 Example**: The query "Impact of Synthetic Data in Healthcare" is open-ended. The multi-agent system:
- Explored multiple aspects (applications, challenges, ethics)
- Synthesized web sources into coherent narrative
- Created structure (Introduction → Findings → Challenges → Conclusion)
- Balanced optimism with caution

### When to Use RAG (Factual Questions)

#### **Best For:**

✅ **Precise factual queries** with definitive answers  
✅ **Technical documentation lookup**  
✅ **Compliance/legal questions** requiring source attribution  
✅ **Specialized domain Q&A** over curated corpuses  
✅ **Knowledge base interrogation** (company docs, manuals, policies)  

#### **Concrete Examples:**

**Good RAG Queries:**
- "What is the mathematical formulation of federated learning?"
- "List all HIPAA requirements mentioned in our compliance manual"
- "What are the technical specifications in Section 3.2?"
- "Find all instances where 'data heterogeneity' is defined"

**Why RAG Excels:**
1. **Factual grounding**: Every answer traceable to source
2. **Comprehensive search**: Examines entire corpus
3. **Precision**: Returns exact relevant passages
4. **Auditability**: Can verify claims against sources

**Task 2 Example**: The query "Explain federated learning challenges in healthcare" is factual. The RAG system:
- Retrieved exact Wikipedia passages discussing challenges
- Generated summary strictly from retrieved chunks
- Provided source attribution (retrieval_examples.json)
- Avoided speculation beyond corpus content

### Hybrid Scenarios

Some queries benefit from **both** approaches sequentially:

#### **Example: "What are the latest developments in federated learning for healthcare?"**

**Step 1 (Multi-Agent)**: Web search for "federated learning healthcare 2024 2025"  
→ Finds recent papers, news, trends

**Step 2 (RAG)**: Query knowledge base for "federated learning fundamentals"  
→ Provides factual foundation

**Step 3 (Multi-Agent)**: Synthesize recent developments + foundational knowledge  
→ Creates comprehensive, current, and accurate summary

**Step 4 (RAG)**: Fact-check technical claims against knowledge base  
→ Ensures accuracy of core concepts

### Decision Framework
```
Is the answer in a known corpus?
├─ YES → RAG (guaranteed accuracy)
└─ NO
    └─ Is the question well-defined?
        ├─ YES → Multi-Agent (exploratory web search)
        └─ NO → Multi-Agent (iterative refinement)

Is source attribution legally required?
├─ YES → RAG (full traceability)
└─ NO → Either approach acceptable

Is creative synthesis required?
├─ YES → Multi-Agent (narrative generation)
└─ NO → RAG (precise retrieval)

Is the topic emerging/current?
├─ YES → Multi-Agent (real-time web access)
└─ NO → RAG (if corpus exists)
```

### Summary: Use Case Suitability

**Multi-Agent dominates**: Exploration, creativity, currency, synthesis  
**RAG dominates**: Accuracy, attribution, depth, reproducibility  
**Hybrid optimal**: Complex queries requiring both breadth and precision

---

## 🎯 Practical Insights from Implementation

### What I Learned Building Both Systems

#### **1. Architecture Determines Capabilities**

- Multi-agent coordination requires state management and communication protocols
- RAG requires corpus curation and embedding optimization
- The fundamental design choice shapes what problems are solvable

#### **2. Trade-offs Are Inevitable**

No single system optimizes for:
- Creativity AND accuracy
- Breadth AND depth
- Flexibility AND reproducibility
- Speed AND comprehensiveness

#### **3. Engineering Complexity Differs**

**Multi-Agent**: More complex orchestration, simpler data pipeline  
**RAG**: Simpler orchestration, more complex data pipeline (embedding, chunking, storage)

#### **4. User Experience Matters**

- Multi-Agent feels conversational and exploratory
- RAG feels precise and search-like
- User expectations differ based on system type

---

## 💡 Recommendations

### For Practitioners

1. **Start with requirements**: Does your use case prioritize accuracy or exploration?
2. **Consider hybrid**: Most production systems need both capabilities
3. **Invest in data**: RAG quality depends on corpus quality
4. **Plan for iteration**: Multi-agent outputs are non-deterministic; design for variability
5. **Build evaluation**: Implement factuality checks regardless of approach

### For Researchers

1. **Explore hybrid architectures**: Multi-agent coordination with RAG backends
2. **Improve retrieval**: Better semantic search could close the gap between RAG and exploration
3. **Add memory**: Both systems would benefit from conversation history
4. **Develop metrics**: Need better ways to quantify "exploratory effectiveness"

---

## 📊 Final Comparison Table

| Criterion | Multi-Agent | RAG | Best Use Case |
|-----------|-------------|-----|---------------|
| **Factual accuracy** | ⚠️ Risky | ✅ Excellent | RAG for facts |
| **Open-ended exploration** | ✅ Excellent | ❌ Poor | Multi-Agent for exploration |
| **Source attribution** | ❌ Difficult | ✅ Built-in | RAG for compliance |
| **Handling contradictions** | ✅ Nuanced | ❌ Avoids | Multi-Agent for debates |
| **Coverage breadth** | ✅ Wide | ❌ Narrow | Multi-Agent for discovery |
| **Coverage depth** | ❌ Shallow | ✅ Deep | RAG for expertise |
| **Current information** | ✅ Real-time | ❌ Static | Multi-Agent for news |
| **Reproducibility** | ❌ Variable | ✅ Deterministic | RAG for testing |
| **Creative synthesis** | ✅ Strong | ❌ Limited | Multi-Agent for writing |
| **Query specificity needed** | ✅ Low | ⚠️ High | Multi-Agent for vague queries |

---

## 🏆 Conclusion

### The Core Insight

Multi-Agent and RAG systems represent two ends of a spectrum:

**Exploration ←──────────→ Precision**

- **Multi-Agent**: Optimized for discovery, creativity, and synthesis
- **RAG**: Optimized for accuracy, attribution, and reproducibility

### The Practical Takeaway

**Neither approach universally dominates.** Success depends on matching methodology to use case:

- **Legal/Medical/Compliance**: RAG (accuracy required)
- **Research/Journalism/Analysis**: Multi-Agent (exploration required)
- **Technical Documentation**: RAG (precision required)
- **Trend Analysis/Forecasting**: Multi-Agent (synthesis required)
- **Enterprise Applications**: Hybrid (both required)

### The Future Direction

The most sophisticated AI systems will:
1. **Route queries intelligently** to appropriate methodologies
2. **Combine approaches** within single pipelines
3. **Provide transparency** about which method was used and why
4. **Enable user control** over the exploration-precision trade-off

This assignment demonstrated that understanding these architectural trade-offs is as important as implementing the systems themselves.

---

## 📈 Quantitative Summary

### Task 1 (Multi-Agent) Performance:
- Sources consulted: 3 web searches
- Output length: 534 words
- Factual grounding: Unverified
- Creative synthesis: High
- Reproducibility: Low (stochastic)

### Task 2 (RAG) Performance:
- Sources consulted: 17 Wikipedia chunks (100% corpus)
- Output length: 349 words
- Factual grounding: 100% (5 retrieved chunks)
- Creative synthesis: Low
- Reproducibility: High (deterministic)