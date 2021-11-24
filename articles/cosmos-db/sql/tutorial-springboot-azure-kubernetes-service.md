---
title: 자습서-Azure Cosmos DB SQL API 및 Azure Kubernetes Service를 사용한 스프링 부팅 응용 프로그램
description: 이 자습서에서는 Azure Kubernetes Service에 스프링 부팅 응용 프로그램을 배포 하 고이를 사용 하 여 Azure Cosmos DB SQL API 계정에서 데이터에 대 한 작업을 수행 하는 방법을 보여 줍니다.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: abhishgu
ms.openlocfilehash: ededae9bbe964bf7545fbfb7ac386b1386f5c64d
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077529"
---
# <a name="tutorial---spring-boot-application-with-azure-cosmos-db-sql-api-and-azure-kubernetes-service"></a>자습서-Azure Cosmos DB SQL API 및 Azure Kubernetes Service를 사용한 스프링 부팅 응용 프로그램
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 자습서에서는 Azure Cosmos DB (SQL API 계정)에서 데이터에 대 한 CRUD 작업을 수행 하기 위해 REST api를 노출 하는 스프링 부팅 응용 프로그램을 설정 하 고 배포 합니다. 응용 프로그램을 Docker 이미지로 패키지 하 고 Azure Container Registry에 푸시하고 Azure Kubernetes Service에 배포 하 고 응용 프로그램을 테스트 합니다.

