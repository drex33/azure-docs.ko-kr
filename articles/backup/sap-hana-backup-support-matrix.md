---
title: SAP HANA Backup 지원 매트릭스
description: 이 문서에서는 Azure Backup을 사용하여 Azure VM에 SAP HANA 데이터베이스를 백업할 때 지원되는 시나리오 및 제한 사항에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 10/22/2021
ms.custom: references_regions
ms.openlocfilehash: b9d642d0c70b06677aedcce99c1f21ac4b3a7d34
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244543"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure VM의 SAP HANA 데이터베이스 백업에 대한 지원 매트릭스

Azure Backup은 Azure에 SAP HANA 데이터베이스 백업을 지원합니다. 이 문서에서는 Azure Backup을 사용하여 Azure VM에서 SAP HANA 데이터베이스를 백업하는 경우 지원되는 시나리오 및 제한 사항을 요약합니다.

> [!NOTE]
> 이제 로그 백업 빈도를 최소 15분으로 설정할 수 있습니다. 로그 백업은 데이터베이스에 대한 전체 백업이 성공적으로 완료된 후에만 시작됩니다.

## <a name="scenario-support"></a>시나리오 지원

| **시나리오**               | **지원되는 구성**                                | **지원되지 않는 구성**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **토폴로지**               | Azure Linux VM에서만 실행되는 SAP HANA                    | HLI(HANA 대규모 인스턴스)                                   |
| **지역**                   | **GA:**<br> **아메리카** – 미국 중부, 미국 동부 2, 미국 동부, 미국 중북부, 미국 중남부, 미국 서부 2, 미국 중서부, 미국 서부, 캐나다 중부, 캐나다 동부, 브라질 남부 <br> **아시아 태평양** – 오스트레일리아 중부, 오스트레일리아 중부 2, 오스트레일리아 동부, 오스트레일리아 남동부, 일본 동부, 일본 서부, 한국 중부, 한국 남부, 동아시아, 동남 아시아, 인도 중부, 인도 남부, 인도 서부, 중국 동부, 중국 북부, 중국 동부 2, 중국 북부 2 <br> **유럽** – 서유럽, 북유럽, 프랑스 중부, 영국 남부, 영국 서부, 독일 북부, 독일 중서부, 스위스 북부, 스위스 서부, 스위스 중북부, 노르웨이 동부, 노르웨이 서부 <br> **아프리카/ME** - 남아프리카 공화국 북부, 남아프리카 공화국 서부, 아랍에미리트 북부, 아랍에미리트 중부  <BR>  **Azure Government 지역** | 프랑스 남부, 독일 중부, 독일 북동부, US Gov 아이오와 |
| **OS 버전**            | SLES 12 SP2, SP3, SP4 및 SP5; SLES 15 with SP0, SP1, SP2 및 SP3 <br><br>  RHEL 7.4, 7.6, 7.7, 7.9, 8.1, 8.2 및 8.4                |                                             |
| **HANA 버전**          | HANA 1.x의 SDC, HANA 2.x SPS04의 MDC, SPS05 Rev <= 55(암호화 사용 시나리오에 대해서도 유효성이 검사됨)      |                                                            |
| **HANA 배포**       | 단일 Azure VM의 SAP HANA - 스케일 업만 해당 <br><br> 고가용성 배포의 경우 서로 다른 두 컴퓨터의 두 노드는 별도의 데이터 체인이 있는 개별 노드로 처리됩니다.               | 스케일 아웃 <br><br> 고가용성 배포에서는 백업이 보조 노드로 자동 장애 조치(failover)되지 않습니다. 백업 구성은 각 노드에 대해 개별적으로 수행해야 합니다.                                           |
| **HANA 인스턴스**         | 단일 Azure VM의 단일 SAP HANA 인스턴스 – 스케일 업만 해당 | 단일 VM의 여러 SAP HANA 인스턴스. 한 번에 이러한 여러 인스턴스 중 하나만 보호할 수 있습니다.                  |
| **HANA 데이터베이스 형식**    | 1\.x의 SDC(Single Database Container), 2.x의 MDC(Multi-Database Container) | HANA 1.x의 MDC                                              |
| **HANA 데이터베이스 크기**     | HANA 데이터베이스 크기 <= 8TB(HANA 시스템의 메모리 크기가 아님)               |                                                              |
| **백업 유형**           | 전체, 차등, 증분 및 로그 백업                          |  스냅샷                                       |
| **복원 유형**          | 지원되는 복원 유형에 대한 자세한 내용은 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)을 참조하세요. |                                                              |
| **백업 제한**          | SAP HANA 인스턴스당 최대 8TB의 전체 백업 크기(소프트 제한)         |                                                              |
| **하루 전체 백업 수**     |   하나는 예약 된 백업입니다.  <br><br>   3 번의 주문형 백업 <br><br> 하루에 3 개 이상의 백업을 트리거하지 않는 것이 좋습니다. 그러나 실패 한 시도의 경우 사용자 재시도를 허용 하기 위해 주문형 백업에 대 한 하드 한도는 9 회 시도로 설정 됩니다.  |
| **특수 구성** |                                                              | SAP HANA + Dynamic Tiering <br>  LaMa를 통한 복제        |

------

>[!NOTE]
>Azure Backup은 Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업할 때 일광 절약 시간제 변경을 자동으로 조정하지 않습니다.
>
>필요에 따라 정책을 수동으로 수정합니다.

> [!NOTE]
> 이제 Azure Portal에서 HANA 네이티브 클라이언트(SAP HANA Studio/ Cockpit/ DBA Cockpit)에서 트리거되는 동일한 컴퓨터에 대한 [백업 및 복원 작업을 모니터링](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업하는 방법](./backup-azure-sap-hana-database.md)을 알아봅니다.
* [Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원하는 방법](./sap-hana-db-restore.md)을 알아봅니다.
* [Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법](sap-hana-db-manage.md)을 알아봅니다.
* [SAP HANA 데이터베이스를 백업할 때 발생하는 일반적인 문제를 해결하는 방법](./backup-azure-sap-hana-database-troubleshoot.md)을 알아봅니다.
