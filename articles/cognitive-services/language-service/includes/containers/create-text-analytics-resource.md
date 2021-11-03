---
title: Cognitive Services 언어 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Cognitive Services 언어 리소스를 만드는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9d21f76e73e9ef4581355890b56876ff2e71a954
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102820"
---
## <a name="create-a-cognitive-services-language-resource"></a>Cognitive Services 언어 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **리소스 만들기** 를 선택 하 고 **AI + Machine Learning**  >  **언어** 로 이동 합니다.
   또는 [언어 리소스 만들기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)로 이동 합니다.
1. 필요한 설정을 모두 입력합니다.

    |설정|값|
    |--|--|
    |이름|이름(2-64자)을 입력합니다.|
    |구독|적절한 구독을 선택합니다.|
    |위치|근처 위치를 선택합니다.|
    |가격 책정 계층| 표준 가격 책정 계층을 나타내는 **S** 를 입력합니다.|
    |리소스 그룹|사용 가능한 리소스 그룹을 선택합니다.|

1. **만들기** 를 선택하고, 리소스가 만들어질 때까지 기다립니다. 자동으로 브라우저가 새로 만든 리소스 페이지로 리디렉션합니다.
1. 구성된 `endpoint` 및 API 키를 수집합니다.

    |포털의 리소스 탭|설정|값|
    |--|--|--|
    |**개요**|엔드포인트|엔드포인트를 복사합니다. `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0`와 비슷합니다.|
    |**키**|API 키|두 키 중 하나를 복사합니다. <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`> 같이 공백 또는 대시가 없는 32자리 영숫자 문자열입니다.|
