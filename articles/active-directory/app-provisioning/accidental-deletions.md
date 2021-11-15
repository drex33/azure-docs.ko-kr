---
title: Azure Active Directory의 애플리케이션 프로비전에서 실수로 인한 삭제 방지 사용
description: Azure Active Directory의 애플리케이션 프로비전에서 실수로 인한 삭제 방지를 사용하도록 설정합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 09/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: bc100df9a0d666048ac64ae4d496c68a793deabb
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991910"
---
# <a name="enable-accidental-deletions-prevention-in-the-azure-ad-provisioning-service-preview"></a>Azure AD 프로비전 서비스에서 실수로 인한 삭제 방지 사용(미리 보기)

Azure AD 프로비전 서비스에는 실수로 인한 삭제를 방지하는 기능이 포함되어 있습니다. 이 기능은 사용자가 애플리케이션에서 예기치 않게 사용하지 않도록 설정되거나 삭제되지 않도록 합니다. 

이 기능을 사용하면 삭제 임계값을 지정할 수 있습니다. 이 임계값을 초과하면 삭제를 처리할 수 있도록 관리자가 명시적으로 선택해야 합니다.

> [!NOTE]
> 실수로 인한 삭제는 Workday/SuccessFactors 통합에서 지원되지 않습니다. 또한 범위 지정 변경(예: 범위 지정 필터 변경 또는 "모든 사용자 및 그룹 동기화"에서 "할당된 사용자 및 그룹 동기화"로 변경)에서도 지원되지 않습니다. 실수로 인한 삭제 방지 기능이 완전히 릴리스될 때까지 https://aka.ms/AccidentalDeletionsPreview URL을 사용하여 Azure Portal에 액세스해야 합니다.


## <a name="configure-accidental-deletion-prevention"></a>실수로 인한 삭제 방지 구성
실수로 인한 삭제 방지를 사용하도록 설정하려면 다음을 수행합니다.
1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
2.  **엔터프라이즈 애플리케이션** 을 선택한 다음, 앱을 선택합니다.
3.  **프로비전** 을 선택한 다음, 프로비전 페이지에서 **프로비전 편집** 을 선택합니다.
4. **설정** 아래에서 **실수로 인한 삭제 방지** 확인란을 선택하고, 삭제 임계값을 지정합니다. 또한 알림 이메일 주소가 완성되었는지 확인합니다. 삭제 임계값이 충족되면 이메일이 보내집니다.
5. **저장** 을 선택하여 변경 내용을 저장합니다.

삭제 임계값에 도달하면 작업이 격리되고 알림 이메일이 보내집니다. 그런 다음, 격리된 작업을 허용하거나 거부할 수 있습니다. 격리 동작에 대한 자세한 내용은 [격리 상태의 애플리케이션 프로비전](application-provisioning-quarantine-status.md)을 참조하세요.

## <a name="known-limitations"></a>알려진 제한 사항
다음 두 가지 주요 제한 사항을 인식하고 해결하기 위해 적극적으로 노력하고 있습니다.
- Workday 및 SuccessFactors의 HR 기반 프로비전은 실수로 인한 삭제 기능을 지원하지 않습니다. 
- 프로비전 구성 변경(예: 범위 지정 변경)은 실수로 인한 삭제 기능에서 지원되지 않습니다. 

## <a name="recovering-from-an-accidental-deletion"></a>실수로 인한 삭제에서 복구
실수로 인한 삭제가 발생하면 프로비전 상태 페이지에 표시됩니다.  **프로비전이 격리되었습니다. 자세한 내용은 격리 세부 정보를 참조하세요.** 라고 표시됩니다.

**삭제 허용** 또는 **프로비저닝 로그 보기** 를 클릭할 수 있습니다.

### <a name="allowing-deletions"></a>삭제 허용

**삭제 허용** 작업은 실수로 삭제 임계값을 트리거한 개체를 삭제합니다.  다음 절차에 따라 삭제를 허용합니다.  

