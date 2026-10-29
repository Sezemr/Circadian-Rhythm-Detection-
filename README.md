# Circadian rhythms in mouse liver gene expression

Detecting and characterising 24-hour rhythms in core clock gene mRNA in a public, high-resolution mouse liver time series, using cosinor regression in Python.

![Cosinor fits to core clock genes](clock_genes_cosinor.png)

## Biological question

The mammalian circadian clock is built from transcription–translation feedback loops. CLOCK:BMAL1 activates transcription of *Per*, *Cry* and *Nr1d1*. PER and CRY proteins then repress CLOCK:BMAL1, shutting down their own transcription, while REV-ERBα (encoded by *Nr1d1*) represses *Bmal1*.

This project asks: **do the core clock genes show 24-hour mRNA rhythms in mouse liver under constant darkness, and in what order do they peak across the circadian cycle?**

## Data

- **Dataset:** [GEO GSE11923](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE11923) (Hughes et al., 2009).
- **Design:** C57BL/6J mice were entrained to a 12 h light : 12 h dark cycle, then released into constant darkness. Liver was collected every hour for 48 hours (CT18–CT65), with RNA from 3–5 mice pooled per time point, giving 48 samples.
- **Platform:** GPL1261, Affymetrix Mouse Genome 430 2.0 Array (45,101 probesets).
- Data were used as deposited in GEO (normalised by the original authors). The notebook downloads the data directly from GEO, so no data files are stored in this repository.

## Methods

1. **Log₂ transformation** of expression values (raw intensities spanned a ~60,000-fold range).
2. **Probe-to-gene mapping** using the GEO platform annotation for GPL1261 (2016). *Bmal1* appears under its former official symbol, *Arntl*. Where a gene had several probesets, the one with the **highest mean expression** was used: a selection rule independent of rhythmicity, to avoid biasing the test.
3. **Sample times** were extracted from GEO sample titles and checked: 48 samples, in time order, exactly 1 hour apart.
4. **Cosinor regression:** each gene was fitted with y(t) = M + A·cos(2π(t − φ)/24), where M is the MESOR (mean level), A the amplitude and φ the acrophase (peak time). The model was linearised as y = M + β·cos(2πt/24) + γ·sin(2πt/24) and fitted by least squares, with A = √(β² + γ²) and φ from atan2(γ, β). The period was fixed at 24 h.
5. **Statistics:** rhythmicity was tested with an F-test against a flat line (zero amplitude). P-values were corrected for testing six genes using the Benjamini–Hochberg false discovery rate.

## Results

[Paste the table printed by code cell D here]

**Key findings:**

- [All six / Five of six] core clock genes showed significant 24-hour rhythms (q < 0.05) in constant darkness, indicating that the rhythms are generated internally rather than driven by light.
- The genes peak in a **sequence** across the cycle rather than in two opposing groups: *Nr1d1* (REV-ERBα) peaks earliest (CT[ ]), followed by [ ], and *Bmal1* (*Arntl*) latest (CT[ ]).
- Each rise in *Nr1d1* precedes the fall in *Bmal1* by a few hours, consistent with REV-ERBα repressing *Bmal1* transcription.
- The two cryptochromes behave differently: *Cry1* has a strong rhythm ([ ]-fold) peaking late in the subjective night (CT[ ]), whereas *Cry2* has a much weaker rhythm ([ ]-fold) peaking in the subjective day (CT[ ]). The delayed phase of *Cry1* is a known feature of the mammalian clock.
- *Bmal1* shows the largest rhythm ([ ]-fold peak-to-trough).

## Limitations

- **mRNA, not protein:** microarrays measure mRNA abundance. Protein rhythms can lag behind mRNA by several hours or differ entirely, because of translation and protein degradation (e.g. CRY stability).
- **Fixed 24-hour period:** cosinor estimates amplitude and peak time but not the period. The free-running period in constant darkness may differ slightly from 24 h.
- **Single sinusoid:** genes with sharp or non-sinusoidal peaks (e.g. *Per1*) are fitted less well (lower R²), and 12-hour or 8-hour rhythms (reported for some liver genes by Hughes et al.) are not captured.
- **Pooled samples, no replicates:** each time point is one array of pooled RNA from different mice, so between-animal variability can't be estimated directly, and the F-test assumes independent errors between time points.
- **Feeding as a confounder:** mice still eat rhythmically in constant darkness, so some liver rhythms may be driven by feeding rather than the liver's own clock.
- **Annotation:** probe-to-gene mapping uses a 2016 annotation, and probeset choice may affect amplitude estimates.
- **Scope:** only six core clock genes were analysed.

## How to reproduce

Open the notebook in Google Colab and select **Runtime → Run all**. All data are downloaded automatically from GEO.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Sezemr/circadian-rhythm-detection/blob/main/01_setup_and_data.ipynb)

**Software:** Python in Google Colab, with pandas 2.2.3, NumPy 2.1.3, SciPy 1.16.3 and matplotlib 3.10.0.

## Reference

Hughes ME, DiTacchio L, Hayes KR, Vollmers C, Pulivarthy S, Baggs JE, Panda S, Hogenesch JB (2009). Harmonics of circadian gene transcription in mammals. *PLoS Genetics* 5(4): e1000442. https://doi.org/10.1371/journal.pgen.1000442

## Author

[Your full name], BSc Biochemistry, University College London.
