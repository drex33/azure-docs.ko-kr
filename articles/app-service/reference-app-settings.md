---
title: 환경 변수 및 앱 설정 참조
description: 일반적으로 사용되는 환경 변수와 앱 설정을 사용하여 수정할 수 있는 환경 변수에 대해 설명합니다.
ms.topic: article
ms.date: 06/14/2021
ms.openlocfilehash: ae0f67ae935d31fc242350761e80e5d073a7273a
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892062"
---
# <a name="environment-variables-and-app-settings-in-azure-app-service"></a>Azure App Service 환경 변수 및 앱 설정

[Azure App Service](overview.md)에서, 특정 설정은 배포 또는 런타임 환경에서 환경 변수로 사용할 수 있습니다. 이러한 설정 중 일부는 [앱 설정](configure-common.md#configure-app-settings)으로 수동으로 설정할 때 사용자 지정할 수 있습니다. 이 참조는 사용하거나 사용자 지정할 수 있는 변수를 보여줍니다.

## <a name="app-environment"></a>앱 환경

다음 환경 변수는 일반적으로 앱 환경과 관련이 있습니다.

| 설정 이름| 설명 | 예제 |
|-|-|-|
| `WEBSITE_SITE_NAME` | 읽기 전용입니다. 앱 이름. ||
| `WEBSITE_RESOURCE_GROUP` | 읽기 전용입니다. 앱 리소스를 포함하는 Azure 리소스 그룹 이름. ||
| `WEBSITE_OWNER_NAME` | 읽기 전용입니다. 앱, 리소스 그룹 및 웹스페이스를 소유하는 Azure 구독 ID를 포함합니다. ||
| `REGION_NAME` | 읽기 전용입니다. 앱의 지역 이름. ||
| `WEBSITE_PLATFORM_VERSION` | 읽기 전용입니다. App Service 플랫폼 로그. ||
| `HOME` | 읽기 전용입니다. 홈 디렉터리 경로(예: Windows용 `D:\home`). ||
| `SERVER_PORT` | 읽기 전용입니다. 앱이 수신 대기해야 하는 포트. | |
| `WEBSITE_WARMUP_PATH`  | 슬래시로 시작하는 앱을 준비하기 위한 ping의 상대 경로. 기본값은 루트 경로를 ping하는 `/`입니다. 인증되지 않은 클라이언트를 거부하도록 [App Service 인증](overview-authentication-authorization.md)이 설정된 경우에도 인증되지 않은 클라이언트(예: Azure Traffic Manager)에서 특정 경로를 ping할 수 있습니다. (참고: 이 앱 설정은 AlwaysOn에서 사용하는 경로를 변경하지 않습니다.) ||
| `WEBSITE_COMPUTE_MODE` | 읽기 전용입니다. 앱이 전용(`Dedicated`) 또는 공유(`Shared`) VM에서 실행되는지 여부를 지정합니다. ||
| `WEBSITE_SKU` | 읽기 전용입니다. 앱의 SKU. 가능한 값은 `Free`, `Shared`, `Basic` 및 `Standard`입니다. ||
| `SITE_BITNESS` | 읽기 전용입니다. 앱이 32비트(`x86`) 또는 64비트(`AMD64`)인지 여부를 표시합니다. ||
| `WEBSITE_HOSTNAME` | 읽기 전용입니다. 앱의 기본 호스트 이름. 여기서는 사용자 지정 호스트 이름은 고려하지 않습니다. ||
| `WEBSITE_VOLUME_TYPE` | 읽기 전용입니다. 현재 사용 중인 스토리지 볼륨 유형을 표시합니다. ||
| `WEBSITE_NPM_DEFAULT_VERSION` | 앱에서 사용하는 기본 npm 버전. ||
| `WEBSOCKET_CONCURRENT_REQUEST_LIMIT` | 읽기 전용입니다. 웹소켓의 동시 요청에 대한 제한. **표준** 계층 이상의 경우, 값은 `-1`이지만 VM 크기에 따라 VM당 제한이 있습니다([VM 간 숫자 제한](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) 참조). ||
| `WEBSITE_PRIVATE_EXTENSIONS` | 프라이빗 사이트 확장을 사용하지 않으려면 `0`로 설정합니다. ||
| `WEBSITE_TIME_ZONE` | 기본적으로 앱의 표준 시간대는 항상 UTC입니다. [TimeZone](/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10))에 나열된 유효한 값으로 변경할 수 있습니다. 지정된 값이 인식되지 않으면 UTC가 사용됩니다. | `Atlantic Standard Time` |
| `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG` | 스토리지 볼륨 장애 조치 또는 재구성의 경우 앱이 대기 스토리지 볼륨으로 전환됩니다. `1`의 기본 설정은 스토리지 인프라가 변경되면 작업자 프로세스가 재활용되지 않도록 합니다. WCF(Windows Communication Foundation) 앱을 실행하는 경우, `0`로 설정하여 사용하지 않도록 설정합니다. 설정은 슬롯별로 지정되므로, 모든 슬롯에서 설정해야 합니다. ||
| `WEBSITE_PROACTIVE_AUTOHEAL_ENABLED` | 기본적으로 VM 인스턴스는 할당된 메모리의 90% 이상을 30초 이상 사용하거나, 마지막 2분 동안 총 요청의 80%가 200초보다 오래 걸리는 경우, 미리 "자동 절전 모드"로 설정됩니다. VN 인스턴스가 이러한 규칙 중 하나를 트리거한 경우, 복구 프로세스는 인스턴스의 중복 재시작입니다. 이 복구 동작을 사용하지 않도록 설정하려면 `false`로 설정합니다. 기본값은 `true`입니다. 자세한 내용은 [사전적 자동 절전 모드](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)를 참조하세요. ||
| `WEBSITE_PROACTIVE_CRASHMONITORING_ENABLED` | 24시간 동안 처리되지 않은 예외로 인해, 앱의 VM 인스턴스에서 w3wp.exe 프로세스가 충돌할 때마다 디버거 프로세스가 해당 인스턴스의 주 작업자 프로세스에 연결되고 작업자 프로세스가 다시 충돌할 때 메모리 덤프를 수집합니다. 그런 다음, 이 메모리 덤프가 분석되고 크래시를 발생시킨 스레드의 호출 스택이 App Service 로그에 기록됩니다. 이 자동 모니터링 동작을 사용하지 않도록 설정하려면 `false`로 설정합니다. 기본값은 `true`입니다. 자세한 내용은 [사전적 크래시 모니터링](https://azure.github.io/AppService/2021/03/01/Proactive-Crash-Monitoring-in-Azure-App-Service.html)을 참조하세요. ||
| `WEBSITE_DAAS_STORAGE_SASURI` | 크래시 모니터링(사전적 또는 수동) 중에는 기본적으로 메모리 덤프가 삭제됩니다. 스토리지 Blob 컨테이너에 메모리 덤프를 저장하려면 SAS URI를 지정합니다.  ||
| `WEBSITE_CRASHMONITORING_ENABLED` | [크래시 모니터링](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html)을 수동으로 활성화하려면 `true`로 설정합니다. `WEBSITE_DAAS_STORAGE_SASURI`과 `WEBSITE_CRASHMONITORING_SETTINGS`도 설정해야 합니다. 기본값은 `false`입니다. 원격 디버깅을 사용하는 경우에는 이 설정이 적용되지 않습니다. 또한 이 설정을 `true`로 설정하면, [사전적 크래시 모니터링](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html)을 사용할 수 없습니다. ||
| `WEBSITE_CRASHMONITORING_SETTINGS` | `{"StartTimeUtc": "2020-02-10T08:21","MaxHours": "<elapsed-hours-from-StartTimeUtc>","MaxDumpCount": "<max-number-of-crash-dumps>"}` 형식의 JSON. `WEBSITE_CRASHMONITORING_ENABLED`이 지정된 경우, [크래시 모니터링](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html)을 구성해야 합니다. 스토리지 계정에 크래시 덤프를 저장하지 않고 호출 스택만 기록하려면 JSON에 `,"UseStorageAccount":"false"`을 추가합니다. ||
| `REMOTEDEBUGGINGVERSION` | 원격 디버깅 버전. ||
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | 기본적으로 앱을 만들 때 App Service 가 공유 스토리지를 생성합니다. 사용자 지정 스토리지 계정을 대신 사용하려면, 스토리지 계정의 연결 문자열을 설정합니다. 함수의 경우, [Functions에 대한 앱 설정 참조](../azure-functions/functions-app-settings.md#website_contentazurefileconnectionstring)를 참조하세요. | `DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>` |
| `WEBSITE_CONTENTSHARE` | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`을 사용하여 사용자 지정 스토리지 계정을 지정하는 경우, App Service가 앱에 대한 해당 스토리지 계정에 파일 공유를 생성합니다. 사용자 지정 이름을 사용하려면 이 변수를 원하는 이름으로 설정합니다. 지정된 이름의 파일 공유가 없으면 App Service가 파일 공유를 생성합니다. | `myapp123` |
| `WEBSITE_SCM_ALWAYS_ON_ENABLED` | 읽기 전용입니다. Always On이 사용(`1`)인지 아닌지(`0`) 여부를 표시합니다. ||
| `WEBSITE_SCM_SEPARATE_STATUS` | 읽기 전용입니다. Kudu 앱이 별도의 프로세스에서 실행되고 있는지(`1`) 아닌지(`0`) 여부를 표시합니다. ||

<!-- 
WEBSITE_PROACTIVE_STACKTRACING_ENABLED
WEBSITE_CLOUD_NAME
WEBSITE_MAXIMUM_CONCURRENTCOLDSTARTS
HOME_EXPANDED
USERPROFILE
WEBSITE_ISOLATION
WEBSITE_OS | only appears on windows
WEBSITE_CLASSIC_MODE
 -->

## <a name="variable-prefixes"></a>변수 접두사

다음 표에서는 App Service가 다양한 용도로 사용하는 환경 변수 접두사를 보여줍니다.

| 설정 이름 | 설명 |
|-|-|
| `APPSETTING_` | 고객이 앱 구성에서 앱 설정으로 변수를 설정함을 의미합니다. 앱 설정으로 .NET 앱에 삽입됩니다. |
| `MAINSITE_` | 변수가 앱 자체와 관련되어 있다는 것을 의미합니다. |
| `SCMSITE_` | 변수가 Kudu 앱과 관련되어 있다는 것을 의미합니다. |
| `SQLCONNSTR_` | 앱 구성에서 SQL Server 연결 문자열을 의미합니다. .NET 앱에 연결 문자열로 삽입됩니다. |
| `SQLAZURECONNSTR_` | 앱 구성에서 Azure SQL Database 연결 문자열을 의미합니다. .NET 앱에 연결 문자열로 삽입됩니다. |
| `POSTGRESQLCONNSTR_` | 앱 구성에서 PostgreSQL 연결 문자열을 의미합니다. .NET 앱에 연결 문자열로 삽입됩니다. |
| `CUSTOMCONNSTR_` | 앱 구성에서 사용자 지정 연결 문자열을 의미합니다. .NET 앱에 연결 문자열로 삽입됩니다. |
| `MYSQLCONNSTR_` | 앱 구성에서 Azure SQL Database 연결 문자열을 의미합니다. .NET 앱에 연결 문자열로 삽입됩니다. |
| `AZUREFILESSTORAGE_` | Azure Files의 컨테이너 앱에 대 한 사용자 지정 공유에 대 한 연결 문자열입니다. |
| `AZUREBLOBSTORAGE_` | Azure Blob Storage의 컨테이너 앱에 대 한 사용자 지정 저장소 계정에 대 한 연결 문자열입니다. |
| `NOTIFICATIONHUBCONNSTR_` | Azure Notification Hubs에서 알림 허브에 대 한 연결 문자열을 나타냅니다. |
| `SERVICEBUSCONNSTR_` | Azure Service Bus 인스턴스에 대 한 연결 문자열을 나타냅니다. |
| `EVENTHUBCONNSTR_` | Azure Event Hubs에서 이벤트 허브에 대 한 연결 문자열을 나타냅니다. |
| `DOCDBCONNSTR_` | Azure Cosmos DB 데이터베이스에 대 한 연결 문자열을 나타냅니다. |
| `REDISCACHECONNSTR_` | Redis에 대 한 Azure 캐시의 캐시에 대 한 연결 문자열을 나타냅니다. |
| `FILESHARESTORAGE_` | 사용자 지정 파일 공유에 대 한 연결 문자열을 나타냅니다. |

## <a name="deployment"></a>배포

다음 환경 변수는 앱 배포와 관련되어 있습니다. App Service 빌드 자동화와 관련된 변수에 대해서는 [빌드 자동화](#build-automation)를 참조하세요.

| 설정 이름| 설명 |
|-|-|
| `DEPLOYMENT_BRANCH`| [local Git](deploy-local-git.md) 또는 [cloud Git](deploy-continuous-deployment.md) 배포(예: GitHub)의 경우, Azure에서 배포하려는 분기로 설정합니다. 기본값은 `master`입니다. |
| `WEBSITE_RUN_FROM_PACKAGE`| 로컬 ZIP 패키지에서 앱을 실행하려면 `1`로 설정하고, 원격 ZIP 패키지에서 앱을 실행하려면 외부 URL의 URL로 설정합니다. 자세한 정보는 [Azure App Service에서 ZIP 패키지의 앱 직접 실행하기](deploy-run-package.md)를 참조하세요. |
| `WEBSITE_USE_ZIP` | 더 이상 사용되지 않습니다. `WEBSITE_RUN_FROM_PACKAGE`을 사용합니다. |
| `WEBSITE_RUN_FROM_ZIP` | 더 이상 사용되지 않습니다. `WEBSITE_RUN_FROM_PACKAGE`을 사용합니다. | 
| `WEBSITE_WEBDEPLOY_USE_SCM` | Kudu 배포 엔진 사용을 중지하려면 WebDeploy에 대해 `false`로 설정합니다. 기본값은 `true`입니다. Visual Studio(WebDeploy/MSDeploy)를 사용하여 Linux 앱에 배포하려면 `false`로 설정합니다. |
| `MSDEPLOY_RENAME_LOCKED_FILES` | WebDeploy 배포 중에 복사할 수 없을 때 DLL의 이름을 바꾸려면 `1`로 설정합니다. 이 설정은 `WEBSITE_WEBDEPLOY_USE_SCM`이 `false`로 설정된 경우에는 적용할 수 없습니다. |
| `WEBSITE_DISABLE_SCM_SEPARATION` | 기본적으로, 주 앱과 Kudu 앱은 다른 샌드박스에서 실행됩니다. 앱을 중지할 때 Kudu 앱이 계속 실행되고 있으면 Git 배포 및 MSDeploy를 계속 사용할 수 있습니다. 각 앱에는 자체 로컬 파일이 있습니다. 이 분리를 해제(`true`로 설정)하는 것은 더 이상 완전히 지원되지 않는 레거시 모드입니다. |
| `WEBSITE_ENABLE_SYNC_UPDATE_SITE` | `1`를 설정하여 반환하기 전에 REST API에서 `site` 및 `siteconfig` 업데이트를 호출하고 모든 인스턴스에 완전히 적용되도록 합니다. ARM 템플릿을 사용하여 배포하는 경우의 기본값은 `1`이며, 이는 후속 ARM 호출로 인한 경합 상태를 방지하기 위한 것입니다. |
| `WEBSITE_START_SCM_ON_SITE_CREATION` | ARM 템플릿 배포의 ARM 템플릿에서 `1`로 설정하여 앱 생성의 일부로 Kudu 앱을 미리 시작합니다. |
| `WEBSITE_START_SCM_WITH_PRELOAD` | Linux 앱의 경우, URL을 ping하여 Always On를 사용하도록 설정할 때 Kudu 앱을 강제로 미리 로드하려면 `true`로 설정합니다. 기본값은 `false`입니다. Windows 앱의 경우 Kudu 앱은 항상 미리 로드됩니다. |

<!-- 
WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID
-->

## <a name="build-automation"></a>빌드 자동화

# <a name="kudu-windows"></a>[Kudu(Windows)](#tab/kudu)

Kudu 빌드 구성은 네이티브 Windows 앱에 적용되며, Git 기반 또는 ZIP 기반 배포의 동작을 제어하는 데 사용됩니다.

| 설정 이름| 설명 | 예제 |
|-|-|-|
| `SCM_BUILD_ARGS` | Msbuild 명령줄의 끝에 항목을 추가하여 기본 명령줄의 이전 부분을 재정의합니다. | 클린 빌드를 수행하려면 `-t:Clean;Compile`을 수행합니다.|
| `SCM_SCRIPT_GENERATOR_ARGS` | Kudu는 [여기](http://blog.amitapple.com/post/38418009331/azurewebsitecustomdeploymentpart2)에 설명된 `azure site deploymentscript` 명령을 사용하여 배포 스크립트를 생성합니다. 언어 프레임워크 형식을 자동으로 검색하고 명령에 전달할 매개 변수를 결정합니다. 이 설정은 자동으로 생성된 매개 변수를 재정의합니다. | 리포지토리를 일반 콘텐츠 파일로 처리하려면 `--basic -p <folder-to-deploy>`을 수행합니다. |
| `SCM_TRACE_LEVEL` | 빌드 추적 수준. 기본값은 `1`입니다. 더 많은 추적을 위해 높은 값(최대 4)으로 설정합니다. | `4` |
| `SCM_COMMAND_IDLE_TIMEOUT` | 빌드 프로세스에서 출력을 생성하지 않고 대기하기 위해 시작하는 각 명령에 대한 제한 시간(초)입니다. 그 후에는 명령이 유휴 상태로 간주되고 종료됩니다. 기본값은 `60`(1분)입니다. Azure에서 230초 후에 클라이언트의 연결을 끊는 일반적인 유휴 요청 시간 제한도 있습니다. 그러나 이 명령은 여전히 서버 쪽에서 계속 실행됩니다. | |
| `SCM_LOGSTREAM_TIMEOUT` | 로그 스트리밍을 중지하기 전의 비활성 시간 제한(초). 기본값은 `1800`(30분)입니다.| |
| `SCM_SITEEXTENSIONS_FEED_URL` | 사이트 확장 갤러리의 URL. 기본값은 `https://www.nuget.org/api/v2/`입니다. 이전 피드의 URL은 `http://www.siteextensions.net/api/v2/`입니다. | |
| `SCM_USE_LIBGIT2SHARP_REPOSITORY` | Git 작업에 libgit2sharp 대신 git.exe를 사용하려면 `0`로 설정합니다. | |
| `WEBSITE_LOAD_USER_PROFILE` | ASP.NET 빌드 자동화 동안 `The specified user does not have a valid profile.` 오류가 발생하는 경우(예: Git 배포 중), 이 변수를 `1`로 설정하여 빌드 환경에서 전체 사용자 프로필을 로드합니다. 이 설정은 `WEBSITE_COMPUTE_MODE`이 `Dedicated`인 경우에만 적용할 수 있습니다. | |
| `WEBSITE_SCM_IDLE_TIMEOUT_IN_MINUTES` | SCM(Kudu) 사이트에 대한 시간 제한(분). 기본값은 `20`입니다. | |
| `SCM_DO_BUILD_DURING_DEPLOYMENT` | [ZIP 배포](deploy-zip.md)를 사용하여, 배포 엔진은 ZIP 파일을 있는 그대로 실행할 준비가 된 것으로 가정하고 빌드 자동화를 실행하지 않습니다. [Git 배포](deploy-local-git.md)와 동일한 빌드 자동화를 사용하도록 설정하려면 `true`으로 설정합니다. |

<!-- 
SCM_GIT_USERNAME
SCM_GIT_EMAIL
 -->

# <a name="oryx-linux"></a>[Oryx(Linux)](#tab/oryx)

Oryx 빌드 구성은 Linux 앱에 적용되며, Git 기반 또는 ZIP 기반 배포의 동작을 제어하는 데 사용됩니다. [Oryx 구성](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)을 참조하세요.

-----

## <a name="language-specific-settings"></a>언어별 설정

이 섹션에서는 지원되는 각 언어 프레임워크에 대한 구성 가능한 런타임 설정을 보여줍니다. 배포 시간의 [빌드 자동화](#build-automation) 중에 추가 설정을 사용할 수 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

<!-- 
| DOTNET_HOSTING_OPTIMIZATION_CACHE | 
 -->
| 설정 이름 | 설명 |
|-|-|
| `PORT` | 읽기 전용입니다. Linux 앱의 경우, .NET 런타임이 컨테이너에서 수신 대기하는 포트. |
| `WEBSITE_ROLE_INSTANCE_ID` | 읽기 전용입니다. 현재 인스턴스의 ID. |
| `HOME` | 읽기 전용입니다. 공유 저장소(`/home`)를 가리키는 디렉터리. |
| `DUMP_DIR` | 읽기 전용입니다. 크래시 덤프(`/home/logs/dumps`)의 디렉터리. |
| `APP_SVC_RUN_FROM_COPY` | Linux 앱에만 해당합니다. 기본적으로 앱은 모든 확장된 인스턴스에 대한 공유 디렉터리인 `/home/site/wwwroot`에서 실행됩니다. 컨테이너의 로컬 디렉터리에 앱을 복사하고 실행하려면 이 변수를 `true`로 설정합니다. 이 옵션을 사용하는 경우, `/home/site/wwwroot`에 대한 참조를 하드 코딩하지 않도록 합니다. 대신 `/home/site/wwwroot`에 상대적인 경로를 사용합니다. |
| `MACHINEKEY_Decryption` | Windows 네이티브 앱이나 Windows 컨테이너 앱의 경우 이 변수는 ASP.NET 암호화 루틴을 사용하도록 앱 환경이나 컨테이너에 삽입됩니다([machineKey 요소](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100)) 참조). 기본 `decryption` 값을 재정의하려면 해당 값을 App Service 앱 설정으로 구성하거나 *Web.config* 파일의 `machineKey` 요소에서 직접 해당 값을 설정합니다. |
| `MACHINEKEY_DecryptionKey` | Windows 네이티브 앱이나 Windows 컨테이너 앱의 경우 이 변수는 ASP.NET 암호화 루틴을 사용하도록 앱 환경이나 컨테이너에 삽입됩니다([machineKey 요소](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100)) 참조). 자동으로 생성된 `decryptionKey` 값을 재정의하려면 해당 값을 App Service 앱 설정으로 구성하거나 *Web.config* 파일의 `machineKey` 요소에서 직접 해당 값을 설정합니다.|
| `MACHINEKEY_Validation` | Windows 네이티브 앱이나 Windows 컨테이너 앱의 경우 이 변수는 ASP.NET 암호화 루틴을 사용하도록 앱 환경이나 컨테이너에 삽입됩니다([machineKey 요소](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100)) 참조). 기본 `validation` 값을 재정의하려면 해당 값을 App Service 앱 설정으로 구성하거나 *Web.config* 파일의 `machineKey` 요소에서 직접 해당 값을 설정합니다.|
| `MACHINEKEY_ValidationKey` | Windows 네이티브 앱이나 Windows 컨테이너 앱의 경우 이 변수는 ASP.NET 암호화 루틴을 사용하도록 앱 환경이나 컨테이너에 삽입됩니다([machineKey 요소](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100)) 참조). 자동으로 생성된 `validationKey` 값을 재정의하려면 해당 값을 App Service 앱 설정으로 구성하거나 *Web.config* 파일의 `machineKey` 요소에서 직접 해당 값을 설정합니다.|
<!-- | `USE_DOTNET_MONITOR` | if =true then /opt/dotnetcore-tools/dotnet-monitor collect --urls "http://0.0.0.0:50051" --metrics true --metricUrls "http://0.0.0.0:50050" > /dev/null 2>&1 & -->

