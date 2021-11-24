---
title: .NET SDK를 사용하여 Azure Data Factory 만들기
description: .NET SDK를 사용하여 Azure Data Factory 및 파이프라인을 만들어 Azure Blob 스토리지의 한 위치에서 다른 위치로 데이터를 복사합니다.
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/27/2021
ms.author: jingwang
ms.custom: devx-track-azurepowershell, mode-other
ms.openlocfilehash: e4dbbea154e83a36ae136a504355d4d8ff25bfac
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061889"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>빠른 시작: .NET SDK를 사용하여 데이터 팩터리 및 파이프라인 만들기

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [현재 버전](quickstart-create-data-factory-dot-net.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 빠른 시작에서는 .NET SDK를 사용하여 Azure Data Factory를 만드는 방법을 설명합니다. 이 데이터 팩터리에서 만든 파이프라인은 Azure Blob Storage의 한 폴더에서 다른 폴더로 데이터를 **복사합니다**. Azure Data Factory를 사용하여 데이터를 **변환** 하는 방법에 대한 자습서는 [자습서: Spark를 사용하여 데이터 변환](tutorial-transform-data-spark-portal.md)을 참조하세요.

> [!NOTE]
> 이 문서는 Data Factory 서비스의 자세한 소개를 제공하지 않습니다. Azure Data Factory 서비스 소개는 [Azure Data Factory 소개](introduction.md)를 참조하세요.

[!INCLUDE [data-factory-quickstart-prerequisites](includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

이 문서의 연습에서는 Visual Studio 2019를 사용합니다. Visual Studio 2013, 2015 또는 2017에 대한 절차는 약간 다릅니다.

## <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory에서 애플리케이션 만들기

*방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기* 의 섹션에서 다음 작업을 수행하기 위한 지침을 따릅니다.

1. [Azure Active Directory 애플리케이션 만들기](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)에서 이 자습서에서 만드는 .NET 애플리케이션을 나타내는 애플리케이션을 만듭니다. sign-on URL의 경우 (`https://contoso.org/exampleapp`)에 보이는 더미 URL을 제공할 수 있습니다.
2. [로그인을 위한 값 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)에서 **애플리케이션 ID** 및 **테넌트 ID** 를 가져온 후 이 자습서의 뒷부분에서 사용하게 되므로 이러한 값을 적어둡니다. 
3. [인증서 및 비밀](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)에서 **인증 키** 를 가져오고 이 자습서의 뒷부분에서 사용하게 되므로 이 값을 적어둡니다.
4. [역할에 애플리케이션 할당](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)에서 애플리케이션이 구독에 데이터 팩터리를 생성할 수 있도록 구독 수준에서 애플리케이션을 **참여자** 역할에 할당합니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

다음으로, Visual Studio에서 C# .NET 콘솔 애플리케이션을 만듭니다.

1. **Visual Studio** 를 시작합니다.
2. 시작 창에서 **새 프로젝트 만들기** > **콘솔 앱(.NET Framework)** 을 선택합니다. .NET 버전 4.5.2 이상이 필요합니다.
3. **프로젝트 이름** 에 **ADFv2QuickStart** 를 입력합니다.
4. **만들기** 를 선택하여 프로젝트를 만듭니다.

## <a name="install-nuget-packages"></a>NuGet 패키지 설치

1. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다.
2. **패키지 관리자 콘솔** 페이지에서 다음 명령을 실행하여 패키지를 설치합니다. 자세한 내용은 [Microsoft.Azure.Management.DataFactory nuget 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)를 참조하세요.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>데이터 팩터리 클라이언트 만들기

1. **Program.cs** 를 열고 다음 문을 포함하여 네임스페이스에 대한 참조를 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. 변수를 설정하는 **Main** 메서드에 다음 코드를 추가합니다. 자리 표시자를 고유한 값으로 바꿉니다. 현재 Data Factory를 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics** 를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 컴퓨팅 기능(HDInsight 등)은 다른 지역에 있을 수 있습니다.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```
> [!NOTE]
> 소버린 클라우드의 경우 적절한 ActiveDirectoryAuthority 및 ResourceManagerUrl(BaseUri)의 클라우드 관련 엔드포인트를 사용해야 합니다. 예를 들어 US Azure Gov에서 https://login.microsoftonline.com 대신 https://login.microsoftonline.us 의 권한을 사용하고, https://management.azure.com/ 대신 https://management.usgovcloudapi.net 을 사용한 다음, 데이터 팩터리 관리 클라이언트를 만듭니다. Powershell을 사용하여 각 클라우드 환경에 대한 엔드포인트 목록을 반환하는 “Get-AzEnvironment | Format-List”를 실행하여 다양한 클라우드의 엔드포인트 Url을 쉽게 가져올 수 있습니다.

3. **Main** 메서드에 **DataFactoryManagementClient** 클래스의 인스턴스를 만드는 다음 코드를 추가합니다. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 세트 및 파이프라인을 만듭니다. 또한 이 개체를 사용하여 파이프라인 실행 세부 정보를 모니터링합니다.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.microsoftonline.com/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
   ```


## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

**Main** 메서드에 **데이터 팩터리** 를 만드는 다음 코드를 추가합니다. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>연결된 서비스 만들기

**Main** 메서드에 **Azure Storage 연결된 서비스** 를 만드는 다음 코드를 추가합니다.

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 빠른 시작에서는 복사 원본 및 싱크 저장소 모두에 대해 샘플의 “AzureStorageLinkedService”라는 하나의 Azure Storage 연결된 서비스를 만들기만 하면 됩니다.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>데이터 세트 만들기

**Azure Blob Storage 데이터 세트** 를 만드는 **Main** 메서드에 다음 코드를 추가합니다.

원본에서 싱크로 복사할 데이터를 나타내는 데이터 세트를 정의합니다. 이 예에서 이 Blob 데이터 세트는 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조합니다. 데이터 세트는 값이 데이터 세트를 사용하는 활동에 설정되어 있는 매개 변수를 사용합니다. 매개 변수는 데이터가 상주/저장되는 위치를 가리키는 “folderPath”를 구성하는 데 사용됩니다.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }
        }
    }
);
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>파이프라인 만들기

