---
title: 작업 영역이란?
titleSuffix: Azure Machine Learning
description: 작업 영역은 Azure Machine Learning의 최상위 리소스입니다. 스크립트의 로그, 메트릭, 출력 및 스냅샷을 비롯한 모든 학습 실행의 기록을 유지합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2021
ms.openlocfilehash: 37e6573e1bc26bc79477cf532ef442964f760985
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226784"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역이란?

작업 영역은 Azure Machine Learning의 최상위 리소스로, Azure Machine Learning을 사용할 때 만든 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다.  작업 영역은 스크립트의 로그, 메트릭, 출력 및 스냅샷을 비롯한 모든 학습 실행의 기록을 유지합니다. 이 정보를 사용하여 최고의 모델을 생성하는 학습 실행을 확인합니다.  

원하는 모델이 있다면 작업 영역에 등록하세요. 등록한 모델과 채점 스크립트를 사용하여 Azure Container Instances, Azure Kubernetes Service 또는 FPGA(Field-Programmable Gate Array)에 REST 기반 HTTP 엔드포인트로 배포할 수 있습니다.

## <a name="taxonomy"></a>분류 

다음은 작업 영역의 분류 체계를 보여주는 다이어그램입니다.

[![작업 영역 분류](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

이 다이어그램에서는 작업 영역의 다음 구성 요소를 확인할 수 있습니다.

+ 작업 영역에는 Azure Machine Learning을 실행하는 데 필요한 Python 환경으로 구성된 클라우드 리소스인 [Azure Machine Learning 컴퓨팅 인스턴스](concept-compute-instance.md)가 포함될 수 있습니다.

+ [사용자 역할](how-to-assign-roles.md)을 사용하면 작업 영역을 다른 사용자, 팀 또는 프로젝트와 공유할 수 있습니다.
+ [컴퓨팅 대상](concept-azure-machine-learning-architecture.md#compute-targets)은 실험을 실행하는 데 사용합니다.
+ 작업 영역을 만들면 [연결된 리소스](#resources)도 생성됩니다.
+ [실험](concept-azure-machine-learning-architecture.md#experiments)은 모델을 작성하는 데 사용하는 학습 실행입니다.  
+ [파이프라인](concept-azure-machine-learning-architecture.md#ml-pipelines)은 모델 학습 및 재학습을 위한 재사용 가능한 워크플로입니다.
+ [데이터 세트](concept-azure-machine-learning-architecture.md#datasets-and-datastores)는 모델 학습 및 파이프라인 생성에 사용하는 데이터 관리에 도움이 됩니다.
+ 배포하고 싶은 모델이 있다면 등록된 모델을 만드세요.
+ 등록된 모델과 채점 스크립트를 사용하여 [배포 엔드포인트](concept-azure-machine-learning-architecture.md#endpoints)를 만듭니다.

## <a name="tools-for-workspace-interaction"></a>작업 영역 상호 작용을 위한 도구

작업 영역에서는 다음 방식으로 상호 작용할 수 있습니다.

> [!IMPORTANT]
> 아래에 표시된 도구(미리 보기)는 현재 공개 미리 보기로 제공됩니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

+ 웹에서:
    + [Azure Machine Learning 스튜디오 ](https://ml.azure.com) 
    + [Azure Machine Learning 디자이너](concept-designer.md) 
+ [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)를 사용하는 모든 Python 환경
+ Azure Machine Learning [CLI 확장](./reference-azure-machine-learning-cli.md)을 사용하는 명령줄에서
+ [Azure Machine Learning VS Code 확장](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>작업 영역을 사용하는 기계 학습

기계 학습 작업은 작업 영역을 대상으로 아티팩트를 읽고 씁니다.

+ 실험을 실행하여 모델 학습 - 실험 실행 결과를 작업 영역에 씁니다.
+ 자동화된 ML을 사용하여 모델 학습 - 학습 결과를 작업 영역에 기록합니다.
+ 모델을 작업 영역에 등록합니다.
+ 모델 배포 - 등록된 모델을 사용하여 배포를 만듭니다.
+ 재사용 가능한 워크플로를 만들고 실행합니다.
+ 실험, 파이프라인, 모델, 배포 같은 기계 학습 아티팩트를 확인합니다.
+ 모델을 추적하고 모니터링합니다.

## <a name="workspace-management"></a>작업 영역 관리

다음 작업 영역 관리 작업도 수행할 수 있습니다.

| 작업 영역 관리 작업   | 포털              | 스튜디오 | Python SDK      | Azure CLI        | VS 코드
|---------------------------|---------|---------|------------|------------|------------|
| 작업 영역 만들기        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| 작업 영역 액세스 관리    | **&check;**   || |  **&check;**    ||
| 컴퓨팅 리소스 만들기 및 관리    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Notebook VM 만들기 |   | **&check;** | |     ||

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동하거나 소유하는 구독을 새 테넌트로 이동하는 것은 지원되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> 작업 영역 만들기

작업 영역은 다양한 방법으로 만들 수 있습니다.  

* [Azure Portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace)의 마우스 클릭 인터페이스를 사용하여 각 단계를 진행합니다.
* [Python용 Azure Machine Learning SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace)를 사용하여 Python 스크립트나 Jupyter 노트북에서 바로 작업 영역을 만듭니다.
* 회사 보안 표준을 이용해 생성을 자동화 또는 사용자 지정해야 한다면 [Azure Resource Manager 템플릿](how-to-create-workspace-template.md)이나 [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md)를 사용합니다.
* Visual Studio Code에서 작업한다면 [VS Code 확장](how-to-manage-resources-vscode.md#create-a-workspace)을 사용합니다.

> [!NOTE]
> 작업 영역 이름은 대/소문자를 구분하지 않습니다.

## <a name="sub-resources"></a><a name="sub-resources"></a> 하위 리소스

이러한 하위 리소스는 AML 작업 영역에서 만들어지는 주요 리소스입니다.

* VM: AML 작업 영역에 컴퓨팅 성능을 제공하며 모델 배포 및 학습의 필수적인 부분입니다.
* Load Balancer: 컴퓨팅 인스턴스/클러스터가 중지된 동안에도 트래픽을 관리하기 위해 각 컴퓨팅 인스턴스 및 컴퓨팅 클러스터에 대해 네트워크 부하 분산 장치가 만들어집니다.
* Virtual Network: Azure 리소스가 서로 간에, 인터넷 및 기타 온-프레미스 네트워크와 통신하도록 지원합니다.
* 대역폭: 지역 간 모든 아웃바운드 데이터 전송을 캡슐화합니다.

## <a name="associated-resources"></a><a name="resources"></a> 연결된 리소스

새 작업 영역을 만들면 작업 영역에서 사용되는 여러 Azure 리소스가 자동으로 생성됩니다.

+ [Azure Storage 계정](https://azure.microsoft.com/services/storage/): 작업 영역의 기본 데이터 저장소로 사용합니다.  Azure Machine Learning 컴퓨팅 인스턴스와 함께 사용하는 Jupyter Notebook도 여기에 저장됩니다. 
  
  > [!IMPORTANT]
  > 기본적으로 스토리지 계정은 범용 v1 계정입니다. 작업 영역이 생성되면 [이 계정을 범용 v2](../storage/common/storage-account-upgrade.md)로 업그레이드할 수 있습니다. 범용 v2로 업그레이드한 후에는 스토리지 계정에서 계층 구조 네임스페이스를 사용하도록 설정하지 않아야 합니다.

  기존 Azure Storage 계정을 사용하는 경우에는 계정이 BlobStorage나 프리미엄 계정(Premium_LRS 및 Premium_GRS)이어서는 안 됩니다. 또한 (Azure Data Lake Storage Gen2와 함께 사용하는) 계층 구조 네임스페이스를 가져서도 안 됩니다. Premium Storage 또는 계층 구조 네임스페이스는 모두 작업 영역의 _기본_ 스토리지 계정에서 지원되지 않습니다. 기본이 아닌 스토리지 계정으로 Premium Storage 또는 계층 구조 네임스페이스를 사용할 수 있습니다.
  
+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): 학습 중 및 모델을 배포할 때 사용하는 docker 컨테이너를 등록합니다. 비용을 최소화하기 위해 ACR은 배포 이미지가 생성되기 전까지는 **지연 로드** 됩니다.

    > [!NOTE]
    > 구독 설정에 따라 리소스에 태그를 추가해야 하는 경우 태그를 ACR로 설정할 수 없으므로 Azure Machine Learning에서 만들어진 ACR(Azure Container Registry)가 실패합니다.

+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): 모델 관련 모니터링 정보를 저장합니다.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): 컴퓨팅 대상에서 사용하는 비밀 및 작업 영역에 필요한 기타 중요한 정보를 저장합니다.

> [!NOTE]
> [Python SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace) 또는 [ARM 템플릿을 사용](how-to-create-workspace-template.md)하는 Azure Machine Learning CLI로 작업 영역을 만들 때는 기존 Azure 리소스 인스턴스를 사용해도 됩니다.

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Enterprise Edition의 변경 사항

2020년 9월 기준 Enterprise Edition 작업 영역에서 사용할 수 있었던 모든 기능을 이제 Basic Edition 작업 영역에서도 사용할 수 있습니다. 새 Enterprise 작업 영역은 더 이상 만들 수 없습니다.  `sku` 매개 변수를 사용하는 모든 SDK, CLI 또는 Azure Resource Manager 호출은 계속 작동하지만 Basic 작업 영역이 프로비저닝됩니다.

12월 21일부터 모든 Enterprise Edition 작업 영역은 동일한 기능을 제공하는 Basic Edition으로 자동 설정됩니다. 이 프로세스 중에는 가동 중지 시간이 발생하지 않습니다. 2021년 1월 1일부로 Enterprise Edition은 공식적으로 사용이 중지됩니다. 

두 에디션 모두에서 고객은 사용한 Azure 리소스의 비용만 지불하며 Azure Machine Learning에 대한 추가 요금은 지불하지 않습니다. 자세한 내용은 [Azure Machine Learning 가격 책정 페이지](https://azure.microsoft.com/pricing/details/machine-learning/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

조직의 요구 사항에 맞게 작업 영역을 계획하는 방법에 대한 자세한 내용은 [Azure Machine Learning 구성 및 설정](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-resource-organization)을 참조하세요.

Azure Machine Learning을 시작하려면 다음을 참조하세요.

+ [Azure Machine Learning이란 무엇인가요?](overview-what-is-azure-machine-learning.md)
+ [작업 영역 만들기 및 관리](how-to-manage-workspace.md)
+ [자습서: Azure Machine Learning 시작](quickstart-create-resources.md)
+ [자습서: 자동화된 Machine Learning을 사용하여 처음으로 분류 모델 만들어 보기](tutorial-first-experiment-automated-ml.md) 
+ [자습서: 디자이너를 사용하여 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md)
