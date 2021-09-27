---
title: Azure VM의 SAP HANA 데이터 백업 정보
description: 이 문서에서는 Azure 가상 머신에서 실행되는 SAP HANA 데이터베이스를 백업하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 053d57518e91292105475753279e6fefec19e7a4
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081038"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Azure VM의 SAP HANA 데이터 백업 정보

SAP HANA 데이터베이스는 낮은 RPO(복구 지점 목표)와 신속한 RTO(복구 시간 목표)를 필요로 하는 중요 업무용 워크로드입니다. 이제 [Azure Backup](./backup-overview.md)을 사용하여 [Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업](./tutorial-backup-sap-hana-db.md)할 수 있습니다.

Azure Backup은 SAP HANA의 원시 API를 활용하여 원시 백업 지원을 제공하기 위해 SAP의 [Backint 인증](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)을 받았습니다. Azure Backup에서 제공하는 이 제품은 **인프라가 없는** 백업이라는 Azure Backup의 개념에 맞게 조정되므로 백업 인프라를 배포 및 관리할 필요가 없습니다. 이제 Azure VM에서 실행되는 SAP HANA 데이터베이스(이제 [M 시리즈 VM](../virtual-machines/m-series.md)도 지원됨!)를 원활하게 백업 및 복원할 수 있으며 Azure Backup이 제공하는 엔터프라이즈 관리 기능을 활용할 수 있습니다.

## <a name="added-value"></a>추가 혜택

Azure Backup을 사용하여 SAP HANA 데이터베이스를 백업 및 복원하면 다음과 같은 이점이 있습니다.

* **15분 RPO(복구 지점 목표)** : 최대 15분의 중요 데이터를 복구할 수 있습니다.
* **한 번 클릭으로 특정 시점 복원**: 대체 HANA 서버로 프로덕션 데이터를 쉽게 복원할 수 있습니다. 복원을 수행할 백업 및 카탈로그 체인은 모두 Azure가 내부적으로 관리합니다.
* **장기 보존**: 엄격한 규정 준수 및 감사 요구 사항. 기본 제공 수명 주기 관리 기능을 통해 복구 지점이 자동으로 정리되는 보존 기간을 기준으로 몇 년 동안 백업을 유지합니다.
* **Azure에서의 백업 관리**: Azure Backup의 관리 및 모니터링 기능을 사용하여 관리 환경을 개선합니다. Azure CLI도 지원됩니다.

