# Practical sed exercises

- sed works as: `sed [OPTIONS] 'ADDRESS COMMAND' file(s)` — it reads each input line into the pattern space, applies commands, then prints (unless -n used). Addresses choose which lines the command applies to; commands transform the pattern space or control flow.


### NOTES:

I'll expand the sed syntax overview below — let me know if you want me to add this text into 03_File_Contents_Commands.md.

Quick summary (single-line)
- sed works as: sed [OPTIONS] 'ADDRESS COMMAND' file(s) — it reads each input line into the pattern space, applies commands, then prints (unless `-n` used). Addresses choose which lines the command applies to; commands transform the pattern space or control flow.

1) Command structure
- Basic form inside quotes:
  - sed 'ADDRESS COMMAND' file
  - Examples:
    - `sed '1,10p' file` — print lines 1..10
    - `sed '/^#/d' file` — delete lines beginning with `#`
- Multiple commands:
  - semicolon separated: `sed 's/a/b/g; s/c/d/g' file`
  - multiple -e options: `sed -e 'cmd1' -e 'cmd2'`
  - script file: `sed -f script.sed file`

2) Addresses (which lines the command applies to)
- Line number: `5` (line 5); range: `5,10` (5..10)
- Last line: `$`
- Regular-expression address: `/pattern/` (first and subsequent matches in processing order)
- Ranges using regex: `/^start/,/^end/`
- Apply when address does NOT match: append `!` after address, e.g. `/^#/!p` (print non-`#` lines)
- Example: `sed '100q' file` — quit after printing first 100 lines

3) The `s` (substitute) command — the most used
- Syntax: `s/OLD/NEW/FLAGS`
  - Delimiter need not be `/`: `s:old:new:g` helps when patterns contain slashes.
  - Flags:
    - `g` — global in line (all matches)
    - `p` — print the line when a replacement occurs (useful with `-n`)
    - `N` (a number) — replace the Nth match in the line: `s/foo/bar/2`
    - `i` — case-insensitive (GNU `sed` uses `I` or `i` depending; with `-E` you may use `(?i)` in some sed)
- Backreferences:
  - In regex: capture groups `\(...\)` (basic) or `(...)` with `-E`
  - Use `\1`, `\2` in replacement to reference groups.
  - `&` in replacement = entire matched text.
- Examples:
  - `sed 's/foo/bar/' file` — replace first `foo` per line
  - `sed 's/foo/bar/g' file` — replace every `foo` in each line
  - `sed -n 's/foo/bar/p' file` — print only lines where substitution occurred
  - `sed 's/\(abc\)-\(123\)/\2:\1/'` — swap captured parts (basic R.E.)
  - `sed 's#/var/www#/srv/www#g' file` — alternate delimiter

4) Other frequently used commands
- `d` — delete current pattern space (line removed from output)
  - `sed '/^#/d' file` — remove comment lines
- `p` — print (useful with `-n` to control output)
  - `sed -n '10,20p' file` — print 10..20 only
- `q` — quit (optionally with exit code): `sed '100q' file`
- `a\ text` — append a line after the addressed line (note newline escaping rules)
  - `sed '/^END/a\This is appended' file`
- `i\ text` — insert a line before the addressed line
- `c\ text` — change (replace addressed lines with text)
- `y/source/dest/` — transliterate characters (like tr)
  - `sed 'y/ABCDEFGHIJKLMNOPQRSTUVWXYZ/abcdefghijklmnopqrstuvwxyz/'`  
- `r file` — read file and append its contents after the current line
- `w file` — write the pattern space to another file when command applied
- Branching / flow:
  - `:label` define label, `b label` unconditional branch, `t label` branch on last substitution success (useful for loops)
- Hold space operations (pattern vs hold space — powerful for multi-line tasks):
  - `h` copy pattern space to hold space
  - `H` append pattern space to hold space
  - `g` copy hold to pattern
  - `G` append hold to pattern
  - `x` swap pattern and hold
  - Use these for multi-line transforms or to re-use previous lines (examples below)

