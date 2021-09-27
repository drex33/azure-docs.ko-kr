---
title: 자체 호스팅 개발자 포털의 대안
titleSuffix: Azure API Management
description: Azure API Management에서 개발자 포털을 자체 호스팅할 때 사용할 수 있는 대안에 대해 알아봅니다.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: fb8514423bf4efd42373ad2f111aa3a96c5c0969
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582900"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>자체 호스팅 개발자 포털의 대안

[개발자 포털을 자체 호스팅](developer-portal-self-host.md)할 때 검색할 수 있는 몇 가지 대안이 있습니다.

* 디자이너 및 게시자의 프로덕션 빌드를 사용합니다.

* Azure 함수 앱을 사용하여 포털을 게시합니다.

* 포털의 파일을 CDN(Content Delivery Network)으로 가장하여 페이지 로드 시간을 단축합니다.

이 문서에서는 각 대안에 대한 정보를 제공합니다. 

개발자 포털의 최신 릴리스에 대해 [로컬 환경](developer-portal-self-host.md#step-1-set-up-local-environment)을 설정하지 않았다면 먼저 이 작업을 수행하세요.

## <a name="build-for-production"></a>프로덕션용으로 빌드

협업을 위해 포털의 개발 환경을 온라인에 호스팅하려면 디자이너 및 게시자의 프로덕션 빌드를 사용합니다. 프로덕션 빌드는 파일을 하나로 번들하고 소스 맵을 제외하는 등의 작업을 수행합니다.

다음 명령을 실행하여 `./dist/designer` 디렉터리에 번들을 만듭니다.

```sh
npm run build-designer
```

명령을 실행하면 단일 페이지 애플리케이션이 만들어집니다. 이 애플리케이션은 Azure Blob Storage 정적 웹 사이트와 같은 정적 웹 호스트에 배포할 수 있습니다.

마찬가지로, `./dist/publisher` 폴더에 컴파일되고 최적화된 게시자를 저장합니다.

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>함수 앱을 사용하여 게시

게시 단계를 로컬로 실행하는 대신 클라우드에서 실행합니다.

Azure 함수 앱을 사용하여 게시를 구현하려면 먼저 다음과 같은 작업을 수행해야 합니다.

- [Azure 함수를 만듭니다](../azure-functions/functions-get-started.md). 함수는 JavaScript 언어 함수여야 합니다.
- Install Azure Functions Core Tools를 설치합니다.
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>1단계: 출력 스토리지 구성

콘텐츠를 로컬 폴더에 저장하는 대신 호스팅 웹 사이트(출력 스토리지의 “$web” 컨테이너)에 직접 업로드합니다. `./src/config.publish.json` 파일에서 다음과 같은 변경 사항을 구성합니다.

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>2단계: 함수 앱 빌드 및 배포

`./examples` 폴더에 샘플 HTTP 트리거 함수가 있습니다. 이 함수를 빌드하고 `./dist/function`에 저장하려면 다음 명령을 실행합니다.

```sh
npm run build-function
```

그런 다음 Azure CLI에 로그인하여 함수를 배포합니다.

```azurecli
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

배포한 후에는 HTTP 호출을 사용하여 호출할 수 있습니다.

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>호스팅 및 CDN

[자체 호스팅 개발자 포털](developer-portal-self-host.md)에서 Azure 스토리지 계정을 사용하여 웹 사이트를 호스팅하는 것이 권장됩니다. 그 외에도 호스팅 공급자 서비스를 포함하여 어떠한 솔루션으로 파일을 게시해도 좋습니다.

파일을 CDN(Content Delivery Network)으로 가장하여 페이지 로드 시간을 단축할 수도 있습니다. [Azure CDN](https://azure.microsoft.com/services/cdn/)을 사용하는 것이 권장됩니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 통해 개발자 포털에 대해 자세히 알아봅니다.

- [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)
