---
title: 환경을 Azure Pipelines 통합
description: Azure DevOps CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인에 Azure DevTest Labs 환경을 통합하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/15/2021
ms.openlocfilehash: b3478242dddd7141100f05ae44a09902a2546ffd
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488113"
---
# <a name="integrate-environments-into-your-cicd-pipelines"></a>CI/CD 파이프라인에 환경 통합 
이 문서에서는 하나의 완전한 파이프라인에서 환경을 만들고 배포한 다음 환경을 삭제하는 방법을 알아봅니다. 이 환경을 사용하여 CI(연속 통합)/CD(지속적인 업데이트) 릴리스 파이프라인을 Azure DevTest Labs 통합할 수 있습니다. 통합을 위해 Azure DevOps Services Azure DevTest Labs Tasks 확장을 사용합니다. 이러한 확장을 통해 특정 테스트 작업에 대한 [환경을](devtest-lab-test-env.md) 더 쉽게 배포한 다음 테스트가 완료되면 삭제할 수 있습니다. 

일반적으로 고유한 사용자 지정 빌드-테스트-배포 파이프라인에서는 해당 작업을 각각 개별적으로 수행합니다. 이 문서에 사용된 확장은 [DTL VM 작업 만들기/삭제](devtest-lab-integrate-ci-cd.md) 외에도 다음과 같은 작업을 수행합니다.

- 환경 만들기
- 환경 삭제

## <a name="prerequisites"></a>사전 요구 사항
CI/CD 파이프라인을 Azure DevTest Labs 통합하려면 먼저 다음을 수행해야 합니다. 
1. [Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) Marketplace에서 Azure DevTest Labs Tasks 확장을 설치합니다. 
1. [랩을 만들고](devtest-lab-create-lab.md) 기본적으로 켜져 있는 **공용 환경** 을 사용하도록 구성되었는지 확인합니다.

## <a name="create-a-release-definition--environment"></a>릴리스 정의 & 환경 만들기
릴리스 정의를 만들려면 다음 단계를 수행합니다.

1. Azure DevOps 프로젝트의 **Pipelines** 섹션에서 **릴리스를** 선택합니다.
1. 릴리스 탭에서 새 **파이프라인** **을** 선택합니다.  오른쪽의 **템플릿 선택** 창에서 일반적인 배포 패턴에 대한 주요 템플릿 목록을 찾을 수 있습니다. 
1. 이 파이프라인의 경우 **빈 작업을** 선택하여 개발 또는 테스트 용도로 사용할 환경 만들기를 시작합니다.
1. 빈 작업의 도구 모음에서 **태스크를** 선택한 다음, **1단계를** 선택합니다.

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-stage.png" alt-text="스크린샷은 파이프라인 릴리스 단계를 여는 것을 보여줍니다.":::

1. 단계에 태스크를 추가하려면 **에이전트 작업** 섹션에서 더하기(+) 기호를 선택합니다. 사용 가능한 작업의 검색 가능한 목록이 나타납니다. 
1. 작업 **추가** 창에서 를 검색합니다. `Azure DevTest Labs Create Environment`
1. `Azure DevTest Labs Create Environment`결과에서 작업을 선택하고 **추가를** 선택합니다.
1. 방금 추가한 작업을 선택합니다. **환경 만들기(미리 보기)** Azure DevTest Labs 창이 나타납니다.

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-environment.png" alt-text="스크린샷은 Azure DevTest Labs Azure Pipelines 환경에 필요한 필드를 보여줍니다.":::

2. **작업** 탭에서 다음과 같이 환경을 구성합니다.

   |필드|Description|
   |-----|-----------|
   |**Azure RM 구독**|실행하기 전에 구성할 Azure Resource Manager 구독입니다. **사용 가능한 Azure 서비스 연결** 목록에서 연결을 선택하거나 Azure 구독에 대한 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints)를 참조하세요.|
   |**랩 이름**|이전에 만든 랩의 이름을 선택합니다(필수 조건 참조). 실제로 배포할 랩을 선택해야 합니다. 리소스는 이 랩에서 만들어집니다.  변수 를 사용할 수도 `$(labName)` 있습니다.  식별 이름을 수동으로 입력하면 오류가 발생하므로 드롭다운 목록을 사용하여 정보를 선택합니다.|
   |**환경 이름**|선택한 랩 내에서 만들 환경의 이름입니다.|
   |**리포지토리 이름**|템플릿을 포함하는 소스 코드 리포지토리의 이름입니다. 기본 리포지토리, 또는 `Public Environment Repo` 사용하려는 템플릿이 포함된 다른 리포지토리를 선택할 수 있습니다. 리포지토리는 랩 정책에서 설계되었습니다.  식별 이름을 수동으로 입력하면 오류가 발생하므로 드롭다운 목록을 사용하여 정보를 선택합니다.|
   |**템플릿 이름**|환경을 만드는 데 사용할 템플릿의 이름입니다. 환경 템플릿의 이름을 선택합니다. 식별 이름을 수동으로 입력하면 오류가 발생하므로 드롭다운 목록을 사용하여 정보를 선택합니다.| 
   |**환경 이름**|랩 내에서 환경 인스턴스를 고유하게 식별하는 이름을 입력합니다.  이름은 랩 내에서 고유해야 합니다.|
   |**매개 변수 파일**  &  **매개 변수 재정의**|를 사용하여 사용자 지정 매개 변수를 환경에 전달합니다. 둘 중 하나 또는 둘 다를 사용하여 매개 변수 값을 설정할 수 있습니다. 예를 들어 이러한 필드를 사용하여 암호화된 암호를 전달할 수 있습니다. 변수를 사용하여 로그에 비밀 정보를 전달하지 않도록 방지하고 Azure Key Vault 연결할 수도 있습니다.|

## <a name="delete-the-environment"></a>환경 삭제
최종 단계에서는 Azure DevTest Labs 인스턴스에 배포된 환경을 삭제합니다. 일반적으로 배포된 리소스에서 필요한 개발 작업을 실행하거나 테스트를 실행한 후에는 환경을 삭제합니다.

릴리스 정의에서 **작업 추가** 를 선택한 후 **배포** 탭에서 **Azure DevTest Labs 환경 삭제** 작업을 추가합니다. 다음과 같이 구성합니다.

1. VM을 삭제하려면 [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)를 참조하세요.
    1. **Azure RM 구독** 의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints)를 참조하세요.
    2. **랩 이름** 은 환경이 있는 랩을 선택합니다.
    3. **환경 이름** 은 제거할 환경의 이름을 입력합니다.
2. 릴리스 정의 대한 이름을 입력한 다음, 저장합니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 
- [Resource Manager 템플릿으로 다중 VM 환경 만들기](devtest-lab-create-environment-from-arm.md)
- [DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)의 DevTest Labs 자동화를 위한 빠른 시작 Resource Manager 템플릿
- [VSTS 문제 해결 페이지](/azure/devops/pipelines/troubleshooting)
