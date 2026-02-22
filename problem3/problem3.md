# DIFY WORKSHOP – PROBLEM 3: MEETING MINUTES PDF GENERATOR

## Problem Statement
Build a **Meeting Minutes PDF Generator Agent** using Dify. The agent reads a meeting transcription provided by the user as a paragraph, generates structured Minutes of Meeting (MoM) in Markdown format, converts the Markdown to a PDF using a tool, and returns a download link to the user.

## User Input
- A meeting transcription provided as a **paragraph variable** in Dify

## System Requirements
- Accept the meeting transcription as a paragraph input variable
- Generate structured **Minutes of Meeting (MoM)** in Markdown format containing:
  - Meeting Summary (2–3 sentences)
  - Key Discussion Points (bullet list)
  - Action Items table (Task | Owner | Due Date)
- Use a **tool** (HTTP Request or Code tool) to convert the Markdown to a PDF
- Return a **download link** for the generated PDF to the user

## Suggested Dify Workflow Setup
1. **Workflow App** (not Chat App)
2. **Start Node**: Add a paragraph-type variable for the meeting transcription (e.g., `meeting_transcript`)
3. **LLM Node**: Generate MoM in Markdown format using the transcript variable
4. **HTTP Request Node or Code Node**: Call a Markdown-to-PDF conversion API and get back a file URL
5. **End Node**: Output the PDF download link to the user

## Recommended Markdown-to-PDF Tool
Use an HTTP Request node to call a free Markdown-to-PDF API:

- **API:** `https://md-to-pdf.fly.dev`
- **Method:** POST
- **Body:** `{ "markdown": "{{llm_output}}" }`
- The API returns a PDF file — use the response URL as the download link

## Expected Output to User
```
Your Minutes of Meeting PDF is ready!
Download here: [PDF link]
```

## Bonus Challenge (Optional)
- Add the meeting date as a second input variable and include it in the PDF filename
- Send the PDF link via email using an HTTP tool

## Evaluation Criteria
- Correct use of Dify **Workflow** with a paragraph input variable
- LLM generates clean, properly structured MoM in Markdown
- HTTP/Code tool successfully converts Markdown to PDF
- User receives a working download link
- Action items are in a table with Task, Owner, Due Date columns
