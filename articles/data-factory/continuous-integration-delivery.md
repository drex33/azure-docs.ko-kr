---
title: 지속적인 통합 및 업데이트
description: 연속 통합 및 배달을 사용 하 여 한 환경 (개발, 테스트, 프로덕션)에서 다른 환경으로 Azure Data Factory 파이프라인을 이동 하는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3784d45b59219a6a8851c2ad8bbd842f38015baf
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220413"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure Data Factory의 지속적인 통합 및 지속적인 업데이트

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

연속 통합은 코드 베이스에 대 한 각 변경을 자동으로 가능한 한 빨리 테스트 하는 방법입니다. 연속 전달은 연속 통합 중에 발생 하는 테스트를 따르고 스테이징 또는 프로덕션 시스템에 변경 내용을 푸시합니다.

Azure Data Factory에서 CI/CD(지속적인 통합 및 지속적인 업데이트)는 환경(개발, 테스트, 프로덕션) 간에 Data Factory 파이프라인을 이동하는 것입니다. Azure Data Factory는 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 활용하여 다양한 ADF 엔터티(파이프라인, 데이터 세트, 데이터 흐름 등)의 구성을 저장합니다. Data Factory를 다른 환경으로 승격시키는 두 가지 제안된 방법이 있습니다.

-    Data Factory와 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)의 통합을 사용한 자동화된 배포
-    Azure Resource Manager와 Data Factory UX의 통합을 사용하여 Resource Manager 템플릿을 수동으로 업로드합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD 수명 주기

다음은 Azure Repos Git로 구성된 Azure Data Factory의 CI/CD 수명 주기에 대한 샘플 개요입니다. Git 리포지토리를 구성하는 방법에 대한 자세한 내용은 [Azure Data Factory에서 원본 제어](source-control.md)를 참조하세요.

1.  Azure Repos Git를 사용하여 개발 Data Factory를 만들어 구성합니다. 모든 개발자는 파이프라인 및 데이터 세트와 같은 Data Factory 리소스를 작성할 수 있는 권한이 있어야 합니다.

