---
title: Azure Monitor 에이전트 개요
description: 가상 머신의 게스트 운영 체제에서 모니터링 데이터를 수집하는 Azure Monitor 에이전트의 개요입니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: references_regions
ms.openlocfilehash: 50ab3c65abd6fe57941f18f58ff277de6822389c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216488"
---
# <a name="azure-monitor-agent-overview"></a>Azure Monitor 에이전트 개요
AMA(Azure Monitor 에이전트)는 Azure 가상 머신의 게스트 운영 체제에서 모니터링 데이터를 수집하여 이를 Azure Monitor에 전달합니다. 이 문서는 Azure Monitor 에이전트에 대한 개요를 제공하고 설치 방법 및 데이터 수집 구성 방법에 대한 정보를 포함하고 있습니다.

## <a name="relationship-to-other-agents"></a>다른 에이전트와의 관계
Azure Monitor 에이전트는 현재 Azure Monitor에서 가상 머신으로부터 게스트 데이터를 수집하는 데 사용하는 다음 레거시 에이전트를 대체합니다([알려진 차이점 보기](../faq.yml)).

- [Log Analytics 에이전트](./log-analytics-agent.md): 데이터를 Log Analytics 작업 영역으로 보내고 VM Insights 및 모니터링 솔루션을 지원합니다.
- [진단 확장](./diagnostics-extension-overview.md): 데이터를 Azure Monitor 메트릭(Windows에만 해당), Azure Event Hubs 및 Azure Storage에 보냅니다.
- [Telegraf 에이전트](../essentials/collect-custom-metrics-linux-telegraf.md): 데이터를 Azure Monitor 메트릭에 보냅니다(Linux에만 해당).

이 기능을 단일 에이전트로 통합하는 것 외에도 Azure Monitor 에이전트는 기존 에이전트에 비해 다음과 같은 혜택을 제공합니다.

- **모니터링 범위:** 서로 다른 VM 집합으로부터 서로 다른 데이터 세트를 수집하는 구성을 중앙에서 수행합니다.
- **Linux 멀티 호밍:** 데이터를 Linux VM에서 여러 작업 영역으로 보냅니다.
- **Windows 이벤트 필터링:** 수집되는 Windows 이벤트를 XPATH 쿼리를 사용하여 필터링합니다.
- **개선된 확장 관리:** Azure Monitor 에이전트는 현재 Log Analytics 에이전트의 관리 팩 및 Linux 플러그 인에 비해 투명성이 높고 제어가 용이한 새로운 확장 처리 방법을 사용합니다.

