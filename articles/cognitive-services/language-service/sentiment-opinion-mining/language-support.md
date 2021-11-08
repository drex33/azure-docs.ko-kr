---
title: 감정 분석 및 의견 마이닝 언어 지원
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 언어에 대 한 Azure 인식 서비스의 감정 분석 및 의견 마이닝 기능에서 지원 되는 자연어를 설명 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: a0cbc5bab15e80bb18ca3d4a13efa2d05fe5aeff
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027150"
---
# <a name="sentiment-analysis-and-opinion-mining-language-support"></a>감정 분석 및 의견 마이닝 언어 지원 

이 문서를 사용 하 여 감정 분석 및 의견 마이닝을 통해 지원 되는 자연어를 알아보세요.

> [!NOTE]
> 새 모델 버전이 릴리스되면 언어가 추가 됩니다. 감정 분석에 대 한 현재 일반적으로 사용할 수 있는 모델 버전은 `2020-04-01` 입니다. 최신 미리 보기 모델 버전 ()을 사용 하려면 `2021-10-01-preview` API 호출에서 지정 합니다. 자세한 내용은 [API를 호출 하는 방법을](how-to/call-api.md#specify-the-sentiment-analysis-model) 참조 하세요.

## <a name="sentiment-analysis-language-support"></a>감정 분석 언어 지원

| 언어              | 언어 코드 | 모델 버전부터: |              참고 |
|:----------------------|:-------------:|:--------------------------:|-------------------:|
| 중국어 간체    |   `zh-hans`         |         2019-10-01         | `zh`도 허용됨 |
| 중국어 번체   |   `zh-hant`         |         2019-10-01         |                    |
| 네덜란드어                 |     `nl`            |         2019-10-01        |                    |
| 영어               |     `en`            |         2019-10-01         |                    |
| 프랑스어                |     `fr`            |         2019-10-01         |                    |
| 독일어                |     `de`            |         2019-10-01         |                    |
| 힌디어                 |    `hi`             |         2020-04-01         |                    |
| 이탈리아어               |     `it`            |         2019-10-01         |                    |
| 일본어              |     `ja`            |         2019-10-01         |                    |
| 한국어                |     `ko`            |         2019-10-01         |                    |
| 노르웨이어(복말)   |     `no`            |         2020-04-01         |                    |
| 포르투갈어(브라질)   |    `pt-BR`          |         2020-04-01         |                    |
| 포르투갈어(포르투갈) |    `pt-PT`          |         2019-10-01         | `pt`도 허용됨 |
| 스페인어               |     `es`            |         2019-10-01         |                    |
| 터키어               |     `tr`             |         2020-04-01        |                    |

### <a name="opinion-mining-language-support"></a>의견 마이닝 언어 지원

| 언어              | 언어 코드 | 모델 버전부터: |              참고 |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| 영어               |     `en`      |  2020-04-01              |                    |
| 프랑스어                |     `fr`      |         2021-10-01-미리 보기         |                    |
| 독일어                |     `de`      |         2021-10-01-미리 보기         |                    |
| 이탈리아어               |     `it`      |         2021-10-01-미리 보기         |                    |
| 포르투갈어(브라질)   |    `pt-BR`    |         2021-10-01-미리 보기         |                    |
| 포르투갈어(포르투갈) |    `pt-PT`    |         2021-10-01-미리 보기         | `pt`도 허용됨 |
| 스페인어               |     `es`      |         2021-10-01-미리 보기         |                    |

## <a name="next-steps"></a>다음 단계

[감정 분석 및 의견 마이닝 개요](overview.md)