1.  개발자는 [기능 분기를 만들어](source-control.md#creating-feature-branches) 변경합니다. 가장 최근 변경 내용으로 파이프라인 실행을 디버그합니다. 파이프라인 실행을 디버그하는 방법에 대한 자세한 내용은 [Azure Data Factory를 사용한 반복 개발 및 디버깅](iterative-development-debugging.md)을 참조하세요.

1.  개발자가 변경 내용에 대해 만족하면 기능 분기에서 기본 분기 또는 협업 분기로 끌어오기 요청을 만들어 변경 내용이 피어에 의해 검토됩니다.

1.  끌어오기 요청이 승인되고 변경 내용이 기본 분기에서 병합되면 변경 내용이 개발 팩터리에 게시됩니다.

1.  팀이 테스트 또는 UAT(사용자 승인 테스트) 팩터리에 대한 변경 내용을 배포할 준비가 되면 팀은 Azure Pipelines 릴리스로 이동하여 원하는 버전의 개발 팩터리를 UAT에 배포합니다. 이 배포는 Azure Pipelines 작업의 일부로 발생하고 Resource Manager 템플릿 매개 변수를 사용하여 적절한 구성을 적용합니다.

1.  테스트 팩터리에서 변경 내용을 확인한 후 파이프라인 릴리스의 다음 작업을 사용하여 프로덕션 팩터리에 배포합니다.

> [!NOTE]
> 개발 팩터리만이 Git 리포지토리에 연결됩니다. 테스트 및 프로덕션 팩터리는 Git 리포지토리가 연결되어 있지 않아야 하며 Azure DevOps 파이프라인 또는 리소스 관리 템플릿을 통해서 업데이트해야 합니다.

아래 이미지에는 이 수명 주기의 여러 단계가 강조 표시되어 있습니다.

:::image type="content" source="media/continuous-integration-delivery/continuous-integration-image12.png" alt-text="Azure Pipelines를 사용하는 지속적인 통합 다이어그램":::

## <a name="best-practices-for-cicd"></a>CI/CD에 대한 모범 사례

Data Factory를 통해 Git 통합을 사용할 때 개발에서 테스트, 프로덕션 순서로 변경 내용을 이동하는 CI/CD 파이프라인이 있는 경우 다음 모범 사례를 적용하는 것이 좋습니다.

-   **Git 통합**. Git 통합으로 개발 Data Factory를 구성합니다. 테스트 및 프로덕션에 대한 변경 내용은 CI/CD를 통해 배포되므로 Git 통합이 필요 없습니다.

-   **배포 전 및 배포 후 스크립트**. CI/CD의 Resource Manager 배포 단계 전에 트리거를 중지, 다시 시작, 정리를 수행하는 등의 특정 작업을 완료해야 합니다. 배포 작업 전후에 PowerShell 스크립트를 사용하는 것이 좋습니다. 자세한 내용은 [활성 트리거 업데이트](continuous-integration-delivery-automate-azure-pipelines.md#updating-active-triggers)를 참조하세요. 이 페이지의 맨 아래에 있는 Data Factory 팀에서 [제공한 스크립트](continuous-integration-delivery-sample-script.md)를 사용할 수 있습니다.

-   **통합 런타임 및 공유**. 통합 런타임은 자주 변경되지 않으며 CI/CD의 모든 단계에서 유사합니다. 따라서 Data Factory에서는 CI/CD의 모든 단계에서 동일한 이름 및 유형의 통합 런타임을 사용해야 합니다. 모든 단계에서 통합 런타임을 공유하려면 공유 통합 런타임을 포함하기 위해 3개로 구성된 팩터리를 사용하는 것이 좋습니다. 모든 환경에서 이 공유 팩터리를 연결된 통합 런타임 형식으로 사용할 수 있습니다.

-   **관리형 프라이빗 엔드포인트 배포**. 프라이빗 엔드포인트가 팩터리에 이미 있는데 이름은 동일하지만 속성이 수정된 프라이빗 엔드포인트가 포함된 ARM 템플릿을 배포하려고 하는 경우 배포가 실패합니다. 달리 말하면 프라이빗 엔드포인트가 팩터리에 이미 있는 것과 동일한 속성을 보유하는 한 프라이빗 엔드포인트를 성공적으로 배포할 수 있습니다. 속성이 환경 간에 서로 다른 경우 해당 속성을 매개 변수화하고 배포 중에 각각의 값을 제공하여 속성을 재정의할 수 있습니다.

-   **Key Vault**. 연결 정보가 Azure Key Vault에 저장되어 있는 연결된 서비스를 사용하는 경우 다른 환경에 대해 별도의 키 자격 증명 모음을 유지하는 것이 좋습니다. 또한 각각의 키 자격 증명 모음에 대해 개별 권한 수준을 구성할 수도 있습니다. 예를 들어 팀 멤버에게 프로덕션 비밀에 대한 사용 권한을 부여하지 않을 수 있습니다. 이 접근 방식을 따를 경우 모든 단계에서 동일한 비밀 이름을 유지하는 것이 좋습니다. 동일한 비밀 이름을 유지하는 경우, 별도의 매개 변수인 키 자격 증명 모음 이름이 유일하게 변경되므로 CI/CD 환경에서 각 연결 문자열을 매개 변수화할 필요가 없습니다.

-  **리소스 이름 지정**. ARM 템플릿 제약 조건으로 인해 리소스의 이름에 공백이 포함 된 경우 배포 문제가 발생할 수 있습니다. Azure Data Factory 팀은 리소스 이름에 공백 대신 ‘_’ 또는 ‘-’ 문자 사용을 권장합니다. 예를 들어 ‘Pipeline_1’은 ‘Pipeline 1’보다 더 적합한 이름입니다.

- **노출 컨트롤 및 기능 플래그** 입니다.  팀에서 작업 하는 경우 변경 내용을 병합할 수 있지만 PROD 및 QA와 같은 높은 수준의 환경에서 실행 하지 않으려는 경우가 있습니다. 이 시나리오를 처리하기 위해 ADF 팀은 [기능 플래그를 사용하는 DevOps 개념](/azure/devops/migrate/phase-features-with-feature-flags)을 권장합니다. ADF에서 [전역 매개 변수](author-global-parameters.md) 및 [if 조건 작업](control-flow-if-condition-activity.md)을 결합하여 환경 플래그에 따라 논리 집합을 숨길 수 있습니다.

    기능 플래그를 설정하는 방법을 알아보려면 아래 비디오 자습서를 참조하세요.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="unsupported-features"></a>지원되지 않는 기능

- 기본적으로 Data Factory는 커밋의 cherry-pick 또는 리소스의 선택적 게시를 허용할 수 없습니다. 게시에는 Data Factory에서의 모든 변경 내용이 포함됩니다.

    - Data Factory 엔터티는 서로 종속됩니다. 예를 들어, 트리거는 파이프라인에 종속되고, 파이프라인은 데이터 세트 및 다른 파이프라인에 종속됩니다. 리소스 하위 집합을 선택적으로 게시하면 예기치 않은 동작 및 오류가 발생할 수 있습니다.
    - 선택적으로 게시해야 하는 경우 핫픽스를 사용하는 것이 좋습니다. 자세한 내용은 [핫픽스 프로덕션 환경](continuous-integration-delivery-hotfix-environment.md)을 참조하세요.

- Azure Data Factory 팀은 데이터 팩터리의 개별 엔터티(파이프라인, 데이터 세트 등)에 Azure RBAC 컨트롤을 할당하는 것을 권장하지 않습니다. 예를 들어 개발자가 파이프라인 또는 데이터 세트에 액세스할 수 있는 경우 데이터 팩터리의 모든 파이프라인 또는 데이터 세트에 액세스할 수 있어야 합니다. 데이터 팩터리 내에서 많은 Azure 역할을 구현해야 하는 경우 두 번째 데이터 팩터리를 배포하는 것이 좋습니다.

-   프라이빗 분기에서 게시할 수 없습니다.

-   현재 Bitbucket에서 프로젝트를 호스트할 수 없습니다.

-   현재 경고 및 매트릭스를 매개 변수로 내보내고 가져올 수 없습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Pipelines 릴리스를 사용 하 여 연속 통합 자동화](continuous-integration-delivery-automate-azure-pipelines.md)
- [리소스 관리자 템플릿을 각 환경으로 수동으로 승격](continuous-integration-delivery-manual-promotion.md)
- [리소스 관리자 템플릿에서 사용자 지정 매개 변수 사용](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [연결된 Resource Manager 템플릿](continuous-integration-delivery-linked-templates.md)
- [핫픽스 프로덕션 환경 사용](continuous-integration-delivery-hotfix-environment.md)
- [샘플 배포 전 및 배포 후 스크립트](continuous-integration-delivery-sample-script.md)