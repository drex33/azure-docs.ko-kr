---
title: NER (명명 된 엔터티 인식) 언어 지원
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 언어에 대 한 Azure 인식 서비스의 NER 기능에서 지 원하는 자연어를 설명 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: 7f80599fe9b0a43b7b472bd6eff71ef11b62fe13
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103189"
---
# <a name="named-entity-recognition-ner-language-support"></a>NER (명명 된 엔터티 인식) 언어 지원 

이 문서를 사용 하 여 언어에 대 한 Azure 인식 서비스의 NER 기능에서 지 원하는 자연어를 알아보세요.

> [!NOTE]
> * 새 [모델 버전이](how-to-call.md#specify-the-ner-model) 릴리스되면 언어가 추가 됩니다. 
> * *로 표시된 언어에 대해서는 "Person", "Location" 및 "Organization" 엔터티만 반환됩니다.
> * NER의 현재 모델 버전은 `2021-06-01`입니다.

## <a name="ner-language-support"></a>NER 언어 지원

| 언어              | 언어 코드 | 모델 버전부터: | 참고              |
|:----------------------|:-------------:|:----------------------------:|:------------------:|
| 아랍어               | `ar`          | 2019-10-01                   |                    |
| 중국어 간체    | `zh-hans`     | 2021-01-15                   | `zh`도 허용됨 |
| 중국어-번체 *  | `zh-hant`     | 2019-10-01                   |                    |
| 체코어                | `cs`          | 2019-10-01                   |                    |
| 덴마크어               | `da`          | 2019-10-01                   |                    |
| 독일어                | `nl`          | 2019-10-01                   |                    |
| 영어               | `en`          | 2019-10-01                   |                    |
| 핀란드어              | `fi`          | 2019-10-01                   |                    |
| 프랑스어                | `fr`          | 2021-01-15                   |                    |
| 독일어                | `de`          | 2021-01-15                   |                    |
| 버전               | `he`          | 2019-10-01                   |                    |
| 헝가리어            | `hu`          | 2019-10-01                   |                    |
| 이탈리아어               | `it`          | 2021-01-15                   |                    |
| 일본어              | `ja`          | 2021-01-15                   |                    |
| 한국어                | `ko`          | 2021-01-15                   |                    |
| 노르웨이어 (복말) *  | `no`          | 2019-10-01                   | `nb`도 허용됨 |
| 세련               | `pl`          | 2019-10-01                   |                    |
| 포르투갈어(브라질)   | `pt-BR`       | 2021-01-15                   |                    |
| 포르투갈어(포르투갈) | `pt-PT`       | 2021-01-15                   | `pt`도 허용됨 |
| 러시아어              | `ru`          | 2019-10-01                   |                    |
| 스페인어               | `es`          | 2020-04-01                   |                    |
| 스웨덴              | `sv`          | 2019-10-01                   |                    |
| 족              | `tr`          | 2019-10-01                   |                    |

## <a name="next-steps"></a>다음 단계

[PII 기능 개요](overview.md)
