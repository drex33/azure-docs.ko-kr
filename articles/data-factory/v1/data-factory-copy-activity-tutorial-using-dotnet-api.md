---
title: '자습서: .NET API를 사용하여 복사 작업이 있는 파이프라인 만들기 '
description: 이 자습서에서는 .NET API를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: tutorial
ms.date: 10/22/2021
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d0ae429fe2c12b60f4df405269587efe6c3c7006
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "131457813"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>자습서: .NET API를 사용하여 복사 작업이 있는 파이프라인 만들기
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [복사 마법사](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 템플릿](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [복사 작업 자습서](../quickstart-create-data-factory-dot-net.md)를 참조하세요. 

이 문서에서는 [.NET API](https://portal.azure.com)를 사용하여 Azure Blob 스토리지에서 Azure SQL Database로 데이터를 복사하는 파이프라인이 있는 데이터 팩터리를 만드는 방법에 대해 알아봅니다. Azure Data Factory를 처음 사용하는 경우 이 자습서를 수행하기 전에 [Azure Data Factory 소개](data-factory-introduction.md) 문서를 참조하세요.   

이 자습서에는 다음 한 가지 작업이 포함된 파이프라인을 만듭니다. 복사 작업 복사 작업은 지원되는 데이터 저장소에서 지원되는 싱크 데이터 저장소로 데이터를 복사합니다. 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md)을 참조하세요.

파이프라인 하나에는 활동이 둘 이상 있을 수 있습니다. 한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정함으로써 두 활동을 연결하여 활동을 하나씩 차례로 실행할 수 있습니다. 자세한 내용은 [파이프라인의 여러 작업](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)을 참조하세요. 

> [!NOTE] 
> Data Factory용 .NET API에 대한 포괄적인 설명서는 [데이터 팩터리 .NET API 참조](/dotnet/api/overview/azure/data-factory)를 참조하세요.
> 
> 이 자습서에서 데이터 파이프라인은 원본 데이터 저장소의 데이터를 대상 데이터 저장소로 복사합니다. Azure Data Factory를 사용하여 데이터를 변환하는 방법에 대한 자습서는 [자습서: Hadoop 클러스터를 사용하여 데이터를 변환하는 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [자습서 개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 를 살펴보고 자습서 개요를 가져와서 **필수 구성 요소** 를 완료합니다.
* Visual Studio 2012, 2013 또는 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/)
* Azure PowerShell. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-Az-ps) 문서의 지침을 수행하여 컴퓨터에 Azure PowerShell을 설치합니다. Azure PowerShell을 사용하여 Azure Active Directory 애플리케이션을 만듭니다.

### <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory에서 애플리케이션 만들기
Azure Active Directory 애플리케이션을 만든 다음 애플리케이션의 서비스 주체를 만들고 **데이터 팩터리 참가자** 역할에 할당합니다.

1. **PowerShell** 을 시작합니다.
2. 다음 명령을 실행하고 Azure 포털에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.

    ```powershell
    Connect-AzAccount
    ```
3. 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzSubscription
    ```
4. 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **&lt;NameOfAzureSubscription**&gt;을 Azure 구독의 이름으로 바꿉니다.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > 이 명령의 출력에서 **SubscriptionId** 및 **TenantId** 를 적어둡니다.

5. PowerShell에서 다음 명령을 실행하여 **ADFTutorialResourceGroup** 이라는 Azure 리소스 그룹을 만듭니다.

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    리소스 그룹이 이미 있다면 업데이트(Y)할지 또는 유지(N)할지를 지정합니다.

    다른 리소스 그룹을 사용하는 경우 이 자습서에서 ADFTutorialResourceGroup 대신 해당 리소스 그룹의 이름을 사용해야 합니다.
6. Azure Active Directory 애플리케이션을 만듭니다.

    ```powershell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    다음과 같은 오류가 발생하면 다른 URL을 지정하고 명령을 다시 실행합니다.
    
    ```powershell
    Another object with the same value for property identifierUris already exists.
    ```
7. AD 서비스 주체를 만듭니다.

    ```powershell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. **데이터 팩터리 참가자** 역할에 서비스 주체를 추가합니다.

    ```powershell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. 애플리케이션 ID를 가져옵니다.

    ```powershell
    $azureAdApplication
    ```
    출력에서 애플리케이션 ID(applicationID)를 적어둡니다.

이 단계에서 다음과 같은 4가지 값이 있어야 합니다.

* 테넌트 ID
* 구독 ID
* 애플리케이션 UI
* (첫 번째 명령에 지정된)암호

## <a name="walkthrough"></a>연습
1. Visual Studio 2012/2013/2015를 사용하여 C# .NET 콘솔 애플리케이션을 만듭니다.
   1. **Visual Studio** 2012/2013/2015를 실행합니다.
   2. **File** 을 클릭하고 **New** 를 가리킨 다음 **프로젝트** 를 클릭합니다.
   3. **템플릿** 을 확장하고 **Visual C#** 를 선택합니다. 이 연습에서는 C#을 사용하지만 모든 .NET 언어를 사용할 수 있습니다.
   4. 오른쪽의 프로젝트 형식 목록에서 **콘솔 애플리케이션** 을 선택합니다.
   5. 이름에 **DataFactoryAPITestApp** 을 입력합니다.
   6. **C:\ADFGetStarted** 를 [위치]로 선택합니다.
   7. **확인** 을 클릭하여 프로젝트를 만듭니다.
2. **도구** 를 클릭하고 **NuGet 패키지 관리자** 를 가리킨 다음 **패키지 관리자 콘솔** 을 클릭합니다.
3. **패키지 관리자 콘솔** 에서 다음 단계를 수행합니다.
   1. 다음 명령을 실행하여 Data Factory 패키지를 설치합니다. `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Azure Active Directory 패키지를 설치하려면 다음 명령을 실행합니다(코드에서 Active Directory API를 사용함). `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. 다음 **appSetttings** 섹션을 **App.config** 파일에 추가합니다. 이 설정은 도우미 메서드: **GetAuthorizationHeader** 에서 사용됩니다.

    **&lt;애플리케이션 ID&gt;** , **&lt;암호&gt;** , **&lt;구독 ID&gt;** 및 **&lt;테넌트 ID&gt;** 의 값을 고유한 값으로 바꿉니다.

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. 다음 **using** 문을 프로젝트의 원본 파일(Program.cs)에 추가합니다.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```

6. **DataPipelineManagementClient** 클래스의 인스턴스를 만드는 다음 코드를 **Main** 메서드에 추가합니다. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 입력 및 출력 데이터 세트와 파이프라인을 만듭니다. 또한 이 개체를 사용하여 런타임 시 데이터 세트의 조각을 모니터링합니다.

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > **resourceGroupName** 값을 Azure 리소스 그룹의 이름으로 바꿉니다.
   >
   > 데이터 팩터리 이름(dataFactoryName)을 고유한 이름으로 업데이트합니다. 데이터 팩터리 이름은 전역적으로 고유해야 합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.

7. **데이터 팩터리** 를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```

    데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 예를 들어 원본에서 대상 데이터 저장소에 데이터를 복사하는 복사 작업 및 입력 데이터를 제품 출력 데이터로 변환하는 Hive 스크립트를 실행하는 HDInsight Hive 작업입니다. 이 단계에서는 데이터 팩터리 만들기를 시작하겠습니다.
8. **Azure Storage 연결된 서비스** 를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

   > [!IMPORTANT]
   > **storageaccountname** 및 **accountkey** 를 Azure Storage 계정의 이름 및 키로 바꿉니다.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

    데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 자습서에서는 Azure HDInsight 또는 Azure Data Lake Analytics와 같은 컴퓨팅 서비스를 사용하지 않습니다. Azure Storage(원본) 및 Azure SQL Database(대상) 유형의 두 데이터 저장소를 사용합니다. 

    이에 따라 두 개의 연결된 서비스, 즉 AzureStorage와 AzureSqlDatabase 유형의 AzureStorageLinkedService와 AzureSqlLinkedService를 만듭니다.  

    AzureStorageLinkedService는 Azure 스토리지 계정을 데이터 팩터리에 연결합니다. 이 스토리지 계정은 컨테이너를 만들고 [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 데이터를 업로드한 계정입니다.
9. **Azure SQL 연결된 서비스** 를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

   > [!IMPORTANT]
   > **servername**, **databasename**, **username** 및 **password** 를 서버, 데이터베이스, 사용자 및 암호의 이름으로 바꿉니다.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

    AzureSqlLinkedService는 Azure SQL Database를 데이터 팩터리에 연결합니다. (선택 사항) Azure File Storage를 프로젝트 리더의 DSVM(데이터 과학 Virtual Machine)에 탑재하고 여기에 프로젝트 데이터 자산을 추가합니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 이 데이터베이스에서 emp 테이블을 만들었습니다.
10. **입력 및 출력 데이터 세트** 를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "InputDataset";
    string Dataset_Destination = "OutputDataset";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },
                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```
    
    이전 단계에서는 Azure Storage 계정과 Azure SQL Database를 데이터 팩터리에 연결하는 연결된 서비스를 만들었습니다. 이 단계에서는 AzureStorageLinkedService 및 AzureSqlLinkedService에서 각각 참조하는 데이터 저장소에 저장된 입력 및 출력 데이터를 나타내는 InputDataset 및 OutputDataset이라는 두 개의 데이터 세트를 정의합니다.

    Azure Storage 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure Storage 계정에 연결하는 데 사용하는 연결 문자열을 지정합니다. 그리고 입력 Blob 데이터 세트(InputDataset)는 입력 데이터가 포함된 컨테이너와 폴더를 지정합니다.  

    마찬가지로 Azure SQL Database 연결 서비스는 런타임에 Data Factory 서비스에서 데이터베이스에 연결하는 데 사용하는 연결 문자열을 지정합니다. Azure File Storage 정보를 수동으로 입력하는 방법: 텍스트 파일에 Azure 파일 스토리지 정보가 없는 경우 다음 화면의 지침에 따라 필요한 구독, 스토리지 계정 및 Azure 파일 스토리지 정보를 입력할 수 있습니다.

    이 단계에서는 AzureStorageLinkedService 연결된 서비스에서 나타내는 Azure Storage의 Blob 컨테이너(adftutorial)의 루트 폴더에 있는 Blob 파일(emp.txt)을 가리키는 InputDataset이라는 데이터 세트를 만듭니다. fileName 값을 지정하지 않거나 건너뛰면 입력 폴더에 있는 모든 Blob의 데이터가 대상에 복사됩니다. 이 자습서에서는 fileName 값을 지정합니다.    

    이 단계에서는 **OutputDataset** 이라는 출력 데이터 세트를 만듭니다. 이 데이터 세트는 **AzureSqlLinkedService** 로 표시되는 데이터베이스의 SQL 테이블을 가리킵니다.
11. **파이프라인을 만들고 활성화** 하는 다음 코드를 **Main** 메서드에 추가합니다. 이 단계에서는 **InputDataset** 을 입력으로 사용하고 **OutputDataset** 을 출력으로 사용하는 **복사 활동** 을 포함한 파이프라인을 만듭니다.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2017, 5, 11, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = new DateTime(2017, 5, 12, 0, 0, 0, 0, DateTimeKind.Utc);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
                                    Name = Dataset_Source
                                }
                            },
                            Outputs = new List<ActivityOutput>()
                            {
                                new ActivityOutput()
                                {
                                    Name = Dataset_Destination
                                }
                            },
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    }
                }
            }
        });
    ```

    다음 사항에 유의하세요.
   
    - 작업 섹션에는 **형식** 이 **복사** 로 설정된 작업만 있습니다. 복사 활동에 대한 자세한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md)을 참조하세요. Data Factory 솔루션에서 [데이터 변환 활동](data-factory-data-transformation-activities.md)을 사용할 수도 있습니다.
    - 작업에 대한 입력을 **InputDataset** 으로 설정하고 작업에 대한 출력을 **OutputDataset** 으로 설정합니다. 
    - **typeProperties** 섹션에서 **BlobSource** 를 원본 유형으로 지정하고 **SqlSink** 를 싱크 유형으로 지정합니다. 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 전체 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 지원되는 특정 데이터 저장소를 원본/싱크로 사용하는 방법을 알아보려면 표에 나와 있는 링크를 클릭하세요.  
   
    현재 출력 데이터 세트는 일정을 작동하는 것입니다. 이 자습서에서는 출력 데이터 세트가 한 시간에 한 번씩 조각을 생성하도록 구성됩니다. 이 파이프라인은 하루 24시간 간격, 즉 24시간 동안에 걸친 시작 시간과 종료 시간을 갖습니다. 따라서 24개의 출력 데이터 세트가 파이프라인에 의해 생성됩니다.
