---
title: Azure API Management를 사용하여 내부 가상 네트워크에 연결
titleSuffix: Azure API Management
description: 내부 가상 네트워크에서 Azure API Management를 설정하고 구성하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 06/08/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 84d290558d781b592a114b3f3f5658d1ceebe115
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221225"
---
# <a name="connect-to-an-internal-virtual-network-using-azure-api-management"></a>Azure API Management를 사용하여 내부 가상 네트워크에 연결 
Azure Virtual Networks(VNET)를 사용하면 Azure API Management가 여러 VPN 기술을 사용하여 인터넷에 액세스할 수 없는 API를 관리할 수 있습니다. [외부](./api-management-using-with-vnet.md) 또는 내부 모드를 통해 API Management를 배포할 수 있습니다. 이 문서에서는 내부 VNET 모드에서 API Management를 배포하는 방법을 알아봅니다.

API Management는 내부 VNET 모드로 배포되는 경우, 액세스 권한을 제어하는 VNET 내에서만 다음 서비스 엔드포인트를 볼 수 있습니다.
* 프록시 게이트웨이
* 개발자 포털
* 직접 관리
* Git 

> [!NOTE]
> 서비스 엔드포인트는 공용 DNS에 등록되지 않습니다. VNET에 대한 [DNS를 구성](#apim-dns-configuration)할 때까지 서비스 엔드포인트에 액세스할 수 없습니다.

내부 모드에서 API Management를 사용하여 다음을 수행합니다.

* Azure VPN 연결 또는 Azure ExpressRoute를 사용하여 타사의 외부에서 프라이빗 데이터 센터에 호스팅된 API에 안전하게 액세스할 수 있게 합니다.
* 공통 게이트웨이를 통해 클라우드 기반 API와 온-프레미스 API를 공개함으로써 하이브리드 클라우드 시나리오를 사용하도록 설정합니다.
* 단일 게이트웨이 엔드포인트를 사용하여 여러 지리적 위치에서 호스팅되는 API를 관리합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>필수 조건

+ **활성 Azure 구독** [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Azure API Management 인스턴스(지원되는 S SKU: Developer, Premium, Isolated)** . 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

API Management 서비스가 VNET에 배포된 경우, [포트 목록](./api-management-using-with-vnet.md#required-ports)이 사용되며 포트 목록을 열어야 합니다.

## <a name="creating-an-api-management-in-an-internal-vnet"></a><a name="enable-vpn"></a>내부 VNET에서 API Management 생성하기
내부 가상 네트워크의 API Management 서비스는 내부 부하 분산 장치 뒤에 호스팅됩니다. 부하 분산 장치 SKU는 서비스를 생성하는 데 사용되는 관리 API에 따라 달라집니다. 자세한 내용은 [Azure Load Balancer SKU](../load-balancer/skus.md)를 참조하세요.

| API 버전 | 호스팅 |
| ----------- | ------------- |
| 2020-12-01 | 기본 SKU의 내부 부하 분산 |
| 2020-01-01-preview, 구독의 공용 IP 주소 사용 | 내부 부하 분산 장치 표준 SKU |

### <a name="enable-a-vnet-connection-using-the-azure-portal"></a>Azure Portal을 사용하여 VNET 연결 사용 설정

1. [Azure Portal](https://portal.azure.com/)에서 Azure API Management 인스턴스를 찾습니다.
1. **가상 네트워크** 를 선택합니다.
1. **내부** 액세스 형식을 구성합니다. 자세한 단계는 [Azure Portal을 사용하여 VNET 연결 사용](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal)을 참조하세요.

    ![내부 VNET에서 Azure API Management를 설정하는 메뉴][api-management-using-internal-vnet-menu]

4. **저장** 을 선택합니다.

배포에 성공하면, **개요** 블레이드에 API Management 서비스의 **개인** 가상 IP 주소와 **공용** 가상 IP 주소가 표시됩니다. 

| 가상 IP 주소 | 설명 |
| ----- | ----- |
| **개인 가상 IP 주소** | `gateway`, `portal`, `management`, `scm` 엔드포인트에 액세스할 수 있는 API Management 위임 서브넷 내에서 부하가 분산된 IP 주소입니다. |  
| **공용 가상 IP 주소** | `port 3443`을 통해 `management` 엔드포인트에 대한 컨트롤 플레인 트래픽에 사용됩니다. [ApiManagement][ServiceTags] 서비스 태그로 잠글 수 있습니다. 없음 및 외부 VNet 구성에서는 들어오는 런타임 API 트래픽에 사용됩니다. 또한 모든 VNet 구성에서 인터넷의 나가는 런타임 트래픽에 사용됩니다. |  

![내부 VNET이 구성된 API Management 대시보드][api-management-internal-vnet-dashboard]

> [!NOTE]
> Azure Portal에서 사용할 수 있는 테스트 콘솔의 경우, 해당 게이트웨이 URL이 공용 DNS에 등록되어 있지 않으므로 **내부** VNET 배포 서비스에서 작동하지 않습니다. 대신, **개발자 포털** 에 제공된 테스트 콘솔을 사용해야 합니다.

### <a name="deploy-api-management-into-vnet"></a><a name="deploy-apim-internal-vnet"></a>VNET에 API Management 배포

다음 방법을 사용하여 VNET 연결을 사용하도록 설정할 수도 있습니다.


### <a name="api-version-2020-12-01"></a>API 버전 2020-12-01

* Azure Resource Manager [템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-internal-vnet)

     [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell cmdlet - VNET에서 API Management 인스턴스를 [만들거나](/powershell/module/az.apimanagement/new-azapimanagement) [업데이트](/powershell/module/az.apimanagement/update-azapimanagementregion)합니다.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS 구성
외부 VNET 모드에서 Azure는 DNS를 관리합니다. 내부 VNET 모드의 경우, 자체 DNS를 관리해야 합니다. 다음이 권장됩니다.
1. Azure DNS 프라이빗 영역 구성.
1. Azure DNS 프라이빗 영역을 API Management 서비스를 배포한 VNET에 연결합니다. 

[Azure DNS에서 프라이빗 영역을 설정](../dns/private-dns-getstarted-portal.md)하는 방법을 알아봅니다.

> [!NOTE]
> API Management 서비스는 IP 주소에서 오는 요청을 수신 대기하지 않습니다. 해당 서비스 엔드포인트에 구성된 호스트 이름에 대한 요청에만 응답합니다. 이 엔드포인트에는 다음이 필요합니다.
> * 게이트웨이
> * Azure Portal
> * 개발자 포털
> * 엔드포인트 직접 관리
> * Git

### <a name="access-on-default-host-names"></a>기본 호스트 이름에 대한 액세스
예를 들어 `contosointernalvnet`이라는 API Management 서비스를 만들면 기본적으로 다음 서비스 엔드포인트가 구성됩니다.

| 엔드포인트 | 엔드포인트 구성 |
| ----- | ----- |
| 게이트웨이 또는 프록시 | `contosointernalvnet.azure-api.net` |
| 개발자 포털 | `contosointernalvnet.portal.azure-api.net` |
| 새 개발자 포털 | `contosointernalvnet.developer.azure-api.net` |
| 엔드포인트 직접 관리 | `contosointernalvnet.management.azure-api.net` |
| Git | `contosointernalvnet.scm.azure-api.net` |

이러한 API Management 서비스 엔드포인트에 액세스하려면 API Management를 배포한VNET에 연결된 서브넷에 가상 머신을 생성할 수 있습니다. 서비스의 내부 가상 IP 주소가 10.1.0.5라고 가정하면 다음과 같이 호스트 파일 `%SystemDrive%\drivers\etc\hosts`을 매핑할 수 있습니다.

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

1. 다음 이미지에 표시된 대로 모든 서비스 엔드포인트에 대해 사용자 지정 도메인 이름을 설정하면 됩니다.

   ![API Management를 위한 사용자 지정 도메인 설정][api-management-custom-domain-name]

2. DNS 서버에 레코드를 생성하여 VNET 내에서 액세스할 수 있는 엔드포인트에 액세스합니다.

## <a name="routing"></a><a name="routing"> </a> 라우팅

* 서브넷 범위(DIP)의 부하 분산된 *개인* 가상 IP 주소가 유보되며, VNet 내에서 API Management 서비스 엔드포인트에 액세스하는 데 사용됩니다. 
    * Azure Portal 서비스의 개요 블레이드에서 이 개인 IP 주소를 찾습니다. 
    * VNET에서 사용하는 DNS 서버에 이 주소를 등록합니다.
* 또한 부하 분산된 *공용* IP 주소(VIP)는 `port 3443`을 통해 관리 서비스 엔드포인트에 대한 액세스를 제공하도록 예약됩니다. 
    * Azure Portal의 서비스 개요 블레이드에서 이 공용 IP 주소를 찾습니다. 
    * `port 3443`를 통해 `management` 엔드포인트에 대한 제어 평면 트래픽용 *공용* IP 주소만 사용합니다. 
    * 이 IP 주소는 [ApiManagement][ServiceTags] 서비스 태그로 잠글 수 있습니다.
* DIP 주소는 서비스의 각 가상 머신에 할당되며, VNET *내의* 리소스에 액세스하는 데 사용됩니다. VIP 주소는 VNET *외부* 의 리소스에 액세스하는 데 사용됩니다. IP 제한 목록이 VNET 내의 리소스를 보호하는 경우, 서비스에서 액세스 권한을 부여하거나 제한하도록 API Management 서비스가 배포된 서브넷의 전체 범위를 지정해야 합니다.
* 부하 분산된 공용 및 개인 IP 주소는 Azure Portal의 개요 블레이드에서 확인할 수 있습니다.
* VNET에서 서비스를 제거하거나 추가하는 경우, 공개 및 개인 액세스용으로 할당된 IP 주소가 변경될 수 있습니다. VNET 내에서 DNS 등록, 라우팅 규칙 및 IP 제한 목록을 업데이트해야 할 수 있습니다.

## <a name="related-content"></a><a name="related-content"> </a>관련 콘텐츠
자세한 내용은 다음 문서를 참조하세요.
* [VNET에서 Azure API Management를 설정하는 동안 발생하는 공통 네트워크 구성 문제][Common network configuration problems]
* [VNET FAQ](../virtual-network/virtual-networks-faq.md)
* [DNS에서 레코드 만들기](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/updated-api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/updated-api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/updated-api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
