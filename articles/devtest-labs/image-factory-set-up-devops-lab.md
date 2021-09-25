---
title: Azure DevOps에서 이미지 팩터리 실행
description: 이 문서에서는 Azure DevOps(이전의 Visual Studio Team Services)에서 이미지 팩터리를 실행하는 데 필요한 모든 준비 사항을 설명합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: bc12dc67160a68898191ab802cc9f6e9bd3f3f62
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604863"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Azure DevOps에서 이미지 팩터리 실행
이 문서에서는 Azure DevOps(이전의 Visual Studio Team Services)에서 이미지 팩터리를 실행하는 데 필요한 모든 준비 사항을 설명합니다.

> [!NOTE]
> 어떤 오케스트레이션 엔진이라도 사용할 수 있습니다. Azure DevOps가 필수적인 것은 아닙니다. 이미지 팩터리는 Azure PowerShell 스크립트를 사용하여 실행되므로 Windows 작업 스케줄러, 다른 CI/CD 시스템 등을 사용하여 수동으로 실행할 수 있습니다.

## <a name="create-a-lab-for-the-image-factory"></a>이미지 팩터리 랩 만들기
이미지 팩터리를 설정하는 첫 번째 단계는 Azure DevTest Labs에서 랩을 만드는 것입니다. 이 랩은 가상 머신을 만들고 사용자 지정 이미지를 저장하는 이미지 팩터리 랩입니다. 이 랩은 전체 이미지 팩터리 프로세스의 일부로 간주됩니다. 랩을 만든 후에는 나중에 필요하므로 이름을 저장해야 합니다.

## <a name="scripts-and-templates"></a>스크립트 및 템플릿
팀에서 사용할 이미지 팩터리를 채택하는 다음 단계는 사용 가능한 기능을 파악하는 것입니다. 이미지 팩터리 스크립트 및 템플릿은 [DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)에서 공개적으로 사용할 수 있습니다. 개요는 다음과 같습니다.

- 이미지 팩터리. 루트 폴더입니다.
    - 구성 이미지 팩터리에 대한 입력입니다.
        - GoldenImages. 이 폴더에는 사용자 지정 이미지 정의를 나타내는 JSON 파일이 포함되어 있습니다.
        - Labs.json. 팀이 특정 사용자 지정 이미지를 받도록 등록하는 파일입니다.
- 스크립트. 이미지 팩터리 엔진입니다.

이 섹션의 문서에서는 이 스크립트 및 템플릿에 대한 자세한 정보를 제공합니다.

## <a name="create-an-azure-devops-team-project"></a>Azure DevOps 팀 프로젝트 만들기
Azure DevOps를 사용하면 소스 코드를 저장하고 Azure PowerShell을 한 곳에서 실행할 수 있습니다. 되풀이 실행을 예약하여 이미지를 최신 상태로 유지할 수 있습니다. 결과를 기록하여 문제를 진단할 수 있는 좋은 기능이 있습니다.  Azure DevOps 사용이 필수 요건은 아니지만 사용할 경우 원하는 도구/엔진으로 Azure에 연결할 수 있고 Azure PowerShell을 실행할 수 있습니다.

대신 사용할 기존 DevOps 계정 또는 프로젝트가 있는 경우 이 단계를 건너뜁니다.

시작하려면 Azure DevOps에서 체험 계정을 만듭니다. https://www.visualstudio.com/ 을 방문하여 **Azure DevOps**(이전의 VSTS) 바로 아래의 **체험판 시작하기** 를 선택합니다. 고유한 계정 이름을 선택하고 Git을 사용하여 코드를 관리하도록 선택해야 합니다. 계정을 만든 후에는 팀 프로젝트에 URL을 저장합니다. 샘플 URL로 `https://<accountname>.visualstudio.com/MyFirstProject`가 있습니다.

## <a name="check-in-the-image-factory-to-git"></a>Git에 이미지 팩터리 체크 인
이미지 팩터리의 모든 PowerShell, 템플릿, 구성은 [퍼블릭 DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)에 있습니다. 새 팀 프로젝트로 코드를 가져오는 가장 빠른 방법은 리포지토리를 가져오는 것입니다. 이렇게 하면 전체 DevTest Labs 리포지토리를 풀하므로 추가 문서 및 샘플을 얻을 수 있습니다.

