---
title: 사용자 지정 컨테이너 구성
description: Azure App Service에서 사용자 지정 컨테이너를 구성하는 방법을 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.topic: article
ms.date: 08/25/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: b812495d1046ffc1b9435c0dc2684dd61af89beb
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122865076"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Azure App Service에 대한 사용자 지정 컨테이너 구성

이 문서에서는 Azure App Service에서 실행되도록 사용자 지정 컨테이너를 구성하는 방법을 보여 줍니다.

::: zone pivot="container-windows"

이 가이드에서는 App Service의 Windows 앱 컨테이너화에 대한 주요 개념 및 지침을 제공합니다. Azure App Service를 처음 사용하는 경우 먼저 [사용자 지정 컨테이너 빠른 시작](quickstart-custom-container.md) 및 [자습서](tutorial-custom-container.md)를 따릅니다.

::: zone-end

::: zone pivot="container-linux"

이 가이드에서는 App Service의 Linux 앱 컨테이너화에 대한 주요 개념 및 지침을 제공합니다. Azure App Service를 처음 사용하는 경우 먼저 [사용자 지정 컨테이너 빠른 시작](quickstart-custom-container.md) 및 [자습서](tutorial-custom-container.md)를 따릅니다. [다중 컨테이너 앱 빠른 시작](quickstart-multi-container.md) 및 [자습서](tutorial-multi-container-app.md)도 있습니다.

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>지원되는 부모 이미지

