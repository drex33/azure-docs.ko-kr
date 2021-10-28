---
title: Azure Site Recovery를 통해 Azure VM을 보조 지역으로 장애 조치(failover)하여 재해 복구하는 방법에 대한 자습서입니다.
description: Azure Site Recovery 서비스를 사용하여 재해 복구를 위해 보조 Azure 지역에 복제된 Azure VM을 장애 조치(failover)하고 다시 보호하는 방법에 대한 자습서입니다.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 692b6645b2446fa55233506a0479b95b0743b029
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223161"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>자습서: Azure VM을 보조 지역으로 장애 조치(failover)

[Azure Site Recovery](site-recovery-overview.md)를 사용하여 재해 복구가 설정된 Azure VM을 보조 Azure 지역으로 장애 조치(failover)하는 방법을 알아봅니다. 장애 조치(failover) 후에는 다시 주 지역으로 복제될 수 있도록 대상 지역에서 VM을 다시 보호합니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 필수 구성 요소 확인
> * VM 설정 확인
> * 보조 지역으로 장애 조치(failover) 실행
> * VM을 다시 주 지역으로 복제하기 시작합니다.


> [!NOTE]
> 이 자습서에서는 최소한의 단계로 VM을 장애 조치(failover)하는 방법을 보여줍니다. 전체 설정으로 장애 조치(failover)를 실행하려면 Azure VM [네트워킹](azure-to-azure-about-networking.md), [자동화](azure-to-azure-powershell.md) 및 [문제 해결](azure-to-azure-troubleshoot-errors.md)에 대해 알아보세요.



## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하려면 앞에서 다음 단계를 수행했어야 합니다.

1. 하나 이상의 Azure VM에 대해 복제를 설정합니다. 아직 설정하지 않았다면 이 시리즈의 [첫 번째 자습서를 완료](azure-to-azure-tutorial-enable-replication.md)하여 설정합니다.
2. 복제된 VM에 대해 [재해 복구 연습을 실행](azure-to-azure-tutorial-dr-drill.md)하는 것이 좋습니다. 전체 장애 조치(failover)를 실행하기 전에 연습을 실행하면 프로덕션 환경에 영향을 주지 않고 모든 것이 예상대로 작동하게 만드는 데 도움이 됩니다. 


## <a name="verify-the-vm-settings"></a>VM 설정 확인

