# Neuroimaging and Cognitive Function Analysis

## Overview

This repository contains R code for analyzing relationships between diffusion tensor imaging (DTI) metrics (FA, AD, RD, MD) and executive function composite scores across different age groups. The analysis includes:

- Linear regression models with interaction terms
- Age-stratified visualizations
- Quadratic age relationships
- Johnson-Neyman interval analysis for significant interactions

## Requirements

### Software
- R (version 4.0 or higher recommended)
- RStudio (optional but recommended)

### R Packages
- `ggplot2` for visualization
- `psych` for data scaling
- `jtools` for regression tables
- `interactions` for interaction plots

Install required packages with:
```r
install.packages(c("ggplot2", "psych", "jtools", "interactions"))
```

## Usage

1. **Clone the repository**:
   ```bash
   git clone https://github.com/yourusername/neuro-cognitive-analysis.git
   ```

2. **Run the analysis**:
   - Open `analysis.R` in R/RStudio
   - Run the script interactively or via:
     ```bash
     Rscript analysis.R
     ```

### Using Your Own Data
To use your own dataset:
1. Replace the `prepare_data()` function in `analysis.R` with code to load your data
2. Ensure your data contains these key variables (or rename your columns to match):
   - DTI metrics: FA, AD, RD, MD
   - Cognitive measures: CatSwitchTotC, CatSwitchAcc, TRAIL_SW, CWI_S
   - Demographics: AGE, SEX, ED_YEARS

## Outputs

The analysis generates:

1. **Statistical Models**:
   - Linear models examining DTI-EF relationships
   - Models with age interaction terms
   - Quadratic age models

2. **Visualizations**:
   - Age-stratified scatterplots with regression lines
   - Quadratic age relationships for DTI metrics
   - Interaction plots (using `jtools`)

3. **Results**:
   - Model summaries printed to console
   - Significant interaction effects identified

## Example Results

### Sample Model Output
```r
> summary(results$models$mod.FA)

Call:
lm(formula = EF ~ GENDER + ED_YEARS + FA * AGE, data = data.scale)

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)  0.01245    0.07145   0.174    0.862    
GENDER         -0.10132    0.07145  -1.418    0.158    
ED_YEARS     0.04567    0.07145   0.639    0.523    
FA           0.18452    0.07145   2.582    0.011 *  
AGE         -0.21784    0.07145  -3.049    0.003 ** 
FA:AGE       0.13278    0.07145   1.858    0.065 .  
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

## Customization

Key parameters you might want to adjust:

1. **Age group boundaries**:
   Modify the cut points in the `AGE_GRP` assignment:
   ```r
   cut(data$AGE, breaks = c(0, 34, 54, 69, Inf))
   ```

2. **Executive function composite**:
   Adjust the EF calculation formula:
   ```r
   data.scale$EF <- (data.scale$EFTask1 + data.scale$EFTask2 + 
                    (data.scale$EFTask3 * -1) + (data.scale$EFTask1 * -1))/4
   ```
   Adjust the '* -1' in the formula where tests that have lower scores = better scores so they all measure in the same direction.

3. **Visualization styles**:
   Modify the `custom_theme` object for different plot aesthetics


