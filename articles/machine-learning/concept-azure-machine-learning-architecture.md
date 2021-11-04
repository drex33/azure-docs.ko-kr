---
title: 아키텍처 및 주요 개념
titleSuffix: Azure Machine Learning
description: 이 문서에서는 Azure Machine Learning을 구성하는 아키텍처, 용어 및 개념에 대한 개략적인 이해를 제공합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/21/2021
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: a759bda6549df753d03e1fe0e8933f454f589d74
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563419"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure Machine Learning 작동 방법: 아키텍처 및 개념

[Azure Machine Learning](overview-what-is-azure-machine-learning.md)의 아키텍처 및 개념에 대해 알아봅니다.  이 문서에서는 구성 요소와, 구성 요소가 연동하여 기계 학습 모델의 빌드, 배포 및 유지 관리 프로세스를 지원하는 방법에 대해 간략하게 설명합니다.

## <a name="workspace"></a><a name="workspace"></a> 작업 영역

[기계 학습 작업 영역](concept-workspace.md)은 최상위 수준의 Azure Machine Learning 리소스입니다.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="다이어그램: 작업 영역 및 해당 구성 요소의 Azure Machine Learning 아키텍처":::

작업 영역은 다음에 대한 중앙 위치입니다.

* [컴퓨팅](#compute-instance) 등 모델의 학습 및 배포에 사용하는 리소스를 관리합니다.
* 다음을 포함하여 Azure Machine Learning을 사용할 때 만든 자산을 저장합니다.
  * [환경](#environments)
  * [실험](#experiments)
  * [파이프라인](#ml-pipelines)
  * [데이터 세트](#datasets-and-datastores)
  * [Models](#models)
  * [엔드포인트](#endpoints)

작업 영역에는 작업 영역에서 사용하는 다른 Azure 리소스가 포함됩니다.

+ [ACR(Azure Container Registry)](https://azure.microsoft.com/services/container-registry/): 학습 중 및 모델을 배포할 때 사용하는 docker 컨테이너를 등록합니다. 비용을 최소화하기 위해 ACR은 배포 이미지를 만들 때만 생성됩니다.
+ [Azure Storage 계정](https://azure.microsoft.com/services/storage/): 작업 영역에 대한 기본 데이터 저장소로 사용됩니다.  Azure Machine Learning 컴퓨팅 인스턴스에 사용하는 Jupyter Notebook도 여기에 저장됩니다.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): 모델에 대한 모니터링 정보를 저장합니다.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): 컴퓨팅 대상에서 사용되는 비밀 및 작업 영역에 필요한 기타 중요한 정보를 저장합니다.

작업 영역을 다른 사용자와 공유할 수 있습니다.

## <a name="computes"></a>컴퓨팅

<a name="compute-targets"></a> [컴퓨팅 대상](concept-compute-target.md)은 학습 스크립트를 실행하거나 서비스 배포를 호스트하는 데 사용하는 컴퓨터, 또는 컴퓨터 세트입니다. 로컬 컴퓨터 또는 원격 컴퓨팅 리소스를 컴퓨팅 대상으로 사용할 수 있습니다.  컴퓨팅 대상을 사용하면 로컬 컴퓨터에서 학습을 시작한 다음, 학습 스크립트 변경 없이 클라우드로 확장할 수 있습니다.

Azure Machine Learning에는 기계 학습 작업용으로 구성된 두 가지 완전 관리형 클라우드 기반 VM(가상 머신)이 도입되었습니다.

* <a name="compute-instance"></a> **컴퓨팅 인스턴스**: 컴퓨팅 인스턴스는 기계 학습을 위해 설치된 여러 도구와 환경을 포함하는 VM입니다. 컴퓨팅 인스턴스는 주로 개발 워크스테이션에 사용됩니다.  설정 없이 샘플 Notebook 실행을 시작할 수 있습니다. 컴퓨팅 인스턴스는 학습 및 추론 작업에 대한 컴퓨팅 대상으로도 사용할 수 있습니다.

* **컴퓨팅 클러스터**: 컴퓨팅 클러스터는 다중 노드 크기 조정 기능이 있는 VM의 클러스터입니다. 컴퓨팅 클러스터는 대량 작업 및 프로덕션에 대한 컴퓨팅 대상에 더 적합합니다.  클러스터는 작업이 제출되면 자동으로 크기를 조정합니다.  학습 컴퓨팅 대상으로, 또는 개발/테스트 배포용으로 사용합니다.

학습 컴퓨팅 대상에 대한 자세한 내용은 [학습 컴퓨팅 대상](concept-compute-target.md#train)을 참조하세요.  배포 컴퓨팅 대상에 대한 자세한 내용은 [배포 대상](concept-compute-target.md#deploy)을 참조하세요.

## <a name="datasets-and-datastores"></a>데이터 세트 및 데이터 저장소

[**Azure Machine Learning 데이터 세트**](concept-data.md#datasets)를 사용하면 더 간편하게 데이터에 액세스하고 사용할 수 있습니다. 데이터 세트를 만들면 데이터 원본 위치에 대한 참조와 해당 메타데이터의 복사본을 만듭니다. 데이터가 기존 위치에 남아 있기 때문에 추가 스토리지 비용이 발생하지 않으며 데이터 원본의 무결성에 대한 위험이 없습니다.

자세한 내용은 [Azure Machine Learning 데이터 세트 만들기 및 등록](how-to-create-register-datasets.md)을 참조하세요.  데이터 세트를 사용하는 추가 예제는 [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)을 참조하세요.

데이터 세트는 [데이터 저장소](concept-data.md#datastores)를 사용하여 Azure Storage 서비스에 안전하게 연결합니다. 데이터 저장소는 인증 자격 증명과 원래 데이터 원본의 무결성을 위험에 노출하지 않으면서 연결 정보를 저장합니다. 작업 영역과 연결된 키 자격 증명 모음의 구독 ID 및 토큰 권한 부여 같은 연결 정보를 저장하므로, 스크립트에서 이러한 정보를 하드 코딩할 필요 없이 스토리지에 안전하게 액세스할 수 있습니다.

## <a name="environments"></a>환경

[작업 영역](#workspace) > **환경**

[환경](concept-environments.md)은 기계 학습 모델의 학습 또는 채점이 발생하는 환경의 캡슐화입니다. 환경은 학습 및 채점 스크립트와 관련된 Python 패키지, 환경 변수, 소프트웨어 설정을 지정합니다.  

코드 샘플은 [환경을 사용하는 방법](how-to-use-environments.md#manage-environments)의 "환경 관리" 섹션을 참조하세요.

## <a name="experiments"></a>실험

[작업 영역](#workspace) > **실험**

실험은 지정된 스크립트의 많은 실행을 그룹화한 것입니다. 실험은 항상 작업 영역에 속합니다. 실행을 제출할 때 실험 이름을 제공합니다. 실행에 대한 정보는 해당 실험 아래에 저장됩니다. 실험을 제출할 때 이름이 없으면 새 실험을 자동으로 만듭니다.
  
실험을 사용하는 예제는 [자습서: 첫 번째 모델 학습](tutorial-1st-experiment-sdk-train.md)을 참조하세요.

### <a name="runs"></a>실행

[작업 영역](#workspace) > [실험](#experiments) > **실행**

실행은 학습 스크립트의 단일 실행을 말합니다. 실험은 일반적으로 여러 실행을 포함합니다.

Azure Machine Learning은 모든 실행을 기록하고 실험에 다음 정보를 저장합니다.

* 실행에 대한 메타데이터(타임스탬프, 기간 등)
* 스크립트를 통해 기록된 메트릭
* 실험을 통해 자동 수집되거나 사용자가 명시적으로 업로드한 출력 파일
* 실행 전의 스크립트를 포함하는 디렉터리의 스냅샷

모델을 학습시키기 위해 스크립트를 제출할 때 모델을 생성합니다. 실행에는 0개 이상의 자식 실행이 포함될 수 있습니다. 예를 들어 최상위 실행에는 두 개의 자식 실행이 포함될 수 있고, 각 자식 실행에는 자체 자식 실행이 포함될 수 있습니다.

### <a name="run-configurations"></a>실행 구성

[작업 영역](#workspace) > [실험](#experiments) > [실행](#runs) > **실행 구성**

실행 구성은 지정된 컴퓨팅 대상에서 스크립트를 실행하는 방법을 정의합니다. 구성을 사용하여 스크립트, 컴퓨팅 대상 및 실행할 Azure ML 환경, 모든 분산 작업 관련 구성 및 일부 추가 속성을 지정합니다. 실행에 대해 구성 가능한 전체 옵션 집합의 자세한 내용은 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)를 참조하세요.

실행 구성은 학습 스크립트를 포함하는 디렉터리 내의 파일에 지속될 수 있습니다.   또는 메모리 내 개체로 구성되고 실행을 제출하는 데 사용될 수 있습니다.

예제 실행 구성은 [학습 실행 구성](how-to-set-up-training-targets.md)을 참조하세요.

### <a name="snapshots"></a>스냅샷

[작업 영역](#workspace) > [실험](#experiments) > [실행](#runs) > **스냅샷**

실행을 제출하면 Azure Machine Learning은 스크립트를 포함하는 디렉터리를 zip 파일로 압축하여 컴퓨팅 대상으로 보냅니다. 그런 다음, zip 파일이 추출되고 스크립트가 실행됩니다. 또한 Azure Machine Learning은 zip 파일을 실행 기록의 일부인 스냅샷으로 저장합니다. 작업 영역에 대한 액세스 권한이 있는 사용자는 실행 기록을 찾아보고 스냅샷을 다운로드할 수 있습니다.

### <a name="logging"></a>로깅

Azure Machine Learning은 표준 실행 메트릭을 자동으로 기록합니다. 그러나 [Python SDK를 사용하여 임의의 메트릭을 로그할](how-to-log-view-metrics.md)수도 있습니다.

실시간 상태 실행 모니터링, 완료 후 결과 보기 등 여러 가지 방법으로 로그를 볼 수 있습니다. 자세한 내용은 [ML 실행 로그 모니터링 및 보기](how-to-log-view-metrics.md)를 참조하세요.


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리인 학습 실행을 시작하면 리포지토리에 대한 정보가 실행 기록에 저장됩니다. 이것은 스크립트 실행 구성이나 ML 파이프라인을 사용하여 제출된 실행에서 작동합니다. SDK 또는 Machine Learning CLI에서 제출된 실행에 대해서도 진행됩니다.

자세한 내용은 [Azure Machine Learning에 대한 Git 통합](concept-train-model-git-integration.md)을 참조하세요.

### <a name="training-workflow"></a>학습 워크플로

실험을 실행하여 모델을 학습할 때는 다음 단계가 수행됩니다. 이것은 아래 학습 워크플로 다이어그램에 나와 있습니다.

* Azure Machine Learning은 이전 섹션에서 저장한 코드 스냅샷의 스냅샷 ID를 사용하여 호출됩니다.
* Azure Machine Learning은 실행 ID(선택 사항)와 Machine Learning Service 토큰을 만들며, 이것은 나중에 Machine Learning 컴퓨팅/VM과 같은 컴퓨팅 대상이 Machine Learning Service와 통신하는 데 사용됩니다.
* 관리되는 컴퓨팅 대상(예: Machine Learning 컴퓨팅) 또는 관리되지 않는 컴퓨팅 대상(예: VM) 중 하나를 선택하여 학습 작업을 실행할 수 있습니다. 두 시나리오에 대한 데이터 흐름은 다음과 같습니다.
   * VM/HDInsight에 Microsoft 구독의 키 자격 증명 모음에 있는 SSH 자격 증명을 사용하여 액세스합니다. Azure Machine Learning이 컴퓨팅 대상에서 관리 코드를 실행하여 다음이 수행됩니다.

   1. 환경을 준비합니다. (Docker는 VM 및 로컬 컴퓨터에 대한 옵션입니다. Machine Learning 컴퓨팅에 대한 다음 단계를 참조하면 Docker 컨테이너에서 실험을 실행하는 방식을 이해할 수 있습니다.)
   1. 코드를 다운로드합니다.
   1. 환경 변수 및 구성을 설정합니다.
   1. 사용자 스크립트를 실행합니다(이전 섹션에서 언급한 코드 스냅샷).

   * Machine Learning 컴퓨팅, 작업 영역 관리 ID를 통해 액세스합니다.
Machine Learning 컴퓨팅은 관리되는 컴퓨팅 대상(즉, Microsoft에서 관리됨)이므로 Microsoft 구독 하에 실행됩니다.

   1. 원격 Docker 구성이 시작됩니다(필요한 경우).
   1. 관리 코드가 사용자의 Azure Files 공유에 기록됩니다.
   1. 컨테이너가 초기 명령으로 시작됩니다. 즉, 이전 단계에서 설명한 관리 코드입니다.

* 실행이 완료되면 실행 및 메트릭을 쿼리할 수 있습니다. 이 단계는 아래 흐름 다이어그램에서 학습 컴퓨팅 대상이 실행 메트릭을 Cosmos DB 데이터베이스의 스토리지에서 Azure Machine Learning에 다시 쓸 때 발생합니다. 클라이언트는 Azure Machine Learning을 호출할 수 있습니다. Machine Learning은 Cosmos DB 데이터베이스에서 메트릭을 차례로 끌어와서 클라이언트에 다시 반환합니다.

[![학습 워크플로](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>모델

간단하게 설명하면, 하나의 모델은 입력을 받아들이고 출력을 생성하는 코드의 한 조각을 말합니다. 기계 학습 모델을 만드는 동안 알고리즘을 선택하고, 데이터를 제공하고, [하이퍼 매개 변수](how-to-tune-hyperparameters.md)를 조정합니다. 학습은 학습된 모델을 생성하는 반복 프로세스이며, 학습된 모듈에는 모델이 학습 프로세스 중에 습득한 내용이 캡슐화되어 있습니다.

Azure Machine Learning 외부에서 학습된 모델을 불러올 수 있습니다. 또는 Azure Machine Learning에서 [컴퓨팅 대상](#compute-targets)에 [실험](#experiments)의 [실행](#runs)을 제출하여 모델을 교육할 수 있습니다. 모델이 있으면 작업 영역에 [모델을 등록합니다](#register-model).

Azure Machine Learning은 프레임워크에 제약이 없습니다. 모델을 만드는 경우 Scikit-learn, XGBoost, PyTorch, TensorFlow 및 Chainer와 같은 인기 있는 기계 학습 프레임워크를 사용할 수 있습니다.

Scikit-learn을 사용하여 모델을 학습하는 방법에 대한 예제를 보려면 [자습서: Azure Machine Learning을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.


### <a name="model-registry"></a><a name="register-model"></a> 모델 레지스트리

[작업 영역](#workspace) > **모델**

**모델 레지스트리** 에서는 Azure Machine Learning 작업 영역의 모든 모델을 추적할 수 있습니다.

모델은 이름 및 버전으로 식별됩니다. 기존 이름과 동일한 이름을 사용하여 모델을 등록할 때마다 레지스트리는 해당 모델을 새 버전으로 간주합니다. 버전 번호가 증가하면 동일 이름으로 새 모델을 등록합니다.

모델을 등록할 때 추가 메타데이터 태그를 제공하면 모델을 검색할 때 해당 태그를 사용할 수 있습니다.

> [!TIP]
> 등록된 모델은 모델을 구성하는 하나 이상의 파일에 대한 논리적 컨테이너입니다. 예를 들어, 여러 파일에 저장된 모델의 경우 Azure Machine Learning 작업 영역에서 단일 모델로 등록할 수 있습니다. 등록 후에 등록된 모델을 다운로드하거나 배포하고 등록된 모든 파일을 받을 수 있습니다.

활성 배포에서 사용 중인 등록된 모델은 삭제할 수 없습니다.

모델을 등록하는 예제는 [Azure Machine Learning을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.

## <a name="deployment"></a>배포

[등록된 모델](#register-model)을 서비스 엔드포인트로 배포합니다. 다음 구성 요소가 필요합니다.

* **환경**. 이 환경은 유추를 위해 모델을 실행하는 데 필요한 종속성을 캡슐화합니다.
* **채점 코드**. 이 스크립트는 요청을 수락하고, 모델을 사용하여 요청을 채점하며, 결과를 반환합니다.
* **유추 구성**. 유추 구성은 환경, 항목 스크립트 및 모델을 서비스로 실행하는 데 필요한 다른 구성 요소를 지정합니다.

이러한 구성 요소에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

### <a name="endpoints"></a>엔드포인트

[작업 영역](#workspace) > **엔드포인트**

엔드포인트는 클라우드에서 호스트할 수 있는 웹 서비스로의 모델 인스턴스화입니다.

#### <a name="web-service-endpoint"></a>웹 서비스 엔드포인트

모델을 웹 서비스로 배포할 때 엔드포인트를 Azure Container Instances, Azure Kubernetes Service 또는 FPGA에 배포할 수 있습니다. 모델, 스크립트 및 연결된 파일에서 서비스를 만듭니다. 이러한 항목은 모델에 대한 실행 환경을 포함하는 기본 컨테이너 이미지에 배치됩니다. 이미지에는 웹 서비스에 전송된 점수 매기기 요청을 수신하는 부하 분산된 HTTP 엔드포인트가 있습니다.

Application Insights 원격 분석 또는 모델 원격 분석을 사용하도록 설정하여 웹 서비스를 모니터링할 수 있습니다. 원격 분석 데이터는 본인만 액세스할 수 있습니다.  Application Insights 및 스토리지 계정 인스턴스에 저장됩니다. 자동 크기 조정을 사용하도록 설정한 경우 Azure에서 배포 크기를 자동으로 조정합니다.

다음 다이어그램에서는 웹 서비스 엔드포인트로 배포된 모델에 대한 유추 워크플로를 보여줍니다.

세부 정보는 다음과 같습니다.

* 사용자가 Azure Machine Learning SDK와 같은 클라이언트를 사용하여 모델을 등록합니다.
* 사용자가 모델, 점수 파일 및 기타 모델 종속성을 사용하여 이미지를 만듭니다.
* Docker 이미지가 생성되어 Azure Container Registry에 저장됩니다.
* 이전 단계에서 생성된 이미지를 사용하여 웹 서비스가 컴퓨팅 대상(Container Instances/AKS)에 배포됩니다.
* 채점 요청 정보가 사용자의 구독에 있는 Application Insights에 저장됩니다.
* 원격 분석도 Microsoft/Azure 구독으로 푸시됩니다.

[![유추 워크플로](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


모델을 웹 서비스로 배포하는 예제는 [Azure Container Instance에 이미지 분류 모델 배포](tutorial-deploy-models-with-aml.md)를 참조하세요.

#### <a name="real-time-endpoints"></a>실시간 엔드포인트

디자이너에서 학습된 모델을 배포할 때 [모델을 실시간 엔드포인트](tutorial-designer-automobile-price-deploy.md)로 배포할 수 있습니다. 실시간 엔드포인트는 일반적으로 REST 엔드포인트를 통해 단일 요청을 수신하고 실시간으로 예측을 반환합니다. 이것은 여러 값을 한 번에 처리하고 완료 후에 결과를 데이터 저장소에 저장하는 일괄 처리와 대조적입니다.

#### <a name="pipeline-endpoints"></a>파이프라인 엔드포인트

파이프라인 엔드포인트를 사용하면 REST 엔드포인트를 통해 프로그래밍 방식으로 [ML 파이프라인](#ml-pipelines)을 호출할 수 있습니다. 파이프라인 엔드포인트를 통해 파이프라인 워크플로를 자동화할 수 있습니다.

파이프라인 엔드포인트는 게시된 파이프라인의 컬렉션입니다. 이 논리적 조직에서는 동일한 엔드포인트를 사용하여 여러 파이프라인을 관리하고 호출할 수 있습니다. 파이프라인 엔드포인트에서 게시된 각 파이프라인의 버전이 지정됩니다. 엔드포인트에 대해 기본 파이프라인을 선택하거나 REST 호출에서 버전을 지정할 수 있습니다.
 


## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md)는 Azure CLI의 확장으로, Azure 플랫폼에 대한 플랫폼 간 명령줄 인터페이스입니다. 이 확장은 기계 학습 작업을 자동화하는 명령을 제공합니다.

### <a name="ml-pipelines"></a>ML 파이프라인

[기계 학습 파이프라인](concept-ml-pipelines.md)을 사용하여 기계 학습 단계를 연결하는 워크플로를 만들고 관리합니다. 예를 들어, 파이프라인에는 데이터 준비, 모델 학습, 모델 배포 및 추론/점수 매기기 단계가 포함될 수 있습니다. 각 단계(phase)는 각각 다양한 컴퓨팅 대상에서 자동으로 실행될 수 있는 여러 단계(step)를 포함할 수 있습니다. 

파이프라인 단계는 다시 사용할 수 있으며 해당 단계의 출력이 변경되지 않은 경우 이전 단계를 다시 실행하지 않고 실행할 수 있습니다. 예를 들어, 데이터가 변경되지 않은 경우 비용이 많이 드는 데이터 준비 단계를 다시 실행하지 않고 모델을 다시 학습할 수 있습니다. 파이프라인을 사용하여 데이터 과학자들은 기계 학습 워크플로의 개별 영역에서 작업하면서 협력할 수 있습니다.

## <a name="monitoring-and-logging"></a>모니터링 및 로깅

Azure Machine Learning은 다음과 같은 모니터링 및 로깅 기능을 제공합니다.

* __데이터 과학자__ 의 경우 학습 실행에서 실험을 모니터링하고 정보를 기록할 수 있습니다. 자세한 내용은 다음 아티클을 참조하세요.
   * [학습 실행 시작, 모니터링 및 취소](how-to-track-monitor-analyze-runs.md)
   * [학습 실행에 대한 메트릭 로그](how-to-log-view-metrics.md)
   * [MLflow로 실험 추적](how-to-use-mlflow.md)
   * [TensorBoard로 시각화 실행](how-to-monitor-tensorboard.md)
* __관리자__ 의 경우 작업 영역 및 관련 Azure 리소스와 Azure Monitor의 리소스 생성 및 삭제 같은 이벤트에 대한 정보를 모니터링할 수 있습니다. 자세한 내용은 [Azure Machine Learning 모니터링 방법](monitor-azure-machine-learning.md)을 참조하세요.
* __DevOps__ 또는 __MLOps__ 의 경우 웹 서비스로 배포된 모델에서 생성된 정보를 모니터링하여 배포에서의 문제를 식별하고 서비스에 제출된 데이터를 수집할 수 있습니다. 자세한 내용은 [모델 데이터 수집](how-to-enable-data-collection.md) 및 [Application Insights를 사용한 모니터링](how-to-enable-app-insights.md)을 참조하세요.

## <a name="interacting-with-your-workspace"></a>작업 영역 상호 작용

### <a name="studio"></a>스튜디오

[Azure Machine Learning 스튜디오](overview-what-is-machine-learning-studio.md)는 작업 영역의 모든 아티팩트에 대한 웹 보기를 제공합니다.  데이터 세트, 실험, 파이프라인, 모델 및 엔드포인트의 결과 및 세부 정보를 볼 수 있습니다.  또한 스튜디오에서 컴퓨팅 리소스 및 데이터 저장소를 관리할 수 있습니다.

스튜디오에서 Azure Machine Learning의 일부인 대화형 도구에 액세스할 수도 있습니다.

+ [Azure Machine Learning 디자이너](concept-designer.md)를 사용하여 코드를 작성하지 않고 워크플로 단계를 수행합니다.
+ [자동화된 Machine Learning](concept-automated-ml.md)을 위한 웹 환경
+ [Azure Machine Learning Notebook](how-to-run-jupyter-notebooks.md)은 통합 Jupyter Notebook 서버에서 고유의 코드를 작성하고 실행합니다.
+ [이미지](how-to-create-image-labeling-projects.md) 또는 [텍스트에](how-to-create-text-labeling-projects.md)레이블을 지정하기 위한 프로젝트를 만들고, 관리하고, 모니터링하는 데이터 레이블 지정 프로젝트

### <a name="programming-tools"></a>프로그래밍 도구

> [!IMPORTANT]
> 아래의 표시된 도구(미리 보기)는 현재 공개 미리 보기로 제공됩니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

+  [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)를 사용하여 Python 환경에서 서비스와 상호 작용합니다.
+ [Azure Machine Learning 디자이너](concept-designer.md)를 사용하여 코드를 작성하지 않고 워크플로 단계를 수행합니다. 
+ 자동화에 [Azure Machine Learning CLI](./reference-azure-machine-learning-cli.md)를 사용합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning을 시작하려면 다음을 참조하세요.

* [Azure Machine Learning이란 무엇인가요?](overview-what-is-azure-machine-learning.md)
* [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)
* [자습서(1부): 모델 학습](tutorial-train-models-with-aml.md)