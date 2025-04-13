# Event-Driven Image Processor with Azure Functions and Service Bus

This app processes images uploaded to Blob Storage, resizes them using an Azure Function triggered by an event, and sends notifications via Azure Service Bus. It covers serverless computing, event-based solutions, messaging, and storage.

## Covered AZ-204 Topics
- Develop Azure compute solutions: Azure Functions.
- Develop for Azure storage: Azure Blob Storage.
- Implement Azure security: Managed identities.
- Monitor, troubleshoot, and optimize Azure solutions: Application Insights.
- Connect to and consume Azure services: Azure Service Bus, Event Grid.
## Architecture
- Trigger: Blob Storage upload triggers an Azure Function.
- Processing: Function resizes the image using a library like ImageSharp.
- Messaging: Sends a message to Azure Service Bus topic for notifications.
- Monitoring: Application Insights for telemetry.
- Security: Managed identity for accessing resources.
## Implementation Steps
###	Set Up Blob Storage and Function Trigger 
- Goal: Trigger processing on upload (covers Azure Functions, Blob Storage).
- Create an Azure Function with a Blob trigger:
```bash
func init ImageProcessor --dotnet
cd ImageProcessor
func new --template "BlobTrigger" --name ProcessImage
```
- Add image resizing logic using SixLabors.ImageSharp

```csharp
CollapseWrapCopy
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Processing;

[FunctionName("ProcessImage")]
public static async Task Run(
    [BlobTrigger("images/{name}", Connection = "StorageConnection")] Stream inputBlob,
    [Blob("images/processed/{name}", FileAccess.Write, Connection = "StorageConnection")] Stream outputBlob)
{
    using var image = Image.Load(inputBlob);
    image.Mutate(x => x.Resize(100, 100));
    await image.SaveAsync(outputBlob, image.Metadata.DecodedImageFormat);
}
```
- Deploy to Azure
```bash
func azure functionapp publish imageprocessor
```