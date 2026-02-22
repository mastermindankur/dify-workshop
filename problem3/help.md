# Help Guide: Meeting Minutes PDF Generator (Problem 3)

Welcome! This guide walks you through building a Dify **Workflow** that:
1. Takes a meeting transcription as input
2. Generates structured MoM in Markdown using an LLM
3. Converts the Markdown to a PDF using an HTTP tool
4. Returns a download link to the user

---

## Architecture Overview

```
[Start Node: paragraph variable — meeting_transcript]
        ↓
[LLM Node: Generate MoM in Markdown]
        ↓ (output variable: mom_markdown)
[HTTP Request Node: Convert Markdown → PDF via API]
        ↓ (response: PDF download URL)
[End Node: Return download link to user]
```

---

## Step 1: Create a Workflow App

1. In Dify, go to **Studio** → **Create from Blank** → select **Workflow**.
2. Name it: `Meeting Minutes PDF Generator`.

> 💡 Use **Workflow** (not Chat App or Agent) — this gives you a clean visual pipeline with connected nodes.

---

## Step 2: Configure the Start Node (Input Variable)

1. Click on the **Start** node.
2. Add a new input field:
   - **Variable Name:** `meeting_transcript`
   - **Type:** Paragraph
   - **Label:** Meeting Transcription
3. Save.

This is the text your users will paste — the raw meeting transcription.

---

## Step 3: Add an LLM Node — MoM Generator

1. Add an **LLM** node after the Start node.
2. Connect Start → LLM.
3. Select your model (e.g., `GPT-4o`).
4. Set **Temperature** to `0.2` (for consistent, structured output).
5. In the **System Prompt**, paste:

```
You are a professional meeting secretary.
Your task is to generate structured Minutes of Meeting (MoM) in Markdown format.

STRICT RULES:
- Only use information present in the provided transcription.
- Do not add, infer, or fabricate any details.
- If a due date is not mentioned for an action item, write "End of Week".

OUTPUT FORMAT — respond only with valid Markdown, no extra text:

## Meeting Summary
[2-3 sentence summary of the meeting]

## Key Discussion Points
- [point 1]
- [point 2]

## Action Items
| Task | Owner | Due Date |
|------|-------|----------|
| [task] | [owner] | [due date] |
```

6. In the **User Prompt**, reference the input variable:

```
Here is the meeting transcription:
{{meeting_transcript}}
```

---

## Step 4: Add an HTTP Request Node — Markdown to PDF

1. Add an **HTTP Request** node after the LLM node.
2. Connect LLM → HTTP Request.
3. Configure as follows:

| Field | Value |
|---|---|
| Method | `POST` |
| URL | `https://md-to-pdf.fly.dev` |
| Content-Type Header | `application/json` |
| Body | `{ "markdown": "{{#llm_node.text#}}" }` |

> 💡 `{{#llm_node.text#}}` is how you reference the LLM output in Dify. Replace `llm_node` with whatever your LLM node is named in the workflow.

4. The API will return a direct PDF file URL in the response.

---

## Step 5: Configure the End Node

1. Click the **End** node.
2. Set the output to display the HTTP response (the PDF URL).
3. You can format it as:

```
Your Minutes of Meeting PDF is ready!
Download here: {{http_node.body}}
```

---

## Step 6: Test the Workflow

Click **Run** and paste this sample transcription:

```
Ankur opened the meeting at 10 AM. The team discussed the Q1 product roadmap.
Priya mentioned the backend API is delayed by 2 days.
Rahul agreed to fix the payment gateway bug by Friday.
Sneha will share updated Figma files by Thursday.
Ankur will send the board update email by end of day today.
Meeting closed at 10:45 AM.
```

### ✅ What you should see:
| Step | Expected Result |
|---|---|
| Start | Transcription accepted as paragraph input |
| LLM Node | MoM generated in clean Markdown format |
| HTTP Node | PDF URL returned from the API |
| End Node | User sees a download link |

---

## Prompt Used in LLM Node (Quick Reference)

**System Prompt:**
```
You are a professional meeting secretary.
Your task is to generate structured Minutes of Meeting (MoM) in Markdown format.

STRICT RULES:
- Only use information present in the provided transcription.
- Do not add, infer, or fabricate any details.
- If a due date is not mentioned for an action item, write "End of Week".

OUTPUT FORMAT — respond only with valid Markdown, no extra text:

## Meeting Summary
[2-3 sentence summary of the meeting]

## Key Discussion Points
- [point 1]
- [point 2]

## Action Items
| Task | Owner | Due Date |
|------|-------|----------|
| [task] | [owner] | [due date] |
```

**User Prompt:**
```
Here is the meeting transcription:
{{meeting_transcript}}
```

---

## Evaluation Checklist
- ✅ Workflow App created with a paragraph input variable (`meeting_transcript`)
- ✅ LLM Node generates clean MoM in Markdown with summary, discussion points, and action items table
- ✅ HTTP Request Node calls the Markdown-to-PDF API successfully
- ✅ End Node returns a working PDF download link
- ✅ Action items follow the Task | Owner | Due Date format
- ✅ No hallucinated data — all content comes from the transcription
