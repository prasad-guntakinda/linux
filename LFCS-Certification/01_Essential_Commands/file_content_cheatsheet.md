# File Content Commands — Printable Cheatsheet

A compact, printable cheat-sheet of common Linux commands for viewing, filtering, transforming, and combining file contents.

Viewing & Paging

- `cat file` — print entire file
- `cat -n file` — print with line numbers
- `less file` — pager (search with `/`, navigate with `j`/`k`)
- `more file` — simple pager
- `tac file` — reverse lines

Head / Tail & Live follow

- `head -n N file` — first N lines
- `tail -n N file` — last N lines
- `tail -f file` / `tail -F file` — follow appended data

Sorting & Uniqueness

- `sort file` — lexicographic sort
- `sort -n file` — numeric sort
- `sort -t, -k3 -n file` — sort CSV by column 3
- `sort file | uniq -c | sort -nr` — frequency counts

Field / Column Operations

- `cut -d',' -f1,3 file` — extract CSV columns
- `cut -c1-8 file` — extract character range
- `awk -F',' '$3>100 {print $1,$3}' file` — field-aware filtering

Search & Filter

- `grep 'PATTERN' file` — search
- `grep -i` — case-insensitive
- `grep -r` — recursive
- `grep --line-buffered` — useful in pipelines

Stream-edit & Transform

- `sed 's/old/new/g' file` — substitute
- `sed -n '10,20p' file` — print a range
- `tr '[:upper:]' '[:lower:]' < file` — translate case
- `paste -d',' a b` — merge files side-by-side

Join / Split / Batch tools

- `split -l 5000 huge.log part_` — split file into chunks
- `join -t',' a_sorted b_sorted` — join on first field (files must be sorted)
- `find . -name '*.txt' -print0 | xargs -0 wc -l` — batch processing
- `grep ERROR app.log | tee errors.tmp` — save pipeline output while streaming

Summary / Stats / Formatting

- `wc -l file` — line count
- `nl -ba file` — number lines
- `column -t -s',' file` — tabular pretty-print
- `du -b * | sort -nr | numfmt --to=iec-i` — human-readable sizes

Quick Recipes

- Top 10 IPs:

```
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -n 10
```

- Unique values from CSV column (skip header):

```
tail -n +2 data.csv | cut -d',' -f4 | sort | uniq
```

- Live filter for HTTP 500:

```
tail -F /var/log/nginx/access.log | grep --line-buffered " 500 "
```

Print / Save

- To make a PDF for printing:

```
# convert to PDF (if pandoc installed)
pandoc file_content_cheatsheet.md -o file_content_cheatsheet.pdf
```

Notes

- Use these commands in a safe environment; test destructive commands on copies.
- Expand entries with `man <command>` or `<command> --help` for details.



Below are smaller tables grouped by behavior. Each table shows common commands and a short example to get started.

### Viewing & Paging
| Command | What it does | Example |
| ------- | ------------ | ------- |
| `cat file` | Print entire file (small files) | `cat README.md` |
| `cat -n file` | Print with line numbers | `cat -n script.sh \\| sed -n '1,50p'` |
| `less file` | Pager with search/navigation | `less /var/log/syslog` |
| `more file` | Simple pager | `more file.txt` |
| `tac file` | Print file lines in reverse | `tac logfile | head -n 50` |

**Common options & examples**

- `cat -A file` — show non-printing characters and line ends.
- `cat -T file` — show tabs as `^I`.
- `less -N file` — show line numbers inside `less`.
- `less +F file` — follow mode (like `tail -f`), press `Ctrl-C` then `F` to stop.
- `more -d file` — prompt with `Press space to continue, 'q' to quit.`

Examples:
```
cat -A file.txt
less -N /var/log/syslog
less +F /var/log/app.log
```

### Head / Tail & Live follow
| Command | What it does | Example |
| ------- | ------------ | ------- |
| `head -n N file` | Show first N lines | `head -n 20 access.log` |
| `tail -n N file` | Show last N lines | `tail -n 100 access.log` |
| `tail -f file` | Follow appended data (logs) | `tail -F /var/log/nginx/access.log` |

**Common options & examples**

- `head -c NUM file` — show first NUM bytes.
- `head -n -N file` — show all but last N lines (GNU head).
- `tail -c +BYTES file` — show from byte offset BYTES to end.
- `tail -f -n +1 file` — follow from start of file (useful on rotated logs).
- `tail -F file` — follow name across rotations (safer for log files).

Examples:
```
head -c 200 README.md
tail -n +1 /var/log/syslog | grep ERROR
tail -F /var/log/nginx/access.log
```

### Sorting & Uniqueness
| Command | What it does | Example |
| ------- | ------------ | ------- |
| `sort file` | Lexicographic sort of lines | `sort names.txt` |
| `sort -n` | Numeric sort | `sort -n scores.txt` |
| `sort -t, -k3 -n file` | Sort CSV by column 3 numerically | `sort -t, -k3 -n data.csv` |
| `uniq` | Collapse adjacent duplicate lines (use with `sort`) | `sort names.txt | uniq -c` |

**Common options & examples**

- `sort -r` — reverse order.
- `sort -u` — unique (keep first occurrence).
- `sort -k 2,2` — sort by 2nd field only.
- `uniq -c` — prefix lines by count of occurrences (input must be grouped/sorted).
- `uniq -d` — print only duplicated lines.

Examples:
```
sort -r names.txt
sort -t',' -k2,2 -n data.csv
sort names.txt | uniq -c | sort -nr
```

