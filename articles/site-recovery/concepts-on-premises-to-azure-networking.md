---
title: Azure Site Recovery를 사용한 온-프레미스에서 장애 조치(failover)한 후 Azure VM에 연결
description: Azure Site Recovery를 사용하여 온-프레미스에서 Azure로 장애 조치한 후 Azure VM에 연결하는 방법을 설명합니다.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: harshacs
ms.openlocfilehash: 2dfb7a185a4fac41059f5f987c4fa602abf4a894
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003678"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>온-프레미스에서 장애 조치 후 Azure VM에 연결 


이 문서에서는 장애 조치 후 Azure VM에 성공적으로 연결할 수 있도록 연결을 설정하는 방법을 설명합니다.

Azure로의 온-프레미스 VM(가상 머신) 및 물리적 서버 재해 복구를 설정하는 경우 [Azure Site Recovery](site-recovery-overview.md)는 Azure에 머신을 복제하기 시작합니다. 그런 다음, 중단이 발생하면 온-프레미스 사이트에서 Azure로 장애 조치할 수 있습니다. 장애 조치가 발생하면 Site Recovery는 복제된 온-프레미스 데이터를 사용하여 Azure VM을 만듭니다. 재해 복구 계획의 일환으로 장애 조치 후 이러한 Azure VM에서 실행되는 앱에 연결하는 방법을 파악해야 합니다.

이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 장애 조치 전에 온-프레미스 머신을 준비합니다.
> * 장애 조치 후 Azure VM을 준비합니다. 
> * 장애 조치 후 Azure VM에 대한 IP 주소를 유지합니다.
> * 장애 조치 후 Azure VM에 새 IP 주소를 할당합니다.

## <a name="prepare-on-premises-machines"></a>온-프레미스 머신 준비

Azure VM에 대한 연결을 보장하려면 장애 조치 전에 온-프레미스 머신을 준비합니다.

### <a name="prepare-windows-machines"></a>Windows 머신 준비

온-프레미스 Windows 머신에서 다음을 수행합니다.

1. Windows 설정을 구성합니다. 여기에는 정적 영구 경로 또는 WinHTTP 프록시를 제거하고 디스크 SAN 정책을 **OnlineAll** 로 설정하는 작업이 포함됩니다. 다음 지침을 [따릅니다](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure).

2. [이러한 서비스](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)가 실행 중인지 확인합니다.

3. 원격 데스크톱(RDP)을 사용하도록 설정하여 온-프레미스 머신에 대한 원격 연결을 허용합니다. PowerShell을 통해 RDP를 사용하도록 설정하는 방법에 대해 [알아보세요](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).

4. 장애 조치 후 인터넷을 통해 Azure VM에 액세스하려면 온-프레미스 머신의 Windows Firewall에서 퍼블릭 프로필의 TCP 및 UDP를 허용하고 RDP를 모든 프로필에 대해 허용되는 앱으로 설정합니다.

5. 장애 조치 후 사이트 간 VPN을 통해 Azure VM에 액세스하려면 온-프레미스 머신의 Windows Firewall에서 도메인 및 프라이빗 프로필에 대해 RDP를 허용합니다. RDP 트래픽을 허용하는 방법에 대해 [알아보세요](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules).
6. 장애 조치를 트리거할 때 온-프레미스 VM에서 보류 중인 Windows 업데이트가 없는지 확인합니다. 업데이트가 있는 경우 장애 조치 후에 해당 업데이트를 Azure VM에 설치할 수 있으며, 업데이트가 완료될 때까지 VM에 로그인할 수 없습니다.

### <a name="prepare-linux-machines"></a>Linux 머신 준비

온-프레미스 Linux 머신에서 다음을 수행합니다.

1. 시스템 부팅 시 Secure Shell 서비스가 자동으로 시작되도록 설정되어 있는지 확인합니다.
2. 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.


## <a name="configure-azure-vms-after-failover"></a>장애 조치 후 Azure VM 구성

장애 조치 후 만들어진 Azure VM에서 다음을 수행합니다.

1. 인터넷을 통해 VM에 연결하려면 공용 IP 주소를 VM에 할당합니다. 온-프레미스 머신에 사용한 것과 동일한 공용 IP 주소는 Azure VM에 사용할 수 없습니다. [자세히 알아보기](../virtual-network/ip-services/virtual-network-public-ip-address.md)
2. VM의 NSG(네트워크 보안 그룹) 규칙에서 RDP 또는 SSH 포트로 들어오는 연결을 허용하는지 확인합니다.
3. [부팅 진단](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine)을 확인하여 VM을 살펴봅니다.


