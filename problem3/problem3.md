# DIFY WORKSHOP – PROBLEM 3: MEETING TRANSCRIPTION AGENT

## Problem Statement
Build a **Meeting Minutes Agent** using Dify. The agent should accept daily meeting transcriptions from users, intelligently extract structured minutes and action items, and automatically publish them to a Confluence page.

## User Input
- A daily meeting transcription (raw, unformatted text, pasted by the user)
- The meeting date

## System Requirements
- Extract **meeting summary** (2–3 sentence overview of what was discussed)
- Extract a structured **Minutes of Meeting (MoM)** with key discussion points
- Extract all **Action Items** including:
  - Task description
  - Owner (person responsible)
  - Due date (if mentioned)
- Use the **Confluence tool** to automatically publish the output as a new Confluence page
- The page title should follow the format: `MoM – [Meeting Date]`

## Suggested Dify Agent Setup
1. **Agent App** (not Chat App) with ReAct reasoning enabled
2. **LLM**: Use GPT-4o or equivalent vision/text model
3. **Tools to Enable**:
   - Confluence (built-in Dify tool) — for publishing the final page
4. **System Prompt**: Instruct the agent to follow a strict structured extraction format before calling the Confluence tool

## Expected Output (Published to Confluence)
```
Page Title: MoM – [Date]

## Meeting Summary
[2-3 sentence summary]

## Minutes of Meeting
- [Discussion point 1]
- [Discussion point 2]

## Action Items
| Task | Owner | Due Date |
|------|-------|----------|
| ... | ... | ... |
```

## Bonus Challenge (Optional)
- If no due date is mentioned for an action item, the agent should default to "End of Week" or ask the user for clarification before publishing.
- Add a second Confluence tool call to also update a "Master Action Items" tracker page.

## Evaluation Criteria
- Correct and complete extraction of minutes and action items
- Proper use of the Confluence tool (page successfully published)
- Clean formatting on the Confluence page
- Correct page title convention (`MoM – [Date]`)
- Agent reasoning steps are visible and logical
