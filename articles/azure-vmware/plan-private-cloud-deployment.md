---
title: Azure VMware Solution 배포 계획
description: Azure VMware Solution 배포를 계획하는 방법을 알아봅니다.
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 07/07/2021
ms.openlocfilehash: 4f0f59a01ff53646a23809efa129a154bfba81ee
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444216"
---
# <a name="plan-the-azure-vmware-solution-deployment"></a>Azure VMware Solution 배포 계획

VM(가상 머신) 생성 및 마이그레이션을 위한 성공적인 프로덕션 준비 환경에서는 Azure VMware Solution 배포를 계획하는 것이 중요합니다. 계획 프로세스에서 배포에 필요한 항목을 식별하고 수집합니다. 계획할 때 배포 중에 쉽게 참조할 수 있도록 수집한 정보를 문서화해야 합니다. 성공적인 배포는 VM(가상 머신)을 만들고 마이그레이션을 수행할 수 있는 프로덕션 준비가 완료된 환경을 만듭니다.

이 방법 가이드에서는 다음을 수행하게 됩니다.

> [!div class="checklist"]
> * Azure 구독, 리소스 그룹, 지역 및 리소스 이름 식별
> * 크기 호스트를 식별하고 클러스터 및 호스트 수 결정
> * 적격 Azure 플랜에 대한 호스트 할당량 요청
> * 프라이빗 클라우드 관리를 위한 /22 CIDR IP 세그먼트 식별
> * 단일 네트워크 세그먼트 식별
> * 가상 네트워크 게이트웨이 정의
> * VMware HCX 네트워크 세그먼트 정의

완료되면 마지막에 권장되는 다음 단계에 따라 이 시작 가이드를 계속 진행합니다.


## <a name="identify-the-subscription"></a>구독 식별

Azure VMware Solution을 배포하는 데 사용할 구독을 확인합니다.  새 구독을 만들거나 기존 구독을 사용할 수 있습니다.

>[!NOTE]
>구독은 Microsoft EA(기업계약) 또는 CSP(클라우드 솔루션 공급자) Azure 플랜과 연결되어야 합니다. 자세한 내용은 [자격 기준](request-host-quota-azure-vmware-solution.md#eligibility-criteria)을 참조하세요.

## <a name="identify-the-resource-group"></a>리소스 그룹 식별

Azure VMware Solution에 사용하려는 리소스 그룹을 확인합니다.  일반적으로 리소스 그룹은 특별히 Azure VMware Solution용으로 만들어지지만, 기존 리소스 그룹을 사용할 수 있습니다.

## <a name="identify-the-region-or-location"></a>지역 또는 위치 식별

Azure VMware Solution을 배포하려는 [지역](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)을 식별합니다. 

## <a name="define-the-resource-name"></a>리소스 이름 정의

리소스 이름은 Azure VMware Solution 프라이빗 클라우드의 제목으로 지정되는 친숙하고 설명이 포함된 이름입니다(예: **MyPrivateCloud**).

>[!IMPORTANT]
>이름은 40자를 초과하면 안 됩니다. 이름이 이 제한을 초과하면 프라이빗 클라우드에서 사용할 공용 IP 주소를 만들 수 없습니다. 

## <a name="identify-the-size-hosts"></a>호스트 크기 식별

Azure VMware Solution을 배포할 때 사용할 호스트 크기를 확인합니다.  

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="determine-the-number-of-clusters-and-hosts"></a>클러스터 및 호스트 수 결정

사용자가 수행하는 첫 번째 Azure VMware Solution 배포는 단일 클러스터가 포함된 프라이빗 클라우드로 구성됩니다. 배포에 대한 첫 번째 클러스터에 배포할 호스트 수를 정의해야 합니다.

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]


>[!NOTE]
>클러스터당 호스트 수에 대한 제한, 프라이빗 클라우드당 클러스터 수 및 프라이빗 클라우드당 호스트 수에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-vmware-solution-limits)을 참조하세요.

## <a name="request-a-host-quota"></a>호스트 할당량 요청 

호스트 할당량을 조기에 요청하는 것이 중요하므로 계획 프로세스가 완료되면 Azure VMware Solution 프라이빗 클라우드를 배포할 준비가 된 것입니다.
호스트 할당량을 요청하기 전에 Azure 구독, 리소스 그룹 및 지역을 식별했는지 확인합니다. 또한 호스트 크기를 확인하고 필요한 클러스터 및 호스트 수를 결정해야 합니다.

