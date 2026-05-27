---
title: "Blurry Vision - Do learning paradigms in context-aware Earth embeddings suppress spatial variability?"

tags: [ "deep_learning", "embeddings", "ai"]

date: 2026-05-27T16:12:29-07:00
draft: true
---

# Background

Numerous geospatial embeddings offer competitive performance across downstream applications.
Architecture designs differ fundamentally: 
Pixel-level embeddings (1D CNN, Transformers) exploit spectral/temporal/spectral-temporal information at the pixel-level
Context-aware embeddings (e.g. 2D/3D CNNs, ViT) incorporate spatial context (e.g. textures, shapes) from neighboring pixels. 
Context-aware embeddings tend to produce spatially coherent outputs, but… 
…we observed that compared to pixel-level STM, predictions based on AEF fail to reconstruct high spatial variability in continuous targets.

{{< figure src="/images/blurryvision/blurryglobe.jpg" alt="" width="600px" >}}

-	Wealth of emerging global initiatives on geospatial embeddings [1], [2], [3]
-	Architecture designs differ fundamentally (pixel-level vs. context-aware). 
-	Context-aware training (e.g. CNNs / vision transformers (ViT)) explicitly incorporate spatial context, aggregating information from neighboring pixels. 
-	This potentially enables learning spatial patterns such as texture, shape, and object extent that are characteristic of targets in EO applications, e.g. land cover. 
-	Outputs of context-aware models tend to be more spatially coherent and robust to noise and local variability, improving thematic consistency at the object scale. 
-	Alpha Earth Foundations (AEF) are currently a prominent example of context-aware embeddings available globally and across multiple years. 
-	AEF offer competitive performance for real-world downstream applications [2].
-	Some drawbacks have been identified compared in agricultural applications when compared to handcrafted EO features, including reduced spatial transferability, lacking interpretation, and limited time sensitivity [4].

-	We qualitatively observed that predictions based on AEF fail to capture continuous targets with pronounced spatial variability but instead create overly smooth surfaces.


{{< figure src="/images/blurryvision/figure.jpg" alt="caption" width="600px" >}}

# Reasoning

AEF targets reconstruction of land cover, climate, or DEMs, favoring consistent patches or smooth gradients.
Learning paradigm may not reward the model to learn small-scale spatial semantics, i.e., the model succeeds at its objective without capturing fine spatial details.
This may systematically interfere with the learning of spatially variant and heterogeneous landscape contexts. 


-	Training Earth embedding models with generic reconstruction tasks such as land cover or DEMs aim at generating a broad understanding of surface properties which favours consistent patches of smooth gradients.
-	Potentially, the learning paradigm of AEF does thus not reward the model to learn small-scale spatial semantics, i.e., the training setup might be such that the model can succeed at its objective without needing to capture fine spatial details.
-	This may, however, systematically interfere with the learning of spatially variant and heterogeneous landscape contexts. 

-	We want to explore this as a key limitation of AEF in specific downstream applications where spatial variability is highly relevant, such as canopy height or biomass estimation.

# Hypothesis & RQs

Current learning paradigms in spatially context-aware embeddings, such as AlphaEarth v1, dampen spatial variability in high-variance contexts, e.g. canopy height estimation. 

-	Spatial context is overemphasized and context-aware (i.e. 2D) embeddings, such as AlphaEarth v1 lead to spatially highly consistent predictions. However, these cannot necessarily accurately reflect true spatial variability in heterogeneous environments or in cases where targets can be expected to have high spatial variability, including canopy height or biomass estimation. 

1) Which shallow ML model setup performs best for canopy height (CH) estimation?

2) How does CH estimation performance differ between hand-crafted features (optical + radar STM), pixel-level (TESSERA) and context-aware (AEF) geospatial embeddings?

3) How accurately is true spatial variability in canopy height represented in predictions based on pixel-level vs. context-aware features?


# Experiment

## Data

Study region
-	Miombo woodlands in and around Gilé National Park, Mozambique
-	Heterogeneous open woody landscape with smallholder farmers relying on shifting cultivation, fast regrowth dynamics, regular fire occurrence

