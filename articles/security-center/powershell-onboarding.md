---
title: PowerShell을 사용하여 Microsoft Defender for Cloud에 온보딩
description: 이 문서에서는 PowerShell cmdlet을 사용하여 Microsoft Defender for Cloud를 사용하도록 설정하는 과정을 안내합니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/20/2021
ms.author: memildin
ms.openlocfilehash: 5e2524121aea09142a04b06b5ee6736e977b2b94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452797"
---
# <a name="automate-onboarding-of-microsoft-defender-for-cloud-using-powershell"></a>PowerShell을 사용하여 Microsoft Defender for Cloud의 온보딩 자동화

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud PowerShell 모듈을 사용하여 프로그래매틱한 방식으로 Azure 워크로드를 보호할 수 있습니다.
PowerShell을 사용하면 작업을 자동화하고 수동 작업에 내재된 사용자 오류를 피할 수 있습니다. 이는 특히 모두 처음부터 보호해야 하는 수십만 개의 리소스가 있는 수십 개의 구독이 포함된 대규모 배포에 유용합니다.

PowerShell을 사용하여 Microsoft Defender for Cloud를 온보딩하면 Azure 리소스의 온보딩 및 관리를 프로그래매틱한 방식으로 자동화하고 필요한 보안 컨트롤을 추가할 수 있습니다.

이 문서에서는 전체 구독에서 Defender for Cloud를 배포하기 위해 환경에서 수정 및 사용될 수 있는 샘플 PowerShell 스크립트를 제공합니다. 

이 예제에서는 ID가 d07c0080-170c-4c24-861d-9c817742786c인 구독에서 Defender for Cloud를 사용하도록 설정하고 지능형 위협 보호 및 검색 기능을 제공하는 Microsoft Defender for Cloud의 향상된 보안 기능을 사용하도록 설정하여 높은 수준의 보호를 제공합니다.

1. [Microsoft Defender에서 향상된 보안](enable-enhanced-security.md)을 사용하도록 설정합니다.
 
2. Log Analytics 에이전트가 구독과 연결된 VM에서 수집하는 데이터를 보낼 Log Analytics 작업 영역을 설정합니다(이 예제에서는 기존 사용자 정의 작업 영역(myWorkspace)).

3. [Log Analytics 에이전트를 배포](enable-data-collection.md#auto-provision-mma)하는 Activate Defender for Cloudr의 자동 에이전트 프로비저닝을 활성화합니다.

5. 조직의 [CISO를 Defender for Cloud 경고 및 주요 이벤트의 보안 연락처](configure-email-notifications.md)로 설정합니다.

6. Defender for Cloud의 [기본 보안 정책](tutorial-security-policy.md)을 할당합니다.

## <a name="prerequisites"></a>사전 요구 사항

Defender for Cloud cmdlet을 실행하기 전에 다음 단계를 수행해야 합니다.

1. 관리자 권한으로 PowerShell을 실행합니다.

1. PowerShell에서 다음 명령을 실행합니다.
      
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy AllSigned
    ```

    ```powershell
    Install-Module -Name Az.Security -Force
    ```

## <a name="onboard-defender-for-cloud-using-powershell"></a>PowerShell을 사용하여 Defender for Cloud 온보딩

1. Defender for Cloud 리소스 공급자에 구독을 등록합니다.

    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```

    ```powershell
    Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'
    ```

1. 선택 사항: 구독의 적용 범위 수준(Microsoft Defender for Cloud의 향상된 보안 기능 활성화/비활성화)을 설정합니다. 정의되지 않은 경우 이러한 기능은 비활성됩니다.

    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```

    ```powershell
    Set-AzSecurityPricing -Name "default" -PricingTier "Standard"
    ```

1. 에이전트가 보고할 Log Analytics 작업 영역을 구성합니다. 구독의 VM이 보고할 기존에 만든 Log Analytics 작업 영역이 있어야 합니다. 동일한 작업 영역에 보고할 여러 구독을 정의할 수 있습니다. 정의되지 않은 경우 기본 작업 영역이 사용됩니다.

    ```powershell
    Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"
    ```

1. Azure VM에서 Log Analytics 에이전트 설치를 자동 프로비저닝합니다.
    
    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```
    
    ```powershell
    Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision
    ```

    > [!NOTE]
    > 자동 프로비저닝을 사용하도록 설정하여 Azure 가상 머신이 Microsoft Defender for Cloud를 통해 자동으로 보호되도록 하는 것이 좋습니다.
    >

1. 선택 사항: Defender for Cloud에서 생성한 경고 및 알림의 수신자로 사용되는 온보딩한 구독의 [보안 연락처 세부 정보를 정의](configure-email-notifications.md)하는 것이 좋습니다.

    ```powershell
    Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -AlertAdmin -NotifyOnAlert
    ```

1. 기본 Defender for Cloud 정책 이니셔티브를 할당합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
    ```

    ```powershell
    $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Defender for Cloud Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'
    ```

PowerShell을 사용하여 Microsoft Defender for Cloud를 성공적으로 온보딩하였습니다.

이제 이러한 PowerShell cmdlet을 자동화 스크립트와 함께 사용하여 구독 및 리소스를 프로그래밍 방식으로 반복할 수 있습니다. 이렇게 하면 시간이 절약되고 사용자 오류 가능성이 줄어듭니다. 이 [샘플 스크립트](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1)를 참조로 사용할 수 있습니다.




## <a name="see-also"></a>참고 항목
PowerShell을 사용하여 Defender for Cloud에 대한 온보딩을 자동화하는 방법을 자세히 알아보려면 다음 문서를 참조하세요.

* [Az.Security](/powershell/module/az.security)

Defender for Cloud에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Microsoft Defender for Cloud에서 보안 정책 설정](tutorial-security-policy.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Microsoft Defender for Cloud에서 보안 경고 관리 및 대응](managing-and-responding-alerts.md)-- 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
