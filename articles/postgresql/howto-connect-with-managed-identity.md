---
title: 관리 ID를 사용하여 연결 - Azure Database for PostgreSQL - 단일 서버
description: Azure Database for PostgreSQL 인증을 위해 관리 ID를 사용하여 연결하고 인증하는 방법을 알아봅니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7cc4b086a4ec9d89187345a66d6ae3e39c999ca0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232180"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>관리 ID를 사용하여 Azure Database for PostgreSQL에 연결

시스템 할당 및 사용자 할당 관리 id를 모두 사용 하 여 Azure Database for PostgreSQL에 인증할 수 있습니다. 이 문서에서는 Azure VM (가상 컴퓨터)에 대 한 시스템 할당 관리 id를 사용 하 여 Azure Database for PostgreSQL 서버에 액세스 하는 방법을 보여 줍니다. 관리 Id는 Azure에서 자동으로 관리 되며, 코드에 자격 증명을 삽입할 필요 없이 Azure AD 인증을 지 원하는 서비스에 인증할 수 있습니다.

다음 방법을 알아봅니다.
- Azure Database for PostgreSQL 서버에 대한 액세스 권한을 VM에 부여
- VM의 시스템이 할당 한 id를 나타내는 사용자를 데이터베이스에 만듭니다.
- VM ID를 사용하여 액세스 토큰을 가져와서 Azure Database for PostgreSQL 서버를 쿼리하는 데 사용
- C# 예제 애플리케이션에서 토큰 검색 구현

## <a name="prerequisites"></a>사전 요구 사항

- Azure 리소스에 대한 관리 ID 기능이 익숙하지 않은 경우 [개요](../../articles/active-directory/managed-identities-azure-resources/overview.md)를 참조하세요. Azure 계정이 없으면 계속하기 전에 [체험 계정에 등록](https://azure.microsoft.com/free/)합니다.
- 필요한 리소스를 만들고 역할을 관리하려면 적절한 범위(사용자 구독 또는 리소스 그룹)의 "소유자" 권한이 계정에 필요합니다. 역할 할당에 관한 도움이 필요한 경우 [Azure 역할을 할당하여 Azure 구독 리소스에 대한 액세스 관리](../../articles/role-based-access-control/role-assignments-portal.md)를 참조하세요.
- 관리 ID를 사용하여 데이터베이스에 액세스하는 데 사용할 Azure VM(예: Ubuntu Linux 실행)이 필요합니다.
- [Azure AD 인증](howto-configure-sign-in-aad-authentication.md)이 구성된 Azure Database for PostgreSQL 데이터베이스 서버가 필요합니다.
- C# 예제를 따르려면 먼저 [C#을 사용하여 연결](connect-csharp.md)하는 방법 가이드를 완료합니다.

## <a name="creating-a-system-assigned-managed-identity-for-your-vm"></a>VM에 대 한 시스템 할당 관리 id 만들기

[az vm identity assign](/cli/azure/vm/identity/)을 `identity assign` 명령과 함께 사용하여 기존 VM에 시스템 할당 ID를 사용하도록 설정합니다.

```azurecli-interactive
az vm identity assign -g myResourceGroup -n myVm
```

시스템 할당 관리 id의 응용 프로그램 ID를 검색 합니다 .이 ID는 다음 몇 단계에서 필요 합니다.

```azurecli
# Get the client ID (application ID) of the system-assigned managed identity
az ad sp list --display-name obs-locdev-wus2 --query [*].appId --out tsv
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>관리 ID에 대한 PostgreSQL 사용자 만들기

이제 PostgreSQL 데이터베이스에 Azure AD 관리자 사용자로 연결 하 고 다음 SQL 문을 실행 합니다 `CLIENT_ID` .를 시스템 할당 관리 id에 대해 검색 한 클라이언트 ID로 바꿉니다.

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

이제 `myuser`(선택한 이름으로 바꿈)라는 사용자 이름을 사용하여 인증하면 관리 ID에 액세스할 수 있습니다.

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Azure Instance Metadata Service에서 액세스 토큰 검색

이제 애플리케이션에서 Azure Instance Metadata Service를 통해 액세스 토큰을 검색하여 데이터베이스를 인증하는 데 사용할 수 있습니다.

이 토큰 검색은 `http://169.254.169.254/metadata/identity/oauth2/token`에 대한 HTTP 요청을 수행하고 다음 매개 변수를 전달하여 수행됩니다.

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID`(이전에 검색한 항목)

`access_token` 필드가 포함된 JSON 결과가 반환됩니다. 이 긴 텍스트 값은 데이터베이스에 연결할 때 암호로 사용해야 하는 관리 ID 액세스 토큰입니다.

테스트를 위해 셸에서 다음 명령을 실행할 수 있습니다. `curl`, `jq` 및 `psql` 클라이언트를 설치해야 합니다.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

이제 이전에 구성한 데이터베이스에 연결되었습니다.

## <a name="connecting-using-managed-identity-in-c"></a>C#에서 관리 ID를 사용하여 연결

이 섹션에서는 VM의 사용자가 할당한 관리 ID를 사용하여 액세스 토큰을 가져오고, 이를 사용하여 Azure Database for PostgreSQL을 호출하는 방법을 보여 줍니다. Azure Database for PostgreSQL은 기본적으로 Azure AD 인증을 지원하므로 Azure 리소스에 대한 관리 ID를 사용하여 획득한 액세스 토큰을 직접 수락할 수 있습니다. PostgreSQL에 대한 연결을 만들 때 액세스 토큰을 암호 필드에 전달합니다.

다음은 액세스 토큰을 사용하여 PostgreSQL에 대한 연결을 여는 .NET 코드 예제입니다. 이 코드는 system 할당 관리 id를 사용 하 여 Azure AD에서 액세스 토큰을 가져오는 VM에서 실행 해야 합니다. HOST, USER, DATABASE 및 CLIENT_ID의 값을 바꿉니다.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;
using Azure.Identity;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        //private static string ClientId = "CLIENT_ID";

        static async Task Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure AD...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                var sqlServerTokenProvider = new DefaultAzureCredential();
                accessToken = (await sqlServerTokenProvider.GetTokenAsync(
                    new Azure.Core.TokenRequestContext(scopes: new string[] { "https://ossrdbms-aad.database.windows.net/.default" }) { })).Token;

            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4}; SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

이 명령을 실행하면 다음과 같은 출력이 제공됩니다.

```
Getting access token from Azure AD...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.11, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>다음 단계

* [Azure Database for PostgreSQL로 Azure Active Directory 인증](concepts-aad-authentication.md)에 대한 전반적인 개념 검토
