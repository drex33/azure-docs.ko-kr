---
title: Azure API Management를 사용하여 내부 가상 네트워크에 연결
description: 내부 모드를 사용 하 여 가상 네트워크에서 Azure API Management를 설정 하 고 구성 하는 방법을 알아봅니다.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/10/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee5d0ac639cbd36215df5545d684909af3acd748
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563835"
---
# <a name="connect-to-a-virtual-network-in-internal-mode-using-azure-api-management"></a>Azure API Management를 사용 하 여 내부 모드에서 가상 네트워크에 커넥트 
Azure Vnet (가상 네트워크)를 사용 하 여 Azure API Management는 여러 VPN 기술을 사용 하 여 인터넷에 액세스할 수 없는 Api를 관리 하 여 연결을 만들 수 있습니다. [외부](./api-management-using-with-vnet.md) 또는 내부 모드를 통해 API Management를 배포할 수 있습니다. VNET 연결 옵션, 요구 사항 및 고려 사항은 [Azure API Management에서 가상 네트워크 사용](virtual-network-concepts.md)을 참조 하세요.

이 문서에서는 내부 VNET 모드에서 API Management를 배포하는 방법을 알아봅니다. 이 모드에서는 사용자가 제어 하는 액세스 권한을 가진 VNET 내에서 다음 서비스 끝점만 볼 수 있습니다.
* API 게이트웨이
* 개발자 포털
* 직접 관리
* Git 

