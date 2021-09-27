---
title: 보안 및 액세스 제어 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics 보안 및 액세스 제어 문제를 해결하는 방법을 알아봅니다.
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 09/09/2021
ms.author: lle
ms.openlocfilehash: 990fc37f08116aad5b576e2e207aa04913795894
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814959"
---
# <a name="troubleshoot-azure-data-factory-and-synapse-analytics-security-and-access-control-issues"></a>Azure Data Factory 및 Synapse Analytics 보안 및 액세스 제어 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse Analytics 파이프라인의 보안 및 액세스 제어에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>클라우드 데이터 저장소의 복사 작업에서 연결 문제 발생

#### <a name="symptoms"></a>증상

원본 또는 싱크 데이터 저장소에서 연결 문제가 발생하는 경우 다양한 오류 메시지가 반환될 수 있습니다.

#### <a name="cause"></a>원인 

이 문제의 원인은 일반적으로 다음 요인 중 하나입니다.

* 자체 호스팅 IR(통합 런타임)을 사용하는 경우 자체 호스팅 IR 노드의 프록시 설정

* 자체 호스팅 IR을 사용하는 경우 자체 호스팅 IR 노드의 방화벽 설정

* 클라우드 데이터 저장소의 방화벽 설정

#### <a name="resolution"></a>해결 방법

* 연결 문제인지 확인하려면 다음 사항을 확인하세요.

   - 이 오류는 원본 또는 싱크 커넥터에서 throw됩니다.
   - 복사 작업을 시작할 때 오류가 발생합니다.
   - 이 오류는 노드 중 일부에만 문제가 있는 경우 다중 노드 자체 호스팅 IR에서 임의의 오류가 발생할 수 있기 때문에 Azure IR 또는 자체 호스팅 IR에서 일관됩니다.

* Azure IR을 사용하는 경우 동일한 데이터 저장소에 연결할 수 있으므로 **자체 호스팅 IR** 을 사용하는 경우 프록시, 방화벽 및 네트워크 설정을 확인합니다. 이 문제를 해결하려면 다음을 수행합니다.

   * [자체 호스팅 IR 포트 및 방화벽](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Azure Data Lake Storage 커넥터](./connector-azure-data-lake-store.md)
  
* **Azure IR** 을 사용하는 경우 데이터 저장소의 방화벽 설정을 사용하지 않도록 설정해 봅니다. 이 방법은 다음과 같은 두 가지 상황에서 문제를 해결할 수 있습니다.
  
   * [Azure IR IP 주소](./azure-integration-runtime-ip-addresses.md) 는 허용 목록에 없습니다.
   * *신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용* 기능은 [Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) 및 [Azure Data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities)에 대해 해제되어 있습니다.
   * Azure Data Lake Storage Gen1에 대해 *Azure 서비스에 대한 액세스 허용* 설정이 사용하도록 설정되어 있지 않습니다.

앞의 방법이 전혀 작동하지 않는 경우 Microsoft에 도움을 요청하세요.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>퍼블릭 네트워크 액세스를 사용하지 않도록 설정한 후에 올바르지 않거나 비어 있는 인증 키 문제 발생

#### <a name="symptoms"></a>증상

서비스에 대한 공용 네트워크 액세스를 사용하지 않도록 설정하면 자체 호스팅 통합 런타임에서 다음 오류를 throw합니다. "인증 키가 잘못되었거나 비어 있습니다."

#### <a name="cause"></a>원인

퍼블릭 연결을 사용하지 않도록 설정하고 프라이빗 엔드포인트를 설정하면 다시 연결할 수 없게 되므로 DNS(Domain Name System) 확인 문제로 인해 이 문제가 발생할 수 있습니다.

서비스의 FQDN(정규화된 도메인 이름)이 공용 IP 주소로 확인되었는지 확인하려면 다음을 수행합니다.

1. 서비스의 프라이빗 엔드포인트와 동일한 가상 네트워크에 Azure VM(가상 머신)을 만들었으며,

2. Azure VM에서 서비스 FQDN으로 PsPing 및 Ping을 실행합니다.

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > PsPing 명령에 대한 포트를 지정해야 합니다. 여기에는 포트 443이 표시되지만 반드시 필요한 것은 아닙니다.

