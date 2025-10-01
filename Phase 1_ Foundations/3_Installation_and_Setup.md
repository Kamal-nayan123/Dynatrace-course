# Phase 1: Foundations - Installation & Setup

This guide will walk you through the initial steps of getting Dynatrace up and running. We'll focus on the SaaS free trial, which is the quickest way to get started.

## 1. Dynatrace SaaS Free Trial Account Setup

Getting a free trial is straightforward. It gives you 15 days to explore the full-featured platform.

### Steps:
1.  **Navigate to the Dynatrace Website:** Open your web browser and go to the [Dynatrace free trial page](https://www.dynatrace.com/trial/).
2.  **Sign Up:** You'll need to provide your business email address and some basic information.
3.  **Confirm Your Email:** Check your inbox for a confirmation email from Dynatrace. Click the link inside to verify your account and set a password.
4.  **Select Your Region:** You'll be prompted to choose a region where your Dynatrace environment (your "tenant") will be hosted. Pick the one closest to you or your primary user base for the best performance.
5.  **Tenant Provisioning:** Dynatrace will take a few minutes to provision your dedicated SaaS environment. Once it's ready, you will be logged into your new Dynatrace account.

You now have a fully functional Dynatrace environment! The next step is to start sending data to it.

## 2. Installing Dynatrace OneAgent on Windows/Linux

The OneAgent is the key to collecting data. The Dynatrace UI itself will guide you through this process, but here’s a summary of what to expect.

### In the Dynatrace UI:
1.  After logging in for the first time, you'll likely see a welcome screen or a banner prompting you to install OneAgent.
2.  Click on **"Deploy Dynatrace"** or find the **"Hub"** in the left-hand menu.
3.  Select **"OneAgent"**. Dynatrace will guide you to download the correct installer for your operating system.

### Installing on Linux:
The Dynatrace UI will provide you with a `wget` or `curl` command to download the installer script, followed by a command to run it. It will look something like this:

**Important:** The `TENANT_ID` and `PAAS_TOKEN` are unique to your environment and will be pre-filled in the command you copy from the UI.

```bash
# Example command - DO NOT copy this, use the one from your UI
# 1. Download the installer script
wget -O Dynatrace-OneAgent-Linux.sh "https://your-tenant.live.dynatrace.com/api/v1/deployment/installer/agent/unix/default/latest?Api-Token=YOUR_API_TOKEN&arch=x86"

# 2. Run the installer with root privileges
sudo /bin/sh Dynatrace-OneAgent-Linux.sh
```
The script will handle the installation, and within minutes, your host will appear in the Dynatrace UI.

### Installing on Windows:
The UI will provide a link to download a `.exe` installer.

1.  **Download the installer** from the link provided in the Dynatrace UI.
2.  **Run the installer:** Double-click the downloaded file.
3.  **Follow the wizard:** The installation wizard is straightforward. The installer already includes your tenant information, so you typically just need to click through the steps.
4.  Once the installation is complete, the OneAgent service will start automatically, and the host will report back to your Dynatrace tenant.

## 3. Installing ActiveGate (Optional, but important)

### What is it for?
The ActiveGate is a proxy that sits between your OneAgents and the Dynatrace Cluster. You don't always need it, but it's crucial in certain scenarios:
*   **Firewall/Network Restrictions:** When your monitored hosts don't have direct internet access. OneAgents can send data to the ActiveGate, and the ActiveGate then forwards it to Dynatrace.
*   **Cloud Monitoring:** To monitor cloud platforms like AWS, Azure, and GCP, an ActiveGate pulls API data from the cloud provider.
*   **Synthetic Monitoring:** To run synthetic tests from a private location (e.g., from within your own data center).
*   **Reducing Network Load:** It bundles and compresses data from many OneAgents, reducing the outbound traffic from your network.

### Installation:
The process is similar to installing the OneAgent.
1.  In the Dynatrace UI, go to the **Hub**.
2.  Select **"ActiveGate"**.
3.  Choose your operating system (Linux or Windows).
4.  You will be given a command or an installer file, just like with the OneAgent.
5.  Run the installer on a dedicated server that has access to both your monitored hosts and the Dynatrace cluster (or the internet for SaaS).

## 4. First Look at Dashboards and Smartscape

Once your first OneAgent is installed and reporting data:

*   **Hosts Screen:** Navigate to **"Hosts"** from the left menu. You should see your newly monitored server listed there. You can click on it to see a wealth of information: CPU, memory, disks, network, processes, and more.
*   **Smartscape:** Navigate to **"Smartscape"**. This is where the magic happens. You will see a visual representation of your environment. It might be simple at first (just one host), but as you install more OneAgents, it will automatically build a map showing the connections between your hosts, the processes running on them, and the services they provide.
*   **Dashboards:** Check out the pre-built dashboards. There are default dashboards for Infrastructure, Applications, and more. These will start to populate with data as it comes in.

Congratulations! You have successfully set up Dynatrace and started monitoring your first host. The next phase will be to explore what you can do with all this data.