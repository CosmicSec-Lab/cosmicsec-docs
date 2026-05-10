# 📋 GitHub Secret Setup Guide - ORG_PROJECT_PAT

This comprehensive guide walks you through setting up the `ORG_PROJECT_PAT` GitHub secret for automatic project syncing across your organization.

---

## 🎯 What is ORG_PROJECT_PAT?

**ORG_PROJECT_PAT** is an organization-level secret that enables:
- ✅ Automatic synchronization of GitHub Issues and PRs to Organization Projects
- ✅ Cross-repository project management
- ✅ Automation rules execution across multiple repos
- ✅ Enhanced permissions for project board operations

**Difference from GITHUB_TOKEN:**
- `GITHUB_TOKEN`: Repo-level, expires per workflow run (default)
- `ORG_PROJECT_PAT`: Organization-level, personal access token with extended permissions

---

## 📝 Step-by-Step Setup Instructions

### **Step 1: Generate Personal Access Token (PAT)**

#### On GitHub.com:

1. **Go to Personal Access Tokens:**
   - Click your **profile picture** (top-right corner)
   - Select **Settings**
   - In left sidebar, click **Developer settings**
   - Click **Personal access tokens**
   - Click **Tokens (classic)** (or **Fine-grained tokens** for modern GitHub)

2. **Create New Token (Classic Method - Recommended for organizations):**
   - Click **Generate new token**
   - Select **Generate new token (classic)**

3. **Configure Token:**
   - **Token name:** `org-project-sync-token` (or your preferred name)
   - **Expiration:** Select appropriate timeframe:
     - ⏰ 30 days (short-lived, more secure)
     - 📅 90 days (balanced)
     - ♾️ No expiration (convenience, less secure)
   
   **Recommended:** Set 90-day expiration for security balance

4. **Select Scopes (CRITICAL - Select EXACTLY these):**

   ✅ **REQUIRED Scopes:**
   - `repo` (full control of private repositories)
     - Includes: `repo:status`, `repo_deployment`, `public_repo`
   - `project` (read/write project boards)
   - `admin:org_hook` (manage organization webhooks)
   
   ✅ **ADDITIONAL Recommended:**
   - `read:user` (access user profile data)
   - `user:email` (access user email)

   **Screenshot Reference:**
   ```
   [✓] repo (Full control of private repositories)
   [✓] project (Read and write access to projects)
   [✓] admin:org_hook (Read, write, and activate organization webhooks)
   [✓] read:user (Read user profile data)
   [✓] user:email (Access user email addresses)
   ```

5. **Generate Token:**
   - Scroll to bottom
   - Click **Generate token** button

6. **⚠️ SAVE TOKEN IMMEDIATELY:**
   ```
   🔑 Your token is: ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
   ```
   - **Copy the entire token to a secure location**
   - You will ONLY see it once!
   - Store in password manager or secure file
   - ⛔ **NEVER** commit to Git or share publicly

---

### **Step 2: Add Secret to GitHub Organization**

#### Organization-Level Secret Setup:

1. **Navigate to Organization Settings:**
   - Go to your GitHub organization page
   - Click **Settings** (bottom of left sidebar)
   - Look for organization settings (NOT personal settings)

2. **Access Secrets:**
   - Click **Secrets and variables** in left sidebar
   - Click **Actions**

3. **Create New Organization Secret:**
   - Click **New organization secret** button

4. **Configure Secret:**
   - **Name:** `ORG_PROJECT_PAT` (EXACTLY this name - case sensitive)
   - **Value:** Paste your PAT token (from Step 1)
   - **Repositories:** 
     - Select **Selected repositories**
     - Add all CosmicSec repositories:
       - ✅ cosmicsec-ai
       - ✅ cosmicsec-cli
       - ✅ cosmicsec-core
       - ✅ cosmicsec-deepintel
       - ✅ cosmicsec-deploy
       - ✅ cosmicsec-docs
       - ✅ cosmicsec-sdk
       - ✅ cosmicsec-services
       - ✅ cosmicsec-web

5. **Save Secret:**
   - Click **Add secret** button

   **Confirmation Message:**
   ```
   ✅ Secret ORG_PROJECT_PAT has been added to [X] selected repositories
   ```

---

### **Step 3: Add Secret to Repository Level (Optional but Recommended)**

For each repository:

1. **Go to Repository Settings:**
   - Navigate to each CosmicSec repo
   - Click **Settings**
   - Click **Secrets and variables** > **Actions**

2. **Add Repository Secret:**
   - Click **New repository secret**
   - **Name:** `ORG_PROJECT_PAT`
   - **Value:** Paste same PAT token
   - Click **Add secret**

   Repeat for all 9 repositories

---

### **Step 4: Configure Workflows to Use Secret**

Your workflows already have this configured. Verify:

#### In workflow files (e.g., `.github/workflows/project-auto-sync.yml`):

