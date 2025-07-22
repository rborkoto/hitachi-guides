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

Summary:
- Enable GCP APIs and Datadog integration
- Deploy using serverless-init sidecar for best results
- Initialize dd-trace in app (or via NODE_OPTIONS)
- Set Datadog environment variables
- Validate in Datadog UI for metrics/logs/traces

______



