---
title: 링크 기능
titleSuffix: Azure SQL Managed Instance
description: Azure Managed Instance SQL에 대 한 링크 기능을 사용 하 여 SQL Server에서 클라우드로 데이터를 지속적으로 복제 하거나, SQL Server 데이터베이스를 마이그레이션할 때 최소 가동 중지 시간을 최대한 활용 하는 방법을 알아보세요.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, danil
ms.date: 11/02/2021
ms.openlocfilehash: fcae82f988ed019717f6693d0d3a63a025113fb4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053610"
---
# <a name="link-feature-for-azure-sql-managed-instance-limited-preview"></a>Azure SQL Managed Instance (제한 된 미리 보기)에 대 한 링크 기능
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance의 새로운 링크 기능은 어디서 든 호스트 되는 SQL 서버를 SQL Managed Instance 연결 하 여 하이브리드 유연성과 데이터베이스 이동성을 제공 합니다. 클라우드로 거의 실시간으로 데이터를 복제 하는 방법을 사용 하 여 azure 전용 기능, 성능 및 규모를 활용 하기 위해 Azure에서 읽기 전용 보조 데이터베이스로 워크 로드를 오프 로드할 수 있습니다. 

치명적인 이벤트 후에 Azure의 SQL Managed Instance에서 읽기 전용 작업을 계속 실행할 수 있습니다. 또한 한 번에 하나 이상의 응용 프로그램을 SQL Server에서 SQL Managed Instance으로 동시에 마이그레이션하는 것을 선택할 수 있으며, 현재 Azure의 다른 솔루션과 비교 하 여 최소 가동 중지 시간을 최대한 향상 시킬 수 있습니다.

