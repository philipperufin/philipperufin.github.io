---
title: "Mapping 17 million fields in Mozambique"

date: "2026-04-23"

tags: ["remote sensing", "deep learning", "smallholder agriculture", "mozambique", "deforestation", "earth observation", "food security", "sub-saharan africa"]

links:
  doi: "https://doi.org/10.1088/1748-9326/ae5cb4"
  dataset: "https://doi.org/10.5281/zenodo.18938383"
  code: "https://github.com/philipperufin/smallholder-fields/"

draft: false

---

*We mapped every active crop field in Mozambique from satellite imagery — about 17 million of them.*

**Research with Pauline Lucie Hammer, Leon-Friedrich Thomas, Sá Nogueira Lisboa, Natasha Ribeiro, Almeida Sitoe, Patrick Hostert & Patrick Meyfroidt**  
*Environmental Research Letters, Vol. 21, 084009 · Published 23 April 2026*  
*DOI: [10.1088/1748-9326/ae5cb4](https://doi.org/10.1088/1748-9326/ae5cb4)*

---

Smallholder farming systems in sub-Saharan Africa are notoriously difficult to map from space. Fields are small, landscapes are fragmented, and existing global products tend to miss large parts of agricultural land. Yet, data on where farming actually happens — and at what scale — is fundamental to designing effective land use and sustainability policies.

For this paper we built the first wall-to-wall field delineation dataset for Mozambique. Using 1.5 m resolution SPOT 6/7 imagery and a deep learning workflow based on the DECODE framework, we delineated roughly 17 million individual fields across the country for the target year 2023. The model was pre-trained on data from France and India, then fine-tuned locally, with a secondarymachine learning model to clean up false positives using satellite embedding features. Our map achieved an overall accuracy of 93.6%, with a median IoU of 0.81 against hand-drawn reference boundaries. A few things stood out when analyzing the data:

**There's more cropland than global maps suggest.** Our error-adjusted estimate puts active cropland at around 76,300 km² — well above ESA WorldCover (50,900 km²) or GLAD (40,900 km²). We found agricultural activity in frontier regions covering about 66,000 km² that were simply missing from existing products. These areas are home to an estimated 1.5-2.8 million people.

**Most fields are tiny, but large fields take up a lot of land.** Half of all fields are smaller than 0.2 ha, and 78% are under 0.5 ha. Nevetheless, fields above 1 ha account for 37% of total cropland area, and those above 2.5 ha for 11%. The far end of the field size distribution matters more than it looks in field counts alone.

**Larger fields, more deforestation.** Linking field size to forest cover change (2010–2020) indicates a pattern where areas with larger mean field sizes lost more forest. Regions with mean field size above 2.5 ha lost around 21% of their 2010 tree cover on average, versus 14% where fields are mostly under 0.5 ha. This challenges the narrative that smallholder agriculture is responsible for most agricultural deforestation in Africa — and medium-scale or large farms seem to matter more than commonly acknowledged.

The data have - as is always the case - important limitations that users should be aware of. The cropland class accuracy is moderate (user's and producer's accuracy both around 67-68%), which mostly reflects the challenge of distinguishing active fields from short-term fallows. Tree crops and agroforestry are excluded from the field definition, which is a gap given their presence in Mozambique. Most importantly, our  analysis depends on commercial satellite imagery that is not publicly available — something we flag as a broader problem for sustainability research in data-scarce regions.

---

## Data & Code Availability

| Resource | Description | Link |
|---|---|---|
| **MozFields 2023 dataset** | Cropland fraction and field size estimates at 0.05° resolution | [zenodo.18938383](https://doi.org/10.5281/zenodo.18938383) |
| **Individual field geometries** | ~17 million field polygons (restricted; available for academic research on request) | [zenodo.19481409](https://doi.org/10.5281/zenodo.19481409) |
| **Fine-tuned model weights** | Mozambique-specific model weights | [zenodo.17531366](https://doi.org/10.5281/zenodo.17531366) |
| **Pre-trained model weights** | Pre-trained model weights (France + India training) | [zenodo.7315089](https://doi.org/10.5281/zenodo.7315089) |
| **Pseudo-label generation code** | Code for producing training pseudo-labels | [github: pseudofields](https://github.com/philipperufin/pseudofields/) |
| **DECODE framework** | Model training, inference, and watershed segmentation | [github: decode](https://github.com/waldnerf/decode/) |
| **Inference & post-processing code** | Supplementary code for national-scale inference | [github: smallholder-fields](https://github.com/philipperufin/smallholder-fields/) |
| **Supplementary materials** | Methods appendix, additional figures and tables | [10.1088/1748-9326/ae5cb4/data1](https://doi.org/10.1088/1748-9326/ae5cb4/data1) |

---

## Citation

> Rufin, P., Hammer, P.L., Thomas, L.-F., Lisboa, S.N., Ribeiro, N., Sitoe, A., Hostert, P. & Meyfroidt, P. (2026). National-scale field delineation in Mozambique refines our understanding of cropland distribution, field size, and deforestation actors. *Environmental Research Letters*, 21, 084009. https://doi.org/10.1088/1748-9326/ae5cb4
