---
title: Form Recognizer v2.1용 Docker 컨테이너 설치 및 실행
titleSuffix: Azure Applied AI Services
description: Form Recognizer 온-프레미스용 Docker 컨테이너를 사용하여 양식 및 문서에서 키-값 쌍, 선택 표시, 테이블, 구조를 식별하고 추출합니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
keywords: 온-프레미스, Docker, 컨테이너, 식별
ms.openlocfilehash: 5f8e0c17fd7f76ddb28072e292bbe4ae39dab64c
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530751"
---
# <a name="install-and-run-form-recognizer-v21-preview-containers"></a>Form Recognizer v2.1-preview 컨테이너 설치 및 실행

> [!IMPORTANT]
>
> * Form Recognizer 컨테이너는 게이트 미리 보기 상태입니다. 이 컨테이너를 사용하려면 [온라인 요청](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)을 제출하고 승인을 받아야 합니다. 자세한 내용은 아래 [**컨테이너를 실행하도록 승인 요청**](#request-approval-to-run-the-container)을 참조하세요.
>
> * 온라인 요청 양식을 사용하려면 Azure 구독 ID를 소유하는 조직에 속하고 해당 구독에 대한 액세스 권한이 있거나 액세스 권한이 부여된 유효한 메일 주소를 제공해야 합니다.

Azure Form Recognizer는 기계 학습 기술을 사용하여 자동화된 데이터 처리 소프트웨어를 빌드할 수 있는 Azure Applied AI Services입니다. Form Recognizer를 사용하면 양식 문서에서 텍스트, 키/값 쌍, 선택 표시, 테이블 데이터 등을 식별하고 추출하며, 원본 파일의 관계를 포함하는 정형 데이터를 출력합니다.

이 문서에서는 Form Recognizer 컨테이너를 다운로드, 설치, 실행하는 방법에 대해 알아봅니다. 컨테이너를 사용하면 사용자 환경에서 Form Recognizer 서비스를 실행할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. Form Recognizer 기능은 6개의 Form Recognizer 기능 컨테이너, 즉 **레이아웃**, **명함**,**ID 문서**,  **영수증**, **청구서**, **사용자 지정** 에서 지원됩니다(영수증, 명함 및 ID 문서 컨테이너의 경우 **읽기** OCR 컨테이너도 필요).

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/)이 필요합니다.  계정이 없는 경우 [**체험 계정을 만들 수 있습니다**](https://azure.microsoft.com/free/).

또한 Form Recognizer 컨테이너를 사용하려면 다음이 필요합니다.

| 필수 | 용도 |
|----------|---------|
| **Docker 사용 경험** | 기본 `docker` [용어와 명령](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념을 기본적으로 이해하고 있어야 합니다. |
| **Docker Engine 설치됨** | <ul><li>[호스트 컴퓨터](#host-computer-requirements)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.</li><li> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. </li><li> **Windows** 에서 Docker가 **Linux** 컨테이너를 지원하도록 구성해야 합니다.</li></ul>  |
|**Form Recognizer 리소스** | Azure Portal의 [**단일 서비스 Azure Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [**다중 서비스 Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) 리소스입니다. 컨테이너를 사용하려면 연결된 API 키와 엔드포인트 URI가 있어야 합니다. 두 값 모두 Azure portal Form Recognizer **키 및 엔드포인트** 페이지에서 사용할 수 있습니다. <ul><li>**{FORM_RECOGNIZER_API_KEY}** : 사용 가능한 두 개의 리소스 키 중 하나입니다.<li>**{FORM_RECOGNIZER_ENDPOINT_URI}** : 청구 정보를 추적하는 데 사용되는 리소스의 엔드포인트입니다.</li></li></ul>|
| **Computer Vision API 리소스** | **명함, ID 문서 또는 영수증을 처리하려면 Computer Vision 리소스가 필요합니다.** <ul><li>텍스트 인식 기능에 Azure 리소스(REST API 또는 SDK) 또는 **congnitive-services-recognize-text** [컨테이너](../../../cognitive-services/Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)로 액세스할 수 있습니다. 일반 [청구](#billing) 요금이 적용됩니다.</li> <li>**cognitive-services-recognize-text** 컨테이너를 사용하는 경우 Form Recognizer 컨테이너의 Computer Vision 키는 **cognitive-services-recognize-text** 컨테이너의 Computer Vision `docker run` 또는 `docker compose` 명령에 지정된 키이며, 청구 엔드포인트는 컨테이너의 엔드포인트(예: `http://localhost:5000`)입니다. Computer Vision 컨테이너 및 Form Recognizer 컨테이너를 동일한 호스트에서 함께 사용하면 두 컨테이너 모두를 *5000* 기본 포트에서 시작할 수 없습니다. </li></ul></br>Computer Vision Azure 클라우드 또는 Cognitive Services 컨테이너의 API 키와 엔드포인트를 모두 전달합니다.<ul><li>**{COMPUTER_VISION_API_KEY}** : 사용 가능한 두 개의 리소스 키 중 하나입니다.</li><li> **{COMPUTER_VISION_ENDPOINT_URI}** : 청구 정보를 추적하는 데 사용되는 리소스의 엔드포인트입니다.</li></ul> |

|선택 사항|목적|
|---------|----------|
|**Azure CLI(명령줄 인터페이스)** | [Azure CLI](/cli/azure/install-azure-cli)를 사용하면 온라인 명령 세트를 사용하여 Azure 리소스를 만들고 관리할 수 있습니다. Windows, macOS 및 Linux 환경에 설치할 수 있으며 Docker 컨테이너와 Azure Cloud Shell에서 실행할 수 있습니다. |
|||

## <a name="request-approval-to-run-the-container"></a>컨테이너 실행에 대한 승인 요청

컨테이너 실행에 대한 승인을 요청하려면 [게이트 서비스 신청 양식](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)을 작성하여 제출합니다.

이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 양식이 제출되면 Azure Cognitive Services 팀에서 검토하고 결정을 내려서 이메일로 보내드립니다.

양식에서 Azure 구독 ID와 연결된 이메일 주소를 사용해야 합니다. 컨테이너를 실행하는 데 사용하는 Azure 리소스는 승인된 Azure 구독 ID로 생성되어야 합니다. Microsoft에서 애플리케이션의 상태에 대한 업데이트를 확인하려면 이메일(받은 편지함 및 스팸 메일함 모두)을 확인합니다. 승인을 받으면 MCR(Microsoft Container Registry)에서 컨테이너를 다운로드한 후 실행할 수 있습니다. 이 내용은 문서 뒷부분에 설명되어 있습니다.

## <a name="host-computer-requirements"></a>호스트 컴퓨터 요구 사항

호스트는 Docker 컨테이너를 실행하는 x64 기반 컴퓨터입니다. 다음과 같이 Azure에서 컴퓨터 온-프레미스 또는 Docker 호스팅 서비스일 수 있습니다.

* [Azure Kubernetes Service](../../../aks/index.yml).
* [Azure Container Instances](../../../container-instances/index.yml).
* [Kubernetes](https://kubernetes.io/) 클러스터는 [Azure Stack](/azure-stack/operator)에 배포됩니다. 자세한 내용은 [Azure Stack에 Kubernetes 배포](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)를 참조하세요.

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

#### <a name="required-containers"></a>필수 컨테이너

다음 표에서는 다운로드하는 각 Form Recognizer 컨테이너에 대한 추가 지원 컨테이너를 나열합니다. 자세한 내용은 [청구](#billing) 섹션을 참조하세요.

| 기능 컨테이너 | 지원 컨테이너 |
|---------|-----------|
| **레이아웃** | None |
| **명함** | **Computer Vision Read**|
| **ID 문서** | **Computer Vision Read** |
| **청구서**   | **레이아웃** |
| **Receipt** |**Computer Vision Read** |
| **사용자 지정** | **사용자 지정 API**, **사용자 지정 감독**, **레이아웃**|

#### <a name="recommended-cpu-cores-and-memory"></a>권장 CPU 코어 및 메모리

> [!Note]
> 최소 및 추천 값은 호스트 머신 리소스가 *아니라* Docker 제한을 기반으로 합니다.

##### <a name="read-layout-and-prebuilt-containers"></a>읽기, 레이아웃, 사전 빌드 컨테이너

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| Read 3.2 | 8 코어, 16-GB 메모리 | 8 코어, 24-GB 메모리|
| Layout 2.1-preview | 8 코어, 16-GB 메모리 | 8개 코어, 24GB 메모리 |
| Business Card 2.1-preview | 2개 코어, 4GB 메모리 | 4개 코어, 4GB 메모리 |
| ID Document 2.1-preview | 1개 코어, 2GB 메모리 |2개 코어, 2GB 메모리 |
| Invoice 2.1-preview | 4개 코어, 8GB 메모리 | 8개 코어, 8GB 메모리 |
| Receipt 2.1-preview |  4개 코어, 8GB 메모리 | 8개 코어, 8GB 메모리  |

##### <a name="custom-containers"></a>사용자 지정 컨테이너

다음 호스트 머신 요구 사항은 **학습 및 분석** 요청에 적용됩니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 사용자 지정 API| 0.5개 코어, 0.5GB 메모리| 1개 코어, 1GB 메모리 |
|사용자 지정 감독 | 4개 코어, 2GB 메모리 | 8개 코어, 4GB 메모리|

분석 호출만 하는 경우 호스트 머신 요구 사항은 다음과 같습니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
|사용자 지정 감독(분석) | 1개 코어, 0.5GB | 2개 코어, 1GB 메모리 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* `docker compose` 또는 `docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

> [!TIP]
> [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용하여 다운로드한 컨테이너 이미지를 나열할 수 있습니다. 예를 들어 다음 명령은 다운로드한 각 컨테이너 이미지의 ID, 리포지토리 및 태그를 테이블 형식으로 나열합니다.
>
>  ```docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID         REPOSITORY                TAG
>  <image-id>       <repository-path/name>    <tag-name>
>  ```

## <a name="run-the-container-with-the-docker-compose-up-command"></a>**docker-compose up** 명령으로 컨테이너 실행

* {ENDPOINT_URI} 및 {API_KEY} 값을 Azure 리소스 페이지의 리소스 엔드포인트 URI 및 API 키로 바꿉니다.

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal 키 및 엔드포인트 페이지.":::

* EULA 값이 "accept"로 설정되어 있는지 확인합니다.

* `EULA`, `Billing`, `ApiKey` 값을 지정해야 합니다. 지정하지 않으면 컨테이너가 시작되지 않습니다.

> [!IMPORTANT]
> 구독 키는 Form Recognizer 리소스에 액세스하는 데 사용됩니다. 키를 공유하지 마세요. 예를 들어 Azure Key Vault를 사용하여 안전하게 저장합니다. 또한 이러한 키는 정기적으로 다시 생성하는 것이 좋습니다. API 호출을 수행하는 데는 하나의 키만 필요합니다. 첫 번째 키를 다시 생성하는 경우 두 번째 키를 사용하여 서비스에 계속 액세스할 수 있습니다.

### <a name="layout"></a>[레이아웃](#tab/layout)

다음은 Form Recognizer Layout 컨테이너를 실행하는 자체 포함된 `docker compose` 예제입니다.  `docker compose`에서 YAML 파일을 사용하여 애플리케이션의 서비스를 구성합니다. 그런 다음 `docker-compose up` 명령을 사용하여 구성에서 모든 서비스를 만들고 시작합니다. 레이아웃 컨테이너 인스턴스에 대한 {FORM_RECOGNIZER_ENDPOINT_URI} 및 {{FORM_RECOGNIZER_API_KEY} 값을 입력합니다.

```yml
version: "3.9"
services:
azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    ports:
      - "5000"
    networks:
      - ocrvnet
networks:
  ocrvnet:
    driver: bridge
```

이제 [**docker compose**](https://docs.docker.com/compose/) 명령어로 서비스를 시작할 수 있습니다.

```bash
docker-compose up
```

### <a name="business-card"></a>[명함](#tab/business-card)

다음은 Form Recognizer 명함과 읽기 컨테이너를 함께 실행하는 자체 포함된 `docker compose` 예입니다. `docker compose`에서 YAML 파일을 사용하여 애플리케이션의 서비스를 구성합니다. 그런 다음 `docker-compose up` 명령을 사용하여 구성에서 모든 서비스를 만들고 시작합니다. 명함 컨테이너 인스턴스에 {FORM_RECOGNIZER_ENDPOINT_URI} 및 {FORM_RECOGNIZER_API_KEY} 값을 입력합니다. Computer Vision Read 컨테이너에 {COMPUTER_VISION_ENDPOINT_URI} 및 {COMPUTER_VISION_API_KEY}를 입력합니다.

```yml
version: "3.9"
services:
  azure-cognitive-service-businesscard:
    container_name: azure-cognitive-service-businesscard
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

이제 [**docker compose**](https://docs.docker.com/compose/) 명령어로 서비스를 시작할 수 있습니다.

```bash
docker-compose up
```

### <a name="id-document"></a>[ID 문서](#tab/id-document)

다음은 Form Recognizer ID 문서 및 읽기 컨테이너를 함께 실행하는 자체 포함된 `docker compose` 예입니다. `docker compose`에서 YAML 파일을 사용하여 애플리케이션의 서비스를 구성합니다. 그런 다음 `docker-compose up` 명령을 사용하여 구성에서 모든 서비스를 만들고 시작합니다. ID 문서 컨테이너에 대한 {FORM_RECOGNIZER_ENDPOINT_URI} 및 {FORM_RECOGNIZER_API_KEY} 값을 입력합니다. Computer Vision Read 컨테이너에 {COMPUTER_VISION_ENDPOINT_URI} 및 {COMPUTER_VISION_API_KEY} 값을 입력합니다.

```yml
version: "3.9"
services:
  azure-cognitive-service-id:
    container_name: azure-cognitive-service-id
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

이제 [**docker compose**](https://docs.docker.com/compose/) 명령어로 서비스를 시작할 수 있습니다.

```bash
docker-compose up
```

### <a name="invoice"></a>[청구서](#tab/invoice)

다음은 Form Recognizer 청구서와 레이아웃 컨테이너를 함께 실행하는 자체 포함된 `docker compose` 예입니다. `docker compose`에서 YAML 파일을 사용하여 애플리케이션의 서비스를 구성합니다. 그런 다음 `docker-compose up` 명령을 사용하여 구성에서 모든 서비스를 만들고 시작합니다.  청구서와 레이아웃 컨테이너에 대한 {FORM_RECOGNIZER_ENDPOINT_URI} 및 {FORM_RECOGNIZER_API_KEY} 값을 입력합니다.

```yml
version: "3.9"
services:
  azure-cognitive-service-invoice:
    container_name: azure-cognitive-service-invoice
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceLayoutHost=http://azure-cognitive-service-layout:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    user: root
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

이제 [**docker compose**](https://docs.docker.com/compose/) 명령어로 서비스를 시작할 수 있습니다.

```bash
docker-compose up
```

### <a name="receipt"></a>[Receipt](#tab/receipt)

다음은 Form Recognizer 영수증과 읽기 컨테이너를 함께 실행하는 자체 포함된 `docker compose` 예입니다. `docker compose`에서 YAML 파일을 사용하여 애플리케이션의 서비스를 구성합니다. 그런 다음 `docker-compose up` 명령을 사용하여 구성에서 모든 서비스를 만들고 시작합니다. 영수증 컨테이너에 대한 {FORM_RECOGNIZER_ENDPOINT_URI} 및 {FORM_RECOGNIZER_API_KEY} 값을 입력합니다. Computer Vision Read 컨테이너에 {COMPUTER_VISION_ENDPOINT_URI} 및 {COMPUTER_VISION_API_KEY} 값을 입력합니다.

```yml
version: "3.9"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
       - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

이제 [**docker compose**](https://docs.docker.com/compose/) 명령어로 서비스를 시작할 수 있습니다.

```bash
docker-compose up
```

### <a name="custom"></a>[사용자 지정](#tab/custom)

위에서 언급한 [필수 조건](#prerequisites) 외에도 사용자 지정 문서를 처리하려면 다음을 수행해야 합니다.

####  <a name="bullet-create-a-folder-to-store-the-following-files"></a>&bullet; 다음 파일을 저장할 폴더를 만듭니다.

  1. [ **.env**](#-create-an-environment-file)
  1. [**nginx.conf**](#-create-a-nginx-file)
  1. [**docker-compose.yml**](#-create-a-docker-compose-file)

#### <a name="bullet-create-a-folder-to-store-your-input-data"></a>&bullet; 입력 데이터를 저장할 폴더를 만듭니다.

  1. 이 폴더의 이름을 **shared** 로 지정합니다.
  1. 이 폴더의 파일 경로를 **{SHARED_MOUNT_PATH}** 로 참조합니다.
  1. *Microsoft Notepad* 와 같은 편리한 위치에 파일 경로를 복사합니다. 아래 **.env** 파일에 추가해야 합니다.

#### <a name="bullet-create-a-folder-to-store-the-logs--written-by-the-form-recognizer-service-on-your-local-machine"></a>&bullet; Form Recognizer 서비스에서 작성한 로그를 로컬 머신에 저장할 폴더를 만듭니다.

  1. 이 폴더의 이름을 **output** 으로 지정합니다.
  1. 이 폴더의 파일 경로를 **{OUTPUT_MOUNT_PATH}** 로 참조합니다.
  1. *Microsoft Notepad* 와 같은 편리한 위치에 파일 경로를 복사합니다. 아래 **.env** 파일에 추가해야 합니다.

#### <a name="bullet-create-an-environment-file"></a>&bullet; 환경 파일 만들기

  1. 이 파일의 이름을 **.env** 로 지정합니다.

  1. 다음 환경 변수를 선언합니다.

  ```text
  SHARED_MOUNT_PATH="<file-path-to-shared-folder>"
  OUTPUT_MOUNT_PATH="<file -path-to-output-folder>"
  FORM_RECOGNIZER_ENDPOINT_URI="<your-form-recognizer-endpoint>"
  FORM_RECOGNIZER_API_KEY="<your-form-recognizer-apiKey>"
  RABBITMQ_HOSTNAME="rabbitmq"
  RABBITMQ_PORT=5672
  NGINX_CONF_FILE="<file-path>"
  ```

#### <a name="bullet-create-a-nginx-file"></a>&bullet; **nginx** 파일 만들기

  1. 이 파일의 이름을 **nginx.conf** 로 지정합니다.

  1. 다음 구성을 입력합니다.

```text
worker_processes 1;

events { worker_connections 1024; }

http {

    sendfile on;

    upstream docker-api {
        server azure-cognitive-service-custom-api:5000;
    }

    upstream docker-layout {
        server  azure-cognitive-service-layout:5000;
    }

    server {
        listen 5000;

        location = / {
            proxy_pass         http://docker-api/;

        }

        location /status {
            proxy_pass         http://docker-api/status;

        }

        location /ready {
            proxy_pass         http://docker-api/ready;

        }

        location /swagger {
            proxy_pass         http://docker-api/swagger;

        }

        location /formrecognizer/v2.1/custom/ {
            proxy_pass         http://docker-api/formrecognizer/v2.1/custom/;

        }

        location /formrecognizer/v2.1/layout/ {
            proxy_pass         http://docker-layout/formrecognizer/v2.1/layout/;

        }
    }
}
```

* 동일한 형식의 양식 6개 이상으로 구성된 세트를 수집합니다. 이 데이터를 사용하여 모델을 학습시키고 양식을 테스트합니다. [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)(*sample_data.zip* 다운로드 및 추출)를 사용할 수 있습니다. 위에서 만든 **shared** 폴더에 학습 파일을 다운로드합니다.

* 데이터에 레이블을 지정하려면 [Windows용 Form Recognizer 샘플 레이블 지정 도구](https://github.com/microsoft/OCR-Form-Tools/releases/tag/v2.1-ga)를 다운로드합니다. 다운로드를 통해서는 로컬 파일 시스템에 있는 데이터에 레이블을 지정하는 데 사용할 레이블 지정 도구 .exe 파일을 가져옵니다. 다운로드 프로세스 중에 발생하는 경고는 무시해도 됩니다.

#### <a name="create-a-new-sample-labeling-tool-project"></a>새 샘플 레이블 지정 도구 프로젝트 만들기

* 샘플 레이블 지정 도구 .exe 파일을 두 번 클릭하여 레이블 지정 도구를 엽니다.
* 도구의 왼쪽 창에서 연결 탭을 선택합니다.
* 새 프로젝트를 만들고 이름과 설명을 지정하려면 선택합니다.
* 공급자의 경우 로컬 파일 시스템 옵션을 선택합니다. 로컬 폴더의 경우 샘플 데이터 파일을 저장한 폴더의 경로를 입력해야 합니다.
* 홈 탭으로 다시 이동하고 “사용자 지정을 사용하여 레이블 및 키 값 쌍으로 모델 학습” 옵션을 선택합니다.
* 왼쪽 창에서 학습 단추를 선택하여 레이블이 지정된 모델을 학습합니다.
* 이 연결을 저장하고 사용하여 요청에 레이블을 지정할 수 있습니다.
* 학습된 모델에 대해 선택한 파일을 분석하도록 선택할 수 있습니다.

#### <a name="bullet-create-a-docker-compose-file"></a>&bullet; **docker compose** 파일 만들기

1. 이 파일의 이름을 **docker-compose.yml** 로 지정

2. 다음은 Form Recognizer 레이아웃, 레이블 도구, 사용자 지정 API 및 사용자 지정 감독 컨테이너를 함께 실행하는 자체 포함된 `docker compose` 예입니다. `docker compose`에서 YAML 파일을 사용하여 애플리케이션의 서비스를 구성합니다. 그런 다음 `docker-compose up` 명령을 사용하여 구성에서 모든 서비스를 만들고 시작합니다.

  ```yml
  version: '3.3'
  services:
   nginx:
    image: nginx:alpine
    container_name: reverseproxy
    volumes:
      - ${NGINX_CONF_FILE}:/etc/nginx/nginx.conf
    ports:
      - "5000:5000"
   rabbitmq:
    container_name: ${RABBITMQ_HOSTNAME}
    image: rabbitmq:3
    expose:
      - "5672"
   layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      Logging:Console:LogLevel:Default: Information
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-api:
    container_name: azure-cognitive-service-custom-api
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-supervised:
    container_name: azure-cognitive-service-custom-supervised
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      CustomFormRecognizer:ContainerPhase: All
      CustomFormRecognizer:LayoutAnalyzeUri: http://azure-cognitive-service-layout:5000/formrecognizer/v2.1/layout/analyze
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
  ```

### <a name="ensure-the-service-is-running"></a>서비스가 실행 중인지 확인합니다.

서비스가 가동되어 실행 중인지 확인합니다. Ubuntu 셸에서 이 명령을 실행합니다.

```bash
$cd <folder containing the docker-compose file>

$source .env

$docker-compose up
```

### <a name="create-a-new-connection"></a>새 연결 만들기

* 도구의 왼쪽 창에서 **연결** 탭을 선택합니다.
* **새 프로젝트 만들기** 를 선택하고 이름과 설명을 지정합니다.
* 공급자의 경우 **로컬 파일 시스템** 옵션을 선택합니다. 로컬 폴더의 경우 **샘플 데이터** 파일을 저장한 폴더의 경로를 입력해야 합니다.
* 홈 탭으로 다시 이동하고 **사용자 지정을 사용하여 레이블 및 키 값 쌍으로 모델 학습** 을 선택합니다.
* 왼쪽 창에서 **학습 단추** 를 선택하여 레이블이 지정된 모델을 학습합니다.
* 이 연결을 **저장** 하고 사용하여 요청에 레이블을 지정할 수 있습니다.
* 학습된 모델에 대해 선택한 파일을 분석하도록 선택할 수 있습니다.

---

## <a name="validate-that-the-service-is-running"></a>서비스가 실행 중인지 확인합니다.

컨테이너가 실행되고 있는지 확인하는 방법에는 여러 가지가 있습니다.

* 컨테이너는 `\`에서 컨테이너가 실행 중인지 시각적으로 확인할 수 있는 홈페이지를 제공합니다.

* 즐겨 찾는 웹 브라우저를 열고 확인하려는 컨테이너의 외부 IP 주소와 노출된 포트로 이동하면 됩니다. 아래의 다양한 요청 URL을 사용하여 컨테이너가 실행 중인지 확인합니다. 아래에 나열된 요청 URL 예는 `http://localhost:5000`이지만 특정 컨테이너는 다를 수 있습니다. 컨테이너의 **외부 IP 주소** 와 노출된 포트로 이동해야 합니다.

  요청 URL    | 용도
  ----------- | --------
  |**http://<span></span>localhost:5000/** | 컨테이너는 홈페이지를 제공합니다.
  |**http://<span></span>localhost:5000/ready**     | GET을 사용하여 요청하면 컨테이너가 모델에 대한 쿼리를 수락할 준비가 되었는지 확인할 수 있습니다. 이 요청은 Kubernetes 활동성 및 준비 상태 프로브에 사용될 수 있습니다.
  |**http://<span></span>localhost:5000/status** | GET을 사용하여 요청하면 컨테이너를 시작하는 데 사용된 API 키가 엔드포인트 쿼리를 발생시키지 않고 유효한지 확인합니다. 이 요청은 Kubernetes 활동성 및 준비 상태 프로브에 사용될 수 있습니다.
  |**http://<span></span>localhost:5000/swagger** | 컨테이너는 엔드포인트에 대한 전체 설명서 세트와 사용해 보기 기능을 제공합니다. 이 기능을 사용하면 웹 기반 HTML 양식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 필요한 HTTP 헤더 및 본문 형식을 보여주기 위해 예제 CURL 명령이 제공됩니다.
  |

:::image type="content" source="../media/containers/container-webpage.png" alt-text="스크린샷: Azure 컨테이너 홈페이지.":::

## <a name="stop-the-containers"></a>컨테이너 중지

컨테이너를 중지하려면 다음 명령을 사용합니다.

```console
docker-compose down
```

## <a name="billing"></a>결제

Form Recognizer 컨테이너는 Azure 계정의 Form Recognizer 리소스를 사용하여 청구 정보를 Azure에 보냅니다.

컨테이너에 대한 쿼리는 `ApiKey`에 사용되는 Azure 리소스의 가격 책정 계층으로 청구됩니다. 문서와 이미지를 처리하는 데 사용되는 각 컨테이너 인스턴스에 대해 요금이 청구됩니다. 따라서 명함 기능을 사용하는 경우 Form Recognizer `BusinessCard` 및 `Compuer Vision Read` 컨테이너 인스턴스에 대한 요금이 청구됩니다. 청구서 기능의 경우 Form Recognizer `Invoice` 및 `Layout` 컨테이너 인스턴스에 대한 요금이 청구됩니다. , [Form Recognizer](https://azure.microsoft.com/pricing/details/form-recognizer/) 및 Computer Vision [읽기 기능](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) 컨테이너 가격을 참조하세요.

Azure Cognitive Services 컨테이너는 측정/청구 엔드포인트에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 사용자는 컨테이너가 항상 청구 엔드포인트와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

### <a name="connect-to-azure"></a>Azure에 연결

컨테이너에는 실행할 청구 인수 값이 필요합니다. 이러한 값을 통해 컨테이너는 청구 엔드포인트에 연결할 수 있습니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다. 컨테이너가 허용되는 시간 내에서 Azure에 연결되지 않으면 컨테이너는 계속 실행되지만 청구 엔드포인트가 복원될 때까지 쿼리를 처리하지 않습니다. 10 ~ 15분 간격으로 동시에 10회 동안 연결이 시도됩니다. 10회 시도 안에 청구 엔드포인트에 연결할 수 없는 경우 컨테이너는 요청 처리를 중지합니다. 청구를 위해 Microsoft로 전송되는 정보의 예는 [Cognitive Services 컨테이너 FAQ](../../../cognitive-services/containers/container-faq.yml#how-does-billing-work)를 참조하세요.

### <a name="billing-arguments"></a>청구 인수

다음 세 가지 옵션 모두에 유효한 값이 제공되면 [**docker-compose up**](https://docs.docker.com/engine/reference/commandline/compose_up/) 명령에서 컨테이너를 시작합니다.

| 옵션 | Description |
|--------|-------------|
| `ApiKey` | 청구 정보를 추적하는 데 사용되는 Cognitive Services 리소스의 API 키입니다.<br/>이 옵션의 값은 `Billing`에 지정된 프로비전된 리소스의 API 키로 설정해야 합니다. |
| `Billing` | 청구 정보를 추적하는 데 사용되는 Cognitive Services 리소스의 엔드포인트입니다.<br/>이 옵션의 값은 프로비저닝된 Azure 리소스의 엔드포인트 URI로 설정해야 합니다.|
| `Eula` | 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.<br/>이 옵션의 값은 **동의** 로 설정해야 합니다. |

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](form-recognizer-container-configuration.md)을 참조하세요.

## <a name="summary"></a>요약

이것으로 끝입니다. 이 문서에서는 Form Recognizer 컨테이너의 다운로드, 설치, 실행에 대한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Form Recognizer에서는 7개의 Docker용 Linux 컨테이너를 제공합니다.
* 컨테이너 이미지는 mcr에서 다운로드합니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Form Recognizer 컨테이너 구성 설정](form-recognizer-container-configuration.md) 
* [Form Recognizer 컨테이너 이미지 태그](../../../cognitive-services/containers/container-image-tags.md?tabs=current#form-recognizer)
* [Cognitive Services 컨테이너 지원 페이지 및 릴리스 정보](../../../cognitive-services/containers/container-image-tags.md?tabs=current#form-recognizer)
