# Help Guide: Meeting Transcription Agent in Dify (Problem 3)

Welcome! This guide walks you through building a **Meeting Minutes Agent** in Dify step by step. By the end, you will have an agent that reads a meeting transcription, extracts structured minutes and action items, and publishes them to Confluence — **automatically**.

---

## Architecture Overview

```
User pastes transcription + date
        ↓
[Agent: ReAct Reasoning Loop]
        ↓ (thinks & plans)
[LLM extracts Summary + MoM + Action Items]
        ↓ (tool call)
[Confluence Tool: creates page]
        ↓
Agent confirms with page URL
```

---

## Step 1: Create a New Agent App

1. Log into **Dify** and go to the **Studio** tab.
2. Click **Create from Blank** → select **Agent**.
3. Name it: `Meeting Minutes Agent`.
4. Under **Model**, choose `GPT-4o` or equivalent.
5. Make sure the **Agent Mode** is set to **Function Calling** or **ReAct** — this enables the reasoning loop where the agent decides *when* and *how* to call the Confluence tool.

> 💡 **What is ReAct?** ReAct stands for Reason + Act. The agent first *reasons* about what to do, then *acts* by calling a tool. You can observe this thinking process live in the Dify UI - this is the key feature to showcase!

---

## Step 2: Enable and Configure the Confluence Tool

1. In your Agent app, scroll to the **Tools** section → click **Add Tool**.
2. Search for `Confluence` and click to enable it.
3. Fill in the credentials:

| Field | Value |
|---|---|
| Base URL | `https://yourworkspace.atlassian.net/wiki` |
| Username | Your Atlassian account email |
| API Token | Generate at: `https://id.atlassian.com/manage-profile/security/api-tokens` |

4. Click **Save**.

> ⚠️ **Ask your instructor** for the **Space Key** of the Confluence space you should publish to. You'll need it in the system prompt below.

The Confluence tool exposes the following action that your agent will use:
- **Create Page** — requires: Space Key, Page Title, and Body content

---

## Step 3: Write the System Prompt

This is the most important step. The system prompt tells the agent exactly how to behave. Copy this **exactly** into the **Instructions** field:

```
You are a professional AI Meeting Secretary.

Your job is to process raw meeting transcriptions and publish structured meeting minutes to Confluence.

When the user provides a meeting transcription and date, follow these steps IN ORDER:

STEP 1 - EXTRACT MEETING SUMMARY:
Write a 2-3 sentence summary of the overall meeting. Cover: main topics discussed, key decisions made.

STEP 2 - EXTRACT MINUTES OF MEETING:
List all key discussion points as bullet points. Be concise but complete. Include any decisions made.

STEP 3 - EXTRACT ACTION ITEMS:
Identify every task, who is responsible, and when it is due.
Format as a Markdown table with columns: Task | Owner | Due Date
Rule: If no due date is mentioned for a task, default to "End of Week".

STEP 4 - COMPILE THE FULL PAGE BODY:
Combine Steps 1, 2, and 3 into a single Markdown document using this exact structure:

## Meeting Summary
[your summary here]

## Minutes of Meeting
[bullet points here]

## Action Items
| Task | Owner | Due Date |
|------|-------|----------|
[rows here]

STEP 5 - PUBLISH TO CONFLUENCE:
Use the Confluence tool to create a new page with:
- Space Key: [YOUR_SPACE_KEY]
- Title: "MoM - [date provided by the user]"
- Body: The full Markdown document from Step 4

STEP 6 - CONFIRM:
Tell the user the page was published successfully and share the direct link.

IMPORTANT RULES:
- Never skip the Confluence tool call.
- Never ask the user for clarification before publishing — use defaults where needed.
- Always follow the exact Markdown structure above.
```

> ⚠️ Replace `[YOUR_SPACE_KEY]` with the Space Key given by your instructor before saving.

---

## Step 4: Set LLM Parameters

In the model config (right panel), adjust:

| Parameter | Recommended Value | Reason |
|---|---|---|
| Temperature | `0.2` | Low temperature = more consistent, structured extraction |
| Max Tokens | `2000` | Enough room for minutes + action items table |

---

## Step 5: Test the Agent

Click **Preview** (right side of the screen) and paste the following test input exactly:

---

**Your message to the agent:**

```
Date: 22nd February 2026

Transcription:
Ankur opened the meeting at 10 AM. The team discussed the Q1 product roadmap.
Priya mentioned that the backend API for user authentication is delayed by 2 days.
Rahul agreed to fix the bug in the payment gateway by Friday.
The team discussed moving the design review to next Monday.
Sneha will share the updated Figma files by Thursday.
Ankur will send the board update email by end of day today.
Meeting closed at 10:45 AM.
```

---

### ✅ What you should observe in the Dify UI:

| Step | What happens |
|---|---|
| 1 | Agent shows **reasoning steps** (Thought: I need to extract...) |
| 2 | Agent makes a **Confluence tool call** (visible in the UI) |
| 3 | Agent replies confirming the **page was published + URL** |

---

## Step 6: Verify on Confluence

Open your Confluence workspace and navigate to the target space. You should see:

- **Page title:** `MoM – 22nd February 2026`
- **Page content:** Properly formatted as shown below

### Expected Confluence Page Output:

```markdown
## Meeting Summary
The team reviewed the Q1 roadmap and identified a 2-day delay in backend authentication API. 
Key decisions were made around the design review timeline and board communication.

## Minutes of Meeting
- Q1 product roadmap was reviewed
- Backend API for user authentication is delayed by 2 days (raised by Priya)
- Design review rescheduled to next Monday
- Board update communication to be sent today

## Action Items
| Task | Owner | Due Date |
|------|-------|----------|
| Fix bug in payment gateway | Rahul | Friday |
| Share updated Figma files | Sneha | Thursday |
| Send board update email | Ankur | End of Day Today |
```

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Confluence tool call fails | Double-check your Base URL, Username, and API Token in tool settings |
| Agent doesn't call the tool | Ensure the system prompt ends with the IMPORTANT RULES section |
| Action items missing owners | The agent infers owners from the transcript — make sure names are mentioned clearly |
| Page body is empty | Check that your API Token has Confluence **write** permissions |

---

## Evaluation Checklist

- ✅ Agent App created with ReAct/Function Calling mode enabled
- ✅ Confluence tool configured correctly with valid credentials
- ✅ System prompt follows the 6-step structure
- ✅ Agent reasoning steps are visible in the Dify UI during execution
- ✅ Confluence page is published with the correct title (`MoM – [Date]`)
- ✅ Page contains: Meeting Summary, Minutes of Meeting, Action Items table
- ✅ All action items have a due date (defaulting to "End of Week" if not mentioned)
