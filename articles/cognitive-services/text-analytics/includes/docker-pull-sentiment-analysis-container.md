---
title: 감정 분석 컨테이너에 대한 docker pull
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너에 대한 docker pull 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/21/2021
ms.author: aahi
ms.openlocfilehash: f836a8313927dd773c432428cdf1b12c0cf00aef
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677565"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>감정 분석 v3 컨테이너에 대한 docker pull

감정 분석 컨테이너 v3 컨테이너는 여러 언어로 제공됩니다. 영어 컨테이너용 컨테이너를 다운로드하려면 아래 명령을 사용하세요. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

다른 언어용 컨테이너를 다운로드하려면 `en`을 아래의 언어 코드 중 하나로 바꿉니다. 

| Text Analytics 컨테이너 | 언어 코드 |
|--|--|
| 중국어 간체    |   `zh-hans`   |
| 중국어 번체   |   `zh-hant`   |
| 네덜란드어                 |     `nl`      |
| 영어               |     `en`      |
| 프랑스어                |     `fr`      |
| 독일어                |     `de`      |
| 힌디어                 |    `hi`       |
| 이탈리아어               |     `it`      |
| 일본어              |     `ja`      |
| 한국어                |     `ko`      |
| 노르웨이어(복말)   |     `no`      |
| 포르투갈어(브라질)   |    `pt-BR`    |
| 포르투갈어(포르투갈) |    `pt-PT`    |
| 스페인어               |     `es`      |
| 터키어               |     `tr`      |

Text Analytics 컨테이너에 사용할 수 있는 태그에 대한 전체 설명은 [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654)를 참조하세요.
