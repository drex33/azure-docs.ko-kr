---
title: Azure Database for PostgreSQL 백업 정보
description: Azure Database for PostgreSQL 백업(미리 보기)에 대한 개요
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: references_regions
ms.openlocfilehash: 6a35d0622512e1f4a0a424d479c1c9e29649e328
ms.sourcegitcommit: 5c1cd21464e8165b16eb8d63ab31ab7b1a8f3675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133206880"
---
# <a name="about-azure-database-for-postgresql-backup-preview"></a>Azure Database for PostgreSQL 백업 정보(미리 보기)

Azure Backup 및 Azure Database Services는 최대 10년 동안 백업을 유지하는 Azure Database for PostgreSQL 서버에 대한 엔터프라이즈급 백업 솔루션을 빌드하기 위해 함께 제공됩니다. 이 솔루션은 장기 보존 외에도 다음과 같은 기능을 제공합니다.

- 고객이 개별 데이터베이스 수준에서 예약된 백업 및 주문형 백업을 제어합니다.
- 데이터베이스 수준은 PostgreSQL 서버 또는 Blob Storage로 복원됩니다.
- 모든 작업 및 작업에 대한 중앙 모니터링.
- 백업은 별도의 보안 및 장애 도메인에 저장됩니다. 어떤 상황에서든 원본 서버 또는 구독이 손상된 경우 백업은 [백업](./backup-vault-overview.md) 자격 증명 모음(Azure Backup 관리되는 스토리지 계정)에서 안전하게 유지됩니다.
- **pg_dump** 사용하면 복원의 유연성이 향상됩니다. 이렇게 하면 데이터베이스 버전 간에 복원할 수 있습니다. 

이 솔루션은 독립적으로 또는 최대 35일의 보존 기간을 [제공하는 Azure PostgreSQL에서 제공하는 네이티브 백업 솔루션](../postgresql/concepts-backup.md) 외에도 사용할 수 있습니다. 네이티브 솔루션은 최신 백업에서 복구하려는 경우와 같은 작업 복구에 적합합니다. Azure Backup 솔루션은 규정 준수 요구 사항과 보다 세분화되고 유연한 백업/복원에 도움이 됩니다.

## <a name="support-matrix"></a>지원 매트릭스

