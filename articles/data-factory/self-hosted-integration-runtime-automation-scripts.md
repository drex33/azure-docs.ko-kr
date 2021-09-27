---
title: 로컬 PowerShell 스크립트를 사용하여 자체 호스팅 통합 런타임 설치 자동화
description: 로컬 머신에서 자체 호스팅 통합 런타임의 설치를 자동화합니다.
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: a1a05322a43f791ad9058659ea6e0cd53839196c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814756"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>로컬 PowerShell 스크립트를 사용하여 자체 호스팅 통합 런타임 설치 자동화
로컬 머신(Resource Manager 템플릿을 사용할 수 있는 Azure VM 제외)에 자체 호스팅 통합 런타임의 설치를 자동화하려면 로컬 PowerShell 스크립트를 사용할 수 있습니다. 이 문서에서는 사용할 수 있는 두 가지 스크립트를 소개합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 로컬 머신에서 PowerShell을 시작합니다. 스크립트를 실행하려면 **관리자 권한으로 실행** 을 선택해야 합니다.
* 자체 호스팅 통합 런타임 소프트웨어를 [다운로드](https://www.microsoft.com/download/details.aspx?id=39717)합니다. 다운로드한 파일이 있는 경로를 복사합니다. 
* 자체 호스팅 통합 런타임을 등록하려면 **인증 키** 도 필요합니다.
* 수동 업데이트를 자동화하려면 미리 구성된 자체 호스팅 통합 런타임이 필요합니다.

## <a name="scripts-introduction"></a>스크립트 소개 

> [!NOTE]
> 이러한 스크립트는 자체 호스팅 통합 런타임에서 [설명된 명령줄 유틸리티](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell)를 사용하여 만들어집니다. 필요한 경우 이러한 스크립트를 적절하게 사용자 지정하여 자동화 요구 사항을 충족할 수 있습니다.
> 스크립트는 노드별로 적용해야 하므로 고가용성 설정(2개 이상의 노드)의 경우 모든 노드에서 실행해야 합니다.

* 설치 자동화의 경우 **[InstallGatewayOnLocalMachine.ps1](https://github.com/Azure/Azure-DataFactory/blob/main/SamplesV2/SelfHostedIntegrationRuntime/AutomationScripts/InstallGatewayOnLocalMachine.ps1)** 을 사용하여 새 자체 호스팅 통합 런타임 노드를 설치 및 등록 - 이 스크립트를 사용하여 자체 호스팅 통합 런타임 노드를 설치하고 인증 키로 등록할 수 있습니다. 이 스크립트는 두 개의 인수를 허용하며, **첫 번째** 인수는 로컬 디스크에서 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)의 위치를 지정하고, **두 번째** 인수는 **인증 키**(자체 호스팅 IR 노드 등록용)를 지정합니다.

* 수동 업데이트 자동화의 경우 **[script-update-gateway.ps1](https://github.com/Azure/Azure-DataFactory/blob/main/SamplesV2/SelfHostedIntegrationRuntime/AutomationScripts/script-update-gateway.ps1)** 을 사용하여 자체 호스팅 IR 노드를 특정 버전이나 최신 버전으로 업데이트 - 이 기능은 자동 업데이트를 해제했거나 업데이트를 보다 세밀하게 제어하려는 경우에도 지원됩니다. 이 스크립트를 사용하여 자체 호스팅 통합 런타임 노드를 최신 버전이나 지정된 상위 버전으로 업데이트할 수 있습니다(다운그레이드는 작동하지 않음). 버전 번호를 지정하는 인수를 허용합니다(예: -version 3.13.6942.1). 버전을 지정하지 않으면 항상 자체 호스팅 IR을 [downloads](https://www.microsoft.com/download/details.aspx?id=39717)에 있는 최신 버전으로 업데이트합니다.
    > [!NOTE]
    > 최신 3개 버전만 지정할 수 있습니다. 기존 노드를 최신 버전으로 업데이트하는 데 사용하는 것이 가장 좋습니다. **여기서는 자체 호스트 IR이 등록되어 있는 것으로 가정합니다**. 

## <a name="usage-examples"></a>사용 예

### <a name="for-automating-setup"></a>설치 자동화의 경우
1. [여기](https://www.microsoft.com/download/details.aspx?id=39717)에서 자체 호스팅 IR을 다운로드합니다. 
1. 위의 다운로드한 SHIR MSI(설치 파일)가 있는 경로를 지정합니다. 예를 들어 경로가 *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi* 인 경우 아래 PowerShell 명령줄 예제를 사용하여 이 작업을 수행할 수 있습니다.

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > IR를 등록하려면 [key]를 인증 키로 바꿉니다.
    > "username"을 사용자 이름으로 바꿉니다.
    > 스크립트를 실행할 때 "InstallGatewayOnLocalMachine.ps1" 파일의 위치를 지정합니다. 이 예제에서는 바탕 화면에 저장했습니다.

1. 컴퓨터에 사전 설치된 자체 호스팅 IR이 있으면 스크립트가 자동으로 해당 IR을 제거하고 새 IR을 구성합니다. 다음과 같은 팝업 창이 표시됩니다. :::image type="content" source="media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png" alt-text="configure integration runtime":::

1. 설치 및 키 등록이 완료되면 로컬 PowerShell에 게이트웨이 설치 성공 및 게이트웨이 등록 성공 결과가 표시됩니다.
        [:::image type="content" source="media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox" alt-text="스크립트 1 실행 결과] (미디어/자체 호스팅-통합 런타임-자동화-스크립트/script-1-run-result.png)":::

### <a name="for-automating-manual-updates"></a>수동 업데이트 자동화의 경우
이 스크립트는 최신 자체 호스팅 통합 런타임을 업데이트/설치 + 등록하는 데 사용됩니다. 이 스크립트는 다음 단계를 실행합니다.
1. 현재 자체 호스팅 IR 버전을 확인합니다.
2. 인수에서 최신 버전 또는 지정된 버전을 가져옵니다.
3. 현재 버전보다 최신 버전이 있는 경우
    * 자체 호스팅 IR msi를 다운로드합니다.
    * 업그레이드합니다.

아래 명령줄 예제에 따라 이 스크립트를 사용할 수 있습니다.
* 최신 게이트웨이를 다운로드하여 설치합니다.

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* 지정된 버전의 게이트웨이를 다운로드하여 설치합니다.
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   현재 버전이 이미 최신 버전이면 다음과 같이 업데이트가 필요하지 않음을 알리는 결과가 표시됩니다.   
    [:::image type="content" source="media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox" alt-text="스크립트 2 실행 결과] (미디어/자체 호스팅-통합 런타임-자동화-스크립트/script-2-run-result.png)":::