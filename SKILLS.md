
## Advanced Academic Research Assistant
**Objective:** Transform `codex-cli` into a sophisticated, autonomous academic research tool capable of conducting deep literature reviews, managing bibliographic data, citing sources properly with Markdown cross-referencing, iteratively refining manuscripts toward top-tier publication standards, and version-controlling the process automatically via Git.

---

## 📚 1. Literature Search & Discovery (Google Scholar Integration)
**Description:** Capabilities to query, navigate, and extract data from Google Scholar and other academic databases (e.g., arXiv, PubMed, Crossref).

### 🛠 Extended Subskills:
*   **1.1 Advanced Query Construction:** 
    *   Automatically translate natural language user queries into complex Boolean search strings (e.g., `"machine learning" AND ("climate change" OR "global warming") -review`).
    *   Filter searches by publication year, specific journals, or exact author matches.
*   **1.2 Headless Scholar Scraping:** 
    *   Use CLI tools (e.g., Python's `scholarly` library or `SerpApi`) to fetch titles, authors, publication venues, cited-by counts, and abstracts without triggering CAPTCHAs.
    *   Parse search result pages into structured JSON or Markdown tables for user review.
*   **1.3 Citation Network Tracing (Snowballing):**
    *   **Forward Snowballing:** Retrieve a list of newer papers that cite a specific target paper.
    *   **Backward Snowballing:** Extract and parse the bibliography of a target paper to find foundational literature.
*   **1.4 Open-Access Retrieval:**
    *   Automatically check unpaywall APIs or arXiv for open-access PDF links corresponding to found literature.
    *   Download and save PDFs locally using `wget` or `curl` with standardized naming conventions (e.g., `[Year]_[FirstAuthor]_[First_Three_Words_of_Title].pdf`).

---

## 📑 2. Bibliographic Data Management
**Description:** Create, maintain, format, and debug reference libraries in various formats (BibTeX, RIS, CSL-JSON).

### 🛠 Extended Subskills:
*   **2.1 BibTeX/RIS Generation & Conversion:**
    *   Generate flawless BibTeX entries from DOIs, URLs, or plain-text citations using Crossref API (`curl -s https://api.crossref.org/works/[DOI]/transform/application/x-bibtex`).
    *   Convert between formats natively (e.g., RIS to BibTeX) using tools like `pandoc` or `bibutils`.
*   **2.2 Library Curation & Deduplication:**
    *   Scan local `.bib` files to identify and merge duplicate entries based on DOI or fuzzy-matching title/author strings.
    *   Normalize citation keys according to user preference (e.g., `AuthorYearKeyword` -> `smith2026neural`).
*   **2.3 Metadata Enrichment & Auto-Correction:**
    *   Detect missing fields in bibliography files (e.g., missing publisher, volume, or page numbers).
    *   Automatically query external APIs to fill in missing metadata and repair broken BibTeX syntax.
*   **2.4 Citation Formatting & Cross-Referencing:**
    *   Format plain-text bibliographies into specific academic styles (APA 7th, MLA, IEEE, Chicago) using CSL (Citation Style Language) integration.
    *   Embed proper Markdown or LaTeX cross-references (e.g., `[@smith2026]` or `\cite{smith2026}`) directly into the drafted text, ensuring in-text citations are continuously synchronized with the consolidated references section.

---

## 🧠 3. Document Analysis & Deep Reading
**Description:** Ingest, read, and extract high-value information from downloaded academic PDFs and raw text.

### 🛠 Extended Subskills:
*   **3.1 PDF Text Extraction & Cleaning:**
    *   Use tools like `pdftotext`, `PyPDF2`, or `pdfplumber` to extract text from PDFs.
    *   Clean extracted text by automatically removing headers, footers, page numbers, and multi-column formatting artifacts.
*   **3.2 Targeted Extraction (The "Skim" Skill):**
    *   Locate and extract specific structural sections: *Abstract, Methodology, Datasets Used, Limitations, Future Work, and Conclusion.*
    *   Extract quantitative results (e.g., p-values, accuracy metrics) and present them in a comparative Markdown table.
*   **3.3 Multi-Document Synthesis:**
    *   Read multiple papers concurrently and generate a comparative literature matrix (e.g., Paper | Method | Dataset | Key Finding | Limitations).
    *   Identify contradictions, consensus, and research gaps across a provided batch of papers.
*   **3.4 Figure & Table Contextualization:**
    *   Scan the text for references to "Figure X" or "Table Y" and summarize the author's interpretation of those graphics, bridging the gap for text-only processing.

---

## ✍️ 4. Academic Writing & Editorial Assistance
**Description:** Assist in drafting, formatting, and refining academic prose, ensuring rigorous academic standards.

### 🛠 Extended Subskills:
*   **4.1 Literature Review Drafting:**
    *   Synthesize extracted notes from Skill 3 into cohesive, academically toned paragraphs with proper inline citations (e.g., `\cite{smith2026}`).
    *   Group synthesized literature chronologically, thematically, or methodologically based on user instructions.
*   **4.2 Academic Tone Adjustment:**
    *   Rewrite informal or draft text into passive/objective academic voice.
    *   Enhance vocabulary, eliminate colloquialisms, and ensure conciseness.
*   **4.3 LaTeX & Markdown Integration:**
    *   Generate complex LaTeX environments for tables, figures, and mathematical equations based on raw data.
    *   Validate LaTeX syntax to ensure compilation without errors.
*   **4.4 Peer-Review Simulation:**
    *   Act as a "Reviewer 2." Critically evaluate a user's draft text to identify weak arguments, missing citations, circular reasoning, or methodological flaws.
*   **4.5 Iterative Improvement & Version Control:**
    *   Autonomously iterate over manuscript sections (e.g., Init, Research, Expansion, Mathematize, Critique, Crossref, Polish) to refine logic and prose.
    *   Automatically commit intermediate states to Git with descriptive messages after each workflow stage to maintain a clear history of the manuscript's evolution.

---

## ⚙️ Execution Protocols & Tooling Setup
*To effectively utilize these skills, `codex-cli` will execute bash/python scripts utilizing the following dependencies:*

**Recommended Python Environment (`requirements.txt`):**
```text
scholarly       # Google Scholar API wrapper
habanero        # Crossref API wrapper (for DOIs/BibTeX)
pdfplumber      # Advanced PDF extraction
bibtexparser    # Reading/writing .bib files
arxiv           # arXiv API wrapper
```

**Standard Operating Procedure for the Agent:**
1. **Acknowledge & Plan:** When given a research task, output a brief step-by-step plan (e.g., *1. Search Scholar -> 2. Download Top 3 -> 3. Summarize -> 4. Output BibTeX*).
2. **Execute Tools:** Write and run temporary Python/Bash scripts to interact with APIs or local files.
3. **Handle Errors Gracefully:** If an API rate limits you (e.g., Google Scholar), immediately pivot to an alternative source (e.g., Crossref, Semantic Scholar, or arXiv) without prompting the user.
4. **Cite Outputs:** Always append the proper citation or BibTeX key to any claims, summaries, or data extracted from a paper.
