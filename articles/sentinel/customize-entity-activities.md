---
title: Microsoft Sentinel 엔터티 타임라인의 활동 사용자 지정 | Microsoft Docs
description: 엔터티 페이지의 타임라인에 표시되는 Microsoft Sentinel 트랙 및 표시에 사용자 지정된 활동 추가
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0483d839af7a8707aa3be632f9d168447488e768
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523622"
---
# <a name="customize-activities-on-entity-page-timelines"></a>엔터티 페이지 타임라인에서 활동 사용자 지정

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - 활동 사용자 지정은 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="introduction"></a>소개

Microsoft Sentinel에서 타임라인에 추적하고 제시하는 활동 외에도 추적하려는 다른 활동을 만들고 타임라인에도 표시할 수 있습니다. 연결된 데이터 원본에서 엔터티 데이터의 쿼리를 기반으로 사용자 지정된 활동을 만들 수 있습니다. 다음 예에서는 이 기능을 사용하는 방법을 보여 줍니다.

- 기존의 기본 제공 활동 템플릿을 수정하여 엔터티 타임라인에 새 활동을 추가합니다.

- 사용자 지정 로그(예: 물리적 액세스 제어 로그)에서 새 활동을 추가합니다. 특정 건물의 사용자 입출입 활동을 사용자의 타임라인에 추가할 수 있습니다.

## <a name="getting-started"></a>시작

1. Microsoft Sentinel 탐색 메뉴에서 **엔터티 동작 을** 선택합니다.

1. **엔터티 동작** 블레이드에서 화면 상단의 **엔터티 페이지 사용자 지정** 을 선택합니다.

    :::image type="content" source="./media/customize-entity-activities/entity-behavior-blade.png" alt-text="엔터티 동작 페이지":::

1. **내 활동** 탭에서 만든 활동 목록이 있는 페이지가 표시됩니다. **활동 템플릿** 탭에는 Microsoft 보안 연구원이 기본 제공하는 활동 모음이 표시됩니다. 엔터티 페이지의 타임라인에서 이미 추적되고 표시되는 활동입니다.

    > [!NOTE]
    > - 사용자 정의 활동을 생성하지 않은 한 엔터티 페이지에는 **활동 템플릿** 탭에 나열된 모든 활동이 표시됩니다.
    >
    > - 단일 사용자 지정 활동을 정의하면 **내 활동** 탭에 나타나는 활동 **만** 엔터티 페이지에 표시됩니다.
    >
    > - 엔터티 페이지에서 즉시 가능한 활동을 계속 보려면 추적 및 표시할 각 템플릿에 대한 활동을 생성해야 합니다. 아래의 "템플릿에서 활동 만들기"의 지침을 따르세요.

## <a name="create-an-activity-from-a-template"></a>템플릿에서 활동 만들기

1. **활동 템플릿** 탭을 클릭하면 기본적으로 사용 가능한 다양한 활동을 볼 수 있습니다. 엔터티 형식 및 데이터 원본별로 목록을 필터링할 수 있습니다. 목록에서 활동을 선택하면 미리 보기 창에 다음 세부 정보가 표시됩니다.

    -  활동에 대한 설명입니다.

    - 활동을 구성하는 이벤트를 제공하는 데이터 원본입니다.

    - 원시 데이터의 엔터티를 식별하는 데 사용되는 식별자입니다.

    - 이 활동을 검색하도록 하는 쿼리

1. 미리 보기 창 하단에 있는 **활동 만들기** 단추를 클릭하여 활동 만들기 마법사를 시작합니다.

    :::image type="content" source="./media/customize-entity-activities/activity-details.png" alt-text="활동 세부 정보 보기":::

1. **활동 마법사 - 템플릿에서 새 활동 만들기** 가 열리고 해당 필드가 템플릿에서 이미 채워집니다. **일반** 및 **활동 구성** 탭에서 원하는 대로 변경하거나 모든 항목을 그대로 유지하여 기본 활동을 계속 볼 수 있습니다.

1. 만족스러우면 **검토 및 만들기** 탭을 선택합니다. **유효성 검사 통과** 메시지가 표시되면 하단의 **만들기** 단추를 클릭합니다.

## <a name="create-an-activity-from-scratch"></a>처음부터 활동 만들기

활동 페이지 상단에서 **활동 추가** 를 클릭하여 활동 생성 마법사를 시작합니다.

