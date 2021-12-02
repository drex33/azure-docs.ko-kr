---
title: Azure Automation 데이터 보안
description: 이 문서는 Azure Automation 개인 정보와 데이터를 보호하는 방법을 알아봅니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 03a3851b634fe906d096d89bdf62775829ba7a2f
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133435813"
---
# <a name="management-of-azure-automation-data"></a>Azure Automation 데이터 관리

이 문서에는 Azure Automation 환경에서 데이터를 보호하는 방법을 설명하는 여러 항목이 포함되어 있습니다.

## <a name="tls-12-for-azure-automation"></a>Azure Automation용 TLS 1.2

Azure Automation으로 전송되는 데이터의 보안을 보장하려면 TLS(전송 계층 보안) 1.2 사용을 구성하는 것이 좋습니다. 다음은 HTTPS를 통해 자동화 서비스와 통신하는 메서드나 클라이언트의 목록입니다.

* 웹후크 호출

* 업데이트 관리를 통해 관리되는 컴퓨터와 변경 사항 추적 및 인벤토리를 포함하는 Hybrid Runbook Worker입니다.

* DSC 노드

이전 버전의 TLS/SSL(Secure Sockets Layer)은 취약한 것으로 나타났으며, 여전히 이전 버전과 호환되지만 **사용하지 않는 것이 좋습니다**. TLS 1.3등을 사용할 수 있게 되면 더 안전한 최신 프로토콜을 자동으로 검색하고 활용할 수 있도록 플랫폼 수준 보안 기능을 중단할 수 있으므로 필요하지 않다면 에이전트가 TLS 1.2만을 사용하도록 명시적으로 설정하지 않는 것이 좋습니다.

Hybrid Runbook Worker 역할에 대한 종속성인 Windows 및 Linux용 Log Analytics 에이전트의 TLS 1.2지원에 대한 자세한 내용은 [Log Analytics 에이전트 개요-TLS 1.2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol)를 참조하세요.

### <a name="platform-specific-guidance"></a>플랫폼별 지침

