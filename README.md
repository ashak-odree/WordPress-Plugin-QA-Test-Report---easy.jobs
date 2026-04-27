# 🔌 easy.jobs WordPress Plugin — QA Test Report

A professional **Quality Assurance Test Report** for the **easy.jobs WordPress Plugin (v2.7.1)**, covering **22 test cases** across 7 modules and **8 documented bugs** — discovered through a combination of **manual functional testing** and **source code analysis** on a LocalWP (localhost) environment.

> 📄 **Submitted to:** Startise Hiring Assessment | **Tester:** Ashakuzzaman Odree | **Date:** April 25, 2026

---

## 📋 Report Summary

| Field | Details |
|---|---|
| **Plugin** | easy.jobs WordPress Plugin |
| **Plugin Version** | v2.7.1 |
| **WordPress Version** | 6.9.4 |
| **Total Test Cases** | 22 |
| **Modules Covered** | 7 |
| **Total Bugs Found** | 8 |
| **Critical Bugs** | 3 |
| **High Severity Bugs** | 4 |
| **Medium Severity Bugs** | 3 |
| **Environment** | LocalWP (localhost) |
| **Testing Method** | Manual Testing + Source Code Analysis |
| **Date** | April 25, 2026 |

---

## 🧪 Test Modules Covered

| Module | Test Cases | Status |
|---|---|---|
| A — Plugin Installation & Activation | TC-001 to TC-003 | ✅ All Pass |
| B — API Authentication | TC-004 to TC-007 | ⚠️ 1 Fail |
| C — Job Management | TC-008 to TC-014 | ❌ 2 Fail |
| E — Candidate Management | TC-015 to TC-018 | ✅ All Pass |
| F — Settings | TC-019 to TC-020 | ✅ All Pass |
| G — Security & Edge Cases | TC-021 to TC-022 | ❌ 2 Fail |

---

## 🐛 Bug Summary Table

| Bug ID | Title | Severity | Module | Result |
|---|---|---|---|---|
| BUG-001 | No Validation Error When Connecting with Empty API Key | High | Authentication | ❌ FAIL |
| BUG-002 | Pin Icon Remains Visible on Job Card After Unpinning | Medium | Job Management | ❌ FAIL |
| BUG-003 | Incorrect Popup Message Shown When Pinning a Job (Shows 'Unpinned' Instead of 'Pinned') | High | Job Management | ❌ FAIL |
| BUG-004 | Job Status Remains 'Draft' After Successful Publish Confirmation | **Critical** | Job Management | ❌ FAIL |
| BUG-005 | Deleted Job Still Appears in Jobs List After Successful Delete Confirmation | **Critical** | Job Management | ❌ FAIL |
| BUG-006 | Misleading Validation Error Message When Script Tag Entered in Job Title | Medium | Security / Job Creation | ⚠️ PARTIAL FAIL |
| BUG-007 | Very Long Job Title Silently Rejected with Misleading 'Please provide job title' Error | High | Edge Case / Job Creation | ❌ FAIL |
| BUG-008 | Candidate Search Button Shows Nothing & Enter Key Causes Blank White Screen | **Critical** | Candidates — Search | ❌ FAIL |

---

## 🔍 Bug Details

### 🔴 BUG-004 — Job Status Remains 'Draft' After Successful Publish Confirmation *(Critical)*

**Module:** Job Management — Job Status / Publishing

After clicking Publish on a Draft job, a success popup appears saying *"Congratulation, your job is published successfully!!"* — but returning to the jobs list shows the status badge still stuck on **'Draft'** instead of **'Active'**. The system tells the user the job is published, but the UI contradicts it.

