---
title: Azure Machine Learning 환경 정보
titleSuffix: Azure Machine Learning
description: 다양한 컴퓨팅 대상에 대해 재현, 감사, 이식이 가능한 기계 학습 종속성 정의를 사용할 수 있는 기계 학습 환경에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 11/16/2020
ms.openlocfilehash: 2364f0c029b57649ed56fa9ba93ed3978db2ca41
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531099"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 환경이란?

Azure Machine Learning 환경은 기계 학습이 이루어지는 환경을 캡슐화한 것입니다. 학습 및 채점 스크립트와 관련된 Python 패키지, 환경 변수, 소프트웨어 설정을 지정합니다. 또한 런타임(Python, Spark 또는 Docker)을 지정합니다. 환경은 Machine Learning 작업 영역 내에서 관리되고 버전이 지정된 엔터티로, 다양한 컴퓨팅 대상에 걸쳐 재현, 감사, 이식이 가능한 기계 학습 워크플로를 사용할 수 있습니다.

로컬 컴퓨팅에서 `Environment` 개체를 사용하여 다음을 수행할 수 있습니다.
* 학습 스크립트를 개발합니다.
* 대규모 모델 학습을 위한 Azure Machine Learning 컴퓨팅에서 동일한 환경을 재사용합니다.
* 동일한 환경을 사용하여 모델을 배포합니다.
* 기존 모델이 학습된 환경을 다시 확인합니다.

다음 다이어그램에서는 실행 구성(학습의 경우)과 추론 및 배포 구성(웹 서비스 배포의 경우)에서 하나의 `Environment` 개체를 사용하는 방법을 보여 줍니다.

![기계 학습 워크플로의 환경 다이어그램](./media/concept-environments/ml-environment.png)

환경, 컴퓨팅 대상과 학습 스크립트는 함께 실행 구성, 즉 교육 실행의 전체 사양을 구성합니다.

## <a name="types-of-environments"></a>환경 유형

환경은 크게 ‘큐레이팅’, ‘사용자 관리’, ‘시스템 관리’의 세 가지 카테고리로 나눌 수 있습니다.  

큐레이팅된 환경은 Azure Machine Learning에서 제공하며 기본적으로 작업 영역에서 사용할 수 있습니다. 큐레이팅된 환경은 있는 그대로 사용할 수 있도록 다양한 기계 학습 프레임워크를 시작하는 데 도움이 되는 Python 패키지 및 설정 컬렉션이 포함되어 있습니다. 이와 같이 미리 만들어진 환경에서는 배포 시간을 단축할 수 있습니다. 전체 목록은 [큐레이팅된 환경 문서](resource-curated-environments.md)를 참조하세요.

사용자 관리 환경에서는 사용자가 사용자 환경을 설정하고 컴퓨팅 대상에 학습 스크립트에 필요한 모든 패키지를 설치해야 합니다. Conda에서는 자동으로 환경을 확인하거나 항목을 설치하지 않습니다. 환경을 직접 정의하는 경우에는 버전 `>= 1.0.45`를 PIP 종속성으로 하여 `azureml-defaults`를 나열해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스트하는 데 필요한 기능이 포함되어 있습니다.

