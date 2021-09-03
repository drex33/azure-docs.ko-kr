---
title: Azure AD 권한 관리에서 액세스 패키지에 대한 승인 설정 변경 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 액세스 패키지에 대한 승인 및 요청자 정보 설정을 변경하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 05/16/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bd7a7908b94794c60f351297a34309e94d21137
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112964035"
---
# <a name="change-approval-and-requestor-information-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 승인 및 요청자 정보 설정 변경

액세스 패키지 관리자는 기존 정책을 편집하거나 새 정책을 추가하여 언제든지 액세스 패키지에 대한 승인 및 요청자 정보 설정을 변경할 수 있습니다.

이 문서에서는 기존 액세스 패키지의 승인 및 요청자 정보 설정을 변경하는 방법을 설명합니다.

## <a name="approval"></a>승인

[승인] 섹션에서 사용자가 이 액세스 패키지를 요청할 때 승인이 필요한지 여부를 지정합니다. 승인 설정은 다음과 같은 방식으로 작동합니다.

- 선택한 승인자 또는 대체 승인자 중 한 명만 1단계 승인 요청을 승인하면 됩니다. 
- 요청이 다음 단계로 진행하려면 각 단계의 선택된 승인자 중 하나만 다단계 승인 요청을 승인해야 합니다.
- 한 단계의 다른 승인자가 요청을 승인하기 전에 해당 단계의 선택된 승인자 중 하나가 요청을 거부하거나 아무도 요청을 승인하지 않는 경우에는 요청이 종료되고 사용자에게 액세스 권한이 부여되지 않습니다.
- 승인자는 정책을 통해 액세스를 제어하는 대상에 따라 그룹의 지정된 사용자나 멤버, 요청자의 관리자, 내부 스폰서 또는 외부 스폰서일 수 있습니다.

승인자를 요청 정책에 추가하는 방법에 대한 데모를 보려면 다음 비디오를 시청하세요.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

요청 정책에 다단계 승인을 추가하는 방법에 대한 데모를 보려면 다음 비디오를 시청하세요.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]


## <a name="change-approval-settings-of-an-existing-access-package"></a>기존 액세스 패키지의 승인 설정 변경

액세스 패키지에 대한 요청의 승인 설정을 지정하려면 다음 단계를 수행합니다.

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. 편집할 정책을 선택하거나 액세스 패키지에 새 정책 추가
    1. 새 정책을 만들려면 **정책** 을 클릭하고 **정책 추가** 를 클릭합니다.
    1. 편집하려는 정책을 클릭하고 **편집** 을 클릭합니다.

1. **요청** 탭으로 이동합니다.

1. 선택한 사용자의 요청에 대한 승인을 요청하려면 **승인 필요** 토글을 **예** 로 설정합니다. 또는 요청이 자동으로 승인되도록 하려면 토글을 **아니요** 로 설정합니다.

1. 사용자에게 액세스 패키지를 요청하는 근거를 제공하도록 요구하려면 **요청자 근거 필요** 토글을 **예** 로 설정합니다.
    
1. 이제 요청에 단일 단계 또는 다단계 승인이 필요한지 여부를 확인합니다. **단계 수** 를 필요한 승인 단계 수로 설정합니다.

    ![액세스 패키지 -요청 - 승인 설정](./media/entitlement-management-access-package-approval-policy/approval.png)


필요한 단계 수를 선택한 후 다음 단계를 사용하여 승인자를 추가합니다. 

### <a name="single-stage-approval"></a>1단계 승인

