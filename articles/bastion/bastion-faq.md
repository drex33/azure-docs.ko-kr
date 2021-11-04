---
title: Azure Bastion FAQ | Microsoft Docs
description: Azure Bastion 관련 질문과 대답에 대해 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: cherylmc
ms.openlocfilehash: 251d1a034bafa3a9d77291be63fdc59145b2cc09
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131575810"
---
# <a name="azure-bastion-faq"></a>Azure Bastion FAQ

## <a name="faqs"></a>FAQ

### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Azure Bastion을 통해 연결하려면 내 가상 머신에 공용 IP가 필요한가요?

아니요. Azure Bastion을 사용하여 VM에 연결하는 경우 연결하려는 Azure 가상 머신에는 공용 IP가 필요하지 않습니다. Bastion 서비스는 가상 네트워크 내부에서 가상 머신의 개인 IP를 통해 가상 머신에 대한 RDP/SSH 세션/연결을 설정합니다.

### <a name="is-ipv6-supported"></a>IPv6이 지원되나요?

현재 IPv6은 지원되지 않습니다. Azure Bastion은 IPv4만 지원합니다. 즉, Bastion 리소스에 IPv4 공용 IP 주소만 할당할 수 있으며 Bastion을 사용하여 IPv4 대상 VM에 연결할 수 있습니다. Bastion을 사용하여 이중 스택 대상 VM에 연결할 수도 있지만 Azure Bastion 통해서만 IPv4 트래픽을 보내고 받을 수 있습니다. 

### <a name="can-i-use-azure-bastion-with-azure-private-dns-zones"></a>Azure Private DNS 영역에서 Azure Bastion을 사용할 수 있나요?

Azure Bastion은 특정 내부 엔드포인트와 통신하여 대상 리소스에 성공적으로 연결할 수 있어야 합니다. 따라서 선택한 영역 이름이 이러한 내부 엔드포인트의 이름과 겹치지 않는 한 Azure 프라이빗 DNS 영역에서 Azure Bastion을 사용 *할 수 있습니다*. Azure Bastion 리소스를 배포하기 전에 호스트 가상 네트워크가 다음 이름의 프라이빗 DNS 영역에 연결되어 있지 않은지 확인하세요.
* core.windows.net
* azure.com

