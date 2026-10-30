# HDFC Statement PDF to Excel Extractor
Extract transaction records from supported HDFC Bank account statements and HDFC credit card statement PDFs, then export them to structured Excel workbooks using Python.

The project focuses on transaction data: dates, descriptions, debits, credits, and other fields available in each supported layout. It does not attempt to reproduce an entire statement in Excel. Some credit card workflows also create statement summaries or include selected metadata.

#### HDFC Statement PDF to Excel Extractor is a Python project that converts transaction data from supported HDFC Bank account statements and HDFC credit card statement PDFs into structured Excel workbooks. It reduces the need to manually copy transaction rows from PDFs and prepares the extracted data for filtering, reconciliation, expense analysis, and reporting.

The notebook includes separate workflows for bank account statements, HDFC Pixel credit card statements, newer credit card statements containing a Domestic Transactions section, and credit card year-end statements. Depending on the workflow, it uses PDF text extraction, table detection, regular expressions, text coordinates, font colors, or running-balance changes to identify transactions and separate debits from credits.

Features vary by statement format and include wrapped-description handling, date normalization, month and year fields, transaction-level reward points, multi-file credit card processing, and Excel summaries. The year-end workflow combines multiple extraction methods and provides a formatted workbook with filters, frozen headers, and summary formulas. Extraction results should be checked against the original PDF because layout changes and parsing heuristics can affect completeness and classification.

| Workflow | Layout cues expected by the parser | Input handling | Excel output |
| --- | --- | --- | --- |
| Bank account statement | Transaction date and value date in `DD/MM/YY`; amount and closing balance | One configured PDF path | `HDFC_Transactions_Formatted.xlsx` |
| HDFC Pixel credit card | Date/time such as `05 Oct 2026, 14:30`; rupee amounts; optional leading `+` for credits | Multiple PDFs through Colab upload | `hdfc_pixel_multi_output.xlsx` |
| Newer credit card statement | `Domestic Transactions` section; date/time such as `05/10/2026\|14:30`; amount text and color | Multiple PDFs through Colab upload | `CreditCard_Statements_Output.xlsx` |
| Credit card year-end statement | Transaction date such as `05-Oct-2026`; amount followed by `DR` or `CR` | First file selected in the upload prompt | `<input_filename>_Transactions.xlsx` |
