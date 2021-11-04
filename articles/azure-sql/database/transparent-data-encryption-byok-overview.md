---
title: 고객 관리형 TDE(투명한 데이터 암호화)
description: SQL Database 및 Azure Synapse Analytics에서 Azure Key Vault를 통해 BYOK(Bring Your Own Key)를 TDE(투명한 데이터 암호화)에 지원합니다. BYOK를 통한 TDE 개요, 이점, 작동 방법, 고려 사항 및 권장 사항을 설명합니다.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 06/23/2021
ms.openlocfilehash: 290065bb7410c42695cf2b0062cdd11cb02c9580
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065535"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>고객 관리 키를 사용한 Azure SQL 투명한 데이터 암호화
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

고객 관리형 키를 사용한 Azure SQL [TDE(투명한 데이터 암호화)](/sql/relational-databases/security/encryption/transparent-data-encryption)를 사용하면 미사용 데이터 보호를 위한 BYOK(Bring Your Own Key) 시나리오가 가능하며 조직에서 키와 데이터 관리 시 업무 분리를 구현할 수 있습니다. 고객 관리형 투명한 데이터 암호화를 사용하면 고객은 키 수명 주기 관리(키 만들기, 업로드, 회전, 삭제), 키 사용 권한 및 키 관련 작업 감사를 완전히 제어하며 수행할 수 있습니다.

이 시나리오에서 TDE 보호기라는 DEK(데이터베이스 암호화 키)를 암호화하는 데 사용되는 키는 클라우드 기반 외부 키 관리 시스템인 고객 소유 및 고객 관리형 [AKV(Azure Key Vault)](../../key-vault/general/security-features.md)에 저장된 고객 관리형 비대칭 키입니다. Key Vault는 가용성이 우수하며 RSA 암호화 키에 대해 안전하고 확장성 있는 스토리지입니다. FIPS 140-2 수준 2 유효성 검사를 통과한 HSM(하드웨어 보안 모듈)을 선택 사항으로 지원합니다. 저장된 키에 직접 액세스할 수 없지만, 권한 있는 엔터티에 대한 키를 사용하여 암호화/암호 해독 서비스를 제공합니다. 키는 키 자격 증명 모음에서 생성되거나, 가져오거나, [온-프레미스 HSM 디바이스에서 키 자격 증명 모음으로 전송](../../key-vault/keys/hsm-protected-keys.md)될 수 있습니다.

Azure SQL Database 및 Azure Synapse Analytics의 경우 TDE 보호기는 서버 수준에서 설정되며 해당 서버와 연결된 모든 암호화된 데이터베이스에서 상속됩니다. Azure SQL Managed Instance의 경우 TDE 보호기는 인스턴스 수준에서 설정되며 해당 인스턴스의 모든 암호화된 데이터베이스에 의해 상속됩니다. *서버* 라는 용어는 달리 명시되지 않는 한 이 문서 전체에서 SQL Database 및 Azure Synapse의 서버와 SQL Managed Instance의 관리되는 인스턴스를 모두 나타냅니다.

> [!NOTE]
> 이 문서는 Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics(전용 SQL 풀(이전의 SQL DW))에 적용됩니다. Synapse 작업 영역 내의 전용 SQL 풀을 위한 투명한 데이터 암호화에 관한 설명서는 [Azure Synapse Analytics 암호화](../../synapse-analytics/security/workspaces-encryption.md)를 참조하세요.

> [!IMPORTANT]
> 고객 관리형 TDE 사용을 시작하려는 서비스 관리형 TDE를 사용 중인 사용자의 경우 전환하는 동안 데이터가 암호화된 상태로 유지되며 가동 중지 시간이 발생하지 않고 데이터베이스 파일도 다시 암호화되지 않습니다. 서비스 관리형 키에서 고객 관리형 키로 전환하는 경우에만 빠른 온라인 작업인 DEK를 다시 암호화해야 합니다.

> [!NOTE]
> <a id="doubleencryption"></a> Azure SQL 고객에게 미사용 데이터의 두 계층 암호화를 제공하기 위해 플랫폼 관리형 키를 사용한 인프라 암호화(AES-256 암호화 알고리즘 사용)가 출시되고 있습니다. 이는 이미 사용 가능한 고객 관리형 키가 있는 TDE와 함께 저장 시 암호화의 추가 계층을 제공합니다. Azure SQL Database 및 Managed Instance의 경우 인프라 암호화가 설정되면 master 데이터베이스 및 기타 시스템 데이터베이스를 비롯한 모든 데이터베이스가 암호화됩니다. 이때 고객은 이 기능에 대한 액세스를 요청해야 합니다. 이 기능에 관심이 있는 경우 AzureSQLDoubleEncryptionAtRest@service.microsoft.com에 문의하세요.


