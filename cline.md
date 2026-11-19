# Complete Setup Guide: Budget AI Coding Agent on Fedora Linux
**Stack:** VS Code + Cline + OpenRouter (`DeepSeek V4.1 Flash` & `GLM 5.3 Flash`)  
**Target Budget:** ₦9,000 – ₦10,000 / month (~$6.50 – $7.00 USD)  
**Workload:** 5–9 hours/day (Web, Mobile, Desktop & Backend)

---

## Step 1: Set Up & Protect Your OpenRouter Account

1. **Create an Account**
   * Go to [openrouter.ai](https://openrouter.ai) and sign up using your GitHub or Google account.

2. **Add Credits (~$6.50 – $7.00 USD)**
   * Navigate to **Settings → Credits** (`https://openrouter.aisettings/credits`).
   * Click **Add Credits** and fund **$6.50 or $7.00** (roughly ₦9,000–₦10,000 depending on exchange rate) using a virtual dollar card (e.g., Grey, Chipper, Geegpay, Bybit Card) or crypto.
   * *Tip:* Turn **OFF** "Auto Top-Up" so OpenRouter never charges your card automatically.

3. **Create a Hard-Capped API Key**
   * Go to **Settings → Keys** (`https://openrouter.aisettings/keys`).
   * Click **Create Key**.
   * **Name:** `Fedora-VSCode-Cline`
   * **Credit Limit:** Set a strict limit (e.g., `$6.50` or `$2.00/week` so one bad loop cannot drain your whole month).
   * Click **Create** and **copy the key immediately** (`sk-or-v1-...`). You will not be able to view it again.

---

## Step 2: Install VS Code & Git on Fedora Linux

Open your Fedora terminal (`Ctrl + Alt + T`) and run the following commands to install VS Code officially via Microsoft's DNF repository:

```bash
# 1. Import Microsoft's GPG key
sudo rpm --import https://microsoft.com

# 2. Add the official VS Code repository
echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://microsoft.com\nenabled=1\nautorefresh=1\ntype=rpm-md\ngpgcheck=1\ngpgkey=https://microsoft.com" | sudo tee /etc/yum.repos.d/vscode.repo > /dev/null

# 3. Update DNF cache and install VS Code + Git
sudo dnf check-update
sudo dnf install -y code git
```

Verify the installation:
```bash
code --version
```

---

## Step 3: Install Cline & Free Autocomplete in VS Code

You can install the required extensions directly from your terminal without clicking through menus:

```bash
# 1. Install Cline (Your autonomous coding agent)
code --install-extension saoudrizwan.claude-dev

# 2. Optional: Install GitHub Copilot for FREE tab-autocomplete
# (Saves massive API credits on small boilerplate edits)
code --install-extension GitHub.copilot
```

Launch VS Code in your project folder:
```bash
cd ~/projects/your-project-folder
code .
```

---

## Step 4: Configure Cline with Budget Models (Plan vs. Act)

1. Click the **Cline icon** (robot/bot icon) on the left VS Code Activity Bar.
2. Click the **Gear icon (Settings)** in the top-right corner of the Cline panel.
3. Configure your API Provider:
   * **API Provider:** Select `OpenRouter`
   * **OpenRouter API Key:** Paste your `sk-or-v1-...` key.
4. **Enable "Use different models for Plan and Act"** (Crucial for saving money!):
   * **Plan Mode (The Architect):** Select `deepseek/deepseek-v4.1-flash` *(or `deepseek/deepseek-chat` / `qwen/qwen3-coder`)* — Use this to analyze bugs and plan multi-file changes.
   * **Act Mode (The Builder):** Select `z-ai/glm-5.3-flash` *(or `xiaomi/mimo-v2.6-flash`)* — Ultra-cheap workhorse model for writing and editing the actual code.
5. Click **Done / Save**.

---

## Step 5: Safety Tricks to Keep Your Budget Strictly Under ₦10k

Coding 5–9 hours a day on pay-as-you-go tokens requires discipline. Apply these 5 rules every day:

### 1. Disable Auto-Approve for Expensive Actions
In Cline's bottom chat bar, click **Auto-approve** and configure it safely:
* **Read files & directories:** ✅ Checked (cheap)
* **Edit files:** ❌ Unchecked (review diffs before letting it rewrite 10 files)
* **Execute safe commands:** ❌ Unchecked (prevents infinite terminal error loops like `npm run build` running 15 times in a row while you aren't watching)
* **Max Requests per Task:** Set to `10` or `15` so Cline automatically pauses and asks you before burning tokens in a loop.

### 2. Block Heavy Folders with `.clineignore`
Prevent Cline from scanning `node_modules`, build folders, lockfiles, or mobile binaries. Run this in the root of every project:

```bash
cat << 'EOF' > .clineignore
node_modules/
.git/
dist/
build/
.next/
.expo/
android/app/build/
ios/Pods/
target/
__pycache__/
*.lock
package-lock.json
yarn.lock
pnpm-lock.yaml
*.min.js
*.map
*.png
*.jpg
*.svg
EOF
```

### 3. Tag Specific Files with `@` Instead of Letting the Agent Search
* **Bad (Expensive):** *"Fix the login bug in my app."* (Cline will run 6 file searches and read 15 files = ~40,000 tokens).
* **Good (Cheap):** *"Fix the token refresh bug in `@src/services/auth.ts` and `@src/hooks/useAuth.ts`."* (Cline reads only 2 files = ~2,500 tokens).

### 4. Use Plan Mode First for Big Features
1. Toggle the switch at the bottom of Cline to **Plan**.
2. Ask Cline how it plans to build the feature.
3. Once you agree with the short plan, switch to **Act** and say: *"Proceed with step 1."*

### 5. Watch the Live Cost Counter
Look at the top of the Cline chat window after every prompt—it displays the exact dollar cost (`$0.003`). If a single prompt jumps over `$0.05`, your chat history has grown too large and it is time to reset.

---

## Step 6: How to Keep Project Context Without Burning Tokens

Never keep one chat open all day. Once your context bar passes **40k–50k tokens**, reset using these two files so you **never start from scratch**:

### 1. Create a `.clinerules` File (Permanent Project Brain)
Cline automatically reads `.clinerules` at the start of every new task (~200 tokens). Create one in your project root:

```bash
cat << 'EOF' > .clinerules
# Project Architecture & Rules
- Stack: [e.g., Next.js 15 + TypeScript + Tailwind / React Native Expo / Node.js Express + PostgreSQL]
- Package Manager: pnpm (never use npm or yarn)
- Folder Structure:
  - /src/app -> routes and pages
  - /src/components -> reusable UI components
  - /src/services -> API & database logic

# Coding & Token-Saving Rules
- Write clean, modular TypeScript with strict types.
- Do NOT rewrite entire files if only a small function needs changing; output targeted edits.
- Never install new packages without asking me first.
- Always update PROGRESS.md when finishing a major feature.
EOF
```

### 2. Use `/newtask` + `PROGRESS.md` (Zero-Loss Handoff)
When finishing a feature or when your chat hits ~50k tokens:
1. Tell Cline:
   > *"Summarize what we completed, list modified files, and write the next 3 steps into `@PROGRESS.md`."*
2. Type `/newtask` in the Cline input box (or click the **`+` New Task** button at the top).
3. In your brand-new, empty chat, type:
   > *"Read `@PROGRESS.md` and `@src/target-file.ts`, then continue with Step 1."*

You get **100% of your project memory** with **0% of the bloated chat history**.