**활동 마법사 - 새 활동 만들기** 가 열립니다. 해당 필드는 비어 있습니다.

### <a name="general-tab"></a>일반 탭
1. 활동의 이름을 입력합니다(예: "그룹에 추가된 사용자").

1. 활동에 대한 설명을 입력합니다(예: "Windows 이벤트 ID 4728에 따른 사용자 그룹 구성원 변경").

1. 이 쿼리에서 추적할 엔터티 유형(사용자 또는 호스트)을 선택합니다.

1. 쿼리를 구체화하고 성능을 최적화하는 데 도움이 되는 추가 매개 변수를 기준으로 필터링할 수 있습니다. 예를 들어 **IsDomainJoined** 매개 변수를 선택하고 값을 **True** 로 설정하여 Active Directory 사용자를 필터링할 수 있습니다.

1. 활동의 초기 상태를 **사용** 또는 **사용 안 함** 으로 선택할 수 있습니다.

1. **다음: 활동 구성** 을 선택하여 다음 탭으로 이동합니다.

    :::image type="content" source="./media/customize-entity-activities/create-new-activity.png" alt-text="스크린샷 - 새 활동 만들기":::

### <a name="activity-configuration-tab"></a>활동 구성 탭

#### <a name="writing-the-activity-query"></a>활동 쿼리 작성

여기서는 선택한 엔터티에 대한 활동을 검색하는 데 사용되는 KQL 쿼리를 작성하거나 붙여넣고 타임라인에서 표시되는 방법을 결정합니다.

이벤트의 상관 관계를 지정하고 사용자 지정 활동을 검색하기 위해 KQL은 엔터티 유형에 따라 여러 매개 변수를 입력해야 합니다. 매개 변수는 해당 엔터티의 다양한 식별자입니다.

쿼리 결과와 엔터티 간에 일대일 매핑을 만들려면 강력한 식별자를 선택하는 것이 더 좋습니다. 약한 식별자를 선택하면 부정확한 결과가 생성될 수 있습니다. [엔터티 및 강력한 식별자와 취약한 식별자에 대해 자세히 알아봅니다](entities-in-azure-sentinel.md).

다음 표에서는 엔터티의 식별자에 대한 정보를 제공합니다.

**계정 및 호스트 엔터티에 대한 강력한 식별자**

쿼리에는 하나 이상의 식별자가 필요합니다.

| 엔터티 | ID | Description |
| - | - | - |
| **계정** | Account_Sid | Active Directory에 있는 계정의 온-프레미스 SID |
| | Account_AadUserId | Azure Active Directory 사용자의 Azure AD 개체 ID |
| | Account_Name + Account_NTDomain | SamAccountName과 유사(예: Contoso\Joe) |
| | Account_Name + Account_UPNSuffix | UserPrincipalName과 유사(예: Joe@Contoso.com) |
| **Host** | Host_HostName + Host_NTDomain | FQDN(정규화된 도메인 이름)과 유사 |
| | Host_HostName + Host_DnsDomain | FQDN(정규화된 도메인 이름)과 유사 |
| | Host_NetBiosName + Host_NTDomain | FQDN(정규화된 도메인 이름)과 유사 |
| | Host_NetBiosName + Host_DnsDomain | FQDN(정규화된 도메인 이름)과 유사 |
| | Host_AzureID | Azure Active Directory 호스트의 Azure AD 개체 ID(AAD 도메인에 가입된 경우) |
| | Host_OMSAgentID | 특정 호스트에 설치된 에이전트의 OMS 에이전트 ID(호스트별) |
|

선택한 엔터티에 따라 사용 가능한 식별자가 표시됩니다. 관련 식별자를 클릭하여 커서의 위치에 있는 쿼리에 식별자를 붙여넣습니다.

> [!NOTE]
> - 쿼리에는 **최대 10개의 필드** 가 포함될 수 있으므로 원하는 필드를 예상해야 합니다.
>
> - 검색된 활동을 엔터티의 타임라인에 배치하려면 예상한 필드에 **TimeGenerated** 필드가 포함되어야 합니다.

```kusto
SecurityEvent
| where EventID == "4728"
| where (SubjectUserSid == '{{Account_Sid}}' ) or (SubjectUserName == '{{Account_Name}}' and SubjectDomainName == '{{Account_NTDomain}}' )
| project TimeGenerated, SubjectUserName, MemberName, MemberSid, GroupName=TargetUserName
```

