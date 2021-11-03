---
title: Backup 센터를 사용한 백업 모니터링 및 운영
description: 이 문서에서는 Backup 센터를 사용한 대규모 백업을 모니터링하고 운영하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 45e190fdd495cd5ea074386f601eb6296a90a14a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131035930"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Backup 센터를 사용한 백업 모니터링 및 운영

백업 관리자로서 사용자는 단일 유리 창으로 Backup 센터를 사용하여 작업 및 백업 인벤토리를 매일 모니터링할 수 있습니다. 또한 Backup 센터를 사용하여 주문형 백업 요청에 대한 응답, 백업 복원, 백업 정책 만들기 등의 일반 작업을 수행할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* Backup 센터는 현재 Azure VM 백업, Azure VM 백업의 SQL, Azure VM 백업의 SAP HANA, Azure Files 백업, Azure Blob 백업, Azure Managed Disks 백업 및 Azure Database for PostgreSQL Server 백업에서 지원됩니다.
* 지원되는 시나리오와 지원되지 않는 시나리오 상세 목록은 [지원 매트릭스](backup-center-support-matrix.md)를 참조하세요.

## <a name="backup-instances"></a>Backup 인스턴스

Backup 센터는 백업 공간에서 백업 인스턴스에 대해 검색 및 검색 기능을 쉽게 할 수 있도록 합니다.

Backup 센터에서 **백업 인스턴스** 탭을 선택하면, 액세스 권한이 있는 모든 백업 인스턴스의 세부 정보를 볼 수 있습니다.

 각 백업 인스턴스에 대해 다음과 같은 정보를 볼 수 있습니다.

* 데이터 원본 구독
* 데이터 원본 리소스 그룹
* 최근 복구 지점
* 백업 인스턴스와 연결된 자격 증명 모음
* 보호 상태

 다음 매개 변수를 기준으로 백업 인스턴스 목록을 필터링할 수도 있습니다.

* 데이터 원본 구독
* 데이터 원본 리소스 그룹
* 데이터 원본 위치
* 데이터 원본 유형
* Vault
* 보호 상태
* 데이터 원본 태그

표에서 항목 하나를 마우스 오른쪽 단추로 클릭하면 리소스 탐색, 주문형 백업 및 복원 트리거 또는 백업 중지와 같이 지정된 백업 인스턴스에서 작업을 수행할 수 있습니다.

