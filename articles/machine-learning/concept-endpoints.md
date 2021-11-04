---
title: 엔드포인트(미리 보기)란?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 엔드포인트(미리 보기)을 통해 기계 학습 배포를 단순화하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2, ignite-fall-2021
ms.date: 10/21/2021
ms.openlocfilehash: 03d5c93d27587e88ae1f21e12b8a729fab042ac7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554035"
---
# <a name="what-are-azure-machine-learning-endpoints-preview"></a>Azure Machine Learning 엔드포인트(미리 보기)란? 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Azure Machine Learning 엔드포인트(미리 보기)를 사용하여 실시간 및 일괄 처리 유추 배포를 위한 모델 배포를 간소화합니다. 엔드포인트는 컴퓨팅 유형 간에 모델 배포를 호출하고 관리할 수 있는 통합 인터페이스를 제공합니다.

이 문서에서는 다음에 대해 알아봅니다.
> [!div class="checklist"]
> * 엔드포인트
> * 배포
> * 관리형 온라인 엔드포인트
> * Kubernetes 온라인 엔드포인트
> * 일괄 처리 유추 엔드포인트

## <a name="what-are-endpoints-and-deployments-preview"></a>엔드포인트 및 배포(미리 보기)란?

기계 학습 모델을 학습시킨 후 다른 사람이 추론을 수행하는 데 사용할 수 있도록 모델을 배포해야 합니다. Azure Machine Learning에서는 **엔드포인트**(미리 보기) 및 **배포**(미리 보기)를 사용하여 이 작업을 수행할 수 있습니다.

**엔드포인트** 는 클라이언트가 학습된 모델의 유추(채점) 출력을 수신하기 위해 호출할 수 있는 HTTPS 엔드포인트입니다. 이 콘솔은 다음과 같은 기능을 제공합니다. 
- "키 & 토큰" 기반 인증을 사용한 인증 
- SSL 종료 
- 안정적인 채점 URI(endpoint-name.region.inference.ml.azure.com)


**배포는** 실제 추론을 수행하는 모델을 호스팅하는 데 필요한 리소스 집합입니다. 

단일 엔드포인트는 여러 배포를 포함할 수 있습니다. 엔드포인트 및 배포는 Azure Portal 표시되는 리소스에 Azure Resource Manager 독립적입니다.

