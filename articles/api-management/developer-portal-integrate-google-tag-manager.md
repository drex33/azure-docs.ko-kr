---
title: 개발자 포털에 Google 태그 관리자 통합
titleSuffix: Azure API Management
description: Azure API Management에서 관리형 또는 자체 호스팅 개발자 포털에 Google 태그 관리자를 연결하는 방법을 알아봅니다.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: a3436727470ceb073f5431ef9677fbc17afdda84
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601675"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>API Management 개발자 포털에 Google 태그 관리자 통합

[Google 태그 관리자](https://developers.google.com/tag-manager)는 Google에서 만든 태그 관리 시스템입니다. Google 태그 관리자를 사용하여 웹 사이트에서 추적 및 분석에 사용되는 JavaScript 및 HTML 태그를 관리할 수 있습니다. 예를 들어, Google 태그 관리자를 사용하여 Google Analytics, 열 지도 또는 LiveChat과 같은 챗봇을 통합할 수 있습니다.

이 문서에서 안내하는 단계에 따라 Azure API Management에서 관리형 또는 자체 호스팅 개발자 포털에 Google 태그 관리자를 연결하세요.

## <a name="add-google-tag-manager-to-your-portal"></a>포털에 Google 태그 관리자 추가

아래의 단계에 따라 관리형 또는 자체 호스팅 개발자 포털에 Google 태그 관리자를 연결합니다.

> [!IMPORTANT]
> 관리형 포털의 경우 1단계와 2단계가 필요하지 않습니다. 관리형 포털을 사용하는 경우 4단계로 건너뛰세요.

1. 개발자 포털의 최신 릴리스에 대해 [로컬 환경](developer-portal-self-host.md#step-1-set-up-local-environment)을 설정합니다.

1. **npm** 패키지를 설치하여 [Google 태그 관리자용 Paperbits](https://github.com/paperbits/paperbits-gtm)를 추가합니다.

    ```sh
    npm install @paperbits/gtm --save
    ```

1. `src` 폴더의 `startup.publish.ts` 파일에서 GTM 모듈을 가져오고 등록합니다.

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. 앞 단계의 사이트 구성을 Google 태그 관리자 구성으로 확장합니다.

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>다음 단계

- [포털 배포 자동화](automate-portal-deployments.md)
- [Self-host the developer portal](developer-portal-self-host.md)(개발자 포털 자체 호스팅)