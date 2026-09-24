# Well-Formed User-Agent Database

Well-formed User-Agent templates and generated regular expressions for
deny-by-default traffic filtering.

The goal is to keep a small, readable source database of legitimate-looking
User-Agent formats, then generate anchored regular expressions from it.

## Files

- `well-formed-user-agent-templates.yaml` contains the source templates and
  placeholder regexes.
- `well-formed-user-agent-regexes.txt` contains generated regular expressions,
  one regex per line.
- `well-formed-user-agent-apache.conf` contains generated Apache `SetEnvIf`
  rules.
- `generate-user-agent-regexes.py` regenerates the regex file from the YAML
  templates.

## Regenerate Regexes

```bash
./generate-user-agent-regexes.py
```

Custom input and output paths:

```bash
./generate-user-agent-regexes.py \
  --data well-formed-user-agent-templates.yaml \
  --output well-formed-user-agent-regexes.txt \
  --apache-output well-formed-user-agent-apache.conf
```

Generate regexes for the known not well-formed examples too:

```bash
./generate-user-agent-regexes.py --silly-billy \
  --output silly-billy-user-agent-regexes.txt \
  --apache-output silly-billy-user-agent-apache.conf
```

## Use With Access Logs

For common web server logs where the User-Agent is the sixth double-quoted
field, use [ripgrep](https://github.com/BurntSushi/ripgrep/releases):

```bash
cut -d'"' -f6 access.log | rg --pcre2 -f well-formed-user-agent-regexes.txt
```

## Use With Apache

Generate the Apache include file:

```bash
./generate-user-agent-regexes.py
```

Include the generated `SetEnvIf` rules from Apache server or virtual host
config:

```apache
Include /path/to/well-formed-user-agent-apache.conf
```

Deny requests whose User-Agent does not match any well-formed pattern:

```apache
<Location />
    Require env well_formed_user_agent
</Location>
```

The generated include file sets `well_formed_user_agent=1` when the request
User-Agent matches at least one known well-formed pattern. Requests without
that environment variable are denied by `Require env`.
