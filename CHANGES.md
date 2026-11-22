# Garuda Construction Service — Update (Nov 2026)

1. Return auto-fill shows only bills that still have items to return (fully-returned bills hidden).
2. Pending tab: "Settle Now" on every pending bill (dispatch, return, site) -> Payment Receipt bill (RCPT-xxxxxx) with PDF/WhatsApp. All receipts listed under Pending tab.
3. Qty boxes start empty (placeholder 0); typing 10 gives 10, never 100/010. Applies to every number box.
4. இறக்குக்கூலி (Unloading) added to Return bills and Site Ledger returns (bill, totals, Excel).
5. New "Customers" tab: auto from bills + manual add/edit; per-customer bills, payment history, 30-day rent. Bill forms auto-fill known customers.
6. Monthly Bills (30-day collection) tab + site panel button; new "(c) Copyright" tab with company details and logo (embedded).
7. Lifelong-safety: atomic saves, previous-copy, daily (60 days) + monthly (forever) compressed backups in "Backups" folder, auto-recovery from damaged file, never overwrites a damaged file, flush-on-close, indexed lookups, list caps (tested with 30,000 bills / 24,000 returns / 4,000 sites).
Also fixed: same-day return, advance deducted twice on partial returns, stale qty when switching bills, bill number skipped by preview, 2nd partial-return over-qty.
Tests: node tests/e2e.js (needs Playwright), node tests/storage_test.js

## Update 2
- Site Ledger partial return bill: every line shows OUT (rending) date -> return date, days, rate and amount per lot. Full site bill lists every lot the same way.
- Loading / Unloading shown in ONE row. A4 sheet now holds 16 rows (was 12) so fewer pages.
- Neater screens: Site Ledger tabs (Find / New / All bills) and per-site tabs (Summary, OUT, IN, Missing, Bills, Payments); dispatch/return forms have the summary in a sticky right column, stock panel collapsible.
- Employees: salary settings (per day / per hour, OT rate, allowance), documents view/download, advances, attendance edit + OT, payment history and attendance download (Excel/PDF), auto payroll with manual edit, salary slip with OT and advance.

## Update 3 — Network Sync + bug sweep
Network Sync
- FIXED false "conflict" on slow links (a device's own change echoed back as another device's change; the sync then stopped).
- FIXED data-loss risk: choosing "keep my unsynced changes" at start-up could upload an EMPTY database (exe mode).
- Conflict dialog now ALWAYS saves the version that gets replaced to Backups (Conflict_*.json.gz) — restore it from the Backup tab.
- Version guard: PCs running an older build can no longer overwrite the host's newer data (HTTP 426 + clear message).
- gzip transfer + cached host snapshot + size-based timeouts (large databases no longer time out forever).
- New: "Test connection" button with plain-language reasons (wrong PIN / host app not running / NetBird not connected / version mismatch), host shows its IP addresses, optional LAN access (PIN required), warning when no PIN is set, port-in-use message.
- Window can be closed while changes are unsynced (asks first; changes stay saved locally).
- Screens refresh after data arrives from another PC.
Other bugs fixed
- Restoring an old backup (or receiving old data) now gets every collection + a safe bill counter (previously new screens could crash).
- A bill can no longer be saved with more pieces than are in stock (qty was silently cut while the total stayed).
- A failed save no longer burns a bill number; Excel/Full backup export is compact (3x smaller).
- Number boxes: select-on-focus no longer races with fast typing.
Tests: tests/sync_test.js, sync_test2.js (real host + client over HTTP), fuzz.js (random operations + stock/balance invariants), bugs.js.