3. 두 명령이 지정된 영역을 기준으로 하는 Azure Data Factory 공용 IP로 확인되는지 알아봅니다. IP는 `xxx.xxx.xxx.0` 형식이어야 합니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 다음을 수행하십시오.

- 옵션으로 서비스에 대한 "프라이빗 링크 DNS 영역" 아래에 "Virtual Network 링크"를 수동으로 추가하는 것이 좋습니다. 자세한 내용은 [Azure Private Link](./data-factory-private-link.md#dns-changes-for-private-endpoints) 문서를 참조하세요. 이 지침은 프라이빗 DNS 영역 또는 사용자 지정 DNS 서버를 구성하여 서비스 FQDN을 개인 IP 주소로 확인합니다. 

- 그러나 프라이빗 DNS 영역 또는 사용자 지정 DNS 서버를 구성하지 않으려면 다음 임시 솔루션을 시도해 보세요.

  1. Windows 호스트 파일을 변경하고 개인 IP(서비스의 프라이빗 엔드포인트)를 서비스 FQDN에 매핑합니다.
  
     Azure VM에서 `C:\Windows\System32\drivers\etc`로 이동한 후 메모장에서 *host* 파일을 엽니다. 파일의 끝에 있는 FQDN에 개인 IP를 매핑하는 줄을 추가하고 변경 내용을 저장합니다.
     
     :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png" alt-text="호스트에 개인 IP를 매핑하는 스크린샷":::

  1. 위의 확인 단계와 동일한 명령을 다시 실행하여 개인 IP를 포함하는 응답을 확인합니다.

  1. 자체 호스팅 통합 런타임을 다시 등록하면 문제가 해결됩니다.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>프라이빗 링크로 인해 자체 호스팅 VM에 IR 인증 키를 등록할 수 없습니다.

#### <a name="symptoms"></a>증상

프라이빗 링크가 사용하도록 설정되어 있으므로 자체 호스팅 VM에서 IR 인증 키를 등록할 수 없습니다. 다음과 같은 오류 메시지가 표시됩니다.

"ADF 서비스에서 다음 키를 사용하여 서비스 토큰을 가져오지 못했습니다. *************** 시간 비용: 0.1250079초, 오류 코드: InvalidGatewayKey, activityId: XXXXXXX 및 자세한 오류 메시지는 클라이언트 IP 주소가 올바른 프라이빗 IP가 아닙니다. 원인 Data Factory가 퍼블릭 네트워크에 액세스할 수 없으므로 클라우드로 연결할 수 없습니다."

#### <a name="cause"></a>원인

이 문제는 자체 호스팅 IR을 설치하려는 VM 때문에 발생할 수 있습니다. 클라우드에 연결하려면 퍼블릭 네트워크 액세스를 사용하도록 설정해야 합니다.

#### <a name="resolution"></a>해결 방법

**해결 방법 1**
 
이 문제를 해결하려면 다음을 수행하십시오.

1. [팩터리 - 업데이트](/rest/api/datafactory/Factories/Update) 페이지로 이동합니다.

1. 오른쪽 위에서 **체험해 보기** 단추를 선택합니다.
1. **매개 변수** 에서 필요한 정보를 입력합니다. 
1. **본문** 아래에 다음 속성을 붙여넣습니다.

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. **실행** 을 선택하여 함수를 실행합니다. 

1. **매개 변수** 에서 필요한 정보를 입력합니다. 

1. **본문** 아래에 다음 속성을 붙여넣습니다.
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. **실행** 을 선택하여 함수를 실행합니다. 
1. **응답 코드: 200** 이 표시되는지 확인합니다. 붙여넣은 속성은 JSON 정의에도 표시되어야 합니다.

1. 통합 런타임에 IR 인증 키를 다시 추가합니다.

**해결 방법 2**

이 문제를 해결하려면 [Azure Private Link](./data-factory-private-link.md)으로 이동합니다.

다음 스크린샷에 표시된 것처럼 사용자 인터페이스에서 퍼블릭 네트워크 액세스를 사용하도록 설정해 봅니다.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png" alt-text="네트워킹 창의 '공용 네트워크 액세스 허용'에 대한 '사용' 컨트롤의 스크린샷.":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access-synapse.png" alt-text="네트워킹 창의 '공용 네트워크 액세스 허용'에 대한 '사용' 컨트롤의 스크린샷.":::

---

### <a name="service-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>서비스 프라이빗 DNS 영역이 '찾을 수 없습니다' 오류를 발생시키는 Azure Resource Manager DNS 확인 재정의

#### <a name="cause"></a>원인
Azure Resource Manager 및 서비스는 모두 동일한 프라이빗 영역을 사용하여 Azure Resource Manager 레코드를 찾을 수 없는 시나리오에서 고객의 프라이빗 DNS에서 잠재적 충돌을 일으킵니다.

#### <a name="solution"></a>솔루션
1. Azure Portal에서 프라이빗 DNS 영역 **privatelink.azure.com** 을 찾습니다.
:::image type="content" source="media/security-access-control-troubleshoot-guide/private-dns-zones.png" alt-text="프라이빗 설 DNS 영역을 찾는 스크린샷":::
2. A 레코드 **adf** 가 있는지 확인합니다.
:::image type="content" source="media/security-access-control-troubleshoot-guide/a-record.png" alt-text="A 레코드의 스크린샷":::
3.  **가상 네트워크 링크** 로 이동하고 모든 레코드를 삭제합니다.
:::image type="content" source="media/security-access-control-troubleshoot-guide/virtual-network-link.png" alt-text="가상 네트워크 링크의 스크린샷":::
4.  Azure Portal 서비스로 이동하여 포털에 대한 프라이빗 엔드포인트를 다시 만듭니다.
:::image type="content" source="media/security-access-control-troubleshoot-guide/create-private-endpoint.png" alt-text="프라이빗 엔드포인트를 다시 만드는 스크린샷":::
5.  프라이빗 DNS 영역으로 돌아가서 새 프라이빗 DNS 영역 **privatelink.adf.azure.com** 이 있는지 확인합니다.
:::image type="content" source="media/security-access-control-troubleshoot-guide/check-dns-record.png" alt-text="새 DNS 레코드의 스크린샷":::

### <a name="connection-error-in-public-endpoint"></a>퍼블릭 엔드포인트에 연결 오류 발생

#### <a name="symptoms"></a>증상

Azure Blob Storage 계정 퍼블릭 액세스 권한을 사용하여 데이터를 복사하는 경우 다음과 같은 오류를 나타내며 파이프라인 실행이 실패합니다.

예를 들어, Azure Blob Storage 싱크에서 Azure IR(관리형 VNet이 아닌 퍼블릭)을 사용하며 Azure SQL Database 원본은 관리형 VNet IR을 사용하지 않습니다. 또는 원본/싱크에서는 스토리지 퍼블릭 액세스를 수행할 때만 관리형 VNet IR을 사용합니다.

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>원인

서비스는 여전히 관리형 VNet IR을 사용할 수 있지만, 관리되는 VNet의 Azure Blob Storage 대한 공용 엔드포인트는 테스트 결과에 따라 신뢰할 수 없으며, 관리형 가상 네트워크 & [관리형 프라이빗 엔드포인트에](./managed-virtual-network-private-endpoint.md#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network)따라 서비스의 관리형 Virtual Network 퍼블릭 엔드포인트를 통해 Azure Blob Storage 및 Azure Data Lake Gen2를 연결할 수 없으므로 이러한 오류가 발생할 수 있습니다.

#### <a name="solution"></a>솔루션

- 관리형 VNet IR을 사용하는 경우 원본 및 싱크 쪽에서 프라이빗 엔드포인트를 사용하도록 설정합니다.
- 여전히 퍼블릭 엔드포인트를 사용하려는 경우 원본 및 싱크에 대해 관리형 VNet IR을 사용하는 대신, 퍼블릭 IR로 전환할 수 있습니다. 공용 IR로 다시 전환하더라도 관리되는 VNet IR이 아직 있는 경우 서비스에서 관리되는 VNet IR을 계속 사용할 수 있습니다.

### <a name="internal-error-while-trying-to-delete-a-data-factory-or-synapse-workspace-with-customer-managed-key-cmk-and-user-assigned-managed-identity-ua-mi"></a>CMK(고객 관리 키) 및 UA-MI(사용자 할당 관리 ID)를 사용하여 데이터 팩터리 또는 Synapse 작업 영역을 삭제하는 동안 내부 오류 발생

#### <a name="symptoms"></a>증상
`{\"error\":{\"code\":\"InternalError\",\"message\":\"Internal error has occurred.\"}}`

#### <a name="cause"></a>원인

CMK와 관련된 작업을 수행하는 경우 먼저 서비스와 관련된 모든 작업을 완료한 다음 외부 작업(예: 관리 ID 또는 Key Vault 작업)을 완료해야 합니다. 예를 들어 모든 리소스를 삭제하려면 먼저 서비스 인스턴스를 삭제한 다음 키 자격 증명 모음을 삭제해야 합니다.  키 자격 증명 모음을 먼저 삭제하면 서비스에서 필요한 개체를 더 이상 읽을 수 없고 삭제가 가능한지 여부를 확인할 수 없기 때문에 이 오류가 발생합니다. 

#### <a name="solution"></a>솔루션

다음 세 가지 방법으로 이 문제를 해결할 수 있습니다. 다음과 같습니다.

* CMK 키가 저장된 Key Vault에 대한 서비스의 액세스 권한을 취소했습니다. **Get, Unwrap Key 및 Wrap Key** 권한에 대한 액세스를 다시 할당할 수 있습니다. 이러한 권한은 고객 관리형 키를 사용하도록 설정하는 데 필요합니다. [고객 관리형 키에 대한 액세스 권한 부여를](enable-customer-managed-key.md#grant-data-factory-access-to-azure-key-vault)참조하세요. 권한이 제공되면 서비스를 삭제할 수 있습니다.
 
* 고객이 서비스를 삭제하기 전에 Key Vault/CMK를 삭제했습니다. 서비스의 CMK는 기본 보존 정책이 90일인 "일시 삭제"를 사용하도록 설정되고 "보호 제거"를 사용하도록 설정해야 합니다. 삭제된 키를 복원할 수 있습니다.  
[삭제된 키 복구](../key-vault/general/key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates ) 및 [삭제된 키 값](../key-vault/general/key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault)을 검토하세요.

* 서비스 전에 UA-MI(사용자 할당 관리 ID)가 삭제되었습니다. REST API 호출을 사용하여 이 문제를 복구할 수 있으며 모든 프로그래밍 언어로 선택한 HTTP 클라이언트에서 이 작업을 수행할 수 있습니다. Azure 인증을 사용하여 REST API 호출을 아직 설정하지 않은 경우 POSTMAN/Fiddler를 사용하는 것이 가장 쉬운 방법입니다. 다음 단계를 수행하세요.

   1.  메서드를 사용하여 GET 호출: GET URL(예: )   `https://management.azure.com/subscriptions/YourSubscription/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourFactoryName?api-version=2018-06-01`

   2. 다른 이름으로 새 사용자 관리 ID를 만들어야 합니다(동일한 이름을 사용해도 되지만, 명확성을 위해 GET 응답에 있는 이름과 다른 이름을 사용하는 것이 더 안전함).

   3. 새로 생성된 관리 ID를 가리키도록 encryption.identity 속성 및 identity.userassignedidentities를 수정합니다. userAssignedIdentity 개체에서 clientId 및 principalId를 제거합니다. 

   4.  새 본문을 전달하는 동일한 URL에 PUT 호출을 수행합니다. GET 응답에서 얻은 모든 것을 전달하고 ID만 수정하는 것이 매우 중요합니다. 그러지 않으면 의도하지 않게 다른 설정이 재정의됩니다. 

   5.  호출에 성공하면 엔터티를 다시 확인하고 삭제를 다시 시도할 수 있습니다. 

## <a name="sharing-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 공유

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>다른 테넌트의 자체 호스팅 IR 공유는 지원되지 않습니다. 

#### <a name="symptoms"></a>증상

UI에서 자체 호스팅 IR을 공유하려고 하지만 다른 테넌트의 데이터 팩터리 간에 공유할 수 없는 다른 데이터 팩터리(다른 테넌트)를 확인할 수 있습니다.

#### <a name="cause"></a>원인

자체 호스팅 IR은 테넌트 간에 공유할 수 없습니다.


## <a name="next-steps"></a>다음 단계

문제 해결에 대한 도움이 필요한 경우 다음 리소스를 참조하세요.

*  [Data Factory용 Private Link](data-factory-private-link.md)
*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
