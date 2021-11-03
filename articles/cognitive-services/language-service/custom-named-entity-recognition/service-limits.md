---
title: 사용자 지정 NER (명명 된 엔터티 인식) 서비스 제한
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명명 된 엔터티 인식 (NER)을 사용 하는 경우 데이터 및 서비스 제한에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, references_regions, ignite-fall-2021
ms.openlocfilehash: d56cec53d4fec33ec6db17667d95f9cc246796a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103007"
---
# <a name="custom-named-entity-recognition-ner-service-limits"></a>사용자 지정 NER (명명 된 엔터티 인식) 서비스 제한

사용자 지정 NER를 사용 하는 경우 데이터 및 서비스 제한에 대해 알아보려면이 문서를 사용 합니다.

## <a name="file-limits"></a>파일 제한

* `.txt`만 사용할 수 있습니다. 사용됩니다. 데이터가 다른 형식인 경우 [CLUtils parse 명령을](https://github.com/microsoft/CogSLanguageUtilities/blob/main/CLUtils/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) 사용 하 여 문서를 열고 텍스트를 추출할 수 있습니다.

* 컨테이너에 업로드 된 모든 파일은 데이터를 포함 해야 합니다. 빈 파일은 학습에 사용할 수 없습니다.

* 모든 파일은 컨테이너의 루트에서 사용할 수 있어야 합니다.

* 파일에 허용 되는 최대 길이인 sis 128000 문자 (약 28000 단어 또는 56 페이지)입니다.

* [학습 데이터 집합](how-to/train-model.md#data-split) 에는 파일을 10 개 이상 포함 해야 하며 파일 수는 10만 개이 하 여야 합니다.


## <a name="apis-limits"></a>Api 제한

* Authoring API를 사용 하는 경우 최대 10 개의 POST 요청 및 100 GET 요청 요청이 있습니다.

* 분석 API를 사용 하는 경우 분당 최대 20 개의 GET 또는 POST 요청이 있습니다.

* 요청당 최대 파일 크기는 125000 자입니다. 전체적으로 125000 자를 초과 하지 않는 경우 최대 25 개의 파일을 보낼 수 있습니다.

> [!NOTE]
> 제한이 허용하는 것보다 더 큰 파일을 보내야 하는 경우 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 나눌 수 있습니다. 이 프로세스에 대해 [CLUtils의 청크 명령을](https://github.com/microsoft/CogSLanguageUtilities/tree/main/CLUtils/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand) 사용할 수 있습니다.

## <a name="azure-resource-limits"></a>Azure 리소스 제한

* 리소스당 1개의 스토리지 계정만 연결할 수 있습니다. 이 과정은 되돌릴 수 없습니다. 저장소 계정을 리소스에 연결 하는 경우 나중에 리소스의 연결을 해제할 수 없습니다.

* 리소스당 최대 500개의 프로젝트를 가질 수 있습니다.

* Project 이름은 사용자 지정 NER 및 [사용자 지정 텍스트 분류](../custom-classification/overview.md)둘 다에서 동일한 리소스 내에서 고유 해야 합니다.

## <a name="regional-availability"></a>국가별 가용성 

사용자 지정 텍스트 분류는 일부 Azure 지역에서만 사용할 수 있습니다. [Azure 리소스](how-to/create-project.md)를 만들 때 다음 지역 중 하나에 배포해야 합니다.
* **미국 서부 2**
* **서유럽**
    
## <a name="project-limits"></a>프로젝트 제한

* 각 프로젝트에 대해 1개의 스토리지 컨테이너만 연결할 수 있습니다. 이 과정은 되돌릴 수 없습니다. 컨테이너를 프로젝트에 연결 하는 경우 나중에 해당 컨테이너의 연결을 끊을 수 없습니다.

* 프로젝트 당 [태그 파일](how-to/tag-data.md) 은 1 개만 있을 수 있습니다. 나중에 다른 태그 파일로 변경할 수 없습니다. 프로젝트 내 에서만 태그를 업데이트할 수 있습니다.

* 프로젝트를 만든 후에는 이름을 바꿀 수 없습니다.

* 프로젝트에는 최소 10 개의 태그가 지정 된 파일과 최대 10만 개의 파일이 있어야 합니다.

* 프로젝트당 최대 10개의 학습된 모델을 가질 수 있습니다.

* 모델 이름은 동일한 프로젝트 내에서 고유해야 합니다.

* 모델을 만든 후에는 이름을 바꿀 수 없습니다.

* 프로젝트당 한 번에 하나의 모델만 학습할 수 있습니다.

## <a name="entity-limits"></a>엔터티 제한

* 태그가 지정 된 엔터티는 10 개의 단어를 초과 하지 않는 것이 좋지만 최대 허용 개수는 100 자입니다.

* 프로젝트에 하나 이상의 엔터티 형식이 있어야 하 고 최대 200 엔터티 형식 이어야 합니다.

* 엔터티 당 200 태그가 지정 된 인스턴스를 포함 하는 것이 좋으며, 엔터티 마다 태그가 지정 된 인스턴스를 10 개 이상 포함 해야 합니다.

## <a name="naming-limits"></a>이름 지정 제한

| attribute | 제한 |
|--|--|
| 프로젝트 이름 |  공백 없이 문자 `(a-z, A-Z)` 및 숫자 `(0-9)`만 사용할 수 있습니다. |
| 모델 이름 |  공백 없이 문자 `(a-z, A-Z)` 및 숫자 `(0-9)`만 사용할 수 있습니다. |
| 엔터티 이름| 문자 `(a-z, A-Z)` , 숫자 및 기호만 사용할 수 있습니다 `(0-9)` . `@ # _ . , ^ \ [ ]` |
| 파일 이름 | 공백 없이 문자 `(a-z, A-Z)` 및 숫자 `(0-9)`만 사용할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

[사용자 지정 NER 개요](../overview.md)
