# Scheduling

## Architecture Reference

For a description of the architecture, see
[Scheduling](../../microservices/support/scheduler/Ch-Scheduling.md)
## Introduction

The following API RESTful Web Service(s) for EdgeX Foundry.

[Scheduling V1 API Swagger Documentation](https://app.swaggerhub.com/apis-docs/EdgeXFoundry1/support-scheduler)
<!-- [Scheduling API HTML Documentation](support-scheduler.html) -->

## Description

**Scheduler Service** - a service that can be used to schedule
invocation of a URL. Requires the use of interval(s), and interval
action(s).

**Interval(s)**

:   -   **name** - unique name of the service.
    -   **start** - identifies when the operation starts. Expressed in
        ISO 8601 YYYYMMDD't'HHmmss format. Empty means now.
    -   **end** - identifies when the operation ends. Expressed in ISO
        8601 YYYYMMDD't'HHmmss format. Empty means never
    -   **frequency** - How often the specific resource needs to be polled. It represents as a duration string. The format of this field is to be an unsigned integer followed by a unit which may be "ns", "us" (or "µs"), "ms", "s", "m", "h" representing nanoseconds, microseconds, milliseconds, seconds, minutes or hours. Eg, "100ms", "24h" Empty means no
        frequency.

**Interval Action(s)**

:   -   **name** - unique name of the interval action.
    -   **interval** - unique name of an existing interval.
    -   **target** - the recipient of the interval action (ergo service
        or name).
    -   **protocol** - the protocol type to be used to contact the
        target. (example HTTP).
    -   **httpMethod** - HTTP protocol verb.
    -   **address** - the endpoint server host.
    -   **port** - the desired port.
    -   **path** - the api path which will be acted on.
    -   **parameters** - (optional) parameters which will be included in
        the BODY tag for HttpMethods. Any parameters that should be
        provided to the call, e.g. {"milliseconds":86400000}

## Examples

Create an interval upon which the scheduler will operate :

curl -X POST -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{

:   "name": "midnight", "start": "20180101T000000",
    "frequency": "24h"}'
    "<http://localhost:48081/api/v1/interval>"

Example of a second interval which will run every 20 seconds :

curl -X POST -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{

:   "name": "every20s", "start":"20000101T000000", "end":"",
    "frequency":"20s"}'
    "<http://localhost:48081/api/v1/interval>"

Create an interval action that will invoke the interval action (drive
the scrubber) in core-data :

curl -X POST -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{

:   "name": "scrub-pushed-events", "interval": "midnight",
    "target": "core-data", "protocol": "http", "httpMethod":
    "DELETE", "address": "localhost", "port": 48080, "path":
    "/api/v1/event/scrub"}'
    "<http://localhost:48085/api/v1/intervalaction>"

This is a Random-Boolean-Device which created by edgex-device-virtual
that connects every 20 seconds. :

curl -X POST -H "Content-Type: application/json" -d '{

:   "name": "put-action", "interval": "every20s", "target":
    "edgex-device-modbus", "protocol": "http", "httpMethod":
    "PUT", "address": "localhost", "port": 49990,
    "path":"/api/v1/device/name/Random-Boolean-Device/RandomValue\_Bool",
    "parameters": "{"RandomValue\_Bool":
    "true","EnableRandomization\_Bool": "true"}"

}' "<http://localhost:48085/api/v1/intervalaction>"
