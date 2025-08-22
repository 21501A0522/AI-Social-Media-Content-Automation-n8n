# 🤖 AI Social Media Content Automation (n8n)

Automate social posts from article links.  
You add a link in Google Sheets → n8n summarizes it with OpenAI → it creates a short X/Twitter post → it can post for you.

<img width="955" height="470" alt="Image" src="https://github.com/user-attachments/assets/12b995ba-90d4-4153-afe5-10743e93e61f" />

---

## 🛠️ 1) Prerequisites
- n8n account (cloud or self-hosted)
- Google account (for Sheets)
- OpenAI API key (model: `gpt-4o-mini`)
- X/Twitter developer account (Client ID & Client Secret)

---

## 📑 2) Create the Google Sheet
1. Make a new sheet named **Social Media Workflow**.
2. Add a column header: **Article Links**.
3. Paste article URLs in this column.

Example:

```
Article Links
https://www.ibm.com/think/topics/generative-ai
```
---

## 🔔 3) Add Google Sheets Trigger in n8n
1. Click the **+** button → add **Google Sheets Trigger**.
2. Connect your Google account.
3. **Delete** the **Poll Times** value for now (we will add it later).
4. **Document**: choose `Social Media Workflow`.
5. **Sheet**: choose the correct sheet.
6. **Trigger On**: `Row Added or Updated`.
7. Leave other fields as default.
8. Click **Fetch Test Event**.
9. Run once and check the node output.
---

## 📰 4) Add LLM Chain – Summarize Articles
1. Add **Basic LLM Chain**. Name it **Summarise Articles**.
2. In **Source for Prompt (User Message)** choose **Define below**.
3. Paste this prompt:

```
Summarize this article: {{ $json.Article Links }}
```

4. Add an **OpenAI Chat Model** node under this chain.
   - Name: **OpenAI Model - Article Summarizer**
   - Model: **gpt-4o-mini**
---

## ✍️ 5) Add LLM Chain – Generate X/Twitter Post
1. Add another **Basic LLM Chain**. Name it **Generate X/Twitter Post Content**.
2. Set the prompt to:

```
Draft an X post as an AI industry expert analyzing a key news article. The post should:
- Deliver sharp insights on the article's main points.
- Connect to current AI trends.
- Spark discussion with a thought-provoking question.
- Keep it punchy and engaging.
- End with a clear CTA.
Length should be within 30 words. Focus on what matters most to the AI community.
Article summary: {{ $json.text }}
```

3. Connect this chain to the output of **Summarise Articles**.
4. Add an **OpenAI Chat Model** node under it.
   - Name: **OpenAI Model - X/Twitter Content**
   - Model: **gpt-4o-mini**
---

## 🐦 6) Add X (Twitter) Node
1. Add **X (Twitter)** node and choose **Create Tweet**.
2. Name it **Post to Twitter**.
3. In your Twitter Developer Portal, create a project/app and get **Client ID** and **Client Secret**.
4. In n8n, authenticate the X node (use the same Google email as n8n if prompted).
5. Map the **Text** field to the output of the previous node:

```
{{ $json.text }}
```
---

## 🧪 7) Connect & Test
1. Make sure X/Twitter is connected (and LinkedIn too if you add it later).
2. Click **Execute workflow**.
3. Check that:
   - URLs are read from **Article Links** in the Sheet.
   - The **Summarise Articles** node returns a short summary.
   - The **Generate X/Twitter Post Content** node returns a 30‑word post.
   - The **Post to Twitter** node creates a tweet (or shows a valid preview).

---

## 🔗 Workflow Order 
```
Google Sheets Trigger
→ Summarise Articles (Basic LLM Chain)
→ OpenAI Model - Article Summarizer
→ Generate X/Twitter Post Content (Basic LLM Chain)
→ OpenAI Model - X/Twitter Content
→ Post to X (Twitter)
```

