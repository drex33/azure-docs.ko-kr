---
title: 클라우드에서 데이터 액세스 보호
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 데이터 스토리지 및 데이터 세트를 사용하여 Azure에서 데이터 스토리지에 안전하게 연결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: bc5a0a9c3099274c843b786b69442c7d1f3e25bb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362588"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Azure Machine Learning에서 데이터 액세스 보호

Azure Machine Learning을 사용하면 클라우드에서 데이터에 쉽게 연결할 수 있습니다. 기본 스토리지 서비스에 추상화 계층을 제공하므로 스토리지 유형에 따라 코드를 작성할 필요 없이 데이터에 안전하게 액세스하고 작업할 수 있습니다. Azure Machine Learning은 다음과 같은 특징과 기능을 제공합니다.

*    Pandas 및 Spark DataFrames와의 상호 운용성
*    데이터 계보 버전 관리 및 추적
*    데이터 레이블 지정 
*    데이터 드리프트 모니터링
    
## <a name="data-workflow"></a>데이터 워크플로

클라우드 기반 스토리지 솔루션에서 데이터를 사용할 준비가 되면 다음과 같은 데이터 제공 워크플로를 사용하는 것을 권장합니다. 이 워크플로는 [Azure Storage 계정](../storage/common/storage-account-create.md?tabs=azure-portal) 및 Azure의 클라우드 기반 스토리지 서비스에 데이터를 보유하고 있다고 가정합니다. 

