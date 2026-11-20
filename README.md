# Enigmatic Red — the beta installer

The files the [Enigmatic Red](https://enigmaticred.com/) research-lab beta offers
for download, kept in a public repository as well as on the site. The download
buttons on <https://enigmaticred.com/#beta> lead here first; the site still
serves its own byte-identical copies.

| File | Bytes | SHA-256 |
| --- | --- | --- |
| [`EnigmaticRed-Setup.exe`](EnigmaticRed-Setup.exe) | 7,699,273 | `af60e00d2f3f1255eb0da73d044f849254162a1dccd86a5fa2eba187d1331c9e` |
| [`enigmatic_red-0.1.0b3-py3-none-any.whl`](enigmatic_red-0.1.0b3-py3-none-any.whl) | 488,101 | `953c85faa6f51d82dca58e072db11f02ce31039920211a7c8904707f75363370` |

Both are attached to the [v0.1.0b3 release](../../releases/tag/v0.1.0b3) as well,
which is what a download click on the site resolves to. `SHA256SUMS.txt` carries
the same two numbers in the format `sha256sum -c` reads, and is byte-identical to
the one the site serves.

This repository holds **releases, not source**: the agent's source tree is
private and is not published here. It exists so the published bytes have a home
outside the web host, with a public history and a digest anyone can compare
against the site. The wheel is the same packaged release the beta offers for
macOS and Linux; it is the shipped product, not a checkout.

## Windows — the installer

`EnigmaticRed-Setup.exe` is the one file that installs everything. One
double-click installs Python if the machine has none, the agent, the local UI
server and a local model, and adds separate Terminal and **Enigmatic Red (Agent
UI)** shortcuts. The Agent UI binds to loopback and uses that install's scope,
authorization, safety guard and audit log.

Its first-run setup asks you to review the scope and record written
authorisation for the target you own before any active assessment. The installer
is not a sandbox: intrusive actions still require your approval, and every run
and refusal is written to the audit log.

The installer fetches `enigmatic_red-0.1.0b3-py3-none-any.whl` from
`enigmaticred.com/assets/` and checks it against a digest compiled into the
executable, so a tampered wheel fails the install rather than running.

## What changed in 0.1.0b3

This build brings cited retrieval into planning, not only chat. When an
objective names a weakness, the planning prompt may carry at most two
licence-reviewed records from the local store - OWASP WSTG 4.2, ASVS 5.0.0,
CWE 5.0 or the NIST NVD recent feed - each with publisher, version, URL,
licence and record id. The block is marked untrusted and advisory: a reference
describes a weakness class, never evidence that a target has it, and every claim
still needs the audit run id of the step behind it. The same corpus remains
available in the chat surface.

The local ranker behind both surfaces now splits compound terms, so a query
spelled "cross-site request forgery" finds the guide heading spelled "cross
site"; scores with BM25, so a term in most records counts for little and a long
record does not win by length; drops a hit worth less than a quarter of the best
one; and limits one corpus to two of the three results, so three near-identical
advisories cannot fill an answer. `corpora/retrieval_eval.py` measures that
against the ranker it replaced on a synthetic first-party set (24 records, 11
labelled queries): precision@3 0.846 vs 0.750, recall@3 0.957 vs 0.913,
citation completeness 11/11. Those are lexical retrieval numbers, not a model
benchmark.

No corpus text is bundled with the wheel, evaluation fixtures never enter the
retrieval index, and the release check refuses a wheel that carries the private
tier. The full suite (1,588 tests), the page generators and the corpus manifest
checks pass.

## macOS and Linux — the wheel

The wheel is the same release of the same agent, packaged rather than run. It
installs the agent, not a model.

```sh
python3 -m venv ~/.local/share/enigmaticred/venv
~/.local/share/enigmaticred/venv/bin/python -m pip install \
  https://enigmaticred.com/assets/enigmatic_red-0.1.0b3-py3-none-any.whl
~/.local/share/enigmaticred/venv/bin/red init
```

On macOS or Linux a model is then your own: point `.env` at one you already
run. The Windows installer is the path that sets a local model up for you.

## Check what you downloaded

PowerShell:

```powershell
Get-FileHash .\EnigmaticRed-Setup.exe -Algorithm SHA256
```

or, without PowerShell:

```bat
certutil -hashfile EnigmaticRed-Setup.exe SHA256
```

Compare the result with `SHA256SUMS.txt`, the table above, and the numbers
printed under the download buttons on the site. If all three agree, the bytes
are the bytes that were published; none of them says who built the file, because
nothing here is code-signed yet.

## Windows will warn you

Windows shows a SmartScreen warning for any program downloaded in a browser that
has no code-signing certificate behind it, and this one has none yet. The
warning is about the missing signature, not about anything found in the file.
There are two ways round it:

1. Check the SHA-256 as above. A copy that arrived altered cannot match it.
2. Skip the executable: the wheel in this repository is the same release,
   packaged rather than run, and installing a wheel raises no warning.

When the build does sign the file, the signature is checkable with
`Get-AuthenticodeSignature`, and the digest will change because a signature
changes the bytes.

## The beta itself

- Product page and scope: <https://enigmaticred.com/>
- Download section: <https://enigmaticred.com/#beta>
- Hosted chat, questions only, bring your own model key: <https://beta.enigmaticred.com/>
- Terms and privacy: <https://enigmaticred.com/terms/>, <https://enigmaticred.com/privacy/>

Free research beta: no accounts, no billing. Active assessment runs on your
machine, where scope, written authorisation, the guard and the audit trail apply.
