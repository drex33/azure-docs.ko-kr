---
title: Azure Machine Learning Studio란?
description: 스튜디오는 Azure Machine Learning 작업 영역에 대한 웹 포털입니다. 스튜디오는 포괄적인 데이터 과학 플랫폼을 위해 코드 없는 환경과 코드 우선 환경을 결합합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/23/2021
adobe-target: true
ms.openlocfilehash: d2b3a18341c782cf848ebd8740577004340be0f8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351883"
---
# <a name="what-is-azure-machine-learning-studio"></a>Azure Machine Learning Studio란?

이 문서에서는 [Azure Machine Learning](overview-what-is-azure-machine-learning.md)의 데이터 과학자 개발자를 위한 웹 포털인 Azure Machine Learning 스튜디오에 대해 알아봅니다. 스튜디오는 포괄적인 데이터 과학 플랫폼을 위해 코드 없는 환경과 코드 우선 환경을 결합합니다.

이 문서에서는 다음에 대해 알아봅니다.
>[!div class="checklist"]
> - 스튜디오에서 [기계 학습 프로젝트를 작성](#author-machine-learning-projects)하는 방법.
> - 스튜디오에서 [자산 및 리소스를 관리](#manage-assets-and-resources)하는 방법.
> - [Azure Machine Learning 스튜디오와 ML Studio(클래식)](#ml-studio-classic-vs-azure-machine-learning-studio) 간의 차이점.

운영 체제와 호환되는 가장 최신 브라우저를 사용하는 것이 좋습니다. 다음과 같은 브라우저가 지원됩니다.
  * Microsoft Edge(최신 버전)
  * Safari(최신 버전, Mac만 해당)
  * Chrome(최신 버전)
  * Firefox(최신 버전)

## <a name="author-machine-learning-projects"></a>기계 학습 프로젝트 작성

스튜디오는 형식 프로젝트와 사용자 환경 수준에 따라 여러 제작 환경을 제공합니다.

+ **Notebooks**

  스튜디오에 직접 통합된 관리되는 [Jupyter Notebook 서버](how-to-run-jupyter-notebooks.md)에서 직접 코드를 작성하고 실행합니다. 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="스크린샷: Notebook에서 코드 작성 및 실행":::

+ **Azure Machine Learning 디자이너**

  디자이너를 사용하여 코드를 작성하지 않고도 기계 학습 모델을 학습하고 배포할 수 있습니다. 데이터 세트 및 모듈을 끌어서 놓아 ML 파이프라인을 만듭니다. [디자이너 자습서](tutorial-designer-automobile-price-train-score.md)를 사용해 보세요.

    ![Azure Machine Learning 디자이너 예](media/concept-designer/designer-drag-and-drop.gif)

+ **자동화된 기계 학습 UI**

  사용하기 쉬운 인터페이스로 [자동화된 ML 실험](tutorial-first-experiment-automated-ml.md)을 만드는 방법을 알아보세요. 

  ![Azure Machine Learning 스튜디오 탐색 창의 AutoML](./media/overview-what-is-azure-ml-studio/azure-machine-learning-automated-ml-ui.jpg)

+ **데이터 레이블 지정**

    Azure Machine Learning 데이터 레이블 지정을 사용하여 [이미지 레이블 지정](how-to-create-image-labeling-projects.md) 또는 [텍스트 레이블 지정](how-to-create-text-labeling-projects.md) 프로젝트를 효율적으로 조정합니다.

## <a name="manage-assets-and-resources"></a>자산 및 리소스 관리

브라우저에서 직접 기계 학습 자산을 관리합니다. 원활한 환경을 위해 SDK와 스튜디오 간에 자산이 동일한 작업 영역에서 공유됩니다. 스튜디오를 사용하여 다음을 관리합니다.

- 모델
- 데이터 세트
- 데이터 저장소
- Compute 리소스
- Notebooks
- 실험
- 실행 로그
- Pipelines 
- 파이프라인 엔드포인트

숙련된 개발자라 하더라도 스튜디오는 작업 영역 리소스를 관리하는 방법을 단순화할 수 있습니다.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio(클래식) 및 Azure Machine Learning 스튜디오

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

2015년에 릴리스된 **ML Studio(클래식)** 는 Azure의 최초 끌어서 놓기 기계 학습 모델 작성기입니다. **ML Studio(클래식)** 는 시각적 환경만 제공하는 독립 실행형 서비스입니다. Studio(클래식)는 Azure Machine Learning과 상호 운용되지 않습니다.

**Azure Machine Learning** 은 완전한 데이터 과학 플랫폼을 제공하는 별도의 현대화된 서비스입니다. 코드 우선 및 낮은 코드 환경을 모두 지원합니다.

**Azure Machine Learning 스튜디오** 는 프로젝트 작성 및 자산 관리를 위한 낮은 코드와 코드 없음 옵션이 포함된 Azure Machine Learning *의* 웹 포털입니다. 

신규 사용자인 경우 ML Studio(클래식) 대신 **Azure Machine Learning** 을 선택합니다. 완전한 ML 플랫폼인 Azure Machine Learning은 다음을 제공합니다.

- 대규모 학습을 위한 확장 가능한 컴퓨팅 클러스터.
- 엔터프라이즈 보안 및 거버넌스.
- 인기 있는 오픈 소스 도구와 상호 운용할 수 있습니다.
- 엔드투엔드 MLOps.

### <a name="feature-comparison"></a>기능 비교

[!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

## <a name="troubleshooting"></a>문제 해결

* **스튜디오에서 누락된 사용자 인터페이스 항목** Azure 역할 기반 액세스 제어를 사용하여 Azure Machine Learning으로 수행할 수 있는 작업을 제한할 수 있습니다. 이러한 제한으로 인해 사용자 인터페이스 항목이 Azure Machine Learning 스튜디오에 나타나지 않을 수 있습니다. 예를 들어 컴퓨팅 인스턴스를 만들 수 없는 역할이 할당된 경우 컴퓨팅 인스턴스를 만드는 옵션은 스튜디오에 표시되지 않습니다. 자세한 내용은 [사용자 및 역할 관리](how-to-assign-roles.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[스튜디오](https://ml.azure.com)를 방문하거나 다음 자습서를 통해 다양한 제작 옵션을 살펴보세요.  

[빠른 시작: Azure Machine Learning 서비스 시작](quickstart-create-resources.md)으로 시작합니다.  그런 다음 이러한 리소스를 사용하여 원하는 방법으로 첫 번째 실험을 만듭니다.

  + ["Hello World!" Python 스크립트 실행(1/3부)](tutorial-1st-experiment-hello-world.md)
  + [Jupyter Notebook을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)
  + [자동화된 기계 학습을 사용하여 모델 학습 및 배포](tutorial-first-experiment-automated-ml.md)  
  + [디자이너를 사용하여 모델 학습 및 배포](tutorial-designer-automobile-price-train-score.md)
  + [보안 가상 네트워크에서 스튜디오 사용](how-to-enable-studio-virtual-network.md)