---
title: 사용자 지정 NER에 대한 데이터 준비 및 스키마 설계
titleSuffix: Azure Cognitive Services
description: 사용자 지정형 NER 프로젝트를 성공적으로 만들기 위해 데이터를 선택하고 준비하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 6b00111265ea8f7441fb7164deed2d295d50e82f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101683"
---
# <a name="how-to-prepare-data-and-define-a-schema-for-custom-ner"></a>데이터를 준비하고 사용자 지정 NER에 대한 스키마를 정의하는 방법

사용자 지정 NER 모델을 만들려면 학습을 위한 양질의 데이터가 필요합니다. 이 문서에서는 스키마 정의와 함께 데이터 선택 및 준비에 접근하는 방법을 다룹니다. 스키마는 런타임 시 모델이 텍스트에서 추출해야 하는 엔터티 형식/범주를 정의하며 [엔터티 추출 모델 개발](../overview.md#application-development-lifecycle)의 첫 번째 단계입니다.

## <a name="schema-design"></a>스키마 디자인

스키마는 런타임 시 모델이 텍스트에서 추출해야 하는 엔터티 형식/범주를 정의합니다.

* 데이터 세트의 파일을 검토하여 형식과 구조에 익숙해지도록 합니다.

* 데이터에서 추출할 [엔터티](../glossary.md#entity)를 식별합니다.

    예를 들어 지원 이메일에서 엔터티를 추출하는 경우 "고객 이름", "제품 이름", "고객의 문제", "요청 날짜" 및 "연락처 정보"를 추출해야 할 수 있습니다.

* 엔터티 형식의 모호성을 피합니다.

    **모호성** 은 선택한 유형이 서로 유사할 때 발생합니다. 스키마가 모호할수록 태그가 지정된 데이터가 많을수록 모델을 학습할 수 있습니다.

    예를 들어 법적 계약에서 데이터를 추출하는 경우 "당사자 이름"과 "제2자 이름"을 추출하려면 양 당사자의 이름이 비슷해 보이기 때문에 모호성을 극복하기 위해 더 많은 예를 추가해야 합니다. 모호성을 피하면 시간과 노력을 절약하고 더 나은 결과를 얻을 수 있습니다.

* 복잡한 엔터티를 피합니다. 복잡한 엔터티는 텍스트에서 정확하게 선택하기 어려울 수 있으므로 여러 엔터티로 나누는 것이 좋습니다.

    예를 들어, 모델이 더 작은 엔터티로 분류되지 않은 경우 모델은 "주소"를 추출하는 데 어려움을 겪을 것입니다. 주소가 표시되는 방식에는 매우 다양한 변형이 있으므로 모델이 주소를 분해하지 않고 전체적으로 추출하도록 가르치려면 많은 수의 태그가 지정된 엔터티가 필요합니다. 그러나 "주소"를 "도로명", "사서함", "시", "주" 및 "우편번호"로 바꾸면 모델에 엔터티당 더 적은 수의 태그가 필요합니다.

## <a name="data-selection"></a>데이터 선택

모델을 학습하는 데 사용하는 데이터의 품질은 모델 성능에 큰 영향을 미칩니다.

* 도메인의 문제 공간을 반영하는 실제 데이터를 사용하여 모델을 효과적으로 학습합니다. 합성 데이터를 사용하여 초기 모델 교육 프로세스를 가속화할 수 있지만 실제 데이터와 다를 수 있으며 사용 시 모델의 효율성이 떨어질 수 있습니다.

* 실제 분포에서 벗어나지 않고 최대한 데이터 분포의 균형을 유지합니다. 예를 들어, 다양한 형식과 언어로 제공될 수 있는 법률 문서에서 엔터티를 추출하도록 모델을 학습하는 경우 실제 생활에서 볼 수 있을 것으로 예상되는 다양성을 예시하는 예를 제공해야 합니다.

* 모델의 과적합을 피하기 위해 가능하면 다양한 데이터를 사용합니다. 학습 데이터의 다양성이 적으면 실제 데이터에 존재하지 않을 수 있는 잘못된 상관 관계를 학습하는 모델이 발생할 수 있습니다. 
 
* 데이터에서 중복 파일을 피합니다. 중복 데이터는 학습 프로세스, 모델 메트릭 및 모델 성능에 부정적인 영향을 미칩니다. 

* 데이터의 출처를 고려합니다. 한 사람, 부서 또는 시나리오의 일부에서 데이터를 수집하는 경우 모델이 학습하는 데 중요할 수 있는 다양성을 놓치고 있을 수 있습니다. 

> [!NOTE]
> 파일이 여러 언어로 되어 있는 경우 [프로젝트 만들기](../quickstart.md) 중에 **다중 언어** 옵션을 선택하고 **언어** 옵션을 대부분의 파일 언어로 설정합니다.

## <a name="data-preparation"></a>데이터 준비

프로젝트를 만들기 위한 필수 조건으로 교육 데이터를 스토리지 계정의 Blob 컨테이너에 업로드해야 합니다. Azure에서 직접 또는 Azure Storage Explorer 도구를 사용하여 교육 파일을 만들고 업로드할 수 있습니다. Azure Storage Explorer 도구를 사용하면 더 많은 데이터를 빠르게 업로드할 수 있습니다.  

* [Azure에서 파일 만들기 및 업로드](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
* [Azure Storage Explorer를 사용하여 파일 만들기 및 업로드](/azure/vs-azure-tools-storage-explorer-blobs)

`.txt` 파일만 사용할 수 있습니다. 데이터가 다른 형식인 경우 [CLUtils 구문 분석 명령](https://github.com/microsoft/CogSLanguageUtilities/blob/main/CLUtils/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md)을 사용하여 파일 형식을 변경할 수 있습니다.

 주석이 달린 데이터 세트를 업로드하거나 주석이 없는 데이터 세트를 업로드하고 Language Studio에서 [데이터에 태그를 지정](../how-to/tag-data.md)할 수 있습니다. 
 
## <a name="next-steps"></a>다음 단계

아직 만들지 않았다면 사용자 지정 NER 프로젝트를 만듭니다. 사용자 지정 NER를 처음 사용하는 경우 [빠른 시작](../quickstart.md)에 따라 예제 프로젝트를 만드는 것이 좋습니다. 프로젝트를 만드는 데 필요한 항목에 대한 자세한 내용은 [방법 도움말](../how-to/create-project.md)을 참조하세요. 
