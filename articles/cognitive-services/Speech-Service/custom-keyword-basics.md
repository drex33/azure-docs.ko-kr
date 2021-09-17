---
title: 키워드 만들기 빠른 시작 - Speech Service
titleSuffix: Azure Cognitive Services
description: 디바이스는 항상 키워드(또는 구)를 수신 대기합니다. 사용자가 키워드를 말하면 사용자가 말하기를 중지할 때까지 디바이스에서 모든 후속 오디오를 클라우드로 보냅니다. 키워드 사용자 지정은 디바이스를 차별화하고 브랜드를 강화하는 효과적인 방법입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: pafarley
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 8cf0da3ca3787580513d818a2c0ebca5a07829b6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541102"
---
# <a name="get-started-with-custom-keyword"></a>사용자 지정 키워드 시작

이 빠른 시작에서는 Speech Studio 및 Speech SDK를 사용한 사용자 지정 키워드 작업에 대한 기본 사항을 알아봅니다. 키워드는 제품을 음성으로 활성화할 수 있는 단어 또는 짧은 문구입니다. Speech Studio에서 키워드 모델을 만든 다음 애플리케이션에서 Speech SDK와 함께 사용하는 모델 파일을 내보냅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계에는 Speech 구독과 Speech SDK가 필요합니다. 구독이 없는 경우 [Speech Service 평가판을 사용해 보세요](overview.md#try-the-speech-service-for-free). SDK를 다운로드하려면 해당 플랫폼에 대한 [설치 가이드](quickstarts/setup-platform.md)를 참조하세요.

## <a name="create-a-keyword-in-speech-studio"></a>Speech Studio에서 키워드 만들기

사용자 지정 키워드를 사용하려면 [Speech Studio](https://aka.ms/sdsdk-speechportal)에서 [사용자 지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지를 사용하여 키워드를 만들어야 합니다. 키워드를 제공한 후에는 Speech SDK에서 사용할 수 있는 `.table` 파일을 생성합니다.

> [!IMPORTANT]
> 사용자 지정 키워드 모델 및 결과 `.table` 파일은 Speech Studio에서 **만** 만들 수 있습니다.
> SDK 또는 REST 호출을 사용하여 사용자 지정 키워드를 만들 수는 없습니다.

1. [Speech Studio](https://aka.ms/sdsdk-speechportal)로 이동하여 **로그인** 하거나 음성 구독이 아직 없는 경우 [**구독 만들기**](https://go.microsoft.com/fwlink/?linkid=2086754)를 선택합니다.

1. [사용자 지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지에서 **새 프로젝트** 를 만듭니다. 

1. **이름**, **설명**(선택 사항)을 입력하고 언어를 선택합니다. 언어당 하나의 프로젝트가 필요하며 현재는 영어(미국) 및 중국어(북경어, 간체)로 지원됩니다.

    ![키워드 프로젝트 설명](media/custom-keyword/custom-kws-portal-new-project.png)

1. 목록에서 프로젝트를 선택합니다. 

    ![키워드 프로젝트 선택](media/custom-keyword/custom-kws-portal-project-list.png)

1. 새 키워드 모델을 만들려면 **모델 학습** 을 클릭합니다.

1. 모델 **이름**, **설명**(선택 사항) 및 선택한 **키워드** 를 입력하고 **다음** 을 클릭합니다. 효과적인 키워드 선택에 대한 [지침](keyword-recognition-guidelines.md#choosing-an-effective-keyword)을 참조하세요.

    ![키워드 입력](media/custom-keyword/custom-kws-portal-new-model.png)

1. 포털에서는 키워드에 대한 후보 발음을 만듭니다. 재생 단추를 클릭하여 각 후보를 듣고 잘못된 발음 옆에 있는 확인 표시를 제거합니다. 좋은 발음만 선택한 상태에서 **학습** 을 클릭하여 키워드 모델 생성을 시작합니다. 

    ![올바른 발음을 선택하는 위치를 보여 주는 스크린샷](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 모델이 생성되는 데는 최대 30분이 걸릴 수 있습니다. 모델이 완료되면 키워드 목록이 **처리 중** 에서 **성공** 으로 변경됩니다. 그러면 파일을 다운로드할 수 있습니다.

    ![키워드 검토](media/custom-keyword/custom-kws-portal-download-model.png)

1. 다운로드한 파일은 `.zip` 압축 파일입니다. 압축 파일을 추출하면 확장명이 `.table`인 파일이 표시됩니다. 이 파일은 다음 섹션에서 SDK와 함께 사용되므로 해당 경로를 적어 둡니다. 파일 이름은 키워드 이름과 비슷합니다. 예를 들어 **Activate device** 라는 키워드의 파일 이름은 `Activate_device.table`입니다.

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
