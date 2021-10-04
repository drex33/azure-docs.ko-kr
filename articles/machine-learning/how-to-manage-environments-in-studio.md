---
title: Studio에서 환경 관리
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오에서 환경을 만들고 관리하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 5/25/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: afa72add9b4fe6014b03331e8ee520669edb29b1
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426059"
---
# <a name="manage-software-environments-in-azure-machine-learning-studio"></a>Azure Machine Learning studio에서 소프트웨어 환경 관리

이 문서에서는 Azure Machine Learning 스튜디오에서 Azure Machine Learning [환경](/python/api/azureml-core/azureml.core.environment.environment)을 만들고 관리하는 방법에 대해 알아봅니다. 환경을 사용하면 프로젝트의 소프트웨어 종속성이 진화함에 따라 추적하고 재현할 수 있습니다.

이 문서의 예제는 다음 방법을 보여 줍니다.

* 큐레이팅된 환경을 찾아봅니다.
* 환경을 만들고 패키지 종속성을 지정합니다.
* 기존 환경 사양과 해당 속성을 편집합니다.
* 환경을 다시 빌드하고 이미지 빌드 로그를 봅니다.

Azure Machine Learning에서 환경이 작동하는 방식에 대한 개요는 [ML 환경이란?](concept-environments.md)을 참조하세요. 자세한 내용은 [Azure Machine Learning에 대한 개발 환경 설정 방법](how-to-configure-environment.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

## <a name="browse-curated-environments"></a>큐레이팅된 환경 찾아보기

큐레이팅된 환경에는 Python 패키지 컬렉션이 포함되며, 기본적으로 작업 영역에서 사용할 수 있습니다. 이러한 환경은 캐시 된 Docker 이미지로 지원 되므로 실행 준비 비용 및 지원 교육 및 추론 시나리오를 줄일 수 있습니다. 

환경을 클릭하여 해당 콘텐츠에 대한 자세한 정보를 확인합니다. 자세한 내용은 [Azure Machine Learning 큐레이팅된 환경](resource-curated-environments.md)을 참조하세요. 

## <a name="create-an-environment"></a>환경 만들기

환경을 만들려면 다음을 수행합니다.
1. [Azure Machine Learning 스튜디오](https://ml.azure.com)에서 작업 영역 열기
1. 왼쪽에서 **환경** 을 선택합니다.
1. **사용자 지정 환경** 탭을 선택합니다. 
1. **생성** 단추를 선택합니다. 

다음 중 하나를 지정하여 환경을 만듭니다.
* Pip 요구 사항 [파일](https://pip.pypa.io/en/stable/cli/pip_install)
* Conda yaml [파일](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)
* Docker [이미지](https://hub.docker.com/search?q=&type=image)
* [Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

:::image type="content" source="media/how-to-manage-environments-in-studio/create-page.jpg" alt-text="환경 만들기 마법사":::

엔터티를 만들기 전에 구성 파일을 사용자 지정하고, 태그와 설명을 추가하며, 속성을 검토할 수 있습니다. 

새 환경에 작업 영역의 기존 환경과 동일한 이름을 지정하면 새 버전의 기존 환경이 만들어집니다.

## <a name="view-and-edit-environment-details"></a>환경 세부 정보 보기 및 편집

환경이 만들어지면 이름을 클릭하여 세부 정보를 봅니다. 드롭다운 메뉴를 사용하여 다른 버전의 환경을 선택할 수 있습니다. 여기에서 Docker 및 Conda 계층을 통해 환경의 메타데이터와 콘텐츠를 볼 수 있습니다. 

연필 아이콘을 클릭하여 태그 및 설명과 구성 파일 또는 이미지를 편집합니다. Docker 또는 Conda 섹션을 변경하면 새 버전의 환경이 만들어진다는 점에 유의하세요. 

:::image type="content" source="media/how-to-manage-environments-in-studio/details-page.jpg" alt-text="환경 세부 정보 페이지":::

## <a name="view-image-build-logs"></a>이미지 빌드 로그 보기

세부 정보 페이지 내의 **빌드 로그** 탭을 클릭하여 환경 버전의 이미지 빌드 로그를 봅니다. 

## <a name="rebuild-an-environment"></a>환경 다시 빌드

세부 정보 페이지에서 **다시 빌드** 단추를 클릭하여 환경을 다시 빌드합니다. 이 작업을 사용하여 구성 파일에 고정되지 않은 패키지 버전을 최신 버전으로 업데이트할 수 있습니다. 
