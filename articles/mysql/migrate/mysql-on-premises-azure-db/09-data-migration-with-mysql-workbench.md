---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로의 마이그레이션: MySQL Workbench를 사용하여 데이터 마이그레이션'
description: 설치 가이드의 모든 단계에 따라 다음 단계를 지원하는 환경을 만듭니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 2b3dc8702251a6fcc53386cb17cbe44a45e59db2
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292967"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-data-migration-with-mysql-workbench"></a>MySQL 온-프레미스에서 Azure Database for MySQL로의 마이그레이션: MySQL Workbench를 사용하여 데이터 마이그레이션

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>사전 요구 사항

[데이터 마이그레이션](08-data-migration.md)

## <a name="setup"></a>설치 프로그램

설치 가이드의 모든 단계에 따라 다음 단계를 지원하는 환경을 만듭니다.

## <a name="configuring-server-parameters-source"></a>서버 매개 변수 구성(원본)

선택한 마이그레이션 유형(오프라인 및 온라인)에 따라 데이터의 빠른 송신을 지원하도록 서버 매개 변수를 수정할 것인지 여부를 평가할 수 있습니다. 온라인으로 작업하는 경우 `binlog` 복제를 수행하고 데이터가 자체적으로 동기화될 수 있으므로 서버 매개 변수에 대해 어떤 작업도 수행할 필요가 없을 수 있습니다. 그러나 오프라인 마이그레이션을 수행하는 경우 애플리케이션 트래픽을 중지한 후 서버 매개 변수를 워크로드 지원에서 내보내기 지원으로 전환할 수 있습니다.

## <a name="configuring-server-parameters-target"></a>서버 매개 변수 구성(대상)

Azure Database for MySQL로의 가져오기 프로세스를 시작하기 전에 서버 매개 변수를 검토합니다. 서버 매개 변수는 [Azure Portal](../../howto-server-parameters.md)을 사용하거나 [Azure PowerShell for MySQL cmdlet](../../howto-configure-server-parameters-using-powershell.md)을 호출하여 변경하는 방식으로 검색하고 설정할 수 있습니다.

다음 PowerShell 스크립트를 실행하여 모든 매개 변수를 가져옵니다.

```
\[Net.ServicePointManager\]::SecurityProtocol = \[Net.SecurityProtocolType\]::Tls
12

Install-Module -Name Az.MySql
Connect-AzAccount
$rgName = "{RESOURCE\_GROUP\_NAME}";
$serverName = "{SERVER\_NAME}";
Get-AzMySqlConfiguration -ResourceGroupName $rgName -ServerName $serverName
```

- mysql 도구로 동일한 작업을 수행하려면 [CA 루트 인증](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)을 c:\\temp로 다운로드합니다(이 디렉터리 만들기).

    > [!NOTE]
    > 이 인증서는 변경될 수 있습니다. 최신 인증서 정보에 대해서는 [Azure Database for MySQL에 안전하게 연결하기 위한 사용자 애플리케이션의 SSL 연결 구성](../../howto-configure-ssl.md)을 참조하세요.
    
- 명령 프롬프트에서 다음을 실행합니다. 토큰을 업데이트해야 합니다.

```
mysql --host {servername}.mysql.database.azure.com --database mysql --user 
{u sername}@{servername} -p --ssl-ca=c:\\temp\\BaltimoreCyberTrustRoot.crt.cer
-A -e "SHOW GLOBAL VARIABLES;" \> c:\\temp\\settings\_azure.txt
```

새 \`settings\_azure.txt\` 파일에서 [부록](15-appendix.md#environment-setup)에 표시된 것처럼 기본 Azure Database for MySQL 서버 매개 변수가 표시될 수 있습니다.

마이그레이션을 지원하려면 더 빠른 수신을 허용하도록 대상 MySQL 인스턴스 매개 변수를 설정합니다. 데이터 마이그레이션을 시작하기 전에 다음 서버 매개 변수를 설정해야 합니다.

- `max\_allowed\_packet` – 긴 행으로 인한 오버플로 문제를 방지하기 위해 매개 변수를 `1073741824`(즉, 1GB) 또는 데이터베이스에 있는 행의 최대 크기로 설정합니다. 끌어오거나 읽어야 하는 큰 BLOB 행이 있는 경우 이 매개 변수를 조정하는 것이 좋습니다.

- `innodb\_buffer\_pool\_size` – 마이그레이션 중에 포털의 가격 책정 계층에서 서버를 32개의 vCore 메모리 최적화 SKU로 확장하여 innodb\_buffer\_pool\_size를 늘립니다. Innodb\_buffer\_pool\_size는 Azure Database for MySQL 서버에 대한 컴퓨팅을 확장해야만 늘릴 수 있습니다. 계층의 최댓값에 대해서는 [Azure Database for MySQL의 서버 매개 변수](../../concepts-server-parameters.md#innodb_buffer_pool_size)를 참조하세요. 메모리 최적화 32 vCore 시스템의 최댓값은 `132070244352`입니다.

- `innodb\_io\_capacity` & `innodb\_io\_capacity\_max` - 매개 변수를 `9000`으로 변경하여 마이그레이션 속도를 최적화하도록 IO 사용률을 개선합니다.

- `max\_connections` - 처리량을 늘리기 위해 다중 스레드를 생성하는 도구를 사용하는 경우 해당 도구를 지원하도록 연결을 늘립니다. 기본값은 `151`이고 최댓값은 `5000`입니다.

    > [!NOTE]
    > 스케일링을 수행할 때는 주의해야 합니다. 스토리지 스케일링과 같은 일부 작업은 실행 취소할 수 없습니다.
    
이러한 설정은 아래 Azure PowerShell cmdlet을 사용하여 업데이트할 수 있습니다.

```
Install-Module -Name Az.MySql
$rgName = " {RESOURCE\_GROUP\_NAME}";
$serverName = "{SERVER\_NAME}";

Select-AzSubscription -Subscription "{SUBSCRIPTION\_ID}"
Update-AzMySqlConfiguration -Name max\_allowed\_packet -ResourceGroupName
$rgna me -ServerName $serverName -Value 1073741824  
Update-AzMySqlConfiguration -Name innodb\_buffer\_pool\_size -ResourceGroupName 
$rgname -ServerName $serverName -Value 16106127360
Update-AzMySqlConfiguration -Name innodb\_io\_capacity -ResourceGroupName 
$rgna me -ServerName $serverName -Value 9000
Update-AzMySqlConfiguration -Name innodb\_io\_capacity\_max -ResourceGroupName 
$ rgname -ServerName $serverName -Value 9000

\#required if You've functions