### <a name="current-limitations"></a>현재 제한 사항
기존 에이전트와 비교할 때 이 새 에이전트에는 아직 완전한 패리티가 없습니다.
- **Log Analytics 에이전트(MMA/OMS)와 비교**
    - 현재 모든 Log Analytics 솔루션이 지원되는 것은 아닙니다. [지원되는 기능](#supported-services-and-features)을 참조하세요.
    - Azure Private Links를 지원하지 않습니다.
    - 파일 기반 로그 또는 IIS 로그 수집을 지원하지 않습니다.
- **Azure Diagnostics 확장(WAD/LAD)과 비교:**
    - 대상으로 Event Hubs 및 스토리지 계정을 지원하지 않습니다.
    - 파일 기반 로그, IIS 로그, ETW 이벤트, .NET 이벤트, 크래시 덤프 수집을 지원하지 않습니다.

### <a name="changes-in-data-collection"></a>데이터 수집 변경
기존 에이전트에 대한 데이터 수집을 정의하는 방법은 서로 분명히 다릅니다. 각 방법에는 Azure Monitor 에이전트로 해결되는 문제가 있습니다.

- Log Analytics 에이전트는 구성을 Log Analytics 작업 영역에서 가져옵니다. 이 에이전트는 중앙집중식으로 구성하기는 쉽지만 다른 가상 머신에 대해 독립적으로 정의하기가 어렵습니다. 이 에이전트는 Log Analytics 작업 영역에만 데이터를 보낼 수 있습니다.
- 진단 확장에는 각 가상 머신에 대한 구성이 있습니다. 진단 확장은 다른 가상 머신에 대해 독립적으로 정의하기는 쉽지만 중앙집중식으로 관리하기가 어렵습니다. 진단 확장은 Azure Monitor 메트릭, Azure Event Hubs 또는 Azure Storage에만 데이터를 보낼 수 있습니다. Linux 에이전트의 경우 Azure Monitor 메트릭에 데이터를 보내기 위해 오픈 소스 Telegraf 에이전트가 필요합니다.

Azure Monitor 에이전트는 [데이터 수집 규칙](data-collection-rule-overview.md)을 사용하여 각 에이전트에서 수집할 데이터를 구성합니다. 데이터 수집 규칙을 사용하면 수집 설정을 대규모로 관리하면서도 계속해서 머신의 하위 집합에 대해 고유하고 범위가 지정된 구성을 사용할 수 있습니다. 데이터 수집 규칙은 작업 영역 및 가상 머신을 가리지 않으며 이는 데이터 수집 규칙을 머신 및 환경 전체에서 한 번만 정의하면 다시 사용할 수 있도록 해줍니다. [Azure Monitor 에이전트를 위한 데이터 수집 구성](data-collection-rule-azure-monitor-agent.md)을 참조하세요.

## <a name="should-i-switch-to-the-azure-monitor-agent"></a>Azure Monitor 에이전트로 전환해야 하나요?
Azure Monitor 에이전트는 [Azure Monitor용 레거시 에이전트](agents-overview.md)를 대체합니다. 현재 에이전트에서 새 에이전트로 VM 전환을 시작하려면 다음 요소를 고려하세요.

- **환경 요구 사항:** Azure Monitor 에이전트는 현재 [이러한 운영 체제](./agents-overview.md#supported-operating-systems)를 지원합니다. 향후 운영 체제 버전, 환경 지원 및 네트워킹 요구 사항에 대한 지원은 이 새 에이전트에서 제공될 가능성이 높습니다. 
 
  Azure Monitor 에이전트가 내 환경을 지원하는지를 평가합니다. 지원하지 않는 경우 현재 에이전트를 유지해야 할 수 있습니다. Azure Monitor 에이전트가 현재 환경을 지원한다면 전환을 고려합니다.
- **현재 및 새 기능 요구 사항:** Azure Monitor 에이전트에는 필터링, 범위 지정, 멀티 호밍과 같은 몇 가지 새로운 기능이 도입되었습니다. 그러나 사용자 정의 로그 수집 및 모든 솔루션과의 통합과 같은 다른 기능에 대한 현재 에이전트와 아직 동등하지 않습니다. ([미리 보기의 솔루션을 참조하세요](../faq.yml).) 
 
  Azure Monitor의 대부분의 새로운 기능은 Azure Monitor 에이전트에서만 사용할 수 있습니다. 시간이 지나면 새 에이전트에서만 더 많은 기능을 사용할 수 있습니다. Azure Monitor 에이전트에 필요한 기능이 있는지와 새 에이전트에서 다른 중요한 기능을 가져오지 않고 일시적으로 수행할 수 있는 몇 가지 기능이 있는지를 고려합니다. 
  
  Azure Monitor 에이전트에 필요한 모든 핵심 기능이 있는 경우 에이전트로 전환하는 것이 좋습니다. 필요한 중요한 기능이 있는 경우 Azure Monitor 에이전트가 패리티에 도달할 때까지 현재 에이전트를 계속 사용합니다.
- **재작업에 대한 허용 오차:** 배포 스크립트 및 온보딩 템플릿과 같은 리소스를 사용하여 새 환경을 설정하는 경우 관련된 작업을 평가합니다. 설정에 상당한 양의 작업이 필요한 경우 새 에이전트를 사용하는 새 환경을 설정하는 것이 좋습니다. 
 
  Log Analytics 에이전트에 대한 사용 중단 날짜는 2021년 8월에 게시됩니다. 현재 에이전트는 지원 중단이 시작된 이후에도 몇 년 동안 지원됩니다.

## <a name="supported-resource-types"></a>지원되는 리소스 유형
Azure 가상 머신, 가상 머신 확장 집합 및 Azure Arc 사용 서버가 현재 지원 됩니다. Azure Kubernetes Service 및 기타 컴퓨팅 리소스 종류는 지원되지 않습니다.

## <a name="supported-regions"></a>지원되는 지역
Azure Monitor 에이전트는 Log Analytics를 지 원하는 모든 공용 지역 뿐만 아니라 Azure Government 및 중국 클라우드 에서도 사용할 수 있습니다. 에어 갭(Air-Gap) 클라우드는 아직 지원되지 않습니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제
현재 Azure Monitor 에이전트에서 지원하는 Windows 및 Linux 운영 체제 버전의 목록은 [지원되는 운영 체제](agents-overview.md#supported-operating-systems)를 참조하세요.

## <a name="supported-services-and-features"></a>지원되는 서비스 및 기능
다음 표에는 다른 Azure 서비스를 통한 Azure Monitor 에이전트에 대한 현재 지원이 나와 있습니다.

| Azure 서비스 | 현재 지원 | 자세한 정보 |
|:---|:---|:---|
| [Azure Security Center](../../security-center/security-center-introduction.md) | 프라이빗 미리 보기 | [가입 링크](https://aka.ms/AMAgent) |
| [Azure Sentinel](../../sentinel/overview.md) | <ul><li>Windows 이벤트 전달 (WEF): 비공개 미리 보기</li><li>Windows 보안 이벤트: [공개 미리 보기](../../sentinel/connect-windows-security-events.md?tabs=AMA)</li></ul>  | <ul><li>[가입 링크](https://aka.ms/AMAgent) </li><li>등록 필요 없음</li></ul> |

다음 표에는 Azure Monitor 기능을 통한 Azure Monitor 에이전트에 대한 현재 지원이 나와 있습니다.

| Azure Monitor 기능 | 현재 지원 | 자세한 정보 |
|:---|:---|:---|
| [VM 인사이트](../vm/vminsights-overview.md) | 프라이빗 미리 보기  | [가입 링크](https://aka.ms/amadcr-privatepreviews) |
| [개인 링크 또는 AMPLS를 사용 하 여 커넥트](../logs/private-link-security.md) | AMA에 대 한 비공개 미리 보기 | [가입 링크](https://aka.ms/amadcr-privatepreviews) |
| [VM 인사이트 게스트 상태](../vm/vminsights-health-overview.md) | 퍼블릭 미리 보기 | 새 에이전트에서만 사용 가능 |
| [SQL 인사이트](../insights/sql-insights-overview.md) | 퍼블릭 미리 보기 | 새 에이전트에서만 사용 가능 |

다음 표에는 Azure 솔루션을 통한 Azure Monitor 에이전트에 대한 현재 지원이 나와 있습니다.

| 솔루션 | 현재 지원 | 자세한 정보 |
|:---|:---|:---|
| [변경 내용 추적](../../automation/change-tracking/overview.md) | Azure Security Center 프라이빗 미리 보기에서 파일 무결성 모니터링으로 지원됩니다.  | [가입 링크](https://aka.ms/AMAgent) |
| [업데이트 관리](../../automation/update-management/overview.md) | 에이전트가 필요하지 않은 업데이트 관리 v2(프라이빗 미리 보기)를 사용합니다. | [가입 링크](https://www.yammer.com/azureadvisors/threads/1064001355087872) |

## <a name="coexistence-with-other-agents"></a>다른 에이전트와 함께 사용 가능
Azure Monitor 에이전트는 기존 에이전트와 함께 사용할 수 있으므로 평가 또는 마이그레이션 중에도 기존 기능을 계속 사용할 수 있습니다. 이 기능은 기존 솔루션을 지원하는 제한 사항 때문에 중요합니다. 중복 데이터를 수집하면 쿼리 결과가 왜곡되고 데이터 수집 및 보존에 대해 요금이 더 많이 발생할 수 있으므로 주의해야 합니다. 

예를 들어 VM insights는 Log Analytics 에이전트를 사용하여 성능 데이터를 Log Analytics 작업 영역으로 보냅니다. 사용자가 에이전트에서 Windows 이벤트 및 Syslog 이벤트를 수집하도록 작업 영역을 구성했을 수도 있습니다. Azure Monitor 에이전트를 설치하고 동일한 이벤트 및 성능 데이터에 대한 데이터 수집 규칙을 만드는 경우 중복 데이터가 생성되는 결과가 발생합니다.

따라서 두 에이전트에서 동일한 데이터를 수집하지 않도록 합니다. 그럴 경우 대상을 구분해야 합니다.

## <a name="costs"></a>비용
Azure Monitor 에이전트에 대한 비용은 없지만 데이터 수집에 대해서는 요금이 발생할 수 있습니다. Log Analytics 데이터 수집과 보존 및 고객 메트릭에 대한 자세한 내용은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

## <a name="data-sources-and-destinations"></a>데이터 원본 및 대상
다음 표에는 현재 Azure Monitor 에이전트로 데이터 수집 규칙을 사용하여 수집할 수 있는 데이터 형식과 해당 데이터를 보낼 수 있는 위치가 나열되어 있습니다. Azure Monitor 에이전트를 사용하여 다른 종류의 데이터를 수집하는 인사이트, 솔루션 및 기타 솔루션의 목록은 [Azure Monitor에서 모니터링하는 항목](../monitor-reference.md)을 참조하세요.

Azure Monitor 에이전트는 Azure Monitor 메트릭 (미리 보기) 또는 Azure Monitor 로그를 지 원하는 Log Analytics 작업 영역에 데이터를 보냅니다.

| 데이터 원본 | 대상 | Description |
|:---|:---|:---|
| 성능        | Azure Monitor 메트릭 (미리 보기)<sup>1</sup><br>Log Analytics 작업 영역 | 운영 체제 및 워크로드의 여러 측면에서 성능을 측정하는 숫자 값 |
| Windows 이벤트 로그 | Log Analytics 작업 영역 | Windows 이벤트 로깅 시스템으로 전송된 정보 |
| Syslog             | Log Analytics 작업 영역 | Linux 이벤트 로깅 시스템으로 전송되는 정보 |

<sup>1</sup> Azure Monitor 메트릭을 사용 하는 다른 제한 사항을 검토 하려면 [여기를 클릭](../essentials/metrics-custom-overview.md#quotas-and-limits) 하세요. Linux에서 Azure Monitor 메트릭은 유일한 대상이 1.10.9.0 이상에서 지원 됩니다. 

## <a name="security"></a>보안
Azure Monitor 에이전트에는 키가 필요하지 않지만 대신 [시스템이 할당한 관리 ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)가 필요합니다. 에이전트를 배포하기 전에 각 가상 머신에 시스템이 할당한 관리 ID를 사용하도록 설정해야 합니다.

## <a name="networking"></a>네트워킹
Azure Monitor 에이전트는 Azure 서비스 태그를 지원합니다. AzureMonitor 및 AzureResourceManager 태그가 모두 필요합니다. Azure Monitor 에이전트는 아직 Azure Monitor Private Link 범위에서 작동하지 않습니다. 머신이 인터넷을 통해 통신하기 위해 프록시 서버에 연결하는 경우, 다음의 요구 사항을 검토하여 필요한 네트워크 구성을 이해합니다.

### <a name="proxy-configuration"></a>프록시 구성

Windows 및 Linux용 Azure Monitor 에이전트 확장은 HTTPS 프로토콜을 사용하여 프록시 서버 또는 Log Analytics 게이트웨이를 통해 Azure Monitor와 통신할 수 있습니다. 이를 Azure 가상 머신, Azure 가상 머신 확장 집합 및 서버용 Azure Arc에 사용합니다. 다음 단계에 설명된 대로 구성에 확장 설정을 사용합니다. 사용자 이름과 암호를 사용한 익명 인증과 기본 인증이 모두 지원됩니다.

> [!IMPORTANT]
> 프록시 구성은 대상으로 [Azure Monitor 메트릭 (미리 보기)](../essentials/metrics-custom-overview.md) 에 대해 지원 되지 않습니다. 따라서이 대상에 메트릭을 전송 하는 경우 프록시 없이 공용 인터넷을 사용 합니다.

1. 이 순서도를 사용하여 먼저 *setting* 및 *protectedSetting* 매개 변수의 값을 결정합니다.

   ![확장을 사용하도록 설정할 때 setting 및 protectedSetting 매개 변수의 값을 결정하는 순서도.](media/azure-monitor-agent-overview/proxy-flowchart.png)


2. *setting* 및 *protectedSetting* 매개 변수의 값이 결정되면 PowerShell 명령을 사용하여 Azure Monitor 에이전트를 배포할 때 이러한 추가 매개 변수를 제공합니다. 다음 예는 Azure 가상 머신에 대한 것입니다.

    | 매개 변수 | 값 |
    |:---|:---|
    | SettingString | 문자열로 변환된 이전 순서도의 JSON 개체입니다. 해당하지 않는 경우 생략합니다. {"proxy":{"mode":"application","address":"http://[address]:[port]","auth": false}}를 예로 들 수 있습니다. |
    | ProtectedSettingString | 문자열로 변환된 이전 순서도의 JSON 개체입니다. 해당하지 않는 경우 생략합니다. {"proxy":{"username": "[username]","password": "[password]"}}를 예로 들 수 있습니다. |


# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0 -SettingString <settingString> -ProtectedSettingString <protectedSettingString>
```

# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5 -SettingString <settingString> -ProtectedSettingString <protectedSettingString>
```

---

## <a name="next-steps"></a>다음 단계

- Windows 및 Linux 가상 머신에 [Azure Monitor 에이전트를 설치](azure-monitor-agent-install.md)합니다.
- 에이전트에서 데이터를 수집하고 Azure Monitor로 보내기 위한 [데이터 수집 규칙을 만듭니다](data-collection-rule-azure-monitor-agent.md).
