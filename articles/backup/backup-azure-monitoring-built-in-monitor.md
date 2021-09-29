---
title: Azure Backup 보호된 워크로드 모니터링
description: 이 문서에서는 Azure Portal을 사용하여 Azure Backup 워크로드의 모니터링 및 알림 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/06/2021
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: f3d353f7d42baf1f9cc968cb37baac1077a35085
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231675"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup 워크로드 모니터링

Azure Backup은 백업 요구 사항 및 인프라 토폴로지를 기준으로 하는 여러 백업 솔루션을 제공합니다(온-프레미스 및 Azure). 모든 백업 사용자 또는 관리자는 모든 솔루션에서 발생하는 상황을 확인하고 중요한 시나리오에서 알림이 발생할 것으로 예측할 수 있습니다. 이 문서에서는 Azure Backup 서비스에서 제공하는 모니터링 및 알림 기능에 대해 자세히 설명합니다.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 항목

Recovery Services 자격 증명 모음을 통해 모든 백업 항목을 모니터링할 수 있습니다. 자격 증명 모음의 **백업 항목** 섹션으로 이동하면 자격 증명 모음과 연결된 각 워크로드 유형의 백업 항목 수를 제공하는 보기가 열립니다. 행을 클릭하면 지정된 워크로드 유형의 모든 백업 항목과 각 항목에 대한 마지막 백업 상태에 대한 정보, 사용 가능한 최근 복원 지점 등을 나열하는 상세 보기가 열립니다.

