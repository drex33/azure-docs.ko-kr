---
title: mydumper/myloader를 사용하여 큰 데이터베이스를 Azure Database for MySQL로 마이그레이션
description: 이 문서에서는 mydumper/myloader 도구를 사용하여 Azure Database for MySQL에서 데이터베이스를 백업 및 복원하는 2가지 일반적인 방법에 대해 설명합니다.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: e295e967b9164e9ab4744d18f407a18feb32481e
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538670"
---
# <a name="migrate-large-databases-to-azure-database-for-mysql-using-mydumpermyloader"></a>mydumper/myloader를 사용하여 큰 데이터베이스를 Azure Database for MySQL로 마이그레이션

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL은 클라우드에서 고가용성 MySQL 데이터베이스를 실행, 관리 및 확장하는 데 사용하는 관리형 서비스입니다. 1TB 보다 큰 MySQL 데이터베이스를 Azure Database for MySQL로 마이그레이션하려면 다음과 같은 이점을 제공하는 [mydumper/myloader](https://centminmod.com/mydumper.html)와 같은 커뮤니티 도구를 사용하는 것이 좋습니다.

* 마이그레이션 시간을 줄이는 데 도움이 되는 병렬 처리.
* 비용이 많이 드는 문자 집합 변환 루틴을 방지함으로써 성능 향상.
* 손쉬운 데이터 보기/구문 분석이 가능하도록 테이블, 메타데이터 등에 대한 별도의 파일을 포함한 출력 형식. 모든 스레드에 걸친 스냅샷 유지를 통한 일관성.
* 정확한 주 및 복제본 로그 위치.
* 데이터베이스 및 테이블 포함 및 제외 지정에 대해 PCRE(Perl Compatible Regular Expressions) 지원에 따른 손쉬운 관리.
* 스키마와 데이터가 함께 이동. 다른 논리적 마이그레이션 도구와 달리 별도로 처리할 필요가 없음.

이 빠른 시작에서는 mydumper/myloader를 사용하여 MySQL 데이터베이스를 설치, 백업 및 복원하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

MySQL 데이터베이스 마이그레이션을 시작하려면 먼저 다음을 수행해야 합니다.

1. [Azure Portal을 사용하여 Azure Database for MySQL 서버](./flexible-server/quickstart-create-server-portal.md)를 만듭니다.

2. [Azure Portal](../virtual-machines/linux/quick-create-portal.md)(Ubuntu 권장)을 사용하여 Linux를 실행하는 Azure VM을 만듭니다.
    > [!Note]
    > 도구를 설치하기 전에 다음 사항을 고려하세요.
    >
    > * 원본이 온-프레미스이고 Azure에 대한 높은 대역폭 연결을 사용하는 경우(ExpressRoute 사용) Azure VM에 도구를 설치하는 것이 좋습니다.<br> 
    > * 원본 및 대상 간의 대역폭에 문제가 있는 경우 원본 서버 근처에 mydumper를, 대상 서버 근처에 myloader를 설치하는 것이 좋습니다. **[Azcopy](../storage/common/storage-use-azcopy-v10.md)** 도구를 사용하여 데이터를 온-프레미스 또는 기타 클라우드 솔루션에서 Azure로 이동할 수 있습니다.

3. mysql 클라이언트를 설치하고 다음 단계를 수행합니다.

4. 

    * 다음 명령을 실행하여 Linux를 실행하는 Azure VM에서 패키지 인덱스를 업데이트합니다.
        ```bash
        $ sudo apt update
        ```
    * 다음 명령을 실행하여 mysql 클라이언트 패키지를 설치합니다.
        ```bash
        $ sudo apt install mysql-client
        ```

## <a name="install-mydumpermyloader"></a>mydumper/myloader 설치

mydumper/myloader를 설치하려면 다음 단계를 수행합니다.

1. OS 배포에 따라 적절한 mydumper/myloader 패키지를 다운로드하고 다음 명령을 실행합니다.
2. 
    ```bash
    $ wget https://github.com/maxbube/mydumper/releases/download/v0.10.1/mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Note]
    > $(lsb_release -cs)는 배포를 식별하는 데 도움이 됩니다.

3. mydumper용 .deb 패키지를 설치하려면 다음 명령을 실행합니다.

    ```bash
    $ dpkg -i mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Tip]
    > 설치 관리자가 서로 다르므로 패키지를 설치하는 데 사용하는 명령은 Linux 배포에 따라 다릅니다. mydumper/myloader는 Fedora, RedHat , Ubuntu, Debian, CentOS , openSUSE 및 MacOSX 배포에 대해 사용할 수 있습니다. 자세한 내용은 **[mydumper 설치 방법](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader)** 을 참조하세요.

