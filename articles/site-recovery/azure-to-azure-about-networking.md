---
title: Azure Site Recovery를 사용하는 Azure VM 재해 복구의 네트워킹 정보
description: Azure Site Recovery를 사용하는 Azure VM의 복제에 대한 네트워킹을 간략히 설명합니다.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: harshacs
ms.openlocfilehash: ae72ae66853a075673a2fcf51b4285e7e8676b86
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132901532"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Azure VM 재해 복구의 네트워킹 정보



이 아티클에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 지역 간에 Azure VM을 복제 및 복구할 때의 네트워킹 지침을 제공합니다.

## <a name="before-you-start"></a>시작하기 전에

Site Recovery가 [이 시나리오](azure-to-azure-architecture.md)에 재해 복구를 지원하는 방법을 알아봅니다.

## <a name="typical-network-infrastructure"></a>일반적인 네트워크 인프라

다음 다이어그램에서는 Azure VM에서 실행되는 애플리케이션에 대한 일반적인 Azure 환경을 보여 줍니다.

![Azure VM에서 실행되는 애플리케이션에 대한 일반적인 Azure 환경을 보여 주는 다이어그램입니다.](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

온-프레미스 네트워크와 Azure 간의 연결에 Azure ExpressRoute 또는 VPN 연결을 사용하는 경우 환경은 다음과 같습니다.

![고객 환경](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

일반적으로 네트워크는 방화벽 및 NSG(네트워크 보안 그룹)를 사용하여 보호됩니다. 서비스 태그는 네트워크 연결을 제어하는 데 사용해야 합니다. NSG를 통해 여러 서비스 태그에서 아웃바운드 연결을 제어할 수 있어야 합니다.

>[!IMPORTANT]
> 인증된 프록시를 사용한 네트워크 연결 제어는 Site Recovery에서 지원되지 않으며 복제를 사용할 수 없습니다.

>[!NOTE]
>- 아웃바운드 연결을 제어하기 위해 IP 주소 기반 필터링을 수행하면 안 됩니다.
>- 아웃바운드 연결을 제어하려면 Azure Site Recovery IP 주소가 Azure 라우팅 테이블에 추가되면 안 됩니다.

## <a name="outbound-connectivity-for-urls"></a>URL에 대한 아웃바운드 연결

URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 이러한 Site Recovery URL을 허용하세요.

**URL** | **세부 정보**
--- | ---
*.blob.core.windows.net | VM에서 원본 지역의 캐시 스토리지 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다. VM에 대한 모든 캐시 스토리지 계정을 알고 있는 경우 *.blob.core.windows.net 대신 특정 스토리지 계정 URL(예: cache1.blob.core.windows.net 및 cache2.blob.core.windows.net)에 대한 액세스를 허용할 수 있습니다.
login.microsoftonline.com | Site Recovery 서비스 URL에 대한 권한 부여 및 인증에 필요합니다.
\*.hypervrecoverymanager.windowsazure.com | VM에서 Site Recovery 서비스 통신이 발생할 수 있도록 하는 데 필요합니다.
\*.servicebus.windows.net | VM에서 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 하는 데 필요합니다.
*.vault.azure.net | 포털을 통해 ADE 지원 가상 머신에 대한 복제를 사용하도록 설정하는 액세스 허용
*.automation.ext.azure.com | 포털을 통해 복제된 항목의 모바일 에이전트의 자동 업그레이드 활성화 허용

## <a name="outbound-connectivity-using-service-tags"></a>서비스 태그를 사용하는 아웃바운드 연결

URL 제어 외에도 서비스 태그를 사용하여 연결을 제어할 수 있습니다. 이렇게 하려면 먼저 Azure에서 [네트워크 보안 그룹](../virtual-network/network-security-group-how-it-works.md)을 만들어야 합니다. 그런 다음, 기존 서비스 태그를 사용하여 Azure Site Recovery 서비스에 대한 액세스를 허용하는 NSG 규칙을 만들어야 합니다. 

IP 주소를 사용하여 연결을 제어하는 것과 비교할 때 서비스 태그를 사용하여 연결을 제어할 경우의 이점은 서비스에 연결된 상태를 유지하기 위해 특정 IP 주소에 강하게 종속되지 않는다는 것입니다. 이 시나리오에서는 서비스 중 하나의 IP 주소가 변경될 경우 진행 중인 복제가 머신에 영향을 미치지 않습니다. 반면 하드 코딩된 IP 주소에 대한 종속성으로 인해 복제 상태가 위험해지고 시스템이 위험에 노출됩니다. 또한 서비스 태그는 하드 코딩된 IP 주소보다 더 나은 보안, 안정성, 복원력을 보장합니다.

NSG를 사용하여 아웃바운드 연결을 제어하는 동안 이러한 서비스 태그를 허용해야 합니다.

- 원본 지역의 스토리지 계정:
    - 원본 지역에 대한 NSG 규칙을 기반으로 [스토리지 서비스 태그](../virtual-network/network-security-groups-overview.md#service-tags)를 만듭니다.
    - VM에서 캐시 스토리지 계정에 데이터를 쓸 수 있도록 이러한 주소를 허용합니다.
- AAD에 해당하는 모든 IP 주소에 대한 액세스를 허용하는 [AAD(Azure Active Directory) 서비스 태그](../virtual-network/network-security-groups-overview.md#service-tags) 기반 NSG 규칙을 만드세요.
- 대상 지역에 대한 EventsHub 서비스 태그 기반 NSG 규칙을 만들어 Site Recovery 모니터링에 대한 액세스를 허용합니다.
- 모든 지역에서 Site Recovery 서비스에 대한 액세스를 허용하기 위해 AzureSiteRecovery 서비스 태그 기반 NSG 규칙을 만듭니다.
- AzureKeyVault 서비스 태그 기반 NSG 규칙을 만듭니다. 이는 포털을 통해 ADE 지원 가상 머신의 복제를 활성화하는 경우에만 필요합니다.
- GuestAndHybridManagement 서비스 태그 기반 NSG 규칙을 만듭니다. 이는 포털을 통해 복제된 항목에 대한 모바일 에이전트의 자동 업그레이드를 사용하도록 설정하는 경우에만 필요합니다.
- 프로덕션 NSG에서 규칙을 만들기 전에 테스트 NSG에서 필요한 NSG 규칙을 만들고 문제가 없는지 확인하는 것이 좋습니다.

## <a name="example-nsg-configuration"></a>NSG 구성 예제

이 예제에서는 복제할 VM에 대한 NSG 규칙을 구성하는 방법을 보여 줍니다.

- NSG 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 필요한 모든 IP 주소 범위에 대한 포트:443에 "HTTPS 아웃바운드 허용" 규칙을 사용합니다.
- 이 예제에서는 VM 원본 위치가 "미국 동부"이고 대상 위치가 "미국 중부"라고 가정합니다.

### <a name="nsg-rules---east-us"></a>NSG 규칙 - 미국 동부

1. 아래 스크린샷에 표시된 것처럼 NSG에서 "Storage.EastUS"에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

      ![Storage.EastUS의 네트워크 보안 규칙에 대해 아웃바운드 보안 규칙 추가를 보여주는 스크린샷.](./media/azure-to-azure-about-networking/storage-tag.png)

2. 아래 스크린샷에 표시된 것처럼 NSG에서 “AzureActiveDirectory”에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

      ![Azure AD의 네트워크 보안 규칙에 대해 아웃바운드 보안 규칙 추가를 보여주는 스크린샷.](./media/azure-to-azure-about-networking/aad-tag.png)

3. 위의 보안 규칙과 마찬가지로 대상 위치에 해당하는 NSG에서 "EventHub.CentralUS"에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다. 이 규칙은 Site Recovery 모니터링에 대한 액세스를 허용합니다.

4. NSG에서 “AzureSiteRecovery”에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다. 이 규칙은 모든 지역에서 Site Recovery 서비스에 대한 액세스를 허용합니다.

### <a name="nsg-rules---central-us"></a>NSG 규칙 - 미국 중부

이러한 규칙은 장애 조치 후 대상 지역에서 원본 지역으로의 복제를 활성화하는 데 필요합니다.

1. NSG에서 "Storage.CentralUS"에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

2. NSG에서 “AzureActiveDirectory”에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

3. 위의 보안 규칙과 마찬가지로 원본 위치에 해당하는 NSG에서 "EventHub.EastUS"에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다. 이 규칙은 Site Recovery 모니터링에 대한 액세스를 허용합니다.

4. NSG에서 “AzureSiteRecovery”에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다. 이 규칙은 모든 지역에서 Site Recovery 서비스에 대한 액세스를 허용합니다.

## <a name="network-virtual-appliance-configuration"></a>네트워크 가상 어플라이언스 구성

NVA(네트워크 가상 어플라이언스)를 사용하여 VM에서 아웃바운드 네트워크 트래픽을 제어하는 경우 모든 복제 트래픽이 NVA를 통해 전달되면 어플라이언스는 제한을 받을 수 있습니다. 복제 트래픽이 NVA로 이동하지 않도록 "Storage"에 대한 가상 네트워크에서 네트워크 서비스 엔드포인트를 만드는 것이 좋습니다.

### <a name="create-network-service-endpoint-for-storage"></a>Storage에 대한 네트워크 서비스 엔드포인트 만들기
복제 트래픽이 Azure 경계를 나가지 않도록 "Storage"에 대한 가상 네트워크에서 네트워크 서비스 엔드포인트를 만들 수 있습니다.

- Azure 가상 네트워크를 선택하고 '서비스 엔드포인트'를 클릭합니다.

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- '추가'를 클릭하면 '서비스 엔드포인트 추가' 탭이 열립니다.
- '서비스' 아래의 'Microsoft.Storage' 및 '서브넷' 필드 아래의 필요한 서브넷을 선택하고 '추가'를 클릭합니다.

>[!NOTE]
>방화벽 지원 캐시 스토리지 계정 또는 대상 스토리지 계정을 사용하는 경우 ['신뢰할 수 있는 Microsoft 서비스 허용'](../storage/common/storage-network-security.md)을 선택해야 합니다. 또한 원본 Vnet의 하나 이상의 서브넷에 대한 액세스를 허용해야 합니다.


### <a name="forced-tunneling"></a>강제 터널링

[사용자 지정 경로](../virtual-network/virtual-networks-udr-overview.md#custom-routes)를 사용하여 Azure 기본 시스템 경로의 0.0.0.0/0 주소 접두사를 재정의하고 VM 트래픽을 온-프레미스 NVA(네트워크 가상 어플라이언스)로 우회시킬 수 있지만 Site Recovery 복제에는 이 구성이 권장되지 않습니다. 사용자 지정 경로를 사용하는 경우 복제 트래픽이 Azure 경계를 나가지 않도록 "Storage"에 대한 가상 네트워크에서 [가상 네트워크 서비스 엔드포인트를 생성](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)해야 합니다.

## <a name="next-steps"></a>다음 단계
- [Azure 가상 머신을 복제](./azure-to-azure-quickstart.md)하여 워크로드 보호를 시작합니다.
- Azure 가상 머신 장애 조치(failover)를 위한 [IP 주소 보존](site-recovery-retain-ip-azure-vm-failover.md)에 대해 자세히 알아보세요.
- [ExpressRoute를 사용하는 Azure 가상 머신](azure-vm-disaster-recovery-with-expressroute.md)의 재해 복구에 대해 자세히 알아봅니다.
