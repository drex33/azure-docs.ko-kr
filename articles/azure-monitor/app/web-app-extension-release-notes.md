---
title: Azure 웹 앱 확장에 대 한 릴리스 정보-Application Insights
description: Application Insights를 사용 하 여 런타임 계측에 대 한 Azure Web Apps 확장에 대 한 정보를 릴리스 합니다.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 8b0ddf90f5e6ffbe685c4aea1239323beea6c106
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828817"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Application Insights용 Azure 웹앱 확장에 대한 릴리스 정보

이 문서에는 Application Insights를 사용 하는 런타임 계측에 대 한 Azure Web Apps 확장의 릴리스 정보가 포함 되어 있습니다. 이는 사전 설치 된 확장에만 적용 됩니다.

[Application Insights에 대 한 Azure 웹 앱 확장](azure-web-apps.md)에 대해 자세히 알아보세요.

## <a name="frequently-asked-questions"></a>질문과 대답

- 현재 확장 된 확장의 버전을 확인 하는 방법
    - `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`로 이동합니다. 자세한 내용은 [ASP.NET Core](./azure-web-apps-net-core.md#troubleshooting), [ASP.NET](./azure-web-apps-net.md#troubleshooting), [Java](./azure-web-apps-java.md#troubleshooting)또는 [Node.js](./azure-web-apps-nodejs.md#troubleshooting) 에 대 한 확장/에이전트 기반 모니터링에 대 한 단계별 문제 해결 가이드를 참조 하세요.

- 개인 확장을 사용 하는 경우 어떻게 되나요?
    - 더 이상 지원 되지 않으므로 개인 사이트 확장을 제거 합니다.

## <a name="release-notes"></a>릴리스 정보

### <a name="2838"></a>2.8.38

- JAVA 확장: 2.5.1에서 [GA (Java Agent 3.0.2)](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.0.2) 로 업그레이드 되었습니다.
- Node.js 확장: AI SDK를 1.8.7에서 [1.8.8](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/1.8.8) 로 업데이트 했습니다.
- .NET Core: 지원 되지 않는 버전 (2.0, 2.2, 3.0)을 제거 했습니다. 지원 되는 버전은 2.1 및 3.1입니다.

### <a name="2837"></a>2.8.37

- appsvc Windows 확장: 모든 버전의 System.Diagnostics.DiagnosticSource.dll를 사용 하 여 .net Core를 사용할 수 있습니다.

### <a name="2836"></a>2.8.36

- appsvc Windows 확장: .net Core에서 AI SDK와 함께 작동 하도록 설정 합니다.

### <a name="2835"></a>2.8.35

- appsvc Windows 확장: .net Core 3.1 지원을 추가 했습니다.

### <a name="2833"></a>2.8.33

- .net, .net core, Java, Node.js 에이전트 및 Windows 확장: 소 버린 클라우드 지원. 연결 문자열을 사용 하 여 소 버린 클라우드로 데이터를 보낼 수 있습니다.

### <a name="2831"></a>2.8.31

- ASP.NET Core 에이전트: 업데이트 된 Application Insights SDK의 참조 중 하 나와 관련 된 문제를 해결 했습니다 (2.8.26의 알려진 문제 참조). 잘못 된 버전의 `System.Diagnostics.DiagnosticSource.dll` 가 런타임에 이미 로드 된 경우 코드 없는 확장은 응용 프로그램 충돌을 방지 하 고 백업 합니다. 해당 문제의 영향을 받는 고객의 경우 `System.Diagnostics.DiagnosticSource.dll` bin 폴더에서을 제거 하거나 "ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24"를 설정 하 여 이전 버전의 확장을 사용 하는 것이 좋습니다. 그렇지 않으면 응용 프로그램 모니터링이 사용 되지 않습니다.

### <a name="2826"></a>2.8.26

- ASP.NET Core 에이전트: 업데이트 된 Application Insights SDK와 관련 된 문제가 해결 되었습니다. ApplicationInsights.dll 이미 bin 폴더에 있는 경우 에이전트에서 로드를 시도 하지 않습니다 `AiHostingStartup` . 이는 어셈블리를 통한 리플렉션과 관련 된 문제를 해결 \<AiHostingStartup\> 합니다. GetTypes ().
- 알려진 문제: `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` 다른 버전의 `DiagnosticSource` dll이 로드 되 면 예외가 throw 될 수 있습니다. 예를 들어 `System.Diagnostics.DiagnosticSource.dll` 가 publish 폴더에 있는 경우이 문제가 발생할 수 있습니다. 완화 방법으로 app services: ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24에서 앱 설정을 설정 하 여 이전 버전의 확장을 사용 합니다.

### <a name="2824"></a>2.8.24

- 2.8.21의 다시 패키지 된 버전입니다.

### <a name="2823"></a>2.8.23

- ASP.NET Core 3.0 코드 없는 모니터링 지원을 추가 했습니다.
- 런타임 버전 2.1, 2.2 및 3.0에 대 한 ASP.NET Core SDK를 [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) 로 업데이트 했습니다. .NET Core 2.0을 대상으로 하는 앱은 SDK의 2.1.1 계속 사용 합니다.

### <a name="2814"></a>2.8.14

- 2.3.0 SDK 버전을 .net Core 2.1, 2.2를 대상으로 하는 앱에 대 한 최신 (2.6.1)에서 최신 ()로 업데이트 ASP.NET Core 했습니다. .NET Core 2.0을 대상으로 하는 앱은 SDK의 2.1.1 계속 사용 합니다.

### <a name="2812"></a>2.8.12

- ASP.NET Core 2.2 앱에 대 한 지원.
- 응용 프로그램이 이미 sdk를 사용 하 여 계측 된 경우에도 sdk를 주입 하는 ASP.NET Core 확장의 버그를 수정 했습니다. 2.1 및 2.2 앱의 경우, 이제 응용 프로그램 폴더에 ApplicationInsights.dll 있으면 확장이 백오프 됩니다. 2.0 앱의 경우, 호출로 ApplicationInsights를 사용 하도록 설정한 경우에만 확장이 백업 됩니다 `UseApplicationInsights()` .

- ASP.NET Core 앱에 대 한 불완전 한 HTML 응답을 영구적으로 수정 합니다. 이 픽스는 이제 .NET Core 2.2 앱에 대해 작동 하도록 확장 되었습니다.

- ASP.NET Core apps ()에 대 한 JavaScript 주입을 해제 하는 지원이 추가 되었습니다 `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` . ASP.NET core의 경우 명시적으로 해제 하지 않는 한 JavaScript 주입은 기본적으로 "옵트아웃" 모드로 설정 됩니다. 기본 설정은 현재 동작을 유지 하기 위해 수행 됩니다.

- ikey가 없는 경우에도 삽입을 발생 시킨 ASP.NET Core 확장 버그를 수정 했습니다.
- SDK 버전 접두사 논리에서 원격 분석에 잘못 된 SDK 버전을 발생 시킨 버그를 수정 했습니다.

- 원격 분석이 수집 된 방법을 식별 하기 위해 ASP.NET Core 앱에 대 한 SDK 버전 접두사가 추가 되었습니다.
- 미리 설치 된 확장의 버전을 올바르게 표시 하도록 SCM-ApplicationInsights 페이지를 수정 했습니다.

### <a name="2810"></a>2.8.10

- ASP.NET Core 앱에 대 한 불완전 한 HTML 응답을 수정 합니다.

## <a name="next-steps"></a>다음 단계

- Azure 앱 서비스에 대 한 모니터링을 구성 하는 방법에 대 한 자세한 내용은 [Azure App Service에 대 한 응용 프로그램 모니터링 설명서](azure-web-apps.md) 를 참조 하세요. 
