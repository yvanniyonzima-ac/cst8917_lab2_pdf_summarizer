<!--
---
description: This end-to-end sample shows how implement an intelligent PDF summarizer using Durable Functions. 
page_type: sample
products:
- azure-functions
- azure
urlFragment: durable-func-pdf-summarizer
languages:
- python
- bicep
- azdeveloper
---
-->

# Intelligent PDF Summarizer
The purpose of this sample application is to demonstrate how Durable Functions can be leveraged to create intelligent applications, particularly in a document processing scenario. Order and durability are key here because the results from one activity are passed to the next. Also, calls to services like Cognitive Service or Azure Open AI can be costly and should not be repeated in the event of failures.

This sample integrates various Azure services, including Azure Durable Functions, Azure Storage, Azure Cognitive Services, and Azure Open AI.

The application showcases how PDFs can be ingested and intelligently scanned to determine their content.

![Architecture Diagram](./media/architecture_v2.png)

The application's workflow is as follows:
1.	PDFs are uploaded to a blob storage input container.
2.	A durable function is triggered upon blob upload.
- - Downloads the blob (PDF).
- - Utilizes the Azure Cognitive Service Form Recognizer endpoint to extract the text from the PDF.
- - Sends the extracted text to Azure Open AI to analyze and determine the content of the PDF.
- - Save the summary results from Azure Open AI to a new file and upload it to the output blob container.

Below, you will find the instructions to set up and run this app locally..

## Prerequsites
- [Create an active Azure subscription](https://learn.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).
- [Install the latest Azure Functions Core Tools to use the CLI](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local)
- Python 3.9 or greater
- Access permissions to [create Azure OpenAI resources and to deploy models](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/role-based-access-control).
- [Start and configure an Azurite storage emulator for local storage](https://learn.microsoft.com/azure/storage/common/storage-use-azurite).

## local.settings.json
You will need to configure a `local.settings.json` file at the root of the repo that looks similar to the below. Make sure to replace the placeholders with your specific values.

```json
{
  "Values": {
    "AzureWebJobsStorage": "UseDevelopmentStorage=true",
    "AzureWebJobsFeatureFlags": "EnableWorkerIndexing",
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "BLOB_STORAGE_ENDPOINT": "<BLOB-STORAGE-ENDPOINT>",
    "COGNITIVE_SERVICES_ENDPOINT": "<COGNITIVE-SERVICE-ENDPOINT>",
    "COGNITIVE_SERVICES_KEY":"<COGNITIVE-SERIVE-KEY>",
    "AZURE_OPENAI_ENDPOINT": "AZURE-OPEN-AI-ENDPOINT>",
    "AZURE_OPENAI_KEY": "<AZURE-OPEN-AI-KEY>", 
    "CHAT_MODEL_DEPLOYMENT_NAME": "<AZURE-OPEN-AI-MODEL>"
  }
}
```

**NOTE:** For the azure open ai key and endpoints, use the ones in the Ai Foundry portal found under the Endpoint section of your deployment.

## Running the app locally
1. Start Azurite: Begin by starting Azurite, the local Azure Storage emulator.

2. Install the Requirements: Open your terminal and run the following command to install the necessary packages:

```bash
python3 -m pip install -r requirements.txt
```
3. Create two containers in your storage account. One called `input` and the other called `output`. 

4. Start the Function App: Start the function app to run the application locally.

```bash
func start --verbose
```

5. Upload PDFs to the `input` container. That will execute the blob storage trigger in your Durable Function.

6. After several seconds, your appliation should have finished the orchestrations. Switch to the `output` container and notice that the PDFs have been summarized as new files. 

>Note: The summaries may be truncated based on token limit from Azure Open AI. This is intentional as a way to reduce costs. 

## 📽️ Demo Video

Watch the demo on [YouTube](https://youtu.be/VvvXl2BrwqA)

