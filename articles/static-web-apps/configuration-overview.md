---
title: Azure 정적 Web Apps에 대 한 구성 개요
description: Azure 정적 Web Apps를 구성 하는 다양 한 방법에 대해 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: cshoe
ms.openlocfilehash: 29cd7dbdbaae99d3a861e7f01df870c13785160e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616824"
---
# <a name="configuration-overview"></a>구성 개요

다음은 정적 웹 앱을 구성 하는 데 적용 되는 다른 개념입니다.

- [응용 프로그램 구성](./configuration.md): `staticwebapp.config.json` 응용 프로그램 동작 및 기능을 제어 하는 파일의 규칙을 정의 합니다. 이 파일을 사용 하 여 경로 및 보안 규칙, 사용자 지정 헤더 및 네트워킹 설정을 정의 합니다.

- [빌드 구성](./build-configuration.md): 빌드 프로세스를 제어 하는 설정을 정의 합니다.

- [응용 프로그램 설정](./application-settings.md): 응용 프로그램 수준 설정 및 환경 변수를 설정 합니다.

## <a name="example-scenarios"></a>예제 시나리오

| 다음을 원하는 경우... | 구현 방법... |
|---|---|
| 라우팅 규칙 정의 | [staticwebapps.config json 파일에서 규칙을 만듭니다.](./configuration.md) |
| 빌드를 트리거하는 분기 설정 | [빌드 구성 파일에서 추적 된 분기 이름 업데이트](./build-configuration.md)  |
| 경로에 대 한 액세스 권한이 있는 보안 역할 정의 | [staticwebapps.config json 파일의 역할을 사용 하 여 경로 보호](./configuration.md#securing-routes-with-roles) |
| 경로가 실제 파일과 일치 하지 않는 경우 제공 되는 HTML 파일을 설정 합니다. | [staticwebapps.config json 파일에서 대체 (fallback) 경로를 정의 합니다.](./configuration.md#fallback-routes) |
| HTTP 요청에 대 한 전역 헤더 설정 | [staticwebapps.config json 파일에서 전역 헤더를 정의 합니다.](./configuration.md#global-headers)|
| 사용자 지정 빌드 명령 정의 | [응용 프로그램 구성 파일에서 사용자 지정 빌드 명령 값 설정](./build-configuration.md) |
| 프런트 엔드 빌드에 대 한 환경 변수 설정 | [빌드 구성 파일에서 환경 변수 정의](./build-configuration.md#environment-variables) |
| API에 대 한 환경 변수 설정 | [포털에서 응용 프로그램 설정 설정](./application-settings.md) |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [애플리케이션 구성](configuration.md)
