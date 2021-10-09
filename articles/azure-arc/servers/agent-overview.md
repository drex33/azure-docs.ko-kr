---
title: Connected Machine 에이전트 개요
description: 이 문서에서는 하이브리드 환경에서 호스트되는 가상 머신의 모니터링을 지원하는 Azure Arc 지원 서버 에이전트에 대한 자세한 개요를 제공합니다.
ms.date: 09/30/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e4251ceafd2ab06afc43f8c3ba84f167219d7e14
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713167"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Azure Arc 지원 서버 에이전트 개요

Azure Arc 지원 서버의 Connected Machine 에이전트를 사용하면 Azure 외부에서 회사 네트워크 또는 다른 클라우드 공급자에 호스트되는 Windows 및 Linux 머신을 관리할 수 있습니다. 이 문서에서는 에이전트, 시스템 및 네트워크 요구 사항과 다양한 배포 모델을 상세히 살펴봅니다.

>[!NOTE]
> AMA([Azure Monitor 에이전트](../../azure-monitor/agents/azure-monitor-agent-overview.md))는 Connected Machine 에이전트를 대체하지 않습니다. Azure Monitor 에이전트는 Windows 및 Linux 머신 모두에 대한 Log Analytics 에이전트, 진단 확장 및 Telegraf 에이전트를 대체합니다. 자세한 내용은 새 에이전트에 대한 Azure Monitor 설명서를 검토하세요.

## <a name="agent-component-details"></a>에이전트 구성 요소 세부 정보

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="Azure Arc 사용 서버 에이전트 개요." border="false":::

Azure Connected Machine 에이전트 패키지에는 여러 개의 논리적 구성 요소가 함께 포함되어 있습니다.

* 하이브리드 인스턴스 메타데이터 서비스(HIMDS)는 Azure 및 연결된 머신의 Azure ID에 대한 연결을 관리합니다.

* 게스트 구성 에이전트는 머신에서 필요한 정책을 준수하는지 평가하고 규정 준수를 시행하는 등의 기능을 제공합니다.

    연결되지 않은 머신에 대한 Azure Policy [게스트 구성](../../governance/policy/concepts/guest-configuration.md)과 관련된 다음 동작에 유의하세요.

    * 연결이 끊어진 컴퓨터를 대상으로 하는 Azure Policy 할당은 영향을 받지 않습니다.
    * 게스트 할당은 14일 동안 로컬에 저장됩니다. 14일 기간 내에 연결된 컴퓨터 에이전트가 서비스에 다시 연결하는 경우 정책 할당이 다시 적용됩니다.
    * 할당은 14일 후에 삭제되며 14일 기간 후에는 컴퓨터에 재할당되지 않습니다.

* 확장 에이전트는 설치, 제거 및 업그레이드를 포함하여 VM 확장을 관리합니다. 확장은 Azure에서 다운로드되고 Windows의 `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` 폴더와 Linux의 `/opt/GC_Ext/downloads`에 복사됩니다. Windows에서는 확장이 경로 `%SystemDrive%\Packages\Plugins\<extension>`에 설치되고 Linux에서는 확장이 `/var/lib/waagent/<extension>`에 설치됩니다.

## <a name="instance-metadata"></a>인스턴스 메타데이터

연결 된 컴퓨터에 대 한 메타 데이터 정보는 연결 된 컴퓨터 에이전트가 Azure Arc 사용 서버에 등록 된 후 수집 됩니다. 특히:

* 운영 체제 이름, 형식 및 버전
* 컴퓨터 이름
* 컴퓨터 제조업체 및 모델
* 컴퓨터 FQDN(정규화된 도메인 이름)
* 도메인 이름(Active Directory 도메인에 조인된 경우)
* Connected Machine 에이전트 버전
* Active Directory 및 DNS FQDN(정규화된 도메인 이름)
* UUID(BIOS ID)
* Connected Machine 에이전트 하트비트
* Connected Machine 에이전트 버전
* 관리 ID에 대한 공개 키
* 정책 준수 상태 및 세부 정보(게스트 구성 정책을 사용하는 경우)
* SQL Server 설치(부울 값)
* 클러스터 리소스 ID(Azure Stack HCI 노드의 경우) 

