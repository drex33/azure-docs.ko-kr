---
title: '자습서: VNet 내에서 MySQL 유연한 서버를 사용하여 AKS 클러스터에 Spring Boot 애플리케이션 배포'
description: VNet 내에서 보안 연결을 사용하여 Azure Database for MySQL - 유연한 서버를 사용하여 AKS에서 Spring Boot 애플리케이션을 신속하게 빌드하고 배포하는 방법을 알아봅니다.
ms.service: mysql
author: shreyaaithal
ms.author: shaithal
ms.topic: tutorial
ms.date: 11/11/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8b07ffd9ea4d9cbcba5cf36bcac048f1abccee37
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556942"
---
# <a name="tutorial-deploy-a-spring-boot-application-on-aks-cluster-with-mysql-flexible-server-in-a-vnet"></a>자습서: VNet에서 MySQL 유연한 서버를 사용하여 AKS 클러스터에 Spring Boot 애플리케이션 배포

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 자습서에서는 백 엔드에서 [Azure Database for MySQL](https://spring.io/projects/spring-boot) - 유연한 서버를 사용하여 [AKS(Azure Kubernetes Service)](../../aks/intro-kubernetes.md) 클러스터에 [Spring Boot](overview.md) 애플리케이션을 배포하고 Azure [가상 네트워크](../../virtual-network/virtual-networks-overview.md)내에서 서로 안전하게 통신하는 방법을 알아봅니다. 

> [!NOTE]
> 이 자습서에서는 Kubernetes 개념, Java Spring Boot 및 MySQL을 기본적으로 이해하고 있다고 가정합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독 [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- [Azure CLI(명령줄 인터페이스)](/cli/azure/install-azure-cli)
- 지원되는 [Java Development Kit](/azure/developer/java/fundamentals/java-support-on-azure) 버전 8(Azure Cloud Shell에 포함됨)입니다.
- [Apache Maven](https://maven.apache.org/) 빌드 도구.
- [Git](https://github.com/) 클라이언트
- [Docker](https://www.docker.com/) 클라이언트

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - 유연한 서버 만들기 

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. *eastus* 위치에서 [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹 *rg-mysqlaksdemo를* 만들어 보겠습니다.

1. 명령 프롬프트를 엽니다. 
1. Azure 계정에 로그인합니다.
    ```azurecli-interactive
    az login
    ```
1. Azure 구독을 선택합니다.
    ```azurecli-interactive
    az account set -s <your-subscription-ID>
    ```    
1. 리소스 그룹을 만듭니다.
    ```azurecli-interactive
    az group create --name rg-mysqlaksdemo --location eastus
    ```

### <a name="create-a-mysql-flexible-server"></a>MySQL 유연한 서버 만들기

이제 가상 네트워크(프라이빗 액세스 연결 방법)에 유연한 서버를 만들겠습니다.

1. 이 자습서의 모든 리소스에 대해 Azure 가상 네트워크 *vnet-mysqlaksdemo를* 만들고 MySQL 유연한 서버용 *서브넷 서브넷-mysql을* 만듭니다.

    ```azurecli-interactive
    az network vnet create \
    --resource-group rg-mysqlaksdemo \
    --name vnet-mysqlaksdemo \
    --address-prefixes 155.55.0.0/16 \
    --subnet-name subnet-mysql \
    --subnet-prefix 155.55.1.0/24 
    ```

1. az mysql flexible-server create 명령을 사용하여 위에서 만든 서브넷에 유연한 서버 *mysql-mysqlaksdemo* [Azure Database for MySQL 만듭니다.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create) 관리자 사용자 이름 및 암호에 대한 값을 대체합니다.

    ```azurecli-interactive
    az mysql flexible-server create \
    --name mysql-mysqlaksdemo \
    --resource-group rg-mysqlaksdemo \
    --location eastus \
    --admin-user <your-admin-username> \
    --admin-password <your-admin-password> \
    --vnet vnet-mysqlaksdemo \
    --subnet subnet-mysql
    ```
    
    이제 버스트 가능한 B1MS 컴퓨팅, 32GB 스토리지, 7일 백업 보존 기간 및 제공된 *서브넷 서브넷 서브넷 mysql을* 사용하여 eastus 지역에 유연한 서버를 만들었습니다. 이 서브넷에는 배포된 다른 리소스가 없어야 하며 Microsoft.DBforMySQL/flexibleServers에 위임됩니다.

1. Spring Boot 애플리케이션에서 사용할 새 MySQL ```demo``` 데이터베이스를 구성합니다.

    ```azurecli-interactive
    az mysql flexible-server db create \
    --resource-group rg-mysqlaksdemo \
    --server-name mysql-mysqlaksdemo \
    --database-name demo
    ```

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기 

리소스 그룹에서 프라이빗 Azure Container Registry를 만듭니다. 이 자습서에서는 이후 단계에서 샘플 앱을 Docker 이미지로 이 레지스트리에 푸시합니다. ```mysqlaksdemoregistry```를 레지스트리의 고유한 이름으로 바꿉니다.

```azurecli-interactive
az acr create --resource-group rg-mysqlaksdemo \
--location eastus \
--name mysqlaksdemoregistry \
--sku Basic
```

## <a name="code-the-application"></a>애플리케이션 코딩

이 섹션에서는 데모 애플리케이션을 코딩합니다. 더 빠르게 진행하려면 에서 사용할 수 있는 코딩된 애플리케이션을 [https://github.com/Azure-Samples/tutorial-springboot-mysql-aks](https://github.com/Azure-Samples/tutorial-springboot-mysql-aks) 다운로드하고 다음 섹션인 [이미지 빌드 및 ACR로 푸시로](#build-the-image-and-push-to-acr)건너뛸 수 있습니다. 

1. Spring Initializr를 사용하여 애플리케이션을 생성합니다. 

    ```bash
    curl https://start.spring.io/starter.tgz \
    -d dependencies=web,data-jdbc,mysql \
    -d baseDir=springboot-mysql-aks \
    -d bootVersion=2.5.6.RELEASE \
    -d artifactId=springboot-mysql-aks \
    -d description="Spring Boot on AKS connecting to Azure DB for MySQL" \
    -d javaVersion=1.8 | tar -xzvf -
    ```
    
    기본 Spring Boot 애플리케이션이 폴더 내에 ```springboot-mysql-aks``` 생성됩니다.
    
    다음 [단계에서는 VSCode](https://code.visualstudio.com/docs) 또는 IDE와 같은 선호하는 텍스트 편집기를 사용합니다.

1. Azure Database for MySQL - 유연한 서버를 사용하도록 Spring Boot 구성합니다.

    src/main/resources/application.properties 파일을 열고 아래 조각을 추가합니다. 이 코드는 Kubernetes 매니페스트 파일에서 데이터베이스 호스트, 데이터베이스 이름, 사용자 이름 및 암호를 읽습니다.
    
    ```properties
    logging.level.org.springframework.jdbc.core=DEBUG
    spring.datasource.url=jdbc:mysql://${DATABASE_HOST}:3306/${DATABASE_NAME}?serverTimezone=UTC
    spring.datasource.username=${DATABASE_USERNAME}
    spring.datasource.password=${DATABASE_PASSWORD}
    spring.datasource.initialization-mode=always
    ```
    >[!Warning]
    > ```spring.datasource.initialization-mode=always``` 구성 속성은 서버가 시작될 때마다 Spring Boot에서 나중에 만들 ```schema.sql``` 파일을 사용하여 데이터베이스 스키마를 자동으로 생성함을 의미합니다. 이는 테스트에 유용하지만, 다시 시작할 때마다 데이터가 삭제되므로 프로덕션에서 사용하면 안 됩니다!

    >[!Note]
    >```?serverTimezone=UTC```를 ```spring.datasource.url``` 구성 속성에 추가하여 데이터베이스에 연결할 때 UTC(협정 세계시) 날짜 형식을 사용하도록 JDBC 드라이버에 지시합니다. 그렇지 않으면 Java 서버에서 데이터베이스와 동일한 날짜 형식을 사용하지 않으므로 오류가 발생합니다.

1. 데이터베이스 스키마를 만듭니다.
    
    Spring Boot 자동으로 ```src/main/resources/schema.sql``` 실행되어 데이터베이스 스키마를 만듭니다. 다음 내용이 포함된 해당 파일을 만듭니다.

    ```sql
    DROP TABLE IF EXISTS todo;
    CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
    ```
1. Java Spring Boot 애플리케이션을 코딩합니다.
    
    JDBC를 사용하여 MySQL 서버에서 데이터를 저장하고 검색하는 Java 코드를 추가합니다. ```DemoApplication``` 클래스 옆에 새 ```Todo``` Java 클래스를 만들고 다음 코드를 추가합니다.

    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.data.annotation.Id;
    
    public class Todo {
    
        public Todo() {
        }
    
        public Todo(String description, String details, boolean done) {
            this.description = description;
            this.details = details;
            this.done = done;
        }
    
        @Id
        private Long id;
    
        private String description;
    
        private String details;
    
        private boolean done;
    
        public Long getId() {
            return id;
        }
    
        public void setId(Long id) {
            this.id = id;
        }
    
        public String getDescription() {
            return description;
        }
    
        public void setDescription(String description) {
            this.description = description;
        }
    
        public String getDetails() {
            return details;
        }
    
        public void setDetails(String details) {
            this.details = details;
        }
    
        public boolean isDone() {
            return done;
        }
    
        public void setDone(boolean done) {
            this.done = done;
        }
    }
    ```
    이 클래스는 이전에 만든 ```todo``` 테이블에 매핑된 도메인 모델입니다.

    해당 클래스를 관리하려면 리포지토리가 필요합니다. 동일한 패키지에 새 ```TodoRepository``` 인터페이스를 정의합니다.
    
    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.data.repository.CrudRepository;
    
    public interface TodoRepository extends CrudRepository<Todo, Long> {
    }
    ```

    이 리포지토리는 Spring Data JDBC에서 관리하는 리포지토리입니다.

    데이터를 저장하고 검색할 수 있는 컨트롤러를 만들어 애플리케이션을 완성합니다. 동일한 패키지에 ```TodoController``` 클래스를 구현하고 다음 코드를 추가합니다.
    
    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.http.HttpStatus;
    import org.springframework.web.bind.annotation.*;
    
    @RestController
    @RequestMapping("/")
    public class TodoController {
    
        private final TodoRepository todoRepository;
    
        public TodoController(TodoRepository todoRepository) {
            this.todoRepository = todoRepository;
        }
    
        @PostMapping("/")
        @ResponseStatus(HttpStatus.CREATED)
        public Todo createTodo(@RequestBody Todo todo) {
            return todoRepository.save(todo);
        }
    
        @GetMapping("/")
        public Iterable<Todo> getTodos() {
            return todoRepository.findAll();
        }
    }
    ```

1. 기본 디렉터리 *springboot-mysql-aks에* 새 Dockerfile을 만들고 이 코드 조각을 복사합니다.

    ```dockerfile
    FROM openjdk:8-jdk-alpine
    RUN addgroup -S spring && adduser -S spring -G spring
    USER spring:spring
    ARG DEPENDENCY=target/dependency
    COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
    COPY ${DEPENDENCY}/META-INF /app/META-INF
    COPY ${DEPENDENCY}/BOOT-INF/classes /app
    ENTRYPOINT ["java","-cp","app:app/lib/*","com.example.springbootmysqlaks.DemoApplication"]
    ```

1. *pom.xml* 파일로 이동하여 pom.xml 파일의 컬렉션을 Azure Container Registry 레지스트리 이름과 최신 버전의 로 ```<properties>``` 업데이트합니다. ```jib-maven-plugin```
    참고: ACR 이름에 대문자가 포함된 경우 소문자로 변환해야 합니다.

    ```xml
    <properties>
        <docker.image.prefix>mysqlaksdemoregistry.azurecr.io</docker.image.prefix>
        <jib-maven-plugin.version>3.1.4</jib-maven-plugin.version>
        <java.version>1.8</java.version>
    </properties>
    ```

1. ```<plugins>```아래와 같이 *에* 대한 항목을 포함하는 요소가 있도록pom.xml파일의 컬렉션을 ```<plugin>``` ```jib-maven-plugin``` 업데이트합니다. MCR(Microsoft Container Registry): ```mcr.microsoft.com/java/jdk:8-zulu-alpine```의 기본 이미지를 사용하고 있으며, 여기에는 공식적으로 지원되는 Azure용 JDK가 포함됩니다. 공식적으로 지원되는 JDK가 포함된 다른 MCR 기본 이미지는 [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless), [Java SE JDK 및 Maven](https://hub.docker.com/_/microsoft-java-maven)을 참조하세요.
    
    ```xml
    <plugin>
        <artifactId>jib-maven-plugin</artifactId>
        <groupId>com.google.cloud.tools</groupId>
        <version>${jib-maven-plugin.version}</version>
        <configuration>
            <from>
                <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
            </from>
            <to>
                <image>${docker.image.prefix}/${project.artifactId}</image>
            </to>
        </configuration>
    </plugin>
    ```

## <a name="build-the-image-and-push-to-acr"></a>이미지 빌드 및 ACR에 푸시

명령 프롬프트에서 *springboot-mysql-aks 폴더로* 이동하고 다음 명령을 실행하여 먼저 Azure Container Registry 기본 이름을 설정합니다(그렇지 않은 경우 에서 이름을 지정해야 ```az acr login``` 함). 이미지를 빌드한 다음, 이미지를 레지스트리에 푸시합니다.

이 단계를 실행하는 동안 Docker 디먼이 실행 중인지 확인합니다.

```azurecli-interactive
az config set defaults.acr=mysqlaksdemoregistry
az acr login && mvn compile jib:build
```

## <a name="create-a-kubernetes-cluster-on-aks"></a>AKS에서 Kubernetes 클러스터 만들기

이제 가상 네트워크 *vnet-mysqlaksdemo* 에 AKS 클러스터를 만듭니다. 

이 자습서에서는 AKS에서 Azure CNI 네트워킹을 사용합니다. 대신 kubenet 네트워킹을 구성하려면 [AKS에서 kubenet 네트워킹 사용을 참조하세요.](../../aks/configure-kubenet.md#create-a-service-principal-and-assign-permissions)  

1. AKS 클러스터에서 사용할 *서브넷 서브넷 aks를* 만듭니다.

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group rg-mysqlaksdemo \
    --vnet-name vnet-mysqlaksdemo \
    --name subnet-aks \
    --address-prefixes 155.55.2.0/24
    ```

1. 서브넷 리소스 ID를 확인합니다.

    ```azurecli-interactive
    SUBNET_ID=$(az network vnet subnet show --resource-group rg-mysqlaksdemo --vnet-name vnet-mysqlaksdemo --name subnet-aks --query id -o tsv)
    ```
    
1. ACR(Azure Container Registry) *mysqlaksdemoregistry가* 연결된 가상 네트워크에 AKS 클러스터를 만듭니다.

    ```azurecli-interactive
        az aks create \
        --resource-group rg-mysqlaksdemo \
        --name aks-mysqlaksdemo \
        --network-plugin azure \
        --service-cidr 10.0.0.0/16 \
        --dns-service-ip 10.0.0.10 \
        --docker-bridge-address 172.17.0.1/16 \
        --vnet-subnet-id $SUBNET_ID \
        --attach-acr mysqlaksdemoregistry \
        --dns-name-prefix aks-mysqlaksdemo \
        --generate-ssh-keys
    ```

    또한 다음 IP 주소 범위도 클러스터를 만드는 과정 중에 정의됩니다.
    
    - *--service-cidr* 을 사용하여 AKS 클러스터의 내부 서비스에 IP 주소를 할당합니다. 다음 요구 사항을 충족하는 모든 프라이빗 주소 범위를 사용할 수 있습니다.
        - 클러스터의 가상 네트워크 IP 주소 범위에 속하지 않아야 합니다.
        - 클러스터 가상 네트워크가 피어링된 다른 가상 네트워크와 겹치지 않아야 합니다.
        - 온-프레미스 IP와 겹치지 않아야 합니다.
        - 169.254.0.0/16, 172.30.0.0/16, 172.31.0.0/16 또는 192.0.2.0/24 범위에 속하지 않아야 합니다.
    
    - *--dns-service-ip* 주소는 클러스터의 DNS 서비스에 대한 IP 주소입니다. 이 주소는 *Kubernetes 서비스 주소 범위* 내에 있어야 합니다. 주소 범위의 첫 번째 IP 주소를 사용하지 마세요. 서브넷 범위의 첫 번째 주소는 *kubernetes.default.svc.cluster.local* 주소에 사용됩니다.
    
    - *--docker-bridge-address는* 모든 Docker 설치에 있는 기본 *docker0* 브리지 네트워크 주소를 나타내는 Docker 브리지 네트워크 주소입니다. 클러스터의 서비스 CIDR과 Pod CIDR 등 네트워크의 나머지 CIDR과 충돌하지 않는 주소 공간을 선택합니다.

## <a name="deploy-the-application-to-aks-cluster"></a>AKS 클러스터에 애플리케이션 배포

1. Azure Portal AKS 클러스터 리소스로 이동합니다.

1. 리소스 뷰(네임스페이스, 워크로드, 서비스 및 수신, Storage 또는 구성)에서 **YAML을** 통해 추가 및 **추가를** 선택합니다. 

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-resource-blade.png" alt-text="Azure Portal Azure Kubernetes Service 리소스 보기를 보여 주는 스크린샷.":::

1. 다음 YAML에 붙여넣습니다. MySQL 유연한 서버 관리자 사용자 이름 및 암호에 대한 값을 대체합니다.

    ```yml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: springboot-mysql-aks
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: springboot-mysql-aks
      template:
        metadata:
          labels:
            app: springboot-mysql-aks
        spec:
          containers:
          - name: springboot-mysql-aks
            image: mysqlaksdemoregistry.azurecr.io/springboot-mysql-aks:latest
            env:
            - name: DATABASE_HOST
              value: "mysql-mysqlaksdemo.mysql.database.azure.com"
            - name: DATABASE_USERNAME
              value: "<your-admin-username>"
            - name: DATABASE_PASSWORD
              value: "<your-admin-password>"
            - name: DATABASE_NAME    
              value: "demo"     
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: springboot-mysql-aks
    spec:
      type: LoadBalancer
      ports:
      - port: 80
        targetPort: 8080
      selector:
        app: springboot-mysql-aks
    ```
1. YAML 편집기의 맨 아래에 있는 **추가** 를 선택하여 애플리케이션을 배포합니다.

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-add-with-yaml.png" alt-text="YAML 편집기를 통해 추가를 보여 주는 스크린샷.":::

1. YAML 파일이 추가되면 리소스 뷰어에 Spring Boot 애플리케이션이 표시됩니다. 외부 서비스에 포함된 연결된 외부 IP 주소를 기록해 둡다.

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-external-ip.png" alt-text="Azure Kubernetes 클러스터 서비스 외부 IP의 Azure Portal 보기를 보여 주는 스크린샷":::

## <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션을 테스트할 때 cURL을 사용할 수 있습니다.

먼저 다음 명령을 사용하여 데이터베이스에 새 "todo" 항목을 만듭니다.

```bash
curl --header "Content-Type: application/json" \
--request POST \
--data '{"description":"configuration","details":"congratulations, you have deployed your application correctly!","done": "true"}' \
http://<AKS-service-external-ip>
```

다음으로, 새 cURL 요청을 사용하거나 브라우저에 클러스터 *외부 IP를* 입력하여 데이터를 검색합니다.

```bash
curl http://<AKS-service-external-ip>
```

이 명령은 만든 항목을 포함하여 "todo" 항목 목록을 반환합니다.

```json
[{"id":1,"description":"configuration","details":"congratulations, you have deployed your application correctly!","done":true}]
```

다음은 이러한 cURL 요청의 스크린샷입니다. :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-curl-output.png" alt-text="cURL 요청의 명령줄 출력을 보여 주는 스크린샷":::

브라우저를 통해 유사한 출력을 볼 수 있습니다. :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-browser-output.png" alt-text="브라우저 요청 출력을 보여 주는 스크린샷.":::


축하합니다! Azure Database for MySQL - 유연한 서버를 백 엔드에 사용하여 AKS(Azure Kubernetes Service) 클러스터에 Spring Boot 애플리케이션을 성공적으로 배포했습니다. 


## <a name="clean-up-the-resources"></a>리소스 정리

Azure 요금을 방지하려면 불필요한 리소스를 정리해야 합니다.  클러스터가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name rg-mysqlaksdemo
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제](../../aks/kubernetes-service-principal.md#additional-considerations)를 참조하세요. 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [MySQL을 통해 AKS에 WordPress 앱 배포](tutorial-deploy-wordpress-on-aks.md)

> [!div class="nextstepaction"]
> [MySQL로 PHP(Laravel) 웹앱 빌드](tutorial-php-database-app.md)


