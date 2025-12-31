# **Automated OCR Optimization Pipeline**

A programmatic solution for cleaning and optimizing raw Markdown text generated via Optical Character Recognition (OCR) from *Mythic Bastionland*. This tool utilizes a custom Python script to systematically identify artifacts, correct systemic typos, and standardize formatting.

## **📖 Table of Contents**

* [Overview](https://www.google.com/search?q=%23-overview)  
* [Problem Definition](https://www.google.com/search?q=%23-problem-definition)  
* [Technical Implementation](https://www.google.com/search?q=%23-technical-implementation)  
* [Project Structure](https://www.google.com/search?q=%23-project-structure)  
* [Getting Started](https://www.google.com/search?q=%23-getting-started)  
* [Usage](https://www.google.com/search?q=%23-usage)

## **🔍 Overview**

This repository contains a linear processing pipeline designed to transform raw, artifact-heavy OCR output into production-ready Markdown. The core logic handles regex-based artifact stripping, dictionary-based entity correction, and structural reformatting.

## **🚩 Problem Definition**

The raw output from the OCR engine presented specific data quality issues rendering the text unsuitable for use:

| Issue Category | Description |
| :---- | :---- |
| **Navigation Artifacts** | Embedded internal PDF anchor links (e.g., \[p16\](\#page-15-0)) and HTML tags (\<span\>). |
| **Greedy Match Risks** | Standard regex patterns risked deleting valid text situated between artifacts. |
| **OCR Noise** | Fragmented headers (\# 1 5), isolated page numbers, and non-ASCII characters. |
| **Entity Typos** | Recurring misinterpretations of specific fonts (e.g., "Brue Knight" instead of "True Knight"). |
| **Formatting Decay** | Loss of Markdown blockquotes for stat blocks and misalignment in "Spark Tables." |

## **⚙️ Technical Implementation**

### **Architecture**

The pipeline consists of two distinct phases: **Extraction** and **Sanitization**.

1. **Ingestion (Ghostscript):** The original file Mythic-Bastionland-Feb-2025.pdf is read and rasterized into high-resolution page images.  
2. **OCR Transformation:** External OCR engines convert the rasterized images into raw Markdown.  
3. **Sanitization (Python):** The script clean\_mythic\_bastionland.py ingests the raw Markdown and performs the following:  
   * **Artifact Removal:** Executes pattern-matching substitutions.  
   * **Typo Correction:** Iterates through a dictionary of known misinterpretations.  
   * **Formatting Standardization:** Re-applies Markdown syntax to specific data structures.  
4. **Reporting:** Outputs a statistical summary of the cleaning process.

### **Data Extraction Strategy (Ghostscript)**

Before the Python script runs, Ghostscript is used to read the source PDF and generate the input for the OCR engine. This step ensures high-fidelity text recognition by rendering vector text at 300 DPI.  
**Command Used:**  
gs \-dBATCH \-dNOPAUSE \-sDEVICE=png16m \-r300 \-sOutputFile=page\_%03d.png Mythic-Bastionland-Feb-2025.pdf

### **Key Optimizations (Python)**

#### **Non-Greedy Regex Matching**

To prevent data loss when removing internal links like \[p10\](\#page-9-0):

* **Risk:** Greedy patterns (\\\[.\*\\\]) consume valid text between brackets.  
* **Solution:** Negated Character Classes (\[^\\\]\]\*) enforce boundaries.  
  * *Pattern:* \\\[\[^\\\]\]\*p\\d+\[^\\\]\]\*\\\]\\(\#page-\\d+-\\d+\\)

#### **Targeted Artifact Removal**

* **Image Tags:** Uses non-capturing groups (?:...) to handle both \_Picture\_ and \_Figure\_ naming conventions.  
* **Fragmented Headers:** Utilizes the multiline flag (?m) to identify and remove lines containing *only* digits, spaces, or hashes.

## **📂 Project Structure**

.  
├── clean\_mythic\_bastionland.py    \# Main processing script (v3)  
├── Mythic-Bastionland-Feb-2025.pdf \# Source: Original PDF (Read via Ghostscript)  
├── Mythic-Bastionland-Feb-2025.md \# Input: Raw OCR source file  
├── Mythic-Bastionland-Cleaned.md  \# Output: Cleaned Markdown  
└── README.md                      \# Documentation

## **🚀 Getting Started**

### **Prerequisites**

* **Python 3.6+** (Standard re and os libraries).  
* **Ghostscript** (For initial PDF processing).

### **Installation**

1. Clone the repository:  
   git clone \[https://github.com/yourusername/ocr-optimization.git\](https://github.com/yourusername/ocr-optimization.git)

2. Navigate to the directory:  
   cd ocr-optimization

## **💻 Usage**

### **Phase 1: Extraction**

Process the PDF using Ghostscript (if starting from source):  
gs \-dBATCH \-dNOPAUSE \-sDEVICE=png16m \-r300 \-sOutputFile=page\_%03d.png Mythic-Bastionland-Feb-2025.pdf  
\# (Run OCR tool on generated images to produce Mythic-Bastionland-Feb-2025.md)

### **Phase 2: Cleaning**

1. Ensure Mythic-Bastionland-Feb-2025.md is in the root directory.  
2. Run the cleaning script:  
   python clean\_mythic\_bastionland.py

3. Review the console output:  
   \------------------------------  
   Cleaning Complete.  
   \------------------------------  
   Artifacts Removed:   142  
   Typos Corrected:     35  
   Formatting Updates:  15  
   Output saved to:     Mythic-Bastionland-Cleaned.md  
   \------------------------------

## **🤝 Contributing**

1. Fork the repository.  
2. Create a feature branch (git checkout \-b feature/NewRegex).  
3. Commit your changes (git commit \-m 'Add regex for footer removal').  
4. Push to the branch (git push origin feature/NewRegex).  
5. Open a Pull Request.

## **📄 License**

Distributed under the MIT License. See LICENSE for more information.