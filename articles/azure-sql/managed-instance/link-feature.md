---
title: 링크 기능
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance SQL Server 클라우드로 데이터를 지속적으로 복제하거나 최상의 최소 가동 중지 시간으로 SQL Server 데이터베이스를 마이그레이션하는 링크 기능에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, danil
ms.date: 11/05/2021
ms.openlocfilehash: 19851f1ff3bef1ab57d23013ada78bec5665b7c5
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989810"
---
# <a name="link-feature-for-azure-sql-managed-instance-limited-preview"></a>Azure SQL Managed Instance 링크 기능(제한된 미리 보기)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance 새로운 링크 기능은 어디서나 호스트되는 SQL 서버를 SQL Managed Instance 연결하여 하이브리드 유연성과 데이터베이스 이동성을 제공합니다. 클라우드에 대한 거의 실시간 데이터 복제를 사용하는 방법을 사용하면 Azure에서 읽기 전용 보조로 워크로드를 오프로드하여 Azure 전용 기능, 성능 및 규모를 활용할 수 있습니다. 

이벤트 후 Azure의 SQL Managed Instance 읽기 전용 워크로드를 계속 실행할 수 있습니다. 또한 현재 Azure의 다른 솔루션에 비해 가능한 한 최소한의 가동 중지 시간으로 SQL Server 하나 이상의 애플리케이션을 동시에 SQL Managed Instance 마이그레이션하도록 선택할 수도 있습니다.

