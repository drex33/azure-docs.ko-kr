---
title: 대화형 Language Understanding 제한
titleSuffix: Azure Cognitive Services
description: 대화형 Language Understanding 대한 데이터, 지역 및 처리량 제한에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: ec9a32c4f73e174798a3422a1c152f3972bd076f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053669"
---
# <a name="service-limits-for-conversational-language-understanding"></a>대화형 Language Understanding 대한 서비스 제한

대화형 Language Understanding 서비스의 데이터, 지역 및 처리량 제한에 대해 알아봅니다.

## <a name="region-limits"></a>지역 제한

- 대화형 Language Understanding 미국 서부 2 및 **서유럽의** **2개** 지역에서만 사용할 수 있습니다. 
    - 오케스트레이션 워크플로 프로젝트는 서유럽에서 **대화 프로젝트,** **QnA Maker** 및 **LUIS** 연결을 사용하도록 설정합니다.
    - 오케스트레이션 워크플로 프로젝트는 미국 서부 2에서만 **대화 프로젝트** 및 **QnA Maker** 연결을 사용하도록 설정합니다. LUIS에 대한 작성 미국 서부 2 지역은 없습니다. 
- CLU에 액세스할 수 있는 유일한 SKU는 **Sku가** 있는 **언어** 리소스입니다.

## <a name="data-limits"></a>데이터 제한

대화형 Language Understanding 서비스에 대해 다음과 같은 제한이 관찰됩니다.

|항목|제한|
| --- | --- |
|발언|프로젝트당 15,000*|
|의도|프로젝트당 500|
|엔터티|프로젝트당 100개|
|발화 길이|500자|
|의도 및 엔터티 이름 길이|50자|
|모델|프로젝트당 10|
|프로젝트|리소스당 500|
|동의어|목록 구성 요소당 20,000개|

\**사용자가 추가한 발화만 포함합니다. 오케스트레이션 워크플로 프로젝트를 위해 끌어온 데이터는 합계에 포함되지 않습니다.*


## <a name="throughput-limits"></a>처리량 한도

|항목 | 제한 |
--- | --- 
|호출 작성| 분당 60개 요청|
|예측 호출| 분당 60개 요청|

## <a name="quota-limits"></a>할당량 한도

| 항목 | 제한 |
--- | --- 
|호출 작성| 제한 없음|
|예측 호출| 매월 100,000개|

## <a name="next-steps"></a>다음 단계

[대화형 Language Understanding 개요](overview.md)
