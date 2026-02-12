# Encoding Reparative Description

This repository began with code related to the "Encoding Reparative Description" project, which began in 2023 with a team of researchers at the [University of Michigan School of Information](https://www.si.umich.edu/), the [Bentley Historical Library](https://bentley.umich.edu/), and the University of Michigan's [Humanities Collaboratory](https://sites.lsa.umich.edu/collaboratory/). Over time, it was updated to work directly with the ArchivesSpace API rather than EADs to create a Key Word in Contect (KWIC) report and associated visulalizations to support management of repartive descrition efforts at scale.

As of November 2025, we are cleaning up our repository and rewriting the code. Current functionality includes: 

1. With a Python notebook, use an optional list of relevant ArchivesSpace resources to parse finding aid "front matter" to create a "parsed results" dataset.
2. With a Python notebook, look in parsed results for terms--provided separately--to create a "matched results" dataset.
3. With R, create a KWIC report.

## Usage

### 1. Parse Results

Run the "parse_resources.ipynb" Python notebook first. It takes an optional input of Resource IDs, described below, along with a configuration file described in the notebook, and uses them to parse ArchivesSpace via the API. It produces parsed results (a CSV file) used in later operations, and puts them in a "data" folder--see "parsed_results_*_nativeAmerican.csv" and "parsed_results_*_phillipines.csv" as examples.

**Filename:** parse_resource.ipynb
**Input:** Optionally, you may also provide a text file with relevant ArchivesSpace Resouce IDs, one per line, e.g.:

```
229
8482
4460
256
8480
...
```

If the user does not provide a text file with a list of Resource IDs, the tool will parse ALL ArchivesSpace resources. Since this is so resource intensive, the tool defaults to expecting a text file.

**Output:** A "parsed_results" CSV file in the "data" folder used in "match_terms.ipynb."

### 2. Match Terms

Run the "match_terms.ipynb" Python notebook next. It takes a "parsed_results" CSV file and a list of terms to look for matches in ArchivesSpace data. It produces matched results (a CSV file) used in later operations, and puts them in the same "data" folder--see "matched_results_*_nativeAmerican.csv" and "matched_results_*_phillipines.csv" as examples.

**Filename:** parse_resource.ipynb
**Input:** This script takes as an input the output of "parse_results.ipynb" above. The required term list must be a `.txt` file with one term per line, e.g.:

```
Civilization
Civilized
Uncivilized
Burial
Burials
...
```

See "terms-nativeAmerican.txt," "terms-phillipines.txt," and "terms-MENA.txt" as examples.

**Output:** A "matched_results" CSV file, also in the "data" folder, used to create a KWIC report.

### 3. KWIC Report

A third component of the tool creates a Key Word In Context ([KWIC](https://en.wikipedia.org/wiki/Key_Word_in_Context)) report. The KWIC approach was developed in literary analysis and derived out of the creation of a concordance, a list of every occurrence of a given word or words in a text and facilitates the comparative analysis of usage in different contexts. The technique gained particular usage in early computer processing of text, such as the concordance of the works of Thomas Aquinas, produced in the 1940s and 50s (see [Jacob 2021](https://www.jstor.org/stable/j.ctv1r7878x.7)). A concurrent product of early computer text processing, the KWIC index was developed by Hans Peter Luhn and provided inspiration for our reparative description tools.

The KWIC report uses computational search tools to identify occurrences of specific words, then displays the matches in vertical alignment along with a selected number of words or phrases that come before or after each occurrence. This type of report has been used in fields well beyond literary or hermeneutic analyses. As Kenneth Janda ([2003](https://www.janda.org/workshop/content%20analysis/kwic.htm)) points out, a KWIC display is most useful for "information retrieval rather than content analysis." Our work, however, suggests that KWIC may be equally useful as a tool for initial retrieval, but subsequent analysis and, in this case, a reparative ethic.

Although in a production tool, this code should be developed in the same language as the other tools, we found it quickest to produce the KWIC report using extant software libraries, which in this case were available in the R programming language. Thus, using the RStudio suite and assistance from Claude Code, we created a KWIC report using the [dplyr](https://dplyr.tidyverse.org/) and [quanteda](https://quanteda.io/) pakcages. This step uses a function named create_kwic_analysis(), which takes as input the CSV output of the Match Terms step (see above). The function reads in the matched terms CSV file, splits apart the matches using the matched terms, then adds in source information (finding aid name and collection name), and creates the KWIC report. With additional instructions, the function can sort the report in various ways, including by matched term, EAD tag, or collection. The function can also be requested to output “stats,” which list the total number of matches from the term list words for each term, matches per collection, and matches per EAD tag.
