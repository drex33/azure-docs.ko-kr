---
title: Azure Site Recovery를 사용하여 SQL Server의 재해 복구 설정
description: 이 문서에서는 SQL Server 및 Azure Site Recovery를 사용하여 SQL Server에 대한 재해 복구를 설정하는 방법을 설명합니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 834caf50ca65fdbb4701ed3681546513785686c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529326"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>SQL Server에 대한 재해 복구 설정

이 문서에서는 애플리케이션의 SQL Server 백 엔드를 보호하는 방법을 설명합니다. SQL Server BCDR(비즈니스 연속성 및 재해 복구) 기술과 [Azure Site Recovery](site-recovery-overview.md)의 조합을 사용하여 수행합니다.

시작하기 전에 SQL Server 재해 복구 기능을 이해하고 있어야 합니다. 이러한 기능은 다음과 같습니다.

* 장애 조치(failover) 클러스터링
* Always On 가용성 그룹
* 데이터베이스 미러링
* 로그 전달
* 활성 지리적 복제
* 자동 장애 조치 그룹

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Site Recovery와 BCDR 기술 결합

SQL Server 인스턴스를 복구하기 위한 BCDR 기술은 다음 표에 설명된 대로 RTO(복구 시간 목표) 및 RPO(복구 지점 목표) 요구 사항을 기반으로 선택합니다. Site Recovery를 선택한 기술의 장애 조치 작업과 결합하여 전체 애플리케이션의 복구를 오케스트레이션합니다.

