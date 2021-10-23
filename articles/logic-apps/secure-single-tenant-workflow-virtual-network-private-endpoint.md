---
title: 단일 테넌트 워크플로와 가상 네트워크 간의 트래픽 보호
description: Azure Logic Apps에서 가상 네트워크, 스토리지 계정 및 단일 테넌트 워크플로 사이의 트래픽을 보호합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 500ec9c14dea994b528528389ed03d2b561c1a56
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241823"
---
# <a name="secure-traffic-between-virtual-networks-and-single-tenant-workflows-in-azure-logic-apps-using-private-endpoints"></a>프라이빗 엔드포인트를 사용하여 Azure Logic Apps에서 가상 네트워크와 단일 테넌트 워크플로 간의 트래픽 보호

논리 앱 워크플로와 가상 네트워크 사이에서 안전하고 비공개로 통신하려면 인바운드 트래픽에 대한 프라이빗 엔드포인트를 설정하고 아웃바운드 트래픽에 가상 네트워크 통합을 사용할 수 있습니다.

프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 서비스는 Azure Logic Apps, Azure Storage, Azure Cosmos DB, SQL 또는 자체 Private Link 서비스와 같은 Azure 서비스일 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크의 개인 IP 주소를 사용하여 효과적으로 가상 네트워크에 서비스를 제공합니다.

이 문서에서는 인바운드 트래픽, 아웃바운드 트래픽 및 스토리지 계정에 대한 연결에 대한 프라이빗 엔드포인트를 통해 액세스를 설정하는 방법을 보여 줍니다.

자세한 내용은 다음 설명서를 검토하세요.

- [Azure 프라이빗 엔드포인트란?](../private-link/private-endpoint-overview.md)

- [Azure Private Link란?](../private-link/private-link-overview.md)

- [Azure Logic Apps의 단일 테넌트 논리 앱 워크플로란?](single-tenant-overview-compare.md)

## <a name="prerequisites"></a>필수 구성 요소

위임 없이 서브넷을 포함하는 신규 또는 기존 Azure 가상 네트워크가 있어야 합니다. 서브넷은 가상 네트워크에서 개인 IP 주소를 배포 및 할당하는 데 사용됩니다.

자세한 내용은 다음 설명서를 검토하세요.

- [빠른 시작: Azure 포털을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md)

- [서브넷 위임이란?](../virtual-network/subnet-delegation-overview.md)

- [서브넷 위임 추가 또는 제거](../virtual-network/manage-subnet-delegation.md)

<a name="set-up-inbound"></a>

## <a name="set-up-inbound-traffic-through-private-endpoints"></a>프라이빗 엔드포인트를 통해 인바운드 트래픽 설정

워크플로에 대한 인바운드 트래픽을 보호하려면 다음과 같은 상위 수준 단계를 완료합니다.

1. 요청 트리거 또는 HTTP + 웹후크 트리거와 같은 인바운드 요청을 수신하고 처리할 수 있는 기본 제공 트리거를 통해 워크플로를 시작합니다. 이 트리거는 호출 가능한 엔드포인트를 통해 워크플로를 설정합니다.

1. 가상 네트워크에 프라이빗 엔드포인트를 추가합니다.

1. 테스트 호출을 통해 엔드포인트에 대한 액세스를 확인합니다. 이 엔드포인트를 설정한 후 논리 앱 워크플로를 호출하려면 가상 네트워크에 연결되어 있어야 합니다.

### <a name="prerequisites-for-inbound-traffic-through-private-endpoints"></a>프라이빗 엔드포인트를 통한 인바운드 트래픽에 대한 사전 요구 사항

