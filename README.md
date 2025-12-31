Automated OCR Optimization Pipeline

A programmatic solution for cleaning and optimizing raw Markdown text generated via Optical Character Recognition (OCR) from Mythic Bastionland. This tool utilizes a custom Python script to systematically identify artifacts, correct systemic typos, and standardize formatting.

📖 Table of Contents

Overview

Problem Definition

Technical Implementation

Project Structure

Getting Started

Usage

🔍 Overview

This repository contains a linear processing pipeline designed to transform raw, artifact-heavy OCR output into production-ready Markdown. The core logic handles regex-based artifact stripping, dictionary-based entity correction, and structural reformatting.

🚩 Problem Definition

The raw output from the OCR engine presented specific data quality issues rendering the text unsuitable for use:

Issue Category

Description

Navigation Artifacts

Embedded internal PDF anchor links (e.g., [p16](#page-15-0)) and HTML tags (<span>).

Greedy Match Risks

Standard regex patterns risked deleting valid text situated between artifacts.

OCR Noise

Fragmented headers (# 1 5), isolated page numbers, and non-ASCII characters.

Entity Typos

Recurring misinterpretations of specific fonts (e.g., "Brue Knight" instead of "True Knight").

Formatting Decay

Loss of Markdown blockquotes for stat blocks and misalignment in "Spark Tables."

⚙️ Technical Implementation

Architecture

The pipeline consists of two distinct phases: Extraction and Sanitization.

Ingestion (Ghostscript): The original file Mythic-Bastionland-Feb-2025.pdf is read and rasterized into high-resolution page images.

OCR Transformation: External OCR engines convert the rasterized images into raw Markdown.

Sanitization (Python): The script clean_mythic_bastionland.py ingests the raw Markdown and performs the following:

Artifact Removal: Executes pattern-matching substitutions.

Typo Correction: Iterates through a dictionary of known misinterpretations.

Formatting Standardization: Re-applies Markdown syntax to specific data structures.

Reporting: Outputs a statistical summary of the cleaning process.

Data Extraction Strategy (Ghostscript)

Before the Python script runs, Ghostscript is used to read the source PDF and generate the input for the OCR engine. This step ensures high-fidelity text recognition by rendering vector text at 300 DPI.

Command Used:

gs -dBATCH -dNOPAUSE -sDEVICE=png16m -r300 -sOutputFile=page_%03d.png Mythic-Bastionland-Feb-2025.pdf


Key Optimizations (Python)

Non-Greedy Regex Matching

To prevent data loss when removing internal links like [p10](#page-9-0):

Risk: Greedy patterns (\[.*\]) consume valid text between brackets.

Solution: Negated Character Classes ([^\]]*) enforce boundaries.

Pattern: \[[^\]]*p\d+[^\]]*\]\(#page-\d+-\d+\)

Targeted Artifact Removal

Image Tags: Uses non-capturing groups (?:...) to handle both _Picture_ and _Figure_ naming conventions.

Fragmented Headers: Utilizes the multiline flag (?m) to identify and remove lines containing only digits, spaces, or hashes.

📂 Project Structure

.
├── clean_mythic_bastionland.py    # Main processing script (v3)
├── Mythic-Bastionland-Feb-2025.pdf # Source: Original PDF (Read via Ghostscript)
├── Mythic-Bastionland-Feb-2025.md # Input: Raw OCR source file
├── Mythic-Bastionland-Cleaned.md  # Output: Cleaned Markdown
└── README.md                      # Documentation



🚀 Getting Started

Prerequisites

Python 3.6+ (Standard re and os libraries).

Ghostscript (For initial PDF processing).

Installation

Clone the repository:

git clone [https://github.com/yourusername/ocr-optimization.git](https://github.com/yourusername/ocr-optimization.git)



Navigate to the directory:

cd ocr-optimization



💻 Usage

Phase 1: Extraction

Process the PDF using Ghostscript (if starting from source):

gs -dBATCH -dNOPAUSE -sDEVICE=png16m -r300 -sOutputFile=page_%03d.png Mythic-Bastionland-Feb-2025.pdf
# (Run OCR tool on generated images to produce Mythic-Bastionland-Feb-2025.md)


Phase 2: Cleaning

Ensure Mythic-Bastionland-Feb-2025.md is in the root directory.

Run the cleaning script:

python clean_mythic_bastionland.py



Review the console output:

------------------------------
Cleaning Complete.
------------------------------
Artifacts Removed:   142
Typos Corrected:     35
Formatting Updates:  15
Output saved to:     Mythic-Bastionland-Cleaned.md
------------------------------



🤝 Contributing

Fork the repository.

Create a feature branch (git checkout -b feature/NewRegex).

Commit your changes (git commit -m 'Add regex for footer removal').

Push to the branch (git push origin feature/NewRegex).

Open a Pull Request.

📄 License

Distributed under the MIT License. See LICENSE for more information.
