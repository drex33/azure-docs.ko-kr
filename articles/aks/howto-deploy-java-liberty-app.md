---
title: Open Liberty 또는 WebSphere Liberty를 사용하여 AKS(Azure Kubernetes Service) 클러스터에 Java 애플리케이션 배포
description: Open Liberty 또는 WebSphere Liberty를 사용하여 AKS(Azure Kubernetes Service) 클러스터에 Java 애플리케이션을 배포합니다.
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aks, kubernetes
ms.custom:
- devx-track-java
- devx-track-javaee
- devx-track-javaee-liberty
- devx-track-javaee-liberty-aks
ms.openlocfilehash: a55ddf7530b57442289dd734d299855e7c506e8b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000899"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Open Liberty 또는 WebSphere Liberty를 사용하여 AKS(Azure Kubernetes Service) 클러스터에 Java 애플리케이션 배포

이 문서에서는 다음을 수행하는 방법을 보여줍니다.  
* Open Liberty 또는 WebSphere Liberty 런타임에서 Java, Java EE, Jakarta EE 또는 MicroProfile 애플리케이션을 실행합니다.
* Open Liberty 컨테이너 이미지를 사용하여 애플리케이션 Docker 이미지를 빌드합니다.
* Open Liberty Operator를 사용하여 AKS 클러스터에 컨테이너화된 애플리케이션을 배포합니다.   

Open Liberty Operator는 Kubernetes 클러스터에서 실행되는 애플리케이션의 배포 및 관리를 간소화합니다. Open Liberty Operator를 사용하여 추적 및 덤프 수집과 같은 고급 작업을 수행할 수도 있습니다. 

