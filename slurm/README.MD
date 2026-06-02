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


Finally, trim the schema to only have the `v0.0.43`.
