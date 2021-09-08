---
title: Azure Functions 언어 런타임 지원 정책
description: Azure Functions 언어 런타임 지원 정책에 대해 알아보기
ms.topic: conceptual
ms.date: 08/17/2021
ms.openlocfilehash: b3b5f7cf108fd18ed450a6837a5dd35ceb83dc60
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303970"
---
# <a name="language-runtime-support-policy"></a>언어 런타임 지원 정책

이 문서에서는 Azure 함수 언어 런타임 지원 정책을 설명합니다. 

## <a name="retirement-process"></a>사용 중지 프로세스

Azure Functions 런타임은 운영 체제, Azure Functions 호스트 및 언어별 작업자를 비롯한 다양한 구성 요소를 중심으로 빌드됩니다. 함수 앱에 대한 전체 지원 범위를 유지하기 위해 Azure Functions는 프로그래밍 언어 버전이 수명 종료 날짜에 도달하면 지원이 단계적으로 감소합니다. 대부분의 언어 버전에서 사용 중지 날짜는 커뮤니티 수명 종료 날짜와 일치합니다. 

### <a name="notification-phase"></a>알림 단계

예정된 언어 버전 사용 중지에 대한 알림 이메일을 함수 앱 사용자에게 보냅니다. 알림은 사용 중지 날짜의 최소 1년 전에 만료됩니다. 알림이 있으면 함수 앱에서 사용하는 언어 버전을 지원되는 버전으로 업그레이드할 준비를 해야 합니다.

### <a name="retirement-phase"></a>사용 중지 단계

언어 버전의 수명 종료 날짜부터는 해당 언어 버전을 대상으로 하는 새 함수 앱을 더 이상 만들 수 없습니다.

언어 수명 종료 날짜 이후 사용 중지된 언어 버전을 사용하는 함수 앱은 새로운 기능, 보안 패치 및 성능 최적화에 적합하지 않습니다. 그러나 이러한 함수 앱은 플랫폼에서 계속 실행됩니다. 

> [!IMPORTANT]
>영향을 받는 함수 앱의 언어 버전을 지원되는 버전으로 업그레이드하는 것이 좋습니다.   
>지원되지 않는 언어 버전을 사용하여 함수 앱을 실행하는 경우 함수 앱에 대한 지원을 받기 전에 업그레이드해야 합니다.


## <a name="retirement-policy-exceptions"></a>사용 중지 정책 예외

위에서 설명한 사용 중지 정책에는 몇 가지 예외가 있습니다. 다음은 수명 종료 날짜에 근접하거나 도달했지만 추가 공지가 있을 때까지 플랫폼에서 계속 지원되는 언어 목록입니다. 이러한 언어 버전이 수명 종료 날짜에 도달하면 더 이상 업데이트되거나 패치되지 않습니다. 따라서 이러한 언어 버전에서 함수 앱을 개발하고 실행하지 않는 것이 좋습니다.

|언어 버전                        |EOL 날짜         |사용 중지 날짜|
|-----------------------------------------|-----------------|----------------|
|.NET 5|2022년 2월|TBA|
|Node 6|2019년 4월 30일|2022년 2월 28일| 
|Node 8|2019년 12월 31일|2022년 2월 28일| 
|Node 10|2021년 4월 30일|2022년 9월 30일| 
|PowerShell Core 6| 2020년 9월 4일|2022년 9월 30일|
|Python 3.6 |2021년 12월 23일|2022년 9월 30일| 
 

## <a name="language-version-support-timeline"></a>언어 버전 지원 타임라인

특정 언어 버전 지원 정책 타임라인에 대한 자세한 내용은 다음 외부 리소스를 방문하세요.
* .NET - [dotnet.microsoft.com](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)
* Node - [github.com](https://github.com/nodejs/Release#release-schedule)
* Java - [azul.com](https://www.azul.com/products/azul-support-roadmap/)
* PowerShell - [dotnet.microsoft.com](/powershell/scripting/powershell-support-lifecycle?view=powershell-7.1&preserve-view=true#powershell-releases-end-of-life)
* Python - [devguide.python.org](https://devguide.python.org/#status-of-python-branches)

## <a name="configuring-language-versions"></a>언어 버전 구성

|언어                         | 구성 가이드         |
|-----------------------------------------|-----------------|
|C#(클래스 라이브러리) |[링크](./functions-dotnet-class-library.md#supported-versions)|
|노드 |[링크](./functions-reference-node.md#setting-the-node-version)|
|PowerShell |[링크](./functions-reference-powershell.md#changing-the-powershell-version)|
|Python |[링크](./functions-reference-python.md#python-version)|
 

## <a name="next-steps"></a>다음 단계

함수 앱 언어 버전을 업그레이드하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.


+ [현재 지원되는 언어 버전](./supported-languages.md#languages-by-runtime-version)
