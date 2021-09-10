---
title: Azure에 하이브리드 머신을 대규모로 연결
description: 이 문서에서는 서비스 주체를 사용하여 Azure Arc 지원 서버를 통해 Azure에 머신을 연결하는 방법을 알아봅니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fb78440bf03970616d3d608dcea2de1dc86681bd
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567956"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Azure에 하이브리드 머신을 대규모로 연결

요구 사항에 따라 다양한 옵션을 유연하게 사용하여 사용자 환경에서 여러 Windows 또는 Linux 머신에 Azure Arc 지원 서버를 사용할 수 있습니다. 제공하는 템플릿 스크립트를 사용하여 Azure Arc 연결 설정 등 설치의 모든 단계를 자동화할 수 있습니다. 그러나 대상 머신 및 Azure에서 관리자 권한이 있는 계정을 사용하여 이 스크립트를 대화형으로 실행해야 합니다.

Azure Arc 지원 서버에 머신을 연결하려면 권한 있는 ID를 사용하는 대신 Azure Active Directory [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)를 사용하여 [머신을 대화형으로 연결](onboard-portal.md)하면 됩니다. 서비스 주체는 `azcmagent` 명령을 사용하여 Azure에 머신을 연결하는 데 필요한 최소한의 권한만 부여되는 특별히 한정된 관리 ID입니다. 이는 테넌트 관리자와 같은 더 높은 권한의 계정을 사용하는 것보다 안전하며, 액세스 제어 보안 모범 사례를 준수하는 것입니다. 서비스 주체는 온보딩하는 동안에만 사용되며 다른 용도에는 사용되지 않습니다.  

Connected Machine 에이전트를 설치하고 구성하는 설치 메서드는 사용하는 자동화 메서드에서 머신에 대한 관리자 권한을 요구합니다. Linux에서는 루트 계정을 사용하고 Windows에서는 로컬 관리자 그룹의 멤버여야 합니다.

