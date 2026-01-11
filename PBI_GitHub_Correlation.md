# Case Study: Gemini 3 Crash with Microsoft Power BI Modeling MCP

**Date**: 11 Januari 2026  
**Subject**: Confirmation of Local Installation vs Official Microsoft Repository  
**Linked Issue**: [microsoft/powerbi-modeling-mcp Issue #42](https://github.com/microsoft/powerbi-modeling-mcp/issues/42)

---

## 📌 1. Local Installation Audit

Based on the investigation of folder `C:\Users\HYPE\.gemini\antigravity\scratch\powerbi_mcp\`, the installed files are:
- `powerbi-modeling-mcp.exe` (39MB)
- `msasxpress.dll` (Microsoft Analysis Services component)
- `msalruntime.dll` (Microsoft Authentication Library)
- `AnalysisServices.AppSettings.json`

**Conclusion**: This is the **official Microsoft Power BI Modeling MCP Server** installed via the VS Code Extension or manual VSIX extraction.

---

## 🔍 2. Correlation with Official Bug Reports

A search on GitHub reveals that this exact issue was reported on **December 30, 2025**:
- **Issue Title**: "[Bug]: in Antigravity, gemini models crash after installing the powerbi modeling mcp"
- **Issue Number**: 42
- **Repository**: microsoft/powerbi-modeling-mcp

### Key Findings from Issue #42:
- Matches our observation: Gemini 3 (Pro/Flash) crashes with "Agent execution terminated".
- Matches our observation: Claude models (Sonnet/Opus) work perfectly.
- Confirms that the conflict is specifically between the **PBI MCP executable** and **Gemini's Tool Execution engine**.

---

## 🛠️ 3. Our Added Value (New Insights)

Our debugging process added several critical insights not mentioned in the original GitHub issue:
1. **The Space-in-Path Factor**: We discovered that Gemini 3 is also sensitive to spaces in the workspace folder name (`Data D` vs `DataD`). Even if PBI is fixed, Gemini might still fail if the path has spaces.
2. **Isolation Confirmation**: We performed a systematic binary search across 9 different MCP servers and confirmed that **only PBI** triggers this specific crash in a clean-path workspace.
3. **Verified Workaround**: We created a modular configuration system (`mcp_config_WITHOUT_PBI.json`) that allows for 100% stability with the remaining 8 MCP servers while using Gemini 3.

---

## 🎯 4. Final Recommendations for Sharing

### Strategy A: Contributing to Microsoft Repo
- Post a comment on **Issue #42**.
- Provide the "Space-in-Path" discovery as a secondary factor the developers should be aware of.
- Share the "Binary Search Isolation" log as proof that other MCP servers (like QGIS or GitHub) do not cause this error.

### Strategy B: Personal Documentation (GitHub)
- Create a repo named `ai-agent-debugging`.
- Upload the following files from `00-General_Export/`:
  - `Antigravity_Bug_Report_Gemini3_Tool_Execution.md`
  - `MCP_Binary_Search_Testing_Guide.md`
  - `Workspace_Path_Migration_Analysis.md`
  - This file (`PBI_GitHub_Correlation.md`)

---

**Status**: Verified & Documented  
**Lead Debugger**: Antigravity AI Agent  
**User**: HYPE