# <a name="java"></a>[Java](#tab/java)

| 설정 이름 | 설명 | 예제 |
|-|-|-|
| `JAVA_HOME` | Java 설치 디렉터리의 경로 ||
| `JAVA_OPTS` | Java SE 앱의 경우, `java` 명령에 전달하는 환경 변수. 시스템 변수를 포함할 수 있습니다. Tomcat의 경우, `CATALINA_OPTS`을 사용합니다. | `-Dmysysproperty=%DRIVEPATH%` |
| `AZURE_JAVA_APP_PATH` | 환경 변수는 사용자 지정 스크립트에서 사용할 수 있으므로 로컬에 복사된 후에는 app.jar에 대한 위치를 갖습니다 | |
| `SKIP_JAVA_KEYSTORE_LOAD` | App Service가 인증서를 키 저장소에 자동으로 로드하지 못하도록 하는 값 1 ||
| `WEBSITE_JAVA_JAR_FILE_NAME` | 사용할 .jar 파일. 문자열이 .jar로 끝나지 않는 경우, .jar를 추가합니다. 기본값은 app.jar입니다 ||
| `WEBSITE_JAVA_WAR_FILE_NAME` | 사용할 .war 파일. 문자열이 .war로 끝나지 않는 경우, .war을 추가합니다. 기본값은 app.war입니다 ||
| `JAVA_ARGS` | 다른 java 웹 서버에 필요한 java opts. 기본값은 `-noverify -Djava.net.preferIPv4Stack=true`입니다. ||
| `JAVA_WEBSERVER_PORT_ENVIRONMENT_VARIABLES` | 서버 포트의 Java 웹 프레임 워크에서 사용하는 환경 변수. Spring, Micronaut, Grails, MicroProfile Thorntail, Helidon, Ratpack, and Quarkus 등의 프레임워크가 포함됩니다 ||
| `JAVA_TMP_DIR` | Java 인수에 `-Dsite.tempdir`로 추가됩니다. 기본값은 `TEMP`입니다. ||
| `WEBSITE_SKIP_LOCAL_COPY` | 기본적으로 배포된 app.jar는 `/home/site/wwwroot`에서 로컬 위치로 복사됩니다. 이 동작을 사용하지 않도록 설정하고 `/home/site/wwwroot`에서 직접 app.jar를 로드하려면, 이 변수를 `1` 또는 `true`으로 설정합니다. 로컬 캐시를 사용하는 경우에는 이 설정이 적용되지 않습니다. | |
| `TOMCAT_USE_STARTUP_BAT` | 네이티브 Windows 앱에만 해당합니다. 기본적으로 Tomcat 서버는 `startup.bat`로 시작됩니다. 대신 `catalina.bat`을 사용하려면 `0` 또는 `False`으로 설정합니다. | `%LOCAL_EXPANDED%\tomcat` |
| `CATALINA_OPTS` | Tomcat apps의 경우, `java` 명령에 전달하는 환경 변수. 시스템 변수를 포함할 수 있습니다. | |
| `CATALINA_BASE` | 사용자 지정 Tomcat 설치를 사용하려면, 설치 위치로 설정합니다. | |
| `WEBSITE_JAVA_MAX_HEAP_MB` | Java 최대 힙(MB). 이 설정은 실험판 Tomcat 버전이 사용되는 경우에만 적용됩니다. | |
| `WEBSITE_DISABLE_JAVA_HEAP_CONFIGURATION` | 이 변수를 `true` 또는 `1`으로 설정하여 수동으로 `WEBSITE_JAVA_MAX_HEAP_MB`을 비활성화합니다. | |
| `WEBSITE_AUTH_SKIP_PRINCIPAL` | 기본적으로, 기본 제공 [인증](overview-authentication-authorization.md)을 사용할 때, `isSecure`, `getRemoteAddr`, `getRemoteHost`, `getScheme`, `getServerPort` 등의 Tomcat [HttpServletRequest 인터페이스](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)가 하이드레이션 됩니다. 사용하지 않도록 설정하려면, `1`로 설정합니다.  | |
| `WEBSITE_SKIP_FILTERS` | App Service에 의해 추가된 모든 servlet 필터를 사용하지 않도록 설정하려면 `1`로 설정합니다. ||
| `IGNORE_CATALINA_BASE` | 기본적으로 App Service는 Tomcat 변수 `CATALINA_BASE`이 정의되어 있는지 여부를 확인합니다. 그렇지 않으면, `%HOME%\tomcat\conf\server.xml`이 있는지 조회합니다. 파일이 존재하면, `CATALINA_BASE`을 `%HOME%\tomcat`로 설정합니다. 이 동작을 사용하지 않도록 설정하고 `CATALINA_BASE`을 제거하려면, 이 변수를 `1` 또는 `true`으로 설정합니다. ||
| `PORT` | 읽기 전용입니다. Linux 앱의 경우, Java 런타임이 컨테이너에서 수신 대기하는 포트. | |
| `WILDFLY_VERSION` | 읽기 전용입니다. JBoss(Linux) 앱의 경우, WildFly 버전. | |
| `TOMCAT_VERSION` | 읽기 전용입니다. Linux Tomcat 앱의 경우, Tomcat 버전. ||
| `JBOSS_HOME` | 읽기 전용입니다. JBoss(Linux) 앱의 경우, WildFly 설치 경로. | |
| `AZURE_JETTY9_CMDLINE` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Jetty 9를 시작하기 위한 명령줄 인수. | |
| `AZURE_JETTY9_HOME` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Jetty 9 설치 경로.| |
| `AZURE_JETTY93_CMDLINE` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Jetty 9.3을 시작하기 위한 명령줄 인수. | |
| `AZURE_JETTY93_HOME` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Jetty 9.3 설치 경로. | |
| `AZURE_TOMCAT7_CMDLINE` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Tomcat 7을 시작하기 위한 명령줄 인수. | |
| `AZURE_TOMCAT7_HOME` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Tomcat 7 설치 경로. | |
| `AZURE_TOMCAT8_CMDLINE` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Tomcat 8을 시작하기 위한 명령줄 인수. | |
| `AZURE_TOMCAT8_HOME` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Tomcat 8 설치 경로. | |
| `AZURE_TOMCAT85_CMDLINE` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Tomcat 8.5를 시작하기 위한 명령줄 인수. | |
| `AZURE_TOMCAT85_HOME` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Tomcat 8.5 설치 경로. | |
| `AZURE_TOMCAT90_CMDLINE` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Tomcat 9를 시작하기 위한 명령줄 인수. | |
| `AZURE_TOMCAT90_HOME` | 읽기 전용입니다. 네이티브 Windows 앱의 경우, Tomcat 9 설치 경로. | |
| `AZURE_SITE_HOME` | Java 인수에 `-Dsite.home`로 추가되는 값. 기본값은 `HOME`의 값입니다. | |
| `HTTP_PLATFORM_PORT` | Java 인수에 `-Dport.http`로 추가됩니다. 다른 Java 웹 프레임워크에서 사용하는 `SERVER_PORT`, `MICRONAUT_SERVER_PORT`, `THORNTAIL_HTTP_PORT`, `RATPACK_PORT`, `QUARKUS_HTTP_PORT`, `PAYARAMICRO_PORT`과 같은 환경 변수는 이 값으로 설정됩니다. ||
| `AZURE_LOGGING_DIR` | 네이티브 Windows 앱에만 해당합니다. Java 인수에 `-Dsite.logdir`로 추가됩니다. 기본값은 `%HOME%\LogFiles\`입니다. ||

<!-- 
WEBSITE_JAVA_COPY_ALL
AZURE_SITE_APP_BASE
 -->

# <a name="nodejs"></a>[Node.JS](#tab/node)

| 설정 이름 | 설명 |
|-|-|
| `PORT` | 읽기 전용입니다. Linux 앱의 경우, .Node.js 앱이 컨테이너에서 수신 대기하는 포트. |
| `WEBSITE_ROLE_INSTANCE_ID` | 읽기 전용입니다. 현재 인스턴스의 ID. |
| `PM2HOME` | |
| `WEBSITE_NODE_DEFAULT_VERSION` | 네이티브 Windows 앱의 경우, 앱이 사용하는 기본 노드 버전. [지원되는 Node.js 버전](configure-language-nodejs.md#show-nodejs-version) 은 여기에서 사용할 수 있습니다. |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_REMOTE_DEBUGGING_BREAK
APPSVC_TUNNEL_PORT -->

# <a name="python"></a>[Python](#tab/python)

| 설정 이름 | 설명 |
|-|-|
| `APPSVC_VIRTUAL_ENV` | 읽기 전용입니다. |
| `PORT` | 읽기 전용입니다. Linux 앱의 경우, Python 앱이 컨테이너에서 수신 대기하는 포트. |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_TUNNEL_PORT | -debugAdapter ptvsd -debugPort $APPSVC_TUNNEL_PORT"
APPSVC_REMOTE_DEBUGGING_BREAK | debugArgs+=" -debugWait" -->

# <a name="php"></a>[PHP](#tab/php)

| 설정 이름 | 설명 | 예제|
|-|-|-|
| `PHP_Extensions` | 쉼표로 구분된 PHP 확장 목록. | `extension1.dll,extension2.dll,Name1=value1` |
| `PHP_ZENDEXTENSIONS` | 네이티브 앱 Windows의 경우, `D:\devtools\xdebug\2.6.0\php_7.2\php_xdebug-2.6.0-7.2-vc15-nts.dll`과 같은 XDebug 확장의 경로로 설정합니다. Linux 앱의 경우, `xdebug`로 설정하여 PHP 컨테이너의 XDebug 버전을 사용합니다. ||
| `PHP_VERSION` | 읽기 전용입니다. 선택한 PHP 버전. ||
| `PORT` | 읽기 전용입니다. Apache 서버가 컨테이너에서 수신 대기하는 포트. ||
| `WEBSITE_ROLE_INSTANCE_ID` | 읽기 전용입니다. 현재 인스턴스의 ID. ||
| `WEBSITE_PROFILER_ENABLE_TRIGGER` | `TRUE`로 설정하여 `xdebug.profiler_enable_trigger=1`와 `xdebug.profiler_enable=0`을 기본 `php.ini`로 추가합니다. ||
| `WEBSITE_ENABLE_PHP_ACCESS_LOGS` | `TRUE`로 설정하여 서버에 요청을 기록합니다(`/etc/apache2/apache2.conf`에 `CustomLog \dev\stderr combined`추가). ||
| `APACHE_SERVER_LIMIT` | Apache 특정 변수. 기본값은 `1000`입니다. ||
| `APACHE_MAX_REQ_WORKERS` | Apache 특정 변수. 기본값은 `256`입니다. ||

<!-- 
ZEND_BIN_PATH
MEMCACHESHIM_REDIS_ENABLE
MEMCACHESHIM_PORT 
APACHE_LOG_DIR | RUN sed -i 's!ErrorLog ${APACHE_LOG_DIR}/error.log!ErrorLog /dev/stderr!g' /etc/apache2/apache2.conf 
APACHE_RUN_USER | RUN sed -i 's!User ${APACHE_RUN_USER}!User www-data!g' /etc/apache2/apache2.conf 
APACHE_RUN_GROUP | RUN sed -i 's!User ${APACHE_RUN_GROUP}!Group www-data!g' /etc/apache2/apache2.conf  
-->

# <a name="ruby"></a>[Ruby](#tab/ruby)

| 설정 이름 | 설명 | 예제 |
|-|-|-|
| `PORT` | 읽기 전용입니다. Rails 앱이 컨테이너에서 수신 대기하는 포트. ||
| `WEBSITE_ROLE_INSTANCE_ID` | 읽기 전용입니다. 현재 인스턴스의 ID. ||
| `RAILS_IGNORE_SPLASH` | 기본적으로, Gemfile이 없으면 기본 시작 페이지가 표시됩니다. 시작 페이지를 사용하지 않도록 설정하려면 이 변수를 임의의 값으로 설정합니다. ||
| `BUNDLE_WITHOUT` | `bundle install`에 `--without` 옵션을 추가하려면 제외하려는 그룹에 공백으로 구분하여 변수를 설정합니다. 기본적으로 모든 Gem이 설치됩니다. | `test development` |
| `BUNDLE_INSTALL_LOCATION` | gem을 설치할 디렉터리. 기본값은 `/tmp/bundle`입니다. ||
| `RUBY_SITE_CONFIG_DIR` | 사이트 구성 디렉터리. 기본값은 `/home/site/config`입니다. 컨테이너는 이 디렉터리에서 압축된 gem을 확인합니다. ||
| `SECRET_KEY_BASE` | 기본적으로 임의의 비밀 키 베이스가 생성됩니다. 사용자 지정 비밀 키 베이스를 사용하려면 이 변수를 원하는 키 베이스로 설정합니다. ||
| `RAILS_ENV` | 레일 환경. 기본값은 `production`입니다. ||
| `GEM_PRISTINE` | `gem pristine --all`을 실행하려면 이 변수를 임의의 값으로 설정합니다. ||

-----

## <a name="domain-and-dns"></a>도메인 및 DNS

| 설정 이름| 설명 | 예제 |
|-|-|-|
| `WEBSITE_DNS_SERVER` | 나가는 연결(예: 백 엔드 서비스)에 대한 기본 DNS 서버의 IP 주소. App Service에 대한 기본 DNS 서버는 IP 주소가 `168.63.129.16`인 Azure DNS입니다. 앱이 [VNet 통합](./overview-vnet-integration.md)을 사용하거나 [App Service environment](environment/intro.md)에 있는 경우, 기본적으로 VNet에서 DNS 서버 구성을 상속합니다. | `10.0.0.1` |
| `WEBSITE_DNS_ALT_SERVER` | 나가는 연결에 대한 대체 DNS 서버의 IP 주소. `WEBSITE_DNS_SERVER`을 참조하세요. | |

<!-- 
DOMAIN_OWNERSHIP_VERIFICATION_IDENTIFIERS
 -->

## <a name="tlsssl"></a>TLS/SSL

자세한 내용은 [Azure App Service의 코드에서 TLS/SSL 인증서 사용](configure-ssl-certificate-in-code.md)을 참조하세요.

| 설정 이름| 설명 |
|-|-|
| `WEBSITE_LOAD_CERTIFICATES` | 코드에서 로드하려는 인증서에 대한 쉼표로 구분된 지문 값 또는 모든 인증서를 코드에 로드할 수 있도록 허용하는 `*`. [앱에 추가된 인증서](configure-ssl-certificate.md)만 로드할 수 있습니다. |
| `WEBSITE_PRIVATE_CERTS_PATH` | 읽기 전용입니다. 로드된 프라이빗 인증서에 대한 Windows 컨테이너의 경로. |
| `WEBSITE_PUBLIC_CERTS_PATH` | 읽기 전용입니다. 로드된 공용 인증서에 대한 Windows 컨테이너의 경로. |
| `WEBSITE_INTERMEDIATE_CERTS_PATH` | 읽기 전용입니다. 로드된 중간 인증서에 대한 Windows 컨테이너의 경로. |
| `WEBSITE_ROOT_CERTS_PATH` | 읽기 전용입니다. 로드된 루트 인증서에 대한 Windows 컨테이너의 경로. |

## <a name="deployment-slots"></a>배포 슬롯 

자세한 내용은 [Azure App Service에서 스테이징 환경 설정](deploy-staging-slots.md)을 참조하세요.

| 설정 이름| 설명 | 예제 |
|-|-|-|
|`WEBSITE_SLOT_NAME`| 읽기 전용입니다. 현재 배포 슬롯의 이름. 프로덕션 슬롯의 이름은 `Production`입니다. ||
|`WEBSITE_OVERRIDE_STICKY_EXTENSION_VERSIONS`| 기본적으로 사이트 확장의 버전은 각 슬롯에 따라 다릅니다. 이렇게 하면 교환 후 확장 버전 변경으로 인한 예기치 않은 애플리케이션 동작이 방지됩니다. 확장 버전도 교환하려면, *모든 슬롯* 에서 `1`로 설정합니다. ||
|`WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS`| 기본적으로 특정 설정을 [고정 또는 교환 불가능](deploy-staging-slots.md#which-settings-are-swapped)으로 지정합니다. 기본값은 `true`입니다. 대신, *모든 배포 슬롯* 을 교환할 수 있도록 이 설정을 `false` 또는 `0`로 설정합니다. 특정 설정 형식에 대한 세분화된 제어는 없습니다. ||
|`WEBSITE_SWAP_WARMUP_PING_PATH`| 슬래시로 시작하는 교환에서 대상 슬롯을 준비하기 위한 ping 경로. 기본값은 루트 경로를 ping하는 `/`입니다. | `/statuscheck` |
|`WEBSITE_SWAP_WARMUP_PING_STATUSES`| 교환 중 준비 작업에 대한 유효한 HTTP 응답 코드. 반환된 상태 코드가 목록에 없는 경우 준비 및 교환 작업이 중지됩니다. 기본적으로 모든 응답 코드는 유효합니다. | `200,202` |
| `WEBSITE_SLOT_NUMBER_OF_TIMEOUTS_BEFORE_RESTART` | 슬롯 교환 중 특정 VM 인스턴스에서 사이트를 강제로 다시 시작하는 최대 시간 제한 수. 기본값은 `3`입니다. ||
| `WEBSITE_SLOT_MAX_NUMBER_OF_TIMEOUTS` | 슬롯 교환 중 포기 전에 만들 단일 URL에 대한 최대 시간 제한 요청 수. 기본값은 `5`입니다. ||
| `WEBSITE_SKIP_ALL_BINDINGS_IN_APPHOST_CONFIG` | `true`또는 `1`로 설정하여 `applicationHost.config`의 모든 바인딩을 건너뜁니다. 기본값은 `false`입니다. `applicationHost.config`이 슬롯의 교환된 호스트 이름으로 업데이트되기 때문에, 앱이 재시작을 트리거하는 경우, 이 변수를 `true`로 설정하여 이러한 종류의 재시작을 방지합니다. WCF(Windows Communication Foundation) 앱을 실행하는 경우, 이 변수를 설정하지 마십시오. ||

<!-- 
|`WEBSITE_SWAP_SLOTNAME`||| 
-->

## <a name="custom-containers"></a>사용자 지정 컨테이너

사용자 지정 컨테이너에 대한 자세한 내용은 [Azure에서 사용자 지정 컨테이너 실행](quickstart-custom-container.md)을 참조하세요.

| 설정 이름| 설명 | 예제 |
|-|-|-|
| `WEBSITES_ENABLE_APP_SERVICE_STORAGE` | 확장된 인스턴스 간에 `/home` 디렉터리를 공유할 수 있도록 하려면 `true`로 설정합니다. 사용자 지정 컨테이너에 대한 기본값은 `false`입니다. ||
| `WEBSITES_CONTAINER_START_TIME_LIMIT` | 컨테이너를 다시 시작하기 전에 컨테이너가 시작이 완료되기를 기다리는 시간(초). 기본값은 `230`입니다. 최대 `1800`까지 늘릴 수 있습니다. ||
| `DOCKER_REGISTRY_SERVER_URL` | App Service 사용자 지정 컨테이너를 실행할 때 레지스트리 서버의 URL. 보안을 위해, 이 변수는 컨테이너에 전달되지 않습니다. | `https://<server-name>.azurecr.io` |
| `DOCKER_REGISTRY_SERVER_USERNAME` | `DOCKER_REGISTRY_SERVER_URL`에서 레지스트리 서버로 인증할 사용자 이름. 보안을 위해, 이 변수는 컨테이너에 전달되지 않습니다. ||
| `DOCKER_REGISTRY_SERVER_PASSWORD` | `DOCKER_REGISTRY_SERVER_URL`에서 레지스트리 서버로 인증하는 데 사용할 암호. 보안을 위해, 이 변수는 컨테이너에 전달되지 않습니다. ||
| `WEBSITES_WEB_CONTAINER_NAME` | Docker Compose 앱에서는 컨테이너 중 하나만 인터넷에 액세스할 수 있습니다. 기본 컨테이너 선택을 재정의하려면, 구성 파일에 정의된 컨테이너의 이름으로 설정합니다. 기본적으로, 인터넷 액세스 가능 컨테이너는 포트 80 또는 8080을 정의하는 첫 번째 컨테이너이거나, 이러한 컨테이너가 없으면 구성 파일에 정의된 첫 번째 컨테이너입니다. |  |
| `WEBSITES_PORT` | 사용자 지정 컨테이너의 경우 App Service가 요청을 라우팅할 컨테이너의 사용자 지정 포트 번호입니다. 기본적으로 App Service는 포트 80 및 8080의 자동 포트 검색을 시도합니다. 이 설정은 환경 변수로 컨테이너에 삽입되지 ‘않습니다’. ||
| `WEBSITE_CPU_CORES_LIMIT` | 기본적으로 Windows 컨테이너는 선택한 가격 책정 계층에서 사용할 수 있는 모든 코어를 사용하여 실행됩니다. 컨테이너에서 사용하는 코어 수를 줄이려면, 원하는 코어 제한 수로 설정합니다. 자세한 내용은 [컴퓨팅 코어 수 사용자 지정](configure-custom-container.md?pivots=container-windows#customize-the-number-of-compute-cores)을 참조하세요.||
| `WEBSITE_MEMORY_LIMIT_MB` | 기본적으로 Azure App Service에 배포된 모든 Windows 컨테이너는 1GB RAM으로 제한됩니다. 원하는 메모리 제한(MB)으로 설정합니다. 동일한 플랜의 앱에서 이 설정의 누적 합계는 선택한 가격 책정 계층에서 허용하는 금액을 초과해서는 안됩니다. 자세한 내용은 [컨테이너 메모리 사용자 지정](configure-custom-container.md?pivots=container-windows#customize-container-memory)을 참조하세요. ||
| `CONTAINER_WINRM_ENABLED` | Windows 컨테이너 앱의 경우, `1`로 설정하여 WIN-RM(Windows 원격 관리)을 사용하도록 설정합니다. ||

<!-- 
CONTAINER_ENCRYPTION_KEY
CONTAINER_NAME
CONTAINER_IMAGE_URL
AzureWebEncryptionKey
CONTAINER_START_CONTEXT_SAS_URI
CONTAINER_AZURE_FILES_VOLUME_MOUNT_PATH
CONTAINER_START_CONTEXT
DOCKER_ENABLE_CI
WEBSITE_DISABLE_PRELOAD_HANG_MITIGATION
 -->

## <a name="scaling"></a>크기 조정

| 설정 이름| 설명 |
|-|-|
| `WEBSITE_INSTANCE_ID` | 읽기 전용입니다. 앱이 여러 인스턴스로 확장되는 경우, 현재 VM 인스턴스의 고유 ID. |
| `WEBSITE_IIS_SITE_NAME` | 더 이상 사용되지 않습니다. `WEBSITE_INSTANCE_ID`을 사용합니다. |
| `WEBSITE_DISABLE_OVERLAPPED_RECYCLING` | 반복 재활용을 사용하면 앱의 현재 VM 인스턴스가 종료되기 전에 새 VM 인스턴스가 시작됩니다. 경우에 따라 파일 잠금 문제가 발생할 수 있습니다. `1`로 설정하여 해제해 볼 수 있습니다. |
| `WEBSITE_DISABLE_CROSS_STAMP_SCALE` | 기본적으로, 앱은 Azure Files 또는 Docker 컨테이너를 사용하는 경우에 스탬프를 스케일링할 수 있습니다. `1`또는 `true`로 설정하여 앱의 지역 내에서 스탬프 간 스케일링을 사용하지 않도록 설정합니다. 기본값은 `0`입니다. `WEBSITES_ENABLE_APP_SERVICE_STORAGE`이 `true` 또는 `1`으로 설정된 사용자 지정 Docker 컨테이너는 해당 콘텐츠가 Docker 컨테이너에 완전히 캡슐화되지 않으므로 스탬프 간 스케일링을 할 수 없습니다. |

## <a name="logging"></a>로깅

| 설정 이름| 설명 | 예제 |
|-|-|-|
| `WEBSITE_HTTPLOGGING_ENABLED` | 읽기 전용입니다. Windows 네이티브 앱에 대한 웹 서버 로깅을 사용할지(`1`) 말지(`0`) 여부를 표시합니다. ||
| `WEBSITE_HTTPLOGGING_RETENTION_DAYS` | 웹 서버 로그를 사용하는 경우, Windows 네이티브 앱에 대한 웹 서버 로그의 보존 기간(일). | `10` |
| `WEBSITE_HTTPLOGGING_CONTAINER_URL` | 웹 서버 로그를 사용하는 경우, Windows 네이티브 앱에 대한 웹 서버 로그를 저장할 Blob Storage 컨테이너의 SAS URL. 설정하지 않으면 웹 서버 로그가 앱의 파일 시스템(기본 공유 스토리지)에 저장됩니다. | |
| `DIAGNOSTICS_AZUREBLOBRETENTIONINDAYS` | 애플리케이션 로그를 사용하는 경우, Windows 네이티브 앱에 대한 애플리케이션 로그의 보존 기간(일). | `10` |
| `DIAGNOSTICS_AZUREBLOBCONTAINERSASURL` | 애플리케이션 로그를 사용하는 경우, Windows 네이티브 앱에 대한 애플리케이션 로그를 저장할 Blob Storage 컨테이너의 SAS URL. | |
| `APPSERVICEAPPLOGS_TRACE_LEVEL` | [AppServiceAppLogs](troubleshoot-diagnostic-logs.md#supported-log-types) 로그 유형에 대한 Log Analytics로 발송할 최소 로그 수준. | |
| `DIAGNOSTICS_LASTRESORTFILE` | 생성할 파일 이름 또는 수신기 문제 해결을 위한 내부 오류를 기록하기 위한 로그 디렉터리에 대한 상대 경로. 기본값은 `logging-errors.txt`입니다. ||
| `DIAGNOSTICS_LOGGINGSETTINGSFILE` | `D:\home` 또는 `/home`에 상대적인 로그 설정 파일의 경로. 기본값은 `site\diagnostics\settings.json`입니다. | |
| `DIAGNOSTICS_TEXTTRACELOGDIRECTORY` | 앱 루트(`D:\home\site\wwwroot` 또는 `/home/site/wwwroot`)를 기준으로 하는 로그 폴더. | `..\..\LogFiles\Application`|
| `DIAGNOSTICS_TEXTTRACEMAXLOGFILESIZEBYTES` | 파일의 최대 크기(바이트). 기본값은 `131072`(128KB)입니다. ||
| `DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES` | 로그 폴더의 최대 크기(바이트). 기본값은 `1048576`(1MB)입니다. ||
| `DIAGNOSTICS_TEXTTRACEMAXNUMLOGFILES` | 유지할 최대 로그 파일 수. 기본값은 `20`입니다. | |
| `DIAGNOSTICS_TEXTTRACETURNOFFPERIOD` | 애플리케이션 로깅을 사용하도록 유지하기 위한 시간(밀리초). 기본값은 `43200000`(12시간)입니다. ||
| `WEBSITE_LOG_BUFFERING` | 기본적으로 로그 버퍼링이 사용됩니다. `0`로 설정하여 해제합니다. ||
| `WEBSITE_ENABLE_PERF_MODE` | 네이티브 Windows 앱의 경우, `TRUE`로 설정하여 10초 이내에 반환된 성공적인 요청에 대한 IIS 로그 항목을 해제합니다. 이는 확장된 로깅을 제거하여 성능 벤치마킹을 수행하는 빠른 방법입니다. ||

<!-- 
| `DIAGNOSTICS_AZURETABLESASURL` | old? | |
| WEBSITE_APPSERVICEAPPLOGS_TRACE_ENABLED | Read-only. Added when | | 
| AZMON_LOG_CATEGORY_APPSERVICEAPPLOGS_ENABLED | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICEPLATFORMLOGS_ENABLED  | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICECONSOLELOGS_ENABLED | Read-only. Shows when the AppServiceConsoleLogs category in Azure Monitor settings is enabled. |
WEBSITE_FUNCTIONS_AZUREMONITOR_CATEGORIES
WINDOWS_TRACING_FLAGS
WINDOWS_TRACING_LOGFILE
WEBSITE_FREB_DISABLE
WEBSITE_ARR_SESSION_AFFINITY_DISABLE

-->

## <a name="performance-counters"></a>성능 카운터

다음은 열거하는 경우에는 존재하지 않지만 개별적으로 조회하는 경우 해당 값을 반환하는 '가짜' 환경 변수입니다. 값은 동적이며 모든 조회에서 변경할 수 있습니다.

| 설정 이름| 설명 |
|-|-|
| `WEBSITE_COUNTERS_ASPNET` | ASP.NET 성능 카운터를 포함하는 JSON 개체. |
| `WEBSITE_COUNTERS_APP` | 샌드박스 카운터를 포함하는 JSON 개체. |
| `WEBSITE_COUNTERS_CLR` | CLR 카운터를 포함하는 JSON 개체. |
| `WEBSITE_COUNTERS_ALL` | 다른 세 변수의 조합을 포함하는 JSON 개체. |

## <a name="caching"></a>캐싱

| 설정 이름| 설명 |
|-|-|
| `WEBSITE_LOCAL_CACHE_OPTION` | 로컬 캐시를 사용할 수 있는지 여부. 사용 가능한 옵션은 다음과 같습니다. <br/>- `Default`: 스탬프 수준 전역 설정을 상속합니다.<br/>- `Always`: 앱에 대해 사용하도록 설정합니다.<br/>- OnStorageUnavailability<br/>- `Disabled`: 앱에 대해 사용하지 않도록 설정합니다. |
| `WEBSITE_LOCAL_CACHE_READWRITE_OPTION` | 로컬 캐시의 읽기-쓰기 옵션. 사용 가능한 옵션은 다음과 같습니다. <br/>- `ReadOnly`: 캐시는 읽기 전용입니다.<br/>- `WriteWithCopyBack`: 로컬 캐시에 쓰기를 허용하고 공유 스토리지에 주기적으로 복사합니다. SCM 사이트가 로컬 캐시를 가리키기 때문에 단일 인스턴스 앱에만 적용됩니다.<br/>- `WriteButDiscardChanges`: 로컬 캐시에 대한 쓰기를 허용하지만 로컬에서 변경한 내용을 무시합니다. |
| `WEBSITE_LOCAL_CACHE_SIZEINMB` | 로컬 캐시의 크기(MB). 기본값은 `1000`(1GB)입니다. |
| `WEBSITE_LOCALCACHE_READY` | 앱이 로컬 캐시를 사용하는지 여부를 나타내는 읽기 전용 플래그. |
| `WEBSITE_DYNAMIC_CACHE` | 여러 인스턴스에 대한 액세스를 허용하는 네트워크 파일 공유 특성으로 인해, 동적 캐시는 최근에 액세스한 파일을 인스턴스에 로컬로 캐싱하여 성능을 향상시킵니다. 파일이 수정되면 캐시가 무효화됩니다. 캐시 위치는 `%SYSTEMDRIVE%\local\DynamicCache`입니다(동일한 `%SYSTEMDRIVE%\local` 할당량 적용). 기본적으로, 전체 콘텐츠 캐싱은 파일 콘텐츠와 디렉터리/파일 메타데이터(타임스탬프, 크기, 디렉터리 콘텐츠)를 모두 포함하도록 설정됩니다(`1`로 설정). 로컬 디스크 사용을 절약하려면, `2`로 설정하여 디렉터리/파일 메타데이터(타임스탬프, 크기, 디렉터리 콘텐츠)만 캐싱합니다. 캐싱을 해제하려면 `0`로 설정합니다. |
| `WEBSITE_READONLY_APP` | 동적 캐시를 사용하는 경우, 이 변수를 `1`으로 설정하여 앱 루트(`D:\home\site\wwwroot` 또는 `/home/site/wwwroot`)에 대한 쓰기 권한을 사용하지 않도록 설정할 수 있습니다. `App_Data` 디렉터리를 제외하고, 배타적 잠금이 허용되지 않으므로 잠긴 파일에 의해 배포가 차단되지 않습니다. |

<!-- 
HTTP_X_LOCALCACHE_READY_CHECK
HTTP_X_APPINIT_CHECK
X_SERVER_ROUTED
HTTP_X_MS_REQUEST_ID
HTTP_X_MS_APIM_HOST
HTTP_X_MS_FORWARD_HOSTNAMES
HTTP_X_MS_FORWARD_TOKEN
HTTP_MWH_SECURITYTOKEN
IS_SERVICE_ENDPOINT
VNET_CLIENT_IP
HTTP_X_MS_SITE_RESTRICTED_TOKEN
HTTP_X_FROM
| LOCAL_ADDR | internal private IP address of app |
SERVERS_FOR_HOSTING_SERVER_PROVIDER
NEED_PLATFORM_AUTHORIZATION
TIP_VALUE
TIP_RULE_NAME
TIP_RULE_MAX_AGE
REWRITE_PATH
NEGOTIATE_CLIENT_CERT
| CLIENT_CERT_MODE | used with ClientCertEnabled. Required means ClientCert is required.  Optional means ClientCert is optional or accepted. OptionalInteractiveUser is similar to Optional; however, it will not ask user for Certificate in Browser Interactive scenario.|
| HTTPS_ONLY | set with terraform? |
 -->

## <a name="networking"></a>네트워킹

다음 환경 변수는 [하이브리드 연결](app-service-hybrid-connections.md) 및 [VNET 통합](./overview-vnet-integration.md)과 관련이 있습니다.

| 설정 이름 | 설명 |
|-|-|
| `WEBSITE_RELAYS` | 읽기 전용입니다. 엔드포인트 및 서비스 버스 데이터를 포함하여 하이브리드 연결을 구성하는 데 필요한 데이터입니다. |
| `WEBSITE_REWRITE_TABLE` | 읽기 전용입니다. 런타임에 조회를 수행하여 연결을 적절하게 다시 작성하는 데 사용됩니다. | 
| `WEBSITE_VNET_ROUTE_ALL` | 기본적으로 [지역 VNet 통합](./overview-vnet-integration.md#regional-virtual-network-integration)을 사용하는 경우, 앱은 RFC1918 트래픽만 VNet으로 라우팅합니다. 모든 아웃바운드 트래픽을 VNet으로 라우팅하고 동일한 NSG 및 UDR을 적용하려면 `1`로 설정합니다. 이 설정을 사용하면 VNet을 통해 비 RFC1918 엔드포인트에 액세스하고, 앱에서 나가는 모든 아웃바운드 트래픽을 보호하고, 선택한 네트워크 어플라이언스에 모든 아웃바운드 트래픽을 강제로 터널링할 수 있습니다. |
| `WEBSITE_PRIVATE_IP` | 읽기 전용입니다. [VNet 와 통합](./overview-vnet-integration.md)될 때 앱과 연결된 IP 주소. 지역 VNet 통합의 경우, 값은 위임된 서브넷의 주소 범위에 속하는 IP이고 VNet 통합이 필요한 게이트웨이의 경우 값은 Virtual Network Gateway에 구성된 지점 및 사이트 간 주소 풀의 주소 범위에 속하는 IP입니다. 이 IP는 앱이 VNet을 통해 리소스에 연결하는 데 사용합니다. 또한 설명된 주소 범위 내에서 변경할 수 있습니다. |
| `WEBSITE_PRIVATE_PORTS` | 읽기 전용입니다. VNet 통합에서 앱에서 다른 노드와 통신하는 데 사용할 수 있는 포트를 보여줍니다. |

<!-- | WEBSITE_SLOT_POLL_WORKER_FOR_CHANGE_NOTIFICATION | Poll worker before pinging the site to detect when change notification has been processed. |
WEBSITE_SPECIAL_CACHE
WEBSITE_SOCKET_STATISTICS_ENABLED
| `WEBSITE_ENABLE_NETWORK_HEALTHCHECK` | Enable network health checks that won't be blocked by CORS or built-in authentication. Three check methods can be utilized: <br/>- Ping an IP address (configurable by `WEBSITE_NETWORK_HEALTH_IPADDRS`). <br/>- Check DNS resolution (configurable by `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS`). <br/>- Poll URI endpoints (configurable by `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS`).<br/> |
| `WEBSITE_NETWORK_HEALTH_IPADDRS` | https://msazure.visualstudio.com/One/_git/AAPT-Antares-EasyAuth/pullrequest/3763264 |
| `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_INTEVALSECS` | Interval of the network health check in seconds. The minimum value is 30 seconds. | |
| `WEBSITE_NETWORK_HEALTH_TIMEOUT_INTEVALSECS` | Time-out of the network health check in seconds. | |

-->
<!-- | CONTAINER_WINRM_USERNAME |
| CONTAINER_WINRM_PASSWORD| -->

## <a name="key-vault-references"></a>Key Vault 참조

다음 환경 변수는 [키 자격 증명 모음 참조](app-service-key-vault-references.md)와 관련이 있습니다.

| 설정 이름 | 설명 |
|-|-|
| `WEBSITE_KEYVAULT_REFERENCES` | 읽기 전용입니다. 앱에서 현재 구성된 모든 Key Vault 참조에 대한 정보(상태 포함)를 포함합니다. |
| `WEBSITE_SKIP_CONTENTSHARE_VALIDATION` | 앱의 공유 스토리지 연결(`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 사용)을 Key Vault 참조로 설정하면, 앱은 다음 조건 중 하나에 해당하면 앱 생성 시 키 자격 증명 모음 참조를 해결하지 못하거나 업데이트할 수 없습니다. <br/>- 앱이 시스템 할당 ID를 통해 키 자격 증명 모음에 액세스합니다.<br/>- 앱이 사용자 할당 ID를 통해 키 자격 증명 모음에 액세스하고 키 자격 증명 모음이 [VNet으로 잠깁니다](../key-vault/general/overview-vnet-service-endpoints.md).<br/>생성하거나 업데이트할 때 오류를 방지하려면 이 변수를 `1`로 설정합니다. |
| `WEBSITE_DELAY_CERT_DELETION` | 작업자 프로세스가 종속된 인증서가 종료될 때까지 삭제되지 않도록 하려면, 사용자가 이 env var을 1로 설정할 수 있습니다. |
<!-- | `WEBSITE_ALLOW_DOUBLE_ESCAPING_URL` | TODO | -->

## <a name="cors"></a>CORS

다음 환경 변수는 CORS(원본 간 리소스 공유) 구성과 관련이 있습니다.

| 설정 이름 | 설명 |
|-|-|
| `WEBSITE_CORS_ALLOWED_ORIGINS` | 읽기 전용입니다. CORS에 허용되는 원본을 표시합니다. |
| `WEBSITE_CORS_SUPPORT_CREDENTIALS` | 읽기 전용입니다. `true`에 대한 `Access-Control-Allow-Credentials` 헤더를 설정했는지(`True`) 아닌지(`False`) 여부를 나타냅니다. |

## <a name="authentication--authorization"></a>인증 및 권한 부여

다음 환경 변수는 [App Service 인증](overview-authentication-authorization.md)과 관련이 있습니다.

| 설정 이름| 설명|
|-|-|
| `WEBSITE_AUTH_DISABLE_IDENTITY_FLOW`  | `true`로 설정하면, ASP.NET 기반 웹 애플리케이션(v1 함수 앱 포함)에서 스레드 보안 주체 ID 할당을 사용하지 않도록 설정합니다. 이는 개발자가 인증을 사용하여 사이트에 대한 액세스를 보호할 수 있도록 설계되었지만, 앱 논리 내에서 별도의 로그인 메커니즘을 계속 사용하도록 합니다. 기본값은 `false`입니다. |
| `WEBSITE_AUTH_HIDE_DEPRECATED_SID` | `true` 또는 `false`. 기본값은 `false`입니다. 이는 Azure App Service용 레거시 Azure Mobile Apps 통합을 위한 설정입니다. 이를 `true`로 설정하면 사용자가 프로필 정보를 변경하는 경우 인증된 사용자에 대해 생성된 SID(보안 ID)가 변경되는 문제가 해결됩니다. 이 값을 변경하면 기존 Azure Mobile Apps 사용자 ID가 변경됩니다. 대부분의 앱은 이 설정을 사용할 필요가 없습니다. |
| `WEBSITE_AUTH_NONCE_DURATION`| `_hours_:_minutes_:_seconds_` 형식 내의 _기간_ 값. 기본값은 `00:05:00`(5분)입니다. 이 설정은 모든 브라우저 기반 로그인에 대해 생성된 [암호화 nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce)의 수명을 제어합니다. 지정된 시간에 로그인이 완료되지 않으면 로그인 흐름이 자동으로 다시 시도됩니다. 이 애플리케이션 설정은 V1(classic) 구성 환경에서 사용하기 위한 것입니다. V2 인증 구성 스키마를 사용하는 경우, 대신 `login.nonce.nonceExpirationInterval` 구성 값을 사용해야 합니다. |
| `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` | `true`로 설정하고 사용자가 URL 조각이 포함된 앱 링크를 클릭하면, 로그인 프로세스는 URL의 URL 조각 부분이 로그인 리디렉션 프로세스에서 손실되지 않도록 합니다. 자세한 내용은 [Azure App Service 인증에서 로그인 및 로그아웃 사용자 지정](configure-authentication-customize-sign-in-out.md#preserve-url-fragments)을 참조하세요. |
| `WEBSITE_AUTH_USE_LEGACY_CLAIMS` | 업그레이드 간에 이전 버전과의 호환성을 유지하기 위해, 인증 모듈은 `/.auth/me` API에서 짧은 이름에서 긴 이름으로의 레거시 클레임 매핑을 사용하므로 특정 매핑(예: "역할")은 제외됩니다. 최신 버전의 클레임 매핑을 얻으려면, 이 변수를 `False`로 설정합니다. "역할" 예제에서는 긴 클레임 이름 "http://schemas.microsoft.com/ws/2008/06/identity/claims/role"에 매핑됩니다. |
| `WEBSITE_AUTH_DISABLE_WWWAUTHENTICATE` | `true` 또는 `false`. 기본값은 `false`입니다. `true`로 설정하면 모듈에서 생성된 HTTP 401 응답에서 [`WWW-Authenticate`](https://developer.mozilla.org/docs/Web/HTTP/Headers/WWW-Authenticate) HTTP 응답 헤더를 제거합니다. 이 애플리케이션 설정은 V1(classic) 구성 환경에서 사용하기 위한 것입니다. V2 인증 구성 스키마를 사용하는 경우, 대신 `identityProviders.azureActiveDirectory.login.disableWwwAuthenticate` 구성 값을 사용해야 합니다. |
| `WEBSITE_AUTH_STATE_DIRECTORY`  | 파일 기반 토큰 저장소를 사용할 때 토큰이 저장되는 로컬 파일 시스템 디렉터리 경로. 기본값은 `%HOME%\Data\.auth`입니다. 이 애플리케이션 설정은 V1(classic) 구성 환경에서 사용하기 위한 것입니다. V2 인증 구성 스키마를 사용하는 경우, 대신 `login.tokenStore.fileSystem.directory` 구성 값을 사용해야 합니다. |
| `WEBSITE_AUTH_TOKEN_CONTAINER_SASURL` | 정규화된 Blob 컨테이너 URL. 기본 로컬 파일 시스템을 사용하는 대신, 암호화된 모든 토큰을 저장하고 지정된 Blob Storage 컨테이너에 로드하도록 인증 모듈에 지시합니다.  |
| `WEBSITE_AUTH_TOKEN_REFRESH_HOURS` | 모든 양의 10진수. 기본값은 `72`(시간)입니다. 이 설정은 세션 토큰이 만료된 후 `/.auth/refresh` API를 사용하여 새로 고칠 수 있는 시간을 제어합니다. 주로 세션 토큰을 사용하는 Azure Mobile Apps에 사용하기 위한 것입니다. 이 기간 후에 새로 고침 시도가 실패하고 최종 사용자는 다시 로그인해야 합니다. 이 애플리케이션 설정은 V1(classic) 구성 환경에서 사용하기 위한 것입니다. V2 인증 구성 스키마를 사용하는 경우, 대신 `login.tokenStore.tokenRefreshExtensionHours` 구성 값을 사용해야 합니다. |
| `WEBSITE_AUTH_TRACE_LEVEL`| [Application Logging](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)에 기록된 인증 추적의 세부 정보를 제어합니다. 유효한 값은 `Off`, `Error`, `Warning`, `Information`, `Verbose`입니다. 기본값은 `Verbose`입니다. |
| `WEBSITE_AUTH_VALIDATE_NONCE`| `true` 또는 `false`. 기본값은 `true`입니다. 대화형 로그인 중에 발생하는 [암호화 nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce) 유효성 검사 오류를 일시적으로 디버깅하는 경우를 제외하고, 이 값을 `false`로 설정하면 안 됩니다. 이 애플리케이션 설정은 V1(classic) 구성 환경에서 사용하기 위한 것입니다. V2 인증 구성 스키마를 사용하는 경우, 대신 `login.nonce.validateNonce` 구성 값을 사용해야 합니다. |
| `WEBSITE_AUTH_V2_CONFIG_JSON` | 이 환경 변수는 Azure App Service 플랫폼에서 자동으로 채워지고 통합 인증 모듈을 구성하는 데 사용됩니다. 이 환경 변수의 값은 Azure Resource Manager의 최신 앱의 V2(non-classic) 인증 구성에 해당합니다. 명백하게 구성하기에 적합하지 않습니다. |
| `WEBSITE_AUTH_ENABLED` | 읽기 전용입니다. Windows 또는 Linux 앱에 삽입되어 App Service 인증이 사용되는지 여부를 나타냅니다. |
| `WEBSITE_AUTH_ENCRYPTION_KEY` | 기본적으로, 자동으로 생성된 키는 암호화 키로 사용됩니다. 재정의하려면 원하는 키로 설정합니다. 이는 여러 앱에서 토큰 또는 세션을 공유하려는 경우에 권장됩니다. 지정된 경우 `MACHINEKEY_DecryptionKey` 설정에 우선합니다. |
| `WEBSITE_AUTH_SIGNING_KEY` | 기본적으로 자동으로 생성된 키는 서명 키로 사용됩니다. 재정의하려면 원하는 키로 설정합니다. 이는 여러 앱에서 토큰 또는 세션을 공유하려는 경우에 권장됩니다. 지정된 경우 `MACHINEKEY_ValidationKey` 설정에 우선합니다. |

<!-- System settings
WEBSITE_AUTH_RUNTIME_VERSION
WEBSITE_AUTH_API_PREFIX
WEBSITE_AUTH_TOKEN_STORE
WEBSITE_AUTH_MOBILE_COMPAT
WEBSITE_AUTH_AAD_BYPASS_SINGLE_TENANCY_CHECK
WEBSITE_AUTH_COOKIE_EXPIRATION_TIME
WEBSITE_AUTH_COOKIE_EXPIRATION_MODE
WEBSITE_AUTH_PROXY_HEADER_CONVENTION
WEBSITE_AUTH_PROXY_HOST_HEADER
WEBSITE_AUTH_PROXY_PROTO_HEADER
WEBSITE_AUTH_REQUIRE_HTTPS
WEBSITE_AUTH_DEFAULT_PROVIDER
WEBSITE_AUTH_UNAUTHENTICATED_ACTION
WEBSITE_AUTH_EXTERNAL_REDIRECT_URLS
WEBSITE_AUTH_CUSTOM_IDPS
WEBSITE_AUTH_CUSTOM_AUTHZ_SETTINGS
WEBSITE_AUTH_CLIENT_ID
WEBSITE_AUTH_CLIENT_SECRET
WEBSITE_AUTH_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_CLIENT_SECRET_CERTIFICATE_THUMBPRINT
WEBSITE_AUTH_OPENID_ISSUER
WEBSITE_AUTH_ALLOWED_AUDIENCES
WEBSITE_AUTH_LOGIN_PARAMS
WEBSITE_AUTH_AUTO_AAD
WEBSITE_AUTH_AAD_CLAIMS_AUTHORIZATION
WEBSITE_AUTH_GOOGLE_CLIENT_ID
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GOOGLE_SCOPE
WEBSITE_AUTH_FB_APP_ID
WEBSITE_AUTH_FB_APP_SECRET
WEBSITE_AUTH_FB_APP_SECRET_SETTING_NAME
WEBSITE_AUTH_FB_SCOPE
WEBSITE_AUTH_GITHUB_CLIENT_ID
WEBSITE_AUTH_GITHUB_CLIENT_SECRET
WEBSITE_AUTH_GITHUB_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GITHUB_APP_SCOPE
WEBSITE_AUTH_TWITTER_CONSUMER_KEY
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_CLIENT_ID
WEBSITE_AUTH_MSA_CLIENT_SECRET
WEBSITE_AUTH_MSA_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_SCOPE
WEBSITE_AUTH_FROM_FILE
WEBSITE_AUTH_FILE_PATH
| `WEBSITE_AUTH_CONFIG_DIR` | (Used for the deprecated "routes" feature) |
| `WEBSITE_AUTH_ZUMO_USE_TOKEN_STORE_CLAIMS` | (looks like only a tactical fix) ||
 -->

## <a name="managed-identity"></a>관리 ID

다음 환경 변수는 [관리 ID](overview-managed-identity.md)와 관련이 있습니다.

|설정 이름 | 설명 |
|-|-|
|`IDENTITY_ENDPOINT` | 읽기 전용입니다. 앱의 [관리 ID](overview-managed-identity.md)에 대한 토큰을 검색할 URL. |
| `MSI_ENDPOINT` | 더 이상 사용되지 않습니다. `IDENTITY_ENDPOINT`을 사용합니다. |
| `IDENTITY_HEADER` | 읽기 전용입니다. `IDENTITY_ENDPOINT`에 대한 HTTP GET 요청을 만들 때 `X-IDENTITY-HEADER` 헤더에 추가해야 하는 값입니다. 플랫폼에서 값을 회전합니다. |
| `MSI_SECRET` | 더 이상 사용되지 않습니다. `IDENTITY_HEADER`을 사용합니다. |
<!-- | `WEBSITE_AUTHENTICATION_ENDPOINT_ENABLED` | 기본적으로 사용할 수 없게 설정되어 있나요? TODO | -->

## <a name="health-check"></a>상태 확인

다음 환경 변수는 [상태 검사](monitor-instances-health-check.md)와 관련되어 있습니다.

| 설정 이름 | 설명 |
|-|-|
| `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 인스턴스를 제거하기 전에 실패한 최대 ping 수. `2`과 `100` 사이의 값을 설정합니다. 스케일 업 또는 스케일 아웃할 때 App Service는 새 인스턴스가 준비되도록 Health check 경로를 ping합니다. 자세한 내용은 [상태 검사](monitor-instances-health-check.md)를 참조하세요.|
| `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 정상 상태의 인스턴스가 너무 많아지는 것을 방지하기 위해 인스턴스 중 절반 이하만 제외됩니다. 예를 들어 App Service 계획이 4개의 인스턴스로 확장되고 3개가 비정상이면 최대 2개가 제외됩니다. 나머지 두 인스턴스(정상 1 및 비정상 1)에서는 계속해서 요청을 받습니다. 모든 인스턴스가 비정상인 최악의 시나리오에서는 인스턴스가 제외되지 않습니다. 이 동작을 재정의하려면 `0`과 `100` 사이의 값으로 설정합니다. 값이 높을수록 비정상 인스턴스가 더 많이 제거됩니다. 기본값은 `50`(50%)입니다. |

## <a name="push-notifications"></a>푸시 알림

다음 환경 변수는 [푸시 알림](/previous-versions/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-hub) 기능과 관련이 있습니다.

| 설정 이름 | 설명 |
|-|-|
| `WEBSITE_PUSH_ENABLED` | 읽기 전용입니다. 푸시 알림이 사용하도록 설정된 경우 추가됩니다. |
| `WEBSITE_PUSH_TAG_WHITELIST` | 읽기 전용입니다. 알림 등록에 태그를 포함합니다. |
| `WEBSITE_PUSH_TAGS_REQUIRING_AUTH` | 읽기 전용입니다. 사용자 인증이 필요한 알림 등록의 태그 목록을 포함합니다. |
| `WEBSITE_PUSH_TAGS_DYNAMIC` | 읽기 전용입니다. 자동으로 추가된 알림 등록의 태그 목록을 포함합니다. | 

>[!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *허용 목록* 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

<!-- 
## WellKnownAppSettings

WEBSITE_ALWAYS_PERFORM_PRELOAD
| WEBSITE_DISABLE_PRIMARY_VOLUMES | Set to `true` to disable the primary storage volume for that app. The default is `false`. |
| WEBSITE_DISABLE_STANDBY_VOLUMES | Set to `true` to disable the stand-by storage volume for that app. The default is `false`. This setting has no effect if `WEBSITE_DISABLE_PRIMARY_VOLUMES` is `true`. |
WEBSITE_FAILOVER_ONLY_ON_SBX_NW_FAILURES
WEBSITE_ENABLE_SYSTEM_LOG
WEBSITE_FRAMEWORK_JIT
WEBSITE_ADMIN_SITEID
WEBSITE_STAMP_DEPLOYMENT_ID
| WEBSITE_DEPLOYMENT_ID | Read-only. internal ID of deployment slot |
| WEBSITE_DISABLE_MSI | deprecated |
WEBSITE_VNET_BLOCK_FOR_SETUP_MAIN_SITE
WEBSITE_VNET_BLOCK_FOR_SETUP_SCM_SITE

 -->

## <a name="webjobs"></a>Webjobs

다음 환경 변수는 [WebJobs](webjobs-create.md)와 관련되어 있습니다.

| 설정 이름| 설명 |
|-|-|
| `WEBJOBS_RESTART_TIME`|연속 작업의 경우, 작업을 다시 시작하기 전에 어떤 이유로든 작업 프로세스가 중단되면 몇 초 단위로 지연합니다. |
| `WEBJOBS_IDLE_TIMEOUT`| 트리거된 작업의 경우, 시간 제한(초)으로, 유휴 상태이면 작업이 중단되고 CPU 시간 또는 출력이 없습니다. |
| `WEBJOBS_HISTORY_SIZE`| 트리거된 작업의 경우, 작업당 기록 디렉터리에 유지되는 최대 실행 수. 기본값은 `50`입니다. |
| `WEBJOBS_STOPPED`| 모든 작업 실행을 사용하지 않도록 설정하고 현재 실행 중인 모든 작업을 중지하려면 `1`로 설정합니다. |
| `WEBJOBS_DISABLE_SCHEDULE`| `1`로 설정하여 예약된 모든 트리거를 해제합니다. 작업은 여전히 수동으로 호출할 수 있습니다. |
| `WEBJOBS_ROOT_PATH`| webjob 파일의 절대 또는 상대 경로. 상대 경로의 경우, 값은 기본 루트 경로(`D:/home/site/wwwroot/` 또는 `/home/site/wwwroot/`)와 결합됩니다. |
| `WEBJOBS_LOG_TRIGGERED_JOBS_TO_APP_LOGS`| 트리거된 WebJobs의 출력을 애플리케이션 로그 파이프라인(파일 시스템, Blob, 테이블 지원)으로 보내려면 true로 설정합니다. |
| `WEBJOBS_SHUTDOWN_FILE` | 종료 요청이 검색될 때 App Service가 생성하는 파일입니다. 이 파일의 존재를 감지하고 종료를 시작하는 것은 웹 작업 프로세스의 책임입니다. WebJobs SDK를 사용하는 경우, 이 부분은 자동으로 처리됩니다. |
| `WEBJOBS_PATH` | 읽기 전용입니다. 현재 실행 중인 작업의 루트 경로(일부 임시 디렉터리 아래). |
| `WEBJOBS_NAME` | 읽기 전용입니다. 현재 작업 이름. |
| `WEBJOBS_TYPE` | 읽기 전용입니다. 현재 작업 유형(`triggered` 또는 `continuous`). |
| `WEBJOBS_DATA_PATH` | 읽기 전용입니다. 작업의 로그, 기록 아티팩트까지 포함할 현재 작업 메타데이터 경로. |
| `WEBJOBS_RUN_ID` | 읽기 전용입니다. 트리거된 작업의 경우, 작업의 현재 실행 ID. |

## <a name="functions"></a>Functions

| 설정 이름 | 설명 |
|-|-|
| `WEBSITE_FUNCTIONS_ARMCACHE_ENABLED` | 함수 캐시를 사용하지 않으려면 `0`로 설정합니다. |
| `WEBSITE_MAX_DYNAMIC_APPLICATION_SCALE_OUT` | [Azure Functions에 대한 앱 설정 참조](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_EXTENSION_VERSION` | [Azure Functions에 대한 앱 설정 참조](../azure-functions/functions-app-settings.md) |
`AzureWebJobsSecretStorageType` | [Azure Functions에 대한 앱 설정 참조](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_WORKER_RUNTIME` | [Azure Functions에 대한 앱 설정 참조](../azure-functions/functions-app-settings.md) |
| `AzureWebJobsStorage` | [Azure Functions에 대한 앱 설정 참조](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | [Azure Functions에 대한 앱 설정 참조](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTSHARE` | [Azure Functions에 대한 앱 설정 참조](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTOVERVNET` | [Azure Functions에 대한 앱 설정 참조](../azure-functions/functions-app-settings.md) |
| `WEBSITE_ENABLE_BROTLI_ENCODING` | [Azure Functions에 대한 앱 설정 참조](../azure-functions/functions-app-settings.md) |
| `WEBSITE_USE_PLACEHOLDER` | 사용 계획에서 자리 표시자 함수 최적화를 사용하지 않도록 설정하려면 `0`로 설정합니다. 자리 표시자는 [콜드 부팅을 개선하는](../azure-functions/functions-scale.md#cold-start-behavior) 최적화입니다. |
| `WEBSITE_PLACEHOLDER_MODE` | 읽기 전용입니다. 함수 앱이 자리 표시자 호스트(`generalized`) 또는 자체 호스트(`specialized`)에서 실행되고 있는지 여부를 표시합니다. |
| `WEBSITE_DISABLE_ZIP_CACHE` | 앱이 [ZIP 패키지](deploy-run-package.md)(`WEBSITE_RUN_FROM_PACKAGE=1`)에서 실행되면, 가장 최근에 배포된 5개의 ZIP 패키지가 앱의 파일 시스템(D:\home\data\SitePackages)에 캐싱됩니다. 이 캐시를 사용하지 않으려면 이 변수를 `1`로 설정합니다. Linux 사용 앱의 경우, ZIP 패키지 캐시는 기본적으로 사용하지 않도록 설정됩니다. |
<!--
| `FUNCTIONS_RUNTIME_SCALE_MONITORING_ENABLED` | TODO |
| `WEBSITE_SKIP_FUNCTION_APP_WARMUP` | Apps can use appsetting to opt out of warmup. Restricted to linux only since this is primarily for static sites that use Linux dynamic function apps. Linux dynamic sites are used as placeholder sites for static sites. Function apps don't get specialized until static sites are deployed. This allows function apps used by static sites to skip warmup and using up containers before any content is deployed. TODO |
 WEBSITE_IDLE_TIMEOUT_IN_MINUTES | removed WEBSITE_IDLE_TIMEOUT_IN_MINUTES because they aren't used in Linux Consumption.???
| `WEBSITE_DISABLE_FUNCTIONS_STARTUPCONTEXT_CACHE`| This env var can be set to 1 by users in order to avoid using the Functions StartupContext Cache feature. |
| `WEBSITE_CONTAINER_READY` | The env var is set to '1' to indicate to the Functions Runtime that it can proceed with initializing/specializing 
        // itself. For placeholders, it is set once specialization is complete on DWAS side and detours are reinitialized. For 
        // non-placeholder function apps, it is simply set to 1 when the process is started, because detours are initialized 
        // as part of starting the process (when PicoHelper.dll is loaded, well before any managed code is running).
        // NOTE: This is set on all sites, irrespective of whether it is a Functions site, because the EnvSettings module depends 
        // upon it to decide when to inject the app-settings.|
| `WEBSITE_PLACEHOLDER_PING_PATH` | This env var can be used to set a special warmup ping path on placeholder template sites. |
| ` WEBSITE_PLACEHOLDER_DISABLE_AUTOSPECIALIZATION` | This env var can be used to disabe specialization from being enabled automatically for a given placeholder template site. |
| `WEBSITE_FUNCTIONS_STARTUPCONTEXT_CACHE` | This env var is set only during specialization of a placeholder, to indicate to the Functions Runtime that
        // some function-app related data needed at startup, like secrets, are available in a file at the path specified
        // by this env var. |
WEBSITE_ENABLE_COLD_START_PROFILING | This env var can be set to 1 by internal SLA sites in order to trigger collection of perf profiles, if feature is enabled on the stamp. |
WEBSITE_CURRENT_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_HOME_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_ELASTIC_SCALING_ENABLED
WEBSITE_FILECHANGEAUDIT_ENABLED
| `WEBSITE_HTTPSCALEV2_ENABLED` | This is the default behavior for both v1 and v2 Azure Functions apps. | 
WEBSITE_CHANGEANALYSISSCAN_ENABLED
WEBSITE_DISABLE_CHILD_SPECIALIZATION
 -->

<!-- 
## Server variables
|HTTP_HOST| |
|HTTP_DISGUISED_HOST|the runtime site name for inbound requests.|
HTTP_CACHE_CONTROL
HTTP_X_SITE_DEPLOYMENT_ID
HTTP_WAS_DEFAULT_HOSTNAME
HTTP_X_ORIGINAL_URL
HTTP_X_FORWARDED_FOR
HTTP_X_ARR_SSL
HTTP_X_FORWARDED_PROTO
HTTP_X_APPSERVICE_PROTO
HTTP_X_FORWARDED_TLSVERSION
X-WAWS-Unencoded-URL
CLIENT-IP
X-ARR-LOG-ID
DISGUISED-HOST
X-SITE-DEPLOYMENT-ID
WAS-DEFAULT-HOSTNAME
X-Original-URL
X-MS-CLIENT-PRINCIPAL-NAME
X-MS-CLIENT-DISPLAY-NAME
X-Forwarded-For
X-ARR-SSL
X-Forwarded-Proto
X-AppService-Proto
X-Forwarded-TlsVersion
URL
HTTP_CLIENT_IP
APP_WARMING_UP |Regular/external requests made while warmup is in progress will have a APP_WARMING_UP server variable set to 1|
HTTP_COOKIE
SERVER_NAME
HTTP_X_FORWARDED_HOST
| HTTP_X_AZURE_FDID | Azure Front Door ID. See [](../frontdoor/front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
HTTP_X_FD_HEALTHPROBE
|WEBSITE_LOCALCACHE_ENABLED | shows up in w3wp.exe worker process |
HTTP_X_ARR_LOG_ID
| SCM_BASIC_AUTH_ALLOWED | set to "false" or "0" to disable basic authentication |
HTTP_X_MS_WAWS_JWT
HTTP_MWH_SecurityToken
LB_ALGO_FOR_HOSTING_SERVER_PROVIDER
ENABLE_CLIENT_AFFINITY
HTTP_X_MS_FROM_GEOMASTER
HTTP_X_MS_USE_GEOMASTER_CERT
HTTP_X_MS_STAMP_TOKEN
HTTPSCALE_REQUEST_ID
HTTPSCALE_FORWARD_FRONTEND_KEY
HTTPSCALE_FORWARD_REQUEST
IS_VALID_STAMP_TOKEN
NEEDS_SITE_RESTRICTED_TOKEN
HTTP_X_MS_PRIVATELINK_ID
  -->