> [!NOTE]
> 링크 기능은 현재 SQL Server 2019 Enterprise Edition CU13 이상에 대 한 지원을 포함 하는 제한 된 공개 미리 보기로 릴리스됩니다. 제한 된 공개 미리 보기에 참여 하려면 [지금 등록 하세요](https://aka.ms/mi-link-signup) . 

## <a name="overview"></a>개요

SQL Server와 SQL Managed Instance 간에 거의 실시간으로 데이터를 복제 하는 기본적인 기술은 분산 가용성 그룹을 기반으로 하며, 잘 알려져 있고 검증 된 Always On 가용성 그룹 기술 스택의 일부입니다. SQL Server 온-프레미스 가용성 그룹을 안전 하 고 안전한 방식으로 Azure에서 SQL Managed Instance으로 확장 합니다. 

기존 가용성 그룹이 나 여러 노드가 필요 하지 않습니다. 이 링크는 기존 가용성 그룹을 포함 하지 않는 단일 노드 SQL Server 인스턴스 및 기존 가용성 그룹을 포함 하는 다중 노드 SQL Server 인스턴스를 지원 합니다. 이 링크를 통해 전체 SQL Server 데이터 공간을 클라우드로 마이그레이션하지 않고도 Azure의 최신 혜택을 활용할 수 있습니다.

필요할 때까지 (한 번에 한 달 및 몇 년 동안) 링크를 계속 실행할 수 있습니다. 현대화 경험을 위해 Azure로 마이그레이션할 준비가 되 면이 링크를 통해 현재 사용 가능한 모든 옵션과 비교 하 여 최소 가동 중지 시간으로 크게 향상 된 마이그레이션 환경을 사용 하 여 Managed Instance SQL에 대 한 진정한 온라인 마이그레이션을 제공할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

SQL Server에서 Azure SQL Managed Instance 연결 기능을 통해 복제 된 데이터는 다음과 같은 다양 한 시나리오에서 사용할 수 있습니다. 

- **클라우드로 마이그레이션하지 않고 Azure 서비스 사용** 
- **읽기 전용 작업을 Azure로 오프 로드** 
- **Azure로 마이그레이션**

![Managed Instance 링크 주요 시나리오](./media/managed-instance-link/mi-link-main-scenario.png)


### <a name="use-azure-services"></a>Azure 서비스 사용 

연결 기능을 사용 하 여 클라우드로 마이그레이션하지 않고 SQL Server 데이터를 사용 하 여 Azure 서비스를 활용할 수 있습니다. 예를 들면 보고, 분석, 백업, 기계 학습 및 Azure로 데이터를 전송 하는 기타 작업이 포함 됩니다. 

### <a name="offload-workloads-to-azure"></a>Azure로 워크 로드 오프 로드 

또한 링크 기능을 사용 하 여 Azure로 워크 로드를 오프 로드할 수 있습니다. 예를 들어 응용 프로그램은 읽기/쓰기 작업에 SQL Server를 사용할 수 있으며, 전 세계에 있는 Azure의 60 개 이상의 지역에서 읽기 전용 작업을 SQL Managed Instance으로 오프 로드 합니다. 링크가 설정 되 면 SQL Server의 주 데이터베이스는 읽기/쓰기에 액세스할 수 있지만, Azure에서 SQL Managed Instance에 복제 된 데이터는 읽기 전용으로 액세스할 수 있습니다. 이를 통해 SQL Managed Instance에서 복제 된 데이터베이스를 읽기 확장 및 읽기 전용 작업을 Azure로 오프 로드 하는 데 사용할 수 있는 다양 한 시나리오를 사용할 수 있습니다. SQL 동시에 Managed Instance는 독립적인 읽기/쓰기 데이터베이스를 호스팅할 수도 있습니다. 이렇게 하면 추가 데이터 처리를 위해 복제 된 데이터베이스를 동일한 관리 되는 인스턴스의 다른 읽기/쓰기 데이터베이스로 복사할 수 있습니다.

이 링크는 Azure에서 워크 로드를 통합 하 고 deconsolidation 수 있도록 하는 데이터베이스 범위 (한 데이터베이스 당 하나의 링크)입니다. 예를 들어 여러 SQL 서버에서 azure의 단일 SQL Managed Instance에 데이터베이스를 복제 (통합) 하거나, 데이터베이스와 관리 되는 인스턴스 간의 1 ~ 1 관계를 통해 단일 SQL Server에서 여러 관리 되는 인스턴스로 데이터베이스를 복제 하 여 전 세계의 azure 지역 (deconsolidation)으로 복제할 수 있습니다. 후자는 읽기 전용 복제본으로 사용할 수 있는 전 세계 지역의 모든 지역에서 워크 로드를 고객에 게 더 가깝게 빠르게 가져올 수 있는 효율적인 방법을 제공 합니다.

### <a name="migrate-to-azure"></a>Azure로 마이그레이션 

또한 링크 기능을 사용 하면 SQL Server에서 SQL Managed Instance로 쉽게 마이그레이션할 수 있습니다. 

- 현재 사용할 수 있는 다른 모든 솔루션과 비교 하 여 가장 성능이 뛰어난 최소 가동 중지 시간 마이그레이션
- 모든 서비스 계층에서 SQL Managed Instance로의 온라인 마이그레이션 

링크 기능을 사용 하 여 최소 가동 중지 시간 마이그레이션을 가능 하 게 하므로 기본 작업을 온라인 상태로 유지 하면서 관리 되는 인스턴스로 마이그레이션할 수 있습니다. 일반 용도의 서비스 계층으로 마이그레이션할 때 온라인 마이그레이션을 사용 하 여 이전에 다른 솔루션을 사용할 수 있지만, 이제는 링크 기능을 통해 업무상 중요 한 서비스 계층에 대 한 진정한 온라인 마이그레이션도 가능 합니다. 

## <a name="how-it-works"></a>작동 방식

SQL Managed Instance에 대 한 링크 기능의 기본적인 기술은 분산 가용성 그룹입니다. 이 솔루션은 기존 가용성 그룹 없이 단일 노드 시스템을 지원 하거나 기존 가용성 그룹을 포함 하는 여러 노드 시스템을 지원 합니다.  

![SQL Managed Instance에 대 한 링크 기능이 작동 하는 방식](./media/managed-instance-link/mi-link-ag-dag.png)

VPN 또는 Express 경로와 같은 보안 연결이 온-프레미스 네트워크와 Azure 간에 사용 됩니다. SQL Server azure vm에서 호스트 되는 경우 vm 및 관리 되는 인스턴스 (예: 글로벌 VNet 피어 링) 간에 내부 Azure 백본을 사용할 수 있습니다. 두 시스템 간의 트러스트는 인증서 기반 인증을 사용 하 여 설정 됩니다. 인증서 기반 인증을 사용 하 여 SQL Server 및 SQL에서 공개 키를 교환할 Managed Instance 있습니다.

동일 하거나 다양 한 SQL Server 원본에서 단일 SQL Managed Instance에 대 한 최대 100 개의 링크가 있을 수 있습니다. 이 제한은 지금은 관리 되는 인스턴스에서 호스팅될 수 있는 데이터베이스 수에 따라 결정 됩니다. 마찬가지로, 단일 SQL Server는 데이터베이스와 관리 되는 인스턴스 간의 일대일 관계에서 여러 Azure 지역에 있는 여러 관리 되는 인스턴스를 사용 하 여 여러 병렬 데이터베이스 복제 링크를 설정할 수 있습니다. 이 기능을 사용 하려면 SQL Server 2019에 CU13 이상이 설치 되어 있어야 합니다.

## <a name="sign-up-for-link"></a>링크 등록

링크 기능을 사용 하려면 다음이 필요 합니다.

- SQL Server 2019은 온-프레미스 또는 Azure VM에 설치 된 [CU13](https://support.microsoft.com/topic/kb5005679-cumulative-update-13-for-sql-server-2019-5c1be850-460a-4be4-a569-fe11f0adc535) 이상에서 Enterprise Edition 합니다.
- SQL Server와 관리 되는 인스턴스 간의 네트워크 연결이 필요 합니다. SQL Server 온-프레미스에서 실행 중인 경우 VPN 링크나 Express 경로를 사용 합니다. SQL Server Azure vm에서 실행 중인 경우 관리 되는 인스턴스와 동일한 서브넷에 VM을 배포 하거나 글로벌 VNet 피어 링을 사용 하 여 두 개의 별도 서브넷을 연결 합니다. 
- Azure SQL는 모든 서비스 계층에서 프로 비전 Managed Instance.

다음 링크를 사용 하 여 제한 된 링크 기능의 미리 보기에 등록 합니다. 고객이 현재 사용할 수 있는 제한 된 수 만큼의 노력을 등록 하 고 있습니다. 사용 가능한 첫 번째 기회에서 온-등록 됩니다. 11/02/2021에서 시작 하 여 배포 된 관리 되는 인스턴스는 우선 순위가 지정 되 고이 날짜 이전에 배포 된 관리 되는 인스턴스는 지원 되지만 등록에는 시간이 오래 걸릴 수 있습니다. 

> [!div class="nextstepaction"]
> [링크 기능 미리 보기 등록](https://aka.ms/mi-link-signup)

## <a name="next-steps"></a>다음 단계

링크 기능에 대 한 자세한 내용은 다음을 참조 하세요.

- [SQL Managed Instance의 링크 기능-SQL Server와 Azure 간의 데이터 이동성을 다시 상상](https://aka.ms/mi-link-techblog)합니다.

다른 복제 시나리오의 경우 다음을 고려 하십시오. 

- [Azure SQL Managed Instance(미리 보기)를 사용하여 트랜잭션 복제](replication-transactional-overview.md)
