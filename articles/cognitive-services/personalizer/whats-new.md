---
title: 새로운 기능 - Personalizer
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Personalizer에 대한 뉴스가 포함되어 있습니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/28/2021
ms.openlocfilehash: 4c4d4151342211712f63d254630cc480dd761235
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830242"
---
# <a name="whats-new-in-personalizer"></a>Personalizer의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 이러한 항목에는 릴리스 정보, 비디오, 블로그 게시물 및 기타 유형의 정보가 포함될 수 있습니다. 서비스를 최신 상태로 유지하려면 이 페이지에 책갈피를 설정합니다.

## <a name="release-notes"></a>릴리스 정보

### <a name="may-2021----build-conference"></a>2021년 5월 - //빌드 컨퍼런스

* 자동 최적화(미리 보기) : 시간이 지남에 따라 더 적은 작업으로 지속적으로 개선하기 위해 사용하는 Personalizer 루프를 구성할 수 있습니다. Personalizer는 오프라인 평가를 자동으로 실행하고, 더 나은 기계 학습 설정을 검색하여 적용합니다. 자세한 내용은 [Personalizer 자동 최적화(미리 보기)](concept-auto-optimization.md)를 참조하세요.
* 다중 슬롯 개인 설정(미리 보기): 타일 레이아웃, 회전식 및/또는 사이드바가 있는 경우 동일한 페이지에서 제품 또는 콘텐츠를 권장하는 각 위치에 Personalizer를 사용하는 것이 더 쉽습니다. 이제 Personalizer는 순위 API에서 슬롯 목록을 만들고, 각 슬롯에 작업을 할당하고, 각 슬롯에 대해 보내는 보상 점수에서 학습할 수 있습니다. 자세한 내용은 [다중 슬롯 개인 지정(미리 보기)](concept-multi-slot-personalization.md)을 참조하세요.
* Personalizer는 이제 더 많은 지역에서 사용할 수 있습니다.
* 업데이트된 코드 샘플(GitHub) 및 설명서. 아래 링크를 사용하여 업데이트된 샘플을 확인합니다.
  * [C#/.NET](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)
  * [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)
  * [Python 샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

### <a name="july-2020"></a>2020년 7월

* 새 자습서 - [채팅 봇에서 Personalizer 사용하기](tutorial-use-personalizer-chat-bot.md)

### <a name="june-2020"></a>2020년 6월

* 새 자습서 - [웹앱에서 Personalizer 사용하기](tutorial-use-personalizer-web-app.md)

### <a name="may-2020---build-conference"></a>2020년 5월 - //빌드 컨퍼런스

**공개 미리 보기** 에서는 다음을 사용할 수 있습니다.

 * 학습 동작으로 [실습생 모드](concept-apprentice-mode.md)

### <a name="march-2020"></a>2020년 3월

* 이제 TLS 1.2는 이 서비스에 대한 모든 HTTP 요청에 적용됩니다. 자세한 내용은 [Azure Cognitive Services 보안](../cognitive-services-security.md)을 참조하세요.

### <a name="november-2019---ignite-conference"></a>2019년 11월 - Ignite 컨퍼런스

* Personalizer는 GA(일반 공급)임
* Azure Notebooks [자습서](tutorial-use-azure-notebook-generate-loop-data.md)(전체 수명 주기)

### <a name="may-2019---build-conference"></a>2019년 5월 - //빌드 컨퍼런스

2019 빌드 컨퍼런스에서는 다음 미리 보기 기능이 발표되었습니다.

* [순위 및 보상 학습 루프](what-is-personalizer.md)

## <a name="videos"></a>동영상

### <a name="2019-build-videos"></a>2019 빌드 동영상

* [Cognitive Services Personalizer로 Xbox와 같은 콘텐츠 및 적절한 환경 제공](https://azure.microsoft.com/resources/videos/build-2019-deliver-the-right-experiences-and-content-with-cognitive-services-personalizer/)

## <a name="service-updates"></a>서비스 업데이트

[Cognitive Services에 대한 Azure 업데이트 공지](https://azure.microsoft.com/updates/?product=cognitive-services)

## <a name="next-steps"></a>다음 단계

* [빠른 시작: C#으로 피드백 루프 만들기](./quickstart-personalizer-sdk.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [대화형 데모 사용](https://personalizationdemo.azurewebsites.net/)