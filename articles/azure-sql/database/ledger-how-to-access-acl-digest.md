---
title: ACL(Azure Confidential Ledger)에 저장된 다이제스트에 액세스하는 방법
description: Azure SQL Database 원장을 사용하여 ACL(Azure Confidential Ledger)에 저장된 다이제스트에 액세스하는 방법
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 3f8b5ae7c80c712c441648808f0303528bad8018
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966729"
---
# <a name="how-to-access-the-digests-stored-in-acl"></a>ACL에 저장된 다이제스트에 액세스하는 방법

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 **공개 미리 보기** 로 제공됩니다.

이 문서에서는 [Azure ACL(Azure Confidential Ledger)](../../confidential-ledger/index.yml)에 저장된 [Azure SQL Database 원장](ledger-overview.md) 다이제스트에 액세스하여 엔드투엔드 보안 및 무결성을 보장하는 방법을 보여 줍니다. 이 문서에서는 저장된 정보의 무결성에 액세스하고 확인하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Python 2.7, 3.5.3 이상
- 원장을 사용하는 기존 Azure SQL Database가 있어야 합니다. Azure SQL Database를 아직 만들지 않은 경우 [빠른 시작: 원장을 사용하는 Azure SQL 데이터베이스 만들기](ledger-create-a-single-database-with-ledger-enabled.md)를 참조하세요.
- [Python용 Azure Confidential Ledger 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger)
- [Azure Confidential Ledger](../../confidential-ledger/index.yml)의 실행 중인 인스턴스.

## <a name="how-does-the-integration-work"></a>통합은 어떻게 작동하나요?

