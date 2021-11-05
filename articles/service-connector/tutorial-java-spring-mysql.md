---
title: '자습서: Service Connector를 사용 하여 Azure Database for MySQL에 연결된 Spring Cloud 애플리케이션 배포'
description: Service Connector를 사용하여 Azure Database for MySQL에 연결된 Spring Boot 애플리케이션을 만듭니다.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f25ab8e1eb144675567316194c9b4ca3a547f111
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475239"
---
# <a name="tutorial-deploy-spring-cloud-application-connected-to-azure-database-for-mysql-with-service-connector"></a>자습서: Service Connector를 사용 하여 Azure Database for MySQL에 연결된 Spring Cloud 애플리케이션 배포

이 자습서에서는 Azure Portal 또는 Azure CLI를 사용하여 다음 작업을 완료합니다. 두 방법 모두 다음 절차에 설명되어 있습니다.

> [!div class="checklist"]
> * Azure Spring Cloud의 인스턴스 프로비저닝
> * Azure Spring Cloud에 앱 빌드 및 배포
> * 서비스 커넥터를 사용하여 Azure Database for MySQL와 Azure Spring Cloud 통합

## <a name="1-prerequisites"></a>1. 사전 요구 사항

* [JDK 8 또는 JDK 11 설치](/azure/developer/java/fundamentals/java-jdk-install)
* [Azure 구독에 가입](https://azure.microsoft.com/free/)
* [Azure CLI 버전 2.0.67 이상을 설치](/cli/azure/install-azure-cli)하고 `az extension add --name spring-cloud` 명령을 사용하여 Azure Spring Cloud 확장을 설치합니다.

## <a name="2-provision-an-instance-of-azure-spring-cloud"></a>2. Azure Spring Cloud의 인스턴스 프로비전

다음 절차에서는 Azure CLI 확장을 사용하여 Azure Spring Cloud의 인스턴스를 프로비저닝합니다.

1. Azure Spring Cloud 확장을 사용하여 Azure CLI를 업데이트합니다.

    ```azurecli
    az extension update --name spring-cloud
    ```

1. Azure CLI에 로그인하고 활성 구독을 선택합니다.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Azure Spring Cloud 서비스의 이름을 준비합니다.  이름은 4-32자 사이여야 하며, 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다.  서비스 이름의 첫 글자는 문자여야 하며 마지막 문자는 문자 또는 숫자여야 합니다.

1. Azure Spring Cloud 서비스를 포함할 리소스 그룹을 만듭니다.  Azure Spring Cloud 서비스의 인스턴스에서 만듭니다.

    ```azurecli
    az group create --name ServiceConnector-tutorial-rg
    az spring-cloud create -n <service instance name> -g ServiceConnector-tutorial-rg
    ```

## <a name="3-create-an-azure-database-for-mysql"></a>3. Azure Database for MySQL 만들기

다음 절차에서는 Azure CLI 확장을 사용하여 Azure Spring Cloud의 인스턴스를 프로비저닝합니다.

1. [db-up](/cli/azure/ext/db-up/mysql) 확장을 설치합니다.

```azurecli
az extension add --name db-up
```

다음 명령을 사용하여 Azure Database for MySQL 서버를 만듭니다.

```azurecli
az mysql up --resource-group ServiceConnector-tutorial-rg --admin-user <admin-username> --admin-password <admin-password>
```

- *\<admin-username>* 및 *\<admin-password>* 의 경우 이 MySQL 서버에 대한 관리자 사용자를 만들기 위한 자격 증명을 지정합니다. 관리 사용자 이름은 *azure_superuser*, *azure_pg_admin*, *admin*, *administrator*, *root*, *guest* 또는 *public* 이 될 수 없습니다. *pg_* 로 시작할 수 없습니다. 암호는 다음 세 가지 범주에서 **8~128자** 를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(예: !, #, %). 암호는 사용자 이름을 포함할 수 없습니다.

수동으로 재정의하지 않는 한 서버는 다음과 같은 기본값으로 만들어집니다.

**설정** | **기본값** | **설명**
---|---|---
서버 이름 | 시스템 생성 | Azure Database for MySQL 서버를 식별하는 고유한 이름입니다.
sku-name | GP_Gen5_2 | SKU의 이름입니다. {가격 책정 계층}\_{계산 세대}\_{vCores} 규칙을 축약형으로 따릅니다. 기본값은 2개의 vCore가 있는 범용 5세대 서버입니다. 계층에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/mysql/)를 참조하세요.
backup-retention | 7 | 백업을 보존하는 기간입니다. 단위는 일입니다.
geo-redundant-backup | 사용 안 함 | 이 서버에 지역 중복 백업을 사용할 것인지 여부를 결정합니다.
위치 | westus2 | 서버에 대한 Azure 위치입니다.
ssl-enforcement | 사용 | 이 서버에 SSL을 사용할 것인지 여부를 결정합니다.
storage-size | 5120 | 서버의 스토리지 용량입니다(단위는 메가바이트).
버전 | 5.7 | MySQL 주 버전입니다.


> [!NOTE]
> `az mysql up` 명령과 해당 추가 매개 변수에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure/mysql#az_mysql_up)를 참조하세요.

서버가 만들어지면 다음과 같은 설정이 제공됩니다.

- “devbox”라는 방화벽 규칙이 만들어집니다. Azure CLI는 `az mysql up` 명령이 실행되는 머신의 IP 주소를 검색하여 해당 IP 주소를 허용합니다.
- “Azure 서비스에 대한 액세스 허용”이 켜기로 설정됩니다. 이 설정은 구독에 없는 리소스를 포함하여 모든 Azure 리소스로부터의 연결을 허용하도록 서버의 방화벽을 구성합니다.
- `wait_timeout` 매개 변수가 8시간으로 설정됩니다.
- `sampledb`(이)라는 빈 데이터베이스가 만들어집니다.
- `sampledb`에 대한 권한이 있는 “root”라는 새 사용자가 만들어집니다.

## <a name="4-build-and-deploy-the-app"></a>4. 앱 빌드 및 배포

1. 퍼블릭 엔드포인트가 할당된 앱을 만듭니다. Spring Cloud 프로젝트를 생성할 때 Java 버전 11을 선택한 경우 `--runtime-version=Java_11` 스위치를 포함합니다.

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g ServiceConnector-tutorial-rg --assign-endpoint true
    ```

1. Spring Cloud와 MySQL 데이터베이스 간의 서비스 연결을 만듭니다.

    ```azurecli
    az spring-cloud connection create mysql
    ```

    > [!NOTE]
    > "구독이 Microsoft.ServiceLinker를 사용하도록 등록되어 있지 않습니다."라는 오류 메시지가 표시되면 `az provider register -n Microsoft.ServiceLinker`를 실행하여 Service Connector 리소스 공급자를 등록하고 연결 명령을 다시 실행합니다. 

1. 샘플 코드 복제

    ```bash
    git clone https://github.com/Azure-Samples/serviceconnector-springcloud-mysql-springboot.git
    ```

1. Maven을 사용하여 프로젝트를 빌드합니다.

    ```bash
    cd serviceconnector-springcloud-mysql-springboot
    mvn clean package -DskipTests 
    ```

1. 앱에 대한 Jar 파일을 배포합니다(`target/demo-0.0.1-SNAPSHOT.jar`).

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g ServiceConnector-tutorial-rg  --artifact-path target/demo-0.0.1-SNAPSHOT.jar
    ```

1. 다음 명령을 사용하여 배포 후 앱 상태를 쿼리합니다.

    ```azurecli
    az spring-cloud app list -o table
    ```

    다음과 유사한 출력이 표시됩니다.

    ```
    Name               Location    ResourceGroup    Production Deployment    Public Url                                           Provisioning Status    CPU    Memory    Running Instance    Registered Instance    Persistent Storage
    -----------------  ----------  ---------------  -----------------------  ---------------------------------------------------  ---------------------  -----  --------  ------------------  ---------------------  --------------------
    hellospring         eastus    <resource group>   default                                                                       Succeeded              1      2         1/1                 0/1                    -
    ```

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
