# Phase 2: Core Monitoring - Infrastructure Monitoring

## Introduction
Infrastructure monitoring is the foundation of observability. It involves collecting and analyzing data from the foundational layers of your IT environment: the physical or virtual hosts, the processes they run, the networks that connect them, and the cloud platforms they live on. Dynatrace excels at this by providing deep, automatic, and context-rich visibility into your entire infrastructure stack.

## 1. Host, Process, and Network Monitoring

### Host Monitoring
*   **Technical Definition:** Host monitoring involves tracking the health and performance of the underlying operating system and hardware resources (physical or virtual). Dynatrace OneAgent, once installed on a host, collects a wide range of metrics, including CPU utilization, memory consumption, disk I/O and space, and network interface traffic. This is presented in a unified "Host" view.
*   **Simple Definition:** This is the equivalent of a complete physical check-up for your servers. It checks their vital signs—how busy their "brain" (CPU) is, how much "short-term memory" (RAM) they're using, and how their "circulatory system" (network) is performing.
*   **Real-time Example:** A system administrator gets an alert that a server's CPU has been at 95% for 30 minutes. They open the Host screen in Dynatrace and can immediately see a chart of the CPU usage over time, which processes are consuming the most CPU, and any recent events (like deployments or configuration changes) that might have caused the spike.

### Process Monitoring
*   **Technical Definition:** Dynatrace automatically discovers every process running on a monitored host. It goes beyond simple `top` or `task manager` views by providing deep insights into resource consumption (CPU, memory), network connections, and dependencies for each process. This allows for precise identification of resource-hungry processes.
*   **Simple Definition:** If host monitoring is checking the server's overall health, process monitoring is like looking at the individual organs. You can see exactly which application or program (process) is working hard, consuming resources, or causing problems.
*   **Real-time Example:** The Host screen shows high memory usage. By clicking on the "Processes" tab, the admin sees that a specific `java.exe` process has a memory leak, with its memory consumption steadily climbing over the last few hours. Dynatrace pinpoints the exact process, saving the admin from having to guess.

### Network Monitoring
*   **Technical Definition:** Dynatrace monitors network communication at the process level. It tracks the quality of all TCP connections, measuring traffic volume, connectivity rates, and retransmission rates. This provides a clear picture of how well processes are communicating with each other across the network.
*   **Simple Definition:** Dynatrace listens to the "conversations" between your applications. It can tell you if the conversations are clear and fast, or if they are getting interrupted (retransmissions), which is a sign of network problems.
*   **Real-time Example:** An application is slow, but the host and process metrics look fine. By looking at the network data in Dynatrace, an engineer sees a high retransmission rate between the application server process and the database process, even though they are on different hosts. This indicates a network bottleneck or faulty hardware between the two servers is the root cause of the slowdown.

## 2. Kubernetes & Container Monitoring

*   **Technical Definition:** Dynatrace provides specialized, out-of-the-box observability for containerized environments like Kubernetes, OpenShift, and Docker. By deploying the Dynatrace Operator to a Kubernetes cluster, Dynatrace automatically discovers all nodes, pods, containers, and services. It collects metrics on resource utilization (CPU/memory requests and limits), pod health, and Kubernetes events, and integrates this data into the Smartscape topology.
*   **Simple Definition:** Modern applications are often run in small, isolated "boxes" called containers, which are managed by systems like Kubernetes. Dynatrace understands this modern setup completely. It automatically maps out your entire container environment and shows you how everything is connected and performing.
*   **Diagram: Kubernetes Monitoring with Dynatrace**
    ```mermaid
    graph TD
        subgraph "Kubernetes Cluster"
            Operator("Dynatrace Operator")

            subgraph "Worker Node 1"
                N1_OA("OneAgent DaemonSet") --> N1_P1("Pod: App") & N1_P2("Pod: Database")
            end

            subgraph "Worker Node 2"
                N2_OA("OneAgent DaemonSet") --> N2_P1("Pod: API") & N2_P2("Pod: Cache")
            end

            Operator -- "manages rollout" --> N1_OA
            Operator -- "manages rollout" --> N2_OA
        end

        style Operator fill:#9f9,stroke:#333,stroke-width:2px
    ```
*   **Real-time Example:** A developer notices that their shopping cart service is unreliable. In the Dynatrace Kubernetes dashboard, they can see an overview of their cluster's health. They drill down to the `cart-service` workload and see that several pods are in a `CrashLoopBackOff` state. Dynatrace provides the container logs directly in the UI, which show an "Out of Memory" error. The developer realizes the memory limit for the pod is set too low, and they can quickly fix the issue.

## 3. Cloud Integrations (AWS, Azure, GCP)

*   **Technical Definition:** While OneAgent provides deep visibility into hosts you control (like EC2 instances or VMs), many cloud services are fully managed (e.g., AWS RDS, Azure Functions, Google BigQuery). You can't install an agent on them. For these, Dynatrace uses API-level integrations. An **ActiveGate** is configured with secure credentials to your cloud account. It then periodically queries the cloud provider's monitoring APIs (like AWS CloudWatch) and ingests metrics, properties, and events for all your cloud services.
*   **Simple Definition:** Dynatrace not only monitors the servers you run yourself but also connects directly to your AWS, Azure, or Google Cloud account. It pulls in performance data for all the services that the cloud provider manages for you, like databases or serverless functions. This gives you a single place to see everything.
*   **Real-time Example:** An application is experiencing slow response times. Smartscape shows that the application makes calls to an AWS RDS database. The OneAgent data from the application server looks fine. However, by looking at the Dynatrace dashboard, which includes metrics from the AWS integration, the team sees a spike in "Database Read Latency" for that specific RDS instance. The data was pulled from AWS CloudWatch but is shown in context within Dynatrace, proving the managed database is the source of the problem. This prevents guesswork and finger-pointing between the DevOps and DBA teams.