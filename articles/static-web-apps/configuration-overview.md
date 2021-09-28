---
title: Azure Static Web Apps 대한 구성 개요
description: Azure Static Web Apps 구성하는 다양한 방법에 대해 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: cshoe
ms.openlocfilehash: 34a93dc1203fb404d0d6c2edfd70954545e26b15
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154929"
---
# <a name="configuration-overview"></a>구성 개요

정적 웹앱 구성에는 다음과 같은 다양한 개념이 적용됩니다.

- [애플리케이션 구성:](./configuration.md)파일에서 애플리케이션 동작 및 기능을 제어하는 규칙을 `staticwebapp.config.json` 정의합니다. 이 파일을 사용하여 경로 및 보안 규칙, 사용자 지정 헤더 및 네트워킹 설정을 정의합니다.

- [빌드 구성: 빌드](./build-configuration.md)프로세스를 제어하는 설정을 정의합니다.

- [애플리케이션 설정:](./application-settings.md)백 엔드 API에서 사용할 수 있는 애플리케이션 수준 설정 및 환경 변수를 설정합니다.

## <a name="example-scenarios"></a>예제 시나리오

| 다음을 원하는 경우... | 구현 방법... |
|---|---|
| 라우팅 규칙 정의 | [staticwebapps.config.json 파일에서 규칙 만들기](./configuration.md) |
| 빌드를 트리거하는 분기 설정 | [빌드 구성 파일에서 추적된 분기 이름 업데이트](./build-configuration.md)  |
| 경로에 액세스할 수 있는 보안 역할 정의 | [staticwebapps.config.json 파일에서 역할을 통해 경로 보호](./configuration.md#securing-routes-with-roles) |
| 경로가 실제 파일과 일치하지 않는 경우 제공되는 HTML 파일 설정 | [staticwebapps.config.json 파일에서 대체 경로 정의](./configuration.md#fallback-routes) |
| HTTP 요청에 대한 전역 헤더 설정 | [staticwebapps.config.json 파일에서 전역 헤더 정의](./configuration.md#global-headers)|
| 사용자 지정 빌드 명령 정의 | [애플리케이션 구성 파일에서 사용자 지정 빌드 명령 값 설정](./build-configuration.md) |
| 프런트 엔드 빌드에 대한 환경 변수 설정 | [빌드 구성 파일에서 환경 변수 정의](./build-configuration.md#environment-variables) |
| API에 대한 환경 변수 설정 | [포털에서 애플리케이션 설정 설정](./application-settings.md) |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [애플리케이션 구성](configuration.md)
