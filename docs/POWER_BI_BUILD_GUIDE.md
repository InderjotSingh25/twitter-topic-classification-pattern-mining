# Power BI Build Guide: Twitter Topic Classification & Pattern Mining

Every number in these tables was generated from your notebook's exact pipeline (80/20 split, random_state=42) and matches your PPT (86.67 / 85.00 / 80.00 / 68.33).

## 1. Load data (Home → Get data → Text/CSV)
Load all 11 CSVs: fact_tweets, class_distribution, model_comparison, cv_folds, per_class_metrics, confusion_matrix, test_predictions, frequent_itemsets, association_rules, apriori_vs_fpgrowth, top_words_by_topic.
Insert the picture `architecture_diagram.png` on page 2 (Insert → Image).

## 2. Model (Model view)
Create two small tables (Modeling → New table):
- `dim_topic = DISTINCT(fact_tweets[topic])`
- `dim_model = DISTINCT(model_comparison[model])`
Relationships (one-to-many, single direction from dim → table):
- dim_topic[topic] → fact_tweets[topic], test_predictions[actual], per_class_metrics[topic], top_words_by_topic[topic], class_distribution[topic]
- dim_model[model] → per_class_metrics[model], confusion_matrix[model], cv_folds[model]

## 3. Theme
Canvas 1280x720, background #F4F7FB, header bar #0F2A47, accent teal #0E9F9A, amber #F59E0B.
Topic colours (set in every chart): sports #3B82F6, health #10B981, flood #06B6D4, terrorism #EF4444, politics #F59E0B.
Hybrid model = #0E9F9A in every chart, other models grey #9AA9BC.

## 4. Measures (Modeling → New measure)
```
Total Tweets   = COUNTROWS(fact_tweets)
Test Tweets    = COUNTROWS(test_predictions)
Correct        = CALCULATE(COUNTROWS(test_predictions), test_predictions[result]="Correct")
Test Accuracy  = DIVIDE([Correct],[Test Tweets])
Best Accuracy  = CALCULATE(MAX(model_comparison[accuracy]), model_comparison[model]="Hybrid TF-IDF + SVM")
Best CV Acc    = CALCULATE(MAX(model_comparison[cv_accuracy]), model_comparison[model]="Hybrid TF-IDF + SVM")
Avg Tweet Len  = AVERAGE(fact_tweets[tweet_length])
```

## 5. Pages

### Page 1: Overview (Dataset · Classes · Input/Output)
- 4 cards: Total Tweets (300) · Original rows (351, type it in a text box) · Classes (DISTINCTCOUNT(dim_topic[topic]) = 5) · Best Accuracy (86.67)
- Clustered bar: class_distribution (Axis topic, Legend stage, Values tweets). Shows 351 vs 300.
- Donut: fact_tweets topic count (class balance).
- Histogram-style column chart: tweet_length (bin size 20) from fact_tweets.
- Table (Input → Output): test_predictions[tweet], [actual], [predicted], [result]. Conditional icon on result (green tick / red cross). Title it "Input: tweet text → Output: topic label".
- Slicer: topic (dim_topic).

### Page 2: Architecture
- Full-width image architecture_diagram.png + title bar. Nothing else.

### Page 3: Pattern Mining (Apriori vs FP-Growth)
- Slicer: association_rules[min_support] (single select, default 0.03 = the 3% in your PPT).
- Cards: itemsets found (COUNTROWS of frequent_itemsets filtered by slicer and algorithm = FP-Growth), rules found, strongest lift.
- Table: association_rules (rule, support, confidence, lift) sorted by lift. Data bars on confidence.
- Bar: frequent_itemsets top 10 by support (Apriori vs FP-Growth side by side, identical).
- Line: apriori_vs_fpgrowth (x min_support, y apriori_ms and fpgrowth_ms). Honest note below it: "Same itemsets; at 300 tweets both run in milliseconds."
- Scatter: association_rules confidence vs lift, size = support.

### Page 4: Algorithms: Comparison, Validation & Innovation
- Clustered column: model_comparison (Axis model; Values accuracy, precision, recall, f1) on the 60-tweet test set.
- Clustered column: model_comparison cv_accuracy by model, with cv_accuracy_std as error bars (the VALIDATION TEST: 5-fold x 5 repeats on all 300 tweets).
- Line/box: cv_folds (Axis fold, Legend model, Values accuracy). Hybrid line sits above SVM.
- Innovation panel (cards): SVM 85.00 → Hybrid 86.67 (test), 81.33 → 84.13 (CV), "Hybrid beat SVM in 19 of 25 CV runs (4 lost, 2 tied), Wilcoxon p = 0.0003".
- Text box: "Hybrid = word TF-IDF (1-2 grams) + character TF-IDF (3-5 grams) → Linear SVM".
- Bar: top_words_by_topic (what the model learned), small multiples by topic.

### Page 5: Performance Metrics & Charts
- Slicer: dim_model (default Hybrid TF-IDF + SVM).
- Matrix: confusion_matrix (Rows actual, Columns predicted, Values SUM count). Conditional formatting: background colour scale, white to #0E9F9A.
- Clustered bar: per_class_metrics (Axis topic; Values precision, recall, f1).
- Cards: Accuracy, Precision, Recall, F1 (from model_comparison with the model slicer).
- Table: wrong predictions only (test_predictions filtered result = Wrong): 8 tweets for error analysis.

## 6. Final checks
- Every visual has a title and axis labels. Percent values are already 0-100, format as "0.00".
- Test the topic slicer on page 1 and the model slicer on page 5.
- View → Mobile layout is optional.
- File → Save as .pbix. Publish only if your teacher asks for a link.
