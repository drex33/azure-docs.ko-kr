---
title: VMware HCX를 사용하여 재해 복구 배포
description: VMware HCX 재해 복구를 사용하여 VM(가상 머신)의 재해 복구를 배포하는 방법을 알아봅니다. Azure VMware Solution을 복구 또는 대상 사이트로 사용하는 방법도 알아봅니다.
ms.topic: how-to
ms.date: 06/10/2021
ms.openlocfilehash: f08f1c1ccff0387c5750a355f83de02e1dcd8e09
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430991"
---
# <a name="deploy-disaster-recovery-using-vmware-hcx"></a>VMware HCX를 사용하여 재해 복구 배포

이 문서에는 VMware HCX 솔루션을 사용하고 Azure VMware Solution 프라이빗 클라우드를 복구 또는 대상 사이트로 사용하는 VM(가상 머신)의 재해 복구를 배포합니다.

>[!IMPORTANT]
>HCX의 일부이지만 VMware HCX DR(재해 복구)은 대규모 배포에 권장되지 않습니다. 재해 복구 오케스트레이션은 100% 수동이며, Azure VMware Solution에는 현재 수동 HCX DR 장애 조치(failover)를 지원하기 위한 Runbook 또는 기능이 없습니다. 엔터프라이즈급 재해 복구의 경우 VMware SRM(Site Recovery Manager) 또는 VMware BCDR(비즈니스 연속성 및 재해 복구) 솔루션을 참조하세요.

VMware HCX는 복제 정책에서 세밀하게 제어하고 세분화하는 다양한 작업을 제공합니다. 사용 가능한 작업은 다음과 같습니다.

- **역방향** – 재해 발생 이후입니다. 역방향을 사용하면 사이트 B가 원본 사이트 및 현재 보호된 VM이 있는 사이트 A를 만드는 데 도움이 됩니다.

- **일시 중지** – 선택한 VM과 연결된 현재 복제 정책을 일시 중지합니다.

- **계속하기** – 선택한 VM과 연결된 현재 복제 정책을 계속합니다.

- **제거** – 선택한 VM과 연결된 현재 복제 정책을 제거합니다.

- **지금 동기화** – 바인딩되지 않은 원본 VM을 보호된 VM으로 보냅니다.

이 가이드에서는 다음 복제 시나리오를 다룹니다.

- VM 또는 VM 그룹을 보호합니다.

- VM 또는 VM 그룹의 테스트 복구를 완료합니다.

- VM 또는 VM 그룹을 복구합니다.

- VM 또는 VM 그룹을 역방향으로 보호합니다.

## <a name="protect-vms"></a>VM 보호

1. 원본 사이트에서 **vSphere 클라이언트** 에 로그인하고 **HCX 플러그 인** 에 액세스합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="vSphere 웹 클라이언트의 HCX 옵션을 보여 주는 스크린샷." border="true":::

1. **재해 복구** 영역을 입력하고 **VM 보호** 를 선택합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="vSphere 웹 클라이언트의 재해 복구 대시보드를 보여 주는 스크린샷." border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. 원본 및 원격 사이트를 선택합니다. 이 경우 원격 사이트는 Azure VMware Solution 프라이빗 클라우드여야 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="HCX: 보호된 가상 머신 창을 보여 주는 스크린샷." border="true":::

1. 필요한 경우 **기본 복제** 옵션을 선택합니다.

   - **압축 사용:** 낮은 처리량인 경우에 권장됩니다.

   - **정지 사용:** VM을 일시 중지하여 일관된 복사본이 원격 사이트에 동기화되도록 합니다.

   - **대상 스토리지:** 보호된 VM 및 vSAN 데이터 저장소여야 하는 Azure VMware Solution 프라이빗 클라우드의 원격 데이터 저장소입니다.

   - **컴퓨팅 컨테이너:** 원격 vSphere 클러스터 또는 리소스 풀입니다.

   - **대상 폴더:** 선택 사항인 원격 대상 폴더입니다. 폴더를 선택하지 않으면 VM은 선택한 클러스터 바로 아래에 배치됩니다.

   - **RPO:** 원본 VM과 보호된 VM 간의 동기화 간격입니다. 간격은 5분에서 24시간까지 가능합니다.

   - **스냅샷 간격:** 스냅샷 사이의 간격입니다.

   - **스냅샷 수:** 구성된 스냅샷 간격 내에 있는 총 스냅샷 수입니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="가상 머신 보호 복제 옵션을 보여 주는 스크린샷." border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. 목록에서 하나 이상의 VM을 선택하고 필요에 따라 복제 옵션을 구성합니다.

   기본적으로 VM은 기본 복제 옵션에 구성된 전역 설정 정책을 상속합니다. 선택한 VM의 각 네트워크 인터페이스에 대해 원격 **네트워크 포트 그룹** 을 구성하고 **마침** 을 선택하여 보호 프로세스를 시작합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="가상 머신 보호 네트워크 인터페이스 옵션을 보여 주는 스크린샷." border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. 동일한 재해 복구 영역에서 선택한 각 VM에 대한 프로세스를 모니터링합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="보호 진행 상황을 모니터링하는 가상 머신 보호를 보여 주는 스크린샷." border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. VM이 보호된 후에는 **스냅샷** 탭에서 다른 여러 스냅샷을 볼 수 있습니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="가상 머신 보호 스냅샷 목록을 보여 주는 스크린샷." border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   노란색 삼각형은 스냅샷 및 가상 머신을 테스트 복구 작업에서 테스트하지 않았음을 의미합니다.

   전원이 꺼진 VM과 전원이 켜진 VM 간에는 중요한 차이점이 있습니다. 이미지에는 전원이 켜진 VM에 대한 동기화 프로세스가 표시됩니다. VM의 전체 복사본인 첫 번째 스냅샷이 완료될 때까지 동기화 프로세스를 시작하고 구성된 간격으로 다음 스냅샷을 완료합니다. 전원이 꺼진 VM의 복사본을 동기화하면 VM이 비활성으로 표시되고 보호 작업이 완료로 표시됩니다.  VM이 켜지면 원격 사이트에 대한 동기화 프로세스를 시작합니다.

