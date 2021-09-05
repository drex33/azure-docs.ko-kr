---
title: 백업 센터 지원 매트릭스
description: 이 문서에서는 백업 센터에서 각 워크로드 유형에 대해 지원하는 시나리오를 요약합니다.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: b922f2e320f9b41186f5a6397af5cd5f4b3f7750
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567672"
---
# <a name="support-matrix-for-backup-center"></a>백업 센터 지원 매트릭스

백업 센터는 기업에 [대규모 백업을 관리, 모니터링, 운영 및 분석](backup-center-overview.md)할 수 있는 단일 창을 제공합니다. 이 문서에서는 백업 센터에서 각 워크로드 유형에 대해 지원하는 시나리오를 요약합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

| **범주** | **시나리오**  | **지원되는 워크로드**  | **제한** |
| -------------| ------------- | ----------------------- |------------|
| 모니터링   | 모든 작업 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | <li> 7일 분량의 작업을 즉시 사용할 수 있습니다. <br> <li> 각 필터/드롭다운에서 최대 1000개 항목을 지원합니다. 따라서 백업 센터를 사용하여 테넌트 전체에서 최대 1000개의 구독 및 1000개의 자격 증명 모음을 모니터링할 수 있습니다. |
| 모니터링 | 모든 백업 인스턴스 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | 위와 동일 |
| 모니터링 | 모든 백업 정책 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | 위와 동일 |
| 모니터링 | 모든 자격 증명 모음 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | 위와 동일 |
| 동작 | 백업 구성 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [Azure VM 백업](./backup-support-matrix-iaas.md) 및 [Azure Database for PostgreSQL 서버 백업](backup-azure-database-postgresql.md#support-matrix)에 대한 지원 매트릭스 참조 |
| 동작 | 백업 인스턴스 복원 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [Azure VM 백업](./backup-support-matrix-iaas.md) 및 [Azure Database for PostgreSQL 서버 백업](backup-azure-database-postgresql.md#support-matrix)에 대한 지원 매트릭스 참조 |
| 동작 | 자격 증명 모음 만들기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [Recovery Services 자격 증명 모음](./backup-support-matrix.md#vault-support)에 대한 지원 매트릭스 참조 |
| 동작 | 백업 정책 만들기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [Recovery Services 자격 증명 모음](./backup-support-matrix.md#vault-support)에 대한 지원 매트릭스 참조 |
| 동작 | 백업 인스턴스에 대해 주문형 백업 실행 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [Azure VM 백업](./backup-support-matrix-iaas.md) 및 [Azure Database for PostgreSQL 서버 백업](backup-azure-database-postgresql.md#support-matrix)에 대한 지원 매트릭스 참조 |
| 동작 | 백업 인스턴스에 대해 백업 중지 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [Azure VM 백업](./backup-support-matrix-iaas.md) 및 [Azure Database for PostgreSQL 서버 백업](backup-azure-database-postgresql.md#support-matrix)에 대한 지원 매트릭스 참조 |
| 자세한 정보 | Backup 보고서 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Virtual Machine의 SQL <br><br> <li> Azure Virtual Machine의 SAP HANA <br><br> <li> Azure 파일 <br><br> <li> System Center Data Protection Manager <br><br> <li> Azure Backup 에이전트(MARS) <br><br> <li> Azure Backup 서버(MABS) | [Backup 보고서에 대해 지원되는 시나리오](./configure-reports.md#supported-scenarios) 참조 |
| 거버넌스 | '백업' 범주에서 기본 제공 및 사용자 지정 Azure 정책 조회 및 할당 | 해당 없음 | 해당 없음 |
| 거버넌스 | 백업에 대해 구성되지 않은 데이터 원본 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 | 해당 없음 |
| 모니터링 | 대규모 Azure Monitor 경고 보기 | <li> Azure Virtual Machine <br><br> <li> Azure Database for PostgreSQL 서버 <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA <br><br> <li> Azure 파일<br/><br/> <li>Azure Blob<br/><br/> <li>Azure Managed Disks | [경고](./backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview) 설명서 참조 |
| 동작 | 백업 센터에서 지역 간 복원 작업 실행 | <li> Azure Virtual Machine <br><br> <li> Azure VM의 SQL <br><br> <li> Azure VM의 SAP HANA | [지역 간 복원](./backup-create-rs-vault.md#set-cross-region-restore) 설명서 참조 |

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

| **범주** | **시나리오**  |
|--------------|---------------|
| 동작 | 대규모 자격 증명 모음 설정 구성은 현재 백업 센터에서 지원되지 않습니다. |
| 가용성 | 백업 센터는 현재 국가별 클라우드에서 사용할 수 없습니다. | 

## <a name="next-steps"></a>다음 단계

* [Azure Backup에 대한 지원 매트릭스 검토](./backup-support-matrix.md)
* [Azure VM 백업에 대한 지원 매트릭스 검토](./backup-support-matrix-iaas.md)
* [Azure Database for PostgreSQL 서버 백업에 대한 지원 매트릭스 검토](backup-azure-database-postgresql.md#support-matrix)