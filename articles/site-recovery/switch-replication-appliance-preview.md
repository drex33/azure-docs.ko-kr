---
title: Azure Site Recovery - 미리 보기에서 복제 어플라이언스 전환
description: 이 문서에서는 Azure Site Recovery - 미리 보기에서 VMware VM을 Azure로 복제하는 동안 다양한 복제 어플라이언스 간에 전환하는 방법을 설명합니다.
ms.service: site-recovery
ms.topic: article
ms.date: 09/01/2021
ms.openlocfilehash: 3cf8fa8c761419a73e6bca5dac34d8b1e63cbd04
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537898"
---
# <a name="switch-azure-site-recovery-replication-appliance"></a>Azure Site Recovery 복제 어플라이언스 전환

>[!NOTE]
> 이 문서의 정보는 Azure Site Recovery - 미리 보기에 적용됩니다.

Azure에 대한 VMware VM과 물리적 서버의 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용하는 경우 [온-프레미스 Azure Site Recovery 복제 어플라이언스를 만들고 배포](deploy-vmware-azure-replication-appliance-preview.md)해야 합니다. 복제 어플라이언스에 관한 자세한 내용은 [아키텍처](vmware-azure-architecture-preview.md)를 참조하세요. 조직의 용량 요구 사항에 따라 여러 복제 어플라이언스를 만들고 사용할 수 있습니다.

이 문서에서는 복제 어플라이언스 간에 전환하는 방법에 관한 정보를 제공합니다.

## <a name="application-resilience"></a>애플리케이션 복원력

일반적으로 클래식 아키텍처에서 구성 서버의 복원력을 유지 관리해야 하는 경우 머신의 정기적인 수동 백업을 수행하는 것이 좋습니다. 이는 매우 번거로운 프로세스이며 오류와 누락이 발생하기 쉽습니다.  

이 미리 보기에서는 어플라이언스의 복원력을 높이는 더 나은 방법을 소개합니다. 복제 어플라이언스가 번다운(burn down)되거나 어플라이언스에서 실행되는 머신의 균형을 맞추어야 하는 경우에는 다른 복제 어플라이언스를 실행하고 모든 머신을 새 어플라이언스로 전환하면 됩니다.


## <a name="consideration-for-switching-replication-appliance"></a>복제 어플라이언스 전환 시 고려 사항

다음 시나리오에서 복제 어플라이언스를 전환할 수 있습니다.

- 현재 Azure Site Recovery 복제 어플라이언스가 번다운된 경우(모든 구성 요소에 하트비트가 없는 경우) 전환 작업을 수행해야 합니다.
  - 어플라이언스는 모든 구성 요소에 하트비트가 없는 경우에만 번다운된 것으로 간주합니다. 구성 요소 중 하나에 하트비트가 있더라도 전환 작업이 차단됩니다.
  - 현재 어플라이언스가 번다운된 경우 전환하려는 머신에 액세스하려면 자격 증명을 다시 제공해야 합니다. 부하 분산 중이고 현재 어플라이언스가 계속 중요하지 않은 상태이면 자격 증명이 자동으로 선택되며 다른 어플라이언스로 전환하는 동안 자격 증명을 다시 입력할 필요가 없습니다.
- 복제 어플라이언스의 부하를 분산해야 하는 경우 전환 작업을 수행해야 할 수 있습니다.
- 어플라이언스의 부하를 분산하기 위해 전환을 수행하려는 경우에는 현재 어플라이언스의 모든 구성 요소가 정상 또는 경고 상태여야 합니다. 한 구성 요소라도 하트비트가 누락되면 전환 작업이 차단됩니다.
-  작업이 성공하려면 전환할 어플라이언스가 정상 또는 경고 상태인지 확인합니다.
-  다른 어플라이언스로 전환 작업을 수행하는 경우 온-프레미스에서 Azure로 복제할 머신만 선택할 수 있습니다.  


## <a name="switch-a-replication-appliance"></a>복제 어플라이언스 전환

예를 들어, 다음은 RA1(복제 어플라이언스 1)이 중요해지고 보호된 워크로드를 정상 상태인 RA2(복제 어플라이언스 2)로 이동하려는 시나리오입니다. 또는 부하 분산 또는 조직 수준 변경을 위해 RA1의 워크로드를 RA2로 전환하려고 합니다.

**다음 단계에 따라 어플라이언스를 전환합니다**.

1. **Site Recovery 인프라** 블레이드로 이동하고 **ASR 복제 어플라이언스** 를 선택합니다.

   사용 가능한 어플라이언스와 해당 상태 목록이 표시됩니다. 예를 들어, RA2는 여기서 정상 상태입니다.

   ![정상 복제 어플라이언스 목록](./media/switch-replication-appliance-preview/appliance-health.png)

2. 복제 어플라이언스(RA1)를 선택하고  **어플라이언스 전환** 을 선택합니다.

   ![전환할 복제 어플라이언스 선택](./media/switch-replication-appliance-preview/select-switch-appliance.png)


3. **선택** 에서 다른 복제 어플라이언스(RA2)로 장애 조치(failover)할 머신을 선택합니다. **다음** 을 선택합니다.

   >[!NOTE]
   > 현재 어플라이언스로 보호된 머신만 목록에 표시됩니다. 장애 조치된 머신은 여기에 표시되지 않습니다.  

    ![전환할 머신 선택](./media/switch-replication-appliance-preview/select-machines.png)

4.  **원본 설정** 페이지에서 선택된 각 머신에 대해 다른 복제 어플라이언스를 선택합니다.

   ![복제 어플라이언스의 원본 설정](./media/switch-replication-appliance-preview/source-settings.png)

   >[!NOTE]
   > 현재 어플라이언스가 번다운된 경우 머신에 액세스하려면 자격 증명을 선택해야 합니다. 선택하지 않으면 필드가 사용하지 않도록 설정됩니다.

5. 선택 항목을 검토하고 **어플라이언스 전환** 을 클릭합니다.

   ![복제 어플라이언스 검토](./media/switch-replication-appliance-preview/review-switch-appliance.png)

   다시 동기화가 완료되면 새 어플라이언스로 이동되는 VM의 복제 상태가 정상으로 전환됩니다.

## <a name="next-steps"></a>다음 단계
Azure에 [VMware VM](vmware-azure-set-up-replication-tutorial-preview.md)의 재해 복구를 설정합니다.
