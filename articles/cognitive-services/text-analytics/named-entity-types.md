---
title: 명명된 엔터티 인식에 지원되는 범주
titleSuffix: Azure Cognitive Services
description: Text Analytics API에서 지원되는 엔터티 범주에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 08/11/2021
ms.author: aahi
ms.openlocfilehash: 9d63fc9f90408fd60331f77079fdd8f15ca4aad6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528619"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Text Analytics API v3에서 지원되는 엔터티 범주

이 문서를 사용하면 [NER(명명된 엔터티 인식)](how-tos/text-analytics-how-to-entity-linking.md)에서 반환할 수 있는 엔터티 범주를 찾을 수 있습니다. NER은 예측 모델을 실행하여 입력 문서에서 명명된 엔터티를 식별하고 분류합니다.

개인(`PII`) 및 상태(`PHI`) 정보를 검색하는 기능이 포함된 NER v3.1도 사용할 수 있습니다. 또한 **상태** 탭을 클릭하여 Text Analytics에서 상태에 대해 지원되는 범주 목록을 볼 수 있습니다. 

[마이그레이션 가이드](migration-guide.md?tabs=named-entity-recognition)에서 버전 2.1에서 반환된 유형 목록을 찾을 수 있습니다.

## <a name="entity-categories"></a>엔터티 범주

#### <a name="general"></a>[일반](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[의료](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>다음 단계

* [Text Analytics에서 명명된 엔터티 인식을 사용하는 방법](how-tos/text-analytics-how-to-entity-linking.md)
