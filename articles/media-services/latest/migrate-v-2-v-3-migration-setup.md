---
title: Media Services v2에서 v3으로 마이그레이션 설정
description: 이 문서는 Azure Media Services v2를 v3으로 마이그레이션하기 위한 환경을 설정하는 데 도움이 됩니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, 마이그레이션, 스트리밍, 브로드캐스트, 라이브, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: c2a08059de275efcfb3e65c6a316566a6a081830
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357956"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>3단계 - V3 REST API 또는 클라이언트 SDK로 마이그레이션하도록 설정

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2단계](./media/migration-guide/steps-3.svg)

다음은 Media Services V3 API를 사용하도록 환경을 설정하는 단계를 설명합니다.

## <a name="sdk-model"></a>SDK 모델

V2 API에는 프로그래밍 방식으로 계정을 만들 수 있도록 하는 API 관리와 리소스 관리를 위한 두 가지 클라이언트 SDK가 있습니다.

이전에는 개발자가 AMS 계정에 대한 특정 V2 REST API 엔드포인트와 함께 Azure 서비스 주체 클라이언트 ID 및 클라이언트 비밀을 사용했습니다.

V3 API는 ARM(Azure 리소스 관리) 기반입니다. Azure AD(Azure Active Directory) 서비스 주체 ID 및 키를 사용하여 API에 연결합니다. 개발자는 API에 연결하기 위해 서비스 주체 또는 관리 ID를 만들어야 합니다. V3 API에서 API는 표준 ARM 엔드포인트를 사용하고 다른 모든 Azure 서비스와 유사하고 일관된 모델을 사용합니다.

이전에 2015-10-01 버전의 ARM management API를 사용 하 여 V2 계정을 관리 하는 고객은 V3 API 액세스에 대해 지원 되는 ARM 관리 API의 2020-05-01 이상 버전을 사용 해야 합니다.

## <a name="create-a-new-media-services-account-for-testing"></a>테스트를 위한 새 미디어 서비스 계정 만들기

Azure Portal을 사용하여 [환경 설정](setup-azure-subscription-how-to.md?tabs=portal)을 위한 빠른 시작 단계를 따르세요. API 액세스 및 서비스 주체 인증을 선택하여 이 테스트 계정에 사용할 새 Azure AD 애플리케이션 ID 및 비밀을 생성합니다.

[Media Services 계정 만들기](account-create-how-to.md?tabs=portal).
[Media Services API에 액세스할 수 있는 자격 증명 가져오기](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>원하는 클라이언트 SDK를 다운로드하고 환경 설정

-  [.NET](/dotnet/api/overview/azure/mediaservices/management), .NET Core,  [Node.js](/javascript/api/overview/azure/mediaservices/management),  [Python](/python/api/overview/azure/mediaservices/management),  [Java](/java/api/overview/azure/mediaservices/management),  [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) 및[Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)에 SDK를 사용할 수 있습니다.
- [Azure CLI](/cli/azure/ams) 통합을 통해 간단한 스크립팅을 지원합니다.

> [!NOTE]
> 커뮤니티 PHP SDK는 Azure Media Services V3에 더 이상 사용할 수 없습니다. V2에서 PHP를 사용하는 경우 코드에서 직접 REST API로 마이그레이션해야 합니다.

## <a name="open-api-specifications"></a>오픈 API 사양

- V3은 Azure Resource Manager에서 빌드된 관리 및 운영 기능을 모두 제공하는 통합된 API 화면을 기반으로 합니다. Azure Resource Manager 템플릿을 사용하여 변환, 스트리밍 엔드포인트, 라이브 이벤트 등을 만들고 배포할 수 있습니다.

- [OpenAPI 사양](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01)(이전의 Swagger) 문서에서는 모든 서비스 구성 요소의 스키마를 설명합니다.

- 모든 클라이언트 SDK는 GitHub에 게시된 Open API 사양에서 파생되고 생성됩니다. 이 문서를 게시할 당시에는 최신 Open API 사양은 GitHub에서 공개적으로 유지 관리되고 있습니다. 2020-05-01 버전은 [안정적인 최신 릴리스](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01)입니다.

## <a name="rest"></a>[REST (영문)](#tab/rest)

Media Services v3 REST API 호출에 [Postman](./setup-postman-rest-how-to.md)을 사용합니다.
[REST API 참조 페이지](/rest/api/media/)를 읽어 보세요.

Postman collection에서 2020-05-01 이상 버전 문자열을 사용 해야 합니다.

## <a name="net"></a>[.NET](#tab/net)

환경을 설정하려면 [.NET으로 Media Services v3 API에 연결](configure-connect-dotnet-howto.md) 문서를 읽어보세요.

단순히 PackageManager를 사용하여 최신 SDK를 설치하려면 다음 명령을 사용합니다.

`Install-Package Microsoft.Azure.Management.Media`

또는 .NET CLI를 사용하여 최신 SDK를 설치하려면 다음 명령을 사용합니다.

`dotnet add package Microsoft.Azure.Management.Media`

또한 다양한 시나리오에 대해 전체 .NET 샘플을 [Azure-Samples/media-services-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet)에서 사용할 수 있습니다. 이 리포지토리의 프로젝트는 v3 버전을 사용하여 다양한 Azure Media Services 시나리오를 구현하는 방법을 보여 줍니다.

### <a name="get-started-adjusting-your-code"></a>코드 조정 시작하기

코드 기반에서 `CloudMediaContext` 사용 인스턴스를 검색하여 V3 API로의 업그레이드 프로세스를 시작합니다.

다음 코드는 이전에 v2 .NET SDK를 사용하여 v2 API에 액세스한 방법을 보여 줍니다. 개발자는 `CloudMediaContext`를 만드는 것에서 시작하여 `AzureAdTokenCredentials` 개체로 인스턴스를 만듭니다.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

[Java로 Media Services v3 API에 연결](configure-connect-java-howto.md) 문서를 읽고 환경을 설정합니다.

## <a name="python"></a>[Python](#tab/python)

[Azure Media Services v3 API에 연결 - Python](configure-connect-python-howto.md) 문서를 읽고 환경을 설정합니다.

## <a name="nodejs"></a>[Node.JS](#tab/nodejs)

[Azure Media Services v3 API에 연결 - Node.js](configure-connect-nodejs-howto.md) 문서를 읽고 환경을 설정합니다.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- GitHub에서 [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK를 다운로드합니다.

## <a name="go"></a>[Go](#tab/go)

[Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK를 다운로드합니다.

---
