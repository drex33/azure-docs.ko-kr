---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 보안 구성
description: Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 보안 구성
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 8841c3abae51de0cfcd1391940f9232c4585c02f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536567"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 보안 구성

본 문서에서는 서버 그룹 보안에 관련된 다양한 측면에 대하여 설명합니다.
- 미사용 암호화
- 사용자 관리
   - 일반적인 큐브 뷰
   - _postgres_ 관리 사용자의 암호 변경
- 감사

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>미사용 암호화
데이터 베이스를 저장하는 디스크를 암호화하고/하거나 삽입 또는 업데이트하는 데이터를 암호화하는 데이터베이스 기능을 이용하여 미사용 암호화를 구현할 수 있습니다.

### <a name="hardware-linux-host-volume-encryption"></a>하드웨어: Linux 호스트 볼륨 암호화
시스템 데이터 암호화를 구현하여 Azure Arc 지원 Data Services 설정에서 사용하는 디스크 내의 모든 데이터를 보호합니다. 해당 토픽에 대한 자세한 설명은 다음을 확인하세요.
- Linux의 일반적인 [미사용 데이터 암호화](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) 
- `cryptsetup`암호화 명령(Linux)을 사용한 디스크 암호화(https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) 특히 Azure Arc 지원 데이터 서비스는 귀하가 제공하는 물리적 인프라에서 실행되기 때문에 인프라 보안을 담당해야 합니다.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>소프트웨어: 서버 그룹에 PostgreSQL `pgcrypto` 확장 사용하기
Azure Arc 설정을 호스팅하는 데 사용되는 디스크를 암호화하는 것 외에도 애플리케이션이 데이터베이스의 데이터를 암호화하는 데 사용할 수 있는 메커니즘을 노출하도록 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 구성할 수 있습니다. `pgcrypto`확장은 Postgres의`contrib` 확장 중 일부로, Azure Arc 지원 PostgreSQL Hyperscale 서버 그룹에서 사용할 수 있습니다. 해당`pgcrypto` 확장[에 대한 자세한 내용은 여기](https://www.postgresql.org/docs/current/pgcrypto.html)를 참조하세요.
요약하면, 다음 명령을 통하여 확장을 사용하도록 설정하고, 생성하여, 사용할 수 있습니다.


#### <a name="create-the-pgcrypto-extension"></a>`pgcrypto` 확장 만들기
선택한 클라이언트 도구를 사용하여 서버 그룹에 연결하고 표준 PostgreSQL 쿼리를 실행합니다.
```console
CREATE EXTENSION pgcrypto;
```

> [여기](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)에서 연결 방법에 대하여 자세한 내용을 확인할 수 있습니다.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>서버 그룹에서 사용할 준비가 된 확장 목록을 확인합니다.
서버 그룹에서 사용할 수 있는 확장을 나열하여 `pgcrypto` 확장을 사용할 준비가 되었는지 확인할 수 있습니다.
선택한 클라이언트 도구를 사용하여 서버 그룹에 연결하고 표준 PostgreSQL 쿼리를 실행합니다.
```console
select * from pg_extension;
```
위에 표시된 명령을 통하여 `pgcrypto`를 사용하도록 설정하고 만들었는지 확인하여야 합니다.

#### <a name="use-the-pgcrypto-extension"></a>`pgcrypto` 확장 사용
이제 애플리케이션의 코드를 조정하여 `pgcrypto`에서 제공하는 다음 함수를 사용할 수 있습니다.
- 일반 해시 함수
- 암호 해시 함수
- PGP 암호화 함수
- 원시 암호화 함수
- 임의 데이터 함수

예를 들어, 해시 값을 생성합니다. 명령 실행:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

다음 해시를 반환합니다.

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

또는 다음 예시가 있습니다.

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

다음 해시를 반환합니다.

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

또 다른 예시로, 암호 같이 암호화된 데이터를 저장합니다.

내 애플리케이션이 다음 표에 비밀을 저장한다고 가정합시다.

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

사용자를 만들 때 암호를 암호화합니다.

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

이제 암호가 암호화된 것을 볼 수 있습니다.

```console
select * from mysecrets;
```

출력:

- USERid: 1
- USERname: Me
- USERpassword: $1$Uc7jzZOp$NTfcGo7F10zGOkXOwjHy31

애플리케이션에 연결하여 암호를 전달하면 `mysecrets` 테이블에서 조회하고 해당 애플리케이션에 제공된 암호와 테이블 상에 저장된 암호가 일치하면 해당 사용자의 이름을 반환합니다. 예를 들면 다음과 같습니다.

- 잘못된 암호를 전달합니다.
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   출력 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- 올바른 암호를 전달합니다.

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   출력:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

소규모인 해당 예제는 Postgres `pgcrypto` 확장을 사용하여 Azure Arc 지원 PostgreSQL Hyperscale의 미사용 데이터를 암호화(암호화된 데이터를 저장)할 수 있고 애플리케이션은 이렇게 암호화된 데이터를 조종하기 위하여 `pgcrypto`가 제공한 함수를 사용할 수 있다는 것을 보여줍니다.

## <a name="user-management"></a>사용자 관리
### <a name="general-perspectives"></a>일반적인 큐브 뷰
사용자나 역할을 만들기 위하여 표준 Postgres 방식을 사용할 수 있습니다. 그러나 이 작업을 수행하는 경우 코디네이터 역할에서만 이러한 아티팩트를 사용할 수 있습니다. 미리 보기 중에 이러한 사용자/역할은 아직 코디네이터 노드 외부 및 서버 그룹의 작업자 노드에 배포된 데이터에 액세스할 수 없습니다. 그 이유는 미리 보기에서 사용자 정의가 작업자 노드에 복제되지 않기 때문입니다.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>_postgres_ 관리 사용자의 암호 변경
Azure Arc 지원 PostgreSQL 하이퍼스케일에는 서버 그룹을 만들 때 암호를 설정할 표준 Postgres 관리 사용자 _postgres_ 가 제공됩니다.
암호를 변경하는 명령의 일반적인 형식은 다음과 같습니다.
```azurecli
az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
```

여기서 `--admin-password`는 AZDATA_PASSWORD **세션** 환경 변수 내 값의 존재와 관련된 부울입니다.
AZDATA_PASSWORD **세션** 환경 변수가 존재하며 값이 있다면, 위 명령을 실행할 때 postgres 사용자의 암호를 해당 환경 변수의 해당 값으로 설정하게 됩니다.

AZDATA_PASSWORD **세션** 환경 변수의 값이 존재하되 값이 없거나, AZDATA_PASSWORD **세션** 환경 변수가 존재하지 않는다면, 위 명령을 실행할 때 사용자에게 대화형으로 암호를 입력하라는 메시지가 표시됩니다.

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>대화형 방식으로 postgres 관리 사용자의 암호 변경하기

1. AZDATA_PASSWORD **세션** 환경 변수를 삭제하거나 그 값을 삭제
2. 명령 실행:

   ```azurecli
   az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   예
   ```azurecli
   az postgres arc-server edit -n postgres01 --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   암호를 입력하고 확인하라는 메시지가 표시됩니다.
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   암호를 업데이트하는 동안 해당 명령이 다음과 같이 출력됩니다.
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>AZDATA_PASSWORD **세션** 환경 변수를 사용하여 postgres 관리 사용자의 암호를 변경합니다.
1. 암호로 원하는 값으로 AZDATA_PASSWORD **세션** 환경 변숫값을 설정합니다.
2. 다음 명령을 실행합니다.
   ```azurecli
   az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   예
   ```azurecli
   az postgres arc-server edit -n postgres01 --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   
   암호를 업데이트하는 동안 해당 명령이 다음과 같이 출력됩니다.
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> AZDATA_PASSWORD 세션의 환경 변수가 존재하며 어떤 값을 가지고 있는지 확인하려면 다음을 실행합니다.
> - Linux 클라이언트에서 다음을 실행합니다.
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - PowerShell을 사용하는 Windows 클라이언트에서는 다음을 실행합니다.
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>감사

감사 시나리오에서는 서버 그룹이 Postgres의 `pgaudit` 확장을 사용하도록 구성하세요. `pgaudit`에 대한 자세한 내용은 [`pgAudit` GitHub 프로젝트](https://github.com/pgaudit/pgaudit/blob/master/README.md)를 참조하세요. 서버 그룹에서 `pgaudit` 확장을 사용하도록 하려면 [PostgreSQL 확장 사용하기](using-extensions-in-postgresql-hyperscale-server-group.md)를 참조하세요.



## <a name="next-steps"></a>다음 단계
- [`pgcrypto` 확장](https://www.postgresql.org/docs/current/pgcrypto.html)을 참조하세요.
- [PostgreSQL 확장 사용하기](using-extensions-in-postgresql-hyperscale-server-group.md)를 참조하세요.

