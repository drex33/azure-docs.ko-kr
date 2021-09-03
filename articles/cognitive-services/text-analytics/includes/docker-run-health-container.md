---
title: docker run 명령의 컨테이너 실행 예
titleSuffix: Azure Cognitive Services
description: Health 컨테이너용 docker run 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/18/2021
ms.author: aahi
ms.openlocfilehash: 47a7059e21f1c9b9d6d72644bc08c62b66afc772
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114339407"
---
## <a name="install-the-container"></a>컨테이너 설치

Text Analytics for Health 컨테이너를 설치하고 실행할 수 있는 여러 가지 방법이 있습니다. 

- [Azure Portal](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare)을 사용하여 Text Analytics 리소스를 만들고 Docker를 사용하여 컨테이너를 가져옵니다.
- Docker와 함께 Azure VM을 사용하여 컨테이너를 실행합니다. [Azure의 Docker](../../../docker/index.yml)를 참조하세요.
- 다음 PowerShell 및 Azure CLI 스크립트를 사용하여 리소스 배포 및 컨테이너 구성을 자동화할 수 있습니다.

### <a name="run-the-container-locally"></a>컨테이너를 로컬로 실행

컨테이너 이미지를 다운로드한 후 자신의 환경에서 컨테이너를 실행하려면 다음 `docker run` 명령어를 실행하세요. 다음 자리 표시자를 고유한 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | Text Analytics 리소스의 키입니다. Azure Portal의 리소스 **키 및 엔드포인트** 페이지에서 이 값을 찾을 수 있습니다. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Text Analytics API에 액세스하기 위한 엔드포인트입니다. Azure Portal의 리소스 **키 및 엔드포인트** 페이지에서 이 값을 찾을 수 있습니다. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest \
Eula=accept \
rai_terms=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

이 명령은 다음을 수행합니다.

- 컨테이너 이미지에서 *Text Analytics for Health* 컨테이너를 실행합니다.
- 6코어 CPU 및 12GB(기가바이트) 메모리를 할당합니다.
- 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
- EULA(최종 사용자 사용권 계약) 및 RAI(책임 있는 AI) 사용 약관에 동의합니다.
- 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

### <a name="demo-ui-to-visualize-output"></a>출력을 시각화하는 데모 UI

> [!NOTE]
> 이 데모는 Text Analytics for Health 컨테이너에서만 사용할 수 있습니다.

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다.  컨테이너의 엔드포인트에 `/demo`를 추가하여 액세스할 수 있는 시각화 도구를 컨테이너에 제공했습니다. 예를 들면 다음과 같습니다.

```
http://<serverURL>:5000/demo
```

아래 cURL 요청 예제를 사용하여 배포한 컨테이너에 쿼리를 제출하고 `serverURL` 변수를 적절한 값으로 바꿉니다.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Azure Web App for Containers를 사용하여 컨테이너 설치

Azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/)는 클라우드에서 컨테이너를 실행하기 위한 전용 Azure 리소스입니다. 자동 크기 조정, Docker 컨테이너 및 Docker Compose 지원, HTTPS 지원 등의 기본 기능을 제공합니다.

> [!NOTE]
> Azure 웹앱을 사용하여 자동으로 도메인(`<appservice_name>.azurewebsites.net` 형식)을 가져옵니다.

Azure CLI를 사용하여 이 PowerShell 스크립트를 실행하고 HTTPS를 통해 구독 및 컨테이너 이미지를 사용하여 Web App for Containers를 만듭니다. 첫 번째 요청을 제출하기 전에 스크립트가 완료될 때까지 기다립니다(약 25~30분).

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME 
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Azure 컨테이너 인스턴스를 사용하여 컨테이너 설치

또한 ACI(Azure 컨테이너 인스턴스)를 사용하여 보다 간편하게 배포할 수 있습니다. ACI는 관리형 서버리스 Azure 환경에서 주문형 Docker 컨테이너를 실행할 수 있는 리소스입니다. 

Azure Portal를 사용하여 ACI 리소스를 배포하는 단계는 [Azure Container Instances 사용 방법](../../containers/azure-container-instance-recipe.md)을 참조하세요. Azure CLI를 사용하여 아래 PowerShell 스크립트를 사용할 수도 있습니다. 그러면 컨테이너 이미지를 사용하여 구독에서 ACI를 만듭니다.  첫 번째 요청을 제출하기 전에 스크립트가 완료될 때까지 기다립니다(약 25~30분).  ACI 리소스당 최대 CPU 수 제한이 있으므로 요청당 5개 이상의 큰 문서(각각 약 5,000자)를 전송하려는 경우 이 옵션을 선택하지 마세요.
가용성 정보는 [ACI 지역 지원](../../../container-instances/container-instances-region-availability.md) 문서를 참조하세요. 

> [!NOTE] 
> Azure Container Instances에는 기본 제공 도메인에 대한 HTTPS 지원이 포함되지 않습니다. HTTPS가 필요한 경우 인증서를 만들고 도메인을 등록하는 등 수동으로 구성해야 합니다. NGINX를 사용하여 이 작업을 수행하는 지침은 아래에서 찾을 수 있습니다.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>보안 ACI 연결

기본적으로 컨테이너 API와 함께 ACI를 사용하는 경우에는 보안이 제공되지 않습니다. 일반적으로 컨테이너는 네트워크 브리지에 의해 외부로부터 보호되는 Pod의 일부로 실행되기 때문입니다. 그러나 프런트 연결 구성 요소를 사용하여 컨테이너를 수정하면 컨테이너 엔드포인트를 비공개로 유지할 수 있습니다. 다음 예제에서는 [NGINX](https://www.nginx.com)를 수신 게이트웨이로 사용하여 HTTPS/SSL 및 클라이언트 인증서 인증을 지원합니다.

> [!NOTE]
> NGINX는 오픈 소스 고성능 HTTP 서버 및 프록시입니다. NGINX 컨테이너는 단일 컨테이너에 대한 TLS 연결을 종료하는 데 사용할 수 있습니다. 더 복잡한 NGINX 수신 기반 TLS 종료 솔루션도 가능합니다.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>NGINX를 수신 게이트웨이로 설정

NGINX는 [구성 파일](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)을 사용하여 런타임에 기능을 사용하도록 설정합니다. 다른 서비스에 TLS 종료를 사용하도록 설정하려면 SSL 인증서를 지정하여 TLS 연결을 종료하고 `proxy_pass`를 사용하여 서비스에 대한 주소를 지정해야 합니다. 샘플은 아래에 있습니다.


> [!NOTE]
> `ssl_certificate`는 NGINX 컨테이너의 로컬 파일 시스템 내에 지정된 경로를 예상합니다. `proxy_pass`에 지정된 주소는 NGINX 컨테이너의 네트워크 내에서 사용할 수 있어야 합니다.

NGINX 컨테이너는 `/etc/nginx/conf.d/` 아래에 탑재된 `_.conf_`의 모든 파일을 HTTP 구성 경로에 로드합니다.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Docker Compose 파일 예제

아래 예제에서는 NGINX 및 Text Analytics for Health 컨테이너를 배포하기 위해 [Docker Compose](https://docs.docker.com/compose/reference/overview) 파일을 만드는 방법을 보여 줍니다.

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

이 Docker Compose 파일을 시작하려면 콘솔을 사용하여 파일의 루트 수준에서 다음 명령을 실행합니다.

```bash
docker-compose up
```

자세한 내용은 NGINX 설명서에서 [NGINX SSL 종료](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)를 참조하세요.