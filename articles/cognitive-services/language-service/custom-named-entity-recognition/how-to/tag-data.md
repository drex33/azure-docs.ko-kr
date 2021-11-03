---
title: 사용자 지정 명명 된 엔터티 인식 (NER)을 위해 데이터에 태그를 지정 하는 방법
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명명 된 엔터티 인식 (NER)과 함께 사용 하기 위해 데이터에 태그를 지정 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 2328971a659bd4d4b147f9a708c1b5910e8ce12c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053658"
---
# <a name="tag-your-data-for-custom-named-entity-recognition-ner-in-language-studio"></a>Language studio에서 사용자 지정 명명 된 엔터티 인식 (NER)에 대 한 데이터 태그 지정

사용자 지정 엔터티 추출 모델을 빌드하기 전에 태그가 지정 된 데이터가 있어야 합니다. 데이터에 태그가 지정 되지 않은 경우에는 언어 스튜디오에서 태그를 지정할 수 있습니다. 데이터에 태그를 표시 하려면 [프로젝트](../quickstart.md)를 만들어야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

데이터에 태그를 지정할 때 먼저 다음이 필요 합니다.

* 구성 된 Azure blob storage 계정을 사용 하 여 성공적으로 [프로젝트를 만들었습니다](create-project.md) .
    * 저장소 계정에 [업로드](create-project.md#prepare-training-data) 된 텍스트 데이터입니다.

자세한 내용은 [응용 프로그램 개발 수명 주기](../overview.md#application-development-lifecycle) 를 참조 하세요.

## <a name="tag-your-data"></a>데이터 태그

학습 데이터를 Azure 저장소 계정에 업로드 한 후에는 해당 데이터에 태그를 지정할 수 있도록 해야 합니다. 그러면 모델에서 필요한 클래스와 연결 되는 단어를 알 수 있습니다. 언어 스튜디오에서 데이터에 태그를 지정 하거나 수동으로 데이터에 태그를 지정 하는 경우 이러한 태그는 모델에서 학습 중에 사용할 [JSON 형식](../concepts/data-formats.md) 으로 저장 됩니다.  

데이터에 태그를 추가할 때 다음 사항에 주의 하십시오.

* **정확한 태그 지정**: 각 엔터티를 항상 올바른 형식에 태그를 지정합니다. 추출하려는 항목만 포함하고 태그에 불필요한 데이터를 포함하지 마세요.
* **일관된 태그 지정**: 동일한 엔터티는 모든 파일에서 동일한 태그를 가져야 합니다.
* **완전한 태그 지정**: 모든 파일에 있는 엔터티의 모든 인스턴스에 태그를 지정합니다.

태그가 지정 된 데이터의 전체 자릿수, 일관성 및 완전성은 모델 성능을 결정 하는 주요 요소입니다. 데이터 태그를 다음과 같이 합니다.

1. [언어 스튜디오](https://aka.ms/custom-extraction) 에서 프로젝트 페이지로 이동 하 여 프로젝트를 선택 합니다.

2. 왼쪽 메뉴에서 **태그 데이터** 를 선택 합니다.

3. `.txt`프로젝트에서 사용할 수 있는 모든 파일의 목록을 왼쪽으로 찾을 수 있습니다. 태그를 시작 하려는 파일을 선택 하거나 페이지 아래쪽의 **뒤로** 및 **다음** 단추를 사용 하 여 탐색할 수 있습니다.

4. 태그를 시작 하려면 오른쪽 위 모서리에서 **엔터티 추가** 를 클릭 합니다. **보기 드롭다운에서 보기** 를 변경 하 여 모든 파일 또는 태그가 지정 된 파일만 볼 수 있습니다.

>[!TIP]
> * 필요한 표준 태그는 없습니다. 엔터티 당 50 태그로 시작 하는 것이 좋습니다. 필요한 태그 수는 엔터티의 고유 방식과 서로 차별화 되는 방법에 따라 달라 집니다. 또한 태깅에 따라 달라 지 며 일관 되 고 완료 되어야 합니다.

:::image type="content" source="../media/tagging-screen.png" alt-text="데이터의 태그를 지정 하는 언어 스튜디오 화면을 보여 주는 스크린샷" lightbox="../media/tagging-screen.png":::

프로젝트에 여러 언어를 사용 하도록 설정한 경우 각 문서의 언어를 선택할 수 있는 **언어** 드롭다운을 찾을 수 있습니다.

태그를 지정 하는 동안 변경 내용이 정기적으로 동기화 됩니다. 아직 저장 되지 않은 경우 페이지 맨 위에 경고가 표시 됩니다. 수동으로 저장 하려면 페이지 맨 위에 있는 **태그 저장** 단추를 클릭 합니다.

## <a name="tagging-options"></a>태그 지정 옵션

문서에 태그를 표시 하는 두 가지 옵션이 있습니다.


|옵션 |설명  |
|---------|---------|
|브러시를 사용 하 여 태그     | 화면의 오른쪽 위 모퉁이에 있는 엔터티 옆의 브러시 아이콘을 선택한 다음 엔터티와 연결할 문서의 단어를 강조 표시 합니다.           |
|메뉴를 사용 하 여 태그    | 태그로 태그를 지정할 단어를 강조 표시 하면 메뉴가 표시 됩니다. 이 엔터티에 할당 하려는 태그를 선택 합니다.        |

아래 스크린샷은 브러시를 사용 하 여 태그를 표시 합니다.

:::image type="content" source="../media/tag-options.png" alt-text="사용자 지정 NER 제공 되는 태그 지정 옵션을 보여 주는 스크린샷" lightbox="../media/tag-options.png":::

## <a name="remove-tags"></a>태그 제거

태그를 제거 하려면

1. 태그를 제거 하려는 엔터티를 선택 합니다.
2. 표시 되는 메뉴를 스크롤하고 **태그 제거** 를 선택 합니다.

## <a name="delete-or-rename-entities"></a>엔터티 삭제 또는 이름 바꾸기

엔터티를 삭제 하거나 이름을 바꾸려면:

1. 메뉴의 오른쪽 위 모서리에서 편집 하려는 엔터티를 선택 합니다.
2. 엔터티 옆에 있는 세 개의 점을 클릭 하 고 드롭다운 메뉴에서 원하는 옵션을 선택 합니다.


## <a name="next-steps"></a>다음 단계

데이터에 태그를 지정한 후 데이터를 기반으로 학습 하는 [모델에 대 한 학습](train-model.md) 을 시작할 수 있습니다.
