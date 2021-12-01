---
title: GitOps (Flux v2)를 사용 하는 CI/CD 워크플로-Azure Arc 사용 Kubernetes
description: 이 문서에서는 GitOps를 사용하여 CI/CD 워크플로에 대한 개념적 개요를 제공합니다.
keywords: GitOps, Flux, Kubernetes, K8s, Azure, 투구, Arc, CI/CD, Azure DevOps
services: azure-arc
ms.service: azure-arc
ms.date: 11/29/2021
ms.topic: conceptual
author: eedorenko
ms.author: iefedore
ms.openlocfilehash: e446cae29a6991e919de007d175a928a2ac3c321
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133407915"
---
# <a name="cicd-workflow-using-gitops-flux-v2---azure-arc-enabled-kubernetes"></a>GitOps (Flux v2)를 사용 하는 CI/CD 워크플로-Azure Arc enabled Kubernetes

최신 Kubernetes 배포에는 여러 응용 프로그램, 클러스터 및 환경이 포함 됩니다. GitOps를 사용하여 이러한 복잡한 설정을 보다 쉽게 관리할 수 있으며 Git를 사용하여 Kubernetes 환경의 원하는 상태를 선언적으로 추적할 수 있습니다. 일반적인 Git 도구를 사용 하 여 클러스터 상태를 선언 하면 책임을 늘리고, 오류 조사를 용이 하 게 하 고, 자동화를 통해 환경을 관리할 수 있습니다.

이 개념적 개요에서는 Azure Arc, Azure Repos 및 Azure Pipelines를 사용하여 전체 애플리케이션 변경 수명 주기의 현실로 GitOps를 설명합니다. GitOps 제어 Kubernetes 환경에 대 한 단일 응용 프로그램 변경의 예도 있습니다.

## <a name="architecture"></a>Architecture

하나 이상의 Kubernetes 환경에 배포된 애플리케이션을 고려합니다.

![GitOps CI/CD 아키텍처](./media/gitops/gitops-flux2-ci-cd-arch.png)

### <a name="application-repository"></a>응용 프로그램 리포지토리

응용 프로그램 리포지토리에는 개발자가 내부 루프 중에 작업 하는 응용 프로그램 코드가 포함 되어 있습니다. 이 리포지토리에는 응용 프로그램의 배포 템플릿이 투구 또는 Kustomize과 같은 일반 형식으로 있습니다. 환경 관련 값은 저장되지 않습니다. 이 리포지토리의 변경 내용은 배포 프로세스를 시작하는 PR 또는 CI 파이프라인을 호출합니다.

### <a name="container-registry"></a>컨테이너 레지스트리

Container registry는 Kubernetes 환경에서 사용 되는 첫 번째 및 타사 이미지를 모두 보관 합니다. 사용자가 읽을 수 있는 태그와 이미지를 빌드하는 데 사용 되는 Git 커밋을 사용 하 여 자사 응용 프로그램 이미지를 태그 합니다. 보안, 속도 및 복원력을 위해 타사 이미지를 캐시합니다. 적시에 테스트하고 보안 업데이트를 통합하기 위한 계획을 설정합니다. 자세한 내용은 [ACR 사용 및 유지 관리 공용 콘텐츠](../../container-registry/tasks-consume-public-content.md) 가이드에서 예제를 참조하세요.

### <a name="pr-pipeline"></a>PR 파이프라인

응용 프로그램 리포지토리에 대 한 끌어오기 요청은 PR 파이프라인이 성공적으로 실행 될 때 제어 됩니다. 이 파이프라인은 애플리케이션 코드에 대한 린팅 및 단위 테스트와 같은 기본 품질 게이트를 실행합니다. 파이프라인은 Kubernetes 환경에 배포하는 데 사용되는 애플리케이션 및 lint Dockerfile 및 Helm 템플릿을 테스트합니다. Docker 이미지를 빌드하고 테스트해야 하지만 푸시하지는 않습니다. 신속하게 반복할 수 있도록 파이프라인 기간을 비교적 짧게 유지합니다.

### <a name="ci-pipeline"></a>CI 파이프라인

