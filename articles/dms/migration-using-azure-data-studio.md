---
title: Azure Data Studio를 사용하여 마이그레이션
description: Azure Data Studio에서 Azure SQL 마이그레이션 확장을 사용하여 Azure Database Migration Service로 데이터베이스를 마이그레이션하는 방법을 알아봅니다.
services: database-migration
author: mokabiru
ms.author: mokabiru
manager: ''
ms.reviewer: cheryl.adams
ms.service: dms
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: e33c1fc4b5daa3ca2092e63413dcedcc42f82509
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123354552"
---
# <a name="migrate-databases-with-azure-sql-migration-extension-for-azure-data-studio-preview"></a>Azure Data Studio에 대한 Azure SQL 마이그레이션 확장을 사용하여 데이터베이스 마이그레이션(미리 보기)

[Azure Data Studio](/sql/azure-data-studio/what-is-azure-data-studio.md)에 대한 Azure SQL 마이그레이션 확장을 사용하면 Azure Data Studio에서 새로운 SQL Server 평가 및 마이그레이션 기능을 사용할 수 있습니다.

## <a name="architecture-of-azure-sql-migration-extension-for-azure-data-studio"></a>Azure Data Studio에 대한 Azure SQL 마이그레이션 확장의 아키텍처

Azure DMS(Database Migration Service)는 전체 아키텍처의 핵심 구성 요소 중 하나입니다. DMS는 데이터베이스를 Azure SQL로 마이그레이션할 수 있도록 안정적인 마이그레이션 오케스트레이터를 제공합니다. ADS(Azure Data Studio)에서 Azure SQL 마이그레이션 확장을 사용하여 기존 DMS를 만들거나 재사용합니다.
DMS는 Azure Data Factory의 자체 호스팅 통합 런타임을 사용하여 온-프레미스 네트워크 공유 또는 Azure Storage 계정에서 유효한 백업 파일에 액세스하고 업로드합니다.

마이그레이션 프로세스의 워크플로는 아래에 설명되어 있습니다.

:::image type="content" source="media/migration-using-azure-data-studio/architecture-ads-sql-migration.png" alt-text="DMS와 함께 Azure Data Studio를 사용한 데이터베이스 마이그레이션 아키텍처 다이어그램":::

