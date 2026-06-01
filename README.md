# n8n Workflow: Send Weekly GitHub Digests to Telegram with Qwen

> 🏆 **n8n Verified Creator Workflow**  
> This workflow has been reviewed and approved by the n8n team.  
> 👤 Creator: [Do Thanh Vinh (Kevin Do)](https://github.com/dothanhvinh17)  
> 🔗 Live demo & docs: [vinhautomation.com/en/tools/github-weekly-digest-telegram-qwen](https://www.vinhautomation.com/en/tools/github-weekly-digest-telegram-qwen/)  
> ⬇️ Download workflow: [n8n.io/workflows/15898](https://n8n.io/workflows/15898-send-weekly-github-digests-to-telegram-with-qwen-via-openrouter/)

## 🎯 What It Does

This workflow automatically monitors all GitHub repositories on your account and delivers **AI-generated engineering reports directly to Telegram**.

- 🗓️ **Weekly auto-report**: Every Monday at 9 AM
- 💬 **On-demand commands**: Send `/report`, `/issues`, `/prs`, or `/status` to your bot for instant updates

**Perfect for:** Indie hackers, solo developers, open-source maintainers, and small engineering teams who want lightweight weekly visibility without expensive analytics tools or Jira setups.

[GitHub Repos] → [Fetch Events] → [Aggregate Stats] → [AI Summarize] → [Telegram Report]
↓
[On-demand: /report | /issues | /prs | /status]

🚀 **Key Highlight:** Two triggers in one workflow — weekly schedule for automatic reports, Telegram bot for on-demand queries. Stats are aggregated before being sent to the LLM, significantly reducing token usage. If the AI fails, a fallback code node still formats raw stats — you never lose connection.

## ✨ Key Features

### 🔹 Multi-Mode Reports via Telegram
- `/report`: Full 4-part weekly digest (highlights, per-repo details, insights, priorities)
- `/issues`: List all open issues with age indicators
- `/prs`: Show PR status (open/merged/closed)
- `/status`: Quick health check with emoji indicators (🟢🟡🔴)
- Reports split into multiple Telegram messages → stay within 4096-char limit, mobile-friendly

### 🔹 Smart Collection & Aggregation
- Pulls complete repo list via GitHub User Repos API → auto-filters archived repos
- For each repo: calls Events API for up to 100 recent events
- Events grouped by repo, counted by type (commits, issues, PRs, releases)
- **Only counts (not raw payloads) reach the LLM** → significantly reducing token usage

### 🔹 Comprehensive Error Handling & Fallback
- Switch node handles 3 paths: has activity → generate report | all repos failed → send alert | no activity → silent stop
- If AI model fails → fallback code node formats raw stats without AI
- If all GitHub repos fail to fetch → error alert sent to Telegram
- **User always gets feedback** — never silent failure

### 🔹 Token-Efficient & Low Cost
- Repository activity aggregated + compressed into stats summary before LLM call
- No vector DB, no embeddings, no extra subscription
- AI does summarization, not retrieval → minimal token usage per run

### Steps:

1. **Configure GitHub**: Create Fine-grained PAT with Contents Read-only (scoped to repos to monitor) → Add GitHub Header Auth credential in n8n

2. **Create Telegram Bot**: Create via @BotFather → get bot token + your chat ID (call `https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates` after sending message to bot)

3. **Get OpenRouter API Key**: Sign up at openrouter.ai

4. **Add Credentials to n8n**:
   - GitHub Header Auth (for repo/events API)
   - Telegram Bot API (for send messages)
   - OpenAI API (OpenRouter endpoint)

5. **Configure Nodes**:
   - "Fetch GitHub Repositories" + "Retrieve GitHub Events": Assign GitHub Header Auth credential
   - "Send Messages to Telegram" + "Send Error Alert": Set your Telegram chat ID
   - "OpenAI Report Model": Assign OpenRouter credential
   - "Telegram Message Trigger": Assign Telegram Bot credential

6. **Activate**: Enable workflow → Send `/report` to your Telegram bot to test immediately

## 🧩 Customization Options

- **Filter repos**: If you have >100 repos, add pagination handling or filter to specific repos:
  
  ```javascript
  var myRepos = ['owner/repo1', 'owner/repo2'];
  items = items.filter(function(item) {
    return myRepos.indexOf(item.json.full_name) !== -1;
  });

Change schedule: Edit cron in "Weekly Digest Trigger":
- **Daily at 9am:** `0 9 * * *`
- **Friday 5pm:** `0 17 * * 5`
- **Twice a week:** `0 9 * * 1,4`
- **Swap the LLM:** Change model in "OpenAI Report Model" to any OpenRouter-supported model (GPT-4o, Claude, Gemini) or point to self-hosted model
- **Change output channel:** Replace "Send Messages to Telegram" with Discord, Slack, Notion, or Email nodes — report format works across all platforms
- **Add more commands:** Edit "Parse Telegram Command" to accept new commands (e.g., `/releases`, `/contributors`) → add logic in LLM prompt under new MODE section
- **Single message mode:** Remove `|||` splitting logic in "Format Telegram Messages" → concatenate all report parts into one message


#### 🔸 PHẦN 6: WHAT'S NEXT TABLE

## 📈 What's Next? (Extending This Workflow)

This workflow is intentionally simple — simple means cheap, stable, easy to debug. When your use case grows, consider:

| Enhancement | When to Add |
|-------------|-------------|
| **Social Media Auto-post** | Repurpose digest into LinkedIn/Twitter post with Telegram approval button before publishing |
| **Obsidian Sync** | Push each report as Markdown to Obsidian vault via Git → searchable project history |
| **Unified Weekly Report** | Aggregate GitHub + Gmail + Google Calendar + WakaTime + Notion tasks into single report |
| **Error Knowledge Base** | Extract bug patterns from issues → append to JSON KB → feed into RAG chatbot for future Q&A |
| **Multi-channel Delivery** | Send same report to Telegram + Email + Discord + Slack simultaneously |

## ⚙️ Requirements Summary

- n8n instance (self-hosted or cloud)
- GitHub Personal Access Token (Fine-grained with Contents Read-only, or Classic with `repo` scope)
- Telegram Bot token + chat ID
- OpenRouter API key (or any OpenAI-compatible endpoint)
- GitHub account with at least one repository

## 💡 Cost Note

- ✅ Stats aggregation: **Free** (runs locally in n8n)
- ⚠️ LLM tokens: **Billed by OpenRouter** per report generation
- 📊 Token usage is low because only aggregated counts (not raw event payloads) are sent to the LLM
- For accounts with hundreds of repositories, cost per run remains minimal (~$0.001-0.02 depending on model)

> Monitor usage via OpenRouter dashboard if running on-demand commands at high frequency.

## 🤝 Contributing & Feedback

- Found a bug in the anonymized example? → Open an issue
- Have an improvement idea? → Submit a PR
- Want to discuss a similar use case? → Email [vinh@vinhautomation.com](mailto:vinh@vinhautomation.com)

## 📄 License

MIT License — Free to use, modify, and share for learning purposes.  
Not for commercial resale without prior agreement.

---

🔗 **More by this creator:**  
[github.com/dothanhvinh17](https://github.com/dothanhvinh17) \| [vinhautomation.com/en/tools](https://www.vinhautomation.com/en/tools) \| [n8n.io/creators/dothanhvinh](https://n8n.io/creators/dothanhvinh)
