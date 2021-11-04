---
title: 검토 도구 설정 구성 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 검토 도구를 사용하여 Content Moderator에 대 한 팀, 태그, 커넥터, 워크플로, 자격 증명을 구성하거나 검색합니다.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: pafarley
ms.openlocfilehash: 374957c8175f320b5152abcb661516cad5324304
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426395"
---
# <a name="configure-the-review-tool"></a>검토 도구 구성

[!INCLUDE [deprecation notice](../includes/tool-deprecation.md)]


[검토 도구](https://contentmoderator.cognitive.microsoft.com)에는 대시보드의 **설정** 메뉴를 통해 액세스할 수 있는 몇 가지 중요한 기능이 있습니다.

![Content Moderator 검토 도구 설정 메뉴](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>팀 및 하위 팀 관리

**팀** 탭에서는 특정 [사용자 검토](../review-api.md#reviews)가 시작될 때 알림을 받을 수 있는 사용자 팀 및 하위 팀&mdash;그룹을 관리할 수 있습니다. 검토 도구로 등록할 때 만드는 팀은 하나만 가능하지만 하위 팀은 여러 개 만들 수 있습니다. 팀 관리자는 구성원을 초대하고, 구성원의 사용 권한을 설정하고, 구성원을 다른 하위 팀에 할당할 수 있습니다.

![검토 도구 팀 설정](images/settings-2-team.png)

하위 팀은 에스컬레이션 팀 또는 콘텐츠의 특정 범주를 검토하는 전담 팀을 만들 때 유용합니다. 예를 들어 추가 검토를 위해 성인 콘텐츠를 별도의 팀에 보낼 수 있습니다.

이 섹션에서는 하위 팀을 만들고, 필요할 때 신속하게 검토를 할당하는 방법을 설명합니다. 하지만 [워크플로](workflows.md)를 사용하여 특정 기준에 따라 검토를 할당할 수도 있습니다.

### <a name="create-a-subteam"></a>하위 팀 만들기

**하위 팀** 섹션으로 이동하여 **하위 팀 추가** 를 클릭합니다. 대화 상자에서 하위 팀 이름을 입력하고 **저장** 을 클릭합니다.

![하위 팀 이름](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>팀 동료 초대

아직 기본 팀의 구성원이 아닌 사람을 하위 팀에 할당할 수 없으므로 검토자를 먼저 기본 팀에 추가해야 합니다. **팀** 탭에서 **초대** 를 클릭합니다.

![사용자 초대](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>하위 팀에 팀 동료 할당

**구성원 추가** 단추를 클릭하여 기본 팀의 구성원을 하나 이상의 하위 팀에 할당합니다. 기존 사용자만 하위 팀에 추가할 수 있습니다. 검토 도구에 없는 새 사용자를 추가하려면 [팀 설정] 페이지에서 "초대" 단추를 사용하여 해당 사용자를 초대해야 합니다.

![하위 팀 멤버 할당](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>하위 팀에 검토 할당

하위 팀을 만들고 구성원을 할당하고 나면 해당 하위 팀에 콘텐츠 [검토](../review-api.md#reviews) 할당을 시작할 수 있습니다. 이 작업은 사이트의 **검토** 탭에서 수행됩니다.
한 하위 팀에 콘텐츠를 할당하려면 오른쪽 위 모서리에 있는 줄임표를 클릭하고, **다음으로 이동** 을 선택하고 한 하위 팀을 선택합니다.

![하위 팀에 이미지 검토 할당](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>하위 팀 간에 전환

둘 이상의 하위 팀의 구성원인 경우 해당 하위 팀 간에 전환하여 표시되는 콘텐츠 검토를 변경할 수 있습니다. **검토** 탭에서 **기본** 이라고 레이블이 지정된 드롭다운 메뉴를 선택하고 **하위 팀 선택** 을 선택합니다. 다른 하위 팀용 콘텐츠 검토를 볼 수 있지만 본인이 구성원인 하위 팀에 한합니다.

![하위 팀 간에 전환](images/3-review-image-subteam-2.png)

## <a name="tags"></a>태그들

**태그** 탭에서는 두 개의 기본 조정 태그&mdash;**isadult**(**a**) 및 **isracy**(**r**) 외에 사용자 지정 조정 태그를 정의할 수 있습니다. 사용자 지정 태그를 만들면 기본 태그와 함께 검토에서 사용할 수 있게 됩니다. 표시 여부 설정을 전환하여 검토에 표시되는 태그를 변경할 수 있습니다.

![태그 보기("표시됨" 확인란 포함)](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>사용자 지정 태그 만들기

새 태그를 만들려면 해당 필드에 짧은 코드, 이름, 설명을 입력해야 합니다.

- **짧은 코드**: 태그에 두 글자 코드를 입력합니다. 예: **cb**
- **이름**: 공백 없이 소문자로 짧고 설명이 포함된 태그 이름을 입력합니다. 예: **isbullying**
- **설명**: (선택 사항) 태그가 대상으로 지정하는 콘텐츠 종류에 대한 설명을 입력합니다. 예: **사이버 괴롭힘의 묘사 또는 사례**

**추가** 를 클릭하여 태그를 추가하고 태그 만들기를 마치면 **저장** 을 클릭합니다.

![검토 도구 새 태그 만들기 대화 상자](images/settings-3-tags.png)

### <a name="delete-tags"></a>태그 삭제

태그 목록에서 해당 항목 옆에 있는 휴지통 아이콘을 선택하여 사용자 지정 태그를 삭제할 수 있지만 기본 태그는 삭제할 수 없습니다.

## <a name="connectors"></a>커넥터

**커넥터** 탭을 사용하면 콘텐츠 [워크플로](../review-api.md#workflows)의 일부로서 다양한 방식으로 콘텐츠를 처리할 수 있는 서비스 관련 플러그 인인 커넥터를 관리할 수 있습니다.

워크플로를 만들 때 기본 커넥터는 Content Moderator 커넥터로서, 콘텐츠를 **성인** 또는 **외설** 로 표시하고 욕설을 찾는 등의 작업을 할 수 있습니다. 그러나 해당되는 서비스에 대한 자격 증명이 있기만 하다면 여기에 나열된 다른 커넥터를 사용할 수 있습니다(예: Face 커넥터를 사용하려면 [Face](../../face/overview.md) 구독 키를 받아야 함).

[검토 도구](./human-in-the-loop.md) 에는 다음 커넥터가 포함되어 있습니다.

- 감정
- Face
- PhotoDNA 클라우드 서비스
- 언어 서비스

### <a name="add-a-connector"></a>커넥터 추가

커넥터를 추가하고 콘텐츠 [워크플로](../review-api.md#workflows)에서 사용할 수 있도록 하려면 적절한 **연결** 단추를 선택합니다. 다음 대화 상자에서 해당 서비스에 대한 구독 키를 입력합니다. 완료되면 새 커넥터가 페이지 맨 위에 표시되어야 합니다.

![Content Moderator 커넥터 설정](images/settings-4-connectors.png)

## <a name="workflows"></a>워크플로

**워크플로** 탭에서 [워크플로](../review-api.md#workflows)를 관리할 수 있습니다. 워크플로는 콘텐츠에 대한 클라우드 기반의 필터이며 커넥터와 함께 작동하여 다양한 방식으로 콘텐츠를 정렬하고 적절한 조치를 취합니다. 여기에서 워크플로를 정의하고, 편집하고, 테스트할 수 있습니다. 이 작업을 수행하는 방법에 대한 참고 자료는 [워크플로 정의 및 사용](Workflows.md)을 참조하세요.

![Content Moderator 워크플로 설정](images/settings-5-workflows.png)

## <a name="credentials"></a>자격 증명

**자격 증명** 탭에서는 Content Moderator 구독 키에 빠르게 액세스할 수 있으며 이는 REST 호출 또는 클라이언트 SDK에서 조정 서비스에 액세스하는 데 필요합니다.

![Content Moderator 자격 증명](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>워크플로에 외부 자격 증명 사용

[검토 도구](https://contentmoderator.cognitive.microsoft.com)는 등록할 때 Azure Content Moderator 서비스용 평가판 키를 생성하지만 Azure 계정의 기존 키를 사용하도록 구성할 수도 있습니다. 평가판 키에는 엄격한 사용 제한([가격 책정 및 제한](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/))이 있으므로 대규모 시나리오에 이 방법이 권장됩니다.

Azure에서 [Content Moderator 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)를 만든 경우 Azure Portal에서 해당 리소스로 이동하여 **키** 블레이드를 선택합니다. 키 중 하나를 복사합니다.

![Azure Portal의 Content Moderator 키](images/credentials-azure-portal-keys.PNG)

[검토 도구](https://contentmoderator.cognitive.microsoft.com)의 **자격 증명** 탭에서 **워크플로 설정** 창으로 이동하여 **편집** 을 선택하고 **Ocp-Apim-Subscription-Key** 필드에 키를 붙여넣습니다. 이제 조정 API를 호출하는 워크플로는 Azure 자격 증명을 사용합니다.

> [!NOTE]
> **워크플로 설정** 창의 다른 두 필드는 사용자 지정 용어 및 이미지 목록용입니다. 이에 대해 알아보려면 [사용자 지정 용어](../try-terms-list-api.md) 또는 [사용자 지정 이미지](../try-image-list-api.md) 가이드를 참조하세요.

### <a name="use-your-azure-account-with-the-review-apis"></a>검토 API와 함께 Azure 계정 사용

검토 API와 함께 Azure 키를 사용하려면 리소스 ID를 검색해야 합니다. Azure Portal에서 Content Moderator 리소스로 이동하여 **속성** 블레이드를 선택합니다. 리소스 ID 값을 복사하여 검토 도구의 **자격 증명** 탭에 있는 **허용 목록 리소스 ID** 필드에 붙여넣습니다.

![Azure Portal의 Content Moderator 리소스 ID](images/credentials-azure-portal-resourceid.PNG)

두 위치 모두에 구독 키를 입력한 경우 검토 도구 계정과 함께 제공되는 평가판 키는 사용되지는 않지만 사용 가능한 상태로 유지됩니다.

## <a name="next-steps"></a>다음 단계

[검토 도구 빠른 시작](../quick-start.md)을 따라 콘텐츠 조정 시나리오에서 검토 도구를 사용하기 시작합니다.