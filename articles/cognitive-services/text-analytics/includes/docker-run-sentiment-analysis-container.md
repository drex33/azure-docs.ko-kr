---
title: docker run 명령의 컨테이너 실행 예
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너에 대한 docker run 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 7ece961054c945ce949886976dc45e1178a616b6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677552"
---
감정 분석 v3 컨테이너를 실행하려면 다음 `docker run` 명령을 실행합니다. 아래 자리 표시자를 자체 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{LANGUAGE}** | 실행하려는 컨테이너의 언어입니다. 이 명령이 사용한 명령과 일치하는지 `docker pull` 확인합니다. `-`아래 예제에서 언어 앞에 사용된 하이픈()을 적어 둡다. | `en` |
| **{API_KEY}** | Text Analytics 리소스의 키입니다. Azure Portal의 리소스 **키 및 엔드포인트** 페이지에서 이 값을 찾을 수 있습니다. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Text Analytics API에 액세스하기 위한 엔드포인트입니다. Azure Portal의 리소스 **키 및 엔드포인트** 페이지에서 이 값을 찾을 수 있습니다. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment-{LANGUAGE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 감정 분석 컨테이너 실행
* 1개 CPU 코어 및 8GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.