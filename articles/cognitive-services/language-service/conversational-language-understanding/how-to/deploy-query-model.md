---
title: 대화형 Language Understanding 작업을 보내는 방법
titleSuffix: Azure Cognitive Services
description: Language Understanding에 대 한 요청을 보내는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: e1f53678ef99fb5477700628e14127e5572da816
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053144"
---
# <a name="deploy-and-test-model"></a>모델 배포 및 테스트

데이터 집합에서 [모델을 학습](./train-model.md) 하 고 나면 배포할 준비가 된 것입니다. 모델을 배포한 후에는 예측을 위해 쿼리할 수 있습니다. 

## <a name="deploy-model"></a>모델 배포

모델을 배포 하는 것은이를 호스팅하고 끝점을 통해 예측에 사용할 수 있도록 하는 것입니다. 프로젝트 마다 배포 된 모델을 하나만 가질 수 있으며, 다른 모델을 배포 하면 이전에 배포 된 모델이 덮어쓰여집니다.

모델을 배포 하면 포털에서 직접 또는 연결 된 API를 호출 하 여 모델을 테스트할 수 있습니다.

모델을 선택 하 고 모델 배포 페이지에서 모델 배포를 클릭 하기만 하면 됩니다. 

:::image type="content" source="../media/deploy-model.png" alt-text="언어 스튜디오의 모델 배포 페이지를 보여 주는 스크린샷" lightbox="../media/deploy-model.png":::

**오케스트레이션 워크플로 프로젝트 배포**

오케스트레이션 워크플로 프로젝트를 배포 하는 경우 배포를 확인 하 고 연결 된 서비스에 대 한 매개 변수를 구성 하는 작은 창이 표시 됩니다.

하나 이상의 LUIS 응용 프로그램을 연결 하는 경우 배포 이름을 지정 하 고, *슬롯* 또는 *버전* 유형 배포를 사용 하 고 있는지 여부를 지정 합니다.       
* *슬롯* 배포 유형을 사용 하려면 프로덕션 슬롯과 스테이징 슬롯 중에서 선택 해야 합니다.
* *버전* 배포 유형을 사용 하려면 게시 한 버전을 지정 해야 합니다.

사용자 지정 질문 응답 및 SQL-CLU 연결 또는 연결 되지 않은 의도에 대 한 구성이 필요 하지 않습니다.

LUIS 프로젝트는 오케스트레이션 배포 중에 구성 된 슬롯에 **게시** 되어야 하 고 사용자 지정 질문 응답 kb은 해당 프로덕션 슬롯에도 게시 해야 합니다.

:::image type="content" source="../media/deploy-connected-services.png" alt-text="오케스트레이션 워크플로 프로젝트에 대 한 배포 화면을 보여 주는 스크린샷" lightbox="../media/deploy-connected-services.png":::

## <a name="send-a-conversational-language-understanding-request"></a>대화형 Language Understanding 요청 보내기

모델을 배포한 후에는 예측을 위해 배포 된 모델을 사용 하기 시작할 수 있습니다. 테스트 모델 페이지 외부에서 제공 된 사용자 지정 끝점에 대 한 API 요청을 통해 배포 된 모델 호출을 시작할 수 있습니다. 이 끝점 요청은 모델 내에서 정의 된 의도 및 엔터티 예측을 가져옵니다.

**모델 배포** 페이지로 이동 하 고, 배포 된 모델을 선택 하 고, "예측 URL 가져오기"를 클릭 하 여 끝점에 대 한 전체 URL을 가져올 수 있습니다.

:::image type="content" source="../media/prediction-url.png" alt-text="예측 요청 및 URL을 보여 주는 스크린샷" lightbox="../media/prediction-url.png":::

## <a name="api-response-for-a-conversations-project"></a>대화 프로젝트에 대 한 API 응답

대화 프로젝트에서 프로젝트 내에 있는 용도와 엔터티 모두에 대 한 예측을 얻을 수 있습니다. 
- 의도 및 엔터티에는 모델에서 프로젝트의 특정 요소를 예측 하는 것과 관련 하 여 0.0 ~ 1.0 사이의 신뢰도 점수가 포함 됩니다. 
- 상위 점수 매기기 의도가 자체 매개 변수 내에 포함 되어 있습니다.
- 예측 된 엔터티만 응답에 표시 됩니다.
- 엔터티는 다음을 표시 합니다.
    - 추출 된 엔터티의 텍스트입니다.
    - 오프셋 값으로 표시 되는 시작 위치입니다.
    - 길이 값으로 표시 되는 엔터티 텍스트의 길이입니다.

## <a name="api-response-for-an-orchestration-workflow-project"></a>오케스트레이션 워크플로에 대 한 API 응답 Project

오케스트레이션 워크플로 프로젝트는 가장 중요 한 점수 매기기의 응답과 연결 된 서비스의 응답을 반환 합니다.
- 의도 내에서 *Targetkind* 매개 변수를 사용 하 여 orchestrator의 최상위 의도 (대화, LUIS 또는 QnA Maker)에서 반환 된 응답의 형식을 확인할 수 있습니다.
- *Result* 매개 변수에 연결 된 서비스에 대 한 응답을 가져옵니다. 

요청 내에서 orchestrator가 해당 서비스에 라우팅하는 경우 연결 된 각 서비스에 대해 추가 매개 변수를 지정할 수 있습니다.
- 프로젝트 매개 변수 내에서 필요에 따라 연결 된 서비스에 대해 다른 쿼리를 지정할 수 있습니다. 다른 쿼리를 지정 하지 않으면 원래 쿼리가 사용 됩니다.
- *Direct target* 매개 변수를 사용 하면 orchestrator의 라우팅 결정을 무시 하 고 특정 연결 된 의도를 직접 대상으로 지정 하 여 응답을 강제로 적용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [대화형 Language Understanding 개요](../overview.md)