1. Azure Storage에 연결 정보를 저장하는 [Azure Machine Learning 데이터 스토리지](#datastores)를 만듭니다.

2. 해당 데이터 스토리지에서 기본 스토리지에 있는 특정 파일을 가리키도록 [Azure Machine Learning 데이터 세트](#datasets)를 만듭니다. 

3. 기계 학습 실험에서 해당 데이터 세트를 사용하려면 다음 중 하나를 수행
    1. 모델 학습을 위해 실험용 컴퓨팅 대상에 탑재합니다.

        **OR** 

    1. 자동화된 Machine Learning(자동화된 ML) 실험 실행, 기계 학습 파이프라인 또는 [Azure Machine Learning 디자이너](concept-designer.md)와 같은 Azure Machine Learning 솔루션에서 직접 사용합니다.

4. 모델 출력 데이터 세트가 데이터 드리프트를 감지할 수 있게 [데이터 세트 모니터](#drift)를 만듭니다. 

5. 데이터 드리프트가 감지되면 입력 데이터 세트를 업데이트하고 모델을 적절하게 다시 학습시킵니다.

다음 다이어그램은 권장되는 워크플로를 시각적으로 보여 줍니다.

![다이어그램에서 Azure Storage Service는 데이터 스토리지로, 데이터 스토리지는 데이터 세트로 흐릅니다. 데이터 세트는 모델 학습으로, 모델 학습은 데이터 드리프트로, 데이터 드리프트는 다시 데이터 세트로 흐릅니다.](./media/concept-data/data-concept-diagram.svg)

<a name="datastores"></a>
## <a name="connect-to-storage-with-datastores"></a>데이터 스토리지를 사용하여 스토리지에 연결

Azure Machine Learning 데이터 스토리지는 Azure의 데이터 스토리지에 대한 연결 정보를 안전하게 유지하므로 스크립트에서 코딩할 필요가 없습니다. 스토리지 계정에 쉽게 연결하고 기본 스토리지 서비스의 데이터에 액세스하기 위해 [데이터 스토리지를 등록하고 만듭니다](how-to-access-data.md). 

데이터 스토리지로 등록할 수 있는 Azure에서 지원되는 클라우드 기반 스토리지 서비스:

+ Azure Blob 컨테이너
+ Azure 파일 공유
+ Azure 데이터 레이크
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks 파일 시스템
+ Azure Database for MySQL

>[!TIP]
> 일반적으로 데이터 스토리지를 만드는 데 사용할 수 있는 기능에는 서비스 주체 또는 SAS(공유 액세스 서명) 토큰과 같은 스토리지 서비스에 액세스하기 위한 자격 증명 기반 인증이 필요합니다. 해당 자격 증명은 작업 영역에 대한 ‘읽기’ 권한이 있는 사용자가 액세스할 수 있습니다. <br><br>이것이 걱정된다면 [스토리지 서비스(미리 보기)에 대해 ID 기반 데이터 액세스를 사용하는 데이터 스토리지를 생성합니다](how-to-identity-based-data-access.md). 이 기능은 [실험적인](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능으로, 언제든지 변경할 수 있습니다.

<a name="datasets"></a>
## <a name="reference-data-in-storage-with-datasets"></a>데이터 세트와 스토리지의 참조 데이터

Azure Machine Learning 데이터 세트는 데이터의 복사본이 아닙니다. 데이터 세트를 생성하면, 해당 스토리지 서비스에 있는 데이터에 대한 참조와 메타데이터 복사본을 생성하게 됩니다. 

데이터 세트는 지연 평가되고 데이터는 기존 위치에 남아 있기 때문에

* 추가 스토리지 비용이 들지 않습니다.
* 실수로 데이터 원본을 변경할 위험이 없습니다.
* ML 워크플로 성능 속도를 향상합니다.

스토리지의 데이터와 상호 작용하려면 데이터를 기계 학습 작업에 사용할 수 있는 개체로 패키징하는 [데이터 세트를 만듭니다](how-to-create-register-datasets.md). 데이터를 수집하는 복잡함 없이 여러 실험에서 데이터를 공유하고 재사용하려면 데이터 세트를 작업 영역에 등록합니다.

데이터 세트는 로컬 파일, 퍼블릭 URL, [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) 또는 Azure Storage 서비스에서 데이터 스토리지를 통해 만들 수 있습니다. 

데이터 세트에는 두 가지 유형이 있습니다. 

+ [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset)는 데이터 스토리지 또는 퍼블릭 URL에 있는 하나 또는 여러 개의 파일을 참조합니다. 데이터가 이미 정리되어 학습 실험에서 사용할 준비가 된 경우, FileDatasets에서 참조하는 파일을 컴퓨팅 대상으로 [다운로드 또는 탑재](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)할 수 있습니다.

+ [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset)는 
제공된 파일 또는 파일 목록을 구문 분석하여 데이터를 테이블 형식으로 나타냅니다. TabularDataSet를 Pandas 또는 Spark DataFrame에 로드하여 추가 조작 및 정리할 수 있습니다. TabularDataset를 생성할 수 있는 전체 데이터 형식 목록은 [TabularDatasetFactory 클래스](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory)를 참조하세요.

추가 데이터 세트 기능은 다음 문서에서 확인할 수 있습니다.

+ [버전 및 추적](how-to-version-track-datasets.md) 데이터 세트 계보.
+ 데이터 드리프트 감지에 도움이 되도록 [데이터 세트 모니터링](how-to-monitor-datasets.md)을 합니다.    

## <a name="work-with-your-data"></a>데이터 작업

데이터 세트를 사용하면 Azure Machine Learning 기능과의 원활한 통합을 통해 많은 기계 학습 작업을 수행할 수 있습니다. 

+ [데이터 레이블 지정 프로젝트](#label) 만들기
+ 기계 학습 모델 교육하기
     + [자동화된 ML 실험](how-to-use-automated-ml-for-ml-models.md)
     + [디자이너](tutorial-designer-automobile-price-train-score.md#import-data)
     + [Notebook](how-to-train-with-datasets.md)
     + [Azure Machine Learning 파이프라인](./how-to-create-machine-learning-pipelines.md)
+ [기계 학습 파이프라인](./how-to-create-machine-learning-pipelines.md)에서 [일괄 처리 유추](./tutorial-pipeline-batch-scoring-classification.md)를 사용해 점수를 매기기 위한 데이터 세트에 액세스합니다.
+ [데이터 드리프트](#drift) 감지를 위한 데이터 세트 모니터를 설정합니다.

<a name="label"></a>

## <a name="label-data-with-data-labeling-projects"></a>데이터 레이블 지정 프로젝트로 데이터에 레이블을 지정

많은 양의 데이터에 레이블을 지정하는 것은 기계 학습 프로젝트에서 골칫거리인 경우가 많습니다. 이미지 분류 또는 개체 감지와 같은 컴퓨터 비전 구성 요소가 있는 프로젝트에는 일반적으로 수천 개의 이미지와 이에 해당하는 레이블이 필요합니다.

Azure Machine Learning은 레이블 지정 프로젝트를 만들고, 관리하고, 모니터링할 수 있는 중앙 위치를 제공합니다. 레이블 지정 프로젝트를 사용하면 데이터, 레이블 및 팀 멤버를 조정하여 레이블 지정 작업을 더 효율적으로 관리할 수 있습니다. 현재 지원되는 작업은 이미지 분류(다중 레이블 또는 다중 클래스) 및 경계 상자를 사용하는 개체 식별입니다.

이미지 [레이블 지정 프로젝트](how-to-create-image-labeling-projects.md) 또는 [텍스트 레이블 지정 프로젝트](how-to-create-text-labeling-projects.md)를 만들고 기계 학습 실험에 사용할 데이터 세트를 출력합니다.

<a name="drift"></a>

## <a name="monitor-model-performance-with-data-drift"></a>데이터 드리프트로 모델 성능 모니터링

기계 학습의 컨텍스트에서 데이터 드리프트는 모델의 성능 저하를 초래하는 모델 입력 데이터의 변경 내용입니다. 시간이 지날수록 모델 정확도가 저하되는 가장 큰 이유 중 하나이므로, 데이터 드리프트를 모니터링하면 모델 성능 문제를 감지하는 데 도움이 됩니다.

데이터 세트의 새 데이터에 대한 데이터 드리프트를 감지하고 경고하는 방법에 대한 자세한 내용은 [데이터 세트 모니터 생성하기](how-to-monitor-datasets.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계 

+ [이러한 단계에 따라](how-to-create-register-datasets.md) Azure Machine Learning 스튜디오 또는 Python SDK를 사용하여 데이터 세트를 만드세요.
+ [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)으로 데이터 세트 학습 예제를 테스트해 보세요.