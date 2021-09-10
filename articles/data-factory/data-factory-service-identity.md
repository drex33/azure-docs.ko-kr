---
title: Data Factory에 대한 관리 ID
description: Azure Data Factory의 관리 ID에 대해 알아봅니다.
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a350553659ea6028e3fb2079f790d14ae1653a86
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536696"
---
# <a name="managed-identity-for-data-factory"></a>Data Factory에 대한 관리 ID

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Data Factory(이전의 Managed Service Identity/MSI)의 관리 ID가 무엇이며 어떻게 작동하는지를 이해하는 데 도움을 줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

Data Factory의 관리 ID를 사용하면 데이터 엔지니어가 자격 증명을 관리할 필요가 없습니다. 관리 ID는 Azure AD(Azure Active Directory) 인증을 지원하는 리소스에 연결할 때 Data Factory 인스턴스의 ID를 제공합니다. 예를 들어 Data Factory는 관리 ID를 사용하여 데이터 관리자가 자격 증명을 안전하게 저장하거나 스토리지 계정에 액세스할 수 있는 [Azure Key Vault](../key-vault/general/overview.md)와 같은 리소스에 액세스할 수 있습니다. Data Factory는 관리 ID를 사용하여 Azure AD 토큰을 가져올 수 있습니다.

Data Factory에서 지원하는 관리 ID에는 두 가지 유형이 있습니다. 

- **시스템 할당:** Data Factory를 사용하면 서비스 인스턴스에서 직접 관리 ID를 사용하도록 설정할 수 있습니다. Data Factory 생성 중에 시스템 할당 관리 ID를 허용하면 해당 서비스 인스턴스의 수명 주기와 연결된 Azure AD에 ID가 만들어집니다. 의도적으로 해당 Azure 리소스만 이 ID를 사용하여 Azure AD에서 토큰을 요청할 수 있습니다. 따라서 리소스가 삭제되면 Azure에서 자동으로 ID를 삭제합니다.
- **사용자 할당:** 관리 ID를 독립 실행형 Azure 리소스로 만들 수도 있습니다. [사용자가 할당한 관리 ID를 만들고](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), 이를 하나 이상의 Data Factory 인스턴스에 할당할 수 있습니다. 사용자가 할당한 관리 ID는 이를 사용하는 리소스와 별도로 관리됩니다.



Data Factory에 대한 관리 ID는 다음과 같은 이점을 제공합니다.

- [Azure Key Vault에서 자격 증명을 저장](store-credentials-in-key-vault.md)하는 경우에 데이터 팩터리 관리 ID를 Azure Key Vault 인증에 사용합니다.
- Azure Blob Storage, Azure Data Explorer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, REST, Databricks 활동, 웹 활동 등을 비롯한 관리 ID 인증을 사용하여 데이터 저장소 또는 컴퓨터에 액세스합니다. 자세한 내용은 커넥터 및 작업 문서를 확인하세요.
- 사용자가 할당한 관리 ID는 Azure Key Vault에 저장된 고객 관리형 키를 사용하여 Data Factory 메타데이터를 암호화/암호 해독하는 데도 사용되며 이중 암호화를 제공합니다. 

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID 

>[!NOTE]
> 시스템 할당 관리 ID는 이전 버전과의 호환성을 위해 Data Factory 설명서 및 Data Factory UI에서 '관리 ID'라고도 합니다. 이를 참조할 때 '사용자가 할당한 관리 ID'가 명시적으로 설명됩니다. 

#### <a name="generate-system-assigned-managed-identity"></a><a name="generate-managed-identity"></a> 시스템 할당 관리 ID 생성

Data Factory에 대한 시스템 할당 관리 ID는 다음과 같이 생성됩니다.

