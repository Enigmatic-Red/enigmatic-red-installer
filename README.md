# Enigmatic Red — the installer

The files the [Enigmatic Red](https://enigmaticred.com/) research-lab release
offers for download, kept in a public repository as well as on the site. The
download buttons on <https://enigmaticred.com/#beta> lead here first; the site
still serves its own byte-identical copies.

| File | Bytes | SHA-256 |
| --- | --- | --- |
| [`EnigmaticRed-Setup.exe`](EnigmaticRed-Setup.exe) | 8,116,352 | `217acc0385df57585efa059427e4dd4ce367b3971aca4e783495bb24b2cf7398` |
| [`enigmatic_red-0.1.0-py3-none-any.whl`](enigmatic_red-0.1.0-py3-none-any.whl) | 538,245 | `218a3a772aabefca66cca39ccb87f62ce2487567d7c32128ff13153d508ec995` |
| [`SHA256SUMS.txt`](../../releases/download/v0.1.0/SHA256SUMS.txt) | 192 | `eb938dbc9451532fb6dd4733cab3f072ce3b7578bdb23f6686280bb65af24eae` |

Both are attached to the [v0.1.0 release](../../releases/tag/v0.1.0) as well,
which is what a download click on the site resolves to. `SHA256SUMS.txt` carries
the same two numbers in the format `sha256sum -c` reads, and is byte-identical to
the one the site serves.

The shipped scope is limited to `localhost` and `127.0.0.1`. The ignored,
operator-local `config/scope.yaml` is excluded from the wheel, which ships only
the loopback-only `config/scope.example.yaml` template. The newly supplied transparent rotary-telephone logo (768?768 PNG) is used
byte-for-byte in the Agent UI; a matching multi-resolution Windows icon is
embedded in the installer and used by its shortcuts. The refreshed responsive
desktop interface respects reduced-motion preferences.

This repository holds **releases, not source**: the agent's source tree is
private and is not published here. It exists so the published bytes have a home
outside the web host, with a public history and a digest anyone can compare
against the site. The wheel is the same packaged release the beta offers for
Linux; it is the shipped product, not a checkout.

## Windows — the installer

`EnigmaticRed-Setup.exe` is the one file that installs everything. One
double-click installs Python if the machine has none, the agent, the local UI
server, and writes one **Enigmatic Red** shortcut to the Start Menu and the
Desktop. The Agent UI binds to loopback and uses that install's scope,
authorization, safety guard and audit log.

Local Ollama is the default: the installer downloads the CPU-friendly
`llama3.2:3b` model (about 2 GB) and writes local endpoint/model settings.
`--byok` instead configures Featherless and downloads no model weights. The
installed Agent UI also offers other OpenAI-compatible provider presets and
custom endpoints, editable model IDs and optional model discovery. Hosted
providers must support Chat Completions; agent runs also need tool calling.
`EnigmaticRed-Setup.exe --dry-run` prints the whole plan before it begins.

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

Fresh installs now default to local Ollama (`llama3.2:3b`). The desktop API & Models screen can switch between Ollama, OpenAI-compatible provider presets, or a custom endpoint, and lets the operator edit both model IDs and discover available models. The key stays in local configuration and is never returned to the page.

Carried in from the last beta (0.1.0b5): setup writes and saves the scope as
YAML where you are standing, the sidebar's scope card can be edited and reloaded
in place, and a turn that asks what your configured scope allows answers from
the chain in force instead of sending you to a terminal. A reading of your
sentence becomes a mission's anchor only when the target is a system and is the
word you typed — not the model's paraphrase — and a phrase that is neither is
answered as a question, so nothing runs.

The 0.1.0 files above were rebuilt on 2026-11-25 to carry the operator's own
command line. `command` runs one program with its own arguments as a single
argv, with no shell, and the guard classes the line by the binary it names: an
`nmap` line is active, while an unregistered or intrusive program stops for the
`y`/`n` on that exact line, and a pipe, a redirect or a second host is refused
rather than interpreted. In the local Agent UI the same line is typed into the
run bar and posted to `/api/exec`, which is offered only where a run is allowed.

## Linux — the wheel (command line)

The wheel is the same release of the same agent, packaged rather than run. It
installs the agent's command line, `red`, not the Agent UI window and not a
model.

```sh
python3 -m venv ~/.local/share/enigmaticred/venv
~/.local/share/enigmaticred/venv/bin/python -m pip install \
  https://enigmaticred.com/assets/enigmatic_red-0.1.0-py3-none-any.whl
~/.local/share/enigmaticred/venv/bin/red init
```

If your distribution marks the system Python as externally managed (PEP 668),
install with `pipx install` or `uv tool install` against the same URL instead
of a hand-made venv.

The wheel defaults to local Ollama but does not install the Ollama service or
pull model weights. Install Ollama and pull `llama3.2:3b`, or configure an
OpenAI-compatible endpoint in `.env`; `red key` stores your hosted credential
without putting it in an argument. The Windows installer is the one-click local
model setup and the only release that includes the Agent UI window.

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


## Release history

Every release is a tag with its own notes: the files that version shipped, with
their digests, and what it could do. In summary:

| Version | Published | What that version did |
| --- | --- | --- |
| `v0.1.0` | 2026-11-25 | Corrected stable build under the existing tag: local Ollama by default, configurable OpenAI-compatible providers/endpoints/models, API-key controls, and the same release wheel for Linux CLI. Includes the guarded command feature, loopback-only shipped scope, the supplied installer/UI logo, and a refreshed responsive desktop interface; the files and digests above are this build. |
| `v0.1.0b5` | 2026-11-23 | Last beta: the `controlled_poc` budget, `red scan` printing every parsed line whole instead of its first token, and console scripts resolved before they run, with `red doctor` reporting a shadowed one. |
| `v0.1.0b1` | 2026-11-14 | First public beta: the installer, the loopback agent UI, scope and written authorisation before any active assessment, and the audit log. **Unusable now** — its installer pins a wheel the site no longer serves, so it stops at the download. |

The `README.md` and `SHA256SUMS.txt` inside the `v0.1.0b1` and `v0.1.0b5` tags
are older snapshots: they print sizes and digests from builds that were
re-uploaded before those tags were cut, and b5's still names an
`enigmatic_red-0.1.0b4` wheel. A tag cannot be rewritten without moving it, so
the snapshots stay. Each release's notes carry the numbers that match its
attached files, read back from GitHub's asset records.

