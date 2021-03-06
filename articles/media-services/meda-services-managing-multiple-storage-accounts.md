---
title: "Verwalten von Media Services-Medienobjekten für mehrere Speicherkonten | Microsoft-Dokumentation"
description: "In diesem Artikel erhalten Sie Anweisungen zum Verwalten von Media Services-Medienobjekten für mehrere Speicherkonten."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 4e4a9ec3-8ddb-4938-aec1-d7172d3db858
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: f813dc5f126386f9ad474e113183e7b5d4c8a71a
ms.contentlocale: de-de
ms.lasthandoff: 04/27/2017

---
# <a name="managing-media-services-assets-across-multiple-storage-accounts"></a>Verwalten von Media Services-Medienobjekten für mehrere Speicherkonten
Ab Microsoft Azure Media Services 2.2 können Sie an ein Media Services-Konto mehrere Speicherkonten anfügen. Die Möglichkeit, mehrere Speicherkonten an ein Media Services-Konto anzufügen, bietet die folgenden Vorteile:

* Lastenausgleich der Assets für mehrere Speicherkonten.
* Skalierung von Media Services für umfangreiche Inhaltsverarbeitung (derzeit ist ein Speicherkonto auf eine maximale Größe von 500 TB beschränkt). 

In diesem Thema wird erläutert, wie mehrere Speicherkonten mit [Azure Resource Manager-APIs](https://docs.microsoft.com/rest/api/media/mediaservice) und [PowerShell](/powershell/module/azurerm.media) an ein Media Services-Konto angefügt werden. Es wird zudem beschrieben, wie beim Erstellen von Medienobjekten mit dem Media Services-SDK verschiedene Speicherkonten angegeben werden. 

## <a name="considerations"></a>Überlegungen
Wenn Sie Ihrem Media Services-Konto mehrere Speicherkonten zuordnen, gelten die folgenden Überlegungen:

* Alle an ein Media Services-Konto angefügten Speicherkonten müssen sich im gleichen Rechenzentrum wie das Media Services-Konto befinden.
* Derzeit kann ein Speicherkonto nicht mehr getrennt werden, nachdem es an ein Media Services-Konto angefügt wurde.
* Das primäre Speicherkonto wird zum Zeitpunkt der Erstellung des Media Services-Kontos angegeben. Derzeit können Sie das Standardspeicherkonto nicht ändern. 
* Wenn Sie gegenwärtig ein Konto für kalten Speicher zu einem AMS-Konto hinzufügen möchten, muss das Speicherkonto ein Blobtyp sein. Zudem muss es auf nicht primär festgelegt sein.

Weitere Überlegungen:

Media Services verwendet beim Erstellen von URLs für den Streaminginhalt den Wert der **IAssetFile.Name**-Eigenschaft (z.B. http://{WAMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Aus diesem Grund ist die Prozentkodierung nicht zulässig. Der Wert der „Name“-Eigenschaft darf keines der folgenden für die [Prozentcodierung reservierten Zeichen](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) enthalten: !*'();:@&=+$,/?%#[]". Darüber hinaus darf „.“ nur einmal für die Dateinamenerweiterung vorhanden sein.

## <a name="to-attach-storage-accounts"></a>So fügen Sie Speicherkonten an  

Um Ihre Speicherkonten Ihrem AMS-Konto anzufügen, verwenden Sie [Azure Resource Manager-APIs](https://docs.microsoft.com/rest/api/media/mediaservice) und [PowerShell](/powershell/module/azurerm.media), wie im folgenden Beispiel gezeigt.

    $regionName = "West US"
    $subscriptionId = " xxxxxxxx-xxxx-xxxx-xxxx- xxxxxxxxxxxx "
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccount1Name = "skystorage1"
    $storageAccount2Name = "skystorage2"
    $storageAccount1Id = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccount1Name"
    $storageAccount2Id = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccount2Name"
    $storageAccount1 = New-AzureRmMediaServiceStorageConfig -StorageAccountId $storageAccount1Id -IsPrimary
    $storageAccount2 = New-AzureRmMediaServiceStorageConfig -StorageAccountId $storageAccount2Id
    $storageAccounts = @($storageAccount1, $storageAccount2)
    
    Set-AzureRmMediaService -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccounts $storageAccounts

### <a name="support-for-cool-storage"></a>Unterstützung für kalten Speicher

Wenn Sie gegenwärtig ein Konto für kalten Speicher zu einem AMS-Konto hinzufügen möchten, muss das Speicherkonto ein Blobtyp sein. Zudem muss es auf nicht primär festgelegt sein.

## <a name="to-manage-media-services-assets-across-multiple-storage-accounts"></a>So verwalten Sie Media Services-Medienobjekte für mehrere Speicherkonten
Im folgenden Code werden mit dem aktuellen Media Services-SDK die folgenden Aufgaben ausgeführt:

1. Anzeigen aller Speicherkonten, die dem angegebenen Media Services-Konto zugeordnet sind.
2. Abrufen des Namens des Standardspeicherkontos.
3. Erstellen eines neuen Medienobjekts im Standardspeicherkonto.
4. Erstellen eines Ausgabemedienobjekts für den Codierungsauftrag im angegebenen Speicherkonto.
   
```
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace MultipleStorageAccounts
{
    class Program
    {
        // Location of the media file that you want to encode. 
        private static readonly string _singleInputFilePath =
            Path.GetFullPath(@"../..\supportFiles\multifile\interview2.wmv");

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        private static CloudMediaContext _context;

        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Display the storage accounts associated with 
            // the specified Media Services account:
            foreach (var sa in _context.StorageAccounts)
                Console.WriteLine(sa.Name);

            // Retrieve the name of the default storage account.
            var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
            Console.WriteLine("Name: {0}", defaultStorageName.Name);
            Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);

            // Retrieve the name of a storage account that is not the default one.
            var notDefaultStroageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
            Console.WriteLine("Name: {0}", notDefaultStroageName.Name);
            Console.WriteLine("IsDefault: {0}", notDefaultStroageName.IsDefault);

            // Create the original asset in the default storage account.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None,
                defaultStorageName.Name, _singleInputFilePath);
            Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);

            // Create an output asset of the encoding job in the other storage account.
            IAsset outputAsset = CreateEncodingJob(asset, notDefaultStroageName.Name, _singleInputFilePath);
            if (outputAsset != null)
                Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);

        }

        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();

            // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
            var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);

            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My encoding job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset", storageName,
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish. 
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // Get an updated job reference.
            job = GetJob(job.Id);

            // If job state is Error the event handling 
            // method for job progress should log errors.  Here we check 
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                Console.WriteLine("\nExiting method due to job error.");
                return null;
            }

            // Get a reference to the output asset from the job.
            IAsset outputAsset = job.OutputMediaAssets[0];

            return outputAsset;
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

        private static void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("********************");
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine("Please wait while local tasks or downloads complete...");
                    Console.WriteLine("********************");
                    Console.WriteLine();
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    Console.WriteLine("An error occurred in {0}", job.Id);
                    break;
                default:
                    break;
            }
        }

        static IJob GetJob(string jobId)
        {
            // Use a Linq select query to get an updated 
            // reference by Id. 
            var jobInstance =
                from j in _context.Jobs
                where j.Id == jobId
                select j;
            // Return the job reference as an Ijob. 
            IJob job = jobInstance.FirstOrDefault();

            return job;
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


