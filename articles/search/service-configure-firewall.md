---
title: IP 방화벽 구성
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 서비스에 대한 액세스를 특정 IP 주소로 제한하도록 IP 제어 정책을 구성합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: e403a71525a8400f47dee01c14ac192c13ab3826
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223192"
---
# <a name="configure-an-ip-firewall-for-azure-cognitive-search"></a>Azure Cognitive Search 대한 IP 방화벽 구성

Azure Cognitive Search Azure 가상 네트워크 보안 그룹에서 찾을 수 있는 IP 규칙과 비슷하게 방화벽을 통한 인바운드 액세스에 대한 IP 규칙을 지원합니다. IP 규칙을 활용하여 검색 서비스 액세스를 승인된 컴퓨터 및 클라우드 서비스 집합으로 제한할 수 있습니다. 승인된 컴퓨터 및 서비스 집합에서 검색 서비스에 저장된 데이터에 액세스하려면 호출자가 유효한 권한 부여 토큰을 제시해야 합니다.

이 문서에 설명된 대로 기본 계층 이상에서 프로비전된 검색 서비스에 대해 Azure Portal IP 규칙을 설정할 수 있습니다. 또는 [Management REST API version 2020-03-13](/rest/api/searchmanagement/), [Azure PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search)를 사용할 수 있습니다.

<a id="configure-ip-policy"></a> 

## <a name="set-ip-ranges-in-azure-portal"></a>Azure Portal IP 범위 설정

Azure Portal IP 액세스 제어 정책을 설정하려면 Azure Cognitive Search 서비스 페이지로 이동하고 왼쪽 탐색 창에서 **네트워킹을** 선택합니다. 엔드포인트 네트워킹 연결은 **공용 액세스** 여야 합니다. 연결이 프라이빗 액세스 또는 **공유 프라이빗** **액세스** 로 설정된 경우 프라이빗 엔드포인트를 통해서만 검색 서비스에 액세스할 수 있습니다.

:::image type="content" source="media/service-configure-firewall/azure-portal-firewall.png" alt-text="Azure Portal에서 IP 방화벽 설정을 구성하는 방법을 보여 주는 스크린샷" border="true":::

Azure Portal은 CIDR 형식으로 IP 주소와 IP 주소 범위를 지정할 수 있습니다. CIDR 표기법의 예로는 8.8.8.0/24가 있습니다. 이는 8.8.8.0에서 8.8.8.255까지 범위의 IP를 나타냅니다.

Azure Cognitive Search 서비스에서 IP 액세스 제어 정책을 사용하도록 설정한 후, 허용되는 IP 주소 범위의 허용 목록에 없는 머신의 데이터 평면에 대한 요청을 할 경우 요청은 거부됩니다. 

## <a name="allow-access-from-azure-portal"></a>Azure Portal에서 액세스 허용

기본적으로 IP 규칙이 구성되면 Azure Portal 일부 기능이 사용하지 않도록 설정됩니다. 서비스 수준 정보를 보고 관리할 수 있지만 인덱스, 인덱서 및 기타 최상위 리소스에 대한 포털 액세스는 제한됩니다.

포털을 통해 서비스 관리를 유지하려면 예외 아래에서 "액세스 허용" 옵션을 **선택합니다.** 또는 [VS Code 확장을](https://aka.ms/vscode-search) 사용하여 콘텐츠를 관리합니다.

## <a name="allow-access-from-your-client"></a>클라이언트에서 액세스 허용

검색 서비스에 인덱싱 및 쿼리 요청을 푸시하는 클라이언트 애플리케이션은 IP 범위로 표시되어야 합니다. Azure에서는 일반적으로 서비스의 FQDN을 ping하여 IP 주소를 확인할 수 있습니다(예: `ping <your-search-service-name>.search.windows.net` 는 검색 서비스의 IP 주소를 반환함). 

클라이언트에 대한 IP 주소를 제공하면 요청이 완전히 거부되지 않지만 콘텐츠 및 작업에 성공적으로 액세스하려면 권한 부여도 필요합니다. 다음 방법 중 하나를 사용하여 요청을 인증합니다.

+ [키 기반 인증](search-security-api-keys.md)- 요청에 대해 관리자 또는 쿼리 API 키가 제공됩니다.
+ [역할 기반 권한 부여](search-security-rbac.md)- 여기서 호출자는 검색 서비스에 대한 보안 역할의 멤버이며 등록된 앱은 Azure Active Directory [OAuth 토큰을 제공합니다.](search-howto-aad.md)

### <a name="rejected-requests"></a>거부된 요청

요청이 허용 목록에 없는 IP 주소에서 시작된 경우 일반 **403 사용할 수 없음** 응답이 추가 세부 정보 없이 반환됩니다.

## <a name="next-steps"></a>다음 단계

클라이언트 애플리케이션이 Azure의 정적 웹앱인 경우 검색 서비스 IP 방화벽 규칙에 포함할 IP 범위를 확인하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure App Service의 인바운드 및 아웃바운드 IP 주소](../app-service/overview-inbound-outbound-ips.md)