응용 프로그램 CI 파이프라인은 모든 PR 파이프라인 단계를 실행 하 여 테스트 및 배포 검사를 확장 합니다. 주에 대 한 각 커밋에 대해 파이프라인을 실행 하거나 커밋 그룹을 사용 하 여 일반 흐름에서 실행할 수 있습니다.

이 단계에서 PR 파이프라인에서 수행 하는 데 너무 많이 사용 되는 응용 프로그램 테스트는 다음을 비롯 하 여 수행할 수 있습니다.

* 컨테이너 레지스트리에 이미지 푸시
* 이미지 작성, lint 및 테스트
* 원시 yamls의 템플릿 생성

CI 빌드를 마치면 CD 단계에서 배포 준비에 사용할 수 있는 아티팩트가 생성 됩니다.

### <a name="flux"></a>Flux

Flux는 각 클러스터에서 실행 되며 원하는 상태를 유지 관리 해야 하는 에이전트입니다. 에이전트는 사용자 정의 간격으로 GitOps 리포지토리를 폴링하고 git 리포지토리에 선언 된 상태를 사용 하 여 클러스터 상태를 조정 합니다.

자세한 내용은 [Flux 자습서](./tutorial-use-gitops-flux2.md)를 참조 하세요.

### <a name="cd-pipeline"></a>CD 파이프라인

성공적인 CI 빌드에 의해 CD 파이프라인이 자동으로 트리거됩니다. 이 파이프라인 환경에서는 환경 관련 값이 이전에 게시 된 템플릿으로 대체 되 고 이러한 값을 사용 하 여 GitOps 리포지토리에 대해 새 끌어오기 요청이 발생 합니다. 이 끌어오기 요청에는 하나 이상의 Kubernetes 클러스터에 대 한 desired 상태의 제안 된 변경 내용이 포함 되어 있습니다. 클러스터 관리자가 끌어오기 요청을 검토 하 고 GitOps 리포지토리에 병합을 승인 합니다. 파이프라인은 끌어오기 요청이 병합 될 때까지 기다린 후 Flux가 동기화 되어 상태 변경을 적용 합니다.

### <a name="gitops-repository"></a>GitOps 리포지토리

GitOps 리포지토리는 클러스터의 모든 환경에 대 한 현재 desired 상태를 나타냅니다. 이 리포지토리에 대 한 모든 변경 내용은 각 클러스터의 Flux 서비스에 의해 선택 되 고 배포 됩니다. 원하는 클러스터 상태에 대 한 변경 내용은 끌어오기 요청으로 표시 됩니다. 그러면이 요청을 검토 하 고 마지막으로 변경 승인에 병합 합니다. 이러한 끌어오기 요청에는 배포 템플릿에 대 한 변경 내용과 그 결과 렌더링 된 Kubernetes 매니페스트가 포함 됩니다. 낮은 수준으로 렌더링된 매니페스트를 사용하면 일반적으로 템플릿 수준에서 보이지 않는 변경 내용 검사를 보다 신중하게 수행할 수 있습니다.

### <a name="gitops-connector"></a>GitOps 커넥터

