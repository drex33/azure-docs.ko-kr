---
title: 대화형 Language Understanding 이전 버전과의 호환성
titleSuffix: Azure Cognitive Services
description: LUIS와 대화형 Language Understanding 간의 이전 버전과의 호환성에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: f4c354780cd5d32c2c801b76bde2b8b6abd2d555
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101374"
---
# <a name="backwards-compatibility-with-luis-applications"></a>LUIS 애플리케이션과 이전 버전과의 호환성

대화형 Language Understanding에서 기존 LUIS 애플리케이션의 일부 콘텐츠를 다시 사용할 수 있습니다. 대화형 Language Understanding 프로젝트를 사용하는 경우 다음을 수행할 수 있습니다.
* LUIS 애플리케이션 JSON 파일에서 CLU 대화 프로젝트를 만듭니다.
* 오케스트레이션 워크플로 프로젝트에 연결할 수 있는 LUIS 애플리케이션을 만듭니다.  
  
> [!NOTE]
> 이 가이드에서는 언어 리소스를 만들었다고 가정합니다. 서비스를 시작하는 경우 [빠른 시작 문서](../quickstart.md)를 참조하세요. 

## <a name="import-a-luis-application-json-file-into-conversational-language-understanding"></a>LUIS 애플리케이션 JSON 파일을 대화형 Language Understanding으로 가져오기

LUIS 애플리케이션 JSON 파일을 가져오려면 새 프로젝트 만들기 옆에 있는 **아이콘** 을 클릭하고 **가져오기** 를 선택합니다. 그런 다음, LUIS 파일을 선택합니다. 대화형 Language Understanding으로 새 프로젝트를 가져올 때 내보낸 LUIS 애플리케이션 JSON 파일을 선택할 수 있으며 서비스는 현재 사용 가능한 기능을 사용하여 프로젝트를 자동으로 만듭니다.

:::image type="content" source="../media/import.png" alt-text="대화 프로젝트의 가져오기 단추를 보여주는 스크린샷." lightbox="../media/import.png":::

### <a name="supported-features"></a>지원되는 기능
LUIS JSON 애플리케이션을 CLU로 가져올 때 다음 기능을 사용하여 **대화** 프로젝트를 만듭니다.

|**기능**|**참고 사항**|
| :- | :- |
|의도|모든 의도는 동일한 이름의 CLU 의도로 전송됩니다.|
|ML 엔터티|모든 ML 엔터티는 이름이 같은 CLU 엔터티로 전송됩니다. ML 레이블은 유지되고 엔터티의 학습된 구성 요소를 학습하는 데 사용됩니다. 구조화된 ML 엔터티는 최상위 엔터티로만 전송됩니다. 개별 하위 엔터티는 무시됩니다.|
|발언|모든 LUIS 발화는 의도 및 엔터티 레이블이 있는 CLU 발화로 전송됩니다. 구조화된 ML 엔터티 레이블은 최상위 엔터티 레이블만 고려하고 개별 하위 엔터티 레이블은 무시됩니다.|
|culture|대화 프로젝트의 기본 언어는 LUIS 앱 문화권이 됩니다. 문화권이 지원되지 않으면 가져오기가 실패합니다. |

### <a name="unsupported-features"></a>지원되지 않는 기능

LUIS JSON 애플리케이션을 CLU로 가져올 때 특정 기능은 무시되지만 애플리케이션 가져오기는 차단되지 않습니다. 다음 기능은 무시됩니다.

|**기능**|**참고 사항**|
| :- | :- |
|애플리케이션 설정|문장 부호 정규화, 분음 부호 정규화 및 모든 학습 데이터 사용과 같은 설정은 의도 및 엔터티에 대한 예측을 개선하기 위한 것이었습니다. CLU의 새 모델은 문장 부호와 같은 작은 변경 내용에 민감하지 않으므로 설정으로 사용할 수 없습니다.|
|기능|구 목록 기능 및 의도에 대한 기능은 모두 무시됩니다. 기능은 CLU가 새 모델을 통해 기본 제공할 수 있는 LUIS에 대한 의미 체계 이해를 도입하기 위한 것입니다.|
|패턴|패턴은 의도 분류의 품질 부족을 다루는 데 사용되었습니다. CLU의 새 모델은 패턴 없이도 더 나은 성능을 발휘할 것으로 예상됩니다.|
|Pattern.Any 엔터티|Pattern.Any 엔터티는 ML 엔터티 추출의 품질 부족을 다루는 데 사용되었습니다. CLU의 새 모델은 pattern.any 엔터티 없이도 더 나은 성능을 발휘할 것으로 예상됩니다.|
|정규식 엔터티| 현재 지원되지 않음 |
|구조화된 ML 엔터티| 현재 지원되지 않음 |
|엔터티 나열 | 현재 지원되지 않음 |
|미리 빌드된 엔터티 | 현재 지원되지 않음 |
|ML 엔터티의 필수 엔터티 기능 | 현재 지원되지 않음 |
|ML 엔터티의 필수가 아닌 엔터티 기능 | 현재 지원되지 않음 |
|역할 | 현재 지원되지 않음 |

## <a name="use-a-published-luis-application-in-conversational-language-understanding-orchestration-projects"></a>대화형 Language Understanding 오케스트레이션 프로젝트에서 게시된 LUIS 애플리케이션 사용

대화형 Language Understanding에 사용하는 것과 동일한 언어 리소스가 소유한 게시된 LUIS 애플리케이션에만 연결할 수 있습니다. **서유럽** 애플리케이션에서 제작 리소스를 Language **S** 리소스로 변경할 수 있습니다. LUIS 애플리케이션에 다른 리소스를 할당하는 단계는 [LUIS 설명서](../../../luis/luis-how-to-azure-subscription.md#assign-luis-resources)를 참조하세요. LUIS 애플리케이션을 내보낸 다음, 언어 리소스로 가져올 수도 있습니다. 오케스트레이션 프로젝트에 연결하려는 경우 대화형 Language Understanding에 나타나도록 LUIS 애플리케이션을 학습하고 게시해야 합니다.


## <a name="next-steps"></a>다음 단계

[대화형 Language Understanding 개요](../overview.md)
