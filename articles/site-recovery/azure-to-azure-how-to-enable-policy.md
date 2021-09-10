---
title: Azure Policy를 사용하여 VM에 대해 Azure Site Recovery를 사용하도록 설정
description: Azure Site Recovery를 사용하여 VM을 보호하는 정책 지원을 사용하도록 설정하는 방법을 알아봅니다.
author: rishjai-msft
ms.author: rishjai
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 1c936df9ffb467d732e0c07651e7c6fb31f28b5c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536268"
---
# <a name="using-policy-with-azure-site-recovery-public-preview"></a>정책 및 Azure Site Recovery 사용(퍼블릭 미리 보기)

이 문서에서는 Azure Policy를 사용하여 리소스에 대해 [Azure Site Recovery](./site-recovery-overview.md)를 설정하는 방법을 설명합니다. [Azure Policy](../governance/policy/overview.md)는 Azure 리소스에 특정 비즈니스 규칙을 적용하고 해당 리소스의 규정 준수를 평가하는 데 도움이 됩니다.

## <a name="disaster-recovery-with-azure-policy"></a>Azure Policy를 사용한 재해 복구
Site Recovery는 계획되거나 계획되지 않은 영역/지역 중단이 발생하는 경우 애플리케이션 가동 및 실행을 유지하는 데 도움이 됩니다. Azure Portal를 통해 대규모로 머신에서 Site Recovery를 사용하도록 설정하는 것은 어려울 수 있습니다. 이제 포털을 통해 특정 리소스 그룹(정책 ‘범위’)에 대한 Site Recovery를 일괄적으로 사용하도록 설정할 수 있습니다.

Azure Policy에서 이 문제를 해결합니다. 리소스 그룹에 대한 재해 복구 정책을 만든 후에는 리소스 그룹에 추가된 모든 새 가상 머신에서 자동으로 Site Recovery를 사용하도록 설정합니다. 또한 리소스 그룹에 이미 있는 모든 가상 머신의 경우 ‘수정’(아래에 세부 정보)이라는 프로세스를 통해 Site Recovery를 사용하도록 설정할 수 있습니다.

>[!NOTE]
>이 정책의 ‘범위’는 리소스 그룹 수준에 있어야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 정책 할당 방법은 [여기](../governance/policy/assign-policy-portal.md)를 참조하세요.
- Azure 간 재해 복구 아키텍처는 [여기](./azure-to-azure-architecture.md)에서 자세히 알아보세요.
- Azure Site Recovery 정책 지원에 대한 지원 매트릭스를 검토합니다.

**시나리오** | **지원 정책**
--- | ---
Managed Disks | 지원됨
관리되지 않는 디스크  | 지원되지 않음
여러 디스크 | 지원됨
가용성 집합 | 지원됨
가용성 영역 | 지원됨
ADE(Azure Disk Encryption) 지원 VM | 지원되지 않음
PPG(근접 배치 그룹) | 지원됨
CMK(고객 관리형 키) 지원 디스크 | 지원되지 않음
S2D(스토리지 공간 다이렉트) 클러스터 | 지원되지 않음
Azure Resource Manager 배포 모델 | 지원됨
클래식 배포 모델 | 지원되지 않음
영역 간 DR  | 지원됨
Azure에서 기본값으로 적용되는 다른 정책과의 상호 운용성(있는 경우) | 지원됨

>[!NOTE]
>다음과 같은 경우에는 Site Recovery가 활성화되지 않습니다. 그러나 리소스 규정 준수에 _비준수_ 로 반영됩니다.
>1. 지원되지 않는 VM이 정책 범위 내에서 만들어진 경우
>1. VM이 PPG뿐만 아니라 가용성 집합의 일부인 경우

## <a name="create-a-policy-assignment"></a>정책 할당 만들기
이 섹션에서는 새로 만든 모든 리소스에 대해 Azure Site Recovery를 사용하도록 설정하는 정책 할당을 만듭니다.
1. **Azure Portal** 로 이동한 후 **Azure Policy** 로 이동합니다.
1. Azure Policy 페이지의 왼쪽에서 **할당** 을 선택합니다. 할당은 특정 범위에서 실행되도록 할당된 정책입니다.
   :::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assignments.png" alt-text="정책 개요 페이지에서 할당 페이지를 선택하는 스크린샷" border="false":::

1. **정책 - 할당** 창의 위쪽에서 **정책 할당** 을 선택합니다.
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assign-policy.png" alt-text="할당 페이지에서 '정책 할당'을 선택하는 스크린샷" border="false":::

1. **정책 할당** 페이지에서 줄임표를 선택하고 구독, 리소스 그룹을 차례로 선택하여 **범위** 를 설정합니다. 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 그런 다음, **범위** 페이지 하단에 있는 **선택** 단추를 사용합니다.