[Gitops 커넥터](https://github.com/microsoft/gitops-connector) 는 flux 에이전트와 Gitops 리포지토리/CD 파이프라인 간에 연결을 만듭니다. 클러스터에 변경 내용을 적용 하는 동안 Flux는 모든 단계 변경 및 상태 검사 수행의 GitOps 커넥터에 알립니다. 이 구성 요소는 어댑터 역할을 합니다. Git 리포지토리와 통신 하는 방법을 "알 수 있으며, 동기화 진행률이 GitOps 리포지토리에 표시 되도록 Git 커밋 상태를 업데이트 합니다. 배포가 완료 되 면 (성공 또는 실패 여부에 관계 없이) 커넥터가 통합 테스트와 같은 배포 후 작업을 수행할 수 있도록 CD 파이프라인에 계속 알립니다.

### <a name="kubernetes-clusters"></a>Kubernetes 클러스터

하나 이상의 Azure Arc 사용 Kubernetes 클러스터는 응용 프로그램에 필요한 다양 한 환경을 제공 합니다. 예를 들어 단일 클러스터는 서로 다른 네임스페이스를 통해 개발 및 QA 환경을 모두 제공할 수 있습니다. 두 번째 클러스터는 환경을 보다 쉽게 분리하고 보다 세분화된 제어를 제공할 수 있습니다.

## <a name="example-workflow"></a>예제 워크플로

애플리케이션 개발자로서 Alice는 다음을 수행합니다.

* 애플리케이션 코드를 작성합니다.
* Docker 컨테이너에서 애플리케이션을 실행하는 방법을 결정합니다.
* Kubernetes 클러스터에서 컨테이너 및 종속 서비스를 실행하는 템플릿을 정의합니다.

Alice는 애플리케이션이 여러 환경에서 실행할 기능이 필요하다는 것은 알고 있지만 각 환경에 대한 특정 설정은 알지 못합니다.

Alice가 애플리케이션 배포 템플릿에 사용되는 Docker 이미지를 변경하는 애플리케이션을 변경하려고 한다고 가정합니다.

1. Alice는 배포 템플릿을 변경 하 여 응용 프로그램 리포지토리의 "alice" 라는 원격 분기에 푸시하고 "main" 분기에 대 한 검토를 위해 끌어오기 요청을 엽니다.
2. Alice는 팀에 변경 내용을 검토하도록 요청합니다.
    * PR 파이프라인은 유효성 검사를 실행합니다.
    * PR 파이프라인 실행 및 팀 승인이 성공 하면 변경 내용이 병합 됩니다.
3. 그런 다음 CI 파이프라인이 시작 하 고 Alice의 변경 내용을 확인 하 고 성공적으로 완료 됩니다.
    * 변경 내용은 클러스터에 배포해도 안전하며, 아티팩트는 CI 파이프라인 실행에 저장됩니다.
4. CI (CI) 파이프라인이 성공적으로 실행 되 면 CD 파이프라인이 트리거됩니다.
    * CD 파이프라인은 Alice의 CI 파이프라인 실행에 의해 저장된 아티팩트를 선택합니다.
    * CD 파이프라인은 템플릿을 환경 관련 값으로 대체 하 고 GitOps 리포지토리의 기존 클러스터 상태에 대 한 변경 내용을 적용 합니다.
    * CD 파이프라인은 클러스터 상태에 대 한 원하는 변경 내용을 사용 하 여 GitOps 리포지토리의 프로덕션 분기에 대 한 끌어오기 요청을 만듭니다.
5. Alice 팀은 끌어오기 요청을 검토 하 고 승인 합니다.
    * 변경 내용은 환경에 해당하는 대상 분기로 병합됩니다.
6. 몇 분 내에 Flux는 GitOps 리포지토리를 변경 하 고 Alice의 변경 내용을 가져옵니다.
    * Docker 이미지 변경으로 인해 애플리케이션 Pod에 업데이트가 필요합니다.
    * Flux는 클러스터에 변경 내용을 적용합니다.
    * Flux는 [Gitops 커넥터](https://github.com/microsoft/gitops-connector)를 통해 gitops 리포지토리에 배포 상태를 보고 합니다. 
7. CD 파이프라인은 자동화 된 테스트를 실행 하 여 새 배포가 성공적으로 완료 되 고 예상 대로 작동 하는지 확인 합니다.
   > [!NOTE]
   > 배포를 대상으로 하는 추가 환경의 경우 CD 파이프라인은 다음 환경에 대 한 끌어오기 요청을 만들어 반복 하 고 4-7 단계를 반복 합니다. 이 프로세스에는 보안 관련 변경 또는 프로덕션 환경과 같은 위험한 배포 또는 환경에 대한 추가 승인이 필요할 수 있습니다.
8. 모든 환경에서 성공적인 배포를 수신 하면 파이프라인이 완료 됩니다.

## <a name="next-steps"></a>다음 단계

Flux 구성을 사용 하 여 클러스터와 Git 저장소 간에 연결을 만드는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Flux를 사용 하는 GitOps에 대해 알아보기](./conceptual-gitops-flux2.md)
