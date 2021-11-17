---
title: NER(사용자 지정 명명된 엔터티 인식) 서비스 제한
titleSuffix: Azure Cognitive Services
description: NER(사용자 지정 명명된 엔터티 인식)를 사용할 때의 데이터 및 서비스 제한에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, references_regions, ignite-fall-2021
ms.openlocfilehash: 255c0f5f85f1f7b385fed82c36a799004f03ed0b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488219"
---
# <a name="custom-named-entity-recognition-ner-service-limits"></a>NER(사용자 지정 명명된 엔터티 인식) 서비스 제한

이 문서를 사용하여 사용자 지정 NER를 사용할 때의 데이터 및 서비스 제한에 대해 알아봅니다.

## <a name="file-limits"></a>파일 제한

* `.txt`만 사용할 수 있습니다. 사용됩니다. 데이터가 다른 형식인 경우 [CLUtils 구문 분석 명령을](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) 사용하여 문서를 열고 텍스트를 추출할 수 있습니다.

* 컨테이너에 업로드된 모든 파일에는 데이터가 포함되어야 합니다. 빈 파일은 학습에 사용할 수 없습니다.

* 모든 파일은 컨테이너의 루트에서 사용할 수 있어야 합니다.

* 파일 sis에 허용되는 최대 길이는 약 28,000개 단어 또는 56페이지인 128,000자입니다.

* [학습 데이터 세트에는](how-to/train-model.md#data-split) 10개 이상의 파일과 100,000개 이하의 파일이 포함되어야 합니다.


## <a name="apis-limits"></a>API 제한

* Authoring API를 사용하는 경우 분당 최대 10개의 POST 요청과 100개의 GET 요청이 있습니다.

* 분석 API를 사용하는 경우 분당 최대 20개의 GET 또는 POST 요청이 있습니다.

* 요청당 최대 파일 크기는 125,000자입니다. 총 125,000자를 초과하지 않는 한 최대 25개 파일을 보낼 수 있습니다.

> [!NOTE]
> 제한이 허용하는 것보다 더 큰 파일을 보내야 하는 경우 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 나눌 수 있습니다. 이 프로세스에 [CLUtils의 Chunk 명령을](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand/README.md) 사용할 수 있습니다.

## <a name="azure-resource-limits"></a>Azure 리소스 제한

* 리소스당 1개의 스토리지 계정만 연결할 수 있습니다. 이 과정은 되돌릴 수 없습니다. 스토리지 계정을 리소스에 연결하는 경우 나중에 연결을 해제할 수 없습니다.

* 리소스당 최대 500개의 프로젝트를 가질 수 있습니다.

* Project 이름은 사용자 지정 NER 및 사용자 지정 [텍스트 분류에서](../custom-classification/overview.md)동일한 리소스 내에서 고유해야 합니다.

## <a name="regional-availability"></a>국가별 가용성 

사용자 지정 텍스트 분류는 일부 Azure 지역에서만 사용할 수 있습니다. [Azure 리소스](how-to/create-project.md)를 만들 때 다음 지역 중 하나에 배포해야 합니다.
* **미국 서부 2**
* **서유럽**
    
## <a name="project-limits"></a>프로젝트 제한

* 각 프로젝트에 대해 1개의 스토리지 컨테이너만 연결할 수 있습니다. 이 과정은 되돌릴 수 없습니다. 컨테이너를 프로젝트에 연결하는 경우 나중에 연결을 끊을 수 없습니다.

* 프로젝트당 태그 [파일은 1개만](how-to/tag-data.md) 사용할 수 있습니다. 나중에 다른 태그 파일로 변경할 수 없습니다. 프로젝트 내에서만 태그를 업데이트할 수 있습니다.

* 만든 후에는 프로젝트 이름을 바꿀 수 없습니다.

* 프로젝트에 태그가 지정된 파일이 10개 이상 있고 최대 100,000개 파일이 있어야 합니다.

* 프로젝트당 최대 10개의 학습된 모델을 가질 수 있습니다.

* 모델 이름은 동일한 프로젝트 내에서 고유해야 합니다.

* 만든 후에는 모델의 이름을 바꿀 수 없습니다.

* 프로젝트당 한 번에 하나의 모델만 학습할 수 있습니다.

## <a name="entity-limits"></a>엔터티 제한

* 태그가 지정된 엔터티는 10개 단어를 초과하지 않는 것이 좋지만 허용되는 최대값은 100자입니다.

* 프로젝트에 1개 이상의 엔터티 형식이 있어야 하며 최대값은 200개 엔터티 형식입니다.

* 엔터티당 약 200개의 태그가 지정된 인스턴스를 갖는 것이 좋으며 엔터티당 태그가 지정된 인스턴스는 최소 10개여야 합니다.

## <a name="naming-limits"></a>이름 지정 제한

| attribute | 제한 |
|--|--|
| 프로젝트 이름 |  공백 없이 문자 `(a-z, A-Z)` 및 숫자 `(0-9)`만 사용할 수 있습니다. |
| 모델 이름 |  공백 없이 문자 `(a-z, A-Z)` 및 숫자 `(0-9)`만 사용할 수 있습니다. |
| 엔터티 이름| 문자, 숫자 및 기호만 사용할 수 `(a-z, A-Z)` `(0-9)` 있습니다. `@ # _ . , ^ \ [ ]` |
| 파일 이름 | 공백 없이 문자 `(a-z, A-Z)` 및 숫자 `(0-9)`만 사용할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

[사용자 지정 NER 개요](../overview.md)
