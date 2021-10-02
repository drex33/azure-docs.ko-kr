---
title: Azure Application Insights 에이전트 - 시작하기 | Microsoft Docs
description: Application Insights 에이전트에 대한 빠른 시작 가이드입니다. 웹 사이트 재배포 없이 웹 사이트 성능을 모니터링합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 01/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7f044133c3692ef440e69ad4f0a82e5240334129
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360906"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>온-프레미스 서버에 대한 Azure Monitor Application Insights 에이전트 시작하기

이 문서에는 대부분의 환경에서 작동하는 데 필요한 빠른 시작 명령이 포함되어 있습니다.
지침은 업데이트를 배포하는 PowerShell 갤러리에 따라 달라집니다.
이러한 명령은 PowerShell `-Proxy` 매개 변수를 지원합니다.

이러한 명령에 대한 설명, 사용자 지정 지침, 문제 해결 관련 정보는 [자세한 지침](status-monitor-v2-detailed-instructions.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="download-and-install-via-powershell-gallery"></a>PowerShell 갤러리를 통해 다운로드 및 설치

### <a name="install-prerequisites"></a>필수 구성 요소 설치

- 모니터링을 사용하도록 설정하려면 연결 문자열이 필요합니다. 연결 문자열이 Application Insights 리소스의 개요 블레이드에 표시됩니다. 자세한 내용은 [연결 문자열](./sdk-connection-string.md?tabs=net#finding-my-connection-string) 페이지를 참조하세요.

> [!NOTE]
> 2020년 4월 기준으로 PowerShell 갤러리는 TLS 1.1 및 1.0을 사용하지 않습니다.
>
> 필요할 수 있는 추가 필수 조건은 [PowerShell 갤러리 TLS 지원](https://devblogs.microsoft.com/powershell/powershell-gallery-tls-support)을 참조하세요.
>

관리자 권한으로 PowerShell을 실행합니다.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
```    
PowerShell을 닫습니다.

### <a name="install-application-insights-agent"></a>Application Insights 에이전트 설치
관리자 권한으로 PowerShell을 실행합니다.
```powershell    
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
```    

> [!NOTE]
> `Install-Module` cmdlet의 `AllowPrerelease` 스위치를 사용하면 베타 릴리스를 설치할 수 있습니다. 
>
> 자세한 내용은 [설치 모듈](/powershell/module/powershellget/install-module#parameters)을 참조하세요.
>

### <a name="enable-monitoring"></a>모니터링 사용

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -ConnectionString 'InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```
    
        
## <a name="download-and-install-manually-offline-option"></a>수동으로 다운로드 및 설치(오프라인 옵션)
### <a name="download-the-module"></a>모듈 다운로드
[PowerShell 갤러리](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)에서 모듈의 최신 버전을 수동으로 다운로드합니다.

### <a name="unzip-and-install-application-insights-agent"></a>Application Insights 에이전트 압축 풀기 및 설치
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>모니터링 사용

```powershell
Enable-ApplicationInsightsMonitoring -ConnectionString 'InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```




## <a name="next-steps"></a>다음 단계

 원격 분석 보기:

- [메트릭을 탐색하여](../essentials/metrics-charts.md) 성능 및 사용량을 모니터링합니다.
- [이벤트 및 로그를 검색](./diagnostic-search.md)하여 문제를 진단합니다.
- [분석을 통해](../logs/log-query-overview.md) 고급 쿼리를 수행합니다.
- [대시보드를 만듭니다](./overview-dashboard.md).

 원격 분석 더 추가:

- [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- [웹 클라이언트 원격 분석을 추가](./javascript.md)하여 웹 페이지 코드에서 예외를 확인하고 추적 호출을 활성화합니다.
- [Application Insights SDK를 코드에 추가](./asp-net.md)하여 추적 및 로그 호출을 삽입할 수 있도록 합니다.

Application Insights Agent에서 지원하는 더 많은 작업:

- [구체적인 지침](status-monitor-v2-detailed-instructions.md)을 검토하여 여기에 있는 명령의 설명을 확인하세요.
- 가이드를 참조하여 Application Insights Agent [문제를 해결](status-monitor-v2-troubleshoot.md)할 수 있습니다.