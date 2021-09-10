---
title: 자습서 - Azure Site Recovery를 사용하여 Linux VM에 대한 재해 복구 설정
description: Azure Site Recovery 서비스를 사용하여 다른 Azure 지역에 Linux VM의 재해 복구를 설정하는 방법을 알아봅니다.
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: linux
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: ff3c4dc893a6760c317b39b4a76f4de2e24da96e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690907"
---
# <a name="tutorial-set-up-disaster-recovery-for-linux-virtual-machines"></a>자습서: Linux 가상 머신에 대한 재해 복구 설정

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 자습서에서는 Linux를 실행하는 Azure VM에 대한 재해 복구를 설정하는 방법을 보여 줍니다. 이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Linux VM에 재해 복구 사용
> * 재해 복구 훈련을 실행하여 예상대로 작동하는지 확인
> * 훈련 후 VM 복제 중지

VM에 복제를 사용하도록 설정하면 Site Recovery Mobility Service 확장이 VM에 설치되고 [Azure Site Recovery](../../site-recovery/site-recovery-overview.md)에 등록됩니다. 복제하는 동안 VM 디스크 쓰기는 원본 VM 지역의 캐시 스토리지 계정으로 전송됩니다. 여기에서 대상 지역으로 데이터가 전송되고 데이터에서 복구 지점이 생성됩니다.  재해 복구 중에 VM을 다른 지역으로 장애 조치(failover)하는 경우 복구 지점은 VM을 대상 지역에 만드는 데 사용됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

1. Azure 구독을 통해 대상 지역에 VM을 만들 수 있는지 확인합니다. Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 관리자이며 필요한 권한을 보유하고 있습니다.
2. 구독 관리자가 아닌 경우에는 관리자와 협력하여 다음 권한을 할당받습니다.
    - 기본 제공되는 Virtual Machine 기여자 역할 또는 다음에 대한 특정 권한:
        - 선택한 가상 네트워크에 VM 만들기
        - Azure Storage 계정에 씁니다.
        - Azure 관리 디스크에 씁니다.
     - 자격 증명 모음에서 Site Recovery 작업을 관리하기 위한 Site Recovery 기여자 기본 제공 역할입니다. 
