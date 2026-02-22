# Guide: Creating the Legal Advisor Bot in Dify

Welcome to Problem 2! This guide will walk you through setting up a Retrieval-Augmented Generation (RAG) Legal Advisor Bot using **Dify**. The goal is to create a bot that strictly answers labor law questions based *only* on the provided PDFs.

Follow these step-by-step instructions to complete the problem:

## Step 1: Create Your Knowledge Base
First, we need to give our bot the legal documents it will read from.
1. In Dify, navigate to the **Knowledge** tab from the top navigation bar.
2. Click on **Create Knowledge**.
3. Choose the **Upload text file** option.
4. Upload all 4 PDF document files provided to you in the `problem2/` directory (excluding `problem2.pdf`).
5. Click **Next** to proceed to the configuration step.

## Step 2: Configure Retrieval & Chunking Settings
To ensure the LLM gets the most relevant sections of the laws, we must configure how Dify processes and searches these documents.
1. **Chunk Settings:** Keep the chunk size between **500 to 1000 tokens**. This provides enough context for legal clauses without overwhelming the model.
2. **Retrieval Setting:** Choose **Vector Search** (or Hybrid Search if available/preferred).
3. **Top-K:** Set the Top-K value to **4**. This setting controls the number of *chunks* retrieved, not the number of *documents*. When a user asks a question, Dify searches through all the 500-1000 token pieces you created in Step 2, and retrieves the 4 most relevant chunks regardless of which document they came from. It does not retrieve all documents every time.
4. Click **Save & Process** and wait for Dify to finish embedding the documents.

## Step 3: Create the Agent
Now, let's create the conversational interface.
1. Go to the **Studio** tab and click **Create from Blank**.
2. Select **Agent** as the application type.
3. Give your agent a name (e.g., "Legal Advisor Bot 2026").
4. In the agent configuration screen, find the **Context** (or Knowledge) section and click **Add**. Select the Knowledge Base you just created in Step 1.

## Step 4: Write a Strict System Prompt
This is the most critical step to prevent the bot from hallucinating (making things up) or answering general questions outside the provided laws.

In the **Instructions / System Prompt** box, copy and paste the following:

```text
You are an expert Indian Labour Law Legal Advisor. 
Your sole purpose is to answer user questions STRICTLY based on the provided context documents (Indian Labour Codes and 2026 amendments).

RULES:
1. You MUST answer ONLY using the retrieved context. 
2. You MUST cite the specific Act name and Section number in every answer.
3. You MUST explain the legal language in plain, easy-to-understand English.
4. If the answer to the user's question is NOT found in the provided context, you MUST reply exactly with: "Outside scope of provided laws."
5. DO NOT hallucinate, guess, or use outside knowledge.
```

## Step 5: Adjust LLM Parameters
To ensure the bot's answers are deterministic and strictly factual:
1. Locate the **Parameters** section (usually under the selected LLM model details).
2. Set the **Temperature** to a very low value, such as **0.1**. 
   > *Note: A low temperature makes the model's responses more focused and less creative, which is exactly what we want for factual legal answers. You can try changing it to 0.7 later to see how it might negatively affect the strictness of the adherence to the text.*

## Step 6: Test Your Application
Your bot is ready! Use the "Preview" chat window to run the test cases. 

**Valid Queries (Should return cited answers):**
- *What changes were introduced in the Industrial Relations Code Amendment Act, 2026?*
- *Under the Code on Wages, when must employers pay wages?*

**Out-of-Scope Query (Should return the exact refusal message):**
- *What is the punishment for murder?*

If it successfully refuses the murder question and correctly cites the others, you have successfully completed Problem 2! Publish your app.
