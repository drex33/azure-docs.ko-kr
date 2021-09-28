---
title: Microsoft.ApplicationInsights.SnapshotCollector NuGet 패키지에 대한 릴리스 정보 - Application Insights
description: Application Insights 스냅샷 디버거에서 사용하는 Microsoft.ApplicationInsights.SnapshotCollector NuGet 패키지에 대한 릴리스 정보입니다.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: a1c74f23076c456abeb056e68e69a098d873e16d
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154568"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Microsoft.ApplicationInsights.SnapshotCollector에 대한 릴리스 정보

이 문서에는 Application Insights 스냅샷 디버거에서 사용하는 .NET 애플리케이션에 대한 Microsoft.ApplicationInsights.SnapshotCollector NuGet 패키지에 대한 릴리스 정보가 포함되어 있습니다.

.NET 애플리케이션에 대한 Application Insights 스냅샷 디버거에 대해 자세히 [알아봅니다](./snapshot-debugger.md) .

버그 보고서 및 피드백의 경우, https://github.com/microsoft/ApplicationInsights-SnapshotCollector 에서 GitHub에 대한 문제를 엽니다.

## <a name="release-notes"></a>릴리스 정보

## <a name="142"></a>[1.4.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.4.1)
사용자가 보고 한 버그를 해결 하기 위한 지점 릴리스입니다.
### <a name="bug-fixes"></a>버그 수정
- Fix [ArgumentException: 대리자는 동일한 형식 이어야 합니다.](https://github.com/microsoft/ApplicationInsights-SnapshotCollector/issues/16)

## <a name="141"></a>[1.4.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.4.1)
1.4.0에 도입 된 주요 변경 내용을 되돌리는 지점 릴리스입니다.
### <a name="bug-fixes"></a>버그 수정
- [WebJobs에서 수정 메서드를 찾을 수 없습니다](https://github.com/microsoft/ApplicationInsights-SnapshotCollector/issues/15) .

## <a name="140"></a>[1.4.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.4.0)
여러 향상 된 기능을 제공 하 고 Application Insights 수집을 위해 AAD (Azure Active Directory) 인증에 대 한 지원을 추가 했습니다.
### <a name="changes"></a>변경
- Snapshot Collector 패키지 크기가 60% 감소 했습니다. 10.34 MB에서 4.11 MB까지
- Snapshot Collector 에서만 netstandard 2.0을 대상으로 합니다.
- 2.15.0에 대 한 Application Insights SDK 종속성을 범프 합니다.
- 덤프를 쓸 때 MinidumpWithThreadInfo를 추가 합니다.
- Snapshot Collector 에이전트와 업 로더 간의 동기화를 향상 시키기 위해 CompatibilityVersion을 추가 합니다.
- App Service에서 과도 한 파일 i/o를 방지 하려면 SnapshotUploader LogFile 명명 알고리즘을 변경 합니다.
- 업로드 된 blob 메타 데이터에 pid, 역할 이름 및 프로세스 시작 시간을 추가 합니다.
- 가능한 경우 Snapshot Collector 및 스냅숏 업 로더를 사용 합니다.
### <a name="new-features"></a>새 기능
- Microsoft.applicationinsights.snapshotcollector에 Azure Active Directory 인증을 추가 합니다. Application Insights Azure AD 인증에 대 한 자세한 내용은 [여기](./azure-ad-authentication.md)를 참조 하세요.

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
1\.4.0-pre에서 픽스를 백포트하는 포인트 릴리스입니다.
### <a name="bug-fixes"></a>버그 수정
- [종료 시 ObjectDisposedException](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097)을 수정합니다.

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
Azure App Service의 코드 없는 연결 시나리오 테스트에서 발견된 문제를 해결하기 위한 포인트 릴리스입니다.
### <a name="changes"></a>변경
- netcoreapp3.0 대상은 이제 Microsoft.ApplicationInsights.AspNetCore >= 2.1.1 (previously >= 2.1.2)에 종속됩니다.

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
두 가지 심각한 문제를 해결하기 위한 포인트 릴리스입니다.
### <a name="bug-fixes"></a>버그 수정
- 1\.3.6에서 기호 검색 알고리즘을 변경할 때 손상된 wwwroot/bin 폴더의 PDB 검색을 수정합니다.
- 원격 분석에서 소음이 많은 ExtractWasCalledMultipleTimesException을 수정합니다.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>변경
- SnapshotCollector의 netcoreapp2.0 대상은 Microsoft.ApplicationInsights.AspNetCore >= 2.1.1 (again)에 종속됩니다. 이렇게 하면 1.3.5 이전의 동작으로 되돌아갑니다. 이를 1.3.6에서 업그레이드하려고 했지만, 일부 Azure App Service 시나리오는 중단되었습니다.
### <a name="new-features"></a>새로운 기능
- Snapshot Collector는 APPLICATIONINSIGHTS_CONNECTION_STRING 환경 변수 또는 TelemetryConfiguration에서 ConnectionString을 읽고 구문 분석합니다. 주로 이를 사용하여 스냅샷 서비스에 연결하기 위한 엔드포인트를 설정합니다. 자세한 내용은 [연결 문자열 문서](./sdk-connection-string.md)를 참조하십시오.
### <a name="bug-fixes"></a>버그 수정
- 호환되지 않는 SecurityProtocol (TLS 1.2 필요)로 인해 일부 환경에서 WebRequest가 실패했기 때문에 net45를 제외한 모든 대상에 대해 HttpClient를 사용하도록 전환되었습니다.

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>변경
- 이제 SnapshotCollector는 모든 대상 프레임워크에 대해 Microsoft.ApplicationInsights >= 2.5.1에 종속됩니다. 애플리케이션이 이전 버전의 Microsoft.ApplicationInsights SDK에 종속된 경우, 이는 호환성이 손상되는 변경 사항입니다.
- 스냅샷 업로더에서 TLS 1.0 및 1.1에 대한 지원을 제거합니다.
- 이제 PDB 검색 기간에는 15분이 아닌 24시간이 기본값으로 설정됩니다. SnapshotCollectorConfiguration의 PdbRescanInterval를 통해 구성할 수 있습니다.
- PDB 검색은 재귀적이 아닌 최상위 폴더만 검색합니다. 이는 기호가 이진 폴더의 하위 폴더에 있다면 호환성이 손상되는 변경일 수 있습니다.
### <a name="new-features"></a>새로운 기능
- 로그 폴더를 오래된 파일로 채우지 않도록 SnapshotUploader의 로그 회전
- .NET Core 3.0 애플리케이션에 대한 역최적화(deoptimization) 지원(ReJIT on attach를 통해).
- NuGet 패키지에 기호를 추가합니다.
- 미니덤프를 업로드할 때 추가 메타데이터를 설정합니다.
- SnapshotCollectorTelemetryProcessor에 초기화된 속성을 추가했습니다. Snapshot Collector가 완전히 초기화되고 서비스 엔드포인트에 연결될 때 취소되는 CancellationToken입니다.
- 이제 동적으로 스냅샷 메서드의 예외의 스냅샷을 캡처할 수 있습니다. 예를 들어, Entity Framework 쿼리를 통해 스냅샷 컴파일된 식 트리가 있습니다.
### <a name="bug-fixes"></a>버그 수정
- 상태 모니터 때문에 AmbiguousMatchException이 Snapshot Collector를 로드합니다.
- GetSnapshotCollector 확장 메서드는 이제 모든 TelemetrySinks를 검색합니다.
- 지원되지 않는 플랫폼에서 스냅샷 업로더를 시작하지 마십시오.
- 동적 메서드를 도로 풀(deoptimize) 때 InvalidOperationException을 처리합니다(예: Entity Framework).

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- 소버린 클라우드에 대한 지원 추가(이전 버전은 소버린 클라우드에서 작동하지 않음)
- AddSnapshotCollector()를 사용하여 더 쉽게 Snapshot Collector를 추가합니다. 자세한 내용은 [여기](./snapshot-debugger-appservice.md)를 참조하세요.
- Blob 블록을 확인하려면 FISMA MD5 설정을 사용합니다. 이렇게 하면 OS가 FIPS 규격 모드로 설정된 경우 사용할 수 없는 기본 .NET MD5 암호화 알고리즘이 방지됩니다.
- 함수 호출을 도로 풀 때 .NET Framework 프레임을 무시합니다. 이 동작은 DeoptimizeIgnoredModules 구성 설정으로 제어할 수 있습니다.
- 둘 이상의 함수 호출을 도로 풀 수 있도록 하는 `DeoptimizeMethodCount` 구성 설정을 추가합니다. 자세한 내용은 여기를 참조하세요.

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- 구조화된 계측 키를 허용합니다.
- SnapshotUploader 견고성 강화 - 이전 업로더 로그를 이동할 수 없는 경우에도 계속 시작합니다.
- 1\.3.3에서 사용하지 않도록 설정된 SnapshotUploader.exe가 즉시 종료될 때 추가 원격 분석 보고를 다시 사용하도록 설정합니다.
- 내부 원격 분석을 단순화합니다.
- _실험적 기능_: Snappoint 컬렉션 계획: "snapshotOnFirstOccurence"를 추가합니다. 자세한 내용은 [여기](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)를 참조하세요.

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- SnapshotUploader.exe가 응답을 중지하고 .NET Core 앱에 대한 스냅샷을 업로드하지 않는 버그가 수정되었습니다.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _실험적 기능_: Snappoint 컬렉션 계획. 자세한 내용은 [여기](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)를 참조하세요.
- SnapshotUploader.exe는 프로세스가 종료될 때까지 기다리지 않고 런타임이 SnapshotCollector가 로드되는 AppDomain을 언로드할 때 종료됩니다. 이렇게 하면 IIS에서 호스팅될 때 수집기 안정성이 향상됩니다.
- 동일한 계측 키를 사용하는 여러 SnapshotCollector 인스턴스가 동일한 SnapshotUploader 프로세스를 공유 하도록 허용하는 구성을 추가합니다. ShareUploaderProcess (기본값 `true`).
- SnapshotUploader.exe가 즉시 종료될 때 추가 원격 분석을 보고합니다.
- SnapshotUploader.exe가 디스크에 써야 하는 지원 파일 수를 줄였습니다.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- RtlCloneUserProcess API를 사용하는 스냅샷 수집에 대한 지원을 제거하고, PssCaptureSnapshots API만 지원합니다.
- 1 ~ 3에서 10분 이내에 캡처할 수 있는 스냅샷 수에 대한 기본 제한을 늘립니다.
- SnapshotUploader.exe가 TLS 1.1 및 1.2를 협상할 수 있도록 허용
- SnapshotUploader가 경고 또는 오류를 로그하는 경우 추가 원격 분석 보고
- 백 엔드 서비스에서 일일 할당량에 도달했음을 보고하는 경우 스냅샷 생성 중지(하루당 50 스냅샷)
- 두 인스턴스가 동시에 실행되는 것을 허용 하지 않도록 SnapshotUploader.exe에 추가 검사를 추가합니다.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>변경
- .NET Framework를 대상으로 하는 애플리케이션의 경우, Snapshot Collector는 이제 Microsoft.ApplicationInsights 버전 2.3.0 이상에 종속됩니다.
2\.2.0 이상이 사용됩니다.
이는 대부분의 애플리케이션에 문제가 되지 않는다고 생각 하지만, 이 변경으로 인해 최신 Snapshot Collector를 사용하지 못하게 되는 경우 저희에게 알려주세요.
- 실패한 업로드를 다시 시도할 때 스냅샷 업로더에서 지수 백오프 지연을 사용합니다.
- ServerTelemetryChannel(사용 가능한 경우)을 사용하여 원격 분석을 더욱 안정적으로 보고합니다.
- 종속성 추적에서 무시되도록 스냅샷 디버거 서비스에 대한 초기 연결에 'SdkInternalOperationsMonitor'를 사용합니다.
- 스냅샷 디버거 서비스에 대한 초기 연결에서 원격 분석을 향상합니다.
- 다음에 대한 추가 원격 분석을 보고합니다.
  - Azure App Service 버전.
  - Azure 컴퓨팅 인스턴스.
  - 컨테이너.
  - Azure 함수 앱.
### <a name="bug-fixes"></a>버그 수정
- 문제 카운터 재설정 주기가 24일로 설정된 경우 이를 24 시간으로 해석합니다.
- 스냅샷을 삭제하는 동안 예외가 발생한 경우, 스냅샷 업로더가 새 스냅샷 처리를 중지하는 버그를 수정했습니다.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- 스냅샷 업로더 이진을 사용하여 강력한 이름 서명을 수정합니다.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>변경
- 이제 SnapshotUploader(64).exe에 필요한 파일은 기본 DLL에 리소스로 포함됩니다. 즉, SnapshotCollectorFiles 폴더는 더 이상 생성되지 않으며, 빌드 및 배포를 간소화하고, 솔루션 탐색기의 클러터를 줄여 줍니다. 업그레이드 시 주의해서 `.csproj` 파일의 변경 내용을 검토합니다. `Microsoft.ApplicationInsights.SnapshotCollector.targets` 파일이 더 이상 필요 없습니다.
- ProvideAnonymousTelemetry가 false로 설정된 경우에도 원격 분석은 Application Insights 리소스에 로그됩니다. 따라서 Azure Portal에서 상태 검사 기능을 구현할 수 있습니다. ProvideAnonymousTelemetry는 제품 지원 및 개선을 위해 Microsoft로 전송되는 원격 분석에만 영향을 줍니다.
- TempFolder 또는 ShadowCopyFolder가 환경 변수로 리디렉션되는 경우, 해당 환경 변수가 설정될 때까지 수집기가 유휴 상태로 유지됩니다.
- 프록시 서버를 통해 인터넷에 연결하는 애플리케이션의 경우, Snapshot Collector가 이제 프록시 설정을 자동으로 검색하여 SnapshotUploader.exe에 전달합니다.
- SnapshotUplaoder 프로세스의 우선 순위를 낮춥니다(가능한 경우). 이 우선 순위는 IsLowPrioirtySnapshotUploader 옵션을 통해 재정의할 수 있습니다.
- Snapshot Collector를 프로그래밍 방식으로 구성하려는 시나리오에 대한 TelemetryConfiguration에 GetSnapshotCollector 확장 메서드를 추가했습니다.
- 고객 지향 원격 분석에서 애플리케이션 버전 대신 Application Insights SDK 버전을 설정합니다.
- 2분 후에 첫 번째 하트비트 이벤트를 보냅니다.
### <a name="bug-fixes"></a>버그 수정
- 예외가 null 또는 변경할 수 없는 데이터 사전인 경우, NullReferenceException을 수정합니다.
- 업로더에서 공유 위반이 발생하는 경우 PDB를 몇 번 다시 시도합니다.
- 시작할 때 두 개 이상의 스레드가 원격 분석 파이프라인을 호출하는 경우, 중복된 원격 분석을 수정합니다.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>변경
- XML 문서 주석 파일은 이제 NuGet 패키지에 포함됩니다.
- 잡음이 있는 예외가 있고 이에 대한 스냅샷을 만들지 않으려면, 시나리오에 대해 ExcludeFromSnapshotting 확장 메서드를 `System.Exception`에 추가했습니다.
- IsEnabledWhenProfiling 구성 속성을 추가했습니다. 기본값은 true입니다. 이는 이전 버전에서 변경된 내용으로, 이전 버전에서는 Application Insights Profiler에서 자세한 컬렉션을 수행하는 경우, 스냅샷 만들기를 일시적으로 사용하지 않도록 설정됩니다. 이전 동작은 이 속성을 false로 설정하여 복구할 수 있습니다.
### <a name="bug-fixes"></a>버그 수정
- SnapshotUploader64.exe에 올바르게 로그인합니다.
- 원격 분석 프로세서의 이중 초기화를 방지합니다.
- 여러 파이프라인이 있는 앱에서 원격 분석의 이중 로깅을 방지합니다.
- 컬렉션 계획의 만료 시간 내에 버그를 수정하여 24시간 후에 스냅샷을 방지할 수 있습니다.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
이 버전의 가장 큰 변경 내용(따라서 새로운 부 버전 번호로 이동)은 스냅샷 만들기 및 처리 파이프라인을 다시 작성하는 것입니다. 이전 버전에서는 이 기능이 네이티브 코드(ProductionBreakpoints *.dll 및 SnapshotHolder*.exe)에서 구현되었습니다. 새 구현은 P/Invokes를 사용하는 모든 관리 코드입니다. 새 파이프라인을 사용하는 이 첫 번째 버전에서는 원래 동작과 크게 다르지 않습니다. 새 구현에서는 더 나은 오류 보고를 허용하고, 향후 개선 사항에 대해 설정합니다.

### <a name="other-changes-in-this-version"></a>이 버전의 기타 변경 내용
- MinidumpUploader.exe는 SnapshotUploader.exe (또는 SnapshotUploader64.exe)로 이름이 변경되었습니다.
- 요청을 역최적화/재최적화(DeOptimize/ReOptimize)할 수 있는 타이밍 원격 분석이 추가되었습니다.
- 미니덤프 업로드를 위해 gzip 압축을 추가했습니다.
- PDB가 잠겨 사이트 업그레이드를 방지하는 문제를 해결했습니다.
- 섀도 복사할 때 원래 폴더 이름(SnapshotCollectorFiles)을 로그합니다.
- OOM으로 인한 사이트 재시작을 방지하기 위해 64 비트 프로세스에 대한 메모리 제한을 조정합니다.
- 사용하지 않도록 설정한 후에도 스냅샷이 계속 수집되는 문제를 해결합니다.
- 고객의 AI 리소스에 하트비트 이벤트를 로그합니다.
- 문제 ID에서 "원본"을 제거하여 스냅샷 속도를 향상시킵니다.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>변경
확대된 사용량 원격 분석
- .NET 버전 및 OS 검색 및 보고
- 추가 Azure 환경(클라우드 서비스, Service Fabric) 검색 및 보고
- 하트비트 원격 분석에서 예외 메트릭(첫 번째 예외 수 및 TrackException 호출 수)을 기록하고 보고합니다.
### <a name="bug-fixes"></a>버그 수정
- 내부 예외(Win32Exception)가 throw되지 않는 SqlException을 올바르게 처리합니다.
- MinidumpUploader에 대한 명령줄 인수를 잘못 구문 분석하게 만든 기호 폴더에서 후행 공백을 자릅니다.
- 스냅샷 디버거 에이전트의 엔드포인트에 대한 실패한 연결의 무한 재시도를 방지합니다.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>변경
- 호스트 메모리 보호를 추가 했습니다. 이 기능을 통해 호스트 컴퓨터의 메모리에 대한 영향을 줄입니다.
- Azure Portal 스냅샷 보기 환경을 개선합니다.