3. Linux VM에서 [지원되는 운영 체제](../../site-recovery/azure-to-azure-support-matrix.md#linux)를 실행하고 있는지 확인합니다.
4. VM 아웃바운드 연결이 URL 기반 프록시를 사용하는 경우 이러한 URL에 액세스할 수 있는지 확인합니다. 인증된 프록시는 사용은 지원되지 않습니다.

    **이름** | **퍼블릭 클라우드** | **정부 클라우드** | **세부 정보**
    --- | --- | --- | ---
    Storage | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| VM에서 원본 지역의 캐시 스토리지 계정에 데이터를 씁니다. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Site Recovery 서비스 URL에 대한 권한을 부여하고 인증합니다. 
    복제 | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |VM이 Site Recovery 서비스와 통신합니다. 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | VM은 모니터링 및 진단 데이터에 대한 Site Recovery에 기록합니다. 

4. NSG(네트워크 보안 그룹)를 사용하여 VM에 대한 네트워크 트래픽을 제한하는 경우 이러한 서비스 태그(IP 주소 그룹)를 사용하여 VM에 대한 아웃바운드 연결(HTTPS 443)을 허용하는 NSG 규칙을 만듭니다. 먼저 테스트 NSG에서 규칙을 사용해 보세요.

    **Tag** | **허용** 
    --- | --- 
    스토리지 태그 | VM에서 캐시 스토리지 계정에 데이터를 쓸 수 있도록 합니다.
    Azure AD 태그 | Azure AD에 해당하는 모든 IP 주소에 대한 액세스를 허용합니다.
    EventsHub 태그 | Site Recovery 모니터링에 대한 액세스를 허용합니다.
    AzureSiteRecovery 태그 | 모든 지역에서 Site Recovery 서비스에 대한 액세스를 허용합니다.
    GuestAndHybridManagement | 복제를 사용하도록 설정된 VM에서 실행 중인 Site Recovery Mobility 에이전트를 자동으로 업그레이드하려는 경우에 사용합니다.
5. VM에 최신 루트 인증서가 있는지 확인합니다. Linux VM의 경우 Linux 배포자가 제공한 지침에 따라 VM에서 신뢰할 수 있는 최신 루트 인증서 및 인증서 해지 목록을 가져옵니다.

## <a name="create-a-vm-and-enable-disaster-recovery"></a>VM 만들기 및 재해 복구 사용

VM을 만드는 경우 필요에 따라 재해 복구를 사용하도록 설정할 수 있습니다.

1. [Linux VM을 만듭니다](quick-create-portal.md).
2. **관리** 탭의 **Site Recovery** 아래에서 **재해 복구 사용** 을 선택합니다.
3. **보조 지역** 에서 재해 복구를 위해 VM을 복제하려는 대상 지역을 선택합니다.
4. **보조 구독** 에서 대상 VM이 만들어질 대상 구독을 선택합니다. 대상 VM은 원본 VM을 원본 지역에서 대상 지역으로 장애 조치(failover)할 때 만들어집니다.
5. **Recovery Services 자격 증명 모음** 에서 복제에 사용하려는 자격 증명 모음을 선택합니다. 자격 증명 모음이 없는 경우 **새로 만들기** 를 선택합니다. 자격 증명 모음을 배치할 리소스 그룹 및 자격 증명 모음 이름을 선택합니다.
6. **Site Recovery 정책** 에서 기본 정책을 그대로 유지하거나 **새로 만들기** 를 선택하여 사용자 지정 값을 설정합니다.

    - 복구 지점은 특정 시점에 생성되는 VM 디스크의 스냅샷에서 생성됩니다. VM을 장애 조치(failover)하는 경우 복구 지점을 사용하여 대상 지역에서 VM을 복원합니다. 
    - 크래시 일치 복구 지점이 5분마다 만들어집니다. 이 설정은 수정할 수 없습니다. 크래시 일치 스냅샷은 스냅샷이 만들어질 때 디스크에 있던 데이터를 캡처합니다. 메모리의 데이터를 포함하지 않습니다. 
    - 기본적으로 Site Recovery는 크래시 일치 복구 지점을 24시간 동안 유지합니다. 사용자 지정 값은 0~72시간으로 설정할 수 있습니다.
    - 앱 일치 스냅샷은 4시간마다 수행됩니다.
    - 기본적으로 Site Recovery는 복구 지점을 24시간 동안 저장합니다.

7. **가용성 옵션** 에서 VM을 독립 실행형으로 배포할지, 가용성 영역에 배포할지, 아니면 가용성 집합에 배포할지 여부를 지정합니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/create-vm.png" alt-text="VM 관리 속성 페이지에서 복제를 사용하도록 설정합니다.":::

8. VM 만들기를 완료합니다.

## <a name="enable-disaster-recovery-for-an-existing-vm"></a>기존 VM에 재해 복구 사용

기존 VM에서 재해 복구를 사용하도록 설정하려면 다음 절차를 사용합니다.

1. Azure Portal에서 VM 속성 페이지를 엽니다.
2. **작업** 에서 **재해 복구** 를 선택합니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/existing-vm.png" alt-text="기존 VM에 대한 재해 복구 옵션 열기":::

3. **기본 사항** 에서 VM이 가용성 영역에 배포되는 경우 가용성 영역 간에 재해 복구를 선택할 수 있습니다.
4. **대상 지역** 에서 VM을 복제하려는 지역을 선택합니다. 원본 및 대상 지역은 동일한 Azure Active Directory 테넌트에 있어야 합니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/basics.png" alt-text="VM에 대한 기본 재해 복구 옵션 설정":::

5. 완료되면 **다음: 고급 설정** 을 선택합니다.
6. **고급 설정** 에서 설정을 검토하고, 값을 사용자 지정 설정으로 수정할 수 있습니다. 기본적으로 Site Recovery는 소스 설정을 미러링하여 대상 리소스를 만듭니다.

    - **대상 구독**. 장애 조치(failover) 후 대상 VM이 만들어지는 구독입니다.
    - **대상 VM 리소스 그룹**. 장애 조치(failover) 후 대상 VM이 만들어지는 리소스 그룹입니다.
    - **대상 가상 네트워크**. 장애 조치(failover) 후 대상 VM이 만들어질 때 해당 대상 VM이 있는 Azure 가상 네트워크입니다.
    - **대상 가용성**. 대상 VM이 가용성 집합 또는 가용성 영역에서 단일 인스턴스로 만들어지는 경우입니다.
    - **근접 배치**. 해당하는 경우 장애 조치(failover) 대상 VM이 있는 근접 배치 그룹을 선택합니다.
    - **스토리지 설정-캐시 스토리지 계정**. 복구는 원본 지역의 스토리지 계정을 임시 데이터 저장소로 사용합니다. 원본 VM 변경 내용은 대상 위치로 복제되기 전에 이 계정에 캐시됩니다.
        - 기본적으로 자격 증명 모음마다 하나의 캐시 스토리지 계정이 만들어지고 다시 사용됩니다.
        - VM에 대한 캐시 계정을 사용자 지정하려는 경우 다른 스토리지 계정을 선택할 수 있습니다.
    - **스토리지 설정-복제 관리 디스크**. 기본적으로 Site Recovery는 복제 관리 디스크를 대상 지역에 만듭니다.
        -  기본적으로 대상 관리 디스크는 동일한 스토리지 유형(표준 HDD/SSD 또는 프리미엄 SSD)을 사용하여 원본 VM 관리 디스크를 미러링합니다.
        - 스토리지 유형은 필요에 따라 사용자 지정할 수 있습니다.
    - **복제 설정**. VM이 있는 자격 증명 모음 및 VM에 사용되는 복제 정책을 표시합니다. 기본적으로 Site Recovery에서 VM에 대해 만든 복구 지점은 24시간 동안 유지됩니다.
    - **확장 설정**. Site Recovery에서 복제하는 VM에 설치된 Site Recovery Mobility Service 확장에 대한 업데이트를 관리함을 나타냅니다.
        - 표시된 Azure 자동화 계정은 업데이트 프로세스를 관리합니다.
        - 자동화 계정은 사용자 지정할 수 있습니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="대상 및 복제 설정 요약을 표시하는 페이지":::

6. **검토 + 복제 시작** 을 선택합니다.

7. **복제 시작** 을 선택합니다. 배포가 시작되고 Site Recovery가 대상 리소스 생성을 시작합니다. 알림에서 복제 진행률을 모니터링할 수 있습니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="복제 진행률에 대한 알림":::

## <a name="check-vm-status"></a>VM 상태 확인

복제 작업이 완료된 후 VM 복제 상태를 확인할 수 있습니다.

1. VM 속성 페이지를 엽니다.
2. **작업** 에서 **재해 복구** 를 선택합니다.
3. **기본 정보** 섹션을 확장하여 자격 증명 모음, 복제 정책 및 대상 설정에 대한 기본값을 검토합니다.
4. **상태** 에서 VM의 복제 상태, 에이전트 버전, 장애 조치(failover) 준비 및 최신 복구 지점과 관련된 정보를 얻습니다. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="VM 재해 복구를 위한 기본 정보 보기":::

5. **인프라 보기** 에서 원본 및 대상 VM, 관리 디스크 및 캐시 스토리지 계정에 대한 시각적 개요를 얻습니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="VM 재해 복구를 위한 인프라 시각적 맵":::

## <a name="run-a-drill"></a>훈련 실행

훈련을 실행하여 재해 복구가 예상대로 작동하는지 확인합니다. 테스트 장애 조치(failover)를 실행하면 진행 중인 복제 또는 프로덕션 환경에 영향을 주지 않고 VM의 복사본이 생성됩니다.

1. VM 재해 복구 페이지에서 **테스트 장애 조치(failover)** 를 선택합니다.
2. **테스트 장애 조치(failover)** 에서 복구 지점에 대한 기본 **최근 처리(낮은 RPO)** 설정을 그대로 둡니다.

   이 옵션은 가장 낮은 RPO(복구 지점 목표)를 제공하며, 일반적으로 대상 지역에서 가장 빠르게 VM을 회전합니다. Site Recovery 서비스로 보낸 모든 데이터를 먼저 처리하여 각 VM에 대한 복구 지점을 만든 후에 해당 복구 지점으로 장애 조치(failover)합니다. 이 복구 지점에는 장애 조치(failover)가 트리거될 때 Site Recovery로 복제된 모든 데이터가 있습니다.

3. 장애 조치(failover) 후 VM이 위치할 가상 네트워크를 선택합니다. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="테스트 장애 조치(failover) 옵션을 설정하는 페이지":::

4. 테스트 장애 조치(failover) 프로세스가 시작됩니다. 알림에서 진행률을 모니터링할 수 있습니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="테스트 장애 조치(failover) 알림"::: 
    
   테스트 장애 조치(failover)가 완료된 후 VM은 **기본 정보** 페이지에서 테스트 장애 조치(failover) 정리 보류 중 상태가 됩니다. 
  
## <a name="clean-up-resources"></a>리소스 정리

훈련 후 Site Recovery에서 VM이 자동으로 정리됩니다. 
 
1. 자동 정리를 시작하려면 **테스트 장애 조치(failover) 정리** 를 선택합니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="기본 정보 페이지에서 정리 시작"::: 

6. **테스트 장애 조치(failover) 정리** 에서 장애 조치(failover)에 대해 기록할 메모를 입력한 다음, **테스트가 완료되었습니다. 테스트 장애 조치(failover) 가상 머신을 삭제하세요** 를 선택합니다. 그런 다음, **확인** 을 선택합니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="메모를 기록하고 테스트 VM을 삭제하는 페이지"::: 

7. 삭제 프로세스가 시작됩니다. 알림에서 진행률을 모니터링할 수 있습니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="테스트 VM 삭제를 모니터링하기 위한 알림"::: 


### <a name="stop-replicating-the-vm"></a>VM 복제 중지

재해 복구 훈련을 완료한 후에는 계속해서 전체 장애 조치(failover)를 시도하는 것이 좋습니다. 전체 장애 조치(failover)를 수행하지 않으려면 복제를 사용하지 않도록 설정할 수 있습니다. 다음을 수행합니다.

- 복제된 머신의 Site Recovery 목록에서 VM이 제거됩니다.
- VM에 대한 Site Recovery 청구가 중지됩니다.
- 원본 복제 설정이 자동으로 정리됩니다.

다음과 같이 복제를 중지합니다.

1. VM 재해 복구 페이지에서 **복제 사용 안 함** 을 선택합니다.
2. **복제 사용 안 함** 에서 복제를 사용하지 않도록 설정하는 이유를 선택합니다. 그런 다음, **확인** 을 선택합니다.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="복제를 사용하지 않도록 설정하고 이유를 제공하는 페이지"::: 


복제하는 동안 VM에 설치된 Site Recovery 확장은 자동으로 제거되지 않습니다. VM에 대한 복제를 사용하지 않도록 설정하고 나중에 다시 복제하지 않으려면 다음과 같이 Site Recovery 확장을 수동으로 제거하면 됩니다. 

1. VM > **설정** > **확장** 으로 이동합니다.
2. **확장** 페이지에서 Linux에 대한 각 *Microsoft.Azure.RecoveryServices* 항목을 선택합니다.
3. 확장에 대한 속성 페이지에서 **제거** 를 선택합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure VM에 대한 재해 복구를 구성하고 재해 복구 훈련을 실행했습니다. 이제 VM에 대한 전체 장애 조치(failover)를 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [VM을 다른 Azure 지역으로 장애 조치(failover)](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