사용자 지정 Windows 이미지의 경우 원하는 프레임워크에 적합한 [부모 이미지(기본 이미지)](https://docs.docker.com/develop/develop-images/baseimages/)를 선택해야 합니다.

- .NET Framework 앱을 배포하려면 Windows Server 2019 Core [LTSC(장기 서비스 채널)](/windows-server/get-started/servicing-channels-comparison#long-term-servicing-channel-ltsc) 릴리스를 기반으로 하는 부모 이미지를 사용합니다. 
- .NET Core 앱을 배포하려면 Windows Server 2019 Nano [SAC(반기 서비스 채널)](/windows-server/get-started/servicing-channels-comparison#semi-annual-channel) 릴리스를 기반으로 하는 부모 이미지를 사용합니다. 

앱을 시작하는 동안 부모 이미지를 다운로드하는 데 다소 시간이 걸립니다. 그러나 Azure App Service에서 이미 캐시된 다음 부모 이미지 중 하나를 사용하여 시작 시간을 줄일 수 있습니다.

- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):2004
- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):ltsc2019
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-2004
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-ltsc2019
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>사용자 지정 컨테이너의 Docker 이미지 변경

기존 사용자 지정 컨테이너 앱을 현재 Docker 이미지에서 새 이미지로 변경하려면 다음 명령을 사용합니다.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>프라이빗 레지스트리의 이미지 사용

Azure Container Registry와 같은 프라이빗 레지스트리의 이미지를 사용하려면 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

*\<username>* 및 *\<password>* 의 경우 프라이빗 레지스트리 계정에 대한 로그인 자격 증명을 제공합니다.

## <a name="i-dont-see-the-updated-container"></a>업데이트된 컨테이너가 표시되지 않음

새 컨테이너를 가리키도록 Docker 컨테이너 설정을 변경하는 경우 앱에서 새 컨테이너의 HTTP 요청을 처리하는 데 몇 분 정도 걸릴 수 있습니다. 새 컨테이너를 끌어오고 시작하는 동안 App Service는 이전 컨테이너의 요청을 계속 처리합니다. 새 컨테이너가 시작되고 요청을 받을 준비가 된 경우에만 App Service에서 요청을 해당 컨테이너에 보내기 시작합니다.

## <a name="how-container-images-are-stored"></a>컨테이너 이미지를 저장하는 방법

App Service에서 사용자 지정 Docker 이미지를 처음 실행하는 경우 App Service는 `docker pull`을 수행하고 모든 이미지 레이어를 끌어옵니다. 이러한 레이어는 온-프레미스에서 Docker를 사용하는 경우와 같이 디스크에 저장됩니다. 앱이 다시 시작될 때마다 App Service는 `docker pull`을 수행하지만 변경된 레이어만 끌어옵니다. 변경 내용이 없는 경우 App Service는 로컬 디스크의 기존 레이어를 사용합니다.

가격 책정 계층의 크기를 확장/축소하는 등의 이유로 앱에서 컴퓨팅 인스턴스를 변경하는 경우 App Service는 모든 레이어를 다시 끌어와야 합니다. 인스턴스를 더 추가하기 위해 확장하는 경우에도 마찬가지입니다. 크기 조정 작업 없이 앱 인스턴스가 변경될 수 있는 드문 경우도 있습니다.

## <a name="configure-port-number"></a>포트 번호 구성

기본적으로 App Service는 사용자 지정 컨테이너가 80 포트에서 수신 대기한다고 가정합니다. 컨테이너가 다른 포트를 수신 대기하는 경우 App Service 앱에서 `WEBSITES_PORT` 설정을 지정합니다. 이는 [Cloud Shell](https://shell.azure.com)을 통해 설정할 수 있습니다. Bash에서:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service는 현재 컨테이너에서 HTTP 요청에 대해 하나의 포트만 공개하도록 허용합니다. 

## <a name="configure-environment-variables"></a>환경 변수 구성

사용자 지정 컨테이너는 외부로 제공해야 하는 환경 변수를 사용할 수 있습니다. 이는 [Cloud Shell](https://shell.azure.com)을 통해 전달할 수 있습니다. Bash에서:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

앱이 실행되면 App Service 앱 설정이 환경 변수로 프로세스에 자동으로 삽입됩니다. URL(`https://<app-name>.scm.azurewebsites.net/Env)`)을 사용하여 컨테이너 환경 변수를 확인할 수 있습니다.

앱에서 프라이빗 레지스트리 또는 Docker Hub의 이미지를 사용하는 경우 리포지토리에 액세스하기 위한 자격 증명이 환경 변수(`DOCKER_REGISTRY_SERVER_URL`, `DOCKER_REGISTRY_SERVER_USERNAME` 및 `DOCKER_REGISTRY_SERVER_PASSWORD`)에 저장됩니다. 보안 위험으로 인해 이러한 예약된 변수 이름은 애플리케이션에 공개되지 않습니다.

::: zone pivot="container-windows"
IIS 또는 .NET Framework(4.0 이상) 기반 컨테이너의 경우 App Service를 통해 자동으로 .NET 앱 설정 및 연결 문자열로 `System.ConfigurationManager`에 삽입됩니다. 다른 모든 언어 또는 프레임워크의 경우 프로세스에 대해 다음 해당 접두사 중 하나가 포함된 환경 변수로 제공됩니다.

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

이 메서드는 환경 변수가 *docker-compose.yml* 파일에 지정된 단일 컨테이너 앱 또는 다중 컨테이너 앱 모두에서 작동합니다.

::: zone-end

## <a name="use-persistent-shared-storage"></a>영구 공유 스토리지 사용

::: zone pivot="container-windows"

앱의 파일 시스템에서 *C:\home* 디렉터리를 사용하여 다시 시작할 때마다 파일을 유지하고 인스턴스 간에 공유할 수 있습니다. 컨테이너 앱에서 영구 스토리지에 액세스할 수 있도록 앱의 `C:\home`이 제공됩니다.

영구 스토리지가 사용하지 않도록 설정되면 `C:\home` 디렉터리에 대한 쓰기가 유지되지 않습니다. [Docker 호스트 로그 및 컨테이너 로그](#access-diagnostic-logs)는 컨테이너에 연결되지 않은 기본 영구 공유 스토리지에 저장됩니다. 영구 스토리지가 사용하도록 설정되면 `C:\home` 디렉터리에 대한 모든 쓰기가 유지되고, 확장된 앱의 모든 인스턴스에서 액세스할 수 있으며, `C:\home\LogFiles`에서 로그에 액세스할 수 있습니다.

::: zone-end

::: zone pivot="container-linux"

앱의 파일 시스템에서 */home* 디렉터리를 사용하여 다시 시작할 때마다 파일을 유지하고 인스턴스 간에 공유할 수 있습니다. 컨테이너 앱에서 영구 스토리지에 액세스할 수 있도록 앱의 `/home`이 제공됩니다.

영구 스토리지가 사용하지 않도록 설정되면 앱을 다시 시작할 때마다 또는 여러 인스턴스 간에 `/home` 디렉터리에 대한 쓰기가 유지되지 않습니다. 유일한 예외는 Docker 및 컨테이너 로그를 저장하는 데 사용되는 `/home/LogFiles` 디렉터리입니다. 영구 스토리지가 사용하도록 설정되면 `/home` 디렉터리에 대한 모든 쓰기가 유지되고, 확장된 앱의 모든 인스턴스에서 액세스할 수 있습니다.

::: zone-end

기본적으로 영구 스토리지는 사용하지 않도록 설정되고, 설정은 앱 설정에서 공개되지 않습니다. 이를 사용하도록 설정하려면 [Cloud Shell](https://shell.azure.com)을 통해 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 앱 설정을 지정합니다. Bash에서:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> [사용자 고유의 영구 스토리지를 구성](configure-connect-to-azure-storage.md)할 수도 있습니다.

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service는 프런트 엔드에서 TLS/SSL을 종료합니다. 즉, TLS/SSL 요청이 앱에 도달하지 않습니다. TLS/SSL에 대한 지원을 앱에 구현할 필요가 없고 구현하지도 않아야 합니다. 

프런트 엔드는 Azure 데이터 센터 내에 있습니다. 앱에서 TLS/SSL을 사용하면 인터넷상의 트래픽이 항상 안전하게 암호화됩니다.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>ASP.NET 컴퓨터 키 삽입 사용자 지정

 컨테이너가 시작되는 동안 자동으로 생성된 키는 ASP.NET 암호화 루틴에 대한 컴퓨터 키로 컨테이너에 삽입됩니다. 환경 변수(`MACHINEKEY_Decryption`, `MACHINEKEY_DecryptionKey`, `MACHINEKEY_ValidationKey`, `MACHINEKEY_Validation`)를 검색하여 [컨테이너에서 이러한 키를 찾을](#connect-to-the-container) 수 있습니다. 

앱이 새 키에 종속되는 경우 다시 시작할 때마다 해당 키에서 ASP.NET 양식 인증 및 보기 상태를 다시 설정할 수 있습니다. 키를 자동으로 다시 생성하지 않도록 방지하려면 [수동으로 해당 키를 App Service 앱 설정으로 설정](#configure-environment-variables)합니다. 

## <a name="connect-to-the-container"></a>컨테이너에 연결

`https://<app-name>.scm.azurewebsites.net/DebugConsole`로 이동하여 진단 작업을 위해 Windows 컨테이너에 직접 연결할 수 있습니다. 작동 방식은 다음과 같습니다.

- 디버그 콘솔을 사용하면 PowerShell 세션 시작, 레지스트리 키 검사 및 전체 컨테이너 파일 시스템 탐색과 같은 대화형 명령을 실행할 수 있습니다.
- 위의 그래픽 브라우저와는 별도로 작동하며, [공유 스토리지](#use-persistent-shared-storage)의 파일만 표시합니다.
- 확장된 앱에서 디버그 콘솔은 컨테이너 인스턴스 중 하나에 연결됩니다. 위쪽 메뉴의 **인스턴스** 드롭다운에서 다른 인스턴스를 선택할 수 있습니다.
- 콘솔 내에서 컨테이너에 대한 변경 내용은 Docker 이미지의 일부가 아니므로 앱을 다시 시작할 때 유지되지 *않습니다*(공유 스토리지의 변경 내용 제외). 레지스트리 설정 및 소프트웨어 설치와 같은 변경 내용을 유지하려면 해당 변경 내용을 Dockerfile의 일부로 만듭니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

App Service는 Docker 호스트의 작업과 컨테이너 내의 작업을 기록합니다. Docker 호스트의 로그(플랫폼 로그)는 기본적으로 제공되지만 컨테이너 내의 애플리케이션 로그 또는 웹 서버 로그는 수동으로 사용하도록 설정해야 합니다. 자세한 내용은 [애플리케이션 로깅 사용](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) 및 [웹 서버 로깅 사용](troubleshoot-diagnostic-logs.md#enable-web-server-logging)을 참조하세요. 

Docker 로그에 액세스하는 방법에는 여러 가지가 있습니다.

- [Azure Portal에서](#in-azure-portal)
- [Kudu 콘솔에서](#from-the-kudu-console)
- [Kudu API를 통해](#with-the-kudu-api)
- [Azure Monitor로 로그 보내기](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>Azure Portal에서

Docker 로그는 포털의 앱 **컨테이너 설정** 페이지에 표시됩니다. 로그는 잘리지만, **다운로드** 를 클릭하여 모든 로그를 다운로드할 수 있습니다. 

### <a name="from-the-kudu-console"></a>Kudu 콘솔에서

`https://<app-name>.scm.azurewebsites.net/DebugConsole`로 이동하고, **LogFiles** 폴더를 클릭하여 개별 로그 파일을 확인합니다. 전체 **LogFiles** 디렉터리를 다운로드하려면 디렉터리 이름 왼쪽에 있는 **다운로드** 아이콘을 클릭합니다. FTP 클라이언트를 사용하여 이 폴더에 액세스할 수도 있습니다.

콘솔 터미널에서는 영구 공유 스토리지를 사용하도록 설정할 수 없으므로 기본적으로 `C:\home\LogFiles` 폴더에 액세스할 수 없습니다. 콘솔 터미널에서 이 동작을 사용하도록 설정하려면 [영구 공유 스토리지를 사용하도록 설정](#use-persistent-shared-storage)합니다.

FTP 클라이언트를 사용하여 현재 사용하고 있는 Docker 로그를 다운로드하려고 하면 파일 잠금으로 인해 오류가 발생할 수 있습니다.

### <a name="with-the-kudu-api"></a>Kudu API를 통해

`https://<app-name>.scm.azurewebsites.net/api/logs/docker`로 직접 이동하여 Docker 로그에 대한 메타데이터를 확인합니다. 둘 이상의 로그 파일이 나열될 수 있으며, `href` 속성을 통해 로그 파일을 직접 다운로드할 수 있습니다. 

모든 로그를 하나의 ZIP 파일에 함께 다운로드하려면 `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`에 액세스합니다.

## <a name="customize-container-memory"></a>컨테이너 메모리 사용자 지정

기본적으로 Azure App Service에 배포된 모든 Windows 컨테이너는 1GB RAM으로 제한됩니다. 이 값은 [Cloud Shell](https://shell.azure.com)을 통해 `WEBSITE_MEMORY_LIMIT_MB` 앱 설정을 제공하여 변경할 수 있습니다. Bash에서:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

값은 MB 단위로 정의되며, 호스트의 총 실제 메모리보다 작거나 같아야 합니다. 예를 들어 RAM이 8GB인 App Service 요금제에서 모든 앱에 대한 `WEBSITE_MEMORY_LIMIT_MB`의 누적 합계는 8GB를 초과하지 않아야 합니다. 각 가격 책정 계층에서 사용할 수 있는 메모리 양에 대한 정보는 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/windows/)의 **Premium v3 서비스 플랜** 섹션에서 확인할 수 있습니다.

## <a name="customize-the-number-of-compute-cores"></a>컴퓨팅 코어 수 사용자 지정

기본적으로 Windows 컨테이너는 선택한 가격 책정 계층에서 사용할 수 있는 모든 코어를 사용하여 실행됩니다. 예를 들어 스테이징 슬롯에서 사용하는 코어 수를 줄일 수 있습니다. 컨테이너에서 사용하는 코어 수를 줄이려면 `WEBSITE_CPU_CORES_LIMIT` 앱 설정을 기본 설정 코어 수로 설정합니다. 이는 [Cloud Shell](https://shell.azure.com)을 통해 설정할 수 있습니다. Bash에서:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> 앱 설정을 업데이트하면 자동 다시 시작이 트리거되어 가동 중지 시간을 최소화합니다. 프로덕션 앱의 경우 스테이징 슬롯으로 교환하고, 스테이징 슬롯에서 앱 설정을 변경한 다음, 프로덕션으로 다시 교환하는 것이 좋습니다.

Kudu 콘솔(`https://<app-name>.scm.azurewebsites.net`)로 이동하고 PowerShell에서 다음 명령을 입력하여 조정된 숫자를 확인합니다. 각 명령은 숫자를 출력합니다.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

프로세서는 다중 코어 또는 하이퍼스레딩 프로세서일 수 있습니다. 각 가격 책정 계층에서 사용할 수 있는 코어 수에 대한 정보는 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/windows/)의 **Premium v3 서비스 플랜** 섹션에서 확인할 수 있습니다.

## <a name="customize-health-ping-behavior"></a>상태 ping 동작 사용자 지정

App Service는 컨테이너가 시작되고 HTTP ping에 응답할 때 컨테이너가 성공적으로 시작된 것으로 간주합니다. 상태 ping 요청에는 `User-Agent= "App Service Hyper-V Container Availability Check"` 헤더가 포함되어 있습니다. 컨테이너가 시작되었지만 특정 시간이 지난 후에도 ping에 응답하지 않으면 App Service에서 컨테이너가 시작되지 않았다는 이벤트를 Docker 로그에 기록합니다. 

애플리케이션에서 리소스를 많이 사용하는 경우 컨테이너가 적시에 HTTP ping에 응답하지 않을 수 있습니다. HTTP ping이 실패할 때의 작업을 제어하려면 `CONTAINER_AVAILABILITY_CHECK_MODE` 앱 설정을 지정합니다. 이는 [Cloud Shell](https://shell.azure.com)을 통해 설정할 수 있습니다. Bash에서:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

PowerShell에서:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

다음 표에는 가능한 값이 나와 있습니다.

| 값 | 설명 |
| - | - |
| **복구** | 3회 연속 가용성 검사 후 컨테이너를 다시 시작합니다. |
| **ReportOnly** | 기본값입니다. 컨테이너를 다시 시작하지 않고, 3회 연속 가용성 검사 후 컨테이너에 대한 Docker 로그에 보고합니다. |
| 해제 | 가용성을 확인하지 않습니다. |

## <a name="support-for-group-managed-service-accounts"></a>그룹 관리 서비스 계정 지원

gMSA(그룹 관리 서비스 계정)는 현재 App Service의 Windows 컨테이너에서 지원되지 않습니다.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>SSH 사용

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. 사용자 지정 컨테이너에서 SSH를 지원하려면 해당 컨테이너를 Docker 이미지 자체에 추가해야 합니다.

> [!TIP]
> App Service의 모든 기본 제공 Linux 컨테이너에서 SSH 명령을 이미지 리포지토리에 추가했습니다. [Node.js 10.14 리포지토리](https://github.com/Azure-App-Service/node/blob/master/10.14)에서 다음 지침에 따라 사용하도록 설정되는 방법을 확인할 수 있습니다. Node.js 기본 제공 이미지의 구성은 약간 다르지만 원칙적으로는 동일합니다.

- 다음 예제와 같이 [sshd_config 파일](https://man.openbsd.org/sshd_config)을 리포지토리에 추가합니다.

    ```
    Port            2222
    ListenAddress       0.0.0.0
    LoginGraceTime      180
    X11Forwarding       yes
    Ciphers aes128-cbc,3des-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr
    MACs hmac-sha1,hmac-sha1-96
    StrictModes         yes
    SyslogFacility      DAEMON
    PasswordAuthentication  yes
    PermitEmptyPasswords    no
    PermitRootLogin     yes
    Subsystem sftp internal-sftp
    ```

    > [!NOTE]
    > 이 파일은 OpenSSH를 구성하며 다음 항목을 포함해야 합니다.
    > - `Port`는 2222로 설정해야 합니다.
    > - `Ciphers`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`aes128-cbc,3des-cbc,aes256-cbc`
    > - `MACs`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`hmac-sha1,hmac-sha1-96`

- Dockerfile에서 다음 명령을 추가합니다.

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    이 구성은 컨테이너에 대한 외부 연결을 허용하지 않습니다. 컨테이너의 2222 포트는 개인 가상 네트워크의 브리지 네트워크 내에서만 액세스할 수 있으며, 인터넷에서 공격자가 액세스할 수 없습니다.

- 컨테이너에 대한 시작 스크립트에서 SSH 서버를 시작합니다.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>다중 컨테이너 앱 구성

- [Docker Compose에서 영구 스토리지 사용](#use-persistent-storage-in-docker-compose)
- [미리 보기 제한 사항](#preview-limitations)
- [Docker Compose 옵션](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Docker Compose에서 영구 스토리지 사용

WordPress와 같은 다중 컨테이너 앱이 제대로 작동하려면 영구 스토리지가 필요합니다. 이를 사용하도록 설정하려면 Docker Compose 구성이 컨테이너 *외부* 의 스토리지 위치를 가리켜야 합니다. 컨테이너 내부의 스토리지 위치에 대한 변경 내용은 앱을 다시 시작한 이후에는 유지되지 않습니다.

[Cloud Shell](https://shell.azure.com)에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 명령을 사용하여 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 앱 설정을 지정하여 영구 스토리지를 사용하도록 설정합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

*docker-compose.yml* 파일에서 `volumes` 옵션을 `${WEBAPP_STORAGE_HOME}`에 매핑합니다. 

`WEBAPP_STORAGE_HOME`은 앱의 영구 스토리지에 매핑되는 App Service의 환경 변수입니다. 예를 들면 다음과 같습니다.

```yaml
wordpress:
  image: <image name:tag>
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>미리 보기 제한 사항

다중 컨테이너는 현재 미리 보기에 있습니다. 지원되지 않는 App Service 플랫폼 기능은 다음과 같습니다.

- 인증/권한 부여
- 관리 ID
- CORS
- Docker Compose 시나리오에서는 VNET 통합이 지원되지 않습니다.
- 현재 Azure App Services의 Docker Compose는 4,000자로 제한되어 있습니다.

### <a name="docker-compose-options"></a>Docker Compose 옵션

다음 목록에는 지원되는 Docker Compose 구성 옵션 및 지원되지 않는 Docker Compose 구성 옵션이 나와 있습니다.

#### <a name="supported-options"></a>지원되는 옵션

- 명령을 사용합니다.
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>지원되지 않는 옵션

- build(허용 안 함)
- depends_on(무시됨)
- networks(무시됨)
- secrets(무시됨)
- 포트 80 및 8080 이외의 포트(무시됨)

> [!NOTE]
> 명시적으로 호출되지 않는 다른 옵션은 공개 미리 보기에서 무시됩니다.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 사용자 지정 컨테이너를 사용하여 사용자 지정 소프트웨어를 Azure App Service로 마이그레이션](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [자습서: 다중 컨테이너 WordPress 앱](tutorial-multi-container-app.md)

::: zone-end

또는 다음 추가 리소스를 참조하세요.

- [환경 변수 및 앱 설정 참조](reference-app-settings.md)
- [Windows/Linux 컨테이너에서 인증서 로드](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
