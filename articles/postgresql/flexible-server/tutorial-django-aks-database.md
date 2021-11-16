---
title: '자습서: Azure CLI를 사용하여 PostgreSQL 유연한 서버를 통해 AKS 클러스터에 Django 배포'
description: Azure Database for PostgreSQL - 유연한 서버를 사용하여 Django를 AKS에 빠르게 빌드하고 배포하는 방법을 알아봅니다.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a1c19100cad65c965c567c93c67db1033a4bba10
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136849"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>자습서: Azure Database for PostgreSQL - 유연한 서버를 사용하여 AKS에 Django 앱 배포

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Database for PostgreSQL - 유연한 서버(미리 보기)를 통해 Django 애플리케이션을 AKS(Azure Kubernetes Service) 클러스터에 배포합니다.

**[AKS](../../aks/intro-kubernetes.md)** 는 클러스터를 빠르게 배포하고 관리할 수 있는 관리되는 Kubernetes 서비스입니다. **[Azure Database for PostgreSQL - 유연한 서버(미리 보기)](overview.md)** 는 데이터베이스 관리 기능 및 구성 설정에 대한 보다 세부적인 제어와 유연성을 제공하도록 설계된 완전 관리형 데이터베이스 서비스입니다.

> [!NOTE]
> - Azure Database for PostgreSQL 유연한 서버는 현재 공개 미리 보기에 있습니다.
> - 이 빠른 시작에서는 Kubernetes 개념, Django 및 PostgreSQL에 대한 기본 지식이 있다고 가정합니다.