![RS 자격 증명 모음 백업 항목을 보여 주는 스크린샷](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> DPM을 사용하여 Azure에 백업된 항목의 경우 목록에 DPM 서버를 사용하여 보호된 모든 데이터 원본(디스크 및 온라인)이 표시됩니다. 백업 데이터가 보존되는 데이터 원본에 대한 보호가 중지된 경우에도 데이터 원본은 포털에 계속 나열됩니다. 데이터 원본의 세부 정보로 이동하여 복구 지점이 디스크, 온라인 또는 둘 다에 있는지를 확인할 수 있습니다. 또한 온라인 보호가 중지되었지만 데이터가 유지되는 데이터 원본의 경우 데이터가 완전히 삭제될 때까지 온라인 복구 지점에 대한 비용 청구는 계속됩니다.
>
> 백업 항목이 Recovery Services 자격 증명 모음 포털에 표시되려면 DPM 버전이 DPM 1807(5.1.378.0) 또는 DPM 2019(버전 10.19.58.0 이상)이어야 합니다.

## <a name="backup-jobs-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 작업

Azure Backup은 Azure Backup으로 보호되는 워크로드에 대한 기본 제공 모니터링 및 경고 기능을 제공합니다. Recovery Services 자격 증명 모음 설정에서 **모니터링** 섹션은 기본 제공 작업 및 경고를 제공합니다.

![RS 자격 증명 모음 기본 제공 모니터링을 보여 주는 스크린샷](media/backup-azure-monitoring-laworkspace/rs-vault-inbuilt-monitoring-menu.png)

작업(job)은 백업 구성, 백업, 복원, 삭제 등의 작업(operation)을 수행하는 경우에 생성됩니다.

다음 Azure Backup 솔루션의 작업이 여기에 표시됩니다.

- Azure VM 백업
- Azure 파일 백업
- Azure 워크로드 백업(예: SQL 및 SAP HANA)
- MARS(Microsoft Azure Recovery Services) 에이전트

SC-DPM(System Center Data Protection Manager) 및 MABS(Microsoft Azure Backup Server)의 작업이 표시되지 않습니다.

> [!NOTE]
> Azure VM 내에서 SQL 및 SAP HANA 백업 같은 Azure 워크로드에는 상당한 수의 백업 작업이 있습니다. 예를 들어, 로그 백업은 15분마다 실행될 수 있습니다. 따라서 이러한 DB 워크로드의 경우 사용자가 트리거한 작업만 표시됩니다. 예약된 백업 작업은 표시되지 않습니다.

## <a name="backup-alerts-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 경고

경고는 사용자가 관련 작업을 수행할 수 있도록 사용자에게 알림을 제공하는 경우에 주로 발생합니다. **백업 경고** 섹션에는 Azure Backup 서비스에서 생성된 경고가 표시됩니다. 이러한 경고는 서비스에서 정의되며 사용자가 만들 수 없습니다.

### <a name="alert-scenarios"></a>경고 시나리오

다음 시나리오는 서비스에서 경고 표시 가능 시나리오로 정의됩니다.

- 백업/복원 실패
- MARS(Microsoft Azure Recovery Services) 에이전트에 대해 결과를 나타내며 백업을 성공했습니다.
- 데이터 보존을 사용하여 보호 중지/데이터 삭제를 사용하여 보호 중지
- 자격 증명 모음에 대해 일시 삭제 기능을 사용하지 않도록 설정
- 데이터베이스 워크로드에 지원되지 않는 백업 유형

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>다음 Azure Backup 솔루션의 경고가 여기에 표시됩니다.

- Azure VM 백업
- Azure 파일 백업
- SQL, SAP HANA 같은 Azure 워크로드 백업
- MARS(Microsoft Azure Recovery Services) 에이전트

> [!NOTE]
> 여기에는 SC-DPM(System Center Data Protection Manager) 및 MABS(Microsoft Azure Backup Server)의 경고가 표시되지 않습니다.

### <a name="consolidated-alerts"></a>통합된 경고

SQL 및 SAP HANA 같은 Azure 워크로드 백업 솔루션의 경우 로그 백업은 매우 자주 생성될 수 있습니다(정책에 따라 최대 15분마다). 따라서 로그 백업 실패가 매우 자주 발생할 수도 있습니다(최대 15분마다). 이 시나리오에서는 실패할 때마다 경고가 나타나는 경우 최종 사용자가 작업하기 어려워집니다. 따라서 첫 번째 발생에 대한 경고를 보낸 후, 후속 실패가 동일한 근본 원인으로 인해 발생하는 경우 추가 경고가 생성되지 않습니다. 첫 번째 경고가 실패 횟수로 업데이트됩니다. 그러나 사용자가 경고를 비활성화한 경우 다음 번에 경고가 발생하면 다른 경고가 트리거되고 해당 발생에 대한 첫 번째 경고로 처리됩니다. 이와 같은 방식으로 Azure Backup은 SQL 및 SAP HANA 백업에 대한 경고 통합을 수행합니다. 주문형 백업 작업은 통합되지 않습니다.

### <a name="exceptions-when-an-alert-is-not-raised"></a>경고가 발생하지 않는 예외

실패 시 경고가 발생하지 않는 몇 가지 예외가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- 사용자가 실행 중인 작업을 명시적으로 취소함
- 다른 백업 작업이 진행 중이므로 작업이 실패합니다(이전 작업이 완료될 때까지 기다려야 하므로 여기에서 수행할 작업이 없음).
- 백업된 Azure VM이 더 이상 존재하지 않으므로 VM 백업 작업이 실패합니다.
- [통합된 경고](#consolidated-alerts)

위의 예외는 이러한 작업의 결과(주로 사용자가 트리거함)가 포털/PS/CLI 클라이언트에 즉시 표시된다는 사실에서 설계되었습니다. 따라서 사용자는 즉시 인식하게 되며 알림이 필요하지 않습니다.

### <a name="alert-types"></a>경고 유형

경고 심각도에 따라 다음과 같은 세 가지 유형으로 경고를 정의할 수 있습니다.

- **중요**: 원칙적으로 모든 백업 또는 복구 실패(예약되었거나 사용자가 트리거)로 인해 경고가 생성되고, 중요 알림 및 백업 삭제와 같은 파괴적 작업으로 표시됩니다.
- **경고**: 백업 작업이 성공하지만 경고가 거의 없으면 경고 알림으로 표시됩니다. 경고 알림은 현재 Azure Backup 에이전트 백업에 대해서만 사용할 수 있습니다.
- **정보**: 현재 Azure Backup 서비스에 의해 생성된 정보 알림이 없습니다.

## <a name="notification-for-backup-alerts"></a>백업 경고에 대한 알림

> [!NOTE]
> 알림 구성은 Azure Portal을 통해서만 수행할 수 있습니다. PS/CLI/REST API/Azure Resource Manager 템플릿은 지원되지 않습니다.

경고가 발생하면 사용자에게 알림이 제공됩니다. Azure Backup는 메일을 통해 기본 제공 알림 메커니즘을 제공합니다. 경고가 생성될 때 알림을 받을 개별 메일 주소 또는 메일 그룹을 지정할 수 있습니다. 각 개별 경고에 대한 알림을 받을지 또는 시간별 요약으로 그룹화한 후 알림을 받을지 선택할 수도 있습니다.

![RS 자격 증명 모음 기본 제공 메일 알림 스크린샷](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

알림이 구성되면 환영 또는 소개 메일을 받게 됩니다. 이렇게 하면 경고가 발생할 때 Azure Backup이 주소로 메일을 보낼 수 있습니다.<br>

빈도가 시간별 요약으로 설정되고, 경고가 발생하고 1시간 안에 해결되면 예정된 시간별 요약에 포함되지 않게 됩니다.

> [!NOTE]
>
> - **데이터 삭제로 보호 중지** 와 같은 파괴적인 작업을 수행하면, Recovery Services 자격 증명 모음에 대해 알림을 구성하지 않았더라도 경고가 발생한 후 메일이 구독 소유자, 관리자 및 공동 관리자에게 전송됩니다.
> - 성공한 작업에 대한 알림을 구성하려면 [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)를 사용합니다.

## <a name="inactivating-alerts"></a>경고 비활성화

활성 경고를 비활성화/해결하려면 비활성화할 경고에 해당하는 목록 항목을 선택할 수 있습니다. 이렇게 하면 경고에 대한 자세한 정보를 표시하는 화면이 열리고 맨 위에 **비활성화** 단추가 표시됩니다. 이 단추를 선택하면 경고 상태가 **비활성** 으로 변경됩니다. 해당 경고에 해당하는 목록 항목을 마우스 오른쪽 단추로 클릭하고 **비활성화** 를 선택하여 경고를 비활성화할 수도 있습니다.

![Backup 센터 경고 비활성 스크린샷](media/backup-azure-monitoring-laworkspace/vault-alert-inactivate.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Azure Backup에 대한 Azure Monitor 경고(미리 보기)

또한 Azure Backup은 Azure Monitor를 통해 경고를 제공하여 사용자가 백업을 비롯한 다양한 Azure 서비스에서 경고 관리를 위한 일관된 환경을 제공할 수 있도록 합니다. Azure Monitor 경고를 사용하여 메일, ITSM, 웹후크, 논리 앱 등, Azure Monitor에서 지원하는 알림 채널로 경고를 라우팅할 수 있습니다.

현재 Azure Backup에서는 두 가지 유형의 기본 제공 경고를 사용할 수 있습니다.

* **보안 경고**: 백업 데이터 삭제 또는 자격 증명 모음에 대한 일시 삭제 기능 비활성화와 같은 시나리오의 경우 보안 경고(심각도 Sev 0)가 발생하며 Azure Portal에 표시되거나 다른 클라이언트(PowerShell, CLI, REST API)를 통해 사용됩니다. 보안 경고는 기본적으로 생성되며 해제할 수 없습니다. 그러나 알림(예: 메일)이 발생되어야 하는 시나리오를 제어할 수 있습니다. 알림을 구성하는 방법에 대한 자세한 내용은 [작업 규칙](../azure-monitor/alerts/alerts-action-rules.md)을 참조하세요.
* **작업 실패 경고**: 백업 실패 및 복원 실패와 같은 시나리오의 경우 Azure Backup에서는 Azure Monitor(심각도 Sev 1)를 통해 기본 제공 경고를 제공합니다. 보안 경고와 달리 작업 실패 시나리오에 대한 Azure Monitor의 경고를 해제하도록 선택할 수 있습니다. 예를 들어 Log Analytics를 통해 작업 실패에 대한 사용자 지정 경고 규칙을 이미 구성했으며 작업이 실패할 때마다 기본 제공 경고를 발생할 필요가 없는 경우입니다. 기본적으로 작업 실패에 대한 경고는 해제됩니다. 자세한 내용은 [해당 시나리오에 대한 알림 켜기 섹션](#turning-on-azure-monitor-alerts-for-job-failure-scenarios)을 참조하세요.
 
다음 표에는 Azure Monitor 및 지원되는 워크로드/자격 증명 모음 유형을 통해 현재 사용할 수 있는 여러 백업 경고(미리 보기 상태)가 요약되어 있습니다.

| **경고 범주** | **경고 이름** | **지원되는 워크로드 유형/자격 증명 모음 유형** | **설명** | 
| ------------------ | -------------  | ------------------------------------------ | -------- |
| 보안 | 백업 데이터 삭제 | <li> Azure Virtual Machine <br><br> <li> Azure VM의 SQL(AG가 아닌 시나리오) <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure Backup 에이전트 <br><br> <li> DPM <br><br> <li> Azure Backup 서버 <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure Blob <br><br> <li> Azure Managed Disks  | 이 경고는 사용자가 백업을 중지하고 백업 데이터를 삭제할 때 발생합니다(참고 – 자격 증명 모음의 일시 삭제 기능이 사용되지 않는 경우 백업 데이터 삭제 경고가 수신되지 않음). |
| 보안 | 예정된 제거 | <li> Azure Virtual Machine <br><br> <li> Azure VM의 SQL(AG가 아닌 시나리오) <br><br> <li> Azure VM의 SAP HANA | 일시 삭제를 지원하는 모든 워크로드의 경우 Azure Backup 서비스에서 항목의 백업 데이터가 영구적으로 제거되기까지 2일이 남은 경우 이 경고가 발생합니다. | 
| 보안 | 제거 완료 | <li> Azure Virtual Machine <br><br> <li> Azure VM의 SQL(AG가 아닌 시나리오) <br><br> <li> Azure VM의 SAP HANA | 백업 데이터 삭제 |
| 보안 | 자격 증명 모음에 대해 일시 삭제를 사용하지 않음 | Recovery Services 자격 증명 모음 | 이 경고는 항목의 일시 삭제된 백업 데이터를 Azure Backup 서비스에서 영구적으로 삭제한 경우 발생합니다. | 
| 작업 | 백업 실패 | <li> Azure Virtual Machine <br><br> <li> Azure VM의 SQL(AG가 아닌 시나리오) <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure Backup 에이전트 <br><br> <li> Azure 파일 <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure Managed Disks | 이 경고는 백업 작업이 실패했을 때 발생합니다. 기본적으로 백업 실패에 대한 경고는 해제됩니다. 자세한 내용은 [해당 시나리오에 대한 알림 켜기 섹션](#turning-on-azure-monitor-alerts-for-job-failure-scenarios)을 참조하세요. | 
| 작업 | 복원 실패 | <li> Azure Virtual Machine <br><br> <li> Azure VM의 SQL(AG가 아닌 시나리오) <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure Backup 에이전트 <br><br> <li> Azure 파일 <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure Blob <br><br> <li> Azure Managed Disks| 이 경고는 복원 작업이 실패했을 때 발생합니다. 기본적으로 복원 실패에 대한 경고는 해제됩니다. 자세한 내용은 [해당 시나리오에 대한 알림 켜기 섹션](#turning-on-azure-monitor-alerts-for-job-failure-scenarios)을 참조하세요. | 

### <a name="turning-on-azure-monitor-alerts-for-job-failure-scenarios"></a>Azure Monitor의 작업 실패 시나리오에 대한 경고 설정

백업 실패 및 복원 실패 시나리오에 대한 Azure Monitor 경고를 옵트인하려면 아래 단계를 따르세요.

1. Azure Portal로 이동하여 **미리 보기 기능** 을 검색합니다.

    ![포털에서 미리 보기 기능을 보기 위한 스크린샷](media/backup-azure-monitoring-laworkspace/portal-preview-features.png)

2. 옵트인할 수 있는 모든 미리 보기 기능 목록을 볼 수 있습니다.

    * Recovery Services 자격 증명 모음에 백업된 워크로드에 대한 작업 실패 알림을 받으려면 Microsoft.RecoveryServices 공급자(3열)에 해당하는 **EnableAzureBackupJobFailureAlertsToAzureMonitor** 라는 플래그를 선택합니다.
    * Backup 자격 증명 모음에 백업된 워크로드에 대한 작업 실패 알림을 받으려면 Microsoft.DataProtection 공급자(3열)에 해당하는 **EnableAzureBackupJobFailureAlertsToAzureMonitor** 라는 플래그를 선택합니다.

    ![경고 미리 보기 등록 스크린샷](media/backup-azure-monitoring-laworkspace/alert-preview-feature-flags.png)

3. **등록** 을 클릭하여 구독에서 이 기능을 사용하도록 설정합니다.
    > [!NOTE]
    > 등록이 적용되려면 최대 24시간이 걸릴 수 있습니다. 여러 구독에 대해 이 기능을 사용으로 설정하려면 화면 상단에서 관련 구독을 선택하여 위의 프로세스를 반복합니다. 경고를 계속 수신하려면 초기 등록 후 새 리소스가 구독에 생성된 경우 미리 보기 플래그를 다시 등록하는 것이 좋습니다.

4. 또한 기능 등록 정보가 예상대로 Azure Backup 서비스와 동기화되도록 리소스 공급자를 등록하는 것이 좋습니다. 리소스 공급자를 등록하려면 기능 플래그를 등록한 구독에서 다음 PowerShell 명령을 실행합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace <ProviderNamespace>
```

Recovery Services 자격 증명 모음에 대한 경고를 받으려면 _ProviderNamespace_ 매개 변수에 _Microsoft.RecoveryServices_ 값을 사용합니다. Backup 자격 증명 모음에 대한 경고를 받으려면 _Microsoft.DataProtection_ 값을 사용합니다.

### <a name="viewing-fired-alerts-in-the-azure-portal"></a>Azure Portal에서 발생된 경고 보기 

자격 증명 모음에 대한 경고가 발생하면 Backup 센터로 이동하여 Azure Portal에서 경고를 볼 수 있습니다. **개요** 탭에서 심각도별로 구분된 활성 경고 요약을 볼 수 있습니다. 표시되는 경고에는 다음 두 가지 종류가 있습니다.

* **데이터 원본 경고**: 백업 중인 특정 데이터 원본에 연결된 경고(예: VM 백업 또는 복원 실패, 데이터베이스의 백업 데이터 삭제 등)가 **데이터 원본 경고** 섹션 아래에 표시됩니다.
* **전체 경고**: 특정 데이터 원본에 연결되지 않은 경고(예: 자격 증명 모음의 일시 삭제 기능을 사용하지 않게 설정)는 **글로벌 경고** 섹션에 나타납니다.

위의 각 경고 유형은 **보안** 및 **구성** 경고로 구분됩니다. 현재 보안 경고에는 백업 데이터를 삭제하거나 자격 증명 모음의 일시 삭제를 사용하지 않게 설정하는 시나리오가 포함됩니다(위 섹션에 자세히 설명된 해당 워크로드의 경우). 구성된 경고에는 백업 실패 및 복원 실패가 포함됩니다. 해당 경고는 미리 보기 포털에 기능을 등록한 후에만 발생하기 때문입니다.

![백업 센터에서 경고를 보여 주는 스크린샷](media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alerts.png)

숫자(또는 **경고** 메뉴 항목)를 클릭하면 관련 필터가 적용된 모든 활성 경고 목록이 열립니다. 구독, 리소스 그룹, 자격 증명 모음, 심각도, 상태 등과 같은 다양한 속성을 기준으로 필터링할 수 있습니다. 경고 중 하나를 클릭하면 영향을 받는 데이터 원본, 경고 설명 및 권장 사항 등에 관한 자세한 내용을 볼 수 있습니다.

![경고 세부 정보를 표시하는 스크린샷](media/backup-azure-monitoring-laworkspace/backup-center-alert-details.png) 

**경고 상태 변경** 을 클릭하여 경고 상태를 **확인됨** 또는 **닫힘** 으로 변경할 수 있습니다.

![경고 상태를 변경하는 스크린샷](media/backup-azure-monitoring-laworkspace/backup-center-change-alert-state.png) 

> [!NOTE]
> - Backup 센터에는 현재 Azure 기반 워크로드의 경고만 표시됩니다. 온-프레미스 리소스에 대한 경고를 보려면 Recovery Services 자격 증명 모음으로 이동하여 **경고** 메뉴 항목을 클릭합니다.
> - Azure Monitor 경고만 Backup 센터에 표시됩니다. 이전 경고 솔루션(Recovery Services 자격 증명 모음의 [백업 경고](#backup-alerts-in-recovery-services-vault) 탭을 통해 액세스)에서 발생한 경고는 Backup 센터에 표시되지 않습니다.
Azure Monitor 경고에 대한 자세한 내용은 [Azure의 경고 개요](../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

### <a name="configuring-notifications-for-alerts"></a>경고 알림 구성

Azure Monitor 경고의 알림을 구성하려면 작업 규칙을 만들어야 합니다. 다음 단계에서는 지정된 메일 주소로 메일 알림을 보내는 작업 규칙을 만드는 방법을 보여 줍니다. ITSM, 웹후크, 논리 앱 등의 다른 알림 채널로 해당 경고를 라우팅하는 데 비슷한 지침이 적용됩니다.

1. Azure Portal에서 **Backup 센터** 로 이동합니다. **경고** 메뉴 항목을 클릭하고 **작업 관리** 를 선택합니다.

    ![Backup 센터의 작업 관리 스크린샷](media/backup-azure-monitoring-laworkspace/backup-center-manage-actions.png) 

2. **작업 규칙(미리 보기)** 탭으로 이동하고 **새 작업 규칙** 을 클릭합니다.

    ![새 작업 규칙을 만들기 위한 스크린샷](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-rule.png) 

3. 작업 규칙을 적용해야 하는 범위를 선택합니다. 구독에 포함된 모든 리소스의 작업 규칙을 적용할 수 있습니다. 필요에 따라 경고에 필터를 적용하여 특정 심각도의 경고에 대한 알림만 생성할 수도 있습니다.

    ![작업 규칙 범위를 설정하는 스크린샷](media/backup-azure-monitoring-laworkspace/azure-monitor-action-rule-scope.png) 

4. 작업 그룹을 만듭니다. 작업 그룹은 경고에 대한 알림(예: 메일 주소)이 전송되어야 하는 대상입니다.
 
    ![새 작업 그룹을 만들기 위한 스크린샷](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-group.png) 

5. **기본** 탭에서 작업 그룹의 이름과 해당 그룹이 생성되어야 하는 구독 및 리소스 그룹을 선택합니다.

    ![작업 그룹의 기본 속성을 보여 주는 스크린샷](media/backup-azure-monitoring-laworkspace/azure-monitor-action-groups-basic.png) 

6. **알림** 탭에서 **메일/SMS 메시지/푸시/음성** 을 선택하고 수신자 메일 ID를 입력합니다.

    ![알림 속성 설정을 보여 주는 스크린샷](media/backup-azure-monitoring-laworkspace/azure-monitor-email.png) 

7. **검토+만들기** 를 클릭한 다음 **만들기** 를 클릭하여 작업 그룹을 배포합니다.

8. 마지막으로 작업 규칙을 저장합니다.

[Azure Monitor에서 작업 규칙에 대한 자세한 정보](../azure-monitor/alerts/alerts-action-rules.md)


## <a name="next-steps"></a>다음 단계

[Azure Monitor를 사용하여 Azure Backup 워크로드 모니터링](backup-azure-monitoring-use-azuremonitor.md)
