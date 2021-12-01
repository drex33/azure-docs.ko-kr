---
title: 상태 컨테이너에 Text Analytics를 사용 하는 방법
titleSuffix: Azure Cognitive Services
description: 상태 Docker 컨테이너에 대 한 Text Analytics를 사용 하 여 온-프레미스에서 의료 정보를 추출 하 고 레이블 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/29/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 8520088eab71372d0eb35717867c10fa256b5498
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133368891"
---
# <a name="use-text-analytics-for-health-containers"></a>상태 컨테이너에 Text Analytics 사용

컨테이너를 사용 하면 사용자의 인프라에서 health API에 대 한 Text Analytics를 호스트할 수 있습니다. 상태에 대 한 Text Analytics를 원격으로 호출 하 여 충족 될 수 없는 보안 또는 데이터 거 버 넌 스 요구 사항이 있는 경우 컨테이너가 좋은 옵션 일 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

상태 컨테이너에 대해 Text Analytics를 사용 하기 전에 다음 필수 구성 요소를 충족 해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

* 호스트 컴퓨터에 [Docker](https://docs.docker.com/)가 설치되어 있습니다. Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. 
    * Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.
    * [Docker 개념](https://docs.docker.com/get-started/overview/)에 대한 기본 지식이 있어야 합니다. 
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> </a> 무료 (F0) 또는 표준 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)을 사용 하는 언어 리소스 언어 리소스를 만듭니다.

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>호스트 컴퓨터 요구 사항 및 권장 사항

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

다음 표에서는 상태 컨테이너의 Text Analytics에 대 한 최소 및 권장 사양을 설명 합니다. 각 CPU 코어는 속도가 2.6GHz 이상이어야 합니다. 허용되는 TPS(초당 트랜잭션 수)도 나열됩니다.

|  | 최소 호스트 사양 | 권장 호스트 사양 | 최소 TPS | 최대 TPS|
|---|---------|-------------|--|--|
| **1 개 문서/요청**   |  4개 코어, 10GB 메모리 | 6개 코어, 12GB 메모리 |15 | 30|
| **10 개 문서/요청**   |  6개 코어, 16GB 메모리 | 8개 코어, 20GB 메모리 |15 | 30|

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 CPU 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft 퍼블릭 컨테이너 레지스트리에서 이 컨테이너 이미지를 다운로드합니다.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```


[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

컨테이너가 호스트 컴퓨터에 있으면 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. 컨테이너는 중지할 때까지 계속 실행됩니다.

> [!IMPORTANT]
> * 다음 섹션에서 Docker 명령은 줄 연속 문자 같은 백 슬래시, `\`을 사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
> * 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.
>   * 또한 rai ( [담당 AI](/legal/cognitive-services/text-analytics/transparency-note-health)  ) 승인이 값과 함께 제공 되어야 합니다 `accept` .
> * 감정 분석 및 언어 감지 컨테이너는 API v3을 사용하며 출시되었습니다. 핵심 구 추출 컨테이너는 API의 v2를 사용하며 미리 보기 상태입니다.

Text Analytics for Health 컨테이너를 설치하고 실행할 수 있는 여러 가지 방법이 있습니다. 

- Azure Portal를 사용 하 여 언어 리소스를 만들고 Docker를 사용 하 여 컨테이너를 가져옵니다.
- Docker와 함께 Azure VM을 사용하여 컨테이너를 실행합니다. [Azure의 Docker](../../../../docker/index.yml)를 참조하세요.
- 다음 PowerShell 및 Azure CLI 스크립트를 사용하여 리소스 배포 및 컨테이너 구성을 자동화할 수 있습니다.

상태 컨테이너에 Text Analytics를 사용 하는 경우 API 요청 및 응답에 포함 된 데이터는 Microsoft에 표시 되지 않으며 데이터에 적용 된 모델을 학습 하는 데 사용 되지 않습니다. 

### <a name="run-the-container-locally"></a>컨테이너를 로컬로 실행

컨테이너 이미지를 다운로드한 후 자신의 환경에서 컨테이너를 실행하려면 다음 `docker run` 명령어를 실행하세요. 다음 자리 표시자를 고유한 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | 언어 리소스의 키입니다. Azure Portal의 리소스 **키 및 엔드포인트** 페이지에서 이 값을 찾을 수 있습니다. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | API에 액세스 하기 위한 끝점입니다. Azure Portal의 리소스 **키 및 엔드포인트** 페이지에서 이 값을 찾을 수 있습니다. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest \
Eula=accept \
rai_terms=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
```

이 명령은 다음을 수행합니다.

- 컨테이너 이미지에서 Text Analytics for Health 컨테이너를 실행합니다.
- 6코어 CPU 및 12GB(기가바이트) 메모리를 할당합니다.
- 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
- EULA(최종 사용자 사용권 계약) 및 RAI(책임 있는 AI) 사용 약관에 동의합니다.
- 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

### <a name="demo-ui-to-visualize-output"></a>출력을 시각화하는 데모 UI

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
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Language resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Language resource.
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

Azure Portal를 사용하여 ACI 리소스를 배포하는 단계는 [Azure Container Instances 사용 방법](../../../containers/azure-container-instance-recipe.md)을 참조하세요. Azure CLI를 사용하여 아래 PowerShell 스크립트를 사용할 수도 있습니다. 그러면 컨테이너 이미지를 사용하여 구독에서 ACI를 만듭니다.  첫 번째 요청을 제출하기 전에 스크립트가 완료될 때까지 기다립니다(약 25~30분).  ACI 리소스당 최대 CPU 수 제한이 있으므로 요청당 5개 이상의 큰 문서(각각 약 5,000자)를 전송하려는 경우 이 옵션을 선택하지 마세요.
가용성 정보는 [ACI 지역 지원](../../../../container-instances/container-instances-region-availability.md) 문서를 참조하세요. 

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
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Language resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Language resource.
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

아래 예제에서는 NGINX 및 health 컨테이너를 배포 하기 위해 [docker 작성](https://docs.docker.com/compose/reference/overview) 파일을 만드는 방법을 보여 줍니다.

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

[!INCLUDE [Running multiple containers on the same host](../../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다.

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다.

[!INCLUDE [Container's API documentation](../../../../../includes/cognitive-services-containers-api-documentation.md)]


### <a name="structure-the-api-request-for-the-container"></a>컨테이너에 대한 API 요청 구성

Postman을 사용하거나 아래의 cURL 요청 예를 사용하여 배포한 컨테이너에 쿼리를 제출하고 `serverURL` 변수를 적절한 값으로 바꿀 수 있습니다.  컨테이너의 URL에 있는 API 버전은 호스팅된 API와 다릅니다.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

다음 JSON은 상태 요청의 POST 본문에 대 한 Text Analytics에 연결 된 JSON 파일의 예입니다.

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>컨테이너 응답 본문

다음 JSON은 컨테이너 화 된 동기 호출의 상태 응답 본문에 대 한 Text Analytics의 예입니다.

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

## <a name="run-the-container-with-client-library-support"></a>클라이언트 라이브러리 지원으로 컨테이너를 실행 합니다.

컨테이너 버전부터 `3.0.017010001-onprem-amd64` 또는 컨테이너를 사용 하는 경우 `latest` [클라이언트 라이브러리](../quickstart.md)를 사용 하 여 상태 컨테이너에 대 한 Text Analytics를 실행할 수 있습니다. 이렇게 하려면 다음 매개 변수를 명령에 추가 합니다 `docker run` .

`enablelro=true`

이후에 클라이언트 개체를 인증할 때 컨테이너가 실행 중인 끝점을 사용 합니다.

`http://localhost:5000`

예를 들어 c #을 사용 하는 경우 다음 코드를 사용 합니다.

```csharp
var client = new TextAnalyticsClient("http://localhost:5000", "your-text-analytics-key");
```

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](configure-containers.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

상태 컨테이너에 대 한 Text Analytics Azure 계정의 _언어_ 리소스를 사용 하 여 azure로 청구 정보를 보냅니다. 

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

## <a name="summary"></a>요약

이 문서에서는 상태 컨테이너에 대 한 Text Analytics를 다운로드, 설치 및 실행 하기 위한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

* 상태에 대 한 Text Analytics는 Docker 용 Linux 컨테이너를 제공 합니다.
* 컨테이너 이미지는 Microsoft 컨테이너 레지스트리(MCR)에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 컨테이너의 호스트 URI를 지정 하 여 REST API 또는 SDK를 사용 하 여 상태 컨테이너에 대 한 Text Analytics에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정은 [컨테이너 구성](configure-containers.md)을 참조하세요.