## <a name="pre-requisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [무료 계정을](https://azure.microsoft.com/free/) 만들거나 Azure 구독 없이 [무료로 Azure Cosmos DB 사용해 보세요](https://azure.microsoft.com/try/cosmosdb/) .
- [JDK(Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). 사용자 `JAVA_HOME` 환경 변수를 JDK가 설치 된 경로를 가리키도록 지정 합니다.
- [Azure CLI](/cli/azure/install-azure-cli) 하 여 Azure 서비스를 프로 비전 합니다.
- 이미지 및 컨테이너를 관리 하는 [Docker](https://docs.docker.com/engine/install/) .
- [Maven](https://maven.apache.org/download.cgi) 및 [Git](https://www.git-scm.com/downloads)의 최신 버전입니다.
- 응용 [프로그램을 노출 하는](https://curl.se/download.html) REST api를 호출 합니다.

## <a name="provision-azure-services"></a>Azure 서비스 프로비전

이 섹션에서는이 자습서에 필요한 Azure 서비스를 만듭니다.

- Azure Cosmos DB
- Azure Container Registry
- Azure Kubernetes Service

### <a name="create-a-resource-group-for-the-azure-resources-used-in-this-tutorial"></a>이 자습서에서 사용 되는 Azure 리소스에 대 한 리소스 그룹 만들기

1. Azure CLI를 사용 하 여 Azure 계정에 로그인 합니다.

   ```azurecli
   az login
   ```

1. Azure 구독을 선택합니다.

   ```azurecli
   az account set -s <enter subscription ID>
   ```

1. 리소스 그룹을 만듭니다.

   ```azurecli
   az group create --name=cosmosdb-springboot-aks-rg --location=eastus
   ```

    > [!NOTE]
    > `cosmosdb-springboot-aks-rg`을 리소스 그룹의 고유한 이름으로 바꿉니다.
    
### <a name="create-an-azure-cosmos-db-sql-api-database-account"></a>Azure Cosmos DB SQL API 데이터베이스 계정 만들기

이 명령을 사용 하 여 Azure CLI를 사용 하 [Azure Cosmos DB SQL API 데이터베이스 계정을](manage-with-cli.md#create-an-azure-cosmos-db-account) 만듭니다.

```azurecli
az cosmosdb create --name <enter account name> --resource-group <enter resource group name>
```

### <a name="create-a-private-azure-container-registry-using-the-azure-cli"></a>Azure CLI를 사용 하 여 개인 Azure Container Registry 만들기

> [!NOTE]
> `cosmosdbspringbootregistry`를 레지스트리의 고유한 이름으로 바꿉니다.

```azurecli
az acr create --resource-group cosmosdb-springboot-aks-rg --location eastus \
    --name cosmosdbspringbootregistry --sku Basic
```

### <a name="create-a-kubernetes-cluster-on-azure-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure에서 Kubernetes 클러스터 만들기

1. 다음 명령은 Azure Container Registry (ACR)가 연결 된 *cosmosdb* -cosmosdb-springboot를 클러스터 이름 *으로 사용 하* 여 springboot-Aks 리소스 그룹에 Kubernetes 클러스터를 만듭니다 `cosmosdbspringbootregistry` .

    ```azurecli
    az aks create \
        --resource-group cosmosdb-springboot-aks-rg \
        --name cosmosdb-springboot-aks \
        --node-count 1 \
        --generate-ssh-keys \
        --attach-acr cosmosdbspringbootregistry \
        --dns-name-prefix=cosmosdb-springboot-aks-app
    ```

    이 명령을 완료하는 데 다소 시간이 걸릴 수 있습니다.

1. `kubectl`을 (를) 설치 하지 않은 경우 Azure CLI를 사용 하 여 수행할 수 있습니다.

   ```azurecli
   az aks install-cli
   ```

1. Azure Kubernetes 서비스 클러스터에 대 한 액세스 자격 증명을 가져옵니다.

   ```azurecli
   az aks get-credentials --resource-group=cosmosdb-springboot-aks-rg --name=cosmosdb-springboot-aks
   
   kubectl get nodes
   ```

## <a name="build-the-application"></a>애플리케이션 빌드

1. 응용 프로그램을 복제 하 고 올바른 디렉터리로 변경 합니다.

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-springboot-aks.git
    
    cd cosmosdb-springboot-aks
    ```

1. `Maven`응용 프로그램을 빌드하는 데 사용 합니다. 이 단계가 끝나면 폴더에 응용 프로그램 JAR 파일이 생성 되어야 합니다 `target` .

   ```bash
   ./mvnw install
   ```

## <a name="run-the-application-locally"></a>로컬에서 애플리케이션 실행

Azure Kubernetes Service에서 응용 프로그램을 실행 하려는 경우이 섹션을 건너뛰고 [Docker 이미지 푸시](#push-docker-image-to-azure-container-registry) 로 이동 하 여 Azure Container Registry

1. 응용 프로그램을 실행 하기 전에 `application.properties` Azure Cosmos DB 계정의 세부 정보를 사용 하 여 파일을 업데이트 합니다.

   ```properties
   azure.cosmos.uri=https://<enter cosmos db account name>.azure.com:443/
   azure.cosmos.key=<enter cosmos db primary key>
   azure.cosmos.database=<enter cosmos db database name>
   azure.cosmos.populateQueryMetrics=false
   ```

   > [!NOTE]
   > 응용 프로그램을 시작 하면 데이터베이스 및 컨테이너 (이라고 함 `users` )가 자동으로 만들어집니다.

1. 애플리케이션을 로컬로 실행합니다.

   ```bash
   java -jar target/*.jar
   ```

2. 응용 프로그램에 [대 한 액세스](#access-the-application) 를 계속 하거나 다음 섹션을 참조 하 여 응용 프로그램을 Kubernetes에 배포 합니다.

## <a name="push-docker-image-to-azure-container-registry"></a>Azure Container Registry에 Docker 이미지 푸시

1. Docker 이미지 빌드

   ```bash
   docker build -t cosmosdbspringbootregistry.azurecr.io/spring-cosmos-app:v1 .
   ```

   > [!NOTE]
   > 을 `cosmosdbspringbootregistry` Azure Container Registry 이름으로 바꿉니다.

1. Azure Container Registry에 로그인 합니다.

   ```bash
   az acr login -n cosmosdbspringbootregistry
   ```

1. Azure Container Registry에 이미지를 푸시하고 나열 합니다.

   ```bash
   docker push cosmosdbspringbootregistry.azurecr.io/spring-cosmos-app:v1

   az acr repository list --name cosmosdbspringbootregistry --output table
   ```

## <a name="deploy-application-to-azure-kubernetes-service"></a>Azure Kubernetes Service에 응용 프로그램 배포

1. `Secret` `app.yaml` Azure Cosmos DB 설정의 세부 정보를 사용 하 여를 편집 합니다.

    ```yml
    ...
    apiVersion: v1
    kind: Secret
    metadata:
      name: app-config
    type: Opaque
    stringData:
      application.properties: |
        azure.cosmos.uri=https://<enter cosmos db account name>.azure.com:443/
        azure.cosmos.key=<enter cosmos db primary key>
        azure.cosmos.database=<enter cosmos db database name>
        azure.cosmos.populateQueryMetrics=false
    ...
    ```

    > [!NOTE]
    > 응용 프로그램을 시작 하면 데이터베이스 및 컨테이너 ( `users` )가 자동으로 만들어집니다.


2. Kubernetes에 배포 하 고가 상태로 전환 될 때까지 기다립니다 `Pod` `Running` .

    ```bash
    kubectl apply -f deploy/app.yaml

    kubectl get pods -l=app=spring-cosmos-app -w
    ```

   > [!NOTE]
   > 다음을 사용 하 여 응용 프로그램 로그를 확인할 수 있습니다. `kubectl logs -f $(kubectl get pods -l=app=spring-cosmos-app -o=jsonpath='{.items[0].metadata.name}') -c spring-cosmos-app`


## <a name="access-the-application"></a>애플리케이션 액세스

응용 프로그램이 Kubernetes에서 실행 중이 고 포트를 통해 로컬에 액세스 하려는 경우 `8080` 아래 명령을 실행 합니다.

```bash
kubectl port-forward svc/spring-cosmos-app-internal 8080:8080
```

REST 끝점을 호출 하 여 응용 프로그램을 테스트 합니다. `Data Explorer`Azure Portal에서 Azure Cosmos DB 계정의 메뉴로 이동 하 고 컨테이너에 액세스 `users` 하 여 작업 결과를 확인할 수도 있습니다.

1. 새 사용자 만들기

    ```bash
    curl -i -X POST -H "Content-Type: application/json" -d '{"email":"john.doe@foobar.com", "firstName": "John", "lastName": "Doe", "city": "NYC"}' http://localhost:8080/users
    
    curl -i -X POST -H "Content-Type: application/json" -d '{"email":"mr.jim@foobar.com", "firstName": "mr", "lastName": "jim", "city": "Seattle"}' http://localhost:8080/users
    ```
    
    성공 하면 HTTP 응답을 받아야 합니다 `201` .

1. 사용자를 업데이트합니다.

    ```bash
    curl -i -X POST -H "Content-Type: application/json" -d '{"email":"john.doe@foobar.com", "firstName": "John", "lastName": "Doe", "city": "Dallas"}' http://localhost:8080/users
    ```

1. 모든 사용자 나열

    ```bash
    curl -i http://localhost:8080/users
    ```

1. 기존 사용자 가져오기

    ```bash
    curl -i http://localhost:8080/users/john.doe@foobar.com
    ```
    
    사용자 세부 정보를 사용 하 여 JSON 페이로드를 다시 가져와야 합니다. 예를 들면 다음과 같습니다.
    
    ```json
    {
      "email": "john.doe@foobar.com",
      "firstName": "John",
      "lastName": "Doe",
      "city": "Dallas"
    }
    ```

1. 존재 하지 않는 사용자를 가져오려고 시도 합니다.

    ```bash
    curl -i http://localhost:8080/users/not.there@foobar.com
    ```
    
    HTTP 응답을 받게 됩니다 `404` .

1. 사용자 바꾸기

    ```bash
    curl -i -X PUT -H "Content-Type: application/json" -d '{"email":"john.doe@foobar.com", "firstName": "john", "lastName": "doe","city": "New Jersey"}' http://localhost:8080/users/
    ```

1. 존재 하지 않는 사용자를 바꾸려고 시도 합니다.

    ```bash
    curl -i -X PUT -H "Content-Type: application/json" -d '{"email":"not.there@foobar.com", "firstName": "john", "lastName": "doe","city": "New Jersey"}' http://localhost:8080/users/
    ```
        
    HTTP 응답을 받게 됩니다 `404` .

1. 사용자 삭제

    ```bash
    curl -i -X DELETE http://localhost:8080/users/mr.jim@foobar.com
    ```

1. 존재 하지 않는 사용자를 삭제 합니다.

    ```bash
    curl -X DELETE http://localhost:8080/users/go.nuts@foobar.com
    ```
    
    HTTP 응답을 받게 됩니다 `404` .

### <a name="access-the-application-using-a-public-ip-address-optional"></a>공용 IP 주소를 사용 하 여 응용 프로그램에 액세스 (선택 사항)

`LoadBalancer`Azure Kubernetes service에서 형식의 서비스를 만들면 Azure Load Balancer 프로 비전 됩니다. 그런 다음 해당 공용 IP 주소를 사용 하 여 응용 프로그램에 액세스할 수 있습니다. 

1. 형식의 Kubernetes 서비스 만들기 `LoadBalancer`

    > [!NOTE]
    > 이렇게 하면 공용 IP 주소를 사용 하 여 Azure Load Balancer 만들어집니다.

    ```bash
    kubectl apply -f deploy/load-balancer-service.yaml
    ```

1. Azure Load Balancer 만들어질 때까지 기다립니다. 그때까지 `EXTERNAL-IP` Kubernetes 서비스에 대 한은 상태로 유지 됩니다 `<pending>` .

    ```bash
    kubectl get service spring-cosmos-app -w
    
    NAME                TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)          AGE
    spring-cosmos-app   LoadBalancer   10.0.68.83   <pending>     8080:31523/TCP   6s
    ```

    > [!NOTE]
    > `CLUSTER-IP` 사용자의 경우 값은 다를 수 있습니다.

1. Azure Load Balancer 생성이 완료 되 면를 `EXTERNAL-IP` 사용할 수 있습니다.

    ```bash
    NAME                TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)          AGE
    spring-cosmos-app   LoadBalancer   10.0.68.83   20.81.108.180   8080:31523/TCP   18s
    ```
   
   > [!NOTE]
   > `EXTERNAL-IP` 사용자의 경우 값은 다를 수 있습니다.

1. 공용 IP 주소 사용

   프로세스를 종료 `kubectl watch` 하 고 `curl` 포트와 함께 공용 IP 주소를 사용 하 여 위의 명령을 반복 합니다 `8080` . 예를 들어 모든 사용자를 나열 하려면 다음을 수행 합니다.

   ```bash
    curl -i http://20.81.108.180:8080/users
   ```
    
   > [!NOTE]
   > `20.81.108.180`사용자 환경에 대 한 공용 IP 주소로 대체 합니다.

## <a name="kubernetes-resources-for-the-application"></a>응용 프로그램에 대 한 리소스 Kubernetes

이 응용 프로그램에 대 한 Kubernetes 리소스와 관련 된 몇 가지 주요 사항은 다음과 같습니다.

- 스프링 부팅 응용 프로그램은 `Deployment` [Azure Container Registry의 Docker 이미지](https://github.com/Azure-Samples/cosmosdb-springboot-aks/blob/main/deploy/app.yaml#L21) 를 기반으로 하는 Kubernetes
- Azure Cosmos DB 구성은 `application.properties` `/config` [컨테이너 내부의](https://github.com/Azure-Samples/cosmosdb-springboot-aks/blob/main/deploy/app.yaml#L26)경로에 탑재 됩니다.
- [ `Volume` Kubernetes](https://github.com/Azure-Samples/cosmosdb-springboot-aks/blob/main/deploy/app.yaml#L15) 를 사용 하 여이 작업을 수행할 수 있습니다 .이는 응용 프로그램과 함께 생성 된 [Kubernetes 비밀](https://github.com/Azure-Samples/cosmosdb-springboot-aks/blob/main/deploy/app.yaml#L49)을 참조 합니다. 아래 명령을 실행 하 여이 파일이 응용 프로그램 컨테이너에 있는지 확인할 수 있습니다.

    ```bash
    kubectl exec -it $(kubectl get pods -l=app=spring-cosmos-app -o=jsonpath='{.items[0].metadata.name}') -c spring-cosmos-app -- cat /config/application.properties
    ```

- 이 응용 프로그램에 대 한 [선거의 및 준비 프로브](https://github.com/Azure-Samples/cosmosdb-springboot-aks/blob/main/deploy/app.yaml#L34) 구성은 스프링 부팅 응용 프로그램이 [Kubernetes 환경에 배포](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html#actuator.endpoints.kubernetes-probes) 되 고에 배포 될 때 [스프링 부팅 발동기](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html) 에서 사용할 수 있는 HTTP 끝점을 참조 합니다  -  `/actuator/health/liveness` `/actuator/health/readiness` . 
- Kubernetes 클러스터 내에서 *내부적으로* 스프링 부팅 응용 프로그램의 REST 끝점에 액세스 하기 위해 [clusterip 서비스](https://github.com/Azure-Samples/cosmosdb-springboot-aks/blob/main/deploy/app.yaml#L61) 를 만들 수 있습니다.
- 공용 IP 주소를 통해 응용 프로그램에 액세스 하기 위한 [LoadBalancer 서비스](https://github.com/Azure-Samples/cosmosdb-springboot-aks/blob/main/deploy/load-balancer-service.yaml) 를 만들 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Kubernetes Service에 스프링 부팅 응용 프로그램을 배포 하 고이를 사용 하 여 Azure Cosmos DB SQL API 계정에서 데이터에 대 한 작업을 수행 하는 방법을 알아보았습니다.

> [!div class="nextstepaction"]
> [SQL API 용 스프링 데이터 Azure Cosmos DB v3](sql-api-sdk-java-spring-v3.md)