[최상위 사전 요구 사항에서의 가상 네트워크 설정](#prerequisites) 외에도 요청을 수신할 수 있는 기본 제공 트리거로 시작되는 신규 또는 기존 단일 테넌트 기반 논리 앱 워크플로가 있어야 합니다.

예를 들어 요청 트리거는 워크플로를 포함하여 다른 호출자의 인바운드 요청을 수신하고 처리할 수 있는 엔드포인트를 워크플로에 만듭니다. 이 엔드포인트는 워크플로를 호출하고 트리거하는 데 사용할 수 있는 URL을 제공합니다. 이 예제에서는 요청 트리거를 계속 진행합니다.

자세한 내용은 다음 설명서를 검토하세요.

- [Azure Logic Apps에서 단일 테넌트 논리 앱 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)

- [Azure Logic Apps를 사용하여 인바운드 HTTPS 요청 수신 및 응답](../connectors/connectors-native-reqres.md)

### <a name="create-the-workflow"></a>워크플로 만들기

1. 아직 없는 경우 단일 테넌트 기반 논리 앱과 빈 워크플로를 만듭니다.

1. 디자이너가 열리면 워크플로에서 첫 번째 단계로 요청 트리거를 추가합니다.

   > [!NOTE]
   > 가상 네트워크 내부에서만 요청 트리거와 웹후크 트리거를 호출할 수 있습니다. 관리 API 웹후크 트리거 및 작업은 호출을 수신하기 위해 퍼블릭 엔드포인트가 필요하므로 작동하지 않습니다. 

1. 시나리오 요구 사항에 따라 워크플로에서 실행하려는 다른 작업을 추가합니다.

1. 완료되면 워크플로를 저장합니다.

자세한 내용은 [Azure Logic Apps에서 단일 테넌트 논리 앱 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)를 검토하세요.

#### <a name="copy-the-endpoint-url"></a>엔드포인트 URL 복사

1. 워크플로 메뉴에서 **개요** 를 선택합니다.

1. **개요** 페이지에서 나중에 사용하도록 **워크플로 URL** 을 복사하고 저장합니다.

   워크플로를 트리거하려면 이 URL을 호출하거나 요청을 보냅니다.

1. URL을 호출하거나 요청을 보내 URL이 작동하는지 확인합니다. 도구(예: Postman)를 사용하여 요청을 보낼 수 있습니다.

### <a name="set-up-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 설정

1. 논리 앱 메뉴의 **설정** 에서 **네트워킹** 을 선택합니다.

1. **네트워킹** 페이지의 **프라이빗 엔드포인트 연결** 에서 **프라이빗 엔드포인트 연결 구성** 을 선택합니다.

1. **프라이빗 엔드포인트 연결 페이지** 에서 **추가** 를 선택합니다.

1. **프라이빗 엔드포인트 추가** 창이 열리면 엔드포인트에 대해 요청된 정보를 제공합니다.

   자세한 내용은 [프라이빗 엔드포인트 속성](../private-link/private-endpoint-overview.md#private-endpoint-properties)을 검토하세요.

1. Azure가 프라이빗 엔드포인트를 성공적으로 프로비전한 후에는 워크플로 URL 호출을 다시 시도합니다.

   이번에는 예상대로 `403 Forbidden` 오류가 발생합니다. 프라이빗 엔드포인트가 설정되어 제대로 작동함을 의미합니다.

1. 연결이 제대로 작동하는지 확인하려면 프라이빗 엔드포인트가 있는 동일한 가상 네트워크에 가상 머신을 만들고 논리 앱 워크플로 호출을 시도합니다.

### <a name="considerations-for-inbound-traffic-through-private-endpoints"></a>프라이빗 엔드포인트를 통한 인바운드 트래픽에 대한 고려 사항

- 가상 네트워크 외부에서 액세스하는 경우 모니터링 보기는 트리거와 작업의 입력 및 출력에 액세스할 수 없습니다.

- Visual Studio Code 또는 Azure CLI로부터의 배포는 가상 네트워크 내부에서만 작동합니다. 배포 센터를 사용하여 논리 앱을 GitHub 리포지토리에 연결할 수 있습니다. 이후 Azure 인프라를 사용하여 코드를 빌드하고 배포할 수 있습니다.

  GitHub 통합이 작동하려면 논리 앱에서 `WEBSITE_RUN_FROM_PACKAGE` 설정을 제거하거나 값을 `0`으로 설정합니다.

- Private Link를 사용하도록 설정해도 App Service 인프라를 통과하는 아웃바운드 트래픽에는 영향을 미치지 않습니다.

<a name="set-up-outbound"></a>

## <a name="set-up-outbound-traffic-through-private-endpoints"></a>프라이빗 엔드포인트를 통해 아웃바운드 트래픽 설정

논리 앱에서 아웃바운드 트래픽을 보호하려면 논리 앱을 가상 네트워크와 통합할 수 있습니다. 기본적으로 논리 앱의 아웃바운드 트래픽은 `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`과 같은 프라이빗 주소로 이동할 때 네트워크 보안 그룹(NSG) 및 사용자 정의 경로(UDR)의 영향을 받습니다.

사용자 고유의 DNS (도메인 이름 서버)를 가상 네트워크와 함께 사용 하는 경우 논리 앱 리소스의 `WEBSITE_DNS_SERVER` 앱 설정을 dns에 대 한 IP 주소로 설정 합니다. 보조 DNS가 있는 경우 라는 다른 앱 설정을 추가 `WEBSITE_DNS_ALT_SERVER` 하 고 DNS의 IP에도 값을 설정 합니다. 또한 내부 IP 주소에서 개인 끝점을 가리키도록 DNS 레코드를 업데이트 합니다. 전용 끝점은 특정 리소스에 대 한 공용 주소가 아니라 개인 주소로 DNS 조회를 전송 하 여 작동 합니다. 자세한 내용은 [개인 끝점 검토-Azure virtual network와 앱 통합](../app-service/overview-vnet-integration.md#private-endpoints)을 참조 하세요.

> [!IMPORTANT]
> Azure Logic Apps 런타임이 작동 하려면 백 엔드 저장소에 지속적으로 연결 해야 합니다. Azure 호스팅 관리 커넥터가 작동하려면 관리 API 서비스에 중단 없이 연결해야 합니다.

### <a name="considerations-for-outbound-traffic-through-private-endpoints"></a>프라이빗 엔드포인트를 통한 아웃바운드 트래픽에 대한 고려 사항

가상 네트워크 통합 설정은 아웃 바운드 트래픽에만 영향을 줍니다. App Service 공유 끝점을 계속 사용 하는 인바운드 트래픽을 보호 하려면 [개인 끝점을 통해 인바운드 트래픽 설정](#set-up-inbound)을 검토 합니다.

자세한 내용은 다음 설명서를 검토하세요.

- [Azure 가상 네트워크에 앱 통합](../app-service/overview-vnet-integration.md)

- [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)

- [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)

## <a name="connect-to-storage-account-with-private-endpoints"></a>프라이빗 엔드포인트를 사용하여 스토리지 계정에 연결

가상 네트워크 내의 리소스만 연결할 수 있도록 스토리지 계정 액세스를 제한할 수 있습니다. Azure Storage는 스토리지 계정에 프라이빗 엔드포인트를 추가하는 것을 지원합니다. 그러면 논리 앱 워크플로에서 이러한 끝점을 사용 하 여 저장소 계정과 통신할 수 있습니다. 자세한 내용은 [Azure Storage 전용 끝점 사용](../storage/common/storage-private-endpoints.md)을 참조 하세요.

> [!NOTE]
> 다음 단계를 수행 하려면 저장소 계정에 대 한 공용 액세스를 일시적으로 설정 해야 합니다. 조직의 정책으로 인해 공용 액세스를 사용 하도록 설정할 수 없는 경우에도 개인 저장소 계정을 사용 하 여 논리 앱을 배포할 수 있습니다. 그러나 배포를 위해 Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 해야 합니다. ARM 템플릿 예제를 보려면 [개인 끝점을 사용 하 여 보안 저장소 계정을 사용 하 여 논리 앱 배포](https://github.com/VeeraMS/LogicApp-deployment-with-Secure-Storage)를 검토 하세요.

1. 각 테이블, 큐, Blob 및 파일 스토리지 서비스에 대해 서로 다른 프라이빗 엔드포인트를 만듭니다.

1. 논리 앱을 배포할 때 저장소 계정에서 임시 공용 액세스를 사용 하도록 설정 합니다.

   1. [Azure Portal](https://portal.azure.com)에서 저장소 계정 리소스를 엽니다.

   1. 저장소 계정 리소스 메뉴의 **보안 + 네트워킹** 에서 **네트워킹** 을 선택 합니다.

   1. **네트워킹** 창의 **방화벽 및 가상 네트워크** 탭에 있는 **액세스 허용에서** **모든 네트워크** 를 선택 합니다.

1. Azure Portal 또는 Visual Studio Code를 사용 하 여 논리 앱 리소스를 배포 합니다.

1. 배포가 완료 되 면 논리 앱과 저장소 계정에 연결 되는 가상 네트워크 또는 서브넷의 개인 끝점 간의 통합을 사용 하도록 설정 합니다.

   1. [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스를 엽니다.

   1. 논리 앱 리소스 메뉴의 **설정** 아래에서 **네트워킹** 을 선택 합니다.

   1. 논리 앱과 개인 끝점의 IP 주소 간에 필요한 연결을 설정 합니다.

   1. 가상 네트워크를 통해 논리 앱 워크플로 데이터에 액세스하려면 논리 앱 리소스 설정에서 `WEBSITE_CONTENTOVERVNET` 설정을 로 `1` 설정합니다.

   가상 네트워크에서 고유한 DNS(도메인 이름 서버)를 사용하는 경우 논리 앱 리소스의 `WEBSITE_DNS_SERVER` 앱 설정을 DNS의 IP 주소로 설정합니다. 보조 DNS가 있는 경우 라는 다른 앱 설정을 `WEBSITE_DNS_ALT_SERVER` 추가하고 값을 DNS의 IP로 설정합니다. 또한 내부 IP 주소에서 프라이빗 엔드포인트를 가리키도록 DNS 레코드를 업데이트합니다. 프라이빗 엔드포인트는 특정 리소스에 대한 공용 주소가 아닌 개인 주소에 DNS 조회를 전송하여 작동합니다. 자세한 내용은 [프라이빗 엔드포인트 - Azure 가상 네트워크와 앱 통합을 검토하세요.](../app-service/overview-vnet-integration.md#private-endpoints)

1. 이러한 앱 설정을 적용한 후 스토리지 계정에서 공용 액세스를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [어디서나 Logic Apps 사용: Logic Apps(단일 테넌트)를 사용한 네트워킹 가능성](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)