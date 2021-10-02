---
title: 백업 Azure Database for PostgreSQL
description: 장기 보존(미리 보기)을 사용한 Azure Database for PostgreSQL 백업에 대해 알아보기
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 25304aee2a759b55b8b3139aa2ae57511c967595
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389877"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>장기 보존(미리 보기)을 사용한 Azure Database for PostgreSQL 백업

이 문서에서는 Azure Database for PostgreSQL 서버를 백업 하는 방법을 설명 합니다.

## <a name="configure-backup-on-azure-postgresql-databases"></a>Azure PostgreSQL 데이터베이스에서 백업 구성

여러 Azure PostgreSQL 서버에서 여러 데이터베이스에 백업을 구성할 수 있습니다. Azure Backup를 사용 하 여 Azure PostgreSQL 데이터베이스에서 백업을 구성 하려면 다음 단계를 수행 합니다.

1. **백업 자격 증명 모음**  ->  **+ 백업** 으로 이동 합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-inline.png" alt-text="백업을 추가 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/adding-backup-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-details-inline.png" alt-text="백업 정보를 추가 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/adding-backup-details-expanded.png":::

   또는 [백업 센터](/azure/backup/backup-center-overview)에서이 페이지로 이동할 수 있습니다. 

1. 백업 일정 및 보존 기간을 정의 하는[백업 정책을](#create-backup-policy) 선택/만듭니다.

   :::image type="content" source="./media/backup-azure-database-postgresql/create-or-add-backup-policy-inline.png" alt-text="백업 정책을 추가 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/create-or-add-backup-policy-expanded.png":::

1. **백업할 Azure Postgres 데이터베이스 선택**: 자격 증명 모음과 동일한 지역에 있는 경우 구독에서 azure PostgreSQL 서버 중 하나를 선택 합니다. 화살표를 확장하여 서버 내의 데이터베이스 목록을 확인합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-inline.png" alt-text="Azure PostgreSQL 데이터베이스를 선택 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-inline.png" alt-text="Azure PostgreSQL 서버를 선택 하는 방법을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-expanded.png":::


1. 선택한 데이터베이스에 연결 하기 위한 자격 증명을 저장 하는 **Azure key vault를 할당** 합니다. 개별 행 수준에서 키 자격 증명 모음을 할당 하려면 **키 자격 증명 모음 및 암호 선택** 을 클릭 합니다. 행을 다중 선택 하 여 키 자격 증명 모음을 할당 하 고 표의 상단 메뉴에서 키 자격 증명 모음 할당을 클릭할 수도 있습니다. 

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-azure-key-vault-inline.png" alt-text="Azure key vault를 할당 하는 방법을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/assign-azure-key-vault-expanded.png"::: 

1. 비밀 정보를 지정 하려면 다음 옵션 중 하나를 사용 합니다. 

   1. **비밀 Uri 입력**: 비밀 uri를 공유/알 수 있는 경우이 옵션을 사용 합니다. **키 자격 증명 모음**  ->  **비밀 (비밀 선택)**  ->  **암호 식별자** 에서 비밀 URI를 복사할 수 있습니다.

      :::image type="content" source="./media/backup-azure-database-postgresql/enter-secret-uri-inline.png" alt-text="비밀 URI를 입력 하는 방법을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/enter-secret-uri-expanded.png":::  

      그러나이 옵션을 사용 하면 Azure Backup는 참조 된 key vault에 대 한 표시 여부를 확인할 수 없습니다. 따라서 key vault에 대 한 액세스 권한은 인라인으로 부여할 수 없습니다. 백업 관리자는 Postgres 및/또는 키 자격 증명 모음 관리자와 함께 백업 작업이 성공 하기 위해 구성 백업 흐름 외부에서 [키 자격 증명 모음에 대](backup-azure-database-postgresql-overview.md#access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server) 한 백업 자격 증명 모음에 대 한 액세스 권한을 수동으로 부여 해야 합니다.

   1. **키 자격 증명 모음 선택**: 키 자격 증명 모음 및 암호 이름을 알고 있는 경우이 옵션을 사용 합니다. 이 옵션을 사용 하는 경우 (키 자격 증명 모음에 대 한 쓰기 권한이 있는 backup admin) 키 자격 증명 모음에 대 한 액세스 권한을 인라인으로 부여할 수 있습니다. 키 자격 증명 모음 및 암호는 이전에 존재 하거나 이동할 때 생성 될 수 있습니다. 암호는 서버에 대 한 ' 백업 ' 권한이 부여 된 데이터베이스 사용자의 자격 증명으로 업데이트 된 ADO.net format의 PG 서버 연결 문자열 인지 확인 합니다. [ [주요 자격 증명 모음에서 비밀](#create-secrets-in-the-key-vault)을 만드는 방법]에 대해 자세히 알아보세요.

      :::image type="content" source="./media/backup-azure-database-postgresql/assign-secret-store-inline.png" alt-text="비밀 저장소를 할당 하는 방법을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/assign-secret-store-expanded.png":::

      :::image type="content" source="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-inline.png" alt-text="Azure Key Vault에서 비밀 선택을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-expanded.png":::   

1. 비밀 정보 업데이트가 완료 되 면 키 자격 증명 모음 정보가 업데이트 된 후 유효성 검사가 시작 됩니다. 여기서 백업 서비스는 주요 자격 증명 모음에서 비밀 정보를 읽고 데이터베이스에 연결 하는 데 필요한 모든 [액세스 권한](backup-azure-database-postgresql-overview.md#key-vault-based-authentication-model)()이 있는지 확인 합니다. 하나 이상의 액세스 권한이 없는 경우 오류 메시지 중 하나를 표시 합니다. _역할 할당은 완료 되지 않았거나 사용자는 역할을 할당할 수 없습니다_.

   :::image type="content" source="./media/backup-azure-database-postgresql/validation-of-secret-inline.png" alt-text="비밀의 유효성 검사를 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/validation-of-secret-expanded.png":::   

   1. **사용자가 역할을 할당할 수 없음**:이 메시지는 사용자 (백업 관리자)가 PostgreSQL 서버 및/또는 key vault에 대 한 쓰기 액세스 권한이 없는 경우 표시 되는 **세부 정보 보기** 에 나열 된 권한 작업 단추에서 할당 템플릿을 다운로드 하 고 PostgreSQL 및/또는 키 자격 증명 모음 관리자가 실행 하도록 합니다. 필요한 리소스에 필요한 사용 권한을 할당 하는 데 도움이 되는 ARM 템플릿입니다. 템플릿이 성공적으로 실행 되 면 백업 구성 페이지에서 **다시 유효성 검사** 를 클릭 합니다.

      :::image type="content" source="./media/backup-azure-database-postgresql/download-role-assignment-template-inline.png" alt-text="역할 할당 템플릿을 다운로드 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/download-role-assignment-template-expanded.png":::    

   1. **역할 할당 완료 안** 됨:이 메시지는 사용자 (백업 관리자)가 PostgreSQL 서버 및/또는 key vault에 대 한 쓰기 권한을 가지 면 **세부 정보 보기** 에 나열 된 대로 누락 된 사용 권한을 할당 하는 경우 표시 됩니다. 위쪽의 작업 메뉴에서 **누락 된 역할 할당** 작업 단추를 사용 하 여 PostgreSQL 서버 및/또는 주요 자격 증명 모음에 대 한 사용 권한을 인라인으로 부여할 수 있습니다.

      :::image type="content" source="./media/backup-azure-database-postgresql/role-assignment-not-done-inline.png" alt-text="역할 할당에 대 한 오류를 보여 주는 스크린샷 완료 되지 않습니다." lightbox="./media/backup-azure-database-postgresql/role-assignment-not-done-expanded.png":::     

1. 상단 메뉴에서 **누락 된 역할 할당** 을 선택 하 고 역할을 할당 합니다. 프로세스가 시작 되 면 백업 자격 증명 모음에 KV 및/또는 PG 서버에 대 한 [누락 된 액세스 권한이](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server) 부여 됩니다. 액세스 권한을 부여 해야 하는 범위를 정의할 수 있습니다. 작업이 완료 되 면 유효성 검사를 다시 시작 합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-missing-roles-inline.png" alt-text="누락 된 역할을 할당 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/assign-missing-roles-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/define-scope-of-access-permission-inline.png" alt-text="액세스 권한 범위를 정의 하는 것을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/define-scope-of-access-permission-expanded.png":::     

   - 백업 자격 증명 모음은 key vault의 비밀에 액세스 하 고 데이터베이스에 대 한 테스트 연결을 실행 하 여 자격 증명이 올바르게 입력 되었는지 확인 합니다. 데이터베이스 사용자의 권한을 확인 하 여 데이터베이스 사용자에 게 데이터베이스 [에 대 한 백업 관련 권한이](backup-azure-database-postgresql-overview.md#database-users-backup-privileges-on-the-database)있는지 여부도 확인 합니다.

   - PostgreSQL admin에는 기본적으로 데이터베이스에 대 한 모든 백업 및 복원 권한이 있습니다. 따라서 유효성 검사가 성공 합니다.
   - 권한이 낮은 사용자에 게는 데이터베이스에 대 한 백업/복원 권한이 없을 수 있습니다. 따라서 유효성 검사가 실패 합니다. PowerShell 스크립트는 동적으로 생성 됩니다 (레코드/선택한 데이터베이스 당 하나). [PowerShell 스크립트를 실행 하 여 데이터베이스의 데이터베이스 사용자에 게 이러한 권한을 부여](#create-secrets-in-the-key-vault)합니다. 또는 PG admin 또는 PSQL 도구를 사용 하 여 이러한 권한을 할당할 수 있습니다.

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-inline.png" alt-text="키 자격 증명 모음에서 백업 자격 증명 모음 액세스 비밀을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/run-test-connection.png" alt-text="연결 테스트를 시작 하는 프로세스를 보여 주는 스크린샷":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-inline.png" alt-text="테스트를 실행 하기 위해 사용자 자격 증명을 제공 하는 방법을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-expanded.png":::      

1. 백업 준비가 완료 된 레코드를 성공으로 유지 하 여 작업 제출의 마지막 단계를 진행 합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-readiness-as-success-inline.png" alt-text="백업 준비 상태를 보여 주는 스크린샷에 성공 했습니다." lightbox="./media/backup-azure-database-postgresql/backup-readiness-as-success-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/review-backup-configuration-details-inline.png" alt-text="백업 구성 검토 페이지를 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/review-backup-configuration-details-expanded.png":::      

1. 백업 구성 작업을 제출 하 고 **백업 인스턴스** 아래에서 진행 상황을 추적 합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql/submit-configure-backup-operation-inline.png" alt-text="백업 구성 제출 및 추적 진행률을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/submit-configure-backup-operation-expanded.png":::      

## <a name="create-backup-policy"></a>Backup 정책 만들기

백업 흐름을 구성 하는 동안 이동에 대 한 백업 정책을 만들 수 있습니다. 또는 **백업 센터**  ->  **백업 정책**  ->  **추가** 로 이동 합니다.

1. 새 정책에 대한 이름을 입력합니다.

   :::image type="content" source="./media/backup-azure-database-postgresql/enter-name-for-new-policy-inline.png" alt-text="새 정책의 이름을 입력 하는 프로세스를 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/enter-name-for-new-policy-expanded.png":::

1. 백업 일정을 정의합니다. 현재 주간 백업 옵션만 사용할 수 있습니다. 그러나 여러 요일에 백업을 예약할 수 있습니다.

1. **보존** 설정을 정의합니다. 하나 이상의 보존 규칙을 추가할 수 있습니다. 각 보존 규칙은 특정 백업에 대한 입력과 해당 백업에 대한 데이터 저장소 및 보존 기간을 가정합니다.

1. 두 데이터 저장소 (또는 계층) 중 하나에 백업을 저장 하려면 **백업 데이터 저장소** (표준 계층) 또는 **보관 데이터 저장소** (미리 보기)를 선택 합니다.

1. 백업 데이터 저장소에서 만료 될 때 백업을 보관 데이터 저장소로 이동 하려면 **만료 날짜** 를 선택 합니다.

   **기본 보존 규칙** 은 다른 보존 규칙이 없을 때 적용 되며 기본값은 3 개월입니다.

   - 보존 기간 범위는 **백업 데이터 저장소** 에서 7일에서 10년 사이입니다.
   - 보존 기간 범위는 **보관 데이터 저장소** 에서 7개월에서 10년 사이입니다.

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-inline.png" alt-text="5를 보여 주는 스크린샷   만료 시 백업을 보관 데이터 저장소로 이동 하려면 만료 날짜를 선택 합니다." lightbox="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-expanded.png":::

>[!Note]
>보존 규칙은 미리 결정된 우선 순위 순서로 평가됩니다. 우선 순위는 연도별 규칙의 가장 높은 것이 고, 그 다음에는 매월, 주간 규칙은입니다. 다른 규칙이 한정되지 않은 경우 기본 보존 설정이 적용됩니다. 예를 들어 동일한 복구 지점은 매주 수행된 첫 번째 성공한 백업뿐만 아니라 매월 수행된 첫 번째 성공한 백업일 수 있습니다. 그러나 월별 규칙 우선 순위가 주간 규칙의 우선 순위 보다 높기 때문에 매월 수행 된 첫 번째 성공한 백업에 해당 하는 보존은 적용 됩니다.
## <a name="create-secrets-in-the-key-vault"></a>키 자격 증명 모음에서 비밀 만들기

암호는 서버에 대 한 **백업** 권한이 부여 된 데이터베이스 사용자의 자격 증명으로 업데이트 된 _ADO.NET_ format의 PG 서버 연결 문자열입니다. PG 서버에서 연결 문자열을 복사 하 고 텍스트 편집기에서 편집 하 여 _사용자 ID와 암호_ 를 업데이트 합니다. 

:::image type="content" source="./media/backup-azure-database-postgresql/pg-server-connection-string-inline.png" alt-text="PG 서버 연결 문자열을 비밀로 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/pg-server-connection-string-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/create-secret-inline.png" alt-text="비밀 PG 서버 연결 문자열을 만드는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/create-secret-expanded.png":::

## <a name="run-powershell-script-to-grant-privileges-to-database-users"></a>PowerShell 스크립트를 실행 하 여 데이터베이스 사용자에 게 권한 부여

백업 구성 중에 동적으로 PowerShell 스크립트 생성은 PG 관리자 자격 증명과 함께 데이터베이스 사용자를 입력으로 받아 데이터베이스의 데이터베이스 사용자에 게 백업 관련 권한을 부여 합니다.

[Psql 도구가](https://www.enterprisedb.com/download-postgresql-binaries) 컴퓨터에 있어야 하 고 path 환경 변수가 psql tools 경로에 적절 하 게 설정 되어 있어야 합니다.

:::image type="content" source="./media/backup-azure-database-postgresql/psql-set-environment-inline.png" alt-text="환경 설정 응용 프로그램을 검색 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/psql-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/system-properties-to-set-environment-inline.png" alt-text="시스템 속성에서 환경을 설정 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/system-properties-to-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/adding-environment-variables-inline.png" alt-text="기본 환경 변수를 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/adding-environment-variables-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/editing-environment-variables-inline.png" alt-text="설정 해야 하는 환경 변수를 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/editing-environment-variables-expanded.png":::

Azure PostgreSQL 인스턴스의 **연결 보안 설정** 에서 네트워크 연결을 허용 하는 컴퓨터의 IP 주소를 allowlist 확인 합니다.

## <a name="generate-an-on-demand-backup"></a>주문형 백업 생성

정책에 지정된 일정에 없는 백업을 트리거하려면 **백업 인스턴스** -> **지금 백업** 으로 이동합니다.
연결된 백업 정책에 정의된 보존 규칙 목록에서 선택합니다.

:::image type="content" source="./media/backup-azure-database-postgresql/navigate-to-retention-rules-inline.png" alt-text="연결 된 백업 정책에 정의 된 보존 규칙의 목록을 탐색 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/navigate-to-retention-rules-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/choose-retention-rules-inline.png" alt-text="연결 된 백업 정책에 정의 된 보존 규칙을 선택 하는 옵션을 보여 주는 스크린샷" lightbox="./media/backup-azure-database-postgresql/choose-retention-rules-expanded.png":::

## <a name="next-steps"></a>다음 단계

[Azure Backup를 사용 하 여 PostgreSQL 데이터베이스 백업 문제 해결](backup-azure-database-postgresql-troubleshoot.md)