> [!NOTE]
> Azure Bastion 서비스는 Azure VM에 대한 프라이빗 RDP 및 SSH 액세스를 제공합니다. 이 서비스에 대해 [자세히 알아보세요](../bastion/bastion-overview.md).

## <a name="set-a-public-ip-address"></a>공용 IP 주소 설정

Azure VM에 공용 IP 주소를 수동으로 할당하는 대신, Site Recovery [복구 계획](site-recovery-create-recovery-plans.md)에서 스크립트 또는 Azure Automation runbook을 사용하여 장애 조치 중에 주소를 할당하거나 Azure Traffic Manager를 사용하여 DNS 수준 라우팅을 설정할 수 있습니다. 퍼블릭 주소를 설정하는 방법에 대해 [자세히 알아보세요](concepts-public-ip-address-with-site-recovery.md).


## <a name="assign-an-internal-address"></a>내부 주소 할당

장애 조치 후 Azure VM의 내부 IP 주소를 설정하려면 다음 두 가지 옵션을 사용할 수 있습니다.

- **동일한 IP 주소 유지**: Azure VM에서 온-프레미스 머신에 할당된 것과 동일한 IP 주소를 사용할 수 있습니다.
- **다른 IP 주소 사용**: Azure VM에 다른 IP 주소를 사용할 수 있습니다.


## <a name="retain-ip-addresses"></a>IP 주소 유지

Site Recovery를 사용하면 Azure로 장애 조치할 때 동일한 IP 주소를 유지할 수 있습니다. 동일한 IP 주소를 유지하면 장애 조치 후 잠재적인 네트워크 문제가 방지되지만 다소 복잡할 수 있습니다.

- 대상 Azure VM이 온-프레미스 사이트와 동일한 IP 주소/서브넷을 사용하는 경우 주소가 겹치므로 사이트 간 VPN 연결 또는 ExpressRoute를 사용하여 서로 연결할 수 없습니다. 서브넷은 고유해야 합니다.
- Azure VM에서 앱을 사용할 수 있도록 장애 조치 후 온-프레미스에서 Azure로 연결해야 합니다. Azure는 확장된 VLAN을 지원하지 않으므로 IP 주소를 유지하려는 경우 온-프레미스 머신 외에도 전체 서브넷을 장애 조치하여 Azure로 IP 공간을 이동해야 합니다.
- 서브넷 장애 조치를 사용하면 특정 서브넷을 온-프레미스 및 Azure에서 동시에 사용할 수 없게 됩니다.

IP 주소를 유지하려면 다음 단계를 수행해야 합니다.

- 복제된 항목의 컴퓨팅 및 네트워크 속성에서 온-프레미스 설정을 미러링할 대상 Azure VM에 대한 네트워크 및 IP 주소를 설정합니다.
- 서브넷은 재해 복구 프로세스의 일부로 관리해야 합니다. 온-프레미스 네트워크와 일치하는 Azure VNet이 필요합니다. 또한 장애 조치 후에는 서브넷이 Azure로 이동되었다는 사실과 새 IP 주소 위치를 반영하도록 네트워크 경로를 수정해야 합니다.  

### <a name="failover-example"></a>장애 조치 예제

예를 살펴보겠습니다.

- 가상의 회사인 Woodgrove Bank는 온-프레미스에 비즈니스 앱을 호스트하고 Azure에 모바일 앱을 호스트합니다.
- 사이트 간 VPN을 통해 온-프레미스에서 Azure로 연결합니다. 
- Woodgrove는 Site Recovery를 사용하여 온-프레미스 머신을 Azure에 복제합니다.
- 온-프레미스 앱은 하드 코드된 IP 주소를 사용하므로 Azure에서 동일한 IP 주소를 유지하려고 합니다.
- 온-프레미스에서 앱을 실행하는 머신은 다음 세 개의 서브넷에서 실행됩니다.
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- Azure에서 실행되는 앱은 Azure VNet **Azure 네트워크** 의 다음 두 서브넷에 있습니다.
    - 172.16.1.0/24
    - 172.16.2.0/24

주소를 유지하기 위해 수행하는 작업은 다음과 같습니다.

1. 복제를 사용하도록 설정하는 경우 머신이 **Azure 네트워크** 에 복제하도록 지정합니다.
2. Azure에서 **복구 네트워크** 를 만듭니다. 이 VNet은 온-프레미스 네트워크에서 192.168.1.0/24 서브넷을 미러링합니다.
3. Woodgrove는 두 네트워크 간에 [VNet 간 연결](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)을 설정합니다. 

    > [!NOTE]
    > 애플리케이션 요구 사항에 따라 장애 조치 전에 Site Recovery [복구 계획](site-recovery-create-recovery-plans.md)의 수동 단계/스크립팅된 단계/Azure Automation runbook으로 또는 장애 조치가 완료된 후에 VNet 간 연결을 설정할 수 있습니다.

