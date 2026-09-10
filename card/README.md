# Staff card pages

Self-contained pages opened by a tap (NFC) or scan (QR) of a printed
PeakTravel staff card. Each person gets one folder here, named by their slug:

    card/<slug>/index.html   the page a tap or scan opens
    card/<slug>/contact.vcf  vCard 3.0, imports natively on iOS and Android

The slug is the URL, so `card/rawezh-qasm/` resolves at
`https://peaktravel.net/card/rawezh-qasm/`.

## Live

| Slug           | Name        | Position            | ID      |
|----------------|-------------|---------------------|---------|
| `rawezh-qasm`  | Rawezh Qasm | Ticketing Supervisor| PT-0429 |

## Rules

- **Never rename a folder.** The slug is printed on the physical card and
  written into its NFC chip, so the URL has to resolve forever. If a slug ever
  must change, leave a permanent redirect from the old one.
- **No listing page at `/card`.** `card/index.html` redirects to the homepage
  on purpose — a listing would expose the whole staff roster. Keep it that way,
  and keep the staff roster out of any file served from this folder.
- **Do not extract assets or bundle.** Each `index.html` is self-contained: the
  portrait is embedded as a data URI and all styles are inline. There is nothing
  to build, compile or install.
- **Keep `<meta name="robots" content="noindex">`.** It is deliberate.

## Adding a person

1. Create `card/<slug>/` and drop in the generated `index.html` and
   `contact.vcf`.
2. Add a row to the table above.
3. Open the URL on a real iPhone and a real Android and confirm
   **Save to contacts** opens the contact sheet. Cards are printed against
   these URLs, so verify before any print run.

## Serving `contact.vcf`

GitHub Pages serves these as `text/x-vcard`, set from the file extension. That
is correct and needs no change — Android's contact importer registers for
`text/x-vcard`, `text/vcard` and `text/directory` alike, and iOS accepts both
vCard types. Verified 2026-09-10.

**Never put a `download` attribute on the vCard link.** It tells the browser to
write the file to storage instead of handing it to the OS, which overrides
content-type handling entirely — Android then saves a dead file instead of
opening the contact importer, and iOS 13+ saves to Files instead of showing the
contact sheet. No amount of server MIME configuration can undo it.

Expect a platform difference when testing: iOS Safari shows an inline contact
preview, while Android has none — it routes the file through Downloads or an
"Open with" chooser into Contacts. That is normal, not a bug.

To re-check the type after a deploy (PowerShell):

    curl.exe -sI https://peaktravel.net/card/rawezh-qasm/contact.vcf | findstr /i "content-type"

## Not built yet

A `/verify/<idNumber>` page showing photo, name, department and active/expired
only — no contact details — so a client can confirm someone is staff. That one
needs somewhere to store card status, so it is a separate decision.