1. **원본 SQL Server**: SQL Server 인스턴스 온-프레미스, 프라이빗 클라우드 또는 모든 퍼블릭 클라우드 가상 머신입니다. SQL Server 2008 이상의 모든 버전이 지원됩니다.
1. **대상 Azure SQL**: 지원되는 Azure SQL 대상은 Azure Virtual Machines의 Azure SQL Managed Instance 또는 SQL Server입니다([전체 관리 모드](../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md#management-modes)에서 SQL IaaS 에이전트 확장으로 등록됨).
1. **네트워크 파일 공유**: 마이그레이션할 데이터베이스에 대한 백업 파일이 저장되는 SMB(서버 메시지 블록) 네트워크 파일 공유입니다. Azure Storage Blob 컨테이너 및 Azure Storage 파일 공유도 지원됩니다.
1. **Azure Data Studio**: [Azure Data Studio의 Azure SQL 마이그레이션 확장](/sql/azure-data-studio/extensions/azure-sql-migration-extension)을 다운로드하여 설치합니다.
1. **Azure DMS**: 마이그레이션 파이프라인을 오케스트레이션하여 온-프레미스에서 Azure로 데이터 이동 활동을 수행하는 Azure 서비스입니다. DMS는 ADF(Azure Data Factory) 자체 호스팅 IR(통합 런타임)과 연결되어 있으며 자체 호스팅 IR을 등록하고 모니터링하는 기능을 제공합니다.
1. **자체 호스팅 IR(통합 런타임)** : 자체 호스팅 IR은 원본 SQL Server 및 백업 파일 위치에 연결할 수 있는 컴퓨터에 설치해야 합니다. DMS는 인증 키를 제공하고 자체 호스팅 IR을 등록합니다.
1. **Azure Storage에 백업 파일 업로드**: DMS는 자체 호스팅 IR을 사용하여 온-프레미스 백업 위치에서 프로비저닝된 Azure Storage 계정으로 유효한 백업 파일을 업로드합니다. 데이터 이동 활동 및 파이프라인은 마이그레이션 워크플로에서 자동으로 만들어져 백업 파일을 업로드합니다.
1. **대상 Azure SQL에서 백업 복원**: DMS는 Azure Storage 계정에서 지원되는 대상 Azure SQL로 백업 파일을 복원합니다. 
    > [!IMPORTANT]
    > 온라인 마이그레이션 모드를 사용하면 DMS는 원본 백업 파일을 Azure Storage에 지속적으로 업로드하고 대상에 대한 컷오버의 마지막 단계를 완료할 때까지 대상에 복원합니다.
    >
    > 오프라인 마이그레이션 모드에서 DMS는 원본 백업 파일을 Azure Storage에 업로드하고 사용자가 컷오버를 수행할 필요 없이 대상에 복원합니다.

## <a name="prerequisites"></a>필수 조건

지원되는 모든 마이그레이션 시나리오에 공통적인 Azure Database Migration Service 필구 구성 요소는 다음을 수행해야 합니다.

* [Azure Data Studio 다운로드 및 설치](/sql/azure-data-studio/download-azure-data-studio.md)
* Azure Data Studio Marketplace에서 [Azure SQL 마이그레이션 확장 설치](/sql/azure-data-studio/extensions/azure-sql-migration-extension.md)
* 아래 나열된 기본 제공 역할 중 하나에 할당된 Azure 계정이 있습니다.
    - 대상 Azure SQL Managed Instance(및 SMB 네트워크 공유에서 데이터베이스 백업 파일을 업로드하기 위한 스토리지 계정)의 기여자
    - 대상 Azure SQL Managed Instance 또는 Azure Storage 계정을 포함하는 Azure 리소스 그룹에 대한 소유자 또는 기여자 역할
    - Azure 구독에 대한 소유자 또는 기여자 역할
* 대상 [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md) 또는 [Azure Virtual Machine의 SQL Server](../azure-sql/virtual-machines/windows/create-sql-vm-portal.md)를 만듭니다.
* 원본 SQL Server는 연결하는 데 사용되는 로그인이 *sysadmin* 서버 역할의 구성원이거나 `CONTROL SERVER` 권한이 있는지 확인합니다. 
* 전체 데이터베이스 및 트랜잭션 로그 백업 파일에 대해 다음 스토리지 옵션 중 하나를 사용합니다. 
    - SMB 네트워크 공유 
    - Azure Storage 계정 파일 공유 또는 Blob 컨테이너 

    > [!IMPORTANT]
    > - 데이터베이스 백업 파일이 SMB 네트워크 공유에 제공된 경우 DMS 서비스가 데이터베이스 백업 파일을 업로드할 수 있도록 [Azure Storage 계정을 만듭니다](../storage/common/storage-account-create.md).  Azure Database Migration Service 인스턴스와 동일한 지역에 Azure Storage 계정을 만들어야 합니다.
    > - Azure Database Migration Service는 백업을 새로 시작하는 것이 아니라 사용자가 재해 복구 계획의 일부로 보유한 기존 백업을 마이그레이션에 사용합니다.
    > - [`WITH CHECKSUM` 옵션을 사용하여 백업](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)해야 합니다. 
    > - 각 백업은 별도의 백업 파일 또는 여러 백업 파일에 쓸 수 있습니다. 그러나 단일 백업 미디어에 여러 백업(예: 전체 및 t-log)을 추가하는 것은 지원되지 않습니다. 
    > - 압축된 백업을 사용하여 대량 백업 마이그레이션과 관련된 잠재적인 문제 발생 가능성을 줄일 수 있습니다.
* 원본 SQL Server 인스턴스를 실행하는 서비스 계정에 데이터베이스 백업 파일이 포함된 SMB 네트워크 공유에 대한 읽기 및 쓰기 권한이 있는지 확인합니다.
* TDE(투명한 데이터 암호화)로 보호되는 데이터베이스의 원본 SQL Server 인스턴스 인증서는 데이터를 마이그레이션하기 전에 대상 Azure SQL Managed Instance 또는 Azure Virtual Machine의 SQL Server로 마이그레이션해야 합니다. 자세한 내용은 [TDE 보호 데이터베이스의 인증서를 Azure SQL Managed Instance로 마이그레이션](../azure-sql/managed-instance/tde-certificate-migrate.md) 및 [TDE 보호 데이터베이스를 다른 SQL Server로 이동](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)을 참조하세요.
    > [!TIP]
    > 데이터베이스에 [항상 암호화](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio)로 보호되는 민감한 데이터가 포함되어 있는 경우 DMS와 함께 Azure Data Studio를 사용하는 마이그레이션 프로세스는 Always Encrypted 키를 대상 Azure SQL Managed Instance 또는 Azure Virtual Machine의 SQL Server로 자동 마이그레이션합니다.

* 데이터베이스 백업이 네트워크 파일 공유에 있는 경우 데이터베이스 백업에 액세스하고 마이그레이션하기 위해 [자체 호스팅 통합 런타임](../data-factory/create-self-hosted-integration-runtime.md)을 설치할 컴퓨터를 제공합니다. 마이그레이션 마법사는 자체 호스팅 통합 런타임을 다운로드하고 설치하기 위한 다운로드 링크와 인증 키를 제공합니다. 마이그레이션을 준비하기 위해 자체 호스팅 통합 런타임을 설치하려는 시스템에 다음 아웃바운드 방화벽 규칙 및 도메인 이름이 사용하도록 설정되어 있는지 확인합니다.

    | 도메인 이름                                          | 아웃바운드 포트 | Description                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | 퍼블릭 클라우드: `{datafactory}.{region}.datafactory.azure.net`<br> 또는 `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> 중국: `{datafactory}.{region}.datafactory.azure.cn` | 443            | 자체 호스팅 통합 런타임에서 데이터 마이그레이션 서비스에 연결하는 데 필요합니다. <br>퍼블릭 클라우드에서 새로 만들어진 Data Factory의 경우 `{datafactory}.{region}.datafactory.azure.net` 형식인 자체 호스팅 통합 런타임 키에서 FQDN을 찾습니다. 이전 Data Factory의 경우 FQDN이 자체 호스팅 통합 키에 표시되지 않으면 *.frontend.clouddatahub.net을 대신 사용합니다. |
    | `download.microsoft.com`    | 443            | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. 자동 업데이트를 사용하지 않도록 설정한 경우 이 도메인 구성을 건너뛸 수 있습니다. |
    | `*.core.windows.net`          | 443            | 네트워크 공유에서 데이터베이스 백업을 업로드하기 위해 Azure Storage 계정에 연결하는 자체 호스팅 통합 런타임에서 사용합니다. |

    > [!TIP]
    > 데이터베이스 백업 파일이 이미 Azure Storage 계정에 제공된 경우 마이그레이션 프로세스 중에 자체 호스팅 통합 런타임이 필요하지 않습니다.

* 자체 호스팅 통합 런타임을 사용하는 경우 런타임이 설치된 시스템이 백업 파일이 있는 원본 SQL Server 인스턴스 및 네트워크 파일 공유에 연결할 수 있는지 확인합니다. 네트워크 파일 공유에 대한 액세스를 허용하려면 아웃바운드 포트 445를 사용하도록 설정해야 합니다.
* Azure Database Migration Service를 처음 사용하는 경우 Microsoft.DataMigration 리소스 공급자가 구독에 등록되어 있는지 확인하세요. 다음 단계에 따라 [리소스 공급자를 등록](/quickstart-create-data-migration-service-portal.md#register-the-resource-provider)할 수 있습니다.

### <a name="recommendations-for-using-self-hosted-integration-runtime-for-database-migrations"></a>데이터베이스 마이그레이션을 위한 자체 호스팅 통합 런타임 사용에 대한 권장 사항
- 여러 원본 SQL Server 데이터베이스에 단일 자체 호스팅 통합 런타임을 사용합니다.
- 각 머신에는 자체 호스팅 통합 런타임 인스턴스를 하나씩만 설치합니다.
- 하나의 자체 호스팅 통합 런타임만 하나의 DMS와 연결합니다.
- 자체 호스팅 통합 런타임은 설치된 시스템의 리소스(메모리/CPU)를 사용합니다. 원본 SQL Server와 다른 시스템에 자체 호스팅 통합 런타임을 설치합니다. 그러나 자체 호스팅 통합 런타임이 데이터 원본에 가까우면, 데이터 원본에 연결하는 시간이 줄어듭니다. 
- 온-프레미스 SMB 네트워크 공유에 데이터베이스 백업이 있는 경우에만 자체 호스팅 통합 런타임을 사용합니다. 원본 데이터베이스 백업이 이미 Azure Storage Blob 컨테이너에 있는 경우 데이터베이스 마이그레이션에 자체 호스팅 통합 런타임이 필요하지 않습니다.
- 단일 시스템에서 자체 호스팅 통합 런타임당 최대 10개의 동시 데이터베이스 마이그레이션을 권장합니다. 동시 데이터베이스 마이그레이션 수를 늘리려면 자체 호스팅 런타임을 최대 4개의 노드로 확장하거나 다른 시스템에서 별도의 자체 호스팅 통합 런타임을 만듭니다.
- 릴리스된 모든 새로운 기능, 버그 수정 및 개선 사항을 자동으로 적용하도록 자체 호스팅 통합 런타임을 자동 업데이트하도록 구성합니다. 자세한 내용은 [자체 호스팅 통합 런타임 자동 업데이트](../data-factory/self-hosted-integration-runtime-auto-update.md)를 참조하세요.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항
- 대상 Azure SQL Managed Instance 또는 Azure Virtual Machine의 SQL Server에서 DMS를 사용하여 기존 데이터베이스를 덮어쓰는 것은 지원되지 않습니다.
- 원본 토폴로지와 일치하도록 대상에서 고가용성 및 재해 복구를 구성하는 것은 DMS에서 지원되지 않습니다.
- 다음 서버 개체는 지원되지 않습니다.
    - 로그인
    - SQL Server 에이전트 작업
    - 자격 증명
    - SSIS 패키지
    - 서버 역할
    - 서버 감사
- PowerShell/CLI를 사용하여 Azure Data Studio로 마이그레이션 자동화는 지원되지 않습니다.
- Azure SQL Database로의 마이그레이션은 지원되지 않습니다.
- 특정 방화벽 규칙으로 보호되거나 프라이빗 엔드포인트로 구성된 Azure Storage 계정은 마이그레이션이 지원되지 않습니다.
- DMS를 사용한 데이터베이스 마이그레이션을 위해 Azure Data Factory에서 만든 기존 자체 호스팅 통합 런타임을 사용할 수 없습니다. 처음에 자체 호스팅 통합 런타임은 Azure Data Studio에서 Azure SQL 마이그레이션 확장을 사용하여 만들어야 하며 추가 데이터베이스 마이그레이션에 재사용할 수 있습니다.
> [!IMPORTANT]
> **여러 데이터베이스를 Azure VM의 SQL Server로 마이그레이션할 때 알려진 문제:** 여러 데이터베이스를 Azure VM의 동일한 SQL Server로 동시에 마이그레이션하면 대부분의 데이터베이스에서 마이그레이션이 실패합니다. 특정 시점에 단일 데이터베이스만 Azure VM의 SQL Server로 마이그레이션해야 합니다.  

### <a name="regions"></a>영역
미리 보기 중에 대상 Azure SQL Managed Instance 또는 Azure Virtual Machine의 SQL Server로 SQL Server 데이터베이스를 다음 지역 중 하나로 마이그레이션합니다.
- 오스트레일리아 동부
- 오스트레일리아 남동부
- 캐나다 중부
- 캐나다 동부
- 인도 중부
- 미국 중부
- 미국 동부
- 미국 동부 2
- 프랑스 중부
- 일본 동부
- 미국 중북부
- 미국 중남부
- 동남아시아
- 인도 남부
- 영국 남부
- 서유럽
- 미국 서부
- 미국 서부 2

## <a name="pricing"></a>가격 책정
- Azure Database Migration Service는 Azure Data Studio의 Azure SQL 마이그레이션 확장과 함께 무료로 사용할 수 있습니다. 서비스 또는 Azure SQL 마이그레이션 확장을 사용하는 비용 없이 Azure Database Migration Service를 사용하여 여러 SQL Server 데이터베이스를 마이그레이션할 수 있습니다.
- 온-프레미스에서 Azure로 데이터베이스를 마이그레이션하기 위한 데이터 이동 또는 데이터 수신 비용이 없습니다. 원본 데이터베이스를 다른 지역이나 Azure VM에서 이동하는 경우 대역폭 공급자 및 라우팅 시나리오에 따라 [대역폭 요금](https://azure.microsoft.com/pricing/details/bandwidth/)이 발생할 수 있습니다.
- Azure Data Studio를 설치할 고유한 컴퓨터 또는 온-프레미스 서버를 제공합니다.
- 온-프레미스 네트워크 공유에서 데이터베이스 백업에 액세스하려면 자체 호스팅 통합 런타임이 필요합니다.

## <a name="next-steps"></a>다음 단계

- Azure SQL 마이그레이션 확장의 개요 및 설치는 [Azure Data Studio용 Azure SQL 마이그레이션 확장](/sql/azure-data-studio/extensions/azure-sql-migration-extension.md)을 참조하세요.
