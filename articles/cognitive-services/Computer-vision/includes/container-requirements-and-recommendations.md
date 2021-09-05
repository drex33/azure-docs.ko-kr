---
title: 컨테이너 요구 사항 및 추천
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2021
ms.author: aahi
ms.openlocfilehash: 81e86eda454bdbd262f6c38a4766ee2a899d225d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336089"
---
> [!NOTE]
> 요구 사항 및 권장 사항은 초당 단일 요청을 포함하는 벤치마크를 기준으로 하며, 523KB의 스캔된 비즈니스 문자 이미지를 사용하여 29개 줄과 총 803 문자를 포함합니다. 권장 구성으로 인해 최소 구성에 비해 응답 속도가 약 2배 더 빨라집니다.

다음 표에서는 각 Read OCR 컨테이너의 최소 및 권장 리소스 할당에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| Read 2.0-preview | 1개 코어, 8GB 메모리 |    8 코어, 16-GB 메모리 |
| Read 3.2 | 4개 코어, 16GB 메모리 | 8 코어, 24-GB 메모리 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.