프라이빗 엔드포인트 통합 Azure 프라이빗 DNS 영역을 사용하는 경우 여러 Azure 서비스에 [권장되는 DNS 영역 이름](../private-link/private-endpoint-dns.md#azure-services-dns-zone-configuration)이 위에 나열된 이름과 겹칩니다. Azure Bastion 사용은 이러한 설정으로 지원되지 *않습니다*.

또한 Azure Bastion 사용은 국가별 클라우드의 Azure 프라이빗 DNS 영역에서 지원되지 않습니다.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>RDP 또는 SSH 클라이언트가 필요하나요?

아니요. Azure Portal에서 Azure 가상 머신에 RDP/SSH로 액세스할 때 RDP 또는 SSH 클라이언트가 필요하지 않습니다. [Azure Portal](https://portal.azure.com)을 사용하여 브라우저에서 직접 가상 머신에 대한RDP/SSH 액세스를 가져올 수 있습니다.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Azure 가상 머신에서 실행되는 에이전트가 필요하나요?

아니요. 브라우저 또는 Azure 가상 머신에 에이전트나 소프트웨어를 설치할 필요가 없습니다. Bastion 서비스는 에이전트 없이 사용할 수 있으며 RDP/SSH용 추가 소프트웨어가 필요하지 않습니다.

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>RDP 세션에서는 어떤 기능이 지원되나요?

지금은 텍스트 복사/붙여넣기만 지원됩니다. 파일 복사와 같은 기능은 지원되지 않습니다. [Azure Bastion 피드백 페이지](https://feedback.azure.com/d365community/forum/8ae9bf04-8326-ec11-b6e6-000d3a4f0789?c=c109f019-8326-ec11-b6e6-000d3a4f0789)에서 새 기능에 대한 피드백을 공유해 주시기 바랍니다.

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Bastion 보안 강화는 AADJ VM 확장 조인 VM에서 작동하나요?

이 기능은 Azure AD 사용자를 사용하는 AADJ VM 확장 조인 머신에서 작동하지 않습니다. 자세한 내용은 [Windows Azure VM 및 Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements)를 참조하세요.

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>어떤 브라우저가 지원되나요?

브라우저는 HTML 5를 지원해야 합니다. Windows에서 Microsoft Edge 브라우저 또는 Chrome을 사용합니다. Apple Mac의 경우 Google Chrome 브라우저를 사용하세요. Microsoft Edge Chromium도 Windows와 Mac에서 모두 지원됩니다.

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>가격 책정이란?

자세한 내용은 [가격 책정 페이지](https://aka.ms/BastionHostPricing)를 참조하세요.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Azure Bastion은 고객 데이터를 어디에 저장하나요?

Azure Bastion은 배포된 지역 외부로 고객 데이터를 이동하거나 저장하지 않습니다.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>가상 머신에 액세스할 때 꼭 필요한 역할이 있나요?

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할
* Virtual Network에 대한 리더 역할(피어링된 Virtual Network가 없는 경우 필요하지 않음).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Azure Bastion이 Azure 호스팅 VM에 관리 용도로 액세스하는 경우 RDS CAL이 필요한가요?

아니요. Azure Bastion이 관리 용도로만 Windows Server VM에 액세스하는 경우에는 [RDS CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab)이 필요하지 않습니다.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Bastion 원격 세션 중에 지원되는 키보드 레이아웃은?

Azure Bastion 현재 VM 내에서 다음과 같은 키보드 레이아웃을 지원합니다.
* en-us-qwerty
* en-gb-qwerty
* de-ch-qwertz
* de-de-qwertz
* fr-be-azerty
* fr-fr-azerty
* fr-ch-qwertz
* hu-hu-qwertz
* it-it-qwerty
* ja-jp-qwerty
* pt-br-qwerty
* es-es-qwerty
* es-latam-qwerty
* sv-se-qwerty
* tr-tr-qwerty

대상 언어에 대한 올바른 키 매핑을 위해 컴퓨터의 로컬 언어 또는 VM 내의 원격 언어를 en-us-qwerty로 설정해야 합니다. 즉, 원격 VM 언어가 대상 언어로 설정된 동안 로컬 컴퓨터 언어를 영어(미국)로 설정하거나 그 반대로 설정해야 합니다. 컴퓨터 설정에서 컴퓨터에 영어(미국) 언어를 추가할 수 있습니다.

### <a name="does-azure-bastion-support-timezone-configuration-or-timezone-redirection-for-target-vms"></a><a name="timezone"></a>Azure Bastion은 대상 VM에 대한 표준 시간대 구성 또는 표준 시간대 리디렉션을 지원하나요?

Azure Bastion은 현재 표준 시간대 리디렉션을 지원하지 않으며 표준 시간대를 구성할 수 없습니다.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Azure Bastion 서브넷에서 UDR(사용자 정의 라우팅)이 지원됩니까?

아니요. Azure Bastion 서브넷에서 UDR은 지원되지 않습니다.

동일한 가상 네트워크에서 Azure Bastion과 Azure Firewall/NVA(네트워크 가상 어플라이언스)를 모두 포함하는 시나리오의 경우, Azure Bastion과 VM 간의 통신이 비공개이기 때문에 Azure Bastion 서브넷에서 Azure Firewall으로 트래픽을 강제로 전송할 필요가 없습니다. 자세한 내용은 [Bassion으로 Azure Firewall 뒤에 있는 VM에 액세스](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)를 참조하세요.

### <a name="can-i-upgrade-from-a-basic-sku-to-a-standard-sku"></a><a name="upgradesku"></a> 기본 SKU에서 표준 SKU로 업그레이드할 수 있나요?

예. 단계는 [SKU 업그레이드](upgrade-sku.md)를 참조하세요. SKU에 대한 자세한 내용은 [구성 설정](configuration-settings.md#skus) 문서를 참조하세요.

### <a name="can-i-downgrade-from-a-standard-sku-to-a-basic-sku"></a><a name="downgradesku"></a> 표준 SKU에서 기본 SKU로 다운그레이드할 수 있나요?

아니요. 표준 SKU에서 기본 SKU로의 다운그레이드는 지원되지 않습니다. SKU에 대한 자세한 내용은 [구성 설정](configuration-settings.md#skus) 문서를 참조하세요.

### <a name="can-i-deploy-multiple-azure-resources-in-my-azure-bastion-subnet"></a><a name="subnet"></a> Azure Bastion 서브넷에 여러 Azure 리소스를 배포할 수 있나요?

아니요. Azure Bastion 서브넷(*AzureBastionSubnet*)은 Azure Bastion 리소스의 배포용으로만 예약되어 있습니다.

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>왜 Bastion 세션이 시작하기 전에 "세션이 만료되었습니다." 오류 메시지를 받나요?

세션은 Azure Portal에서만 시작되어야 합니다. Azure Portal에 로그인하고 세션을 다시 시작합니다. 다른 브라우저 세션이나 탭에서 URL로 직접 이동하는 경우 이 오류가 예상됩니다. 세션이 더 안전한지 Azure Portal을 통해서만 세션에 액세스할 수 있는지 확인하도록 돕습니다.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>배포 실패를 처리하는 방법은?

오류 메시지를 검토하고 필요에 따라 [Azure Portal에서 지원 요청을 제기합니다](../azure-portal/supportability/how-to-create-azure-support-request.md). [Azure 구독 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)에서 배포 오류가 발생할 수 있습니다. 특히, 고객은 구독당 허용되는 공용 IP 주소 수 제한으로 인해 Azure Bastion 배포가 실패할 수 있습니다.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>재해 복구 계획에 Azure Bastion을 통합하려면 어떻게 할까요?

Azure Bastion은 VNet 또는 피어링된 VNet 내부에 배포되고 Azure 지역에 연결됩니다. Azure Bastion을 DR(재해 복구) 사이트 VNet에 배포하는 것은 고객의 책임입니다. Azure 지역에 장애가 발생하는 경우 VM을 DR 지역으로 장애 조치(failover)하세요. 그런 다음, DR 지역에 배포된 Azure Bastion 호스트를 사용하여 현재 여기에 배포된 VM에 연결합니다.

## <a name="vnet-peering"></a><a name="peering"></a>VNet 피어링

### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>피어링된 가상 네트워크 간에 여러 Bastion 호스트를 배포할 수 있나요?

예. 기본적으로 사용자는 VM이 상주하는 동일한 가상 네트워크에 배포된 Bastion 호스트를 볼 수 있습니다. 그러나 **연결** 메뉴에서 사용자는 피어링된 네트워크에서 검색된 여러 Bastion 호스트를 볼 수 있습니다. 가상 네트워크에 배포된 VM에 연결하는 데 사용하고자 하는 Bastion 호스트를 선택할 수 있습니다.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>피어링된 VNet이 다른 구독에 배포되는 경우 Bastion을 통한 연결이 작동하나요?

예, Bastion을 통한 연결은 단일 테넌트에 대한 다른 구독에서 피어링된 VNet에 대해 계속 작동합니다. 서로 다른 두 테넌트 간의 구독은 지원되지 않습니다. **연결** 드롭다운 메뉴에서 Bastion을 보려면 사용자는 **구독 > 전역 구독** 에서 액세스할 수 있는 하위 항목을 선택해야 합니다.

:::image type="content" source="./media/bastion-faq/global-subscriptions.png" alt-text="전역 구독 필터" lightbox="./media/bastion-faq/global-subscriptions.png":::

### <a name="does-bastion-support-connectivity-to-azure-virtual-desktop"></a>Bastion은 Azure Virtual Desktop에 대한 연결을 지원하나요?
아니요, Azure Virtual Desktop에 대한 Bastion 연결은 지원되지 않습니다.

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>피어링된 VNet에 액세스할 수 있지만 여기에 배포된 VM은 볼 수 없습니다.

사용자에게 VM 및 피어링된 VNet 모두에 대한 **읽기** 액세스 권한이 있는지 확인합니다. 또한 사용자에게 다음 리소스에 대한 **읽기** 액세스 권한이 있는지 IAM에서 확인합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할
* Virtual Network에 대한 리더 역할(피어링된 Virtual Network가 없는 경우 필요하지 않음).

|사용 권한|설명|사용 권한 유형|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |요새 호스트 가져오기|작업|
|Microsoft.Network/virtualNetworks/BastionHosts/action |Virtual Network의 요새 호스트 참조를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|Virtual Network의 요새 호스트 참조를 가져옵니다.|작업|
|Microsoft.Network/networkInterfaces/read|네트워크 인터페이스 정의를 가져옵니다.|작업|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|네트워크 인터페이스 IP 구성 정의를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/read|가상 네트워크 정의를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|가상 네트워크 서브넷의 모든 가상 머신에 대한 참조를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/virtualMachines/read|가상 네트워크의 모든 가상 머신에 대한 참조를 가져옵니다.|작업|
