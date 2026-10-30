# HDFC Statement PDF to Excel Extractor
Extract transaction records from supported HDFC Bank account statements and HDFC credit card statement PDFs, then export them to structured Excel workbooks using Python.

The project focuses on transaction data: dates, descriptions, debits, credits, and other fields available in each supported layout. It does not attempt to reproduce an entire statement in Excel. Some credit card workflows also create statement summaries or include selected metadata.

#### HDFC Statement PDF to Excel Extractor is a Python project that converts transaction data from supported HDFC Bank account statements and HDFC credit card statement PDFs into structured Excel workbooks. It reduces the need to manually copy transaction rows from PDFs and prepares the extracted data for filtering, reconciliation, expense analysis, and reporting.

The notebook includes separate workflows for bank account statements, HDFC Pixel credit card statements, newer credit card statements containing a Domestic Transactions section, and credit card year-end statements. Depending on the workflow, it uses PDF text extraction, table detection, regular expressions, text coordinates, font colors, or running-balance changes to identify transactions and separate debits from credits.

Features vary by statement format and include wrapped-description handling, date normalization, month and year fields, transaction-level reward points, multi-file credit card processing, and Excel summaries. The year-end workflow combines multiple extraction methods and provides a formatted workbook with filters, frozen headers, and summary formulas. Extraction results should be checked against the original PDF because layout changes and parsing heuristics can affect completeness and classification.

| Workflow | Layout cues expected by the parser | Input handling | Excel output |
| --- | --- | --- | --- |
| **Bank account statement** | Transaction date and value date in `DD/MM/YY`; amount and closing balance | One configured PDF path | `HDFC_Transactions_Formatted.xlsx` |
| **HDFC Pixel credit card** | Date/time such as `05 Oct 2026, 14:30`; rupee amounts; optional leading `+` for credits | Multiple PDFs through Colab upload | `hdfc_pixel_multi_output.xlsx` |
| **Newer credit card statement** | `Domestic Transactions` section; date/time such as `05/10/2026\|14:30`; amount text and color | Multiple PDFs through Colab upload | `CreditCard_Statements_Output.xlsx` |
| **Credit card year-end statement** | Transaction date such as `05-Oct-2026`; amount followed by `DR` or `CR` | First file selected in the upload prompt | `<input_filename>_Transactions.xlsx` |


These are layout-specific parsers. An HDFC logo or card name alone does not establish compatibility. Use text-based PDFs with selectable text. Scanned-image extraction and password entry/decryption are not implemented.


# Bank account statements
* Reads PDF text with pdfplumber.
* Identifies transaction starts using date patterns and merges continuation lines within each page.
* Filters configured statement summary and footer markers.
* Separates narration, reference, value date, amount, and closing balance internally.
* Attempts to locate the opening balance in statement summary text.
* Infers debit or credit from changes in the running balance.
* Attempts to reorder consecutive transactions sharing the same date when their balance chain does not match the extracted order.
* Exports dates in DD-MM-YYYY format, adds month and year, and leaves Mode and Purpose blank for later use.

# Technology stack
| Component | Role in this notebook |
| --- | --- |
| Python | Parsing and workflow logic |
| `pdfplumber` | Bank text extraction and year-end table/text extraction |
| PyMuPDF (`pymupdf`; imported as `fitz` or `pymupdf`) | Pixel and newer credit card text, coordinates, and colors |
| `pandas` | DataFrame creation, date fields, aggregation, and Excel export |
| `openpyxl` | Excel writing and year-end workbook formatting |
| `PyPDF2` | Additional year-end text extraction strategy |
| `tabula-py` | Conditional year-end table extraction strategy; requires Java |
| `re`, `datetime` | Pattern matching and date processing |
| `google.colab.files` | Interactive uploads and selected workbook downloads |

# How extraction works
1. Read the PDF: extract text, tables, or text spans using the library selected for the layout.
2. Recognize transactions: locate supported date patterns, table rows, section headings, and amount markers.
3. Clean fields: combine supported continuation lines and remove recognized non-transaction content.
4. Separate debits and credits: use running-balance changes, an amount's leading +, green font color, or explicit DR/`CR`, depending on the parser.
5. Build records: create a pandas DataFrame and add the workflow's date fields and placeholders.
6. Export: write the appropriate Excel workbook and any workflow-specific summary.

# Contributing
For a parsing issue, include the workflow name, a description of the layout difference, the expected result, and a synthetic or redacted minimal example. Useful improvements include safer duplicate handling, explicit validation flags, consistent output schemas, cross-page transaction handling, and automated checks using synthetic statements. These are proposed improvements, not current features.
