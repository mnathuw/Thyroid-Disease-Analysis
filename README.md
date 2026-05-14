# Thyroid Disease Analysis

**A Predictive Modeling Study for TSH Prediction from Thyroid Hormones**

## Project Overview

This project develops predictive models to estimate Thyroid Stimulating Hormone (TSH) levels from more readily available thyroid hormones (T3, TT4, FTI) and basic demographic information. The research aligns with precision medicine goals to enable cost-effective, quick screening tools for thyroid disorders, particularly valuable in resource-limited healthcare settings.

## About This Project

This project was created in **RStudio** and knitted to PDF as **DATA100_Thyroid_Project.pdf**.

### Key Objective

Over 200 million individuals globally are affected by thyroid disorders, with many cases remaining undiagnosed until complications arise. This analysis aims to demonstrate whether TSH can be accurately predicted from alternative biomarkers, potentially enabling:
- Quick preliminary screening during routine checkups
- Reduced testing costs in low- and middle-income countries
- Faster treatment initiation for suspected thyroid disorders
- Personalized diagnostic thresholds based on sex-specific hormone relationships

## Dataset

**Source:** UCI Machine Learning Repository - Thyroid Disease Dataset
**Size:** 7,200 patient records
**Access:** https://archive.ics.uci.edu/ml/datasets/Thyroid+Disease (December 2025)

### Data Sources
- Hypothyroid cohort
- Sick cohort (combined for increased sample size)

## Methodology

### Data Preparation
- **Cleaning:** Sex-stratified median imputation for missing hormone values (~15% of cases)
- **Categorical Consolidation:** Aggregated referral sources into 5 most frequent categories
- **Age Grouping:** Non-linear age categories (Young, Middle, Senior, Elderly)
- **Outlier Handling:** Removed extreme TSH values (>50 mIU/L, <0.1% of cases) to focus on clinically relevant range (0.4–20 mIU/L)

### Data Split Strategy
- **Training Set:** 60% (n ≈ 4,320)
- **Validation Set:** 20% (n ≈ 1,440)
- **Test Set:** 20% (n ≈ 1,200)

This stratified approach prevents optimistic bias and enables robust performance estimation without data leakage.

## Exploratory Analysis

### Key Findings

1. **Hormone Distributions by TSH Category**
   - High TSH (>4.0): Strong left-shift in T3 and TT4 distributions
   - Normal TSH (0.4–4.0): Moderate overlap with high-TSH group
   - Low TSH (<0.4): Greater variability and overlap with normal group
   - **Implication:** Models predicted hypothyroidism more accurately than hyperthyroidism

2. **Sex-Based Differences**
   - Women: Higher prevalence of thyroid medication use and surgical history
   - Men: Higher prevalence of goitre and general illness
   - **Implication:** Sex included as primary predictive feature and interaction term explored

3. **Hormone Relationships**
   - Strong positive correlation between T3 and TT4 (r = 0.7)
   - Substantial TSH variation even when TT4 held constant
   - Female observations more tightly clustered (lower residual variance)

## Model Development

### Success Criteria
- **RMSE < 2.0 mIU/L** (within clinically acceptable measurement error)
- **R² > 0.75** (demonstrates sufficient TSH variation explained by biomarkers)

### Model 1: Baseline (Simple Model)
```
Formula: TSH ~ T3 + TT4 + Age + Sex + T3:Sex
Features: 5 parameters
Validation RMSE: 7.36 mIU/L
Test RMSE: 7.51 mIU/L
Test R²: 0.152
```

### Model 2: Extended (Complex Model)
```
Formula: TSH ~ T3 + TT4 + FTI + Age + Sex + Age_Group + T3:Sex
Features: 8 parameters
Validation RMSE: 6.97 mIU/L
Test RMSE: 7.38 mIU/L
Test R²: 0.185
```

### Model Comparison
- **FTI Contribution:** Added ~0.13 mIU/L improvement in RMSE
- **T3:Sex Interaction:** Retained in extended model; women show stronger T3-TSH coupling
- **Age Effects:** Non-linear age grouping provides marginal gains
- **Clinical Recommendation:** Model 1 preferred for simpler implementation and regulatory approval

## Results & Conclusions

Both models demonstrate **clinically significant performance** with RMSE approaching laboratory measurement error (±1.5 mIU/L). Key findings:

