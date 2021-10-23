---
title: Azure Portal을 사용하여 Azure SQL Edge 배포
description: Azure Portal을 사용하여 Azure SQL Edge를 배포하는 방법을 알아봅니다.
keywords: SQL Edge 배포
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: b801fdb7093e69853c01ed1922ff577962040ac5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262416"
---
# <a name="deploy-azure-sql-edge"></a>Azure SQL Edge 배포 

Azure SQL Edge는 IoT 및 Azure IoT Edge 배포에 최적화된 관계형 데이터베이스 엔진으로, IoT 애플리케이션과 솔루션을 위한 고성능 데이터 스토리지 및 처리 계층을 만드는 기능을 제공합니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Azure IoT Edge를 통해 Azure SQL Edge 모듈 만들기를 시작하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Portal](https://portal.azure.com/)에 로그인합니다.
* [Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md)를 만듭니다.
* [Azure IoT Edge 디바이스](../iot-edge/how-to-provision-single-device-linux-symmetric.md)를 만듭니다.

> [!NOTE]
> Azure Linux VM을 IoT Edge 디바이스로 배포하려면 이 [빠른 시작 가이드](../iot-edge/quickstart-linux.md)를 참조하세요.

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Azure Marketplace의 SQL Edge 모듈 배포

Azure Marketplace는 [IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)을 포함하여 Azure에서 실행되도록 인증되고 최적화된 광범위한 엔터프라이즈 애플리케이션 및 솔루션을 검색할 수 있는 온라인 애플리케이션 및 서비스 마켓플레이스입니다. Azure SQL Edge는 Marketplace를 통해 에지 디바이스에 배포할 수 있습니다.

1. Azure Marketplace에서 Azure SQL Edge 모듈을 찾습니다.<br><br>

   ![MarketPlace의 SQL Edge](media/deploy-portal/find-offer-marketplace.png)

2. 요구 사항에 가장 부합하는 소프트웨어 요금제를 선택하고 **만들기** 를 클릭합니다. <br><br>

   ![올바른 소프트웨어 요금제 선택](media/deploy-portal/pick-correct-plan.png)

3. IoT Edge 모듈의 대상 디바이스 페이지에서 다음 세부 정보를 지정한 후 **만들기** 를 클릭합니다.

   |**필드**  |**설명**  |
   |---------|---------|
   |Subscription  |  이 IoT Hub가 만들어진 Azure 구독 |
   |IoT Hub   |  IoT Edge 디바이스가 등록된 IoT Hub의 이름이며 "디바이스에 배포" 옵션 선택|
   |IoT Edge 디바이스 이름  |  SQL Edge를 배포할 IoT Edge 디바이스의 이름 |

4. **디바이스에서 모듈 설정:** 페이지에서 **IoT Edge 모듈** 아래에 있는 Azure SQL Edge 모듈을 클릭합니다. 기본 모듈 이름은 *AzureSQLEdge* 로 설정되어 있습니다. 

5. **IoT Edge 모듈 업데이트** 블레이드의 ‘모듈 설정’ 섹션에서 ‘IoT Edge 모듈 이름’, ‘다시 시작 정책’, ‘원하는 상태’의 값을 원하는 대로 지정합니다.    

   > [!IMPORTANT]    
   > 모듈의 **이미지 URI** 설정은 변경하거나 업데이트하지 마세요.

6. **IoT Edge 모듈 업데이트** 블레이드의 ‘환경 변수’ 섹션에서 환경 변수의 값을 원하는 대로 지정합니다. Azure SQL Edge 환경 변수의 전체 목록은 [환경 변수를 사용하여 구성](configure.md#configure-by-using-environment-variables)을 참조하세요. 다음은 모듈에 대해 정의되는 기본 환경 변수입니다. 

   |**매개 변수**  |**설명**|
   |---------|---------|
   | MSSQL_SA_PASSWORD  | 기본값을 변경하여 SQL Edge 관리자 계정에 대해 강력한 암호를 지정합니다. |
   | MSSQL_LCID   | 기본값을 변경하여 SQL Edge에 사용할 언어 ID를 원하는 대로 설정합니다. 예를 들어 1036은 프랑스어입니다. |
   | MSSQL_COLLATION | 기본값을 변경하여 SQL Edge의 기본 데이터 정렬을 설정합니다. 이 설정은 데이터 정렬에 대한 언어 ID(LCID)의 기본 매핑을 재정의합니다. |

   > [!IMPORTANT]    
   > 모듈의 **ACCEPT_EULA** 환경 변수는 변경하거나 업데이트하지 마세요.

7. **IoT Edge 모듈 업데이트** 블레이드의 ‘컨테이너 만들기 옵션’ 섹션에서 다음 옵션을 요구 사항에 따라 업데이트합니다. 
   - **호스트 포트**: 지정한 호스트 포트를 컨테이너의 포트 1433(기본 SQL 포트)에 매핑합니다.
   - **바인딩** 및 **탑재**: 둘 이상의 SQL Edge 모듈을 배포해야 하는 경우 탑재 옵션을 업데이트하여 영구적 볼륨의 새 원본 및 대상 쌍을 만들어야 합니다. 탑재 및 볼륨에 대한 자세한 내용은 docker 설명서의 [볼륨 사용](https://docs.docker.com/storage/volumes/)을 참조하세요. 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
        "Binds": [
            "sqlvolume:/sqlvolume"
        ],
        "PortBindings": {
            "1433/tcp": [
                {
                    "HostPort": "1433"
                }
            ]
        },
        "Mounts": [
            {
                "Type": "volume",
                "Source": "sqlvolume",
                "Target": "/var/opt/mssql"
            }
        ]
    },
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > 구성 만들기 설정에서 정의된 `PlanId` 환경 변수는 변경하지 마세요. 이 값을 변경하면 Azure SQL Edge 컨테이너가 시작되지 않습니다. 
   
8. **IoT Edge 모듈 업데이트** 창에서 **업데이트** 를 클릭합니다.
9. 배포 경로를 정의해야 하는 경우 **디바이스에서 모듈 설정** 페이지에서 **다음: 경로 >** 를 클릭합니다. 그렇지 않으면 **검토 + 만들기** 를 클릭합니다. 경로를 구성하는 방법에 대한 자세한 내용은 [IoT Edge에서 모듈 배포 및 경로 설정](../iot-edge/module-composition.md)을 참조하세요.
11. **디바이스에서 모듈 설정** 페이지에서 **만들기** 를 클릭합니다.

## <a name="connect-to-azure-sql-edge"></a>Azure SQL Edge에 연결

다음 단계에서는 Azure SQL Edge에 연결하기 위해 컨테이너 내에서 Azure SQL Edge 명령줄 도구 **sqlcmd** 를 사용합니다.

> [!NOTE]      
> ARM64 버전의 Azure SQL Edge 컨테이너 내에서는 SQL 명령줄 도구(sqlcmd)를 사용할 수 없습니다.

1. `docker exec -it` 명령을 사용하여 실행 중인 컨테이너 내에서 대화형 bash 셸을 시작합니다. 다음 예제에서 `azuresqledge`는 IoT Edge 모듈의 `Name` 매개 변수로 지정된 이름입니다.

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

다음 섹션에서는 **sqlcmd** 및 Transact-SQL을 사용하여 새 데이터베이스를 만들고, 데이터를 추가하고, 쿼리를 실행하는 단계를 안내합니다.

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

SQL 연결을 지원하는 모든 외부 Linux, Windows 또는 macOS 도구에서 Azure SQL Edge 인스턴스에 연결하고 SQL 쿼리를 실행할 수 있습니다. 외부에서 SQL Edge 컨테이너에 연결하는 방법에 대한 자세한 내용은 [Azure SQL Edge 연결 및 쿼리](./connect.md)를 참조하세요.

이 빠른 시작에서는 IoT Edge 디바이스에 SQL Edge 모듈을 배포했습니다. 

## <a name="next-steps"></a>다음 단계

- [SQL Edge의 ONNX를 사용하는 Machine Learning 및 AI](onnx-overview.md)
- [IoT Edge를 사용하여 SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md)
- [Azure SQL Edge에서 데이터 스트리밍](stream-data.md)
- [배포 오류 문제 해결](troubleshoot.md)