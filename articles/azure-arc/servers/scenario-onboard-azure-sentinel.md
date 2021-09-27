---
title: Azure Arc 지원 서버를 Azure Sentinel에 온보딩
description: Azure Arc 지원 서버를 Azure Sentinel에 추가하고 해당 보안 상태를 사전에 모니터링하는 방법에 대해 알아봅니다.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: d37a6b62a5a3d95cc02090b4536db2076b2e1b5c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837236"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure Arc 지원 서버를 Azure Sentinel에 온보딩

이 문서는 Azure Arc 지원 서버를 [Azure Sentinel](../../sentinel/overview.md)에 온보딩하고 보안 관련 이벤트 수집을 시작하는 데 도움을 주기 위해 작성되었습니다. Azure Sentinel은 기업 전체에서 경고 검색, 위협 표시, 사전 예방식 헌팅 및 위협 대응을 위한 단일 솔루션을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 요구 사항을 충족해야 합니다.

- [Log Analytics 작업 영역](../../azure-monitor/logs/data-platform-logs.md) Log Analytics 작업 영역에 대한 자세한 내용은 [Azure Monitor 로그 배포 디자인](../../azure-monitor/logs/design-logs-deployment.md)을 참조하세요.

- Azure Sentinel은 [구독에서 활성화되었습니다](../../sentinel/quickstart-onboard.md).

- 머신 또는 서버가 Azure Arc 지원 서버에 연결되어 있습니다.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure Arc 지원 서버를 Azure Sentinel에 온보딩

Azure Sentinel은 기본 제공되고 실시간 통합을 제공하는 Microsoft 솔루션을 위한 다양한 커넥터와 함께 제공됩니다. 실제 및 가상 머신의 경우 로그를 수집하고 Azure Sentinel에 전달하는 Log Analytics 에이전트를 설치할 수 있습니다. Azure Arc 지원 서버는 다음 방법을 사용하여 Log Analytics 에이전트 배포를 지원합니다.

- VM 확장 프레임워크 사용.

    Azure Arc 지원 서버의 이 기능을 사용하면 Log Analytics 에이전트 VM 확장을 비 Azure Windows 및/또는 Linux 서버에 배포할 수 있습니다. VM 확장은 Azure Arc 지원 서버에서 관리되는 하이브리드 머신 또는 서버에서 다음 방법을 사용하여 관리될 수 있습니다.

    - [Azure Portal](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [Resource Manager 템플릿](manage-vm-extensions-template.md)

- Azure Policy 사용.

    이 방법을 사용하면 Azure Policy [Linux에 Log Analytics 에이전트 배포 또는 Windows Azure Arc 머신](../../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책을 사용하여 Azure Arc 지원 서버에 Log Analytics 에이전트가 설치되어 있는지 감사합니다. 에이전트가 설치되지 않은 경우에는 재구성 작업을 사용하여 에이전트를 자동으로 배포합니다. 또는 VM용 Azure Monitor를 사용하는 머신을 모니터링하려는 경우에는 [VM용 Azure Monitor 사용](../../governance/policy/samples/built-in-initiatives.md#monitoring)을 대신 사용하여 Log Analytics 에이전트를 설치하고 구성합니다.

Azure Policy를 사용하여 Windows 또는 Linux용 Log Analytics 에이전트를 설치하는 것이 좋습니다.

Arc 지원 서버가 연결되면 데이터는 Azure Sentinel로 스트리밍되기 시작하고 작업을 시작할 준비가 됩니다. [기본 제공 통합 문서](../../sentinel/get-visibility.md)에서 로그를 확인하고 Log Analytics에서 쿼리를 작성하여 [데이터를 조사](../../sentinel/investigate-cases.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Sentinel을 사용하여 위협 검색](../../sentinel/detect-threats-built-in.md)을 시작합니다.