|플랫폼/언어 | 지원 | 추가 정보 |
| --- | --- | --- |
|Linux | Linux 배포판은 TLS 1.2 지원에 대해 [OpenSSL](https://www.openssl.org)을 사용하는 경향이 있습니다.  | [OpenSSL Changelog](https://www.openssl.org/news/changelog.html)를 확인하여 OpenSSL 버전이 지원되는지 확인합니다.|
| Windows 8.0 - 10 | 지원됨, 기본적으로 활성화됩니다. | [기본 설정](/windows-server/security/tls/tls-registry-settings)을 여전히 사용하는지 확인하려면  |
| Windows Server 2012 - 2016 | 지원되며 기본적으로 사용하도록 설정됩니다. | [기본 설정](/windows-server/security/tls/tls-registry-settings)을 여전히 사용하는지 확인하려면 |
| Windows 7 SP1 및 Windows Server 2008 R2 SP1 | 지원되지만 기본적으로 사용하도록 설정되지 않습니다. | 활성화하는 방법에 대한 자세한 내용은 [TLS(전송 계층 보안) 레지스트리 설정](/windows-server/security/tls/tls-registry-settings) 페이지를 참조하세요.  |

## <a name="data-retention"></a>데이터 보존

Azure Automation에서 리소스를 삭제할 경우 영구적으로 제거하기 전에 감사를 위해 수일 동안 보존됩니다. 이 기간 동안에는 리소스를 보거나 사용할 수 없습니다. 이 정책은 삭제되는 Automation 계정에 속한 리소스에도 적용됩니다. 보존 정책은 모든 사용자에게 적용되며 지금은 사용자 지정할 수 없습니다. 하지만 데이터를 더 오랜 기간 동안 유지해야 하는 경우 [Azure Automation 작업 데이터를 Azure Monitor 로그로 전달](automation-manage-send-joblogs-log-analytics.md)할 수 있습니다.

다음 표에는 여러 리소스에 대한 보존 정책이 요약되어 있습니다.

| 데이터 | 정책 |
|:--- |:--- |
| 계정 |사용자가 삭제한 후 30일이 지나면 계정은 영구적으로 제거됩니다. |
| 자산 |자산은 사용자가 삭제한 후 30일이 지나거나 사용자가 자산을 보유한 계정을 삭제한 후 30일이 지나면 영구적으로 제거됩니다. 자산에는 변수, 일정, 자격 증명, 인증서, Python 2 패키지 및 연결이 포함됩니다. |
| DSC 노드 |DSC 노드는 Azure Portal이나 Windows PowerShell에서 [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) cmdlet을 사용하여 자동화 계정에서 노드의 등록을 취소한 후 30일이 지나면 영구적으로 제거됩니다. 노드는 또한 사용자가 노드를 보유하는 계정을 삭제한 후 30일이 지나면 영구적으로 제거됩니다. |
| 작업 |작업 완료, 중지 또는 일시 중단과 같은 수정 이후 30일이 지나면 작업이 삭제되고 영구 제거됩니다. |
| 모듈 |모듈은 사용자가 삭제한 후 30일이 지나면 영구적으로 제거됩니다. 또는 사용자가 모듈을 보유한 계정을 삭제한 후 30일이 지나면 영구적으로 제거됩니다. |
| 노드 구성/MOF 파일 |이전 노드 구성은 새 노드 구성이 생성된 후 30일이 지나면 영구적으로 제거됩니다. |
| 노드 보고서 |노드 보고서는 해당 노드에 대한 새 보고서가 생성된 후 90일이 지나면 영구적으로 제거됩니다. |
| Runbook |Runbook은 사용자가 리소스를 삭제한 후 30일이 지나면 영구적으로 제거됩니다. 또는 사용자가 리소스를 보유한 계정을 삭제한 후 30일이 지나면 영구적으로 제거됩니다<sup>1</sup>. |

<sup>1</sup> Runbook은 30일 내에 Microsoft Azure 지원 서비스에 Azure 지원 인시던트를 제출하면 복구할 수 있습니다. 여전히 도움이 필요한 경우 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 요청 제출** 을 선택합니다.

## <a name="data-backup"></a>데이터 백업

Azure에서 Automation 계정을 삭제하면 계정의 모든 개체가 삭제됩니다. 개체에는 Runbook, 모듈, 구성, 설정, 작업 및 자산이 포함됩니다. 계정을 삭제한 후에는 개체를 복구할 수 없습니다. Automation 계정을 삭제하기 전에 다음 정보를 사용하여 Automation 계정의 내용을 백업할 수 있습니다.

### <a name="runbooks"></a>Runbook

Azure Portal 또는 Windows PowerShell의 [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition)을 사용하여 Runbook을 스크립트 파일로 내보낼 수 있습니다. [Azure Automation에서 Runbook 관리](manage-runbooks.md)에서 설명하는 것과 같이 다른 Automation 계정으로 스크립트 파일을 가져올 수 있습니다.

### <a name="integration-modules"></a>통합 모듈

Azure Automation에서 통합 모듈을 내보낼 수 없으며 자동화 계정 외부에서 사용할 수 있어야 합니다.

### <a name="assets"></a>자산

인증서, 연결, 자격 증명, 일정 및 변수 등 Azure Automation 자산을 내보낼 수 없습니다. 대신 Azure Portal 및 Azure cmdlet을 사용하여 이러한 자산에 대한 세부 정보를 확인할 수 있습니다. 그런 다음 이러한 세부 정보를 사용하여 다른 Automation 계정으로 가져온 Runbook에서 사용하는 자산을 만듭니다.

자격 증명의 암호 필드 또는 암호화된 변수 값은 cmdlet을 사용하여 검색할 수 없습니다. 이러한 값을 모르는 경우 Runbook에서 검색할 수 있습니다. 변수 값 검색은 [Azure Automation에서 변수 관리](shared-resources/variables.md)를 참조하세요. 자격 증명 값 검색을 자세히 알아보려면 [Azure Automation의 자격 증명 자산](shared-resources/credentials.md)을 참조하세요.

### <a name="dsc-configurations"></a>DSC 구성

Azure Portal 또는 Windows PowerShell의 [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) cmdlet을 사용하여 DSC 구성을 스크립트 파일로 내보낼 수 있습니다. 다른 Automation 계정에서 이러한 구성을 가져오고 사용할 수 있습니다.

## <a name="geo-replication-in-azure-automation"></a>Azure Automation의 지역에서 복제

지역 복제는 Azure Automation 계정에서 표준입니다. 계정을 설정할 때 주 지역을 선택합니다. 내부 Automation 지역 복제 서비스는 자동으로 계정에 보조 지역을 할당합니다. 그런 다음 서비스는 주 지역에서 보조 지역으로 계정 데이터를 지속적으로 백업합니다. 주 지역 및 보조 지역의 전체 목록은 [Azure의 지역 간 복제: 비즈니스 연속성 및 재해 복구에서](../availability-zones/cross-region-replication-azure.md)찾을 수 있습니다.

Automation 지역 복제 서비스에 의해 생성된 백업은 Automation 자산, 구성 등의 전체 복사본입니다. 주 지역이 중단되고 데이터가 손실되는 경우 이 백업을 사용할 수 있습니다. 주 지역 데이터가 손실되는 예기치 않은 이벤트가 발생한 경우 Microsoft는 복구를 시도합니다.

> [!NOTE]
> Azure Automation은 고객이 선택한 지역에 고객 데이터를 저장합니다. BCDR의 목적을 위해 브라질 남부 및 동남 아시아를 제외한 모든 지역에 대해 Azure Automation 데이터는 다른 지역(Azure 쌍을 이루는 지역)에 저장됩니다. 브라질 지역의 브라질 남부(상파울루 주) 및 아시아 태평양 지역의 동남 아시아 지역(싱가포르)의 지역에만 해당 지역에 대한 데이터 상주 요구 사항을 수용하기 위해 동일한 지역에 Azure Automation 데이터를 저장합니다.

지역 실패가 발생하는 경우 Automation 지역 복제 서비스는 외부 고객에게 직접 액세스할 수 없습니다. 지역 실패가 발생하는 경우 Automation 구성 및 Runbook을 유지하고자 하는 경우 다음을 수행합니다.

1. 주 Automation 계정의 지역에 연결할 보조 지역을 선택합니다.

2. 보조 지역에서 Automation 계정을 만듭니다.

3. 주 계정에서 Runbook을 스크립트 파일로 내보냅니다.

4. Runbook을 보조 지역의 Automation 계정으로 가져옵니다.

## <a name="next-steps"></a>다음 단계

* Azure Automation의 보안 자산에 대한 자세한 내용은 [Azure Automation의 보안 자산 암호화](automation-secure-asset-encryption.md)를 참조하세요.

* 지역 복제에 대한 자세한 내용은 [활성 지역 복제 만들기 및 사용](../azure-sql/database/active-geo-replication-overview.md)을 참조하세요.
