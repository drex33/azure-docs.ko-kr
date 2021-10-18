---
title: Azure Digital Twins 솔루션에 대한 보안
titleSuffix: Azure Digital Twins
description: Azure Digital Twins를 사용한 보안 모범 사례를 이해합니다.
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 59fa14f66e5fdb1424a4bac30a30cf70118149b4
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131471"
---
# <a name="secure-azure-digital-twins"></a>Azure Digital Twins 보호

Azure Digital Twins는 보안을 위해 배포 과정에서 특정 데이터, 리소스 및 작업에 대한 정확한 액세스 제어를 가능하게 합니다. 이는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md) 라는 세분화된 역할 및 권한 관리 전략을 통해 수행됩니다.

Azure Digital Twins는 미사용 데이터 암호화도 지원합니다.

## <a name="roles-and-permissions-with-azure-rbac"></a>Azure RBAC를 사용한 역할 및 권한

Azure RBAC는 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)(Azure AD)와의 통합을 통해 Azure Digital Twins에 제공됩니다.

Azure RBAC를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있는 *보안 주체* 에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에 의해 인증되며, 그 대가로 OAuth 2.0 토큰을 받습니다. 이 토큰은 Azure Digital Twins 인스턴스에 대한 액세스 요청의 권한을 부여하는 데 사용할 수 있습니다.

### <a name="authentication-and-authorization"></a>인증 및 권한 부여

Azure AD를 사용하는 경우, 액세스는 2단계 프로세스로 진행됩니다. 보안 주체(사용자, 그룹 또는 애플리케이션)가 Azure Digital Twins에 액세스하려고 할 때 요청을 *인증* 하고 *권한 부여* 해야 합니다. 

1. 먼저, 보안 주체의 ID가 *인증* 되고 OAuth 2.0 토큰이 반환됩니다.
2. 그 다음, 토큰은 지정된 리소스에 대한 액세스 *권한을 부여* 하기 위해 Azure Digital Twins 서비스에 요청의 일부로 전달됩니다.

인증 단계에서는 런타임 시 OAuth 2.0 액세스 토큰을 포함하는 애플리케이션 요청이 필요합니다. 애플리케이션이 [Azure Functions](../azure-functions/functions-overview.md) 앱과 같은 Azure 엔터티 내에서 실행되는 경우 **관리 ID** 를 사용하여 리소스에 액세스할 수 있습니다. 관리 ID에 대한 자세한 내용은 다음 섹션을 참조하세요.

권한 부여 단계를 수행하려면 Azure 역할을 보안 주체에 할당해야 합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 가지는 권한이 결정됩니다. Azure Digital Twins는 Azure Digital Twins 리소스에 대한 권한 세트를 포함하는 Azure 역할을 제공합니다. 이러한 역할은 이 문서의 뒷부분에 설명되어 있습니다.

Azure에서 지원되는 역할 및 역할 할당에 대한 자세한 내용은 Azure RBAC 설명서의 [여러 역할 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)를 참조하세요.

#### <a name="authentication-with-managed-identities"></a>관리 ID를 사용하여 인증

[Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 애플리케이션 코드가 실행되는 배포와 관련된 보안 ID를 만들 수 있도록 하는 Azure 간 기능입니다. 그런 다음, 애플리케이션에 필요한 특정 Azure 리소스에 액세스하기 위한 사용자 지정 권한을 부여하는 액세스 제어 역할에 해당 ID를 연결할 수 있습니다.

관리 ID를 사용하는 경우 Azure 플랫폼에서 이 런타임 ID를 관리합니다. ID 자체 또는 액세스해야 하는 리소스에 대한 액세스 키를 애플리케이션 코드나 구성에 저장하고 보호할 필요가 없습니다. Azure App Service 애플리케이션 내에서 실행되는 Azure Digital Twins 클라이언트 앱은 SAS 규칙 및 키 또는 기타 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱에는 Azure Digital Twins 네임스페이스의 엔드포인트 주소만 필요합니다. 앱이 연결되면 Azure Digital Twins는 관리 엔터티의 컨텍스트를 클라이언트에 바인딩합니다. 관리 ID와 연결되면 Azure Digital Twins 클라이언트는 모든 권한 부여된 작업을 수행할 수 있습니다. 그런 다음, 관리 엔터티를 Azure Digital Twins Azure 역할(아래 설명)과 연결하여 권한을 부여합니다.

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>권한 부여: Azure Digital Twins에 대한 Azure 역할

Azure는 Azure Digital Twins [데이터 평면 API](concepts-apis-sdks.md#overview-data-plane-apis)에 대한 액세스 권한을 부여하기 위해 **두 가지 Azure 기본 제공 역할** 을 제공합니다. 이름 또는 ID로 역할을 참조할 수 있습니다.

| 기본 제공 역할 | 설명 | ID | 
| --- | --- | --- |
| Azure Digital Twins 데이터 소유자 | Azure Digital Twins 리소스에 대한 모든 권한을 제공합니다. | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure Digital Twins 데이터 읽기 권한자 | Azure Digital Twins 리소스에 대한 읽기 액세스를 제공합니다. | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

다음 두 가지 방법으로 역할을 할당할 수 있습니다.
* Azure Portal에서 Azure Digital Twins의 액세스 제어(IAM) 창을 통해([Azure Portal를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md) 참조)
* 역할 추가 또는 제거하는 CLI 명령을 통해

이를 수행하는 방법에 대한 자세한 단계는 Azure Digital Twins [엔드투엔드 솔루션 연결](tutorial-end-to-end.md)을 시도해 보세요.

기본 제공 역할이 정의되는 방법에 대한 자세한 내용은 Azure RBAC 설명서의 [역할 정의 이해](../role-based-access-control/role-definitions.md)를 참조하세요. Azure 사용자 지정 역할 만들기에 대한 자세한 내용은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

##### <a name="automating-roles"></a>역할 자동화

자동화된 시나리오에서 역할을 참조할 때는 이름 대신 **ID** 로 참조하는 것이 좋습니다. 이름은 릴리스 간에 변경될 수 있지만 ID는 변경되지 않으므로 자동화에서 보다 안정적인 참조가 됩니다.

> [!TIP]
> `New-AzRoleAssignment`([참조](/powershell/module/az.resources/new-azroleassignment))와 같은 cmdlet으로 역할을 할당하는 경우 `-RoleDefinitionName` 대신 `-RoleDefinitionId` 매개 변수를 사용하여 역할 이름 대신 ID를 전달할 수 있습니다.

### <a name="permission-scopes"></a>사용 권한 범위

Azure 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따르면 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다.

다음 목록에서는 Azure Digital Twins 리소스에 액세스할 수 있는 범위를 지정할 수 있는 수준을 설명합니다.
* 모델: 이 리소스에 대한 작업은 Azure Digital Twins에 업로드된 [모델](concepts-models.md)에 대한 제어를 지시합니다.
* Query Digital Twins Graph: 이 리소스에 대한 작업은 Azure Digital Twins 그래프 내의 디지털 트윈에서 [쿼리 작업](concepts-query-language.md)을 실행하는 기능을 결정합니다.
* Digital Twin: 이 리소스에 대한 작업은 트윈 그래프의 [디지털 트윈](concepts-twins-graph.md)에 대한 CRUD 작업을 제어합니다.
* Digital Twin 관계: 이 리소스에 대한 작업은 트윈 그래프에서 디지털 트윈 간의 [관계](concepts-twins-graph.md)에 대한 CRUD 작업에 대한 제어를 정의합니다.
* 이벤트 경로: 이 리소스에 대한 작업은 Azure Digital Twins에서 [Event Hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md) 또는 [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)과 같은 엔드포인트 서비스로 [이벤트를 라우팅](concepts-route-events.md)할 권한을 결정합니다.

### <a name="troubleshooting-permissions"></a>권한 문제 해결

사용자가 자신의 역할에서 허용하지 않는 작업을 수행하려고 하면 서비스 요청에서 `403 (Forbidden)` 읽기 오류가 수신될 수 있습니다. 자세한 내용 및 문제 해결 단계는 [실패한 서비스 요청 문제 해결: 오류 403(사용할 수 없음) 을](troubleshoot-error-403.md)참조하세요.

## <a name="managed-identity-for-accessing-other-resources"></a>다른 리소스에 액세스하기 위한 관리 ID

Azure Digital Twins 인스턴스에 대한 [Azure Active Directory(Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **관리 ID** 를 설정하면 인스턴스가 [Azure Key Vault](../key-vault/general/overview.md)와 같은 다른 Azure AD 보호 리소스에 쉽게 액세스할 수 있습니다. ID는 Azure 플랫폼에서 관리하며 비밀을 프로비전하거나 회전할 필요가 없습니다. Azure AD의 관리 ID에 관한 자세한 내용은  [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요. 

Azure는 시스템 할당과 사용자 할당의 두 가지 유형의 관리 ID를 지원합니다. 현재 Azure Digital Twins는 **시스템 할당 ID** 만 지원합니다. 

Azure Digital Instance에 대해 시스템 할당 관리 ID를 사용하여 [사용자 정의 엔드포인트](concepts-route-events.md#create-an-endpoint)에 인증할 수 있습니다. Azure Digital Twines는 [Event Hub](../event-hubs/event-hubs-about.md) 및  [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 대상을 위한 엔드포인트에, 그리고 [배달 못한 편지 이벤트](concepts-route-events.md#dead-letter-events)를 위한 [Azure Storage Container](../storage/blobs/storage-blobs-introduction.md) 엔드포인트에 대해 시스템 할당 ID 기반 인증을 지원합니다. [Event Grid](../event-grid/overview.md) 엔드포인트는 현재 관리 ID에 대해 지원되지 않습니다.

Azure Digital Twins에 대해 시스템 관리 ID를 활성화하고 이를 사용하여 이벤트를 라우팅하는 방법에 대한 지침은 [관리 ID를 사용하여 이벤트 라우팅](how-to-route-with-managed-identity.md)을 참조하세요.

## <a name="private-network-access-with-azure-private-link-preview"></a>Azure Private Link를 사용한 프라이빗 네트워크 액세스(미리 보기)

[Azure Private Link](../private-link/private-link-overview.md)는 Azure 리소스(예: [Azure Event Hubs](../event-hubs/event-hubs-about.md), [Azure Storage](../storage/common/storage-introduction.md) 및 [Azure Cosmos DB](../cosmos-db/introduction.md))와 [Azure Virtual Network(VNet)](../virtual-network/virtual-networks-overview.md)의 프라이빗 엔드포인트를 통해 Azure 호스팅 고객 및 파트너 서비스에 액세스할 수 있는 서비스입니다. 

마찬가지로 Azure Digital Twin 인스턴스에 대한 프라이빗 엔드포인트를 사용하여 가상 네트워크에 위치한 클라이언트가 Private Link를 통해 안전하게 인스턴스에 액세스할 수 있습니다. 

프라이빗 엔드포인트는 Azure VNet 주소 공간의 IP 주소를 사용합니다. 프라이빗 네트워크의 클라이언트와 Azure Digital Twins 인스턴스 간의 네트워크 트래픽은 VNet와 Microsoft 백본 네트워크의 Private Link를 통해 트래버스하여, 공용 인터넷에 노출되지 않습니다. 다음은 이 시스템을 시각적으로 표현한 것입니다.

:::image type="content" source="media/concepts-security/private-link.png" alt-text="Private Link를 통해 Azure Digital Twins 인스턴스에 연결하는 퍼블릭 클라우드 액세스가 없는 보호된 VNET 네트워크를 보여주는 다이어그램.":::

Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트를 구성하면 Azure Digital Twins 인스턴스를 보호하고 공개적으로 노출되지 않도록 할 수 있을 뿐만 아니라 VNet에서 데이터가 반출되지 않도록 할 수 있습니다.

Azure Digital Twins용 Private Link를 설정하는 방법에 대한 지침은 [Private Link로 프라이빗 액세스 사용(미리 보기)](./how-to-enable-private-link.md)을 참조하세요.

### <a name="design-considerations"></a>설계 고려 사항 

Azure Digital Twins용 Private Link로 작업할 때 고려해야 할 몇 가지 요소는 다음과 같습니다.
* **가격 책정**: 가격에 대한 자세한 내용은  [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요. 
* **지역별 가용성**: Azure Digital Twins에서 이 기능은 Azure Digital Twins를 사용할 수 있는 모든 Azure 지역에서 사용할 수 있습니다. 
* **Azure Digital Twins 인스턴스당 최대 프라이빗 엔드포인트 수**: 10

Private Link의 제한에 대한 자세한 내용은  [Azure Private Link 설명서: 제한](../private-link/private-link-service-overview.md#limitations)을 참조하세요.

## <a name="service-tags"></a>서비스 태그

**서비스 태그** 는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트하여 네트워크 보안 규칙을 자주 업데이트할 때 발생하는 복잡성을 최소화합니다. 서비스 태그에 대한 자세한 내용은  [가상 네트워크 태그](../virtual-network/service-tags-overview.md)를 참조하세요. 

보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용하여  [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#security-rules)  또는  [Azure Firewall](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의할 수 있습니다. 규칙의 적절한  *원본*  또는  *대상*  필드에 서비스 태그 이름(이 경우  **AzureDigitalTwins**)을 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. 

다음은 **AzureDigitalTwins** 서비스 태그의 세부 정보입니다.

| 태그 | 목적 | 인바운드 또는 아웃바운드를 사용할 수 있나요? | 지역 범위를 지원할 수 있나요? | Azure Firewall에서 사용할 수 있나요? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>참고: 이 태그 또는 이 태그에 포함된 IP 주소를 사용하여 [이벤트 경로](concepts-route-events.md)에 구성된 엔드포인트에 대한 액세스를 제한할 수 있습니다. | 인바운드 | 예 | 예 |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>이벤트 경로 엔드포인트에 액세스하기 위해 서비스 태그 사용 

다음은 Azure Digital Twins에서 서비스 태그를 사용하여 [이벤트 경로](concepts-route-events.md) 엔드포인트에 액세스하는 단계입니다.

1. 먼저 Azure IP 범위 및 서비스 태그를 보여주는 JSON 파일 참조인 [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드합니다. 

2. JSON 파일에서 "AzureDigitalTwins" IP 범위를 찾습니다.  

3. 엔드포인트에 연결된 외부 리소스(예: [Event Grid](../event-grid/overview.md), [Event Hub](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 또는 [배달 못한 편지 이벤트](concepts-route-events.md#dead-letter-events)에 대한 [Azure Storage](../storage/blobs/storage-blobs-overview.md))의 설명서를 참조하여 해당 리소스에 대한 IP 필터를 설정하는 방법을 확인하세요.

4. *2 단계* 의 IP 범위를 사용하여 외부 리소스에 대한 IP 필터를 설정합니다.  

5. 필요에 따라 정기적으로 IP 범위를 업데이트합니다. 범위는 시간이 지남에 따라 변경될 수 있으므로 정기적으로 확인하고 필요할 때 새로 고치는 것이 좋습니다. 이러한 업데이트의 빈도는 다를 수 있지만 일주일에 한 번 확인하는 것이 좋습니다.

## <a name="encryption-of-data-at-rest"></a>미사용 데이터 암호화

Azure Digital Twins는 데이터 센터에 기록된 미사용 및 전송 중인 데이터를 암호화하고, 액세스할 때 이를 해독합니다. 이 암호화는 Microsoft 관리 암호화 키를 사용하여 수행됩니다.

## <a name="cross-origin-resource-sharing-cors"></a>CORS(원본 간 리소스 공유)

Azure Digital Twins는 현재 **CORS(원본 간 리소스 공유)** 를 지원하지 않습니다. 따라서 브라우저 앱, [API 관리(APIM)](../api-management/api-management-key-concepts.md) 인터페이스 또는 [Power Apps](/powerapps/powerapps-overview) 커넥터에서 REST API를 호출하는 경우 정책 오류가 표시될 수 있습니다.

이 오류를 해결하기 위해 다음 작업 중 하나를 수행할 수 있습니다.
* 메시지에서 CORS 헤더 `Access-Control-Allow-Origin`을 제거합니다. 이 헤더는 응답을 공유할 수 있는지 여부를 나타냅니다. 
* 또는 CORS 프록시를 만들고 이를 통해 Azure Digital Twins REST API를 요청합니다. 

## <a name="next-steps"></a>다음 단계

* [인스턴스 및 인증 설정](how-to-set-up-instance-portal.md)에서 해당 개념이 실제로 적용되는지 확인하세요.

* [앱 인증 코드 쓰기](how-to-authenticate-client.md)에서 클라이언트 애플리케이션 코드의 이러한 개념과 상호 작용하는 방법을 참조하세요.

* [Azure RBAC](../role-based-access-control/overview.md)에 대해 자세히 알아보세요.