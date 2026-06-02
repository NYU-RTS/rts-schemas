# SLURM REST API


`openapi.json` contains the dump from slurmtrestd.

This is then filtered down to `filtered.json` via `openapi-format` with the `filter.yaml` filter specification as:
```
~> npx openapi-format -f filter.yaml -o filtered.json openapi.json
============================================================================================================================
OpenAPI-Format CLI settings:
- Sort file:		(defaultSort.json)
- Filter file:		filter.yaml
- Input file:		openapi.json
- Output file:		filtered.json
============================================================================================================================
✅  OpenAPI filtered & formatted successfully
============================================================================================================================
```

The schema for `v0_0_43_job_info_power` is invalid (array with no items) and has been manually removed:
```
~> bat -r 2125:2132 filtered.json
───────┬────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: filtered.json
───────┼────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
2125   │           "power": {
2126   │             "type": "object",
2127   │             "properties": {
2128   │               "flags": {
2129   │                 "type": "array",
2130   │                 "deprecated": true
2131   │               }
2132   │             }
───────┴──────────────────────────────────────────────────────────────────────────────────────────────
```


Finally, trim the schema to only have the `v0.0.43`.