4. 장애 조치 전에 Site Recovery의 머신 속성에서는 다음 절차에 설명된 대로 대상 IP 주소를 온-프레미스 머신의 주소로 설정합니다.
5. 장애 조치 후 동일한 IP 주소를 사용하여 Azure VM을 만듭니다. Woodgrove는 VNet 피어링(전송 연결 사용)을 사용하여 **Azure 네트워크** 에서 **복구 네트워크** VNet에 연결합니다.
6. 온-프레미스에서 Woodgrove는 192.168.1.0/24가 Azure로 이동되었음을 반영하기 위한 경로 수정을 비롯하여 네트워크를 변경해야 합니다.  

**장애 조치 전 인프라**

![서브넷 장애 조치(failover) 전](./media/site-recovery-network-design/network-design7.png)


**장애 조치 후 인프라**

![서브넷 장애 조치(failover) 후](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>대상 네트워크 설정 지정

장애 조치 전에 대상 Azure VM의 네트워크 설정 및 IP 주소를 지정합니다.

1.  Recovery Services 자격 증명 모음 -> **복제된 항목** 에서 온-프레미스 머신을 선택합니다.
2. 머신의 **컴퓨팅 및 네트워크** 페이지에서 **편집** 을 클릭하여 대상 Azure VM에 대한 네트워크 및 어댑터 설정을 구성합니다.
3. **네트워크 속성** 에서 장애 조치 후에 Azure VM을 만들 때 Azure VM이 배치될 대상 네트워크를 선택합니다.
4. **네트워크 인터페이스** 에서 대상 네트워크에 네트워크 어댑터를 구성합니다. 기본적으로 Site Recovery는 온-프레미스 머신에서 검색된 모든 NIC를 표시합니다.
    - 대상 네트워크에서 특정 NIC가 필요하지 않은 경우 **대상 네트워크 인터페이스 유형** 에서 각 NIC를 **기본**, **보조** 또는 **만들지 않음** 으로 설정할 수 있습니다. 네트워크 어댑터 하나를 장애 조치를 위해 기본 어댑터로 설정해야 합니다. 대상 네트워크를 수정하면 Azure VM의 모든 NIC에 영향을 줍니다.
    - NIC 이름을 클릭하여 Azure VM이 배포될 서브넷을 지정합니다.
    - **동적** 을 대상 Azure VM에 할당하려는 개인 IP 주소로 덮어씁니다. IP 주소가 지정되지 않으면 Site Recovery는 장애 조치 시 서브넷에서 다음으로 사용할 수 있는 IP 주소를 NIC에 할당합니다.
    - Azure로의 온-프레미스 장애 조치를 위한 NIC 관리에 대해 [자세히 알아보세요](site-recovery-manage-network-interfaces-on-premises-to-azure.md).


## <a name="get-new-ip-addresses"></a>새 IP 주소 가져오기

이 시나리오에서는 Azure VM이 장애 조치 후에 새 IP 주소를 갖게 됩니다. 장애 조치 후 생성된 가상 머신에 대한 새 IP 주소를 설정하려면 다음 단계를 참조하세요.

1. **복제된 항목** 으로 이동합니다.
2. 원하는 Azure 가상 머신을 선택합니다.
3. **컴퓨팅 및 네트워크** 를 선택하고 **편집** 을 선택합니다.

     ![장애 조치 네트워킹 구성 사용자 지정](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 장애 조치 네트워크 설정을 업데이트하려면 구성하려는 NIC에 대해 **편집** 을 선택합니다. 열린 다음 페이지에서 테스트 장애 조치 및 장애 조치 위치에 미리 만든 해당 IP 주소를 제공합니다.

    ![NIC 구성 편집](media/azure-to-azure-customize-networking/nic-drilldown.png)

5. **확인** 을 선택합니다.

이제 Site Recovery는 이러한 설정을 적용하고, 대상 IP 범위에서 사용할 수 있는 경우 장애 조치의 가상 머신이 해당 IP 주소를 통해 선택한 리소스에 연결되도록 합니다. 이 시나리오에서는 전체 서브넷을 장애 조치할 필요가 없습니다. 장애 조치된 머신에서 가상 머신의 새 IP 주소를 가리키도록 레코드를 업데이트하려면 DNS 업데이트가 필요합니다.

## <a name="next-steps"></a>다음 단계
온-프레미스 Active Directory 및 DNS를 Azure로 복제하는 [방법을 알아봅니다](site-recovery-active-directory.md).