:::image type="content" source="./media/customize-entity-activities/new-activity-query.png" alt-text="스크린샷 - 활동을 검색하는 쿼리 입력":::

#### <a name="presenting-the-activity-in-the-timeline"></a>타임라인에 활동 표시

편의를 위해 활동 출력에 동적 매개 변수를 추가하여 활동이 타임라인에 표시되는 방식을 결정할 수 있습니다.

Microsoft Sentinel은 사용할 수 있는 기본 제공 매개 변수를 제공하며, 쿼리에서 프로젝션한 필드를 기반으로 다른 매개 변수를 사용할 수도 있습니다.

`{{ParameterName}}` 매개 변수에 다음 형식을 사용합니다.

활동 쿼리가 유효성 검사를 통과하고 쿼리 창 아래에 **쿼리 결과 보기** 링크가 표시되면 **사용 가능한 값** 섹션을 확장하여 동적 활동 제목을 만들 때 사용할 수 있는 매개 변수를 볼 수 있습니다.

특정 매개 변수 옆에 있는 **복사** 아이콘을 선택하여 해당 매개 변수를 클립보드에 복사해 위의 **작업 제목** 입력란에 붙여넣을 수 있습니다.

쿼리에 다음 매개 변수를 추가합니다.

- 쿼리에서 예상한 모든 필드

- 쿼리에 언급된 엔터티의 엔터티 식별자

- `StartTimeUTC`, 활동 시작 시간(UTC 시간)을 추가합니다.

- `EndTimeUTC`, 활동 종료 시간(UTC 시간)을 추가합니다.

- `Count`, 여러 KQL 쿼리 출력을 단일 출력으로 요약합니다.

    `count` 매개 변수는 편집기에 완전히 표시되지 않더라도 백그라운드에서 쿼리에 다음 명령을 추가합니다.

    ```kql
    Summarize count() by <each parameter you’ve projected in the activity>
    ```

    그런 다음, 항목 페이지에서 **버킷 크기** 필터를 사용하면 백그라운드에서 실행되는 쿼리에 다음 명령어도 추가됩니다.

    ```kql
    Summarize count() by <each parameter you’ve projected in the activity>, bin (TimeGenerated, Bucket in Hours)
    ```

예를 들면 다음과 같습니다.

:::image type="content" source="./media/customize-entity-activities/new-activity-title.png" alt-text="스크린샷 - 활동 제목에 사용 가능한 값 확인":::

쿼리 및 활동 제목이 만족스러우면 **다음: 검토** 를 선택합니다.

### <a name="review-and-create-tab"></a>검토 및 만들기 탭

1. 사용자 지정 활동의 모든 구성 정보를 확인합니다.

1. **유효성 검사 통과** 메시지가 나타나면 **만들기** 를 클릭하여 활동을 만듭니다. 나중에 **내 활동** 탭에서 편집하거나 변경할 수 있습니다.

## <a name="manage-your-activities"></a>활동 관리 

**내 활동** 탭에서 사용자 지정 활동을 관리합니다. 활동 행 끝에 있는 줄임표(...)를 클릭하여 다음을 수행합니다.

- 활동을 편집합니다.
- 활동을 복제하여 약간 다른 새 활동을 만듭니다.
- 활동을 삭제합니다.
- 활동을 삭제하지 않고 사용하지 않도록 설정합니다.

## <a name="view-activities-in-an-entity-page"></a>엔터티 페이지에서 활동 보기

엔터티 페이지를 입력할 때마다 해당 엔터티에 대해 사용하는 모든 활동 쿼리가 실행되어 엔터티 타임라인에 최신 정보가 제공됩니다. 타임라인에 경고 및 책갈피와 함께 활동이 표시됩니다. 

**타임라인 콘텐츠** 필터를 사용하여 활동(또는 활동, 경고 및 책갈피의 조합)만 표시할 수 있습니다.  

**활동** 필터를 사용하여 특정 활동을 표시하거나 숨길 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 엔터티 페이지 타임라인에 대한 사용자 지정 활동을 만드는 방법을 알아보았습니다. Microsoft Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [엔터티 페이지](identify-threats-with-entity-behavior-analytics.md)에서 전체 그림을 가져옵니다.
- [엔터티 및 식별자](entities-reference.md)의 전체 목록을 참조하세요.
