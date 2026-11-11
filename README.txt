AI ASSIGNMENT - General-purpose classical reasoning system
==========================================================

Pipeline: natural-language knowledge base -> internal representation -> classical reasoning -> True / False
No generative AI is used anywhere. Every final decision comes from the classical engines in the notebook.

FILES
-----
reasoning_agent.ipynb  Complete implementation (parser, forward chaining, backward chaining, FOL resolution,
                       causal-model reasoner), experiments, reasoning traces, submission.csv generator, report
                       generator. Prints final accuracy and inference time at the end:
                         - Overall Accuracy       = average of the accuracies on the three datasets
                         - Overall Inference Time = average time per sample (seconds) in each dataset,
                                                    averaged over the three datasets
report.pdf             Short report (Times New Roman, justified, 1.5 line spacing): representation, algorithms,
                       experiments, reasoning traces.
submission.csv         Predictions (columns: id,label) for the private test set -- produced by the notebook
                       (see "HOW TO RUN"). Not included until the private test file is available.
README.txt             This file.

HOW TO RUN (Python 3.9+, e.g. Google Colab)
-------------------------------------------
1. pip install datasets reportlab        (datasets: to download RuleTaker, CounterBench, FOLIO from Hugging Face)
2. Open reasoning_agent.ipynb and "Run all".
   - Section 8 downloads the public datasets (edit HF_IDS if a dataset id differs).
   - Section 10 runs the development experiments (negation semantics chosen on public data, forward vs backward
     chaining comparison, FOLIO / CounterBench evaluation) and prints accuracy and time per dataset.
   - Section 14 regenerates report.pdf from the numbers/traces of that run.
3. For the final submission set TEST_PATH (Section 12) to the private test file (csv/tsv/json/jsonl/parquet),
   e.g. TEST_PATH = '/content/private_test.csv', and re-run Sections 12-13. The notebook writes submission.csv
   with columns id,label (True/False). Columns are detected automatically: id, context/premises,
   question/statement/conclusion; "premises-FOL"/"conclusion-FOL" are used when present.
4. Re-run Section 14 (last cell) to refresh report.pdf after all experiments are done.

NOTE ON THE SHIPPED OUTPUTS
---------------------------
The notebook and report in this folder were generated in an environment WITHOUT internet access, so the public
datasets could not be downloaded there. The stored outputs/numbers are from a synthetic engine-correctness check
only (clearly labelled as such) -- they are NOT results on RuleTaker, CounterBench or FOLIO. Run the notebook with
internet access to obtain and store the real numbers before submitting.

DESIGN SUMMARY
--------------
* Parser: deterministic, template-based; facts, "If ... then ...", "All/Every ... are ...", "X, Y things are Z",
  negation, conjunctions, relations ("The cat chases the dog"). Unmatched sentences become propositional atoms and
  are counted as "unparsed".
* Reasoning: semi-naive forward chaining with negation-as-failure (alternating fixpoint) or explicit open-world
  negation; goal-directed backward chaining; first-order resolution (CNF + Skolemisation + set-of-support) for FOL
  annotations; abduction/intervention reasoning on a Boolean causal model for CounterBench-style questions.
* Efficiency: models are cached per knowledge base, so many queries over one theory cost a single inference.

RULES COMPLIANCE
----------------
* The private test set is never used for development; no labels are created manually for it.
* Python only; standard library (plus `datasets`, `reportlab` for data download / PDF).
