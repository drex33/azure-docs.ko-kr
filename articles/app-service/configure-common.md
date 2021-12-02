---
title: 포털에서 앱 구성
description: Azure Portal에서 App Service 앱에 대한 일반 설정을 구성하는 방법을 알아봅니다. 앱 설정, 앱 구성, 연결 문자열, 플랫폼, 언어 스택, 컨테이너 등.
keywords: azure app service, 웹앱, 앱 설정, 환경 변수
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 12/07/2020
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: f1860e3b239742edf6a9d1154ec64530ba1664ce
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133439725"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Azure Portal에서 App Service 앱 구성

이 문서에서는 [Azure Portal]을 사용하여 웹 앱, 모바일 백 엔드 또는 API 앱에 대한 일반 설정을 구성하는 방법을 설명합니다.

## <a name="configure-app-settings"></a>앱 설정 구성

App Service에서 앱 설정은 애플리케이션 코드에 환경 변수로 전달되는 변수입니다. Linux 앱 및 사용자 지정 컨테이너의 경우, App Service는 `--env` 플래그를 사용하여 컨테이너에 앱 설정을 전달하고 컨테이너에서 환경 변수를 설정합니다. 두 경우 모두 앱을 시작할 때 앱 환경에 삽입됩니다. 앱 설정을 추가, 제거 또는 편집하는 경우 App Service는 앱 다시 시작을 트리거합니다. 앱 설정 이름에는 마침표 ()를 사용할 수 없습니다 `.` . 앱 설정에 마침표가 포함 된 경우 마침표는 컨테이너의 밑줄로 바뀝니다.

[Azure Portal]에서 **App Services** 를 검색하여 선택한 다음, 앱을 선택합니다. 

![App Services 검색](./media/configure-common/search-for-app-services.png)

앱의 왼쪽 메뉴에서 **구성** > **애플리케이션 설정** 을 선택합니다.

![애플리케이션 설정](./media/configure-common/open-ui.png)

ASP.NET 및 ASP.NET Core 개발자의 경우, App Service에서 앱 설정을 설정하는 것은 *Web.config* 또는 *appsettings.json* 의 `<appSettings>`에서 설정하는 것과 같지만 App Service의 값이 *Web.config* 또는 *appsettings.json* 의 값을 재정의합니다. *Web.config* 또는 *appsettings.json* 에서 개발 설정(예: 로컬 MySQL 암호) 및 프로덕션 비밀(예: Azure MySQL 데이터베이스 암호)을 App Service 내에 안전하게 보관할 수 있습니다. 로컬에서 디버그할 때 동일한 코드가 개발 설정을 사용하고 Azure에 배포될 때 프로덕션 비밀을 사용합니다.