|지원  |세부 정보  |
|---------|---------|
|지원되는 배포   |  [Azure Database for PostgreSQL - 단일 서버](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|지원되는 Azure 지역 |  미국 동부, 미국 동부 2, 미국 중부, 미국 중남부, 미국 서부, 미국 서부 2, 미국 중서부, 브라질 남부, 캐나다 중부, 북유럽, 서유럽, 영국 남부, 영국 서부, 독일 중서부, 스위스 북부, 스위스 서부, 동아시아, 동남 아시아, 일본 동부, 일본 서부, 한국 중부, 한국 남부, 인도 중부, 오스트레일리아 동부, 오스트레일리아 중부, 오스트레일리아 중부 2,  아랍에미리트 북부  |
|지원되는 Azure PostgreSQL 버전    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>기능 고려 사항 및 제한 사항

- 모든 작업은 Azure Portal만 지원됩니다. 
- 최대 데이터베이스 크기에 권장되는 제한은 400GB입니다.
- 지역 간 백업을 지원하지 않습니다. 따라서 Azure PostgreSQL 서버를 다른 지역의 자격 증명 모음에 백업할 수 없습니다. 마찬가지로 자격 증명 모음과 동일한 지역 내에 있는 서버로만 백업을 복원할 수 있습니다. 그러나 구독 간 백업 및 복원을 지원합니다. 
- 복원하는 동안에는 데이터만 복구됩니다. "역할"은 복원되지 않습니다.
- 미리 보기에서는 테스트 환경에서만 솔루션을 실행하는 것이 좋습니다.

## <a name="backup-process"></a>백업 프로세스

1. 백업 관리자는 백업하려는 Azure PostgreSQL 데이터베이스를 지정할 수 있습니다. 또한 지정된 데이터베이스에 연결하는 데 필요한 자격 증명을 저장하는 Azure Key Vault의 세부 정보를 지정할 수도 있습니다. 이러한 자격 증명은 Azure Key Vault의 데이터베이스 관리자가 안전하게 시드합니다.
1. 그런 다음 백업 서비스는 지정된 PostgreSQL 서버로 [인증하고](#azure-backup-authentication-with-the-postgresql-server) 해당 데이터베이스를 백업할 수 있는 적절한 권한이 있는지 유효성을 검사합니다.
1. Azure Backup 백업 확장이 설치된 VM(작업자 역할)을 스핀업하여 보호된 PostgreSQL 서버와 통신합니다. 이 확장은 코디네이터와 PostgreSQL 플러그 인으로 구성됩니다. 코디네이터는 백업 및 복원과 같은 다양한 작업에 대한 워크플로를 트리거하고 플러그 인은 실제 데이터 흐름을 관리합니다.
1. 예약된 시간에 코디네이터는 플러그 인과 통신하여 **pg_dump(사용자 지정)** 를 사용하여 PostgreSQL 서버에서 백업 데이터를 스트리밍하기 시작합니다.
1. 플러그 인은 백업 자격 증명 모음에 의해 마스킹된 Azure Backup 관리되는 스토리지 계정으로 데이터를 직접 전송하므로 준비 위치가 필요하지 않습니다. 데이터는 Microsoft 관리 키를 사용하여 암호화되며 Azure Backup 서비스를 통해 스토리지 계정에 저장됩니다.

 :::image type="content" source="./media/backup-azure-database-postgresql-overview/backup-process.png" alt-text="백업 프로세스를 보여 주는 다이어그램":::

## <a name="azure-backup-authentication-with-the-postgresql-server"></a>PostgreSQL 서버로 인증 Azure Backup

Azure Backup Azure에서 규정한 엄격한 보안 지침을 따릅니다. 백업할 리소스에 대한 사용 권한은 가정되지 않으며 사용자가 명시적으로 부여해야 합니다. 

### <a name="key-vault-based-authentication-model"></a>키 자격 증명 모음 기반 인증 모델

Azure Backup 서비스는 각 백업을 하는 동안 Azure PostgreSQL에 연결해야 합니다. 데이터베이스에 해당하는 '사용자 이름 + 암호'(또는 연결 문자열)는 이 연결을 만드는 데 사용되지만 이러한 자격 증명은 Azure Backup 함께 저장되지 않습니다. 대신 Azure Key Vault의 데이터베이스 관리자가 이러한 자격 증명을 [비밀로](../key-vault/secrets/about-secrets.md)안전하게 시드해야 합니다. 워크로드 관리자는 자격 증명을 관리하고 회전할 책임이 있습니다. Azure Backup 키 자격 증명 모음에서 가장 최근의 비밀 세부 정보를 호출하여 백업을 받습니다.
 
:::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-based-authentication-model.png" alt-text="워크로드 또는 데이터베이스 흐름을 보여 주는 다이어그램":::

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-backup"></a>Azure PostgreSQL 데이터베이스 백업에 필요한 사용 권한 집합

1. Backup 자격 증명 모음의 MSI에 다음 액세스 권한을 부여합니다.

   - Azure PostgreSQL 서버에 대한 _읽기 권한자_ 액세스.
   - Azure Key Vault에서 비밀 사용자(또는 비밀을 얻거나 나열) 액세스를 _Key Vault._

1. 네트워크 가시선 액세스:

   - Azure PostgreSQL 서버 – **Azure 서비스 플래그에 대한 액세스 허용** 플래그를 **예** 로 설정합니다.
   - 키 자격 증명 모음 – **신뢰할 수 있는 Microsoft 서비스** 플래그를 **예** 로 설정할 수 있습니다.

1. 데이터베이스에 대한 데이터베이스 사용자의 백업 권한

>[!Note]
>원하는 리소스에 대한 '쓰기' 액세스 권한이 있는 경우 한 번의 클릭으로 [백업 흐름 구성](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases) 내에서 이러한 권한을 부여하거나, 필요한 권한이 없는 경우(여러 개인과 관련된 경우) ARM 템플릿을 사용할 수 있습니다. 

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-restore"></a>Azure PostgreSQL 데이터베이스 복원에 필요한 사용 권한 집합

복원 권한은 백업에 필요한 권한과 유사하며 대상 PostgreSQL 서버 및 해당 키 자격 증명 모음에 대한 권한을 부여해야 합니다. 백업 흐름 구성과 달리 이러한 권한을 인라인으로 부여하는 환경은 현재 사용할 수 없습니다. 따라서 [Postgres 서버 및 해당 키](#grant-access-on-the-azure-postgresql-server-and-key-vault-manually)자격 증명 모음 에 대한 액세스 권한을 수동으로 부여해야 합니다.

또한 키 자격 증명 모음에 저장된 자격 증명에 해당하는 데이터베이스 사용자에 데이터베이스에 대한 다음과 같은 복원 권한이 있는지 확인합니다.

- ALTER USER 사용자 이름 CREATEDB;
- 데이터베이스 사용자에게 역할 _azure_pg_admin_ 할당합니다.

### <a name="azure-active-directory-based-authentication-model"></a>Azure Active Directory 기반 인증 모델

이전에 Azure AD(Azure Active Directory)를 기반으로 하는 다른 인증 모델을 시작했습니다. 그러나 이제 새로운 키 자격 증명 모음 기반 인증 모델(위에서 설명한 대로)을 대체 옵션으로 제공하여 구성 프로세스를 용이하게 합니다. 

[이 문서를 다운로드하여](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) 이 인증 모델을 사용하기 위한 자동화된 스크립트 및 관련 지침을 얻을 수 있습니다. 백업 및 복원을 위해 Azure PostgreSQL 서버에 적절한 사용 권한 집합을 부여합니다.

>[!Note]
>모든 새 구성 보호는 새 키 자격 증명 모음 인증 모델에서만 진행됩니다. 그러나 Azure AD 기반 인증을 사용하여 보호를 구성한 모든 기존 백업 인스턴스는 계속 존재하며 정기적인 백업을 수행합니다. 이러한 백업을 복원하려면 Azure AD 기반 인증을 따라야 합니다.

## <a name="grant-access-on-the-azure-postgresql-server-and-key-vault-manually"></a>수동으로 Azure PostgreSQL 서버 및 Key Vault에 대한 액세스 권한 부여

Azure Backup 필요한 모든 액세스 권한을 부여하려면 다음 섹션을 참조하세요.

### <a name="access-permissions-on-the-azure-postgresql-server"></a>Azure PostgreSQL 서버에 대한 액세스 권한

1. Azure PostgreSQL 서버에서 Backup 자격 증명 모음의 MSI **읽기 권한자** 액세스를 설정합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-inline.png" alt-text="Azure PostgreSQL 서버에서 Backup 자격 증명 모음의 MSI 판독기 액세스를 설정하는 옵션을 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-expanded.png":::

1. Azure PostgreSQL 서버의 네트워크 가시선 액세스: 'Azure 서비스에 대한 액세스 허용' 플래그를 '예'로 설정합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-inline.png" alt-text="Azure PostgreSQL 서버에서 네트워크 가시선 액세스를 설정하는 옵션을 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-expanded.png":::

### <a name="access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server"></a>Azure Key Vault에 대한 액세스 권한(PostgreSQL 서버와 연결됨)

1. Azure Key Vault에서 Backup 자격 증명 모음의 MSI **Key Vault 비밀 사용자(또는** 비밀을 **얻거나** **나열)** 액세스를 설정합니다. 권한을 할당하려면 역할 할당 또는 액세스 정책을 사용할 수 있습니다. 도움이 되지 않으므로 두 옵션을 모두 사용하여 권한을 추가할 필요는 없습니다.

   - Azure RBAC(Azure 역할 기반 액세스 제어) 권한 부여를 사용하면(즉, 권한 모델이 Azure 역할 기반 액세스 제어로 설정)

     - 액세스 제어에서 키 자격 증명 모음에 대한 백업 _자격 증명 모음의_ MSI Key Vault 비밀 사용자 액세스 권한을 부여합니다. 해당 역할의 보유자는 비밀을 읽을 수 있습니다.
     - [Azure RBAC 를 사용하여 Azure Key Vault에 액세스할 수 있는 권한을 애플리케이션에 부여합니다.](../key-vault/general/rbac-guide.md?tabs=azure-cli)

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-inline.png" alt-text="비밀 사용자 액세스를 제공하는 옵션을 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-inline.png" alt-text="키 자격 증명 모음에 백업 자격 증명 모음의 MSI Key Vault 비밀 사용자 액세스 권한을 부여하는 옵션을 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-expanded.png":::  

   - 액세스 정책 사용(즉, 권한 모델이 자격 증명 모음 액세스 정책으로 설정)

     - 비밀에 대한 Get 및 List 권한을 설정합니다.
     - Azure Key Vault [액세스 정책 할당에](../key-vault/general/assign-access-policy.md?tabs=azure-portal) 대해 알아보기

     :::image type="content" source="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-inline.png" alt-text="권한 모델을 사용하여 사용 권한을 부여하는 옵션을 보여주는 스크린샷은 자격 증명 모음 액세스 정책 모델로 설정됩니다." lightbox="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-expanded.png":::  
 

1. 키 자격 증명 모음에 대한 네트워크 가시선 액세스: 신뢰할 수 있는 **Microsoft 서비스 허용** 플래그를 **예** 로 설정합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-inline.png" alt-text="키 자격 증명 모음에 대한 네트워크 가시선 액세스에 대해 신뢰할 수 있는 Microsoft 서비스 허용 플래그를 yes로 설정하는 것을 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-expanded.png":::

### <a name="database-users-backup-privileges-on-the-database"></a>데이터베이스에 대한 데이터베이스 사용자의 백업 권한

[PG 관리](#using-the-pg-admin-tool) 도구에서 다음 쿼리를 실행합니다(username을 데이터베이스 사용자 ID로 바꾸기). 

```
DO $do$
DECLARE
sch text;
BEGIN
EXECUTE format('grant connect on database %I to %I', current_database(), 'username');
FOR sch IN select nspname from pg_catalog.pg_namespace
LOOP
EXECUTE format($$ GRANT USAGE ON SCHEMA %I TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL TABLES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON TABLES TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL SEQUENCES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON SEQUENCES TO username $$, sch);
END LOOP;
END;
$do$
```

## <a name="using-the-pg-admin-tool"></a>PG 관리 도구 사용

[PG 관리자 도구가](https://www.pgadmin.org/download/) 없는 경우 다운로드합니다. 이 도구를 통해 Azure PostgreSQL 서버에 연결할 수 있습니다. 또한 이 서버에 데이터베이스 및 새 사용자를 추가할 수 있습니다.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-inline.png" alt-text="PG 관리 도구를 사용하여 Azure PostgreSQL 서버에 연결하는 프로세스를 보여주는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-expanded.png":::

선택한 이름으로 새 서버를 만듭니다. Azure Portal Azure PostgreSQL 리소스 뷰에 표시된 서버 이름과 동일한 호스트 **이름/주소 이름을** 입력합니다.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-inline.png" alt-text="PG 관리 도구를 사용하여 새 서버를 만드는 옵션을 보여주는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-inline.png" alt-text="서버 이름과 동일한 호스트 이름 또는 주소 이름을 입력하는 옵션을 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-expanded.png":::

연결을 통과할 방화벽 규칙에 _현재 클라이언트 ID 주소를_ 추가해야 합니다.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-inline.png" alt-text="현재 클라이언트 ID 주소를 방화벽 규칙에 추가하는 프로세스를 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-expanded.png":::

서버에 새 데이터베이스 및 데이터베이스 사용자를 추가할 수 있습니다. 데이터베이스 사용자의 경우 새 **로그인/그룹 역할**'을 추가합니다. **로그인할 수 있나요?가** **예** 로 설정되어 있는지 확인합니다.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-inline.png" alt-text="서버에 새 데이터베이스 및 데이터베이스 사용자를 추가하는 프로세스를 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-inline.png" alt-text="데이터베이스 사용자에 대한 새 로그인 또는 그룹 역할을 추가하는 프로세스를 보여주는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-inline.png" alt-text="로그인 가능 옵션의 확인이 예로 설정되는 것을 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-expanded.png":::

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL 백업](backup-azure-database-postgresql.md)
