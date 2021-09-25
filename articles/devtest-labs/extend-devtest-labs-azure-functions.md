---
title: Azure Functions 사용하여 Azure DevTest Labs 확장
description: Azure Functions를 사용하여 Azure DevTest Labs를 확장하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8a6200dbfce99ee7904dc1a65965e95d81e98471
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623649"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Azure Functions를 사용하여 DevTest Labs 연장
Azure Functions를 사용하여 DevTest Labs에서 이미 지원하는 것 이상의 추가 시나리오를 지원할 수 있습니다. Azure Functions를 사용하여 비즈니스 관련 요구에 맞게 서비스의 기본 제공 기능을 확장할 수 있습니다. 다음 목록에서는 가능한 몇 가지 시나리오를 제공합니다. 이 문서에서는 이러한 샘플 시나리오 중 하나를 구현하는 방법을 보여 줍니다.

- 랩에서 VM(가상 머신)에 대한 최상위 수준 요약 제공
- [원격 데스크톱 게이트웨이를 사용하도록 랩 구성](configure-lab-remote-desktop-gateway.md)
- 내부 지원 페이지에 대한 준수 보고
- 사용자가 구독에서 증가된 사용 권한이 필요한 작업을 완료할 수 있도록 설정
- [DevTest Labs 이벤트 기반 워크플로 시작](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>개요
[Azure Functions](../azure-functions/functions-overview.md)는 Azure의 서버리스 컴퓨팅 플랫폼입니다. DevTest Labs를 사용하여 솔루션에서 Azure Functions를 사용하면 기존 기능을 사용자 지정 코드를 사용하여 확장할 수 있습니다. Azure Functions에 대한 자세한 내용은 [Azure Functions 설명서](../azure-functions/functions-overview.md)를 참조하세요. Azure Functions가 DevTest Labs에서 요구 사항 또는 전체 시나리오를 충족시키는 데 도움이 될 수 있는 방법을 설명하기 위해 이 문서에서는 다음과 같이 랩에서 VM에 대한 최상위 요약을 제공하는 예제를 사용합니다.

**예제 요구 사항/시나리오**: 사용자는 운영 체제, 소유자 및 적용된 모든 아티팩트를 포함하는 랩의 모든 VM에 대한 세부 정보를 볼 수 있습니다.  또한 **Windows 업데이트 적용** 아티팩트가 최근에 적용되지 않은 경우 이를 적용하는 쉬운 방법이 있습니다.

시나리오를 완료하려면 다음 다이어그램에 설명된 대로 두 개의 함수를 사용합니다.  

![전체 흐름](./media/extend-devtest-labs-azure-functions/flow.png)

이러한 샘플 함수의 소스 코드는 [DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions)에 있습니다(C# 및 PowerShell 구현은 모두 사용 가능).

- **UpdateInternalSupportPage**: 이 함수는 DevTest Labs를 쿼리하고 가상 머신에 대한 세부 정보를 사용하여 직접 내부 지원 페이지를 업데이트합니다.
- **ApplyWindowsUpdateArtifact**: 랩의 VM에 대해 이 함수는 **Windows 업데이트** 아티팩트를 적용합니다.

## <a name="how-it-works"></a>작동 방식
사용자가 DevTest Labs에서 **내부 지원** 페이지를 선택하면 VM, 랩 소유자 및 지원 연락처에 대한 정보를 포함하는 미리 채워진 페이지가 제공됩니다.  

**새로 고치려면 여기를 선택** 단추를 선택하는 경우 페이지는 첫 번째 Azure 함수: **UpdateInternalSupportPage** 를 호출합니다. 함수는 정보에 대해 DevTest Labs를 쿼리한 다음, **내부 지원** 페이지를 새 정보로 다시 작성합니다.

Windows 업데이트 아티팩트가 최근에 적용되지 않은 모든 VM에 대해 수행할 수 있는 추가 작업이 있습니다. VM에 Windows 업데이트를 적용하는 단추가 있습니다. VM에 대해 ***Windows 업데이트 실행** 단추를 선택하면 페이지에서 두 번째 Azure Function: **ApplyWindowsUpdateArtifact** 를 호출합니다. 이 함수는 가상 머신이 실행되고 있는지 여부를 확인하고, 그렇다면 [Windows 업데이트](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) 아티팩트를 직접 적용합니다.

## <a name="step-by-step-walkthrough"></a>단계별 안내
이 섹션에서는 **내부 지원** 페이지를 업데이트하는 데 필요한 Azure 리소스를 설정하는 방법에 대한 단계별 지침을 제공합니다. 이 연습에서는 DevTest Labs를 확장하는 한 가지 예제를 제공합니다. 다른 시나리오에 이 패턴을 사용할 수 있습니다.

### <a name="step-1-create-a-service-principal"></a>1단계: 서비스 주체 만들기 
첫 번째 단계는 랩을 포함하는 구독에 대한 사용 권한이 있는 서비스 주체를 가져오는 것입니다. 서비스 주체는 암호 기반 인증을 사용해야 합니다. [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps) 또는 [Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)을 사용하여 이 작업을 수행할 수 있습니다. 사용할 서비스 주체가 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

서비스 주체의 **애플리케이션 ID**, **키** 및 **테넌트 ID** 를 적어둡니다. 이 연습의 뒷부분에서 필요합니다. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>2단계: 샘플 다운로드 및 Visual Studio 2019에서 열기
리포지토리를 복제하거나 [여기](https://github.com/Azure/azure-devtestlab/archive/master.zip)에서 리포지토리를 다운로드하여 [C# Azure Functions 샘플](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp)의 복사본을 로컬로 다운로드합니다.  

1. Visual Studio 2019로 샘플 솔루션을 엽니다.  
1. 아직 설치하지 않은 경우 Visual Studio용 **Azure 개발** 워크로드를 설치합니다. **도구** -> **도구 및 기능 가져오기** 메뉴 항목을 통해 설치할 수 있습니다.

    ![Azure 개발 워크로드](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. 솔루션을 빌드합니다. **빌드**, **솔루션 빌드** 메뉴 항목을 차례로 선택합니다.

### <a name="step-3-deploy-the-sample-to-azure"></a>3단계: Azure에 샘플 배포
Visual Studio의 **솔루션 탐색기** 창에서 **AzureFunctions** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **게시** 를 선택합니다. 마법사에 따라 새 또는 기존 Azure Function App에 대한 게시를 완료합니다. Visual Studio를 사용하여 Azure 함수 개발 및 배포에 대한 자세한 내용은 [Visual studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md)을 참조하세요.

![게시 대화 상자](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>4단계: 애플리케이션 설정 수집
함수를 게시한 후에는 Azure Portal에서 이러한 함수에 대한 URL을 가져와야 합니다. 

1. [Azure Portal](https://portal.azure.com)로 이동합니다. 
1. 함수 앱을 찾습니다.
1. **함수 앱** 페이지에서 함수를 선택합니다. 
1. 다음 그림에 표시된 것처럼 **함수 URL 가져오기** 를 선택합니다. 

    ![Azure 함수 URL](./media/extend-devtest-labs-azure-functions/function-url.png)
4. URL을 복사하고 저장합니다. 다른 Azure 함수에 대해서도 이러한 단계를 반복합니다. 

또한 서비스 주체에 대한 추가 정보(예: 애플리케이션 ID, 키 및 테넌트 ID)가 필요합니다.


### <a name="step-5--update-application-settings"></a>5단계: 애플리케이션 설정 업데이트
Visual Studio에서 Azure Function을 게시한 후 **작업** 아래에서 **Azure App Service 설정 편집** 을 선택합니다. 다음 애플리케이션 설정 업데이트(원격):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays(기본값은 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![애플리케이션 설정](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>6단계: Azure 함수 테스트
이 연습의 마지막 단계는 Azure 함수를 테스트하는 것입니다.  

1. 3단계에서 만든 함수 앱의 **UpdateInternalSupportPage** 함수로 이동합니다. 
1. 페이지의 오른쪽에서 **테스트** 를 선택합니다. 
1. 경로 속성(LABNAME, RESOURCEGROUPNAME 및 SUBSCRIPTIONID)에 입력합니다.
1. **실행** 을 선택하여 함수를 실행합니다.  

    이 함수는 지정된 랩의 내부 지원 페이지를 업데이트합니다. 또한 사용자가 다음 번에 함수를 직접 호출하는 단추도 포함되어 있습니다.

    ![함수 테스트](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>다음 단계
Azure Functions는 이미 기본 제공되는 것 외에도 DevTest Labs의 기능을 확장하고 고객이 자신의 팀에 고유한 요구 사항을 충족하도록 도울 수 있습니다. 이 패턴은 더 자세히 다룰 수 있도록 계속해서 확장할 수 있습니다.  DevTest Labs에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [DevTest Labs Enterprise 참조 아키텍처](devtest-lab-reference-architecture.md)
- [질문과 대답](devtest-lab-faq.yml)
- [DevTest Labs 확장](devtest-lab-guidance-scale.md)
- [PowerShell을 사용하여 DevTest Labs 자동화](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)
