# Universal Download Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the three architecture download cards with one centered `Universal` card while preserving the existing visual language and leaving the current URL as an editable placeholder.

**Architecture:** Keep the existing `.download-grid` and `.download-card` components in `index.html`. Change the grid to a centered single-column layout with a constrained width, then reduce the download markup to one card and replace architecture-specific copy with Universal copy.

**Tech Stack:** Static HTML and CSS in `index.html`; PowerShell static assertions for verification.

## Global Constraints

- Only modify download-section CSS and HTML; do not alter navigation, other page sections, version information, or external assets.
- Keep the existing card icon, button styling, hover feedback, and download metadata.
- Temporarily keep the existing first-card URL as the editable download placeholder.
- The final download container must be a centered single-column layout and remain responsive on mobile.

---

### Task 1: Establish the failing static regression check

**Files:**
- Test: inline PowerShell assertions against `index.html` (no repository test file required)

**Interfaces:**
- Consumes: current `index.html` download-section source.
- Produces: a failing check proving the current page still contains three cards and no Universal card.

- [ ] **Step 1: Run the pre-change assertion**

```powershell
$html = Get-Content -Raw -Encoding utf8 -LiteralPath 'index.html'
$downloadSection = [regex]::Match($html, '<section class="section download[\s\S]*?</section>').Value
$cardCount = ([regex]::Matches($downloadSection, 'class="download-card"')).Count
if ($cardCount -ne 1) { throw "Expected one download card, found $cardCount" }
if ($downloadSection -notmatch '>Universal<') { throw 'Expected Universal label' }
```

Expected: FAIL because the current source contains three cards and architecture-specific labels.

### Task 2: Merge and center the download card

**Files:**
- Modify: `index.html:465-470` for the `.download-grid` layout rules.
- Modify: `index.html:755-780` for the download-card markup.

**Interfaces:**
- Consumes: existing `.download-grid`, `.download-card`, `.icon`, `.abi`, `.size`, and `.btn` styles.
- Produces: one centered card labeled `Universal`, with the current first-card URL retained as a placeholder.

- [ ] **Step 1: Change the grid to one centered constrained column**

Update `.download-grid` so its columns are `minmax(0, 420px)`, it remains centered with `margin: 0 auto`, and its content is centered with `justify-content: center`.

- [ ] **Step 2: Remove the two additional architecture cards**

Keep one `.download-card` and remove the `armeabi-v7a` and `x86_64` card blocks.

- [ ] **Step 3: Replace architecture-specific card copy**

Use the following content in the remaining card while preserving its icon and button:

```html
<div class="abi">Universal</div>
<div class="size">全架构通用包</div>
<a class="btn btn-primary" href="https://ghproxy.net/https://github.com/KasumiAmi/YunoSeekAPP/releases/download/v1.0.5_0727/app-arm64-v8a-release.apk">下载</a>
```

### Task 3: Verify the static page contract

**Files:**
- Test: inline PowerShell assertions against `index.html`.

**Interfaces:**
- Consumes: the modified download section.
- Produces: evidence that the section has one Universal card, centered single-column CSS, preserved metadata, and balanced section markup.

- [ ] **Step 1: Run the focused assertions**

```powershell
$html = Get-Content -Raw -Encoding utf8 -LiteralPath 'index.html'
$downloadSection = [regex]::Match($html, '<section class="section download[\s\S]*?</section>').Value
if (([regex]::Matches($downloadSection, 'class="download-card"')).Count -ne 1) { throw 'Download card count is not 1' }
if ($downloadSection -notmatch '>Universal<') { throw 'Universal label is missing' }
if ($downloadSection -match 'arm64-v8a|armeabi-v7a|x86_64') { throw 'Architecture-specific labels remain in the download card' }
if ($html -notmatch 'grid-template-columns:minmax\(0,420px\)') { throw 'Single-column constrained grid is missing' }
if ($html -notmatch 'justify-content:center') { throw 'Centered grid alignment is missing' }
if ($downloadSection -notmatch 'class="download-meta"') { throw 'Download metadata was removed' }
if (([regex]::Matches($html, '<section\b')).Count -ne ([regex]::Matches($html, '</section>')).Count) { throw 'Section tags are unbalanced' }
Write-Output 'Universal download assertions passed.'
```

Expected: all assertions pass with exit code 0.

- [ ] **Step 2: Review the final diff**

```powershell
git diff --check
git diff -- index.html
```

Expected: no whitespace errors; the diff is limited to the download CSS and HTML changes described above.

### Task 4: Commit the implementation

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Stage the page change**

```powershell
git add -- index.html
```

- [ ] **Step 2: Commit the page change**

```powershell
git commit -m "feat: merge download cards into universal option"
```
