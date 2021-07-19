---
title: .NET 앱용 Azure Application Insights 스냅샷 디버거
description: 프로덕션 .NET 앱에서 예외가 throw되면 디버그 스냅샷이 자동으로 수집됨
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 10/23/2019
author: cweining
ms.author: cweining
ms.reviewer: cweining
ms.openlocfilehash: c377f6b1ff84f04506badf581dc7c12d64e68e0d
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109839386"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET 앱의 예외에 대한 디버그 스냅샷
예외가 발생할 때 라이브 웹 애플리케이션에서 자동으로 디버그 스냅샷을 수집할 수 있습니다. 스냅샷은 예외가 throw되었을 때의 소스 코드 및 변수의 상태를 보여 줍니다. [Azure Application Insights](./app-insights-overview.md)의 스냅샷 디버거는 웹앱에서 예외 원격 분석을 모니터링합니다. 프로덕션에서 문제를 진단하는 데 필요한 정보를 유지하도록 많이 throw되는 예외에 대한 스냅샷을 수집합니다. 애플리케이션에 [스냅샷 수집기 NuGet 패키지](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)를 포함하고 선택적으로 [ApplicationInsights.config](./configuration-with-applicationinsights-config.md)에서 컬렉션 매개 변수를 구성합니다. 스냅샷은 Application Insights 포털의 [예외](./asp-net-exceptions.md)에 표시됩니다.

포털에서 디버그 스냅샷을 확인하여 호출 스택을 보고 각 호출 스택 프레임에서 변수를 검사할 수 있습니다. 소스 코드가 있는 좀 더 강력한 디버깅 환경을 구현하려면 Visual Studio 2019 Enterprise에서 스냅샷을 엽니다. 또한 Visual Studio에서 예외를 기다리지 않고 [snappoint에서 대화형으로 스냅샷을 만들도록 설정](/visualstudio/debugger/debug-live-azure-applications)할 수도 있습니다.

디버그 스냅샷은 15일 동안 저장됩니다. 이 보존 정책은 애플리케이션 단위로 설정됩니다. 이 값을 늘려야 하는 경우 Azure Portal에서 지원 사례를 열어 증가를 요청할 수 있습니다.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>애플리케이션에 대해 Application Insights 스냅샷 디버거 사용
스냅샷 컬렉션을 다음에 사용할 수 있습니다.
* .NET Framework 및 .NET Framework 4.5 이상을 실행하는 ASP.NET 애플리케이션
* Windows에서 .NET Core 2.1(LTS) 또는 3.1(LTS)을 실행하는 .NET Core 및 ASP.NET Core 애플리케이션.
* Windows의 .NET 5.0 애플리케이션.

.NET Core 2.0, 2.2 또는 3.0은 지원되지 않기 때문에 사용하지 않는 것이 좋습니다.