Update-AzMySqlConfiguration -Name log\_bin\_trust\_function\_creators 
-ResourceGr oupName $rgname -ServerName $serverName -Value ON
```
## <a name="data"></a>데이터

### <a name="tool-choice"></a>도구 선택

원본 시스템의 데이터베이스 개체 및 사용자를 마이그레이션한 상태에서 마이그레이션을 시작할 수 있습니다. MySQL 버전 8.0에서 실행되는 데이터베이스는 Azure DMS를 사용하여 워크로드를 마이그레이션할 수 없습니다. 대신 마이그레이션 사용자는 MySQL Workbench를 사용해야 합니다.

### <a name="manual-import-and-export-steps"></a>수동 가져오기 및 내보내기 단계

- MySQL Workbench를 열고 로컬 데이터베이스의 루트 사용자로 연결합니다.

- \*\*관리\*\*에서 \*\*데이터 내보내기\*\*를 선택합니다. **reg\_app** 스키마를 선택합니다.

- **내보낼 개체** 에서 **덤프 저장 프로시저 및 함수**, **덤프 이벤트** 및 **덤프 트리거** 를 선택합니다.

- **내보내기 옵션** 에서 **자체 포함 파일로 내보내기** 를 선택합니다.

- 또한 **스키마 만들기 포함** 확인란을 선택합니다. 올바른 mysqldump 구성을 확인하려면 아래 이미지를 참조하세요.

    ![스키마 만들기 포함](./media/image6.jpg)

    **테스트**

- 이러한 옵션 중 하나가 사용할 수 없는 것으로 표시되면 출력 창에서 이러한 옵션이 차단될 수 있습니다. 편집기 레이아웃만 변경합니다.

    ![편집기 레이아웃](./media/image7.jpg)

    **테스트**

- **내보내기 진행률** 탭을 선택합니다.

- **내보내기 시작** 을 선택합니다. MySQL Workbench에서 `mysqldump` 도구를 호출합니다.

- 새로 만든 내보내기 스크립트를 엽니다.

- `DEFINER` 문을 찾은 후 유효한 사용자로 변경하거나 완전히 제거합니다.

> [!NOTE]
> 이 작업은 mysqldump 명령에서 `--skip-definer`를 전달하여 수행할 수 있습니다. 이것은 MySQL Workbench에서 사용할 수 있는 옵션이 아니므로 export 명령에서 줄을 수동으로 제거해야 합니다. 여기서는 제거할 네 가지 항목을 나타내지만, 한 MySQL 버전에서 다른 버전으로 마이그레이션할 때 실패할 수 있는 다른 항목(예: 새 예약어)이 있을 수 있습니다.

- `SET GLOBAL` 문을 찾은 후 유효한 사용자로 변경하거나 완전히 제거합니다.

- `sql\_mode`가 `NO\_AUTO\_CREATE\_USER`로 설정되지 않았는지 확인합니다.

- `hello\_world` 함수를 제거합니다.

- MySQL Workbench에서 Azure Database for MySQL에 대한 새 연결을 만듭니다.

    - 호스트 이름에 대해 전체 서버 DNS(예: `servername.mysql.database.azure.com`)를 입력합니다.

    - 사용자 이름(예: `sqlroot@servername`)을 입력합니다.

    - **SSL** 탭을 선택합니다.

    - SSL CA 파일의 경우 **BaltimoreCyberTrustRoot.crt.cer** 키 파일로 이동합니다.

    - **연결 테스트** 를 선택하고 연결이 완료되었는지 확인합니다.

    - **확인** 을 선택합니다.

        ![MySQL 연결 대화 상자](./media/image8.jpg)

        **MySQL 연결 대화 상자가 표시됩니다.**

- **파일-\>SQL 스크립트 열기** 를 선택합니다.

- 덤프 파일을 찾아 **열기** 를 선택합니다.

- **실행** 을 선택합니다.

## <a name="update-applications-to-support-ssl"></a>SSL을 지원하도록 애플리케이션 업데이트

- Visual Studio Code에서 Java Server API로 전환합니다.

- **launch.json** 파일을 엽니다.

- **DB\_CONNECTION\_URL** 을 다음으로 업데이트합니다. `jdbc:mysql://serverDNSname:3306/reg\_app?useUnicode=true\&useJDBCCompliantT imezoneShift=true\&useLegacyDatetimeCode=false\&serverTimezone=UTC\&verifySe rverCertificate=true\&useSSL=true\&requireSSL=true\&noAccessToProcedureBodie s=true.` 추가 SSL 매개 변수를 확인합니다.

- **DB\_USER\_NAME** 을 **conferenceuser@servername** 으로 업데이트합니다.

- 디버그 구성을 시작하고 애플리케이션이 새 데이터베이스에서 로컬로 작동하는지 확인합니다.

## <a name="revert-server-parameters"></a>서버 매개 변수 되돌리기

Azure Database for MySQL 대상 인스턴스에서 다음 매개 변수를 변경할 수 있습니다. 이러한 매개 변수는 Azure Portal을 사용하거나 [Azure PowerShell for MySQL cmdlet](../../howto-configure-server-parameters-using-powershell.md)을 사용하여 설정할 수 있습니다.

```
$rgName = "YourRGName";
$serverName = "servername";
Update-AzMySqlConfiguration -Name max\_allowed\_packet -ResourceGroupName 
$rgna me -ServerName $serverName -Value 536870912
Update-AzMySqlConfiguration -Name innodb\_buffer\_pool\_size -ResourceGroupName 
$rgname -ServerName $serverName -Value 16106127360
Update-AzMySqlConfiguration -Name innodb\_io\_capacity -ResourceGroupName $rgna 
me -ServerName $serverName -Value 200
Update-AzMySqlConfiguration -Name innodb\_io\_capacity\_max -ResourceGroupName 
$ rgname -ServerName $serverName -Value 2000
```
## <a name="change-connection-string-for-the-java-api"></a>Java API에 대한 연결 문자열 변경

- 다음 명령을 사용하여 App Service Java API에 대한 연결 문자열을 변경합니다.

```
$rgName = "YourRGName"; 
$app_name = "servername";
az webapp config appsettings set -g $rgName -n $app_name 
--settings DB_CONNECTION_URL={DB_CONNECTION_URL}
```

> [!NOTE]
> Portal을 사용하여 연결 문자열을 설정할 수 있습니다.

- App Service API 다시 시작

```
az webapp restart -g $rgName -n $app\_name
```
온-프레미스에서 Azure Database for MySQL로의 마이그레이션을 완료했습니다\!  


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [마이그레이션 후 관리](./10-post-migration-management.md)