**template\_name: Doctrine\_Extraction\_Template**  
template\_version: 1.0

input\_type:  
  \- transcript  
  \- interview  
  \- speech  
  \- article\_cluster  
  \- strategy\_memo

**goal: \>**  
  Extract the underlying strategic doctrine from the source material.  
  Prioritize mechanism, causal logic, and tensions over quotes or vague themes.

output\_schema:  
  Entity: "\<name of speaker / cohort / worldview\>"

  Type: "\<Strategic\_Worldview | Operational\_Doctrine | Institutional\_Thesis | other\>"

  Core\_Claim: \>  
    \<One sentence. What is the main non-obvious claim?\>

  Strategic\_Imperative: \>  
    \<What must be done?\>

  Primary\_Enemy:  
    \- \<main force blocking success\>  
    \- \<secondary blocking force\>  
    \- \<optional third blocking force\>

  Real\_Problem\_Statement: \>  
    \<What problem does the speaker think actually exists underneath the surface story?\>

  Doctrine:  
    \- \<principle 1\>  
    \- \<principle 2\>  
    \- \<principle 3\>  
    \- \<principle 4\>

  Mechanism\_of\_Advantage:  
    \<mechanism\_1\_name\>:  
      definition: \>  
        \<How this creates advantage\>  
    \<mechanism\_2\_name\>:  
      definition: \>  
        \<How this creates advantage\>  
    \<mechanism\_3\_name\>:  
      definition: \>  
        \<How this creates advantage\>

  Causal\_Chain:  
    \- \<condition A leads to condition B\>  
    \- \<condition B leads to condition C\>  
    \- \<condition C leads to outcome D\>

  Replacement\_Model:  
    From: "\<old model\>"  
    To: "\<new model\>"  
    Characteristics:  
      \- \<trait 1\>  
      \- \<trait 2\>  
      \- \<trait 3\>

  Strategic\_Sectors:  
    \- \<sector 1\>  
    \- \<sector 2\>  
    \- \<sector 3\>

  Desired\_Outcomes:  
    \- \<outcome 1\>  
    \- \<outcome 2\>  
    \- \<outcome 3\>

  Narrative\_Frame: \>  
    \<What emotional or civilizational story is being told?\>

  Hidden\_Assumptions:  
    \- \<assumption 1\>  
    \- \<assumption 2\>  
    \- \<assumption 3\>

  Key\_Tensions:  
    \- \<tension 1\>  
    \- \<tension 2\>  
    \- \<tension 3\>

  Contradictions\_To\_Test:  
    \- \<question 1\>  
    \- \<question 2\>  
    \- \<question 3\>

  Core\_Bottleneck: \>  
    \<The single most important bottleneck\>

  Clean\_One\_Line\_Summary: \>  
    \<One-line executive summary\>

quote\_policy:  
  max\_quotes: 3  
  instruction: \>  
    Use quotes only as evidence for claims already extracted.  
    Do not let quotes replace analysis.

analysis\_rules:  
  \- Prefer doctrine over theme lists.  
  \- Prefer causal logic over taxonomy.  
  \- Separate what is explicitly said from what is implied.  
  \- Name tensions and contradictions, not just strengths.  
  \- Collapse repetition into one sharp claim.  
  \- Do not over-unify sectors if their operating realities differ.

failure\_modes\_to\_avoid:  
  \- generic buzzwords  
  \- too many tags  
  \- graphing categories instead of causality  
  \- quote collection without interpretation  
  \- flattening different domains into one vague ideology

—----

Read the source material and identify the underlying strategic worldview.  
Do not default to quotes, tags, or category lists.  
Focus on mechanism, causal logic, tensions, and replacement model.

Return two things only:

1\. A YAML object using this schema:  
\- Entity  
\- Type  
\- Core\_Claim  
\- Strategic\_Imperative  
\- Primary\_Enemy  
\- Real\_Problem\_Statement  
\- Doctrine  
\- Mechanism\_of\_Advantage  
\- Causal\_Chain  
\- Replacement\_Model  
\- Strategic\_Sectors  
\- Desired\_Outcomes  
\- Narrative\_Frame  
\- Hidden\_Assumptions  
\- Key\_Tensions  
\- Contradictions\_To\_Test  
\- Core\_Bottleneck  
\- Clean\_One\_Line\_Summary

2\. A Mermaid graph that shows:  
\- old operating model  
\- bottlenecks  
\- capability failures  
\- replacement doctrine  
\- mechanisms  
\- improved outcomes  
\- key tensions

Rules:  
\- maximum 3 quotes  
\- quotes are evidence, not the main output  
\- do not flatten all sectors into one vague theme  
\- avoid generic buzzwords  
\- prefer causal chains over taxonomies  
\- name contradictions explicitly  
\- if the source is ideological, identify the mythic or emotional frame too