- **Azure Portal 또는 PowerShell** 을 통해 데이터 팩터리를 만들 때 관리 ID가 항상 자동으로 생성됩니다.
- **SDK** 를 통해 데이터 팩터리를 만들 때, 생성할 팩터리 개체에서 "Identity = new FactoryIdentity()"를 지정하는 경우에만 관리 ID가 생성됩니다. [.NET 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md#create-a-data-factory)에서 예제를 참조하세요.
- **REST API** 를 통해 데이터 팩터리를 만들 때, 요청 본문에 "identity" 섹션을 지정하는 경우에만 관리 ID가 생성됩니다. [REST 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md#create-a-data-factory)에서 예제를 참조하세요.

데이터 팩터리에 다음 [관리 ID 검색](#retrieve-managed-identity) 지침과 관련된 관리 ID가 없는 것이 확인되면 데이터 팩터리를 프로그래밍 방식으로 ID 초기자로 업데이트하여 명시적으로 생성할 수 있습니다.

- [PowerShell을 사용하여 관리 ID 생성](#generate-system-assigned-managed-identity-using-powershell)
- [REST API를 사용하여 관리 ID 생성](#generate-system-assigned-managed-identity-using-rest-api)
- [Azure Resource Manager 템플릿을 사용하여 관리 ID 생성](#generate-system-assigned-managed-identity-using-an-azure-resource-manager-template)
- [SDK를 사용하여 관리 ID 생성](#generate-system-assigned-managed-identity-using-sdk)

>[!NOTE]
>
>- 관리 ID는 수정할 수 없습니다. 이미 관리 ID가 있는 데이터 팩터리를 업데이트해도 영향은 없으며 관리 ID는 변경되지 않습니다.
>- 팩터리 개체에 "identity" 매개 변수를 지정하지 않거나 REST 요청 본문에 "identity" 섹션을 지정하지 않고 관리 ID가 이미 있는 데이터 팩터리를 업데이트하면 오류가 발생합니다.
>- 데이터 팩터리를 삭제하면 연결된 관리 ID가 함께 삭제됩니다.

##### <a name="generate-system-assigned-managed-identity-using-powershell"></a>PowerShell을 사용하여 시스템 할당 관리 ID 생성

**Set-AzDataFactoryV2** 명령을 다시 호출하면 새로 생성된 "Identity" 필드가 표시됩니다.

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

##### <a name="generate-system-assigned-managed-identity-using-rest-api"></a>REST API를 사용하여 시스템 할당 관리 ID 생성

요청 본문의 "identity" 섹션에서 아래 API를 호출합니다.

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**요청 본문**: "identity": { "type": "SystemAssigned" }를 추가합니다.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**응답**: 관리 ID가 자동으로 생성되고 "identity" 섹션이 그에 따라 채워집니다.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

##### <a name="generate-system-assigned-managed-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 시스템 할당 관리 ID 생성

**템플릿**: "identity": { "type": "SystemAssigned" }를 추가합니다.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

##### <a name="generate-system-assigned-managed-identity-using-sdk"></a>SDK를 사용하여 시스템 할당 관리 ID 생성

Identity=new FactoryIdentity()를 사용하여 데이터 팩터리 create_or_update 함수를 호출합니다. .NET을 사용하는 샘플 코드:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

#### <a name="retrieve-system-assigned-managed-identity"></a><a name="retrieve-managed-identity"></a> 시스템 할당 관리 ID 검색

Azure Portal에서 또는 프로그래밍 방식으로 관리 ID를 검색할 수 있습니다. 다음 섹션에서는 몇 가지 샘플을 보여 줍니다.

>[!TIP]
> 관리 ID가 표시되지 않으면 팩터리를 업데이트하여 [관리 ID를 생성](#generate-managed-identity)합니다.

#### <a name="retrieve-system-assigned-managed-identity-using-azure-portal"></a>Azure Portal에서 시스템 할당 관리 ID 검색

Azure Portal -> 데이터 팩터리 -> 속성에서 관리 ID 정보를 찾을 수 있습니다.

- 관리 ID 개체 ID
- 관리 ID 테넌트

관리 ID 정보는 Azure Blob, Azure Data Lake Storage, Azure Key Vault 등의 관리 ID 인증을 지원하는 연결된 서비스를 만들 때도 표시됩니다.

사용 권한을 부여할 때 Azure 리소스의 액세스 제어(IAM) 탭에서 역할 할당을 추가하고 액세스 권한을 할당하고 시스템 할당 관리 ID에서 Data Factory을 선택하고 팩터리 이름으로 선택합니다. 또는 일반적으로 개체 ID 또는 데이터 팩터리 이름을 관리 ID 이름으로 사용하여 이 ID를 찾을 수 있습니다. 관리 ID의 애플리케이션 ID를 알아야 하는 경우 PowerShell을 사용할 수 있습니다.

#### <a name="retrieve-system-assigned-managed-identity-using-powershell"></a>PowerShell을 사용하여 시스템 할당 관리 ID 검색

다음과 같이 특정 데이터 팩터리를 가져오는 경우 관리 ID의 보안 주체 ID 및 테넌트 ID가 반환됩니다. **Principalid** 를 사용하여 액세스 권한을 부여합니다.

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

보안 주체 ID를 복사하여 애플리케이션 ID를 가져온 다음, 보안 주체 ID를 매개 변수로 사용하여 아래 Azure Active Directory 명령을 실행할 수 있습니다.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

#### <a name="retrieve-managed-identity-using-rest-api"></a>REST API를 사용하여 관리 ID 검색

다음과 같이 특정 데이터 팩터리를 가져오는 경우 관리 ID의 보안 주체 ID 및 테넌트 ID가 반환됩니다.

요청에서 아래 API를 호출합니다.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**응답**: 아래 예제에서와 같이 응답을 받게 됩니다. "ID" 섹션이 그에 따라 채워집니다.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> ARM 템플릿에서 관리 ID를 검색하려면 ARM JSON에 **출력** 섹션을 추가합니다.

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

Azure Active Directory 사용자 할당 관리 ID를 만들고, 삭제하고, 관리할 수 있습니다. 자세한 내용은 [Azure Portal에서 사용자가 할당한 관리 ID를 만들기, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 설명서를 참조하세요. 

### <a name="credentials"></a>자격 증명

사용자가 할당한 관리 ID, 서비스 주체를 포함할 수 있는 자격 증명을 소개하고 AAD(Azure Active Directory) 인증을 지원하는 연결된 서비스에서 사용할 수 있는 시스템 할당 관리 ID도 나열합니다. 이를 통해 모든 AAD 기반 자격 증명을 통합하고 관리할 수 있습니다.  

다음은 인증을 위해 연결된 서비스에서  **관리 ID** 를 사용하는 일반적인 단계입니다. 

1. Azure Portal, SDK, PowerShell, REST API를 사용하여 사용자가 할당한 관리 ID를 Data Factory 인스턴스에 연결합니다. 
   아래 스크린샷에서는 Azure Portal(Data Factory 블레이드)을 사용하여 사용자가 할당한 관리 ID를 연결했습니다. 

   :::image type="content" source="media/managed-identities/uami-azure-portal.jpg" alt-text="Azure Portal을 사용하여 사용자가 할당한 관리 ID를 연결하는 방법을 보여주는 스크린샷" lightbox="media/managed-identities/uami-azure-portal.jpg":::

2. Data Factory 사용자 인터페이스에서 대화형으로 '자격 증명'을 만듭니다. 1단계에서 데이터 팩터리와 연결된 사용자가 할당한 관리 ID를 선택할 수 있습니다. 

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-1.png" alt-text="새 자격 증명을 만드는 첫 번째 단계를 보여주는 스크린샷" lightbox="media/managed-identities/credential-adf-ui-create-new-1.png":::

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-2a.png" alt-text="새 자격 증명을 만드는 두 번째 단계를 보여주는 스크린샷" lightbox="media/managed-identities/credential-adf-ui-create-new-2a.png":::

3. 새 연결된 서비스를 만들고 인증에서 '사용자가 할당한 관리 ID'를 선택합니다.

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-linked-service.png" alt-text=" 관리 ID 인증을 사용하는 새 연결된 서비스를 보여주는 스크린샷" lightbox="media/managed-identities/credential-adf-ui-create-new-linked-service.png":::

> [!NOTE] 
> 위의 작업에는 SDK/PowerShell/REST API를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

데이터 팩터리 관리 ID를 사용하는 시기 및 방법을 소개하는 다음 항목을 참조하세요.

- [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)
- [Azure 리소스 인증을 위해 관리 ID를 사용하여 Azure Data Lake Store 간에 데이터 복사](connector-azure-data-lake-store.md)

데이터 팩터리 관리 ID의 기준이 되는 Azure 리소스의 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
