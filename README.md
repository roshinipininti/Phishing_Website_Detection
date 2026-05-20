# Phishing Website Detection using Machine Learning

**Author:** Roshini Pininti
**Domain:** Cybersecurity


## Abstract

Phishing attacks are one of the most damaging forms of cyber attacks, where fraudulent websites impersonate legitimate ones to steal sensitive user information like passwords and bank details. Traditional rule-based detection methods are no longer effective as these attacks continue to evolve.

This project develops a machine learning-based phishing detection system using URL-based and content-based structural features. Three heterogeneous datasets were merged into a consolidated dataset of 68,024 records with 24 features. Five classification algorithms were trained and evaluated, with XGBoost emerging as the best model — achieving 88.73% accuracy and a ROC-AUC of 0.9578 after hyperparameter tuning.


## Project Structure
├── Phishing_Website_Detection_.ipynb
├── PHISHING WEBSITE DETECTION REPORT.docx
└── README.md

## Problem Statement

The goal is to build a precise ML classifier that distinguishes phishing websites from legitimate ones based on lexical and structural properties of URLs and web pages. The task is binary classification — 1 for Phishing, 0 for Legitimate — using quantifiable features like URL length, special character count, domain entropy, number of subdomains, HTTPS token presence, and more.

## Datasets Used

Three publicly available datasets were used and merged into one final dataset.

D1 is sourced from the UCI Repository and contains 77,891 rows with 42 URL-based lexical features. D2 is from Mendeley Data with 11,430 rows and 89 features, perfectly balanced between phishing and legitimate classes. D3 is the PhiUSIIL dataset from Kaggle with 31,225 rows and 56 content-based features.

The final merged dataset `phishing_merged_final.csv` contains 68,024 records with 24 features, approximately 51.3% legitimate and 48.7% phishing, with zero missing values — no oversampling technique like SMOTE was needed due to the near-perfect class balance.

## Data Preprocessing

Labels across all three datasets were standardized to binary integers (0 = Legitimate, 1 = Phishing). URLs were normalized by stripping http/https prefixes, www, whitespace, and trailing slashes. Semantically equivalent columns were renamed to a common convention before merging. D2 and D3 were combined using a LEFT JOIN on the normalized URL key, then concatenated with D1. Missing values were handled using median imputation for numerical columns and most-frequent strategy for categorical ones. A Pearson correlation threshold of 0.95 was applied for feature removal, though no features exceeded this threshold.

## Key EDA Findings

URL length and URL entropy are the strongest individual indicators of phishing. Phishing URLs tend to be significantly longer, more character-dense, and contain more special characters compared to legitimate ones. Phishing domains also tend to have longer and more complex subdomains to mimic trusted domain names.

Features with high correlation to the label (r ≥ 0.30) include url_length, entropy_of_url, number_of_special_char_in_url, number_of_digits_in_url, and average_subdomain_length. Medium correlation features (0.10–0.30) include nb_qm_url, nb_slash_url, entropy_of_domain, having_query, and domain_length. Low correlation features (r < 0.10) include nb_at_url, nb_dollar_url, nb_percent_url, and nb_hyphens_url.


## Models Trained

Five classification algorithms were trained and compared on an 80/20 stratified train-test split.

**Logistic Regression** served as the linear baseline using the sigmoid function with StandardScaler preprocessing and max_iter of 500.

**Random Forest** is a bagging ensemble of 150 decision trees with max_depth of 15, robust against outliers and capable of handling non-linear boundaries.

**XGBoost** is a sequential gradient boosting framework with L1/L2 regularization, using n_estimators=15, max_depth=6, and learning_rate=0.1.

**LightGBM** uses a leaf-wise tree growing approach with Gradient-based One-Side Sampling (GOSS) and Exclusive Feature Bundling (EFB), configured with n_estimators=150 and learning_rate=0.1.

**Extra Trees** is a highly randomized tree ensemble similar to Random Forest but with random split thresholds, configured with n_estimators=150 and max_depth=15.


## Results

Among all baseline models, XGBoost performed best with 87.55% accuracy and a ROC-AUC of 0.9485. LightGBM came in as a close second with 87.27% accuracy. Random Forest showed the highest precision (0.8994) but lower recall. Extra Trees had the highest precision overall (0.9096) but the lowest recall (0.7330) among ensemble models. Logistic Regression had the lowest performance at 78.12% accuracy due to the non-linear nature of URL features.

After hyperparameter tuning using RandomizedSearchCV with 60 iterations and 5-fold stratified cross-validation, the XGBoost model improved significantly. Accuracy went from 87.55% to 88.73%, Recall improved from 84.22% to 88.51% (the most critical gain — 284 more phishing sites correctly caught), F1 Score improved from 0.8683 to 0.8845, and ROC-AUC improved from 0.9485 to 0.9578. The classification threshold was also optimized to 0.46 (from default 0.50) to maximize the F1 Score.


## Visualizations

The notebook includes class distribution bar charts, URL length KDE plots comparing phishing vs legitimate URLs, a feature correlation heatmap across all 24 features, top 10 features correlated with the label, boxplots for the top 6 most discriminating features, multi-metric grouped bar charts for all 5 models, ROC curves and Precision-Recall curves for all models, a model performance heatmap, before vs after tuning comparison charts, side-by-side confusion matrices, and a final ROC curve comparison of baseline vs tuned XGBoost.


## Tech Stack

- **Language:** Python
- **Libraries:** scikit-learn, XGBoost, LightGBM, pandas, numpy, matplotlib, seaborn
- **Environment:** Jupyter Notebook

## Conclusion

This project successfully built, evaluated, and optimized a phishing website detection system using machine learning. Three datasets totalling over 120,000 raw records were cleaned and merged into a high-quality dataset of 68,024 records. The tuned XGBoost model achieved 88.73% accuracy, an F1 Score of 0.8845, and a ROC-AUC of 0.9578 — demonstrating strong generalization across decision thresholds and making it suitable for real-world applications such as browser extensions, email gateways, and web proxies.