```yaml
env:
  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  ORG_PROJECT_PAT: ${{ secrets.ORG_PROJECT_PAT }}

jobs:
  sync-project:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Sync to Organization Project
        env:
          PROJECT_ID: ${{ secrets.PROJECT_ID }}
        run: |
          python3 .github/scripts/project_updater_v2.py \
            --token ${{ secrets.ORG_PROJECT_PAT }} \
            --project-id ${{ env.PROJECT_ID }}
```

---

### **Step 5: Verify Secret Access**

#### Test 1: Check Secret Appears in Workflow

1. Go to repository
2. Click **Actions**
3. Find **"Advanced Project Sync"** workflow
4. Click on most recent run
5. Click **project-auto-sync** job
6. Expand **"Set up environment"**
7. Verify line shows: `✓ ORG_PROJECT_PAT set`

#### Test 2: Run Manual Workflow Dispatch

1. Go to repository **Actions** tab
2. Select **"Advanced Project Sync V2"** workflow
3. Click **Run workflow**
4. Select branch: **main** (or default)
5. Click **Run workflow**
6. Monitor execution logs for success

Expected output:
```
🚀 Advanced Project Updater V2 Starting
📋 Syncing GitHub Issues...
✓ Synced 5 issues
🔀 Syncing GitHub Pull Requests...
✓ Synced 3 pull requests
⚙️  Applying Automation Rules...
✅ Project Update Completed
```

---

## 🔒 Security Best Practices

### ✅ DO:
- ✅ Use token expiration (30-90 days)
- ✅ Create separate PATs for different purposes
- ✅ Review GitHub secret audit logs regularly
- ✅ Rotate tokens on schedule (every 90 days)
- ✅ Audit secret access in organization settings
- ✅ Monitor workflow execution logs for unauthorized access

### ❌ DON'T:
- ❌ Commit secrets to repository
- ❌ Use personal account tokens (use org tokens)
- ❌ Share tokens via Slack, email, or chat
- ❌ Use `No expiration` for production tokens
- ❌ Reuse same token across multiple organizations
- ❌ Grant unnecessary scopes

---

## 🔄 Token Rotation (Every 90 Days)

### When to Rotate:
- After 90 days (if expiration set)
- If token is suspected compromised
- After security audit
- When team member leaves

### How to Rotate:

1. **Generate New Token:**
   - Follow Step 1 again with same settings
   - Copy new token

2. **Update All Locations:**
   - Organization secrets
   - Repository secrets (all 9 repos)
   - Any documentation

3. **Delete Old Token:**
   - GitHub Settings → Developer settings → Personal access tokens
   - Find old token
   - Click **Delete**

4. **Verify New Token Works:**
   - Run test workflow (Step 5)
   - Monitor logs for successful execution

---

## ❓ Troubleshooting

### Issue: "Workflow Failed - ORG_PROJECT_PAT not found"

**Solution:**
1. Verify secret name is exactly `ORG_PROJECT_PAT` (case-sensitive)
2. Verify secret added to **Actions** secrets (not Dependabot)
3. Verify repository is in selected list
4. Re-run workflow with **Run workflow** button

### Issue: "Permission denied to sync project"

**Solution:**
1. Verify token has `project` scope
2. Verify token has `repo` scope
3. Check if token expired (GitHub notifications)
4. Create new token with correct scopes

### Issue: "Workflow shows secret as masked but not used"

**Solution:**
1. Verify Python script references `ORG_PROJECT_PAT` environment variable
2. Check workflow file for correct syntax: `${{ secrets.ORG_PROJECT_PAT }}`
3. Review workflow logs for error messages

### Issue: "Organization members can't see secret"

**Solution:**
1. Secrets are organization-level, not visible to all members
2. Only organization owners can view/manage secrets
3. Secret values are never displayed (only names visible)
4. This is normal security behavior

---

## 📊 Validation Checklist

Before considering setup complete, verify:

- [ ] PAT token generated with correct scopes
- [ ] Token stored securely (password manager)
- [ ] `ORG_PROJECT_PAT` added to organization secrets
- [ ] `ORG_PROJECT_PAT` selected for all 9 repositories
- [ ] Repository secrets updated (all 9 repos)
- [ ] Workflows reference `${{ secrets.ORG_PROJECT_PAT }}`
- [ ] Manual workflow dispatch executed successfully
- [ ] Project board shows synced issues
- [ ] Logs show successful automation rules applied
- [ ] Token expiration date noted for calendar

---

## 🎓 Additional Resources

- **GitHub Docs:** https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens
- **Organization Secrets:** https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions
- **Workflow Syntax:** https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions

---

## 📞 Support

If you encounter issues:

1. **Check workflow logs:** Actions → Workflow run → Job logs
2. **Verify secret exists:** Settings → Secrets and variables → Actions
3. **Review token scopes:** Developer settings → Personal access tokens
4. **Check organization permissions:** Settings → Members and permissions

---

**Last Updated:** `2024`  
**Applies to:** CosmicSec organization (all 9 repositories)  
**Workflow Feature:** Advanced Project Sync V2