마찬가지로 다른 언어 스택은 런타임에 환경 변수로 앱 설정을 가져옵니다. 언어 스택 관련 단계는 다음을 참조하세요.

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.JS](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [사용자 지정 컨테이너](configure-custom-container.md#configure-environment-variables)

앱 설정은 저장될 때 항상 암호화됩니다(미사용 암호화).

> [!NOTE]
> [Key Vault 참조](app-service-key-vault-references.md)를 사용하여 [Key Vault](../key-vault/index.yml)에서 앱 설정을 해결할 수 있습니다.

### <a name="show-hidden-values"></a>숨겨진 값 표시

기본값으로 앱 설정의 값은 보안을 위해 포털에서 숨겨집니다. 앱 설정의 숨겨진 값을 보려면 해당 설정의 **값** 필드를 클릭합니다. 모든 앱 설정의 값을 보려면 **값 표시** 단추를 클릭합니다.

### <a name="add-or-edit"></a>추가 또는 편집

새 앱 설정을 추가하려면 **새 애플리케이션 설정** 을 클릭합니다. 대화 상자에서 [설정을 현재 슬롯에 고정](deploy-staging-slots.md#which-settings-are-swapped)할 수 있습니다.

설정을 편집하려면 오른쪽에 있는 **편집** 단추를 클릭합니다.

완료되면 **업데이트** 를 클릭합니다. **구성** 페이지에서 다시 **저장** 을 클릭해야 합니다.

> [!NOTE]
> 기본 Linux 앱 서비스 또는 사용자 지정 Linux 컨테이너에서, `ApplicationInsights:InstrumentationKey`과 같은 앱 설정 이름의 중첩된 JSON 키 구조체는 App Service에서 키 이름으로 `ApplicationInsights__InstrumentationKey`로 구성해야 합니다. 즉, 모든 `:`을 `__`로(밑줄 두개)로 대체해야 합니다.
>

### <a name="edit-in-bulk"></a>대량 편집

앱 설정을 대량으로 추가하거나 편집하려면 **고급 편집** 단추를 클릭합니다. 완료되면 **업데이트** 를 클릭합니다. **구성** 페이지에서 다시 **저장** 을 클릭해야 합니다.

앱 설정에는 다음과 같은 JSON 형식이 있습니다.

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

### <a name="automate-app-settings-with-the-azure-cli"></a>Azure CLI를 통한 앱 설정 자동화

Azure CLI를 사용하여 명령줄에서 설정을 만들고 관리할 수 있습니다.

- [az webapp config app settings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set)를 사용하여 설정에 값을 할당합니다.

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings <setting-name>="<value>"
    ```
        
    `<setting-name>`을 설정 이름으로 바꾸고 `<value>`를 할당할 값으로 대체합니다. 이 명령은 설정이 없는 경우 설정을 만듭니다.
    
- [az webapp config appsettings list](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_list)를 통해 모든 설정 및 해당 값을 표시합니다.
    
    ```azurecli-interactive
    az webapp config appsettings list --name <app-name> --resource-group <resource-group-name>
    ```
    
- [az webapp config app settings delete](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_delete)를 통해 하나 이상의 설정을 제거합니다.

    ```azurecli-interactive
    az webapp config appsettings delete --name <app-name> --resource-group <resource-group-name> --setting-names {<names>}
    ```
    
    `<names>`을 공백으로 구분된 설정 이름 목록으로 대체합니다.

## <a name="configure-connection-strings"></a>연결 문자열 구성

[Azure Portal]에서 **App Services** 를 검색하여 선택한 다음, 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성** > **애플리케이션 설정** 을 선택합니다.

![애플리케이션 설정](./media/configure-common/open-ui.png)

ASP.NET 및 ASP.NET Core 개발자의 경우, App Service에서 연결 문자열을 설정하는 것은 *Web.config* 의 `<connectionStrings>`에서 설정하는 것과 같지만, App Service에서 설정한 값은 *Web.config* 값을 재정의합니다. *Web.config* 에서 개발 설정(예: 데이터베이스 파일)을 App Service 내에 안전하게 보관하고 프로덕션 비밀(예: SQL Database 자격 증명)을 App Service 내에 안전하게 보관할 수 있습니다. 로컬에서 디버그할 때 동일한 코드가 개발 설정을 사용하고 Azure에 배포될 때 프로덕션 비밀을 사용합니다.

다른 언어 스택의 경우, 연결 문자열이 값에 액세스하기 위해 변수 키에 특별한 서식이 필요하기 때문에 [앱 설정](#configure-app-settings)을 사용하는 것이 좋습니다. 

> [!NOTE]
> 비 .NET 언어에 대한 앱 설정 대신 연결 문자열을 사용하는 경우는 다음 한 가지입니다. 특정 Azure 데이터베이스 유형은 App Service 앱에서 데이터베이스에 대한 연결 문자열을 구성하는 _경우에만_ 앱과 함께 백업됩니다. 자세한 내용은 [백업되는 항목](manage-backup.md#what-gets-backed-up)을 참조하세요. 이 자동화된 백업이 필요하지 않은 경우 앱 설정을 사용합니다.

런타임에 연결 문자열은 환경 변수로 사용할 수 있으며, 다음 연결 형식이 접두사로 사용됩니다.

* SQLServer: `SQLCONNSTR_`  
* MySQL: `MYSQLCONNSTR_` 
* SQLAzure: `SQLAZURECONNSTR_` 
* 사용자 지정: `CUSTOMCONNSTR_`
* PostgreSQL: `POSTGRESQLCONNSTR_`  

예를 들어 MySql 연결 문자열 이름이 *connectionstring1* 로 지정된 경우 환경 변수 `MYSQLCONNSTR_connectionString1`을 통해 액세스될 수 있습니다. 언어 스택 관련 단계는 다음을 참조하세요.

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.JS](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [사용자 지정 컨테이너](configure-custom-container.md#configure-environment-variables)

연결 문자열은 저장될 때 항상 암호화됩니다(미사용 암호화).

> [!NOTE]
> [Key Vault 참조](app-service-key-vault-references.md)를 사용하여 [Key Vault](../key-vault/index.yml)에서 연결 문자열을 해결할 수 있습니다.

### <a name="show-hidden-values"></a>숨겨진 값 표시

기본값으로 연결 문자열의 값은 보안을 위해 포털에서 숨겨집니다. 연결 문자열의 숨겨진 값을 보려면 해당 문자열의 **값** 필드를 클릭하면 됩니다. 모든 연결 문자열의 값을 보려면 **값 표시** 단추를 클릭합니다.

### <a name="add-or-edit"></a>추가 또는 편집

새 연결 문자열을 추가하려면 **새 연결 문자열** 을 클릭합니다. 대화 상자에서 [연결 문자열을 현재 슬롯에 고정](deploy-staging-slots.md#which-settings-are-swapped)할 수 있습니다.

설정을 편집하려면 오른쪽에 있는 **편집** 단추를 클릭합니다.

완료되면 **업데이트** 를 클릭합니다. **구성** 페이지에서 다시 **저장** 을 클릭해야 합니다.

### <a name="edit-in-bulk"></a>대량 편집

연결 문자열을 대량으로 추가하거나 편집하려면 **고급 편집** 단추를 클릭합니다. 완료되면 **업데이트** 를 클릭합니다. **구성** 페이지에서 다시 **저장** 을 클릭해야 합니다.

연결 문자열의 JSON 형식은 다음과 같습니다.

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>일반 설정 구성

[Azure Portal]에서 **App Services** 를 검색하여 선택한 다음, 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성** > **일반 설정** 을 선택합니다.

![일반 설정](./media/configure-common/open-general.png)

여기에서 앱에 대한 몇 가지 일반 설정을 구성할 수 있습니다. 일부 설정에서는 [더 높은 가격 책정 계층으로 스케일 업](manage-scale-up.md)해야 합니다.

- **스택 설정**: 언어 및 SDK 버전을 포함하여 앱을 실행할 소프트웨어 스택입니다.

    Linux 앱 및 사용자 지정 컨테이너의 경우 언어 런타임 버전을 선택하고 선택적 시작 명령 또는 **시작 명령** 파일을 설정할 수 있습니다.

    ![Linux 컨테이너에 대한 일반 설정](./media/configure-common/open-general-linux.png)

- **플랫폼 설정**: 다음을 포함하여 호스팅 플랫폼에 대한 설정을 구성할 수 있습니다.
    - **비트 수**: 32비트 또는 64비트. (포털에서 만든 App Service의 기본값은 32 비트.)
    - **WebSocket 프로토콜**: 예를 들어 [ASP.NET SignalR] 또는 [socket.io](https://socket.io/)입니다.
    - **Always On**: 트래픽이 없는 경우에도 앱을 로드 상태로 유지합니다. **Always On** 를 설정하지 않으면 (기본값) 들어오는 요청이 없이 20 분 후에 앱이 언로드됩니다. 언로드된 앱은 준비 시간 때문에 새 요청에 대해 대기 시간이 길어질 수 있습니다. **Always On** 설정되면 프런트 엔드 부하 분산 장치는 5분 마다 애플리케이션 루트에 GET 요청을 보냅니다. 연속 ping은 앱이 언로드될 수 없도록 방지합니다.
    
        Always On은 연속 WebJobs 또는 CRON 언어 식을 사용하여 트리거되는 WebJobs에 필요합니다.
    - **관리형 파이프라인 버전**: IIS [파이프라인 모드]입니다. 이전 버전의 IIS가 필요한 레거시 앱이 있는 경우 **클래식** 으로 설정합니다.
    - **HTTP 버전**: [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 프로토콜에 대한 지원을 사용하도록 **2.0** 으로 설정합니다.
    > [!NOTE]
    > 대부분의 최신 브라우저는 TLS를 통해서만 HTTP/2 프로토콜을 지원하는 반면에 암호화되지 않은 트래픽은 HTTP/1.1을 계속 사용합니다. 클라이언트 브라우저가 HTTP/2를 사용하여 앱에 연결되도록 하려면 사용자 지정 DNS 이름을 보호합니다. 자세한 내용은 [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)를 참조하세요.
    - **ARR 선호도**: 다중 인스턴스 배포에서 클라이언트가 세션 수명 동안 동일한 인스턴스로 라우팅되는지 확인합니다. 상태 비저장 애플리케이션의 경우 이 옵션을 **Off** 로 설정할 수 있습니다.
- **디버깅**: [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure), [Node.js](configure-language-nodejs.md#debug-remotely) 앱에 대해 원격 디버깅을 사용하도록 설정합니다. 이 옵션은 48시간 후에 자동으로 해제됩니다.
- **수신 클라이언트 인증서**: [상호 인증](app-service-web-configure-tls-mutual-auth.md)에 클라이언트 인증서가 필요합니다.

## <a name="configure-default-documents"></a>기본 문서 구성

이 설정은 Windows 앱에 대해서만 지원됩니다.

[Azure Portal]에서 **App Services** 를 검색하여 선택한 다음, 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성** > **기본 문서** 를 선택합니다.

![기본 문서](./media/configure-common/open-documents.png)

기본 문서는 웹 사이트의 루트 URL에 표시되는 웹 페이지입니다. 목록에서 첫 번째로 일치되는 파일이 사용됩니다. 새 기본 문서를 추가하려면 **새 문서** 를 클릭합니다. 반드시 **저장** 을 클릭하세요.

앱에서 정적 콘텐츠를 제공하는 대신 URL을 기반으로 경로를 사용하는 모듈을 사용하는 경우 기본 문서가 필요하지 않습니다.

## <a name="configure-path-mappings"></a>경로 매핑 구성

[Azure Portal]에서 **App Services** 를 검색하여 선택한 다음, 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성** > **경로 매핑** 을 선택합니다.

![경로 매핑](./media/configure-common/open-path.png)

> [!NOTE] 
> **경로 매핑** 탭에는 여기에 표시된 예시와 다른 OS별 설정이 표시될 수 있습니다.

### <a name="windows-apps-uncontainerized"></a>Windows 앱(컨테이너화되지 않은 경우)

Windows 앱의 경우 IIS 처리기 매핑과 가상 애플리케이션 및 디렉터리를 사용자 지정할 수 있습니다.

처리기 매핑을 사용하면 특정 파일 확장명에 대한 요청을 처리하는 사용자 지정 스크립트 프로세서를 추가할 수 있습니다. 사용자 지정 처리기를 추가하려면 **새 처리기 매핑** 을 클릭합니다. 다음과 같이 처리기를 구성합니다.

- **확장명**. 처리할 파일 확장명입니다(예: *\*.php* 또는 *handler.fcgi*).
- **스크립트 프로세서**. 스크립트 프로세서의 절대 경로입니다. 파일 확장명과 일치하는 파일에 대한 요청이 스크립트 프로세서에서 처리됩니다. 경로 `D:\home\site\wwwroot` 를 사용하여 앱의 루트 디렉터리를 참조합니다.
- **인수**. 스크립트 프로세서에 대한 선택적 명령줄 인수입니다.

각 앱에는 `D:\home\site\wwwroot`에 매핑되는 기본 루트 경로(`/`)가 있습니다. 여기서 코드는 기본값으로 배포됩니다. 앱 루트가 다른 폴더에 있거나 리포지토리에 둘 이상의 애플리케이션이 있는 경우, 여기에서 가상 애플리케이션 및 디렉터리를 편집하거나 추가할 수 있습니다. 

**경로 매핑** 탭에서 **새 가상 애플리케이션 또는 디렉터리** 를 클릭합니다. 

- 가상 디렉터리를 실제 경로에 매핑하려면 **디렉터리** 확인란을 선택된 상태로 둡니다. 가상 디렉터리 및 웹 사이트 루트(`D:\home`)에 대한 해당 상대(실제) 경로를 지정합니다.
- 가상 디렉터리를 웹 애플리케이션으로 표시하려면 **디렉터리** 확인란의 선택을 취소합니다.
  
  ![디렉터리 확인란](./media/configure-common/directory-check-box.png)

### <a name="containerized-apps"></a>컨테이너화된 앱

[컨테이너화된 앱에 대한 사용자 지정 스토리지를 추가](configure-connect-to-azure-storage.md)할 수 있습니다. 컨테이너화된 앱에는 모든 Linux 앱 및 App Service에서 실행되는 Windows 및 Linux 사용자 지정 컨테이너도 포함됩니다. **새 Azure Storage 탑재** 를 클릭하고 다음과 같이 사용자 지정 스토리지를 구성합니다.

- **이름**: 표시 이름.
- **구성 옵션**: **기본** 또는 **고급**.
- **스토리지 계정**: 원하는 컨테이너가 있는 스토리지 계정입니다.
- **스토리지 유형**: **Azure Blob** 또는 **Azure Files**.
  > [!NOTE]
  > Windows 사용자 지정 컨테이너는 Azure Files 지원합니다.
- **스토리지 컨테이너**: 기본 구성의 경우에 원하는 컨테이너입니다.
- **공유 이름**: 고급 구성의 경우 파일 공유 이름입니다.
- **액세스 키**: 고급 구성의 경우 액세스 키입니다.
- **탑재 경로**: 사용자 지정 스토리지를 탑재할 컨테이너의 절대 경로입니다.

자세한 내용은 [App Service의 컨테이너에서 네트워크 공유로 Azure Storage에 액세스](configure-connect-to-azure-storage.md)를 참조하세요.

## <a name="configure-language-stack-settings"></a>언어 스택 설정 구성

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.JS](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>사용자 지정 컨테이너 구성

[Azure App Service에 대한 사용자 지정 컨테이너 구성](configure-custom-container.md) 참조

## <a name="next-steps"></a>다음 단계

- [환경 변수 및 앱 설정 참조](reference-app-settings.md)
- [Azure App Service에서 사용자 지정 도메인 이름 구성]
- [Azure App Service에서 스테이징 환경 설정]
- [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
- [진단 로그 활성화](troubleshoot-diagnostic-logs.md)
- [Azure App Service에서 앱 크기 조정]
- [Azure App Service의 기본 사항 모니터링]
- [applicationHost.xdt를 통한 applicationHost.config 설정 변경](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Azure App Service에서 사용자 지정 도메인 이름 구성]: ./app-service-web-tutorial-custom-domain.md
[Azure App Service에서 스테이징 환경 설정]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: ./web-sites-monitor.md
[Azure App Service의 기본 사항 모니터링]: ./web-sites-monitor.md
[파이프라인 모드]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Azure App Service에서 앱 크기 조정]: ./manage-scale-up.md
