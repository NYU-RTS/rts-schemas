## Generatiom process

- Get the OpenAPI schema via:
```
curl -kL -H "Authorization: Bearer $(oc whoami -t)" $(oc whoami --show-server)/openapi/v2 > openapi.json
```

- Convert via:
```
npx "@openapi-contrib/openapi-schema-to-json-schema" --input openapi.json --output json-schema.json
```
