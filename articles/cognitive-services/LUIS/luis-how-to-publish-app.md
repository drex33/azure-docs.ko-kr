---
title: 앱 게시 - LUIS
titleSuffix: Azure Cognitive Services
description: 활성 LUIS 빌드와 테스트를 마치면 엔드포인트에 게시하여 클라이언트 애플리케이션에 적용하세요.
services: cognitive-services
author: aahill
manager: nitinme
ms.author: aahi
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 10/28/2021
ms.openlocfilehash: 8c006a47f6967be5387e462a2387d1b59195b685
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430784"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>스테이징 또는 프로덕션 엔드포인트에 활성 상태의 학습된 앱 게시

활성 LUIS 앱을 빌드하고, 학습하고, 테스트를 마치면 엔드포인트에 게시하여 클라이언트 애플리케이션에 적용하세요.

## <a name="publishing"></a>게시
1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. 엔드포인트에 게시하려면 맨 위 오른쪽 패널에서 **게시** 를 선택하세요.

    ![오른쪽 위에 있는 탐색 모음의 게시 단추](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. 게시된 예측 엔드포인트에 대한 설정을 선택한 다음, **게시** 를 선택합니다.

    ![게시 설정을 선택한 다음, 게시 단추를 선택합니다.](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>슬롯 게시

팝업 창이 표시되면 올바른 슬롯을 선택합니다.

* 준비
* 프로덕션

두 게시 슬롯을 모두 사용하면 게시된 엔드포인트에서 두 가지 서로 다른 버전의 앱을 사용하거나 두 가지 다른 엔드포인트에서 동일한 버전을 사용할 수 있습니다.

### <a name="publishing-regions"></a>게시 지역

앱은 **관리** ->  **[Azure 리소스](luis-how-to-azure-subscription.md#assign-luis-resources)** 페이지에서 LUIS 포털에 추가된 LUIS 예측 엔드포인트 리소스와 연결된 모든 지역에 게시됩니다.

예를 들어, [www.luis.ai](https://www.luis.ai)에서 만든 앱의 경우 **westus** 와 **eastus** 의 두 지역에서 LUIS 리소스를 만들고 이를 앱에 리소스로 추가하면 두 지역 모두에 해당 앱이 게시됩니다. LUIS 지역에 대한 자세한 내용은 [지역](luis-reference-regions.md)을 참조하세요.

> [!TIP]
> 3개의 작성 지역이 있습니다. 게시하려는 지역에서 작성해야 합니다. 모든 지역에 게시해야 하는 경우 3개의 작성 지역 모두에서 작성 프로세스와 그에 따른 학습 모델을 관리해야 합니다.


## <a name="configuring-publish-settings"></a>게시 설정 구성

슬롯을 선택한 후 다음에 대한 게시 설정을 구성합니다.

* 정서 분석
* 음성 초기화

게시한 후에는 **관리** 섹션의 **게시 설정** 페이지에서 이러한 설정을 검토할 수 있습니다. 게시할 때마다 설정을 변경할 수 있습니다. 게시를 취소하면 게시 중에 적용한 변경 내용도 취소됩니다.

### <a name="when-your-app-is-published"></a>앱이 게시되는 경우

앱이 성공적으로 게시되면 브라우저 위쪽에 성공 알림이 표시됩니다. 알림에는 엔드포인트에 대한 링크도 포함됩니다.

엔드포인트 URL이 필요한 경우 링크를 선택하세요. 상단 메뉴에서 **관리** 를 선택한 다음, 왼쪽 메뉴에서 **Azure 리소스** 를 선택하여 엔드포인트 URL로 이동할 수도 있습니다.

## <a name="sentiment-analysis"></a>정서 분석

<a name="enable-sentiment-analysis"></a>

감정 분석을 사용하면 LUIS를 [언어 서비스와](https://azure.microsoft.com/services/cognitive-services/text-analytics/) 통합하여 감정 및 핵심 구 분석을 제공할 수 있습니다.

언어 서비스 키를 제공할 필요가 없으며 Azure 계정에 이 서비스에 대한 청구 요금은 없습니다.

감정 데이터는 데이터의 긍정적(1에 가까움) 또는 부정적(0에 가까움) 감정을 나타내는 1과 0 사이의 점수입니다. `positive`, `neutral` 및 `negative`의 감정 레이블은 지원되는 문화를 따릅니다. 현재 영어만 감정 레이블을 지원합니다.

감정 분석에서 JSON 엔드포인트의 응답에 대한 자세한 내용은 [감정 분석](luis-reference-prebuilt-sentiment.md)을 참조하세요.

## <a name="speech-priming"></a>음성 초기화

음성 프라이밍은 텍스트를 음성으로 변환하기 전에 Speech Services로 LUIS 모델을 보내는 프로세스입니다. 이를 통해 Speech Service는 모델에 대해 음성 변환을 보다 정확하게 제공할 수 있습니다. 이를 통해 한 번의 음성 통화를 수행하고 LUIS 응답을 다시 받음으로써 봇 음성 및 LUIS 요청과 응답을 한 번에 호출합니다. 전반적인 대기 시간이 단축됩니다.

## <a name="next-steps"></a>다음 단계

* LUIS에 대한 Azure 구독 키에 키를 추가하고 Bing Spell Check 키를 설정하고 결과에 모든 의도를 포함하는 방법은 [키 관리](./luis-how-to-azure-subscription.md)를 참조하세요.
* 테스트 콘솔에서 게시된 앱을 테스트하는 방법에 대한 지침은 [앱 학습 및 테스트](luis-interactive-test.md)를 참조하세요.

