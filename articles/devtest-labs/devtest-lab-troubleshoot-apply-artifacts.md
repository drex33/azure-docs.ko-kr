---
title: 아티팩트 문제 해결
description: Azure DevTest Labs 가상 머신에서 아티팩트를 적용할 때 발생하는 문제를 해결하는 방법을 알아봅니다.
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee5d056a092e6cfb632c65cc0876c9a1ef8988e6
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846656"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Azure DevTest Labs 가상 머신에서 아티팩트를 적용할 때 발생하는 문제 해결

가상 머신에서 아티팩트를 적용할 때 다양한 이유로 실패할 수 있습니다. 이 문서에서는 가능한 원인을 파악하는 데 도움이 되는 몇 가지 방법을 설명합니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure DTL(DevTest Labs) 전문가에게 문의할 수 있습니다. 또는 Azure 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 지원 받기를 선택합니다.   

> [!NOTE]
> 이 문서는 Windows 가상 머신 및 Windows가 아닌 가상 머신에 모두 적용됩니다. 이러한 머신에는 약간의 차이가 있지만 이 문서에서 명확하게 설명할 것입니다.

## <a name="quick-troubleshooting-steps"></a>빠른 문제 해결 단계
VM이 실행 중인지 확인합니다. DevTest Labs를 사용하려면 VM이 실행 중이어야 하며 [Microsoft Azure VM 에이전트(가상 머신 에이전트)](../virtual-machines/extensions/agent-windows.md)가 설치 및 준비되어 있어야 합니다.

> [!TIP]
> **Azure Portal** 에서 VM의 **아티팩트 관리** 페이지로 이동하여 VM에서 아티팩트를 적용할 준비가 되어 있는지 확인합니다. 해당 페이지의 맨 위에 메시지가 표시됩니다. 
> 
> **Azure PowerShell** 을 사용하여 GET 작업에서 확장하는 경우에만 반환되는 **canApplyArtifacts** 플래그를 검사합니다. 다음 예제 명령을 참조하세요.

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>문제 해결 방법 
DevTest Labs 및 Resource Manager 배포 모델을 사용하여 만든 VM에 발생한 문제는 다음 방법 중 하나로 해결할 수 있습니다.

- **Azure Portal** - 문제의 원인이 될 수 있는 요소에 대한 시각적 힌트를 신속하게 받아야 하는 경우에 유용합니다.
- **Azure PowerShell** - PowerShell 프롬프트에 익숙한 경우 Azure PowerShell cmdlet을 사용하여 DevTest Labs 리소스를 신속하게 쿼리합니다.

> [!TIP]
> VM 내 아티팩트 실행을 검토하는 방법에 대한 자세한 내용은 [랩에서 아티팩트 실패 진단](devtest-lab-troubleshoot-artifact-failure.md)을 참조하세요.

## <a name="symptoms-causes-and-potential-resolutions"></a>증상, 원인 및 잠재적 해결 방법 

### <a name="artifact-appears-to-stop-responding"></a>아티팩트가 응답하지 않는 것처럼 보임

미리 정의된 시간 제한 기간이 만료될 때까지 아티팩트가 응답하지 않는 것처럼 보이며 아티팩트가 **실패** 로 표시됩니다.

아티팩트가 응답을 중지한 것처럼 보이면 먼저 중지된 위치를 확인합니다. 아티팩트는 실행되는 동안 다음 단계 중 하나에서 차단될 수 있습니다.

- **초기 요청 중**. DevTest Labs는 Azure Resource Manager 템플릿을 만들어 CSE(사용자 지정 스크립트 확장) 사용을 요청합니다. 백그라운드에서 리소스 그룹 배포가 트리거됩니다. 이 수준에서 오류가 발생하는 경우 해당 VM에 대한 리소스 그룹의 **활동 로그** 에서 세부 정보를 얻을 수 있습니다.  
    - 활동 로그는 랩 VM 페이지 탐색 모음에서 액세스할 수 있습니다. 이 항목을 선택하면 **가상 머신에 아티팩트 적용**(아티팩트 적용 작업이 직접 트리거된 경우) 또는 **가상 머신 추가 또는 수정**(아티팩트 적용 작업이 VM 만들기 프로세스의 일부인 경우)에 대한 항목이 표시됩니다.
    - 해당 항목에서 오류를 찾습니다. 경우에 따라 오류에 태그가 지정되지 않으므로 각 항목을 조사해야 합니다.
    - 항목마다 세부 정보를 조사하는 경우 JSON 페이로드 콘텐츠를 검토해야 합니다. 해당 문서의 맨 아래에 오류가 표시될 수 있습니다.