다음 환경이 지원됩니다.

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 함수](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* OS 제품군 4 이상을 실행하는 [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 이상을 실행하는 [Azure Service Fabric 서비스](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 이상을 실행하는 [Azure Virtual Machines 및 가상 머신 확장 집합](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 이상 또는 Windows 8.1 이상을 실행하는 [온-프레미스 가상 머신 또는 물리적 머신](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> 클라이언트 애플리케이션(예를 들어, WPF, Windows Forms 또는 UWP)은 지원되지 않습니다.

스냅샷 디버거를 사용하도록 설정해도 스냅샷이 표시되지 않으면 [문제 해결 가이드](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)를 확인하세요.

## <a name="grant-permissions"></a>권한 부여

스냅샷에 대한 액세스는 Azure RBAC(Azure 역할 기반 액세스 제어)로 보호됩니다. 스냅샷을 검사하려면 먼저 구독 소유자가 사용자를 필요한 역할에 추가해야 합니다.

> [!NOTE]
> 소유자 및 참가자는 이 역할을 자동으로 소유하지는 않습니다. 스냅샷을 보려면 해당 스냅샷을 역할에 추가해야 합니다.

구독 소유자는 스냅샷을 검사할 사용자에게 `Application Insights Snapshot Debugger` 역할을 할당해야 합니다. 대상 Application Insights 리소스 또는 리소스 그룹이나 구독에 대한 구독 소유자가 개별 사용자 또는 그룹에 이 역할을 할당할 수 있습니다.

1. Azure Portal에서 Application Insights 리소스로 이동합니다.
1. **액세스 제어(IAM)** 를 클릭합니다.
1. **+역할 할당 추가** 단추를 클릭합니다.
1. **역할** 드롭다운 목록에서 **Application Insights 스냅샷 디버거** 를 선택합니다.
1. 추가할 사용자의 이름을 검색하고 입력합니다.
1. **저장** 단추를 클릭하여 역할에 사용자를 추가합니다.


> [!IMPORTANT]
> 스냅샷에는 변수 및 매개 변수 값에 개인 데이터 또는 기타 중요한 정보가 포함될 수 있습니다. 스냅샷 데이터는 App Insights 리소스와 동일한 지역에 저장됩니다.

## <a name="view-snapshots-in-the-portal"></a>포털에서 스냅샷 보기

애플리케이션에서 예외가 발생하고 스냅샷이 생성되고 나면 볼 수 있는 스냅샷이 있어야 합니다. 예외가 발생한 시간부터 포털에서 스냅샷이 준비되어 볼 수 있을 때까지 5 ~ 10분이 걸릴 수 있습니다. 스냅샷을 보려면 **실패** 창에서 **작업** 탭을 보는 경우 **작업** 단추를 선택하거나 **예외** 탭을 보는 경우 **예외** 단추를 선택합니다.

![실패 페이지](./media/snapshot-debugger/failures-page.png)

오른쪽 창에서 작업 또는 예외를 선택하여 **엔드투엔드 트랜잭션 세부 정보** 창을 연 다음 예외 이벤트를 선택합니다. 지정된 예외에 대해 스냅샷을 사용할 수 있는 경우 [예외](./asp-net-exceptions.md)에 대한 세부 정보와 함께 **디버그 스냅샷 열기** 단추가 오른쪽 창에 나타납니다.

![예외에서 디버그 스냅샷 열기 단추](./media/snapshot-debugger/e2e-transaction-page.png)

디버그 스냅샷 보기에는 호출 스택 및 변수 창이 표시됩니다. 호출 스택 창에서 호출 스택의 프레임을 선택하면 변수 창에 해당 함수 호출에 대한 지역 변수 및 매개 변수가 표시됩니다.

![포털에서 디버그 스냅샷 보기](./media/snapshot-debugger/open-snapshot-portal.png)

중요한 정보가 스냅샷에 포함될 수 있으며 기본적으로 표시되지 않습니다. 스냅샷을 보려면 `Application Insights Snapshot Debugger` 역할이 할당되어 있어야 합니다.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Visual Studio 2017 Enterprise 이상에서 스냅샷 보기
1. **스냅샷 다운로드** 단추를 클릭하여 Visual Studio Enterprise에서 열 수 있는 `.diagsession` 파일을 다운로드합니다.

2. `.diagsession` 파일을 열려면 스냅샷 디버거 Visual Studio 구성 요소를 설치해야 합니다. 스냅샷 디버거 구성 요소는 Visual Studio에서 ASP.net 워크로드의 필수 구성 요소이며 Visual Studio 설치 관리자의 개별 구성 요소 목록에서 선택할 수 있습니다. Visual Studio 2017 버전 15.5 이전 버전의 Visual Studio를 사용하는 경우 [Visual Studio Marketplace](https://aka.ms/snapshotdebugger)에서 확장을 설치해야 합니다.

3. 스냅샷 파일을 연 후에 Visual Studio에서 미니덤프 디버깅 페이지가 표시됩니다. **관리 코드 디버그** 를 클릭하여 스냅샷을 디버깅하기 시작합니다. 예외가 throw되는 코드 줄에 스냅샷이 열리고 프로세스의 현재 상태를 디버그할 수 있습니다.

    ![Visual Studio에서 디버그 스냅샷 보기](./media/snapshot-debugger/open-snapshot-visualstudio.png)

다운로드한 스냅샷에는 웹 애플리케이션 서버에 있는 모든 기호 파일이 포함되어 있습니다. 이러한 기호 파일은 소스 코드에 스냅샷 데이터를 연결하는 데 필요합니다. App Service 앱의 경우 웹앱을 게시할 때 기호 배포를 사용할 수 있는지를 확인합니다.

## <a name="how-snapshots-work"></a>스냅샷 작동 방식

스냅샷 수집기는 [Application Insights 원격 분석 프로세서](./configuration-with-applicationinsights-config.md#telemetry-processors-aspnet)로 구현됩니다. 애플리케이션이 실행되면 스냅샷 수집기 원격 분석 프로세서가 애플리케이션의 원격 분석 파이프라인에 추가됩니다.
애플리케이션에서 [TrackException](./asp-net-exceptions.md#exceptions)을 호출할 때마다 스냅샷 수집기는 throw된 예외의 형식과 throw하는 메서드에서 문제 ID를 계산합니다.
애플리케이션에서 TrackException을 호출할 때마다 해당 문제 ID에 대한 카운터가 증가합니다. 카운터가 `ThresholdForSnapshotting` 값에 도달하면 문제 ID가 수집 계획에 추가됩니다.

또한 Snapshot Collector는 [AppDomain.CurrentDomain.FirstChanceException](/dotnet/api/system.appdomain.firstchanceexception) 이벤트에 가입하여 예외가 throw되었을 때 이를 모니터링합니다. 해당 이벤트가 발생하면 예외의 문제 ID가 계산되어 수집 계획의 문제 ID와 비교됩니다.
일치하는 항목이 있으면 실행 중인 프로세스의 스냅샷이 만들어집니다. 스냅샷에는 고유 식별자가 할당되고, 예외는 해당 식별자로 스탬프 처리됩니다. FirstChanceException 처리기가 반환되면 throw된 예외는 정상으로 처리됩니다. 결국, 예외는 스냅샷 식별자와 함께 Application Insights에 보고되는 TrackException 메서드에 다시 도달합니다.

주 프로세스는 계속 실행되고 매우 짧은 중단을 통해 사용자에게 트래픽을 제공합니다. 한편 스냅샷은 스냅샷 업로더 프로세스에 전달됩니다. 스냅샷 업로더는 미니덤프를 만들고, 관련된 모든 기호(.pdb) 파일과 함께 이를 Application Insights에 업로드합니다.

> [!TIP]
> - 프로세스 스냅샷은 실행 중인 프로세스의 일시 중단된 복제본입니다.
> - 스냅샷을 만드는 데는 약 10~20 밀리초가 걸립니다.
> - `ThresholdForSnapshotting`의 기본값은 1이며, 최솟값이기도 합니다. 따라서 스냅샷을 만들려면 먼저 앱에서 동일한 예외를 **두 번** 트리거해야 합니다.
> - Visual Studio에서 디버그하는 동안 스냅샷을 생성하려면 `IsEnabledInDeveloperMode`를 true로 설정합니다.
> - 스냅샷을 만드는 속도는 `SnapshotsPerTenMinutesLimit` 설정으로 제한됩니다. 기본적으로 10분마다 하나의 스냅샷으로 제한됩니다.
> - 매일 최대 50개의 스냅샷을 업로드할 수 있습니다.

## <a name="limitations"></a>제한 사항

기본 데이터 보존 기간은 15일입니다. Application Insights 인스턴스마다 하루에 최대 50개의 스냅샷이 허용됩니다.

### <a name="publish-symbols"></a>기호 게시
스냅샷 디버거를 사용하려면 Visual Studio에서 변수를 디코딩하고 디버깅 환경을 제공하기 위해 프로덕션 서버에 기호 파일이 있어야 합니다.
Visual Studio 2017의 15.2 버전 이상은 App Service에 게시할 때 기본적으로 릴리스 빌드에 대한 기호를 게시합니다. 이전 버전에서는 기호가 릴리스 모드에서 게시될 수 있게 게시 프로필 `.pubxml` 파일에 다음 줄을 추가해야 합니다.

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Azure Compute 및 기타 형식의 경우 기호 파일이 주 애플리케이션 .dll의 동일한 폴더(일반적으로 `wwwroot/bin`)에 있거나 현재 경로에서 사용할 수 있는지 확인합니다.

> [!NOTE]
> 사용할 수 있는 다른 기호 옵션에 관한 자세한 내용은 [Visual Studio 설명서](/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019&preserve-view=true#output
)를 참조하세요. 최상의 결과를 위해서는 “전체”, “이식 가능” 또는 “포함”을 사용하는 것이 좋습니다.

### <a name="optimized-builds"></a>최적화된 빌드
경우에 따라 JIT 컴파일러에서 적용한 최적화로 인해 릴리스 빌드에서 지역 변수가 표시되지 않습니다.
그러나 Azure App Services에서 스냅샷 수집기는 수집 계획에 속한 throw하는 메서드를 최적화 해제할 수 있습니다.

> [!TIP]
> 최적화 해제 지원을 받으려면 Application Insights 사이트 확장을 App Service에 설치합니다.

## <a name="next-steps"></a>다음 단계
애플리케이션에 대해 Application Insights 스냅샷 디버거를 사용으로 설정합니다.

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 함수](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 서비스](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines 및 Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [온-프레미스 가상 머신 또는 물리적 머신](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights 스냅샷 디버거 이상:
 
* 예외를 기다리지 않고 스냅샷을 가져오기 위해 [코드에서 snappoint를 설정](/visualstudio/debugger/debug-live-azure-applications)합니다.
* [웹앱의 예외 진단](./asp-net-exceptions.md)에서는 Application Insights에서 추가 예외를 표시하는 방법을 설명합니다.
* [스마트 검색](./proactive-diagnostics.md)은 성능 예외를 자동으로 검색합니다.