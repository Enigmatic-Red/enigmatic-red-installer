# Enigmatic Red — the installer

The files the [Enigmatic Red](https://enigmaticred.com/) research-lab release
offers for download, kept in a public repository as well as on the site. The
download buttons on <https://enigmaticred.com/#beta> lead here first; the site
still serves its own byte-identical copies.

| File | Bytes | SHA-256 |
| --- | --- | --- |
| [`EnigmaticRed-Setup.exe`](EnigmaticRed-Setup.exe) | 7,703,969 | `2b2ff3027fddef3a130a72ce0f1b6ab1e9a30696d9ffb83b8f48de06a9d8b1ce` |
| [`enigmatic_red-0.1.0-py3-none-any.whl`](enigmatic_red-0.1.0-py3-none-any.whl) | 525,992 | `eee1165f5bf751351122a13b4dea565d1350d73d9eca2b06e68de88c8d372eae` |

Both are attached to the [v0.1.0 release](../../releases/tag/v0.1.0) as well,
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
server, and writes one **Enigmatic Red** shortcut to the Start Menu and the
Desktop. The Agent UI binds to loopback and uses that install's scope,
authorization, safety guard and audit log.

A model is bring-your-own by default: the installer writes the BYOK keys and an
OpenAI-compatible endpoint into `.env`. Ollama and local weights are installed
only when `--local-model` is asked for, and the tag pulled is `--model`
(`llama3.2:3b` unless overridden). `EnigmaticRed-Setup.exe --dry-run` prints the
whole plan and changes nothing; `--help` lists the rest.

Its first-run setup asks you to review the scope and record written
authorisation for the target you own before any active assessment. The installer
is not a sandbox: intrusive actions still require your approval, and every run
and refusal is written to the audit log.

The installer fetches `enigmatic_red-0.1.0-py3-none-any.whl` from
`enigmaticred.com/assets/` and checks it against a digest compiled into the
executable, so a tampered wheel fails the install rather than running. It needs
no elevation: the private venv and the download cache live in
`%LOCALAPPDATA%\EnigmaticRed`, per user, and nothing is registered with
Add/Remove Programs.

## What changed in 0.1.0

0.1.0 is the first release that is not a beta. The installer and the wheel are
built from one tree and pinned to each other by digest, a CI job installs the
published wheel into a fresh environment and drives the interface before the tag
is worth cutting, and `COMPATIBILITY.md` writes down what 0.1.x keeps — the
command line, the exit codes, the config home, the audit log and the
distribution.

Carried in from the last beta (0.1.0b5): setup writes and saves the scope as
YAML where you are standing, the sidebar's scope card can be edited and reloaded
in place, and a turn that asks what your configured scope allows answers from
the chain in force instead of sending you to a terminal. A reading of your
sentence becomes a mission's anchor only when the target is a system and is the
word you typed — not the model's paraphrase — and a phrase that is neither is
answered as a question, so nothing runs.

## macOS and Linux — the wheel

The wheel is the same release of the same agent, packaged rather than run. It
installs the agent, not a model.

```sh
python3 -m venv ~/.local/share/enigmaticred/venv
~/.local/share/enigmaticred/venv/bin/python -m pip install \
  https://enigmaticred.com/assets/enigmatic_red-0.1.0-py3-none-any.whl
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
changes the bytes. `installer/sign.ps1` in the source tree is that hook: it runs
inside the build before the digest is computed, and skips with a sentence when
no certificate is configured.

## The release itself

- Product page and scope: <https://enigmaticred.com/>
- Download section: <https://enigmaticred.com/#beta>
- Hosted chat, questions only, bring your own model key: <https://beta.enigmaticred.com/>
- Terms and privacy: <https://enigmaticred.com/terms/>, <https://enigmaticred.com/privacy/>

Free research release: no accounts, no billing. Active assessment runs on your
machine, where scope, written authorisation, the guard and the audit trail apply.