1. 이전 단계에서 만든 Azure DevOps 프로젝트를 방문합니다(URL은 **https:\//\<accountname>.visualstudio.com/MyFirstProject** 와 유사함).
2. **리포지토리 가져오기** 를 선택합니다.
3. DevTest Labs 리포지토리의 **복제본 URL** `https://github.com/Azure/azure-devtestlab`을 입력합니다.
4. **가져오기** 를 선택합니다.

    ![Git 리포지토리 가져오기](./media/set-up-devops-lab/import-git-repo.png)

정확히 필요한 항목(이미지 팩터리 파일)만 체크 인하려면 [여기](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs)에 나오는 단계에 따라 Git 리포지토리를 복제하고 **scripts/ImageFactory** 디렉터리에 있는 파일만 푸시합니다.

## <a name="create-a-build-and-connect-to-azure"></a>빌드를 만들고 Azure에 연결
이제 Azure DevOps의 Git 리포지토리에 원본 파일이 저장되어 있습니다. 이번에는 파이프라인에서 Azure PowerShell을 실행하도록 설정해야 합니다. 해당 단계를 수행하는 데는 많은 옵션이 있습니다. 이 문서에서는 편의를 위해 빌드 정의를 사용하지만 DevOps 빌드나 DevOps 릴리스(단일 또는 복수 환경), Windows 작업 스케줄러 같은 다른 실행 엔진, Azure PowerShell을 실행할 수 있는 다른 도구를 사용할 수도 있습니다.

> [!NOTE]
> 일부 PowerShell 파일은 만들 사용자 지정 이미지가 많은 경우(10개 초과) 실행하는 데 시간이 오래 걸리는 점에 유의해야 합니다. 사용 가능한 DevOps 빌드/릴리스의 호스트된 에이전트에는 30분 시간 제한이 있으므로 많은 이미지를 빌드하기 시작한 후에는 사용 가능한 호스트된 에이전트를 사용할 수 없습니다. 사용하려는 도구가 무엇이든 시간 제한 문제는 모든 도구에 적용되므로 장기간 실행되는 Azure PowerShell 스크립트의 경우 일반적인 시간 제한을 확장할 수 있는지 미리 확인하는 것이 좋습니다. Azure DevOps에서는 유료의 호스트된 에이전트를 사용할 수도 있고 고유한 빌드 에이전트를 사용할 수도 있습니다.

1. 시작하려면 DevOps 프로젝트 홈페이지에서 **빌드 설정** 을 선택합니다.

    ![빌드 설정 단추](./media/set-up-devops-lab/setup-build-button.png)
2. 빌드 **이름** 을 지정합니다(예: 이미지를 빌드하여 DevTest Labs에 전달).
3. **빈** 빌드 정의를 선택하고 **적용** 을 선택하여 빌드를 만듭니다.
4. 이 단계에서 빌드 에이전트에 대해 **호스트됨** 을 선택할 수 있습니다.
5. 빌드 정의를 **저장** 합니다.

    ![빌드 정의](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>빌드 변수 구성
명령줄 매개 변수를 간소화하려면 이미지 팩터리를 구동하는 키 값을 빌드 변수 세트로 캡슐화합니다. **변수** 탭을 선택하면 여러 기본 변수 목록이 표시됩니다. 다음은 Azure DevOps에 입력할 변수 목록입니다.


| 변수 이름 | 값 | 참고 |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | 리포지토리에서 **구성** 폴더의 전체 경로입니다. 위에서 전체 리포지토리를 가져온 경우 왼쪽의 값이 맞습니다. 전체 리포지토리를 가져오지 않은 경우 구성 위치를 가리키도록 업데이트하세요. |
| DevTestLabName | MyImageFactory | 이미지를 생성하는 데 팩터리로 사용되는 Azure DevTest Labs의 랩 이름입니다. 랩이 없는 경우 랩을 만듭니다. 랩이 서비스 엔드포인트에서 액세스할 수 있는 동일한 구독에 있는지 확인합니다. |
| ImageRetention | 1 | 형식마다 저장할 이미지 수입니다. 기본값은 1로 설정합니다. |
| MachinePassword | ******* | 가상 머신의 기본 제공 관리자 계정 암호입니다. 임시 계정이므로 보호되는지 확인해야 합니다. 오른쪽의 작은 잠금 아이콘을 선택하여 보안 문자열인지 확인합니다. |
| MachineUserName | ImageFactoryUser | 가상 머신의 기본 제공 관리자 계정 사용자 이름입니다. 임시 계정입니다. |
| StandardTimeoutMinutes | 30 | 정기 Azure 작업 중 대기해야 하는 시간 제한입니다. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 랩이 있고 서비스 엔드포인트에서 액세스할 수 있는 구독의 ID입니다. |
| VMSize | Standard_A3 | **만들기** 단계에 사용할 가상 머신의 크기입니다. 만들어지는 VM은 임시 VM입니다. 크기는 [랩에 사용할 수 있는](devtest-lab-set-lab-policy.md) 크기여야 합니다. [구독 코어 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md)이 충분한지 확인합니다.

![변수 빌드](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Azure에 연결
다음 단계에서는 서비스 주체를 설정합니다. 사용자 대신 DevOps 빌드 에이전트가 Azure에서 작동할 수 있도록 하는 Azure Active Directory의 ID입니다. 설정하려면 먼저 Azure PowerShell 빌드 단계를 추가합니다.

1. **작업 추가** 를 선택합니다.
2. **Azure PowerShell** 을 검색합니다.
3. 찾은 후에는 **추가** 를 선택하여 빌드에 작업을 추가합니다. 이렇게 하면 추가된 작업이 왼쪽에 표시됩니다.

![PowerShell 설정 단계](./media/set-up-devops-lab/set-up-powershell-step.png)

서비스 주체를 설정하는 가장 빠른 방법은 Azure DevOps에서 이 작업을 수행하도록 허용하는 것입니다.

1. 방금 추가한 **작업** 을 선택합니다.
2. **Azure 연결 형식** 의 경우 **Azure Resource Manager** 를 선택합니다.
3. **관리** 링크를 선택하여 서비스 주체를 설정합니다.

자세한 내용은 이 [블로그 게시물](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)을 참조하세요. **관리** 링크를 선택하면 DevOps에서 Azure에 대한 연결을 설정하는 데 적합한 위치(블로그 게시물의 두 번째 스크린샷)로 이동합니다. 연결을 설정하는 경우 **Azure Resource Manager 서비스 엔드포인트** 를 선택해야 합니다.

## <a name="complete-the-build-task"></a>빌드 작업 완료
빌드 작업을 선택하면 오른쪽 창에 채워야 하는 모든 세부 정보가 표시됩니다.

1. 먼저 빌드 작업의 이름을 **가상 머신 만들기** 로 지정합니다.
2. **Azure Resource Manager** 를 선택하여 만들어진 **서비스 주체** 를 선택합니다.
3. **서비스 엔드포인트** 를 선택합니다.
4. **스크립트 경로** 는 오른쪽에 있는 **...(줄임표)** 를 선택합니다.
5. **MakeGoldenImageVMs.ps1** 스크립트로 이동합니다.
6. 스크립트 매개 변수는 다음과 유사합니다. `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![빌드 정의 완료](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>빌드 큐 대기
새 빌드를 큐에 대기시켜 모든 것이 올바르게 설정되었는지 확인해 보겠습니다. 빌드가 실행되는 동안 [Azure Portal](https://portal.azure.com)로 전환하고 이미지 팩터리 랩에서 **모든 가상 머신** 을 선택하여 모든 것이 제대로 작동하는지 확인합니다. 랩에 세 개의 가상 머신이 만들어진 것이 표시되어야 합니다.

![랩의 VM](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>다음 단계
Azure DevTest Labs를 기반으로 이미지 팩터리를 설정하는 첫 번째 단계가 완료되었습니다. 이 시리즈의 다음 문서에서는 해당 VM을 일반화하여 사용자 지정 이미지에 저장합니다. 그런 다음, 다른 모든 랩에 배포합니다. 이 시리즈의 다음 문서 [사용자 지정 이미지 저장 및 여러 랩에 배포](image-factory-save-distribute-custom-images.md)를 참조하세요.
