# AGENTS.md

This project maintains well-formed User-Agent templates and generated matching
patterns.

The source of truth for both templates and pattern inputs is
`well-formed-user-agent-templates.yaml`. When looking for an existing
User-Agent template or deciding where to add/update one, start there.

Generated regex outputs are derived from that YAML file. Do not edit generated
files first unless the task explicitly asks for generated output only.
