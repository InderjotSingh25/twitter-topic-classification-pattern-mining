# Data dictionary

All files are in `data/processed/` and are the exact tables loaded into Power BI.
Percent values are already on a 0-100 scale.

| File | Columns | Description |
|---|---|---|
| `fact_tweets.csv` | tweet_id, topic, tweet, clean_tweet, tweet_length, word_count, followers, friends, retweet_count, is_retweet, split | The 300 cleaned tweets with topic and train/test split |
| `tweets_classified.csv` | same as above + predicted_topic, confidence_margin, result | Every tweet with the Hybrid model's prediction |
| `test_predictions.csv` | tweet_id, tweet, actual, one column per model, predicted, result | Predictions of all four models on the 60 test tweets |
| `class_distribution.csv` | stage, topic, tweets | Tweets per topic, original (351) vs cleaned (300) |
| `model_comparison.csv` | model, accuracy, precision, recall, f1, macro_f1, cv_accuracy, cv_accuracy_std, cv_precision, cv_recall, cv_f1, hybrid_vs_svm_wins_of_25, hybrid_vs_svm_ties, hybrid_vs_svm_pvalue | Test-set and cross-validation metrics. The p-value is rounded to 0.0 in this file; the exact value is about 0.0003 |
| `cv_folds.csv` | model, fold, accuracy | Accuracy of each cross-validation run |
| `per_class_metrics.csv` | model, topic, precision, recall, f1, support | Per-topic metrics for each model |
| `confusion_matrix.csv` | model, actual, predicted, count | Confusion matrix for each model, in long format |
| `frequent_itemsets.csv` | min_support, algorithm, itemset, size, support | Frequent itemsets from Apriori and FP-Growth at each minimum support |
| `association_rules.csv` | min_support, rule, antecedent, consequent, support, confidence, lift | Association rules with confidence of 50% or more |
| `apriori_vs_fpgrowth.csv` | min_support, itemsets, apriori_ms, fpgrowth_ms | Itemset counts and runtimes per minimum support |
| `top_words_by_topic.csv` | topic, rank, word, weight | Highest-weight words per topic |
| `pipeline_stages.csv` | stage_order, stage, branch, rows | Row counts at each pipeline stage |
| `pipeline_steps.csv` | step_no, step, branch, what_happens, tool | The steps of the pipeline |

## Data note

The tweets are public text collected for academic use. They contain Twitter handles
mentioned inside the tweet text. If you reuse this data, follow the platform's terms and
remove or anonymise handles where needed.