지원 팀에서 호스트 할당량 요청을 받으면 요청을 확인하고 호스트를 할당하는 데 최대 5일(영업일 기준)이 걸립니다.

- [EA 고객](request-host-quota-azure-vmware-solution.md#request-host-quota-for-ea-customers)
- [CSP 고객](request-host-quota-azure-vmware-solution.md#request-host-quota-for-csp-customers)


## <a name="define-the-ip-address-segment-for-private-cloud-management"></a>프라이빗 클라우드 관리에 사용되는 IP 주소 세그먼트 정의

Azure VMware Solution에는 /22 CIDR 네트워크가 필요합니다(예: `10.0.0.0/22`). 이 주소 공간은 더 작은 네트워크 세그먼트(서브넷)로 분할되어 vCenter, VMware HCX, NSX-T 및 vMotion 기능을 비롯한 Azure VMware Solution 관리 세그먼트에 사용됩니다. 다이어그램에는 Azure VMware Solution 관리 IP 주소 세그먼트가 강조 표시되어 있습니다.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Azure VMware Solution 관리 IP 주소 세그먼트를 보여주는 다이어그램" border="false":::  

>[!IMPORTANT]
>/22 CIDR 네트워크 주소 블록은 온-프레미스 또는 Azure에 있는 기존 네트워크 세그먼트와 겹치지 않아야 합니다. 프라이빗 클라우드당 /22 CIDR 네트워크를 분할하는 방법에 대한 자세한 내용은 [라우팅 및 서브넷 고려 사항](tutorial-network-checklist.md#routing-and-subnet-considerations)을 참조하세요.



## <a name="define-the-ip-address-segment-for-vm-workloads"></a>VM 워크로드의 IP 주소 세그먼트 정의

모든 VMware 환경과 마찬가지로 VM은 네트워크 세그먼트에 연결해야 합니다.  Azure VMware Solution의 프로덕션 배포가 확장됨에 따라 온-프레미스 및 로컬 NSX-T 네트워크 세그먼트의 L2 확장 세그먼트 조합이 있는 경우가 많습니다. 

초기 배포의 경우 단일 네트워크 세그먼트(IP 네트워크)를 식별합니다(예: `10.0.4.0/24`). 이 네트워크 세그먼트는 주로 초기 배포 중 테스트 목적으로 사용됩니다.  주소 블록은 온-프레미스 또는 Azure 내에서 네트워크 세그먼트와 겹치지 않아야 하며 이미 정의된 /22 네트워크 세그먼트 내에 있지 않아야 합니다. 
  
:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="확인 - 가상 머신 워크로드에 대한 IP 주소 세그먼트" border="false":::     


## <a name="define-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 정의

Azure VMware Solution에는 Azure Virtual Network와 ExpressRoute 회로가 필요합니다. *기존* 또는 *새* ExpressRoute 가상 네트워크 게이트웨이를 사용할지 여부를 정의합니다. *새* 가상 네트워크 게이트웨이를 사용하기로 결정하면 프라이빗 클라우드를 만든 후에 가상 네트워크 게이트웨이를 만듭니다. 기존 ExpressRoute 가상 네트워크 게이트웨이를 사용할 수 있으며 계획 목적으로 사용할 ExpressRoute 가상 네트워크 게이트웨이를 기록해 둡니다. 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Azure VMware Solution에 연결된 Azure Virtual Network를 보여주는 다이어그램" border="false":::

>[!IMPORTANT]
>Azure Virtual WAN의 가상 네트워크 게이트웨이에 연결할 수 있지만 이는 이 빠른 시작의 범위에 해당되지 않습니다.

## <a name="define-vmware-hcx-network-segments"></a>VMware HCX 네트워크 세그먼트 정의

VMware HCX는 데이터 센터와 클라우드 전반에서 애플리케이션 마이그레이션, 워크로드 리밸런싱, 비즈니스 연속성을 단순화하는 애플리케이션 모바일 플랫폼입니다. 다양한 마이그레이션 유형을 통해 VMware 워크로드를 Azure VMware Solution 및 기타 연결된 사이트로 마이그레이션할 수 있습니다. 

VMware HCX Connector는 여러 IP 세그먼트가 필요한 가상 어플라이언스의 하위 집합을 배포합니다(자동화됨). 네트워크 프로필을 만드는 경우 IP 세그먼트를 사용합니다. 파일럿 또는 소규모 제품 사용 사례를 지원하는 VMware HCX 배포에 대해 다음을 확인합니다.  마이그레이션의 요구에 따라 필요에 따라 수정합니다. 

- **관리 네트워크:** 온-프레미스에 VMware HCX를 배포할 때 VMware HCX에 대한 관리 네트워크를 식별해야 합니다.  일반적으로 온-프레미스 VMware 클러스터에서 사용하는 것과 동일한 관리 네트워크입니다.  VMware HCX에 대해 이 네트워크 세그먼트에서 최소한 **두 개** 의 IP를 식별합니다. 파일럿 또는 소규모 사용 사례 이외의 배포 규모에 따라 더 많은 수의 배포가 필요할 수 있습니다.

  >[!NOTE]
  >대규모 환경을 준비하려면 온-프레미스 VMware 클러스터에 사용되는 관리 네트워크를 사용하는 대신 새 /26 네트워크를 만들고 해당 네트워크를 온-프레미스 VMware 클러스터에 포트 그룹으로 제공합니다.  그런 다음, 최대 10개의 서비스 메시와 60개의 네트워크 확장기(서비스 메시당 -1개)를 만들 수 있습니다. Azure VMware Solution 프라이빗 클라우드를 사용하여 네트워크 확장기당 **8개** 의 네트워크를 확장할 수 있습니다.

- **업링크 네트워크:** 온-프레미스에 VMware HCX를 배포할 때 VMware HCX에 대한 업링크 네트워크를 식별해야 합니다. 관리 네트워크에 사용하는 것과 동일한 네트워크를 사용합니다. 

- **vMotion 네트워크:** 온-프레미스에 VMware HCX를 배포할 때 VMware HCX에 대한 vMotion 네트워크를 식별해야 합니다.  일반적으로 온-프레미스 VMware 클러스터에서 vMotion에 사용하는 것과 동일한 네트워크입니다.  VMware HCX에 대해 이 네트워크 세그먼트에서 최소한 **두 개** 의 IP를 식별합니다. 파일럿 또는 소규모 사용 사례 이외의 배포 규모에 따라 더 많은 수의 배포가 필요할 수 있습니다.

  분산 가상 스위치 또는 vSwitch0에 vMotion 네트워크를 노출해야 합니다. 그렇지 않은 경우 수용할 수 있도록 환경을 수정합니다.

  >[!NOTE]
  >많은 VMware 환경에서는 vMotion에 라우팅되지 않은 네트워크 세그먼트를 사용하므로 문제가 발생하지 않습니다.
  
- **복제 네트워크:** 온-프레미스에 VMware HCX를 배포할 때 복제 네트워크를 정의해야 합니다. 관리 및 업링크 네트워크에 사용하는 것과 동일한 네트워크를 사용합니다.  온-프레미스 클러스터 호스트에서 전용 복제 VMkernel 네트워크를 사용하는 경우 이 네트워크 세그먼트에서 **두 개** 의 IP 주소를 예약하고 복제 네트워크에 대한 복제 VMkernel 네트워크를 사용합니다.


## <a name="determine-whether-to-extend-your-networks"></a>네트워크를 확장할지 여부 결정

필요한 경우 네트워크 세그먼트를 온-프레미스에서 Azure VMware Solution으로 확장할 수 있습니다. 네트워크 세그먼트를 확장한 경우 지금 이러한 지침에 따라 해당 네트워크를 식별합니다.

- 네트워크는 온-프레미스 VMware 환경에서 [vDS(vSphere 분산 스위치)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html)에 연결해야 합니다.  
- [vSphere 표준 스위치](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html)에 있는 네트워크는 확장할 수 없습니다.

>[!IMPORTANT]
>이러한 네트워크는 배포 중이 아니라 구성의 마지막 단계로 확장됩니다.


## <a name="next-steps"></a>다음 단계
이제 필요한 정보를 수집하고 문서화했으므로 다음 자습서로 계속 진행하여 Azure VMware Solution 프라이빗 클라우드를 만듭니다.

> [!div class="nextstepaction"]
> [Azure VMware Solution 배포](deploy-azure-vmware-solution.md)
