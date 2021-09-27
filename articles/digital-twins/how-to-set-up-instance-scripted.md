---
title: 인스턴스 및 인증 설정(스크립팅됨)
titleSuffix: Azure Digital Twins
description: 자동화된 배포 스크립트를 실행하여 Azure Digital Twins 서비스의 인스턴스를 설정하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 9/3/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: f7fdcf7a1f0ad3c4f9a4a715f14a8da55f2d9904
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124826892"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Azure Digital Twins 인스턴스 및 인증 설정(스크립팅됨)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정하는 등 **새 Azure Digital Twins 인스턴스를 설정** 하는 단계에 대해 설명합니다. 이 문서를 완료한 후에는 Azure Digital Twins 인스턴스에 대한 프로그래밍을 시작할 수 있습니다.

이 버전의 이 문서에서는 프로세스를 간소화하는 [자동화된 배포 스크립트 샘플](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)을 실행하여 이러한 단계를 완료합니다. 
* 백그라운드에서 스크립트를 실행하는 수동 CLI 단계를 보려면 [인스턴스 및 인증 설정(CLI)](how-to-set-up-instance-cli.md)에서 이 문서의 CLI 버전을 참조하세요.
* Azure Portal에 따라 수동 단계를 보려면 [인스턴스 및 인증 설정(포털)](how-to-set-up-instance-portal.md)에서 이 문서의 포털을 참조하세요.

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

## <a name="prerequisites-permission-requirements"></a>필수 조건: 권한 요구 사항
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>필수 구성 요소: 스크립트 다운로드

샘플 스크립트는 PowerShell로 작성됩니다. 이 샘플은 샘플 링크로 이동하고 제목 아래에 있는 코드 찾아보기 단추를 선택하여 머신에 다운로드할 수 있는 *[Azure Digital Twins 엔드투엔드 샘플](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)* 의 일부입니다. 그러면 *코드* 단추와 *ZIP 다운로드* 를 선택하여 .zip으로 다운로드할 수 있는 샘플용 GitHub 리포지토리로 이동합니다.

:::image type="content" source="media/includes/download-repo-zip.png" alt-text="GitHub에 대한 digital-twins-samples 리포지토리의 스크린샷은 zip으로 다운로드하는 단계를 강조 표시합니다." lightbox="media/includes/download-repo-zip.png":::

그러면 .zip 폴더가 **digital-twins-samples-master.zip** 으로 머신에 다운로드됩니다. 머신의 폴더로 이동하고 파일의 압축을 풀어 파일을 추출합니다.

압축을 푼 폴더에서 배포 스크립트는 _digital-twins-samples-master > scripts > **deploy.ps1**_ 에 위치합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>배포 스크립트 실행

이 문서에서는 Azure Digital Twins 코드 샘플을 사용하여 Azure Digital Twins 인스턴스 및 필요한 인증을 반 자동으로 배포합니다. 또한 스크립팅된 상호 작용을 직접 작성하기 위한 시작 지점으로 사용할 수 있습니다.