Azure SQL 서버는 [원장 데이터베이스](ledger-overview.md#ledger-database)의 다이제스트를 주기적으로 계산하여 Azure Confidential Ledger에 저장합니다. 사용자는 언제든지 Azure Confidential Ledger에서 다이제스트를 다운로드하고 Azure SQL Database 원장에 저장된 다이제스트와 비교하여 데이터의 무결성에 대한 유효성 검사를 수행할 수 있습니다. 다음 단계에서는 이에 대해 설명합니다.

## <a name="1-find-the-digest-location"></a>1. 다이제스트 위치 찾기

> [!NOTE]
> 다이제스트를 저장하는 데 여러 Azure Confidential Ledger 인스턴스가 사용된 경우, 쿼리가 둘 이상의 행을 반환합니다. 각 행에 대해 2~6단계를 반복하여 Azure Confidential Ledger의 모든 인스턴스에서 다이제스트를 다운로드합니다.

[SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 다음 쿼리를 실행합니다. 다이제스트가 저장되는 Azure Confidential Ledger 인스턴스의 엔드포인트가 출력에 표시됩니다.

```sql
SELECT * FROM sys.database_ledger_digest_locations WHERE path like '%.confidential-ledger.azure.com%
```

## <a name="2-determine-the-subledgerid"></a>2. Subledgerid 확인

우리는 쿼리 출력의 경로 열 값에 관심이 있습니다. 이는 `host name` 및 `subledgerid`라는 두 부분으로 구성됩니다. 예를 들어, `https://contoso-ledger.confidential-ledger.azure.com/sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000`이라는 Url에서 `host name`은 `https://contoso-ledger.confidential-ledger.azure.com`이고 `subledgerid`는 `sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000`입니다. 이는 4단계에서 다이제스트를 다운로드하는 데 사용합니다.

## <a name="3-obtain-an-azure-ad-token"></a>3. Azure AD 토큰 가져오기

Azure Confidential Ledger API는 Azure AD(Azure Active Directory) 전달자 토큰을 호출자 ID로 허용합니다. 이 ID는 프로비저닝 중에 Azure Resource Manager를 통해 ACL에 액세스해야 합니다. SQL Database에서 장부를 사용하도록 설정한 사용자는 Azure Confidential Ledger에 대한 관리자 액세스 권한을 자동으로 부여받습니다. 토큰을 가져오려면 사용자가 Azure Portal에서 사용한 것과 동일한 계정을 사용하여 [Azure CLI](/cli/azure/install-azure-cli)를 통해 인증해야 합니다. 사용자가 인증되면 [AzureCliCredential](/python/api/azure-identity/azure.identity.azureclicredential)을 사용하여 전달자 토큰을 검색하고 Azure Confidential Ledger API를 호출할 수 있습니다.

ACL에 대한 액세스 권한이 있는 ID를 사용하여 Azure AD에 로그인합니다.

```azure-cli
az login
```

전달자 토큰을 검색합니다.

```python
from azure.identity import AzureCliCredential
credential = AzureCliCredential()
```

## <a name="4-download-the-digests-from-azure-confidential-ledger"></a>4. Azure Confidential Ledger에서 다이제스트 다운로드

다음 Python 스크립트는 Azure Confidential Ledger에서 다이제스트를 다운로드합니다. 이 스크립트는 [Python용 Azure Confidential Ledger 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger)를 사용합니다.

```python
from azure.identity import AzureCliCredential
from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

ledger_id = "contoso-ledger"
identity_server_url = "https://identity.confidential-ledger.core.azure.com"
sub_ledger_id = "sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000"
ledger_host_url = f"https://{ledger_id}.confidential-ledger.azure.com"
initial_path = f"/app/transactions?api-version=0.1-preview&subLedgerId={sub_ledger_id}"

identity_client = ConfidentialLedgerIdentityServiceClient(identity_server_url)
network_identity = identity_client.get_ledger_identity(
    ledger_id=ledger_id
)

ledger_tls_cert_file_name = f"{ledger_id}_certificate.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)

credential = AzureCliCredential()
ledger_client = ConfidentialLedgerClient(
    endpoint=ledger_host_url, 
    credential=credential,
    ledger_certificate_path=ledger_tls_cert_file_name
)

ranged_result = ledger_client.get_ledger_entries(
    sub_ledger_id=sub_ledger_id
)

entries = 0

for entry in ranged_result:
    entries += 1
    print(f"\nTransaction id {entry.transaction_id} contents: {entry.contents}")

if entries == 0:
    print("\n***No digests are found for the supplied SubledgerID.")
else:
    print("\n***No more digests were found for the supplied SubledgerID.")
```

## <a name="5-download-the-digests-from-the-sql-server"></a>5. SQL Server에서 다이제스트 다운로드

> [!NOTE]
> 이는 Azure SQL Database 원장에 저장된 해시가 시간이 흐르면서 변경되지 않았는지 확인하는 방법입니다. Azure SQL Database 원장의 무결성을 완벽하게 감사하려면 [원장 테이블을 확인하여 변조를 감지하는 방법](ledger-verify-database.md)을 참조하세요.

[SSMS](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 다음 쿼리를 실행합니다. 이 쿼리는 Genesis의 블록 다이제스트를 반환합니다.

```sql
SELECT * FROM sys.database_ledger_blocks
```

## <a name="6-comparison"></a>6. 비교

`block_id`를 키로 사용하여 Azure Confidential Ledger에서 검색된 다이제스트와 SQL 데이터베이스에서 반환된 다이제스트를 비교합니다. 예를 들어 `block_id` = `1`의 다이제스트는 `block_id`= `2` 행에서 `previous_block_hash` 열의 값입니다. 마찬가지로, `block_id` = `3`의 경우 `block_id` = `4` 행에 있는 `previous_block_id` 열의 값입니다. 해시 값의 불일치는 잠재적인 데이터 변조를 나타냅니다.

데이터 변조가 의심되는 경우 [원장 테이블을 확인하여 변조를 감지하는 방법](ledger-verify-database.md)을 참조하여 Azure SQL Database 원장에 대한 전체 감사를 수행합니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 원장 개요](ledger-overview.md)
- [데이터베이스 원장](ledger-database-ledger.md)
- [다이제스트 관리 및 데이터베이스 확인](ledger-digest-management-and-database-verification.md)
- [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)
- [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)
- [원장 테이블을 확인하여 변조를 감지하는 방법](ledger-verify-database.md)
