---
title: 사용자 지정 NER (명명 된 엔터티 인식) 모델에 대 한 평가 보기
titleSuffix: Azure Cognitive Services
description: 사용자 지정 NER (명명 된 엔터티 인식) 모델의 평가 점수를 보는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 62724137bd02f8c3ff58665ad92a4346eddc0b53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103154"
---
# <a name="view-the-models-evaluation-and-details"></a>모델의 평가 및 세부 정보 보기

모델의 학습을 마친 후 모델 세부 정보를 확인 하 고 테스트 집합에 대해 얼마나 잘 수행 되는지 확인할 수 있습니다. 여기에는 [학습](train-model.md#data-split)중에 생성 되는 임의의 데이터의 10%가 포함 됩니다. 테스트 집합은 학습 프로세스 중에 모델에 도입되지 않은 데이터로 구성됩니다. 평가 프로세스가 완료 되려면 데이터 집합에 10 개 이상의 파일이 있어야 합니다. 또한 [학습 된 모델](train-model.md)을 사용 하 여 [사용자 지정 NER 프로젝트](../quickstart.md) 를 포함 해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 구성 된 Azure blob storage 계정을 사용 하 여 성공적으로 [프로젝트를 만들었습니다](create-project.md) .
    * 저장소 계정에 [업로드](create-project.md#prepare-training-data) 된 텍스트 데이터입니다.
* [태그가 지정 된 데이터](tag-data.md)
* [성공적으로 학습 된 모델](train-model.md)

자세한 내용은 [응용 프로그램 개발 수명 주기](../overview.md#application-development-lifecycle) 를 참조 하세요.

## <a name="view-the-models-evaluation-details"></a>모델의 평가 세부 정보 보기

1. [언어 스튜디오](https://aka.ms/languageStudio)에서 프로젝트 페이지로 이동 합니다.
    1. 언어 스튜디오에서 **정보 추출** 섹션을 찾습니다.
    2. **명명 된 사용자 지정 엔터티 추출** 을 선택 합니다.

2. 화면 왼쪽의 메뉴에서 **모델 세부 정보 보기** 를 선택 합니다.

3. **상태** 열에서 모델 학습 상태를 확인 하 고 **f1 점수** 열에서 모델의 f1 점수를 확인 합니다. 모델 이름을 클릭 하 여 자세한 내용을 확인할 수 있습니다.

4. **개요** 에서 **모델 수준** 평가 메트릭과 **엔터티 성능 메트릭** 아래의 **엔터티 수준** 평가 메트릭을 찾을 수 있습니다. 모델에 대 한 혼동 행렬은 **테스트 집합 혼동 행렬** 아래에 있습니다.
    
    > [!NOTE]
    > 여기에 표시 된 모든 엔터티를 찾지 못하면 해당 엔터티는 테스트 집합 내의 파일에 없기 때문입니다.

    :::image type="content" source="../media/model-details.png" alt-text="언어 스튜디오에서 모델 성능 메트릭의 스크린샷" lightbox="../media/model-details.png":::

## <a name="next-steps"></a>다음 단계

* 모델의 평가를 검토 한 후 [모델 개선을](improve-model.md)시작할 수 있습니다.
* [평가에 사용 되는 메트릭에](../concepts/evaluation-metrics.md)대해 알아봅니다. 
