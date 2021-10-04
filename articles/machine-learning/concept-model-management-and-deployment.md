---
title: 'MLOps: ML 모델 관리'
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning(MLOps)을 통한 모델 관리에 대해 알아봅니다. 모델을 배포, 관리, 계보 추적 및 모니터링하여 지속적으로 개선합니다. '
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
author: jpe316
ms.author: jordane
ms.date: 07/08/2021
ms.custom: seodec18
ms.openlocfilehash: e58e820471bbde5d56996958b82c573c69d332b8
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427175"
---
# <a name="mlops-model-management-deployment-lineage-and-monitoring-with-azure-machine-learning"></a>MLOps: Azure Machine Learning 모델 관리, 배포, 계보 및 모니터링

이 문서에서는 Azure Machine Learning 사용하여 모델의 수명 주기를 관리하는 방법을 알아봅니다. Azure Machine Learning은 Machine Learning Operations(MLOps) 방식을 사용합니다. MLOps는 기계 학습 솔루션의 품질 및 일관성을 개선합니다. 

## <a name="what-is-mlops"></a>MLOps란?

Machine Learning Operations(MLOps)는 워크플로의 효율성을 향상시키는 [DevOps](https://azure.microsoft.com/overview/what-is-devops/) 원칙 및 사례를 기반으로 합니다. 예를 들어 연속 통합, 제공, 배포입니다. MLOps는 다음과 같은 목표에 따라 해당 원칙을 기계 학습 프로세스에 적용합니다.

* 모델의 실험 및 개발 속도 향상
* 프로덕션 단계로의 모델 배포 속도 향상
* 품질 보증 및 엔드 투 엔드 계보 추적

Azure Machine Learning은 다음과 같은 MLOps 기능을 제공합니다.

- **재현 가능한 ML 파이프라인을 만듭니다**. Machine Learning 파이프라인을 사용하면 데이터 준비, 학습, 채점 프로세스에 대해 반복 및 재사용이 가능한 단계를 정의할 수 있습니다.
- 모델 학습 및 배포를 위해 **재사용 가능한 소프트웨어 환경을 만듭니다**.
- **어디에서나 모델을 등록, 패키징, 배포** 합니다. 또한 모델을 사용하는 데 필요한 연결된 메타데이터를 추적할 수 있습니다.
- **ML 수명 주기 전체에 걸쳐 거버넌스 데이터를 캡처합니다**. 로그된 계보 정보에는 모델을 게시하는 주체, 변경 사유, 프로덕션 환경에서 모델이 배포 또는 사용된 시점 등이 포함될 수 있습니다.
- **ML 수명 주기 중 이벤트를 알림 및 경고** 합니다. 예를 들면 실험 완료, 모델 등록, 모델 배포, 데이터 드리프트 검색이 있습니다.
- **ML 애플리케이션을 모니터링하여 운영 및 ML 관련 문제를 해결** 합니다. 학습 및 추론 간에 모델 입력을 비교하고, 모델별 메트릭을 탐색하고, ML 인프라에 대한 모니터링 및 경고를 제공합니다.
- **Azure Machine Learning 및 Azure Pipelines를 통해 ML 수명 주기 전체를 자동화** 합니다. 파이프라인을 사용하면 모델을 자주 업데이트하고, 새 모델을 테스트하고, 다른 애플리케이션 및 서비스와 함께 새 ML 모델을 지속적으로 배포할 수 있습니다.

MLOps에 대한 자세한 내용은 [MLOps(Machine Learning DevOps)](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-mlops)를 참조하세요.
## <a name="create-reproducible-ml-pipelines"></a>재현 가능한 ML 파이프라인 만들기

Azure Machine Learning의 ML 파이프라인을 사용하여 모델 학습 프로세스와 관련된 모든 단계를 연결합니다.

ML 파이프라인은 모델 평가에 대한 하이퍼 매개 변수 조정에 대한 데이터 준비부터 기능 추출까지의 단계를 포함할 수 있습니다. 자세한 내용은 [ML 파이프라인](concept-ml-pipelines.md)을 참조하세요.

[디자이너](concept-designer.md)를 사용하여 ML 파이프라인을 만드는 경우 언제든지 디자이너 페이지의 오른쪽 상단에 있는 **‘...’** 을 클릭하고 **복제** 를 선택할 수 있습니다. 파이프라인을 복제하면 이전 버전을 잃지 않고 파이프라인 디자인을 반복할 수 있습니다.  

## <a name="create-reusable-software-environments"></a>재사용 가능한 소프트웨어 환경 만들기

Azure Machine Learning 환경을 사용하면 프로젝트의 소프트웨어 종속성이 진화함에 따라 추적하고 재현할 수 있습니다. 환경을 사용하면 수동 소프트웨어 구성 없이도 빌드를 재현할 수 있습니다.

환경은 프로젝트의 PIP 및 Conda 종속성을 설명하고 모델의 학습 및 배포 모두에 사용할 수 있습니다. 자세한 내용은 [Azure Machine Learning 환경이란?](concept-environments.md)을 참조하세요.

## <a name="register-package-and-deploy-models-from-anywhere"></a>어디에서나 모델 등록, 패키징, 배포

### <a name="register-and-track-ml-models"></a>ML 모델 등록 및 추적

모델을 등록하면 작업 영역에서 Azure 클라우드의 모델을 저장하고 버전을 지정할 수 있습니다. 모델 레지스트리에서는 학습된 모델을 쉽게 구성하고 추적할 수 있습니다.

> [!TIP]
> 등록된 모델은 모델을 구성하는 하나 이상의 파일에 대한 논리적 컨테이너입니다. 예를 들어, 여러 파일에 저장된 모델의 경우 Azure Machine Learning 작업 영역에서 단일 모델로 등록할 수 있습니다. 등록 후에 등록된 모델을 다운로드하거나 배포하고 등록된 모든 파일을 받을 수 있습니다.

등록된 모델은 이름 및 버전으로 식별됩니다. 모델을 기존 이름과 동일한 이름으로 등록할 때마다 레지스트리에서 버전을 증가시킵니다. 등록 시 추가 메타데이터 태그를 제공할 수 있습니다. 해당 태그는 모델을 검색할 때 사용됩니다. Azure Machine Learning은 Python 3.5.2 이상을 사용하여 로드할 수 있는 모든 모델을 지원합니다.

> [!TIP]
> Azure Machine Learning 외부에서 학습된 모델을 등록할 수도 있습니다.

활성 배포에서 사용 중인 등록된 모델은 삭제할 수 없습니다.
자세한 내용은 [모델 배포](how-to-deploy-and-where.md#registermodel)의 모델 등록 섹션을 참조하세요.

> [!IMPORTANT]
> Azure Machine Learning Studio의 모델 페이지에서 필터링 기준 옵션으로 `Tags`를 선택한 경우 고객은 `TagName : TagValue` 대신 `TagName=TagValue`(공백 없음)를 사용해야 합니다.

### <a name="profile-models"></a>모델 프로필

Azure Machine Learning은 모델을 배포할 때 생성될 서비스의 CPU 및 메모리 요구 사항을 이해하는 데 도움이 될 수 있습니다. 프로파일링은 모델을 실행하는 서비스를 테스트하고 CPU 사용량, 메모리 사용량, 응답 대기 시간 등의 정보를 반환합니다. 또한 리소스 사용량에 따라 CPU 및 메모리 권장 사항을 제공합니다.
자세한 내용은 [모델 배포](how-to-deploy-profile-model.md)의 프로파일링 섹션을 참조하세요.

### <a name="package-and-debug-models"></a>모델 패키징 및 디버그

모델을 프로덕션 환경에 배포하기 전에 Docker 이미지로 패키징됩니다. 대부분의 경우 이미지 생성은 배포 중에 백그라운드에서 자동으로 수행됩니다. 이미지를 수동으로 지정할 수 있습니다.

배포에 문제가 발생하는 경우 문제 해결 및 디버깅을 위해 로컬 개발 환경에 배포할 수 있습니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md#registermodel) 및 [배포 문제 해결](how-to-troubleshoot-deployment.md)을 참조하세요.

### <a name="convert-and-optimize-models"></a>모델 변환 및 최적화

모델을 [ONNX](https://onnx.ai)(Open Neural Network Exchange)로 변환하면 성능이 향상될 수 있습니다. 평균적으로 ONNX로 변환하면 성능이 2배 향상될 수 있습니다.

ONNX with Azure Machine Learning에 대한 자세한 내용은 [ML 모델 만들기 및 가속화](concept-onnx.md) 문서를 참조하세요.

### <a name="use-models"></a>모델 사용

학습된 기계 학습 모델은 클라우드 또는 로컬에서 웹 서비스로 배포됩니다. 배포는 추론에 CPU, GPU 또는 FPGA(필드 프로그래머블 게이트 어레이)를 사용합니다. Power BI의 모델을 사용할 수도 있습니다.

모델을 웹 서비스로 사용하는 경우 다음 항목을 제공합니다.

* 서비스/디바이스에 제출된 데이터의 점수를 매기는 데 사용되는 모델입니다.
* 항목 스크립트. 이 스크립트는 요청을 수락하고, 모델을 사용하여 데이터의 점수를 매기고, 응답을 반환합니다.
* 모델과 항목 스크립트에 필요한 PIP 및 Conda 종속성을 설명하는 Azure Machine Learning 환경입니다.
* 모델 및 항목 스크립트에 필요한 텍스트, 데이터 등의 추가 자산입니다.

또한 대상 배포 플랫폼의 구성도 제공합니다. 예를 들어 Azure Kubernetes Service에 배포 시 VM 제품군 유형, 사용 가능한 메모리, 코어 수가 있습니다.

이미지를 만들 때 Azure Machine Learning에 필요한 구성 요소도 추가됩니다. 예를 들어, 웹 서비스를 실행하는 데 필요한 자산이 있습니다.

#### <a name="batch-scoring"></a>일괄 처리 점수 매기기
일괄 처리 채점은 ML 파이프라인을 통해 지원됩니다. 자세한 내용은 [빅 데이터의 일괄 예측](./tutorial-pipeline-batch-scoring-classification.md)을 참조하세요.

#### <a name="real-time-web-services"></a>실시간 웹 서비스

다음 컴퓨팅 대상을 사용하여 **웹 서비스** 에서 모델을 사용할 수 있습니다.

* Azure Container Instance
* Azure Kubernetes Service
* 로컬 개발 환경

모델을 웹 서비스로 배포하려면 다음 항목을 제공해야 합니다.

* 모델 또는 모델의 앙상블입니다.
* 모델을 사용하려면 종속성이 필요합니다. 예를 들어 요청을 수락하고 모델, Conda 종속성 등을 호출하는 스크립트입니다.
* 모델을 배포하는 방법 및 위치를 설명하는 배포 구성입니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md)를 참조하세요.

#### <a name="controlled-rollout"></a>제어된 롤아웃

Azure Kubernetes Service에 배포하는 경우 제어된 롤아웃을 사용하여 다음과 같은 시나리오를 사용할 수 있습니다.

* 배포에 대해 엔드포인트의 여러 버전을 만듭니다.
* 다른 버전의 엔드포인트로 트래픽을 라우팅하여 A/B 테스트 수행합니다.
* 엔드포인트 구성에서 트래픽 비율을 업데이트하여 엔드포인트 버전을 변경합니다.

자세한 내용은 [ML 모델의 제어된 롤아웃](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)을 참조하세요.

### <a name="analytics"></a>분석

Microsoft Power BI는 데이터 분석을 위해 기계 학습 모델을 사용할 수 있도록 지원합니다. 자세한 내용은 [Power BI(미리 보기)에서 Azure Machine Learning 통합](/power-bi/service-machine-learning-integration)을 참조하세요.

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>ML 수명 주기 전체에 걸쳐 캡처에 필요한 거버넌스 데이터 캡처

Azure ML은 메타데이터를 사용하여 모든 ML 자산의 엔드투엔드 감사 내역을 추적하는 기능을 제공합니다.

- Azure ML은 [Git에 통합](how-to-set-up-training-targets.md#gitintegration)되어 코드를 가져온 리포지토리/분기/커밋에 대한 정보를 추적합니다.
- [Azure ML 데이터 세트](how-to-create-register-datasets.md)를 통해 데이터를 추적, 프로파일링, 버전 관리할 수 있습니다.
- [해석 기능](how-to-machine-learning-interpretability.md)을 사용하면 모델을 설명하고, 규정 준수를 충족하며, 모델이 지정된 입력에 대한 결과를 도출하는 방식을 이해할 수 있습니다.
- Azure ML 실행 기록은 모델 학습에 사용되는 코드, 데이터, 컴퓨팅에 대한 스냅샷을 저장합니다.
- Azure ML 모델 레지스트리는 모델과 연결된 모든 메타데이터(학습에 사용된 실험, 배포 위치, 배포의 상태 등)를 캡처합니다.
- [Azure에 통합](how-to-use-event-grid.md)하면 ML 수명 주기의 이벤트에 대해 작업을 수행할 수 있습니다. 예를 들면 모델 등록, 배포, 데이터 드리프트, 학습(실행) 이벤트가 있습니다.

> [!TIP]
> 모델 및 데이터 세트에 대한 일부 정보가 자동으로 캡처되고 __태그__ 를 사용하여 추가 정보를 추가할 수 있습니다. 작업 영역에서 등록된 모델 및 데이터 세트를 찾을 때 태그를 필터로 사용할 수 있습니다.
>
> 등록된 모델을 사용하여 데이터 세트를 연결하는 단계는 선택 사항입니다. 모델을 등록할 때 데이터 세트를 참조하는 방법에 대한 자세한 내용은 [모델](/python/api/azureml-core/azureml.core.model%28class%29) 클래스 참조를 참조하세요.


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>ML 수명 주기 이벤트 알림, 자동화, 경고
Azure ML은 ML 수명 주기의 이벤트를 알리고 자동화하는 데 사용할 수 있는 Azure EventGrid에 주요 이벤트를 게시합니다. 자세한 내용은 [이 문서](how-to-use-event-grid.md)를 참조하세요.


## <a name="monitor-for-operational--ml-issues"></a>운영 및 ML 문제 모니터링

모니터링을 사용하면 모델에 전송되는 데이터 및 모델이 반환하는 예측을 이해할 수 있습니다.

이 정보는 모델이 사용되는 방식을 이해하는 데 도움이 됩니다. 수집된 입력 데이터는 모델의 향후 버전을 학습하는 데 유용할 수도 있습니다.

자세한 내용은 [모델 데이터 컬렉션을 사용하도록 설정하는 방법](how-to-enable-data-collection.md)을 참조하세요.

## <a name="retrain-your-model-on-new-data"></a>새 데이터에 대한 모델 재학습

새로운 정보를 수신하면 모델의 유효성을 검사하거나, 모델을 업데이트하거나, 처음부터 다시 학습해야 하는 경우가 많습니다. 도메인에서 새로운 데이터 수신을 예상하는 경우도 있습니다. 그리고 [데이터 세트에서 데이터 드리프트 검색하기(미리 보기)](how-to-monitor-datasets.md)에 설명된 대로 특정 센서에 대한 변경, 계절 효과와 같은 자연 데이터 변경 또는 다른 기능과의 관계에 따른 기능 변화 등이 발생하여 모델 성능이 저하되는 경우도 있습니다. 

학습을 다시 해야 하는 경우를 구분하는 방법에 대한 명확한 답은 없습니다. 그러나 앞에서 설명한 Azure ML 이벤트 및 모니터링 도구는 자동화를 위한 좋은 시작점입니다. 재학습을 결정한 후에는 다음을 수행해야 합니다. 

- 반복 가능한 자동화된 프로세스를 사용하여 데이터 전처리
- 새 모델 학습
- 새 모델의 출력을 이전 모델의 출력과 비교
- 사전 정의된 조건을 사용하여 이전 모델을 바꿀지 여부를 선택 

위 단계에서 중요한 점은 재학습을 임시로 실행하는 것이 아니라 자동화해야 한다는 것입니다. [Azure Machine Learning 파이프라인](concept-ml-pipelines.md)은 데이터 준비, 학습, 유효성 검사, 배포와 관련된 워크플로를 만드는 데 적합합니다. 파이프라인과 Azure Machine Learning 디자이너를 재학습 시나리오에 활용하는 방법은 [Azure Machine Learning 디자이너를 통한 모델 재학습](how-to-retrain-designer.md)을 참조하세요. 

## <a name="automate-the-ml-lifecycle"></a>ML 수명 주기 자동화 

GitHub 및 Azure Pipelines를 사용하여 모델을 학습하는 연속 통합 프로세스를 만들 수 있습니다. 데이터 과학자가 프로젝트에 대한 Git 리포지토리 변경을 확인하는 일반적인 시나리오에서는 Azure Pipeline이 학습 실행을 시작합니다. 그런 다음 실행 결과를 검사하여 학습된 모델의 성능 특성을 확인할 수 있습니다. 모델을 웹 서비스로 배포하는 파이프라인을 만들 수도 있습니다.

[Azure Machine Learning 확장](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)을 사용하면 Azure Pipelines를 더 쉽게 작업할 수 있습니다. Azure Pipelines가 다음과 같이 개선됩니다.

* 서비스 연결을 정의할 때 작업 영역을 선택할 수 있습니다.
* 학습 파이프라인에서 생성된 학습된 모델이 릴리스 파이프라인을 트리거할 수 있습니다.

Azure Machine Learning에서 Azure Pipelines를 사용하는 방법에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Azure Pipelines를 통한 ML 모델의 연속 통합 및 배포](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine Learning MLOps](https://aka.ms/mlops) 리포지토리
* [Azure Machine Learning MLOpsPython](https://github.com/Microsoft/MLOpspython) 리포지토리

또한 Azure Data Factory를 사용하여 학습에 사용할 데이터를 준비하는 데이터 수집 파이프라인을 만들 수 있습니다. 자세한 내용은 [데이터 수집 파이프라인](how-to-cicd-data-ingestion.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 리소스를 읽고 탐구하여 자세히 알아보세요.

+ Azure Machine Learning을 통한 [모델 배포 방법 및 위치](how-to-deploy-and-where.md)

+ [자습서: ACI에서 이미지 분류 모델 배포](tutorial-deploy-models-with-aml.md)

+ [엔드투엔드 MLOps 예제 리포지토리](https://github.com/microsoft/MLOps)

+ [Azure Pipelines를 사용한 ML 모델 CI/CD](/azure/devops/pipelines/targets/azure-machine-learning)

+ [배포된 모델을 사용](how-to-consume-web-service.md)하는 클라이언트 만들기

+ [대규모 기계 학습](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI 참조 아키텍처 및 모범 사례 보고서](https://github.com/microsoft/AI)
