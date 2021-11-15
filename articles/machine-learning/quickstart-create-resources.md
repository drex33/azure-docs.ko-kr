---
title: '빠른 시작: 작업 영역 리소스 만들기'
titleSuffix: Azure Machine Learning
description: 기계 학습 모델을 학습하는 데 사용할 수 있는 Azure Machine Learning 작업 영역 및 클라우드 리소스를 만듭니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 10/21/2021
adobe-target: true
ms.custom: FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 749400cc3f5c798b16dc08485f0cbff14cb276e7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556950"
---
# <a name="quickstart-create-workspace-resources-you-need-to-get-started-with-azure-machine-learning"></a>빠른 시작: Azure Machine Learning을 시작하는 데 필요한 작업 영역 리소스 만들기

이 빠른 시작에서는 작업 영역을 만든 다음, 작업 영역에 컴퓨팅 리소스를 추가합니다. 그러면 Azure Machine Learning을 시작하기 위해 필요한 모든 것을 갖게 됩니다.  

작업 영역은 기계 학습의 최상위 리소스로, Azure Machine Learning을 사용할 때 만든 아티팩트를 보고 관리할 수 있는 중앙 집중식 환경을 제공합니다. 컴퓨팅 리소스는 기계 학습 모델을 학습, 배포, 자동화, 관리 및 추적하는 데 사용할 수 있는 사전 구성된 클라우드 기반 환경을 제공합니다.


## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-workspace"></a>작업 영역 만들기

