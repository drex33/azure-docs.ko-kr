---
title: Azure Backup 메트릭을 사용하여 백업 상태 모니터링(미리 보기)
description: 이 문서에서는 Azure Backup 백업 상태를 모니터링하는 데 사용할 수 있는 메트릭에 대해 알아봅니다.
ms.topic: conceptual
author: v-amallick
ms.date: 10/20/2021
ms.author: v-amallick
ms.service: backup
ms.openlocfilehash: 31e68c8a25dcf4fee199a7c73869cc5de64793e9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053014"
---
# <a name="monitor-the-health-of-your-backups-using-azure-backup-metrics-preview"></a>Azure Backup 메트릭을 사용하여 백업 상태 모니터링(미리 보기)

Azure Backup 백업 상태를 모니터링할 수 있는 Azure Monitor 통해 기본 제공 메트릭 집합을 제공합니다. 또한 메트릭이 정의된 임계값을 초과할 때 트리거되는 경고 규칙을 구성할 수 있습니다.

Azure Backup 다음과 같은 주요 기능을 제공합니다.

* 관련 추세와 함께 백업 항목의 백업 및 복원 상태와 관련된 첫 번째 메트릭을 보는 기능
* 이러한 메트릭에 대한 사용자 지정 경고 규칙을 작성하여 백업 항목의 상태를 효율적으로 모니터링하는 기능
* 이메일, ITSM, webhook, 논리 앱 등과 같이 Azure Monitor 지원하는 다양한 알림 채널로 발생된 메트릭 경고를 라우팅할 수 있습니다.

[Azure Monitor 메트릭에 대해 자세히 알아봅니다.](/azure/azure-monitor/essentials/data-platform-metrics)

## <a name="supported-scenarios"></a>지원되는 시나리오

- 다음 워크로드 유형에 대한 기본 제공 메트릭을 지원합니다.

  - Azure VM, Azure VM의 SQL 데이터베이스
  - Azure VM에서 데이터베이스 SAP HANA
  - Azure Files.

  HANA 인스턴스 워크로드 유형에 대한 메트릭은 현재 지원되지 않습니다.

- 각 지역 및 구독의 모든 Recovery Services 자격 증명 모음에 대한 메트릭을 한 번에 볼 수 있습니다. Azure Portal 더 큰 범위에 대한 메트릭 보기는 현재 지원되지 않습니다. 메트릭 경고 규칙을 구성하는 데에도 동일한 제한이 적용됩니다.

## <a name="supported-built-in-metrics"></a>지원되는 기본 제공 메트릭

현재 Azure Backup 다음 메트릭을 지원합니다.

- **Backup 상태 이벤트:** 이 메트릭의 값은 특정 시간 내에 자격 증명 모음에 대해 발생한 백업 작업 상태와 관련된 상태 이벤트의 수를 나타냅니다. 백업 작업이 완료되면 Azure Backup 서비스에서 백업 상태 이벤트를 만듭니다. 작업 상태(예: 성공 또는 실패)에 따라 이벤트와 연결된 차원이 달라집니다.

- **복원 상태 이벤트:** 이 메트릭의 값은 특정 시간 내에 자격 증명 모음에 대해 발생한 복원 작업 상태와 관련된 상태 이벤트의 수를 나타냅니다. 복원 작업이 완료되면 Azure Backup 서비스는 복원 상태 이벤트를 만듭니다. 작업 상태(예: 성공 또는 실패)에 따라 이벤트와 연결된 차원이 달라집니다.

기본적으로 개수는 자격 증명 모음 수준에 표시됩니다. 특정 백업 항목 및 작업 상태에 대한 개수를 보려면 지원되는 차원에 대한 메트릭을 필터링할 수 있습니다.

다음 표에서는 백업 상태 이벤트 및 복원 상태 이벤트 메트릭에서 지원하는 차원을 나열합니다.
 