> [!NOTE]
> 링크 기능은 현재 SQL Server 2019 Enterprise Edition CU13 이상에만 지원되는 제한된 공개 미리 보기로 릴리스됩니다. [이제 등록하여](https://aka.ms/mi-link-signup) 제한된 공개 미리 보기에 참여합니다. 

## <a name="overview"></a>개요

SQL Server 및 SQL Managed Instance 간의 근 실시간 데이터 복제의 기본 기술은 잘 알려져 있고 검증된 Always On 가용성 그룹 기술 스택의 일부인 분산 가용성 그룹을 기반으로 합니다. SQL Server 온-프레미스 가용성 그룹을 확장하여 안전하고 안전한 방식으로 Azure에서 SQL Managed Instance. 

기존 가용성 그룹 또는 여러 노드가 필요하지 않습니다. 이 링크는 기존 가용성 그룹이 없는 단일 노드 SQL Server 인스턴스와 기존 가용성 그룹이 있는 다중 노드 SQL Server 인스턴스를 지원합니다. 링크를 통해 전체 SQL Server 데이터 자산이 클라우드로 마이그레이션하지 않고도 Azure의 최신 이점을 활용할 수 있습니다.

링크가 필요한 한 월, 심지어 몇 년 동안 링크를 계속 실행할 수 있습니다. 또한 현대화 여정의 경우 Azure로 마이그레이션할 준비가 되면 링크를 통해 현재 사용할 수 있는 다른 모든 옵션에 비해 가동 중지 시간을 최소화하면서 마이그레이션 환경을 크게 향상하여 SQL Managed Instance 진정한 온라인 마이그레이션을 제공합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

SQL Server Azure SQL Managed Instance 링크 기능을 통해 복제된 데이터는 다음과 같은 다양한 시나리오에서 사용할 수 있습니다. 

- **클라우드로 마이그레이션하지 않고 Azure 서비스 사용** 
- **읽기 전용 워크로드를 Azure로 오프로드** 
- **Azure로 마이그레이션**

![Managed Instance 링크 기본 시나리오](./media/managed-instance-link/mi-link-main-scenario.png)


### <a name="use-azure-services"></a>Azure 서비스 사용 

링크 기능을 사용하여 클라우드로 마이그레이션하지 않고 SQL Server 데이터를 사용하는 Azure 서비스를 활용합니다. 예를 들어 보고, 분석, 백업, 기계 학습 및 Azure로 데이터를 보내는 기타 작업이 있습니다. 

### <a name="offload-workloads-to-azure"></a>워크로드를 Azure로 오프로드 

링크 기능을 사용하여 워크로드를 Azure로 오프로드할 수도 있습니다. 예를 들어 애플리케이션은 읽기/쓰기 워크로드에 SQL Server 사용하는 동시에 읽기 전용 워크로드를 오프로드하여 전 세계 Azure의 60개 이상 지역에서 SQL Managed Instance 수 있습니다. 링크가 설정되면 SQL Server 주 데이터베이스는 읽기/쓰기에 액세스할 수 있고, Azure에서 SQL Managed Instance 복제된 데이터는 읽기 전용으로 액세스할 수 있습니다. 이렇게 하면 읽기 확장 및 읽기 전용 워크로드를 Azure로 오프로드하는 데 SQL Managed Instance 복제된 데이터베이스를 사용할 수 있는 다양한 시나리오가 가능합니다. SQL Managed Instance 독립적으로 읽기/쓰기 데이터베이스를 호스트할 수도 있습니다. 이렇게 하면 복제된 데이터베이스를 동일한 관리형 인스턴스의 다른 읽기/쓰기 데이터베이스로 복사하여 추가 데이터 처리를 할 수 있습니다.

이 링크는 데이터베이스 범위(데이터베이스당 하나의 링크)로, Azure에서 워크로드의 통합 및 분리를 허용합니다. 예를 들어 여러 SQL 서버에서 Azure의 단일 SQL Managed Instance 데이터베이스를 복제하거나(통합) 데이터베이스와 관리되는 인스턴스 간의 1~1 관계를 통해 단일 SQL Server 여러 관리형 인스턴스로 데이터베이스를 복제할 수 있습니다. 후자는 읽기 전용 복제본으로 사용할 수 있는 전 세계 모든 지역의 고객에게 워크로드를 신속하게 더 가깝게 만들 수 있는 효율적인 방법을 제공합니다.

### <a name="migrate-to-azure"></a>Azure로 마이그레이션 

링크 기능을 사용하면 SQL Server SQL Managed Instance 쉽게 마이그레이션할 수 있으므로 다음을 사용할 수 있습니다. 

- 현재 사용 가능한 다른 모든 솔루션에 비해 최소 가동 중지 시간 마이그레이션이 가장 큽니다.
- 모든 서비스 계층에서 SQL Managed Instance 진정한 온라인 마이그레이션 

링크 기능을 사용하면 가동 중지 시간을 최소화할 수 있기 때문에 기본 워크로드를 온라인으로 유지하면서 관리되는 인스턴스로 마이그레이션할 수 있습니다. 범용 서비스 계층으로 마이그레이션할 때 이전에 다른 솔루션으로 온라인 마이그레이션을 구현할 수 있었지만, 이제 링크 기능을 통해 중요 비즈니스용 서비스 계층으로의 진정한 온라인 마이그레이션도 가능합니다. 

## <a name="how-it-works"></a>작동 방식

SQL Managed Instance 링크 기능의 기본 기술은 분산 가용성 그룹입니다. 솔루션은 기존 가용성 그룹이 없는 단일 노드 시스템 또는 기존 가용성 그룹이 있는 여러 노드 시스템을 지원합니다.  

![SQL Managed Instance 링크 기능은 어떻게 작동하나요?](./media/managed-instance-link/mi-link-ag-dag.png)

VPN 또는 Express Route와 같은 보안 연결은 온-프레미스 네트워크와 Azure 간에 사용됩니다. SQL Server Azure VM에서 호스트되는 경우 내부 Azure 백본을 VM과 관리형 인스턴스 간에 사용할 수 있습니다(예: 전역 VNet 피어링). 두 시스템 간의 트러스트는 인증서 기반 인증을 사용하여 설정되며, SQL Server 및 SQL Managed Instance 공개 키를 교환합니다.

동일하거나 다양한 SQL Server 원본에서 단일 SQL Managed Instance 최대 100개의 링크가 있을 수 있습니다. 이 제한은 현재 관리되는 인스턴스에서 호스트될 수 있는 데이터베이스 수에 의해 제어됩니다. 마찬가지로 단일 SQL Server 데이터베이스와 관리되는 인스턴스 간의 1 대 1 관계에서 서로 다른 Azure 지역의 여러 관리형 인스턴스와 여러 병렬 데이터베이스 복제 링크를 설정할 수 있습니다. 이 기능을 사용하려면 SQL SERVER 2019에 CU13 이상 설치가 필요합니다.

## <a name="sign-up-for-link"></a>링크 등록

링크 기능을 사용하려면 다음이 필요합니다.

- SQL Server 2019 Enterprise Edition 온-프레미스 또는 Azure VM에 [CU13 이상이](https://support.microsoft.com/topic/kb5005679-cumulative-update-13-for-sql-server-2019-5c1be850-460a-4be4-a569-fe11f0adc535) 설치되어 있습니다.
- SQL Server 관리되는 인스턴스 간의 네트워크 연결이 필요합니다. SQL Server 온-프레미스에서 실행되는 경우 VPN 링크 또는 Express 경로를 사용합니다. SQL Server Azure VM에서 실행 중인 경우 VM을 관리되는 인스턴스와 동일한 서브넷에 배포하거나 글로벌 VNet 피어링을 사용하여 두 개의 별도 서브넷을 연결합니다. 
- Azure SQL Managed Instance 모든 서비스 계층에서 프로비전됩니다.

다음 링크를 사용하여 링크 기능의 제한된 미리 보기에 등록합니다. 

> [!div class="nextstepaction"]
> [링크 기능 미리 보기에 등록](https://aka.ms/mi-link-signup)

## <a name="next-steps"></a>다음 단계

링크 기능에 대한 자세한 내용은 다음을 참조하세요.

- [Managed Instance 링크 – SQL Server Azure에 연결하여 를 다시 애니메이션합니다.](https://aka.ms/mi-link-techblog)

다른 복제 시나리오의 경우 다음을 고려합니다. 

- [Azure SQL Managed Instance(미리 보기)를 사용하여 트랜잭션 복제](replication-transactional-overview.md)