1. 자격 증명 모음 > **복제된 항목** 에서 VM을 선택합니다.

    ![[개요] 페이지에서 VM 속성을 여는 옵션](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. VM **개요** 페이지에서 VM이 보호되고 있으며 정상 상태인지 확인한 후 장애 조치(failover)를 실행합니다.
    ![VM 속성 및 상태를 확인하는 페이지](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. 장애 조치(failover) 전에 다음을 확인합니다.
    - VM이 지원되는 [Windows](azure-to-azure-support-matrix.md#windows) 또는 [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) 운영 체제를 실행하고 있습니다.
    - VM이 [컴퓨팅](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [스토리지](azure-to-azure-support-matrix.md#replicated-machines---storage) 및 [네트워킹](azure-to-azure-support-matrix.md#replicated-machines---networking) 요구 사항을 준수합니다.

## <a name="run-a-failover"></a>장애 조치(Failover) 실행


1. VM **개요** 페이지에서 **장애 조치(failover)** 를 선택합니다.

    ![복제된 항목의 장애 조치(failover) 단추](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. **장애 조치(failover)** 에서 복구 지점을 선택합니다. 대상 지역의 Azure VM은 이 복구 지점의 데이터를 사용하여 생성됩니다.
  
   - **가장 최근에 처리됨**: Site Recovery에서 처리한 최신 복구 지점을 사용합니다. 타임스탬프가 표시됩니다. 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 목표 시간)가 제공됩니다.
   -  **최신**: Site Recovery로 전송된 모든 데이터를 처리하여 각 VM의 복구 지점을 만든 후에 해당 복구 지점으로 장애 조치(failover)합니다. 장애 조치(failover)가 트리거될 때 모든 데이터가 Site Recovery로 복제되므로 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
   - **최신 앱 일치**: 이 옵션은 VM을 최신 앱 일치 복구 시점으로 장애 조치(failover)합니다. 타임스탬프가 표시됩니다.
   - **Custom**: 특정 복구 지점으로 장애 조치(failover)합니다. 사용자 지정은 단일 VM을 장애 조치(failover)할 때만 사용할 수 있으며, 복구 계획을 사용하지 않습니다.

    > [!NOTE]
    > 복제를 사용하도록 설정한 후 VM에 디스크를 추가한 경우 복제 지점은 복구에 사용할 수 있는 디스크를 표시합니다. 예를 들어 두 번째 디스크를 추가하기 전에 만든 복제 지점은 "2개 중 1개 디스크"를 표시합니다.

4. 장애 조치(Failover)를 시작하기 전에 Site Recovery에서 원본 VM을 종료하도록 하려면 **장애 조치(Failover)를 시작하기 전에 머신을 종료합니다** 를 선택합니다. 종료하여 데이터 손실을 방지할 수 있습니다. 종료가 실패하더라도 장애 조치는 계속됩니다. 

    ![장애 조치(failover) 설정 페이지](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. 장애 조치(failover)를 시작하려면 **확인** 을 선택합니다.
4. 알림에서 장애 조치(failover)를 모니터링합니다.

    ![진행률 알림](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![성공 알림](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. 장애 조치(failover) 후 대상 지역에 생성된 Azure VM은 **Virtual Machines** 에 표시됩니다. VM이 실행 중이고 적절히 크기가 조정되었는지 확인합니다. VM에 다른 복구 지점을 사용하려면 **기본 정보** 페이지에서 **복구 지점 변경** 을 선택합니다.
6. 장애 조치(failover)된 VM에 만족하는 경우 [개요] 페이지에서 **커밋** 을 선택하여 장애 조치(failover)를 완료합니다.

    ![[커밋] 단추](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. **커밋** 에서 **확인** 을 선택하여 확인합니다. 커밋은 Site Recovery에서 VM에 사용 가능한 복구 지점을 모두 삭제합니다. 그러면 복구 지점을 변경할 수 없습니다.

8. 알림에서 커밋 진행률을 모니터링합니다.

    ![커밋 진행률 알림](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![커밋 성공 알림](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

## <a name="reprotect-the-vm"></a>VM 다시 보호

장애 조치(failover) 후 보조 지역에서 VM을 다시 보호해야만 주 지역으로 다시 복제됩니다. 

1. 시작하기 전에 VM **상태** 가 *장애 조치(Failover) 커밋됨* 인지 확인합니다.
2. 주 지역에 액세스할 수 있는지, 그리고 주 지역에 VM을 만들 수 있는 권한이 있는지 확인합니다.
3. VM **개요** 페이지에서 **다시 보호** 를 선택합니다.

   ![VM을 다시 보호하도록 설정하는 단추](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. **다시 보호** 에서 복제 방향(보조 지역에서 주 지역으로)을 확인하고, 주 지역의 대상 설정을 검토합니다. 신규로 표시된 리소스는 다시 보호 작업의 일환으로 Site Recovery에서 만든 것입니다.

     ![다시 보호 설정 페이지](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. **확인** 을 선택하여 다시 보호 프로세스를 시작합니다. 이 프로세스는 초기 데이터를 대상 위치로 보낸 다음, VM의 델타 정보를 대상에 복제합니다.
7. 알림에서 다시 보호 진행률을 모니터링합니다. 

    ![다시 보호 진행률 알림](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![다시 보호 성공 알림](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>다음 단계

이 자습서에서는 VM을 주 지역에서 보조 지역으로 장애 조치(failover)하고, 다시 주 지역으로 복제하기 시작했습니다. 이제 보조 지역에서 주 지역으로 장애 복구(failback) 할 수 있습니다.

> [!div class="nextstepaction"]
> [주 지역으로 장애 복구(failback)](azure-to-azure-tutorial-failback.md)
