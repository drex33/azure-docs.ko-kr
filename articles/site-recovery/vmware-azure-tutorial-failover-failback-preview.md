---
title: Site Recovery를 사용하여 VMware VM을 Azure로 장애 조치(failover) - 미리 보기
description: Azure Site Recovery에서 VMware VM을 Azure로 장애 조치(failover)하는 방법 알아보기 - 미리 보기
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: 42ccd96287fefed0a6c7ef4cee8ddf776541c8ba
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122447140"
---
# <a name="fail-over-vmware-vms---preview"></a>VMware VM 장애 조치(failover) - 미리 보기

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) - 미리 보기를 사용하여 온-프레미스 VMware VM(가상 머신)을 Azure로 장애 조치(failover)하는 방법을 설명합니다.

클래식 릴리스의 장애 조치(failover)에 대한 자세한 내용은 [이 문서](vmware-azure-tutorial-failover-failback.md)를 참조하세요.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * VMware VM 속성이 Azure 요구 사항을 준수하는지 확인합니다.
> * 특정 VM을 Azure로 장애 조치(failover)합니다.

> [!NOTE]
> 자습서는 시나리오에 맞는 가장 간단한 배포 경로를 보여줍니다. 가능한 경우 기본 옵션을 사용하고 가능한 모든 설정과 경로를 보여주지 않습니다. 장애 조치(failover)에 대해 자세히 알아보려면 [VM 및 물리적 서버 장애 조치(failover)](site-recovery-failover.md)를 참조하세요.

