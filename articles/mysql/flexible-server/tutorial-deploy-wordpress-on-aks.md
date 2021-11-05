---
title: '자습서: Azure CLI를 사용하여 MySQL 유연한 서버를 통해 AKS 클러스터에 WordPress 배포'
description: Azure Database for MySQL - 유연한 서버를 사용하여 WordPress를 AKS에 빠르게 빌드하고 배포하는 방법을 알아봅니다.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: vc, devx-track-azurecli
ms.openlocfilehash: d311dbc14f5a2227f10a29a553adafdbbc28494c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472735"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>자습서: Azure Database for MySQL - 유연한 서버를 사용하여 AKS에 WordPress 앱 배포

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Database for MySQL - 유연한 서버를 통해 WordPress 애플리케이션을 AKS(Azure Kubernetes Service) 클러스터에 배포합니다.
**[AKS](../../aks/intro-kubernetes.md)** 는 클러스터를 빠르게 배포하고 관리할 수 있는 관리형 Kubernetes 서비스입니다. **[Azure Database for MySQL - 유연한 서버](overview.md)** 는 데이터베이스 관리 기능 및 구성 설정에 대한 보다 세부적인 제어와 유연성을 제공하도록 설계된 완전 관리형 데이터베이스 서비스입니다.

> [!NOTE]
> 이 빠른 시작에서는 Kubernetes 개념, WordPress 및 MySQL에 대한 기본 지식이 있다고 가정합니다.