5) Multi-line commands and the pattern/hold space
- By default sed processes one line at a time (the "pattern space"). Use `N` to append next input line into the pattern space (so pattern space contains newline `\n`).
- Example: join pairs of lines into one line:
  - `sed 'N;s/\n/ /' file` — read next line into pattern space and replace the newline with space.
- Example: delete Windows CR (`\r`):
  - `sed -i 's/\r$//' file` or `sed 's/\r$//' file > out`

6) In-place editing portability
- GNU sed: `sed -i.bak 's/a/b/g' file` (creates `file.bak`)
- BSD/macOS sed: `sed -i '' 's/a/b/g' file` — note the required empty string for no backup. For portability, prefer `-i.bak` or write to a temp file and move it.
- `-r` vs `-E`:
  - GNU `sed` historically uses `-r` for extended regex; POSIX `sed`-like macOS uses `-E`. Use `-E` for better portability.

7) Using sed scripts and files
- Put multiple commands in a file `script.sed`:
  ```
  /^#/d
  s/foo/bar/g
  1,10p
  ```
  - Use: `sed -f script.sed input`
- Or use `-e` for each command: `sed -e 'cmd1' -e 'cmd2'`

8) Examples with common tasks
- Replace only the second occurrence of `foo` on a line:
  - `sed 's/foo/bar/2'`
- Replace only on lines matching a pattern:
  - `sed '/ERROR/ s/old/new/g' logfile`
- Insert a line after matching pattern:
  - `sed '/^Section:/a\-- new line --' file`
- Remove trailing spaces:
  - `sed -E 's/[[:space:]]+$//' file`
- Print line numbers:
  - `sed -n '=' file | sed 'N;s/\\n/\\t/'`  (or use `nl`/`cat -n`)

9) Advanced example: swap pairs of lines
- Swap each pair of lines (1<>2, 3<>4):
  - `sed 'N; s/\(.*\)\n\(.*\)/\2\n\1/' file`
  - Explanation: `N` loads next line into pattern space, then swap captured halves.

10) Regex flavor & escaping
- Default sed uses BRE (basic regex). Parentheses must be escaped `\(...\)` unless using `-E`.
- With `-E` you can use `()` and `+`, `?`, `{}` without backslashes.
- To match a literal `/` in `s///`, either escape `\/` or use a different delimiter `s#/#x#`.

11) Shell variable pitfalls
- To use shell variables in sed:
  - Use double quotes but be mindful of regex backslashes: `sed "s/$old/$new/g" file` — but escape any `&` in replacement or use `printf '%s\n' "$new" | sed ...` alternatives.
  - Safer: use `awk` for heavy shell-variable-driven substitutions, or escape variables before passing.

12) Performance & when not to use sed
- sed is fast for streaming, line-based edits.
- For complex record/field processing, `awk` or a small script in Python/perl is often clearer.
- For very large multi-line context tasks prefer awk/python.

13) Portability notes
- GNU sed adds extensions: `--posix`, `-r` vs `-E`, `--sandbox`, `-i` behaviour — check `sed --version` or man page.
- macOS/BSD sed differences: `-i` requires argument (''), `-E` is supported.
- When writing educational docs, show both variants where necessary.