현재 지원되는 백업 및 복원 시나리오를 보려면 [SAP HANA 시나리오 지원 매트릭스](./sap-hana-backup-support-matrix.md#scenario-support)를 참조하세요.

## <a name="backup-architecture"></a>백업 아키텍처

Azure VM 내에서 실행 되는 SAP HANA 데이터베이스를 백업 하 고 백업 데이터를 Azure Recovery Services 자격 증명 모음에 직접 스트리밍할 수 있습니다.

![백업 아키텍처 다이어그램](./media/sap-hana-db-about/backup-architecture.png)

* 백업 프로세스는 먼저 Azure에서 [Recovery Services 자격 증명 모음을 만듭니다](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault). 이 자격 증명 모음은 시간에 따라 생성된 백업 및 복구 지점을 저장하는 데 사용됩니다.
* SAP HANA 서버를 실행하는 Azure VM은 자격 증명 모음에 등록되며 백업할 데이터베이스가 [검색](./tutorial-backup-sap-hana-db.md#discover-the-databases)됩니다. Azure Backup 서비스에서 데이터베이스를 검색할 수 있도록 하려면 HANA 서버에서 루트 사용자로 [preregistration 스크립트](https://go.microsoft.com/fwlink/?linkid=2173610)를 실행해야 합니다.
* 이 스크립트는 **AZUREWLBACKUPHANAUSER** 데이터베이스 사용자를 만들고 사용자가 이미 만든 사용자 지정 백업 사용자를 사용한 다음 **hdbuserstore** 에 동일한 이름을 사용 하 여 해당 키를 만듭니다. 스크립트의 기능에 [대해 자세히 알아보세요](/azure/backup/tutorial-backup-sap-hana-db#what-the-pre-registration-script-does) .
* Azure Backup Service는 이제 등록된 SAP HANA 서버에 **HANA용 Azure Backup 플러그인** 을 설치합니다.
* 작성 전 등록 스크립트/사용자 지정 백업 사용자가 만든 **AZUREWLBACKUPHANAUSER** 데이터베이스 사용자 (등록 전 스크립트에 입력으로 추가 됨)는 **HANA에 대 한 Azure Backup 플러그 인** 에서 모든 백업 및 복원 작업을 수행 하는 데 사용 됩니다. 이 스크립트를 실행 하지 않고 SAP HANA 데이터베이스에 대 한 백업을 구성 하려고 하면 **UserErrorHanaScriptNotRun** 오류가 발생할 수 있습니다.
* 검색된 데이터베이스에 [백업을 구성](./tutorial-backup-sap-hana-db.md#configure-backup)하려면 필요한 백업 정책을 선택하고 백업을 사용하도록 설정합니다.

* 백업이 구성되면 Azure Backup 서비스는 보호된 SAP HANA 서버에서 데이터베이스 수준에서 다음과 같은 Backint 매개 변수를 설정합니다.
  *  [catalog_backup_using_backint:true]
  *  [enable_accumulated_catalog_backup:false]
  *  [parallel_data_backup_backint_channels:1]
  *  [log_backup_timeout_s:900)]
  *  [backint_response_timeout:7200]

>[!NOTE]
>이러한 매개 변수가 호스트 수준에 *없는지* 확인합니다. 호스트 수준 매개 변수는 이러한 매개 변수를 재정의하므로 예기치 않은 동작이 발생할 수 있습니다.
>

* **HANA용 Azure Backup 플러그인** 은 모든 백업 일정 및 정책 세부 정보를 유지 관리합니다. 예약된 백업을 트리거하고 Backint API를 통해 **HANA 백업 엔진** 과 통신합니다.
* **HANA 백업 엔진** 은 백업할 데이터가 포함된 Backint 스트림을 반환합니다.
* 전체 백업 또는 차등 백업인 (Azure Portal에서 트리거된) 모든 예약 백업 및 주문형 백업은 **HANA용 Azure 백업 플러그인** 의해 시작됩니다. 하지만 로그 백업은 **HANA 백업 엔진** 자체에서 관리 및 트리거됩니다.
* SAP HANA용 Azure Backup, BackInt 인증 솔루션은 기본 디스크 또는 VM 형식에 의존하지 않습니다. 백업은 HANA에 의해 생성된 스트림으로 수행됩니다.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Azure SAP HANA 백업으로 Azure VM 백업 사용

데이터베이스 수준 백업 및 복구 기능을 제공하는 Azure에서 SAP HANA 백업을 사용하는 것 외에도 Azure VM 백업 솔루션을 사용하여 OS 및 데이터베이스가 아닌 디스크를 백업할 수 있습니다.

데이터베이스 백업 및 복구에 [Backint 인증 Azure SAP HANA 백업 솔루션](#backup-architecture)을 사용할 수 있습니다.

OS 및 기타 데이터베이스가 아닌 디스크 백업에 [Azure VM 백업](backup-azure-vms-introduction.md)을 사용할 수 있습니다. VM 백업은 매일 한 번씩 수행되며 모든 디스크(**WA(쓰기 가속기) OS 디스크** 및 **Ultra Disk** 제외)를 백업합니다. Azure SAP HANA 백업 솔루션을 사용하여 데이터베이스를 백업하므로 [Azure VM에 대한 선택적 디스크 백업 및 복원](selective-disk-backup-restore.md) 기능을 사용하여 OS 및 데이터베이스가 아닌 디스크에 대해서만 파일에 일관적인 백업을 수행할 수 있습니다.

SAP HANA를 실행하는 VM을 복원하려면 다음 단계를 수행합니다.

* 최신 복구 지점에서 [Azure VM 백업에서 새 VM을 복원](backup-azure-arm-restore-vms.md)합니다. 또는 빈 VM을 새로 만들고 최신 복구 지점에서 디스크를 연결합니다.
* WA 디스크가 제외되면 복원되지 않습니다. 이 경우 빈 WA 디스크와 로그 영역을 만듭니다.
* 다른 모든 구성(예: IP, 시스템 이름 등)이 설정되면 VM이 Azure Backup에서 DB 데이터를 받도록 설정됩니다.
* 이제 [Azure SAP HANA DB 백업](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) 에서 원하는 지정 시간으로 DB를 VM에 복원합니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원](./sap-hana-db-restore.md)하는 방법 알아보기
* [Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법](./sap-hana-db-manage.md)을 알아봅니다.
