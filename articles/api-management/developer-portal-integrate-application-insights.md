---
title: 개발자 포털에 Application Insights 통합
titleSuffix: Azure API Management
description: 관리형 또는 자체 호스팅 개발자 포털에 Application Insights를 통합하는 방법을 알아봅니다.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: b495a6141e064df80e57c4c96eff67fc928d58c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601693"
---
# <a name="integrate-application-insights-to-developer-portal"></a>개발자 포털에 Application Insights 통합

Application Insights는 Azure Monitor의 널리 사용되는 기능 중 하나입니다. Application Insights는 개발자와 DevOps 전문가를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. Application Insights를 사용하여 개발자 포털을 모니터링하고 성능 변칙을 검색하세요. Application Insights에는 개발자 포털을 방문한 사용자들이 실제로 어떤 일들을 하는지 알아보는 데 도움이 되는 강력한 분석 도구가 포함되어 있습니다.

## <a name="add-application-insights-to-your-portal"></a>포털에 Application Insights 추가

아래의 단계에 따라 관리형 또는 자체 호스팅 개발자 포털에 Application Insights를 연결합니다.

> [!IMPORTANT]
> 관리형 포털의 경우 1단계와 2단계가 필요하지 않습니다. 관리형 포털을 사용하는 경우 4단계로 건너뛰세요.

1. 개발자 포털의 최신 릴리스에 대해 [로컬 환경](developer-portal-self-host.md#step-1-set-up-local-environment)을 설정합니다.

1. **npm** 패키지를 설치하여 [Azure용 Paperbits](https://github.com/paperbits/paperbits-azure)를 추가합니다.

    ```console
    npm install @paperbits/azure --save
    ```

1. `src` 폴더의 `startup.publish.ts` 파일에서 Application Insights 모듈을 가져오고 등록합니다.

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. 포털의 구성을 가져옵니다.

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. 앞 단계의 사이트 구성을 Application Insights 구성으로 확장합니다.

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>다음 단계

다음 문서를 통해 개발자 포털에 대해 자세히 알아봅니다.

- [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)
- [포털 배포 자동화](automate-portal-deployments.md)
- [Self-host the developer portal](developer-portal-self-host.md)(개발자 포털 자체 호스팅)
