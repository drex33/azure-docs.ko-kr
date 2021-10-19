---
title: 가상 머신의 SQL Server로 SQL Server 데이터베이스 마이그레이션 | Microsoft Docs
description: 온-프레미스 사용자 데이터베이스를 Azure 가상 머신의 SQL Server로 마이그레이션하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: bluefooted
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: migration
ms.topic: how-to
ms.date: 08/18/2018
ms.author: pamela
ms.reviewer: mathoma
ms.openlocfilehash: 1d5454ad84baadf82ecea93793cc4cf22d4fb22f
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165301"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>Azure 가상 머신의 SQL Server로 SQL Server 데이터베이스 마이그레이션

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

온-프레미스 SQL Server 사용자 데이터베이스를 Azure VM(가상 머신)의 SQL Server로 마이그레이션하는 다양한 방법이 있습니다. 이 문서는 다양한 방법을 간략하게 설명하고 다양한 시나리오에 대해 가장 좋은 방법을 권장합니다.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 및 SQL Server 2008 R2는 온-프레미스 인스턴스에 대한 [지원 수명 주기 종료](https://www.microsoft.com/sql-server/sql-server-2008)에 다다르고 있습니다. 지원을 연장하려면 SQL Server 인스턴스를 Azure VM으로 마이그레이션하거나 연장 보안 업데이트를 구입하여 온-프레미스로 유지할 수 있습니다. 자세한 내용은 [Azure를 사용하여 SQL Server 2008 및 2008 R2에 대한 지원 연장](sql-server-2008-extend-end-of-support.md)을 참조하세요.

## <a name="what-are-the-primary-migration-methods"></a>기본 마이그레이션 메서드란?

기본 마이그레이션 메서드는 다음과 같습니다.

* 압축을 사용하여 온-프레미스 백업을 수행하고 수동으로 Azure VM에 백업 파일을 복사합니다.
* URL에 백업을 수행하고 URL에서 Azure VM으로 복원합니다.
* 데이터 및 로그 파일을 분리하고 Azure Blob 스토리지에 복사한 다음 URL에서 Azure VM의 SQL Server에 연결합니다.
* 온-프레미스 물리적 컴퓨터를 Hyper-V VHD로 변환하고 Azure Blob 스토리지로 업로드한 다음 업로드한 VHD를 사용하여 새 VM으로 배포합니다.
* Windows Import/Export 서비스를 사용하여 하드 드라이브를 운송합니다.
* 온-프레미스에 AlwaysOn 가용성 그룹 배포가 있는 경우에는 [Azure 복제본 추가 마법사](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability)를 사용하여 Azure에 복제본을 만들고 장애 조치(failover)한 다음 사용자가 Azure 데이터베이스 인스턴스에 연결되도록 설정합니다.
* SQL Server [트랜잭션 복제](/sql/relational-databases/replication/transactional/transactional-replication)를 사용하여 Azure SQL Server 인스턴스를 구독자로 구성하고 복제를 비활성화한 다음 사용자가 Azure 데이터베이스 인스턴스로 연결되도록 설정합니다.

> [!TIP]
> Azure에서 SQL Server VM 간에 데이터베이스를 이동하기 위해 동일한 방법을 사용할 수 있습니다. 예를 들어, SQL Server 갤러리 이미지 VM을 한 버전에서 다른 버전으로 업그레이드하는 방법이 지원되지 있습니다. 이 경우에 새 버전을 사용하여 새 SQL Server VM을 만들고 이 문서에서 데이터베이스를 이동하는 마이그레이션 방법 중 하나를 사용해야 합니다. 

## <a name="choose-a-migration-method"></a>마이그레이션 방법 선택

최상의 데이터 전송 성능을 위해 압축된 백업 파일을 사용하여 Azure VM에 데이터베이스 파일을 마이그레이션합니다.

데이터베이스 마이그레이션 프로세스 동안 가동 중지 시간을 최소화하려면 AlwaysOn 옵션 또는 트랜잭션 복제 옵션을 사용합니다.

위의 메서드를 사용할 수 없는 경우 수동으로 데이터베이스를 마이그레이션합니다. 일반적으로 데이터베이스 백업부터 시작하고 Azure로 데이터베이스 백업을 복사한 다음 데이터베이스를 복원합니다. 또한 데이터베이스 파일 자체를 Azure에 복사한 다음 연결할 수 있습니다. 데이터베이스를 Azure VM에 수동으로 마이그레이션하는 프로세스를 수행하는 몇 가지 방법이 있습니다.

> [!NOTE]
> 이전 버전의 SQL Server를 SQL Server 2014 또는 SQL Server 2016으로 업그레이드하는 경우 변경이 필요할지를 고려해야 합니다. 마이그레이션 프로젝트의 일환으로 SQL Server의 새 버전에서 지원하지 않는 기능에 대한 모든 종속성을 신중하게 검토할 것을 권장합니다. 지원되는 버전 및 시나리오에 대한 자세한 내용은 [SQL Server로 업그레이드](/sql/database-engine/install-windows/upgrade-sql-server)를 참조하세요.

다음 테이블에는 기본 마이그레이션 메서드가 나열되어 있고 각 메서드를 사용하기에 가장 적합한 경우가 설명되어 있습니다.

| 방법 | 원본 데이터베이스 버전 | 대상 데이터베이스 버전 | 원본 데이터베이스 백업 크기 제약 조건 | 메모 |
| --- | --- | --- | --- | --- |
| [압축을 사용하여 온-프레미스 백업을 수행하고 Azure 가상 머신에 백업 파일을 수동으로 복사](#back-up-and-restore) |SQL Server 2005 이상 |SQL Server 2005 이상 |[Azure VM 스토리지 제한](../../../index.yml) | 이 기술은 머신 간에 데이터베이스를 이동하는 간단하고 검증된 방법입니다. |
| [URL에 백업을 수행하고 URL에서 Azure 가상 머신으로 복원](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 이상 | SQL Server 2012 SP1 CU2 이상 | SQL Server 2016의 경우 12.8TB 미만, 그렇지 않은 경우 1TB 미만 | 이 방법은 Azure Storage를 사용하여 VM에 백업 파일을 이동하는 또 다른 방법입니다. |
| [데이터 및 로그 파일을 분리하여 Azure Blob 스토리지에 복사한 후 URL에서 Azure 가상 머신의 SQL Server에 연결](#detach-and-attach-from-a-url) | SQL Server 2005 이상 |SQL Server 2014 이상 | [Azure VM 스토리지 제한](../../../index.yml) | Azure File Storage를 DSVM에 탑재하려면 [데이터 과학 팀에 대한 팀 리더 작업](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)의 섹션 4에서 설명하는 지침을 참조하세요. |
| [온-프레미스 컴퓨터를 Hyper-V VHD로 변환하고 Azure Blob Storage에 업로드한 후 업로드된 VHD를 사용하여 새 가상 머신 배포](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 이상 |SQL Server 2005 이상 |[Azure VM 스토리지 제한](../../../index.yml) |[자체 SQL Server 라이선스를 소유](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)하는 경우, 이전 버전의 SQL Server에서 실행되는 데이터베이스를 마이그레이션하는 경우, 또는 다른 사용자 데이터베이스 및/또는 시스템 데이터베이스에 종속되는 데이터베이스 마이그레이션의 일부로 시스템과 사용자 데이터베이스를 함께 마이그레이션하는 경우에 사용합니다. |
| [Windows Import/Export 서비스를 사용하여 하드 드라이브 제공](#ship-a-hard-drive) |SQL Server 2005 이상 |SQL Server 2005 이상 |[Azure VM 스토리지 제한](../../../index.yml) |매우 큰 데이터베이스에 사용하는 경우와 같이 수동 복사 메서드가 너무 느린 경우 [Windows Import/Export 서비스](../../../import-export/storage-import-export-service.md) 를 사용 |
| [Azure 복제본 추가 마법사 사용](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) |SQL Server 2012 이상 |SQL Server 2012 이상 |[Azure VM 스토리지 제한](../../../index.yml) |가동 중지 시간을 최소화하고 Always On 온-프레미스 배포가 있는 경우 사용 |
| [SQL Server 트랜잭션 복제 사용](/sql/relational-databases/replication/transactional/transactional-replication) |SQL Server 2005 이상 |SQL Server 2005 이상 |[Azure VM 스토리지 제한](../../../index.yml) |가동 중지 시간을 최소화하고 Always On 온-프레미스 배포가 없는 경우 사용 |

## <a name="back-up-and-restore"></a>백업 및 복원

압축을 사용하여 데이터베이스를 백업하고 VM에 백업을 복사한 다음 데이터베이스를 복원합니다. VM 디스크의 최대 크기가 1TB이므로 백업 파일이 1TB를 초과하는 경우 백업 파일을 스트라이핑해야 합니다. 수동 메서드를 사용하여 사용자 데이터베이스를 마이그레이션하려면 다음과 같은 일반적인 단계를 사용합니다.

1. 온-프레미스 위치에 전체 데이터베이스 백업을 수행합니다.
2. 원하는 버전의 SQL Server로 가상 머신을 만들거나 업로드합니다.
3. 요구 사항에 따라 연결을 설정합니다. [Azure에서 SQL Server Virtual Machine 연결(Resource Manager)](ways-to-connect-to-sql.md)을 참조하세요.
4. 원격 데스크톱, Windows Explorer 또는 명령 프롬프트의 copy 명령을 사용하여 백업 파일을 VM에 복사합니다.

## <a name="backup-to-url-and-restore-from-url"></a>URL에 백업 및 URL에서 복원

로컬 파일에 백업하는 대신 [URL에 백업](/sql/relational-databases/backup-restore/sql-server-backup-to-url)을 사용한 다음 URL에서 VM으로 복원할 수 있습니다. SQL Server 2016은 스트라이프 백업 세트를 지원합니다. 이는 성능을 위해 권장되며 Blob당 크기 제한을 초과하려면 필요합니다. 매우 큰 데이터베이스의 경우 [Windows Import/Export 서비스](../../../import-export/storage-import-export-service.md) 를 사용하는 것이 좋습니다.

## <a name="detach-and-attach-from-a-url"></a>URL에서 분리 및 연결

데이터베이스 및 로그 파일을 분리하고 [Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)로 전송합니다. 그런 다음 Azure VM의 URL에서 데이터베이스를 연결합니다. 물리적 데이터베이스 파일이 Blob Storage에 상주하도록 하려면 이 방법을 사용합니다. 이는 매우 큰 데이터베이스에 유용할 수 있습니다. 수동 메서드를 사용하여 사용자 데이터베이스를 마이그레이션하려면 다음과 같은 일반적인 단계를 사용합니다.

1. 온-프레미스 데이터베이스 인스턴스에서 데이터베이스 파일을 분리합니다.
2. [AZCopy 명령줄 유틸리티](../../../storage/common/storage-use-azcopy-v10.md)를 사용하여 분리된 데이터베이스를 Azure Blob 스토리지에 복사
3. Azure URL의 데이터베이스 파일을 Azure VM에서 SQL Server 인스턴스로 연결합니다.

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>VM으로 변환하고, URL에 업로드하고, 새 VM으로 배포

이 방법을 사용하여 온-프레미스 SQL Server 인스턴스의 모든 시스템과 사용자 데이터베이스를 Azure 가상 머신으로 마이그레이션합니다. 수동 메서드를 사용하여 전체 SQL Server 인스턴스를 마이그레이션하려면 다음과 같은 일반적인 단계를 사용합니다.

1. 물리적 또는 가상 머신을 Hyper-V VHD로 변환합니다.
2. [Add-AzureVHD cmdlet](/previous-versions/azure/dn495173(v=azure.100))을 사용하여 VHD 파일을 Azure Storage에 업로드합니다.
3. 업로드된 VHD를 사용하여 새 가상 머신을 배포합니다.

> [!NOTE]
> 전체 애플리케이션을 마이그레이션하려면 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)사용을 고려합니다.

## <a name="ship-a-hard-drive"></a>하드 드라이브 운송

네트워크를 통한 업로드가 매우 비싸거나 실행 불가능한 상황에서 [Windows 가져오기 / 내보내기 서비스 방법](../../../import-export/storage-import-export-service.md)을 사용하여 대량의 파일 데이터를 Azure Blob 스토리지로 전송합니다. 이 서비스를 사용하여 해당 데이터가 포함된 하나 이상의 하드 드라이브를 Azure 데이터 센터로 보내서 데이터를 스토리지 계정으로 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)를 참조하세요.

> [!TIP]
> SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.yml)을 참조하세요.

Azure SQL Server Virtual Machine을 만드는 방법에 대한 지침은 CSS SQL Server 엔지니어 블로그에서 [캡처한 이미지에서 Azure SQL 가상 머신을 '복제'하는 팁과 트릭](/archive/blogs/psssql/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images)을 참조하세요.