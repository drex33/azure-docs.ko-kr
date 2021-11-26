---
title: 보안 개요
titleSuffix: Azure Cognitive Search
description: 엔드포인트, 콘텐츠 및 작업을 보호하는 Azure Cognitive Search의 보안 기능에 대해 알아 봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2021
ms.custom: references_regions
ms.openlocfilehash: 79be99461e9557ccb55c59604998330a803e5774
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133128933"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Azure Cognitive Search에 대한 보안 개요

이 문서에서는 데이터 및 작업을 보호하는 Azure Cognitive Search의 보안 기능에 대해 설명합니다.

## <a name="network-traffic-patterns"></a>네트워크 트래픽 패턴

검색 서비스는 Azure에서 호스트되며 일반적으로 퍼블릭 네트워크 연결을 통해 액세스됩니다. 서비스의 액세스 패턴을 이해하면 검색 가능한 콘텐츠에 대한 무단 액세스를 효과적으로 방지하는 보안 전략을 설계할 수 있습니다.

Cognitive Search에는 다음과 같은 세 가지 기본 네트워크 트래픽 패턴이 있습니다.

+ 검색 서비스에 대한 클라이언트의 인바운드 요청(주요 패턴)
+ Azure 및 다른 위치의 다른 서비스에 대해 검색 서비스에서 실행한 아웃바운드 요청
+ 보안 Microsoft 백본 네트워크를 통한 내부 서비스 간 요청

인바운드 요청은 개체 만들기, 데이터 로드 및 쿼리 등 다양합니다. 데이터 및 작업에 대한 인바운드 액세스의 경우 요청이 있을 때 API 키부터 보안 조치가 진행되도록 할 수 있습니다. 그런 다음, IP 방화벽의 인바운드 규칙으로 보완하거나 퍼블릭 인터넷에서 서비스를 완전히 보호하는 프라이빗 엔드포인트를 만들 수 있습니다.