![Backup 센터 - 인스턴스](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Backup 작업

Backup 센터를 통해 백업 공간에 생성된 모든 작업에 대한 자세한 정보를 보고, 실패 작업에 대해 적절한 조치를 취할 수 있습니다.

Backup 센터에서 **Backup 작업** 메뉴 항목을 선택하면 모든 작업을 볼 수 있습니다. 각 작업에는 다음 정보가 들어 있습니다.

* 작업과 연결된 백업 인스턴스
* 데이터 원본 구독
* 데이터 원본 리소스 그룹
* 데이터 원본 위치
* 작업
* 작업 상태
* 작업 시작 시간
* 작업 기간

표에서 항목을 선택하면 지정된 작업에 대한 자세한 정보를 볼 수 있습니다. 하나의 항목을 마우스 오른쪽 단추로 클릭하면 리소스를 탐색하여 필요한 작업을 수행할 수 있습니다.

![Backup 센터 - 작업](./media/backup-center-monitor-operate/backup-center-jobs.png)

**Backup 작업** 탭을 사용하여 지난 7일 동안의 작업을 볼 수 있습니다. 이전 작업을 보려면 [Backup Reports](backup-center-obtain-insights.md)를 사용합니다.

## <a name="metrics"></a>메트릭

Azure Backup 백업 상태를 모니터링할 수 있는 Azure Monitor 통해 기본 제공 메트릭 집합을 제공합니다. 메트릭을 보려면 백업 **센터로** 이동하고 메뉴에서 **메트릭을** 클릭합니다.

:::image type="content" source="./media/metrics-overview/metrics-chart-inline.png" alt-text="백업 상태를 모니터링하는 기본 제공 메트릭을 보여주는 스크린샷." lightbox="./media/metrics-overview/metrics-chart-expanded.png":::

Azure Backup 다음과 같은 주요 기능을 제공합니다.

* 관련 추세와 함께 백업 항목의 백업 및 복원 상태와 관련된 첫 번째 메트릭을 볼 수 있습니다.
* 이러한 메트릭에 대한 사용자 지정 경고 규칙을 작성하여 백업 항목의 상태를 효율적으로 모니터링할 수 있습니다.
* 이메일, ITSM, webhook, 논리 앱 등과 같이 Azure Monitor 지원하는 다양한 알림 채널로 발생된 메트릭 경고를 라우팅할 수 있습니다.

[Azure Backup 메트릭에 대해 자세히 알아봅니다.](metrics-overview.md)

## <a name="alerts"></a>경고

경고를 보려면 **Backup 센터로** 이동하여 메뉴에서 **경고를** 클릭합니다.

**개요** 대시보드에서 지난 24시간 동안의 미해결 경고 요약도 볼 수 있습니다.

:::image type="content" source="./media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alert-inline.png" alt-text="Backup 센터의 경고를 보여주는 스크린샷." lightbox="./media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alert-expanded.png":::

현재 백업 센터에는 다음과 같은 유형의 경고가 표시됩니다.

* **Azure Backup(미리 보기)에 대한 기본 Azure Monitor 경고:** 여기에는 기본 제공 보안 경고 및 Azure Backup Azure Monitor 통해 제공하는 구성된 경고가 포함됩니다. [이 솔루션에서 지원하는 경고 시나리오에 대해 자세히 알아봅니다.](backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview)
* **Azure Backup 대한 메트릭 경고(미리 보기)**: 만든 메트릭 경고 규칙에 따라 발생하는 경고가 포함됩니다. [Azure Backup 메트릭 경고에 대해 자세히 알아보기](metrics-overview.md)

>[!NOTE]
>- 현재 Backup 센터는 Azure 기반 워크로드에 대한 경고만 표시합니다. 온-프레미스 리소스에 대한 경고를 보려면 Recovery Services 자격 증명 모음으로 이동하여 메뉴에서 **경고를** 클릭합니다.
>- 백업 센터에는 Azure Monitor 경고만 표시됩니다. 이전 경고 솔루션에서 발생된 경고(Recovery [Services](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) 자격 증명 모음의 백업 경고 탭에서 액세스)는 Backup 센터에 표시되지 않습니다.

Azure Monitor 경고에 대한 자세한 내용은 [Azure의 경고 개요를 참조하세요.](../azure-monitor/alerts/alerts-overview.md)

### <a name="datasource-and-global-alerts"></a>데이터 원본 및 글로벌 경고

다음과 같은 경고 클래스가 표시됩니다.

* **데이터 원본 경고:** 백업 중인 특정 데이터 원본에 연결된 경고(예: VM에 대한 백업 또는 복원 실패, 데이터베이스에 대한 백업 데이터 삭제 등)는 **데이터 원본 경고** 섹션 아래에 표시됩니다. 메트릭 경고의 경우, 발생된 경고에 데이터 원본 ID 차원이 연결된 경우 데이터 원본 경고 아래에 발생된 **경고가 표시됩니다.**
* **글로벌 경고:** 특정 데이터 원본에 연결되지 않은 경고(예: 자격 증명 모음에 대한 일시 삭제 기능 사용 안 함)는 **글로벌 경고** 섹션 아래에 표시됩니다. 메트릭 경고의 경우, 발생된 경고에 연결된 데이터 원본 ID가 없는 경우 글로벌 경고 아래에 발생된 **경고가** 표시됩니다.

## <a name="vaults"></a>자격 증명 모음

Backup 센터에서 **자격 증명 모음** 메뉴 항목을 선택하면, 액세스 권한이 있는 모든 [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md) 및 [백업 자격 증명 모음](backup-vault-overview.md) 목록을 볼 수 있습니다. 다음 매개 변수를 사용하여 목록을 필터링할 수 있습니다.

* 자격 증명 모음 구독
* 자격 증명 모음 리소스 그룹
* 자격 증명 모음 이름
* 정책과 연결된 데이터 원본 유형

목록에서 항목을 선택하면 지정된 자격 증명 모음으로 이동할 수 있습니다.

![Backup 센터 - 자격 증명 모음](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Backup 정책

Backup 센터에서 Backup 정책에 대한 주요 정보를 보고 편집할 수 있습니다.

**Backup 정책** 메뉴 항목을 선택하면, 백업 공간에서 만든 모든 정책을 볼 수 있습니다. 자격 증명 모음 구독, 리소스 그룹, 데이터 원본 유형 및 자격 증명 모음을 기준으로 목록을 필터링할 수 있습니다. 표에서 항목을 마우스 오른쪽 단추로 클릭하면 해당 정책에 대한 연결된 항목을 보거나, 정책을 편집하거나, 필요한 경우 삭제할 수 있습니다.

![Backup 센터 - 정책](./media/backup-center-monitor-operate/backup-center-policies.png)


## <a name="resource-centric-views"></a>리소스 중심 보기

조직에서 여러 리소스를 공통 자격 증명 모음에 백업하고 각 리소스 소유자가 소유한 리소스의 백업 정보만 보려는 경우 Backup 센터에서 리소스 중심 보기를 사용할 수 있습니다. 리소스 중심 보기를 사용하려면 '액세스할 수 있는 데이터 원본에 대한 정보만 표시' 확인란을 선택합니다. 이 옵션은 현재 **개요,** **백업 인스턴스,** **작업**, 경고 탭에서 **지원됩니다.** 지원되는 워크로드는 Azure VM, Azure VM SQL, Azure VM, Azure Blob, Azure Disks의 SAP HANA.

> [!NOTE]
> 사용자는 리소스 중심 보기를 사용하더라도 자격 증명 모음에 필요한 RBAC 권한이 있어야 합니다. 이 보기의 목적은 개별 사용자가 소유하지 않은 리소스(예: VM)에 대한 정보를 보지 않도록 하는 것입니다.

## <a name="next-steps"></a>다음 단계

* [백업 공간 제어](backup-center-govern-environment.md)
* [Backup 센터를 사용하여 작업 수행](backup-center-actions.md)
* [백업에 대한 인사이트 얻기](backup-center-obtain-insights.md)