| **차원 이름**        | **설명**   | 
| ----------------------------| ----------------- |
| 데이터 원본 ID               | 작업과 연결된 [데이터 원본의](azure-backup-glossary.md#datasource) 고유 ID입니다. <br><br> <ul><li> VM 및 파일과 같은 Azure 리소스의 경우 리소스의 ARM ID(Azure Resource Manager ID)가 포함됩니다. <br> 예를 들어 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM` </li><br><br> <li> VM 내의 SQL/HANA 데이터베이스의 경우 VM의 ARM ID와 데이터베이스 세부 정보가 포함됩니다. <br> 예를 들어 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM/providers/Microsoft.RecoveryServices/backupProtectedItem/SQLDataBase;mssqlserver;msdb` </li></ul>  <br><br> SQL AG 데이터베이스 백업의 경우 이러한 시나리오에는 VM(데이터 **원본)이** 없기 때문에 데이터 원본 ID 필드가 비어 있습니다. AG 내의 특정 데이터베이스에 대한 메트릭을 보려면 **백업 인스턴스 ID** 필드를 사용합니다.|
| 데이터 원본 유형             | 작업과 연결된 [데이터 원본의](azure-backup-glossary.md#datasource) 형식입니다. 지원되는 데이터 원본 유형은 다음과 같습니다. <br><br> <ul><li> Microsoft.Compute/virtualMachines(Azure Virtual Machines)</li> <br><br> <li> Microsoft. Storage/storageAccounts/fileServices/shares(Azure Files) </li>  <br><br> <li> SQLDatabase(Azure VM의 SQL) </li><br><br> <li> SAPHANADataBase(Azure VM의 SAP HANA)</li></ul>   |
| 백업 인스턴스 ID          | 작업과 연결된 [백업 인스턴스의](azure-backup-glossary.md#backup-instance--backup-item) ARM ID입니다. <br><br> 예를 들어 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;iaasvmcontainerv2;testRG;testVM` |
| 백업 인스턴스 이름        | 쉽게 읽을 수 있는 백업 인스턴스의 이름입니다. `{protectedContainerName};{backupItemFriendlyName}`형식입니다. <br><br> 예를 들어 `testStorageAccount;testFileShare`      |
| 상태               | 작업이 완료된 후의 백업 항목 상태를 나타냅니다. 정상, 일시적 비정상, 영구 비정상, 일시적 저하, 영구 저하 값 중 하나를 사용할 수 있습니다. <br> <br> <ul> <li> 백업/복원 작업이 성공하면 상태가 _정상인_ 상태 이벤트가 나타납니다. </li><br><br><li> 서비스 오류로 인해 작업 실패에 비정상이 표시되고 사용자 오류로 인해 _Degraded가_ 실패하는 것으로 나타납니다. </li> <br><br><li> 동일한 백업 항목에 대해 동일한 오류가 반복적으로 발생하면 상태가 _일시적인 비정상/저하됨에서_ 영구 _비정상/저하됨_ 으로 변경됩니다. </li></ul> |

## <a name="monitoring-scenarios"></a>모니터링 시나리오

### <a name="view-metrics-in-the-azure-portal"></a>Azure Portal에서 메트릭 보기

Azure Portal 메트릭을 보려면 다음 단계를 수행합니다.

1. Azure Portal **Backup 센터로** 이동하고 메뉴에서 **메트릭을** 클릭합니다.

   :::image type="content" source="./media/metrics-overview/backup-center-metrics-selection-inline.png" alt-text="백업 센터에서 메트릭을 선택하는 방법을 보여주는 스크린샷." lightbox="./media/metrics-overview/backup-center-metrics-selection-expanded.png":::

1. 메트릭을 보려는 자격 증명 모음 또는 자격 증명 모음 그룹을 선택합니다.

   현재 메트릭을 볼 수 있는 최대 범위는 특정 구독 및 지역의 모든 Recovery Services 자격 증명 모음입니다. 예를 들어 _TestSubscription1의_ 미국 동부에 있는 모든 Recovery Services 자격 증명 모음입니다.

1. **메트릭을 선택하여** _백업 상태 이벤트 또는 복원 상태 이벤트를 확인합니다._

   그러면 자격 증명 모음에 대한 상태 이벤트 수를 보여주는 차트가 렌더링됩니다. 화면 맨 위에 있는 필터를 사용하여 시간 범위 및 집계 세분성을 조정할 수 있습니다.

   :::image type="content" source="./media/metrics-overview/metrics-chart-inline.png" alt-text="메트릭을 선택하는 프로세스를 보여주는 스크린샷." lightbox="./media/metrics-overview/metrics-chart-expanded.png":::

1. 다른 차원별로 메트릭을 필터링하려면 **필터 추가** 단추를 클릭하고 관련 차원 값을 선택합니다. 

   - 예를 들어 Azure VM 백업에 대해서만 상태 이벤트 수를 확인하려면 필터를 `Datasource Type = Microsoft.Compute/virtualMachines` 추가합니다. 
   - 자격 증명 모음 내의 특정 데이터 원본 또는 백업 인스턴스에 대한 상태 이벤트를 보려면 데이터 원본 ID/백업 인스턴스 ID 필터를 사용합니다.
   - 실패한 백업에 대해서만 상태 이벤트를 보려면 비정상 또는 성능 저하 상태에 해당하는 값을 선택하여 HealthStatus에서 필터를 사용합니다.

   :::image type="content" source="./media/metrics-overview/metrics-filters-inline.png" alt-text="다른 차원별로 메트릭을 필터링하는 프로세스를 보여주는 스크린샷." lightbox="./media/metrics-overview/metrics-filters-expanded.png":::

### <a name="configure-alerts-and-notifications-on-your-metrics"></a>메트릭에 대한 경고 및 알림 구성

메트릭에 대한 경고 및 알림을 구성하려면 다음 단계를 수행합니다.

1. 메트릭 차트의 맨 위에서 **새 경고 규칙을** 클릭합니다.

1. 경고를 만들 범위를 선택합니다.   <br><br>    범위 제한은 메트릭 보기 섹션에 설명된 [제한과 동일합니다.](#view-metrics-in-the-azure-portal)

1. 경고를 발생시켜야 하는 조건을 선택합니다.      <br><br>   기본적으로 일부 필드는 메트릭 차트의 선택 항목에 따라 미리 채워집니다. 필요에 따라 매개 변수를 편집할 수 있습니다. 자격 증명 모음의 각 데이터 원본에 대한 개별 경고를 생성하려면 메트릭 경고 규칙에서 **차원** 선택을 사용합니다. 다음은 몇 가지 시나리오입니다.

   - 각 데이터 원본에 대해 실패한 백업 작업에 대한 경고 발생:

     **경고 규칙: 의 최근 24시간 동안 백업 상태 이벤트가 0으로 > 경우 경고를 발생합니다.**
     - Dimensions["HealthStatus"]= "Persistent Unhealthy / Transient Unhealthy"
     - Dimensions["DatasourceId"]= "All current and future values"

   - 자격 증명 모음의 모든 백업이 하루 동안 성공한 경우 경고 발생:

     **경고 규칙: 의 최근 24시간 동안 백업 상태 이벤트가 < 1인 경우 경고를 발생합니다.**
     - Dimensions["HealthStatus"]="Persistent Unhealthy / Transient Unhealthy / Persistent Degraded / Transient Degraded"

   :::image type="content" source="./media/metrics-overview/metric-alert-condition-inline.png" alt-text="경고가 발생되어야 하는 조건을 선택하는 옵션을 보여주는 스크린샷." lightbox="./media/metrics-overview/metric-alert-condition-expanded.png":::

   >[!NOTE]
   >경고 규칙 조건의 일부로 더 많은 차원을 선택하면 비용이 증가합니다(가능한 차원 값의 고유한 조합 수에 비례). 더 많은 차원을 선택하면 발생된 경고에 대한 더 많은 컨텍스트를 얻을 수 있습니다.


1. 작업 그룹을 사용하여 이러한 경고에 대한 알림을 구성하려면 작업 그룹을 경고 규칙의 일부로 구성하거나 별도의 작업 규칙을 만듭니다.

   메일, ITSM, webhook, 논리 앱, SMS와 같은 다양한 알림 채널을 지원합니다. [작업 그룹 에 대해 자세히 알아봅니다.](/azure/azure-monitor/alerts/action-groups)

   :::image type="content" source="./media/metrics-overview/action-group-inline.png" alt-text="작업 그룹을 사용하여 이러한 경고에 대한 알림을 구성하는 프로세스를 보여주는 스크린샷" lightbox="./media/metrics-overview/action-group-expanded.png":::

1. 자동 해결 동작 구성 - 필요에 따라 메트릭 경고를 _상태 비주성_ 또는 _상태 비지정으로_ 구성할 수 있습니다.

   - 오류와 관계없이 모든 작업 실패에 대해 경고를 생성하려면 동일한 기본 원인(상태 비저장 동작)으로 인해 경고 규칙에서 경고 자동 해결 옵션을 선택 **취소합니다.**
   - 또는 경고를 상태 보관으로 구성하려면 동일한 확인란을 선택합니다. 따라서 범위에서 메트릭 경고가 발생하면 다른 오류로 새 메트릭 경고가 생성되지 않습니다. 경고 생성 조건이 세 번의 연속 평가 주기 동안 false로 평가되면 경고가 자동으로 해결됩니다. 조건이 다시 true로 평가되면 새 경고가 생성됩니다.

[Azure Monitor 메트릭 경고의 상태 비주소 및 상태 비지정 동작에 대해 자세히 알아봅니다.](/azure/azure-monitor/alerts/alerts-troubleshoot-metric#make-metric-alerts-occur-every-time-my-condition-is-met)

:::image type="content" source="./media/metrics-overview/auto-resolve-alert-inline.png" alt-text="자동 확인 동작을 구성하는 프로세스를 보여주는 스크린샷." lightbox="./media/metrics-overview/auto-resolve-alert-expanded.png":::

### <a name="managing-alerts"></a>경고 관리

발생된 메트릭 경고를 보려면 다음 단계를 수행합니다.

1. 백업 **센터**  >  **경고 로 이동합니다.**
1. **신호 유형**  =  **메트릭** 및 **구성된 경고 유형에** 대한 필터링입니다.  =  
1. 경고를 클릭하여 경고에 대한 자세한 내용을 보고 해당 상태를 변경합니다.

   :::image type="content" source="./media/metrics-overview/backup-center-metric-alerts-inline.png" alt-text="발생된 메트릭 경고를 보는 프로세스를 보여주는 스크린샷." lightbox="./media/metrics-overview/backup-center-metric-alerts-expanded.png":::

>[!NOTE]
>경고에는 **모니터 조건(발생/해결됨)** 및 **경고 상태(신규/Ack/종료됨)의** 두 필드가 있습니다.
>- **경고 상태:** 이 필드를 편집할 수 있습니다(아래 스크린샷 참조).
>- **모니터 조건:** 이 필드를 편집할 수 없습니다. 이 필드는 서비스 자체가 경고를 해결하는 시나리오에서 더 많이 사용됩니다. 예를 들어 메트릭 경고의 자동 해결 동작은 **모니터 조건** 필드를 사용하여 경고를 해결합니다.


#### <a name="datasource-alerts-and-global-alerts"></a>데이터 원본 경고 및 글로벌 경고

경고 규칙 구성에 따라 발생된 경고는 백업 센터의 **데이터 원본 경고** 섹션 또는 글로벌 **경고** 섹션 아래에 표시됩니다.

- 경고에 연결된 데이터 원본 ID 차원이 있는 경우 데이터 원본 경고 아래에 발생된 **경고가 표시됩니다.**
- 경고에 연결된 데이터 원본 ID 차원이 없으면 특정 데이터 원본에 경고를 묶는 정보가 없으면 **발생된** 경고가 글로벌 경고 아래에 표시됩니다.

[여기에서 데이터 원본 및 글로벌 경고에 대해 자세히 알아보세요.](backup-center-monitor-operate.md#alerts)

### <a name="accessing-metrics-programmatically"></a>프로그래밍 방식으로 메트릭 액세스

PowerShell, CLI 또는 REST API 같은 다양한 프로그래밍 방식 클라이언트를 사용하여 메트릭 기능에 액세스할 수 있습니다. 자세한 내용은 [Azure Monitor REST API 설명서를](/azure/azure-monitor/essentials/rest-api-walkthrough) 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Azure Backup 모니터링 및 보고에 대해 자세히 알아보세요.](monitoring-and-alerts-overview.md)
- [Azure Monitor 메트릭에 대해 자세히 알아봅니다.](/azure/azure-monitor/essentials/data-platform-metrics)
- [Azure 경고에 대해 자세히 알아보세요.](/azure/azure-monitor/alerts/alerts-overview)