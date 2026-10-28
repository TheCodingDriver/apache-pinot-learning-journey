# Wellington-Management
# Learning Apache Pinot Under Pressure: From Unfamiliar Technology to a Performance-Focused POC

## Executive summary

One of the interesting challenges I worked on was during my time on the Limit Central project, a market-risk management application used by front-office teams and risk managers.

We were dealing with a large volume of data, approximately 99 million records every day. Our existing process involved loading this data into a cache, performing calculations and transformations, and making the processed information available to users.

The challenge was the amount of time this process was taking. Loading the data into the cache alone took close to two hours, and the overall processing cycle could take several hours.

For a risk-management application, this delay was significant because users needed access to the latest information to make timely decisions.

We started exploring whether there was a better way to handle large-volume data retrieval, and that was when Apache Pinot came into the picture.

## 1. The challenge: why I needed to learn quickly

Limit Central supported risk-related analysis for front-office users and risk managers. The existing pipeline had three linked challenges:

- **Slow data availability:** large daily datasets took significant time to load into the cache and become useful to downstream consumers.
- **Expensive retrieval and processing:** accessing and manipulating large volumes of data added latency to the workflow.
- **A growing need for fresher information:** stakeholders needed data sooner to support time-sensitive decisions.

I was familiar with Java-based enterprise systems and relational database workflows, but Apache Pinot's distributed analytics architecture was unfamiliar. The immediate objective was not to master every Pinot feature; it was to determine whether Pinot could materially improve the workload we actually had.

## 2. How I approached the unfamiliar technology

### Start with the workload, not the tool

I first framed the problem in terms of **data freshness, ingestion time, query latency, and operational complexity**. That gave me a way to evaluate Pinot against the existing pipeline rather than adopting it because it was a newer technology.

### Learn only the architecture needed to test the hypothesis

I focused on the components that mattered to the use case:

| Concept | What I needed to understand |
|---|---|
| Controller | How cluster metadata and table configuration are managed |
| Broker | How incoming queries are routed to servers |
| Server and segments | How data is stored and queries are executed |
| Offline and real-time ingestion | How historical/batch and streaming data can become queryable |
| Columnar storage and indexes | Why selective filters and aggregations can be much faster |
| Operational overhead | What cluster management and additional infrastructure would cost |

### Turn the learning into a concrete POC

I moved from conceptual reading to a workload-specific proof of concept: investigate data ingestion, understand segment-based storage, explore suitable query patterns, and compare data-loading and processing times against the existing approach. I documented findings and collaborated with other teams to share what we learned.

## 3. What I got wrong early — and how my understanding changed

**Suggested reflection — verify that this matches your actual experience before publishing:**

My initial mental model was too broad: I thought of Pinot primarily as a way to make *batch processing itself* faster. That was an oversimplification. Pinot is optimized for **low-latency analytical queries**; it is not a drop-in replacement for every batch-processing engine, transactional database, or application cache.

The more useful question became: **Which part of the end-to-end workflow is slow, and can moving the relevant analytical reads into Pinot remove that bottleneck?**

That shift changed how I evaluated the technology. I began separating **ingestion duration**, **data freshness**, **query latency**, and **overall batch completion time** instead of using one performance number to represent all four. I also learned to consider the cost of running a distributed cluster alongside the speed benefits.

This was the key learning: a new technology is valuable only when its architecture matches the workload and its trade-offs are understood.

## 4. What the POC showed

The internal case-study notes report the following outcomes:

| Measure | Earlier workflow | Reported POC / improved workflow | Interpretation |
|---|---:|---:|---|
| Daily record volume | ~99 million | ~99 million | Scale of the use case, not a performance improvement |
| Data loading | ~2 hours | ~20 minutes | ~83% reduction in elapsed loading time |
| End-of-day processing | ~3 hours | ~60–90 minutes | ~50–67% reduction in elapsed processing time |
| Compute resource costs | Baseline not provided | ~30% potential reduction | Estimate in the project notes; not a verified bill reduction |

**Measurement caveat:** The uploaded notes do not provide benchmark methodology, hardware specifications, exact query distributions, sample sizes, or production monitoring evidence. These results should be described as *reported POC outcomes*, not independently reproducible production benchmarks.

## 5. How I knew I had caught up

I judged progress by whether I could move beyond explaining Pinot in theory and apply it to the actual engineering problem:

1. **Explain the architecture in practical terms:** describe how brokers, servers, segments, and indexing affect data access.
2. **Connect design choices to bottlenecks:** distinguish slow ingestion from slow analytical reads and end-to-end batch delays.
3. **Build and evaluate a POC:** translate the hypothesis into a working investigation with measurable outcomes.
4. **Communicate trade-offs:** discuss why Pinot could help analytical workloads while adding operational overhead and not replacing Oracle's transactional role.
5. **Transfer the learning:** document best practices and share the findings with cross-functional colleagues.

The strongest evidence that I had become productive was not that I knew every Pinot feature. It was that I could **use a previously unfamiliar system to test an architectural hypothesis, explain the results, and help the team make a more informed engineering decision**.

## 6. What I would do differently next time

- Establish separate baseline metrics for ingestion, query latency, data freshness, and total batch duration before testing alternatives.
- Capture representative queries and their selectivity so indexing decisions can be evaluated systematically.
- Record P50/P95 latency, throughput, infrastructure cost, and correctness alongside elapsed processing time.
- Test operational failure scenarios, recovery behavior, and data consistency before recommending a production rollout.
- Keep a short decision log: assumptions, experiments, results, and rejected options.

## 7. The takeaway

This experience taught me how to become productive in an unfamiliar technology quickly: **anchor learning to a real bottleneck, acquire the minimum architecture knowledge needed, test assumptions with a focused POC, revise the mental model when it is too simplistic, and demonstrate progress through measurable outcomes and knowledge sharing.**

---

### Scope and confidentiality

This write-up is a high-level account of an internal proof of concept. It intentionally excludes proprietary schemas, customer data, credentials, infrastructure details, and source code. The quantitative outcomes are taken from project notes and are not presented as public, independently verified benchmarks. Confirm that your employer/client permits publication of the project name and metrics before sharing this repository publicly.
