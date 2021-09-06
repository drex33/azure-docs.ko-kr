---
title: 전 세계 Azure 지역에서 재해 복구 사용
description: 이 문서에서는 Azure Site Recovery의 글로벌 재해 복구 기능에 대해 설명합니다.
author: JYOTHIRMAISURI
manager: evansma
ms.service: site-recovery
ms.topic: article
ms.date: 08/09/2021
ms.author: v-jysur
ms.openlocfilehash: 1179558e01ff23c66db652395e10c7d6259af09b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567099"
---
# <a name="enable-global-disaster-recovery-using-azure-site-recovery"></a>Azure Site Recovery를 통해 글로벌 재해 복구 사용

이 문서에서는 동일한 또는 다른 Azure 지역 내 한 가용성 영역에서 다른 가용성 영역으로 Azure 가상 머신을 복제, 장애 조치(failover) 및 장애 복구(failback)하는 방법을 설명합니다.

>[!NOTE]
> Azure Site Recovery는 사용자가 선택한 원본 및 대상 지역이 아닌 곳에서 데이터를 이동하거나 저장하지 않습니다. 다른(제3의) 지역에서 Recovery Services 자격 증명 모음을 선택할 수 있습니다. Recovery Services 자격 증명 모음에는 메타데이터가 포함되지만, 실제 고객 데이터는 없습니다.

Azure Site Recovery 서비스는 계획된 정전 및 계획되지 않은 정전 중 비즈니스 앱 작동을 유지하여 비즈니스 연속성 및 재해 복구 전략에 기여합니다. 지역 중단 시 애플리케이션을 계속 가동하고 실행하기 위해 권장되는 재해 복구 옵션입니다.

## <a name="disaster-recovery-for-global-azure-regions"></a>전 세계 Azure 지역에 대한 재해 복구

Azure는 Azure 간 재해 복구를 사용하여 최고의 클라우드 네이티브 재해 복구 솔루션을 시작하는 최초의 대규모 퍼블릭 클라우드 공급자였습니다. 이 제품을 사용하면 동일한 대륙의 한 Azure 지역에서 다른 Azure 지역으로 애플리케이션을 복제하고 장애 조치(fail over)할 수 있었습니다. 또한 영역 간 재해 복구도 지원합니다.   

이제 Azure Site Recovery는 글로벌 재해 복구를 지원합니다. 이제 대륙을 넘어 모든 Azure 지역에서 애플리케이션을 복제하고 장애 조치(fail over)할 수 있습니다.

여러분 중 다수는 글로벌 비즈니스를 운영하고 있으며 Azure의 글로벌 입지를 활용하여 전 세계에 애플리케이션을 호스팅하고 복제하고자 합니다. 여기서 Azure Site Recovery를 통해 글로벌 재해 복구를 활용할 수 있습니다. 이 새로운 제품은 Azure에서 Azure 재해 복구까지 대륙적 경계를 제거합니다. 강력한 글로벌 네트워킹 백본에서 지원되며, 전 세계 원하는 Azure 지역에 애플리케이션을 복제하고 장애 조치(fail over)할 수 있습니다.  

이 제품은 영역 간 재해 복구, 대륙 내 재해 복구 및 글로벌 재해 복구의 네이티브 퍼블릭 클라우드 재해 복구 포트폴리오를 완료합니다.

>[!NOTE]
>먼 대륙에서 DR(재해 복구) 지역을 선택하는 것은 RPO(복구 지점 목표)에 작은 영향을 주지만 Azure Site Recovery의 SLA에 영향을 미칠 만큼 중요하지는 않습니다.

이 제품은 영역 간 재해 복구, 대륙 내 재해 복구 및 글로벌 재해 복구의 네이티브 퍼블릭 클라우드 재해 복구 포트폴리오를 완료합니다.  

## <a name="before-you-begin"></a>시작하기 전에
이 문서에서는 [Azure 간 재해 복구 자습서](azure-to-azure-tutorial-enable-replication.md)에 설명된 대로 여러분이 Azure Site Recovery 배포를 할 준비가 된 것으로 가정합니다.

필수 구성 조건이 충족되고 Recovery Services 자격 증명 모음을 만들었는지 확인하세요.

>[!NOTE]
> 가상 머신에서 복제를 사용하도록 설정하기 전에 [지원 매트릭스](azure-to-azure-support-matrix.md)를 참조하세요.

## <a name="supported-platform-features"></a>지원되는 플랫폼 기능

| **기능** | **지원 정책** |
| --- | --- |
| 클래식 VM | No |
| ARM VM |  Yes |
| Azure Disk Encryption v1(이중 패스, Azure Active Directory(Azure AD) 사용) |  Yes |
| Azure Disk Encryption v2(단일 패스, Azure AD 사용 안 함) |  예 |
|     |  아니요 |
| 관리 디스크 |  예 |
| 고객 관리형 키 |   Yes |
| 근접 배치 그룹 |  Yes |
| 백업 상호 운용성 | 파일 수준 백업 및 복원이 지원됩니다. 디스크 및 VM 수준 백업 및 복원이 지원되지 않습니다. |
| 핫 추가/제거 | 영역 간 복제를 사용하도록 설정한 후 디스크를 추가할 수 있습니다. 영역 간 복제를 사용하도록 설정한 후에는 디스크 제거가 지원되지 않습니다. |

## <a name="enable-replication"></a>복제 사용

어플라이언스를 신속하게 복제하려면 다음 단계를 사용합니다.

1. 포털에서 가상 머신으로 이동합니다.

2. 왼쪽에서 **작업** 아래에 있는 **재해 복구** 를 선택합니다.

3. **기본** 에서 **대상 지역** 을 선택합니다.

   전 세계에서 사용 가능한 모든 Azure 지역이 드롭다운 메뉴 아래에 표시됩니다. 기본 설정에 따라 대상 지역을 선택합니다.  

   ![글로벌 재해 복구를 위한 설정](./media/azure-to-azure-enable-global-disaster-recovery/enable-global-disaster-recovery.png)

   필요에 따라 다른 구성을 변경할 수 있습니다.

4. **대상 구독**, **대상 가상 네트워크**, **대상 가용성**, **대상 근접 배치 그룹** 과 같은 다른 설정에 대한 **고급 설정** 을 클릭합니다.

5. **검토 + 복제 시작** 을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [영역 간 재해 복구](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)에 대해 알아봅니다.
- [Azure 간 재해 복구 아키텍처](azure-to-azure-architecture.md)에 대해 알아봅니다.
