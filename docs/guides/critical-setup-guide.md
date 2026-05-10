# 🚀 CosmicSec Workflow V3 - Setup & Configuration Guide

## Overview

This guide covers the setup for 5 critical workflow enhancements deployed to all 9 CosmicSec repositories:

1. ✅ **Duplicate Issue Detection & UPDATE Logic** - Cross-repo duplicate detection
2. ✅ **OpenCode AI Integration** - Automated code fixing and enhancement
3. ✅ **Security Scanning & Auto-Fix** - GitHub security vulnerabilities scanning
4. ✅ **Code Quality Improvements** - Feature/logic enhancement suggestions
5. ✅ **Cross-Repo Access** - Org-wide issue visibility

---

## 1️⃣ CRITICAL: OpenCode API Integration Setup

### What You Need:
- No API key is required for the default OpenCode free model (`opencode`).
- GitHub Organization/Personal access to secrets (optional for cross-repo features).

### Notes on Models and Secrets

- Default behavior: workflows use the OpenCode free model (`opencode`) and do not require an `ANTHROPIC_API_KEY` secret.
- Optional: If you prefer to use an Anthropic Claude model, you may add `ANTHROPIC_API_KEY` as a repository secret for those repos and update `OPENCODE_MODEL` accordingly.

### OpenCode Workflow Triggers

OpenCode workflows trigger on:

```yaml
✅ Issue created/edited → Auto-analysis
✅ PR opened/updated → Code review
✅ Comment with /opencode → Manual trigger
✅ Schedule: Every 6 hours → Auto-improvement scan
✅ workflow_dispatch → Manual execution
```

### Test OpenCode Integration:

1. Create a test issue or PR in any repo
2. Comment: `/opencode fix this`
3. Watch the workflow run at: Actions → opencode-agent.yml
4. Observe the OpenCode analysis and suggested fixes

---

## 2️⃣ Cross-Repo Issue Access Setup

### For Org-Wide Duplicate Detection

The system needs to see issues from all 9 repos. This requires:

#### Option A: Use Organization Token (Recommended)

1. **Create org-level Personal Access Token:**
   - Go to: https://github.com/settings/personal-access-tokens/new
   - Select repositories: All 9 CosmicSec repos
   - Grant scopes:
     - `repo:read` (read repo content)
     - `issues:read` (read issues)
     - `pull_requests:read` (read PRs)

2. **Add to GitHub Secrets as ORG_COSMICSEC_TOKEN:**
   ```bash
   gh secret set ORG_COSMICSEC_TOKEN --repo CosmicSec-Lab/cosmicsec-ai
   ```

3. **Add to each workflow that needs it:**
   ```yaml
   env:
     ORG_TOKEN: ${{ secrets.ORG_COSMICSEC_TOKEN }}
   ```

#### Option B: Use GitHub App

1. Create a private GitHub App in org settings
2. Grant it permissions:
   - Read access to Issues
   - Read access to Pull Requests
3. Install on all 9 repos
4. Store the app credentials as secrets

### Verify Cross-Repo Access:

Check `.github/issue-creation-summary.json` after workflow runs:

```json
{
  "duplicate_pairs": [
    {
      "new_title": "...",
      "existing_repo": "cosmicsec-services",
      "existing_issue_number": 42
    }
  ]
}
```

---

## 3️⃣ Security Scanning Setup

### Automatic GitHub Security Advisories Scanning

The `security-scan.yml` workflow automatically:

✅ Scans Python dependencies (Bandit, Safety, pip-audit)
✅ Scans Node.js dependencies (npm audit)
✅ Checks GitHub Security tab for advisories
✅ Creates issues for high/critical vulnerabilities
✅ Auto-fixes with `npm audit fix` and `pip install --upgrade`

### Enable GitHub Vulnerability Alerts:

For each repo:

1. Go to **Settings → Code security and analysis**
2. Enable:
   - ✅ **Dependency graph**
   - ✅ **Dependabot alerts**
   - ✅ **Dependabot security updates**
   - ✅ **Secret scanning**

