---
title: 암호화 키 정보 찾기
titleSuffix: Azure Cognitive Search
description: Azure Key Vault에서 키를 관리할 수 있도록 인덱스 또는 동의어 맵에서 사용되는 암호화 키 이름 및 버전을 검색합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/21/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2f34d653a698a7ef2ee3dee21d46345ed9a7301a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003821"
---
# <a name="find-encrypted-objects-and-information"></a>암호화된 개체 및 정보 찾기

Azure Cognitive Search에서는 고객 관리형 암호화 키가 Azure Key Vault에서 생성, 저장, 관리됩니다. 개체가 암호화되었는지 여부, Azure Key Vault에 사용된 키 이름 또는 버전을 확인해야하는 경우, REST API 또는 Azure SDK를 사용하여 검색 서비스 내의 개체 정의에서 **encryptionKey** 속성을 검색합니다.

고객 관리형 키로 암호화되지 않은 개체에는 빈 **encryptionKey** 속성이 있습니다. 그렇지 않으면, 다음 예제와 유사한 정의가 표시될 수 있습니다.

```json
"encryptionKey": {
"keyVaultUri": "https://demokeyvault.vault.azure.net",
"keyVaultKeyName": "myEncryptionKey",
"keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
"accessCredentials": {
    "applicationId": "00000000-0000-0000-0000-000000000000",
    "applicationSecret": "myApplicationSecret"
    }
}
```

**encryptionKey** 구문은 암호화된 모든 개체에 대해 동일합니다. 개체 이름 및 설명과 동일한 수준의 첫 번째 수준 속성입니다.

## <a name="get-the-admin-api-key"></a>관리 API 키 가져오기

검색 서비스에서 개체 정의를 검색하려면 먼저 관리 API 키를 제공해야 합니다. 개체 정의 및 메타데이터를 쿼리하는 요청에 관리자 API 키가 필요합니다. 관리 API 키를 가져오는 가장 쉬운 방법은 포털을 통하는 것입니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Search Service 개요 페이지를 엽니다.

1. 왼쪽에서 **키** 를 클릭하고 관리 API를 복사합니다. 인덱스 및 동의어 맵 검색에는 관리자 키가 필요합니다.

나머지 단계를 수행하려면 PowerShell 및 REST API로 전환합니다. 포털에는 개체에 대한 암호화 키 정보가 표시되지 않습니다.

## <a name="retrieve-object-properties"></a>개체 속성 검색

PowerShell 및 REST를 사용하여 다음 명령을 실행하여 변수를 설정하고 개체 정의를 가져옵니다. 

또는 [.NET](/dotnet/api/azure.search.documents.indexes.searchindexclient.getindexes), [Python](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient), [JavaScript](/javascript/api/@azure/search-documents/searchindexclient), [Java](/java/api/com.azure.search.documents.indexes.searchindexclient.getindex)용 Azure SDK를 사용할 수도 있습니다.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>다음 단계

키 사용을 모니터링할 수 있도록 Azure Key Vault에서 [로깅을 사용하도록 설정](../key-vault/general/logging.md)하는 것이 좋습니다.

Azure Key 사용 또는 고객 관리형 암호화 구성에 대한 자세한 내용은 다음을 수행합니다.

+ [빠른 시작: PowerShell을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../key-vault/secrets/quick-create-powershell.md)

+ [Azure Cognitive Search에서 데이터 암호화에 사용할 고객 관리형 키 구성](search-security-manage-encryption-keys.md)
