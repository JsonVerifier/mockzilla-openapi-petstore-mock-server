# Mockzilla OpenAPI Petstore Mock Server

A ready-to-run Go mock server for the Swagger Petstore OpenAPI sample, generated with [Mockzilla](https://github.com/mockzilla/mockzilla).

I started with the Mockzilla codegen template and turned it into a small working example instead of another blank starter repo. It generates realistic Petstore responses, validates requests against OpenAPI, includes a stable endpoint for repeatable tests, and provides a browser-based API Explorer.

## Run it on Windows

You need [Git](https://git-scm.com/download/win) and [Go 1.25.3 or newer](https://go.dev/dl/).

Open PowerShell:

```powershell
git clone https://github.com/JsonVerifier/mockzilla-openapi-petstore-mock-server.git
Set-Location .\mockzilla-openapi-petstore-mock-server

go test ./...
go run ./cmd/server
```

The server starts on `http://localhost:2200`. Keep that PowerShell window open.

In a second PowerShell window:

```powershell
curl.exe http://localhost:2200/petstore/pets/featured
```

Expected response:

```json
{"name":"Pixel","tag":"featured","id":1001,"status":"available"}
```

Open `http://localhost:2200/` to use the API Explorer. Stop the server with `Ctrl+C`.

To build a reusable Windows executable:

```powershell
New-Item -ItemType Directory -Force .\bin | Out-Null
go build -o .\bin\petstore-mock.exe ./cmd/server
.\bin\petstore-mock.exe
```

No separate installer is required.

## Run it on Linux or macOS

```bash
git clone https://github.com/JsonVerifier/mockzilla-openapi-petstore-mock-server.git
cd mockzilla-openapi-petstore-mock-server
go test ./...
go run ./cmd/server
```

Then open `http://localhost:2200/` or test the stable endpoint:

```bash
curl http://localhost:2200/petstore/pets/featured
```

## Available endpoints

| Method | Endpoint | Behaviour |
|---|---|---|
| `GET` | `/petstore/pets` | Returns a generated list of pets |
| `POST` | `/petstore/pets` | Validates the request and returns a generated pet |
| `GET` | `/petstore/pets/featured` | Always returns the same `Pixel` response |
| `GET` | `/petstore/pets/{id}` | Returns a generated pet |
| `DELETE` | `/petstore/pets/{id}` | Returns the OpenAPI-defined delete response |

The generated endpoints are for API development and testing. They do not use a persistent database, so `POST` does not permanently add a pet and `DELETE` does not permanently remove one.

## What changed from the original template

- Removed the tracked 49 MB server build artifact.
- Removed the Hello World example service and its generated registration.
- Changed the Go module to this repository's path.
- Replaced the default generated names with values such as `Byte`, `Cache`, `Proxy`, `Socket`, and `Zilla`.
- Added the Pet `status` enum: `available`, `pending`, and `sold`.
- Added `GET /petstore/pets/featured`.
- Added a fixed `Pixel` response for deterministic tests and examples.

## Customize the mock

The three main files are:

- `pkg/petstore/setup/openapi.yml` defines paths, request bodies, responses, and schemas.
- `pkg/petstore/setup/context.yml` supplies custom values used for generated data.
- `pkg/petstore/service.go` overrides generated responses with your own Go logic.

After changing the OpenAPI spec, regenerate the code and service registry.

PowerShell:

```powershell
go generate ./...
go run github.com/mockzilla/mockzilla/v2/cmd/gen/discover -output cmd/server/services_gen.go pkg
go test ./...
```

Linux or macOS:

```bash
make generate
make discover
make test
```

Do not edit `pkg/petstore/gen.go` manually. It is generated from the OpenAPI spec.

## Project structure

```text
cmd/server/                         Server entry point and service registry
pkg/petstore/service.go             Optional custom response logic
pkg/petstore/gen.go                 Generated handlers and types
pkg/petstore/setup/openapi.yml      Petstore OpenAPI specification
pkg/petstore/setup/context.yml      Custom generated data values
pkg/petstore/setup/config.yml       Latency, errors, caching, and upstream settings
resources/data/app.yml              Server and API Explorer configuration
.github/workflows/                  CI, release builds, and Mockzilla deployment
```

## Powered by Mockzilla

[Mockzilla](https://github.com/mockzilla/mockzilla) generates typed Go handlers and realistic mock responses from OpenAPI specifications. This repository is a customized example built from the [Mockzilla codegen template](https://github.com/mockzilla/mockzilla-codegen-template).

The bundled Petstore specification is a sample API for development and testing. This repository is not an official Swagger Petstore project and is not affiliated with or endorsed by SmartBear.
