---
title: '자습서: Azure PowerShell을 사용하여 데이터를 이동하는 파이프라인 만들기 '
description: 이 자습서에서는 Azure PowerShell을 사용하여 복사 작업을 사용하는 Azure Data Factory 파이프라인을 만듭니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: tutorial
ms.date: 10/22/2021
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 99b968a63648a9e79a00c7f731e924414d727b65
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "131446177"
---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 데이터를 이동하는 Data Factory 파이프라인 만들기
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [복사 마법사](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 템플릿](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [복사 작업 자습서](../quickstart-create-data-factory-powershell.md)를 참조하세요. 

이 문서에서는 PowerShell을 사용하여 데이터를 Azure Blob 스토리지에서 Azure SQL Database로 복사하는 파이프라인이 있는 데이터 팩터리를 만드는 방법을 알아봅니다. Azure Data Factory를 처음 사용하는 경우 이 자습서를 수행하기 전에 [Azure Data Factory 소개](data-factory-introduction.md) 문서를 참조하세요.   

이 자습서에는 다음 한 가지 작업이 포함된 파이프라인을 만듭니다. 복사 작업 복사 작업은 지원되는 데이터 저장소에서 지원되는 싱크 데이터 저장소로 데이터를 복사합니다. 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md)을 참조하세요.

파이프라인 하나에는 활동이 둘 이상 있을 수 있습니다. 한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정함으로써 두 활동을 연결하여 활동을 하나씩 차례로 실행할 수 있습니다. 자세한 내용은 [파이프라인의 여러 작업](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)을 참조하세요.

