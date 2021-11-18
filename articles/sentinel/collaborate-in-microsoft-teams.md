---
title: Microsoft Sentinel 인시던트 팀과 Microsoft Teams 공동 작업 | Microsoft Docs
description: Microsoft Sentinel에서 Microsoft Teams 연결하여 Microsoft Sentinel 데이터를 사용하여 팀의 다른 사용자와 협업하는 방법을 알아봅니다.
author: batamig
ms.topic: how-to
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5172aafb17a831f5997910ac2ca7037b8ccf0981
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757372"
---
# <a name="collaborate-in-microsoft-teams-public-preview"></a>Microsoft Teams에서 공동 작업(퍼블릭 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel은 [Microsoft Teams](/microsoftteams/)직접 통합을 지원하므로 특정 인시던트에서 팀워크로 바로 이동할 수 있습니다.


> [!IMPORTANT]
> Microsoft Teams와의 통합은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="overview"></a>개요

Microsoft Sentinel에서 직접 Microsoft Teams 통합하면 팀이 조직 전체 및 외부 이해 관계자와 원활하게 협업할 수 있습니다.

Microsoft Sentinel *인시던트 팀과* Microsoft Teams 사용하여 관련 담당자 간 커뮤니케이션 및 조정을 중앙 집중화합니다. 인시던트 팀은 심각도가 높으면서 계속 진행 중인 인시던트의 전용 회의 브리지로 사용할 때 특히 유용합니다.

통신 및 협업에 이미 Microsoft Teams 사용하는 조직은 Microsoft Sentinel 통합을 사용하여 보안 데이터를 대화 및 일상적인 작업에 직접 가져올 수 있습니다. 

Microsoft Sentinel 인시던트 팀은 항상 Microsoft Sentinel의 가장 업데이트된 최신 데이터를 보유하고 있으며, 팀에서 가장 관련성이 큰 데이터를 직접 확보합니다.

## <a name="required-permissions"></a>필요한 사용 권한

Microsoft Sentinel에서 팀을 만들려면 다음을 수행합니다.

- 팀을 만드는 사용자는 Microsoft Sentinel에서 인시던트 쓰기 권한이 있어야 합니다. 예를 들어 [Microsoft Sentinel 응답기](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) 역할은 이 권한에 이상적인 최소 역할입니다.

- 팀을 만드는 사용자에게는 Microsoft Teams에서 팀을 만들 수 있는 권한도 있어야 합니다.

- [읽기](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader)권한자, [응답기](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)또는 [기여자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) 역할이 있는 사용자를 비롯한 모든 Microsoft Sentinel 사용자는 액세스를 요청하여 만든 팀에 대한 액세스 권한을 얻을 수 있습니다.

## <a name="use-an-incident-team-to-investigate"></a>인시던트 팀을 사용하여 조사

인시던트에 직접 Microsoft Teams를 통합하여 *인시던트 팀* 과 함께 조사하세요.

**인시던트 팀을 만들려면**:

1. Microsoft Sentinel의 **위협 관리**  >  **인시던트** 표에서 현재 조사 중인 인시던트 를 선택합니다.

1. 오른쪽에 표시되는 인시던트 창 맨 아래에서 **작업** > **팀 만들기** 를 선택합니다.

    [ ![인시던트 팀에서 공동 작업할 팀을 만듭니다.](media/collaborate-in-microsoft-teams/create-team.png)](media/collaborate-in-microsoft-teams/create-team.png#lightbox)

    **새 팀** 창이 오른쪽에 열립니다. 인시던트 팀에 대해 다음 설정을 정의합니다.

    - **팀 이름**: 자동으로 인시던트의 이름으로 정의됩니다. 쉽게 식별할 수 있도록 필요에 따라 이름을 수정합니다.
    - **설명**: 인시던트 팀에 대한 의미 있는 설명을 입력합니다.
    - **그룹 추가**: 인시던트 팀에 추가할 Azure AD 그룹을 하나 이상 선택합니다. 이 페이지에서 개별 사용자는 지원되지 않습니다. 개별 사용자를 추가해야 하는 경우 팀을 만든 후 [Microsoft Teams에서 사용자를 추가](#more-users)합니다.

        > [!TIP]
        > 동일한 팀에서 정기적으로 작업하는 경우 별표 :::image type="icon" source="media/collaborate-in-microsoft-teams/save-as-favorite.png" border="false":::를 선택하여 즐겨찾기로 저장할 수 있습니다.
        >
        > 다음 번에 팀을 만들 때 즐겨찾기가 자동으로 선택됩니다. 만든 다음 팀에서 제거하려면 **삭제** :::image type="icon" source="media/collaborate-in-microsoft-teams/delete-user-group.png" border="false":::를 선택하거나, 별표 :::image type="icon" source="media/collaborate-in-microsoft-teams/save-as-favorite.png" border="false":::를 다시 선택하여 즐겨찾기에서 팀을 완전히 제거합니다.
        >

1. 그룹 추가가 완료되면 **만들기** 를 선택하여 인시던트 팀을 만듭니다.

    인시던트 창이 새로 고쳐지고 새 인시던트 팀에 대한 링크가 **팀 이름** 제목 아래에 표시됩니다.

    [ ![인시던트에 추가된 Teams 통합 링크를 클릭합니다.](media/collaborate-in-microsoft-teams/teams-link-added-to-incident.jpg)](media/collaborate-in-microsoft-teams/teams-link-added-to-incident.jpg#lightbox)


1. **Teams 통합** 링크를 선택하여 Microsoft Teams로 전환합니다. 여기에서 인시던트에 대한 모든 데이터가 **인시던트 페이지** 탭에 표시됩니다.

    [ ![Microsoft Teams의 인시던트 페이지입니다.](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

필요한 경우 Teams에서 조사에 대한 대화를 계속합니다. Teams에서 직접 전체 인시던트 정보를 확인할 수 있습니다.

> [!TIP]
> - <a name="more-users"></a>팀에 개별 사용자를 추가해야 하는 경우 **게시물** 탭에서 **더 많은 사람 추가** 단추를 사용하여 Microsoft Teams에서 사용자를 추가할 수 있습니다.
>
> - [인시던트를 닫으면](investigate-cases.md#closing-an-incident) Microsoft Teams에서 만든 관련 인시던트 팀이 보관됩니다. 인시던트가 다시 열려 있으면 Microsoft Teams에서도 관련 인시던트 팀이 다시 열리므로 사용자는 대화를 중단한 위치에서 바로 대화를 계속할 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [자습서: Microsoft Sentinel을 사용하여 인시던트 조사](investigate-cases.md)
- [Microsoft Teams의 팀 및 채널 개요](/microsoftteams/teams-channels-overview/)