Reference
Aerial LiDAR Canopy Height Model @ 1m resolution (2022, source: https://doi.org/10.1038/s43247-024-01448-x)
Aggregated to 10m using mean resampling

Input features
Bi-seasonal S1 / S2 STMs (n=264)
Alpha Earth Foundations (AEF) (n=64)
TESSERA embeddings (n=128)

{{< figure src="/images/blurryvision/aoi.jpg" alt="Study area and 1 m canopy height model used here. For reference, aggregation windows of 5, 10, 25, and 50 Sentinel-2 pixels are visualized" width="600px" >}}

## Workflow

{{< figure src="/images/blurryvision/experiment.jpg" alt="Experimental setup" width="600px" >}}

Predictions
-	We sampled random points from 10m aggregated canopy height model (mean, median, min, max). 
-	Stratified random sampling with bins of 5m (0-5 m, 5-10 m, …, 25-30 m, >30 m) with 1,000 samples per category. The population was split into training (70%) and testing (30%). 
-	We trained regression models on median, mean, and max canopy height @ 10m based on the training split. Inputs were STMs (264 bands), TESSERA (128 bands), AEF (64 bands). 
-	Model evaluation was based on predictions of the test split using RMSE, MAE, ME, as well as R², intercept and slope of linear model between observed~predicted canopy height. Permutations (n = 20) were used to capture variance in performance due to random train/test split.
-	Spatial predictions were created to compare spatial patterns and variance between predictions. 

Analyses
-	Comparison of global performance metrics (RMSE, MAE, R², etc.) across input features and models (XGBoost (XGB), Support Vector Regression (SVR), Random Forest Regression (RFR))
-	Locally, spatial variance metrics are quantified by calculating mean, median, range, and standard deviation of canopy height between STM, AEF, TESSERA and reference (CHM) across non-overlapping windows of varying sizes (5x5, 10x10, 20x20, 50x50, 100x100 pixels) and comparing their statistical distributions. 
-	Residual analysis: with increasing spatial variance, AEF residuals should increase, while STMs remain constant? Scatterplots x axis SD in 5x5 pixel windows, y axis mean error = mean(obs-pred).

-	Comparison of input features and models (XGB, SVR, RFR)
-	Permutation (N=20) to obtain variability of performance across varying train/test splits
-	Selection of best model depending on metric, we here choose XGB due to closest linear fits between observed and predicted across inputs.


# Results

## Models
AEF performs best, followed by STM 
TESSERA does not yield good results in CH estimation
XGBoost yields stable behaviour and fits close to 1:1 line
Overall performance depends on sampling scheme, but patterns remain.

{{< figure src="/images/blurryvision/results_models.png" alt="Canopy height estimation performance with different input features across model types and performance metrics. 20-fold permutations using different training / validation splits" width="600px" >}}

… we continue with XGBoost.

## Global Performance

Global performance indicates slight differences in performance between AEF & STM
AEF consistently yields higher R², lower RMSE
TESSERA has higher spread & stronger tendency for overestimation

{{< figure src="/images/blurryvision/results_global.png" alt="Scatterplots of observed vs. predicted (XGBoost) canopy height across input features." width="600px" >}}

## Patterns

Spatial appearance of predictions is distinct across input features.
STM has visually higher pixel-level variability.

AEF appears smooth, creating underestimation in herbaceous wetland pockets.

TESSERA spatial patterns appear noisy / blocky, potentially linked to Transformer artefacts?

{{< figure src="/images/blurryvision/results_patterns.png" alt="Spatial patterns of observed (top row) and predicted canopy height (second to last row) using XGBoost and STM, AEF, and TESSERA as input features." width="600px" >}}

## Spatial Variance

Quantifying differences between reference and predicted mean / SD CH in non-overlapping NxN pixel windows.
Mean CH consistently overestimated, STM & AEF similar, TESSERA large spread

SD CH consistently under-estimated, and STM appears slightly closer to 0 line.

Is the higher variance in STM predictions true or simply noise resembling true variance ?

{{< figure src="/images/blurryvision/variance.png" alt="Density curves of difference in canopy height metrics (mean, standard deviation) between reference CHM and predictions across window sizes of 5, 10, and 25. Colors indicate input datasets used for predictions (STM, AEF, TESSERA)." width="600px" >}}

## Performance ~ Variance

Results: Performance ~ Variance

To isolate the effect of artificial "blurriness" in high-variance settings, we relate SD CH (representing spatial variability) to error metrics per NxN pixel windows.

Following our hypothesis and empirical insights until this point we would expect

{{< figure src="/images/blurryvision/performance_variance_scheme.jpg" alt="caption" width="600px" >}}

Median trend lines indicate this trend for some variance value ranges.
Compared to STM, AEF has 
lower errors in low-variance settings 
higher errors in high-variance settings
Signal remains weak…

{{< figure src="/images/blurryvision/results_performance_variance.jpg" alt="caption" width="600px" >}}

# Discussion

CH estimation - how to narrow down the parameter space?
CH metrics: mean, median, max…
Regression models: justification for either approach possible
Sampling schemes: random or stratified (+ N bins)

How to better isolate and quantify "blurryness"?
Measures of spatial autocorrelation?
Image processing or computer vision approaches?

TESSERA
Artefacts - is this a common pattern in TESSERA?
Invited co-authors to contribute 

We are fishing in the dark - what are your thoughts? Is it worthwile to continue?