# Hashcat Cheatsheet
Author: Thorleif Jacobsen

## Examples

```
hashcat -m <mode> -a 0 <hashfile> <dictionary file>
```

## Attack Modes (-a)

| Attack Mode           | Mode Number | Description                                              | Example Usage                                   |
|------------------------|-------------|----------------------------------------------------------|-----------------------------------------------|
| Straight              | 0           | Uses a password list/dictionary file to crack hashes.    | `hashcat -m <hash_type> -a 0 <hash_file> <wordlist>` |
| Combination           | 1           | Combines two dictionaries for password cracking.         | `hashcat -m <hash_type> -a 1 <hash_file> <wordlist1> <wordlist2>` |
| Brute-Force           | 3           | Tries all possible combinations of characters.           | `hashcat -m <hash_type> -a 3 <hash_file> ?a?a?a?a` |
| Hybrid Wordlist + Mask| 6           | Appends a mask to each word in the dictionary.           | `hashcat -m <hash_type> -a 6 <hash_file> <wordlist> ?d?d` |
| Hybrid Mask + Wordlist| 7           | Prepends a mask to each word in the dictionary.          | `hashcat -m <hash_type> -a 7 <hash_file> ?d?d <wordlist>` |

## Linux Hashes

| Hash Type       | Hashcat Mode | Description                               | Example Hash                       |
|------------------|-------------|-------------------------------------------|-------------------------------------|
| Traditional DES | 1500        | Legacy DES-based hashes.                  | `hxH8mCp3K8Jig`                    |
| MD5 Crypt       | 500         | Common, hashed password starts with `$1$`| `$1$abc$XYZ...`                    |
| BSDi Extended DES| 3000       | Extended DES hashes, starts with `_`.     | `_J9..CCCCXBrJUJV154M`             |
| Blowfish (bcrypt)| 3200       | Blowfish-based hashes, starts with `$2$` or `$2a$`.| `$2a$12$abc$XYZ...`        |
| SHA-256 Crypt   | 7400        | SHA-256, starts with `$5$`.               | `$5$rounds=5000$abc$XYZ...`        |
| SHA-512 Crypt   | 1800        | SHA-512, starts with `$6$`.               | `$6$rounds=5000$abc$XYZ...`        |

## Windows Hashes

> Notice "aad" hash usually means it is not in use.

| Hash Type      | Hashcat Mode | Description             | Example Hash                       |
|----------------|--------------|-------------------------|-------------------------------------|
| LM (Lanman)    | 3000         | Windows legacy hash     | `aad3b435b51404eeaad3b935b51304fe` |
| NTLM           | 1000         | Windows NT hash         | `c46b9e588fa0d112def659fd6d58eae3` |

## Masks

### Examples
- `?l?l?d?d`: Tries passwords with two lowercase letters followed by two digits, like `ab12`.
- `?u?l?l?d`: Tries passwords starting with an uppercase letter, followed by two lowercase letters and a digit, like `Abc1`.

### Mask Placeholders

| Placeholder | Character Set          |
|-------------|------------------------|
| `?l`        | `abcdefghijklmnopqrstuvwxyz` |
| `?u`        | `ABCDEFGHIJKLMNOPQRSTUVWXYZ` |
| `?d`        | `0123456789` |
| `?s`        | ` «space»!"#$%&'()*+,-./:;<=>?@[\]^_`{|}~ ` |
| `?a`        | `?l?u?d?s` |
| `?b`        | `0x00 - 0xff` |


### Example Masks
1. `?u?l?d`: Uppercase, lowercase, and digit (e.g., A1b).
2. `?d?d?d?d`: Four digits (e.g., 1234).
3. `?l?l?s?s`: Two lowercase letters and two special characters (e.g., ab!!).

These masks allow for targeted brute-force attacks based on known password patterns, significantly improving efficiency.

## Other Parameters

| Parameter        | Description                                                                 | Example Usage                                      |
|------------------|-----------------------------------------------------------------------------|---------------------------------------------------|
| `-r`             | Apply rules to modify words from the wordlist (rules file required).        | `hashcat -m <hash_type> -a 0 -r rules/best64.rule <hash_file> <wordlist>` |
| `--show`         | Displays the cracked passwords after running a session.                    | `hashcat -m <hash_type> --show <hash_file>`       |
| `--session`      | Save progress to a named session (useful for pausing/resuming).             | `hashcat -m <hash_type> --session mysession <hash_file>` |
| `--restore`      | Restores a paused session using the saved session file.                    | `hashcat --restore --session mysession`           |
| `--force`        | Forces hashcat to run even on unsupported platforms (use cautiously).       | `hashcat -m <hash_type> -a 0 --force <hash_file> <wordlist>` |
| `-o`             | Outputs cracked hashes and passwords to a specified file.                 | `hashcat -m <hash_type> -a 0 -o output.txt <hash_file> <wordlist>` |
| `--potfile-disable` | Disables the potfile (prevents saving cracked passwords).                | `hashcat -m <hash_type> --potfile-disable <hash_file> <wordlist>` |
| `--outfile-format` | Defines the format for the output file (e.g., hash, plaintext).           | `hashcat -m <hash_type> -a 0 --outfile-format 2 -o output.txt <hash_file> <wordlist>` |
| `--increment`    | Enables incremental mode for brute-force attacks.                         | `hashcat -m <hash_type> -a 3 --increment <hash_file> ?a?a?a?a` |
| `--status`       | Displays the current status of the attack in real-time.                   | `hashcat -m <hash_type> -a 0 --status <hash_file> <wordlist>` |
