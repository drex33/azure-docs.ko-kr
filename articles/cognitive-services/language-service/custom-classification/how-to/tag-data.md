---
title: 사용자 지정 분류를 위해 데이터에 태그를 지정하는 방법 - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류 API에 사용할 데이터에 태그를 지정하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: bcfa81dc4cf65252179ecb6411ce67f2ba9bdce7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102942"
---
# <a name="tag-text-data-for-training-your-model"></a>모델 학습을 위한 텍스트 데이터 태그 

사용자 지정 텍스트 분류 모델을 만들기 전에 먼저 태그가 지정된 데이터가 있어야 합니다. 데이터에 태그가 아직 지정되지 않은 경우 Language Studio에서 태그를 지정할 수 있습니다. 태그가 지정된 데이터는 텍스트를 해석하는 방법을 모델에 알리며 학습 및 평가에 사용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

데이터에 태그를 지정할 수 있으려면 다음이 필요합니다.
* 구성된 Azure Blob Storage 계정으로 [성공적으로 만든 프로젝트](create-project.md) 
* 스토리지 계정에 [업로드된](create-project.md#prepare-training-data) 텍스트 데이터입니다.

자세한 내용은 [애플리케이션 개발 수명 주기를](../overview.md#application-development-lifecycle) 참조하세요.

<!--Tagging your data will let you [train your model](train-model.md), [evaluate it](train-model.md), and use it to [classify text](call-api.md).-->

## <a name="tag-your-data"></a>데이터 태그

학습 데이터를 Azure Storage 계정에 업로드한 후에는 모델이 필요한 클래스와 연결될 단어를 알 수 있도록 태그를 추가해야 합니다. Language Studio에서 데이터에 태그를 지정하거나 수동으로 데이터에 태그를 지정하는 경우 이러한 태그는 학습 중에 모델에서 사용할 [JSON 형식으로](../concepts/data-formats.md) 저장됩니다. 

데이터에 태그를 입력할 때 다음을 염두에 두어야 합니다.

* 일반적으로 태그가 지정된 데이터가 많을수록 데이터에 정확하게 태그가 지정된 경우 더 나은 결과가 발생합니다.

* 클래스당 약 50개의 태그가 지정된 파일을 포함하는 것이 좋습니다. 모델 성능은 [스키마의](design-schema.md)가능한 모호성과 태그가 지정된 데이터의 품질에 따라 달라지므로 모델이 최상의 성능을 발휘할 수 있도록 보장할 수 있는 고정된 수는 없습니다.

다음 단계에 따라 데이터에 태그를 입력합니다.

1. [Language Studio의](https://aka.ms/custom-classification)프로젝트 페이지로 이동합니다.

1. 왼쪽 메뉴에서 **데이터 태그 지정을** 선택합니다.

3. 왼쪽의 프로젝트에서 사용할 수 있는 모든 .txt 파일 목록을 찾을 수 있습니다. 태그 지정을 시작하려는 파일을 선택하거나 페이지 아래쪽의 뒤로 및 다음 단추를 사용하여 탐색할 수 있습니다.  
    
4.  보기 드롭다운 메뉴에서 보기를 변경하여 모든 파일을 보거나 태그가 지정된 파일만 **볼** 수 있습니다. 

    > [!NOTE]
    > 프로젝트에 여러 언어를 사용하도록 설정한 경우 추가 **언어** 드롭다운 메뉴를 찾을 수 있습니다. 각 문서의 언어를 선택합니다.

5. 태그 지정을 시작하기 전에 오른쪽 위 모서리에서 프로젝트에 클래스를 추가합니다.


    :::image type="content" source="../media/tag-1.png" alt-text="데이터 태그 지정 화면을 보여주는 스크린샷" lightbox="../media/tag-1.png":::

6. 파일 태그 지정을 시작합니다.

    * **단일 레이블 분류:** 파일에 하나의 클래스로만 태그를 지정할 수 있습니다. 이 파일에 태그를 지정할 클래스 옆에 있는 라디오 단추 중 하나를 확인하면 됩니다.

      :::image type="content" source="../media/tag-single.png" alt-text="단일 레이블 분류 메뉴를 보여주는 스크린샷" lightbox="../media/tag-single.png":::

    * **여러 레이블 분류:** 파일에 여러 클래스로 태그를 지정할 수 있습니다. 이 파일에 태그를 지정할 클래스 옆에 있는 적용 가능한 확인란을 모두 확인하면 됩니다.

      :::image type="content" source="../media/tag-multi.png" alt-text="여러 레이블 분류 메뉴를 보여주는 스크린샷" lightbox="../media/tag-multi.png":::

태그를 지정하는 동안 변경 내용이 주기적으로 동기화됩니다. 아직 저장되지 않은 경우 페이지 맨 위에 경고가 표시됩니다. 수동으로 저장하려면 페이지 맨 위에 있는 태그 저장 단추를 클릭합니다.

## <a name="remove-tags"></a>태그 제거

태그를 제거하려면 클래스 옆의 단추를 선택 취소합니다.

## <a name="delete-or-classes"></a>또는 클래스 삭제

클래스를 삭제/이름을 바꾸려면

1. 오른쪽 메뉴에서 편집하려는 클래스를 선택합니다.
2. 세 점을 클릭하고 드롭다운 메뉴에서 원하는 옵션을 선택합니다.

## <a name="next-steps"></a>다음 단계

데이터에 태그를 지정한 후에는 데이터를 기반으로 학습할 [모델 학습을](train-model.md) 시작할 수 있습니다.
