---
name: amazon-download-fnsku-labels
description: Download Amazon FNSKU product labels from Seller Central for a supplied list of seller SKUs. Use when a user wants to look up SKUs in Seller Central, retrieve or print FNSKU/barcode labels, save label PDFs, or organize label downloads for Amazon FBA inventory.
---

# Download Amazon FNSKU Labels

Use the Browser skill's `tab.playwright` API for all Seller Central interactions. Do not use shell-installed Playwright, browser profiles, cookies, saved passwords, or direct HTTP requests to Seller Central. Work only in the user's existing interactive session; do not request, handle, or store credentials, MFA codes, or session data.

## Playwright operating rules

- Initialize and select the browser according to the Browser skill, then obtain a tab and use its `playwright` API for inspection, navigation, clicks, and form input.
- Read the current page before acting. Use stable, user-visible locators (role, accessible name, label, or text) and scope them to the active page or dialog. Do not rely on fragile CSS classes, arbitrary indexes, or guessed URLs.
- Wait for a relevant page state or dialog instead of fixed delays. Use a browser download event only as an additional signal; Seller Central can hand the file to Chrome without emitting that event.
- Treat a CAPTCHA, sign-in, MFA, unexpected confirmation, or automation-blocked page as a handoff: stop interacting and ask the user to complete it in the selected browser, then resume from the current page.
- Do not bypass Amazon controls, anti-bot checks, rate limits, or access restrictions.

## Collect and confirm the input

- Accept pasted SKUs, a CSV/XLSX column, or a plain-text file. Preserve the seller SKU exactly; trim surrounding whitespace and ignore blank rows.
- Confirm the relevant Seller Central marketplace when it is ambiguous. Do not assume a SKU in one marketplace exists in another.
- Clarify only material label choices before download: label format/page layout, quantity per SKU, and whether one PDF per SKU or combined downloads are wanted. If the user provides no preference, use Seller Central's current default label format and one label per SKU.
- Unless the user specifies another destination, collect only the current run's matched PDFs into `FNSKU labels/current/` in the current workspace. Create `FNSKU labels/` and `FNSKU labels/current/` first if they do not already exist. Keep the originals in Chrome Downloads.
- Treat `FNSKU labels/current/` as a replaceable working folder for the latest batch, not a long-term archive. At the start of each run, remove stale PDFs from that folder before copying the new batch results. Do not delete anything from Chrome Downloads.
- Do not skip Seller Central just because a matching PDF exists from an earlier run. The user's default experience is to receive a fresh current batch for the SKUs they just requested. Only reuse an existing workspace PDF when the user explicitly asks to reuse existing labels instead of downloading fresh ones.

## Download workflow

1. Before the first final print click, snapshot the existing PDFs in the user's Chrome Downloads folder (full path, size, and modification time). State that this requires Downloads-folder access; request it if it has not already been authorized. Set a batch start time.
2. Prepare `FNSKU labels/current/` as the latest-batch folder. Create `FNSKU labels/` and `FNSKU labels/current/` if either folder is missing, then remove only stale PDFs inside `current/`, leaving other folders and Chrome Downloads untouched. If preserving previous workspace copies is required, move them to a timestamped subfolder only when the user explicitly asks for archiving.
3. Navigate with Playwright to Seller Central and confirm that the correct marketplace is active. If sign-in or MFA is required, ask the user to complete it in the selected browser and continue only after they say it is ready.
4. Use Playwright to operate Seller Central's inventory search or FNSKU/label-printing flow. On SKU Central, expand `See more`, choose `Print item labels`, then identify the newly opened `printitemlabel` tab by matching its URL to the seller SKU. Prefer native bulk selection or batch label creation when the UI supports all requested SKUs; otherwise process the list one SKU at a time.
5. Verify the matched listing before requesting labels: seller SKU, product title or ASIN, and the displayed FNSKU must correspond to the intended product. Stop and flag any ambiguous, inactive, or unmatched SKU rather than guessing.
6. On each label page, verify that the row contains the intended seller SKU and that the label count and paper format match the agreed choices. Do not assume that the label page's default is correct.
7. Ask for action-time confirmation immediately before clicking the final `Print Item Labels` button, unless the user explicitly authorized label generation in the current task.
8. Click the final `Print Item Labels` button exactly once per SKU. Seller Central may show a standard barcode-cover reminder immediately afterward; dismiss that alert once with the browser dialog API. Treat dismissing the reminder as part of the original submission, not as a cancellation, and never click `Print Item Labels` again after the alert closes.
9. Use a short, bounded observation to check for a generated PDF tab, changed page state, or browser download event. If none is observable, continue without a second print click; Chrome may still have received the file.
10. After all requests, inspect Downloads again and select only completed `.pdf` files that are new or changed since the snapshot and batch start. Ignore `.crdownload` files. Sort candidate PDFs by creation/modification time, newest last, to isolate only the files from the current run. Do not select an earlier `products (n).pdf` merely because its filename resembles Amazon's output.
11. Expect one new PDF per submitted SKU unless Seller Central visibly produced a combined file. When one-file-per-SKU output is expected, sort the new PDFs by creation/modification time and match them to the corresponding one-time print submissions in chronological order. If the number of new PDFs does not match the submitted SKU count, do not guess: preserve the candidate files, report the mismatch, and ask the user to identify the files.
12. Copy—never move or delete—each confidently matched PDF into the existing or newly created `FNSKU labels/current/` folder, naming it `<seller-SKU>--<quantity>-labels.pdf`. Because the current folder was cleared for this run, do not add timestamp suffixes unless two requested rows have the same seller SKU and quantity in the same batch. Verify each copy by comparing its size (and a checksum when practical).
13. Keep a result table with seller SKU, resolved FNSKU, product identifier/title, paper format, label count, source download filename, copied workspace path, and status (`downloaded`, `needs user download check`, `not found`, `ambiguous`, `error`).

## Completion checks

- Report a file as `downloaded` when it is confidently matched and copied from Chrome Downloads into the workspace; otherwise use `needs user download check`. The Browser Playwright API alone cannot save the file, so use the Downloads-folder collection step rather than retrying Seller Central.
- Report successful downloads and every exception. Never silently skip a requested SKU.
- On a timeout or browser interruption, do not resubmit a label request. Reconnect to the existing `printitemlabel` tab, inspect its current state, and preserve it as a handoff for the user if the outcome remains uncertain. Do not use an alert or missing download event as a reason to issue a second print click.
- Leave Chrome Downloads files in place. It is OK to clear stale PDFs from `FNSKU labels/current/` at the start of a run because that folder is only the latest-batch workspace. Do not delete labels, listings, inventory, shipments, or browser data.

## Seller Central changes

Seller Central navigation and UI labels vary by marketplace and account. Follow the currently visible native flow rather than inventing menu names. The only intended side effect is generating/downloading labels; do not edit listing data, create shipments, change inventory, or submit unrelated actions.
