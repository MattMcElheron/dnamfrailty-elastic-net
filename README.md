# DNAmFrailty – Elastic Net
Reproducible code for the elastic net modelling used in the manuscript McElheron et al 2025.

## Contents
- `analysis/DNAmFrailty_Elastic_Net.Rmd` – main analysis notebook
- `data/` – no raw patient data is tracked; see below
- `.github/workflows/render.yml` – CI to render the Rmd on push and attach the HTML as an artifact
- `CITATION.cff` – citation metadata
- `LICENSE` – project license (MIT)

## TL;DR
Assuming you have Bvals loading (CpG x Individual) and clinical metadata (Individual x Features, i.e. Frailty Index), 
this is how DNAmFrailty was first derived. On a typical PhD-worthy laptop, the entire workflow takes under 45 mins to run on 500 samples,
the majority of which is loading/QC.
```R
set.seed(123)                           # Set Random Seed
FrailtyIndex <- sheet[["FrailtyIndex"]] # Outcome, here is Rockwood Frailty Index
split_prop = 0.65                       # Training Test Split
train_idx <- createDataPartition(FrailtyIndex, p = split_prop, list = FALSE)

# Split training data (Bvals) and outcome (Frailty Data)
Bvals_train <- t(Bvals[,  train_idx, drop = FALSE])
Bvals_test  <- t(Bvals[, -train_idx, drop = FALSE])
Frailty_Index_Train <- FrailtyIndex[train_idx]
Frailty_Index_test  <- FrailtyIndex[-train_idx]

# Tune Alpha Parameter
cv_list <- lapply(alpha_grid, function(a) {
  cv.glmnet(x = as.matrix(Bvals_train),
            y = Frailty_Index_Train,
            alpha = a,
            family = "Poisson",
            nfolds = folds)
})

# Pick alpha with lowest CV error at lambda.min, train model
cv_cvm_min <- sapply(cv_list, function(cv) min(cv$cvm, na.rm = TRUE))
best_alpha <- alpha_grid[which.min(cv_cvm_min)]
DNAmFrailtyModel    <- cv_list[[which.min(cv_cvm_min)]]

# Make Predictions on test/future datasets
# Note that the readout of Poisson models require exponentiating :)
DNAmFrailty_test <- predict(DNAmFrailtyModel, newx = as.matrix(Bvals_test),
                        s = "lambda.min", type = "response")
sheet$DNAmFrailty <- predict(DNAmFrailtyModel, newx = as.matrix(Bvals),
                        s = "lambda.min", type = "response")

# To estimate DNAmFrailty in independent cohrts, data is QC'ed and inputted into the DNAmFrailtyModel.
```

## Quick start
```bash
# 1) Clone
git clone https://github.com/USER/dnamfrailty-elastic-net.git
cd dnamfrailty-elastic-net

# 2) (Recommended) Use renv to lock R packages
R -q -e 'install.packages("renv"); renv::init(bare = TRUE); renv::install(c("rmarkdown","glmnet","caret","dplyr","tidyr")); renv::snapshot()'

# 3) Render
R -q -e 'rmarkdown::render("analysis/DNAmFrailty_Elastic_Net.Rmd")'
```

## Data availability
No raw PHI/clinical data are committed to this repository. Place required inputs (e.g., `Bvalues`, `sheet`) in `data/` or provide paths accessible to the Rmd.

## Reproducibility
- See `renv`.
- Zenodo DOI available upon acceptance.

## Code availability (for manuscript)
> The analysis code supporting this study is publicly available at: **https://github.com/USER/dnamfrailty-elastic-net** (tag: `v1.0.0`).
> A snapshot of the repository at publication is archived on Zenodo (DOI: *to be added upon release*).

## Citation
If you use this code, please cite the paper (McElheron et al., 2025, once published) and this repository (see `CITATION.cff`).

## License
MIT — see `LICENSE`.