1. **첫 번째 승인자** 를 추가합니다.
    
    정책이 디렉터리에 있는 사용자에 대한 액세스를 제어하도록 설정된 경우 **승인자로서 관리자** 를 선택할 수 있습니다. 또는 드롭다운 메뉴에서 [특정 승인자 선택]을 선택한 후 **승인자 추가** 를 클릭하여 특정 사용자를 추가합니다.
    
    ![액세스 패키지 -요청 - 디렉터리에 있는 사용자 - 첫 번째 승인자](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    이 정책이 디렉터리에 없는 사용자에 대한 액세스를 제어하도록 설정된 경우 **외부 스폰서** 또는 **내부 스폰서** 를 선택할 수 있습니다. 또는 [특정 승인자 선택] 아래에서 **승인자 추가** 또는 그룹을 클릭하여 특정 사용자를 추가합니다.
    
    ![액세스 패키지 - 요청 - 디렉터리에 없는 사용자 - 첫 번째 승인자](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. **관리자** 를 첫 번째 승인자로 선택한 경우 **대체 추가** 를 클릭하여 디렉터리에서 하나 이상의 사용자 또는 그룹을 대체 승인자로 선택합니다. 대체 승인자는 권한 관리에서 액세스를 요청하는 사용자의 관리자를 찾을 수 없는 경우 요청을 받습니다.

    관리자는 권한 관리에서 **관리자** 특성을 사용하여 찾습니다. 특성은 Azure AD의 사용자 프로필에 있습니다. 자세한 내용은 [Azure Active Directory를 사용하여 사용자 프로필 정보 추가 또는 업데이트](../fundamentals/active-directory-users-profile-azure-portal.md)를 참조하세요.

1. **특정 승인자 선택** 을 선택한 경우 **승인자 추가** 를 클릭하여 디렉터리에서 하나 이상의 사용자 또는 그룹을 승인자로 선택합니다.

1. **며칠 내에 결정해야 하나요?** 아래의 상자에서 승인자가 이 액세스 패키지에 대한 요청을 검토해야 하는 일 수를 지정합니다.

    이 기간 내에 요청이 승인되지 않으면 해당 요청이 자동으로 거부됩니다. 사용자는 액세스 패키지에 대한 다른 요청을 제출해야 합니다.

1. 승인자가 결정에 대한 근거를 제공하도록 요구하려면 [승인자 근거 필요]를 **예** 로 설정합니다.

    근거는 다른 승인자와 요청자에게 표시됩니다.

### <a name="multi-stage-approval"></a>다단계 승인

다단계 승인을 선택한 경우 각 추가 단계에 대해 승인자를 추가해야 합니다.

1. **두 번째 승인자** 를 추가합니다. 
    
    디렉터리에 있는 사용자인 경우 [특정 승인자 선택] 아래에서 **승인자 추가** 를 클릭하여 특정 사용자를 두 번째 승인자로 추가합니다.

    ![액세스 패키지 -요청 - 디렉터리에 있는 사용자 - 두 번째 승인자](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    디렉터리에 없는 사용자인 경우 **내부 스폰서** 또는 **외부 스폰서** 를 두 번째 승인자로 선택합니다. 승인자를 선택한 후 대체 승인자를 추가합니다.

    ![액세스 패키지 -요청 - 디렉터리에 없는 사용자 - 두 번째 승인자](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. **며칠 내에 결정해야 하나요?** 아래의 상자에서 두 번째 승인자가 요청을 승인해야 하는 일 수를 지정합니다. 

1. [승인자 근거 필요] 토글을 **예** 또는 **아니요** 로 설정합니다.

### <a name="alternate-approvers"></a>대체 승인자

각 단계에서 요청을 승인할 수 있는 기본 승인자를 지정하는 것과 비슷한 방식으로 대체 승인자를 지정할 수 있습니다. 대체 승인자가 있으면 요청이 만료(시간 제한)되기 전에 요청이 승인 또는 거부되는지 확인하는 데 도움이 됩니다. 각 단계에서 기본 승인자와 함께 대체 승인자를 나열할 수 있습니다.

단계에서 대체 승인자를 지정하면 기본 승인자가 요청을 승인하거나 거부할 수 없는 경우 정책 설정 중에 지정한 전달 일정에 따라 보류 중인 요청이 대체 승인자에게 전달됩니다. 대체 승인자는 보류 중인 요청을 승인하거나 거부하는 이메일을 받습니다.

요청이 대체 승인자에게 전달된 후에도 기본 승인자는 해당 요청을 승인하거나 거부할 수 있습니다. 대체 승인자는 동일한 [내 액세스] 사이트를 사용하여 보류 중인 요청을 승인하거나 거부합니다.

승인자 및 대체 승인자가 될 사용자나 사용자 그룹을 나열할 수 있습니다. 첫 번째, 두 번째 및 대체 승인자가 될 여러 사용자 집합을 나열해야 합니다.
예를 들어, Alice와 Bob을 첫 번째 단계 승인자로 나열한 경우 Carol과 Dave를 대체 승인자로 나열합니다. 다음 단계를 사용하여 액세스 패키지에 대한 대체 승인자를 추가합니다.

1. 단계의 승인자 아래에서 **고급 요청 설정 표시** 를 클릭합니다.

    :::image type="content" source="media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png" alt-text="액세스 패키지 - 정책 - 고급 요청 설정 표시":::

1. **작업을 수행하지 않은 경우 대체 승인자에게 전달하시겠습니까?** 토글을 **예** 로 설정합니다.

1. **대체 승인자 추가** 를 클릭하고, 목록에서 대체 승인자를 선택합니다.

    ![액세스 패키지 - 정책 - 대체 승인자 추가](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

    [첫 번째 승인자]에 대해 [승인자로서 관리자]를 선택하면 대체 승인자 필드에서 선택할 수 있는 추가 옵션인 **두 번째 수준 관리자를 대체 승인자로** 가 제공됩니다. 이 옵션을 선택하는 경우 시스템에서 두 번째 수준 관리자를 찾을 수 없으면 요청을 전달할 대체 승인자를 추가해야 합니다.

1. **며칠 후에 대체 승인자에게 전달하시겠습니까?** 상자에서 승인자가 요청을 승인하거나 거부해야 하는 일수를 입력합니다. 요청 기간 전에 요청을 승인하거나 거부한 승인자가 없는 경우 요청이 만료(시간 제한)되고, 사용자가 액세스 패키지에 대한 다른 요청을 제출해야 합니다. 

    요청은 요청 기간이 반기에 도달한 후 1일 동안만 대체 승인자에게 전달될 수 있으며, 주 승인자의 결정은 최소 4일 후에 시간 제한을 초과해야 합니다. 요청 시간 제한이 3보다 작거나 같으면 요청을 대체 승인자에게 전달할 시간이 충분하지 않습니다. 이 예에서 요청 기간은 14일입니다. 이에 따라 요청 기간은 7일에 반기에 도달합니다. 결과적으로 요청은 8일 이전에 전달할 수 없습니다. 또한 요청 기간의 마지막 날에도 요청을 전달할 수 없습니다. 결국 이 예에서 요청을 전달할 수 있는 최신 날짜는 13일입니다.

## <a name="enable-requests"></a>요청 사용

1. 요청 정책의 사용자가 액세스 패키지를 즉시 사용할 수 있게 하려면 [사용] 토글을 **예** 로 이동합니다.

    액세스 패키지 만들기가 완료되면 나중에 언제든지 사용할 수 있습니다.

    **없음(관리자 직접 할당에만 해당)** 을 선택하고 사용을 **아니요** 로 설정하면 관리자가 이 액세스 패키지를 직접 할당할 수 없습니다.

    ![액세스 패키지 - 정책 - 정책 설정 사용](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. **다음** 을 클릭합니다.

## <a name="collect-additional-requestor-information-for-approval"></a>승인에 대한 추가 요청자 정보 수집

사용자가 올바른 액세스 패키지에 액세스할 수 있도록 하기 위해 요청 시 요청자에게 사용자 지정 텍스트 필드 또는 여러 선택 질문에 응답하도록 요구할 수 있습니다. 정책당 질문 수가 20개로 제한되며 여러 선택 질문에 대한 답변은 25개로 제한됩니다. 그런 다음, 승인자에게 질문을 제시하여 결정을 내리도록 돕습니다.

1. **요청자 정보** 탭으로 이동하여 **질문** 하위 탭을 클릭합니다.
 
1. **질문** 상자에서 질문에 대해 요청자에게 질문하려는 내용(표시 문자열이라고도 함)을 입력합니다.

    ![액세스 패키지 - 정책 - 요청자 정보 사용 설정](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. 액세스 패키지에 액세스해야 하는 사용자의 커뮤니티에서 모두 공통된 기본 언어가 사용되지는 않을 경우 myaccess.microsoft.com에서 액세스를 요청하는 사용자의 환경을 개선할 수 있습니다. 환경을 개선하기 위해 다른 언어에 대한 대체 표시 문자열을 제공할 수 있습니다. 예를 들어 사용자의 웹 브라우저가 스페인어로 설정되어 있고 스페인어 표시 문자열이 구성된 경우 해당 문자열은 요청하는 사용자에게 표시됩니다. 요청에 대한 지역화를 구성하려면 **지역화 추가** 를 클릭합니다.
    1. **질문에 대한 지역화 추가** 창에서 질문을 지역화할 언어에 대한 **언어 코드** 를 선택합니다.
    1. **지역화된 텍스트** 상자에서 질문을 구성한 언어로 입력합니다.
    1. 필요한 모든 지역화가 추가되면 **저장** 을 클릭합니다.

    ![액세스 패키지 - 정책 - 지역화된 텍스트 구성](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. 요청자가 대답하게 하려는 **대답 형식** 을 선택합니다. 대답 형식에는 *짧은 텍스트*, *여러 선택 항목* 및 *긴 텍스트* 가 포함됩니다.
 
    ![액세스 패키지 - 정책 - 여러 선택 항목 대답 형식 편집 및 지역화 선택](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. 여러 선택 항목을 선택하는 경우 **편집 및 지역화** 단추를 클릭하여 대답 옵션을 구성합니다.
    1. 편집 및 지역화를 선택하면 **질문 보기/편집** 창이 열립니다.
    1. **대답 값** 상자에서 질문에 대답할 때 요청자에게 제공하려는 응답 옵션을 입력합니다.
    1. 필요한 만큼 응답을 입력합니다.
    1. 다중 선택 옵션에 대한 고유한 지역화를 추가하려면 특정 옵션을 지역화하려는 언어에 대해 **선택적 언어 코드** 를 선택합니다.
    1. 지역화 텍스트 상자에 구성한 언어로 옵션을 입력합니다.
    1. 각 다중 선택 옵션에 필요한 모든 지역화를 추가한 후 **저장** 을 클릭합니다.
    
    ![액세스 패키지 - 정책 - 여러 선택 항목 옵션 입력](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. 액세스 패키지에 대한 액세스를 요청할 때 요청자에게 이 질문에 답변하도록 요구하려면 **필요** 아래의 확인란을 클릭합니다.

1. 필요에 따라 나머지 탭(예: 수명 주기)을 입력합니다.

액세스 패키지 정책에서 요청자 정보를 구성한 후에 질문에 대한 요청자의 응답을 볼 수 있습니다. 요청자 정보를 보는 방법에 관한 지침은 [질문에 대한 요청자 답변 보기](entitlement-management-request-approve.md#view-requestors-answers-to-questions)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [액세스 패키지의 수명 주기 설정 변경](entitlement-management-access-package-lifecycle-policy.md)
- [액세스 패키지의 요청 보기](entitlement-management-access-package-requests.md)