- **Expected:** Job status updates to 'Active' immediately after successful publish.
- **Actual:** Status remains 'Draft' despite success popup. False confirmation to the user.
- **Impact:** Job seekers may not see the job on the frontend even though the admin believes it is published. Directly affects the core purpose of the plugin.
- **Fix:** Refresh the jobs list or update the status badge in real time after a successful publish API response.
- 🎥 [Video Evidence](https://youtu.be/z3tfKUBNjxM)

---

### 🔴 BUG-005 — Deleted Job Still Appears After Successful Delete Confirmation *(Critical)*

**Module:** Job Management — Delete Job

Clicking Delete on a job shows a success popup: *"Job deleted successfully!! Click ok to continue"* — but after clicking Ok, the deleted job **remains visible** in the jobs list. The UI does not update to reflect the deletion.

- **Expected:** Job disappears from the list immediately after delete confirmation.
- **Actual:** Job still appears. Admin cannot confirm whether deletion actually worked.
- **Impact:** Admins may attempt to delete the same job multiple times, causing confusion and distrust in the plugin's reliability.
- **Fix:** Remove the job card from the UI immediately after a successful delete API response, either by DOM removal or automatic list refresh.
- 🎥 [Video Evidence](https://youtu.be/UokL4QhxF3k)

---

### 🔴 BUG-008 — Candidate Search Crashes Admin Panel on Enter Key *(Critical)*

**Module:** Candidates — Search Functionality

Two problems found in the candidate search:
1. **Search button** shows no results and no feedback at all — silent failure.
2. **Pressing Enter** on the search box causes the entire WordPress admin panel to go completely **blank (white screen)**. URL changes to `admin.php?` with empty query. User must navigate away manually to recover.

- **Expected:** Search button and Enter key should show matching candidates or a 'No results found' message.
- **Actual:** Button does nothing. Enter key crashes the admin panel.
- **Root Cause (Likely):** Enter key submits a form with no action defined, loading `admin.php` with an undefined page parameter.
- **Fix:** Add `preventDefault()` on Enter key in the search input; fix the search button to properly trigger the search API and show results.
- 🎥 [Video Evidence](https://youtu.be/8x986VrATpI)

---

### 🟠 BUG-001 — No Feedback When Connecting with Empty API Key *(High)*

**Module:** Authentication / Settings > API Key

Clicking Connect with an empty API key field does **nothing** — no error message, no loading indicator, no feedback whatsoever.

- **Expected:** A validation error: *"API key is required. Please enter your API key."*
- **Actual:** The button appears to do nothing. Users think the plugin is broken.
- **Fix:** Add client-side validation to check if the API key field is empty before making any API call.
- 🎥 [Video Evidence](https://youtu.be/yVO3Jej1Kh0)

---

### 🟠 BUG-003 — Wrong Toast Notification When Pinning a Job *(High)*

**Module:** Job Management — Toast Notifications

When a job is pinned (after being unpinned), the toast notification incorrectly shows **"Job Unpinned"** instead of **"Job Pinned"**. Both the pin and unpin actions show the same message.

- **Expected:** Pin action → "Job Pinned"; Unpin action → "Job Unpinned".
- **Actual:** Both actions show "Job Unpinned". The message is hardcoded, not dynamic.
- **Fix:** Make the notification message dynamic based on the current toggle state.

---

### 🟠 BUG-007 — Long Job Title Silently Rejected with Wrong Error *(High)*

**Module:** Edge Case / Job Creation — Input Validation

A 300+ character job title is accepted in the input field with no warning or character counter. On save, the system shows *"Please provide job title"* — the same error as an empty field — with no indication that the title is too long.

- **Two Problems:** (1) No character limit enforced on input. (2) Wrong error message on rejection.
- **Fix:** Add a character counter, `maxlength` attribute, and a specific error: *"Job title is too long. Please keep it under X characters."*

---

### 🟡 BUG-002 — Pin Icon Doesn't Update After Unpinning *(Medium)*

**Module:** Job Management — Jobs List

After unpinning a job, the pin icon remains filled/highlighted on the job card. The UI does not visually update to reflect the unpinned state.

- **Fix:** Toggle the pin icon style immediately after a successful unpin API response (filled → outline).

---

### 🟡 BUG-006 — Misleading Error When Script Tag Entered in Job Title *(Medium / Partial Fail)*

**Module:** Security / Job Creation — Input Validation

**Security is intact** — the XSS script (`<script>alert('xss')</script>`) did NOT execute. However, when the input is rejected, the error shown is *"Please provide job title"* — as if the field is empty, which it is not.

- **Fix:** Show a descriptive error: *"Job title contains invalid characters (< > are not allowed). Please use plain text only."*

---

## 📊 Bug Category Overview

| Category | Bug Count | Bug IDs |
|---|---|---|
| UI / State Sync (False Success) | 2 | BUG-004, BUG-005 |
| Input Validation / Error Messages | 3 | BUG-001, BUG-006, BUG-007 |
| UI Feedback / Toast Notifications | 2 | BUG-002, BUG-003 |
| Search / Crash | 1 | BUG-008 |

---

## ✅ Test Results Overview

| Result | Count |
|---|---|
| ✅ PASS | 16 |
| ❌ FAIL | 5 |
| ⚠️ PARTIAL FAIL | 1 |
| **Total** | **22** |

---

## 💡 Insights & Suggestions

**Performance**
- The plugin appears to call the external API on every page load. Adding WordPress transient caching for job listing data would reduce repeated API calls and improve performance.

**UX Improvements**
- Add a setup wizard for first-time users who have not yet connected an API key.
- Show inline validation errors next to each field rather than generic messages.
- Make PRO feature badges clearly link to an upgrade or pricing page.

**Root Cause Pattern**
- BUG-004 and BUG-005 share the same underlying issue: the jobs list component does not re-fetch or update state after a successful API call. A single fix — ensuring the UI syncs with the API response — would resolve both bugs.

---

## 🗂️ My Approach

I reviewed the plugin source code first to understand the main feature areas — including the API class, settings, admin, public, shortcode, Gutenberg blocks, and security-related logic. That review helped me identify bugs directly from code before running the plugin. I then prioritized test cases by risk, focusing on installation, authentication, job management, frontend behavior, candidate management, settings, security, and edge cases.

---

## 🛠️ Environment

| Parameter | Value |
|---|---|
| **Plugin** | easy.jobs v2.7.1 |
| **WordPress** | 6.9.4 |
| **Environment** | LocalWP (localhost) |
| **Test Type** | Manual Testing + Source Code Analysis |
| **OS** | Windows |
| **Date** | April 25, 2026 |

---

## 🔧 Tools Used

| Tool | Purpose |
|---|---|
| LocalWP | Local WordPress environment setup |
| Browser DevTools | Network inspection, console error analysis |
| Source Code Review | Pre-test bug identification from plugin files |
| Manual Exploratory Testing | Bug discovery, reproduction, and documentation |
| YouTube | Video evidence recording and sharing |

---

## 📁 Repository Contents

```
easy-jobs-WordPress-Plugin-QA-Test-Report/
│
├── Task_WordPress_plugin_Report_Ashakuzzaman_Odree.pdf   # Full QA test report (PDF)
└── README.md                                              # This file
```

---

## 🎥 Video Evidence Links

| Bug / Test Case | YouTube Link |
|---|---|
| BUG-001 — Empty API Key | [Watch](https://youtu.be/yVO3Jej1Kh0) |
| TC-007 — Disconnect API Key | [Watch](https://youtu.be/OgL_dbAgZSA) |
| TC-013 — Delete Job | [Watch](https://youtu.be/UokL4QhxF3k) |
| BUG-004 — Draft after Publish | [Watch](https://youtu.be/z3tfKUBNjxM) |
| TC-017 — Rate Candidate | [Watch](https://youtu.be/ch1TT_9F_bw) |
| TC-018 — Pipeline Drag & Drop | [Watch](https://youtu.be/L1KtqmdRtTs) |
| BUG-008 — Candidate Search Crash | [Watch](https://youtu.be/8x986VrATpI) |

---



<br>



## <b>Connect with Me at</b>
<br>
<div align='center'>





<a href="https://www.facebook.com/ashak.odree/" target="blank">
<img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/facebook.svg" alt="ashakuzzaman odree" height="30" width="40" /></a>


<a href="https://www.instagram.com/ashak_odree/" target="blank">
<img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/instagram.svg" alt="ashak_odree" height="30" width="40" /></a>


<a href="https://www.linkedin.com/in/ashak-odree/" target="blank">
<img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/linked-in-alt.svg" alt="ashakuzzaman odree" height="30" width="40" /></a>


<a href="https://twitter.com/ashak_odree" target="blank">
<img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/twitter.svg" alt="@ashak_odree" height="30" width="40" /></a>
	
<a href="https://www.youtube.com/channel/UC8_-lmRrTG990jkiQ7pFsUw" target="blank">
<img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/youtube.svg" alt="Ashak Odree" height="30" width="40" /></a>	

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif">