아웃바운드 요청에는 읽기 및 쓰기 작업이 모두 포함될 수 있습니다. 아웃바운드 호출의 주 에이전트는 인덱서 및 구성 기술 세트입니다. 인덱서의 경우 읽기 작업에는 [문서 크래킹](search-indexer-overview.md#document-cracking) 및 데이터 수집이 포함됩니다. 또한 인덱서는 지식 저장소를 만들고, 캐시된 강화를 유지하고, 디버그 세션을 유지할 때 Azure Storage에 쓸 수 있습니다. 마지막으로 기술 세트는 외부 코드를 실행하는 사용자 지정 기술(예: Azure Functions 또는 웹앱)을 포함할 수도 있습니다.

내부 요청에는 진단 로깅, 암호화, Azure Active Directory를 통한 인증 및 권한 부여, 프라이빗 엔드포인트 연결, Cognitive Services에 대한 기본 제공 기술 요청 등의 태스크에 대한 서비스 간 호출이 포함됩니다.

## <a name="network-security"></a>네트워크 보안

<a name="service-access-and-authentication"></a>

인바운드 보안 기능은 보안 및 복잡성 수준을 높여 검색 서비스 엔드포인트를 보호합니다. Cognitive Search는 모든 요청에 대해 인증된 액세스를 위한 API 키가 필요한 [키 기반 인증](search-security-api-keys.md)을 사용합니다.

필요에 따라 특정 IP 주소에 대한 액세스를 제한하는 방화벽 규칙을 설정하여 추가 제어 계층을 구현할 수 있습니다. 고급 보호를 위해 Azure Private Link를 사용하도록 설정하여 모든 인터넷 트래픽으로부터 서비스 엔드포인트를 보호할 수 있습니다.

### <a name="connect-over-the-public-internet"></a>퍼블릭 인터넷을 통해 연결

검색 서비스 엔드포인트는 기본적으로 관리자에 대한 키 기반 인증을 사용하거나 검색 서비스 엔드포인트에 대한 쿼리 액세스를 사용하여 퍼블릭 클라우드를 통해 액세스됩니다. 키는 필수입니다. 유효한 키를 제출하면 요청이 신뢰할 수 있는 엔터티에서 시작되었다는 증명으로 간주됩니다. 키 기반 인증은 다음 섹션에서 설명합니다. API 키가 없으면 요청에 대해 401 및 404 응답을 받게 됩니다.

### <a name="connect-through-ip-firewalls"></a>IP 방화벽을 통해 연결

검색 서비스에 대한 액세스를 추가로 제어하기 위해 특정 IP 주소 또는 IP 주소 범위에 대한 액세스를 허용하는 인바운드 방화벽 규칙을 만들 수 있습니다. 모든 클라이언트 연결은 허용된 IP 주소를 통해 수행되어야 합니다. 그렇지 않으면 연결이 거부됩니다.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="IP 제한 액세스에 대한 샘플 아키텍처 다이어그램":::

포털을 사용하여 [인바운드 액세스를 구성](service-configure-firewall.md)할 수 있습니다.

또는 관리 REST API를 사용할 수 있습니다. API 버전 2020-03-13부터 [IpRule](/rest/api/searchmanagement/2020-08-01/services/create-or-update#iprule) 매개 변수를 사용하면 검색 서비스에 대한 액세스 권한을 부여하려는 IP 주소를 개별적으로 또는 범위에서 식별하여 서비스에 대한 액세스를 제한할 수 있습니다.

### <a name="connect-to-a-private-endpoint-network-isolation-no-internet-traffic"></a>프라이빗 엔드포인트에 연결(네트워크 격리, 인터넷 트래픽 없음)

Azure Cognitive Search에 대한 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 설정하면 가[가상 네트워크](../virtual-network/virtual-networks-overview.md)의 클라이언트에서 [Private Link](../private-link/private-link-overview.md)를 통해 검색 인덱스의 데이터에 안전하게 액세스할 수 있습니다.

프라이빗 엔드포인트는 검색 서비스에 연결하기 위해 가상 네트워크 주소 공간의 IP 주소를 사용합니다. 클라이언트와 검색 서비스 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크를 통과하여 퍼블릭 인터넷에서 공개되지 않도록 합니다. VNET을 사용하면 온-프레미스 네트워크 및 인터넷을 통해 리소스 간에 안전하게 통신할 수 있습니다.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="프라이빗 엔드포인트 액세스에 대한 샘플 아키텍처 다이어그램":::

이 솔루션은 가장 안전하지만, 추가 서비스를 사용하면 추가 비용이 발생하므로 자세히 살펴보기 전에 혜택을 명확히 이해하고 있어야 합니다. 비용에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/private-link/)를 참조하세요. 이러한 구성 요소가 함께 작동하는 방법에 대한 자세한 내용은 이 문서의 위쪽에 있는 비디오를 시청하세요. 프라이빗 엔드포인트 옵션의 적용 범위는 비디오의 5분 48초 지점에서 시작됩니다. 엔드포인트를 설정하는 방법에 대한 지침은 [Azure Cognitive Search에 대한 프라이빗 엔드포인트 만들기](service-create-private-endpoint.md)를 참조하세요.

### <a name="outbound-connections-to-external-services"></a>외부 서비스에 대한 아웃바운드 연결

인덱서와 기술 세트는 모두 외부 연결을 만들 수 있는 개체입니다. 이러한 메커니즘 중 하나를 사용하여 개체 정의의 일부로 연결 정보를 제공합니다.

+ 연결 문자열의 자격 증명

+ 연결 문자열의 관리 ID

  Azure Storage, Azure SQL, Cosmos DB 또는 기타 Azure 데이터 원본에서 데이터에 액세스할 때 검색을 신뢰할 수 있는 서비스로 만들기 위해 관리 ID를 설정할 수 있습니다. 관리 ID는 연결에 대한 자격 증명 또는 액세스 키를 대체합니다. 이 기능에 대한 자세한 내용은 [관리 ID를 사용하여 데이터 원본에 연결](search-howto-managed-identities-data-sources.md)을 참조하세요.

## <a name="authentication"></a>인증

검색 서비스에 대한 인바운드 요청의 경우 인증은 신뢰할 수 있는 원본에서 제공한 요청임을 증명하는 [API 키](search-security-api-keys.md)(임의로 생성된 숫자 및 문자로 구성된 문자열)를 통해 수행됩니다. 또는 Azure Active Directory 인증 및 역할 기반 권한 부여에 대한 새로운 지원이 [현재 미리 보기](search-security-rbac.md)로 제공됩니다.

인덱서에서 수행하는 아웃바운드 요청은 외부 서비스의 인증을 받습니다. Cognitive Search의 인덱서 하위 서비스는 관리 ID를 사용하여 Azure에서 신뢰할 수 있는 서비스로 만들어 다른 서비스에 연결할 수 있습니다. 자세한 내용은 [관리 ID를 사용하여 데이터 원본에 대한 인덱서 연결 설정](search-howto-managed-identities-data-sources.md)을 참조하세요.

## <a name="authorization"></a>권한 부여

Cognitive Search는 콘텐츠 관리 및 서비스 관리를 위한 다양한 권한 부여 모델을 제공합니다. 

### <a name="authorization-for-content-management"></a>콘텐츠 관리 권한 부여

콘텐츠에 대한 권한 부여 및 콘텐츠와 관련된 작업은 요청에 제공된 [API 키](search-security-api-keys.md)를 통해 부여되는 쓰기 액세스 권한입니다. API 키는 인증 메커니즘이지만, API 키 유형에 따라 액세스 권한도 부여합니다.

+ 관리자 키(검색 서비스에서 create-read-update-delete 작업에 대한 읽기-쓰기 액세스 허용) - 서비스가 프로비저닝될 때 만들어집니다.

+ 쿼리 키(인덱스의 문서 컬렉션에 대한 읽기 전용 액세스 허용) - 필요에 따라 만들어지며, 쿼리를 실행하는 클라이언트 애플리케이션을 위해 설계되었습니다.

애플리케이션 코드에서 콘텐츠 및 옵션에 대한 액세스를 허용하도록 엔드포인트와 API 키를 지정합니다. 엔드포인트는 서비스 자체, 인덱스 컬렉션, 특정 인덱스, 문서 컬렉션 또는 특정 문서일 수 있습니다. 함께 연결되면 엔드포인트, 작업(예: 만들기 또는 업데이트 요청) 및 권한 수준(키 기반의 전체 권한 또는 읽기 전용 권한)에서 콘텐츠와 작업을 보호하는 보안 수식을 구성합니다.

> [!NOTE]
> Azure RBAC(역할 기반 액세스 제어)를 사용하는 데이터 평면 작업에 대한 권한 부여는 현재 미리 보기로 제공됩니다. [API 키 대신 역할 할당을 사용](search-security-rbac.md)하려면 이 미리 보기 기능을 사용할 수 있습니다.

### <a name="controlling-access-to-indexes"></a>인덱스에 대한 액세스 제어

Azure Cognitive Search에서 개별 인덱스는 보안 개체가 아닙니다. 대신 인덱스에 대한 액세스는 작업의 컨텍스트와 함께 서비스 계층(제공하는 API 키에 따라 읽기 또는 쓰기 액세스)에서 결정됩니다.

읽기 전용 액세스의 경우 [쿼리 키](search-security-api-keys.md)를 사용하여 연결할 쿼리 요청을 구성하고, 앱에서 사용하는 특정 인덱스를 포함할 수 있습니다. 쿼리 요청에서 동시에 인덱스를 조인하거나 여러 인덱스에 액세스한다는 개념은 없으므로 모든 요청은 정의에 따라 단일 인덱스를 대상으로 지정합니다. 따라서 쿼리 요청 자체의 구조(키 및 단일 대상 인덱스)는 보안 경계를 정의합니다.

인덱스에 대한 관리자 및 개발자 액세스는 구분되지 않습니다. 둘 다 서비스에서 관리되는 개체를 만들고, 삭제하고, 업데이트하는 쓰기 액세스 권한이 필요합니다. 서비스에 대한 [관리자 키](search-security-api-keys.md)가 있는 모든 사용자는 동일한 서비스에서 모든 인덱스를 읽거나, 수정하거나, 삭제할 수 있습니다. 인덱스를 우발적이거나 악의적인 삭제로부터 보호하기 위해 코드 자산에 대한 내부 원본을 제어하여 원치 않는 삭제나 수정을 되돌리는 문제를 해결합니다. Azure Cognitive Search에는 가용성을 보장하기 위해 클러스터 내에서 장애 조치(failover) 기능이 있지만 인덱스를 만들거나 로드하는 데 사용되는 전용 코드를 저장하거나 실행하지 않습니다.

인덱스 수준에서 보안 경계를 요구하는 다중 테넌트 솔루션의 경우 이러한 솔루션에는 일반적으로 중간 계층이 포함됩니다. 고객은 인덱스 격리를 처리하기 위해 이를 사용합니다. 다중 테넌트 사용 사례에 대한 자세한 내용은 [다중 테넌트 SaaS 애플리케이션 및 Azure Cognitive Search에 대한 디자인 패턴](search-modeling-multitenant-saas-applications.md)을 참조하세요.

### <a name="controlling-access-to-documents"></a>문서에 대한 액세스 제어

검색 결과에 대한 세부적인 사용자별 제어가 필요한 경우 쿼리에 대한 보안 필터를 빌드하여 지정된 보안 ID와 연결된 문서를 반환할 수 있습니다. 

개념적으로 "행 수준 보안"과 동일하며, 인덱스 내의 콘텐츠에 대한 권한 부여는 Azure Active Directory의 엔터티에 매핑되는 미리 정의된 역할 또는 역할 할당을 사용하여 기본적으로 지원되지 않습니다. Cosmos DB와 같은 외부 시스템의 데이터에 대한 모든 사용자 권한은 Cognitive Search를 통해 인덱싱되므로 해당 데이터와 함께 전송되지 않습니다.

"행 수준 보안"이 필요한 솔루션에 대한 해결 방법에는 보안 그룹 또는 사용자 ID를 나타내는 필드를 데이터 원본에 만든 다음, Cognitive Search의 필터를 사용하여 ID를 기준으로 문서 및 콘텐츠의 검색 결과를 선택적으로 조정하는 방법이 포함됩니다. 다음 표에서는 권한이 없는 콘텐츠의 검색 결과를 잘라내는 방법에 대한 두 가지 방법을 설명합니다.

| 접근 방식 | 설명 |
|----------|-------------|
|[ID 필터에 따라 보안 조정](search-security-trimming-for-azure-search.md)  | 사용자 ID 액세스 제어를 구현하기 위한 기본 워크플로를 문서화합니다. 인덱스에 보안 식별자를 추가하는 방법을 다루고 금지된 콘텐츠의 결과를 잘라내는 해당 필드에 대한 필터링을 설명합니다. |
|[Azure Active Directory ID에 따라 보안 조정](search-security-trimming-for-azure-search-with-aad.md)  | 이 문서는 이전 문서를 확장하여 Azure 클라우드 플랫폼의 [체험 서비스](https://azure.microsoft.com/free/) 중 하나인 Azure AD(Azure Active Directory)에서 ID를 검색하는 단계를 제공합니다. |

### <a name="authorization-for-service-management"></a>서비스 관리에 대한 권한 부여

서비스 관리 작업에 대한 권한은 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 부여됩니다. Azure RBAC는 Azure 리소스의 프로비저닝을 위한 [Azure Resource Manager](../azure-resource-manager/management/overview.md)를 기반으로 하는 권한 부여 시스템입니다. 

Azure Cognitive Search에서 Resource Manager는 서비스를 만들거나 삭제하고, API 키를 관리하며, 서비스를 스케일링하는 데 사용됩니다. 따라서 Azure 역할 할당은 [포털](search-manage.md), [PowerShell](search-manage-powershell.md) 또는 [관리 REST API](/rest/api/searchmanagement)를 사용하는지 여부에 관계없이 이러한 작업을 수행할 수 있는 사용자를 결정합니다.

검색 서비스 관리에 대한 [세 가지 기본 역할](search-security-rbac.md)이 정의됩니다. 역할 할당은 지원되는 모든 방법(포털, PowerShell 등)을 사용하여 수행할 수 있으며 서비스 전체에 적용됩니다. 소유자 및 기여자 역할은 다양한 관리 기능을 수행할 수 있습니다. 읽기 권한자 역할은 필수 정보만 보는 사용자에게 할당할 수 있습니다.

> [!Note]
> Azure 전체 메커니즘을 사용하여 구독 또는 리소스를 잠가서 관리자 권한이 있는 사용자가 검색 서비스를 실수로 삭제하거나 무단으로 삭제하는 것을 방지할 수 있습니다. 자세한 내용은 [예기치 않은 삭제를 방지하기 위해 리소스 잠그기](../azure-resource-manager/management/lock-resources.md)를 참조하세요.

<a name="encryption"></a>

## <a name="data-protection"></a>데이터 보호

스토리지 계층에서는 인덱스, 동의어 맵, 인덱서, 데이터 원본 및 기술 세트의 정의를 포함하여 디스크에 저장된 모든 서비스 관리 콘텐츠에 대해 데이터 암호화가 기본적으로 제공됩니다. 필요에 따라 인덱싱된 콘텐츠를 추가로 암호화하기 위해 CMK(고객 관리형 키)를 추가할 수 있습니다. 2020년 8월 1일 이후에 만들어진 서비스의 경우 CMK 암호화는 인덱싱된 콘텐츠의 전체 "이중 암호화"를 위해 임시 디스크의 데이터까지 확장됩니다.

### <a name="data-in-transit"></a>전송 중 데이터

Azure Cognitive Search에서 암호화는 연결 및 전송에서 시작하여 디스크에 저장된 콘텐츠까지 확장됩니다. 퍼블릭 인터넷에서 수행되는 검색 서비스의 경우 Azure Cognitive Search는 443 HTTPS 포트에서 수신 대기합니다. 모든 클라이언트-서비스 연결은 TLS 1.2 암호화를 사용합니다. 이전 버전(1.0 또는 1.1)은 지원되지 않습니다.

### <a name="encrypted-data-at-rest"></a>암호화된 미사용 데이터

검색 서비스에서 내부적으로 처리하는 데이터의 경우 다음 표에서 [데이터 암호화 모델](../security/fundamentals/encryption-models.md)에 대해 설명합니다. 지식 저장소, 증분 보강 및 인덱서 기반 인덱싱과 같은 일부 기능은 다른 Azure 서비스의 데이터 구조에서 읽거나 씁니다. 이러한 서비스에는 Azure Cognitive Search와 별도로 자체 수준의 암호화 지원이 있습니다.

| 모델 | 키&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 요구 사항&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 제한 | 적용 대상 |
|------------------|-------|-------------|--------------|------------|
| 서버 쪽 암호화 | Microsoft 관리형 키 | 없음(기본 제공) | 없음. 2018년 1월 24일 이후에 만들어진 콘텐츠의 경우 모든 지역의 모든 계층에서 사용할 수 있습니다. | 콘텐츠(인덱스 및 동의어 맵) 및 정의(인덱서, 데이터 원본, 기술 세트) |
| 서버 쪽 암호화 | 고객 관리형 키 | Azure Key Vault | 2019년 1월 이후 이후에 만들어진 콘텐츠의 경우 모든 지역의 청구 가능한 계층에서 사용할 수 있습니다. | 데이터 디스크의 콘텐츠(인덱스 및 동의어 맵) |
| 서버 쪽 이중 암호화 | 고객 관리형 키 | Azure Key Vault | 2020년 8월 1일 이후에 검색 서비스에서 선택한 지역의 청구 가능한 계층에서 사용할 수 있습니다. | 데이터 디스크 및 임시 디스크의 콘텐츠(인덱스 및 동의어 맵) |

### <a name="service-managed-keys"></a>서비스 관리형 키

서비스 관리형 암호화는 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하는 [Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 기반으로 하는 Microsoft 내부 작업입니다. 완전히 암호화되지 않은 인덱스(2018년 1월 이전에 만들어짐)에 대한 증분 업데이트를 포함하여 모든 인덱싱에서 자동으로 수행됩니다.

### <a name="customer-managed-keys-cmk"></a>CMK(고객 관리형 키)

고객 관리형 키에는 Azure Cognitive Search와 같이 다른 지역에 있지만 동일한 구독에 있을 수 있는 추가 청구 가능 서비스인 Azure Key Vault가 필요합니다. CMK 암호화를 사용하도록 설정하면 인덱스 크기가 증가하고 쿼리 성능이 저하됩니다. 현재까지 관찰한 결과에 따르면 실제 성능은 인덱스 정의 및 쿼리 유형에 따라 다르지만 쿼리 시간이 30%-60% 증가하는 것으로 예상할 수 있습니다. 이 성능의 영향으로 인해 실제로 필요한 인덱스에서만 이 기능을 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [Azure Cognitive Search에서 고객 관리형 암호화 키 구성](search-security-manage-encryption-keys.md)을 참조하세요.

<a name="double-encryption"></a>

### <a name="double-encryption"></a>이중 암호화

Azure Cognitive Search에서 이중 암호화는 CMK의 확장입니다. 이중 암호화(CMK를 통해 한 번 및 서비스 관리형 키를 통해 다시 한 번)로 이해되며, 데이터 디스크에 기록되는 장기 스토리지 및 임시 디스크에 기록되는 단기 스토리지를 포함하는 포괄적인 범위로 이해됩니다. 이중 암호화는 특정 날짜 이후에 만든 서비스에서 구현됩니다. 자세한 내용은 [이중 암호화](search-security-manage-encryption-keys.md#double-encryption)를 참조하세요.

## <a name="security-management"></a>보안 관리

### <a name="api-keys"></a>API 키

API 키 기반 인증을 사용하는 것은 Azure 보안 모범 사례에 따라 관리자 키를 정기적으로 다시 생성하기 위한 계획이 있어야 함을 의미합니다. 검색 서비스당 최대 두 개의 관리자 키가 있습니다. API 키를 보호하고 관리하는 방법에 대한 자세한 내용은 [api-key 만들기 및 관리](search-security-api-keys.md)를 참조하세요.

#### <a name="activity-and-diagnostic-logs"></a>활동 및 진단 로그

Cognitive Search는 사용자 ID를 기록하지 않으므로 특정 사용자의 정보에 대한 로그를 참조할 수 없습니다. 그러나 서비스에서 로그 만들기-읽기-업데이트-삭제 작업을 수행하므로 특정 작업의 에이전시를 이해하기 위해 다른 로그와 상호 연결할 수 있습니다.

Azure의 경고 및 로깅 인프라를 사용하여 쿼리 볼륨 급증 또는 예상되는 워크로드에서 벗어난 다른 작업을 선택할 수 있습니다. 로그를 설정하는 방법에 대한 자세한 내용은 [로그 데이터 수집 및 분석](monitor-azure-cognitive-search.md) 및 [쿼리 요청 모니터링](search-monitor-queries.md)을 참조하세요.

### <a name="certifications-and-compliance"></a>인증 및 규정 준수

Azure Cognitive Search는 정기적인 감사에 참가하고, 퍼블릭 클라우드와 Azure Government 모두에 대해 다양한 전체, 지역 및 산업별 표준에 대해 인증을 받았습니다. 전체 목록은 공식 감사 보고서 페이지에서 [**Microsoft Azure 규정 준수 제안** 백서](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)를 다운로드하세요.

규정 준수를 위해 [Azure Policy](../governance/policy/overview.md)를 사용하여 높은 수준의 [Azure Security Benchmark](../security/benchmarks/introduction.md) 보안 모범 사례를 구현할 수 있습니다. Azure Security Benchmark는 서비스 및 데이터에 대한 위협을 완화하기 위해 수행해야 하는 주요 작업에 매핑되는 보안 제어로 성문화된 보안 권장 사항의 모음입니다. 현재 [네트워크 보안](../security/benchmarks/security-control-network-security.md), [로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md) 및 [데이터 보호](../security/benchmarks/security-control-data-protection.md)를 포함하여 11개의 보안 제어가 있습니다.

Azure Policy는 Azure Security Benchmark의 표준을 포함하여 여러 표준에 대한 규정 준수를 관리하는 데 도움이 되는 Azure에 기본 제공되는 기능입니다. 잘 알려진 벤치마크의 경우 Azure Policy는 두 조건을 모두 제공하는 기본 제공 정의 및 비준수를 해결하는 실행 가능한 응답을 제공합니다.

Azure Cognitive Search의 경우 현재 하나의 기본 제공 정의가 있습니다. 이는 진단 로깅에 대한 것입니다. 이 기본 제공 기능을 사용하면 진단 로깅이 누락된 검색 서비스를 식별하는 정책을 할당한 다음, 해당 진단 로깅을 설정할 수 있습니다. 자세한 내용은 [Azure Cognitive Search에 대한 Azure Policy 규정 준수 제어](security-controls-policy.md)를 참조하세요.

## <a name="watch-this-video"></a>이 동영상 시청

보안 아키텍처 및 각 기능 범주에 대한 개요를 보려면 아래의 빠른 학습 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="see-also"></a>참고 항목

+ [Azure 보안 기본 사항](../security/fundamentals/index.yml)
+ [Azure Security](https://azure.microsoft.com/overview/security)
+ [Microsoft Defender for Cloud](../security-center/index.yml)
