---
title: Azure Site Recovery를 통해 재해 복구를 위한 VMware VM 사용
description: 이 문서에서는 Azure Site Recovery 서비스를 통해 재해 복구를 위해 VMware VM 복제를 사용하는 방법을 설명합니다.
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.author: sharrai
ms.date: 05/27/2021
ms.openlocfilehash: 4c25baba45431e2e7af6b9803337a5af051d75d2
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576097"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>VMware VM에 대해 Azure로의 복제를 사용하도록 설정

이 문서에서는 Azure로의 온-프레미스 VMware VM(가상 머신) 복제를 사용하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 시스템이 다음 조건을 충족한다고 가정합니다.

- [온-프레미스 원본 환경을 설정](vmware-azure-set-up-source.md)합니다.
- [Azure에서 대상 환경을 설정](vmware-azure-set-up-target.md)합니다.
- 시작하기 전에 [요구 사항 및 필수 구성 요소를 확인](vmware-physical-azure-support-matrix.md)합니다. 유의해야 할 중요한 사항은 다음과 같습니다.
  - 복제된 머신에서 [지원되는 운영 체제](vmware-physical-azure-support-matrix.md#replicated-machines).
  - [스토리지/디스크](vmware-physical-azure-support-matrix.md#storage) 지원.
  - 온-프레미스 머신에서 준수해야 하는 [Azure 요구 사항](vmware-physical-azure-support-matrix.md#azure-vm-requirements).

### <a name="resolve-common-issues"></a>일반적인 문제 해결

- 각 디스크는 4TB보다 작아야 합니다.
- 운영 체제 디스크는 동적 디스크가 아닌 기본 디스크여야 합니다.
- 2세대 UEFI 지원 가상 머신의 경우 운영 체제 제품군이 Windows이고 부팅 디스크는 300GB보다 작아야 합니다.

## <a name="before-you-start"></a>시작하기 전에

VMware 가상 머신을 복제하는 경우 다음 정보를 염두에 두어야 합니다.

- Azure 사용자 계정에 특정 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있어야 Azure에 새 가상 머신을 복제할 수 있습니다.
- VMware VM은 15분마다 검색됩니다. 검색 후 Azure Portal에 VM이 표시되려면 15분 이상 걸릴 수 있습니다. 새 vCenter 서버 또는 vSphere 호스트를 추가하는 경우 검색이 15분 이상 걸릴 수 있습니다.
- 포털에서 가상 머신의 환경 변경 내용을 업데이트하는 데 15분 이상 걸릴 수 있습니다. 예를 들어 VMware 도구 설치 같은 경우가 이에 해당합니다.
- VMware VM에 대해 마지막으로 검색한 시간을 확인할 수 있습니다. vCenter Server/vSphere 호스트에 대한 **구성 서버** 페이지의 **마지막 연락** 필드를 참조하세요.
- 예약된 검색을 기다리지 않고 복제에 대해 가상 머신을 추가하려면 구성 서버를 클릭하지 않은 상태에서 강조 표시한 후 **새로 고침** 을 선택합니다.
- 복제를 사용하는 경우 가상 머신이 준비되면 프로세스 서버가 자동으로 해당 VM에 Azure Site Recovery Mobility Service를 설치합니다.

## <a name="enable-replication"></a>복제 사용

이 섹션의 단계를 수행하기 전에 다음 정보를 검토해야 합니다.

- 이제 Azure Site Recovery는 모든 새 복제를 관리 디스크에 직접 복제합니다. 프로세스 서버는 대상 지역의 캐시 스토리지 계정에 복제 로그를 씁니다. 해당 로그는 명명 규칙이 `asrseeddisk`인 복제본 관리 디스크에 복구 지점을 만드는 데 사용됩니다.
- 관리 디스크로의 복제에 대한 PowerShell 지원은 [Az.RecoveryServices 모듈 버전 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)부터 사용할 수 있습니다.
- 장애 조치(failover) 시 선택한 복구 지점은 대상 관리 디스크를 만드는 데 사용됩니다.
- 이전에 대상 스토리지 계정에 복제하도록 구성된 VM은 영향을 받지 않습니다.
- 새 가상 머신에 대한 스토리지 계정으로 복제는 REST(Representational State Transfer) API 및 PowerShell을 통해서만 사용할 수 있습니다. 스토리지 계정으로 복제하려면 Azure REST API 버전 2016-08-10 또는 2018-01-10을 사용합니다.

복제를 사용하려면 다음 단계를 따릅니다.

1. **2단계: 애플리케이션 복제** > **원본** 으로 이동합니다. 처음으로 복제를 사용한 후 자격 증명 모음에서 **+복제** 를 선택하여 추가 가상 머신에 대해 복제를 사용합니다.
1. **원본** 페이지 > **원본** 에서 구성 서버를 선택합니다.
1. **머신 형식** 은 **가상 머신** 또는 **물리적 머신** 을 선택합니다.
1. **vCenter/vSphere 하이퍼바이저** 에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다. 이 설정은 물리적 컴퓨터를 복제하는 경우에는 관련이 없습니다.
1. 프로세스 서버를 선택합니다. 생성된 추가 프로세스 서버가 없는 경우 드롭다운 메뉴에서 구성 서버의 기본 제공 프로세스 서버를 사용할 수 있습니다. 각 프로세스 서버의 상태는 권장되는 제한 및 기타 매개 변수에 따라 표시됩니다. 정상 프로세스 서버를 선택합니다. [중요한](vmware-physical-azure-monitor-process-server.md#process-server-alerts) 프로세스 서버는 선택할 수 없습니다. 오류를 [해결](vmware-physical-azure-troubleshoot-process-server.md)**하거나** [스케일 아웃 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정할 수 있습니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="복제 사용 원본 창":::

   > [!NOTE]
   > [버전 9.24](site-recovery-whats-new.md)부터 프로세스 서버의 상태 경고를 향상하기 위해 추가 경고가 도입되었습니다. 모든 경고가 생성되도록 Site Recovery 구성 요소를 버전 9.24 이상으로 업그레이드하세요.

1. **대상** 으로는 장애 조치(failover)된 가상 머신을 만들 구독 및 리소스 그룹을 선택합니다. 장애 조치된 VM은 Azure에서 사용할 배포 모델을 선택합니다.
1. 장애 조치(failover) 후 Azure VM이 연결할 Azure 네트워크 및 서브넷을 선택합니다. 네트워크는 Site Recovery 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다.

   **선택한 머신에 대해 지금 구성** 을 선택하여 보호를 위해 선택한 모든 가상 머신에 네트워크 설정을 적용합니다. 가상 머신별로 Azure 네트워크를 선택하려면 **나중에 구성** 을 선택합니다. 네트워크가 없는 경우 만들어야 합니다. Azure Resource Manager를 사용하여 네트워크를 만들려면 **새로 만들기** 를 선택합니다. 해당하는 경우 서브넷을 선택하고 **확인** 을 선택합니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="복제 사용 대상 창":::

1. **가상 머신** > **가상 머신 선택** 에서 복제하려는 각 가상 머신을 선택합니다. 복제를 사용할 수 있는 가상 머신만 선택할 수 있습니다. 그런 다음, **확인** 을 선택합니다. 특정 가상 머신을 찾을 수 없거나 선택할 수 없는 경우 문제를 해결하려면 [원본 머신이 Azure Portal에 나열되지 않음](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication)을 참조하세요.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="복제 사용 가상 머신 선택 창":::

1. **속성** > **속성 구성** 에서 프로세스 서버가 VM에 Site Recovery Mobility Service를 자동으로 설치하는 데 사용하는 계정을 선택합니다. 또한 데이터 변동 패턴에 따라 복제에 사용할 대상 관리 디스크의 형식을 선택합니다.
1. 기본적으로 원본 VM의 모든 디스크가 복제됩니다. 복제에서 디스크를 제외하려면 복제하지 않을 디스크의 **포함** 확인란 선택을 취소합니다. 그런 다음, **확인** 을 선택합니다. 나중에 추가 속성을 설정할 수 있습니다. 디스크 제외에 대해 [자세히 알아보세요](vmware-azure-exclude-disk.md).

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="복제 사용 속성 구성 창":::

1. **복제 설정** > **복제 설정 구성** 에서 올바른 복제 정책이 선택되어 있는지 확인합니다. **설정** > **복제 정책** > ‘정책 이름’ > **설정 편집** 에서 복제 정책 설정을 수정할 수 있습니다. 정책에 적용된 변경 내용은 복제 및 새 가상 머신에도 적용됩니다.
1. 가상 머신을 복제 그룹으로 수집하려는 경우 **다중 VM 일관성** 을 사용합니다. 그룹 이름을 지정하고 **확인** 을 선택합니다.

   > [!NOTE]
   > - 복제 그룹의 가상 머신은 함께 복제되고 장애 조치(failover) 시 공유 크래시 일관성 및 앱 일관성 복구 지점을 갖습니다.
   > - 워크로드를 미러링하도록 VM 및 물리적 서버를 함께 수집합니다. 다중 VM 일관성을 사용하면 워크로드 성능에 영향을 줄 수 있습니다. 가상 머신이 동일한 워크로드를 실행하고 일관성이 필요한 경우에만 이 작업을 수행합니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="복제 사용 창":::

1. **복제 사용** 을 선택합니다. **설정** > **작업** > **Site Recovery 작업** 에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 가상 머신이 장애 조치(Failover)를 수행할 준비가 되어 있습니다.

## <a name="monitor-initial-replication"></a>초기 복제 모니터링

보호된 항목의 “복제 사용”이 완료되면 Azure Site Recovery는 원본 머신에서 대상 지역으로 데이터의 복제(동기화와 동의어)를 시작합니다. 이 기간에 원본 디스크의 복제본이 생성됩니다. 원본 디스크 복사가 완료된 후에만 델타 변경 내용이 대상 지역에 복사됩니다. 원본 디스크를 복사하는 데 걸리는 시간은 다음과 같은 여러 매개 변수에 따라 달라집니다.

- 원본 머신 디스크의 크기
- Azure로 데이터를 전송하는 데 사용할 수 있는 대역폭(배포 계획을 활용하여 필요한 최적의 대역폭을 확인할 수 있음)
- 메모리, 사용 가능한 디스크 공간, 보호된 항목에서 받은 데이터를 캐시 및 처리하는 데 사용할 수 있는 CPU와 같은 프로세스 서버 리소스(프로세스 서버가 [정상](vmware-physical-azure-monitor-process-server.md#monitor-proactively)인지 확인)

초기 복제의 진행 상태를 추적하려면 Azure Portal -> 복제된 항목 -> 복제된 항목의 “상태” 열 값 모니터링에서 복구 서비스 자격 증명 모음으로 이동합니다. 상태에는 초기 복제의 완료율이 표시됩니다. 상태를 가리키면 “총 전송된 데이터”를 확인할 수 있습니다. 상태를 클릭하면 상황별 페이지가 열리고 다음 매개 변수가 표시됩니다.

- 마지막 새로 고침 시간 - 서비스가 전체 머신의 복제 정보를 새로 고친 마지막 시간을 나타냅니다.
- 완료율 - VM에 대해 완료된 초기 복제의 비율을 나타냅니다.
- 총 전송된 데이터 - VM에서 Azure로 전송된 데이터의 양을 나타냅니다.

    :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-state.png" alt-text="복제 상태" lightbox="media/vmware-azure-enable-replication/initial-replication-state.png":::

- 동기화 진행률(디스크 수준에서 세부 정보 추적하기 위해)
    - 복제 상태
      - 복제를 아직 시작하지 않은 경우 상태는 “대기 중”으로 업데이트됩니다. 초기 복제 중에는 한 번에 3개의 디스크만 복제됩니다. 프로세스 서버에서 제한을 방지하기 위해 이 메커니즘을 따릅니다.
      - 복제가 시작되면 상태가 “진행 중”으로 업데이트됩니다.
      - 초기 복제가 완료되면 상태가 “완료”로 표시됩니다.        
   - Site Recovery는 원본 디스크를 읽고, 데이터를 Azure로 전송하고, 디스크 수준에서 진행률을 캡처합니다. Site Recovery는 사용되지 않은 디스크 크기에 대한 복제를 건너뛰고 이를 완료된 데이터에 추가합니다. 따라서 모든 디스크에서 전송된 데이터의 합계는 VM 수준에서 “총 전송된 데이터”에 추가되지 않을 수 있습니다.
   - 디스크에 대한 정보 풍선을 클릭하면 디스크에 대해 복제(동기화와 동의어)가 트리거된 시기, 마지막 15분 동안 Azure로 전송된 데이터, 마지막으로 새로 고친 시간 스탬프에 대한 세부 정보를 얻을 수 있습니다. 이 시간 스탬프는 원본 머신에서 Azure 서비스가 정보를 받은 마지막 시간을 나타냅니다. :::image type="content" source="media/vmware-azure-enable-replication/initial-replication-info-balloon.png" alt-text="초기 복제 정보 풍선 세부 정보" lightbox="media/vmware-azure-enable-replication/initial-replication-info-balloon.png":::
   - 각 디스크의 상태가 표시됨
      - 복제가 예상보다 느린 경우 디스크 상태가 경고로 바뀝니다.
      - 복제가 진행되고 있지 않으면 디스크 상태가 위험으로 바뀝니다.

상태가 위험/경고 상태인 경우 머신 및 [프로세스 서버](vmware-physical-azure-monitor-process-server.md)의 복제 상태가 정상인지 확인합니다. 

복제를 사용하는 작업이 완료되면 복제 진행률은 즉시 0%가 되며 총 전송된 데이터는 NA가 됩니다. 클릭하면 식별된 각 디스크에 대한 데이터가 “NA”가 됩니다. 이는 복제를 아직 시작하지 않았으며 Azure Site Recovery가 아직 최신 통계를 수신하지 못했음을 나타냅니다. 진행률은 30분 간격으로 새로 고침됩니다.

> [!NOTE]
> 정확한 진행률이 캡처되고 Site Recovery 서비스로 전송되도록 구성 서버, 스케일 아웃 프로세스 서버 및 모바일 에이전트를 버전 9.36 이상으로 업데이트해야 합니다.


## <a name="view-and-manage-vm-properties"></a>VM 속성 보기 및 관리

다음으로 원본 가상 머신의 속성을 확인합니다. Azure VM 이름이 [Azure 가상 머신 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)을 준수해야 합니다.

1. **설정** > **복제된 항목** 으로 이동하고 가상 머신을 선택합니다. **필수** 페이지는 VM 설정 및 상태에 대한 정보를 표시합니다.
1. **속성** 에서 해당 VM에 대한 복제 및 장애 조치(failover) 정보를 볼 수 있습니다.
1. **컴퓨팅 및 네트워크** > **컴퓨팅 속성** 에서 여러 VM 속성을 변경할 수 있습니다.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="컴퓨팅 및 네트워크 속성 창":::

   - **Azure VM 이름**: 필요한 경우 Azure 요구 사항을 충족하도록 이름을 수정합니다.
   - **대상 VM 크기 또는 VM 유형**: 기본 VM 크기는 대상 Azure 지역에서 디스크 수, NIC 수, CPU 코어 수, 메모리 및 사용 가능한 VM 역할 크기를 포함하는 매개 변수를 기반으로 선택됩니다. Azure Site Recovery는 모든 조건을 충족하는 사용 가능한 첫 번째 VM 크기를 선택합니다. 장애 조치(failover) 전에 언제든지 필요에 따라 다른 VM 크기를 선택할 수 있습니다. VM 디스크 크기도 원본 디스크 크기를 기반으로 합니다. VM 디스크 크기는 장애 조치 후에만 변경할 수 있습니다. [VM 디스크의 확장성 및 성능 목표](../virtual-machines/disks-scalability-targets.md)에서 디스크 크기 및 IOPS 속도에 대해 자세히 알아보세요.
   - **리소스 그룹**: 사후 장애 조치(failover)에 포함되는 가상 머신이 속한 [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups)을 선택할 수 있습니다. 이 설정은 장애 조치 전에 언제든지 변경할 수 있습니다. 장애 조치 후, 가상 머신을 다른 리소스 그룹으로 마이그레이션하는 경우 해당 가상 머신의 보호 설정이 중단됩니다.
   - **가용성 세트**: 가상 머신이 사후 장애 조치(failover)에 포함되어야 하는 경우 [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md)을 선택할 수 있습니다. 가용성 집합을 선택할 때 다음 정보를 염두에 두어야 합니다.
     - 지정된 리소스 그룹에 속하는 가용성 집합만 나열됩니다.
     - 서로 다른 가상 네트워크에 있는 VM은 동일한 가용성 집합에 속할 수 없습니다.
     - 동일한 크기의 가상 머신만 가용성 집합의 일부가 될 수 있습니다.

1. 또한 대상 네트워크, 서브넷, Azure VM에 할당된 IP 주소에 대한 정보를 추가할 수 있습니다.
1. **디스크** 에서 복제될 VM의 운영 체제 및 데이터 디스크를 볼 수 있습니다.

### <a name="configure-networks-and-ip-addresses"></a>네트워크 및 IP 주소 구성

대상 IP 주소를 설정할 수 있습니다.

- 주소를 입력하지 않으면 장애 조치(failover)된 VM이 DHCP를 사용합니다.
- 장애 조치(failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(failover)가 작동하지 않습니다.
- 주소를 테스트 장애 조치(failover) 네트워크에서 사용할 수 있는 경우 테스트 장애 조치에 동일한 대상 IP 주소를 사용할 수 있습니다.

네트워크 어댑터 수는 다음과 같이 대상 가상 머신에 대해 지정하는 크기에 따라 결정됩니다.

- 원본 가상 머신의 네트워크 어댑터 수가 대상 VM 크기에 허용되는 어댑터 수보다 적거나 같은 경우 대상의 어댑터 수는 원본과 동일해야 합니다.
- 원본 가상 머신의 어댑터 수가 대상 VM 크기에 허용된 수를 초과하면 대상 크기 최대치가 사용됩니다. 예를 들어 원본 가상 머신에 2개의 네트워크 어댑터가 있고 대상 VM 크기가 4개를 지원하는 경우 대상 가상 머신에는 2개의 어댑터가 있습니다. 원본 VM에 2개의 어댑터가 있지만 대상 크기에서 1개만 지원하는 경우 대상 VM에는 1개의 어댑터만 있습니다.
- 가상 머신에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다. 또한 목록에서 표시되는 첫 번째 어댑터는 Azure 가상 머신의 기본 네트워크 어댑터가 됩니다.

### <a name="azure-hybrid-benefit"></a>Azure 하이브리드 혜택

Microsoft Software Assurance 고객은 Azure 하이브리드 혜택을 사용하여 Azure로 마이그레이션되는 Windows Server 컴퓨터에 대한 라이선스 비용을 절감할 수 있습니다. 이 이점은 Azure 재해 복구에도 적용됩니다. 이 이점을 받을 자격이 있다면 장애 조치(failover)가 있는 경우 Site Recovery가 만드는 가상 머신에 이 이점을 할당할 수 있습니다.

1. 복제된 가상 머신의 **컴퓨터 및 네트워크 속성** 으로 이동합니다.
1. Azure 하이브리드 혜택을 받을 수 있는 Windows Server 라이선스가 있는지 묻는 질문에 답변합니다.
1. 장애 조치(failover) 시 만들어지는 VM에 이점을 적용하는 데 사용할 수 있는 Software Assurance가 포함된 적절한 Windows Server 라이선스가 있는지 확인합니다.
1. 복제된 가상 머신에 대한 설정을 저장합니다.

Azure 하이브리드 혜택에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="next-steps"></a>다음 단계

가상 머신이 보호된 상태에 도달한 후 [장애 조치(failover)](site-recovery-failover.md)를 시도하여 애플리케이션이 Azure에 표시되는지 확인합니다.

- 복제를 사용하지 않기 위해 등록 및 보호 설정을 정리하는 방법에 대해 [자세히 알아보세요](site-recovery-manage-registration-and-protection.md).
- PowerShell을 사용하여 가상 머신의 복제를 자동화하는 방법에 대해 [자세히 알아보세요](vmware-azure-disaster-recovery-powershell.md).