1. **정책 정의** 옆에 있는 줄임표를 선택하여 ‘정책 정의 선택기’를 시작합니다. “복제를 사용하도록 설정하여 가상 머신에서 재해 복구 구성”을 검색하고 정책을 선택합니다.
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-policy-definition.png" alt-text="기본 사항 페이지에서 ‘정책 정의’를 선택하는 스크린샷입니다." border="true":::

1. 선택한 정책 이름이 **할당 이름** 에 자동으로 채워지지만, 할당 이름을 변경할 수 있습니다. 여러 Azure Site Recovery 정책을 동일한 범위에 할당하려는 경우 유용할 수 있습니다.

1. **다음** 을 선택하여 정책에 대한 Azure Site Recovery 속성을 구성합니다.

## <a name="configure-target-settings-and-properties"></a>대상 설정 및 속성 구성
정책을 만들어 Azure Site Recovery를 사용하도록 설정하는 과정을 진행하고 있습니다. 이제 대상 설정 및 속성을 구성해 보겠습니다.
1. 현재 위치는 다음과 유사한 ‘정책 할당’ 워크플로의 ‘매개 변수’ 섹션입니다. :::image type="content" source="./media/azure-to-azure-how-to-enable-policy/specify-parameters.png" alt-text="매개 변수 페이지에서 매개 변수를 설정하는 스크린샷입니다." border="true"::: 
1. 해당 매개 변수에 적절한 값을 선택합니다.
    - **원본 지역**: 정책을 적용할 수 있는 Virtual Machines의 원본 지역입니다.
    >[!NOTE]
    >정책 범위의 원본 지역에 속하는 모든 Virtual Machines에 정책이 적용됩니다. 원본 지역에 없는 Virtual Machines는 ‘리소스 규정 준수’에 포함되지 않습니다.
    - **대상 지역**: 원본 가상 머신 데이터가 복제될 위치입니다. Site Recovery는 고객이 복제할 수 있는 대상 지역 목록을 제공합니다. Recovery Services 자격 증명 모음의 위치와 동일한 위치를 사용하는 것이 좋습니다.
    - **대상 리소스 그룹**: 모든 복제된 가상 머신이 속한 리소스 그룹입니다. Site Recovery는 기본적으로 대상 지역에 새 리소스 그룹을 만듭니다.
    - **자격 증명 모음 리소스 그룹**: Recovery Services 자격 증명 모음이 있는 리소스 그룹입니다.
    - **Recovery Services 자격 증명 모음**: 범위의 모든 VM을 보호할 자격 증명 모음입니다. 필요한 경우 정책에서 사용자를 대신하여 새 볼트를 생성할 수 있습니다.
    - **복구 가상 네트워크**: 대상 지역에서 복구 가상 머신에 사용할 기존 가상 네트워크를 선택합니다. 필요한 경우 정책에서 새 가상 네트워크도 만들 수 있습니다.
    - **대상 가용성 영역**: 가상 머신이 장애 조치(failover)할 대상 지역의 가용성 영역을 입력합니다.
    >[!NOTE]
    >영역 간 시나리오의 경우 원본 지역과 동일한 대상 지역을 선택하고 ‘대상 가용성 영역’에서 다른 가용성 영역을 선택해야 합니다.     
    >리소스 그룹의 일부 가상 머신이 이미 대상 가용성 영역에 있는 경우 영역 간 DR을 설정하면 해당 가상 머신에 정책이 적용되지 않습니다.
    - **영향**: 정책 실행을 사용하도록 설정하거나 사용하지 않도록 설정합니다. 정책을 만든 후 곧바로 사용하도록 설정하려면 _DeployIfNotExists_ 를 선택합니다.

1. **다음** 을 선택하여 수정 작업을 결정합니다.

## <a name="remediation-and-other-properties"></a>수정 및 기타 속성
1. Azure Site Recovery에 대한 대상 속성이 구성되었습니다. 하지만 이 정책은 정책 범위에서 새로 만든 가상 머신에만 적용됩니다. 정책이 할당된 후 수정 작업을 통해 기존 리소스에 정책을 적용할 수 있습니다. 여기에서 ‘수정 작업 만들기’ 확인란을 선택하여 수정 작업을 만들 수 있습니다.

1. Azure Policy는 범위의 리소스에 대해 Azure Site Recovery를 사용하도록 설정할 수 있는 소유자 권한이 있는 [관리 ID](../governance/policy/how-to/remediate-resources.md)를 만듭니다.

1. ‘미준수 메시지’ 탭에서 정책에 대한 사용자 지정 미준수 메시지를 구성할 수 있습니다.

1. 페이지 맨 아래에 있는 다음을 선택하거나 페이지 맨 위에 있는 _검토 + 만들기_ 탭을 선택하여 할당 마법사의 다음 세그먼트로 이동합니다.

1. 선택한 옵션을 검토한 다음, 페이지 아래쪽에서 _만들기_ 를 선택합니다.

## <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
