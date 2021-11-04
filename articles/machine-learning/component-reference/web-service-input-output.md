---
title: '웹 서비스 입력/출력: 구성 요소 참조'
description: Azure Machine Learning 디자이너에서 웹 서비스 구성 요소를 사용하여 입력 및 출력을 관리하는 방법을 알아봅니다.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 67741a786f60b45e2e0cc7f728aa136397220899
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570121"
---
# <a name="web-service-input-and-web-service-output-components"></a>웹 서비스 입력 및 웹 서비스 출력 구성 요소

이 문서에서는 Azure Machine Learning 디자이너의 웹 서비스 입력 및 웹 서비스 출력 구성 요소에 대해 설명합니다.

웹 서비스 입력 구성 요소는 **DataFrameDirectory** 형식의 입력 포트에만 연결할 수 있습니다. 웹 서비스 출력 구성 요소는 **DataFrameDirectory** 형식의 출력 포트에서만 연결할 수 있습니다. **웹 서비스** 범주 아래의 구성 요소 트리에서 두 구성 요소를 찾을 수 있습니다. 

웹 서비스 입력 구성 요소는 사용자 데이터가 파이프라인에 입력되는 위치를 나타냅니다. 웹 서비스 출력 구성 요소는 실시간 유추 파이프라인에서 사용자 데이터가 반환되는 위치를 나타냅니다.

## <a name="how-to-use-web-service-input-and-output"></a>웹 서비스 입력 및 출력을 사용하는 방법

학습 [파이프라인에서 실시간 유추 파이프라인을 만들면](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) 웹 서비스 입력 및 웹 서비스 출력 구성 요소가 자동으로 추가되어 사용자 데이터가 파이프라인에 입력되는 위치와 데이터가 반환되는 위치를 표시합니다. 

> [!NOTE]
> 실시간 유추 파이프라인의 자동 생성은 규칙 기반의 최상의 프로세스입니다. 정확성은 보장되지 않습니다. 

요구 사항을 충족하기 위해 웹 서비스 입력 및 웹 서비스 출력 구성 요소를 수동으로 추가하거나 제거할 수 있습니다. 실시간 유추 파이프라인에 하나 이상의 웹 서비스 입력 구성 요소와 하나의 웹 서비스 출력 구성 요소가 있는지 확인합니다. 웹 서비스 입력 또는 웹 서비스 출력 구성 요소가 여러 개 있는 경우 고유한 이름이 있는지 확인합니다. 구성 요소의 오른쪽 패널에 이름을 입력할 수 있습니다.

웹 서비스 입력 및 웹 서비스 출력 구성 요소를 제출되지 않은 파이프라인에 추가하여 실시간 유추 파이프라인을 수동으로 만들 수도 있습니다.

> [!NOTE]
> 파이프라인 형식은 처음 제출할 때 결정됩니다. 처음으로 제출하기 전에 웹 서비스 입력 및 웹 서비스 출력 구성 요소를 추가해야 합니다.

다음 예제에서는 Python 스크립트 실행 구성 요소에서 실시간 유추 파이프라인을 수동으로 만드는 방법을 보여줍니다. 

![예제](media/module/web-service-input-output-example.png)
   
파이프라인을 제출하고 실행이 성공적으로 완료되면 실시간 엔드포인트를 배포할 수 있습니다.
   
> [!NOTE]
>  앞의 예제에서 **수동으로 데이터 입력** 은 웹 서비스 입력을 위한 데이터 스키마를 제공하며 실시간 엔드포인트를 배포하는 데 필요합니다. 일반적으로 데이터 스키마를 제공하기 위해 웹 서비스 **입력이** 연결된 포트에 항상 구성 요소 또는 데이터 세트를 연결해야 합니다.
   
## <a name="next-steps"></a>다음 단계
[실시간 엔드포인트 배포](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint)에 대해 자세히 알아보세요.

Azure Machine Learning [사용할 수 있는 구성 요소 집합을](component-reference.md) 참조하세요.