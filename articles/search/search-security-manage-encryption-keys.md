---
title: 고객 관리형 키를 사용하여 데이터 암호화
titleSuffix: Azure Cognitive Search
description: Azure Key Vault에서 만들고 관리하는 키를 사용하여 Azure Cognitive Search 내 인덱스 및 동의어 맵에 대한 서버 쪽 암호화를 보충합니다.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 5513e4f26f6b2fed17f406f43110eb358179ce79
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232829"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Azure Cognitive Search에서 데이터 암호화에 사용할 고객 관리형 키 구성

Azure Cognitive Search는 [서비스 관리형 키](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)를 사용하여 콘텐츠를 자동으로 암호화합니다. 보호가 더 필요하면 Azure Key Vault에서 만들고 관리하는 키를 사용하여 추가적인 암호화 계층으로 기본 암호화를 보완할 수 있습니다. 암호화할 수 있는 개체에는 인덱스, 동의어 목록, 인덱서, 데이터 원본 및 기술 세트가 포함됩니다.

이 문서에서는 고객이 관리하는 키 암호화를 설정하는 단계를 안내합니다. 이때 유의해야 할 몇 가지 사항이 있습니다.

+ 고객 관리형 키 암호화는 [Azure Key Vault](../key-vault/general/overview.md)에 따라 달라집니다. 사용자 고유의 암호화 키를 만들고 Azure Key Vault에 저장할 수도 있고 Azure Key Vault의 API를 사용하여 암호화 키를 생성할 수도 있습니다.

+ 고객 관리형 키를 사용한 암호화는 개체가 생성될 때 개체별로 사용됩니다. 이미 존재하는 콘텐츠는 암호화할 수 없습니다.

암호화는 암호를 해독하는 데 계산 비용이 많이 들기 때문에 중요한 콘텐츠만 암호화됩니다. 여기에는 인덱스 및 동의어 목록 내의 모든 콘텐츠가 포함됩니다. 인덱서, 데이터 원본 및 기술 세트의 경우 연결 문자열, 설명, 키 및 사용자 입력을 저장하는 필드만 암호화됩니다. 예를 들어 기술 세트에는 Cognitive Services 키가 있고 일부 기술에는 사용자 지정 엔터티와 같은 사용자 입력이 허용됩니다. 기술에 대한 키 및 사용자 입력이 암호화됩니다.

## <a name="double-encryption"></a>이중 암호화

이중 암호화는 CMK(고객 관리형 키) 암호화의 확장입니다. CMK 암호화는 데이터 디스크에 작성되는 장기 스토리지에 적용됩니다. *이중 암호화* 라는 용어는 단기 스토리지(임시 디스크에 작성된 콘텐츠)의 추가 암호화를 의미합니다. 구성이 필요 없습니다. 개체에 CMK를 적용하면 이중 암호화가 자동으로 호출됩니다.

이중 암호화는 모든 지역에서 사용할 수 있지만 지원은 두 단계로 롤아웃되었습니다. 첫 번째 롤아웃은 2020년 8월이었으며 아래에 나열된 5개 지역이 포함되었습니다. 2021년 5월 두 번째 롤아웃에서는 이중 암호화를 나머지 모든 지역으로 확대했습니다. 이전 서비스에서 CMK를 사용하고 이중 암호화를 사용하려는 경우 원하는 지역에 새 검색 서비스를 만들어야 합니다.