> [!NOTE]
> 이 문서는 모든 데이터 팩터리 cmdlet을 다루지 않습니다. 이러한 cmdlet에 대한 포괄적인 설명서는 [Data Factory Cmdlet 참조](/powershell/module/az.datafactory)를 참조하세요.
> 
> 이 자습서에서 데이터 파이프라인은 원본 데이터 저장소의 데이터를 대상 데이터 저장소로 복사합니다. Azure Data Factory를 사용하여 데이터를 변환하는 방법에 대한 자습서는 [자습서: Hadoop 클러스터를 사용하여 데이터를 변환하는 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- [자습서 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 문서에서 나열하는 필수 구성 요소를 완료합니다.
- **Azure PowerShell** 을 설치합니다. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-Az-ps)의 지침을 따르세요.

## <a name="steps"></a>단계
이 자습서의 일부로 수행하는 단계는 다음과 같습니다.

1. Azure **데이터 팩터리** 를 만듭니다. 이 단계에서는 ADFTutorialDataFactoryPSH라는 Azure Data Factory를 만듭니다. 
1. 데이터 팩터리에서 **연결된 서비스** 를 만듭니다. 이 단계에서는 두 가지 유형의 연결된 서비스 즉, Azure Storage 및 Azure SQL Database를 만듭니다. 

    AzureStorageLinkedService는 Azure 스토리지 계정을 데이터 팩터리에 연결합니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 컨테이너를 만들고 이 스토리지 계정에 데이터를 업로드했습니다.   

    AzureSqlLinkedService는 Azure SQL Database를 데이터 팩터리에 연결합니다. (선택 사항) Azure File Storage를 프로젝트 리더의 DSVM(데이터 과학 Virtual Machine)에 탑재하고 여기에 프로젝트 데이터 자산을 추가합니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 이 데이터베이스에서 SQL 테이블을 만들었습니다.   
1. 데이터 팩터리에서 입력 및 출력 **데이터 세트** 를 만듭니다.  

    Azure Storage 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure Storage 계정에 연결하는 데 사용하는 연결 문자열을 지정합니다. 그리고 입력 Blob 데이터 세트는 데이터가 포함된 컨테이너와 폴더를 지정합니다.  

    마찬가지로 Azure SQL Database 연결 서비스는 런타임에 Data Factory 서비스에서 데이터베이스에 연결하는 데 사용하는 연결 문자열을 지정합니다. (선택 사항) 프로젝트의 데이터 자산을 저장하는 데 사용할 Azure File Storage를 만듭니다.
1. 데이터 팩터리에서 **파이프라인** 을 만듭니다. 이 단계에서는 복사 활동을 사용하여 파이프라인을 만듭니다.   

    복사 작업은 Azure Blob 스토리지의 Blob에서 Azure SQL Database의 테이블로 데이터를 복사합니다. 파이프라인의 복사 활동을 사용하여 지원되는 모든 원본의 데이터를 지원되는 모든 대상으로 복사할 수 있습니다. 지원되는 데이터 저장소 목록은 [데이터 이동 활동](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 문서를 참조하세요. 
1. 파이프라인을 모니터링합니다. 이 단계에서는 PowerShell을 사용하여 입력 및 출력 데이터 세트의 조각을 **모니터링** 합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기
> [!IMPORTANT]
> 아직 완료하지 않은 경우 [자습서의 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 완료합니다.   

데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 예를 들어 원본에서 대상 데이터 저장소에 데이터를 복사하는 복사 작업 및 입력 데이터를 제품 출력 데이터로 변환하는 Hive 스크립트를 실행하는 HDInsight Hive 작업입니다. 이 단계에서는 데이터 팩터리 만들기를 시작하겠습니다.

1. **PowerShell** 을 시작합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다.

    다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.

    ```powershell
    Connect-AzAccount
    ```   

    다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzSubscription
    ```

    다음 명령을 실행하여 사용하려는 구독을 선택합니다. **&lt;NameOfAzureSubscription**&gt;을 Azure 구독의 이름으로 바꿉니다.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```
1. 다음 명령을 실행하여 **ADFTutorialResourceGroup** 이라는 Azure 리소스 그룹을 만듭니다.

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    이 자습서의 일부 단계에서는 **ADFTutorialResourceGroup** 이라는 리소스 그룹을 사용한다고 가정합니다. 다른 리소스 그룹을 사용하는 경우 이 자습서에서 ADFTutorialResourceGroup 대신 해당 리소스 그룹을 사용해야 합니다.
1. **New-AzDataFactory** cmdlet을 실행하여 **ADFTutorialDataFactoryPSH** 라는 데이터 팩터리를 만듭니다.  

    ```powershell
    $df=New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
    이 이름은 이미 사용되었을 수도 있습니다. 따라서 접두사 또는 접미사(예: ADFTutorialDataFactoryPSH05152017)를 추가하여 데이터 팩터리 이름을 고유하게 만들어 해당 명령을 다시 실행합니다.  

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음과 같은 오류가 발생하면 이름(예: yournameADFTutorialDataFactoryPSH)을 변경합니다. 이 자습서의 단계를 수행하는 동안 ADFTutorialFactoryPSH 대신 이 이름을 사용합니다. Data Factory 아티팩트에 대한 내용은 [Data Factory - 명명 규칙](data-factory-naming-rules.md)을 참조하세요.

    ```
    Data factory name "ADFTutorialDataFactoryPSH" is not available
    ```
* Data Factory 인스턴스를 만들려면 Azure 구독의 참가자 또는 관리자여야 합니다.
* Data Factory의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.
* 다음 오류 메시지가 표시될 수 있습니다. “**이 구독이 Microsoft.DataFactory 네임스페이스를 사용하도록 등록되어 있지 않습니다.** ” 다음 중 하나를 수행하고 다시 게시해 보세요.

  * Azure PowerShell에서 다음 명령을 실행하여 데이터 팩터리 공급자를 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    데이터 팩터리 공급자가 등록되어 있는지 확인하려면 다음 명령을 실행합니다.

    ```powershell
    Get-AzResourceProvider
    ```
  * Azure 구독을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. Azure Portal에서 Data Factory 블레이드로 이동하거나 Data Factory를 만듭니다. 이 작업은 공급자를 자동으로 등록합니다.

## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 자습서에서는 Azure HDInsight 또는 Azure Data Lake Analytics와 같은 컴퓨팅 서비스를 사용하지 않습니다. Azure Storage(원본) 및 Azure SQL Database(대상) 유형의 두 데이터 저장소를 사용합니다. 

이에 따라 두 개의 연결된 서비스, 즉 AzureStorage와 AzureSqlDatabase 유형의 AzureStorageLinkedService와 AzureSqlLinkedService를 만듭니다.  

AzureStorageLinkedService는 Azure 스토리지 계정을 데이터 팩터리에 연결합니다. 이 스토리지 계정은 컨테이너를 만들고 [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 데이터를 업로드한 계정입니다.   

AzureSqlLinkedService는 Azure SQL Database를 데이터 팩터리에 연결합니다. (선택 사항) Azure File Storage를 프로젝트 리더의 DSVM(데이터 과학 Virtual Machine)에 탑재하고 여기에 프로젝트 데이터 자산을 추가합니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 이 데이터베이스에서 emp 테이블을 만들었습니다. 

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Azure Storage 계정에 대한 연결된 서비스 만들기
이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결합니다.

1. 다음 내용이 포함된 **AzureStorageLinkedService.json** 이라는 JSON 파일을 **C:\ADFGetStartedPSH** 폴더에 만듭니다. (아직 없는 경우 ADFGetStartedPSH 폴더를 만듭니다.)

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;accountname&gt;과 &lt;accountkey&gt;를 Azure Storage 계정의 이름과 키로 바꿉니다. 

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ``` 
1. **Azure PowerShell** 에서 **ADFGetStartedPSH** 폴더로 전환합니다.
1. **New-AzDataFactoryLinkedService** cmdlet을 실행하여 연결된 서비스를 만듭니다. **AzureStorageLinkedService** 연결된 서비스를 만듭니다. 이 자습서에서 사용하는 이 cmdlet 및 다른 Data Factory cmdlet을 사용하려면 **ResourceGroupName** 및 **DataFactoryName** 매개 변수에 대한 값을 전달해야 합니다. 또는 cmdlet을 실행할 때마다 ResourceGroupName 및 DataFactoryName을 입력하지 않고 New-AzDataFactory cmdlet에서 반환한 DataFactory 개체를 전달할 수 있습니다. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureStorageLinkedService.json
    ```
    샘플 출력은 다음과 같습니다.

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ``` 

    이 연결된 서비스를 만드는 다른 방법은 DataFactory 개체를 지정하는 대신 리소스 그룹 이름과 데이터 팩터리 이름을 지정하는 것입니다.  

    ```powershell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName <Name of your data factory> -File .\AzureStorageLinkedService.json
    ```

### <a name="create-a-linked-service-for-azure-sql-database"></a>Azure SQL Database에 대한 연결된 서비스 만들기
이 단계에서는 Azure SQL Database를 데이터 팩터리에 연결합니다.

1. 다음 내용이 포함된 AzureSqlLinkedService.json이라는 JSON 파일을 C:\ADFGetStartedPSH 폴더에 만듭니다.

    > [!IMPORTANT]
    > &lt;servername&gt;, &lt;databasename&gt;, &lt;username@servername&gt; 및 &lt;password&gt;를 서버, 데이터베이스, 사용자 계정 및 암호의 이름로 바꿉니다.

    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
1. 다음 명령을 실행하여 연결된 서비스를 만듭니다.

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

    샘플 출력은 다음과 같습니다.

    ```
    LinkedServiceName : AzureSqlLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ```

   서버에 대해 **Azure 서비스에 대한 액세스 허용** 설정이 켜져 있는지 확인합니다. 이 설정을 확인하고 켜려면 다음 단계를 수행합니다.

    1. [Azure Portal](https://portal.azure.com)에 로그인
    1. 왼쪽에 있는 **더 많은 서비스 >** 를 클릭하고 **데이터베이스** 범주에서 **SQL 서버** 를 클릭합니다.
    1. SQL 서버 목록에서 서버를 선택합니다.
    1. SQL 서버 블레이드에서 **방화벽 설정 표시** 링크를 클릭합니다.
    1. **방화벽 설정** 블레이드에서 **Azure 서비스에 대한 액세스 허용** 에 대해 **켜기** 를 클릭합니다.
    1. 도구 모음에서 **저장** 을 클릭합니다. 

## <a name="create-datasets"></a>데이터 세트 만들기
이전 단계에서는 Azure Storage 계정과 Azure SQL Database를 데이터 팩터리에 연결하는 연결된 서비스를 만들었습니다. 이 단계에서는 AzureStorageLinkedService 및 AzureSqlLinkedService에서 각각 참조하는 데이터 저장소에 저장된 입력 및 출력 데이터를 나타내는 InputDataset 및 OutputDataset이라는 두 개의 데이터 세트를 정의합니다.

Azure Storage 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure Storage 계정에 연결하는 데 사용하는 연결 문자열을 지정합니다. 그리고 입력 Blob 데이터 세트(InputDataset)는 입력 데이터가 포함된 컨테이너와 폴더를 지정합니다.  

마찬가지로 Azure SQL Database 연결 서비스는 런타임에 Data Factory 서비스에서 데이터베이스에 연결하는 데 사용하는 연결 문자열을 지정합니다. Azure File Storage 정보를 수동으로 입력하는 방법: 텍스트 파일에 Azure 파일 스토리지 정보가 없는 경우 다음 화면의 지침에 따라 필요한 구독, 스토리지 계정 및 Azure 파일 스토리지 정보를 입력할 수 있습니다. 

### <a name="create-an-input-dataset"></a>입력 데이터 세트 만들기
이 단계에서는 AzureStorageLinkedService 연결된 서비스에서 나타내는 Azure Storage의 Blob 컨테이너(adftutorial)의 루트 폴더에 있는 Blob 파일(emp.txt)을 가리키는 InputDataset이라는 데이터 세트를 만듭니다. fileName 값을 지정하지 않거나 건너뛰면 입력 폴더에 있는 모든 Blob의 데이터가 대상에 복사됩니다. 이 자습서에서는 fileName 값을 지정합니다.  

1. 다음 내용이 포함된 **InputDataset.json** 이라는 JSON 파일을 **C:\ADFGetStartedPSH** 폴더에 만듭니다.

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
                "folderPath": "adftutorial/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```

    다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

    | 속성 | Description |
    |:--- |:--- |
    | type | Azure Blob Storage에 데이터가 있기 때문에 type 속성은 **AzureBlob** 으로 설정됩니다. |
    | linkedServiceName | 이전에 만든 **AzureStorageLinkedService** 를 참조합니다. |
    | folderPath | 입력 Blob이 포함된 Blob **컨테이너** 와 **폴더** 를 지정합니다. 이 자습서에서 adftutorial은 Blob 컨테이너이며, 폴더는 루트 폴더입니다. | 
    | fileName | 이 속성은 선택 사항입니다. 이 속성을 생략하면 folderPath의 모든 파일이 선택됩니다. 이 자습서에서는 fileName에 대해 **emp.txt** 를 지정하므로 해당 파일만 처리를 위해 선택됩니다. |
    | format -> type |입력 파일은 텍스트 형식이므로 **TextFormat** 을 사용합니다. |
    | columnDelimiter | 입력 파일의 열은 **쉼표(`,`)** 로 구분됩니다. |
    | frequency/interval | frequency는 **Hour** 로 설정되고, interval은 **1** 로 설정됩니다. 즉 입력 조각이 **매시간** 사용될 수 있습니다. 다시 말하면, Data Factory 서비스가 지정한 Blob 컨테이너(**adftutorial**)의 루트 폴더에서 매 시간마다 입력 데이터를 찾습니다. 이러한 시간 이전 또는 이후가 아니라 파이프라인의 시작 시간과 종료 시간 내에 있는 데이터를 찾습니다.  |
    | external | 이 파이프라인에 의해 데이터가 생성되지 않는 경우 이 속성은 **true** 로 설정됩니다. 이 자습서의 입력 데이터는 이 파이프라인에 의해 생성되지 않는 emp.txt 파일에 있으므로 이 속성을 true로 설정합니다. |

    이러한 JSON 속성에 대한 자세한 내용은 [Azure Blob 커넥터 문서](data-factory-azure-blob-connector.md#dataset-properties)를 참조하세요.
1. 다음 명령을 실행하여 데이터 팩터리 데이터 세트를 만듭니다.

    ```powershell  
    New-AzDataFactoryDataset $df -File .\InputDataset.json
    ```
    샘플 출력은 다음과 같습니다.

    ```
    DatasetName       : InputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureBlobDataset
    Policy            : Microsoft.Azure.Management.DataFactories.Common.Models.Policy
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

### <a name="create-an-output-dataset"></a>출력 데이터 세트 만들기
이 단계의 일부에서는 **OutputDataset** 라는 출력 데이터 세트를 만듭니다. 이 데이터 세트는 **AzureSqlLinkedService** 로 표시되는 Azure SQL Database의 SQL 테이블을 가리킵니다. 

1. 다음 내용이 포함된 **OutputDataset.json** 이라는 JSON 파일을 **C:\ADFGetStartedPSH** 폴더에 만듭니다.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

    다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

    | 속성 | Description |
    |:--- |:--- |
    | type | Azure SQL Database의 테이블에 데이터가 복사되기 때문에 type 속성은 **AzureSqlTable** 로 설정됩니다. |
    | linkedServiceName | 이전에 만든 **AzureSqlLinkedService** 를 참조합니다. |
    | tableName | 데이터가 복사되는 **테이블** 을 지정했습니다. | 
    | frequency/interval | frequency는 **Hour** 로 설정되고, interval은 **1** 입니다. 즉 출력 조각이 이러한 시간 이전 또는 이후가 아니라 파이프라인의 시작 시간과 종료 시간 사이에서 **매시간** 생성됩니다.  |

    데이터베이스의 emp 테이블에 **ID**, **FirstName** 및 **LastName** 이라는 세 개의 열이 있습니다. ID는 ID 열이므로 여기서 **FirstName** 및 **LastName** 만 지정해야 합니다.

    이러한 JSON 속성에 대한 자세한 내용은 [Azure SQL 커넥터 문서](data-factory-azure-sql-connector.md#dataset-properties)를 참조하세요.
1. 다음 명령을 실행하여 Data Factory 데이터 세트를 만듭니다.

    ```powershell   
    New-AzDataFactoryDataset $df -File .\OutputDataset.json
    ```

    샘플 출력은 다음과 같습니다.

    ```
    DatasetName       : OutputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureSqlTableDataset
    Policy            :
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

## <a name="create-a-pipeline"></a>파이프라인 만들기
이 단계에서는 **InputDataset** 을 입력으로 사용하고 **OutputDataset** 을 출력으로 사용하는 **복사 활동** 을 포함한 파이프라인을 만듭니다.

현재 출력 데이터 세트는 일정을 작동하는 것입니다. 이 자습서에서는 출력 데이터 세트가 한 시간에 한 번씩 조각을 생성하도록 구성됩니다. 이 파이프라인은 하루 24시간 간격, 즉 24시간 동안에 걸친 시작 시간과 종료 시간을 갖습니다. 따라서 24개의 출력 데이터 세트가 파이프라인에 의해 생성됩니다. 

1. **C:\ADFGetStartedPSH** 폴더에 다음과 같은 내용으로 **ADFTutorialPipeline.json** 이라는 JSON 파일을 만듭니다.

    ```json   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```
    다음 사항에 유의하세요.

   - 작업 섹션에는 **형식** 이 **복사** 로 설정된 작업만 있습니다. 복사 활동에 대한 자세한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md)을 참조하세요. Data Factory 솔루션에서 [데이터 변환 활동](data-factory-data-transformation-activities.md)을 사용할 수도 있습니다.
   - 작업에 대한 입력을 **InputDataset** 으로 설정하고 작업에 대한 출력을 **OutputDataset** 으로 설정합니다. 
   - **typeProperties** 섹션에서 **BlobSource** 를 원본 유형으로 지정하고 **SqlSink** 를 싱크 유형으로 지정합니다. 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 전체 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 지원되는 특정 데이터 저장소를 원본/싱크로 사용하는 방법을 알아보려면 표에 나와 있는 링크를 클릭하세요.  

     **시작** 속성 값을 현재 날짜로 바꾸고 **종료** 값을 다음 날짜로 바꿉니다. 날짜 부분만 지정하고 날짜/시간의 시간 부분은 건너뛸 수 있습니다. 예를 들어, "2016-02-03"은 "2016-02-03T00:00:00Z"과 동일합니다.

     start 및 end 날짜/시간은 둘 다 [ISO 형식](https://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예를 들면 다음과 같습니다. 2016-10-14T16:32:41Z. **종료** 시간은 선택 사항이지만 이 자습서에서는 사용합니다. 

     **종료** 속성 값을 지정하지 않는 경우 "**시작 + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **종료** 속성 값으로 **9999-09-09** 를 지정합니다.

     앞의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.

     파이프라인 정의의 JSON 속성에 대한 설명은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 복사 활동 정의의 JSON 속성에 대한 설명은 [데이터 이동 활동](data-factory-data-movement-activities.md)을 참조하세요. BlobSource에서 지원하는 JSON 속성에 대한 설명은 [Azure Blob 커넥터 문서](data-factory-azure-blob-connector.md)를 참조하세요. SqlSink에서 지원하는 JSON 속성에 대한 설명은 [Azure SQL Database 커넥터 문서](data-factory-azure-sql-connector.md)를 참조하세요.
1. 다음 명령을 실행하여 Data Factory 테이블을 만듭니다.

    ```powershell   
    New-AzDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

    샘플 출력은 다음과 같습니다. 

    ```
    PipelineName      : ADFTutorialPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.PipelinePropertie
    ProvisioningState : Succeeded
    ```

**축하합니다.** Azure Blob 스토리지에서 Azure SQL Database에 데이터를 복사하는 파이프라인이 있는 Azure 데이터 팩터리를 성공적으로 만들었습니다. 

## <a name="monitor-the-pipeline"></a>파이프라인 모니터링
이 단계에서는 Azure PowerShell을 사용하여 Azure 데이터 팩터리에서 진행되는 작업을 모니터링합니다.

1. &lt;DataFactoryName&gt;을 데이터 팩터리 이름으로 바꿔 **Get-AzDataFactory** 를 실행하고, 출력을 $df 변수에 할당합니다.

    ```powershell  
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name <DataFactoryName>
    ```

    예를 들면 다음과 같습니다.
    ```powershell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH0516
    ```

    그런 다음 $df의 내용을 출력하도록(print) 실행하여 다음 출력을 봅니다. 

    ```
    PS C:\ADFGetStartedPSH> $df

    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DataFactoryId     : 6f194b34-03b3-49ab-8f03-9f8a7b9d3e30
    ResourceGroupName : ADFTutorialResourceGroup
    Location          : West US
    Tags              : {}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DataFactoryProperties
    ProvisioningState : Succeeded
    ```
1. **Get-AzDataFactorySlice** 를 실행하여 파이프라인의 출력 데이터 세트인 **OutputDataset** 의 모든 조각에 대한 세부 정보를 가져옵니다.  

    ```powershell   
    Get-AzDataFactorySlice $df -DatasetName OutputDataset -StartDateTime 2017-05-11T00:00:00Z
    ```

   이 설정은 파이프라인 JSON의 **시작** 값과 일치해야 합니다. 현재 날짜의 오전 12시부터 다음 날짜의 오전 12시까지 각 시간마다 하나씩, 24개의 조각이 표시됩니다.

   다음은 출력의 세 가지 샘플 조각입니다. 

    ``` 
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 11:00:00 PM
    End               : 5/12/2017 12:00:00 AM
    RetryCount        : 0
    State             : Ready
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 9:00:00 PM
    End               : 5/11/2017 10:00:00 PM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 8:00:00 PM
    End               : 5/11/2017 9:00:00 PM
    RetryCount        : 0
    State             : Waiting
    SubState          : ConcurrencyLimit
    LatencyStatus     :
    LongRetryCount    : 0
    ```
1. **Get-AzDataFactoryRun** 을 실행하여 **특정** 조각에 대한 작업 실행의 세부 정보를 가져옵니다. 이전 명령의 출력에서 날짜-시간 값을 복사하여 StartDateTime 매개 변수의 값을 지정합니다. 

    ```powershell  
    Get-AzDataFactoryRun $df -DatasetName OutputDataset -StartDateTime "5/11/2017 09:00:00 PM"
    ```

   샘플 출력은 다음과 같습니다. 

    ```
    Id                  : c0ddbd75-d0c7-4816-a775-704bbd7c7eab_636301332000000000_636301368000000000_OutputDataset
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH0516
    DatasetName         : OutputDataset
    ProcessingStartTime : 5/16/2017 8:00:33 PM
    ProcessingEndTime   : 5/16/2017 8:01:36 PM
    PercentComplete     : 100
    DataSliceStart      : 5/11/2017 9:00:00 PM
    DataSliceEnd        : 5/11/2017 10:00:00 PM
    Status              : Succeeded
    Timestamp           : 5/16/2017 8:00:33 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy  
    ```

Data Factory cmdlet에 대한 포괄적인 설명서는 [Data Factory cmdlet 참조](/powershell/module/az.datafactory)를 참조하세요.

## <a name="summary"></a>요약
이 자습서에서는 Azure Blob에서 Azure SQL Database로 데이터를 복사하는 Azure 데이터 팩터리를 만들었습니다. PowerShell를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 세트 및 파이프라인을 만들었습니다. 이 자습서에서 수행한 단계를 요약하면 다음과 같습니다.  

1. Azure **데이터 팩터리** 를 만들었습니다.
1. **연결된 서비스** 를 만들었습니다.

   a. 입력 데이터를 보유하는 Azure Storage 계정을 연결하는 **Azure Storage** 에 연결된 서비스입니다.     
   b. 출력 데이터를 보유하는 SQL Database를 연결하는 **Azure SQL** 연결된 서비스입니다.
1. 파이프라인의 입력 데이터와 출력 데이터를 설명하는 **데이터 세트** 를 만들었습니다.
1. 원본으로 **BlobSource** 를 사용하고 싱크로 **SqlSink** 를 사용하는 **복사 작업** 으로 **파이프라인** 을 만들었습니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Blob 스토리지를 원본 데이터 저장소로 사용하고 Azure SQL Database를 복사 작업의 대상 데이터 저장소로 사용했습니다. 다음 표에서는 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 목록을 제공합니다. 

[!INCLUDE [data-factory-supported-data-stores](includes/data-factory-supported-data-stores.md)]

데이터 저장소간에 데이터를 복사하는 방법에 대해 알아보려면 테이블에서 데이터 저장소에 대한 링크를 클릭하세요. 