## <a name="create-a-backup-using-mydumper"></a>mydumper를 사용하여 백업 만들기

* mydumper를 사용하여 백업을 만들려면 다음 명령을 실행합니다.

    ```bash
    $ mydumper --host=<servername> --user=<username> --password=<Password> --outputdir=./backup --rows=100000 --compress --build-empty-files --threads=16 --compress-protocol --trx-consistency-only --ssl  --regex '^(<Db_name>\.)' -L mydumper-logs.txt
    ```

이 명령에서는 다음 변수를 사용합니다.

* **--host:** 연결할 호스트
* **--user:** 필요한 권한이 있는 사용자 이름 
* **--password:** 사용자 암호
* **--rows:** 테이블을 여러 행의 청크로 분할 시도
* **--outputdir:** 출력 파일을 덤프할 디렉터리
* **--regex:** 데이터베이스 일치에 대한 정규식.
* **--trx-consistency-only:** 트랜잭션 일관성만
* **--threads:** 사용할 스레드 수. 기본값은 4. 컴퓨터의 vCore의 2배에 해당하는 값을 사용하는 것이 좋습니다.

    >[!Note] 
    >mydumper에서 사용할 수 있는 다른 옵션에 대해 자세히 알아보려면 다음 명령을 실행합니다. **mydumper --help**. 자세한 내용은 [mydumper\myloader 설명서](https://centminmod.com/mydumper.html)를 참조하세요.<br>
    여러 데이터베이스를 병렬로 덤프하려면 예시에 있는 regex 변수를 수정할 수 있습니다. **regex ’^(DbName1\.|DbName2\.)**

## <a name="restore-your-database-using-myloader"></a>myloader를 사용하여 데이터베이스 복원

* mydumper를 사용하여 백업한 데이터베이스를 복원하려면 다음 명령을 실행합니다.

    ```bash
    $ myloader --host=<servername> --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=500 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

이 명령에서는 다음 변수를 사용합니다.

* **--host:** 연결할 호스트
* **--user:** 필요한 권한이 있는 사용자 이름 
* **--password:** 사용자 암호
* **--directory:** 백업이 복원된 위치. 
* **--queries-per-transaction:** 500 미만의 값으로 설정하는 것이 좋습니다.
* **--threads:** 사용할 스레드 수. 기본값은 4. 컴퓨터의 vCore의 2배에 해당하는 값을 사용하는 것이 좋습니다.

> [!Tip]
> myloader에서 사용할 수 있는 다른 옵션에 대해 자세히 알아보려면 다음 명령을 실행합니다. **myloader --help**.

데이터베이스가 복원된 후에는 원본 데이터베이스와 대상 데이터베이스 간의 데이터 일관성의 유효성을 항상 검사하는 것이 좋습니다.

> [!Note]
> **[여기](https://github.com/maxbube/mydumper/issues)** 에서 mydumper/myloader 도구 관련 문제 또는 피드백을 제출하세요.

## <a name="next-steps"></a>다음 단계

* [GitHub의 mydumper/myloader 프로젝트](https://github.com/maxbube/mydumper)에 대해 알아봅니다.
* [대량 MySQL 데이터베이스를 마이그레이션하는 방법](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)에 대해 알아봅니다.
* [자습서: Azure Database for MySQL의 최소 가동 중지 시간 마이그레이션 – 단일 서버에서 Azure Database for MySQL로 - 유연한 서버](howto-migrate-single-flexible-minimum-downtime.md)
* 입력 데이터 복제에 대해 자세히 알아보기  [Azure Database for MySQL에 데이터 복제 - 유연한 서버](flexible-server/concepts-data-in-replication.md) 및 [Azure Database for MySQL Flexible Server 입력 데이터 복제 구성](./flexible-server/how-to-data-in-replication.md)
* 일반적으로 발생하는 [마이그레이션 오류](./howto-troubleshoot-common-errors.md)