## <a name="pre-requisites"></a>필수 구성 요소
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- 새 브라우저 창에서 [Azure Cloud Shell](https://shell.azure.com)을 시작합니다. 로컬 머신에서 [Azure CLI도 설치](/cli/azure/install-azure-cli#install)할 수 있습니다. local install을 사용하는 경우 [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 Azure CLI에 로그인합니다.  인증 프로세스를 완료하려면 터미널에 표시되는 단계를 수행합니다. 
- [az version](/cli/azure/reference-index?#az_version)을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 최신 버전으로 업그레이드하려면 [az upgrade](/cli/azure/reference-index?#az_upgrade)를 실행합니다. 이 문서에는 최신 버전의 Azure CLI가 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. *eastus* 위치에서 [az-group-create](/cli/azure/group#az_group_create) 명령을 사용하여 *django-project* 리소스 그룹을 만들어 보겠습니다.

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> 리소스 그룹의 위치는 리소스 그룹 메타데이터가 저장되는 위치입니다. 또한 리소스를 만드는 동안 다른 지역을 지정하지 않은 경우 리소스가 Azure에서 실행되는 위치이기도 합니다.

다음 예제 출력에서는 성공적으로 만들어진 리소스 그룹이 나와 있습니다.

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS 클러스터 만들기

[az aks create](/cli/azure/aks#az_aks_create) 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 하나의 노드가 있는 *djangoappcluster* 라는 클러스터를 만듭니다. 이 작업을 완료하는 데는 몇 분 정도 걸립니다.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

몇 분 후 명령이 완료되면 클러스터에 대한 JSON 형식 정보가 반환됩니다.

> [!NOTE]
> AKS 클러스터를 만들 때 AKS 리소스를 저장하기 위해 두 번째 리소스 그룹이 자동으로 만들어집니다. [AKS를 통해 두 개의 리소스 그룹이 만들어지는 이유는 무엇인가요?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)를 참조하세요.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터를 관리하려면 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. 

> [!NOTE] 
> Azure CLI를 로컬로 실행하는 경우 [az aks install-cli](/cli/azure/aks#az_aks_install_cli) 명령을 실행하여 `kubectl`을 설치합니다.

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials) 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

다음 예제 출력은 이전 단계에서 만든 단일 노드를 보여줍니다. 노드 상태가 *준비* 인지 확인합니다.

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버 만들기
[az postgreSQL flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) 명령을 사용하여 유연한 서버를 만듭니다. 다음 명령은 Azure CLI의 로컬 컨텍스트에 있는 서비스 기본값 및 값을 사용하여 서버를 만듭니다.

```azurecli-interactive
az postgres flexible-server create --public-access all
```

생성된 서버에는 다음과 같은 특성이 있습니다.
- 새 빈 데이터베이스(```postgres```): 서버를 처음 프로비저닝하면 이 데이터베이스가 만들어집니다. 이 빠른 시작에서는 이 데이터베이스를 사용합니다.
- 자동 생성된 서버 이름, 관리 사용자 이름, 관리자 암호, 리소스 그룹 이름(로컬 컨텍스트에서 아직 지정되지 않은 경우): 리소스 그룹과 동일한 위치에 있음
- 퍼블릭 액세스 인수를 사용하면 올바른 사용자 이름과 암호를 사용하는 모든 클라이언트에 대해 퍼블릭 액세스 권한이 있는 서버를 만들 수 있습니다.
- 명령에서 로컬 컨텍스트를 사용하므로 ```django-project``` 리소스 그룹 및 ```eastus``` 지역에 서버를 만듭니다.


## <a name="build-your-django-docker-image"></a>Django docker 이미지 빌드

새 [Django 애플리케이션](https://docs.djangoproject.com/en/3.1/intro/)을 만들거나 기존 Django 프로젝트를 사용합니다. 코드가 이 폴더 구조에 있는지 확인합니다. 

> [!NOTE] 
> 애플리케이션이 없는 경우 [**Kubernetes 매니페스트 파일**](./tutorial-django-aks-database.md#create-kubernetes-manifest-file)로 직접 이동하여 샘플 이미지 [mksuni/django-aks-app:latest](https://hub.docker.com/r/mksuni/django-aks-app)를 사용할 수 있습니다. 

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
          . . . . . . .
    ├───static
         . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
Django 애플리케이션에서 kubernetes 앱에 할당된 외부 IP를 사용하도록 ```settings.py```에서 ```ALLOWED_HOSTS```를 업데이트합니다.

```python
ALLOWED_HOSTS = ['*']
```

```settings.py``` 파일의 ```DATABASES={ }``` 섹션을 업데이트합니다. 아래 코드 조각은 Kubernetes 매니페스트 파일에서 데이터베이스 호스트, 사용자 이름 및 암호를 읽습니다.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>requirements.txt 파일 생성
Django 애플리케이션에 대한 종속성을 나열하는 ```requirements.txt``` 파일을 만듭니다. ```requirements.txt``` 파일의 예제는 다음과 같습니다. [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/)를 사용하여 기존 애플리케이션에 대한 requirements.txt 파일을 생성할 수 있습니다.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Dockerfile 만들기
```Dockerfile```이라는 새 파일을 만들고, 아래 코드 조각을 복사합니다. 이 Dockerfile 파일은 Python 3.8을 설정하고 requirements.txt 파일에 나열된 모든 요구 사항을 설치합니다.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>이미지 빌드
```cd``` 명령을 사용하여 터미널의 ```my-django-app``` 디렉터리에 있는지 확인합니다. 다음 명령을 실행하여 게시판 이미지를 빌드합니다.

``` bash

docker build --tag myblog:latest .

```

이미지를 [Docker 허브](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) 또는 [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md)에 배포합니다.

> [!IMPORTANT]
>ACR(Azure Container Registry)을 사용하는 경우 ```az aks update``` 명령을 실행하여 ACR 계정을 AKS 클러스터에 연결합니다.
>
>```azurecli-interactive
>az aks update -n djangoappcluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes 매니페스트 파일 만들기

Kubernetes 매니페스트 파일은 어떤 컨테이너 이미지가 실행되는지 등과 같은 클러스터에 대해 원하는 상태를 정의합니다. ```djangoapp.yaml```이라는 매니페스트 파일을 만들고, 다음 YAML 정의에 복사해 보겠습니다. 

>[!IMPORTANT]
> - ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]```를 실제 Django docker 이미지 이름 및 태그(예: ```docker-hub-user/myblog:latest```)로 바꿉니다.  매니페스트 파일에서 데모 샘플 앱 ```mksuni/django-aks-app:latest```를 사용할 수 있습니다.
> - 아래 ```env``` 섹션을 postgres 유연한 서버의 ```SERVERNAME```, ```YOUR-DATABASE-USERNAME```, ```YOUR-DATABASE-PASSWORD```로 업데이트합니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USER
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>AKS 클러스터에 Django 배포
[kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) 명령을 사용하여 애플리케이션을 배포하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f djangoapp.yaml
```

다음 예제 출력에는 성공적으로 만들어진 배포 및 서비스가 나와 있습니다.

```output
deployment "django-app" created
service "python-svc" created
```

```django-app``` 배포를 사용하면 앱에 사용할 이미지, Pod 수 및 Pod 구성과 같은 배포에 대한 세부 정보를 설명할 수 있습니다. 외부 IP를 통해 애플리케이션을 공개하는 ```python-svc``` 서비스가 만들어집니다.

## <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션이 실행되면 애플리케이션 프런트 엔드를 인터넷에 공개하는 Kubernetes 서비스가 만들어집니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 명령을 사용합니다.

```azurecli-interactive
kubectl get service python-svc --watch
```

처음에는 *django-app* 서비스에 대한 *EXTERNAL-IP* 가 *보류 중* 으로 표시됩니다.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* 주소가 *보류 중* 에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다. 다음 예제 출력은 서비스에 할당된 유효한 공용 IP 주소를 보여줍니다.

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

이제 웹 브라우저를 서비스의 외부 IP 주소로 열어 Django 애플리케이션을 봅니다.  

>[!NOTE]
> - Django 사이트는 현재 HTTPS를 사용하지 않습니다. [사용자 고유의 인증서를 사용하여 TLS를 사용하도록 설정](../../aks/ingress-own-tls.md)하는 것이 좋습니다.
> - [HTTP 라우팅](../../aks/http-application-routing.md)을 클러스터에 사용하도록 설정할 수 있습니다. http 라우팅이 사용하도록 설정되면 AKS 클러스터에서 수신 컨트롤러를 구성합니다. 애플리케이션이 배포되면 솔루션에서 애플리케이션 엔드포인트에 대해 공개적으로 액세스할 수 있는 DNS 이름도 만듭니다.

## <a name="run-database-migrations"></a>데이터베이스 마이그레이션 실행

django 애플리케이션의 경우 데이터베이스 마이그레이션을 실행하거나 정적 파일을 수집해야 합니다. ```$ kubectl exec <pod-name> -- [COMMAND]```를 사용하여 이러한 django 셸 명령을 실행할 수 있습니다.  명령을 실행하기 전에 ```kubectl get pods```를 사용하여 Pod 이름을 찾아야 합니다. 

```bash
$ kubectl get pods
```

다음과 같은 출력이 표시됩니다.
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Pod 이름을 찾았으면 ```$ kubectl exec <pod-name> -- [COMMAND]``` 명령을 사용하여 django 데이터베이스 마이그레이션을 실행할 수 있습니다. ```/code/```는 위의 ```Dockerfile```에 정의된 프로젝트에 대한 작업 디렉터리입니다.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

출력은 다음과 같습니다. 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

문제가 발생하면 ```kubectl logs <pod-name>```을 실행하여 애플리케이션에서 throw하는 예외를 확인합니다. 애플리케이션이 정상적으로 작동하는 경우 ```kubectl logs```를 실행하면 다음과 같은 출력이 표시됩니다.

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>리소스 정리

Azure 요금을 방지하려면 불필요한 리소스를 정리해야 합니다.  클러스터가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제](../../aks/kubernetes-service-principal.md#additional-considerations)를 참조하세요. 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- AKS 클러스터에 대한 [Kubernetes 웹 대시보드에 액세스](../../aks/kubernetes-dashboard.md)하는 방법 알아보기
- [지속적인 배포를 사용하도록 설정](../../aks/deployment-center-launcher.md)하는 방법 알아보기
- [클러스터 크기를 조정](../../aks/tutorial-kubernetes-scale.md)하는 방법 알아보기
- [postgres 유연한 서버를 관리](./quickstart-create-server-cli.md)하는 방법 알아보기
- 데이터베이스 서버에 대한 [서버 매개 변수를 구성](./howto-configure-server-parameters-using-cli.md)하는 방법 알아보기
