---
title: SQL Server 2008 & 2008 R2에 대한 확장 지원
description: SQL Server 인스턴스를 Azure로 마이그레이션하거나 확장된 지원을 구매하여 온-프레미스 인스턴스를 유지함으로써 SQL Server 2008 및 SQL Server 2008 R2에 대한 지원을 확장합니다.
services: virtual-machines-windows
documentationcenter: ''
author: bluefooted
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: pamela
ms.reviewer: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: ea249347c2fca760e2c835aa28b7b4888e684276
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130160897"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Azure를 사용하여 SQL Server 2008 및 SQL Server 2008 R2 지원 확장
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 2008 및 SQL Server 2008 R2는 모두 [지원 종료(EOS) 수명 주기](https://www.microsoft.com/sql-server/sql-server-2008)에 도달했습니다. 많은 고객이 여전히 두 버전을 모두 사용하고 있으므로 지속적인 지원을 위해 몇 가지 옵션을 제공하고 있습니다. 온-프레미스 SQL Server 인스턴스를 Azure VM(가상 머신)으로 마이그레이션하거나, Azure SQL Database로 마이그레이션하거나, 온-프레미스를 유지하면서 확장된 보안 업데이트를 구매할 수 있습니다.

관리되는 인스턴스와 달리 Azure VM으로 마이그레이션하면 애플리케이션을 다시 인증할 필요가 없습니다. 또한 온-프레미스를 유지하는 것과 달리 Azure VM으로 마이그레이션하면 확장된 보안 패치를 무료로 받을 수 있습니다.

이 문서의 나머지 부분에서는 SQL Server 인스턴스를 Azure VM으로 마이그레이션하기 위한 고려 사항을 제공합니다.

지원 종료 옵션에 대한 자세한 내용은 [지원 종료 옵션](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)을 참조하세요.

## <a name="provisioning"></a>프로비전

Azure Marketplace에서 사용할 수 있는 종량제의 **Windows Server 2008 R2의 SQL Server 2008 R2** 이미지가 있습니다.

SQL Server 2008을 사용하고 있는 고객은 SQL Server 2008 R2를 자체 설치하거나 업그레이드해야 합니다. 마찬가지로, Windows Server 2008의 고객은 사용자 지정 VHD에서 VM을 배포하거나 Windows Server 2008 R2로 업그레이드해야 합니다.

Azure Marketplace를 통해 배포된 이미지는 SQL IaaS 확장이 미리 설치된 상태로 제공됩니다. SQL IaaS 확장은 유연한 라이선스 및 자동화된 패치를 위한 요구 사항입니다. 자체 설치된 VM을 배포하는 고객은 SQL IaaS 확장을 수동으로 설치해야 합니다. SQL IaaS 확장은 Windows Server 2008에서 지원되지 않습니다.

> [!NOTE]
> SQL Server의 **만들기** 및 **관리** 블레이드가 Azure Portal에서 SQL Server 2008 R2 이미지와 함께 작동하더라도 다음 기능은 _지원되지 않습니다_. 자동 백업, Azure Key Vault 통합, R 서비스 및 스토리지 구성.

## <a name="licensing"></a>라이선스
종량제 SQL Server 2008 R2 배포가 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 변환될 수 있습니다.

SA(Software Assurance) 기반 라이선스를 종량제로 변환하려면 고객은 [SQL IaaS 에이전트 확장](sql-agent-extension-manually-register-single-vm.md)에 등록해야 합니다. 등록 후에는 Azure 하이브리드 혜택과 종량제 간에 SQL 라이선스 유형을 교환할 수 있습니다.

Azure VM에 자체 설치된 SQL Server 2008 또는 SQL Server 2008 R2 인스턴스는 SQL IaaS 에이전트 확장에 등록하고 해당 라이선스 유형을 종량제로 전환할 수 있습니다.

## <a name="migration"></a>마이그레이션
수동 백업/복원 방법으로 EOS SQL Server 인스턴스를 Azure VM으로 마이그레이션할 수 있습니다. 이는 온-프레미스에서 Azure VM으로의 가장 일반적인 마이그레이션 방법입니다.

### <a name="azure-site-recovery"></a>Azure Site Recovery

대량 마이그레이션의 경우 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 서비스를 사용하는 것이 좋습니다. Azure Site Recovery를 통해 고객은 온-프레미스에서 Azure VM으로 SQL Server를 포함하여 전체 VM을 복제할 수 있습니다.

SQL Server는 복구를 보장하기 위해 앱 일치 Azure Site Recovery 스냅샷이 필요합니다. Azure Site Recovery는 최소 1시간 간격으로 앱 일치 스냅샷을 지원합니다. Azure Site Recovery 마이그레이션으로 SQL Server에 사용할 수 있는 최소 RPO(복구 지점 목표)는 1시간입니다. RTO(복구 시간 목표)는 2시간에 SQL Server 복구 시간을 더한 값입니다.

### <a name="database-migration-service"></a>Database Migration Service

SQL Server를 2012 이상 버전으로 업그레이드하여 온-프레미스에서 Azure VM으로 마이그레이션하는 경우 고객이 사용할 수 있는 옵션은 [Azure Database Migration Service](../../../dms/dms-overview.md)입니다.

## <a name="disaster-recovery"></a>재해 복구

Azure VM에서 EOS SQL Server에 대한 재해 복구 솔루션은 다음과 같습니다.

- **SQL Server 백업:** Azure Backup을 사용하여 15분 RPO 및 지정 시간 복구로 인해 랜섬웨어, 실수로 인한 삭제 및 손상으로부터 EOS SQL Server 2008 및 2008 R2를 보호할 수 있습니다. 자세한 내용은 [이 문서](../../../backup/sql-support-matrix.md#scenario-support)를 참조하세요.
- **로그 전달**: RTO를 줄이기 위해 연속 복원을 사용하여 다른 영역 또는 Azure 지역에 로그 전달 복제본을 만들 수 있습니다. 로그 전달을 수동으로 구성해야 합니다.
- **Azure Site Recovery**: Azure Site Recovery 복제를 통해 영역 및 지역 간에 VM을 복제할 수 있습니다. 재해 발생 시 복구를 보장하기 위해 SQL Server는 앱 일치 스냅샷이 필요합니다. EOS SQL Server 재해 복구를 위해 Azure Site Recovery는 최소 1시간의 RPO와 2시간(SQL Server 복구 시간 추가)의 RTO를 제공합니다.

## <a name="security-patching"></a>보안 패치

[SQL IaaS 에이전트 확장](sql-agent-extension-manually-register-single-vm.md)에 SQL Server VM이 등록된 후 SQL Server VM에 대한 확장된 보안 업데이트는 Microsoft 업데이트 채널을 통해 제공됩니다. 패치는 수동 또는 자동으로 다운로드할 수 있습니다.

*Automated patching* 이 사용됩니다. Azure에서는 자동화된 패치를 통해 SQL Server와 운영 체제를 자동으로 패치합니다. SQL Server IaaS 확장이 설치된 경우 유지 관리 기간을 요일, 시간 및 기간으로 지정할 수 있습니다. Azure에서 유지 관리 기간에 패치를 수행합니다. 유지 관리 기간 일정에서는 VM 로캘 시간을 사용합니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server의 자동화된 패치](automated-patching.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

SQL Server VM을 Azure로 마이그레이션합니다.

* [Azure VM에서 SQL Server로 SQL Server 데이터베이스 마이그레이션](migrate-to-vm-from-sql-server.md)

Azure Virtual Machines에서 SQL Server 시작:

* [Azure Portal에서 SQL Server VM 만들기](sql-vm-create-portal-quickstart.md)

SQL Server VM에 대한 일반적인 질문에 대한 답변 구하기:

* [Azure Virtual Machines의 SQL Server에 대한 FAQ](frequently-asked-questions-faq.yml)

지원 종료 옵션과 확장된 보안 업데이트에 대해 자세히 알아보세요.

* [지원 종료](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [확장된 보안 업데이트](/sql/sql-server/end-of-support/sql-server-extended-security-updates)