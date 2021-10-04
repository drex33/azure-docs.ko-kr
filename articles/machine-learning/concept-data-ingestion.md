---
title: 데이터 수집과 자동화
titleSuffix: Azure Machine Learning
description: 기계 학습 모델을 훈련시키기 위해 사용 가능한 데이터 수집 옵션의 장단점을 알아보세요.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: b26073a4ee2d5d4667c30d4ceceb1380f7371c2c
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129430177"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Azure Machine Learning 워크플로의 데이터 수집 옵션

이 문서에서는 Azure Machine Learning에서 사용할 수 있는 데이터 수집 옵션의 장단점에 대해 알아봅니다. 

다음 중에서 선택합니다.
+ [Azure Data Factory](#azure-data-factory) 파이프라인을 데이터를 추출, 로드 및 변환하기 위해 특별히 빌드

+ [Azure Machine Learning Python SDK](#azure-machine-learning-python-sdk)에 사용자 지정 코드를 입력해 데이터 수집 작업.

+ 양쪽 모두를 조합

데이터 수집은 구조화되지 않은 데이터가 하나 이상의 원본에서 추출된 후 기계 학습 모델 학습에 동원되는 프로세스입니다. 특히 수동으로 수행 하는 경우와 여러 원본에서 많은 양의 데이터가 있는 경우 시간이 많이 소요됩니다. 이러한 작업을 자동화하면 리소스를 확보하고 모델에서 가장 최근의 데이터와 적용 가능한 데이터를 사용할 수 있습니다.

## <a name="azure-data-factory"></a>Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) 는 데이터 수집 파이프라인의 데이터 원본 모니터링 및 트리거를 기본 지원합니다.  

다음 표에서는 데이터 수집 워크플로에 Azure Data Factory를 사용하는 경우의 장단점을 요약하여 보여줍니다.

|장점|단점
---|---
데이터 추출, 로드, 변환 목적으로 특화되어 빌드 되었습니다.|현재는 Azure Data Factory 파이프라인 작업 집합이 제한됩니다 
대규모 오케스트레이션 데이터 이동 및 변환 목적의 데이터 기반 워크플로를 만들 수 있습니다.|생성 및 유지 관리 비용이 많이 듭니다. 자세한 내용은 Azure Data Factory의 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 를 참조하세요.
[Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) 및 [Azure Functions](../data-factory/control-flow-azure-function-activity.md) 와 같은 다양 한 Azure 도구와 통합 | 기본적으로 스크립트를 실행하지 않고 스크립트 실행을 위한 별도의 계산에 의존합니다 
기본적으로 데이터 원본에서 트리거된 데이터 수집을 지원합니다| 
데이터 준비 및 모델 학습 프로세스는 별도입니다.|
Azure Data Factory 데이터 흐름에 포함된 데이터 계보 기능|
스크립팅을 쓰지 않는 방법을 사용하는 낮은 코드 환경 [사용자 인터페이스를](../data-factory/quickstart-create-data-factory-portal.md) 제공합니다 |

이러한 단계와 다음 다이어그램은 Azure Data Factory 데이터 수집 워크플로를 보여줍니다.

1. 원본에서 데이터 끌어오기
1. 데이터를 변환하고 출력 Blob 컨테이너에 저장합니다. 이 컨테이너는 Azure Machine Learning의 데이터 스토리지로 기능합니다
1. 준비된 데이터를 저장하면 Azure Data Factory 파이프라인은 모델 학습을 위해 준비된 데이터를 수신하는 학습 Machine Learning 파이프라인을 호출합니다


    ![ADF 데이터 수집](media/concept-data-ingestion/data-ingest-option-one.svg)
    
기계 학습용 데이터 수집 파이프라인을 [Azure Data Factory](how-to-data-ingest-adf.md)를 사용해 빌드하는 방법을 알아봅니다.

## <a name="azure-machine-learning-python-sdk"></a>Azure Machine Learning Python SDK 

[Python SDK](/python/api/overview/azure/ml)를 사용하면 데이터 수집 작업을 [Azure Machine Learning 파이프라인](./how-to-create-machine-learning-pipelines.md) 단계에 통합할 수 있습니다.

다음 표에는 SDK를 사용하는 장단점과 데이터 수집 작업에 대한 ML 파이프라인 단계가 요약되어 있습니다.

장점| 단점
---|---
사용자 고유의 Python 스크립트 구성 | 기본적으로 데이터 원본 변경 트리거를 지원하지 않습니다. 논리 앱 또는 Azure 함수 구현 필요
모든 모델 학습 실행의 일부로 데이터 준비|데이터 수집 스크립트를 만들려면 개발 기술이 필요합니다.
[Azure Machine Learning 컴퓨팅](concept-compute-target.md#azure-machine-learning-compute-managed)을 포함하여 다양한 컴퓨팅 대상에서 데이터 준비 스크립트를 지원합니다 |수집 메커니즘을 만들기 위한 사용자 인터페이스를 제공하지 않습니다

다음 다이어그램에서 Azure Machine Learning 파이프라인은 데이터 수집 및 모델 학습의 두 단계로 구성됩니다. 데이터 수집 단계는 로컬/웹 원본에서 데이터 추출과 같은 Python 라이브러리 및 Python SDK를 사용하여 수행할 수 있는 작업과 누락된 값 대체와 같은 데이터 변환을 포함합니다. 그런 다음 학습 단계에서는 준비된 데이터를 학습 스크립트에 대한 입력으로 사용하여 기계 학습 모델을 학습합니다. 

![Azure 파이프라인 + SDK 데이터 수집](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>다음 단계

다음 안내서를 따라하세요.
* [Azure Data Factory를 사용하여 데이터 수집 파이프라인 빌드](how-to-data-ingest-adf.md)

* [Azure Pipelines를 사용하여 데이터 수집 파이프라인을 자동화하고 관리](how-to-cicd-data-ingestion.md).