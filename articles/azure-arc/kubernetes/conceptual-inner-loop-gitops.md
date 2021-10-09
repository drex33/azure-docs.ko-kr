---
title: GitOps를 채택한 팀의 내부 루프 개발자 경험
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: conceptual
author: sudivate
ms.author: sudivate
description: '이 문서에서는 GitOps를 채택한 팀의 내부 루프 개발자 경험에 대한 개념적인 개요를 제공합니다. '
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, 컨테이너, CI, CD, Azure DevOps, 내부 루프, 개발 경험
ms.openlocfilehash: 3bd60c3236bb187f14b924f4246d8e6fcfed4ed3
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706416"
---
# <a name="inner-loop-developer-experience-for-teams-adopting-gitops"></a>GitOps를 채택한 팀의 내부 루프 개발자 경험

이 문서에서는 설정된 내부 루프가 개발자 생산성을 어떻게 향상시켜줄 수 있고 GitOps를 채택한 팀이 내부 개발 루프에서 외부 루프로 매끄럽게 전환하는 데 어떤 도움을 주는지에 대해 설명합니다.

## <a name="inner-dev-loop-frameworks"></a>내부 개발 루프 프레임워크

컨테이너 빌드 및 배포는 내부 개발 경험을 느리게 하고 팀 생산성에 영향을 줄 수 있습니다. 클라우드 네이티브 개발 팀은 강력한 내부 개발 루프 프레임워크의 이점을 활용할 수 있습니다. 내부 개발 루프 프레임워크는 반복되는 코드 작성, 빌드, 디버깅 프로세스에 도움이 됩니다.

내부 개발 루프 프레임워크 기능에는 다음이 포함됩니다.

 
- 코드 작성, 컨테이너, 대상 클러스터에 배포와 같은 반복되는 단계를 자동화합니다. 
- 원격 및 로컬 클러스터를 쉽게 사용하고 하이브리드 설정을 위한 로컬 터널 디버깅을 지원합니다.
- 팀 기반 생산성을 위해 사용자 정의 흐름을 구성합니다.
- 마이크로 서비스 종속성 처리를 허용합니다. 
- 핫 다시 로드, 포트 전달, 로그 및 터미널 액세스를 지원합니다. 



서비스의 성숙도 및 복잡성에 따라 개발 팀은 내부 개발 루프를 가속화하기 위해 사용할 클러스터 설정을 결정합니다. 

* 완전 로컬
* 완전 원격
* 하이브리드 


다행스럽게도 나열된 기능을 지원하는 많은 프레임워크가 존재합니다. Microsoft는 로컬 터널 디버깅을 위해 Bridge to Kubernetes를 제공하며, DevSpace, Scaffold, Tilt와 같은 유사한 서비스도 시장에 출시되어 있습니다.