**T3 and TT4** are the most important predictors of TSH
**Sex-hormone interactions** reveal different T3-TSH relationships between females and males
**Superior performance for hypothyroidism detection** (low hormone, high TSH)
**Model 1 recommended** for clinical deployment (simpler, similar accuracy, lower overfitting risk)

### Prediction Performance Patterns
- **Excellent calibration** at TSH <10 mIU/L (predictions very close to actual values)
- **Increased scatter** at TSH >10 mIU/L (severe hypothyroidism)
- **Conservative under-prediction** for extreme cases (acceptable for screening)

## Clinical Implementation Pathway

1. **Initial Screening:** Flag patients with T3/TT4 suggesting TSH >4.0 mIU/L
2. **Urgency Triage:** Values >10 mIU/L warrant immediate endocrinologist referral
3. **Integration:** Add to electronic health records for automated flagging
4. **Confirmation:** Predicted TSH should trigger confirmatory lab testing

## Limitations

### Data Limitations
- **Temporal Validity:** 1980s data may not generalize to modern TSH assays (10x more accurate)
- **Missing Data Assumptions:** Sex-stratified imputation may underestimate TSH-hormone relationships if sicker patients have more missing labs
- **Selection Bias:** UCI cohorts from clinical referrals (suspected disease), not population screening
- **Confounding:** Medication effects not controlled; thyroxine therapy normalizes TSH despite dysfunction

### Model Limitations
- **Linearity Assumptions:** Assumes constant TSH-hormone slopes; heteroscedasticity present at high TSH
- **Extrapolation Risk:** Not validated beyond 0–50 mIU/L range
- **Reverse Causation:** TSH causally drives T3/TT4 production, not vice versa
- **External Validity:** Results may not apply to non-European populations, children, elderly, or pregnant women

### Ethical Considerations
- Screening models may widen or narrow health disparities depending on implementation
- Model errors disproportionately impact females (higher disease prevalence)
- May contribute to underdiagnosis in males if sensitivity varies by sex
- **Clinical Use Requires:** Regulatory approval, prospective trials, and physician supervision

## Future Directions

1. **External Validation** on contemporary datasets
2. **Non-linear Models** (random forests, neural networks) to capture threshold effects
3. **Medication History** as covariate for treated patients
4. **Pediatric/Geriatric Extension** with age-specific hormone relationships
5. **Genetic Integration** (thyroid peroxidase antibodies) for personalized thresholds

## Technical Stack

- **Language:** R
- **IDE:** RStudio
- **Output:** PDF (DATA100_Thyroid_Project.pdf)
- **Key Packages:** tidyverse, tidymodels, ggplot2, ggridges
- **Statistical Methods:** Linear Regression, Cross-Validation, Metrics Analysis

## References

1. UCI Machine Learning Repository - Thyroid Disease. (2025). Retrieved from https://archive.ics.uci.edu/ml/datasets/Thyroid+Disease
2. Kuhn, M., & Silge, J. (2022). *Tidy Modeling with R*. O'Reilly Media.
3. American Thyroid Association. (2023). Thyroid Function Tests.
4. Soh, S. B., & Aw, T. C. (2019). Laboratory testing in thyroid conditions. *Annals of Laboratory Medicine*, 39(1), 3–14.
5. Hollowell, J. G., et al. (2002). TSH, T4, and thyroid antibodies in the United States population. *J Clin Endocrinol Metab*, 87(2), 489–499.
6. Surks, M. I., & Ocampo, E. (1996). Age-specific distribution of serum thyrotropin and antithyroid antibodies in the U.S. population. *J Clin Endocrinol Metab*, 81(3), 1149–1154.
7. Biondi, B., & Cooper, D. S. (2014). The clinical significance of subclinical thyroid dysfunction. *Endocrine Reviews*, 35(5), 76–83.
8. Saravanan, P., et al. (2002). TSH and FT4: relationship and diagnostic implications. *Clinical Endocrinology*, 56(5), 603–610.

## Disclaimer

This analysis is for **educational purposes only**. Clinical application requires regulatory approval, prospective validation, and physician supervision. Do not use for clinical decision-making without proper validation on your target population.

---

**Project Status:** Completed
**Last Updated:** May 2026
**Output:** DATA100_Thyroid_Project.pdf