12. **Main** 메서드에 다음 코드를 추가하여 출력 데이터 세트의 데이터 조각 상태를 가져옵니다. 이 샘플에서는 조각만 필요합니다.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");        
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. 데이터 조각의 실행 정보를 가져오는 다음 코드를 **Main** 메서드에 추가합니다.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. **Main** 메서드에서 사용하는 다음 도우미 메서드를 **Program** 클래스에 추가합니다.

    > [!NOTE] 
    > 다음 코드를 복사하여 붙여넣을 때 복사된 코드가 Main 메서드와 동일한 수준인지 확인하십시오.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. 솔루션 탐색기에서 프로젝트(DataFactoryAPITestApp)를 확장하고 **참조** 를 마우스 오른쪽 단추로 클릭한 다음 **참조 추가** 를 클릭합니다. **System.Configuration** 어셈블리에 대한 확인란을 선택하고 **확인** 을 클릭합니다.
16. 콘솔 애플리케이션을 빌드합니다. 메뉴에서 **빌드** 를 클릭하고 **솔루션 빌드** 를 클릭합니다.
17. Azure Blob Storage의 **adftutorial** 컨테이너에 하나 이상의 파일이 있는지 확인합니다. 그렇지 않은 경우 메모장에서 다음 내용이 포함된 **Emp.txt** 파일을 만들어 adftutorial 컨테이너에 업로드합니다.

    ```
    John, Doe
    Jane, Doe
    ```
