---
author: baanders
description: Azure Digital Twins 모델의 유효성을 검사하는 방법을 설명하는 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 064f8e322d75e8c92f8ae9d0bd976ebbfb1bdb49
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438590"
---
> [!TIP]
> 모델을 만든 후에는 Azure Digital Twins 인스턴스에 업로드하기 전에 오프라인으로 모델의 유효성을 검사하는 것이 좋습니다.

인스턴스에 업로드하기 전에 DTDL이 올바른지 확인하도록 모델 문서의 유효성을 검사할 수 있는 언어 중립적 [DTDL 유효성 검사기 샘플](/samples/azure-samples/dtdl-validator/dtdl-validator)이 있습니다.

DTDL 유효성 검사기 샘플은 클라이언트 측 라이브러리로 NuGet에서 제공되는 .NET DTDL 파서 라이브러리인 [Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)를 기반으로 합니다. 라이브러리를 직접 사용하여 고유한 유효성 검사 솔루션을 디자인할 수도 있습니다. 파서 라이브러리를 사용하는 경우 Azure Digital Twins에서 실행 중인 버전과 호환되는 버전을 사용해야 합니다. 현재 이 버전은 *3.12.4* 입니다.

사용 예제를 포함하여 유효성 검사기 샘플과 파서 라이브러리에 대한 자세한 내용은 [모델 구문 분석 및 유효성 검사](../articles/digital-twins/how-to-parse-models.md)에서 확인할 수 있습니다.