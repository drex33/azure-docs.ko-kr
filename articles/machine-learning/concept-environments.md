---
title: Azure Machine Learning 환경 정보
titleSuffix: Azure Machine Learning
description: 다양한 컴퓨팅 대상에 대해 재현, 감사, 이식이 가능한 기계 학습 종속성 정의를 사용할 수 있는 기계 학습 환경에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 09/23/2021
ms.openlocfilehash: aa1bc31118770842d8f935669be2f4467aa06ba4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468672"
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

사용자 관리 환경에서는 사용자가 사용자 환경을 설정하고 컴퓨팅 대상에 학습 스크립트에 필요한 모든 패키지를 설치해야 합니다. 또한 모델 배포에 필요한 종속성을 포함 해야 합니다.

[Conda](https://conda.io/docs/) 에서 Python 환경을 관리 하려면 시스템 관리 환경을 사용 합니다. 새 conda 환경은 기본 docker 이미지 위에 있는 conda 사양에서 구체화 됩니다.

## <a name="create-and-manage-environments"></a>환경 만들기 및 관리

AzureML Python SDK, Azure Machine Learning CLI, Azure Machine Learning studio의 환경 페이지 및 [VS Code 확장](how-to-manage-resources-vscode.md#create-environment)과 같은 클라이언트에서 환경을 만들 수 있습니다. 필요한 경우 모든 클라이언트에서 기본 이미지, Dockerfile 및 Python 계층을 사용자 지정할 수 있습니다.

특정 코드 샘플은 [환경 사용 방법](how-to-use-environments.md#create-an-environment)의 "환경 만들기" 섹션을 참조하세요. 

환경도 작업 영역을 통해 쉽게 관리할 수 있으며 다음을 수행할 수 있습니다.

* 환경을 등록 합니다.
* 작업 영역에서 교육 또는 배포에 사용할 환경을 가져옵니다.
* 기존 환경의 새 인스턴스를 편집 하 여 새 인스턴스를 만듭니다.
* 시간이 지남에 따라 환경에 대 한 변경 내용을 확인 하 여 재현 가능성 보장 합니다.
* 사용자 환경에서 자동으로 Docker 이미지를 빌드합니다.

"익명" 환경은 실험을 제출할 때 작업 영역에 자동으로 등록 됩니다. 이러한 기능은 나열 되지 않지만 버전에서 검색할 수 있습니다.

코드 샘플은 [환경을 사용하는 방법](how-to-use-environments.md#manage-environments)의 "환경 관리" 섹션을 참조하세요.

## <a name="environment-building-caching-and-reuse"></a>환경 빌드, 캐싱, 재사용

Azure Machine Learning는 환경 정의를 Docker 이미지 및 conda 환경으로 작성 합니다. 또한 환경을 이후 학습 실행 및 서비스 엔드포인트 배포에서 재사용할 수 있도록 캐시합니다. 학습 스크립트를 원격으로 실행 하려면 Docker 이미지를 만들어야 하지만 로컬 실행은 conda 환경을 직접 사용할 수 있습니다. 

### <a name="submitting-a-run-using-an-environment"></a>환경을 사용하여 실행 제출

환경을 사용하여 원격 실행을 처음으로 제출하는 경우 Azure Machine Learning Service는 작업 영역과 연결된 ACR(Azure Container Registry)에서 [ACR 빌드 작업](../container-registry/container-registry-tasks-overview.md)을 호출합니다. 그러면 빌드된 Docker 이미지가 작업 영역 ACR에 캐시됩니다. 큐레이팅된 환경에서는 전체 ACR에 캐시된 Docker 이미지가 지원됩니다. 실행 시작 시 컴퓨팅 대상이 관련 ACR에서 이미지를 검색합니다.

로컬 실행의 경우 환경 정의에 따라 Docker 또는 conda 환경이 만들어집니다. 그러면 대상 컴퓨팅(로컬 런타임 환경 또는 로컬 Docker 엔진)에서 스크립트가 실행됩니다.

### <a name="building-environments-as-docker-images"></a>Docker 이미지로 환경 빌드

특정 환경 정의의 이미지가 작업 영역 ACR에 아직 없는 경우 새 이미지가 빌드됩니다. 이미지 빌드는 두 단계로 구성됩니다.

 1. 기본 이미지 다운로드 및 Docker 단계 실행
 2. 환경 정의에 지정된 Conda 종속성에 따라 Conda 환경 빌드

[사용자 관리 종속성](/python/api/azureml-core/azureml.core.environment.pythonsection)을 지정하면 두 번째 단계가 생략됩니다. 이 경우 기본 이미지에 포함하거나 첫 번째 단계에서 사용자 지정 Docker 단계를 지정하여 Python 패키지를 설치해야 합니다. 또한 Python 실행 파일에 올바른 위치를 지정해야 합니다. [사용자 지정 Docker 기본 이미지](./how-to-deploy-custom-container.md)를 사용할 수도 있습니다.

### <a name="image-caching-and-reuse"></a>이미지 캐싱 및 재사용

다른 실행에 동일한 환경 정의를 사용 하는 경우 Azure Machine Learning 작업 영역 ACR에서 캐시 된 이미지를 재사용 하 여 시간을 절약 합니다.

캐시 된 이미지의 세부 정보를 보려면 Azure Machine Learning studio에서 환경 페이지를 확인 하거나 메서드를 사용 합니다 [`Environment.get_image_details`](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-) .

캐시 된 이미지를 다시 사용 하거나 새 이미지를 빌드 할지 여부를 결정 하기 위해 AzureML는 환경 정의에서 [해시 값](https://en.wikipedia.org/wiki/Hash_table) 을 계산 하 고이를 기존 환경의 해시와 비교 합니다. 해시는 환경 정의를 기반으로 합니다.
 
 * Base image
 * 사용자 지정 docker 단계
 * Python 패키지
 * Spark 패키지

해시는 환경 이름 또는 버전의 영향을 받지 않습니다. 환경의 이름을 바꾸거나 다른 환경과 동일한 설정 및 패키지를 사용 하 여 새 환경을 만드는 경우 해시 값은 동일 하 게 유지 됩니다. 그러나 Python 패키지를 추가 또는 제거 하거나 패키지 버전을 변경 하는 것과 같은 환경 정의 변경으로 인해 결과 해시 값이 변경 됩니다. 환경에서 종속성 또는 채널의 순서를 변경 하면 해시가 변경 되며 새 이미지 빌드도 필요 합니다. 마찬가지로 큐 레이트 환경을 변경 하면 새 "비 큐 레이트" 환경이 생성 됩니다. 

> [!NOTE]
> 환경 이름을 변경 하지 않고 큐 레이트 환경에 대 한 로컬 변경 내용을 제출할 수 없습니다. "AzureML-" 및 "Microsoft" 접두사는 큐 레이트 환경 전용으로 예약 되어 있으며 이름이 둘 중 하나로 시작 하는 경우 작업 제출에 실패 합니다.

환경의 계산 된 해시 값은 작업 영역 및 전역 ACR 또는 계산 대상 (로컬 실행에만 해당)의 계산 된 해시 값과 비교 됩니다. 일치 하는 항목이 있으면 캐시 된 이미지를 가져오고 사용 합니다. 그렇지 않으면 이미지 빌드가 트리거됩니다.

다음 다이어그램은 세 가지 환경 정의를 보여 줍니다. 그 중 두 가지에는 서로 다른 이름과 버전이 있지만 동일한 기본 이미지와 Python 패키지가 있습니다. 그러면 동일한 해시 및 해당 캐시 된 이미지가 생성 됩니다. 세 번째 환경에는 다양 한 Python 패키지와 버전이 있으므로 다른 해시 및 캐시 된 이미지를 포함 합니다.

![환경 캐싱 및 Docker 이미지 다이어그램](./media/concept-environments/environment-caching.png)

작업 영역 ACR의 실제 캐시 된 이미지는 끝에 해시가 표시 되는 것과 같은 이름을 갖습니다 `azureml/azureml_e9607b2514b066c851012848913ba19f` .

>[!IMPORTANT]
> * 고정 해제 된 패키지 종속성 (예:)을 사용 하 여 환경을 만드는 경우 환경 `numpy` 에서 환경을 *만들 때 사용할 수* 있었던 패키지 버전을 사용 합니다. 일치 하는 정의를 사용 하는 모든 향후 환경에서는 원래 버전을 사용 합니다. 
>
>   패키지를 업데이트 하려면 이미지를 다시 작성 하도록 버전 번호를 지정 합니다. 이에 대 한 예는로 변경 됩니다 `numpy` `numpy==1.18.1` . 중첩 된 항목을 포함 한 새 종속성이 설치 되며 이전에 작동 하 던 시나리오가 손상 될 수 있습니다.
>
> * 환경 정의에서와 같이 고정 해제 된 기본 이미지를 사용 하면 `mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04` 태그가 업데이트 될 때마다 이미지가 다시 작성 `latest` 됩니다. 이를 통해 이미지는 최신 패치와 시스템 업데이트를 수신할 수 있습니다.

> [!WARNING]
>  이 [`Environment.build`](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) 메서드는 고정 되지 않은 패키지를 업데이트 하 고 해당 캐시 된 이미지에 해당 하는 모든 환경 정의에 대해 재현 가능성를 중단 시킬 수 있는 부작용으로 캐시 된 이미지를 다시 빌드합니다.

## <a name="next-steps"></a>다음 단계

* Azure Machine Learning에서 [환경을 만들고 사용](how-to-use-environments.md)하는 방법을 알아봅니다.
* [환경 클래스](/python/api/azureml-core/azureml.core.environment%28class%29)에 대한 Python SDK 참조 문서를 참조하세요.
