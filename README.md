# 1H NMR Relational Fingerprinting

A Shazam-inspired fingerprinting system for **¹H NMR peak lists**, designed for compound identification and mixture dereplication using relational peak geometry rather than full spectra.

---

# Overview

This project transforms a ¹H NMR peak list into a set of **relational fingerprints** based on:

* Chemical shift differences (ΔδH)
* Relative intensities
* Relative areas
* Peak widths
* Multiplicity relationships

The approach is inspired by the audio fingerprinting strategy used by Shazam:

Instead of matching entire spectra directly, the system converts spectra into thousands of local peak-to-peak relationships and compares these relationships against a reference database.

This allows:

* Identification of pure compounds
* Recognition of compounds inside mixtures
* Robustness to missing peaks
* Robustness to moderate chemical shift drift
* Scalability to large spectral databases

---

# Concept

Traditional NMR matching often relies on:

* Direct peak overlap
* Spectral correlation
* Manual interpretation

This system instead converts spectra into relational networks.

Example:

| Peak A   | Peak B   |
| -------- | -------- |
| 1.25 ppm | 2.37 ppm |

Relationship:

ΔδH = 1.12 ppm

This relationship becomes a fingerprint.

Thousands of these fingerprints are generated for every spectrum.

Unknown spectra are then compared against a library of fingerprints.

---

# Main Features

✅ Build reference databases from peak lists

✅ HMDB-compatible workflows

✅ Mixture searching

✅ Relational fingerprint generation

✅ Weighted scoring

✅ Interactive constellation visualization

✅ Structure annotation support

* SMILES
* InChIKey
* Generic compound names

✅ Exportable databases

* Standardized peak database
* Fingerprint database
* Search results

---

# Installation

## Clone repository

```bash
git clone https://github.com/YOUR_USERNAME/nmr-relational-fingerprinting.git

cd nmr-relational-fingerprinting
```

---

## Create environment

```bash
conda create -n nmr_fp python=3.11
conda activate nmr_fp
```

---

## Install dependencies

```bash
pip install streamlit pandas numpy plotly pillow
```

---

## Run application

```bash
streamlit run app_1D_test.py
```

---

# Workflow

The application is divided into four major sections.

---

# 1. Build Database

Upload reference peak lists.

Supported formats:

```text
.csv
.tsv
.txt
.zip
```

Minimum required column:

```text
delta_H
```

Example:

| delta_H |
| ------- |
| 0.91    |
| 1.23    |
| 2.54    |

---

Recommended format:

| compound_id | compound_name | delta_H | intensity |
| ----------- | ------------- | ------- | --------- |
| HMDB00001   | Alanine       | 1.48    | 100       |
| HMDB00001   | Alanine       | 3.78    | 75        |

---

# Optional Structure Mapping

You may upload an additional metadata file:

```csv
HMDB_ID,SMILES,INCHI_KEY,GENERIC_NAME
HMDB00001,CC(C(=O)O)N,QNAYBMKLOCPYGJ-UHFFFAOYSA-N,Alanine
```

This information is merged automatically.

---

# 2. Generate Fingerprints

Once the database is loaded:

Click:

```text
Generate ¹H NMR Hash Database
```

The software generates relational fingerprints.

---

## What is a fingerprint?

Example peaks:

```text
1.20 ppm
2.50 ppm
```

Spacing:

```text
ΔδH = 1.30 ppm
```

After quantization:

```text
130 bins
```

Fingerprint:

```text
PROTON_PAIR|130
```

Converted into a stable hash:

```text
a1b4c8f3...
```

Thousands of these fingerprints are generated for every compound.

---

# Fingerprint Modes

## Geometry Only

Uses:

```text
ΔδH only
```

Recommended for:

* mixtures
* noisy spectra
* experimental peak lists

---

## Geometry + Intensity

Adds:

```text
relative intensity ratios
```

---

## Geometry + Area

Adds:

```text
integrated peak areas
```

---

## Full Relational

Uses:

```text
ΔδH
intensity ratio
area ratio
width ratio
```

Best for curated databases.

---

## Multiplet Aware

Adds:

```text
singlet
doublet
triplet
quartet
etc.
```

relationships.

---

# Search Unknown Spectrum

Upload:

```text
CSV
TSV
TXT
```

containing an unknown spectrum or mixture.

Example:

| delta_H | intensity |
| ------- | --------- |
| 0.89    | 250       |
| 1.32    | 400       |
| 2.11    | 100       |
| 3.75    | 500       |

---

Click:

```text
Search unknown against database
```

---

# Scoring

For every reference compound:

The software calculates:

### Matched Hashes

Number of shared fingerprints.

### Coverage Score

```text
matched_hashes
---------------
reference_hashes
```

### Weighted Coverage Score

Uses rarity weighting:

Rare fingerprints contribute more than common fingerprints.

This helps distinguish compounds that share common NMR motifs.

---

# Understanding the Results

Higher values indicate stronger evidence that the compound is present.

Important:

A compound does NOT need:

```text
100% peak overlap
```

to be identified.

Only a sufficient fraction of its fingerprint network must be represented.

This is especially useful for mixtures.

---

# Constellation Matching

The system displays a constellation network.

Nodes:

```text
experimental peaks
```

Edges:

```text
matched relational fingerprints
```

Example:

```text
Peak A ----- Peak B
```

means:

The spacing relationship between A and B is also present in the reference compound.

---

# How to Interpret the Constellation Plot

A line DOES NOT represent:

❌ a chemical bond

❌ J-coupling

❌ COSY connectivity

---

A line DOES represent:

✅ a matched geometric relationship

between two peaks.

The more matched relationships:

* the denser the constellation
* the stronger the evidence

---

# Recommended Parameters

For mixtures:

```text
Fingerprint Mode:
Geometry Only

Minimum ΔδH:
0.02 ppm

Maximum ΔδH:
1.5 ppm

Maximum neighbors:
5

Minimum relative intensity:
0.02
```

---

# Typical Workflow

## Pure Compound Validation

Reference:

```text
Alanine
```

Unknown:

```text
Alanine
```

Expected:

```text
Coverage ≈ 1.0
```

---

## Drift Test

Apply:

```text
±0.02 ppm
```

random shift.

Expected:

```text
High coverage retained
```

---

## Artificial Mixture

Combine:

```text
Alanine
Glucose
Valine
```

into one peak list.

Expected:

All three compounds appear among top hits.

---

## Real Extract

Upload peak list from:

* plant extract
* microbial extract
* fraction

and evaluate ranked candidates.

---

# Example Database Files

Reference database:

```text
hmdb_1h_peak_database_standardized.csv
```

Structure mapping:

```text
hmdb_structures_mapping.csv
```

Generated fingerprint database:

```text
proton_nmr_relational_hash_database.csv
```

---

# Current Limitations

Current MVP uses:

```text
peak tables only
```

and does not yet process:

* FID files
* JCAMP
* Bruker raw data
* full spectral matrices

---

# Future Development

Planned improvements:

* Direct BMRB NMR-STAR parser
* HMDB integration
* HSQC fingerprints
* COSY fingerprints
* Simulated drift testing
* Artificial mixture generator
* Machine-learning scoring
* Network-based compound decomposition
* Interactive structure viewer

---

# Citation

If you use this software in research, please cite:

Ricardo M. Borges

Institute of Natural Products Research (IPPN)

Federal University of Rio de Janeiro (UFRJ)

Brazil

---

# License

MIT License

---

# Contact

Ricardo M. Borges

IPPN-UFRJ

Rio de Janeiro, Brazil

For collaborations, bug reports, and feature requests, please open an issue on GitHub.