1. **삭제 허용** 을 선택합니다.
2. 확인 메시지에서 **예** 를 클릭하여 삭제를 허용합니다.
3. 삭제가 허용되었다는 확인 메시지가 표시되고 상태가 다음 주기에 정상으로 복귀됩니다.

### <a name="rejecting-deletions"></a>삭제 거부

삭제를 허용하지 않으려면 다음을 수행해야 합니다.
- 삭제 원본을 조사합니다. 프로비저닝 로그를 사용하여 자세한 내용을 확인할 수 있습니다.
- 사용자/그룹을 앱에 다시 할당하거나, 사용자/그룹을 복원하거나, 프로비전 구성을 업데이트하여 삭제를 방지합니다.
- 사용자/그룹이 삭제되지 않도록 방지하기 위해 필요한 변경을 수행했으면 프로비전을 다시 시작합니다. 사용자/그룹이 삭제되지 않도록 방지하기 위해 필요한 변경을 완료할 때까지 프로비전을 다시 시작하지 마세요. 


### <a name="test-deletion-prevention"></a>삭제 방지 테스트
이 기능은 사용 안 함/삭제 이벤트를 트리거하여 테스트할 수 있습니다. 즉, 임계값을 낮은 숫자(예: 3)로 설정한 다음, 범위 지정 필터를 변경하고, 사용자를 할당 취소하고, 디렉터리에서 사용자를 삭제합니다(다음 섹션의 일반적인 시나리오 참조). 

프로비전 작업을 실행하고(20~40분), 프로비전 페이지로 다시 이동합니다. 격리에서 프로비전 작업이 표시되며, 삭제를 허용하도록 선택하거나 프로비저닝 로그를 검토하여 삭제가 발생한 이유를 파악할 수 있습니다.

## <a name="common-de-provisioning-scenarios-to-test"></a>테스트할 일반적인 프로비전 해제 시나리오
- 사용자를 삭제하고 휴지통에 넣습니다.
- 사용자의 로그인을 차단합니다.
- 애플리케이션에서 사용자 또는 그룹의 할당을 취소합니다.
- 앱에 대한 액세스 권한을 제공하는 그룹에서 사용자를 제거합니다.

프로비전 해제 시나리오에 대한 자세한 내용은 [애플리케이션 프로비전이 작동하는 방식](how-provisioning-works.md#de-provisioning)을 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-scenarios-count-toward-the-deletion-threshold"></a>삭제 임계값에 포함할 수 있는 시나리오는 무엇인가요?
사용자를 대상 애플리케이션에서 제거하도록 설정되면 삭제 임계값에 대해 계산됩니다. 대상 애플리케이션에서 사용자를 제거할 수 있는 시나리오에는 애플리케이션에서 사용자의 할당 취소 및 디렉터리에서 사용자의 일시 삭제/영구 삭제가 포함될 수 있습니다. 삭제 임계값에 대한 삭제 횟수가 계산된 그룹이 포함됩니다. 삭제 외에도 동일한 기능이 사용 안 함에도 작동합니다.

### <a name="what-is-the-interval-that-the-deletion-threshold-is-evaluated-on"></a>삭제 임계값이 평가되는 간격은 어떻게 되나요?
각 주기마다 평가됩니다. 단일 주기 동안 삭제 횟수가 임계값을 초과하지 않으면 "회로 차단기"가 트리거되지 않습니다. 안정 상태에 도달하는 데 여러 주기가 필요한 경우 삭제 임계값은 주기별로 평가됩니다.

### <a name="how-are-these-deletion-events-logged"></a>이러한 삭제 이벤트는 어떻게 기록하나요?
사용하지 않도록 설정되거나 삭제되어야 하지만 삭제 임계값으로 인해 이렇게 되지 않은 사용자를 확인할 수 있습니다. **프로비저닝 로그** 로 이동한 다음, *StagedAction* 또는 *StagedDelete* 를 사용하여 **작업** 을 필터링합니다.


## <a name="next-steps"></a>다음 단계 

- [애플리케이션 프로비저닝 작동 방법](how-provisioning-works.md)
- [애플리케이션 프로비저닝 배포 계획](plan-auto-user-provisioning.md)
