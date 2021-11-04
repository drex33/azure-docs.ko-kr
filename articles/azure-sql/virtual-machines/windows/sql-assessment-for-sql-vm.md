---
title: SQL 평가
description: 성능 문제를 식별하고 SQL Server VM이 Azure Portal SQL 평가 기능을 사용하여 모범 사례를 따르도록 구성되어 있는지 평가합니다.
author: ebruersan
ms.author: ebrue
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/02/2021
ms.reviewer: mathoma
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0c163f3ea101ec47eda25bc81f78033893cad6a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500282"
---
# <a name="sql-assessment-for-sql-server-on-azure-vms-preview"></a>SQL Azure VM의 SQL Server 평가(미리 보기)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Portal SQL 평가 기능은 가능한 성능 문제를 식별하고 Azure VM(Virtual Machines)의 SQL Server SQL [평가 API에서](/sql/sql-assessment-api/sql-assessment-api-overview)제공하는 [다양한 규칙 설정을](https://github.com/microsoft/sql-server-samples/blob/master/samples/manage/sql-assessment-api/DefaultRuleset.csv) 사용하여 모범 사례를 따르도록 구성되었는지 평가합니다. 

SQL 평가 기능은 현재 미리 보기로 제공됩니다.

## <a name="overview"></a>개요

SQL 평가 기능을 사용하도록 설정하면 SQL Server 인스턴스 및 데이터베이스를 검사하여 인덱스, 사용되지 않은 기능, 사용 또는 누락된 추적 플래그, 통계 등의 권장 사항을 제공합니다. 권장 사항 [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) [SQL VM 관리 페이지에](manage-sql-vm-portal.md) 표시됩니다. 


평가 결과는 [MMA(Microsoft Monitoring Agent)를](../../../azure-monitor/agents/log-analytics-agent.md)사용하여 [Log Analytics 작업 영역에](../../../azure-monitor/logs/quick-create-workspace.md) 업로드됩니다. VM이 Log Analytics를 사용하도록 이미 구성된 경우 SQL 평가 기능은 기존 연결을 사용합니다.  그렇지 않으면 MMA 확장이 SQL Server VM에 설치되고 지정된 Log Analytics 작업 영역에 연결됩니다.

평가 실행 시간은 사용자 환경(데이터베이스, 개체 수 등)에 따라 달라지고 몇 분에서 최대 1시간까지의 기간이 있습니다. 마찬가지로 평가 결과의 크기도 사용자 환경에 따라 달라집니다. 평가는 인스턴스 및 해당 인스턴스의 모든 데이터베이스에 대해 실행됩니다.

## <a name="prerequisites"></a>필수 구성 요소

SQL 평가 기능을 사용하려면 다음 필수 구성 조건이 있어야 합니다. 

- SQL Server VM은 [전체 모드에서 SQL Server IaaS 확장에](sql-agent-extension-manually-register-single-vm.md#full-mode)등록되어야 합니다. 
- 평가 결과를 업로드할 SQL Server VM과 동일한 구독에 있는 [Log Analytics 작업 영역](../../../azure-monitor/logs/quick-create-workspace.md) 
- SQL Server 2012 이상 버전이어야 합니다.


## <a name="enable"></a>사용

SQL 평가를 사용하도록 설정하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com) 로그인하고 [SQL Server VM 리소스로](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines)이동합니다. 
1. 설정 아래에서 **SQL** **평가를** 선택합니다. 
1. **SQL 평가** 또는 구성 사용을 선택하여 **구성**  페이지로 이동합니다. 
1. SQL **평가 사용** 상자를 확인하고 다음을 제공합니다.
    1. 평가를 업로드할 [Log Analytics 작업 영역입니다.](../../../azure-monitor/logs/quick-create-workspace.md) SQL Server VM이 이전에 작업 영역과 연결되지 않은 경우 드롭다운에서 구독의 기존 작업 영역을 선택합니다. 그렇지 않으면 이전에 연결된 작업 영역이 이미 채워져 있습니다.  
    1. **실행 일정** 입니다. 필요에 따라 또는 일정에 따라 자동으로 평가를 실행하도록 선택할 수 있습니다. 일정을 선택하는 경우 빈도(매주 또는 매월), 요일, 되풀이(1-6주마다) 및 평가를 시작해야 하는 시간(로컬-VM 시간)을 제공합니다. 
