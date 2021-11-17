---
title: Azure Automation 네트워크 구성 세부 정보
description: 이 문서에서는 Azure Automation State Configuration, Azure Automation Hybrid Runbook Worker, 업데이트 관리, 변경 내용 추적 및 인벤토리에 필요한 네트워크 세부 정보를 제공합니다.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 7f441f49d7f94447ab16a6a3f98b0db55dabcc1f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491453"
---
# <a name="azure-automation-network-configuration-details"></a>Azure Automation 네트워크 구성 세부 정보

이 페이지에서는 [Hybrid Runbook Worker 및 State Configuration](#hybrid-runbook-worker-and-state-configuration), [업데이트 관리와 변경 내용 추적 및 인벤토리](#update-management-and-change-tracking-and-inventory)에 필요한 네트워킹 세부 정보를 제공합니다.

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Hybrid Runbook Worker 및 State Configuration

Azure Automation과 커뮤니케이션하기 위해 Hybrid Runbook Worker 및 [Automation State Configuration](automation-dsc-overview.md)에는 다음 포트와 URL이 필요합니다.

* 포트: 아웃바운드 인터넷 액세스에는 443만 필요
* 글로벌 URL: `*.azure-automation.net`
* US Gov 버지니아의 글로벌 URL: `*.azure-automation.us`
* 에이전트 서비스: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker에 대한 네트워크 계획

시스템 및 사용자 Hybrid Runbook Worker에서 Azure Automation에 연결하고 등록하려면 이 섹션에서 설명하는 포트 번호 및 URL에 대한 액세스 권한이 있어야 합니다. 또한 작업자가 Azure Monitor Log Analytics 작업 영역에 연결하려면 [Log Analytics 에이전트에 필요한 포트 및 URL](../azure-monitor/agents/log-analytics-agent.md)에 액세스할 수 있어야 합니다.

특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터와 Hybrid Runbook Worker의 통신을 제한할 수 있습니다. [Azure Automation에서 사용하는 DNS 레코드](how-to/automation-region-dns-records.md)에서 필요한 DNS 레코드를 검토합니다.

### <a name="configuration-of-private-networks-for-state-configuration"></a>State Configuration에 대한 개인 네트워크 구성

노드가 개인 네트워크에 있는 경우 위에서 정의한 포트 및 URL이 필요합니다. 이러한 리소스는 관리 노드에 대한 네트워크 연결을 제공하고 DSC가 Azure Automation과 통신할 수 있도록 합니다.

[WaitFor * 리소스](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)와 같이 노드 간에 통신하는 DSC 리소스를 사용하는 경우에는 노드 간 트래픽만 허용해야 합니다. 이러한 네트워크 요구 사항을 이해하려면 각 DSC 리소스에 대한 문서를 참조하세요.

TLS 1.2에 대한 클라이언트 요구 사항을 이해하려면 [Azure Automation용 TLS 1.2](automation-managing-data.md#tls-12-for-azure-automation)를 참조하세요.

## <a name="update-management-and-change-tracking-and-inventory"></a>업데이트 관리와 변경 내용 추적 및 인벤토리

이 표의 주소는 업데이트 관리와 변경 내용 추적 및 인벤토리 모두에 필요합니다. 표 다음에 나오는 단락은 둘 다에도 적용됩니다.

이와 같은 주소에 대한 통신에는 **443 포트** 가 사용됩니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*.azure-automation.us|

Automation 서비스 및 Log Analytics 작업 영역에 대한 트래픽을 허용하도록 네트워크 그룹 보안 규칙을 만들거나 Azure Firewall을 구성하는 경우 [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** 및 **AzureMonitor** 를 사용합니다. 네트워크 보안 규칙의 지속적인 관리를 간소화합니다. Azure VM에서 Automation 서비스에 안전하게 비공개로 연결하려면 [Azure Private Link 사용](./how-to/private-link-security.md)을 검토하세요. 온-프레미스 방화벽 구성의 일부로 포함할 현재 서비스 태그 및 범위 정보를 얻으려면 [다운로드 가능한 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Automation - 업데이트 관리 개요](update-management\overview.md)에 대해 알아보세요.
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에 대해 알아보세요.
* [변경 내용 추적 및 인벤토리](change-tracking\overview.md)에 대해 알아보세요.
* [Automation State Configuration](automation-dsc-overview.md)에 대해 알아보세요.
