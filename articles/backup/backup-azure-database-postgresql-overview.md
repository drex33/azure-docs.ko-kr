---
title: Azure Database for PostgreSQL 백업 정보
description: Azure Database for PostgreSQL 백업 (미리 보기)에 대 한 개요
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: references_regions
ms.openlocfilehash: 3740d4c7d149181638da93a3a5ad713c2c230f29
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154416"
---
# <a name="about-azure-database-for-postgresql-backup-preview"></a>Azure Database for PostgreSQL 백업 정보 (미리 보기)

Azure Backup 및 Azure Database Services는 최대 10년 동안 백업을 유지하는 Azure Database for PostgreSQL 서버에 대한 엔터프라이즈급 백업 솔루션을 빌드하기 위해 함께 제공됩니다. 장기 보존 외에도 솔루션은 다음과 같은 기능을 제공 합니다.

- 고객이 개별 데이터베이스 수준에서 예약 된 백업 및 요청 시 백업을 제어 했습니다.
- PostgreSQL 서버 또는 모든 blob 저장소로 데이터베이스 수준 복원
- 모든 작업 및 작업에 대한 중앙 모니터링.
- 백업은 별도의 보안 및 장애 도메인에 저장됩니다. 모든 상황에서 원본 서버 또는 구독이 손상 되 면 백업이 [백업 자격 증명 모음](/azure/backup/backup-vault-overview) (Azure Backup 관리 저장소 계정)에서 안전 하 게 유지 됩니다.
- **Pg_dump** 를 사용 하면 복원의 유연성을 높일 수 있습니다. 이렇게 하면 데이터베이스 버전 간 복원에 도움이 됩니다. 

이 솔루션은 독립적으로 사용할 수도 있고 [Azure PostgreSQL에서 제공 하는 기본 백업 솔루션](/azure/postgresql/concepts-backup) 외에도 최대 35 일 동안 보존을 제공 합니다. 네이티브 솔루션은 최신 백업에서 복구하려는 경우와 같은 작업 복구에 적합합니다. Azure Backup 솔루션을 통해 규정 준수 요구와 보다 세분화 된 백업/복원에 도움이 됩니다.

## <a name="support-matrix"></a>지원 매트릭스

