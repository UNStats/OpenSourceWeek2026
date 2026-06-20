# UN System Data Commons — Getting Started

> Hackathon resources for accessing the UN System Data Commons REST API and MCP endpoints.
>
> This repository contains this guide plus a ready-to-use Postman collection and environment (in [`docs/`](docs/)). See [API interactions with Postman](#api-interactions-with-postman) below.

> List of all available statistical variables can be found (in [`docs/`](docs/)). 

## Hackathon environments

#### Custom DC

[https://cdc-un-cs-datacommons-web-service-620046630330.us-central1.run.app/](https://cdc-un-cs-datacommons-web-service-620046630330.us-central1.run.app/)

#### DC Platform

[https://dc-un-dev-dc-datacommons-service-72utkhfqvq-uc.a.run.app/](https://dc-un-dev-dc-datacommons-service-72utkhfqvq-uc.a.run.app/)

#### SDMX (experimental, data parity with DC Platform)

[https://un-sdmx-demo-dc-datacommons-service-496370955550.us-central1.run.app/](https://un-sdmx-demo-dc-datacommons-service-496370955550.us-central1.run.app/)

## Connecting to MCP endpoints

For more detailed information, follow the instructions available on [datacommons.org](http://datacommons.org), starting with [Configure the MCP server \- Docs \- Data Commons](https://docs.datacommons.org/custom_dc/mcp.html).

First identify the {{dc\_base\_url}} for the environment to be used. The MCP endpoint is available from the path `{{dc_base_url}}/mcp` (for example, [`https://dc-un-dev-dc-datacommons-service-72utkhfqvq-uc.a.run.app/mcp`](https://dc-un-dev-dc-datacommons-service-72utkhfqvq-uc.a.run.app/mcp))

### Connecting with Gemini

*Determine whether you prefer to directly modify the settings.json for your gemini installation, or potentially use the cli to directly add the MCP service.*

*To install Gemini, refer to the documentation : [https://geminicli.com/docs/](https://geminicli.com/docs/)*

#### Option a: Modify the gemini settings.json

```
$ open .gemini/settings.json

# Ensure the contents include:
{
  ... // Note there may be additional settings preceding the mcpServers
  "mcpServers": {
    "undata-cdc": {
      "httpUrl": "https://dc-un-dev-dc-datacommons-service-72utkhfqvq-uc.a.run.app/mcp"
    }
  }
}
```

#### Option b: Associate the UN MCP server via cli

*Note that the cli has the same effect by modifying the settings.json above*

```
$ gemini mcp add undata {{dc_base_url}}/mcp

e.g.
$ gemini mcp add --transport http undata https://dc-un-dev-dc-datacommons-service-72utkhfqvq-uc.a.run.app/
```

#### Post connection

Open the gemini console and confirm the connection is valid:

```
$ gemini

> /mcp

...
Note: First startup may take longer. Tool availability will update automatically.

Configured MCP servers:

🟢 undata - Ready (2 tools)
  Tools:
  - mcp_undata_get_observations
  - mcp_undata_search_indicators

# And for more details on the MCP tools
$ /mcp desc undata
```

If you do not see a green `Ready` for the mcp instance, the connection is incomplete and tool calls may not succeed.

**Using MCP**   
List the available MCP tools to understand the types of queries supported and data available from the MCP service.

```
$ /mcp list

$ What variables are available from undata for describing climate change?

...
Data Commons provides a wide range of variables for describing climate change, which are primarily organized under
  Greenhouse Gas / CO₂ Emissions and UN Sustainable Development Goal 13 (SDG 13: Climate Action).

  Here is a structured breakdown of the key variables available, along with their unique identifier (DCID):

  ---

  1. Greenhouse Gas (GHG) and Carbon Dioxide (CO₂) Emissions
  These variables track actual emission amounts overall, per capita, or by specific sectors.

  Total & Per Capita Emissions
   * Total Greenhouse Gas Emissions: Annual_Emissions_GreenhouseGas
   * Non-Biogenic Greenhouse Gas Emissions: Annual_Emissions_GreenhouseGas_NonBiogenic
   * CO₂ Emissions (Annual): eia/INTL.4008-8-MMTCD.A
   * CO₂ Emissions Per Capita: Amount_Emissions_CarbonDioxide_PerCapita

  Sector-Specific Emissions (CO₂ vs. GHG)
...
```

### Connecting with Claude Code

Configuring the MCP connection with Claude is direct from the console:

```
+$ claude mcp add --transport http undata https://dc-un-dev-dc-datacommons-service-72utkhfqvq-uc.a.run.app/mcp
```

#### Post connection

From within the console, Claude Code provides rich inspection utilities for connected MCP services.

```
$ claude

> /mcp list
```

From the MCP list, you can press `return` to select the `undata` instance to see the connection status and view available MCP tools. To explore available variables, escape to the console and ask relevant questions, e.g. `> What variables describe clean water access in Africa?`

## API Interactions with Postman

While it's possible to use curl to issue all of your API requests for development and testing, [Postman](https://www.getpostman.com/) makes API exploration easier.

This repository includes a Postman collection of API endpoints plus the relevant environment variables. They work with both Custom Data Commons and [datacommons.org](http://datacommons.org) by changing the `base_url` in the environment.

### Files

Both files live in the [`docs/`](docs/) folder:

* [`datacommons_rest_apis.postman_collection.json`](docs/datacommons_rest_apis.postman_collection.json) — the Postman collection.
* [`datacommons_rest_apis.postman_environment.json`](docs/datacommons_rest_apis.postman_environment.json) — the Postman environment, with configurable `base_url` and `api_key`.

### Referenced APIs

The collection includes REST V2 sample requests for:

* `/v2/node` — fetch node property labels and linked values.
* `/v2/observation` — fetch statistical observations, available variables, facets, and filtered observations.
* `/v2/resolve` — find DCIDs before using Node or Observation.

The requests use POST bodies from the REST V2 examples, so relation expressions do not need to be URL-encoded by hand.

### How to configure

Import both JSON files into Postman, select the `Data Commons REST APIs Environment`, then set:

* `base_url` — API base URL without a trailing slash and without the API version.
  * Custom DC pattern: `https://<CUSTOM_HOST>/core/api` (e.g. `https://dc-un-dev-dc-datacommons-service-72utkhfqvq-uc.a.run.app/core/api`)
  * datacommons.org pattern: `https://api.datacommons.org`
* `api_key` — leave blank for Custom Data Commons. Set only when calling datacommons.org.

The collection adds the `X-API-Key` header only when `api_key` is non-empty. Keeping `base_url` at `/core/api` (instead of `/core/api/v2`) leaves room to add other API families later.

### Loading in Postman

1. Open Postman and choose the workspace you want to use. You do not need to create an account or log in to use the "thin" local client.
2. Click `Import` in the sidebar.
3. Select both JSON files from the [`docs/`](docs/) folder.
4. Click `Import`.
5. Select the `Data Commons REST APIs Environment`.
6. Set `base_url` to the Data Commons endpoint, for example:

   ```text
   https://<CUSTOM_HOST>/core/api
   ```

7. Keep `api_key` blank for Custom Data Commons.
8. Open the collection and run the V2 Node / Observation / Resolve requests.

## SDMX API Usage (experimental)

This note summarizes the SDMX API surface currently available for hackathon usage.

#### SDMX Environment (with data parity with DC Platform)

[https://un-sdmx-demo-dc-datacommons-service-496370955550.us-central1.run.app/](https://un-sdmx-demo-dc-datacommons-service-496370955550.us-central1.run.app/)

### Support
- SDMX version 3.0
- REST endpoints use SDMX v3 style paths under `/sdmx/v3`.
- Dataflow: `dataflow/DATACOMMONS/DF_OBSERVATIONS/1.0.0`

### Components

There is no DSD endpoint yet. Use this model for now:

| Type | Component |
| --- | --- |
| Dimension | `variableMeasured` |
| Dimension | `observationAbout` |
| Dimension | `unit` |
| Dimension | `measurementMethod` |
| Dimension | `observationPeriod` |
| Dimension | `provenance` |
| Time dimension | `TIME_PERIOD` |
| Measure | `OBS_VALUE` |
| Attribute | `scalingFactor` |

### Data API

```text
GET /sdmx/v3/data/dataflow/DATACOMMONS/DF_OBSERVATIONS/1.0.0/*
```

Output:

- JSON-STAT by default
- SDMX-CSV 2.0 with `format=csv`

Required filters:

- `c[variableMeasured]`
- `c[observationAbout]`

Optional filter:

- `c[TIME_PERIOD]`

CSV example:

```sh
curl -g 'https://un-sdmx-demo-dc-datacommons-service-496370955550.us-central1.run.app/core/api/sdmx/v3/data/dataflow/DATACOMMONS/DF_OBSERVATIONS/1.0.0/*?c[variableMeasured]=undata/sdg/AG_FLS_INDEX.PRODUCT--AGG_ANIMAL_PROD&c[observationAbout]=Earth&format=csv'
```

Restrictions:

- Filters only support comma OR values. `+`, duplicate filters, and operators
  such as `ge:2020` are not supported.
- Only the `*` key is supported.
- SDMX Data JSON and SDMX Data XML are not supported.

### Availability API

```text
GET /sdmx/v3/availability/dataflow/DATACOMMONS/DF_OBSERVATIONS/1.0.0/*/{componentID}
```

Output:

- SDMX-JSON 2.0

Required filter:

- `c[variableMeasured]`

Example:

```sh
curl -g 'https://un-sdmx-demo-dc-datacommons-service-496370955550.us-central1.run.app/core/api/sdmx/v3/availability/dataflow/DATACOMMONS/DF_OBSERVATIONS/1.0.0/*/provenance?c[variableMeasured]=undata/eclac/EDU_YEARS.AGE--AGE-Y15T24__SEX--SEX-F__URBANIZATION--URBANIZATION-R'
```

Supported lookup components:

- `observationAbout`
- `variableMeasured`
- `unit`
- `measurementMethod`
- `observationPeriod`
- `provenance`

Restrictions:

- Only `c[variableMeasured]` is allowed as a filter.
- `TIME_PERIOD` is not supported.
- `references=all` is not supported.
- `mode=available` is not supported.