다음 메타데이터 정보는 Azure의 에이전트에서 요청합니다.

* 리소스 위치(지역)
* 가상 머신 ID
* 태그들
* Azure Active Directory 관리 ID 인증서
* 게스트 구성 정책 할당
* 확장 요청 - 설치, 업데이트 및 삭제

## <a name="download-agents"></a>에이전트 다운로드

Windows 및 Linux용 Azure Connected Machine 에이전트 패키지는 아래에 나열된 위치에서 다운로드할 수 있습니다.

* Microsoft 다운로드 센터에서 [Windows 에이전트 Windows Installer 패키지](https://aka.ms/AzureConnectedMachineAgent)를 다운로드합니다.

* Linux 에이전트 패키지는 선호하는 배포 패키지 형식(.RPM 또는 .DEB)을 사용하여 Microsoft [패키지 리포지토리](https://packages.microsoft.com/)를 통해 배포됩니다.

Windows 및 Linux용 Azure Connected Machine 에이전트는 요구 사항에 따라 수동 또는 자동으로 최신 릴리스로 업그레이드할 수 있습니다. 자세한 내용은 [여기](manage-agent.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="supported-environments"></a>지원되는 환경

Azure Arc 사용 서버는 Azure *외부* 에서 호스트 되는 모든 물리적 서버 및 가상 컴퓨터에 연결 된 컴퓨터 에이전트를 설치 하도록 지원 합니다. VMware, Azure Stack HCI 및 기타 클라우드 환경과 같은 플랫폼에서 실행 되는 가상 컴퓨터를 포함 합니다. Azure Arc 사용 서버는 azure Vm에서 실행 되는 가상 컴퓨터 Azure Stack Azure Stack 또는 azure에서 실행 중인 가상 컴퓨터에 에이전트를 설치 하는 것을 지원 하지 않습니다.

### <a name="supported-operating-systems"></a>지원되는 운영 체제

Azure Connected Machine 에이전트를 공식적으로 지원하는 Windows 및 Linux 운영 체제 버전은 다음과 같습니다.

- Windows server 2008 r2 SP1, Windows Server 2012 R2, 2016, 2019 및 2022 (server Core 포함)
- Ubuntu 16.04, 18.04 및 20.04 LTS(x64)
- CentOS Linux 7 및 8(x64)
- SLES(SUSE Linux Enterprise Server) 12 및 15(x64)
- RHEL(Red Hat Enterprise Linux) 7 및 8(x64)
- Amazon Linux 2(x64)
- Oracle Linux 7

> [!WARNING]
> Linux 호스트 이름 또는 Windows 컴퓨터 이름은 이름에 예약된 단어나 상표 중 하나를 사용할 수 없습니다. 그렇지 않으면 Azure에 연결된 컴퓨터를 등록하려고 하면 실패합니다. 예약된 단어 목록은 [예약된 리소스 이름 오류 해결](../../azure-resource-manager/templates/error-reserved-resource-name.md)을 참조하세요.

> [!NOTE]
> Azure Arc 사용 서버는 Amazon Linux를 지원 하지만 다음은이 배포판를 지원 하지 않습니다.
> * Azure Monitor VM insights에서 사용 하는 종속성 에이전트
> * Azure Automation 업데이트 관리

### <a name="software-requirements"></a>소프트웨어 요구 사항

* NET Framework 4.6 이상이 필요합니다. [.NET Framework를 다운로드](/dotnet/framework/install/guide-for-developers)합니다.
* Windows PowerShell 5.1이 필요합니다. [Windows Management Framework 5.1을 다운로드](https://www.microsoft.com/download/details.aspx?id=54616)합니다.

### <a name="required-permissions"></a>필요한 사용 권한

* 머신을 온보딩하려면 리소스 그룹에서 **Azure Connected Machine 온보딩** 또는 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할이 있는 구성원이어야 합니다.

* 머신을 읽고, 수정하고, 삭제하려면 리소스 그룹에서 **Azure Connected Machine 리소스 관리자** 역할의 구성원이어야 합니다.

* **스크립트 생성** 메서드를 사용할 때 드롭다운 목록에서 리소스 그룹을 선택하려면 최소한 해당 리소스 그룹에 대한 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할의 구성원이어야 합니다.

### <a name="azure-subscription-and-service-limits"></a>Azure 구독 및 서비스 한도

서버용 Azure Arc 지원 서버를 사용하여 머신을 구성하기 전에, Azure Resource Manager [구독 한도](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 및 [리소스 그룹 한도](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)를 검토하여 연결할 머신 수를 계획합니다.

Azure Arc 지원 서버는 리소스 그룹에서 최대 5,000개의 머신 인스턴스를 지원합니다.

### <a name="register-azure-resource-providers"></a>Azure 리소스 공급자 등록

Azure Arc 사용 서버는이 서비스를 사용 하기 위해 구독의 다음 Azure 리소스 공급자에 종속 됩니다.

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

리소스 공급자가 등록되어 있지 않으면 다음 명령을 사용하여 등록할 수 있습니다.

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

[Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)의 단계에 따라 Azure Portal에서 리소스 공급자를 등록할 수도 있습니다.

### <a name="transport-layer-security-12-protocol"></a>전송 계층 보안 1.2 프로토콜

Azure로 전송되는 데이터의 보안을 보장하려면 TLS(전송 계층 보안) 1.2를 사용하도록 머신을 구성하는 것이 좋습니다. 이전 버전의 TLS/SSL(Secure Sockets Layer)은 취약한 것으로 나타났으며, 여전히 이전 버전과 호환되지만 **사용하지 않는 것이 좋습니다**.

|플랫폼/언어 | 지원 | 추가 정보 |
| --- | --- | --- |
|Linux | Linux 배포판은 TLS 1.2 지원에 대해 [OpenSSL](https://www.openssl.org)을 사용하는 경향이 있습니다. | [OpenSSL Changelog](https://www.openssl.org/news/changelog.html)를 확인하여 OpenSSL 버전이 지원되는지 확인합니다.|
| Windows Server 2012 R2 이상 | 지원됨, 기본적으로 활성화됩니다. | [기본 설정](/windows-server/security/tls/tls-registry-settings)을 여전히 사용하는지 확인하려면|

## <a name="networking-configuration"></a>네트워킹 구성

Linux 및 Windows용 Connected Machine 에이전트는 TCP 포트 443을 통해 안전하게 Azure Arc로 아웃바운드 통신을 수행합니다. 머신이 인터넷을 통해 통신하기 위해 방화벽이나 프록시 서버를 통해 연결해야 하는 경우 에이전트는 HTTP 프로토콜을 사용하는 대신 아웃바운드로 통신합니다. 트래픽이 이미 암호화되어 있기 때문에 프록시 서버를 사용해도 Connected Machine 에이전트가 더 안전해지지 않습니다.

공용 네트워크와 프록시 서버를 사용 하는 대신 azure Arc에 대 한 네트워크 연결을 더욱 안전 하 게 보호 하기 위해 [Azure Arc 개인 링크 범위](private-link-security.md) (미리 보기)를 구현할 수 있습니다.

> [!NOTE]
> Azure Arc 사용 서버는 연결 된 컴퓨터 에이전트에 대 한 프록시로 [Log Analytics 게이트웨이](../../azure-monitor/agents/gateway.md) 를 사용 하는 것을 지원 하지 않습니다.
>

방화벽 또는 프록시 서버가 아웃바운드 연결을 제한하는 경우 아래에 나열된 URL이 차단되지 않았는지 확인합니다. 에이전트가 서비스와 통신하는 데 필요한 IP 범위 또는 도메인 이름만 허용하는 경우 다음 서비스 태그와 URL에 대한 액세스도 허용해야 합니다.

서비스 태그:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure
* 스토리지

URL:

| 에이전트 리소스 | Description |
|---------|---------|
|`management.azure.com`|Azure 리소스 관리자|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |게스트 구성|
|`*.his.arc.azure.com`|하이브리드 ID 서비스|
|`*.blob.core.windows.net`|Azure Arc 사용 서버 확장에 대 한 다운로드 원본|

Preview 에이전트(버전 0.11 이하)에서도 다음 URL에 액세스할 수 있어야 합니다.

| 에이전트 리소스 | Description |
|---------|---------|
|`agentserviceapi.azure-automation.net`|게스트 구성|
|`*-agentservice-prod-1.azure-automation.net`|게스트 구성|

각 서비스 태그/지역의 IP 주소 목록은 JSON 파일 - [Azure IP 범위 및 서비스 태그 – 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조하세요. Microsoft는 각 Azure 서비스 및 여기에 사용되는 IP 범위를 포함하는 주간 업데이트를 게시합니다. JSON 파일의 이 정보는 각 서비스 태그에 해당하는 IP 범위의 현재 지정 시간 목록입니다. IP 주소는 변경될 수 있습니다. 방화벽 구성에 IP 주소 범위가 필요한 경우 모든 Azure 서비스에 대한 액세스를 허용하기 위해 **AzureCloud** 서비스 태그를 사용해야 합니다. 이러한 URL의 보안 모니터링 또는 검사를 해제하지 말고, 다른 인터넷 트래픽처럼 허용합니다.

자세한 내용은 [서비스 태그 개요](../../virtual-network/service-tags-overview.md)를 검토하세요.

## <a name="installation-and-configuration"></a>설치 및 구성

하이브리드 환경의 머신을 Azure에 직접 연결할 때 요구 사항에 따라 다양한 방법을 사용할 수 있습니다. 다음 표를 통해 조직에 가장 적합한 방법을 결정할 수 있습니다.

> [!IMPORTANT]
> Connected Machine 에이전트를 Azure Windows 가상 머신에 설치할 수 없습니다. 설치를 시도하면 이를 감지하고 롤백합니다.

| 방법 | Description |
|--------|-------------|
| 대화형 | [Azure Portal에서 머신 연결](onboard-portal.md)의 단계에 따라 머신 한 대 또는 약간의 머신에 에이전트를 수동으로 설치합니다.<br> Azure Portal에서 스크립트를 생성하고 머신에서 실행하여 에이전트의 설치 및 구성 단계를 자동화할 수 있습니다.|
| 대규모 | [서비스 주체를 사용하여 머신 연결](onboard-service-principal.md)의 지침에 따라 여러 머신의 에이전트를 설치하고 구성합니다.<br> 이 방법은 비 대화형으로 머신을 연결하는 서비스 주체를 만듭니다.|
| 대규모 | [Automation 업데이트 관리에서 Azure로 하이브리드 컴퓨터 커넥트](onboard-update-management-machines.md)메서드를 따라 여러 컴퓨터에 대 한 에이전트를 설치 하 고 구성 합니다.<br> 이 메서드는 서비스 주체를 만들고 Azure Automation 업데이트 관리를 사용 하 여 관리 되는 여러 컴퓨터에 대 한 에이전트를 설치 및 구성 하 여 비 대화형으로 컴퓨터를 연결 합니다. |
| 대규모 | [Windows PowerShell DSC 사용](onboard-dsc.md) 방법에 따라 여러 머신의 에이전트를 설치하고 구성합니다.<br> 이 메서드는 서비스 주체를 사용하여 머신을 비 대화형으로 PowerShell DSC와 연결합니다. |

## <a name="connected-machine-agent-technical-overview"></a>Connected Machine 에이전트 기술 개요

### <a name="windows-agent-installation-details"></a>Windows 에이전트 설치 세부 정보

다음 세 가지 방법 중 하나를 사용하여 Windows용 Connected Machine 에이전트를 설치할 수 있습니다.

* `AzureConnectedMachineAgent.msi` 파일을 두 번 클릭합니다.
* 명령 셸에서 `AzureConnectedMachineAgent.msi` Windows Installer 패키지를 실행하여 수동으로 설치합니다.
* PowerShell 세션에서 스크립팅된 메서드를 사용합니다.

Windows용 Connected Machine 에이전트를 설치하면 다음과 같은 시스템 차원 구성 변경 내용이 적용됩니다.

* 설치 중에 생성되는 설치 폴더는 다음과 같습니다.

    |폴더 |Description |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |에이전트 지원 파일이 포함되어 있는 기본 설치 경로입니다.|
    |%ProgramData%\AzureConnectedMachineAgent |에이전트 구성 파일이 포함되어 있습니다.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |가져온 토큰이 포함되어 있습니다.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |서비스 등록 정보를 기록하는 에이전트 구성 파일 `agentconfig.json`이 포함되어 있습니다.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | 게스트 구성 에이전트 파일을 포함하는 설치 경로입니다. |
    |%ProgramData%\GuestConfig |Azure의 (적용) 정책을 포함합니다.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | 확장은 Azure에서 다운로드되어 여기에 복사됩니다.|

* 에이전트를 설치하는 동안 대상 머신에 다음 Windows 서비스가 만들어집니다.

    |서비스 이름 |표시 이름 |프로세스 이름 |Description |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds |이 서비스는 Azure 인스턴스 메타데이터 서비스(IMDS)를 구현하여 Azure 및 연결된 머신의 Azure ID에 대한 연결을 관리합니다.|
    |GCArcService |게스트 구성 Arc 서비스 |gc_service |컴퓨터의 필요한 상태 구성을 모니터링합니다.|
    |ExtensionService |게스트 구성 확장 서비스 | gc_service |머신을 대상으로 하는 필수 확장을 설치합니다.|

* 에이전트 설치 중에 다음 환경 변수가 생성됩니다.

    |속성 |기본값 |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* 문제 해결에 사용할 수 있는 로그 파일이 여러 개 있습니다. 이 내용은 다음 표에 설명되어 있습니다.

    |로그 |Description |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |에이전트(HIMDS) 서비스의 세부 정보 및 Azure와의 상호 작용을 기록합니다.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |자세한 정보(-v) 인수를 사용하는 경우 azcmagent tool 명령의 출력이 포함됩니다.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |DSC 서비스 활동의 세부 정보,<br> 특히 HIMDS 서비스와 Azure Policy 간의 연결을 기록합니다.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |DSC 서비스 원격 분석 및 자세한 정보 로깅에 대한 세부 정보를 기록합니다.|
    |%ProgramData%\GuestConfig\ext_mgr_logs|확장 에이전트 구성 요소에 대한 세부 정보를 기록합니다.|
    |%ProgramData%\GuestConfig\extension_logs\<Extension>|설치된 확장의 세부 정보를 기록합니다.|

* 로컬 보안 그룹 **하이브리드 에이전트 확장 애플리케이션** 이 만들어집니다.

* 에이전트를 제거하는 동안 다음 아티팩트가 제거되지 않습니다.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent 및 하위 디렉터리
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux 에이전트 설치 세부 정보

Linux용 Connected Machine 에이전트는 Microsoft [패키지 리포지토리](https://packages.microsoft.com/)에서 호스팅되는 배포(.RPM 또는 .DEB)에 대한 기본 설정 패키지 형식으로 제공됩니다. 에이전트는 셸 스크립트 번들 [Install_linux_azcmagent.sh](https://aka.ms/azcmagent)를 사용하여 설치되고 구성됩니다.

Linux용 Connected Machine 에이전트를 설치하면 다음과 같은 시스템 차원 구성 변경 내용이 적용됩니다.

* 설치 중에 생성되는 설치 폴더는 다음과 같습니다.

    |폴더 |Description |
    |-------|------------|
    |/var/opt/azcmagent/ |에이전트 지원 파일이 포함되어 있는 기본 설치 경로입니다.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | 게스트 구성 에이전트 파일을 포함하는 설치 경로입니다.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |가져온 토큰이 포함되어 있습니다.|
    |/var/lib/GuestConfig |Azure의 (적용) 정책을 포함합니다.|
    |/opt/GC_Ext/downloads|확장은 Azure에서 다운로드되어 여기에 복사됩니다.|

* 에이전트를 설치하는 동안 대상 머신에 다음 디먼이 만들어집니다.

    |서비스 이름 |표시 이름 |프로세스 이름 |Description |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure Connected Machine 에이전트 서비스 |himds |이 서비스는 Azure 인스턴스 메타데이터 서비스(IMDS)를 구현하여 Azure 및 연결된 머신의 Azure ID에 대한 연결을 관리합니다.|
    |gcad.service |GC Arc 서비스 |gc_linux_service |컴퓨터의 필요한 상태 구성을 모니터링합니다. |
    |extd.service |확장 서비스 |gc_linux_service | 머신을 대상으로 하는 필수 확장을 설치합니다.|

* 문제 해결에 사용할 수 있는 로그 파일이 여러 개 있습니다. 이 내용은 다음 표에 설명되어 있습니다.

    |로그 |Description |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |에이전트(HIMDS) 서비스의 세부 정보 및 Azure와의 상호 작용을 기록합니다.|
    |/var/opt/azcmagent/log/azcmagent.log |자세한 정보(-v) 인수를 사용하는 경우 azcmagent tool 명령의 출력이 포함됩니다.|
    |/opt/logs/dsc.log |DSC 서비스 활동의 세부 정보,<br> 특히 himds 서비스와 Azure Policy 간의 연결을 기록합니다.|
    |/opt/logs/dsc.telemetry.txt |DSC 서비스 원격 분석 및 자세한 정보 로깅에 대한 세부 정보를 기록합니다.|
    |/var/lib/GuestConfig/ext_mgr_logs |확장 에이전트 구성 요소에 대한 세부 정보를 기록합니다.|
    |/var/lib/GuestConfig/extension_logs|설치된 확장의 세부 정보를 기록합니다.|

* 에이전트 설치 중에 다음 환경 변수가 생성됩니다. 이러한 변수는 `/lib/systemd/system.conf.d/azcmagent.conf`에 설정됩니다.

    |속성 |기본값 |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* 에이전트를 제거하는 동안 다음 아티팩트가 제거되지 않습니다.

    * /var/opt/azcmagent
    * /opt/logs

### <a name="agent-resource-governance"></a>에이전트 리소스 거버넌스

Azure Arc 사용 서버 연결 된 컴퓨터 에이전트는 에이전트 및 시스템 리소스 소비를 관리 하도록 설계 되었습니다. 에이전트는 다음과 같은 경우에 리소스 거버넌스에 접근합니다.

- 게스트 구성 에이전트는 정책을 평가하기 위해 CPU를 최대 5% 제한합니다.
- 확장 서비스 에이전트는 CPU의 최대 5%를 사용하도록 제한됩니다.

   - 이 제한은 설치/설치 제거/업그레이드 작업에만 적용됩니다. 설치되고 나면 확장에서 자체 리소스 사용률을 담당하며 5% CPU 제한은 적용되지 않습니다.
   - Log Analytics 에이전트 및 Azure Monitor 에이전트는 Red Hat Linux, CentOS 및 기타 엔터프라이즈 Linux 변형에서 설치/업그레이드/제거 작업을 수행 하는 동안 최대 60%의 CPU를 사용할 수 있습니다. 해당 시스템에 미치는 [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux)의 성능 영향을 수용하기 위해 확장 프로그램과 운영 체제 조합의 경우 한도는 더 높습니다.

## <a name="next-steps"></a>다음 단계

* azure arc 사용 서버 평가를 시작 하려면 [azure arc 사용 서버를 사용 하는 하이브리드 컴퓨터 커넥트](learn/quick-enable-hybrid-vm.md)문서를 따르세요.

* Azure Arc 지원 서버 에이전트를 배포하고 다른 Azure 관리 및 모니터링 서비스와 통합하기 전에 [계획 및 배포 가이드를](plan-at-scale-deployment.md)검토하세요.

* 문제 해결 정보는 [Connected Machine 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.