Open Liberty에 대한 자세한 내용은 [Open Liberty 프로젝트 페이지](https://openliberty.io/)를 참조하세요. IBM WebSphere Liberty에 대한 자세한 내용은 [WebSphere Liberty 제품 페이지](https://www.ibm.com/cloud/websphere-liberty)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 이 문서에는 최신 버전의 Azure CLI가 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.
* 이 가이드의 명령을 (Azure Cloud Shell 대신) 로컬로 실행하는 경우:
  * Unix와 비슷한 운영 체제가 설치된 로컬 컴퓨터를 준비합니다(예: Ubuntu, macOS, Linux용 Windows 하위 시스템).
  * Java SE 구현을 설치합니다(예: [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * [Maven](https://maven.apache.org/download.cgi) 3.5.0 이상을 설치합니다.
  * 해당 OS용 [Docker](https://docs.docker.com/get-docker/)를 설치합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다.  

*eastus* 위치에서 [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 *java-liberty-project* 라는 리소스 그룹을 만듭니다. 이 리소스 그룹은 나중에 ACR(Azure Container Registry) 인스턴스 및 AKS 클러스터를 만드는 데 사용됩니다. 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>ACR 인스턴스 만들기

[az acr create](/cli/azure/acr#az_acr_create) 명령을 사용하여 ACR 인스턴스를 만듭니다. 다음 예제는 *youruniqueacrname* 이라는 ACR 인스턴스를 만듭니다. *youruniqueacrname* 은 Azure 내에서 고유해야 합니다.

```azurecli-interactive
export REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

잠시 후 다음을 포함하는 JSON 출력이 표시됩니다.

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>ACR 인스턴스에 연결

이미지를 ACR 인스턴스에 밀어넣으려면 먼저 로그인해야 합니다. 다음 명령을 실행하여 연결을 확인합니다.

```azurecli-interactive
export LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
export USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
export PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

ACR 인스턴스에 성공적으로 로그인한 경우 명령 출력의 끝에 `Login Succeeded`가 표시됩니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

[az aks create](/cli/azure/aks#az_aks_create) 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 하나의 노드가 있는 *myAKSCluster* 라는 클러스터를 만듭니다. 이 작업을 완료하는 데는 몇 분 정도 걸립니다.

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

몇 분 후 명령이 완료되면 다음을 포함하여 클러스터에 대한 JSON 형식 정보가 반환됩니다.

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>AKS 클러스터에 연결

Kubernetes 클러스터를 관리하려면 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. `kubectl`을 로컬로 설치하려면 [az aks install-cli](/cli/azure/aks#az_aks_install_cli) 명령을 사용합니다.

```azurecli-interactive
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> 위의 명령은 [Kubernetes 구성 파일](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)의 기본 위치인 `~/.kube/config`를 사용합니다. *--file* 을 사용하여 Kubernetes 구성 파일의 다른 위치를 지정할 수 있습니다.

클러스터에 대한 연결을 확인하려면 [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

다음 예제 출력은 이전 단계에서 만든 단일 노드를 보여줍니다. 노드 상태가 *준비* 인지 확인합니다.

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Open Liberty Operator 설치

클러스터를 만들고 연결한 후 다음 명령을 실행하여 [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.1)를 설치합니다.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.1/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>애플리케이션 이미지 빌드

AKS 클러스터에서 Liberty 애플리케이션을 배포하고 실행하려면 [Open Liberty 컨테이너 이미지](https://github.com/OpenLiberty/ci.docker) 또는 [WebSphere Liberty 컨테이너 이미지](https://github.com/WASdev/ci.docker)를 사용하여 애플리케이션을 Docker 이미지로 컨테이너화합니다.

1. 이 가이드를 위한 샘플 코드를 복제합니다. 샘플은 [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks)에 있습니다.
1. 디렉터리를 로컬 복제본의 `javaee-app-simple-cluster`로 변경합니다.
1. `mvn clean package`를 실행하여 애플리케이션을 패키징합니다.
1. `mvn liberty:dev`를 실행하여 애플리케이션을 테스트합니다. 성공하면 명령 출력에 `The defaultServer server is ready to run a smarter planet.`이 표시됩니다. `CTRL-C`를 사용하여 애플리케이션을 중지합니다.
1. 속성에 대한 값을 `artifactId` 검색하고 `version` 에 `pom.xml` 정의됩니다.

   ```azurecli-interactive
   artifactId=$(mvn -q -Dexec.executable=echo -Dexec.args='${project.artifactId}' --non-recursive exec:exec)
   version=$(mvn -q -Dexec.executable=echo -Dexec.args='${project.version}' --non-recursive exec:exec)
   ```
1. `cd target`를 실행하여 디렉터리를 샘플 빌드로 변경합니다.
1. 다음 명령 중 하나를 실행하여 애플리케이션 이미지를 빌드하고 ACR 인스턴스에 밀어넣습니다.
   * Open Liberty를 경량 오픈 소스 Java™ 런타임으로 사용하려면 Open Liberty 기본 이미지를 사용하여 빌드합니다.

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t ${artifactId}:${version} -r $REGISTRY_NAME .
     ```

   * 상용 버전의 Open Liberty를 사용하려면 WebSphere Liberty 기본 이미지를 사용하여 빌드합니다.

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t ${artifactId}:${version} -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>AKS 클러스터에 애플리케이션 배포

AKS 클러스터에 Liberty 애플리케이션을 배포하려면 아래 단계를 수행합니다.

1. AKS 클러스터가 인증되어 ACR 인스턴스에서 이미지를 끌어올 수 있도록 끌어오기 비밀을 만듭니다.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. 현재 작업 디렉터리가 로컬 복제본의 `javaee-app-simple-cluster/target`인지 확인합니다.
1. 다음 명령을 실행하여 복제본이 3개 있는 Liberty 애플리케이션을 AKS 클러스터에 배포합니다. 명령 출력도 인라인으로 표시됩니다.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-cafe-cluster"
   kubectl apply -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication ${artifactId}-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-cafe-cluster         youruniqueacrname.azurecr.io/javaee-cafe:1.0.25         True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment ${artifactId}-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-cluster         0/3     3            0           20s
   ```

1. `READY` 열 아래에 `3/3`이 표시되고, `AVAILABLE` 열 아래에 `3`이 표시될 때까지 기다린 다음 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다.

### <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션이 실행되면 Kubernetes 부하 분산 장치 서비스가 애플리케이션 프런트 엔드를 인터넷에 공개합니다. 이 프로세스는 시간이 약간 걸릴 수 있습니다.

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 명령을 사용합니다.

```azurecli-interactive
kubectl get service ${artifactId}-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-cafe-cluster         LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

*EXTERNAL-IP* 주소가 *보류 중* 에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다.

서비스의 외부 IP 주소(위의 예에서 `52.152.189.57`)로 웹 브라우저를 열어 애플리케이션 홈페이지를 확인합니다. 페이지의 왼쪽 위에 애플리케이션 복제본의 pod 이름이 표시됩니다. 몇 분 정도 기다렸다가 페이지를 새로 고쳐 AKS 클러스터에서 제공하는 부하 분산으로 인해 표시되는 다른 pod 이름을 확인합니다.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="AKS에 성공적으로 배포된 Java liberty 애플리케이션":::

>[!NOTE]
> - 애플리케이션은 현재 HTTPS를 사용하지 않습니다. [사용자 고유의 인증서를 사용하여 TLS를 사용하도록 설정](ingress-own-tls.md)하는 것이 좋습니다.

## <a name="clean-up-the-resources"></a>리소스 정리

Azure 요금을 방지하려면 불필요한 리소스를 정리해야 합니다.  클러스터가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 컨테이너 서비스, 컨테이너 레지스트리 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

이 가이드에 사용된 참조에서 자세한 내용을 알아볼 수 있습니다.

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Open Liberty](https://openliberty.io/)
* [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Open Liberty 서버 구성](https://openliberty.io/docs/ref/config/)
* [Liberty Maven 플러그 인](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Open Liberty 컨테이너 이미지](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty 컨테이너 이미지](https://github.com/WASdev/ci.docker)
