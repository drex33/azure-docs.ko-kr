---
title: .NET 및 Azure ID를 사용하여 WebPubSubServiceClient를 만드는 방법
description: .NET 및 Azure ID를 사용하여 WebPubSubServiceClient를 만드는 방법
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: 95143bce385245eb1130d2f9e7317b46f7e31e62
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730436"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-net-and-azure-identity"></a>`WebPubSubServiceClient`.NET 및 Azure ID를 사용하여 를 만드는 방법

이 방법 가이드에서는 .NET에서 Azure Active Directory 사용하여 를 만드는 방법을 `WebPubSubServiceClient` 보여줍니다.

## <a name="requirements"></a>요구 사항

- nuget.org [Azure.Identity를 설치합니다.](https://www.nuget.org/packages/Azure.Identity)

  ```bash
  Install-Package Azure.Identity
  ```

- nuget.org [Azure.Messaging.WebPubSub](https://www.nuget.org/packages/Azure.Messaging.WebPubSub) 설치

  ```bash
  Install-Package Azure.Messaging.WebPubSub 
  ```

## <a name="sample-codes"></a>샘플 코드

1. Azure `TokenCredential` ID SDK를 사용하여 을 만듭니다.

    ```C#
    using Azure.Identity

    namespace chatapp 
    {
        public class Program
        {
            public static void Main(string[] args)
            {
                var credential = new DefaultAzureCredential();
            }
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

    자세한 내용은 [.NET용 Azure ID 클라이언트 라이브러리를](/dotnet/api/overview/azure/identity-readme) 참조하세요.

2. 그런 다음 , 및 를 사용하여 `client` `endpoint` 를 `hub` `credential` 만듭니다. 

    ```C#
    using Azure.Identity

    public class Program
    {
        public static void Main(string[] args)
        {
            var credential = new DefaultAzureCredential();
            var client = new WebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", credential);
        }
    }
    ```

    또는 를 통해 에 `IServiceCollections` `BuilderExtensions` 삽입합니다.

    ```C#
    using System;

    using Azure.Identity;

    using Microsoft.Extensions.Azure;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;

    namespace chatapp
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddAzureClients(builder =>
                {
                    var credential = new DefaultAzureCredential();
                    builder.AddWebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", credential);
                });
            }
        }
    }
    ```

    이 클라이언트를 사용하는 방법에 대한 자세한 내용은 [.NET용 Azure Web PubSub 서비스 클라이언트 라이브러리를](/dotnet/api/overview/azure/messaging.webpubsub-readme-pre) 참조하세요.

## <a name="complete-sample"></a>전체 샘플

- [AAD 인증이 있는 간단한 채팅방](https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/chatapp-aad)