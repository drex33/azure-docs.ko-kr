---
title: Azure VMware Solution에서 VMware HCX 구성
description: Azure VMware Solution 프라이빗 클라우드용 온-프레미스 VMware HCX Connector를 구성합니다.
ms.topic: tutorial
ms.date: 07/30/2021
ms.openlocfilehash: 1249a694e01e01d4e7aa31c639c4422be2372ee4
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255479"
---
# <a name="configure-on-premises-vmware-hcx-connector"></a>온-프레미스 VMware HCX 커넥터 구성

[VMware HCX 추가 기능](install-vmware-hcx.md)을 설치하면 Azure VMware Solution 프라이빗 클라우드에 대해 온-프레미스 VMware HCX 커넥터를 구성할 준비가 된 것입니다.  

이 방법 가이드에서는 다음을 수행하게 됩니다.

* 온-프레미스 VMware HCX Connector를 Azure VMware Solution HCX Cloud Manager와 페어링
* 네트워크 프로필, 컴퓨팅 프로필 및 서비스 메시 구성
* 어플라이언스 상태를 확인하고 마이그레이션이 가능한지 유효성 검사

이러한 단계를 완료하면 VM(가상 머신) 만들기 및 마이그레이션을 위한 프로덕션 준비가 완료된 환경을 갖추게 됩니다. 

## <a name="prerequisites"></a>필수 조건

- [VMware HCX 커넥터](install-vmware-hcx.md)가 설치되었습니다.