> [!NOTE]
> 서비스 엔드포인트는 공용 DNS에 등록되지 않습니다. VNET에 대해 [DNS를 구성할](#dns-configuration) 때까지 서비스 끝점에 액세스할 수 없게 됩니다.

내부 모드에서 API Management를 사용하여 다음을 수행합니다.

* Azure VPN 연결 또는 Azure Express 경로를 사용 하 여 외부의 타사에서 개인 데이터 센터에 호스트 된 Api를 안전 하 게 액세스할 수 있도록 합니다.
* 공통 게이트웨이를 통해 클라우드 기반 API와 온-프레미스 API를 공개함으로써 하이브리드 클라우드 시나리오를 사용하도록 설정합니다.
* 단일 게이트웨이 엔드포인트를 사용하여 여러 지리적 위치에서 호스팅되는 API를 관리합니다.

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-vnet-internal.png" alt-text="내부 VNET에 커넥트":::

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>사전 요구 사항

일부 필수 구성 요소는 `stv2` `stv1` API Management 인스턴스에 대 한 [계산 플랫폼](compute-infrastructure.md) 의 버전 (또는)에 따라 달라 집니다. 

> [!TIP]
> 포털을 사용 하 여 API Management 인스턴스의 네트워크 구성을 만들거나 업데이트 하는 경우 인스턴스는 `stv2` 계산 플랫폼에서 호스팅됩니다.

### <a name="stv2"></a>[stv2](#tab/stv2)

+ **API Management 인스턴스.** 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

* API Management 인스턴스와 동일한 지역 및 구독에 있는 **가상 네트워크 및 서브넷** 서브넷은 다른 Azure 리소스를 포함할 수 있습니다.

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

   > [!NOTE]
   > 플랫폼의 내부 가상 네트워크에 API Management 서비스를 배포 하는 경우 `stv2` 공용 IP 주소 리소스를 사용 하 여 [표준 SKU](../load-balancer/skus.md)의 내부 부하 분산 장치 뒤에 호스트 됩니다.

### <a name="stv1"></a>[stv1](#tab/stv1)

+ **API Management 인스턴스.** 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

* API Management 인스턴스와 동일한 지역 및 구독에 있는 **가상 네트워크 및 서브넷**

    서브넷은 API Management 인스턴스에 전용 이어야 합니다. 다른 리소스가 포함된 Resource Manager VNET 서브넷에 Azure API Management 인스턴스를 배포하려고 하면 배포가 실패합니다.

   > [!NOTE]
   > 플랫폼의 내부 가상 네트워크에 API Management 서비스를 배포 하는 경우 `stv1` [기본 SKU](../load-balancer/skus.md)의 내부 부하 분산 장치 뒤에 호스팅됩니다.

---

## <a name="enable-vnet-connection"></a>VNET 연결 사용

### <a name="enable-vnet-connectivity-using-the-azure-portal-stv2-platform"></a>Azure Portal (플랫폼)를 사용 하 여 VNET 연결 사용 `stv2`

1. [Azure Portal](https://portal.azure.com)로 이동하여 API Management 인스턴스를 찾습니다. **API Management 서비스** 를 검색하고 선택합니다.
1. API Management 인스턴스를 선택합니다.
1. **가상 네트워크** 를 선택합니다.
1. **내부** 액세스 유형을 선택 합니다.
1. API Management 서비스가 프로 비전 되는 위치 (지역) 목록에서: 
    1. **위치** 를 선택합니다.
    1. **가상 네트워크**, **서브넷** 및 **IP 주소** 를 선택 합니다. 
        * VNET 목록은 사용자가 구성하고 있는 하위 지역에 설정된 Azure 구독에서 사용할 수 있는 Resource Manager VNET으로 채워집니다.
1. **적용** 을 선택합니다. API Management 인스턴스의 **가상 네트워크** 페이지가 새 VNET 및 선택한 서브넷 항목으로 업데이트됩니다.
   :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png" alt-text="Azure Portal에서 내부 VNET 설정":::
1. API Management 인스턴스의 나머지 위치에 대한 VNET 설정을 계속 구성합니다.
1. 위쪽 탐색 모음에서 **저장** 을 선택하고 **네트워크 구성 적용** 을 선택합니다.

    API Management 인스턴스를 업데이트하는 데 15~45분이 걸릴 수 있습니다.

배포에 성공하면, **개요** 블레이드에 API Management 서비스의 **개인** 가상 IP 주소와 **공용** 가상 IP 주소가 표시됩니다. IP 주소에 대 한 자세한 내용은이 문서의 [라우팅](#routing) 을 참조 하세요.

:::image type="content" source="media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png" alt-text="Azure Portal에서 주소가 지정 된 공용 및 개인 IP"::: 

> [!NOTE]
> 게이트웨이 URL은 공용 DNS에 등록 되지 않으므로 Azure Portal에서 사용할 수 있는 테스트 콘솔은 **내부** VNET 배포 된 서비스에 대해 작동 하지 않습니다. 대신, **개발자 포털** 에 제공된 테스트 콘솔을 사용해야 합니다.

### <a name="enable-connectivity-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 연결 사용

#### <a name="api-version-2021-01-01-preview-stv2-platform"></a>API 버전 2021-01-01-미리 보기 ( `stv2` 플랫폼)

* Azure Resource Manager [템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet-publicip)

     [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet-publicip%2Fazuredeploy.json)

#### <a name="api-version-2020-12-01-stv1-platform"></a>API 버전 2020-12-01 ( `stv1` 플랫폼)

* Azure Resource Manager [템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet)

     [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

### <a name="enable-connectivity-using-azure-powershell-cmdlets-stv1-platform"></a>Azure PowerShell cmdlet을 사용 하 여 연결 사용 ( `stv1` 플랫폼)

VNET에서 API Management 인스턴스를 [만들거나](/powershell/module/az.apimanagement/new-azapimanagement) [업데이트](/powershell/module/az.apimanagement/update-azapimanagementregion) 합니다.

## <a name="dns-configuration"></a>DNS 구성

외부 VNET 모드에서 Azure는 DNS를 관리합니다. 내부 VNET 모드의 경우, 자체 DNS를 관리해야 합니다. 다음이 권장됩니다.
1. Azure [DNS 개인 영역](../dns/private-dns-overview.md)을 구성 합니다.
1. Azure DNS 개인 영역을 API Management 서비스를 배포한 VNET에 연결 합니다. 

[Azure DNS에서 프라이빗 영역을 설정](../dns/private-dns-getstarted-portal.md)하는 방법을 알아봅니다.

> [!NOTE]
> API Management 서비스는 해당 IP 주소에 대 한 요청을 수신 하지 않습니다. 해당 서비스 엔드포인트에 구성된 호스트 이름에 대한 요청에만 응답합니다. 이 엔드포인트에는 다음이 필요합니다.
> * API 게이트웨이
> * Azure Portal
> * 개발자 포털
> * 엔드포인트 직접 관리
> * Git

### <a name="access-on-default-host-names"></a>기본 호스트 이름에 대한 액세스
예를 들어 `contosointernalvnet`이라는 API Management 서비스를 만들면 기본적으로 다음 서비스 엔드포인트가 구성됩니다.

| 엔드포인트 | 엔드포인트 구성 |
| ----- | ----- |
| API Gateway | `contosointernalvnet.azure-api.net` |
| 개발자 포털 | `contosointernalvnet.portal.azure-api.net` |
| 새 개발자 포털 | `contosointernalvnet.developer.azure-api.net` |
| 엔드포인트 직접 관리 | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

이러한 API Management 서비스 엔드포인트에 액세스하려면 API Management를 배포한VNET에 연결된 서브넷에 가상 머신을 생성할 수 있습니다. 서비스의 내부 가상 IP 주소가 10.1.0.5 인 것으로 가정 하면 다음과 같이 호스트 파일을 매핑할 수 있습니다. Windows에서이 파일은에 `%SystemDrive%\drivers\etc\hosts` 있습니다. 

| 내부 가상 IP 주소 | 엔드포인트 구성 |
| ----- | ----- |
| 10.1.0.5 | `contosointernalvnet.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.portal.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.developer.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.management.azure-api.net` |
| 10.1.0.5 | `contosointernalvnet.scm.azure-api.net` |

그런 다음 만든 가상 머신에서 모든 서비스 엔드포인트에 액세스할 수 있습니다.

VNET에서 사용자 지정 DNS 서버를 사용하는 경우, DNS 레코드를 생성하고 VNET의 어느 곳에서나 이러한 엔드포인트에 액세스할 수 있습니다.

### <a name="access-on-custom-domain-names"></a>사용자 지정 도메인 이름에 대한 액세스

기본 호스트 이름으로 API Management 서비스에 액세스하지 않으려면, 

1. 다음 이미지와 같이 모든 서비스 엔드포인트에 대한 [사용자 지정 도메인 이름을](configure-custom-domain.md) 설정합니다.

    :::image type="content" source="media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png" alt-text="사용자 지정 도메인 이름 설정":::

2. DNS 서버에 레코드를 생성하여 VNET 내에서 액세스할 수 있는 엔드포인트에 액세스합니다.

## <a name="routing"></a>라우팅

다음 가상 IP 주소는 내부 가상 네트워크의 API Management 인스턴스에 대해 구성됩니다. API Management IP [주소에](api-management-howto-ip-addresses.md)대해 자세히 알아봅니다.

| 가상 IP 주소 | 설명 |
| ----- | ----- |
| **개인 가상 IP 주소** | API 게이트웨이, 개발자 포털, 관리 및 Git 엔드포인트에 액세스할 수 있는 API Management 인스턴스의 DIP(서브넷 범위) 내에서 부하가 분산된 IP 주소입니다.<br/><br/>VNET에서 사용하는 DNS 서버에 이 주소를 등록합니다.     |  
| **공용 가상 IP 주소** | 포트 3443을 통해 관리 엔드포인트에 대한 제어 평면 *트래픽에만* 사용됩니다. [ApiManagement][ServiceTags] 서비스 태그로 잠글 수 있습니다. | 

부하가 분산된 공용 및 개인 IP 주소는 Azure Portal **개요** 블레이드에서 찾을 수 있습니다.

> [!NOTE]
> API Management 인스턴스의 VIP 주소는 다음과 같은 경우에 변경됩니다.
> * VNET이 사용하거나 사용하지 않도록 설정됩니다. 
> * API Management **외부** 가상 네트워크 모드에서 **내부** 가상 네트워크 모드로 또는 그 반대로 이동합니다.
> * [영역 중복](zone-redundancy.md) 설정은 인스턴스의 위치에서 활성화, 업데이트 또는 비활성화됩니다(SKU에만 Premium).
>
> VNET 내에서 DNS 등록, 라우팅 규칙 및 IP 제한 목록을 업데이트해야 할 수 있습니다.

### <a name="vip-and-dip-addresses"></a>VIP 및 DIP 주소

DIP 주소는 서비스의 각 기본 가상 머신에 할당되고 VNET *내의* 리소스에 액세스하는 데 사용됩니다. VIP 주소는 VNET *외부* 의 리소스에 액세스하는 데 사용됩니다. IP 제한 목록이 VNET 내의 리소스를 보호하는 경우, 서비스에서 액세스 권한을 부여하거나 제한하도록 API Management 서비스가 배포된 서브넷의 전체 범위를 지정해야 합니다.

[권장 서브넷 크기](virtual-network-concepts.md#subnet-size)에 대해 자세히 알아봅니다.

#### <a name="example"></a>예제

내부 VNET의 Premium 계층에 1개의 API Management [용량 단위를](api-management-capacity.md) 배포하는 경우 3개의 IP 주소가 사용됩니다. 프라이빗 VIP의 경우 1개, VM 2개에 대한 DIP에 각각 하나씩 사용됩니다. 4단위로 확장하는 경우 서브넷의 추가 DIP에 더 많은 IP가 소비됩니다.  

대상 엔드포인트에 고정된 DIP 집합만 허용 목록에 있는 경우 나중에 새 단위를 추가하면 연결 오류가 발생합니다. 이러한 이유로 서브넷은 전적으로 제어에 있으므로 백 엔드에서 전체 서브넷을 허용 나열하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

* [VNET에서 Azure API Management 설정하는 경우 네트워크 구성][Common network configuration problems]
* [VNET FAQ](../virtual-network/virtual-networks-faq.md)
* [DNS에서 레코드 만들기](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags

