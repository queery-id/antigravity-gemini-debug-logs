# MCP Server Testing Guide - Binary Search Method

**Date**: 11 Januari 2026, 06:46  
**Status**: ✅ Confirmed - MCP servers cause Gemini failure  
**Method**: Binary Search (Divide & Conquer)  
**Estimated Tests**: 4-5 tests (vs 9 tests for linear)

---

## 🎯 **Testing Strategy**

### Why Binary Search?

```yaml
Linear Testing (one-by-one):
  - Tests needed: 9 (one per MCP server)
  - Time: ~45 minutes (5 min per test)
  - Pros: Simple, thorough
  - Cons: Slow, tedious

Binary Search (divide & conquer):
  - Tests needed: 4-5 (halving each time)
  - Time: ~20-25 minutes
  - Pros: Fast, efficient
  - Cons: Slightly more complex
  
Recommendation: Binary Search ⭐
```

---

## 📋 **Test Sequence**

### **Test 1: First Half (5 servers)** 🔴 START HERE

**File**: `mcp_test_1_first_half.json`

**Servers**:
- pbi
- wordpress-queery
- qgis
- context7
- sqlite

**Steps**:
1. Copy content dari `mcp_test_1_first_half.json`
2. Paste ke Antigravity Settings → MCP Servers
3. Save & Restart Antigravity
4. Test Gemini: "Create file test1.md with content 'Test 1'"

**Results**:
- ⬜ **WORKS** → Problem in second half → Go to Test 2B
- ⬜ **FAILS** → Problem in first half → Go to Test 2A

---

### **Test 2A: First Quarter (3 servers)**

**File**: `mcp_test_3_first_quarter.json`

**Servers**:
- pbi
- wordpress-queery
- qgis

**Steps**:
1. Copy content dari `mcp_test_3_first_quarter.json`
2. Paste ke Antigravity Settings → MCP Servers
3. Save & Restart Antigravity
4. Test Gemini: "Create file test2a.md with content 'Test 2A'"

**Results**:
- ⬜ **WORKS** → Problem in (context7, sqlite) → Go to Test 3A
- ⬜ **FAILS** → Problem in (pbi, wordpress, qgis) → Go to Test 3B

---

### **Test 2B: Second Half (4 servers)**

**File**: `mcp_test_2_second_half.json`

**Servers**:
- github
- google-drive
- postgres
- notion

**Steps**:
1. Copy content dari `mcp_test_2_second_half.json`
2. Paste ke Antigravity Settings → MCP Servers
3. Save & Restart Antigravity
4. Test Gemini: "Create file test2b.md with content 'Test 2B'"

**Results**:
- ⬜ **WORKS** → No problem found (unlikely!)
- ⬜ **FAILS** → Problem in second half → Test individually

---

### **Test 3A: Context7 vs SQLite**

**File**: `mcp_test_4_context_sqlite.json`

**Test Context7 Only**:
```json
{
    "mcpServers": {
        "context7": {
            "command": "npx",
            "args": ["-y", "@upstash/context7-mcp@latest", "--api-key", "ctx7sk-YOUR_CONTEXT7_API_KEY_HERE"]
        }
    }
}
```
- ⬜ **WORKS** → context7 is OK
- ⬜ **FAILS** → **context7 is the culprit!** 🎯

**Test SQLite Only**:
```json
{
    "mcpServers": {
        "sqlite": {
            "command": "npx",
            "args": ["-y", "@modelcontextprotocol/server-sqlite"]
        }
    }
}
```
- ⬜ **WORKS** → sqlite is OK
- ⬜ **FAILS** → **sqlite is the culprit!** 🎯

---

### **Test 3B: PBI vs WordPress vs QGIS** ⭐ MOST LIKELY

**Priority Order** (test in this order):

#### 3B.1: Test QGIS Only (HIGHEST SUSPICION)

**File**: `mcp_test_individual_qgis.json`

**Why suspect**: Uses `uv` command + local path

**Test**:
- ⬜ **WORKS** → qgis is OK
- ⬜ **FAILS** → **qgis is the culprit!** 🎯

#### 3B.2: Test PBI Only

**File**: `mcp_test_individual_pbi.json`

**Why suspect**: Uses .exe file

**Test**:
- ⬜ **WORKS** → pbi is OK
- ⬜ **FAILS** → **pbi is the culprit!** 🎯

#### 3B.3: Test WordPress Only

**Why suspect**: Uses JWT token

**Test**:
```json
{
    "mcpServers": {
        "wordpress-queery": {
            "command": "npx",
            "args": ["-y", "@automattic/mcp-wordpress-remote@latest"],
            "env": {
                "WP_API_URL": "https://queery.my.id/",
                "JWT_TOKEN": "YOUR_WORDPRESS_JWT_TOKEN_HERE"
            }
        }
    }
}
```
- ⬜ **WORKS** → wordpress is OK
- ⬜ **FAILS** → **wordpress is the culprit!** 🎯

---

## 🎯 **Quick Test (If You Want to Skip Binary Search)**

**My Top 3 Suspects** (in order):

### 1. QGIS (70% confidence)
**File**: `mcp_test_individual_qgis.json`
- Uses `uv` command (might not be compatible with Gemini)
- Uses local workspace path
- Most complex setup

### 2. PBI (20% confidence)
**File**: `mcp_test_individual_pbi.json`
- Uses .exe file
- Custom executable

### 3. Context7 (10% confidence)
**File**: `mcp_test_individual_context7.json`
- Uses API key
- External service

**Quick Test Strategy**:
1. Test QGIS only → If fails, FOUND IT!
2. If works, test PBI only → If fails, FOUND IT!
3. If works, test Context7 only → If fails, FOUND IT!
4. If all work individually, test combinations

---

## 📊 **Test Results Tracker**

### Binary Search Results

| Test | Servers | Result | Time | Notes |
|------|---------|--------|------|-------|
| Test 1 | First Half (5) | ⬜ W / ⬜ F | | |
| Test 2A | First Quarter (3) | ⬜ W / ⬜ F | | |
| Test 2B | Second Half (4) | ⬜ W / ⬜ F | | |
| Test 3A | Context7 | ⬜ W / ⬜ F | | |
| Test 3A | SQLite | ⬜ W / ⬜ F | | |
| Test 3B | QGIS | ⬜ W / ⬜ F | | |
| Test 3B | PBI | ⬜ W / ⬜ F | | |
| Test 3B | WordPress | ⬜ W / ⬜ F | | |

### Quick Test Results

| MCP Server | Result | Notes |
|------------|--------|-------|
| QGIS | ⬜ W / ⬜ F | |
| PBI | ⬜ W / ⬜ F | |
| Context7 | ⬜ W / ⬜ F | |

---

## 🔧 **After Finding the Culprit**

### Option 1: Disable Problematic MCP

```yaml
Action:
  1. Remove problematic MCP from config
  2. Keep all other MCP servers enabled
  3. Use Gemini normally (without that MCP)

Pros:
  ✅ Gemini works
  ✅ Other MCP servers still available
  
Cons:
  ❌ Lose functionality of that MCP server
```

### Option 2: Use Claude for That MCP

```yaml
Action:
  1. Keep all MCP servers enabled
  2. Use Claude when you need that specific MCP
  3. Use Gemini for other tasks

Pros:
  ✅ All MCP servers available
  ✅ Can still use problematic MCP (with Claude)
  
Cons:
  ❌ Need to switch models
  ❌ Higher cost when using Claude
```

### Option 3: Report Bug & Wait for Fix

```yaml
Action:
  1. Report bug to MCP server maintainer
  2. Report bug to Antigravity team
  3. Use workaround (Option 1 or 2) while waiting

Pros:
  ✅ Help improve the tool
  ✅ Might get fixed in future
  
Cons:
  ❌ No immediate solution
  ❌ Uncertain timeline
```

---

## 📝 **Bug Report Template**

When you find the culprit, use this template to report:

```markdown
**Title**: Gemini 3 fails when [MCP_NAME] MCP server is enabled

**Environment**:
- Antigravity Version: 1.13.3
- OS: Windows 10/11
- Model: Gemini 3 Flash/Pro
- MCP Server: [MCP_NAME] version [VERSION]

**Description**:
Gemini 3 models fail with "Agent execution terminated due to error" when [MCP_NAME] MCP server is enabled in workspace.

**Steps to Reproduce**:
1. Enable [MCP_NAME] MCP server in Antigravity workspace
2. Select Gemini 3 Flash or Pro model
3. Request any file operation (e.g., "Create a file test.md")
4. Error occurs: "Agent execution terminated due to error"

**Expected Behavior**:
Gemini should execute the request successfully

**Actual Behavior**:
Agent terminates with error

**Workaround**:
- Disable [MCP_NAME] MCP server
- OR use Claude models instead

**Additional Context**:
- Claude models work fine with same MCP server
- Gemini works fine without MCP servers
- Gemini works fine in Playground (no MCP servers)
- Issue confirmed through binary search testing
```

---

## 🎯 **Recommended Testing Path**

### For Maximum Speed (My Recommendation):

```yaml
Path: Quick Test (Top 3 Suspects)

Steps:
  1. Test QGIS only (70% chance)
  2. If works, test PBI only (20% chance)
  3. If works, test Context7 only (10% chance)
  4. If all work, do binary search

Expected Time: 10-15 minutes
Success Rate: ~90%
```

### For Maximum Thoroughness:

```yaml
Path: Full Binary Search

Steps:
  1. Test 1: First Half
  2. Test 2A or 2B (depending on Test 1)
  3. Test 3A or 3B (depending on Test 2)
  4. Individual tests if needed

Expected Time: 20-25 minutes
Success Rate: 100%
```

---

## ✅ **Next Steps**

1. **Choose testing method**:
   - ⬜ Quick Test (recommended)
   - ⬜ Binary Search (thorough)

2. **Run tests** following guide above

3. **Document results** in tracker

4. **Report findings** when culprit found

5. **Choose solution**:
   - ⬜ Disable problematic MCP
   - ⬜ Use Claude for that MCP
   - ⬜ Report bug & wait

---

**Ready to start testing!** 🚀

Recommendation: Start with **Quick Test → QGIS** (highest probability)
