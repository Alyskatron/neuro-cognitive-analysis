# Neuroimaging and Cognitive Function Analysis
# This script analyzes relationships between diffusion metrics and executive function

# Clear workspace
rm(list = ls())

# Load required packages
required_packages <- c("psych", "ggplot2", "jtools", "interactions")
new_packages <- required_packages[!(required_packages %in% installed.packages()[,"Package"])]
if(length(new_packages)) install.packages(new_packages)
lapply(required_packages, require, character.only = TRUE)

# Global settings
options(scipen = 999)  # Disable scientific notation

# Function to simulate or load data
prepare_data <- function() {
  # For public use, replace with either:
  # 1. Public dataset loading code
  # 2. Simulated data (as shown here)
  
  set.seed(123)
  n <- 200
  simulated_data <- data.frame(
    CatSwitchTotC = rnorm(n),
    CatSwitchAcc = rnorm(n),
    TRAIL_SW = rnorm(n),
    CWI_S = rnorm(n),
    AD = rnorm(n, mean = 1.2, sd = 0.1),
    RD = rnorm(n, mean = 0.7, sd = 0.1),
    MD = rnorm(n, mean = 0.9, sd = 0.1),
    FA = rnorm(n, mean = 0.4, sd = 0.1),
    AGE = sample(20:80, n, replace = TRUE),
    SEX = sample(0:1, n, replace = TRUE),
    ED_YEARS = sample(10:20, n, replace = TRUE)
  )
  
  return(simulated_data)
}

# Main analysis function
run_analysis <- function(data) {
  # Scale the data
  data.scale <- as.data.frame(scale(data))
  
  # Create executive function composite
  data.scale$EF <- (data.scale$EFTask1 + data.scale$EFTask2 + 
                    (data.scale$EFTask3 * -1) + (data.scale$EFTask5 * -1))/4
  data$EF <- (data.scale$EFTask1 + data.scale$EFTask2 + 
                    (data.scale$EFTask3 * -1) + (data.scale$EFTask5 * -1))/4
  
  # Prepare data for jtools
  data.jtools <- data.frame(
    EF = data.scale$EF,
    AD = data$AD,
    RD = data$RD,
    MD = data$MD,
    FA = data$FA,
    AGE = data$AGE,
    ED_YEARS = data$ED_YEARS,
    GENDER = data$GENDER
  )
  
  # Create age groups
  data.scale$AGE_US <- data$AGE
  data.scale$AGE_GRP <- cut(data.scale$AGE_US, 
                           breaks = c(0, 34, 54, 69, Inf),
                           labels = c(1, 2, 3, 4))
  data$AGE_GRP <- cut(data$AGE, 
                     breaks = c(0, 34, 54, 69, Inf),
                     labels = c(1, 2, 3, 4))
  
  # Fit models
  models <- list(
    mod.FA = lm(EF ~ GENDER + ED_YEARS + FA * AGE, data = data.scale),
    mod.AD = lm(EF ~ GENDER + ED_YEARS + AD * AGE, data = data.scale),
    mod.MD = lm(EF ~ GENDER + ED_YEARS + MD * AGE, data = data.scale),
    mod.RD = lm(EF ~ GENDER + ED_YEARS + RD * AGE, data = data.scale)
  )
  
  # Quadratic models
  data.scale$AGE2 <- data.scale$AGE^2
  quad_models <- list(
    mod.FA.quad = lm(EF ~ GENDER + ED_YEARS + FA * AGE + AGE2, data = data.scale),
    mod.AD.quad = lm(EF ~ GENDER + ED_YEARS + AD * AGE + AGE2, data = data.scale),
    mod.MD.quad = lm(EF ~ GENDER + ED_YEARS + MD * AGE + AGE2, data = data.scale),
    mod.RD.quad = lm(EF ~ GENDER + ED_YEARS + RD * AGE + AGE2, data = data.scale)
  )
  
  return(list(
    data = data,
    data.scale = data.scale,
    data.jtools = data.jtools,
    models = models,
    quad_models = quad_models
  ))
}

# Visualization function
create_plots <- function(data, data.scale) {
  # Custom theme
  custom_theme <- theme_bw() + 
    theme(panel.grid.major = element_blank(),
          panel.grid.minor = element_blank())
  
  # Age group plots
  plots <- list(
    FA_plot = ggplot(data.scale, aes(x = FA, y = EF, color = AGE_GRP)) + 
      geom_point() + 
      geom_smooth(method = lm, se = FALSE) + 
      custom_theme,
    
    AD_plot = ggplot(data.scale, aes(x = AD, y = EF, color = AGE_GRP)) + 
      geom_point() + 
      geom_smooth(method = lm, se = FALSE) + 
      custom_theme,
    
    MD_plot = ggplot(data.scale, aes(x = MD, y = EF, color = AGE_GRP)) + 
      geom_point() + 
      geom_smooth(method = lm, se = FALSE) + 
      custom_theme,
    
    RD_plot = ggplot(data.scale, aes(x = RD, y = EF, color = AGE_GRP)) + 
      geom_point() + 
      geom_smooth(method = lm, se = FALSE) + 
      custom_theme
  )
  
  # Quadratic age relationships
  quad_plots <- list(
    MD_quad = ggplot(data, aes(x = AGE, y = MD)) + 
      geom_point() + 
      geom_smooth(method = lm, formula = y ~ poly(x, 2)) +
      labs(y = 'Mean Diffusivity', x = 'Age') +
      scale_x_continuous(limits = c(20, 100), 
      breaks = seq(20, 100, by = 10)),
    
    RD_quad = ggplot(data, aes(x = AGE, y = RD)) + 
      geom_point() + 
      geom_smooth(method = lm, formula = y ~ poly(x, 2)) +
      labs(y = 'Radial Diffusivity', x = 'Age') +
      scale_x_continuous(limits = c(20, 100), 
      breaks = seq(20, 100, by = 10)),
    
    AD_quad = ggplot(data, aes(x = AGE, y = AD)) + 
      geom_point() + 
      geom_smooth(method = lm, formula = y ~ poly(x, 2)) +
      labs(y = 'Axial Diffusivity', x = 'Age') +
      scale_x_continuous(limits = c(20, 100), 
      breaks = seq(20, 100, by = 10))
  )
  
  return(c(plots, quad_plots))
}

# Run the analysis
raw_data <- prepare_data()
analysis_results <- run_analysis(raw_data)
plots <- create_plots(analysis_results$data, analysis_results$data.scale)

# Display one of the plots as an example
print(plots$FA_plot)

# Print model summaries
print(summary(analysis_results$models$mod.FA))