### Field / Column Operations
| Command | What it does | Example |
| ------- | ------------ | ------- |
| `cut -d'D' -fN file` | Extract fields using delimiter `D` | `cut -d',' -f1,3 users.csv` |
| `cut -cX-Y` | Extract character ranges | `cut -c1-8 /proc/cpuinfo` |
| `awk` | Field-aware processing & filtering | `awk -F',' '$3>100 {print $1,$3}' data.csv` |

**Common options & examples**

- `cut -f1,3 -d',' file` — select fields 1 and 3.
- `cut -c 1-10` — first 10 characters of each line.
- `awk -F':' '{print $1}' /etc/passwd` — print first field using `:` as FS.
- `awk '{sum+=$2} END {print sum}' file` — sum numeric second column.

Examples:
```
cut -d',' -f2 users.csv
awk -F':' '{print $1, $3}' /etc/passwd
awk '{sum+=$2} END {print "Total:", sum}' sales.txt
```

### Search & Filter
| Command | What it does | Example |
| ------- | ------------ | ------- |
| `grep PATTERN file` | Search for pattern in file | `grep -n "ERROR" app.log` |
| `grep -i` | Case-insensitive search | `grep -i "timeout" *.log` |
| `grep -r` | Recursive search | `grep -r "TODO" .` |

**Common options & examples**

- `grep -n` — show line numbers for matches.
- `grep -c` — count matching lines.
- `grep -v` — invert match (show non-matching lines).
- `grep -E` — use extended regex (alternation, +, ?)
- `grep --line-buffered` — useful when piping into further commands.

Examples:
```
grep -n "ERROR" /var/log/app.log
grep -c "connection" *.log
grep -v "DEBUG" app.log | less
```

### Stream-edit & Transform
| Command | What it does | Example |
| ------- | ------------ | ------- |
| `sed` | Stream editor (substitute, delete, ranges) | `sed 's/old/new/g' config.template` |
| `tr` | Translate or delete characters | `tr '[:upper:]' '[:lower:]' < FILE` |
| `paste` | Merge files side-by-side | `paste -d',' file1 file2 > merged.csv` |

**Common options & examples**

- `sed -n '10,20p' file` — print lines 10–20.
- `sed -i.bak 's/foo/bar/g' file` — in-place replace with backup.
- `tr -d '\r'` — remove CR (useful when converting DOS files).
- `tr -s ' ' '\n'` — squeeze spaces, translate to newlines (text-to-words).

Examples:
```
sed -n '1,5p' config.template
sed -i.bak 's/localhost/127.0.0.1/g' config.template
tr -d '\r' < dosfile.txt > unixfile.txt
```

### Join / Split / Batch tools
| Command | What it does | Example |
| ------- | ------------ | ------- |
| `split` | Break file into chunks by size/lines | `split -l 5000 huge.log part_` |
| `join` | Join two files on a common field (sorted) | `join -t',' <(sort a) <(sort b)` |
| `xargs` | Build and run command lines from input | `find . -name '*.txt' -print0 | xargs -0 wc -l` |
| `tee` | Write pipeline output to file and stdout | `grep ERROR app.log | tee errors.tmp | wc -l` |

**Common options & examples**

- `split -b 10M file prefix` — split by bytes (10 MiB chunks).
- `split -l 1000 file prefix` — split by lines (1000-line chunks).
- `join -1 1 -2 2 -t',' a_sorted b_sorted` — join using different key columns.
- `xargs -0 -I{} cp {} /dest` — run command for each input safely with null termination.
- `tee -a logfile` — append to logfile instead of overwriting.

Examples:
```
split -b 50M big.iso iso_part_
split -l 2000 big.log chunk_
find . -name '*.txt' -print0 | xargs -0 -I{} gzip {}
grep ERROR app.log | tee -a errors.log | wc -l
```

### Summary / Stats / Formatting
| Command | What it does | Example |
| ------- | ------------ | ------- |
| `wc -l` | Count lines | `wc -l access.log` |
| `nl` | Number lines (formatted) | `nl -ba script.sh` |
| `column -t -s',' file` | Pretty-print table-like data | `column -t -s',' data.csv` |
| `numfmt` | Format numbers (sizes) | `du -b * | sort -nr | numfmt --to=iec-i` |

**Common options & examples**

- `wc -w` — word count, `wc -c` — byte/character count.
- `nl -ba -v1 -s'. '` — start numbering at 1 and use a separator.
- `column -t -s$'\t' file` — pretty-print tab-separated data.
- `numfmt --to=iec --suffix=B` — use IEC (KiB/MiB) formatting.

Examples:
```
wc -l -w -c README.md
nl -ba script.sh | sed -n '1,50p'
column -t -s',' data.csv | head -n 20
du -b * | sort -nr | numfmt --to=iec --suffix=B
```

### Common pipelines (recipes)

- Top 10 most frequent IPs from an access log:
```
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -n 10
```

- Extract a CSV column (skip header) and get unique values:
```
tail -n +2 data.csv | cut -d',' -f4 | sort | uniq
```

- Live filter logs for HTTP 500 errors:
```
tail -F /var/log/nginx/access.log | grep --line-buffered " 500 "
```

### Tips

- Use `awk` when you need field-aware logic rather than many `cut`/`sed` steps.
- Prefer `--line-buffered` or `stdbuf` for predictable streaming behavior in long pipelines.
- Be cautious with `sed -i` and `>` redirections — test on copies first.

This set of grouped tables is intended as a compact, behavior-oriented cheat-sheet. Use `man <command>` or `<command> --help` for full details.
