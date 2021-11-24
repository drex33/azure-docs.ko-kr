---
title: Docker를 사용하여 Azure SQL Edge 배포 - Azure SQL Edge
description: Docker를 사용한 Azure SQL Edge 배포에 대해 알아봅니다.
keywords: SQL Edge, 컨테이너, docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.custom: mode-other
ms.openlocfilehash: 3fe3070316c48eb8e75d7bcf0880b3d22e767aac
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133056598"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Docker를 사용하여 Azure SQL Edge 배포

이 빠른 시작에서는 Docker를 사용하여 Azure SQL Edge 컨테이너 이미지를 끌어와 실행합니다. 그런 다음, **sqlcmd** 로 연결하여 첫 번째 데이터베이스를 만들고 쿼리를 실행합니다.

이 이미지는 Ubuntu 18.04 기반의 Azure SQL Edge로 구성됩니다. Linux 또는 Mac/Windows용 Docker에서 Docker Engine 1.8+와 함께 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 지원되는 모든 Linux 배포판 또는 Mac/Windows용 Docker에서 Docker Engine 1.8+. 자세한 내용은 [사용자 Docker 설치](https://docs.docker.com/engine/installation/)를 참조하세요. Azure SQL Edge 이미지는 Ubuntu 18.04를 기반으로 하므로 Ubuntu 18.04 docker 호스트를 사용하는 것이 좋습니다.
- Docker **overlay2** 스토리지 드라이버. 대부분의 사용자에게 적용되는 기본값입니다. 이 스토리지 공급자를 사용하지 않고 변경해야 하는 경우 [overlay2 구성에 대한 docker 설명서](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver)의 지침 및 경고를 참조하세요.
- 최소 10GB의 디스크 공간.
- 최소 1GB의 RAM.
- [Azure SQL Edge의 하드웨어 요구 사항](./features.md#hardware-support)


## <a name="pull-and-run-the-container-image"></a>컨테이너 이미지를 끌어와 실행하기

다음 단계를 시작하기 전에 이 문서의 맨 위에서 기본 셸(bash, PowerShell 또는 cmd)을 선택했는지 확인합니다.

1. Microsoft Container Registry에서 Azure SQL Edge 컨테이너 이미지를 끌어옵니다.

    - Azure SQL Edge 컨테이너 이미지 끌어오기
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> 이 문서에서는 bash 명령으로 `sudo`를 사용합니다. macOS 및 Windows에서는 `sudo`가 필요하지 않을 수 있습니다. Linux에서 `sudo`를 사용하여 Docker를 실행하지 않으려는 경우 Docker 그룹을 구성하고 해당 그룹에 사용자를 추가할 수 있습니다. 자세한 내용은 [Linux용 설치 후 단계](https://docs.docker.com/engine/install/linux-postinstall/)를 참조하세요.

이전 명령은 최신 Azure SQL Edge 컨테이너 이미지를 끌어옵니다. 사용 가능한 모든 이미지를 보려면 [azure-sql-egde Docker 허브 페이지](https://hub.docker.com/_/microsoft-azure-sql-edge)를 참조하세요.

2. Docker를 사용하여 컨테이너 이미지를 실행하려면 bash 셸(Linux/macOS) 또는 상승된 권한 PowerShell 명령 프롬프트에서 다음 명령을 사용할 수 있습니다.
    
    - Developer Edition으로 실행되는 Azure SQL Edge 인스턴스 시작
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Premium Edition으로 실행되는 Azure SQL Edge 인스턴스 시작
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Windows에서 PowerShell을 사용하여 다음 명령을 실행하는 경우 작은따옴표 대신 큰따옴표를 사용합니다.


    > [!NOTE]
    > 암호는 Microsoft SQL 데이터베이스 엔진 기본 암호 정책을 따라야 합니다. 그렇지 않으면 컨테이너가 SQL 엔진을 설정할 수 없어 작동이 중지됩니다. 기본적으로 암호는 8자 이상이어야 하며 대문자, 소문자, 십진수 숫자 및 기호의 네 가지 집합 중 세 집합의 문자를 포함해야 합니다. [docker 로그](https://docs.docker.com/engine/reference/commandline/logs/) 명령을 실행하여 오류 로그를 검사할 수 있습니다.
    
    다음 표에서는 이전 `docker run` 보기의 매개 변수에 대해 설명합니다.

    | 매개 변수 | Description |
    |-----|-----|
    | **-e "ACCEPT_EULA=Y"** |  [최종 사용자 사용권 계약](https://go.microsoft.com/fwlink/?linkid=2139274) 수락을 확인하기 위해 **ACCEPT_EULA** 변수를 어떤 값에 설정합니다. Azure SQL Edge 이미지에 대한 필수 설정입니다. |
    | **-e "MSSQL_SA_PASSWORD=yourStrong(!)Password"** | 8자 이상이고 [Azure SQL Edge 암호 요구 사항](/sql/relational-databases/security/password-policy)을 충족하는 강력한 암호를 지정합니다. Azure SQL Edge 이미지에 대한 필수 설정입니다. |
    | **-p 1433:1433** | 호스트 환경의 TCP 포트(첫 번째 값)를 컨테이너의 TCP 포트(두 번째 값)로 매핑합니다. 이 예제에서 Azure SQL Edge는 컨테이너의 TCP 1433에서 수신 대기하고 호스트의 1433 포트에 공개됩니다. |
    | **--name azuresqledge** | 컨테이너에 대해 임의로 생성된 이름보다는 사용자 지정 이름을 지정합니다. 둘 이상의 컨테이너를 실행하는 경우 이 동일한 이름을 다시 사용할 수 없습니다. |
    | **-d** | 백그라운드(디먼)에서 컨테이너를 실행합니다. |

    모든 Azure SQL Edge 환경 변수의 전체 목록은 [환경 변수를 사용하여 Azure SQL Edge 구성](configure.md#configure-by-using-environment-variables)을 참조하세요. [mssql.conf 파일](configure.md#configure-by-using-an-mssqlconf-file)을 사용하여 Azure SQL Edge 컨테이너를 구성할 수도 있습니다.

3. Docker 컨테이너를 보려면 `docker ps` 명령을 사용합니다.
   
   ```bash
    sudo docker ps -a
   ```

4. **상태** 열이 **Up** 이면 Azure SQL Edge는 컨테이너에서 실행되고 **포트** 열에 지정된 포트에서 수신 대기합니다. Azure SQL Edge 컨테이너의 **상태** 열이 **Exited** 이면 Azure SQL Edge 설명서의 문제 해결 섹션을 참조하세요.

    `-h`(호스트 이름) 매개 변수도 유용하지만 간단한 설명을 위해 이 자습서에서 사용되지 않습니다. 이렇게 하면 컨테이너의 내부 이름이 사용자 지정 값으로 변경됩니다. 이 값은 다음 Transact SQL 쿼리에서 반환되는 이름입니다.

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    `-h` 및 `--name`을 같은 값에 설정하는 것은 대상 컨테이너를 쉽게 식별하는 데 유용한 좋은 방법입니다.

5. 마지막 단계로 SA 암호를 변경합니다. `SA_PASSWORD`는 `ps -eax` 출력에 표시되고 동일한 이름의 환경 변수에 저장되기 때문입니다. 아래 단계를 참조하세요.

## <a name="change-the-sa-password"></a>SA 암호 변경

**SA** 계정은 설치 중에 생성되는 Azure SQL Edge 인스턴스의 시스템 관리자입니다. Azure SQL Edge 컨테이너를 만든 후 컨테이너에서 `echo $SA_PASSWORD`를 실행하여 지정한 `MSSQL_SA_PASSWORD` 환경 변수를 검색할 수 있습니다. 보안을 위해 SA 암호를 변경합니다.

1. SA 사용자에게 사용할 강력한 암호를 선택합니다.

2. `docker exec`를 사용하여 **sqlcmd** 를 실행하고 Transact-SQL을 사용하여 암호를 변경합니다. 다음 예제에서는 이전 암호 `<YourStrong!Passw0rd>`와 새 암호 `<YourNewStrong!Passw0rd>`를 사용자 고유의 암호 값으로 바꿉니다.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Azure SQL Edge에 연결

다음 단계에서는 Azure SQL Edge에 연결하기 위해 컨테이너 내에서 Azure SQL Edge 명령줄 도구 **sqlcmd** 를 사용합니다.

> [!NOTE]
> sqlcmd 도구는 SQL Edge 컨테이너의 ARM64 버전 내에서 사용할 수 없습니다.

1. `docker exec -it` 명령을 사용하여 실행 중인 컨테이너 내에서 대화형 bash 셸을 시작합니다. 다음 예에서 `azuresqledge`은 컨테이너를 만들 때 `--name` 매개 변수가 지정한 이름입니다.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. 컨테이너 내부로 들어가면 sqlcmd를 사용하여 로컬로 연결합니다. Sqlcmd는 기본적으로 경로에 있지 않으므로 전체 경로를 지정해야 합니다.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > 명령줄에서 암호를 생략하여 입력하라는 메시지가 표시되도록 할 수 있습니다.

3. 성공하면 **sqlcmd** 명령 프롬프트 `1>`이 표시됩니다.

## <a name="create-and-query-data"></a>데이터 만들기 및 쿼리

다음 섹션에서는 **sqlcmd** 및 Transact-SQL을 사용하여 새 데이터베이스를 만들고, 데이터를 추가하고, 간단한 쿼리를 실행하는 단계를 안내합니다.

### <a name="create-a-new-database"></a>새 데이터베이스 만들기

다음 단계에서는 `TestDB`라는 새 데이터베이스를 만듭니다.

1. **sqlcmd** 명령 프롬프트에서 다음 Transact-SQL 명령을 붙여넣어 테스트 데이터베이스를 만듭니다.

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. 다음 줄에 서버에 있는 모든 데이터베이스의 이름을 반환하는 쿼리를 작성합니다.

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>데이터 삽입

다음으로 새 테이블 `Inventory`를 만들고 두 개의 새 행을 삽입합니다.

1. **sqlcmd** 명령 프롬프트에서 컨텍스트를 새 `TestDB` 데이터베이스로 전환합니다.

   ```sql
   USE TestDB
   ```

2. `Inventory`라는 새 테이블을 만듭니다.

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. 새 테이블에 데이터를 삽입합니다.

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. `GO`를 입력하여 앞의 명령을 실행합니다.

   ```sql
   GO
   ```

### <a name="select-data"></a>데이터 선택

이제 쿼리를 실행하여 `Inventory` 테이블에서 데이터를 반환합니다.

1. **sqlcmd** 명령 프롬프트에서 `Inventory` 테이블에서 수량이 152보다 큰 행을 반환하는 쿼리를 입력합니다.

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. 다음 명령을 실행합니다.

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>sqlcmd 명령 프롬프트 종료

1. **sqlcmd** 세션을 종료하려면 `QUIT`를 입력합니다.

   ```sql
   QUIT
   ```

2. 컨테이너에서 대화형 명령 프롬프트를 종료하려면 `exit`을 입력합니다. 컨테이너는 대화형 bash 셸을 종료한 후에도 계속 실행됩니다.

## <a name="connect-from-outside-the-container"></a> 컨테이너 외부에서 연결

SQL 연결을 지원하는 모든 외부 Linux, Windows 또는 macOS 도구에서 Docker 머신에 있는 Azure SQL Edge 인스턴스에 연결할 수 있습니다. 외부에서 SQL Edge 컨테이너에 연결하는 방법에 대한 자세한 내용은 [Azure SQL Edge 연결 및 쿼리](connect.md)를 참조하세요.

## <a name="remove-your-container"></a>컨테이너 제거

이 자습서에 사용되는 Azure SQL Edge 컨테이너를 제거하려면 다음 명령을 실행합니다.

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> 컨테이너를 영구적으로 중지하고 제거하면 컨테이너의 모든 Azure SQL Edge 데이터가 삭제됩니다. 데이터를 보존해야 하는 경우 [컨테이너에서 백업 파일을 만들고 복사](backup-restore.md)하거나 [컨테이너 데이터 지속성 기술](configure.md#persist-your-data)을 사용합니다.

## <a name="next-steps"></a>다음 단계

- [SQL Edge에 ONNX를 사용하는 Machine Learning 및 AI](onnx-overview.md)
- [IoT Edge를 사용하여 SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md).
- [Azure SQL Edge에서 데이터 스트리밍](stream-data.md)