## <a name="complete-a-test-recover-of-vms"></a>VM의 테스트 복구 완료

1. Azure VMware Solution 프라이빗 클라우드인 원격 사이트의 **vSphere 클라이언트** 에 로그인합니다. 
1. **HCX 플러그 인** 에서 재해 복구 영역에 있는 모든 VM의 세로 줄임표를 선택하여 작업 메뉴를 표시한 후 **VM 테스트 복구** 를 선택합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="테스트 복구 VM 메뉴 옵션을 보여 주는 스크린샷." border="true":::

1. VM의 다양한 상태를 테스트하는 데 사용할 테스트 및 스냅샷에 대한 옵션을 선택합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="테스트할 복제본 스냅샷 인스턴스를 보여 주는 스크린샷." border="true":::

1. **테스트** 를 선택하면 복구 작업이 시작됩니다.

1. 작업을 마치면 Azure VMware Solution 프라이빗 클라우드 vCenter에서 새 VM을 확인할 수 있습니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="복구 확인 작업 요약을 보여 주는 스크린샷." border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. VM 또는 VM에서 실행 중인 애플리케이션을 테스트한 후에는 정리를 수행하여 테스트 인스턴스를 삭제합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="정리 테스트 인스턴스를 보여 주는 스크린샷." border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>VM 복구

1. Azure VMware Solution 프라이빗 클라우드인 원격 사이트의 **vSphere 클라이언트** 에 로그인하고 **HCX 플러그 인** 에 액세스합니다.

   복구 시나리오의 경우 이 예에 사용된 VM 그룹입니다.

1. 목록에서 복구할 VM을 선택하고 **작업** 메뉴를 연 다음 **VM 복구** 를 선택합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="VM 복구 메뉴 옵션을 보여 주는 스크린샷." border="true":::

1. 각 인스턴스에 대한 복구 옵션을 구성하고 **복구** 를 선택하여 복구 작업을 시작합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="대상 사이트에 VM을 복구하기 위한 확인을 보여 주는 스크린샷." border="true":::

1. 복구 작업이 완료되면 원격 vCenter Server 인벤토리에 새 VM이 표시됩니다.

## <a name="complete-a-reverse-replication-on-vms"></a>VM에서 역방향 복제 완료

1. Azure VMware Solution 프라이빗 클라우드의 **vSphere 클라이언트** 에 로그인하고 **HCX 플러그 인** 에 액세스합니다.
   
   >[!NOTE]
   > 역방향 복제를 시작하기 전에 원본 사이트의 원래 VM이 꺼져 있는지 확인합니다. VM의 전원이 꺼진 경우 작업이 실패합니다.

1. 목록에서 원본 사이트로 다시 복제할 VM을 선택하고 **작업** 메뉴를 연 다음 **역방향** 을 선택합니다. 
1. **역방향** 을 선택하여 복제를 시작합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="역방향 메뉴 옵션을 보여 주는 스크린샷." border="true":::

1. 각 VM의 세부 정보 섹션을 모니터링합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="역방향 작업의 결과를 보여 주는 스크린샷." border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>재해 복구 계획 자동화

VMware HCX에는 현재 재해 복구 계획을 만들고 자동화하는 메커니즘이 기본으로 제공되지 않습니다. 그러나 VMware HCX는 재해 복구 작업을 위한 API를 포함하여 일련의 REST API를 제공합니다. API 사양은 URL의 VMware HCX Manager 내에서 액세스할 수 있습니다.

이러한 API는 재해 복구에서 다음 작업을 담당합니다.

- 보호

- 복구

- 테스트 복구

- 계획된 복구

- Reverse

- 쿼리

- 테스트 정리

- 일시 중지

- 다시 시작

- 보호 제거

- 다시 구

JSON의 복구 작업 페이로드의 예는 다음과 같습니다.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

이러한 API를 사용하여 재해 복구 계획의 생성 및 실행을 자동화하는 사용자 지정 메커니즘을 빌드할 수 있습니다.
