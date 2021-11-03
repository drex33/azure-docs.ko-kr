---
title: 사용자 지정 텍스트 분류에 사용되는 정의
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류에 사용되는 정의에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 0699f4c32a62be3b07fd795357302f71ffcce8c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053666"
---
# <a name="terms-and-definitions-used-in-custom-text-classification"></a>사용자 지정 텍스트 분류에 사용되는 용어 및 정의 

사용자 지정 텍스트 분류에 사용되는 정의에 대해 알아봅니다. 

## <a name="project"></a>프로젝트

프로젝트는 데이터를 기반으로 하는 사용자 지정 AI 모델을 빌드하기 위한 작업 영역입니다. 사용자 및 사용 중인 Azure 리소스에 대한 기여자 액세스 권한이 있는 다른 사용자만 프로젝트에 액세스할 수 있습니다.
사용자 지정 텍스트 분류 프로젝트를 만들기 위한 필수 조건으로 [리소스를 스토리지 계정에 연결](how-to/create-project.md)해야 합니다.
프로젝트 만들기 흐름의 일부로 데이터 세트를 업로드한 Blob 컨테이너에 연결해야 합니다. 프로젝트에는 컨테이너에서 사용할 수 있는 모든 `.txt` 파일이 자동으로 포함됩니다. 프로젝트 내에 여러 모델이 모두 동일한 데이터 세트에 빌드되어 있을 수 있습니다. 자세한 내용은 [서비스 제한](service-limits.md) 문서를 참조하세요.

프로젝트 내에서 다음 작업을 수행할 수 있습니다.

* [데이터 태그 지정](./how-to/tag-data.md): 모델을 학습할 때 파일을 분류하는 방법을 학습할 수 있도록 데이터 세트의 각 파일에 해당 클래스/클래스에 태그를 지정하는 프로세스입니다.
* [모델 빌드 및 학습](./how-to/train-model.md): 프로젝트의 핵심 단계입니다. 이 단계에서 모델은 태그가 지정된 데이터에서 학습을 시작합니다. 
* [모델 평가 세부 정보 보기](./how-to/view-model-evaluation.md): 모델 성능을 검토하여 개선할 여지가 있는지 또는 결과에 만족하는지 결정합니다.
* [모델 개선(선택 사항)](./how-to/improve-model.md): 모델의 문제가 있는지 확인하고 성능을 개선합니다.
* [모델 배포](quickstart.md?pivots=language-studio#deploy-your-model): 모델을 사용할 수 있도록 합니다. 
* [모델 테스트](quickstart.md?pivots=language-studio#test-your-model): 모델을 테스트하고 수행 방법을 확인합니다.

### <a name="project-types"></a>프로젝트 형식

사용자 지정 텍스트 분류는 두 가지 유형의 프로젝트를 지원합니다.

* **단일 레이블 분류**: 데이터 세트의 각 파일에 대해 하나의 클래스만 할당할 수 있습니다. 예를 들어 영화 스크립트인 경우 파일은 `Action`, `Thriller` 또는 `Romance`만 될 수 있습니다.

* **다중 레이블 분류**: 데이터 세트의 각 파일에 대해 **다중** 클래스를 할당할 수 있습니다. 예를 들어 영화 스크립트인 경우 파일은 `Action` 또는 `Action` 및 `Thriller` 또는 `Romance`가 될 수 있습니다.

## <a name="model"></a>모델

모델은 특정 작업을 수행하도록 학습된 개체(이 경우 사용자 지정 텍스트 분류)입니다. 모델은 나중에 텍스트를 분류하는 데 사용할 수 있도록 학습할 태그가 지정된 데이터를 제공하여 학습됩니다. 모델의 성능에 만족하면 배포할 수 있으며, 이를 통해 [사용할 수 있습니다](https://aka.ms/ct-runtime-swagger).

## <a name="class"></a>클래스

클래스는 텍스트의 전체 분류를 나타내는 데 사용되는 사용자 정의 범주입니다. 학습을 위해 모델에 전달하기 전에 할당된 클래스를 사용하여 데이터에 태그를 지정합니다. 
