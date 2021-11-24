---
title: '빠른 시작: SDK를 통한 학습 루프 만들기 및 사용 - Personalizer'
description: 이 빠른 시작에서는 Personalizer 클라이언트 라이브러리를 사용하여 기술 자료를 만들고 관리하는 방법을 보여줍니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020, mode-other
keywords: personalizer, Azure personalizer, 기계 학습
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: f00250cc7d4f375b62b8e24f0613da830bd8425b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133062319"
---
# <a name="quickstart-personalizer-client-library"></a>빠른 시작: Personalizer 클라이언트 라이브러리

이 빠른 시작에서는 Personalizer 서비스를 사용하여 맞춤형 콘텐츠를 표시합니다.

Personalizer 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.

 * 순위 API - 콘텐츠 및 컨텍스트에 대해 제공하는 실시간 정보에 따라 작업에서 가장 적합한 항목을 선택합니다.
 * 보상 API - 비즈니스 요구에 따라 보상 점수를 확인한 다음, 이 API를 사용하여 Personalizer로 보냅니다. 이 점수는 1은 양호, 0은 나쁨과 같은 단일 값일 수 있으며, 비즈니스 요구에 따라 생성하는 알고리즘일 수 있습니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Personalizer 작동 방식](how-personalizer-works.md)
> [Personalizer를 사용하는 위치](where-can-you-use-personalizer.md)
