---
title: Visual Studio Code를 사용하여 JavaScript 함수 만들기 - Azure Functions
description: JavaScript 함수를 만든 다음, Visual Studio Code의 Azure Functions 확장을 사용하여 Azure Functions의 서버리스 호스팅에 로컬 Node.js 프로젝트를 게시하는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 11/18/2021
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-node_uiex
ms.custom: mode-other
ms.openlocfilehash: 9bd8a7b535346b9c94fb74aa020ce1d0208d5cf5
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133065093"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>빠른 시작: Visual Studio Code를 사용하여 Azure에서 JavaScript 함수 만들기

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Visual Studio Code를 사용하여 HTTP 요청에 응답하는 JavaScript 함수를 만듭니다. 코드를 로컬로 테스트한 다음, 서버리스 Azure Functions 환경에 배포합니다.

이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다.

이 문서의 [CLI 기반 버전](create-first-function-cli-node.md)도 있습니다.

## <a name="configure-your-environment"></a>환경 구성

시작하기 전에 다음 요구 사항을 갖추었는지 확인합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js 14.x](https://nodejs.org/en/download/releases/) 또는 [Node.js 16.x](https://nodejs.org/en/download/releases/)(미리 보기). `node --version` 명령을 사용하여 버전을 확인합니다.  

+ [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나인 [Visual Studio Code](https://code.visualstudio.com/).

+ Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

+ [Azure Functions Core Tools 4.x](functions-run-local.md#install-the-azure-functions-core-tools).

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>로컬 프로젝트 만들기

이 섹션에서는 Visual Studio Code를 사용하여 JavaScript에서 로컬 Azure Functions 프로젝트를 만듭니다. 이 문서의 뒷부분에서 함수 코드를 Azure에 게시합니다.

1. 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **새 프로젝트 만들기...** 아이콘을 선택합니다.

    ![새 프로젝트 만들기 선택](./media/functions-create-first-function-vs-code/create-new-project.png)

1. 프로젝트 작업 영역에 대한 디렉터리 위치를 선택하고 **선택** 을 선택합니다.

    > [!NOTE]
    > 다음 단계는 작업 영역 외부에서 완료하도록 설계되었습니다. 이 경우 작업 영역에 포함된 프로젝트 폴더를 선택하지 마십시오.

1. 프롬프트에서 다음 정보를 제공합니다.

    |prompt|선택 영역|
    |--|--|
    |**함수 프로젝트에 대한 언어 선택**|`JavaScript`을 선택합니다.|
    |**프로젝트의 첫 번째 함수에 사용할 템플릿 선택**|`HTTP trigger`을 선택합니다.|
    |**함수 이름 제공**|`HttpExample`.|
    |**권한 부여 수준**|누구나 함수 엔드포인트를 호출할 수 있도록 하는 `Anonymous`를 선택합니다. 권한 부여 수준에 대해 알아보려면 [권한 부여 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요.|
    |**프로젝트를 여는 방법 선택**|`Add to workspace`을 선택합니다.|

    Visual Studio Code는 이 정보를 사용하여 HTTP 트리거를 통해 Azure Functions 프로젝트를 생성합니다. 탐색기에서 로컬 프로젝트 파일을 볼 수 있습니다. 생성된 파일에 대한 자세한 내용은 [생성된 프로젝트 파일](functions-develop-vs-code.md#generated-project-files)을 참조하세요. 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

함수가 로컬 컴퓨터에서 제대로 실행되는지 확인한 후에 Visual Studio Code를 사용하여 프로젝트를 Azure에 직접 게시합니다.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

<a name="Publish the project to Azure"></a>

## <a name="deploy-the-project-to-azure"></a>Azure에 프로젝트 배포

이 섹션에서는 Azure 구독에서 함수 앱 및 관련 리소스를 만든 다음, 코드를 배포합니다. 

> [!IMPORTANT]
> 기존 함수 앱에 배포하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다. 


1. 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **함수 앱에 배포...** 단추를 선택합니다.

    ![Azure에 프로젝트 게시](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. 프롬프트에서 다음 정보를 제공합니다.

    |prompt| 선택 영역|
    |--|--|
    |**Azure에서 함수 앱 선택**|`+ Create new Function App`을 선택합니다. (이 문서에서 다루지 않는 `Advanced` 옵션은 선택하지 마세요.)|
    |**함수 앱에 대해 전역적으로 고유한 이름 입력**|URL 경로에 유효한 이름을 입력합니다. 입력한 이름이 Azure Functions에서 고유한지 확인하기 위해 유효성을 검사합니다.|
    |**런타임 선택**|로컬에서 실행 중인 Node.js 버전을 선택합니다. `node --version` 명령을 사용하여 버전을 확인할 수 있습니다.|
    |**새 리소스의 위치 선택**|성능을 향상시키려면 근처에 있는 [지역](https://azure.microsoft.com/regions/)을 선택합니다.|

    확장은 알림 영역에서 Azure에 생성되는 개별 리소스의 상태를 보여줍니다.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure 리소스 생성 알림":::

    완료되면 함수 앱 이름을 기반으로 하는 이름을 사용하여 구독에 다음 Azure 리소스가 생성됩니다.

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

1. 함수 앱을 만들고 배포 패키지가 적용되면 알림이 표시됩니다. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

1. 이 알림에서 **출력 보기** 를 선택하여 사용자가 만든 Azure 리소스를 포함한 만들기 및 배포 결과를 표시합니다. 알림이 누락된 경우 오른쪽 아래 모서리에 있는 종 모양 아이콘을 선택하여 다시 확인합니다.

    ![전체 알림 만들기](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

## <a name="change-the-code-and-redeploy-to-azure"></a>코드를 변경하고 Azure에 다시 배포

1. VSCode Explorer 보기에서 `./HttpExample/index.js` 파일을 선택합니다. 
1. 파일을 JSON 개체를 생성하고 반환할 다음 코드로 바꿉니다.

    ```javascript
    module.exports = async function (context, req) {
        
        try {
            context.log('JavaScript HTTP trigger function processed a request.');
    
            // Read incoming data
            const name = (req.query.name || (req.body && req.body.name));
            const sport = (req.query.sport || (req.body && req.body.sport));
        
            // fail if incoming data is required
            if (!name || !sport) {
    
                context.res = {
                    status: 400
                };
                return;
            }
            
            // Add or change code here
            const message = `${name} likes ${sport}`;
        
            // Construct response
            const responseJSON = {
                "name": name,
                "sport": sport,
                "message": message,
                "success": true
            }
    
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: responseJSON,
                contentType: 'application/json'
            };
        } catch(err) {
            context.res = {
                status: 500
            };
        }
    }
    ```
1. 로컬로 [함수 앱을 다시 실행](#run-the-function-locally)합니다.
1. **요청 본문 입력** 프롬프트에서 요청 메시지 본문을 { "name": "Tom","sport":"basketball" }로 변경합니다. Enter를 눌러 이 요청 메시지를 함수로 보냅니다.
1. 알림에서 응답을 확인합니다.

    ```json
    {
      "name": "Tom",
      "sport": "basketball",
      "message": "Tom likes basketball",
      "success": true
    }
    ```

1. Azure에 [함수를 다시 배포](#deploy-the-project-to-azure)합니다.

## <a name="troubleshooting"></a>문제 해결

이 빠른 시작을 사용할 때 발생하는 가장 일반적인 문제를 해결하려면 아래 표를 참조하세요.

|문제|솔루션|
|--|--|
|로컬 함수 프로젝트 만들 수 없나요?|[Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)이 설치되어 있는지 확인합니다.|
|함수를 로컬에서 실행할 수 없나요?|[Azure Functions Core Tools가 설치](functions-run-local.md?tabs=windows%2Ccsharp%2Cbash)되어 있는지 확인합니다. <br/>Windows에서 실행하는 경우 Visual Studio Code의 기본 터미널 셸이 WSL Bash로 설정되어 있지 않은지 확인합니다.|
|Azure에 함수를 배포할 수 없나요?|출력에서 오류 정보를 검토합니다. 오른쪽 아래 모서리에 있는 종 모양 아이콘은 출력을 볼 수 있는 또 다른 방법입니다. 기존 함수 앱에 게시했나요? 이렇게 하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다.|
|클라우드 기반 함수 앱을 실행할 수 없나요?|매개 변수에 보내려면 쿼리 문자열을 사용해야 합니다.|

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code-extension.md)]

## <a name="next-steps"></a>다음 단계

[Visual Studio Code](functions-develop-vs-code.md?tabs=javascript)를 사용하여 간단한 HTTP 트리거 함수가 있는 함수 앱을 만들었습니다. 다음 문서에서는 Azure Cosmos DB 또는 Azure Storage에 연결하여 해당 함수를 확장합니다. 다른 Azure 서비스에 연결하는 방법에 대한 자세한 내용은 [Azure Functions의 기존 함수에 바인딩 추가](add-bindings-existing-function.md?tabs=javascript)를 참조하세요. 보안에 대해 자세히 알아보려면 [Azure Functions 보안](security-concepts.md)을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Cosmos DB에 연결](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-javascript)
> [Azure Queue Storage에 연결](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