Azure Machine Learning은 엔드포인트 및 배포 개념을 사용하여 [온라인 엔드포인트](#what-are-online-endpoints-preview) 및 [일괄 엔드포인트](#what-are-batch-endpoints-preview)와 같은 다양한 유형의 엔드포인트를 구현합니다.

### <a name="multiple-developer-interfaces"></a>여러 개발자 인터페이스

여러 개발자 도구를 사용하여 배포 및 온라인 엔드포인트를 만들고 관리합니다.
- Azure CLI
- Azure Resource Manager/REST API
- Azure Machine Learning 스튜디오 웹 포털
- Azure Portal(IT/관리자)
- Azure CLI 인터페이스 및 REST/ARM 인터페이스를 사용하여 CI/CD MLOps 파이프라인 지원

## <a name="what-are-online-endpoints-preview"></a>온라인 엔드포인트(미리 보기)란?

**온라인 엔드포인트**(미리 보기)는 온라인(실시간) 유추에 사용되는 엔드포인트입니다. **일괄 처리 엔드포인트** 에 비해 **온라인 엔드포인트** 에는 클라이언트에서 데이터를 수신할 준비가 되어 있고 실시간으로 응답을 다시 보낼 수 있는 **배포** 가 포함되어 있습니다.

다음 다이어그램은 '파란색' 및 '녹색'의 두 배포가 있는 온라인 엔드포인트를 보여 주는 다이어그램입니다. 파란색 배포는 CPU SKU가 있는 VM을 사용하고 모델의 v1을 실행합니다. 녹색 배포는 GPU SKU가 있는 VM을 사용하고 모델의 v2를 사용합니다. 엔드포인트는 들어오는 트래픽의 90%를 파란색 배포로 라우팅하도록 구성되고 녹색은 나머지 10%를 받습니다.

:::image type="content" source="media/concept-endpoints/endpoint-concept.png" alt-text="트래픽을 두 배포로 분할하는 엔드포인트를 보여주는 다이어그램":::

### <a name="online-endpoints-requirements"></a>온라인 엔드포인트 요구 사항

온라인 엔드포인트를 만들려면 다음 요소를 지정해야 합니다.
- 모델 파일(또는 작업 영역에서 등록된 모델 지정) 
- 점수 매기기 스크립트 - 점수 매기기/추론을 수행하는 데 필요한 코드
- 환경 - Conda 종속성이 있는 Docker 이미지 또는 dockerfile 
- 컴퓨팅 인스턴스 및 크기 조정 설정 

[CLI](how-to-deploy-managed-online-endpoints.md) 및 [스튜디오 웹 포털](how-to-use-managed-online-endpoint-studio.md)에서 온라인 엔드포인트를 배포하는 방법을 알아봅니다.

### <a name="test-and-deploy-locally-for-faster-debugging"></a>더 빠른 디버깅을 위해 로컬로 테스트 및 배포

클라우드에 배포하지 않고 엔드포인트를 테스트하려면 로컬로 배포합니다. Azure Machine Learning은 Azure ML 이미지를 모방하는 로컬 Docker 이미지를 만듭니다. Azure Machine Learning은 로컬로 배포를 빌드 및 실행하고 빠른 반복을 위해 이미지를 캐시합니다.

### <a name="native-bluegreen-deployment"></a>네이티브 파란색/녹색 배포 

단일 엔드포인트에는 여러 배포가 있을 수 있음을 기억하세요. 온라인 엔드포인트는 부하 분산을 수행하여 각 배포에 대한 트래픽의 백분율을 제공할 수 있습니다.

트래픽 할당은 서로 다른 인스턴스 간에 요청을 분산하여 파란색/녹색 배포를 안전하게 롤아웃하는 데 사용할 수 있습니다.

> [!TIP]
> 요청은 의 HTTP 헤더를 포함하여 구성된 트래픽 부하 분산을 무시할 수 `azureml-model-deployment` 있습니다. 헤더 값을 요청이 라우팅할 배포의 이름으로 설정합니다.

:::image type="content" source="media/concept-endpoints/traffic-allocation.png" alt-text="배포 간 트래픽 할당을 설정하는 슬라이더 인터페이스를 보여주는 스크린샷":::

[온라인 엔드포인트에 안전하게 롤아웃](how-to-safely-rollout-managed-endpoints.md)하는 방법을 알아봅니다.

### <a name="application-insights-integration"></a>Application Insights 통합

모든 온라인 엔드포인트는 Application Insights와 통합되어 SLA를 모니터링하고 문제를 진단합니다. 

그러나 [관리형 온라인 엔드포인트](#managed-online-endpoints-vs-kubernetes-online-endpoints-preview)에는 Azure 로그 및 Azure 메트릭과의 기본 통합도 포함됩니다.

### <a name="security"></a>보안

- 인증: 키 및 Azure ML 토큰
- 관리 ID: 사용자 할당 및 시스템 할당(관리형 온라인 엔드포인트만 해당)
- 엔드포인트 호출을 위한 기본 SSL

### <a name="autoscaling"></a>자동 확장

자동 크기 조정은 애플리케이션의 부하를 처리하기 위해 적절한 양의 리소스를 자동으로 실행합니다. 관리형 엔드포인트는 Azure Monitor 자동 크기 조정 기능과의 통합을 통해 [자동 크기](/azure/azure-monitor/autoscale/autoscale-overview.md) 조정을 지원합니다. 메트릭 기반 크기 조정(예: CPU 사용률 >70%), 일정 기반 크기 조정(예: 최대 업무 시간에 대한 크기 조정 규칙) 또는 조합을 구성할 수 있습니다.

:::image type="content" source="media/concept-endpoints/concept-autoscale.png" alt-text="자동 크기 조정이 규칙에 따라 최소 인스턴스와 최대 인스턴스 간에 유연하게 제공하는 것을 보여 주는 스크린샷":::

### <a name="visual-studio-code-debugging"></a>Visual Studio Code 디버깅

Visual Studio Code 사용하면 엔드포인트를 대화형으로 디버그할 수 있습니다.

:::image type="content" source="media/concept-endpoints/visual-studio-code-full.png" alt-text="VSCode의 엔드포인트 디버깅 스크린샷." lightbox="media/concept-endpoints/visual-studio-code-full.png" :::

## <a name="managed-online-endpoints-vs-kubernetes-online-endpoints-preview"></a>관리형 온라인 엔드포인트 및 Kubernetes 온라인 엔드포인트(미리 보기)

온라인 엔드포인트에는 **관리형 온라인 엔드포인트(미리** 보기) 및 **Kubernetes 온라인 엔드포인트(미리** 보기)의 두 가지 유형이 있습니다. 다음 표는 몇 가지 주요 차이점을 보여줍니다.

|  | 관리형 온라인 엔드포인트 | Kubernetes 온라인 엔드포인트 |
|-|-|-|
| **권장 사용자** | 관리 모델 배포 및 향상된 MLOps 환경을 원하는 사용자 | Kubernetes를 선호하고 인프라 요구 사항을 자체 관리할 수 있는 사용자 |
| **인프라 관리** | 관리형 컴퓨팅 프로비전, 크기 조정, 호스트 OS 이미지 업데이트 및 보안 강화 | 사용자 책임 |
| **컴퓨팅 형식** | 관리(AmlCompute) | Kubernetes 클러스터(Kubernetes) |
| **기본 모니터링** | [Azure 모니터링](how-to-monitor-online-endpoints.md) <br> (대기 시간 및 처리량과 같은 주요 메트릭 포함) | 지원되지 않음 |
| **기본 로깅** | [엔드포인트 수준의 Azure Logs 및 Log Analytics](how-to-deploy-managed-online-endpoints.md#optional-integrate-with-log-analytics) | 지원됨 |
| **Application Insights** | 지원 여부 | 지원 여부 |
| **관리 ID** | [지원됨](how-to-access-resources-from-endpoints-managed-identities.md) | 지원됨 |
| **Virtual Network(VNET)** | 지원되지 않음(공개 미리 보기) | 지원됨 |
| **비용 보기** | [엔드포인트 및 배포 수준](how-to-view-online-endpoints-costs.md) | 클러스터 수준 |

### <a name="managed-online-endpoints"></a>관리형 온라인 엔드포인트

관리형 온라인 엔드포인트는 배포 프로세스를 간소화하는 데 도움이 될 수 있습니다. 관리되는 온라인 엔드포인트는 Kubernetes 온라인 엔드포인트보다 다음과 같은 이점을 제공합니다.

- 관리형 인프라
    - 컴퓨팅을 자동으로 프로비전하고 모델을 호스트(VM 유형 및 크기 조정 설정만 지정하면 됨) 
    - 기본 호스트 OS 이미지를 자동으로 업데이트하고 패치합니다.
    - 시스템 오류가 있는 경우 자동 노드 복구

- 모니터링 및 로그
    - [Azure Monitor와의 네이티브 통합](how-to-monitor-online-endpoints.md)을 사용하여 모델 가용성, 성능 및 SLA를 모니터링합니다.
    - Azure Log Analytics와 로그 및 네이티브 통합을 사용하여 배포를 디버그합니다.

    :::image type="content" source="media/concept-endpoints/log-analytics-and-azure-monitor.png" alt-text="엔드포인트 대기 시간의 Azure Monitor 그래프를 보여주는 스크린샷":::

- 관리 ID
    -  [채점 스크립트의 보안 리소스에 액세스하기 위해 관리 ID](tutorial-deploy-managed-endpoints-using-system-managed-identity.md) 사용

- 비용 보기 
    - 관리형 온라인 엔드포인트를 사용하면 [엔드포인트 및 배포 수준에서 비용을 모니터링](how-to-view-online-endpoints-costs.md)할 수 있습니다.
    
    :::image type="content" source="media/concept-endpoints/endpoint-deployment-costs.png" alt-text="엔드포인트 및 배포의 비용 차트 스크린샷":::

단계별 자습서는 [관리형 온라인 엔드포인트를 배포하는 방법](how-to-deploy-managed-online-endpoints.md)을 참조하세요.

## <a name="what-are-batch-endpoints-preview"></a>일괄 처리 엔드포인트(미리 보기)란?

**일괄 처리 엔드포인트(미리** 보기)는 일정 기간 동안 대량의 데이터에 대한 일괄 처리 유추를 수행하는 데 사용되는 엔드포인트입니다.  **일괄 처리 엔드포인트** 는 데이터에 대한 포인터를 수신하고 작업을 비동기적으로 실행하여 컴퓨팅 클러스터에서 데이터를 병렬로 처리합니다. 일괄 처리 엔드포인트는 추가 분석을 위해 데이터 저장소에 출력을 저장합니다.

:::image type="content" source="media/concept-endpoints/batch-endpoint.png" alt-text="단일 일괄 처리 엔드포인트가 요청을 여러 배포로 라우팅할 수 있음을 보여 주는 다이어그램이며, 그 중 하나가 기본값입니다.":::

### <a name="batch-deployment-requirements"></a>Batch 배포 요구 사항

일괄 처리 배포를 만들려면 다음 요소를 지정해야 합니다.

- 모델 파일(또는 작업 영역에 등록된 모델 지정)
- Compute
- 점수 매기기 스크립트 - 점수 매기기/추론을 수행하는 데 필요한 코드
- 환경 - Conda 종속성이 있는 Docker 이미지

[MLFlow 모델을](how-to-use-mlflow.md)배포하는 경우 둘 다 자동으로 생성되기 때문에 점수 매기기 스크립트 및 실행 환경을 제공할 필요가 없습니다.

Azure CLI 및 스튜디오 웹 [포털에서 일괄 처리 엔드포인트를 배포하고 사용하는](how-to-use-batch-endpoint.md) 방법을 알아봅니다. [](how-to-use-batch-endpoints-studio.md)

### <a name="managed-cost-with-autoscaling-compute"></a>자동 크기 조정 컴퓨팅으로 관리되는 비용

일괄 처리 엔드포인트를 호출하면 비동기 일괄 처리 유추 작업이 트리거됩니다. 컴퓨팅 리소스는 작업이 시작될 때 자동으로 프로비전되고 작업이 완료되면 자동으로 할당 취소됩니다. 따라서 컴퓨팅을 사용할 때만 비용을 지불합니다.

각 개별 일괄 처리 유추 작업에 대한 컴퓨팅 리소스 설정(예: 인스턴스 수) 및 고급 설정(예: 미니 일괄 처리 크기, 오류 임계값 등)을 [재정의하여](how-to-use-batch-endpoint.md#configure-the-output-location-and-overwrite-settings) 실행 속도를 높이고 비용을 절감할 수 있습니다.

### <a name="flexible-data-sources-and-storage"></a>유연한 데이터 원본 및 스토리지

일괄 처리 엔드포인트를 호출할 때 입력 데이터에 대해 다음 옵션을 사용할 수 있습니다.

- Azure Machine Learning 등록 데이터 세트 - 자세한 내용은 [Azure Machine Learning 데이터 세트 만들기](how-to-train-with-datasets.md)를 참조하세요.
- 클라우드 데이터 - 데이터 저장소의 공용 데이터 URI 또는 데이터 경로입니다. 자세한 내용은 [Azure Machine Learning 스튜디오를 사용하여 데이터에 연결](how-to-connect-data-ui.md)을 참조하세요.
- 로컬로 저장된 데이터

데이터 저장소 및 경로에 스토리지 출력 위치를 지정합니다. 기본적으로 일괄 처리 엔드포인트는 작업 이름(시스템 생성 GUID)별로 정리된 작업 영역의 기본 Blob 저장소에 출력을 저장합니다.

### <a name="security"></a>보안

- 인증: Azure Active Directory 토큰
- 엔드포인트 호출을 위한 기본 SSL

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 관리형 온라인 엔드포인트를 배포하는 방법](how-to-deploy-managed-online-endpoints.md)
- [Azure CLI 사용하여 일괄 처리 엔드포인트를 배포하는 방법](how-to-use-batch-endpoint.md)
- [스튜디오를 사용하여 관리형 온라인 엔드포인트를 배포하는 방법](how-to-use-managed-online-endpoint-studio.md)
- [REST를 사용하여 모델 배포(미리 보기)](how-to-deploy-with-rest.md)
- [관리형 온라인 엔드포인트를 모니터링하는 방법](how-to-monitor-online-endpoints.md)
- [온라인 엔드포인트 비용을 보는 방법](how-to-view-online-endpoints-costs.md)
- [Azure Machine Learning을 사용하여 리소스 할당량 관리 및 증가](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