## <a name="benefits-of-the-customer-managed-tde"></a>고객 관리형 TDE의 이점

고객 관리형 TDE는 고객에게 다음과 같은 이점을 제공합니다.

- TDE 보호기의 사용 및 관리를 완벽하게 세부적으로 제어,

- TDE 보호기 사용의 투명성,

- 조직 내 키 및 데이터 관리에서 업무 분리를 구현할 수 있음,

- Key Vault 관리자가 키 액세스 권한을 해지하여 암호화된 데이터베이스에 액세스할 수 없도록 설정할 수 있음,

- AKV의 키 중앙 관리,

- AKV는 Microsoft가 암호화 키를 보거나 추출할 수 없도록 디자인되었으므로 최종 고객의 신뢰가 향상됨,

## <a name="how-customer-managed-tde-works"></a>고객 관리형 TDE의 작동 방법

![고객 관리형 TDE의 설정 및 기능](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

DEK의 암호화를 위해 AKV에 저장된 TDE 보호기를 사용할 수 있으려면 키 자격 증명 모음 관리자는 고유 Azure AD(Azure Active Directory) ID를 사용하여 서버에 대한 다음 액세스 권한을 부여해야 합니다.

- **get** - Key Vault에서 키의 퍼블릭 파트 및 속성 검색

- **wrapKey** - DEK를 보호(암호화)할 수 있음

- **unwrapKey** - DEK를 보호 해제(암호 해독)할 수 있음

키 자격 증명 모음 관리자는 나중에 감사될 수 있도록 [키 자격 증명 모음 감사 이벤트의 로깅을 사용하도록 설정](../../azure-monitor/insights/key-vault-insights-overview.md)할 수도 있습니다.

서버가 AKV에서 TDE 보호기를 사용하도록 구성된 경우 서버는 암호화를 위해 각 TDE 지원 데이터베이스의 DEK를 키 자격 증명 모음으로 보냅니다. 키 자격 증명 모음은 암호화된 DEK를 반환하며 이 DEK는 이후 사용자 데이터베이스에 저장됩니다.

필요한 경우 서버는 암호 해독을 위해 보호된 DEK를 키 자격 증명 모음으로 보냅니다.

로깅이 사용되는 경우 감사자는 Azure Monitor를 사용하여 키 자격 증명 모음 AuditEvent 로그를 검토할 수 있습니다.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>고객 관리형 TDE를 구성하기 위한 요구 사항

### <a name="requirements-for-configuring-akv"></a>AKV 구성을 위한 요구 사항

- 키 자격 증명 모음 및 SQL Database/관리되는 인스턴스는 동일한 Azure Active Directory 테넌트에 속해야 합니다. 교차 테넌트 키 자격 증명 모음 및 서버 상호 작용은 지원되지 않습니다. 나중에 리소스를 이동하려면 AKV를 사용하는 TDE를 다시 구성해야 합니다. [리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)에 관해 자세히 알아보세요.

- 실수로 인한 키(또는 키 자격 증명 모음) 삭제로 인한 데이터 손실을 방지하려면 키 자격 증명 모음에서 [일시 삭제](../../key-vault/general/soft-delete-overview.md) 및 [제거 보호](../../key-vault/general/soft-delete-overview.md#purge-protection) 기능을 사용하도록 설정해야 합니다. 
    - 일시 삭제된 리소스는 고객이 복구하거나 제거하지 않는 한 90일 동안 유지됩니다. *복구* 및 *제거* 작업에는 키 자격 증명 모음 액세스 정책과 연결된 고유 권한이 있습니다. 일시 삭제 기능은 Azure Portal, [PowerShell](../../key-vault/general/key-vault-recovery.md?tabs=azure-powershell) 또는 [Azure CLI](../../key-vault/general/key-vault-recovery.md?tabs=azure-cli)사용하여 사용하도록 설정할 수 있습니다.
    - [Azure CLI](../../key-vault/general/key-vault-recovery.md?tabs=azure-cli) 또는 [PowerShell을](../../key-vault/general/key-vault-recovery.md?tabs=azure-powershell)사용하여 제거 보호를 켰을 수 있습니다. 제거 보호를 사용하도록 설정하면 보존 기간이 지났을 때까지 삭제된 상태의 자격 증명 모음 또는 개체를 제거할 수 없습니다. 기본 보존 기간은 90일이지만 Azure Portal 통해 7~90일로 구성할 수 있습니다.   

> [!IMPORTANT]
> 고객 관리형 TDE로 구성되는 서버와 고객 관리형 TDE를 사용하는 기존 서버에 대해 키 자격 증명 모음에서 일시 삭제 및 제거 보호를 모두 사용하도록 설정해야 합니다. 고객 관리형 TDE를 사용하는 서버의 경우 연결된 키 자격 증명 모음에서 일시 삭제 및 제거 보호를 사용하지 않는 경우 데이터베이스 만들기, 지역 복제 설정, 데이터베이스 복원, TDE 보호기 업데이트 등의 작업을 수행하면 다음 오류 메시지와 함께 *실패합니다. "제공된 Key Vault URI가 잘못되었습니다. 키 자격 증명 모음이 일시 삭제 및 제거 보호로 구성되었는지 확인하세요."*

- Azure Active Directory ID를 사용하여 키 자격 증명 *모음(get*, *wrapKey*, *unwrapKey)에* 대한 액세스 권한을 서버 또는 관리되는 인스턴스에 부여합니다. Azure Portal 사용하는 경우 서버를 만들 때 Azure AD ID가 자동으로 만들어집니다. PowerShell 또는 Azure CLI 사용하는 경우 Azure AD ID를 명시적으로 만들어야 하며 확인해야 합니다. PowerShell을 사용하는 경우 자세한 단계별 지침은 [BYOK 기반 TDE 구성](transparent-data-encryption-byok-configure.md) 및 [SQL Managed Instance에 대해 BYOK 기반 TDE 구성](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md)을 참조하세요.
    - 키 자격 증명 모음(액세스 정책 또는 Azure RBAC)의 권한 모델에 따라 키 자격 증명 모음에 대한 액세스 정책을 만들거나 암호화 서비스 [암호화 사용자](/azure/key-vault/general/rbac-guide#azure-built-in-roles-for-key-vault-data-plane-operations)Key Vault 역할로 새 Azure RBAC 역할 할당을 만들어 키 자격 증명 모음 액세스 권한을 부여할 수 있습니다.

- AKV와 함께 방화벽을 사용하는 경우 ‘신뢰할 수 있는 Microsoft 서비스가 방화벽을 우회하도록 허용’ 옵션을 사용하도록 설정해야 합니다.

### <a name="requirements-for-configuring-tde-protector"></a>TDE 보호기를 구성하기 위한 요구 사항

- TDE 보호기는 비대칭, RSA 또는 RSA HSM 키여야 합니다. 지원되는 키 길이는 2048비트 및 3072비트입니다.

- 키 활성화 날짜(설정된 경우)는 과거의 날짜와 시간이어야 합니다. 만료 날짜(설정된 경우)는 미래 날짜 및 시간이어야 합니다.

- 키가 *사용* 상태여야 합니다.

- 기존 키를 키 자격 증명 모음으로 가져올 때, 지원되는 파일 형식(`.pfx`, `.byok` 또는 `.backup`)으로 제공해야 합니다.

> [!NOTE]
> 이제 Azure SQL은 관리형 HSM에 저장된 RSA 키를 TDE 보호기로 사용하도록 지원합니다. 이 기능은 **퍼블릭 미리 보기** 상태입니다. Azure Key Vault 관리형 HSM은 FIPS 140-2 수준 3 유효성이 검사된 HSM을 사용하여 클라우드 애플리케이션용 암호화 키를 보호할 수 있는 완전 관리형 고가용 단일 테넌트 표준 규격 클라우드 서비스입니다. [관리형 HSM](../../key-vault/managed-hsm/index.yml)에 대해 자세히 알아봅니다.


## <a name="recommendations-when-configuring-customer-managed-tde"></a>고객 관리형 TDE 구성 시 권장 사항

### <a name="recommendations-when-configuring-akv"></a>AKV 구성 시 권장 사항

- 단일 구독에서 전체로서 최대 500개 범용 또는 200개 중요 비즈니스용 데이터베이스를 키 자격 증명 모음과 연결하여 서버가 키 자격 증명 모음의 TDE 보호기에 액세스할 때 고가용성을 보장합니다. 해당 수치는 환경에 따라 달라지며 [키 자격 증명 모음 서비스 한도](../../key-vault/general/service-limits.md)에 설명되어 있습니다. 해당 서버에 있는 데이터베이스 수만큼 키 작업이 자격 증명 모음에 대해 트리거되므로 여기서는 서버 장애 조치(failover) 후에 문제를 방지하기 위해서만 사용됩니다.

- 이 중요한 리소스를 삭제할 수 있는 사용자를 제어하고 실수로 인한 삭제나 무단 삭제를 방지할 수 있도록 키 자격 증명 모음에 리소스 잠금을 설정합니다. [리소스 잠금](../../azure-resource-manager/management/lock-resources.md)을 자세히 알아봅니다.

- 모든 암호화 키에 대한 감사 및 보고 사용: Key Vault는 다른 보안 정보 및 이벤트 관리 도구에 쉽게 삽입되는 로그를 제공합니다. Operations Management Suite [Log Analytics](../../azure-monitor/insights/key-vault-insights-overview.md)는 이미 통합된 서비스의 한 예입니다.

- 서로 다른 지역에 있는 두 개의 키 자격 증명 모음에 각 서버를 연결하고 동일한 키 자료를 보관하여 암호화된 데이터베이스의 고가용성을 보장합니다. TDE 보호기와 동일한 지역에 있는 키 자격 증명 모음의 키만 표시합니다. 동일한 지역의 키 자격 증명 모음에 영향을 주는 작동 중단이 발생하면 시스템은 원격 지역의 키 자격 증명 모음으로 자동으로 전환됩니다.

> [!NOTE]
> 고객 관리형 TDE를 유연하게 구성할 수 있도록 한 지역의 Azure SQL Database 서버 및 Managed Instance 이제 다른 지역의 키 자격 증명 모음에 연결할 수 있습니다. 서버와 키 자격 증명 모음은 동일한 지역에 공동 배치할 필요가 없습니다. 

### <a name="recommendations-when-configuring-tde-protector"></a>TDE 보호기 구성 시 권장 사항

- TDE 보호기의 복사본을 안전한 장소에 보관하거나 에스크로 서비스로 에스크로합니다.

- 키 자격 증명 모음에서 키를 생성하는 경우 AKV에서 키를 처음으로 사용하기 전에 키 백업을 만듭니다. 백업은 Azure Key Vault로만 복원할 수 있습니다. [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey) 명령을 자세히 알아봅니다.

- 키의 내용(예: 키 특성, 태그, ACL)이 변경될 때마다 새 백업을 만듭니다.

- 키를 회전할 때 키 자격 증명 모음에서 **이전 버전의 키를 유지** 합니다. 그러면 이전 데이터베이스 백업을 복원할 수 있습니다. 데이터베이스의 TDE 보호기가 변경되면 데이터베이스의 이전 백업이 최신 TDE 보호기를 사용하도록 **업데이트되지 않습니다**. 복원 시 각 백업에는 만들 때 암호화한 TDE 보호기가 필요합니다. 키 회전은 [PowerShell을 사용하여 투명한 데이터 암호화 방지 프로그램 회전](transparent-data-encryption-byok-key-rotation.md)에 나와 있는 지침에 따라 수행할 수 있습니다.

- 서비스 관리형 키로 전환한 후에도 이전에 사용한 모든 키를 AKV에서 유지합니다. 이렇게 하면 AKV에 저장된 TDE 보호기를 사용하여 데이터베이스 백업을 복원할 수 있습니다.  Azure Key Vault에서 만든 TDE 보호기는 서비스 관리형 키를 사용하여 나머지 저장된 백업이 모두 만들어질 때까지 유지되어야 합니다. [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey)를 사용하여 이러한 키의 복구 가능한 백업 복사본을 만듭니다.

- 데이터가 손실될 위험 없이 보안 인시던트 중에 잠재적으로 손상될 수 있는 키를 제거하려면 [잠재적으로 손상될 수 있는 키 제거](transparent-data-encryption-byok-remove-tde-protector.md)의 단계를 따릅니다.

## <a name="inaccessible-tde-protector"></a>액세스할 수 없는 TDE 보호기

투명한 데이터 암호화가 고객 관리형 키를 사용하도록 구성된 경우 데이터베이스를 온라인 상태로 유지하려면 TDE 보호기에 지속적인 액세스 권한이 필요합니다. 서버가 AKV의 고객 관리형 TDE 보호기에 대한 액세스 권한을 잃게 되면 최대 10분 내에 데이터베이스는 먼저 해당하는 오류 메시지와 함께 모든 연결을 거부하고 해당 상태를 ‘액세스할 수 없음’으로 변경합니다. 액세스할 수 없음 상태의 데이터베이스에서 허용되는 유일한 작업은 삭제하는 것입니다.

> [!NOTE]
> 일시적인 네트워킹 중단으로 인해 데이터베이스에 액세스할 수 없는 경우에는 작업이 필요하지 않으며 데이터베이스가 자동으로 다시 온라인 상태로 전환됩니다.

키에 대한 액세스 권한을 복원한 후 데이터베이스를 다시 온라인 상태로 전환하려면 추가 시간과 단계가 필요하며, 이는 키에 액세스하지 않고 경과된 시간과 데이터베이스에 있는 데이터의 크기에 따라 달라질 수 있습니다.

- 키 액세스가 8시간 이내에 복원되면 데이터베이스는 다음 시간 내에 자동 복구됩니다.

- 키 액세스 복원이 8시간을 초과하면 자동 복구가 가능하지 않으며, 데이터베이스를 복구하려면 포털에서 추가 단계가 필요하고 데이터베이스 크기에 따라 상당한 시간이 걸릴 수 있습니다. 데이터베이스가 다시 온라인 상태가 되면 [장애 조치(failover) 그룹](auto-failover-group-overview.md) 구성, 특정 시점 복원 기록 및 태그와 같은 이전에 구성된 서버 수준 설정이 **손실됩니다**. 따라서 8시간 내에 기본 키 액세스 문제를 식별하고 해결할 수 있는 알림 시스템을 구현하는 것이 좋습니다.

액세스할 수 없는 데이터베이스를 다시 온라인으로 전환하기 위해 포털에 필요한 추가 단계를 아래에서 확인합니다.

![TDE BYOK 데이터베이스에 액세스할 수 없음](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>실수로 인한 TDE 보호기 액세스 해지

키 자격 증명 모음에 대한 충분한 액세스 권한을 가진 사용자가 다음과 같은 실수로 인해 키에 대한 서버 액세스 권한을 사용하지 않도록 설정하는 문제가 발생할 수 있습니다.

- 서버에서 키 자격 증명 모음의 *get*, *wrapKey*, *unwrapKey* 권한을 해지합니다.

- 키 삭제

- 키 자격 증명 모음 삭제

- 키 자격 증명 모음의 방화벽 규칙 변경

- Azure Active Directory에서 서버의 관리 ID 삭제

[데이터베이스가 액세스할 수 없게 되는 일반적인 원인](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current&preserve-view=true#common-errors-causing-databases-to-become-inaccessible)을 자세히 알아봅니다.

## <a name="monitoring-of-the-customer-managed-tde"></a>고객 관리형 TDE 모니터링

데이터베이스 상태를 모니터링하고 TDE 보호기 액세스 권한 손실을 경고하기 위해 다음 Azure 기능을 구성합니다.

- [Azure Resource Health](../../service-health/resource-health-overview.md). TDE 보호기에 대한 액세스 권한이 손실된 액세스할 수 없는 데이터베이스는 데이터베이스에 대한 첫 번째 연결이 거부된 후 “액세스할 수 없음”으로 표시됩니다.
- [활동 로그](../../service-health/alerts-activity-log-service-notifications-portal.md) 고객 관리형 키 자격 증명 모음의 TDE 보호기에 대한 액세스가 실패하면 항목이 활동 로그에 추가됩니다.  해당 이벤트에 관한 경고를 만들면 최대한 빠르게 액세스를 복구할 수 있습니다.
- [작업 그룹](../../azure-monitor/alerts/action-groups.md)은 이메일/SMS/푸시/보이스, Logic App, 웹후크, ITSM 또는 자동화 런북과 같은 기본 설정에 따라 알림 및 경고를 보내도록 정의할 수 있습니다.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>고객 관리형 TDE를 사용한 데이터베이스 백업 및 복원

Key Vault의 키를 사용하여 데이터베이스가 TDE를 통해 암호화되면 새로 생성된 모든 백업도 동일한 TDE 보호기로 암호화됩니다. TDE 보호기가 변경되면 데이터베이스의 이전 백업이 최신 TDE 보호기를 사용하도록 **업데이트되지 않습니다**.

Key Vault에서 TDE 보호기로 암호화된 백업을 복원하려면 대상 서버에서 키 자료를 사용할 수 있는지 확인합니다. 따라서 데이터베이스 백업을 복원할 수 있도록 이전 버전의 TDE 보호기를 모두 키 자격 증명 모음에서 유지하는 것이 좋습니다.

> [!IMPORTANT]
> 언제든지 서버에 둘 이상의 TDE 보호기를 설정할 수 없습니다. Azure Portal 블레이드에서 “키를 기본 TDE 보호기로 설정”이 표시된 키입니다. 그러나 여러 추가 키를 TDE 보호기로 표시하지 않고 서버에 연결할 수 있습니다. 해당 키는 DEK를 보호하는 데 사용되지 않지만, 백업 파일이 해당 지문을 사용한 키로 암호화된 경우 백업에서 복원하는 동안 해당 키를 사용할 수 있습니다.

백업 복원에 필요한 키를 대상 서버에서 더 이상 사용할 수 없는 경우 복원 시도 시 다음 오류 메시지가 반환됩니다. "대상 서버 `<Servername>`는 \<Timestamp #1> 및 \<Timestamp #2> 사이에 작성된 모든 AKV URI에 대한 액세스 권한이 없습니다. 모든 AKV URI를 복원한 후 작업을 다시 시도하세요."

이를 완화하려면 대상 서버에 대해 [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet을 실행하거나 대상 관리 인스턴스에 대해 [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey)를 실행하여 사용 가능한 키 목록을 반환하고 누락된 키를 식별합니다. 모든 백업을 복원할 수 있도록 하려면 복원 대상 서버에서 필요한 모든 키에 액세스할 수 있는지 확인합니다. 해당 키는 TDE 보호기로 표시할 필요가 없습니다.

SQL Database의 백업 복구에 대한 자세한 내용은 [SQL Database에서 데이터베이스 복구](recovery-using-backups.md)를 참조하세요. Azure Synapse Analytics의 전용 SQL 풀에 대한 백업 복구에 대한 자세한 내용은 [전용 SQL 풀 복구](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md)를 참조하세요. SQL Managed Instance를 사용한 SQL Server의 기본 백업/복원은 [빠른 시작: SQL Managed Instance로 데이터베이스 복원](../managed-instance/restore-sample-database-quickstart.md)을 참조하세요.

로그 파일에 대한 추가 고려 사항: 백업된 로그 파일은 회전되었고 현재 데이터베이스가 새 TDE 보호기를 사용하더라도 원래 TDE 보호기로 암호화된 상태로 유지됩니다.  복원할 때 데이터베이스를 복원하려면 두 키가 모두 필요합니다.  로그 파일에서 이 Azure Key Vault에 저장된 TDE 보호기를 사용하는 동안 데이터베이스가 서비스 관리형 TDE를 사용하도록 변경된 경우에도 복원할 때 이 키가 필요합니다.

## <a name="high-availability-with-customer-managed-tde"></a>고객 관리형 TDE의 고가용성

서버의 구성된 지역 복제가 없는 경우에도 동일한 키 자료를 사용하여 서로 다른 두 지역에서 서로 다른 두 개의 키 자격 증명 모음을 사용하도록 서버를 구성하는 것이 좋습니다. 다른 지역의 보조 키 자격 증명 모음에 있는 키는 TDE 보호기로 표시되지 않아야 하며 허용되지도 않습니다. 기본 키 자격 증명 모음에 영향을 주는 중단이 발생하는 경우에만 시스템은 보조 키 자격 증명 모음에 있는 동일한 지문이 있는 다른 연결된 키로 자동으로 전환됩니다(있는 경우). 단, 해지된 액세스 권한으로 인해 또는 키나 키 자격 증명 모음이 삭제되어 TDE 보호기에 액세스할 수 없는 경우에는 전환이 발생하지 않습니다. 이는 고객이 의도적으로 서버가 키에 액세스하지 못하도록 제한하는 경우를 나타낼 수 있습니다. 다른 지역에 있는 두 개의 키 자격 증명 모음에 동일한 키 자료를 제공하려면 키 자격 증명 모음 외부에 키를 생성하고 두 키 자격 증명 모음으로 가져오는 방식으로 수행할 수 있습니다. 

또는 서버와 동일한 지역에 배치된 기본 키 자격 증명 모음을 사용하여 키를 생성하고 다른 Azure 지역의 키 자격 증명 모음에 키를 복제하여 수행할 수 있습니다. [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/Backup-AzKeyVaultKey) cmdlet을 사용하여 기본 키 자격 증명 모음에서 암호화된 형식의 키를 검색한 다음, [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet을 사용하고 두 번째 지역의 키 자격 증명 모음을 지정하여 키를 복제합니다. 또는 Azure Portal을 사용하여 키를 백업하고 복원합니다. 키 백업/복원 작업은 동일한 Azure 구독 및 [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/) 내의 주요 자격 증명 모음 간에만 허용됩니다.  

![단일 서버 HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR 및 고객 관리형 TDE

[활성 지역 복제](active-geo-replication-overview.md) 및 [장애 조치(failover) 그룹](auto-failover-group-overview.md) 시나리오에서 관련된 각 서버에는 동일한 Azure 지역의 서버와 같은 위치에 있어야 하는 별도의 키 자격 증명 모음이 필요합니다. 지역에 중단이 발생하여 주 데이터베이스에 액세스할 수 없게 되고 장애 조치(failover)가 트리거되는 경우 지역 보조 데이터베이스가 동기화되고 로컬 키 자격 증명 모음의 동일한 키를 사용하여 인수될 수 있도록 고객은 전체 키 자격 증명 모음에서 키 자료를 일관되게 유지해야 합니다. 최대 4개의 보조 데이터베이스를 구성할 수 있으며 체인(보조 데이터베이스의 보조 데이터베이스)은 지원되지 않습니다.

불완전한 키 자료로 인해 설정하는 동안 또는 지역 복제 중에 발생하는 문제를 방지하려면 고객 관리형 TDE를 구성할 때 다음 규칙을 따라야 합니다.

- 관련된 모든 키 자격 증명 모음에는 해당 서버의 동일한 속성 및 동일한 액세스 권한이 있어야 합니다.

- 관련된 모든 키 자격 증명 모음은 동일한 키 자료를 포함해야 합니다. 현재 TDE 보호기뿐만 아니라 백업 파일에 사용될 수 있는 모든 이전 TDE 보호기에 적용됩니다.

- TDE 보호기의 초기 설정 및 회전은 둘 다 보조 데이터베이스에서 수행한 후 주 데이터베이스에서 수행해야 합니다.

![장애 조치 그룹 및 Geo-DR](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

장애 조치(failover)를 테스트하려면 [활성 지역 복제 개요](active-geo-replication-overview.md)의 단계를 따릅니다. SQL Database가 두 주요 자격 증명 모음에 대한 액세스 권한을 유지했는지 확인하기 위해 장애 조치(failover) 테스트를 정기적으로 수행해야 합니다.

**이제 한 지역의 Azure SQL Database 서버와 Managed Instance를 다른 지역의 주요 자격 증명 모음에 연결할 수 있습니다.** 서버와 주요 자격 증명 모음은 동일한 지역에 배치 하지 않아도 됩니다. 이를 통해 간단 하 게 주 서버와 보조 서버를 동일한 키 자격 증명 모음 (모든 지역)에 연결할 수 있습니다. 이렇게 하면 별도의 키 자격 증명 모음이 두 서버에 모두 사용 되는 경우 키 자료가 동기화 되지 않을 수 있는 시나리오를 방지할 수 있습니다. Azure Key Vault에는 서비스 또는 지역 장애 시 키와 키 자격 증명 모음을 계속 사용할 수 있도록 하는 여러 가지 중복성 계층이 있습니다. [Azure Key Vault 가용성 및 중복성](../../key-vault/general/disaster-recovery-guidance.md)

## <a name="next-steps"></a>다음 단계

고객 관리형 TDE를 사용하여 일반적인 작업에 대해 다음 PowerShell 샘플 스크립트를 확인할 수도 있습니다.

- [PowerShell을 사용하여 SQL Database의 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-key-rotation.md)

- [PowerShell을 사용하여 SQL Database의 TDE(투명한 데이터 암호화) 보호기 제거](transparent-data-encryption-byok-remove-tde-protector.md)

- [SQL Managed Instance에서 PowerShell을 사용하여 자체 키로 투명한 데이터 암호화 관리](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)
