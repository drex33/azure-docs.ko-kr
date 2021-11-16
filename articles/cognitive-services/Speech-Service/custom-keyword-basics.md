---
title: 키워드 만들기 빠른 시작 - Speech Service
titleSuffix: Azure Cognitive Services
description: 디바이스는 항상 키워드(또는 구)를 수신 대기합니다. 사용자가 키워드를 말하면 장치는 사용자가 말하기를 중단할 때까지 사용자의 받아쓰기를 클라우드로 보냅니다. 키워드 사용자 지정은 디바이스를 차별화하고 브랜드를 강화하는 효과적인 방법입니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: eur
ms.custom: devx-track-csharp, ignite-fall-2021
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 59756ec624bfc4d716bcf222195f53029be05420
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493186"
---
# <a name="get-started-with-custom-keyword"></a>사용자 지정 키워드 시작

이 빠른 시작에서는 사용자 지정 키워드를 사용 하는 기본 사항에 대해 알아봅니다. 키워드는 제품이 음성으로 활성화 될 수 있도록 하는 단어 또는 짧은 문구입니다. Speech Studio에서 키워드 모델을 만듭니다. 그런 다음 응용 프로그램에서 음성 SDK를 사용 하 여 사용 하는 모델 파일을 내보냅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계에는 음성 구독과 음성 SDK가 필요 합니다. 구독이 없는 경우 [Speech Service 평가판을 사용해 보세요](overview.md#try-the-speech-service-for-free). SDK를 다운로드하려면 해당 플랫폼에 대한 [설치 가이드](quickstarts/setup-platform.md)를 참조하세요.

## <a name="create-a-keyword-in-speech-studio"></a>Speech Studio에서 키워드 만들기

사용자 지정 키워드를 사용하려면 [Speech Studio](https://aka.ms/sdsdk-speechportal)에서 [사용자 지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지를 사용하여 키워드를 만들어야 합니다. 키워드를 제공한 후에는 Speech SDK에서 사용할 수 있는 `.table` 파일을 생성합니다.

> [!IMPORTANT]
> 사용자 지정 키워드 모델 및 결과 `.table` 파일은 Speech Studio에서 **만** 만들 수 있습니다.
> SDK 또는 REST 호출을 사용하여 사용자 지정 키워드를 만들 수는 없습니다.

1. [Speech Studio](https://aka.ms/sdsdk-speechportal) 로 이동 하 여 **로그인** 합니다. 음성 구독이 없는 경우 [**음성 서비스 만들기**](https://go.microsoft.com/fwlink/?linkid=2086754)로 이동 합니다.

1. [사용자 지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지에서 **새 프로젝트 만들기** 를 선택 합니다. 

1. 사용자 지정 키워드 프로젝트의 **이름**, **설명** 및 **언어** 를 입력 합니다. 프로젝트 마다 하나의 언어만 선택할 수 있으며, 현재는 영어 (미국) 및 중국어 (북경어, 간체)로 지원 됩니다. 

    ![키워드 프로젝트 설명](media/custom-keyword/custom-kw-portal-new-project.png)

1. 목록에서 프로젝트 이름을 선택 합니다. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-project-list.png" alt-text="키워드 프로젝트를 선택 합니다.":::

1. 가상 길잡이에 대 한 사용자 지정 키워드를 만들려면 **새 모델 만들기** 를 선택 합니다.

1. 선택한 모델, **설명** 및 **키워드** 의 **이름을** 입력 하 고 **다음** 을 선택 합니다. 효과적인 키워드 선택에 대한 [지침](keyword-recognition-guidelines.md#choosing-an-effective-keyword)을 참조하세요.

    ![키워드 입력](media/custom-keyword/custom-kw-portal-new-model.png)

1. 포털에서는 키워드에 대한 후보 발음을 만듭니다. 재생 단추를 선택 하 고 잘못 된 발음 옆의 검사를 제거 하 여 각 후보를 수신 합니다. 사용자에 게 키워드를 표시 하는 방법에 해당 하는 모든 발음를 선택 하 고 **다음** 을 선택 하 여 키워드 모델 생성을 시작 합니다. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-choose-prons.png" alt-text="올바른 발음를 선택 하는 위치를 보여 주는 스크린샷":::

1. 모델 유형을 선택한 다음, **만들기** 를 선택 합니다. [키워드 인식 지역 지원](keyword-recognition-region-support.md) 설명서에서 **고급** 모델 유형을 지 원하는 지역 목록을 볼 수 있습니다. 

1. 모델을 생성 하는 데 최대 30 분이 걸릴 수 있습니다. 모델이 완료되면 키워드 목록이 **처리 중** 에서 **성공** 으로 변경됩니다. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-review-keyword.png" alt-text="키워드를 검토 합니다.":::

1. 왼쪽의 축소 가능 메뉴에서 **튜닝** 을 선택 하 여 모델을 튜닝 하 고 다운로드 합니다. 다운로드한 파일은 `.zip` 압축 파일입니다. 압축 파일을 추출하면 확장명이 `.table`인 파일이 표시됩니다. `.table`이 파일은 SDK와 함께 사용 되므로 경로를 기록해 두어야 합니다.

    :::image type="content" source="media/custom-keyword/custom-kw-portal-download-model.png" alt-text="모델 테이블을 다운로드 합니다.":::


## <a name="use-a-keyword-model-with-the-speech-sdk"></a>Speech SDK와 함께 키워드 모델 사용

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK 가져오기](speech-sdk.md)
