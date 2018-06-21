
API Reference
=============

Generic Ingestion
-----------------

The generic ingestion specification provides enough context to map the ping to a schema.

The *namespace* distinguishes different data collection systems from each other.
Telemetry is the largest consumer of the ingestion system to date.
The *document type* differentiates messages in the ingestion pipeline.
For example, the schemas of the main and crash pings share little overlap.
The *document version* allows for versioning between documents.
Finally, the *document id* is used to check for duplicates.
This is validated in the running pipeline, but not supported here.

::

    POST /submit/<namespace>/<doctype>/<docversion/[<docid>]


The schemas are mounted under the application directory `/app/resources/schemas` with the following convention::

    /schemas/<NAMESPACE>/<DOCTYPE>.<DOCVERSION>.schema.json


The following tree shows a subset of the resource directory::

    /app/resources
    └── schemas
        ├── telemetry
        │   ├── anonymous
        │   │   └── anonymous.4.schema.json
        │   ├── core
        │   │   ├── core.1.schema.json
        │   │   ├── core.2.schema.json
        │   │   ├── core.3.schema.json
        │   │   ├── core.4.schema.json
        │   │   ├── core.5.schema.json
        │   │   ├── core.6.schema.json
        │   │   ├── core.7.schema.json
        │   │   ├── core.8.schema.json
        │   │   └── core.9.schema.json
        │   ├── crash
        │   │   └── crash.4.schema.json
        │   ├── main
        │   │   └── main.4.schema.json
        │   └─── ...
        │   │   ├── ...
        │   │   ├── ...
        │   │   └── ...
        └── testing
            └── test
                └── test.1.schema.json


Telemetry Ingestion
-------------------

The edge-validator implements the Edge Server `POST request specification <https://docs.telemetry.mozilla.org/concepts/pipeline/http_edge_spec.html#postput-request>`_ for Firefox Telemetry. 
The validator will reroute the request as a generic ingestion request.

::

    POST /submit/<namespace>/<docid>/<appName>/<appVersion>/<appUpdateChannel>/<appBuildId>