18. 메뉴에서 **디버그** -> **디버깅 시작** 을 클릭하여 샘플을 실행합니다. **데이터 조각의 실행 정보 가져오기** 가 표시되면 몇 분 동안 기다린 다음 **ENTER** 키를 누릅니다.
19. Azure 포털을 사용하여 데이터 팩터리 **APITutorialFactory** 가 다음 아티팩트로 생성되었는지 확인합니다.
    * 연결된 서비스: **LinkedService_AzureStorage**
    * 데이터 세트: **InputDataset** 및 **OutputDataset**
    * 파이프라인: **PipelineBlobSample**
20. 지정된 데이터베이스의 **emp** 테이블에서 두 개의 직원 레코드가 만들어졌는지 확인합니다.

## <a name="next-steps"></a>다음 단계
Data Factory용 .NET API에 대한 포괄적인 설명서는 [데이터 팩터리 .NET API 참조](/dotnet/api/overview/azure/data-factory)를 참조하세요.

이 자습서에서는 Azure Blob 스토리지를 원본 데이터 저장소로 사용하고 Azure SQL Database를 복사 작업의 대상 데이터 저장소로 사용했습니다. 다음 표에서는 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 목록을 제공합니다. 

[!INCLUDE [data-factory-supported-data-stores](includes/data-factory-supported-data-stores.md)]

데이터 저장소간에 데이터를 복사하는 방법에 대해 알아보려면 테이블에서 데이터 저장소에 대한 링크를 클릭하세요.