14) Short troubleshooting checklist
- If your substitution doesn't happen:
  - Check escaping of `/`, `\`, `&`, and shell quoting.
  - Try `sed -n 's/old/new/p'` to see whether the pattern matches at all.
- If `-i` fails on macOS: use `-i '' 's/.../.../' file` or create backup `-i .bak`.
- If your regex uses `+`/`?`/`()` without escaping, add `-E`.

If you want, I can:
- Insert this expanded sed-syntax section into 03_File_Contents_Commands.md (I can patch it now).
- Provide a few more advanced worked examples (hold-space tricks, multiline deletes).
- Convert the exercises into a small automated shell script that runs and verifies answers.

Which would you like next?

---
---

The following exercises are practical, runnable, and progressively more challenging. Each task includes a hint or commands to get you started.

1) Simple substitution
- Task: Replace the word `apple` with `orange` in a file `fruits.txt`.
- Hint:
```sh
printf "apple banana\napple pie\nfruit\n" > fruits.txt
sed 's/apple/orange/' fruits.txt
cat fruits.txt
# without --in-place it will just do changes and print it to console but not save changes to save use --in-place or -i option
sed -i 's/apple/MANGO/' fruits.txt
# Before modification if you want to take backup then give suffix to -i option

sed -i.backup 's/MANGO/orange/' fruits.txt


```


2) Global substitution
- Task: For the line `foo foo foo` replace every `foo` with `bar`.
- Hint:
```sh
printf "foo foo foo\n" > repeat.txt
sed 's/foo/bar/g' repeat.txt
```

3) Replace only the 2nd occurrence on each line
- Task: Given `one two one two one`, change only the 2nd `one` to `ONE`.
- Hint:
```sh
printf "one two one two one\n" > occ.txt
sed 's/one/ONE/2' occ.txt
```

4) Delete comment lines
- Task: Remove lines starting with `#` from a config file `cfg.txt`.
- Hint:
```sh
printf "#a\nvalue=1\n#b\nvalue=2\n" > cfg.txt
sed '/^#/d' cfg.txt
```

5) Extract a slice of lines to a file
- Task: Extract lines 50..60 from a 1..200 numbered file into `slice.txt`.
- Hint:
```sh
seq 1 200 > many.txt
sed -n '50,60p' many.txt > slice.txt
```

6) In-place edit with backup
- Task: Replace `localhost` with `127.0.0.1` in `server.conf`, keeping a `.bak` backup.
- Hint:
```sh
printf "server=localhost\nport=80\n" > server.conf
sed -i.bak 's/localhost/127.0.0.1/g' server.conf
ls -l server.conf server.conf.bak
```

7) Date reformatting with capture groups
- Task: Convert `YYYY-MM-DD` lines to `DD/MM/YYYY` in `dates.txt`.
- Hint:
```sh
printf "2025-11-19\n1999-01-01\n" > dates.txt
sed -E 's/([0-9]{4})-([0-9]{2})-([0-9]{2})/\3\/\2\/\1/g' dates.txt
```

8) Swap every pair of lines
- Task: Swap lines (1<>2, 3<>4, ...) in `pairs.txt`.
- Hint:
```sh
printf "a\nb\nc\nd\ne\nf\n" > pairs.txt
sed 'N; s/\(.*\)\n\(.*\)/\2\n\1/' pairs.txt
```

9) Remove trailing whitespace from file (in-place)
- Task: Strip trailing spaces/tabs from each line in `trailing.txt` and save changes to the same file (keep a `.orig` backup).
- Hint:
```sh
printf "line1   \nline2\t\n" > trailing.txt
sed -i.orig -E 's/[[:space:]]+$//' trailing.txt
cat trailing.txt
ls trailing.txt.orig
```

10) Advanced — multi-line context
- Task: For a file where records span multiple lines separated by a blank line, replace `foo` with `bar` only inside each record (not in the blank separators).
- Hint: this is advanced — consider using the hold space or `awk`; a simple approach with `sed` uses paragraph mode if available, or `awk` is easier. Example with `awk`:
```sh
awk 'BEGIN{RS="\n\n"; ORS="\n\n"} {gsub(/foo/,"bar"); print}' input.txt
```

Notes
- Try each command first without `-i` to confirm expected output. Use backup suffixes with `-i` for safety.
- For portability on macOS, remember `sed -i ''` behavior differs — using a backup like `-i.bak` is safer.
