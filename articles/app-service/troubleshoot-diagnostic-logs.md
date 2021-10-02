---
title: 진단 로깅 사용
description: 진단 로그를 사용하도록 설정하는 방법, 애플리케이션에 계측을 추가하는 방법 및 Azure에서 기록된 정보에 액세스하는 방법에 대해 알아봅니다.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 07/06/2021
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: ac47322f33ac8f2efd08e5b04e639f09959dee28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352020"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Azure App Service에서 앱에 대한 진단 로깅 사용
## <a name="overview"></a>개요
Azure는 [App Service 앱](overview.md)을 디버그하는 데 도움이 되는 기본 제공 진단을 제공합니다. 이 문서에서는 진단 로그를 사용하도록 설정하는 방법, 애플리케이션에 계측을 추가하는 방법 및 Azure에서 기록된 정보에 액세스하는 방법을 설명합니다.

이 문서에서는 진단 로그 작업에 [Azure Portal](https://portal.azure.com)과 Azure CLI를 사용합니다. Visual Studio를 사용하여 진단 로그로 작업하는 방법에 대한 자세한 내용은 [Visual Studio에서 Azure 문제 해결](troubleshoot-dotnet-visual-studio.md)을 참조하세요.

> [!NOTE]
> 이 문서의 로깅 지침 외에도 Azure Monitoring을 사용하는 새로운 통합 로깅 기능이 있습니다. 이 기능에 대한 자세한 내용은 [Azure Monitor에 로그 보내기(미리 보기)](#send-logs-to-azure-monitor-preview) 섹션에서 확인할 수 있습니다. 
>
>

|유형|플랫폼|위치|Description|
|-|-|-|-|
| 애플리케이션 로깅 | Windows, Linux | App Service 파일 시스템 및/또는 Azure Storage BLOB | 애플리케이션 코드로 생성된 로그 메시지입니다. 사용자가 선택한 웹 프레임워크 또는 사용자 언어의 표준 로깅 패턴을 사용하여 직접 애플리케이션 코드에서 메시지를 생성할 수 있습니다. 각 메시지에는 **중요**, **오류**, **경고**, **정보**, **디버그** 및 **추적** 범주 중 하나가 할당됩니다. 애플리케이션 로깅을 사용하도록 설정할 때 심각도 수준을 설정하여 로깅을 원하는 세부 정보를 선택할 수 있습니다.|
| 웹 서버 로깅| Windows | App Service 파일 시스템 또는 Azure Storage BLOB| [W3C 확장 로그 파일 형식의](/windows/desktop/Http/w3c-logging)원시 HTTP 요청 데이터. 각 로그 메시지에는 HTTP 메서드, 리소스 URI, 클라이언트 IP, 클라이언트 포트, 사용자 에이전트, 응답 코드 등의 데이터가 포함됩니다. |
| 상세한 오류 메시지| Windows | App Service 파일 시스템 | 클라이언트 브라우저로 전송된 *.htm* 오류 페이지의 복사본입니다. 보안상의 이유로, 자세한 오류 페이지는 프로덕션 환경에서 클라이언트로 전송되어서는 안 되지만 App Service는 HTTP 코드 400 이상이 포함된 애플리케이션 오류가 발생할 때마다 오류 페이지를 저장할 수 있습니다. 이 페이지에는 서버에서 오류 코드를 반환하는 이유를 확인하는 데 유용한 정보가 포함될 수 있습니다. |
| 실패한 요청 추적 | Windows | App Service 파일 시스템 | 요청을 처리하는 데 사용된 IIS 구성 요소 추적 및 각 구성 요소에 소요된 시간을 포함하는 실패한 요청에 대한 자세한 추적 정보입니다. 사이트 성능을 향상시키거나 특정 HTTP 오류를 분리하려는 경우에 유용합니다. 실패한 각 요청마다 로그 파일을 볼 수 있는 XML 로그 파일과 XSL 스타일 시트가 포함된 폴더가 하나씩 생성됩니다. |
| 배포 로깅 | Windows, Linux | App Service 파일 시스템 | 앱에 콘텐츠를 게시하는 경우에 대한 로그. 배포 로깅은 자동으로 수행되며 이에 대한 구성 설정은 없습니다. 배포가 실패한 이유를 확인하는 데 도움이 됩니다. 예를 들어 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 사용하는 경우 배포 로깅을 사용하여 스크립트가 실패한 이유를 확인할 수 있습니다. |

> [!NOTE]
> App Service는 애플리케이션 문제를 해결하는 데 유용한 전용 대화형 진단 도구를 제공합니다. 자세한 내용은 [Azure App Service 진단 개요](overview-diagnostics.md)를 참조하세요.
>
> 또한 다른 Azure 서비스를 사용하여 [Azure Monitor](../azure-monitor/app/azure-web-apps.md)와 같은 앱의 로깅 및 모니터링 기능을 향상시킬 수 있습니다.
>

## <a name="enable-application-logging-windows"></a>애플리케이션 로깅 사용(Windows)

> [!NOTE]
> BLOB 스토리지에 대한 애플리케이션 로깅은 App Service와 동일한 지역에 있는 스토리지 계정만 사용할 수 있습니다

[Azure Portal](https://portal.azure.com)에서 Windows 앱에 대한 애플리케이션 로깅을 사용하도록 설정하려면 앱으로 이동하여 **App Service 로그** 를 선택합니다.

**Application Logging(Filesystem)** 또는 **Application Logging(BLOB)** 중 하나 또는 둘 모두에 대해 **켜기** 를 선택합니다. 

**Filesystem** 옵션은 임시 디버깅 용도로 사용되며 12시간 이내에 해제됩니다. **BLOB** 옵션은 장기간 로깅을 위한 옵션이며, 로그를 쓸 BLOB 스토리지 컨테이너가 필요합니다.  또한 **BLOB** 옵션에는 로그 메시지의 원본 VM 인스턴스 ID(`InstanceId`), 스레드 ID(`Tid`) 및 보다 세분화된 타임스탬프([`EventTickCount`](/dotnet/api/system.datetime.ticks))와 같은 로그 메시지의 추가 정보가 포함됩니다.

> [!NOTE]
> 현재 .NET 애플리케이션 로그만 Blob Storage에 쓸 수 있습니다. Java, PHP, Node.js, Python 애플리케이션 로그는 App Service 파일 시스템에만 저장할 수 있습니다. 단, 외부 스토리지에 로그를 기록하기 위해 코드를 수정하지 않아도 됩니다.
>
> [스토리지 계정의 액세스 키를 다시 생성](../storage/common/storage-account-create.md)하는 경우 각 로깅 구성을 다시 설정하여 업데이트한 키를 사용해야 합니다. 가상 하드 디스크 파일에 대한 중요 정보를 제공하려면
>
> 1. **구성** 탭에서 해당 로깅 기능을 **끄기** 로 설정합니다. 설정을 저장합니다.
> 2. 스토리지 계정 Blob에 로깅을 다시 사용합니다. 설정을 저장합니다.
>
>

로깅할 **수준** 또는 로깅할 세부 정보의 수준을 선택합니다. 다음 표에는 각 수준에 포함된 로그 범주가 나와 있습니다.

| Level | 포함된 범주 |
|-|-|
|**사용 안 함** | 없음 |
|**오류** | 오류, 위험 |
|**경고** | 경고, 오류, 위험|
|**정보** | 정보, 경고, 오류, 위험|
|**Verbose** | 추적, 디버그, 정보, 경고, 오류, 위험(모든 범주) |

작업을 마쳤으면 **저장** 을 선택합니다.

> [!NOTE]
> Blob에 로그를 작성 하는 경우 앱을 삭제 하 고 blob에 로그를 유지 하는 경우 보존 정책이 더 이상 적용 되지 않습니다. 자세한 내용은 [리소스 삭제 후 발생할 수 있는 비용](overview-manage-costs.md#costs-that-might-accrue-after-resource-deletion)을 참조 하세요.
>

## <a name="enable-application-logging-linuxcontainer"></a>애플리케이션 로깅 사용(Linux/컨테이너)

[Azure Portal](https://portal.azure.com)에서 Linux 앱 또는 사용자 지정 컨테이너 앱에 대한 애플리케이션 로깅을 사용하도록 설정하려면 앱으로 이동하여 **App Service 로그** 를 선택합니다.

**애플리케이션 로깅** 에서 **파일 시스템** 을 선택합니다.

**할당량(MB)** 에서 애플리케이션 로그에 쓸 디스크 할당량을 지정합니다. **보존 기간(일)** 에서 로그를 보존할 일수를 설정합니다.

작업을 마쳤으면 **저장** 을 선택합니다.

## <a name="enable-web-server-logging"></a>웹 서버 로깅 사용

[Azure Portal](https://portal.azure.com)에서 Windows 앱에 대한 웹 서버 로깅을 사용하도록 설정하려면 앱으로 이동하여 **App Service 로그** 를 선택합니다.

**웹 서버 로깅** 의 경우 BLOB 스토리지에 로그를 저장하려면 **스토리지** 를 선택하고, App Service 파일 시스템에 로그를 저장하려면 **파일 시스템** 을 선택합니다. 

**보존 기간(일)** 에서 로그를 보존할 일수를 설정합니다.

> [!NOTE]
> [스토리지 계정의 선택키를 다시 생성](../storage/common/storage-account-create.md)하는 경우 해당 로깅 구성을 다시 설정하여 업데이트한 키를 사용해야 합니다. 가상 하드 디스크 파일에 대한 중요 정보를 제공하려면
>
> 1. **구성** 탭에서 해당 로깅 기능을 **끄기** 로 설정합니다. 설정을 저장합니다.
> 2. 스토리지 계정 Blob에 로깅을 다시 사용합니다. 설정을 저장합니다.
>
>

작업을 마쳤으면 **저장** 을 선택합니다.

> [!NOTE]
> Blob에 로그를 작성 하는 경우 앱을 삭제 하 고 blob에 로그를 유지 하는 경우 보존 정책이 더 이상 적용 되지 않습니다. 자세한 내용은 [리소스 삭제 후 발생할 수 있는 비용](overview-manage-costs.md#costs-that-might-accrue-after-resource-deletion)을 참조 하세요.
>

## <a name="log-detailed-errors"></a>자세한 오류 로그

[Azure Portal](https://portal.azure.com)에서 Windows 앱에 대한 오류 페이지 또는 실패한 요청 추적을 저장하려면 앱으로 이동하여 **App Service 로그** 를 선택합니다.

**자세한 오류 로깅** 또는 **실패한 요청 추적** 에서 **켜기** 를 선택한 다음, **저장** 을 선택합니다.

두 로그 유형 모두 App Service 파일 시스템에 저장됩니다. 최대 50건의 오류(파일/폴더)가 보존됩니다. HTML 파일 수가 50개를 초과하면 가장 오래된 오류 파일이 자동으로 삭제됩니다.

실패한 요청 추적 기능은 기본적으로 400에서 600 사이의 HTTP 상태 코드로 실패한 요청 로그를 캡처합니다. 사용자 지정 규칙을 지정하려면 *web.config* 파일의 `<traceFailedRequests>` 섹션을 재정의할 수 있습니다.

## <a name="add-log-messages-in-code"></a>코드에 로그 메시지 추가

애플리케이션 코드에서 일반적인 로깅 기능을 사용해 애플리케이션 로그에 로그 메시지를 보냅니다. 예를 들면 다음과 같습니다.

- ASP.NET 애플리케이션은 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 클래스를 사용하여 애플리케이션 진단 로그에 정보를 로깅할 수 있습니다. 예를 들면 다음과 같습니다.

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- 기본적으로 ASP.NET Core는 [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 로깅 공급자를 사용합니다. 자세한 내용은 [Azure의 ASP.NET Core 로깅](/aspnet/core/fundamentals/logging/)을 참조하세요. WebJobs SDK 로깅에 대한 자세한 내용은 [Azure WebJobs SDK 시작](./webjobs-sdk-get-started.md#enable-console-logging)을 참조하세요.

## <a name="stream-logs"></a>로그 스트리밍

로그를 실시간으로 스트리밍하기 전에 원하는 로그 유형을 사용하도록 설정합니다. */LogFiles* 디렉터리(d:/home/logfiles)에 저장된 .txt, .log 또는 .htm으로 끝나는 파일에 기록된 정보는 App Service로 스트리밍됩니다.

> [!NOTE]
> 일부 유형의 로깅 버퍼는 로그 파일에 기록하고 이로 인해 스크림에서 이벤트가 작동하지 않을 수 있습니다. 예를 들어 사용자가 페이지를 방문할 때 발생한 애플리케이션 로그 항목이 페이지 요청에 대한 해당 HTTP 로그 항목보다 먼저 스트림에 표시될 수 있습니다.
>

### <a name="in-azure-portal"></a>Azure Portal에서

[Azure Portal](https://portal.azure.com)에서 로그를 스트리밍하려면 앱으로 이동하여 **로그 스트리밍** 을 선택합니다. 

### <a name="in-cloud-shell"></a>Cloud Shell에서

로그를 [Cloud Shell](../cloud-shell/overview.md)에서 라이브로 스트리밍하려면 다음 명령을 사용합니다.

> [!IMPORTANT]
> 이 명령은 Linux 앱 서비스 계획에서 호스트되는 웹앱에서는 작동하지 않을 수 있습니다.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

HTTP와 같은 특정 로그 유형을 필터링하려면 **--Provider** 매개 변수를 사용합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### <a name="in-local-terminal"></a>로컬 터미널에서

로컬 콘솔에서 로그를 스트리밍하려면 Azure CLI를 [설치](/cli/azure/install-azure-cli)하고 [계정에 로그인](/cli/azure/authenticate-azure-cli)합니다. 로그인한 후에는 [Cloud Shell 지침](#in-cloud-shell)을 따릅니다.

## <a name="access-log-files"></a>로그 파일 액세스

로그 유형에 대한 Azure Storage BLOB 옵션을 구성하는 경우 Azure Storage에서 작동하는 클라이언트 도구가 필요합니다. 자세한 내용은 [Azure Storage 클라이언트 도구](../storage/common/storage-explorers.md)를 참조하세요.

App Service 파일 시스템에 저장된 로그의 경우 브라우저에서 ZIP 파일을 다운로드하는 가장 쉬운 방법은 다음과 같습니다.

- Linux/컨테이너 앱: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows 앱: `https://<app-name>.scm.azurewebsites.net/api/dump`

Linux/컨테이너 앱의 경우 ZIP 파일에는 Docker 호스트와 Docker 컨테이너 양쪽의 콘솔 출력 로그가 포함되어 있습니다. 스케일 아웃된 앱의 경우 ZIP 파일에는 각 인스턴스에 대한 로그 집합이 하나 포함됩니다. App Service 파일 시스템에서 해당 로그 파일은 */home/LogFiles* 디렉터리의 콘텐츠입니다.

Windows 앱의 경우 ZIP 파일에는 App Service 파일 시스템의 *D:\Home\LogFiles* 디렉터리의 콘텐츠가 포함되어 있습니다. 구조는 다음과 같습니다.

| 로그 형식 | 디렉터리 | 설명 |
|-|-|-|
| **애플리케이션 로그 전송 사용** |*/LogFiles/Application/* | 하나 이상의 텍스트 파일을 포함합니다. 로그 메시지의 형식은 사용하는 로깅 공급자에 따라 달라 집니다. |
| **실패한 요청 추적** | */LogFiles/W3SVC#########/* | XML 파일 및 XSL 파일을 포함합니다. 브라우저에서 형식이 지정된 XML 파일을 볼 수 있습니다. |
| **자세한 오류 로그** | */LogFiles/DetailedErrors/* | HTM 오류 파일이 포함되어 있습니다. 브라우저에서 HTM 파일을 볼 수 있습니다.<br/>실패한 요청 추적을 보는 또 다른 방법은 포털에서 앱 페이지로 이동하는 것입니다. 왼쪽 메뉴에서 **문제 진단 및 해결** 을 선택하고 **실패한 요청 추적 로그** 를 검색한 다음 아이콘을 클릭하여 원하는 추적을 확인합니다. |
| **웹 서버 로그** | */LogFiles/http/RawLogs/* | [W3C 확장 로그 파일 형식](/windows/desktop/Http/w3c-logging)을 사용하여 서식이 지정된 텍스트 파일이 포함되어 있습니다. 이 정보는 텍스트 편집기나 [로그 파서](https://www.iis.net/downloads/community/2010/04/log-parser-22) 같은 유틸리티를 사용하여 읽을 수 있습니다.<br/>App Service는 `s-computername`, `s-ip`, 또는 `cs-version` 필드를 지원하지 않습니다. |
| **배포 로그** | */LogFiles/Git/* 및 */deployments/* | Git 배포용 로그와 내부 배포 프로세스로 생성된 로그가 포함되어 있습니다. |

## <a name="send-logs-to-azure-monitor-preview"></a>Azure Monitor에 로그 보내기(미리 보기)

새 [Azure Monitor 통합](https://aka.ms/appsvcblog-azmon)을 사용하여 [진단 설정(미리 보기)을 만들어](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) Storage Accounts, Event Hubs 및 Log Analytics에 로그를 보낼 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![진단 설정(미리 보기)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>지원되는 로그 유형

다음 표에서는 지원되는 로그 유형 및 그 설명을 표시합니다. 

| 로그 형식 | Windows | Windows 컨테이너 | Linux | Linux 컨테이너 | Description |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | 예 | 예 | 예 | 표준 출력 및 표준 오류 |
| AppServiceHTTPLogs | 예 | 예 | 예 | 예 | 웹 서버 로그 |
| AppServiceEnvironmentPlatformLogs | 예 | 해당 없음 | 예 | 예 | App Service Environment: 크기 조정, 구성 변경 및 상태 로그|
| AppServiceAuditLogs | 예 | 예 | 예 | 예 | FTP 및 Kudu를 통한 로그인 작업 |
| AppServiceFileAuditLogs | 예 | 예 | TBA | TBA | 사이트 콘텐츠 대상의 파일 변경 내용(**프리미엄 계층 이상에서만 사용 가능**) |
| AppServiceAppLogs | ASP.NET & Tomcat <sup>1</sup> | ASP.NET & Tomcat <sup>1</sup> | Java SE & Tomcat Blessed Images<sup>2</sup> | Java SE & Tomcat Blessed Images<sup>2</sup> | 애플리케이션 로그 전송 사용 |
| AppServiceIPSecAuditLogs  | 예 | 예 | 예 | 예 | IP 규칙에서 보낸 요청 |
| AppServicePlatformLogs  | TBA | 예 | 예 | 예 | 컨테이너 작업 로그 |
| AppServiceAntivirusScanAuditLogs | 예 | 예 | 예 | 예 | Microsoft Defender를 사용한 [바이러스 백신 검사 로그](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html)(**프리미엄 계층만 사용 가능**) | 

<sup>1</sup> Tomcat 앱의 경우 앱 설정에 `TOMCAT_USE_STARTUP_BAT`를 추가하고 `false` 또는 `0`으로 설정합니다. *최신* Tomcat 버전에 있어야 하며 *java.util.logging* 을 사용해야 합니다.

<sup>2</sup> Java SE 앱의 경우 앱 설정에 `WEBSITE_AZMON_PREVIEW_ENABLED`를 추가하고 `true` 또는 `1`로 설정합니다.

## <a name="next-steps"></a><a name="nextsteps"></a> 다음 단계
* [Azure Monitor를 사용하여 로그 쿼리](../azure-monitor/logs/log-query-overview.md)
* [Azure App Service에서 모니터링하는 방법](web-sites-monitor.md)
* [Visual Studio에서 Azure App Service 문제 해결](troubleshoot-dotnet-visual-studio.md)
* [HDInsight에서 앱 로그 분석](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
