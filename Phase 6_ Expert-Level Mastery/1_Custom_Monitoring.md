# Phase 6: Expert-Level Mastery - Custom Monitoring

## Introduction
While Dynatrace provides unparalleled automatic, out-of-the-box observability, there are always scenarios that require extending the platform to ingest data from custom applications, specialized hardware, or niche technologies. Expert-level mastery involves knowing how to close these monitoring gaps using Dynatrace's flexible data ingestion capabilities, including its APIs, extensions framework, and native support for OpenTelemetry.

## 1. Creating Custom Metrics with Dynatrace API

*   **Technical Definition:** Dynatrace provides a powerful Metrics Ingest API that allows you to send any custom time-series data directly to the platform. Metrics must be formatted in the Dynatrace Metrics Ingestion Protocol, which is a simple, line-based text format: `metric.key,dimension1=value1,dimension2=value2 metric_value timestamp`. You can send this data via a standard authenticated HTTP POST request.
*   **Simple Definition:** This is a direct, programmable way to "teach" Dynatrace about new numbers you want to track. Your own application code or a custom script can send any metric you can dream of—like "active users in our game" or "loaves of bread baked per hour"—directly to Dynatrace so it can be dashboarded and alerted on like any other metric.
*   **Practical Example:** A company has a custom-built Python application that processes batch jobs. They want to monitor the number of jobs processed and the average processing time. The developers add a few lines of code to the application using the `requests` library:
    ```python
    import requests

    def send_metrics(jobs_processed, avg_time_ms):
        metric_payload = f"custom.app.jobs.processed,source=batch_app {jobs_processed}\\n"
        metric_payload += f"custom.app.jobs.processing_time,source=batch_app {avg_time_ms}"

        # DYNATRACE_URL and DYNATRACE_API_TOKEN are environment variables
        response = requests.post(
            f"{DYNATRACE_URL}/api/v2/metrics/ingest",
            headers={"Authorization": f"Api-Token {DYNATRACE_API_TOKEN}", "Content-Type": "text/plain"},
            data=metric_payload
        )
        print(response.status_code)

    # At the end of a batch run...
    send_metrics(150, 2500)
    ```
    These two new metrics, `custom.app.jobs.processed` and `custom.app.jobs.processing_time`, are now available throughout Dynatrace for charting, anomaly detection, and custom alerting.

## 2. Using Extensions for 3rd Party Monitoring

*   **Technical Definition:** **Dynatrace Extensions** are modular packages that extend Dynatrace's data collection capabilities. They run on the **Extension Execution Controller (EEC)**, which is part of every OneAgent and ActiveGate. Extensions can be written in a declarative YAML format or using a Python SDK. They are primarily used to pull data from technologies where the OneAgent cannot be installed or that are not supported out-of-the-box (e.g., network devices, storage arrays, specialized middleware).
*   **Simple Definition:** Extensions are like official "plug-ins" for Dynatrace. If you have a piece of technology that Dynatrace doesn't automatically understand (like a firewall or a specific brand of database), you can use or create an extension to teach Dynatrace how to talk to it and pull in its performance data.
*   **Types of Extensions:**
    *   **OneAgent Extensions:** Run directly on a monitored host and are best for collecting data from a process or system local to that host.
    *   **ActiveGate Extensions:** Run on an ActiveGate and are used for monitoring remote technologies that cannot have a OneAgent installed, typically by polling their APIs or endpoints over the network.
*   **Practical Example:** An IT team needs to monitor the health of a Cisco network switch. They cannot install a OneAgent on the switch. Instead, they navigate to the Dynatrace Hub and find a "Cisco Switches (SNMP)" extension. They install this extension, and it runs on an ActiveGate within their network. They configure the extension with the IP address and SNMP credentials for their switch. The extension then starts polling the switch every minute, pulling in metrics like interface traffic, error counts, and CPU/memory utilization. It even creates a custom device entity in Smartscape for the switch, showing its relationship to the hosts that are connected to it.

## 3. OpenTelemetry Integration

*   **Technical Definition:** OpenTelemetry (OTel) is an open-source, vendor-neutral observability framework for instrumenting, generating, collecting, and exporting telemetry data (metrics, logs, and traces). Dynatrace is a fully compatible OpenTelemetry backend. You can configure your OpenTelemetry-instrumented applications or the OpenTelemetry Collector to export data directly to your Dynatrace environment. Dynatrace automatically maps OTel data to its Smartscape topology model, linking it with data collected by OneAgent.
*   **Simple Definition:** OpenTelemetry is like a universal adapter for observability. It's a standard way for any application to produce performance data. Dynatrace fully supports this standard. This means you can use open-source tools to instrument your application and then simply point it at Dynatrace to store, visualize, and analyze all that data. It's a great way to get data from technologies where a OneAgent might not be the best fit, like a custom C++ application.
*   **Diagram: OneAgent and OpenTelemetry Working Together**
    ```mermaid
    graph TD
        subgraph "Application Stack & Instrumentation"
            Svc1["Java / .NET Service"] -- Instrumented by --> Inst1("OneAgent<br>(PurePath® Technology)")
            Svc2["Go / Rust / C++ Service"] -- Instrumented by --> Inst2("OpenTelemetry<br>(Open Standard)")
        end

        subgraph "Unified Observability Platform"
            DT["Dynatrace Platform<br>(Data is unified, correlated, and analyzed)"]
        end

        Inst1 -- "PurePath® Data" --> DT
        Inst2 -- "OTel Protocol Data" --> DT
    ```
*   **Practical Example:** A company has a polyglot microservices architecture. Their primary services are written in Java and are monitored by OneAgent. However, a new high-performance data processing service is written in Rust, for which there is no OneAgent. The developers use the OpenTelemetry Rust libraries to instrument their code, generating traces for incoming requests and database calls. They configure their OTel exporter to point to their Dynatrace tenant's OTel endpoint. Now, when a request flows from a Java service (monitored by OneAgent) to the Rust service (monitored by OTel), the trace context is automatically propagated. In Dynatrace, they see a single, seamless PurePath that shows the transaction flowing through both services, providing true end-to-end visibility despite the different instrumentation methods.