**Main** 메서드에 **복사 작업이 있는 파이프라인** 을 만드는 다음 코드를 추가합니다.

이 예제에서 이 파이프라인은 하나의 활동을 포함하고 입력 Blob 경로 및 출력 Blob 경로의 두 매개 변수를 사용합니다. 이러한 매개 변수의 값은 파이프라인이 트리거/실행될 때 설정됩니다. 복사 활동은 입력 및 출력 시 이전 단계에서 만든 동일한 Blob 데이터 세트를 참조합니다. 데이터 세트를 입력된 데이터 세트로 사용하는 경우 입력된 경로가 지정됩니다. 또한 데이터 세트를 출력된 데이터 세트로 사용하는 경우 출력된 경로가 지정됩니다. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

**Main** 메서드에 **파이프라인 실행** 을 트리거하는 다음 코드를 추가합니다.

이 코드는 파이프라인에 지정된 **inputPath** 및 **outputPath** 매개 변수의 값을 원본 및 싱크 Blob 경로의 실제 값으로 설정합니다.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>파이프라인 실행 모니터링

1. **Main** 메서드에 다음 코드를 추가하여 데이터 복사가 완료될 때까지 지속적으로 상태를 검사합니다.

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. **Main** 메서드에 복사 작업 실행 세부 정보(예: 읽거나 쓴 데이터의 크기)를 검색하는 다음 코드를 추가합니다.

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>코드 실행

애플리케이션을 빌드하고 시작한 다음, 파이프라인 실행을 확인합니다.

콘솔에서 데이터 팩터리, 연결된 서비스, 데이터 세트, 파이프라인 및 파이프라인 실행 만들기에 대한 진행 상황을 출력합니다. 그런 다음 파이프라인 실행 상태를 확인합니다. 데이터 읽기/쓰기 크기를 포함하는 복사 작업 실행 세부 정보가 표시될 때까지 기다립니다. 그런 다음, [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 변수에 지정한 대로 Blob이 "inputBlobPath"에서 "outputBlobPath"로 복사되었는지 확인합니다.

### <a name="sample-output"></a>샘플 출력

```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>출력 확인

파이프라인은 자동으로 **adftutorial** Blob 컨테이너에서 출력 폴더를 만듭니다. 그런 다음, 입력 폴더에서 출력 폴더로 **emp.txt** 파일을 복사합니다. 

1. Azure Portal에서 위의 [Blob 컨테이너에 대한 입력 폴더 및 파일 추가](#add-an-input-folder-and-file-for-the-blob-container) 섹션에서 중지했던 **adftutorial** 컨테이너 페이지에 있는 **새로 고침** 을 선택하여 출력 폴더를 참조하세요. 
2. 폴더 목록에서 **출력** 을 선택합니다.
3. **emp.txt** 가 출력 폴더에 복사되었는지 확인합니다. 

## <a name="clean-up-resources"></a>리소스 정리

데이터 팩터리를 프로그래밍 방식으로 삭제하려면 프로그램에 다음 코드 줄을 추가합니다. 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>다음 단계

이 샘플의 파이프라인은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요. 
