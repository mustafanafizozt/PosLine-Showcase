# Architecture Overview

This document is a high-level portfolio description based on the developed PosLine workflows. It does not include internal deployment addresses, proprietary schemas, credentials or implementation source.

## Management Console

The React console provides terminal inventory, group management, application workflows, diagnostics, configuration and bulk-job visibility. REST provides initial data and explicit actions. WebSocket events update the operational view as results arrive.

## Backend

Spring Boot owns payload validation, online-device checks, command creation, MQTT publishing and response ingestion. Command records retain progress and final results so an operator can distinguish delivery from successful execution.

Bulk jobs resolve target devices, retain an operation-time target snapshot and dispatch through existing per-terminal command paths with controlled parallelism. Acknowledgements and final results remain attributable to individual devices.

H2 with Flyway is used for development persistence. Redis stores live state such as recent heartbeat information. A production database or large-scale deployment is not claimed by this showcase.

## MQTT Control Plane

EMQX routes small commands, responses and telemetry. Each terminal has an isolated namespace and an mTLS client identity. Backend reachability decisions use heartbeat recency to distinguish online, stale and offline devices.

Commands are correlated by identifier and terminal identity. Reception, execution, success, failure, rejection and expiry are distinct states; an acknowledgement alone is not a completed operation.

## Android POS Agent

The Kotlin Agent runs the device-side communication and command handlers. Android permissions and platform capabilities determine whether a device action can execute. Unsupported or unauthorized actions return structured failures instead of successful-looking acknowledgements.

Companion payment and TaxiPos applications use explicit AIDL/Binder interfaces protected by signature permissions. The companion applications do not need access to MQTT credentials.

## Large Payloads and Privacy

APK files are delivered over HTTP in the development workflow; metadata, hashes and signatures are used for download verification. Progress is reported separately from the command's final result.

Screen capture requires terminal-user consent. JPEG data is uploaded over HTTPS and delivered to the requesting console through a transient WebSocket event. Images are not part of persistent command history.

## Illustrations

The repository's diagram and UI images are newly created conceptual assets. The UI uses synthetic data and is not a reproduction of an approved private company screen. These assets describe responsibilities and workflows without exposing the private implementation.