다양한 유형의 장애 조치(failover)에 [대해 알아봅니다](failover-failback-overview.md#types-of-failover). 복구 계획에서 여러 VM을 장애 조치(failover)하려는 경우 [이 문서](site-recovery-failover.md)를 검토하세요.

## <a name="before-you-start"></a>시작하기 전에

이전 자습서 완료:

1. VMware VM을 온-프레미스에 재해 복구하도록 [Azure를 설정](tutorial-prepare-azure.md)했는지 확인합니다.
2. 재해 복구용 온-프레미스 [VMware](vmware-azure-tutorial-prepare-on-premises.md) 환경을 준비합니다.
3. [VMware VM](vmware-azure-set-up-replication-tutorial-preview.md)에 대한 재해 복구를 설정합니다.
4. [재해 복구 훈련](tutorial-dr-drill-azure.md)을 실행하여 모든 항목이 예상대로 작동하는지 확인합니다.

## <a name="verify-vm-properties"></a>VM 속성 확인

장애 조치(failover)를 실행하기 전에 먼저 VM 속성을 확인하여 해당 VM이 [Azure 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)을 충족하는지 검토합니다.

다음과 같이 속성을 확인합니다.

1. **보호된 항목** 에서 **복제된 항목** 을 선택하고 확인하려는 VM을 선택합니다.

2. **복제된 항목** 창에 VM 정보, 상태 및 최신 사용 가능한 복구 지점의 요약이 제공됩니다. 자세한 내용을 보려면 **속성** 을 선택합니다.

3. **컴퓨팅 및 네트워크** 에서 필요에 따라 이러한 속성을 수정할 수 있습니다.
    * Azure 이름
    * Resource group
    * 대상 크기
    * Managed Disks 설정

4. 다음을 비롯한 네트워크 설정을 보고 수정할 수 있습니다.

    * 장애 조치(failover) 후에 Azure VM이 배치될 네트워크 및 서브넷
    * 여기에 할당될 IP 주소

5. **디스크** 에서 VM의 운영 체제 및 데이터 디스크에 대한 정보를 볼 수 있습니다.

## <a name="run-a-failover-to-azure"></a>Azure에 대한 장애 조치(Failover) 실행

1. **설정** > **복제된 항목** 에서 장애 조치(failover)하려는 VM을 선택한 후 **장애 조치(failover)** 를 선택합니다.
2. **장애 조치(Failover)** 에서 장애 조치(failover)할 **복구 지점** 을 선택합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
   * **최신**: 이 옵션은 먼저 Site Recovery로 전송된 모든 데이터를 처리합니다. 장애 조치(failover) 후에 생성된 Azure VM은 장애 조치(failover)가 트리거되었을 때 Site Recovery로 복제된 모든 데이터를 보유하므로, 이 옵션은 가장 낮은 RPO(복구 목표 시점)를 제공합니다.
   * **가장 최근에 처리됨**: 이 옵션은 VM을 Site Recovery에서 처리된 최신 복구 지점으로 장애 조치(failover)합니다. 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로, 이 옵션은 낮은 RTO(복구 목표 시간)를 제공합니다.
   * **최신 앱 일치**: 이 옵션은 VM을 Site Recovery에서 처리된 최신 앱 일치 복구 지점으로 장애 조치(failover)합니다.
   * **Custom**: 이 옵션을 통해 복구 지점을 지정할 수 있습니다.

3. 장애 조치를 트리거하기 전에 원본 VM을 종료하려고 시도하려면 **장애 조치(failover)를 시작하기 전에 머신을 종료합니다.** 를 선택합니다. 종료가 실패하더라도 장애 조치(failover)는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.

  일부 시나리오에서는 장애 조치(failover)를 위해서는 추가 처리가 필요하며 이러한 작업을 완료하는 데는 약 8~10분이 소요됩니다. 다음에 대한 테스트 장애 조치(failover) 시간이 길어질 수 있습니다.

  * VMware Linux VM
  * DHCP 서비스를 사용하도록 설정되지 않은 VMware VM
  * 다음 부팅 드라이버가 없는 VMware VM: storvsc, vmbus, storflt, intelide, atapi.

  > [!WARNING]
  > 진행 중인 장애 조치(Failover)는 취소하지 마세요. 장애 조치(failover)를 시작하기 전에 VM 복제가 중지됩니다. 진행 중인 장애 조치(failover)를 취소하면 장애 조치(failover)가 중지되지만 VM은 다시 복제되지 않습니다.

## <a name="connect-to-failed-over-vm"></a>장애 조치(failover)된 VM에 연결

1. 장애 조치(failover) 후에 RDP(원격 데스크톱 프로토콜) 및 SSH(보안 셸)를 사용하여 Azure VM에 연결하려는 경우 [요구 사항이 충족되었는지 확인](failover-failback-overview.md#connect-to-azure-after-failover)합니다.
2. 장애 조치(failover) 후 VM으로 이동하고 [연결](../virtual-machines/windows/connect-logon.md)하여 유효성을 검사합니다.
3. 장애 조치(failover) 후 다른 복구 지점을 사용하려면 **복구 지점 변경** 을 사용합니다. 다음 단계에서 장애 조치(failover)를 커밋하면 이 옵션을 더 이상 사용할 수 없습니다.
4. 유효성 검사 후 **커밋** 을 선택하여 장애 조치(failover) 후 VM의 복구 지점을 마무리합니다.
5. 커밋 후 다른 사용 가능한 복구 지점을 모두 삭제합니다. 이 단계를 수행하면 장애 조치(failover)가 완료됩니다.

>[!TIP]
> 장애 조치(failover) 후 연결 문제가 발생하는 경우 [문제 해결 가이드](site-recovery-failover-to-azure-troubleshoot.md)를 따릅니다.

## <a name="planned-failover-from-azure-to-on-premises"></a>Azure에서 온-프레미스로 계획된 장애 조치(Failover)

Azure에서 온-프레미스로 계획된 장애 조치(failover)를 수행할 수 있습니다. 계획된 장애 조치(failover) 작업이므로 계획된 장애 조치(failover) 작업이 트리거된 후에 복구 지점이 생성됩니다.

>[!NOTE]
> 계속하기 전에, 머신의 복제 상태가 정상인지 확인합니다. 또한 어플라이언스 및 모든 구성 요소도 정상인지 확인합니다.

계획된 장애 조치(failover)가 트리거되면 보류 중인 변경 내용이 온-프레미스에 복사되고, VM의 최신 복구 지점이 생성되고 Azure VM이 종료됩니다. 그 후 온-프레미스 머신이 켜집니다.

계획된 장애 조치(failover)가 성공적으로 끝나면 머신이 온-프레미스 환경에서 활성화됩니다.

> [!NOTE]
> 보호된 머신에 iSCSI 디스크가 있는 경우 장애 조치(failover) 시 Azure에서 구성이 유지됩니다. Azure에서 온-프레미스로 계획된 장애 조치(failover)가 완료된 후에는 iSCSI 구성을 유지할 수 없습니다. 따라서 vmdk 디스크는 온-프레미스 머신에 만들어집니다. 중복 디스크를 제거하려면 데이터가 vmdk 디스크로 대체될 때 iSCSI 디스크를 삭제합니다.


### <a name="failed-over-vm-to-azure---requirements"></a>VM을 Azure로 장애 조치(fail over) - 요구 사항

VM이 Azure로 장애 조치(failover)된 후 다음을 확인합니다.

1. Azure의 VM은 항상 켜져 있어야 합니다.
2. 모바일 에이전트 서비스 *service 1* 및 *service 2* 가 VM에서 실행되어야 합니다. 그래야만 VM의 모바일 에이전트가 Azure의 Azure Site Recovery 서비스와 통신할 수 있습니다.
3. VM에서 [여기](vmware-azure-architecture-preview.md#set-up-outbound-network-connectivity)에 언급된 URL에 액세스할 수 있어야 합니다.

## <a name="cancel-planned-failover"></a>계획된 장애 조치(Failover) 취소

온-프레미스 환경이 준비되지 않았거나 문제가 있는 경우 계획된 장애 조치(failover)를 취소할 수 있습니다. 나중에 언제든지 온-프레미스 조건이 회복될 때 계획된 장애 조치(failover)를 수행할 수 있습니다.

**계획된 장애 조치(failover)를 취소** 하려면 다음을 수행합니다.

1. 복구 서비스 자격 증명 모음의 머신으로 이동하여 **장애 조치(failover) 취소** 를 선택합니다.
2. **확인** 을 클릭합니다.
3. *장애 조치(failover) 취소* 작업이 진행되는 방식에 대한 정보를 읽습니다.

Azure Site Recovery가 실패한 작업을 취소하지 못하게 방해하는 문제가 있는 경우 작업에 제공된 권장 단계를 수행합니다. 권장 작업을 수행한 후, 취소 작업을 다시 시도합니다.

이전에 계획된 장애 조치(failover) 작업이 취소됩니다. Azure의 머신은 *계획된 장애 조치(failover)* 가 트리거되기 직전 상태로 돌아갑니다.

계획된 장애 조치(failover)의 경우 어플라이언스에서 VM 디스크를 분리한 후, 전원을 켜기 전에 스냅샷을 생성합니다.

VM이 제대로 부팅되지 않거나 일부 애플리케이션이 제대로 나타나지 않거나, 어떤 이유로 계획된 장애 조치(failover)를 취소하고 다시 시도하기로 결정한 경우 다음을 수행합니다.

1. 모든 변경 내용을 되돌립니다.

2. 이전에 생성한 스냅샷을 사용하여 전원을 공급하기 전과 동일한 상태로 디스크를 되돌립니다.

3. 마지막으로, 디스크를 어플라이언스에 다시 연결하고 복제를 다시 시작합니다.

이 동작은 레거시/클래식 아키텍처의 동작과 다릅니다.

- 미리 보기를 사용하면 나중에 장애 조치(failback) 작업을 다시 수행할 수 있습니다.

- 레거시 아키텍처에서는 VM이 부팅되지 않거나, 애플리케이션이 나타나지 않거나, 그 밖의 이유로 인해 장애 조치(failback)를 취소하고 다시 시도할 수 없습니다.  


> [!NOTE]
> Azure에서 온-프레미스로의 계획된 장애 조치(failover)만 취소할 수 있습니다. 온-프레미스에서 Azure로의 장애 조치(failover)는 취소할 수 없습니다.

### <a name="planned-failover---failure"></a>계획된 장애 조치(failover) - 실패

계획된 장애 조치(failover)가 실패하면 Azure Site Recovery는 실패한 작업을 취소하는 작업을 자동으로 시작하고 계획된 장애 조치(failover) 직전의 머신 상태를 검색합니다.

마지막으로 계획된 장애 조치(failover) 작업의 취소가 실패하면 Azure Site Recovery는 취소를 수동으로 시작하라는 메시지를 표시합니다.

이 정보는 계획된 장애 조치(failover) 작업이 실패하고 복제된 항목의 상태 문제가 있을 때 제공됩니다.

문제가 지속되면 Microsoft 지원에 문의하세요. 복제를 사용하지 않도록 **설정하지 마세요**.

## <a name="re-protect-the-on-premises-machine-to-azure-after-successful-planned-failover"></a>계획된 장애 조치(failover)가 성공한 후 온-프레미스 머신을 Azure로 다시 보호

계획된 장애 조치(failover)가 성공하면 머신이 온-프레미스에서 활성화됩니다. 나중에 머신을 보호하려면 머신이 Azure에 복제(다시 보호)되도록 합니다.

이렇게 하려면 머신 > **다시 보호** 로 이동하고, 원하는 어플라이언스를 선택하고, 복제 정책을 선택하고 계속 진행합니다.

복제 및 초기 복제를 사용하도록 설정하면 원치 않는 중단을 방지하고 비즈니스 연속성을 제공하기 위한 복구 지점이 생성됩니다.

## <a name="next-steps"></a>다음 단계

장애 조치(failover) 후 Azure VM을 온-프레미스로 다시 보호합니다. VM이 다시 보호되고 온-프레미스 사이트로 복제되는 것을 확인한 후, 준비가 되면 Azure에서 장애 복구를 수행합니다.

> [!div class="nextstepaction"]
> [Azure VM 다시 보호](vmware-azure-reprotect.md)
> [Azure에서 장애 복구(failback)](vmware-azure-failback.md)