Cloud Shell에서 배포 스크립트를 실행하는 단계는 다음과 같습니다.
1. 브라우저에서 [Azure Cloud Shell](https://shell.azure.com/) 창으로 이동합니다. 이 명령을 사용하여 로그인합니다.
    ```azurecli-interactive
    az login
    ```
    CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다. 그렇지 않으면 *https://aka.ms/devicelogin* 에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다.
 
2. Cloud Shell 아이콘 모음에서 Cloud Shell이 PowerShell 버전을 실행하도록 설정되었는지 확인합니다.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="PowerShell 버전 선택을 보여주는 Azure Portal의 Cloud Shell 창 스크린샷.":::

1. "파일 업로드/다운로드" 아이콘을 선택하고, "업로드"를 선택합니다.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="업로드 아이콘 선택을 보여주는 Azure Portal의 Cloud Shell 창 스크린샷.":::

    머신에서 _**deploy.ps1**_ 파일로 이동하고(_digital-twins-samples-master > scripts > **deploy.ps1**_ 에서) "열기"를 선택합니다. 그러면 Cloud Shell 창에서 실행할 수 있도록 Cloud Shell에 파일이 업로드됩니다.

4. Cloud Shell 창에서 `./deploy.ps1` 명령을 전송하여 스크립트를 실행합니다. 아래 명령을 복사할 수 있습니다.(Cloud Shell에 붙여 넣으려면 Windows 및 Linux에서는 **Ctrl+Shift+V** 를 사용하거나 macOS에서는 **Cmd+Shift+V** 를 사용할 수 있습니다. 오른쪽 클릭 메뉴를 사용할 수도 있습니다.)

    ```azurecli-interactive
    ./deploy.ps1
    ```

    이 스크립트는 Azure Digital Twins 인스턴스를 만들고 Azure 사용자에게 인스턴스의 *Azure Digital Twins 데이터 소유자* 역할을 할당합니다.

    스크립트가 자동화된 설치 단계를 실행하면 다음 값을 전달하라는 메시지가 표시됩니다.
    * 인스턴스의 경우: 사용할 Azure 구독의 *구독 ID*
    * 인스턴스의 경우: 인스턴스를 배포할 *위치* 입니다. Azure Digital Twins를 지원하는 지역을 확인하려면 [지역별 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 참조하세요.
    * 인스턴스의 경우: *리소스 그룹* 이름 기존 리소스 그룹을 사용하거나 새 이름을 입력하여 만들 수 있습니다.
    * 인스턴스의 경우: Azure Digital Twins 인스턴스의 *이름* 구독에 이미 지정된 이름을 사용하고 있는 지역에 다른 Azure Digital Twins 인스턴스가 있는 경우 다른 이름을 선택하라는 메시지가 표시됩니다.

다음은 스크립트에서 출력 로그를 발췌한 것입니다.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="배포 스크립트를 실행하여 입력 및 출력 로그를 보여주는 Azure Portal의 Cloud Shell 창 스크린샷." lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

스크립트가 성공적으로 완료되면 최종 인쇄물은 `Deployment completed successfully`로 표시됩니다. 그렇지 않으면 오류 메시지를 해결하고, 스크립트를 다시 실행합니다. 이미 완료한 단계를 무시하고 종료된 지점에서 입력 요청을 다시 시작합니다.

> [!NOTE]
> 이 스크립트는 현재 Azure Digital Twins(*Azure Digital Twins 데이터 소유자*) 내에서 Cloud Shell의 스크립트를 실행하는 동일한 사용자에게 필요한 관리 역할을 할당합니다. 인스턴스를 관리하는 다른 사람에게 이 역할을 할당해야 하는 경우 [Azure Portal 지침](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 또는 [CLI 지침](how-to-set-up-instance-cli.md#set-up-user-access-permissions)을 통해 이 작업을 수행할 수 있습니다.

>[!NOTE]
>현재 스크립팅된 설치와 관련하여 **알려진 문제** 가 있습니다. 일부 사용자(특히 개인 [MSA(Microsoft 계정](https://account.microsoft.com/account))의 사용자)가 **_Azure Digital Twins 데이터 소유자_ 에 대한 역할 할당이 생성되지 않은 것** 을 찾을 수 있습니다.
>
>이 문서의 뒷부분에 나오는 [사용자 역할 할당 확인](#verify-user-role-assignment) 섹션을 사용하여 역할 할당을 확인하고 필요한 경우 [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 또는 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)를 사용하여 역할 할당을 수동으로 설정할 수 있습니다.
>
>이 문제에 대한 자세한 내용은 [문제 해결: Azure Digital Twins의 알려진 문제](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup)를 참조하세요.

## <a name="verify-success-and-collect-important-values"></a>성공 확인 및 중요한 값 수집

스크립트에 의해 설정된 리소스 및 사용 권한 생성을 확인하려면 아래 지침을 사용하여 [Azure Portal](https://portal.azure.com)에서 해당 항목을 확인할 수 있습니다. 단계의 성공 여부를 확인할 수 없는 경우 단계를 다시 시도합니다. [Azure Portal](how-to-set-up-instance-portal.md) 또는 [CLI](how-to-set-up-instance-cli.md) 지침을 사용하여 단계를 개별적으로 수행할 수 있습니다.

또한 이 섹션에서는 Azure Digital Twins 인스턴스 작업을 계속할 때 필요할 수 있는 스크립트에 의해 설정된 리소스에서 중요한 값을 찾는 방법을 보여 줍니다. 이러한 값을 안전한 위치에 저장하거나 나중에 필요할 때 찾을 수 있도록 이 섹션으로 돌아옵니다. 다른 사용자가 인스턴스에 대한 프로그래밍을 수행하는 경우 다른 사용자와도 이 값을 공유해야 합니다.

### <a name="verify-instance"></a>인스턴스 확인

인스턴스가 만들어졌는지 확인하려면 Azure Portal의 [Azure Digital Twins 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)로 이동합니다. 포털 검색 창에서 *Azure Digital Twins* 를 검색하여 이 페이지로 직접 이동할 수 있습니다.

이 페이지에는 모든 Azure Digital Twins 인스턴스가 나열됩니다. 목록에서 새로 만든 인스턴스의 이름을 찾습니다.

확인에 실패한 경우 [포털](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) 또는 [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)를 사용하여 인스턴스 만들기를 다시 시도할 수 있습니다.

### <a name="collect-instance-values"></a>인스턴스 값 수집

[Azure Digital Twins 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)에서 인스턴스의 이름을 선택하여 인스턴스의 *개요* 페이지를 엽니다. 해당 *이름*, *리소스 그룹* 및 *호스트 이름* 을 확인합니다. 나중에 인스턴스를 식별하고 연결하는 데 필요할 수 있습니다.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Azure Digital Twins 인스턴스의 개요 페이지에서 중요한 값을 강조 표시하는 Azure Portal의 스크린샷.":::

### <a name="verify-user-role-assignment"></a>사용자 역할 할당 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> 스크립트는 현재 이 필수 역할을 Cloud Shell의 스크립트를 실행하는 동일한 사용자에게 할당합니다. 인스턴스를 관리하는 다른 사람에게 이 역할을 할당해야 하는 경우 Azure Portal([지침](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 또는 CLI([지침](how-to-set-up-instance-cli.md#set-up-user-access-permissions))를 통해 이 작업을 수행할 수 있습니다.

확인에 실패한 경우 [포털](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 또는 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)를 사용하여 자체 역할 할당을 다시 실행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins CLI 명령을 사용하여 인스턴스에서 개별 REST API 호출을 테스트합니다. 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Azure Digital Twins CLI 명령 집합](concepts-cli.md)

또는 인증 코드를 사용하여 인스턴스에 클라이언트 애플리케이션을 연결하는 방법을 참조하세요.
* [앱 인증 코드 쓰기](how-to-authenticate-client.md)