- **아티팩트를 실행하려는 경우**. 네트워킹 또는 스토리지 문제 때문일 수 있습니다. 자세한 내용은 이 문서 뒷부분의 해당 섹션을 참조하세요. 스크립트 작성 방식 때문일 수도 있습니다. 예를 들면 다음과 같습니다.
    - PowerShell 스크립트에는 **필수 매개 변수** 가 있지만 사용자가 값을 비워 두도록 허용하거나 artifactfile.json 정의 파일에 속성 기본값이 없어 매개 변수 중 하나에서 값을 전달하는 데 실패하는 경우가 있습니다. 스크립트는 사용자 입력을 대기하고 있으므로 응답을 중지합니다.
    - PowerShell 스크립트를 실행의 일부로 사용하려면 **사용자 입력이 필요** 합니다. 스크립트는 사용자 개입 없이도 자동으로 작동하도록 작성되어야 합니다.

- **VM 에이전트가 준비되는 데 시간이 오래 걸림**. VM이 처음 시작되거나 사용자 지정 스크립트 확장이 아티팩트 적용 요청을 제공하기 위해 처음 설치될 때 VM은 VM 에이전트를 업그레이드하거나 VM 에이전트가 초기화될 때까지 기다려야 할 수 있습니다. VM 에이전트가 초기화하는 데 시간이 오래 걸리는 경우 사용하는 서비스가 있을 수 있습니다. 이 경우 문제 해결에 대한 자세한 내용은 [Azure 가상 머신 에이전트 개요](../virtual-machines/extensions/agent-windows.md)를 참조하세요.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>스크립트로 인해 아티팩트가 응답하지 않는 것처럼 보이는지 확인하려면

