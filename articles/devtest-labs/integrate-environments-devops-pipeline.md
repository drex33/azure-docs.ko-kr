---
title: Azure Pipelines에 DevTest Labs 환경 통합
description: Azure DevTest Labs 환경을 Azure Pipelines CI (지속적인 통합) 및 CD (지속적인 업데이트) 파이프라인에 통합 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 11/17/2021
ms.openlocfilehash: 7d16280f90dbd0bcf0c3a30a797cee08f70d8075
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132937441"
---
# <a name="integrate-devtest-labs-environments-into-azure-pipelines"></a>Azure Pipelines에 DevTest Labs 환경 통합

Azure DevTest Labs Tasks 확장을 사용 하 여 Azure DevTest Labs를 Azure Pipelines에 통합할 수 있습니다. 이 문서에서는 확장을 사용 하 여 환경을 만들고 배포한 다음, 단일 파이프라인에서 환경을 삭제 합니다. 환경을 사용 하 여 Azure Pipelines 지속적인 CI/CD (지속적인 통합/지속적인 업데이트) 릴리스 파이프라인을 Azure DevTest Labs 통합할 수 있습니다.

Azure DevTest Labs Tasks 확장 Azure Pipelines에 다음 작업을 추가 합니다.

- 환경 만들기
- 환경 삭제

이러한 작업을 통해 특정 테스트를 위한 [환경을](devtest-lab-test-env.md) 신속 하 게 배포 하 고 테스트를 완료할 때 환경을 삭제할 수 있습니다. 일반적으로 자체 파이프라인에서 환경 만들기 및 삭제를 별도로 수행 합니다.

Vm 만들기 및 사용자 지정 이미지와 같은 다른 확장 작업에 대 한 자세한 내용은 [Azure Pipelines에 DevTest Labs 통합](devtest-lab-integrate-ci-cd.md)(영문)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Portal에서 [DevTest Labs 랩을 만들거나](devtest-lab-create-lab.md)기존 랩을 사용 합니다. 랩에서 **공용 환경을** 사용 하도록 구성 되어 있는지 확인 합니다 .이는 기본적으로 설정 되어 있습니다.
- [Azure DevOps Services](https://dev.azure.com) 조직에 등록 또는 로그인 하 고 [프로젝트를 만들거나](/vsts/organizations/projects/create-project)기존 프로젝트를 사용 합니다.
- Visual Studio Marketplace에서 Azure DevOps Services 조직으로 [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) 확장을 설치 합니다.

## <a name="create-a-release-pipeline-and-environment"></a>릴리스 파이프라인 및 환경 만들기

1. Azure DevOps 프로젝트의 **Pipelines** 섹션에서 **릴리스** 를 선택 합니다.
1. **새 파이프라인** 을 선택합니다.
1. 오른쪽에 있는 **템플릿을 선택** 하면 일반적인 배포 패턴의 템플릿 목록이 표시 됩니다. 페이지 맨 위에 있는 **빈 작업** 링크를 선택 합니다.
1. **새 릴리스 파이프라인** 페이지에서 도구 모음의 **작업** 을 드롭다운 하 고 **1 단계** 를 선택 합니다.

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-stage.png" alt-text="파이프라인 릴리스 단계 열기를 보여 주는 스크린샷" border="false":::

1. **+** **에이전트 작업** 옆에 있는 더하기 기호를 선택 합니다.
1. **작업 추가** 에서 **Azure DevTest Labs 환경 만들기** 를 검색 하 고 선택 하 고 **추가** 를 선택 합니다.
1. 왼쪽에서 **환경 만들기 작업 Azure DevTest Labs** 선택 합니다.
1. 다음과 같이 **Azure DevTest Labs 환경 만들기 (미리 보기)** 양식을 작성 합니다.
   
   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-environment.png" alt-text="Azure DevTest Labs Azure Pipelines 환경에 필요한 필드를 보여 주는 스크린샷" border="false":::

   - **AZURE RM 구독**: 드롭다운 목록에서 연결 또는 Azure 구독을 선택 합니다.
     > [!NOTE]
     > Azure 구독에 대 한 제한 된 권한 연결을 만드는 방법에 대 한 자세한 내용은 [Azure Resource Manager 서비스 끝점](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)을 참조 하세요.

   - **랩**: 배포 하려는 랩 이름을 선택 합니다. 변수를 사용할 수도 있습니다 `$(labName)` . 수동으로 이름을 입력 하면 오류가 발생 합니다. 드롭다운 목록에서 이름을 선택 합니다.

   - **환경 이름**: 랩에서 만들 환경의 이름을 입력 합니다.

   - **리포지토리**: 템플릿을 포함 하는 소스 코드 리포지토리를 선택 합니다.

     기본 리포지토리, **공용 환경 리포지토리** 또는 사용 하려는 템플릿이 포함 된 다른 리포지토리를 선택할 수 있습니다. 리포지토리는 랩 정책에 지정 됩니다. 이름을 수동으로 입력 하면 오류가 발생 합니다. 드롭다운 목록에서 이름을 선택 합니다.

   - **템플릿**: 환경을 만드는 데 사용할 템플릿을 선택 합니다. 이름을 수동으로 입력 하면 오류가 발생 합니다. 드롭다운 목록에서 이름을 선택 합니다.

   - **매개 변수 파일**: 저장 된 매개 변수 파일의 위치로 이동 합니다.

   - **매개 변수 재정의**: 환경에 사용자 지정 매개 변수를 전달 합니다.

   매개 변수 **파일**, **매개 변수 재정의** 또는 둘 다를 사용 하 여 매개 변수 값을 설정할 수 있습니다. 예를 들어 이러한 필드를 사용 하 여 암호화 된 암호를 전달할 수 있습니다. 변수를 사용 하 여 로그에 비밀 정보를 전달 하는 것을 방지 하 고 Azure Key Vault에 연결할 수도 있습니다.

## <a name="delete-the-environment"></a>환경 삭제

최종 파이프라인 단계는 배포한 환경을 삭제 하는 것입니다. 일반적으로 개발자 작업을 수행 하거나 배포 된 리소스에 대 한 테스트를 실행 한 후 환경을 삭제 합니다.

1. 릴리스 파이프라인에서 **+** **에이전트 작업** 옆에 있는 더하기 기호를 선택 합니다.
1. **작업 추가** 창에서 **Azure DevTest Labs 환경을** 검색 하 고 추가 합니다.
1. 왼쪽에서 **Azure DevTest Labs 환경 삭제** 작업을 선택 합니다.
1. 다음과 같이 양식을 작성 합니다.

   - **AZURE RM 구독**: 연결 또는 구독을 선택 합니다.
   - **랩**: 환경이 있는 랩을 선택 합니다.
   - **환경 이름**: 삭제할 환경의 이름을 선택 합니다.

1. 릴리스 파이프라인 페이지의 맨 위에서 **새 릴리스 파이프라인** 을 선택 하 고 파이프라인에 대 한 새 이름을 입력 합니다.
1. 오른쪽 위에서 **저장** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [ARM 템플릿을 사용 하 여 다중 VM 환경을 만드는](devtest-lab-create-environment-from-arm.md)방법에 대해 알아봅니다.
- [공용 DevTest labs GitHub](https://github.com/Azure/azure-quickstart-templates)리포지토리에서 DevTest labs 자동화를 위한 더 빠른 시작 ARM 템플릿을 탐색 합니다.
- 필요한 경우 [Azure Pipelines 문제 해결](/azure/devops/pipelines/troubleshooting)을 참조 하세요.
