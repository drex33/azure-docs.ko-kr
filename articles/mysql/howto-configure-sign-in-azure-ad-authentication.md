---
title: Azure Active Directory 사용 - Azure Database for MySQL
description: Azure Database for MySQL 인증을 위해 Azure AD(Active Directory)를 설정하는 방법에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 07/23/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d05b48432fd976bf7e5b8add01532aae361968ec
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167196"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>MySQL 인증에 Azure Active Directory 사용

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 Azure Database for MySQL을 사용하여 Azure Active Directory 액세스를 구성하는 방법 및 Azure AD 토큰을 사용하여 연결하는 방법을 단계적으로 알아봅니다.

> [!IMPORTANT]
> Azure Active Directory 인증은 MySQL 5.7 이상에서만 사용할 수 있습니다.

## <a name="setting-the-azure-ad-admin-user"></a>Azure AD 관리 사용자 설정

Azure AD 관리 사용자만 Azure AD 기반 인증에 대한 사용자를 만들거나 사용하도록 설정할 수 있습니다. Azure AD 관리 사용자를 만들려면 다음 단계를 수행하세요.

1. Azure Portal에서 Azure AD에 사용하도록 설정하려는 Azure Database for MySQL 인스턴스를 선택합니다.
2. 설정에서 Active Directory 관리자를 선택합니다.

![azure ad 관리자 설정][2]

3. 고객 테넌트에서 유효한 Azure AD 사용자를 Azure AD 관리자로 선택합니다.

> [!IMPORTANT]
> 관리자를 설정하면 Azure Database for MySQL 서버에 모든 관리자 권한이 있는 새 사용자가 추가됩니다.

MySQL 서버당 하나의 Azure AD 관리자만 만들 수 있으며 다른 사용자를 선택하면 서버에 구성된 기존 Azure AD 관리자를 덮어씁니다.

관리자를 구성하면 이제 로그인할 수 있습니다.

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Azure AD를 사용하여 Azure Database for MySQL에 연결

다음 개략적인 다이어그램은 Azure Database for MySQL에 Azure AD 인증을 사용하는 워크플로를 요약하여 보여 줍니다.

![인증 흐름][1]

Azure AD를 인식하지 못하고 MySQL에 연결할 때 사용자 이름 및 암호 지정만 지원하는 MySQL CLI와 같은 일반적인 MySQL 도구와 작동하도록 Azure AD 통합을 설계했습니다. 위의 그림에 표시된 것처럼 Azure AD 토큰을 암호로 전달합니다.

현재 다음 클라이언트를 테스트했습니다.

- MySQLWorkbench 
- Mysql CLI

또한 가장 일반적인 애플리케이션 드라이버를 테스트했으며 이 페이지의 끝 부분에서 세부 정보를 볼 수 있습니다.

다음은 사용자/애플리케이션에서 Azure AD로 인증해야 하는 단계입니다.

### <a name="prerequisites"></a>필수 구성 요소

Azure Cloud Shell, Azure VM 또는 로컬 머신에서 수행할 수 있습니다. [Azure CLI가 설치](/cli/azure/install-azure-cli)되어 있는지 확인합니다.

### <a name="step-1-authenticate-with-azure-ad"></a>1단계: Azure AD를 사용하여 인증

Azure CLI 도구를 사용하여 Azure AD로 인증을 시작합니다. Azure Cloud Shell에서는 이 단계가 필요하지 않습니다.

```
az login
```

이 명령을 사용하면 Azure AD 인증 페이지에 대한 브라우저 창이 표시됩니다. Azure AD 사용자 ID와 암호를 제공해야 합니다.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2단계: Azure AD 액세스 토큰 검색

Azure Database for MySQL에 액세스하려면 Azure CLI 도구를 호출하여 1단계에서 Azure AD 인증된 사용자의 액세스 토큰을 가져옵니다.

예(공용 클라우드의 경우):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```
위의 리소스 값은 표시된 대로 정확하게 지정해야 합니다. 다른 클라우드의 경우 다음을 사용하여 리소스 값을 조회할 수 있습니다.

```azurecli-interactive
az cloud show
```

Azure CLI 버전 2.0.71 이상은 모든 클라우드에 대해 다음과 같은 더 편리한 버전으로 명령을 지정할 수 있습니다.

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```
PowerShell을 사용하면 다음 명령을 통해 액세스 토큰을 얻을 수 있습니다.

```azurepowershell-interactive
$accessToken = Get-AzAccessToken -ResourceUrl https://ossrdbms-aad.database.windows.net
$accessToken.Token | out-file C:\temp\MySQLAccessToken.txt
```


인증에 성공하면 Azure AD가 액세스 토큰을 반환합니다.

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

토큰은 Azure Database for MySQL 서비스를 대상으로 하고 인증된 사용자에 대한 모든 정보를 인코딩하는 Base 64 문자열입니다.

액세스 토큰의 유효 기간은 ***5분에서 60분*** 사이입니다. Azure Database for MySQL에 로그인하기 직전에 액세스 토큰을 가져오는 것이 좋습니다. 다음 PowerShell 명령을 사용하여 토큰 유효성을 확인할 수 있습니다. 

```azurepowershell-interactive
$accessToken.ExpiresOn.DateTime
```

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>3단계: MySQL로 로그인할 경우 토큰을 암호로 사용

연결할 때 액세스 토큰을 MySQL 사용자 암호로 사용해야 합니다. MySQLWorkbench와 같은 GUI 클라이언트를 사용하는 경우 위에 설명된 메서드를 사용하여 토큰을 검색할 수 있습니다. 

#### <a name="using-mysql-cli"></a>MySQL CLI 사용
CLI를 사용하는 경우 이 축약형 표기법을 사용하여 연결할 수 있습니다. 

