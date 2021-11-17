---
title: 사용자 지정 텍스트 분류 제한
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류를 사용할 때의 데이터 및 속도 제한에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, references_regions, ignite-fall-2021
ms.openlocfilehash: 06db585531cb3a73b291f2c8d45de18a3d298fb2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484082"
---
# <a name="custom-text-classification-limits"></a>사용자 지정 텍스트 분류 제한

이 문서를 사용하여 사용자 지정 텍스트 분류를 사용할 때 데이터 및 속도 제한에 대해 알아봅니다.

## <a name="file-limits"></a>파일 제한

* 사용자 지정 텍스트 분류에는 `.txt` 파일만 사용할 수 있습니다. 데이터가 다른 형식인 경우 [CLUtils 구문 분석 명령](https://aka.ms/CognitiveServicesLanguageUtilities)을 사용하여 문서를 해독하고 텍스트를 추출할 수 있습니다.

* 컨테이너에 업로드된 모든 파일에는 데이터가 포함되어야 하며 학습에 빈 파일이 허용되지 않습니다.

* 모든 파일은 컨테이너의 루트에서 사용할 수 있어야 합니다.

* [학습 데이터 세트](how-to/train-model.md#data-splits)에는 10개 이상의 파일과 1,000,000개 이하의 파일이 포함되어야 합니다.

## <a name="api-limits"></a>API 제한

**API 작성**

* 분당 최대 10개의 POST 요청과 100개의 GET 요청을 보낼 수 있습니다.

**API 분석**

* 분당 최대 20개의 GET 또는 POST 요청을 보낼 수 있습니다.

* 요청당 최대 파일 크기는 125,000자입니다. 파일의 총 크기가 125,000자를 초과하지 않는 한 최대 25개의 파일을 보낼 수 있습니다.

> [!NOTE]
> 제한이 허용하는 것보다 더 큰 파일을 보내야 하는 경우 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 나눌 수 있습니다. 이 프로세스에 대해 [CLUtils의 청크 명령](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand/README.md)을 사용할 수 있습니다.

## <a name="azure-resource-limits"></a>Azure 리소스 제한

* 리소스당 1개의 스토리지 계정만 연결할 수 있습니다. 이 과정은 되돌릴 수 없습니다. 스토리지 계정을 리소스에 연결하면 나중에 연결을 끊을 수 없습니다.

* 리소스당 최대 500개의 프로젝트를 가질 수 있습니다.

* 프로젝트 이름은 사용자 지정 NER(명명된 엔터티 인식) 및 사용자 지정 텍스트 분류 기능 모두에서 동일한 리소스 내에서 고유해야 합니다.

## <a name="regional-availability"></a>국가별 가용성 

사용자 지정 텍스트 분류는 일부 Azure 지역에서만 사용할 수 있습니다. [Azure 리소스](how-to/create-project.md)를 만들 때 다음 지역 중 하나에 배포해야 합니다.
* **미국 서부 2**
* **서유럽**

## <a name="project-limits"></a>프로젝트 제한

* 각 프로젝트에 대해 1개의 스토리지 컨테이너만 연결할 수 있습니다. 이 프로세스는 되돌릴 수 없으며 컨테이너를 프로젝트에 연결하면 나중에 연결을 끊을 수 없습니다.

* 프로젝트당 1개의 태그 파일만 가질 수 있습니다. 태그 파일은 나중에 변경할 수 없으며 프로젝트 내의 태그만 업데이트할 수 있습니다.

* 프로젝트를 만든 후에는 이름을 바꿀 수 없습니다.

* 프로젝트에는 최소 10개의 파일과 최대 1,000,000개의 파일이 있어야 합니다.

* 프로젝트당 최대 10개의 학습된 모델을 가질 수 있습니다.

* 모델 이름은 동일한 프로젝트 내에서 고유해야 합니다.

* 만들기 후에는 모델의 이름을 바꿀 수 없습니다.

* 프로젝트당 한 번에 하나의 모델만 학습할 수 있습니다.

## <a name="classes-limits"></a>수업 제한

* 프로젝트에 최소 2개의 클래스가 있어야 합니다. <!-- The maximum is 200 classes. -->

* 클래스당 약 200개의 태그가 지정된 인스턴스가 있는 것이 권장되며 클래스당 최소 10개의 태그가 지정된 인스턴스가 있어야 합니다.

## <a name="naming-limits"></a>이름 지정 제한

| attribute | 제한 |
|--|--|
| 프로젝트 이름 |  공백 없이 문자 `(a-z, A-Z)` 및 숫자 `(0-9)`만 사용할 수 있습니다. |
| 모델 이름 |  문자 `(a-z, A-Z)`, 숫자 `(0-9)` 및 기호 `@ # _ . , ^ \ [ ]`만 사용할 수 있습니다. |
| 엔터티 이름| 문자 `(a-z, A-Z)`, 숫자 `(0-9)` 및 기호 `@ # _ . , ^ \ [ ]`만 사용할 수 있습니다. |
| 파일 이름 | 공백 없이 문자 `(a-z, A-Z)` 및 숫자 `(0-9)`만 사용할 수 있습니다. |
