---
title: '빠른 시작: 정적 HTML 웹앱 만들기'
description: 몇 분 안에 첫 번째 HTML Hello World를 Azure App Service에 배포합니다. App Service에 배포하는 여러 가지 방법 중 하나인 Git를 사용하여 배포합니다.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c342a898a7ad9f0918313b2292a0105c75f6c8a
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535559"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Azure에서 정적 HTML 웹앱 만들기

이 빠른 시작에서는 기본적인 HTML+CSS 사이트를 다음에 배포하는 방법을 보여줍니다. <abbr title="웹 애플리케이션, REST API 및 모바일 백 엔드를 호스트하는 HTTP 기반 서비스입니다.">Azure App Service</abbr>. 여기에서는 [Cloud Shell](../cloud-shell/overview.md)을 사용해서 빠른 시작을 실행하지만 [Azure CLI](/cli/azure/install-azure-cli)를 사용하여 여기에서 설명하는 명령들을 로컬에서도 실행할 수도 있습니다.

## <a name="1-prepare-your-environment"></a>1. 환경 준비

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[Cloud Shell](../cloud-shell/overview.md)에서 quickstart 디렉터리를 만든 다음, 해당 디렉터리로 이동합니다.

```bash
mkdir quickstart

cd $HOME/quickstart
```

이어서 다음 명령을 실행하여 quickstart 디렉터리에 샘플 앱 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. 웹앱 만들기

샘플 코드가 들어있는 디렉토리로 이동한 후 `az webapp up` 명령을 실행합니다. `<app-name>`을 전역적으로 고유한 이름으로 **바꿉니다**.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>문제 해결</summary>
<ul>
<li><code>az</code> 명령이 인식되지 않는 경우 <a href="#1-prepare-your-environment">환경 준비</a>의 설명대로 Azure CLI가 설치되어 있는지 확인합니다.</li>
<li><code>&lt;app-name&gt;</code>을 모든 Azure에서 고유한 이름으로 바꿉니다(<em>유효한 문자는 <code>a-z</code>, <code>0-9</code> 및 <code>-</code></em>). 좋은 패턴은 회사 이름과 앱 식별자의 조합을 사용하는 것입니다.</li>
<li><code>--sku F1</code> 인수는 무료 가격 책정 계층에 웹앱을 만듭니다. 이 인수를 생략하여 더 빠른 프리미엄 계층을 사용합니다. 이 경우 시간당 비용이 발생합니다.</li>
<li><code>--html</code> 인수는 모든 폴더 콘텐츠를 정적 콘텐츠로 처리하고 빌드 자동화를 사용하지 않도록 설정하라는 의미입니다.</li>
<li>선택적으로 인수 <code>--location &lt;location-name&gt;</code>을 포함할 수 있습니다. 여기서 <code>&lt;location-name&gt;</code>은 사용 가능한 Azure 지역입니다. Azure 계정에 허용되는 지역 목록은 <a href="/cli/azure/appservice#az_appservice_list_locations"><code>az account list-locations</code></a> 명령을 실행하여 검색할 수 있습니다.</li>
</ul>
</details>

이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 

<details>
<summary><code>az webapp up</code>의 기능은 무엇인가요?</summary>
<p><code>az webapp up</code> 명령에는 다음 작업이 포함됩니다.</p>
<ul>
<li>기본 리소스 그룹 만들기</li>
<li>기본 App Service 계획을 만듭니다.</li>
<li>지정된 이름으로 <a href="/cli/azure/webapp#az_webapp_create">App Service 앱을 만듭니다</a>.</li>
<li>현재 작업 디렉터리에서 앱까지의 <a href="/azure/app-service/deploy-zip">배포 파일을 압축합니다</a>.</li>
<li>실행되는 동안 리소스 생성, 로깅 및 ZIP 배포에 대한 메시지가 제공됩니다.</li>
</ul>

완료되면 다음 예와 유사한 정보가 표시됩니다.

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

</details>

나중에 [리소스를 정리](#6-clean-up-resources)하려면 `resourceGroup` 값이 필요합니다.

<hr/>

## <a name="3-browse-to-the-app"></a>3. 앱으로 이동

브라우저에서 앱 URL(`http://<app_name>.azurewebsites.net`)로 이동합니다.

이 페이지는 Azure App Service 웹앱으로 실행됩니다.

![샘플 앱 홈 페이지](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. 앱 업데이트 및 다시 배포

Cloud Shell에서 `nano index.html`을 **입력** 하여 Nano 텍스트 편집기를 엽니다. 

`<h1>` 제목 태그에서 "Azure App Service - 샘플 정적 HTML 사이트"를 "Azure App Service"로 변경합니다.

![Nano index.html](media/quickstart-html/nano-index-html.png)

`^O` 명령을 사용하여 변경 내용을 **저장** 합니다.

`^X` 명령을 사용하여 nano를 **종료** 합니다.

`az webapp up` 명령을 사용하여 앱을 다시 배포합니다.

```bash
az webapp up --html
```

**앱으로 이동** 단계에서 연 브라우저 창으로 다시 전환합니다.

페이지를 **새로 고칩니다**.

![업데이트된 샘플 앱 홈 페이지](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. 새 Azure 앱 관리

[Azure Portal](https://portal.azure.com)로 **이동** 합니다. 

**App Services** 를 **검색** 하여 **선택** 합니다.

![Azure Portal에서 App Services 선택](./media/quickstart-html/portal0.png)

Azure 앱의 이름을 **선택** 합니다.

![Azure 앱에 대한 포털 탐색](./media/quickstart-html/portal1.png)

웹앱의 개요 페이지가 표시됩니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다.

![Azure Portal의 App Service 블레이드](./media/quickstart-html/portal2.png)

왼쪽 메뉴에는 앱을 구성할 수 있는 여러 페이지가 표시됩니다.

<hr/>

## <a name="6-clean-up-resources"></a>6. 리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것으로 생가 생각되는 경우 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다. 리소스 그룹 이름은 [웹앱 만들기](#2-create-a-web-app) 단계에서 자동으로 생성된 것입니다.

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

이 명령을 실행하는 데 1분 정도 걸릴 수 있습니다.

<hr/>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> 사용자 지정 도메인 매핑