## Update 4
- Number boxes: up/down arrows kept (always visible). Fixed the real bug: a mouseup handler stopped the arrow's auto-repeat from ending, so numbers kept changing on hover. Mouse-wheel can no longer change a focused number.
- Site Ledger bills: lost (fixed-amount) items now appear as lines "⚠ காணவில்லை" with qty, OUT date → date and amount (no longer hidden inside சேதார தொகை). Found items show "திரும்ப வந்தது" with OUT → IN date, days, rate and amount. Running missing items show OUT → today.
- Missing tab: each entry shows full details; ✏ Edit (date / note / fixed amount / found date) and 🗑 Delete-Undo (stock reversed, qty returns to pending). Missing Items Bill PDF has OUT, IN, days, rate, status, amount.
- Mark / settle / edit / delete missing now refreshes Site Ledger, bill lists, stock, pending and any open full-bill view.
- Small Construction missing tab: every settlement can be undone from "Recent missing entries" (restores bill + stock; newest first).
- Salary slip redesigned: logo, company address, GSTIN, earnings/deductions tables, net pay, signatures (screen + PDF).
- Dashboard 'Rented vs Available Snapshot' now lists ALL products (was limited to first 8) and shows rented / total under each.
- Missing Items Bill PDF now uses the same sheet layout as all other bills (logo, GSTIN, Tamil, OUT -> IN dates, days, rate, amount).
- Build workflow: every push to main now also publishes a direct-download pre-release ('Latest build') with the installer .exe + SHA256.txt, so the installer no longer has to be downloaded inside the Actions zip.
- Small cleanups: duplicate object keys in site IN-bill preview, safe guard on restoreAutoBackup.
- ROOT-CAUSE FIX (old bill layout): a return bill with 'fully paid' balance (and any sheet HTML that is not valid XML) made the new bill renderer fail, and the app silently fell back to the OLD layout. Fixed the invalid <br> tag, added an XML safety net, and removed the silent old-layout fallbacks (Return bill, Site full bill, Missing bill) so every bill always uses the new sheet format.
- New workflow store-msix.yml: builds a Microsoft Store (MSIX/APPX) package. Microsoft signs Store packages for free (individual developer registration is free), so users install from the Store with no Chrome/SmartScreen/Defender warning.

## Update 5
- Small Construction missing / lost: each settlement is now stored with full details (item, qty, OUT date, date, days, rate, amount). The return bill lists lost items as "⚠ காணவில்லை" lines with the fixed amount and found items as "திரும்ப வந்தது" with days x rate; the raw "[Marked LOST ... ]" note no longer appears in the Site box. The lost / missing charge is its own row and is included in the bill total (bill math verified on 6 random data sets, ~440 settlements).
- New Missing / Lost Items Bill for Small Construction (view + PDF) - in the Missing tab (pending cards + a history list of ALL missing bills), in Completed Bills / Returns cards, and in the Customers tab bill history. Site Ledger: View + PDF buttons in the Missing tab, a card in Bills & History, and an entry in the Customers tab bill history.
- Site Ledger: "Close Site" is now disabled while any balance (incl. lost amount) is still due; the check is also enforced in the close function.
- Contacts: new View screen per contact - trips (date, site / location, amount, note, paid / due) with add / edit / delete, current location with history (a new trip automatically updates the vehicle location), edit contact details, totals.
- Bills: long addresses are wrapped evenly over the lines and the font shrinks to fit instead of being cut off; Return bill Site box now shows the original bill + site address.
- Bill view: new 🖨 Print button (also Ctrl+P while a bill is open) for every bill type — dispatch, return, site, missing/lost, receipts, salary slip. Multi-page bills print one sheet per A4 page.
- Print buttons added next to every bill download: Site Ledger (Full Site Bill, Missing/Lost bill in Missing tab and Bills & History) and Payroll salary-slip lists; every bill view window (dispatch, return, site, receipts, monthly, missing/lost, salary) already has 🖨 Print + Ctrl+P.
- Missing tab (Small Construction): old missing/lost entries (made before structured logging) now also get a Missing / Lost Items Bill - history list with View/Download, a Bill button on each 'Recent missing entries' row, bill button on return cards, and Customers tab history.
- Print now opens an in-app Print Preview first (all pages, zoom - / +, 🖨 Print, ⬇ PDF, Esc to close; Ctrl+P inside the preview prints). Windows' own print window cannot show a preview inside Electron, so the preview is built into the app.
- Site Ledger: Find Site search now works like Small Construction — suggestions appear after 2 letters (site no / name / mobile / place), showing status, balance due and pending pcs; click or ↑ ↓ + Enter opens the site directly; one match + Enter opens it.