1. **적용을** 선택하여 변경 내용을 저장하고 SQL Server VM에 Microsoft Monitoring Agent 배포합니다(아직 배포되지 않은 경우). Azure Portal 알림은 SQL 평가 기능이 SQL Server VM에 대해 준비되면 알려줍니다. 
    

## <a name="assess-sql-server-vm"></a>SQL Server VM 평가

평가 실행:
- 일정에 따라
- 주문형

### <a name="run-scheduled-assessment"></a>예약된 평가 실행

구성 블레이드에서 일정을 설정하면 지정된 날짜 및 시간에 평가가 자동으로 실행됩니다. **구성을** 선택하여 평가 일정을 수정합니다. 새 일정을 제공하면 이전 일정을 덮어씁니다.  

### <a name="run-on-demand-assessment"></a>주문형 평가 실행

SQL Server VM에 SQL 평가 기능을 사용하도록 설정한 후에는 요청 시 평가를 실행할 수 있습니다. 이렇게 하려면 Azure Portal SQL Server [VM 리소스](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 페이지의 SQL 평가 블레이드에서 평가 **실행을** 선택합니다.


## <a name="view-results"></a>결과 보기

**SQL 평가** 페이지의 **평가 결과** 섹션에는 가장 최근의 평가 실행 목록이 표시됩니다. 각 행은 실행의 시작 시간과 상태(예약, 실행 중, 결과 업로드, 완료 또는 실패)를 표시합니다. 각 평가 실행에는 인스턴스를 평가하고 Log Analytics 작업 영역에 결과를 업로드하는 두 부분으로 구성됩니다. 상태 필드는 두 부분을 모두 포함합니다. 평가 결과는 Azure 통합 문서에 표시됩니다. 

다음 세 가지 방법으로 평가 결과 Azure 통합 문서에 액세스합니다. 
- **SQL** 평가 페이지에서 최근에 성공한 **평가 보기 단추를** 선택합니다.
- **SQL 평가** 페이지의 **평가 결과** 섹션에서 완료된 실행을 선택합니다. 
- SQL VM 리소스 페이지의 **개요** 페이지에 있는 **상위 10개 권장 사항에서** **평가 결과 보기를** 선택합니다. 

통합 문서가 열리면 드롭다운을 사용하여 이전 실행을 선택할 수 있습니다. **결과** 페이지를 사용하여 단일 실행의 결과를 보거나 **추세** 페이지를 사용하여 기록 추세를 검토할 수 있습니다.

### <a name="results-page"></a>결과 페이지

**결과** 페이지는 *해결된 모든 새* 에 대한 탭을 사용하여 권장 사항을 구성합니다. 이러한 탭을 사용하여 현재 실행의 모든 권장 사항, 모든 새 권장 사항(이전 실행의 델타) 또는 이전 실행에서 해결된 권장 사항을 볼 수 있습니다. 탭을 사용하면 실행 간의 진행 상황을 추적할 수 있습니다. *Insights* 탭은 가장 되풀이되는 문제와 가장 많은 문제가 있는 데이터베이스를 식별합니다. 이러한 방법을 사용하여 노력을 집중할 위치를 결정합니다. 

그래프 그룹 평가 결과는 심각도의 다양한 범주(높음, 중간 및 낮음)로 표시됩니다. 각 범주를 선택하여 권장 사항 목록을 확인하거나 검색 상자에서 핵심 구를 검색합니다. 가장 심각한 권장 사항으로 시작하고 목록 아래로 이동하는 것이 가장 좋습니다.

테이블에서 **이름별로** 정렬하여 특정 데이터베이스 또는 인스턴스별로 그룹화되는 권장 사항을 확인합니다. 검색 상자를 사용하여 태그 값 또는 핵심 구(예: 성능)를 기반으로 특정 유형의 권장 사항을 볼 수 있습니다. 테이블 오른쪽 위에 있는 아래쪽 화살표를 사용하여 Excel 파일에 대한 전문가 결과를 확인할 수 있습니다. 

그래프의 **전달된** 섹션은 시스템이 이미 따르는 권장 사항을 식별합니다. 

자세한 설명 및 관련 온라인 리소스와 같은 **메시지** 필드를 선택하여 각 권장 사항에 대한 자세한 정보를 봅니다. 
 
### <a name="trends-page"></a>추세 페이지

**추세** 페이지에는 시간에 따른 변경 내용을 표시하는 세 가지 차트, 즉 모든 문제, 새로운 문제 및 해결된 문제가 있습니다. 차트는 진행 상황을 확인하는 데 도움이 됩니다. 이상적으로는 해결된 문제 수가 증가하는 동안 권장 사항 수가 감소해야 합니다. 범례에는 각 심각도 수준에 대한 평균 문제 수가 표시됩니다. 막대를 마우스로 가리키면 각 실행에 대한 개별 vale을 볼 수 있습니다. 

하루에 여러 실행이 있는 경우 최신 실행만 **추세** 페이지의 그래프에 포함됩니다. 

## <a name="known-issues"></a>알려진 문제

SQL 평가를 사용할 때 다음과 같은 알려진 문제 중 일부가 발생할 수 있습니다. 

### <a name="configuration-error-for-enable-assessment"></a>평가 사용 구성 오류

가상 머신이 액세스 권한이 없거나 다른 구독에 있는 Log Analytics 작업 영역과 이미 연결되어 있는 경우 구성 블레이드에 오류가 표시됩니다. 전자의 경우 이러한 지침에 따라 해당 작업 영역에 대한 권한을 얻거나 VM을 다른 Log Analytics 작업 영역으로 전환하여 Microsoft Monitoring Agent 제거할 수 [있습니다.](../../../azure-monitor/agents/agent-manage.md) Log Analytics 작업 영역이 다른 구독에 있는 시나리오를 사용하도록 설정하기 위해 노력하고 있습니다.

### <a name="deployment-failure-for-enable-or-run-assessment"></a>평가 사용 또는 실행에 대한 배포 실패 

실패한 작업과 관련된 오류 메시지를 보려면 SQL VM이 포함된 리소스 그룹의 [배포 기록을](../../../azure-resource-manager/templates/deployment-history.md) 참조하세요. 
 
### <a name="failed-assessments"></a>실패한 평가 

**평가 실행 실패** - SQL IaaS 확장에서 평가를 실행하는 동안 문제가 발생했음을 나타냅니다. 자세한 오류 메시지는 VM 내의 확장 로그에서 사용할 수 `C:\WindowsAzure\Logs\Plugins\Microsoft.SqlServer.Management.SqlIaaSAgent\2.0.X.Y` 있습니다. 여기서 `2.0.X.Y ` 은 최신 버전 폴더가 있습니다.  

**Log Analytics 작업 영역에** 대한 업로드 결과 실패 - MMA(Microsoft Monitoring Agent)가 시간 제한 방식으로 결과를 업로드할 수 없음을 나타냅니다. MMA 확장이 [올바르게 프로비전되었는지](../../../azure-monitor/visualize/vmext-troubleshoot.md) 확인하고 이 [문제 해결 가이드](../../../azure-monitor/agents/agent-windows-troubleshoot.md)에 나열된 연결 문제 및 데이터 수집 문제를 참조하세요. 

>[!TIP]
>Windows TLS 1.0 이상이 적용되고 [여기에](/troubleshoot/windows-server/windows-security/restrict-cryptographic-algorithms-protocols-schannel#schannel-specific-registry-keys)설명된 대로 이전 SSL 프로토콜을 사용하지 않도록 설정했다면 .NET Framework 강력한 암호화를 사용하도록 [구성되어](../../../azure-monitor/agents/agent-windows.md#configure-agent-to-use-tls-12) 있는지도 확인해야 합니다. 

**Log Analytics 작업 영역 데이터 보존으로 인해 결과가 만료되었습니다.** 이는 해당 보존 정책에 따라 결과가 Log Analytics 작업 영역에 더 이상 유지되지 않는다는 것을 나타냅니다. 작업 [영역의 보존 기간을 변경할](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure VM에서 SQL Server SQL Server IaaS 확장에 SQL Server [VM을](sql-agent-extension-manually-register-single-vm.md)등록하려면 [자동 설치,](sql-agent-extension-automatic-registration-all-vms.md)단일 VM 또는 [대량 VM 문서를](sql-agent-extension-manually-register-vms-bulk.md)참조하세요.
- Azure [VM에서](manage-sql-vm-portal.md) SQL Server SQL Server IaaS 확장에서 사용할 수 있는 더 많은 기능에 대해 알아보려면 Azure Portal 사용하여 SQL Server VM 관리를 참조하세요.