| 지역 | 서비스 생성 날짜 |
|--------|-----------------------|
| 미국 서부 2 | 2020년 8월 1일 이후 |
| 미국 동부 | 2020년 8월 1일 이후 |
| 미국 중남부  | 2020년 8월 1일 이후 |
| US Gov 버지니아  | 2020년 8월 1일 이후 |
| US Gov 애리조나  | 2020년 8월 1일 이후 |
| [지원되는 기타 모든 지역](https://azure.microsoft.com/global-infrastructure/services/?products=search#select-product) | 2021년 5월 13일 이후 |

## <a name="prerequisites"></a>사전 요구 사항

이 시나리오에서 사용되는 도구와 서비스는 다음과 같습니다.

+ [청구 가능한 계층](search-sku-tier.md#tier-descriptions)의 [Azure Cognitive Search](search-create-service-portal.md)(기본 이상, 모든 지역).
+ [Azure Key Vault](../key-vault/general/overview.md), [Azure Portal](../key-vault//general/quick-create-portal.md) [Azure CLI](../key-vault//general/quick-create-cli.md) 또는 [Azure PowerShell](../key-vault//general/quick-create-powershell.md)을 사용하여 키 자격 증명 모음을 만들 수 있습니다. Azure Cognitive Search와 동일한 구독. 키 자격 증명 모음에는 **일시 삭제** 및 **보호 제거** 가 설정되어 있어야 합니다.
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 새 테넌트를 설치하지 않은 경우에는 [새 테넌트를 설정](../active-directory/develop/quickstart-create-new-tenant.md)합니다.

암호화된 개체를 만들 수 있는 검색 애플리케이션이 있어야 합니다. 이 코드에서 주요 자격 증명 모음 키 및 Active Directory 등록 정보를 참조합니다. 이 코드는 작업 중인 앱 또는 [C# 코드 샘플 DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)과 같은 프로토타입 코드일 수 있습니다.

> [!TIP]
> [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)또는 [Azure PowerShell](search-get-started-powershell.md)를 사용하여 암호화 키 매개 변수를 포함하는 인덱스 및 동의어 맵을 만드는 REST API를 호출할 수 있습니다. Azure CLI를 사용할 수도 있습니다. 인덱스 또는 동의어 맵에 키를 추가하기 위한 포털 지원은 지원되지 않습니다.

## <a name="key-vault-tips"></a>Key Vault 팁

Azure Key Vault 경우 [PowerShell을 사용하여 Azure Key Vault 비밀을 설정하고 검색](../key-vault/secrets/quick-create-powershell.md)하는 기본 작업에 대해 알아보려면 이 빠른 시작을 살펴보세요. Key Vault를 사용하기 위한 몇 가지 팁은 다음과 같습니다.

+ 필요한 만큼 Key Vault를 사용합니다. 관리형 키는 여러 Key Vault에 있을 수 있습니다. 검색 서비스에는 여러 개의 암호화된 개체가 있을 수 있으며, 각 개체는 서로 다른 고객 관리형 암호화 키로 암호화되어 여러 Key Vault에 저장됩니다.

+ 키 사용을 모니터링할 수 있도록 Key Vault에서 [로깅을 사용하도록 설정](../key-vault/general/logging.md)하는 것이 좋습니다.

+ Key Vault 키 및 Active Directory 애플리케이션 비밀과 등록을 정기적으로 회전하는 동안 엄격한 절차를 수행해야 합니다. 이전 암호 및 키를 삭제하기 전에 새 비밀 및 키를 사용하도록 모든 [암호화된 콘텐츠](search-security-get-encryption-keys.md)를 항상 업데이트합니다. 이 단계를 수행하지 않은 경우 콘텐츠의 암호를 해독할 수 없습니다.

## <a name="1---enable-purge-protection"></a>1 - 제거 방지 사용

첫 번째 단계로 Key Vault에서 [일시 삭제](../key-vault/general/soft-delete-overview.md) 및 [제거 방지](../key-vault/general/soft-delete-overview.md#purge-protection)가 사용하도록 설정되어 있는지 확인합니다. 고객 관리형 키를 사용하는 암호화의 특성으로 인해 Azure 키 자격 증명 모음 키가 삭제되면 아무도 데이터를 검색할 수 없습니다. 

Key Vault 실수로 키 삭제로 인한 데이터 손실을 방지하려면 키 자격 증명 모음에 대해 일시 삭제 및 제거 보호를 사용하도록 설정해야 합니다. 일시 삭제는 기본적으로 사용하도록 설정되어 있으므로 의도적으로 사용하지 않도록 설정한 경우에만 문제가 발생합니다. 제거 보호는 기본적으로 사용하도록 설정되어 있지 않지만 Cognitive Search에서 고객이 관리하는 키 암호화에 필요합니다. 

Portal, PowerShell 또는 Azure CLI 명령을 사용하여 두 속성을 모두 설정할 수 있습니다.

### <a name="using-azure-portal"></a>Azure Portal 사용

1. [Azure Portal에 로그인](https://portal.azure.com)하고 키 자격 증명 모음 개요 페이지를 엽니다.

1. **개요** 페이지의 **Essentials** 에서 **일시 삭제** 및 **보호 제거** 를 사용하도록 설정합니다.

### <a name="using-powershell"></a>PowerShell 사용

1. `Connect-AzAccount`을 실행하여 Azure 자격 증명을 설정합니다.

1. 다음 명령을 실행하여 키 자격 증명 모음에 연결하고 `<vault_name>`을 유효한 이름으로 바꿉니다.

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. 일시 삭제를 사용하여 Azure Key Vault를 만듭니다. 자격 증명 모음에서 사용하지 않도록 설정된 경우 다음 명령을 실행합니다.

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. 제거 방지 사용:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. 업데이트를 저장:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Azure CLI 사용

+ [Azure CLI](/cli/azure/install-azure-cli)을 설치한 경우 다음 명령을 실행하여 필수 속성을 사용하도록 설정할 수 있습니다.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 - Key Vault에 키 만들기

사용할 Azure Key Vault 키가 이미 있는 경우 키 생성을 건너뛰고 키 식별자를 수집합니다. 암호화된 개체를 만들 때 이 정보가 필요합니다.

1. [Azure Portal에 로그인](https://portal.azure.com)하고 키 자격 증명 모음 개요 페이지를 엽니다.

1. 왼쪽에서 **키** 를 선택하고 **+생성/가져오기** 를 선택합니다.

1. **키 만들기** 창에 있는 **옵션** 목록에서 키를 만드는 데 사용할 방법을 선택합니다. d새 키를 **생성** 하거나 기존 키를 **업로드** 하거나 **복원 백업을** 사용하여 키 백업을 선택할 수 있습니다.

1. 키의 **이름을** 입력하고 필요에 따라 다른 키 속성을 선택합니다.

1. **만들기** 를 선택하여 배포를 시작합니다.

1. 키 식별자를 적어둡니다. **키 값 Uri**, **키 이름** 및 **키 버전** 으로 구성됩니다. Azure Cognitive Search에서 암호화된 인덱스를 정의하려면 식별자가 필요합니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="새 키 자격 증명 모음을 만듭니다":::

## <a name="3---register-an-app"></a>3 - 앱 등록

1. [Azure Portal](https://portal.azure.com)에서 구독에 대한 Azure Active Directory 리소스를 찾습니다.

1. 왼쪽의 **관리** 에서 **앱 등록** 을 선택한 다음 **새 등록** 을 선택하세요.

1. 검색 애플리케이션 이름과 유사한 이름으로 등록 이름을 지정하세요. **등록** 을 선택합니다.

1. 앱 등록을 생성하면 애플리케이션 ID를 복사하세요. 애플리케이션에 이 문자열을 제공해야 합니다. 

   [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)를 단계별로 실행하는 경우 이 값을 **appsettings.js** 파일에 붙여 넣습니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Essentials 섹션의 애플리케이션 ID":::

1. 그런 다음 왼쪽 메뉴에서 **인증서 및 암호** 를 선택합니다.

1. **새 클라이언트 비밀** 을 선택합니다. 표시 이름으로 비밀을 지정하고 **추가** 를 선택합니다.

1. 애플리케이션 비밀 복사하기. 샘플을 단계별로 실행하는 경우 이 값을  **의appsettings.js** 파일에 붙여 넣습니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="애플리케이션 암호":::

## <a name="4---grant-permissions"></a>4 - 권한 부여

이 단계에서는 Key Vault에서 액세스 정책을 만듭니다. 이 정책은 사용자가 등록한 애플리케이션에 고객 관리형 키를 사용할 수 있도록 Active Directory 권한을 부여합니다.

지정된 시간에 액세스 권한이 해지될 수 있습니다. 해지되면 해당 키 자격 증명 모음을 사용하는 모든 검색 서비스 인덱스 또는 동의어 맵을 사용할 수 없게 됩니다. 나중에 Key vault 액세스 권한을 복원하면 index\synonym 맵 액세스가 복원됩니다. 자세한 내용은 [키 자격 증명 모음에 대한 보안 액세스](../key-vault/general/security-features.md)를 참조하세요.

1. 계속 Azure Portal에서 키 자격 증명 모음 **개요** 페이지를 엽니다. 

1. 왼쪽에서 **액세스 정책을** 선택하고 **+액세스 정책 추가** 를 선택합니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="키 자격 증명 모음 액세스 정책 추가":::

1. **보안 주체 선택** 을 선택하고 Active Directory에 등록한 애플리케이션을 선택합니다. 이름순으로 검색할 수 있습니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="키 자격 증명 모음 액세스 정책 보안 주체를 선택합니다":::

1. **키 권한** 에서 *가져오기*, *키 래핑 해제* 및 *키 래핑* 으로 설정합니다.

1. **비밀 권한** 에서 *가져오기* 를 선택합니다.

1. **인증서 권한** 에서 *가져오기* 를 선택합니다.

1. **추가** 를 선택하고 **저장** 을 선택합니다.

> [!Important]
> Azure Cognitive Search의 암호화된 콘텐츠는 특정 **버전** 의 특정 Azure Key Vault 키를 사용하도록 구성됩니다. 키 또는 버전을 변경하는 경우 이전 key\version을 삭제하기 **전에** 새 key\version을 사용하도록 인덱스 또는 동의어 맵을 업데이트해야 합니다. 이렇게 하지 않으면 인덱스 또는 동의어 맵을 사용할 수 없게 되며 키 액세스가 손실된 후에는 콘텐츠의 암호를 해독할 수 없습니다.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5-콘텐츠 암호화

인덱스, 동의어 맵, 인덱서, 데이터 원본 또는 기술 집합에 고객 관리형 키를 추가하려면 [REST API 검색](/rest/api/searchservice/) 또는 Azure SDK를 사용하여 암호화를 사용하도록 설정된 개체를 만듭니다. 포털은 동의어 맵 또는 암호화 속성을 노출하지 않습니다. 

1. API 만들기를 호출하여 **encryptionKey** 속성을 지정합니다.

   + [인덱스 만들기](/rest/api/searchservice/create-index)
   + [동의어 맵 만들기](/rest/api/searchservice/create-synonym-map)
   + [인덱서 만들기](/rest/api/searchservice/create-indexer)
   + [데이터 원본 만들기](/rest/api/searchservice/create-data-source)
   + [기술 집합 만들기](/rest/api/searchservice/create-skillset)

1. encryptionKey 구문을 개체 정의에 삽입합니다. 이 속성은 이름 및 설명과 동일한 수준의 첫 번째 수준 속성입니다. [아래 예제](#rest-examples)는 속성 배치를 보여줍니다. 동일한 Key Vault, 키 및 버전을 사용하는 경우 암호화를 사용하도록 설정하는 각 개체에 동일한 encryptionKey 구문을 붙여넣을 수 있습니다.

   다음 JSON 예제에서는 Azure Key Vault 및 Azure Active Directory의 애플리케이션 등록에 대한 자리 표시자 값이 있는 encryptionKey를 보여줍니다.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
        "accessCredentials": {
          "applicationId": "00000000-0000-0000-0000-000000000000",
          "applicationSecret": "myApplicationSecret"
        }
      }
    }
    ```

검색 서비스에서 암호화된 개체를 생성하면 해당 유형의 다른 개체와 마찬가지로 사용할 수 있습니다. 암호화는 사용자와 개발자에게 투명하게 처리됩니다.

> [!Note]
> 이러한 주요 자격 증명 모음 세부 정보는 비밀로 간주되지 않으며 Azure Portal의 관련 Azure Key Vault 키 페이지로 이동하여 쉽게 검색할 수 있습니다.

## <a name="rest-examples"></a>REST 예제

이 섹션에서는 개체 정의에서 `encryptionKey`를 찾을 위치를 볼 수 있도록 여러 개체에 대한 JSON을 보여줍니다.

### <a name="index-encryption"></a>인덱스 암호화

REST API를 통해 새 인덱스를 만드는 방법에 대한 자세한 내용은 [인덱스 만들기(REST API)](/rest/api/searchservice/create-index)에서 찾을 수 있습니다. 여기서 유일한 차이점은 인덱스 정의의 일부로 암호화 키 세부 정보를 지정하는 것입니다.

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true}
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

이제 인덱스 생성 요청을 보낸 다음 인덱스를 정상적으로 사용하기 시작할 수 있습니다.

### <a name="synonym-map-encryption"></a>동의어 맵 암호화

[동의어 맵 Azure Cognitive Search REST API 만들기](/rest/api/searchservice/create-synonym-map)를 사용하여 암호화된 동의어 맵을 만듭니다. 사용할 암호화 키를 지정하려면 `encryptionKey` 속성을 사용합니다.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

이제 동의어 맵 만들기 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

### <a name="data-source-encryption"></a>데이터 원본 암호화

[데이터 원본 만들기(REST API)](/rest/api/searchservice/create-data-source)를 사용하여 암호화된 데이터 원본을 만듭니다. 사용할 암호화 키를 지정하려면 `encryptionKey` 속성을 사용합니다.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

이제 데이터 원본 만들기 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

### <a name="skillset-encryption"></a>기술 세트 암호화

[기술 세트 REST API 만들기](/rest/api/searchservice/create-skillset)를 사용하여 암호화된 기술 세트를 만듭니다. 사용할 암호화 키를 지정하려면 `encryptionKey` 속성을 사용합니다.

```json
{
    "name": "skillset1",
    "skills":  [ omitted for brevity ],
    "cognitiveServices": { omitted for brevity },
      "knowledgeStore":  { omitted for brevity  },
    "encryptionKey": (optional) { 
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "accessCredentials": {
            "applicationId": "00000000-0000-0000-0000-000000000000",
            "applicationSecret": "myApplicationSecret"}
    }
}
```

이제 기술 세트 만들기 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

### <a name="indexer-encryption"></a>인덱서 암호화

[인덱서 REST API 만들기](/rest/api/searchservice/create-indexer)를 사용하여 암호화된 인덱서를 만듭니다. 사용할 암호화 키를 지정하려면 `encryptionKey` 속성을 사용합니다.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

이제 인덱서 만들기 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

>[!Important]
> `encryptionKey`은 기존 검색 인덱스 또는 동의어 맵에 추가할 수 없지만 세 가지 주요 자격 증명 모음 세부 정보(예: 키 버전 업데이트)에 대해 다른 값을 제공하여 업데이트할 수 있습니다. 새 Key Vault 키 또는 새 키 버전으로 변경하는 경우 이전 key\version을 **삭제하기 전에** 먼저 새 key\version을 사용하도록 해당 키를 사용하는 모든 검색 인덱스나 동의어 맵을 업데이트해야 합니다. 이렇게 하지 않으면 인덱스 또는 동의어 맵을 사용할 수 없게 되며 키 액세스가 손실된 후에는 콘텐츠의 암호를 해독할 수 없습니다. 키 자격 증명 모음 액세스 권한을 나중에 복원해도 콘텐츠 액세스가 복원됩니다.

## <a name="simpler-alternative-trusted-service"></a>더 간단한 대안: 신뢰할 수 있는 서비스

테넌트 구성 및 인증 요구 사항에 따라 주요 자격 증명 모음 키에 액세스하기 위한 더 간단한 방법을 구현할 수 있습니다. Active Directory 애플리케이션을 만들고 사용하는 대신 시스템 관리 ID를 사용하도록 설정하여 검색 서비스를 신뢰할 수 있는 서비스로 만들거나 사용자가 할당한 관리 ID를 검색 서비스에 할당할 수 있습니다. 그런 다음, AD 등록 애플리케이션이 아닌 보안 원칙으로 신뢰할 수 있는 검색 서비스 또는 사용자가 할당한 관리 ID를 사용하여 키 자격 증명 모음 키에 액세스합니다.

이러한 두 방법을 모두 사용하면 애플리케이션 등록 및 애플리케이션 비밀에 대한 단계를 생략하고 암호화 키 정의를 간소화할 수 있습니다.

### <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

일반적으로 관리되는 ID를 사용하면 검색 서비스에서 코드에 자격 증명(ApplicationID 또는 ApplicationSecret)을 저장하지 않고 Azure Key Vault에 인증할 수 있습니다. 이러한 유형의 관리되는 ID의 수명 주기는 관리 ID를 하나만 포함할 수 있는 검색 서비스의 수명 주기에 연결됩니다. 관리 ID의 작동 방식에 대한 자세한 내용은 [Azure 리소스의 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

1. 검색 서비스를 신뢰할 수 있는 서비스로 설정합니다.
   ![시스템 할당 관리 ID 켜기](./media/search-managed-identities/turn-on-system-assigned-identity.png "시스템 할당 관리 ID 켜기")

1. Azure Key Vault에서 액세스 정책을 설정할 때 AD에 등록된 애플리케이션 대신 신뢰받는 검색 서비스를 보안 주체로 선택합니다. 액세스 키 권한 부여 단계에 설명된 것처럼 동일한 권한 (여러 개의 가져오기, 래핑, 래핑 해제)을 할당합니다.

1. Active Directory 속성을 생략하는 `encryptionKey`의 단순화된 생성을 사용합니다.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

이 단순화된 방법을 채택할 수 없게 되는 조건은 다음과 같습니다.

+ 검색 서비스가 Azure Key Vault와 다른 Active Directory 테넌트에 있는 경우와 같이 검색 서비스에 대한 액세스 권한을 키 자격 증명 모음에 직접 부여할 수는 없습니다.

+ 단일 검색 서비스는 각각 다른 키 자격 증명 모음에서 다른 키를 사용하는 여러 암호화된 indexes\synonym 맵을 호스트해야 합니다. 여기서 각 키 자격 증명 모음이 인증을 위해 **다른 ID** 를 사용해야 합니다. 검색 서비스에는 관리 ID가 하나만 있을 수 있기 때문에 복수 ID에 대한 요구 사항은 시나리오에 대한 단순화된 접근 방식을 규정하지 않습니다.  

### <a name="user-assigned-managed-identity-preview"></a>사용자가 할당한 관리 ID(미리 보기)

> [!IMPORTANT] 
> 사용자가 할당한 관리 ID 지원은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다.
> 
> REST API 버전 2021-04-30-Preview 및 [Management REST API 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)에서 이 기능을 제공합니다.

사용자가 할당한 관리 ID를 검색 서비스에 할당하면 코드에 자격 증명(ApplicationID 또는 ApplicationSecret)을 저장하지 않고도 검색 서비스가 Azure Key Vault에 인증할 수 있습니다. 이 형식의 관리 ID 수명 주기는 이 검색 서비스의 수명 주기와는 별개입니다. 관리 ID의 작동 방식에 대한 자세한 내용은 [Azure 리소스의 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

1. 사용자 할당 관리 ID를 아직 만들지 않은 경우에는 새로 만들어야 합니다. 사용자가 할당한 관리 ID를 만들려면 다음 단계를 수행합니다.

    1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
    1. **+ 새 리소스 만들기** 를 선택합니다.
    1. "검색 서비스 및 Marketplace" 검색 창에서 "사용자가 할당한 관리 ID"를 검색한 후, **만들기** 을 선택합니다.
    1. ID에 설명이 포함된 이름을 지정합니다.

1. 그런 다음, 사용자가 할당한 관리 ID를 검색 서비스에 할당합니다. [2021-04-01-preview](/rest/api/searchmanagement/management-api-versions) 관리 API를 사용하여 이 작업을 수행할 수 있습니다.

    ID 속성은 형식 및 하나 이상의 정규화된 사용자 할당 ID를 사용합니다.
    
    * **유형** 은 리소스에 사용되는 ID 유형입니다. 'SystemAssigned, UserAssigned' 유형에는 시스템에서 만든 ID와 사용자 할당 ID 세트가 모두 포함되어 있습니다. 'None' 유형은 서비스에서 모든 ID를 제거합니다.
    * **userAssignedIdentities** 에는 사용자가 할당한 관리 ID의 세부 정보가 포함됩니다.
        * 사용자가 할당한 관리 ID 형식: 
            * /구독/**구독 ID**/resourcegroups/**리소스 그룹 이름**/공급자/Microsoft.ManagedIdentity/userAssignedIdentities/**관리 ID 이름**
    
    사용자가 할당한 관리 ID를 검색 서비스에 할당하는 방법의 예:
    
    ```http
    PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
    Content-Type: application/json

    {
      "location": "[region]",
      "sku": {
        "name": "[sku]"
      },
      "properties": {
        "replicaCount": [replica count],
        "partitionCount": [partition count],
        "hostingMode": "default"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]": {}
        }
      }
    } 
    ```

1. Azure Key Vault에서 액세스 정책을 설정할 때 AD 등록 애플리케이션 대신 사용자가 할당한 관리 ID를 원칙으로 선택합니다. 액세스 키 권한 부여 단계에 설명된 것처럼 동일한 권한 (여러 개의 가져오기, 래핑, 래핑 해제)을 할당합니다.

1. Active Directory 속성을 생략하는 `encryptionKey`의 단순화된 생성을 사용하고 ID 속성을 추가합니다. 2021-04-30-preview REST API 버전을 사용해야 합니다.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://[key vault name].vault.azure.net",
        "keyVaultKeyName": "[key vault key name]",
        "keyVaultKeyVersion": "[key vault key version]",
        "identity" : { 
            "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
            "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]"
        }
      }
    }
    ```

## <a name="work-with-encrypted-content"></a>암호화된 열로 작업

고객 관리형 키 암호화를 사용하여 추가 암호화/암호 해독 작업으로 인해 인덱싱 및 쿼리 모두에 대한 대기 시간을 확인할 수 있습니다. Azure Cognitive Search는 암호화 작업을 기록하지 않지만 키 자격 증명 모음 로깅을 통해 키 액세스를 모니터링할 수 있습니다. 키 자격 증명 모음 구성의 일부로 [로깅을 사용](../key-vault/general/logging.md)하는 것이 좋습니다.

키 회전은 시간이 지남에 따라 발생합니다. 키를 회전할 때마다 다음 시퀀스를 따르는 것이 중요합니다.

1. [인덱스 또는 동의어 맵에 사용되는 키를 확인합니다](search-security-get-encryption-keys.md).
1. [키 자격 증명 모음에 새 키를 만들](../key-vault/keys/quick-create-portal.md)지만 원래 키를 사용할 수 있는 상태로 둡니다.
1. 인덱스 또는 동의어 맵의 [encryptionKey 속성을 업데이트](/rest/api/searchservice/update-index)하여 새 값을 사용합니다. 원래 이 속성을 사용하여 만든 개체만 다른 값을 사용하도록 업데이트할 수 있습니다.
1. 키 자격 증명 모음에서 이전 키를 사용하지 않도록 설정하거나 삭제합니다. 키 액세스를 모니터링하여 새 키가 사용되고 있는지 확인합니다.

성능상의 이유로 검색 서비스는 최대 몇 시간 동안 키를 캐시합니다. 새 키를 제공하지 않고 키를 사용하지 않도록 설정하거나 삭제한 경우에는 캐시가 만료될 때까지 쿼리가 일시적으로 계속 작동합니다. 그러나 검색 서비스에서 콘텐츠 암호를 해독할 수 없는 경우 다음 메시지가 표시됩니다. "액세스가 금지되었습니다. 사용된 쿼리 키가 해지되었을 수 있습니다. 다시 시도하십시오." 

## <a name="next-steps"></a>다음 단계

Azure 보안 아키텍처에 익숙하지 않은 경우 [Azure 보안 설명서](../security/index.yml), 특히 이 문서를 검토합니다.

> [!div class="nextstepaction"]
> [미사용 데이터 암호화](../security/fundamentals/encryption-atrest.md)