### View Security Issues Created:

```bash
gh issue list --repo CosmicSec-Lab/cosmicsec-ai --label security
```

---

## 4️⃣ Duplicate Issue Update Logic

### How It Works:

1. **Issue Created** in repo A
2. **Same issue detected** in repo B (same title pattern + labels)
3. **Instead of creating new issue:**
   - Updates existing issue in repo A with new findings
   - Posts cross-repo link comment
   - Marks as duplicate pair in summary

### Verify Duplicate Detection:

Check the issue creation summary:

```bash
cat .github/issue-creation-summary.json
```

Look for:
```json
{
  "updated_issues": [
    {
      "number": 42,
      "repo": "cosmicsec-services",
      "new_findings": "[SECURITY] Similar issue found in cosmicsec-ai"
    }
  ],
  "duplicate_pairs": [...]
}
```

---

## 5️⃣ Manual Workflow Triggering

### Option A: Via GitHub CLI

```bash
# Trigger security scan
gh workflow run security-scan.yml --repo CosmicSec-Lab/cosmicsec-ai

# Trigger OpenCode analysis with custom prompt
gh workflow run opencode-agent.yml --repo CosmicSec-Lab/cosmicsec-ai

# Trigger auto-fix pipeline
gh workflow run auto-fix-and-improve-v3.yml --repo CosmicSec-Lab/cosmicsec-ai
```

### Option B: Via GitHub Web UI

1. Go to repo → **Actions** tab
2. Select workflow: `opencode-agent.yml`
3. Click **Run workflow** (right side)
4. Enter optional prompt
5. Click **Run workflow**

### Option C: Comment-Based Trigger (OpenCode)

```bash
# On any issue or PR, comment:
/opencode fix this security issue
/oc add error handling to this function
/opencode review this PR comprehensively
```

---

## 6️⃣ Configuration for Custom Behavior

### Custom OpenCode Prompt

Edit `.github/workflows/opencode-agent.yml`:

```yaml
prompt: |
  You are our custom code reviewer.
  Focus on:
  1. Security vulnerabilities
  2. Performance optimizations
  3. Missing error handling
  4. Architectural improvements
```

### Custom Issue Creation Rules

Edit `.github/scripts/advanced_smart_issue_creator_v3.py`:

```python
# Adjust duplicate detection threshold
def _title_similarity(self, title1: str, title2: str) -> float:
    return SequenceMatcher(None, title1, title2).ratio()
    # Default: 0.7 (70% match = duplicate)
    # Increase to 0.8 for stricter matching
    # Decrease to 0.6 for looser matching
```

### Custom Enhancement Rules

Edit `.github/scripts/advanced_code_enhancer_v3.py`:

```python
# Add custom improvement patterns
CUSTOM_PATTERNS = {
    "your-pattern": ["keywords", "to", "match"],
}
```

---

## 7️⃣ Troubleshooting

### OpenCode Not Running?

```bash
# Check:
1. If using Anthropic models, ensure `ANTHROPIC_API_KEY` secret is set (optional) ✓
2. Workflow file exists ✓
3. Trigger event happened (issue created, comment added) ✓
4. Repo has proper permissions ✓

# View logs:
gh run list --repo CosmicSec-Lab/cosmicsec-ai | grep opencode
gh run view <RUN_ID> --repo CosmicSec-Lab/cosmicsec-ai --log
```

### Duplicate Detection Not Working?

```bash
# Check:
1. ORG_COSMICSEC_TOKEN set ✓
2. Token has repo:read scope ✓
3. All 9 repos are included ✓
4. previous runs didn't create issues ✓

# Debug:
cat .github/issue-creation-summary.json | jq '.duplicate_pairs'
```

### Security Scan Failing?

```bash
# Check:
1. package.json/requirements.txt in correct location ✓
2. npm/pip installed successfully ✓
3. Audit tools available ✓

# View errors:
gh run view <RUN_ID> --repo CosmicSec-Lab/cosmicsec-web --log-failed
```