배포 유형 | BCDR 기술 | SQL Server의 예상 RTO | SQL Server의 예상 RPO |
--- | --- | --- | ---
Azure IaaS(Infrastructure as a Service) VM(가상 머신) 또는 온-프레미스의 SQL Server.| [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) | 보조 복제본을 주 복제본으로 만드는 데 걸린 시간입니다. | 보조 복제본은 비동기식으로 복제되므로 데이터 손실이 발생합니다.
Azure IaaS VM 또는 온-프레미스의 SQL Server입니다.| [장애 조치(failover) 클러스터링(Always On FCI)](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 노드 간 장애 조치에 걸린 시간입니다. | Always On FCI는 공유 스토리지를 사용하므로 장애 조치 시 스토리지 인스턴스의 같은 보기를 사용할 수 있습니다.
Azure IaaS VM 또는 온-프레미스의 SQL Server입니다.| [데이터베이스 미러링(성능 우선 모드)](/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 미러 서버를 웜 대기 서버로 사용하는 서비스를 강제 적용하는 데 걸린 시간입니다. | 복제는 비동기적이며, 미러 데이터베이스에 주 데이터베이스보다 약간 뒤처질 수 있습니다. 지연 시간은 일반적으로 짧습니다. 하지만 주 서버 또는 미러 서버의 시스템 부하가 많은 경우 길어질 수 있습니다.<br/><br/>로그 전달은 데이터베이스 미러링을 보완할 수 있습니다. 비동기 데이터베이스 미러링의 좋은 대안입니다.
Azure의 PaaS(Platform as a Service)로서의 SQL.<br/><br/>이 배포 유형에는 단일 데이터베이스 및 탄력적 풀이 포함됩니다. | 활성 지리적 복제 | 장애 조치가 트리거된 후 30초.<br/><br/>장애 조치가 보조 데이터베이스 중 하나로 활성화된 경우 다른 모든 보조가 새로운 주 데이터베이스로 자동으로 연결됩니다. | 5초의 RPO.<br/><br/>활성 지역 복제는 SQL Server의 Always On 기술을 사용합니다. 스냅샷 격리를 사용하여 주 데이터베이스에서 커밋된 트랜잭션을 보조 데이터베이스로 비동기식으로 복제합니다.<br/><br/>보조 데이터에는 부분 트랜잭션이 포함되지 않을 수 있습니다.
Azure에서 활성 지역 복제를 사용하여 구성된 PaaS로서의 SQL.<br/><br/>이 배포 유형에는 관리형 인스턴스, 탄력적 풀 및 단일 데이터베이스가 포함됩니다. | 자동 장애 조치 그룹 | 1시간의 RTO. | 5초의 RPO.<br/><br/>자동 장애 조치 그룹은 활성 지역 복제의 맨 위에 그룹 의미 체계를 제공합니다. 하지만 같은 비동기 복제 메커니즘이 사용됩니다.
Azure IaaS VM 또는 온-프레미스의 SQL Server입니다.| Azure Site Recovery를 사용한 복제 | RTO는 일반적으로 15분 미만입니다. 자세한 내용은 [Site Recovery에서 제공하는 RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)를 읽어 보세요. | 애플리케이션 일관성을 위해 1시간, 크래시 일관성을 위해 5분. 더 낮은 RPO를 찾고 있는 경우 다른 BCDR 기술을 사용하세요.

> [!NOTE]
> Site Recovery를 사용하여 SQL 워크로드를 보호하는 데 도움이 되는 몇 가지 중요한 고려 사항은 다음과 같습니다.
> * Site Recovery는 애플리케이션에 구애받지 않습니다. Site Recovery는 지원되는 운영 체제에 배포된 모든 버전의 SQL Server를 보호할 수 있습니다. 자세히 알아보려면 복제된 머신의 [복구를 위한 지원 매트릭스](vmware-physical-azure-support-matrix.md#replicated-machines)를 참조하세요.
> * Azure, Hyper-V, VMware 또는 물리적 인프라의 모든 배포에 Site Recovery를 사용하도록 선택할 수 있습니다. Site Recovery를 사용하여 [SQL Server 클러스터를 보호하는 방법](#how-to-help-protect-a-sql-server-cluster)은 이 문서의 끝에 있는 참고 자료를 따르세요.
> * 머신에서 관찰된 데이터 변경 비율이 [Site Recovery 한도](vmware-physical-azure-support-matrix.md#churn-limits)내에 있는지 확인합니다. 변경 비율은 초당 쓰기 바이트 수로 측정됩니다. Windows를 실행하는 머신의 경우 작업 관리자에서 **성능** 탭을 선택하여 이 변경 비율을 볼 수 있습니다. 각 디스크의 쓰기 속도를 관찰합니다.
> * Site Recovery에서는 스토리지 공간 다이렉트에서 장애 조치 클러스터 인스턴스의 복제를 지원합니다. 자세히 알아보려면 [스토리지 공간 다이렉트 복제를 사용하도록 설정하는 방법](azure-to-azure-how-to-enable-replication-s2d-vms.md)을 참조하세요.
> 
> SQL 워크로드를 Azure로 마이그레이션하는 경우 [Azure Virtual Machines에서 SQL Server의 성능 지침](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md)을 적용하는 것이 좋습니다.

## <a name="disaster-recovery-of-an-application"></a>애플리케이션 재해 복구

Site Recovery는 복구 계획의 도움을 받아 전체 애플리케이션의 장애 조치 및 테스트 장애 조치를 오케스트레이션합니다.

필요에 따라 복구 계획을 완전히 사용자 지정하기 위한 몇 가지 필수 구성 요소가 있습니다. 모든 SQL Server 배포에는 일반적으로 Active Directory 배포가 필요합니다. 애플리케이션 계층에 대한 연결도 필요합니다.

### <a name="step-1-set-up-active-directory"></a>1단계: Active Directory 설정

SQL Server를 제대로 실행하려면 보조 복구 사이트에서 Active Directory를 설정합니다.

* **소기업**: 온-프레미스 사이트를 위한 소수의 애플리케이션과 단일 도메인 컨트롤러가 있습니다. 전체 사이트를 장애 조치하려는 경우 Site Recovery 복제를 사용합니다. 이 서비스는 도메인 컨트롤러를 보조 데이터 센터 또는 Azure에 복제합니다.
* **중견 기업 및 대기업**: 추가 도메인 컨트롤러를 설정해야 할 수도 있습니다.
  - 다수의 애플리케이션 및 Active Directory 포리스트가 있고 애플리케이션 또는 워크로드별로 장애 조치(failver)를 하려는 경우, 보조 데이터 센터 또는 Azure에서 또 다른 도메인 컨트롤러를 설정합니다.
  -  Always On 가용성 그룹을 사용하여 원격 사이트로 복구하는 경우, 보조 사이트 또는 Azure에서 또 다른 도메인 컨트롤러를 설정합니다. 이 도메인 컨트롤러는 복구된 SQL Server 인스턴스에 사용됩니다.

이 문서의 지침에서는 도메인 컨트롤러를 보조 위치에서 사용할 수 있다고 가정합니다. 자세히 알아보려면 [Site Recovery를 사용하여 Active Directory를 보호하도록 지원](site-recovery-active-directory.md)하는 절차를 참조하세요.

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>2단계: 다른 계층과의 연결 확인

데이터베이스 계층이 대상 Azure 지역에서 실행되고 나면 애플리케이션 및 웹 계층과 연결되어 있는지 확인합니다. 테스트 장애 조치와의 연결 유효성을 검사하기 위해 필요한 단계를 미리 수행합니다.

연결 고려 사항을 위해 애플리케이션을 설계하는 방법을 이해하려면 다음 예제를 참조하세요.

* [클라우드 재해 복구에 맞게 애플리케이션 디자인](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [탄력적 풀 재해 복구 전략](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>3단계: Always On, 활성 지역 복제 및 자동 장애 조치 그룹과 상호 운용

BCDR 기술 Always On, 활성 지역 복제 및 자동 장애 조치 그룹에는 대상 Azure 지역에서 실행되는 SQL Server의 보조 복제본이 있습니다. 애플리케이션 장애 조치의 첫 번째 단계는 이 복제본을 주 복제본으로 지정하는 것입니다. 이 단계에서는 보조에 도메인 컨트롤러가 이미 있다고 가정합니다. 자동 장애 조치를 수행하도록 선택하면 이 단계가 필요하지 않을 수 있습니다. 데이터베이스 장애 조치가 완료된 후에만 웹 및 애플리케이션 계층을 장애 조치합니다.

> [!NOTE]
> Site Recovery를 사용하여 SQL 머신을 보호하는 데 도움을 준 경우 이 머신의 복구 그룹을 만들고 복구 계획에 해당 장애 조치를 추가하기만 하면 됩니다.

애플리케이션 및 웹 계층 가상 머신을 사용하여 [복구 계획을 만듭니다](site-recovery-create-recovery-plans.md). 다음 단계에서는 데이터베이스 계층의 장애 조치를 추가하는 방법을 보여 줍니다.

1. [Resource Manager 가상 머신](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/demos/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) 및 [클래식 가상 머신](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/demos/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1) 둘 다에서 SQL 가용성 그룹을 장애 조치하는 스크립트를 가져옵니다. Azure Automation 계정에 스크립트를 가져옵니다.

    [![Azure에 배포 로고](../media/template-deployments/deploy-to-azure.svg)](https://aka.ms/asr-automationrunbooks-deploy)

1. ASR-SQL-FailoverAG 스크립트를 복구 계획의 첫 번째 그룹에 대한 이전 작업으로 추가합니다.

1. 스크립트에서 사용할 수 있는 지침에 따라 자동화 변수를 만듭니다. 이 변수는 가용성 그룹의 이름을 제공합니다.

### <a name="step-4-conduct-a-test-failover"></a>4단계: 테스트 장애 조치 수행

SQL Always On과 같은 일부 BCDR 기술은 기본적으로 테스트 장애 조치를 지원하지 않습니다. 다음 접근 방식은 *해당 기술을 사용할 때만* 사용하는 것이 좋습니다.

1. Azure에서 가용성 그룹 복제본을 호스트하는 VM에서 [Azure Backup](../backup/backup-azure-vms-first-look-arm.md)을 설정합니다.

1. 복구 계획의 테스트 장애 조치를 트리거하기 전에 이전 단계에서 수행된 백업에서 VM을 복구합니다.

    ![Azure Backup에서 구성을 복원하는 창을 보여 주는 스크린샷](./media/site-recovery-sql/restore-from-backup.png)

1. 백업에서 복원된 VM에서 [쿼럼을 강제 적용](/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)합니다.

1. 테스트 장애 조치 네트워크에서 사용할 수 있는 주소가 되도록 수신기의 IP 주소를 업데이트합니다.

    ![규칙 창 및 IP 주소 속성 대화 상자의 스크린샷](./media/site-recovery-sql/update-listener-ip.png)

1. 수신기를 온라인 상태로 전환합니다.

    ![서버 이름과 상태를 보여 주는 Content_AG라는 레이블이 붙은 창의 스크린샷](./media/site-recovery-sql/bring-listener-online.png)

1. 장애 조치 네트워크의 부하 분산 장치에 각 가용성 그룹 수신기에 해당하는 프런트 엔드 IP 주소 풀과 백 엔드 풀의 SQL Server VM에서 하나의 IP 주소가 있는지 확인합니다.

     ![“SQL-AlwaysOn-LB - 프런트 엔드 IP 풀”이라는 창의 스크린샷](./media/site-recovery-sql/create-load-balancer1.png)

    ![“SQL-AlwaysOn-LB - 백 엔드 IP 풀”이라는 창의 스크린샷](./media/site-recovery-sql/create-load-balancer2.png)

1. 이후 복구 그룹에서 애플리케이션 계층의 장애 조치를 추가한 다음에 이 복구 계획을 위한 웹 계층을 추가합니다.

1. 복구 계획의 테스트 장애 조치를 수행하여 애플리케이션의 엔드투엔드 장애 조치를 테스트합니다.

## <a name="steps-to-do-a-failover"></a>장애 조치(failover)를 수행하는 단계

3단계에서 스크립트를 추가하고 4단계에서 유효성을 검사한 다음, 3단계에서 만든 복구 계획의 장애 조치를 수행할 수 있습니다.

애플리케이션 및 웹 계층의 장애 조치 단계는 테스트 장애 조치 및 장애 조치 복구 계획 모두에서 동일해야 합니다.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>SQL Server 클러스터를 보호하도록 지원하는 방법

SQL Server Standard 버전 또는 SQL Server 2008 R2를 실행하는 클러스터의 경우 SQL Server를 보호하도록 지원하기 위해 Site Recovery 복제를 사용하는 것이 좋습니다.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure 대 Azure와 온-프레미스 대 Azure

Azure 지역으로 복제할 때 Site Recovery는 게스트 클러스터 지원을 제공하지 않습니다. SQL Server Standard 버전에서는 저비용 재해 복구 솔루션도 제공하지 않습니다. 이 시나리오에서는 SQL Server 클러스터를 주 위치의 독립 실행형 SQL Server 인스턴스로 보호하고 보조 위치에서 복구하는 것이 좋습니다.

1. 주 Azure 지역 또는 온-프레미스 사이트에 추가 독립 실행형 SQL Server 인스턴스를 구성합니다.

1. 보호하도록 지원할 데이터베이스에 대한 미러로 제공하기 위해 인스턴스를 구성합니다. 높은 보안 모드에서 미러링을 구성합니다.

1. [Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](./hyper-v-azure-tutorial.md) 또는 [VMware VM 및 물리적 서버](./vmware-azure-tutorial.md)의 기본 사이트에 Site Recovery를 구성합니다.

1. 보조 사이트에 새 SQL Server 인스턴스를 복제하도록 Site Recovery 복제를 사용합니다. 높은 보안 미러 복사이므로 주 클러스터와 동기화되지만 Site Recovery 복제를 사용하여 복제됩니다.

   ![기본 사이트, Site Recovery 및 Azure 간의 관계와 흐름을 보여 주는 표준 클러스터 이미지](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>장애 복구 고려 사항

SQL Server Standard 클러스터의 경우 계획되지 않은 장애 조치 후 장애 복구를 수행하려면 SQL Server 백업 및 복원이 필요합니다. 이 작업은 미러를 다시 설정하여 미러 인스턴스에서 원래 클러스터로 수행됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Site Recovery와 함께 사용할 때 SQL Server에 라이선스가 부여되는 방식은 무엇인가요?

SQL Server용 Site Recovery 복제는 Software Assurance 재해 복구 혜택에 포함됩니다. 이 범위는 온-프레미스에서 Azure 재해 복구 및 지역 간 Azure IaaS 재해 복구와 같은 모든 Site Recovery 시나리오에 적용됩니다. 자세한 내용은 [VM의 Azure Site Recovery 가격 책정](https://azure.microsoft.com/pricing/details/site-recovery/)을 참조하세요.

### <a name="will-site-recovery-support-my-sql-server-version"></a>Site Recovery에서 내 SQL Server 버전을 지원하나요?

Site Recovery는 애플리케이션에 구애받지 않습니다. Site Recovery는 지원되는 운영 체제에 배포된 모든 버전의 SQL Server를 보호할 수 있습니다. 자세히 알아보려면 복제된 머신의 [복구를 위한 지원 매트릭스](vmware-physical-azure-support-matrix.md#replicated-machines)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Site Recovery 아키텍처](./azure-to-azure-architecture.md)에 관해 자세히 알아봅니다.
* Azure용 SQL Server의 경우, 보조 Azure 지역의 복구를 위한 [고가용성 솔루션](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions)에 관해 자세히 알아보세요.
* SQL Database의 경우, 보조 Azure 지역에서 복구하기 위한 [비즈니스 연속성](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) 및 [고가용성](../azure-sql/database/high-availability-sla.md) 옵션에 관해 자세히 알아보세요.
* 온-프레미스에 있는 SQL Server 머신의 경우 Azure Virtual Machines에서 복구하기 위한 [고가용성 옵션](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)에 관해 자세히 알아보세요.
