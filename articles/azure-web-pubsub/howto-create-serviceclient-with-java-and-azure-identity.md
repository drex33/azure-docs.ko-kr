---
title: Java 및 Azure ID를 사용하여 WebPubSubServiceClient를 만드는 방법
description: Java 및 Azure ID를 사용하여 WebPubSubServiceClient를 만드는 방법
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: 58beee817fc68eff0e438e94d4eb7889b2572a36
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133286284"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-java-and-azure-identity"></a>Java 및 Azure ID를 사용하여 를 만드는 방법 `WebPubSubServiceClient`

이 방법 가이드에서는 Java 및 Azure ID를 사용하여 를 만드는 방법을 `WebPubSubServiceClient` 보여줍니다.

## <a name="requirements"></a>요구 사항

- [에 azure-identity](https://mvnrepository.com/artifact/com.azure/azure-identity) 종속성을 추가합니다. `pom.xml`

  ```xml
  <dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.4.1</version>
  </dependency>
  ```

  > [!Tip]
  > 최신 버전은 이 [페이지에서](https://mvnrepository.com/artifact/com.azure/azure-identity) 찾을 수 있습니다.

  자세한 내용은 [Java 및 Azure ID를](/azure/developer/java/sdk/identity) 사용하여 Azure 인증을 참조하세요.

- [에 azure-messaging-webpubsub](https://mvnrepository.com/artifact/com.azure/azure-messaging-webpubsub) 종속성을 추가합니다. `pom.xml`

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-messaging-webpubsub</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  > [!Tip]
  > 최신 버전은 이 [페이지에서](https://mvnrepository.com/artifact/com.azure/azure-messaging-webpubsub) 찾을 수 있습니다.

## <a name="sample-codes"></a>샘플 코드

1. Azure `TokenCredential` ID SDK를 사용하여 을 만듭니다.

    ```java
    package com.webpubsub.tutorial;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.DefaultAzureCredentialBuilder;

    public class App {

        public static void main(String[] args) {
            TokenCredential credential = new DefaultAzureCredentialBuilder().build();
        }
    }
    ```

    `credential` 는 클래스에서 상속되는 모든 클래스일 수 `TokenCredential` 있습니다.

    - EnvironmentCredential
    - ClientSecretCredential
    - ClientCertificateCredential
    - ManagedIdentityCredential
    - VisualStudioCredential
    - VisualStudioCodeCredential
    - AzureCliCredential

    자세한 내용은 [Java용 Azure ID 클라이언트 라이브러리를](/java/api/overview/azure/identity-readme) 참조하세요.

2. 그런 다음 , 및 를 사용하여 `client` `endpoint` 를 `hub` `credential` 만듭니다. 

    ```Java
    package com.webpubsub.tutorial;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.DefaultAzureCredentialBuilder;
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.WebPubSubServiceClientBuilder;

    public class App {
        public static void main(String[] args) {

            TokenCredential credential = new DefaultAzureCredentialBuilder().build();

            // create the service client
            WebPubSubServiceClient client = new WebPubSubServiceClientBuilder()
                    .endpoint("<endpoint>")
                    .credential(credential)
                    .hub("<hub>")
                    .buildClient();
        }
    }
    ```

    이 클라이언트를 사용하는 방법에 대한 자세한 내용은 [Java용 Azure Web PubSub 서비스 클라이언트 라이브러리를](/java/api/overview/azure/messaging-webpubsub-readme) 참조하세요.

## <a name="complete-sample"></a>전체 샘플

- [AAD 인증이 있는 간단한 채팅방](https://github.com/Azure/azure-webpubsub/tree/main/samples/java/chatapp-aad)