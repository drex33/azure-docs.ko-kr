---
title: DevOps CI/CD 파이프라인과 Azure DevTest Labs 통합
description: 엔터프라이즈 환경에서 CI (지속적인 통합) 및 CD (지속적인 업데이트) 파이프라인과 Azure DevTest Labs를 사용 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: 321a32c3bbbb6d5e6e3fe4a17df322aab8f12f5d
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132941589"
---
# <a name="integrate-devtest-labs-and-devops-cicd-pipelines"></a>DevTest Labs와 DevOps CI/CD 파이프라인 통합

DevOps는 소프트웨어 개발 (개발)을 시스템 운영 (Ops)과 통합 하는 소프트웨어 개발 방법론입니다. 시스템은 비즈니스 목표와 일치 하는 새로운 소프트웨어 기능, 업데이트 및 픽스를 제공 합니다.

DevOps 방법론에도 적용 됩니다.

- 목표, 사용 패턴 및 고객 피드백을 기반으로 새 기능을 디자인 합니다.
- 문제가 발생 하는 경우 시스템을 수정, 복구 및 강화 합니다.

DevOps 방법론의 한 구성 요소는 CI (지속적인 통합) 및 CD (지속적인 업데이트) 파이프라인입니다. CI/CD 파이프라인은 일련의 단계를 통해 소스 제어에서 정보, 코드 및 리소스를 이동 하 여 시스템을 생성 합니다. 단계에는 빌드, 테스트 및 릴리스가 있습니다.

Azure DevTest Labs는 CI/CD 파이프라인에서 사용할 수 있습니다. 이 문서에서는 엔터프라이즈 환경에서 CI/CD 빌드 및 릴리스 파이프라인에 DevTest Labs를 사용 하는 방법을 설명 합니다.

## <a name="benefits-of-devtest-labs-in-devops-workflows"></a>DevOps 워크플로에서 DevTest Labs의 이점

랩은 기능 영역에서 작업 하는 팀에 집중 해야 합니다. 이 일반적인 포커스를 통해 더 빠르게 변경할 수 있으며, 작은 그룹으로 부정적인 영향을 제한할 수 있습니다. 변경 사항이 나 문제는 다른 항목에 영향을 주지 않고 랩 환경에서 발생 합니다.

일반적인 포커스를 통해 도구, 스크립트 및 Azure Resource Manager (ARM) 템플릿과 같은 영역별 리소스를 공유할 수 있습니다. 개발자는 공유 리소스를 사용 하 여 필요한 모든 코드, 도구 및 구성이 포함 된 Vm (가상 머신)을 만들 수 있습니다. ARM 템플릿은 적절 한 Azure 리소스를 사용 하 여 랩 Vm 및 랩 환경을 만듭니다. 템플릿은 리소스를 동적으로 만들거나 사용자 지정 항목을 사용 하 여 기본 이미지를 만듭니다.

예를 들어 제품이 고객의 컴퓨터에 설치 되는 독립 실행형 시스템 인 시나리오를 가정해 보겠습니다. DevTest Labs는 빠른 내부 루프 코드 테스트를 위해 고객 소프트웨어, 아티팩트 및 구성이 설치 된 랩 Vm을 만들 수 있습니다.

DevOps 워크플로에서 랩을 사용 하면 다음과 같은 이점이 있습니다.

- **포커스가 있는 액세스:** 랩을 구성 요소로 사용 하면 특정 에코 시스템이 제한 된 사용자와 연결 됩니다. 일반적으로 일반적인 영역이 나 특정 기능에서 작업 하는 팀 또는 그룹에는 랩을 할당 합니다.

- **클라우드의 인프라 복제:** 개발자는 소스 코드 및 도구를 사용 하 여 개발자 VM을 포함 하는 개발 에코 시스템을 신속 하 게 설정할 수 있습니다. 또한 개발자는 더 빠른 내부 루프 개발을 위해 프로덕션 구성과 거의 동일한 환경을 만들 수 있습니다.