[Conda](https://conda.io/docs/)를 통해 Python 환경과 스크립트 종속성을 자동으로 관리하고자 하는 경우 시스템 관리 환경을 사용합니다. 새 Conda 환경은 Conda 종속성 개체를 기반으로 빌드됩니다. Azure Machine Learning Service는 수동으로 구성할 수 없는 원격 컴퓨팅 대상에 유용하기 때문에 기본적으로 이러한 유형의 환경을 가정합니다.

## <a name="create-and-manage-environments"></a>환경 만들기 및 관리

다음을 수행하여 환경을 만들 수 있습니다.

* 큐레이팅 환경을 사용하거나 직접 종속성을 정의하여 새 `Environment` 개체를 정의합니다.
* 작업 영역에서 기존 `Environment` 개체를 사용합니다. 이 접근 방식을 사용하면 종속성에 일관성과 재현성을 확보할 수 있습니다.
* 기존 Anaconda 환경 정의에서 가져옵니다.
* Azure Machine Learning CLI 사용
* [VS Code 확장 사용](how-to-manage-resources-vscode.md#create-environment)

특정 코드 샘플은 [환경 사용 방법](how-to-use-environments.md#create-an-environment)의 "환경 만들기" 섹션을 참조하세요. 또한 환경은 작업 영역을 통해 쉽게 관리할 수 있습니다. 환경에는 다음 기능이 포함됩니다.

* 환경은 실험 제출 시 작업 영역에 자동으로 등록됩니다. 수동으로 등록할 수도 있습니다.
* 작업 영역에서 학습 또는 배포에 사용할 환경을 가져오거나 환경 정의를 편집할 수 있습니다.
* 버전 관리를 사용하면 시간 경과에 따른 환경의 변경 내용을 확인할 수 있어 재현 가능성이 확보됩니다.
* 사용자 환경에서 자동으로 Docker 이미지를 빌드할 수 있습니다.

코드 샘플은 [환경을 사용하는 방법](how-to-use-environments.md#manage-environments)의 "환경 관리" 섹션을 참조하세요.

## <a name="environment-building-caching-and-reuse"></a>환경 빌드, 캐싱, 재사용

Azure Machine Learning Service는 환경 정의를 Docker 이미지 및 Conda 환경으로 빌드합니다. 또한 환경을 이후 학습 실행 및 서비스 엔드포인트 배포에서 재사용할 수 있도록 캐시합니다. 학습 스크립트를 원격으로 실행하려면 Docker 이미지를 만들어야 합니다. 반면 로컬 실행은 Conda 환경을 직접 사용할 수 있습니다. 

### <a name="submitting-a-run-using-an-environment"></a>환경을 사용하여 실행 제출

환경을 사용하여 원격 실행을 처음으로 제출하는 경우 Azure Machine Learning Service는 작업 영역과 연결된 ACR(Azure Container Registry)에서 [ACR 빌드 작업](../container-registry/container-registry-tasks-overview.md)을 호출합니다. 그러면 빌드된 Docker 이미지가 작업 영역 ACR에 캐시됩니다. 큐레이팅된 환경에서는 전체 ACR에 캐시된 Docker 이미지가 지원됩니다. 실행 시작 시 컴퓨팅 대상이 관련 ACR에서 이미지를 검색합니다.

로컬 실행의 경우 환경 정의에 따라 Docker 또는 Conda 환경이 생성됩니다. 그러면 대상 컴퓨팅(로컬 런타임 환경 또는 로컬 Docker 엔진)에서 스크립트가 실행됩니다.

### <a name="building-environments-as-docker-images"></a>Docker 이미지로 환경 빌드

작업 영역 ACR에 환경 정의가 아직 없는 경우 새 이미지가 빌드됩니다. 이미지 빌드는 두 단계로 구성됩니다.

 1. 기본 이미지 다운로드 및 Docker 단계 실행
 2. 환경 정의에 지정된 Conda 종속성에 따라 Conda 환경 빌드

[사용자 관리 종속성](/python/api/azureml-core/azureml.core.environment.pythonsection)을 지정하면 두 번째 단계가 생략됩니다. 이 경우 기본 이미지에 포함하거나 첫 번째 단계에서 사용자 지정 Docker 단계를 지정하여 Python 패키지를 설치해야 합니다. 또한 Python 실행 파일에 올바른 위치를 지정해야 합니다. [사용자 지정 Docker 기본 이미지](./how-to-deploy-custom-container.md)를 사용할 수도 있습니다.

### <a name="image-caching-and-reuse"></a>이미지 캐싱 및 재사용

다른 실행에 동일한 환경 정의를 사용하는 경우 Azure Machine Learning Service는 작업 영역 ACR에서 캐시된 이미지를 재사용합니다. 

캐시된 이미지의 세부 정보를 보려면 [Environment.get_image_details](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-) 메서드를 사용합니다.

캐시된 이미지를 재사용할지, 새 이미지를 빌드할지 여부를 결정하기 위해, 서비스는 환경 정의에서 [해시 값](https://en.wikipedia.org/wiki/Hash_table)을 계산하고 이를 기존 환경의 해시와 비교합니다. 해시는 다음을 기반으로 합니다.
 
 * 기본 이미지 속성 값
 * 사용자 지정 Docker 단계 속성 값
 * Conda 정의에 있는 Python 패키지 목록
 * Spark 정의에 있는 패키지 목록 

해시는 환경 이름 또는 버전에 종속되지 않습니다. 환경 이름을 바꾸거나 기존 환경과 일치하는 속성 및 패키지로 새 환경을 만드는 경우 해시 값은 동일하게 유지됩니다. 그러나 Python 패키지를 추가 또는 제거하거나 패키지 버전을 변경하는 등 환경 정의를 변경하면 해시 값이 변경됩니다. 환경에서 종속성 또는 채널의 순서를 변경하면 새로운 환경이 생성되고 새로운 이미지 빌드가 필요합니다. 큐레이팅된 환경을 변경하면 해시가 무효화되고 새 ‘비 큐레이팅’ 환경이 생성됩니다.

계산된 해시 값은 작업 영역 및 전체 ACR(또는 로컬 실행의 경우 컴퓨팅 대상)의 해시 값과 비교됩니다. 일치하는 값이 있으면 캐시된 이미지를 가져오고, 그렇지 않으면 이미지 빌드가 트리거됩니다. 캐시된 이미지를 끌어오는 시간에는 다운로드 시간이 포함되지만, 새로 빌드된 이미지를 끌어오는 시간에는 빌드 시간과 다운로드 시간이 모두 포함됩니다. 

다음 다이어그램은 세 가지 환경 정의를 보여 줍니다. 두 환경의 이름 및 버전은 다르지만 기본 이미지 및 Python 패키지는 동일합니다. 하지만 해시가 동일하기 때문에 동일한 캐시된 이미지에 해당합니다. 세 번째 환경의 Python 패키지와 버전은 다르므로, 다른 캐시된 이미지에 해당합니다.

![Docker 이미지로 환경 캐싱을 보여주는 다이어그램](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> * 고정되지 않은 패키지 종속성이 있는 환경을 만드는 경우(예: `numpy`) 환경은 *환경이 만들어질 때 설치된* 패키지 버전을 사용합니다. 또한 일치하는 정의를 사용하는 모든 향후 환경은 원래 버전을 사용합니다. 
>
>   패키지를 업데이트하려면 이미지를 다시 빌드하려는 버전 번호(예: `numpy==1.18.1`)를 지정합니다. 중첩된 종속성 포함한 새 종속성이 설치되어 이전에 작업 중인 시나리오가 중단될 수 있습니다.
>
> * 환경 정의에서 `mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04`와 같은 고정되지 않은 기본 이미지를 사용하면 최신 태그가 업데이트될 때마다 환경이 다시 빌드됩니다. 취약성, 시스템 업데이트 및 패치와 같은 다양한 이유로 최신 버전을 유지하려는 경우를 가정합니다. 

> [!WARNING]
>  [Environment.build](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) 메서드는 캐시된 이미지를 다시 빌드합니다. 이 경우 캐시된 이미지에 해당하는 모든 환경 종속성에 대해 고정 해제된 패키지가 업데이트되고 재현 가능성을 중단하는 부작용이 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Machine Learning에서 [환경을 만들고 사용](how-to-use-environments.md)하는 방법을 알아봅니다.
* [환경 클래스](/python/api/azureml-core/azureml.core.environment%28class%29)에 대한 Python SDK 참조 문서를 참조하세요.
