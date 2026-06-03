# Chapter 20: Observability's Stakeholders and Allies

## Core Idea
Observability data — arbitrarily wide structured events capturing user behavior — contains business intelligence that non-engineering teams need. Making this data accessible to adjacent teams creates organizational allies who actively prioritize and fund observability initiatives.

## Key Concepts
- **Democratizing observability data**: Everyone in the organization has a stake in understanding how software behaves with real users. Observability enables this without requiring deep technical expertise.
- **Adjacent team use cases**:
  - **Customer support**: Triage customer-specific issues by querying customer ID dimensions. Confirm whether reported issues are related to known problems. Reduce "blind" ticket accumulation.
  - **Customer success**: Identify which customers are still using features being deprecated. Detect activation patterns ("users who create custom reports are 10x more likely to adopt analytics"). Measure training efficacy.
  - **Product teams**: Understand real feature usage vs. expected. Find adoption outliers. Quantify funnel drop-off points.
  - **Sales**: Identify which features sell (highest demo usage). Understand strategic customer workloads for SLO prioritization.
  - **Executives**: Translate observability data into cross-cutting strategic language. Connect reliability investments to business outcomes.

- **Observability vs. BI tool comparison**:
  | Dimension | Observability | BI/Data Warehouse |
  |-----------|--------------|-------------------|
  | Query speed | Sub-second to seconds (mandatory) | Minutes to hours (OK) |
  | Accuracy trade-off | Fast > perfect (near-correct) | Perfect, always |
  | Data freshness | Seconds from now | Hours to days |
  | Schema | Schema-on-read, dynamic | Schema-on-write, predefined |
  | Retention focus | Recent + rolling window | Permanent historical |
  | Time windows | Seconds to minutes per trace | Days to months per journey |

- **Complementary use**: BI shows macro trends (feature usage by month); observability shows micro-level requests (exactly which users, with which parameters, in which sequence) feeding into macro views.

## Mental Models
- Think of observability as the "ground truth" layer that BI tools aggregate: BI answers "how did February go?" and observability answers "why did that one customer have 3x slower checkout than others last Tuesday?"
- Sharing the observability tool across engineering and business units creates a single domain language — everyone can see the same data, expressed in terms comprehensible to all.
- Building allies: show each adjacent team how to answer their own domain questions with your observability data → they become active supporters.

## Anti-patterns
- **Treating observability as engineering-only**: Leaves the most powerful adoption allies (product, support, sales, executives) uninformed and unengaged.
- **Using BI tools for real-time operational questions**: BI cannot answer "what is happening right now for customer X?" in the seconds required during an incident.
- **Using observability for long-term historical business reporting**: Observability is ephemeral by design; 2-year-old request traces are not the right data for annual business trends.

## Key Takeaways
1. The same event data used for incident debugging also answers product, support, and business questions.
2. Customer support enabled with observability can triage customer-specific issues before engineering escalation is needed.
3. Observability vs. BI: observability is fast, fresh, and ephemeral; BI is slow, permanent, and schema-constrained.
4. Create templates and saved queries for non-engineering stakeholders to lower the barrier to self-serve.
5. Building organizational allies accelerates adoption velocity far more than any purely technical argument.

## Connects To
- **Ch 19**: Business case ROI framework relies on the non-engineering benefits described here.
- **Ch 21**: OMM's "Understand User Behavior" capability directly maps to the use cases in this chapter.
- **Ch 5**: Wide events with business-logic dimensions are the prerequisite for non-engineering use cases.
