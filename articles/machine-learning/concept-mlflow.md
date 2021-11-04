---
title: MLflow 및 Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 통해 MLflow를 설정하여 ML 모델에서 메트릭 및 아티팩트를 기록하고 ML 모델을 웹 서비스로 배포하는 방법을 알아봅니다.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4900e66afa994b97a479e377e300c13ff29ae8e4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555118"
---
# <a name="mlflow-and-azure-machine-learning"></a>MLflow 및 Azure Machine Learning

[MLflow](https://www.mlflow.org)는 기계 학습 실험의 수명 주기를 관리하기 위한 오픈 소스 라이브러리입니다.  MLFlow 추적 URI 및 로그 API(총칭 [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api))는 실험 환경에 관계없이(컴퓨터에서 로컬로, 원격 컴퓨팅 대상, 가상 머신 또는 Azure Databricks 클러스터에서) 학습 실행 메트릭과 모델 아티팩트를 로깅하고 추적하는 MLflow의 구성 요소입니다. 

MLflow 추적 및 Azure Machine Learning을 함께 사용하면 Azure Machine Learning 작업 영역에 실험의 실행 메트릭을 추적하고 모델 아티팩트를 저장할 수 있습니다. 해당 실험은 컴퓨터, 원격 컴퓨팅 대상, 가상 머신 또는 [Azure Databricks 클러스터](how-to-use-mlflow-azure-databricks.md)에서 로컬로 실행되었을 수 있습니다. 

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow 클라이언트와 Azure Machine Learning 클라이언트 비교

 다음 표에는 Azure Machine Learning을 사용할 수 있는 여러 클라이언트와 해당 함수 기능이 요약되어 있습니다.

 MLflow 추적은 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)를 통해 사용할 수 있는 메트릭 로깅 및 아티팩트 스토리지 기능을 제공합니다.

| 기능 | MLflow 추적 및 배포 | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| 작업 영역 관리 |   | ✓ | ✓ | ✓ |
| 데이터 저장소 사용  |   | ✓ | ✓ | |
| 메트릭 로깅      | ✓ | ✓ |   | |
| 아티팩트 업로드 | ✓ | ✓ |   | |
| 메트릭 보기     | ✓ | ✓ | ✓ | ✓ |
| 컴퓨팅 관리   |   | ✓ | ✓ | ✓ |
| 모델 배포    | ✓ | ✓ | ✓ | ✓ |
|모델 성능 모니터링||✓|  |   |
| 데이터 드리프트 검색 |   | ✓ |   | ✓ |


## <a name="track-experiments"></a>실험 추적

MLflow 추적을 사용하면 Azure Machine Learning MLflow 실험의 백 엔드로 연결할 수 있습니다. 그런 다음 아래 작업을 수행할 수 있습니다.

+ [Azure Machine Learning 작업 영역](./concept-azure-machine-learning-architecture.md#workspace)에서 실험 메트릭과 아티팩트를 추적하고 로깅합니다. 실험을 위해 MLflow 추적을 이미 사용하고 있는 경우 작업 영역이 학습 메트릭 및 모델을 저장할 중앙 집중화된 안전하고 확장성 있는 위치를 제공합니다. 

+ MLflow 및 Azure Machine Learning 모델 레지스트리에서 모델을 추적하고 관리합니다.

+ [Azure Databricks 학습 실행을 추적합니다](how-to-use-mlflow-azure-databricks.md).

자세한 내용은 [MLflow 및 Azure Machine Learning을 사용하여 ML 모델 추적](how-to-use-mlflow.md)을 참조하세요. 

## <a name="train-mlflow-projects"></a>MLflow 프로젝트 학습

[MLflow 프로젝트](https://www.mlflow.org/docs/latest/projects.html) 및 Azure Machine Learning 백 엔드 지원(미리 보기)을 사용하여 학습 작업을 제출하기 위해 MLflow의 추적 URI 및 로깅 API(총칭하여 MLflow 추적)를 사용하는 방법을 알아봅니다. Azure Machine Learning 추적을 사용하여 로컬로 작업을 제출하거나 [Azure Machine Learning 컴퓨팅](./how-to-create-attach-compute-cluster.md)을 통해 실행을 클라우드로 마이그레이션할 수 있습니다.

자세한 내용은[MLflow 프로젝트 및 Azure Machine Learning을 사용하여 ML 모델 학습](how-to-train-mlflow-projects.md)을 참조하세요.


## <a name="deploy-mlflow-experiments"></a>MLflow 실험 배포

[Mlflow 모델을 Azure 웹 서비스로 배포하여](how-to-deploy-mlflow-models.md) 프로덕션 모델에 Azure Machine Learning의 모델 관리 및 데이터 드리프트 검색 기능을 활용하고 적용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [MLflow 및 Azure Machine Learning을 사용하여 ML 모델 추적](how-to-use-mlflow.md). 
* [MLflow 프로젝트 및 Azure Machine Learning을 사용하여 ML 모델 학습](how-to-train-mlflow-projects.md).
* [MLflow를 사용하여 Azure Databricks 실행 추적](how-to-use-mlflow-azure-databricks.md)
* [MLflow를 사용하여 모델을 배포](how-to-deploy-mlflow-models.md)합니다.