---

## 8️⃣ Deployment Verification Checklist

For each of 9 repos, verify:

```bash
# ✅ V3 Scripts deployed
ls -la .github/scripts/advanced_smart_issue_creator_v3.py
ls -la .github/scripts/advanced_code_enhancer_v3.py

# ✅ V3 Workflows deployed
ls -la .github/workflows/opencode-agent.yml
ls -la .github/workflows/security-scan.yml
ls -la .github/workflows/auto-fix-and-improve-v3.yml

# ✅ Commits pushed
git log --oneline | grep -E "opencode|security|npm-fix|scripts-v3|workflows-v3"

# ✅ Secrets configured
gh secret list --repo CosmicSec-Lab/<repo>
```

---

## 9️⃣ Performance & Cost Considerations

### OpenCode (default free) / Anthropic (optional)

- **OpenCode (free model)**: Default `opencode` model used by workflows — no API key required and suitable for most automation.
- **Anthropic (optional)**: If you prefer Claude models, add `ANTHROPIC_API_KEY` and be aware of pay-per-use costs.
- **Rate Limit (Anthropic)**: ~100 requests/minute per API key
- **Recommendation**: Set scheduled workflows to run every 6 hours (not hourly)
- **Estimate (Anthropic)**: ~$0.50-2.00/month per repo for typical usage (varies with usage)

### GitHub Actions

- **Included**: 2,000 minutes/month free
- **Our usage**: ~5-10 minutes per workflow run
- **Estimate**: ~50-100 workflow runs/month = 250-1000 minutes
- **Within free tier**: ✅ Yes

### Best Practices

1. Use `schedule: cron: '0 */6 * * *'` for automated scans (not hourly)
2. Disable workflows for archived/inactive repos
3. Use `continue-on-error: true` to prevent cascading failures
4. Monitor GitHub Actions usage in org settings

---

## 🔟 Next Steps

### Immediate (Today):

1. [ ] Optionally add `ANTHROPIC_API_KEY` to any repo secrets if you want Anthropic models
2. [ ] Add `ORG_COSMICSEC_TOKEN` for cross-repo access (recommended)
3. [ ] Test OpenCode by commenting `/opencode analyze` on a test issue
4. [ ] Verify security scan runs and detects issues

### Short-term (This Week):

1. [ ] Review OpenCode suggestions on generated PRs
2. [ ] Approve and merge security fixes
3. [ ] Monitor duplicate issue detection
4. [ ] Fine-tune OpenCode prompts for your team

### Medium-term (This Month):

1. [ ] Integrate with project boards for issue tracking
2. [ ] Set up notifications for security/critical issues
3. [ ] Create runbooks for handling OpenCode suggestions
4. [ ] Document team standards in custom prompts

---

## 📚 Additional Resources

- **OpenCode Docs**: https://opencode.ai/docs/github/
- **Anthropic API Docs**: https://docs.anthropic.com/
- **GitHub Actions**: https://docs.github.com/en/actions
- **GitHub Security**: https://docs.github.com/en/code-security

---

## ✨ Summary of New Capabilities

| Feature | Status | Setup Required |
|---------|--------|-----------------|
| Duplicate Issue Detection | ✅ Deployed | ORG_TOKEN for cross-repo |
| OpenCode AI Integration | ✅ Deployed | Optional (ANTHROPIC_API_KEY for Claude) |
| Security Scanning | ✅ Deployed | GitHub Alerts enabled |
| Code Quality Improvements | ✅ Deployed | None (automatic) |
| Auto-Fix & Format | ✅ Deployed | None (automatic) |
| Cross-Repo Access | ✅ Deployed | ORG_TOKEN for cross-repo |

---

**Questions?** Check the troubleshooting section or review workflow logs via GitHub CLI.

**Last Updated**: May 7, 2026  
**Applies to**: All 9 CosmicSec repositories  
**Workflow Version**: V3 (Critical Gaps Edition)
