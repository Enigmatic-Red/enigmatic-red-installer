# Enigmatic Red — the beta installer

The files the [Enigmatic Red](https://enigmaticred.com/) research-lab beta offers
for download, kept in a public repository as well as on the site. The download
buttons on <https://enigmaticred.com/#beta> lead here first; the site still
serves its own byte-identical copies.

| File | Bytes | SHA-256 |
| --- | --- | --- |
| [`EnigmaticRed-Setup.exe`](EnigmaticRed-Setup.exe) | 7,699,098 | `8759847e739ea119d5e6bfbbecfacaf24d3e2c9521793e6fe42d025194307844` |
| [`enigmatic_red-0.1.0b4-py3-none-any.whl`](enigmatic_red-0.1.0b4-py3-none-any.whl) | 508,967 | `4ae0b6b7aa5b9f4646d7d0d3fa66e980f188cf715483bb648a370d3a2e82d17a` |

Both are attached to the [v0.1.0b4-5 release](../../releases/tag/v0.1.0b4-5) as well,
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

The installer fetches `enigmatic_red-0.1.0b4-py3-none-any.whl` from
`enigmaticred.com/assets/` and checks it against a digest compiled into the
executable, so a tampered wheel fails the install rather than running.

## What changed in 0.1.0b4

Two defects in the shipped CLI, and one new budget level.

`red scan` printed only the first token of each line it parsed out of a tool's
output, so a wrapped or padded line lost everything after it. Every parsed line
is now printed as it arrived - which matters most where a tool prints its
finding and its evidence on the same line.

A console script is now resolved before it is run: the interpreter's virtualenv
and `sysconfig` script directories are checked first. A virtualenv that has an
`httpx.EXE` beside the module used to execute that file in place of httpx;
`red doctor` now reports the shadow instead of running it.

`--budget controlled_poc` is the one budget that reaches the `exploit_safe`
class: one exact, non-destructive proof of concept, defaulting to three
targets, twenty minutes and twenty actions. It never reaches `forbidden`, and
the authority chain still asks for approval - a budget says how much is
allowed, never that it is allowed.

The release check clears `build/lib` before every build and refuses a wheel
that carries the private tier under either spelling, so a package deleted from
the tree can no longer ship out of a stale build tree.

The full suite (1,885 tests), the page generators and the digest checks pass.

## macOS and Linux — the wheel

The wheel is the same release of the same agent, packaged rather than run. It
installs the agent, not a model.

```sh
python3 -m venv ~/.local/share/enigmaticred/venv
~/.local/share/enigmaticred/venv/bin/python -m pip install \
  https://enigmaticred.com/assets/enigmatic_red-0.1.0b4-py3-none-any.whl
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
