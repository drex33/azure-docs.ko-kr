---
title: Microsoft 센티널에 Azure Arc 사용 서버 등록
description: Microsoft 센티널에 Azure Arc 사용 서버를 추가 하 고 해당 보안 상태를 사전에 모니터링 하는 방법에 대해 알아봅니다.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 9a7f7fb8edb6edf18d9f8d5fed0608520cb3b80f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306494"
---
# <a name="onboard-azure-arc-enabled-servers-to-microsoft-sentinel"></a>Microsoft 센티널에 Azure Arc 사용 서버 등록

이 문서는 Azure Arc 사용 서버를 [Microsoft 센티널](../../sentinel/overview.md) 에 등록 하 고 보안 관련 이벤트 수집을 시작 하는 데 도움이 됩니다. Microsoft 센티널은 엔터프라이즈 전체에서 경고 검색, 위협 가시성, 자동 관리 구하기 및 위협 대응을 위한 단일 솔루션을 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 요구 사항을 충족해야 합니다.

- [Log Analytics 작업 영역](../../azure-monitor/logs/data-platform-logs.md) Log Analytics 작업 영역에 대한 자세한 내용은 [Azure Monitor 로그 배포 디자인](../../azure-monitor/logs/design-logs-deployment.md)을 참조하세요.

- Microsoft 센티널이 [구독에서 사용 하도록 설정](../../sentinel/quickstart-onboard.md)되었습니다.

- 머신 또는 서버가 Azure Arc 지원 서버에 연결되어 있습니다.

## <a name="onboard-azure-arc-enabled-servers-to-microsoft-sentinel"></a>Microsoft 센티널에 Azure Arc 사용 서버 등록

Microsoft 센티널은 Microsoft 솔루션에 대 한 다양 한 커넥터와 함께 제공 되며, 기본적으로 제공 되며 실시간 통합을 제공 합니다. 실제 및 가상 컴퓨터의 경우 로그를 수집 하 고 Microsoft 센티널에 전달 하는 Log Analytics 에이전트를 설치할 수 있습니다. Azure Arc 사용 서버는 다음 방법을 사용 하 여 Log Analytics 에이전트 배포를 지원 합니다.

- VM 확장 프레임워크 사용.

    Azure Arc 지원 서버의 이 기능을 사용하면 Log Analytics 에이전트 VM 확장을 비 Azure Windows 및/또는 Linux 서버에 배포할 수 있습니다. Azure Arc 사용 서버에서 관리 하는 하이브리드 컴퓨터 또는 서버에서 다음 방법을 사용 하 여 VM 확장을 관리할 수 있습니다.

    - [Azure Portal](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [Resource Manager 템플릿](manage-vm-extensions-template.md)

- Azure Policy 사용.

    이 방법을 사용 하 여 azure arc 사용 서버에 Log Analytics 에이전트가 설치 되어 있는 경우 azure [에 Log Analytics 에이전트를 배포 하거나 azure arc 컴퓨터 Windows](../../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책을 사용 하 여 감사를 Azure Policy 합니다. 에이전트가 설치되지 않은 경우에는 재구성 작업을 사용하여 에이전트를 자동으로 배포합니다. 또는 VM용 Azure Monitor를 사용하는 머신을 모니터링하려는 경우에는 [VM용 Azure Monitor 사용](../../governance/policy/samples/built-in-initiatives.md#monitoring)을 대신 사용하여 Log Analytics 에이전트를 설치하고 구성합니다.

Azure Policy를 사용하여 Windows 또는 Linux용 Log Analytics 에이전트를 설치하는 것이 좋습니다.

호 사용 서버가 연결 된 후 데이터는 Microsoft 센티널로 스트리밍을 시작 하 고 작업을 시작할 준비가 되었습니다. [기본 제공 통합 문서](../../sentinel/get-visibility.md)에서 로그를 확인하고 Log Analytics에서 쿼리를 작성하여 [데이터를 조사](../../sentinel/investigate-cases.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Microsoft 센티널을 사용 하 여 위협 검색을](../../sentinel/detect-threats-built-in.md)시작 하세요.