1. 해당 가상 머신에 로그인합니다.
2. 가상 머신에서 로컬로 스크립트를 복사하거나 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>` 아래의 가상 머신에서 스크립트를 찾습니다. 아티팩트 스크립트를 다운로드한 위치입니다.
3. 관리자 권한 명령 프롬프트를 사용하여 스크립트를 로컬로 실행하고 문제가 발생될 때 사용한 것과 동일한 매개 변수 값을 제공합니다.
4. 스크립트가 원치 않는 동작에서 발생하는지 확인합니다. 이 경우 아티팩트에 대한 업데이트를 요청하거나(퍼블릭 리포지토리의 경우) 직접 수정합니다(프라이빗 리포지토리의 경우).

> [!TIP]
> [퍼블릭 리포지토리](https://github.com/Azure/azure-devtestlab)에서 호스트되는 아티팩트 관련 문제를 해결하고 검토 및 승인을 위해 변경 내용을 제출할 수 있습니다. [README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) 문서의 **기여** 섹션을 참조하세요.
> 
> 고유한 아티팩트 작성에 대한 자세한 내용은 [AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) 문서를 참조하세요.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>VM 에이전트로 인해 아티팩트가 응답하지 않는 것처럼 보이는지 확인하려면
1. 해당 가상 머신에 로그인합니다.
2. 파일 탐색기를 사용하여 **C:\WindowsAzure\logs** 로 이동합니다.
3. **WaAppAgent.log** 파일을 찾아 엽니다.
4. VM 에이전트가 시작되는 시기와 초기화를 완료할 때를 표시하는 항목을 찾습니다. 첫 번째 전송된 하트비트입니다. 최신 항목 또는 특히 문제가 발생한 기간의 항목을 찾는 것이 좋습니다.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    이 예제에서는 하트비트 전송으로 인해 VM 에이전트 시작 시간이 10분 20초가 걸렸습니다. 이 경우 원인은 OOBE 서비스가 시작하는 데 시간이 오래 걸렸기 때문입니다.

> [!TIP]
> Azure 확장에 대한 일반적인 내용은 [Azure 가상 머신 확장 및 기능](../virtual-machines/extensions/overview.md)을 참조하세요.

## <a name="storage-errors"></a>스토리지 오류
DevTest Labs를 사용하려면 아티팩트를 캐시하도록 만들어진 랩의 스토리지 계정에 액세스해야 합니다. DevTest Labs에서 아티팩트를 적용하는 경우 구성된 리포지토리에서 아티팩트 구성과 해당 파일을 읽습니다. 기본적으로 DevTest Labs는 **퍼블릭 아티팩트 리포지토리** 에 대한 액세스를 구성합니다.

VM이 구성된 방법에 따라 이 리포지토리에 직접 액세스하지 못할 수도 있습니다. 의도적으로 DevTest Labs는 랩이 처음 초기화할 때 생성된 스토리지 계정에 아티팩트를 캐시합니다.

VM에서 Azure Storage 서비스로의 트래픽이 차단되는 경우와 같이 이 스토리지 계정에 대한 액세스가 어떠한 방식으로든 차단되면 다음과 유사한 오류가 표시될 수 있습니다.

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

위의 오류는 **아티팩트 관리** 에 있는 **아티팩트 결과** 페이지의 **배포 메시지** 섹션에 표시됩니다. 또한 해당 가상 머신의 리소스 그룹 아래에 있는 **활동 로그** 에도 표시됩니다.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Azure Storage 서비스에 대한 통신이 차단되지 않도록 하려면

- **추가된 NSG(네트워크 보안 그룹) 확인**. 모든 가상 네트워크에서 NSG가 자동으로 구성되는 구독 정책이 추가되었을 수 있습니다. 또한 랩의 기본 가상 네트워크(사용되는 경우) 또는 랩에서 구성된 다른 가상 네트워크(VM 생성에 사용됨)에도 영향을 줍니다.

- **기본 랩의 스토리지 계정 확인**. 이 계정은 랩을 만들 때 만든 첫 번째 스토리지 계정이며 이름은 일반적으로 문자 “a”로 시작하고 여러 자리 숫자로 끝납니다. 즉, a\<labname\>#입니다.
    1. 랩의 리소스 그룹으로 이동합니다.
    2. 이름이 규칙과 일치하며 종류가 **스토리지 계정** 인 리소스를 찾습니다.
    3. **방화벽 및 가상 네트워크** 라는 스토리지 계정 페이지로 이동합니다.
    4. **모든 네트워크** 로 설정되었는지 확인합니다. **선택한 네트워크** 옵션이 선택되어 있는 경우 VM을 만드는 데 사용한 랩의 가상 네트워크가 목록에 추가되어 있는지 확인합니다.

문제 해결에 대한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../storage/common/storage-network-security.md)을 참조하세요.

> [!TIP]
> **네트워크 보안 그룹 규칙을 확인합니다**. [IP 흐름 확인](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify)을 사용하여 네트워크 보안 그룹의 규칙이 가상 머신으로 들어가고 나가는 트래픽을 차단하는지를 확인합니다. 효과적인 보안 그룹 규칙을 검토하여 인바운드 **허용** NSG 규칙이 있는지 확인합니다. 자세한 내용은 [효과적인 보안 규칙을 사용하여 VM 트래픽 흐름 문제 해결](../virtual-network/diagnose-network-traffic-filter-problem.md)을 참조하세요.

## <a name="other-sources-of-error"></a>기타 오류 원인
다른 자주 발생할 수 없는 오류 원인이 있습니다. 각 항목을 평가하여 해당 사례에 적용되는지 확인해야 합니다. 그 중 하나는 다음과 같습니다. 

- **프라이빗 리포지토리의 개인용 액세스 토큰 만료**. 만료되면 아티팩트는 나열되지 않으며 리포지토리의 아티팩트 를 참조하는 스크립트는 실패합니다.

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시 되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 사용해 보세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가의 답변을 얻습니다.
* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.

