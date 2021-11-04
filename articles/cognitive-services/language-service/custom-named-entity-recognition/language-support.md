---
title: 사용자 지정 명명 된 엔터티 인식 (NER)에 대 한 언어 및 지역 지원
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명명 된 엔터티 인식 (NER)에서 지 원하는 언어 및 지역에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: references_regions, language-service-custom-ner, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: 499fa25a09699e21610fb2c1812612616fb818da
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102744"
---
# <a name="language-support-for-custom-named-entity-recognition-ner"></a>사용자 지정 명명 된 엔터티 인식 (NER)에 대 한 언어 지원

이 문서를 사용 하 여 사용자 지정 명명 된 엔터티 인식 (NER)에서 현재 지원 되는 언어 및 지역에 대해 알아봅니다.

## <a name="multiple-language-support"></a>다중 언어 지원

사용자 지정 NER를 사용 하면 한 언어로 모델을 학습 하 고 다른 언어로 테스트할 수 있습니다. 이 기능은 모든 언어에 대해 별도의 프로젝트를 빌드하는 대신 한 프로젝트에서 다국어 데이터 세트를 처리할 수 있으므로 시간과 노력을 절약하는 데 도움이 되므로 매우 강력합니다. 데이터 세트가 완전히 동일한 언어일 필요는 없지만 프로젝트를 만들 때 이 옵션을 지정해야 합니다. 평가 프로세스 중에 모델이 특정 언어에서 제대로 수행되지 않는 경우 이 언어로 된 데이터를 학습 집합에 추가하는 것이 좋습니다.

> [!NOTE]
> 여러 언어에 대한 지원을 사용하도록 설정하려면 [프로젝트를 만들](how-to/create-project.md) 때 이 옵션을 사용하도록 설정해야 합니다.

## <a name="language-support"></a>언어 지원

사용자 지정 NER `.txt` 는 다음과 같은 언어의 파일을 지원 합니다.

| 언어 | Locale |  
|--|--|
| 영어(미국) |`en-US` |
| 프랑스어(프랑스) |`fr-FR` |
| 독일어 |`de-DE` |
| 이탈리아어 |`it-IT` |
| 스페인어(스페인) |`es-ES` |
| 포르투갈어(포르투갈) | `pt-PT` |
| 포르투갈어(브라질) | `pt-BR` |

## <a name="next-steps"></a>다음 단계

[사용자 지정 NER 개요](overview.md)