시작하려면 먼저 [사전 요구 사항](agent-overview.md#prerequisites)을 검토하고 구독 및 리소스에서 요구 사항을 충족하는지 확인해야 합니다. 지원되는 지역 및 기타 관련 고려 사항에 대한 자세한 내용은 [Azure 지원 지역](overview.md#supported-regions)을 참조하세요. 또한 [확장 계획 지침](plan-at-scale-deployment.md)을 검토하여 설계 및 배포 기준뿐만 아니라 관리 및 모니터링 권장 사항을 파악합니다.  

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>대규모 온보딩을 위한 서비스 주체 만들기

[Azure PowerShell](/powershell/azure/install-az-ps)을 사용하여 [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) cmdlet으로 서비스 주체를 만들 수 있습니다. 또는 [Azure Portal을 사용하여 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 아래에 나열된 단계를 수행하여 이 작업을 완료할 수 있습니다.

> [!NOTE]
> 서비스 주체를 만들기 전에 계정이 온보딩에 사용할 구독의 **소유자** 또는 **사용자 액세스 관리자** 역할의 구성원이어야 합니다. 역할 할당을 구성할 수 있는 충분한 권한이 없는 경우 서비스 주체가 생성될 수 있지만 머신을 온보딩할 수 없습니다.
>

PowerShell을 사용하여 서비스 주체를 만들려면 다음 단계를 수행합니다.

1. 다음 명령을 실행합니다. [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet의 출력을 변수에 저장해야 합니다. 그렇지 않으면 이후 단계에 필요한 암호를 검색할 수 없습니다.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. `$sp` 변수에 저장된 암호를 검색하려면 다음 명령을 실행합니다.

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 출력에서 **암호** 필드 아래에 있는 암호 값을 찾아 복사합니다. 또한 **ApplicationId** 필드에서 값을 찾아 복사합니다. 나중에 사용할 수 있도록 안전한 장소에 보관합니다. 서비스 주체 암호를 잊어버린 경우 [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet을 사용하여 재설정할 수 있습니다.

다음 속성의 값은 `azcmagent`로 전달되는 매개 변수와 함께 사용됩니다.

* **ApplicationId** 속성의 값은 `--service-principal-id` 매개 변수 값에 사용됩니다.
* **암호** 속성의 값은 에이전트 연결에 사용되는 `--service-principal-secret` 매개 변수에 사용됩니다.

> [!NOTE]
> **Id** 속성이 아닌 서비스 주체 **ApplicationId** 속성을 사용해야 합니다.
>

**Azure Connected Machine 온보딩** 역할은 머신을 온보딩하는 데 필요한 사용 권한만 포함합니다. 해당 범위에 리소스 그룹 또는 구독을 포함할 수 있도록 서비스 주체 권한을 할당할 수 있습니다. 역할 할당을 추가하려면 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)이나 [Azure CLI를 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-cli.md)을 참조하세요.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Azure Portal에서 설치 스크립트 생성

다운로드 및 설치를 자동화하고 Azure Arc와의 연결을 설정하는 스크립트는 Azure Portal에서 사용할 수 있습니다. 프로세스를 완료하려면 다음 단계를 수행합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

1. **서버 - Azure Arc** 페이지에서 왼쪽 위에 있는 **추가** 를 선택합니다.

1. **메서드 선택** 페이지에서 **여러 서버 추가** 타일을 선택한 다음, **스크립트 생성** 을 선택합니다.

1. **스크립트 생성** 페이지에서 머신을 Azure 내에서 관리하려는 구독 및 리소스 그룹을 선택합니다. 머신 메타데이터를 저장할 Azure 위치를 선택합니다. 이 위치는 리소스 그룹의 위치와 같을 수도 있고 다를 수도 있습니다.

1. **필수 구성 요소** 페이지에서 정보를 검토한 후, **다음: 리소스 세부 정보** 를 선택합니다.

1. **리소스 세부 정보** 페이지에서 다음을 제공합니다.

    1. **리소스 그룹** 드롭다운 목록에서 머신을 관리할 리소스 그룹을 선택합니다.
    1. **지역** 드롭다운 목록에서 서버 메타데이터를 저장할 Azure 지역을 선택합니다.
    1. **운영 체제** 드롭다운 목록에서 스크립트가 실행되도록 구성된 운영 체제를 선택합니다.
    1. 머신이 인터넷에 연결하기 위해 프록시 서버를 통해 통신하는 경우 프록시 IP 주소 또는 머신에서 프록시 서버와 통신하는 데 사용할 이름과 포트 번호를 지정합니다. 이 구성을 사용하여 에이전트는 HTTP 프로토콜을 사용하는 프록시 서버를 통해 통신합니다. 해당 값을 `http://<proxyURL>:<proxyport>` 형식으로 입력합니다.
    1. **다음: 인증** 을 선택합니다.

1. **인증** 페이지의 **서비스 주체** 드롭다운 목록에서 **Arc-for-servers** 를 선택합니다.  그런 다음, **다음: 태그** 를 선택합니다.

1. **태그** 페이지에서 제안된 기본 **실제 위치 태그** 를 검토하고 값을 입력하거나 표준을 지원하는 **사용자 지정 태그** 를 하나 이상 지정합니다.

1. 완료되면 **다음: 스크립트 다운로드 및 실행** 을 선택합니다.

1. **스크립트 다운로드 및 실행** 페이지에서 요약 정보를 검토한 다음, **다운로드** 를 선택합니다. 그래도 변경해야 하는 경우 **이전** 을 선택합니다.

Windows의 경우 컴퓨터에 `OnboardingScript.ps1`을 저장하라는 메시지가, Linux의 경우 컴퓨터에 `OnboardingScript.sh`를 저장하라는 메시지가 표시됩니다.

## <a name="install-the-agent-and-connect-to-azure"></a>에이전트를 설치하고 Azure에 연결

이전에 만든 스크립트 템플릿을 사용하면 조직에서 선호하는 자동화 도구를 사용해 여러 하이브리드 Linux 및 Windows 머신에 Connected Machine 에이전트를 설치하고 구성할 수 있습니다. 스크립트는 [Azure Portal에서 Azure에 하이브리드 머신 연결](onboard-portal.md) 문서에서 설명된 단계와 비슷한 단계를 수행합니다. 차이점은 서비스 주체를 사용하여 `azcmagent` 명령으로 Azure Arc에 대한 연결을 설정하는 마지막 단계입니다.

다음은 `azcmagent` 명령이 서비스 주체에 사용하도록 구성하는 설정입니다.

* `service-principal-id`: 서비스 주체의 애플리케이션 ID를 나타내는 고유 식별자(GUID)입니다.
* `service-principal-secret` | 서비스 주체 암호입니다.
* `tenant-id` : Azure AD의 전용 인스턴스를 나타내는 고유 식별자(GUID)입니다.
* `subscription-id` : 머신을 만들려는 Azure 구독의 구독 ID(GUID)입니다.
* `resource-group` : 연결된 머신을 배치할 리소스 그룹 이름입니다.
* `location` : [지원되는 Azure 지역](overview.md#supported-regions)을 참조하세요. 이 위치는 리소스 그룹의 위치와 같을 수도 있고 다를 수도 있습니다.
* `resource-name` : (*선택 사항*) 온-프레미스 머신의 Azure 리소스 표현에 사용됩니다. 이 값을 지정하지 않으면 머신 호스트 이름이 사용됩니다.

[Azcmagent 참조](./manage-agent.md)를 검토하여 `azcmagent` 명령줄 도구에 대해 자세히 알아볼 수 있습니다.

>[!NOTE]
>Windows PowerShell 스크립트는 Windows PowerShell 64비트 버전에서만 실행될 수 있습니다.
>

에이전트가 설치되고 Azure Arc 사용 서버에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 머신을 확인합니다.

![성공적인 서버 연결](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

- 문제 해결 정보는 [Connected Machine 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

- [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.

- [Azure Policy](../../governance/policy/overview.md)를 사용하여 VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md)과 머신을 관리하는 방법을 알아봅니다. 머신이 예상되는 Log Analytics 작업 영역에 보고되는지, [VM 인사이트](../../azure-monitor/vm/vminsights-enable-policy.md)로 모니터링 사용하는지 그리고 기타 등등을 확인하세요.