- VMware HCX Enterprise를 사용하려면 [지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 통해 [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) 추가 항목을 사용하도록 설정했는지 확인합니다.

- [소프트웨어 버전 요구 사항](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)

- 온-프레미스 vSphere 환경(원본 환경)에서 [최소 요구 사항](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)을 충족합니다.

- [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md)는 온-프레미스 및 Azure VMware Solution 프라이빗 클라우드 ExpressRoute 회로 간에 구성됩니다.

- [필요한 모든 포트](https://ports.vmware.com/home/VMware-HCX)는 온-프레미스 구성 요소 및 Azure VMware Solution 프라이빗 간의 통신을 위해 열려 있습니다.

- [VMware HCX 네트워크 세그먼트를 정의합니다.](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments)  VMware HCX에 대한 주요 사용 사례는 워크로드 마이그레이션 및 재해 복구입니다.

- HCX 사용에 대한 정보는 [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-BFD7E194-CFE5-4259-B74B-991B26A51758.html)를 검토하세요.

## <a name="add-a-site-pairing"></a>사이트 페어링 추가

데이터 센터에서 Azure VMware Solution의 VMware HCX Cloud Manager를 VMware HCX Connector와 연결하거나 페어링할 수 있습니다.

> [!IMPORTANT]
> VMware 구성 최대 도구는 온-프레미스 HCX Connector와 HCX Cloud Manager 간의 사이트 쌍을 최대 25개까지 설명하지만 라이선스에서는 3개(HCX Advanced의 경우) 및 10개(HCX Enterprise Edition의 경우)로 제한합니다.

1. 온-프레미스 vCenter에 로그인하고, **홈** 에서 **HCX** 를 선택합니다.

1. **인프라** 에서 **사이트 페어링** 을 선택하고 **원격 사이트에 연결** 옵션(화면 가운데)을 선택합니다.

1. 앞에서 기록한 Azure VMware Solution HCX Cloud Manager URL 또는 IP 주소(`https://x.x.x.9`)와, 프라이빗 클라우드에서 CloudAdmin 역할을 보유한 사용자의 자격 증명을 입력합니다. 그런 다음 **연결** 을 선택합니다.

   > [!NOTE]
   > 사이트 쌍을 성공적으로 설정하려면 다음을 수행합니다.
   > * VMware HCX Connector는 443 포트를 통해 HCX Cloud Manager IP로 라우팅할 수 있어야 합니다.
   >
   > * 사이트 페어링 연결에는 Active Directory와 같은 외부 ID 원본의 서비스 계정이 권장됩니다. 연결된 서비스에 대해 별도의 계정을 설정하는 방법에 대한 자세한 내용은 [액세스 및 ID 개념](./concepts-identity.md)을 참조하세요.

   Azure VMware Solution의 VMware HCX Cloud Manager와 온-프레미스 VMware HCX Connector가 연결(페어링)된 것을 보여주는 화면이 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Azure VMware Solution의 HCX Manager와 VMware HCX Connector의 사이트 페어링을 보여주는 스크린샷.":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: HCX 사이트 페어링](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720) 비디오를 시청하세요.

## <a name="create-network-profiles"></a>네트워크 프로필 만들기

VMware HCX Connector는 여러 IP 세그먼트가 필요한 가상 어플라이언스의 하위 집합을 배포합니다(자동화됨). 네트워크 프로필을 만들 때 [계획 단계](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments) 중에 식별한 IP 세그먼트를 사용합니다.  다음과 같은 4개의 네트워크 프로필을 만듭니다.

   - 관리
   - vMotion
   - 복제
   - 업링크

1. **인프라** 에서 **상호 연결** > **다중 사이트 서비스 메시** > **네트워크 프로필** > **네트워크 프로필 만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="vSphere 클라이언트에서 네트워크 프로필을 만들 위치를 보여주는 스크린샷." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. 각 네트워크 프로필에 대해 네트워크 및 포트 그룹을 선택하고, 이름을 제공하고, 세그먼트의 IP 풀을 만듭니다. 그런 다음 **만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="새 네트워크 프로필에 대한 세부 정보를 보여주는 스크린샷." lightbox="media/tutorial-vmware-hcx/example-configurations-network-profile.png":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: HCX 네트워크 프로필](https://www.youtube.com/embed/O0rU4jtXUxc) 비디오를 시청하세요.

## <a name="create-a-compute-profile"></a>컴퓨팅 프로필 만들기

1. **인프라** 아래에서 **상호 연결** > **컴퓨팅 프로필** > **컴퓨팅 프로필 만들기** 를 차례로 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="컴퓨팅 프로필 만들기를 시작하기 위한 선택 항목을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. 프로필 이름을 입력하고 **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="컴퓨팅 프로필 이름의 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 마이그레이션, 네트워크 확장, 재해 복구 등 사용하도록 설정할 서비스를 선택한 다음, **계속** 을 선택합니다.

   > [!NOTE]
   > 일반적으로 여기서는 아무 것도 변경되지 않습니다.

1. **서비스 리소스 선택** 에서, 선택한 VMware HCX 서비스에 사용할 서비스 리소스(클러스터)를 하나 이상 선택합니다.

1. 온-프레미스 데이터 센터에 클러스터가 표시되면 **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="선택한 서비스 리소스와 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. **데이터 저장소 선택** 에서, VMware HCX 상호 연결 어플라이언스를 배포할 데이터 저장소 스토리지 리소스를 선택합니다. 그런 다음, **계속** 을 선택합니다.

   여러 리소스를 선택하는 경우 VMware HCX는 해당 용량을 모두 사용할 때까지 선택된 첫 번째 리소스를 사용합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="선택한 데이터 스토리지 리소스와 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::

1. **관리 네트워크 프로필** 선택에서, 이전 단계에서 만든 관리 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="관리 네트워크 프로필의 선택 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. **업링크 네트워크 프로필 선택** 에서 이전 절차에서 만든 업링크 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="업링크 네트워크 프로필의 선택 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. **vMotion 네트워크 프로필 선택** 에서, 이전 단계에서 만든 vMotion 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="vMotion 네트워크 프로필의 선택 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. **vSphere 복제 네트워크 프로필 선택** 에서, 이전 단계에서 만든 복제 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="복제 네트워크 프로필의 선택 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. **네트워크 확장 분산 스위치 선택** 에서 계층 2 확장 네트워크의 Azure VMware Solution으로 마이그레이션할 가상 머신이 포함된 스위치를 선택합니다. 그런 다음, **계속** 을 선택합니다.

   > [!NOTE]
   > 계층 2(L2) 확장 네트워크에서 가상 머신을 마이그레이션하지 않는 경우 이 단계를 건너뛸 수 있습니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="분산 가상 스위치의 선택 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 연결 규칙을 검토하고 **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="연결 규칙과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. **마침** 을 선택하여 컴퓨팅 프로필을 만듭니다.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="컴퓨팅 프로필 정보를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: 컴퓨팅 프로필](https://www.youtube.com/embed/e02hsChI3b8) 비디오를 시청하세요.

## <a name="create-a-service-mesh"></a>서비스 메시 만들기

>[!IMPORTANT]
>포트 UDP 500/4500은 온-프레미스 VMware HCX Connector '업링크' 네트워크 프로필 주소와 Azure VMware Solution HCX Cloud '업링크' 네트워크 프로필 주소 사이에서 열리는지 확인합니다.


1. **인프라** 에서 **상호 연결** > **서비스 메시** > **서비스 메시 만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="서비스 메시 만들기를 시작하기 위한 선택 항목의 스크린샷" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 미리 채워진 사이트를 검토한 다음, **계속** 을 선택합니다.

   > [!NOTE]
   > 이번이 첫 번째 서비스 메시 구성인 경우 이 화면을 수정할 필요가 없습니다.

1. 드롭다운 목록에서 원본 및 원격 컴퓨팅 프로필을 선택한 다음, **계속** 을 선택합니다.

   선택하는 항목에 따라 VM이 VMware HCX 서비스를 사용할 수 있는 리소스가 정의됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="원본 컴퓨팅 프로필을 선택하는 것을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="원격 컴퓨팅 프로필을 선택하는 것을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 사용할 서비스를 검토한 다음, **계속** 을 선택합니다.

1. **고급 구성 - 업링크 네트워크 프로필 재정의** 에서 **계속** 을 선택합니다.

   업링크 네트워크 프로필은 원격 사이트의 상호 연결 어플라이언스에 연결할 수 있는 네트워크를 통해 연결됩니다.

1. **고급 구성 - 네트워크 확장 어플라이언스 스케일 아웃** 을 검토하고 **계속** 을 선택합니다.

   어플라이언스당 최대 8개의 VLAN을 가질 수 있지만 다른 어플라이언스를 배포하여 8개의 VLAN을 추가할 수 있습니다. 또한 추가 어플라이언스의 계정을 고려하여 IP 공간이 있어야 하며 어플라이언스당 IP 하나입니다.  자세한 내용은 [VMware HCX 구성 제한](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0)을 참조하세요.

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="VLAN 수를 늘릴 위치를 보여주는 스크린샷." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. **고급 구성 - 트래픽 엔지니어링** 을 검토하고 필요한 대로 수정한 다음, **계속** 를 선택합니다.

1. 토폴로지 미리 보기를 검토하고 **계속** 을 선택합니다.

1. 이 서비스 메시에 사용할 사용자에게 친숙한 이름을 입력하고 **완료** 를 선택하여 마칩니다.

1. **작업 보기** 를 선택하여 배포를 모니터링합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="작업을 살펴보는 단추를 보여주는 스크린샷.":::

   서비스 메시 배포가 성공적으로 완료되면 서비스가 녹색으로 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="서비스의 녹색 표시기를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. 어플라이언스 상태를 검사하여 서비스 메시의 상태를 확인합니다.

1. **상호 연결** > **어플라이언스** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="어플라이언스의 상태를 확인하기 위한 선택 항목을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

   >[!NOTE]
   >서비스 메시를 설정하고 나면 프라이빗 클라우드에서 새 데이터 저장소와 새 호스트를 확인할 수 있습니다. 이 항목은 서비스 메시를 설정한 후 완전히 작동합니다.
   >
   >:::image type="content" source="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png" alt-text="HCX 서비스 메시 데이터 저장소 및 호스트를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: 서비스 메시](https://www.youtube.com/embed/COY3oIws108) 비디오를 시청하세요.

## <a name="create-a-network-extension"></a>네트워크 확장 만들기

온-프레미스 환경에서 Azure VMware Solution으로 네트워크를 확장할 수 있는 선택적 단계입니다.

1. **서비스** 아래에서 **네트워크 확장** > **네트워크 확장 만들기** 를 차례로 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="네트워크 확장 만들기를 시작하기 위한 선택 항목을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Azure VMware Solution으로 확장하려는 각 네트워크를 선택하고 **다음** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="네트워크 선택 항목을 보여주는 스크린샷":::

1. 확장하려는 각 네트워크의 온-프레미스 게이트웨이 IP를 입력한 다음, **제출** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="게이트웨이 IP 주소의 항목을 보여주는 스크린샷":::

   네트워크 확장이 완료될 때까지 몇 분 정도 걸립니다. 확장이 완료되면 상태가 **확장 완료** 로 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="[확장 완료] 상태를 보여주는 스크린샷":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: 네트워크 확장](https://www.youtube.com/embed/gYR0nftKui0) 비디오를 시청하세요.

## <a name="next-steps"></a>다음 단계

HCX 상호 연결 터널 상태가 **가동 중** 이고 녹색인 경우 VMware HCX를 사용하여 Azure VMware Solution VM을 마이그레이션하고 보호할 수 있습니다. Azure VMware Solution은 (네트워크 확장을 포함한 또는 포함하지 않은) 워크로드 마이그레이션을 지원합니다. vSphere 환경에서 워크로드를 계속 마이그레이션할 수 있으며, 온-프레미스에 네트워크를 만들고 해당 네트워크에 VM을 배포할 수 있습니다. 자세한 내용은 [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/index.html)를 참조하세요.

