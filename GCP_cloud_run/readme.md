Integrating Google Cloud Run (Node.js, V2) with Datadog

Reference these docs for updated commands :

Doc References :

Datadog Docs References:

Google Cloud Platform (GCP) Integration Setup
- https://docs.datadoghq.com/integrations/google_cloud_platform/

Google Cloud Run Integration Guide
- https://docs.datadoghq.com/integrations/google-cloud-run/?tab=nodejs

Serverless Init for Cloud Run (Advanced Instrumentation Sidecar)
- https://docs.datadoghq.com/serverless/guide/gcr_serverless_init/

Datadog Node.js APM Library
- https://docs.datadoghq.com/tracing/trace_collection/automatic_instrumentation/dd_libraries/nodejs/

Serverless (Direct Ingestion) Tracing Overview
- https://docs.datadoghq.com/tracing/serverless/

Log Collection from GCP via Pub/Sub and Dataflow
- https://docs.datadoghq.com/integrations/google_cloud_platform/?tab=cloudshell#log-collection

Infrastructure Monitoring for GCP
- https://docs.datadoghq.com/integrations/google_cloud_platform/?tab=cloudshell#metric-collection

A. Cloud Run Metrics & Logs

1. Enable Required GCP APIs

```gcloud services enable run.googleapis.com logging.googleapis.com monitoring.googleapis.com```

2. Set Up Google Cloud Integration in Datadog
- Go to Integrations → Google Cloud Platform in Datadog
- Follow official setup steps:
- Create a service account with roles (Monitoring Viewer, Logging Viewer, etc.)
- Grant token-impersonation access to Datadog
- Upload the service account key in Datadog
- Enable Cloud Run in resource collection
- Cloud Run metrics and logs will now appear in Datadog

3. (Optional) Log Forwarding via Pub/Sub + Dataflow
- Create a Cloud Logging sink and export logs to Pub/Sub
- Deploy the Datadog Dataflow template to stream logs into Datadog
- Datadog Log Collection from GCP

⸻

B. Distributed Tracing (APM) for Node.js on Cloud Run

1. Install Datadog Tracer

npm install dd-trace

2. Initialize Tracer in Your App

```// First import in app.js or server.js
require('dd-trace').init({
  service: 'my-node-app',
  env: process.env.DD_ENV || 'production'
});
```
Or for Cloud Run Functions (Gen2):
```
NODE_OPTIONS="--require dd-trace/init"
```
3. (Recommended) Use Datadog serverless-init Sidecar

Dockerfile Example:
```
FROM gcr.io/datadoghq/serverless-init:latest
COPY . /app
WORKDIR /app
CMD ["node", "app.js"]

	•	This starts DogStatsD, trace agent, and log collection automatically
```
4. Set Environment Variables
```
DD_API_KEY=your_datadog_api_key
DD_SITE=datadoghq.com
DD_ENV=production
DD_SERVICE=my-node-app
```
Set these with 
```
gcloud run deploy --set-env-vars or in your Dockerfile/CI.
```
⸻

C. Validation in Datadog
- Metrics & Logs: View under Infrastructure → Google Cloud Platform
- APM Traces: View under APM → Services

<Refer the docs in the start of the document please>

⸻

# Datadog Tracing for Google Cloud Run Functions (Gen 2, Java)

This guide explains how to enable Datadog APM tracing for Java-based Google Cloud Run Functions (Gen 2) using the Datadog serverless-init sidecar and environment variables—no code changes required.

⸻

Steps to Enable Datadog Tracing

1. Do Not Change Your App Code
	•	You do not modify your Java application code to add tracing.

2. Set Up the Datadog Serverless-Init Sidecar
	•	Add gcr.io/datadoghq/serverless-init:latest as a sidecar container in your Cloud Run function YAML or via the GCP UI.
	•	Mount a shared volume (e.g., /datadog-init) between the function and the sidecar.

3. Configure Environment Variables

Set these environment variables in your Cloud Run function configuration:

```
JAVA_TOOL_OPTIONS="-javaagent:/datadog-init/javaagent.jar"
DD_API_KEY=your_datadog_api_key
DD_SITE=datadoghq.com
DD_SERVICE=your-service-name
DD_ENV=your-environment
DD_SERVERLESS_LOG_PATH=/tmp/ddserverless/logs
```

	•	JAVA_TOOL_OPTIONS tells the JVM to use the Datadog agent jar provided by the sidecar.
	•	The DD_ variables configure APM reporting to Datadog.
	•	DD_SERVERLESS_LOG_PATH enables log collection if desired.

4. Deploy Your Function With Sidecar and Volumes
	•	Deploy the Cloud Run function and sidecar using the GCP UI or a YAML file, ensuring both containers have access to the shared volume.
	•	The sidecar will automatically manage the Java agent and send traces/logs directly to Datadog.

5. Validate in Datadog UI
	•	Open APM > Services in Datadog to see your function traces.
	•	Check Logs for function log events if log path is enabled.

⸻

- https://docs.datadoghq.com/serverless/google_cloud_run/functions/?tab=java
- https://docs.datadoghq.com/serverless/guide/gcr_serverless_init/
- https://docs.datadoghq.com/tracing/setup_overview/setup/java/

⸻




