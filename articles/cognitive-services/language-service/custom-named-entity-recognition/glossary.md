---
title: 사용자 지정 명명 된 엔터티 인식 (NER)에 사용 되는 정의 및 용어
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명명 된 엔터티 인식 (NER)을 사용 하는 경우 발생할 수 있는 몇 가지 정의 및 용어에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 7683f6ef9966672a7b64a9c3474f37d9b95a942c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053065"
---
# <a name="custom-named-entity-recognition-ner-definitions-and-terms"></a>사용자 지정 NER (명명 된 엔터티 인식) 정의 및 용어

이 문서를 사용 하 여 사용자 지정 NER을 사용 하는 경우 발생할 수 있는 몇 가지 정의 및 용어에 대해 알아봅니다.

## <a name="project"></a>프로젝트

프로젝트는 데이터를 기반으로 하는 사용자 지정 AI 모델을 빌드하기 위한 작업 영역입니다. 사용자 및 사용자가 사용 하는 Azure 리소스에 대 한 참가자 액세스 권한이 있는 다른 사용자만 프로젝트에 액세스할 수 있습니다.
사용자 지정 엔터티 추출 프로젝트를 만들기 위한 필수 구성 요소는 [새 프로젝트를 만들](how-to/create-project.md)때 데이터 집합을 사용 하 여 저장소 계정에 리소스를 연결 해야 합니다. 프로젝트에는 컨테이너에서 사용할 수 있는 모든 `.txt` 파일이 자동으로 포함됩니다.

프로젝트 내에서 다음을 수행할 수 있습니다.

* **데이터 태그** 지정: 모델을 학습할 때 추출할 항목을 학습 하도록 데이터에 태그를 지정 하는 프로세스입니다.
* **모델 빌드 및 학습**: 모델을 통해 태그가 지정 된 데이터에서 학습을 시작 하는 프로젝트의 핵심 단계입니다. 
* **모델 평가 세부 정보 보기**: 모델 성능을 검토 하 여 개선을 위한 공간이 있는지 또는 결과에 만족 하는지 결정 합니다.
* **모델 향상**: 모델에 무엇이 무엇이 잘못 되었는지 파악 하 고 성능을 향상 시키는 방법을 알아봅니다. 
* **모델 배포**: 모델을 사용할 수 있도록 설정 합니다. 
* **테스트 모델**: 데이터 집합에서 모델을 테스트 합니다.

## <a name="model"></a>모델

모델은 특정 태스크를 수행 하기 위해 학습 된 개체입니다 (이 경우 사용자 지정 엔터티 추출).

* **모델 학습** 은 태그 된 데이터를 기반으로 추출할 모델을 학습 하는 프로세스입니다.
* **모델 평가** 는 학습 후에 모델의 성능을 파악 하기 위해 즉시 발생 하는 프로세스입니다.
* **모델 배포** 는 사용할 수 있도록 하는 프로세스입니다.

## <a name="entity"></a>엔터티

엔터티는 특정 유형의 정보를 나타내는 텍스트의 범위입니다. 텍스트 범위는 하나 이상의 단어 (또는 토큰)로 구성 될 수 있습니다. 사용자 지정 명명 된 엔터티 인식 (NER)에서 엔터티는 텍스트에서 추출 하려는 정보를 나타냅니다. 

예를 들어 "*John의 John은 Fred에서 25000 USD입니다.*" 엔터티는 다음과 같을 수 있습니다. 

| 엔터티 이름/형식 | 엔터티 |
| -- | -- |
| 대출자 이름 | *John* |
| Lender 이름 | *Fred* |
| 대출 금액 | *25000 USD* |

## <a name="next-steps"></a>다음 단계

* [데이터 및 서비스 제한](service-limits.md).
* [사용자 지정 NER 개요](../overview.md).