[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 최신 버전의 Azure CLI가 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

> [!NOTE]
> 이 빠른 시작의 명령을 로컬로(Azure Cloud Shell 대신) 실행하는 경우 관리자 권한으로 명령을 실행해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. *eastus* 위치에서 [az group create][az-group-create] 명령을 사용하여 *wordpress-project* 리소스 그룹을 만들어 보겠습니다.

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> 리소스 그룹의 위치는 리소스 그룹 메타데이터가 저장되는 위치입니다. 또한 리소스를 만드는 동안 다른 지역을 지정하지 않은 경우 리소스가 Azure에서 실행되는 위치이기도 합니다.

다음 예제 출력에서는 성공적으로 만들어진 리소스 그룹이 나와 있습니다.

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS 클러스터 만들기

[az aks create](/cli/azure/aks#az_aks_create) 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 하나의 노드가 있는 *myAKSCluster* 라는 클러스터를 만듭니다. 이 작업을 완료하는 데는 몇 분 정도 걸립니다.

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
```

몇 분 후 명령이 완료되면 클러스터에 대한 JSON 형식 정보가 반환됩니다.

> [!NOTE]
> AKS 클러스터를 만들 때 AKS 리소스를 저장하기 위해 두 번째 리소스 그룹이 자동으로 만들어집니다. [AKS를 통해 두 개의 리소스 그룹이 만들어지는 이유는 무엇인가요?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)를 참조하세요.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터를 관리하려면 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. `kubectl`을 로컬로 설치하려면 [az aks install-cli](/cli/azure/aks#az_aks_install_cli) 명령을 사용합니다.

```azurecli-interactive
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
```

> [!NOTE]
> 위의 명령은 [Kubernetes 구성 파일](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)의 기본 위치인 `~/.kube/config`를 사용합니다. *--file* 을 사용하여 Kubernetes 구성 파일의 다른 위치를 지정할 수 있습니다.

클러스터에 대한 연결을 확인하려면 [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

다음 예제 출력은 이전 단계에서 만든 단일 노드를 보여줍니다. 노드 상태가 *준비* 인지 확인합니다.

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - 유연한 서버 만들기

[az mysql flexible-server create](/cli/azure/mysql/flexible-server) 명령을 사용하여 유연한 서버를 만듭니다. 다음 명령은 Azure CLI의 로컬 컨텍스트에 있는 서비스 기본값 및 값을 사용하여 서버를 만듭니다.

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

생성된 서버에는 다음과 같은 특성이 있습니다.

- 새 빈 데이터베이스(```flexibleserverdb```): 서버를 처음 프로비저닝하면 이 데이터베이스가 만들어집니다. 이 빠른 시작에서는 이 데이터베이스를 사용합니다.
- 자동 생성된 서버 이름, 관리 사용자 이름, 관리자 암호, 리소스 그룹 이름(로컬 컨텍스트에서 아직 지정되지 않은 경우): 리소스 그룹과 동일한 위치에 있음
- 나머지 서버 구성에 대한 서비스 기본값: 컴퓨팅 계층(버스트 가능), 컴퓨팅 크기/SKU(B1MS), 백업 보존 기간(7일) 및 MySQL 버전(5.7)
- 퍼블릭 액세스 인수를 사용하면 방화벽 규칙으로 보호되는 퍼블릭 액세스 권한이 있는 서버를 만들 수 있습니다. 클라이언트 컴퓨터에서 액세스를 허용하는 방화벽 규칙을 추가할 IP 주소를 제공합니다.
- 명령에서 로컬 컨텍스트를 사용하므로 ```wordpress-project``` 리소스 그룹 및 ```eastus``` 지역에 서버를 만듭니다.

### <a name="build-your-wordpress-docker-image"></a>WordPress Docker 이미지 빌드

[최신 WordPress](https://wordpress.org/download/) 버전을 다운로드합니다. 프로젝트에 대한 새 ```my-wordpress-app``` 디렉터리를 만들고, 다음과 같은 간단한 폴더 구조를 사용합니다.

```wordpress
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```

```wp-config-sample.php```의 이름을 ```wp-config.php```로 바꾸고 21~32번 줄을 다음 코드 조각으로 바꿉니다. 아래 코드 조각은 Kubernetes 매니페스트 파일에서 데이터베이스 호스트, 사용자 이름 및 암호를 읽습니다.

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Dockerfile 만들기

새 Dockerfile을 만들고 다음 코드 조각을 복사합니다. 이 Dockerfile은 PHP를 사용하여 Apache 웹 서버를 설정하고 mysqli 확장을 사용하도록 설정합니다.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>docker 이미지 빌드

```cd``` 명령을 사용하여 터미널의 ```my-wordpress-app``` 디렉터리에 있는지 확인합니다. 다음 명령을 실행하여 이미지를 빌드합니다.

``` bash

docker build --tag myblog:latest .

```

이미지를 [Docker 허브](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) 또는 [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md)에 배포합니다.

> [!IMPORTANT]
> ACR(Azure Container Registry)을 사용하는 경우 ```az aks update``` 명령을 실행하여 ACR 계정을 AKS 클러스터에 연결합니다.
>
> ```azurecli-interactive
> az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```

## <a name="create-kubernetes-manifest-file"></a>Kubernetes 매니페스트 파일 만들기

Kubernetes 매니페스트 파일은 어떤 컨테이너 이미지가 실행되는지 등과 같은 클러스터에 대해 원하는 상태를 정의합니다. `mywordpress.yaml`이라는 매니페스트 파일을 만들고, 다음 YAML 정의에 복사해 보겠습니다.

> [!IMPORTANT]
>
> - ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]```를 실제 WordPress docker 이미지 이름 및 태그(예: ```docker-hub-user/myblog:latest```)로 바꿉니다.
> - 아래 ```env``` 섹션을 MySQL 유연한 서버의 ```SERVERNAME```, ```YOUR-DATABASE-USERNAME```, ```YOUR-DATABASE-PASSWORD```로 업데이트합니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>AKS 클러스터에 WordPress 배포

[kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) 명령을 사용하여 애플리케이션을 배포하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f mywordpress.yaml
```

다음 예제 출력에는 성공적으로 만들어진 배포 및 서비스가 나와 있습니다.

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션이 실행되면 애플리케이션 프런트 엔드를 인터넷에 공개하는 Kubernetes 서비스가 만들어집니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 명령을 사용합니다.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

처음에는 *wordpress-blog* 서비스에 대한 *EXTERNAL-IP* 가 *pending* 으로 표시됩니다.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* 주소가 *보류 중* 에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다. 다음 예제 출력은 서비스에 할당된 유효한 공용 IP 주소를 보여줍니다.

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>WordPress 찾아보기

WordPress 설치 페이지를 보려면 웹 브라우저를 서비스의 외부 IP 주소로 엽니다.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="AKS 및 MySQL 유연한 서버에서 Wordpress 설치 성공":::

> [!NOTE]
>
> - WordPress 사이트는 현재 HTTPS를 사용하지 않습니다. [사용자 고유의 인증서를 사용하여 TLS를 사용하도록 설정](../../aks/ingress-own-tls.md)하는 것이 좋습니다.
> - [HTTP 라우팅](../../aks/http-application-routing.md)을 클러스터에 사용하도록 설정할 수 있습니다.

## <a name="clean-up-the-resources"></a>리소스 정리

Azure 요금을 방지하려면 불필요한 리소스를 정리해야 합니다.  클러스터가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제](../../aks/kubernetes-service-principal.md#additional-considerations)를 참조하세요. 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- AKS 클러스터에 대한 [Kubernetes 웹 대시보드에 액세스](../../aks/kubernetes-dashboard.md)하는 방법 알아보기
- [클러스터 크기를 조정](../../aks/tutorial-kubernetes-scale.md)하는 방법 알아보기
- [MySQL 유연한 서버를 관리](./quickstart-create-server-cli.md)하는 방법 알아보기
- 데이터베이스 서버에 대한 [서버 매개 변수를 구성](./how-to-configure-server-parameters-cli.md)하는 방법 알아보기