- **프리 프로덕션 환경:** CI/CD 파이프라인의 랩은 비동기 테스트를 위해 여러 다른 사전 프로덕션 환경 또는 컴퓨터를 동시에 실행할 수 있습니다. 랩에서 다른 지원 인프라 및 빌드 에이전트를 배포 하 고 관리할 수 있습니다.

## <a name="use-labs-in-cicd-pipelines"></a>CI/CD 파이프라인에서 랩 사용

CI/CD 파이프라인은 중요 한 DevOps 구성 요소입니다. 파이프라인은 개발자의 끌어오기 요청에서 기존 코드와 코드를 통합 하 고 프로덕션 에코 시스템에 코드를 배포 합니다. DevTest Labs 통합의 경우 모든 리소스가 랩에 있어야 하는 것은 아닙니다. 예를 들어 보다 영구적인 리소스를 위해 랩 외부에 Jenkins 호스트를 설정할 수 있습니다. 랩을 CI/CD 파이프라인에 통합 하는 몇 가지 구체적인 예는 다음과 같습니다.

### <a name="build"></a>빌드

빌드 파이프라인은 함께 테스트 하 고 릴리스에 전달 하는 구성 요소 패키지를 만듭니다. 동적으로 구성 하는 인프라를 통해 더 많은 제어가 가능 합니다. 랩은 빌드 에이전트 및 기타 지원 리소스의 위치로 빌드 파이프라인의 일부일 수 있습니다. DevTest Labs는 랩 액세스를 제한 하 여 빌드 에이전트의 보안을 강화 하 고 실수로 인 한 손상의 가능성을 줄여 줍니다.

랩에서 여러 환경을 포함 하는 기능을 사용 하 여 각 빌드를 비동기적으로 실행할 수 있습니다. 빌드 ID는 특정 빌드에서 리소스를 고유 하 게 식별 하는 환경 정보의 일부입니다.

### <a name="test"></a>테스트

CI/CD 파이프라인은 자동 및 수동 테스트를 위해 Vm 및 환경과 같은 DevTest Labs 리소스 만들기를 자동화할 수 있습니다. 파이프라인은 빌드 정보 아티팩트 또는 수식을 사용 하 여 다양 한 사용자 지정 테스트 구성을 사용 하 여 Vm을 만듭니다.

### <a name="release"></a>해제

릴리스 프로세스에서는 코드를 배포 하기 전에 DevTest Labs를 사용 하 여 확인을 수행할 수 있습니다. 프로세스는 테스트와 비슷합니다. 프로덕션 리소스는 DevTest Labs에 배포할 수 없습니다.

### <a name="customization"></a>사용자 지정

Azure DevOps Services의 구성 요소인 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)에는 특정 랩 내에서 vm 및 환경을 조작 하는 기존 태스크가 있습니다. Azure Pipelines는 CI/CD 파이프라인을 관리 하는 한 가지 방법입니다. REST Api 호출, PowerShell 스크립트 실행 또는 Azure CLI 사용을 지 원하는 시스템에 랩을 통합할 수 있습니다.

일부 CI/CD 파이프라인 관리자에는 Azure 및 DevTest Labs 리소스를 관리할 수 있는 기존 오픈 소스 플러그 인이 있습니다. 파이프라인의 필요에 맞게 사용자 지정 스크립팅을 사용 해야 할 수도 있습니다. 작업을 실행 하는 경우 적절 한 역할 (일반적으로 기여자)에서 서비스 주체를 사용 하 여 랩에 액세스 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Pipelines에 DevTest Labs를 통합](devtest-lab-integrate-ci-cd.md)합니다.
- [CI/CD 파이프라인에 환경 통합](integrate-environments-devops-pipeline.md)
- [Azure Pipelines 빌드 및 릴리스 파이프라인에서 DevTest Labs 사용](use-devtest-labs-build-release-pipelines.md)
 