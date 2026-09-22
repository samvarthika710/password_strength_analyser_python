# password_strength_analyser_python
# Password Strength Analyzer

A command-line Python tool that evaluates how strong a password is and gives specific, actionable feedback for improving it.

## Features

- **Length checks** — flags passwords under 8 characters, rewards 12+.
- **Character variety** — checks for lowercase, uppercase, digits, and symbols.
- **Common password detection** — flags passwords found in a known list of frequently used/breached passwords.
- **Pattern detection** — catches sequential runs (`abcd`, `1234`, `qwer`) and repeated characters (`aaaa`, `1111`).
- **Entropy estimate** — calculates approximate entropy in bits based on character pool size and length.
- **0–8 scoring system** with a human-readable rating: `Very Weak`, `Weak`, `Moderate`, `Strong`, `Very Strong`.
- **Interactive loop mode** — keep checking multiple passwords in one session without restarting the script.
- **Hidden input** — uses `getpass` so passwords aren't echoed to the screen during interactive use.

## Requirements

- Python 3.6 or later
- No external dependencies — uses only the standard library (`argparse`, `getpass`, `math`, `re`, `sys`)

## Usage

### Interactive mode (recommended)

Run the script with no arguments to enter a loop. It will keep prompting for passwords until you quit:

```bash
python password_strength_analyzer.py
```

```
Password Strength Analyzer — type 'q' or press Ctrl+C to quit.

Enter a password to analyze (input hidden):
```

Type a password and press Enter to see its report. Keep entering passwords to check more, or type `q`, `quit`, `exit`, or press `Ctrl+C` to stop.

### One-shot mode

Pass a password directly via the `-p` / `--password` flag to check a single password and exit immediately (useful for scripting or automation):

```bash
python password_strength_analyzer.py -p "MyP@ssw0rd123"
```

> **Note:** Passing a password on the command line may leave it visible in your shell history or process list. Prefer interactive mode for anything sensitive.

## Example Output

```
=== Password Strength Report ===
Password (masked): T************z
Length: 14
Estimated entropy: 91.8 bits
Score: 7 / 8
Rating: Very Strong

Suggestions:
  - Looks good! No major issues detected.
```

## How Scoring Works

| Factor | Effect on score |
|---|---|
| Length ≥ 8 | +1 |
| Length ≥ 12 | +1 |
| Each character type present (lower/upper/digit/symbol) | +1 each (up to +4) |
| Entropy ≥ 60 bits | +1 |
| Password is in the common password list | −4 |
| Contains a sequential run (e.g. `abcd`, `1234`) | −1 |
| Contains 4+ repeated characters (e.g. `aaaa`) | −1 |

The final score is clamped between 0 and 8 and mapped to a rating:

| Score | Rating |
|---|---|
| 0–2 | Very Weak |
| 3–4 | Weak |
| 5 | Moderate |
| 6 | Strong |
| 7–8 | Very Strong |

## Limitations

This tool uses simple heuristics and is intended for **educational / demonstration purposes**, not as a production-grade security control. In particular:

- The common-password list is small (~25 entries) compared to real-world breach lists, which contain millions of entries.
- Entropy is estimated assuming random character selection — a predictable password can still score reasonably high on entropy alone (which is why the pattern/common-password checks exist as separate signals).
- It does not check passwords against real breach databases (e.g. Have I Been Pwned).
- It does not perform dictionary-word substitution detection (e.g. `p@ssw0rd`) the way tools like `zxcvbn` do.

For production use, consider pairing this tool with:
- [`zxcvbn`](https://github.com/dropbox/zxcvbn) — realistic password strength estimation based on pattern matching, originally built by Dropbox.
- The [Have I Been Pwned API](https://haveibeenpwned.com/API/v3) — checks passwords against real breach data using k-anonymity (the full password is never transmitted).
- [NIST SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) guidelines, which recommend prioritizing length and breach-checking over forced complexity rules.

## Files

| File | Description |
|---|---|
| `password_strength_analyzer.py` | The main script — run this to analyze passwords. |
| `README.md` | This file. |

## License

Free to use, modify, and distribute for personal or educational purposes.