**예(Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```
#### <a name="using-mysql-workbench"></a>MySQL Workbench 사용
* MySQL Workbench를 시작하고 데이터베이스 옵션을 클릭한 다음 “데이터베이스에 연결”을 클릭
* 호스트 이름 필드에 다음과 같은 MySQL FQDN을 입력 mydb.mysql.database.azure.com
* 사용자 이름 필드에 MySQL Azure Active Directory 관리자 이름을 입력한 다음, FQDN이 아닌 MySQL 서버 이름을 추가(예: user@tenant.onmicrosoft.com @mydb)
* 암호 필드에서 “자격 증명 모음에 저장”을 클릭하고 파일(예: C:\temp\MySQLAccessToken.txt)의 액세스 토큰에 붙여넣기
* 고급 탭을 클릭하고 “Enable Cleartext Authentication Plugin”(일반 텍스트 인증 플러그 인 사용) 확인
* 확인을 클릭하여 데이터베이스에 연결

#### <a name="important-considerations-when-connecting"></a>연결 시 중요한 고려 사항은 다음과 같습니다.

* `user@tenant.onmicrosoft.com`은 연결하는 데 사용하려는 Azure AD 사용자 또는 그룹의 이름
* Azure AD 사용자/그룹 이름 다음에 항상 서버 이름 추가(예: `@mydb`)
* Azure AD 사용자 또는 그룹 이름의 철자를 똑같이 사용해야 함
* Azure AD 사용자 및 그룹 이름은 대/소문자를 구분함
* 그룹으로 연결할 때 그룹 이름만 사용(예: `GroupName@mydb`)
* 이름에 공백이 포함되어 있으면 각 공백 앞에 `\`를 사용하여 이스케이프합니다.

"enable-cleartext-plugin" 설정 - 다른 클라이언트와 유사한 구성을 사용하여 해시되지 않은 서버에 토큰이 전송되도록 해야 합니다.

이제 Azure AD 인증을 사용하여 MySQL 서버에 인증되었습니다.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 Azure AD 사용자 만들기

Azure Database for MySQL 데이터베이스에 Azure AD 사용자를 추가하려면 연결 후 다음 단계를 수행합니다(연결하는 방법은 다음 섹션 참조).

1. 먼저 Azure AD 사용자 `<user>@yourtenant.onmicrosoft.com`이 Azure AD 테넌트의 유효한 사용자인지 확인합니다.
2. Azure AD 관리 사용자로 Azure Database for MySQL 인스턴스에 로그인합니다.
3. Azure Database for MySQL에서 `<user>@yourtenant.onmicrosoft.com` 사용자를 만듭니다.

**예:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

32자를 초과하는 사용자 이름의 경우 연결할 때 별칭을 사용하는 것이 좋습니다. 

예제:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```
> [!NOTE]
> 1. MySQL은 선행 및 후행 공백을 무시 하므로 사용자 이름에 선행 또는 후행 공백이 포함 되 면 안 됩니다. 
> 2. Azure AD를 통해 사용자를 인증하여도 Azure Database for MySQL 데이터베이스 내의 개체에 액세스할 수 있는 권한이 사용자에게 부여되지 않습니다. 필요한 권한을 사용자에게 수동으로 부여해야 합니다.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 Azure AD 그룹 만들기

데이터베이스에 액세스할 수 있는 Azure AD 그룹을 사용하려면 사용자에 사용하는 동일한 메커니즘을 사용하고 그룹 이름을 지정합니다.

**예:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

로그인할 때 그룹 멤버는 개인용 액세스 토큰을 사용하지만 사용자 이름으로 지정된 그룹 이름으로 서명합니다.

## <a name="token-validation"></a>토큰 유효성 검사

Azure Database for MySQL의 Azure AD 인증으로 사용자가 MySQL 서버에 존재하는지 확인하고 토큰 콘텐츠의 유효성을 검사하여 토큰의 유효성을 확인합니다. 다음 토큰 유효성 검사 단계를 수행합니다.

-   토큰이 Azure AD에 의해 서명되고 변조되지 않았는지 검사
-   서버와 연결된 테넌트에 Azure AD에서 토큰을 발급했는지 검사
-   토큰이 만료되지 않았는지 검사
-   토큰이 다른 Azure 리소스가 아닌 Azure Database for MySQL 리소스용인지 검사

## <a name="compatibility-with-application-drivers"></a>애플리케이션 드라이버와의 호환성

대부분의 드라이버가 지원되지만 암호를 일반 텍스트로 보내도록 설정해야 하므로 토큰을 수정하지 않고 보냅니다.

* C/C++
  * libmysqlclient: 지원됨
  * mysql-connector-c++: 지원됨
* Java
  * 커넥터/J(mysql-connector-java): 지원됨, `useSSL` 설정을 활용해야 함
* Python
  * 커넥터/Python: 지원됨
* Ruby
  * mysql2: 지원됨
* .NET
  * mysql-connector-net: 지원됨, mysql_clear_password에 플러그 인을 추가해야 함
  * mysql-net/MySqlConnector: 지원됨
* Node.js
  * mysqljs: 지원되지 않음(패치 없이 일반 텍스트에서 토큰을 전송하지 않음)
  * node-mysql2: 지원됨
* Perl
  * DBD::mysql: 지원됨
  * Net::MySQL: 지원되지 않음
* Go
  * go-sql-driver: 지원됨, 연결 문자열에 `?tls=true&allowCleartextPasswords=true` 추가

## <a name="next-steps"></a>다음 단계

* [Azure Database for MySQL을 사용한 Azure Active Directory 인증](concepts-azure-ad-authentication.md)에 대한 전체 개념을 검토합니다.

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
