---
title: JavaScript 및 Azure Id를 사용 하 여 WebPubSubServiceClient를 만드는 방법
description: JavaScript 및 Azure Id를 사용 하 여 WebPubSubServiceClient를 만드는 방법
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: 9d32f3b7b56fffacf811c9ba1860dec34af917ec
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730467"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-javascript-and-azure-identity"></a>`WebPubSubServiceClient`JavaScript 및 Azure id를 사용 하 여를 만드는 방법

이 방법 가이드에서는 `WebPubSubServiceClient` JavaScript에서 Azure Active Directory를 사용 하 여를 만드는 방법을 보여 줍니다.

## <a name="requirements"></a>요구 사항

- [@azure/identity](https://www.npmjs.com/package/@azure/identity)Npmjs.com에서 패키지를 설치 합니다.

  ```bash
  npm install --save @azure/identity
  ```

- [@azure/web-pubsub](https://www.npmjs.com/package/@azure/web-pubsub)Npmjs.com에서 패키지 설치

  ```bash
  npm install @azure/web-pubsub
  ```

## <a name="sample-codes"></a>샘플 코드

1. `TokenCredential`Azure ID SDK를 사용 하 여를 만듭니다.

    ```javascript
    const { DefaultAzureCredential } = require('@azure/identity')

    let credential = new DefaultAzureCredential();
    ```

    `credential` 는 클래스에서 상속 되는 모든 클래스가 될 수 있습니다 `TokenCredential` .

    - EnvironmentCredential
    - ClientSecretCredential
    - ClientCertificateCredential
    - ManagedIdentityCredential
    - VisualStudioCredential
    - VisualStudioCodeCredential
    - AzureCliCredential

    자세한 내용은 [JavaScript 용 Azure Identity client library](/javascript/api/overview/azure/identity-readme) 를 참조 하세요.

2. 그런 다음 `client` , 및를 사용 하 여를 만듭니다 `endpoint` `hub` `credential` . 

    ```javascript
    const { DefaultAzureCredential } = require('@azure/identity')

    let credential = new DefaultAzureCredential();

    let serviceClient = new WebPubSubServiceClient("<endpoint>", credential, "<hub>");
    ```

    이 클라이언트를 사용 하는 방법에 [대 한 자세한 내용은 JavaScript 용 Azure 웹 PubSub 서비스 클라이언트 라이브러리](/javascript/api/overview/azure/web-pubsub-readme) 를 참조 하세요.

## <a name="complete-sample"></a>전체 샘플

- [AAD Auth를 사용 하는 간단한 공간](https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp-aad)