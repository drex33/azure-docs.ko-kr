---
title: 콘텐츠 참조 구성 및 관리
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance를 구성하고 관리하는 방법을 안내하는 내용으로 구성된 참조 가이드입니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1, ignite-fall-2021
ms.devlang: ''
ms.topic: guide
author: MashaMSFT
ms.author: mathoma
ms.date: 04/16/2019
ms.openlocfilehash: 86511b0cb494ecd5cb9f60fb8aa825a11fe48065
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045637"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Azure SQL Managed Instance 콘텐츠 참조
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance를 관리 및 구성하는 데 도움이 되는 다양한 가이드, 스크립트 및 설명에 대한 콘텐츠 참조를 찾을 수 있습니다.

## <a name="load-data"></a>데이터 로드

- [SQL Server에서 Azure SQL Managed Instance로 마이그레이션 가이드](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md): Azure SQL Managed Instance로 마이그레이션하기 위한 권장되는 마이그레이션 프로세스와 도구에 대해 알아봅니다.
- [Azure SQL Managed Instance로 TDE 인증서 마이그레이션](tde-certificate-migrate.md): SQL Server 데이터베이스가 TDE(투명한 데이터 암호화)로 보호되는 경우 Azure에서 복원하려는 백업의 암호를 해독하는 데 SQL Managed Instance가 사용할 수 있는 인증서를 마이그레이션해야 합니다.
- [BACPAC에서 DB 가져오기](../database/database-import.md)
- [BACPAC로 DB 내보내기](../database/database-export.md)
- [BCP를 사용하여 데이터 로드](../load-from-csv-with-bcp.md)
- [Azure Data Factory를 사용하여 데이터 로드](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>네트워크 구성

- [서브넷 크기 확인](vnet-subnet-determine-size.md): SQL Managed Instance를 배포한 후 서브넷의 크기를 조정할 수 없음으로 서브넷에 배포하려는 관리되는 인스턴스의 수와 유형에 필요한 IP 주소 범위를 계산해야 합니다. 
- [새 VNet 및 서브넷 만들기](virtual-network-subnet-create-arm-template.md): [네트워크 요구 사항](connectivity-architecture-overview.md#network-requirements)에 따라 가상 네트워크 및 서브넷을 구성합니다. 
- [기존 VNet 및 서브넷 구성](vnet-existing-add-subnet.md): 네트워크 요구 사항을 확인하고 기존 가상 네트워크 및 서브넷을 구성하여 SQL Managed Instance를 배포합니다.
- [Azure Storage에 대 한 서비스 끝점 정책 구성 (미리 보기)](service-endpoint-policies-configure.md): 권한이 없는 Azure Storage 계정에 대해 잘못 되거나 악의적인 데이터 반출에 대해 서브넷을 보호 합니다.
- [사용자 지정 DNS 구성](custom-dns-configure.md): db 메일 프로필의 연결된 서버를 통해 SQL Managed Instance에서 사용자 지정 도메인에 대한 외부 리소스 액세스 권한을 부여하도록 사용자 지정 DNS를 구성합니다. 
- [동기화 네트워크 구성](azure-app-sync-network-configuration.md): [Azure 가상 네트워크와 앱을 통합](../../app-service/overview-vnet-integration.md)한 후 연결을 설정할 수 없는 경우 네트워킹 구성 계획을 새로 고칩니다.
- [관리 엔드포인트 IP 주소 찾기](management-endpoint-find-ip-address.md): 관리 목적으로 SQL Managed Instance에서 사용하는 퍼블릭 엔드포인트를 결정합니다. 
- [기본 제공 방화벽 보호 확인](management-endpoint-verify-built-in-firewall.md): SQL Managed Instance가 필수 포트와 기타 기본 제공 방화벽 규칙에 대한 트래픽만 허용하는지 확인합니다. 
- [애플리케이션 연결](connect-application-instance.md): 애플리케이션을 SQL Managed Instance에 연결하는 데 사용할 수 있는 다양한 패턴에 대해 알아봅니다.

## <a name="feature-configuration"></a>기능 구성

- [Azure AD 인증 구성](../database/authentication-aad-configure.md)
- [조건부 액세스 구성](../database/conditional-access-configure.md)
- [Multi-Factor Azure AD 인증](../database/authentication-mfa-ssms-overview.md)
- [Multi-Factor Auth 구성](../database/authentication-mfa-ssms-configure.md)
- [임시 보존 정책 구성](../database/temporal-tables-retention-policy.md)
- [BYOK를 사용하여 TDE 구성](../database/transparent-data-encryption-byok-configure.md)
- [TDE BYOK 키 순환](../database/transparent-data-encryption-byok-key-rotation.md)
- [TDE 보호기 제거](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [메모리 내 OLTP 구성](../in-memory-oltp-configure.md)
- [Azure Automation 구성](../database/automation-manage.md)
- [트랜잭션 복제](replication-between-two-instances-configure-tutorial.md)를 사용하면 관리되는 인스턴스 간에, 또는 온-프레미스 SQL Server와 Managed Instance 간에 데이터를 복제할 수 있습니다.
- [위협 검색 구성](threat-detection-configure.md) - [위협 검색](../database/threat-detection-overview.md)은 SQL 삽입 또는 의심스러운 위치에서의 액세스와 같은 다양한 잠재적 공격을 검색하는 기본 제공 Azure SQL Managed Instance 기능입니다. 
- [경고를 만들면](alerts-create.md) SQL Managed Instance에 대한 CPU 사용률, 스토리지 공간 사용량, IOPS 등 모니터링되는 메트릭에 대한 경고를 설정할 수 있습니다. 

## <a name="monitoring-and-tuning"></a>모니터링 및 튜닝

- [수동 조정](../database/performance-guidance.md)
- [DMV를 사용하여 성능 모니터링](../database/monitoring-with-dmvs.md)
- [쿼리 스토리지를 사용하여 성능 모니터링](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Intelligent Insights로 성능 문제 해결](../database/intelligent-insights-troubleshoot-performance.md)
- [Intelligent Insights 진단 로그 사용](../database/intelligent-insights-use-diagnostics-log.md)
- [메모리 내 OLTP 공간 모니터링](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>확장 이벤트

- [확장 이벤트](../database/xevent-db-diff-from-svr.md)
- [확장 이벤트를 이벤트 파일에 저장](../database/xevent-code-event-file.md)
- [확장 이벤트를 링 버퍼에 저장](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>경고

- [관리형 인스턴스에 대한 경고 만들기](alerts-create.md)

## <a name="operations"></a>작업

- [SQL Managed Instance에서 사용자가 시작한 수동 장애 조치(failover)](user-initiated-failover.md)

## <a name="develop-applications"></a>애플리케이션 개발

- [연결](../database/connect-query-content-reference-guide.md#libraries)
- [Spark 커넥터 사용](../../cosmos-db/create-sql-api-spark.md)
- [앱 인증](../database/application-authentication-get-client-id-keys.md)
- [성능 향상을 위해 일괄 처리 사용](../performance-improve-use-batching.md)
- [연결 지침](../database/troubleshoot-common-connectivity-issues.md)
- [DNS 별칭](../database/dns-alias-overview.md)
- [PowerShell을 사용하여 DNS 별칭 설정](../database/dns-alias-powershell-create.md)
- [포트 - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C 및 C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>애플리케이션 디자인

- [재해 복구를 위한 디자인](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [탄력적 풀을 위한 디자인](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [앱 업그레이드를 위한 디자인](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>다중 테넌트 SaaS 애플리케이션 디자인

- [SaaS 디자인 패턴](../database/saas-tenancy-app-design-patterns.md)
- [SaaS 비디오 사용](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 앱 보안](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>다음 단계

[SQL Managed Instance를 배포](instance-create-quickstart.md)하여 시작합니다.
