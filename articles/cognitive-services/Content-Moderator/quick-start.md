---
title: '빠른 시작: 웹에서 Content Moderator 사용해 보기'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 코드를 작성할 필요 없이 온라인 Content Moderator 검토 도구를 사용하여 Content Moderator의 기본 기능을 테스트합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 09/28/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 콘텐츠 조정자, 콘텐츠 조정
ms.openlocfilehash: 0f600e2c0a7364088e68c70cd6eac90ee31c1b70
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361139"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>빠른 시작: 웹에서 Content Moderator 사용해 보기

[!INCLUDE [deprecation notice](includes/tool-deprecation.md)]

이 빠른 시작에서는 코드를 작성할 필요 없이 온라인 Content Moderator 검토 도구를 사용하여 Content Moderator의 기본 기능을 테스트합니다. 이 서비스를 콘텐츠 조정 앱에 보다 신속하게 통합하려면 [다음 단계](#next-steps) 섹션에서 다른 빠른 시작을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 웹 브라우저

## <a name="set-up-the-review-tool"></a>검토 도구 설정
Content Moderator 검토 도구는 사람 검토자가 의사를 결정할 때 인식 서비스를 지원할 수 있는 웹 기반 도구입니다. 이 가이드에서는 검토 도구를 설정하는 간단한 프로세스를 통해 Content Moderator 서비스의 작동 원리를 알아보겠습니다. [Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트로 이동하여 가입합니다.

![Content Moderator 홈페이지](images/homepage.PNG)

## <a name="create-a-review-team"></a>검토 팀 만들기

다음으로, 검토 팀을 만듭니다. 작업 시나리오에서 이 팀은 서비스의 조정 결정을 수동으로 검토하는 사용자 그룹입니다. 팀을 만들려면 **지역** 을 선택하고 **팀 이름** 및 **팀 ID** 를 입력해야 합니다. 동료를 팀에 초대하려면 여기에 이메일 주소를 입력하면 됩니다.

> [!NOTE]
> **팀 이름** 은 검토 팀의 식별 이름입니다. 이 이름은 Azure Portal에 표시됩니다. **팀 ID** 는 검토 팀을 프로그래밍 방식으로 식별하는 데 사용됩니다.

> [!div class="mx-imgBorder"]
> ![팀 구성원 초대](images/create-team.png)

CMK(고객 관리형 키)를 사용하여 데이터를 암호화하도록 선택하면 E0 가격 책정 계층의 Content Moderator 리소스에 대한 **리소스 ID** 를 묻는 메시지가 표시됩니다. 제공하는 리소스는 이 팀에 고유해야 합니다. 

> [!div class="mx-imgBorder"]
> ![CMK로 팀 구성원 초대](images/create-team-cmk.png)

## <a name="upload-sample-content"></a>샘플 콘텐츠 업로드

이제 샘플 콘텐츠를 업로드할 준비가 완료되었습니다. **시도 > 이미지**, **시도 > 텍스트** 또는 **시도 > 비디오** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![이미지 또는 텍스트 조정 시도](images/tryimagesortext.png)

조정할 콘텐츠를 제출합니다. 다음 샘플 텍스트 콘텐츠를 사용할 수 있습니다.

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

내부적으로, 검토 도구는 조정 API를 호출하여 콘텐츠를 검사합니다. 검사가 완료되면 검토 대기 중인 결과가 있음을 알리는 메시지가 표시됩니다.

> [!div class="mx-imgBorder"]
> ![파일 조정](images/submitted.png)

## <a name="review-moderation-tags"></a>조정 태그 검토

적용된 조정 태그를 검토합니다. 콘텐츠에 적용된 태그와 각 범주의 점수를 볼 수 있습니다. [이미지](image-moderation-api.md), [텍스트](text-moderation-api.md) 및 [비디오](video-moderation-api.md) 조정 문서를 참조하여 다른 콘텐츠 태그가 무엇을 나타내는지 자세히 알아보세요.

<!-- ![Review results](images/reviewresults_text.png) -->

검토 팀은 프로젝트에서 이러한 태그를 필요한 대로 변경하거나 추가할 수 있습니다. **다음** 단추를 사용하여 변경 내용을 제출합니다. 비즈니스 애플리케이션이 Moderator API를 호출하면 태그가 지정된 콘텐츠가 큐에 추가되고, 사람 검토 팀이 검토할 수 있게 됩니다. 이 방법을 사용하면 대량의 콘텐츠를 빠르게 검토할 수 있습니다.

여기서는 Content Moderator 검토 도구를 사용하여 Content Moderator 서비스로 할 수 있는 작업의 예제를 살펴보았습니다. 다음으로, 검토 도구에 대한 자세한 내용 및 검토 API를 사용하여 검토 도구를 소프트웨어 프로젝트에 통합하는 방법을 알아볼 수도 있고, 앱에서 조정 API 자체를 사용하는 방법을 알아보는 [다음 단계](#next-steps) 섹션으로 건너뛸 수도 있습니다.

## <a name="learn-more-about-the-review-tool"></a>검토 도구에 대한 자세한 정보

Content Moderator 검토 도구를 사용하는 방법에 대해 자세히 알아보려면 [검토 도구](Review-Tool-User-Guide/human-in-the-loop.md) 가이드를 살펴보고, 검토 도구 API를 통해 사람 검토 환경을 미세 조정하는 방법을 알아보세요.
- [작업 API](try-review-api-job.md)는 조정 API를 사용하여 콘텐츠를 검사하고 검토 도구에서 검토를 생성합니다. 
- [검토 API](try-review-api-review.md)는 먼저 콘텐츠를 검사하지 않고 사용자 조정자를 위해 이미지, 텍스트 또는 비디오 검토를 직접 만듭니다. 
- [워크플로 API](try-review-api-workflow.md)는 팀이 만드는 사용자 지정 워크플로에 대한 세부 정보를 만들고, 업데이트하고, 가져옵니다.

또는 다음 단계를 계속 진행하여 코드에서 Moderation API 사용을 시작하세요.

## <a name="next-steps"></a>다음 단계

앱에서 Moderation API 자체를 사용하는 방법을 알아봅니다.
- 이미지 조정을 구현합니다. [API 콘솔](try-image-api.md)을 사용하거나 [C# 빠른 시작](client-libraries.md)을 따라 이미지를 검사하고 태그, 신뢰도 점수, 기타 추출된 정보를 사용하여 성인/외설 콘텐츠를 검색합니다.
- 텍스트 조정을 구현합니다. [API 콘솔](try-text-api.md)을 사용하거나 [빠른 시작](client-libraries.md)을 따라 텍스트 콘텐츠에서 잠재적 비속어, 개인 데이터 및 기타 원치 않는 텍스트를 검사합니다.
- 비디오 조정을 구현합니다. [C#의 비디오 조정 방법 가이드](video-moderation-api.md)에 따라 비디오를 검사하고 성인/외설 콘텐츠를 검색합니다. 
