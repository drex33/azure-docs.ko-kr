---
title: 사용자 지정 NER (명명 된 엔터티 인식) 모델을 학습 하는 방법
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명명 된 엔터티 인식 (NER)을 위해 모델을 학습 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 6fdf5ad5ed2eb8291b9a43e6004d8b18c5879b93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103156"
---
# <a name="train-your-custom-named-entity-recognition-ner-model"></a>사용자 지정 NER (명명 된 엔터티 인식) 모델 학습

학습은 모델이 [태그가 지정 된 데이터](tag-data.md)에서 학습 하는 프로세스입니다. 교육이 완료 된 후 모델 [평가 메트릭을 사용](../how-to/view-model-evaluation.md) 하 여 [모델을 개선](../how-to/improve-model.md)해야 하는지 여부를 결정할 수 있습니다.

모델을 학습 하는 데 걸리는 시간은 데이터 집합에 따라 다르며, 몇 시간이 걸릴 수 있습니다. 한 번에 하나의 모델만 학습 하 고, 동일한 프로젝트에서 이미 학습 하 고 있는 경우 다른 모델을 만들거나 학습할 수 없습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* 구성 된 Azure blob storage 계정을 사용 하 여 성공적으로 [프로젝트를 만들었습니다](create-project.md) .
    * 저장소 계정에 [업로드](create-project.md#prepare-training-data) 된 텍스트 데이터입니다.
* [태그가 지정 된 데이터](tag-data.md)

자세한 내용은 [응용 프로그램 개발 수명 주기](../overview.md#application-development-lifecycle) 를 참조 하세요.

## <a name="data-split"></a>데이터 분할

학습 프로세스를 시작 하기 전에 데이터 집합의 파일을 임의로 두 그룹으로 나눕니다.

* **학습 집합** 에는 데이터 집합에 있는 파일의 80%가 포함 됩니다. 모델을 학습 하는 데 사용 되는 기본 집합입니다.

* **테스트 집합** 에는 데이터 집합에서 사용할 수 있는 파일의 20%가 포함 됩니다. 이 집합은 모델의 비편향 [평가](../how-to/view-model-evaluation.md) 를 제공 하는 데 사용 됩니다. 이 집합은 학습 중에 모델에는 도입 되지 않습니다.

## <a name="train-model-in-language-studio"></a>언어 스튜디오에서 모델 학습

1. [언어 스튜디오](https://aka.ms/LanguageStudio)에서 프로젝트 페이지로 이동 합니다.

2. 왼쪽 메뉴에서 **학습** 을 선택합니다.

3. 새 모델을 학습시키려면 **새 모델 학습** 을 선택하고, 아래 텍스트 상자에서 모델 이름을 입력합니다. 이 옵션을 선택 하 고 아래 드롭다운에서 원하는 모델을 선택 하 여 **기존 모델을 덮어쓸** 수 있습니다.

    :::image type="content" source="../media/train-model.png" alt-text="새 모델 만들기" lightbox="../media/train-model.png":::

4. 페이지 아래쪽에서 **학습** 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계

교육이 완료 된 후 모델 [평가 메트릭을](view-model-evaluation.md) 사용 하 여 필요에 따라 [모델을 향상](improve-model.md)시킬 수 있습니다. 모델에 만족 하면 모델을 배포 하 여 텍스트에서 [엔터티를 추출](call-api.md) 하는 데 사용할 수 있게 할 수 있습니다.
