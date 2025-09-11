# DNAmFrailty – Elastic Net
Reproducible code for the elastic net modelling used in the manuscript (target journal: *Nature Communications*).

## Contents
- `analysis/DNAmFrailty_Elastic_Net.Rmd` – main analysis notebook
- `data/` – no raw patient data is tracked; see below
- `.github/workflows/render.yml` – CI to render the Rmd on push and attach the HTML as an artifact
- `CITATION.cff` – citation metadata
- `LICENSE` – project license (MIT)

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
No raw PHI/clinical data are committed to this repository. Place required inputs (e.g., `Bvalues`, `sheet`) in `data/` or provide paths accessible to the Rmd. If permitted, include **de-identified** minimal examples under `data/` or `inst/extdata/` and document them here.

## Reproducibility
- Use `renv` (recommended) to generate a `renv.lock` for the exact package versions used in the paper.
- CI (`render.yml`) installs minimal dependencies and renders the Rmd on each push/PR.
- Consider minting a Zenodo DOI upon acceptance and tagging a `v1.0.0` release.

## Code availability (for manuscript)
> The analysis code supporting this study is publicly available at: **https://github.com/USER/dnamfrailty-elastic-net** (tag: `v1.0.0`). A snapshot of the repository at publication is archived on Zenodo (DOI: *to be added upon release*).

## Citation
If you use this code, please cite the paper (McElheron et al., 2025, once published) and this repository (see `CITATION.cff`).

## License
MIT — see `LICENSE`.
