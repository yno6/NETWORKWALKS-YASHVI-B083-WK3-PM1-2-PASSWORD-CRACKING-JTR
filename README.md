<div>
   
# PASSWORD CRACKING LAB

Cybersecurity & Ethical Hacking — covering two methods for recovering a password from a locked PDF file: an offline CLI tool (John the Ripper) and a browser-based online tool (Networkwalks Hash Calculator + Password Cracker).

</div>

<p align="center">
  <img src="https://img.shields.io/badge/Skill-Cybersecurity-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/Skill-Password%20Cracking-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/Skill-Hash%20Cracking-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/John%20the%20Ripper-JTR-404040?style=flat-square&labelColor=0070C0" />
  <img src="https://img.shields.io/badge/Tool-pdf2john-404040?style=flat-square&labelColor=0070C0" />
  <img src="https://img.shields.io/badge/NetworkWalks-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/Skill-PDF%20Security-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/macOS-Apple%20Silicon-404040?style=flat-square&labelColor=000000&logo=apple&logoColor=white" />
</p>

> ⚠️ **Ethical use only.** These exercises were performed on sample PDF files provided by NetworkWalks, on my own machine, purely to learn how password/hash cracking works and why weak passwords are risky. Never run these techniques against files, accounts, or systems you don't own or have explicit permission to test.


## Background

Files like PDF, ZIP, and Office documents can be password-protected. The password itself usually isn't stored — instead a **hash** (a scrambled, one-way representation of the password) is embedded in the file. Cracking the password means extracting that hash and testing candidate passwords against it until one produces a match.

Two things this lab makes concrete:
- **Encryption vs. hashing** — encryption is reversible with the right key, while hashing is one-way and can only be "reversed" by guessing inputs and comparing outputs.
- **Weak passwords fall fast** — every password cracked below came from a *default* wordlist in under a second.

## Tasks

| # | Task | Tool(s) | Target |
|---|------|---------|--------|
| PM1 | Password Cracking with JTR | John the Ripper (jumbo), `pdf2john.pl` | `My-Locked-PDF1.pdf`, `My Locked PDF2.pdf`, `My Locked PDF3.pdf` |
| PM2 | Password Cracking with Networkwalks Tools | [Networkwalks Hash Calculator](https://networkwalks.com/hash-calculator/) & [Password Cracker](https://networkwalks.com/password-cracker/) | `My Locked PDF1.pdf` |

---

## PM1 — Password Cracking with John the Ripper (JTR)

**Environment:** macOS (Apple Silicon), Homebrew

### Steps

1. **Install John the Ripper (jumbo edition)**
   ```bash
   brew install john-jumbo
   ```
<img width="1168" height="819" alt="installJTR" src="https://github.com/user-attachments/assets/fdc6e98d-3784-4790-9e80-d1e9dcfc8389" />


2. **Extract the hash from each locked PDF** using the `pdf2john.pl` script bundled with John:
   ```bash
   cd ~/Downloads
   perl /opt/homebrew/share/john/pdf2john.pl "My-Locked-PDF1.pdf" > hash1.txt
   cat hash1.txt
   ```
   This produces a hash string in the `$pdf$...` format that John understands.

3. **Run John against the hash file:**
   ```bash
   john hash1.txt
   ```
   John first tries "single crack" mode (rules derived from the filename/metadata), then falls back to its default wordlist (`password.lst`) in wordlist mode.

4. **Display the cracked password:**
   ```bash
   john --show hash1.txt
   ```

5. Repeat for each locked PDF.

### Results

| File | Cracked password | Mode that found it |
|---|---|---|
| `My-Locked-PDF1.pdf` | `password1` | Wordlist (`password.lst`) |
| `My Locked PDF2.pdf` | `password1` | Wordlist (`password.lst`) |
| `My Locked PDF3.pdf` | `1qaz2wsx` | Wordlist (`password.lst`) |


<img width="1285" height="840" alt="finalcode-passbreakJTR" src="https://github.com/user-attachments/assets/e93a8e41-185a-4967-9eac-3986fc49a387" />

All three were cracked in under 1 second against a stock wordlist — none required brute force.

---

## PM2 — Password Cracking with Networkwalks Tools

**Environment:** Any browser (Windows or Kali Linux) — no local tools required.

### Steps

1. Download the encrypted sample PDF (`My Locked PDF1.pdf`) from the [Networkwalks lab page](https://networkwalks.com/project-task-lab-password-cracking-with-networkwalks-tools/).
2. Open the [Hash Calculator](https://networkwalks.com/hash-calculator/) and upload the locked PDF. It returns a hash beginning with `$pdf$...`.
3. Copy the **complete** hash value.
4. Open the [Password Cracker](https://networkwalks.com/password-cracker/), paste in the hash, and start the attack.
5. Wait for the tool to return the cracked password.
6. Open the PDF and confirm it unlocks with the recovered password.

### Result

| File | Cracked password |
|---|---|
| `My Locked PDF1.pdf` | `password1` |

https://github.com/user-attachments/assets/f95e93df-610c-4d37-9180-06777f6f3c17

This matches the result obtained independently via John the Ripper in PM1 — confirming the same underlying PDF encryption/hash scheme is being attacked both ways, just through a different tool.


---


## Key takeaways

- A PDF's password hash (`$pdf$4*4*128*...`) encodes the PDF version, revision, key length, permissions, and the actual crypto material needed to verify a guessed password — that's what both John and the Networkwalks tools parse and attack.

- `password1` and `1qaz2wsx` are both extremely common/weak passwords (keyboard-walk and dictionary-word patterns), which is exactly why a *default* wordlist cracked them instantly with no customization needed.

- A locally-run CLI tool (JTR) and a browser-based tool (Networkwalks) can arrive at the same cracked password — the difference is control/speed/offline capability (JTR) vs. convenience/no-install (Networkwalks).

---

## Screenshots-PDF-Result

<img width="670" height="867" alt="Screenshot 2026-09-26 at 12 21 48 AM" src="https://github.com/user-attachments/assets/418f4155-1ecd-4942-9936-56d656e8555c" />

<img width="616" height="872" alt="Screenshot 2026-09-26 at 12 22 15 AM" src="https://github.com/user-attachments/assets/a340032d-f34e-44e7-ada4-e34b94c30431" />

<img width="512" height="663" alt="Screenshot 2026-09-26 at 12 23 19 AM" src="https://github.com/user-attachments/assets/bc845f8d-db44-49c2-935e-8d5df816f1d9" />

---

## Tools referenced

- [John the Ripper (jumbo)](https://github.com/openwall/john) — via Homebrew (`john-jumbo`)
- [Networkwalks Hash Calculator](https://networkwalks.com/hash-calculator/)
- [Networkwalks Password Cracker](https://networkwalks.com/password-cracker/)
