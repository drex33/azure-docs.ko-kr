---
title: '자습서: 관리 ID를 사용하여 Azure Cosmos DB에 액세스 - Windows - Azure AD'
description: Windows VM에서 시스템 할당 관리 ID를 사용하여 Azure Cosmos DB에 액세스하는 프로세스를 단계별로 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ROBOTS: NOINDEX
ms.openlocfilehash: 33e1c6653769a0fa5d03f86efa50d631e5590db8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041997"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>자습서: Windows VM 시스템 할당 관리 ID를 사용하여 Azure Cosmos DB에 액세스

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Windows VM(가상 머신)에 대한 시스템 할당 관리 ID를 사용하여 Cosmos DB에 액세스하는 방법을 보여 줍니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Cosmos DB 계정 만들기
> * Cosmos DB 계정 액세스 키에 대한 Windows VM 시스템 할당 관리 ID 액세스 권한 부여
> * Azure Resource Manager를 호출하기 위해 Windows VM 시스템 할당 관리 ID를 사용하여 액세스 토큰 가져오기
> * Cosmos DB 호출을 위해 Azure Resource Manager에서 액세스 키 가져오기

## <a name="prerequisites"></a>사전 요구 사항

- Azure 리소스에 대한 관리 ID 기능이 익숙하지 않은 경우 [개요](overview.md)를 참조하세요. 
- Azure 계정이 없으면 계속하기 전에 [체험 계정에 등록](https://azure.microsoft.com/free/)합니다.
- 필요한 리소스 생성 및 역할 관리를 수행하려면 적절한 범위(사용자 구독 또는 리소스 그룹)에서 계정에 "소유자" 권한이 필요합니다. 역할 할당에 관한 도움이 필요한 경우 [Azure 역할을 할당하여 Azure 구독 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.
- [Azure PowerShell](/powershell/azure/install-az-ps) 최신 버전 설치
- 시스템 할당 관리 ID가 활성화된 Windows 가상 머신도 필요합니다.
  - 이 자습서에 대한 가상 머신을 만들어야 하는 경우 [시스템 할당 ID가 설정된 가상 머신 만들기](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)라는 제목의 문서를 수행하면 됩니다.

## <a name="create-a-cosmos-db-account"></a>Cosmos DB 계정 만들기 

Cosmos DB 계정이 아직 없는 경우 지금 만듭니다. 이 단계를 건너뛰고 기존 Cosmos DB 계정을 사용해도 됩니다. 

1. Azure Portal의 왼쪽 위에 있는 **+ 리소스 만들기** 단추를 클릭합니다.
2. **데이터베이스** 를 클릭한 다음, **Azure Cosmos DB** 를 클릭하면 새로운 "새 계정" 패널이 표시됩니다.
3. 나중에 사용하는 Cosmos DB 계정에 대한 **ID** 를 입력합니다.  
4. **API** 는 "SQL"로 설정되어야 합니다. 이 자습서에서 설명하는 방식은 사용 가능한 다른 API 형식으로 사용될 수 있지만 이 자습서의 단계는 SQL API에 대한 것입니다.
5. **구독** 및 **리소스 그룹** 은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.  Cosmos DB를 사용할 수 있는 **위치** 를 선택합니다.
6. **만들기** 를 클릭합니다.

### <a name="create-a-collection"></a>컬렉션 만들기 

다음으로, 이후 단계에서 쿼리할 수 있는 Cosmos DB 계정에서 데이터 컬렉션을 추가합니다.

1. 새로 만든 Cosmos DB 계정으로 이동합니다.
2. **개요** 탭에서 **+/컬렉션 추가** 단추를 클릭하면 "컬렉션 추가" 패널이 슬라이드됩니다.
3. 컬렉션에 데이터베이스 ID, 컬렉션 ID를 제공하고, 스토리지 용량을 선택하고, 파티션 키를 입력하고, 처리량 값을 입력한 다음, **확인** 을 클릭합니다.  이 자습서의 경우 데이터베이스 ID 및 컬렉션 ID로 &quot;Test&quot;를 사용하고, 고정된 스토리지 용량 및 가장 낮은 처리량(400RU/s)을 선택하는 데 충분합니다.  


## <a name="grant-access"></a>액세스 권한 부여

이 섹션에서는 Windows VM 시스템 할당 관리 ID에 Cosmos DB 액세스 키에 대한 액세스 권한을 부여하는 방법을 보여줍니다. Cosmos DB는 Azure AD 인증을 기본적으로 지원하지 않습니다. 그러나 시스템 할당 관리 ID를 사용하여 Resource Manager에서 Cosmos DB 액세스 키를 검색하고 해당 키를 사용하여 Cosmos DB에 액세스할 수 있습니다. 이 단계에서 Cosmos DB 계정의 키에 Windows VM 시스템 할당 관리 ID 액세스 권한을 부여합니다.

PowerShell을 사용하여 Azure Resource Manager에서 Windows VM 시스템 할당 관리 ID 액세스 권한을 Cosmos DB 계정에 부여하려면 다음 값을 업데이트합니다.

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>`

Cosmos DB는 액세스 키를 사용할 때 두 가지 수준의 세분성: 계정에 대한 읽기/쓰기 액세스 및 계정에 대한 읽기 전용 액세스를 지원합니다.  계정에 대한 읽기/쓰기 키를 가져오려는 경우 `DocumentDB Account Contributor` 역할을 할당하거나 계정에 대한 읽기 전용 키를 가져오려는 경우 `Cosmos DB Account Reader Role` 역할을 할당합니다.  이 자습서에서는 `Cosmos DB Account Reader Role`을 할당합니다.

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> 작업을 수행할 수 없는 경우 올바른 권한이 없을 수 있습니다. 키에 대한 쓰기 액세스 권한을 원하는 경우 DocumentDB 계정 기여자와 같은 Azure 역할을 사용하거나 사용자 지정 역할을 만들어야 합니다. 자세한 내용은 [Azure Cosmos DB의 Azure 역할 기반 액세스 제어](../../cosmos-db/role-based-access-control.md)를 검토하세요.

## <a name="access-data"></a>데이터 액세스

이 섹션에서는 Windows VM 시스템 할당 관리 ID에 대한 액세스 토큰을 사용하여 Azure Resource Manager를 호출하는 방법을 보여줍니다. 자습서의 나머지 부분에서는 이전에 만든 VM에서 작업합니다. 

Windows VM에 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli)를 설치해야 합니다.

### <a name="get-an-access-token"></a>액세스 토큰 가져오기

1. Azure Portal에서 **Virtual Machines**, Windows 가상 머신으로 이동한 후 **개요** 페이지 위쪽의 **연결** 을 클릭합니다. 
2. Windows VM을 만들 때 추가한 **사용자 이름** 과 **암호** 를 입력합니다. 
3. 이제 가상 머신에 대한 **원격 데스크톱 연결** 을 만들었으므로 원격 세션에서 PowerShell을 엽니다.
4. PowerShell의 Invoke-WebRequest를 사용하여 Azure Resource Manager에 대한 액세스 토큰을 가져오도록 Azure 리소스 엔드포인트의 로컬 관리 ID에 요청합니다.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    
   다음으로 $response 개체에서 JSON(JavaScript Object Notation) 형식의 문자열로 저장된 “Content” 요소를 추출합니다. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   다음으로는 응답에서 액세스 토큰을 추출합니다.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>액세스 키 가져오기 

이 섹션에서는 Cosmos DB 호출을 위해 Azure Resource Manager에서 액세스 키를 가져오는 방법을 보여줍니다. PowerShell을 사용하여 이전에 가져온 액세스 토큰을 통해 Resource Manager를 호출하여 Cosmos DB 계정 액세스 키를 검색합니다. 액세스 키가 있으면 Cosmos DB를 쿼리할 수 있습니다. 사용자 고유의 값을 사용하여 아래 항목을 바꿉니다.

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>` 
- `<ACCESS TOKEN>` 값을 앞서 검색한 액세스 토큰으로 바꿉니다. 

>[!NOTE]
>읽기/쓰기 키를 검색하려는 경우 키 작업 유형 `listKeys`를 사용합니다.  읽기 전용 키를 검색하려는 경우 키 작업 유형 `readonlykeys`를 사용합니다. 'listkeys'를 사용할 수 없는 경우 관리 ID에 [적절한 역할](../../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)에 할당했는지 확인합니다.

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/readonlykeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
이 응답에서는 키 목록을 제공합니다.  예를 들어, 읽기 전용 키를 가져오는 경우

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
이제 Cosmos DB 계정에 대한 액세스 키가 있으므로 Cosmos DB SDK에 전달하고 계정에 액세스하는 호출을 만들 수 있습니다.  빠른 예제의 경우 Azure CLI에 액세스 키를 전달할 수 있습니다.  Azure Portal에서 Cosmos DB 계정 블레이드의 **개요** 탭에서 `<COSMOS DB CONNECTION URL>`을 가져올 수 있습니다.  `<ACCESS KEY>`를 위에서 얻은 값으로 바꿉니다.

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

이 CLI 명령은 컬렉션에 대한 세부 정보를 반환합니다.

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```


## <a name="disable"></a>사용 안 함

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>다음 단계

이 자습서에서는 Windows VM 시스템 할당 ID를 사용하여 Cosmos DB에 액세스하는 방법을 알아보았습니다.  Cosmos DB에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
>[Azure Cosmos DB 개요](../../cosmos-db/introduction.md)
