---
name: amazon-fnsku-sheet-print
description: Parse shipment spreadsheets where column A is SKU and column H is label quantity, prepare Amazon FNSKU label download batches, count the newest downloaded label PDFs, and print them at 100% scale to a Brother printer. Use when Codex receives a spreadsheet in this fixed layout and needs to turn selected rows into downloaded and printed Amazon FNSKU labels without changing the existing amazon-download-fnsku-labels skill.
---

# Amazon FNSKU Sheet Print

Keep this skill separate from `$amazon-download-fnsku-labels`. Never rename, replace, or edit that skill as part of this workflow.

## Core workflow

1. Read the spreadsheet and use only:
   - column `A` for seller SKU
   - column `H` for number of labels to print
2. Ignore all other spreadsheet columns unless the user explicitly changes the rules later.
3. Determine which rows to use from the user's request.
   - If the user names specific SKUs, use only those SKUs.
   - If the user says to use highlighted rows, inspect the sheet and use only the highlighted rows that match the request.
4. Convert the selected rows into `SKU,quantity` lines.
5. Hand off the actual Amazon download step to `$amazon-download-fnsku-labels`.
6. After download completes, count the newest downloaded label PDFs in the download folder.
7. Use Computer Use to print those newly downloaded PDFs with:
   - printer `Brother MFC-L2690DW`
   - scale `100%`
   - no fit-to-page scaling

## Project path configuration

This skill must work across different devices. Do not hardcode one machine's path as a permanent rule.

Before running the workflow, determine the local Amazon label project root. Prefer this order:

1. A path explicitly given by the user for this device
2. A path already provided in prior task context for this device
3. A likely local project path discovered on disk

The first time this skill is used on a new device, specify the local path of that device's downloaded Amazon label project folder.

The key derived paths vary by device:

- project root: the local `amazon-label-printer` project folder
- labels file: `<project root>/labels.csv`
- downloads folder: `<project root>/downloads`

Read `references/platform-notes.md` when platform-specific handling is needed.

## Spreadsheet rules

- Preserve SKUs exactly except for trimming surrounding whitespace.
- Convert quantities to whole-number label counts.
- Skip blank rows.
- If a selected row has a missing SKU or missing quantity, stop and report the bad row instead of guessing.
- If the user asks for only some SKUs from the sheet, do not include extra rows.

## Download handoff

Use `$amazon-download-fnsku-labels` for the actual Seller Central download run.

When invoking that skill:

- update `<project root>/labels.csv` with header `sku,quantity`
- include only the requested rows
- keep the run in download-only mode unless the user explicitly changes the workflow later
- let the user complete Seller Central sign-in and MFA in the temporary Chrome window
- if the current device does not have the local Amazon label project yet, stop and ask the user for the correct project path or for that project to be copied onto the device first

## Post-download file counting

After the download succeeds, count how many SKUs were downloaded in this batch.

Use this rule:

- the newest downloaded PDFs are at the bottom of the folder in the user's visual workflow
- count from bottom to top using the number of requested SKUs
- if 17 SKUs were requested successfully, the newest 17 PDF files belong to this batch

Prefer verifying the actual newest filenames in the folder before printing so the exact files are known.

## Printing workflow

Use Computer Use for the print step after the PDFs exist in the downloads folder.

Requirements:

- go to the configured downloads folder
- print only the PDFs from the current batch
- use printer `Brother MFC-L2690DW`
- set print scale to `100%`
- do not enable fit-to-page or any scaling mode that changes the label size

If a print dialog opens in a viewer such as Preview or Adobe:

- confirm the correct printer
- confirm `100%` or `Actual Size`
- print the full document for that PDF
- repeat until all batch PDFs are submitted

## Reporting

Report:

- the SKUs selected from the spreadsheet
- the quantities used
- every saved PDF path
- how many batch PDFs were counted for printing
- any SKU download failure
- any print blocker or printer mismatch