이미 작업 영역이 있는 경우 이 섹션을 건너뛰고 [컴퓨팅 인스턴스 만들기](#instance)를 계속합니다.

아직 작업 영역이 없으면 지금 만듭니다.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="create-compute-instance"></a><a name="instance"></a> 컴퓨팅 인스턴스 만들기

자신의 컴퓨터에 Azure Machine Learning을 설치할 수 있습니다.  그러나 이 빠른 시작에서는 이미 설치되어 바로 사용 가능한 개발 환경이 있는 온라인 컴퓨팅 리소스를 만들게 됩니다.  개발 환경에서 Python 스크립트 및 Jupyter Notebook의 코드를 작성하고 실행하는 데 도움이 되는 이 온라인 컴퓨터, 즉 *컴퓨팅 인스턴스* 를 사용합니다.

나머지 자습서 및 빠른 시작을 위해 *컴퓨팅 인스턴스* 를 만들어 이 개발 환경을 사용합니다.

1. 이전 섹션에서 **작업 영역으로 가기** 를 선택하지 않은 경우 [Azure Machine Learning 스튜디오](https://ml.azure.com)에 로그인하고 작업 영역을 선택합니다.
1. 왼쪽에서 **컴퓨팅** 을 선택합니다.
1. 새 컴퓨팅 인스턴스를 만들려면 **+ 새로 만들기** 를 선택합니다.
1. 이름을 제공하고 첫 페이지의 모든 기본값을 유지합니다.
1. **만들기** 를 선택합니다.
 
약 2분 후에 컴퓨팅 인스턴스 변경의 **상태** 가 *만드는 중* 에서 *실행 중* 으로 표시됩니다.  이제 모든 준비가 되었습니다.  

## <a name="create-compute-clusters"></a><a name="cluster"></a> 컴퓨팅 클러스터 만들기

다음으로 컴퓨팅 클러스터를 만듭니다.  클러스터를 사용하면 클라우드의 CPU 또는 GPU 컴퓨팅 노드의 클러스터에 학습 또는 일괄 처리 추론 프로세스를 배포할 수 있습니다.

자동으로 노드 수를 0과 4 사이에서 조정할 컴퓨팅 클러스터를 만듭니다.

1. **컴퓨팅** 섹션의 맨 위 탭에서 **컴퓨팅 클러스터** 를 선택합니다.
1. 새 컴퓨팅 클러스터를 만들려면 **+ 새로 만들기** 를 선택합니다.
1. 첫 번째 페이지의 모든 기본값을 유지하고 **다음** 을 선택합니다.
1. 클러스터 이름을 **cpu-cluster** 로 지정합니다.  이 이름이 이미 존재하는 경우 이니셜을 클러스터 이름에 추가하여 고유하게 만들 수 있습니다.
1. **최소 노드 수** 를 0으로 둡니다.
1. 가능한 경우 **최대 노드 수** 를 4로 변경합니다.  설정에 따라 한도가 더 작을 수 있습니다.
1. **스케일 다운 전 유휴 시간(초)** 을 2400으로 변경합니다.
1. 나머지는 기본값으로 두고, **만들기** 를 선택합니다.

1분 이내에 클러스터의 **상태** 가 *만드는 중* 에서 *성공함* 으로 바뀌었습니다.  이 목록은 유휴 노드, 사용 중인 노드 및 프로비저닝되지 않은 노드의 수와 함께 프로비저닝된 컴퓨팅 클러스터를 표시합니다.  아직 클러스터를 사용하지 않았으므로 현재 모든 노드가 프로비저닝되지 않습니다. 

> [!NOTE]
> 클러스터가 만들어지면 0개의 노드가 프로비저닝됩니다. 작업을 제출할 때까지 클러스터에 대한 비용이 *발생하지 않습니다*. 이 클러스터가 2,400초(40분) 동안 유휴 상태이면 축소됩니다.  이렇게 하면 다시 스케일 업하기까지 기다리지 않고 필요에 따라 몇 가지 자습서에서 사용해 볼 수 있습니다.

## <a name="quick-tour-of-the-studio"></a><a name="studio"></a> 스튜디오 빠른 둘러보기

스튜디오는 Azure Machine Learning의 웹 포털입니다. 이 포털은 포괄적인 데이터 과학 플랫폼을 위해 코드 없는 환경과 코드 우선 환경을 결합합니다.

왼쪽 탐색 모음에서 스튜디오 부분을 검토합니다.

* 스튜디오의 **작성자** 섹션에는 기계 학습 모델을 만들기 시작하는 여러 가지 방법이 있습니다.  다음을 할 수 있습니다.

    * **Notebooks** 섹션을 사용하면 Jupyter Notebook을 만들고, 샘플 Notebook을 복사하고, Notebook 및 Python 스크립트를 실행할 수 있습니다.
    * **자동화된 ML** 은 코드를 작성하지 않고 기계 학습 모델을 단계적으로 만듭니다.
    * **디자이너** 는 사전에 빌드된 구성 요소를 사용하여 모델을 빌드하는 끌어서 놓기 기능을 제공합니다.

* 스튜디오의 **자산** 섹션은 작업을 실행할 때 만드는 자산을 추적하는 데 도움이 됩니다.  새 작업 영역이 있는 경우 이러한 섹션에는 아직 아무것도 없습니다.

* 이미 스튜디오의 **관리** 섹션을 사용하여 컴퓨팅 리소스를 만들었습니다.  또한 이 섹션에서는 작업 영역에 연결하는 데이터 및 외부 서비스를 만들고 관리할 수 있습니다.  

### <a name="workspace-diagnostics"></a>작업 영역 진단

[!INCLUDE [machine-learning-workspace-diagnostics](../../includes/machine-learning-workspace-diagnostics.md)]

## <a name="clean-up-resources"></a><a name="clean-up"></a>리소스 정리

다음 자습서로 계속하려면 [다음 단계](#next-steps)로 건너뜁니다.

### <a name="stop-compute-instance"></a>컴퓨팅 인스턴스 중지

지금 사용하지 않을 경우 컴퓨팅 인스턴스를 중지합니다.

1. 스튜디오의 왼쪽에서 **컴퓨팅** 을 선택합니다.
1. 위쪽 탭에서 **컴퓨팅 인스턴스** 를 선택합니다.
1. 목록에서 컴퓨팅 인스턴스를 선택합니다.
1. 맨 위의 도구 모음에서 **중지** 를 선택합니다.

### <a name="delete-all-resources"></a>모든 리소스 삭제

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이제 다음을 포함하는 Azure Machine Learning 작업 영역이 있습니다.

- 개발 환경에 사용할 컴퓨팅 인스턴스입니다.
- 학습 실행을 제출하는 데 사용할 컴퓨팅 클러스터입니다.

이 리소스를 사용하여 Azure Machine Learning 및 Python 스크립트가 있는 모델을 학습시키는 방법에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Python 스크립트를 사용하여 자세히 알아보기](tutorial-1st-experiment-hello-world.md)
>