|지원  |세부 정보  |
|---------|---------|
|지원되는 배포   |  [Azure Database for PostgreSQL - 단일 서버](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|지원되는 Azure 지역 |  미국 동부, 미국 동부 2, 미국 중부, 미국 중부, 미국 서 부, 미국 서 부 2, 미국 서 부, 미국 서 부, 미국 서 부, 미국 서 부, 유럽 서부, 영국 남부, 영국 서부, 독일 중서부, 스위스 북부, 스위스 서부, 동아시아, 동남 아시아, 일본 동부, 일본 서 부, 대한민국 중부, 대한민국, 인도 중부, 오스트레일리아 동부, 오스트레일리아 중부, 오스트레일리아 중부 2  아랍에미리트 북부  |
|지원되는 Azure PostgreSQL 버전    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>기능 고려 사항 및 제한 사항

- 모든 작업은 Azure Portal만 지원됩니다. 
- 최대 데이터베이스 크기에 권장되는 제한은 400GB입니다.
- 지역 간 백업을 지원하지 않습니다. 따라서 Azure PostgreSQL 서버를 다른 지역의 자격 증명 모음에 백업할 수 없습니다. 마찬가지로 자격 증명 모음과 동일한 지역 내에 있는 서버로만 백업을 복원할 수 있습니다. 그러나 구독 간 백업 및 복원은 지원 됩니다. 
- 복원 중에만 데이터를 복구 합니다. "역할"이 복원 되지 않습니다.
- 미리 보기에서는 테스트 환경 에서만 솔루션을 실행 하는 것이 좋습니다.

## <a name="backup-process"></a>백업 프로세스

1. 백업 관리자는 백업 하려는 Azure PostgreSQL 데이터베이스를 지정할 수 있습니다. 또한 지정 된 데이터베이스에 연결 하는 데 필요한 자격 증명을 저장 하는 Azure key vault의 세부 정보를 지정할 수도 있습니다. 이러한 자격 증명은 Azure key vault의 데이터베이스 관리자에 의해 안전 하 게 시드 됩니다.
1. 그런 다음 백업 서비스는 지정 된 PostgreSQL 서버를 사용 하 여 [인증할 수 있는 적절 한 권한이](#azure-backup-authentication-with-the-postgresql-server) 있는지 확인 하 고 해당 데이터베이스를 백업 합니다.
1. Azure Backup는 보호 된 PostgreSQL 서버와 통신 하기 위해 설치 된 백업 확장으로 VM (작업자 역할)을 회전 합니다. 이 확장은 코디네이터와 PostgreSQL 플러그 인으로 구성 됩니다. 코디네이터는 백업 및 복원과 같은 다양 한 작업에 대 한 워크플로를 트리거하고 플러그 인은 실제 데이터 흐름을 관리 합니다.
1. 예약 된 시간에 코디네이터는 플러그 인과 통신 하 여 **pg_dump (사용자 지정)** 를 사용 하 여 PostgreSQL 서버에서 백업 데이터 스트리밍을 시작 합니다.
1. 플러그 인은 데이터를 관리 되는 저장소 계정 (백업 자격 증명 모음에 의해 마스킹된) Azure Backup에 직접 전송 하 여 스테이징 위치에 대 한 필요성을 제거 합니다. 데이터는 Microsoft 관리 키를 사용하여 암호화되며 Azure Backup 서비스를 통해 스토리지 계정에 저장됩니다.

 :::image type="content" source="./media/backup-azure-database-postgresql-overview/backup-process.png" alt-text="백업 프로세스를 보여 주는 다이어그램":::

## <a name="azure-backup-authentication-with-the-postgresql-server"></a>PostgreSQL 서버를 사용 하 여 인증 Azure Backup

Azure Backup은 Azure에 의해 세분화 된 엄격한 보안 지침을 따릅니다. 백업할 리소스에 대 한 사용 권한은 가정 되지 않으며 사용자가 명시적으로 지정 해야 합니다. 

### <a name="key-vault-based-authentication-model"></a>키 자격 증명 모음 기반 인증 모델

Azure Backup 서비스는 각 백업을 수행 하는 동안 Azure PostgreSQL에 연결 해야 합니다. 데이터베이스에 해당 하는 ' 사용자 이름 + 암호 ' (또는 연결 문자열)를 사용 하 여이 연결을 설정 하는 동안 이러한 자격 증명은 Azure Backup와 함께 저장 되지 않습니다. 대신 이러한 자격 증명은 [Azure key vault](/azure/key-vault/secrets/about-secrets)의 데이터베이스 관리자에 의해 비밀로 안전 하 게 시드 되어야 합니다. 작업 관리자는 자격 증명을 관리 하 고 순환 해야 합니다. Azure Backup는 백업을 수행 하기 위해 key vault에서 가장 최근의 비밀 정보를 호출 합니다.
 
:::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-based-authentication-model.png" alt-text="작업 또는 데이터베이스 흐름을 보여 주는 다이어그램":::

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-backup"></a>Azure PostgreSQL 데이터베이스 백업에 필요한 권한 집합

1. 백업 자격 증명 모음 MSI에 대 한 다음 액세스 권한을 부여 합니다.

   - Azure PostgreSQL 서버에 대 한 _읽기_ 권한입니다.
   - Azure 주요 자격 증명 모음에 대 한 _비밀 사용자_ (또는 get, list) 액세스를 Key Vault 합니다.

1. 네트워크의 시야 액세스 위치:

   - Azure PostgreSQL 서버 – **azure 서비스에 대 한 액세스 허용** 플래그를 **예** 로 설정 합니다.
   - 키 자격 증명 모음 – **신뢰할 수 있는 Microsoft 서비스** 플래그를 **예** 로 설정할 수 있습니다.

1. 데이터베이스에 대 한 데이터베이스 사용자의 백업 권한

>[!Note]
>원하는 리소스에 대 한 ' 쓰기 ' 액세스 권한이 있는 경우 (백업 관리자) 한 번의 클릭으로 [백업 흐름 구성](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases) 에서 이러한 권한을 부여 하거나, 필요한 권한이 없는 경우 ARM 템플릿을 사용할 수 있습니다 (여러 가상 사용자가 관련 된 경우). 

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-restore"></a>Azure PostgreSQL database 복원에 필요한 권한 집합

복원 권한은 백업에 필요한 권한과 비슷하며 대상 PostgreSQL 서버 및 해당 키 자격 증명 모음에 대 한 사용 권한을 부여 해야 합니다. 백업 흐름 구성에서와 달리 이러한 사용 권한을 인라인으로 부여 하는 환경은 현재 사용할 수 없습니다. 따라서 [Postgres 서버 및 해당 키 자격 증명 모음에 대 한 액세스 권한을 수동으로 부여](#grant-access-on-the-azure-postgresql-server-and-key-vault-manually)해야 합니다.

또한 데이터베이스 사용자 (키 자격 증명 모음에 저장 된 자격 증명에 해당)에 데이터베이스에 대 한 다음과 같은 복원 권한이 있는지 확인 합니다.

- ALTER USER username CREATEDB;
- 데이터베이스 사용자에 게 _azure_pg_admin_ 역할을 할당 합니다.

### <a name="azure-active-directory-based-authentication-model"></a>Azure Active Directory 기반 인증 모델

이전에는 Azure Active Directory (Azure AD)를 기반으로 하는 다른 인증 모델을 시작 했습니다. 그러나 이제는 구성 프로세스를 용이 하 게 하는 대체 옵션으로 새로운 키 자격 증명 모음 기반 인증 모델 (위에서 설명 함)을 제공 합니다. 

이 [문서를 다운로드](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) 하 여이 인증 모델을 사용 하는 자동화 된 스크립트 및 관련 지침을 확인 하세요. Azure PostgreSQL 서버에 대해 백업 및 복원에 대 한 적절 한 권한 집합을 부여 합니다.

>[!Note]
>새로운 모든 구성 보호는 새 키 자격 증명 모음 인증 모델에 대해서만 수행 됩니다. 그러나 Azure AD 기반 인증을 사용 하 여 보호를 구성 하는 모든 기존 백업 인스턴스는 계속 존재 하 고 정기적인 백업을 수행 합니다. 이러한 백업을 복원 하려면 Azure AD 기반 인증을 수행 해야 합니다.

## <a name="grant-access-on-the-azure-postgresql-server-and-key-vault-manually"></a>Azure PostgreSQL 서버 및 주요 자격 증명 모음에 대 한 액세스 권한 부여

Azure Backup에 필요한 모든 액세스 권한을 부여 하려면 다음 섹션을 참조 하십시오.

### <a name="access-permissions-on-the-azure-postgresql-server"></a>Azure PostgreSQL 서버에 대 한 액세스 권한

1. Azure PostgreSQL 서버에서 백업 자격 증명 모음 MSI **판독기** 액세스를 설정 합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-inline.png" alt-text="Azure PostgreSQL 서버에서 백업 자격 증명 모음 MSI 판독기 액세스를 설정 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-expanded.png":::

1. Azure PostgreSQL 서버에 대 한 네트워크 시야 액세스: ' Azure 서비스에 대 한 액세스 허용 ' 플래그를 ' 예 '로 설정 합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-inline.png" alt-text="Azure PostgreSQL 서버에서 네트워크 시야 액세스를 설정 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-expanded.png":::

### <a name="access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server"></a>PostgreSQL 서버에 연결 된 Azure Key vault에 대 한 액세스 권한

1. Azure 주요 자격 증명 모음에서 백업 자격 증명 모음 MSI **Key Vault 비밀 사용자** (또는 **get**, **list** 비밀) 액세스를 설정 합니다. 권한을 할당 하려면 역할 할당 또는 액세스 정책을 사용할 수 있습니다. 도움이 되지 않는 옵션을 사용 하 여 사용 권한을 추가할 필요는 없습니다.

   - Azure RBAC (역할 기반 액세스 제어) 권한 부여 사용 (즉, 권한 모델은 Azure 역할 기반 액세스 제어로 설정 됨):

     - 액세스 제어에서 키 자격 증명 모음에 대 한 _암호 사용자 액세스 Key Vault_ 백업 자격 증명 모음의 MSI를 부여 합니다. 해당 역할의 Bearers는 비밀을 읽을 수 있습니다.
     - [AZURE RBAC를 사용 하 여 azure key vault에 액세스할 수 있는 권한을 응용 프로그램에 부여](/azure/key-vault/general/rbac-guide?tabs=azure-cli)합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-inline.png" alt-text="비밀 사용자 액세스를 제공 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-inline.png" alt-text="키 자격 증명 모음에 대 한 암호 사용자 액세스 Key Vault 백업 자격 증명 모음의 MSI를 부여 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-expanded.png":::  

   - 액세스 정책 사용 (즉, 권한 모델은 자격 증명 모음 액세스 정책으로 설정 됨):

     - 비밀에 대 한 Get 및 List 권한을 설정 합니다.
     - [Azure Key Vault 액세스 정책 할당](/azure/key-vault/general/assign-access-policy?tabs=azure-portal) 에 대 한 자세한 정보

     :::image type="content" source="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-inline.png" alt-text="권한 모델을 사용 하 여 권한을 부여 하는 옵션을 보여 주는 스크린샷은 자격 증명 모음 액세스 정책 모델로 설정 됩니다." lightbox="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-expanded.png":::  
 

1. 키 자격 증명 모음에 대 한 네트워크 시야 액세스: **신뢰할 수 있는 Microsoft 서비스 허용** 플래그를 **예** 로 설정 합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-inline.png" alt-text="키 자격 증명 모음에 대 한 네트워크 시야 액세스를 위해 신뢰할 수 있는 Microsoft 서비스 허용 플래그를 예로 설정 하는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-expanded.png":::

### <a name="database-users-backup-privileges-on-the-database"></a>데이터베이스에 대한 데이터베이스 사용자의 백업 권한

[PG 관리](#using-the-pg-admin-tool) 도구에서 다음 쿼리를 실행합니다(사용자 이름을 데이터베이스 _사용자_ ID로 바꾸기).

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
```

## <a name="using-the-pg-admin-tool"></a>PG 관리 도구 사용

[PG 관리자 도구가](https://www.pgadmin.org/download/) 없는 경우 다운로드합니다. 이 도구를 통해 Azure PostgreSQL 서버에 연결할 수 있습니다. 또한 이 서버에 데이터베이스 및 새 사용자를 추가할 수 있습니다.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-inline.png" alt-text="PG 관리 도구를 사용하여 Azure PostgreSQL 서버에 연결하는 프로세스를 보여주는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-expanded.png":::

선택한 이름으로 새 서버를 만듭니다. Azure Portal Azure PostgreSQL 리소스 뷰에 표시된 서버 이름과 동일한 호스트 **이름/주소 이름을** 입력합니다.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-inline.png" alt-text="PG 관리 도구를 사용하여 새 서버를 만드는 옵션을 보여주는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-inline.png" alt-text="서버 이름과 동일한 호스트 이름 또는 주소 이름을 입력하는 옵션을 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-expanded.png":::

연결을 통과할 방화벽 규칙에 _현재 클라이언트 ID 주소를_ 추가해야 합니다.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-inline.png" alt-text="방화벽 규칙에 현재 클라이언트 ID 주소를 추가하는 프로세스를 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-expanded.png":::

서버에 새 데이터베이스 및 데이터베이스 사용자를 추가할 수 있습니다. 데이터베이스 사용자의 경우 새 **로그인/그룹 역할**'을 추가합니다. **로그인할 수 있나요?가** **예** 로 설정되어 있는지 확인합니다.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-inline.png" alt-text="서버에 새 데이터베이스 및 데이터베이스 사용자를 추가하는 프로세스를 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-inline.png" alt-text="데이터베이스 사용자에 대한 새 로그인 또는 그룹 역할을 추가하는 프로세스를 보여주는 스크린샷" lightbox="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-inline.png" alt-text="로그인 가능 옵션의 확인이 예로 설정되는 것을 보여주는 스크린샷." lightbox="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-expanded.png":::

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL 백업](backup-azure-database-postgresql.md)
