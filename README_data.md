# Data — Knowledge Base Documents

The chatbot's knowledge base was built from **6 official documents** provided by the SCU Finance Department. These files are not included in this repository as they contain internal advising materials.

To reproduce this project, you will need to obtain equivalent documents from your institution's finance or advising department and place them in the Google Drive folder specified in the notebook (`MyDrive/FinChatBot/`).

## Documents Used

| File | Format | Contents |
|---|---|---|
| Finance Requirements List | PDF | Full degree requirement checklist for Finance majors (cohort 6/1/2025 and later) |
| Real Estate Minor Check Sheet | Excel (.xlsx) | Course requirements and credit tracking for the RE minor |
| FAQ Advising Questions | Google Doc (exported) | Frequently asked student advising questions with official answers |
| Finance Prerequisites Flowchart | PDF | Visual course sequencing guide for the Finance program |
| 2025–2026 Tentative Finance Course Offerings | Excel (.xlsx) | Upcoming course availability by quarter |
| Webpages for Finance Bot | Google Sheet | Curated list of SCU Finance-related web pages used for web search grounding |

## Supported File Formats

The chatbot's ingestion pipeline can process any combination of:
- `.pdf` — parsed via PyPDFLoader
- `.docx` / `.doc` — parsed via UnstructuredWordDocumentLoader
- `.xlsx` / `.xls` — parsed via UnstructuredExcelLoader

To update the knowledge base, add or remove files from the Google Drive folder and use the **Re-Index Knowledge Base** button in the sidebar.
