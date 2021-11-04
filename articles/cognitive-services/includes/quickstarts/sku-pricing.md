---
title: Cognitive Service SKU 및 가격 책정
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2021
ms.author: pafarley
ms.openlocfilehash: f20f73c37224abe1333d2577db8a54ef07eeed34
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520022"
---
아래 SKU 목록과 가격 정보를 참조하세요. 

#### <a name="multi-service"></a>다중 서비스

| 서비스     | 종류    |
|-------------|------------|
| 여러 서비스. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/) 페이지를 참조하세요.            | `CognitiveServices`     |


#### <a name="vision"></a>Vision

| 서비스    | 종류    |
|------------|---------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision - 예측 | `CustomVision.Prediction` |
| Custom Vision - 학습   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |

#### <a name="speech"></a>음성

| 서비스            | 종류                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| 음성 인식 | `SpeakerRecognition` |

#### <a name="language"></a>언어

| 서비스            | 종류                |
|--------------------|---------------------|
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 언어 서비스   | `TextAnalytics`     |
| 텍스트 번역   | `TextTranslation`   |

#### <a name="decision"></a>의사 결정

| 서비스           | 종류               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>가격 책정 계층 및 요금 청구

가격 책정 계층(및 청구되는 금액)은 인증 정보를 사용하여 전송하는 트랜잭션 수를 기반으로 합니다. 각 가격 책정 계층은 다음을 지정합니다.
* 초당 허용되는 최대 트랜잭션 수(TPS)입니다.
* 가격 책정 계층 내에서 사용하도록 설정된 서비스 기능입니다.
* 미리 정의된 트랜잭션 수에 대한 비용입니다. 이 숫자를 초과하면 서비스에 대한 [가격 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)에 지정된 대로 추가 요금이 발생합니다.

> [!NOTE]
> 대부분의 Cognitive Services에는 서비스를 시도하는 데 사용할 수 있는 체험 계층이 있습니다. 체험 계층을 사용하려면 리소스의 SKU로 `F0`을 사용합니다.