> [!NOTE]
> 시장에 출시된 다른 서비스인 [DevSpace](https://github.com/loft-sh/devspace)를 Microsoft의 이전 DevSpace(지금은 [Bridge to Kubernetes](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes))와 혼동하지 마세요.


## <a name="inner-loop-to-outer-loop-transition"></a>내부 루프에서 외부 루프로 전환 

내부 루프 개발 프레임워크를 평가하고 선택한 후에는 내부 루프에서 외부 루프로의 매끄러운 전환을 빌드합니다.

[GitOps를 사용하는 CI/CD 워크플로](conceptual-gitops-ci-cd.md) 문서 예제에 설명된 것처럼 애플리케이션 개발자는 애플리케이션 리포지토리 내의 애플리케이션 코드에서 작동합니다. 또한 이 애플리케이션 리포지토리는 높은 수준의 개발 Helm 및/또는 Kustomize 템플릿을 갖고 있습니다. CI\CD 파이프라인:

* 높은 수준의 템플릿으로부터 낮은 수준의 매니페스트를 생성하고 환경 특정 값을 추가합니다.
* 특정 환경에 필요한 상태가 포함된 GitOps 리포지토리와 낮은 수준의 매니페스트를 병합하는 끌어오기 요청을 만듭니다. 

개발자에 대한 로컬 구성 값을 사용하여 내부 개발 루프에 대해 비슷한 낮은 수준의 매니페스트를 로컬로 생성할 수 있습니다. 애플리케이션 개발자는 코드가 변경될 때 작업을 반복하고 낮은 수준의 매니페스트를 사용하여 애플리케이션을 배포하고 디버그할 수 있습니다. 낮은 수준의 매니페스트 생성은 개발자의 로컬 구성을 사용하여 내부 루프 워크플로에 통합될 수 있습니다. 대부분의 내부 루프 프레임워크는 사용자 정의 플러그 인을 통해 확장하거나 후크 기반 스크립트 호출을 삽입하여 사용자 정의 흐름을 구성하도록 허용합니다. 

## <a name="example-inner-loop-workflow-built-with-devspace-framework"></a>DevSpace 프레임워크를 사용하여 빌드된 내부 루프 워크플로 예제


### <a name="diagram-a-inner-loop-flow"></a>다이어그램 A: 내부 루프 흐름
:::image type="content" source="media/dev-inner-loop.png" alt-text="DevSpace를 사용하는 내부 루프 흐름을 보여주는 다이어그램입니다.":::

### <a name="diagram-b-inner-loop-to-outer-loop-transition"></a>다이어그램 B: 내부 루프에서 외부 루프로 전환
:::image type="content" source="media/inner-loop-to-outer-loop.png" alt-text="내부 루프에서 외부 루프로의 전환을 보여주는 다이어그램입니다." :::


## <a name="example-workflow"></a>예제 워크플로
애플리케이션 개발자로서 Alice는 다음을 수행합니다.
- 내부 루프 구성을 위해 devspace.yaml을 작성합니다.
- 효율성을 위해 내부 루프를 사용하여 애플리케이션 코드를 작성하고 테스트합니다.
- 외부 루프를 사용하여 스테이징 또는 프로덕션에 배포합니다.


Alice가 로컬 또는 원격 클러스터에서 애플리케이션을 업데이트, 실행, 디버그해야 한다고 가정해보세요.

1. Alice가 .env 파일에 표현된 개발 환경의 로컬 구성을 업데이트합니다.
1. Alice가 `devspace use context`를 실행하고 Kubernetes 클러스터 컨텍스트를 선택합니다.
1.  Alice가 `devspace use namespace <namespace_name>`을 실행하여 작업할 네임스페이스를 선택합니다.
1.  Alice는 애플리케이션 코드에 대해 변경사항을 반복 수행하고 `devspace dev`를 실행하여 애플리케이션을 대상 클러스터에 배포하고 디버그할 수 있습니다.
1. `devspace dev`를 실행하면 Alice의 로컬 구성에 따라 낮은 수준의 매니페스트가 생성되고 애플리케이션이 배포됩니다. 이러한 낮은 수준의 매니페스트는 devspace.yaml의 DevSpace 후크로 구성됩니다.
1. Alice는 코드를 변경할 때마다 컨테이너를 다시 빌드할 필요가 없습니다. DevSpace에서 핫 다시 로드가 사용되므로, 파일 동기화를 사용하여 컨테이너 내부에서 최근 변경 사항을 복사할 수 있습니다.
1. `devspace dev`를 실행하면 프런트엔드에 대한 백엔드 종속성과 같이 devspace.yaml에 구성된 모든 종속성도 배포됩니다. 
1. Alice는 devspace.yaml을 통해 구성된 전달을 통해 애플리케이션에 액세스하여 변경 사항을 테스트합니다.
1. Alice가 변경 사항을 완료한 후에는 `devspace purge`를 실행하여 배포를 삭제하고 새로운 끌어오기 요청을 만들어서 변경 사항을 애플리케이션 리포지토리의 개발 분기에 병합할 수 있습니다.

> [!NOTE]
> 위 워크플로의 샘플 코드는 이 [GitHub 리포지토리](https://github.com/Azure/arc-cicd-demo-src)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Arc 사용하도록 설정된 Kubernetes를](./conceptual-configurations.md) 사용하여 구성 리소스로 클러스터와 Git 리포지토리 간의 연결을 만드는 방법에 대해 자세히 알아봅니다.