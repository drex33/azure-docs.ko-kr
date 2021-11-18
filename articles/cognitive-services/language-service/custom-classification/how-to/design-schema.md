---
title: 데이터 준비 및 스키마 정의 방법
titleSuffix: Azure Cognitive Services
description: 사용자 지정 분류 프로젝트를 위한 데이터 선택, 준비 및 스키마 만들기에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 37e55e4f246ffdc172621bc19b81975ff8324e10
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719545"
---
# <a name="how-to-prepare-data-and-define-a-schema"></a>데이터 준비 및 스키마 정의 방법

사용자 지정 분류 모델을 만들려면 학습을 위한 양질의 데이터가 필요합니다. 이 문서에서는 스키마 정의와 함께 데이터 선택 및 준비에 접근하는 방법을 다룹니다. 스키마는 런타임 시 모델이 텍스트를 분류하는 데 필요한 클래스를 정의하며 [사용자 지정 분류 애플리케이션 개발](../overview.md#project-development-lifecycle)의 첫 번째 단계입니다.


## <a name="data-selection"></a>데이터 선택

모델을 학습하는 데 사용하는 데이터의 품질은 모델 성능에 큰 영향을 미칩니다.

* 도메인의 문제 공간을 반영하는 실제 데이터를 사용하여 모델을 효과적으로 학습합니다. 합성 데이터를 사용하여 초기 모델 교육 프로세스를 가속화할 수 있지만 실제 데이터와 다를 수 있으며 사용 시 모델의 효율성이 떨어질 수 있습니다.

* 실제 분포에서 벗어나지 않고 최대한 데이터 분포의 균형을 유지합니다.

* 모델의 과적합을 피하기 위해 가능하면 다양한 데이터를 사용합니다. 학습 데이터의 다양성이 적으면 실제 데이터에 존재하지 않을 수 있는 잘못된 상관 관계를 학습하는 모델이 발생할 수 있습니다. 
 
* 데이터에서 중복 파일을 피합니다. 중복 데이터는 학습 프로세스, 모델 메트릭 및 모델 성능에 부정적인 영향을 미칩니다. 

* 데이터의 출처를 고려합니다. 한 사람, 부서 또는 시나리오의 일부에서 데이터를 수집하는 경우 모델이 학습하는 데 중요할 수 있는 다양성을 놓치고 있을 수 있습니다. 

> [!NOTE]
> 파일이 여러 언어로 되어 있는 경우 [프로젝트 만들기](../quickstart.md) 중에 **다중 언어** 옵션을 선택하고 **언어** 옵션을 대부분의 파일 언어로 설정합니다.

## <a name="data-preparation"></a>데이터 준비

사용자 지정 텍스트 분류 프로젝트를 만들기 위한 필수 조건으로 교육 데이터를 스토리지 계정의 Blob 컨테이너에 업로드해야 합니다. Azure에서 직접 또는 Azure Storage Explorer 도구를 사용하여 교육 파일을 만들고 업로드할 수 있습니다. Azure Storage Explorer 도구를 사용하면 더 많은 데이터를 빠르게 업로드할 수 있습니다.  

* [Azure에서 파일 만들기 및 업로드](../../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
* [Azure Storage Explorer를 사용하여 파일 만들기 및 업로드](../../../../vs-azure-tools-storage-explorer-blobs.md)

`.txt`만 사용할 수 있습니다. 사용자 지정 텍스트용 파일입니다. 데이터가 다른 형식인 경우 [CLUtils 구문 분석 명령](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md)을 사용하여 파일 형식을 변경할 수 있습니다.

 주석이 달린 데이터 세트를 업로드하거나 주석이 없는 데이터 세트를 업로드하고 Language Studio에서 [데이터에 태그를 지정](../how-to/tag-data.md)할 수 있습니다. 
 
## <a name="schema-design"></a>스키마 디자인

스키마는 런타임에 텍스트를 분류하기 위해 모델이 필요한 클래스를 정의합니다.

* **검토 및 식별**: 데이터 세트의 파일을 검토하여 구조와 콘텐츠를 숙지한 다음 데이터를 분류할 방법을 식별합니다. 

    예를 들어 지원 티켓을 분류하는 경우 *로그인 문제*, *하드웨어 문제*, *연결 문제* 및 *새 장비 요청* 과 같은 클래스가 필요할 수 있습니다.

* **클래스의 모호성 방지**: 지정한 클래스가 서로 유사한 의미를 공유할 때 모호성이 발생합니다. 스키마가 모호할수록 모델을 학습하는 데 더 많은 태그가 지정된 데이터가 필요할 수 있습니다.  

    예를 들어 음식 레시피를 분류하면 어느 정도 비슷할 수 있습니다. *디저트 레시피* 와 *메인 요리 레시피* 를 구별하려면 모델이 두 클래스를 구별할 수 있도록 더 많은 예제에 태그를 지정해야 할 수 있습니다. 모호성을 피하면 시간을 절약하고 더 나은 결과를 얻을 수 있습니다. 

* **범위 외 데이터**: 프로덕션에서 모델을 사용할 때 클래스에 속하지 않는 파일이 예상되는 경우 스키마에 *범위 외* 클래스를 추가하는 것이 좋습니다. 그런 다음 *범위 외* 로 태그가 지정된 몇 개의 파일을 데이터 세트에 추가합니다. 모델은 관련 없는 파일을 인식하고 그에 따라 태그를 예측하는 방법을 학습할 수 있습니다.

## <a name="next-steps"></a>다음 단계

아직 만들지 않았다면 사용자 지정 분류 프로젝트를 만듭니다. 사용자 지정 분류를 처음 사용하는 경우 [빠른 시작](../quickstart.md)에 따라 예시 프로젝트를 만드는 것이 좋습니다. 프로젝트를 만드는 데 필요한 사항에 대한 자세한 내용은 [프로젝트 요구 사항](../how-to/create-project.md)을 참조할 수도 있습니다.