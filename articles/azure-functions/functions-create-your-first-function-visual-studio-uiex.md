---
title: '빠른 시작: Visual Studio를 사용하여 Azure에서 첫 번째 함수 만들기'
description: 이 빠른 시작에서는 Visual Studio를 사용하여 HTTP 트리거 Azure 함수를 만들고 게시하는 방법에 대해 알아봅니다.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f, mode-other
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 88e08181977f78c8cc37a611b22edcdf28ed49f5
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133045004"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>빠른 시작: Visual Studio를 사용하여 Azure에서 첫 번째 함수 만들기

이 문서에서는 Visual Studio를 사용하여 HTTP 요청에 응답하는 C# 클래스 라이브러리 기반 함수를 만듭니다. 코드를 로컬에서 테스트한 후 다음에 배포합니다. <abbr title="서버의 모든 세부 정보가 애플리케이션 개발자에게 투명하게 공개되어 코드 배포 및 관리 프로세스가 간소화되는 런타임 컴퓨팅 환경.">서버를 사용하지 않음</abbr> 환경 <abbr title="애플리케이션을 위한 저렴한 서버리스 컴퓨팅 환경을 제공하는 Azure의 서비스.">Azure 기능</abbr>.

이 빠른 시작을 완료하면 Azure 계정에 미화 몇 센트 이하로 소액의 비용이 부과됩니다. <abbr title="Azure 사용량에 대한 청구 정보를 유지 관리하는 프로필.">Azure 계정</abbr>.

## <a name="1-prepare-your-environment"></a>1. 환경 준비

+ Azure 만들기 <abbr title="Azure 사용량에 대한 청구 정보를 유지 관리하는 프로필.">account</abbr> 활성 <abbr title="Azure에서 리소스를 관리하는 기본 조직 구조이며 일반적으로 조직 내의 개인 또는 부서와 연결됩니다.">subscription</abbr>. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Visual Studio 2019](https://azure.microsoft.com/downloads/)를 설치하고, 설치하는 동안 **Azure 개발** 워크로드를 선택합니다. 

<br/>
<details>
<summary><strong>Azure Functions 프로젝트를 대신 사용</strong></summary>
다음 항목을 만들려는 경우 <abbr title="함께 배포하고 관리할 수 있는 하나 이상의 개별 함수에 대한 논리적 컨테이너.">Azure Functions 프로젝트</abbr> Visual Studio 2017을 대신 사용하여 [최신 Azure Functions 도구](functions-develop-vs.md#check-your-tools-version)를 먼저 설치해야 합니다.
</details>

## <a name="2-create-a-function-app-project"></a>2. 함수 앱 프로젝트 만들기

1. Visual Studio 메뉴에서 **파일** > **새로 만들기** > **프로젝트** 를 차례로 선택합니다.

1. **새 프로젝트 만들기** 에서 검색 상자에 *함수* 를 입력하고, **Azure Functions** 템플릿을 선택한 다음, **다음** 을 선택합니다.

1. **새 프로젝트 구성** 에서 **를 입력합니다. <abbr title="함수 앱 이름은 C# 네임스페이스로 유효해야 하므로 밑줄, 하이픈 또는 다른 영숫자가 아닌 문자를 사용하지 마세요. 프로젝트의 ">프로젝트 이름</abbr>** 을 선택하고 **만들기** 를 선택합니다. 

1. **새 Azure Functions 애플리케이션 만들기** 설정에 다음 정보를 제공합니다.

    + **를 선택합니다.<abbr title=" 이 값은 .NET Core 3.x를 지원하는 Azure Functions의 버전 3.x 런타임을 사용하는 함수 프로젝트를 만듭니다. Azure Functions 1.x는 .NET Framework를 지원합니다.">Functions 런타임 드롭다운의 Azure Functions v3(.NET Core)</abbr>** (자세한 내용은 [Azure Functions 런타임 버전 개요](functions-versions.md)를 참조하세요.)
    
    + **를 선택합니다. <abbr title="이 값은 HTTP 요청에 의해 트리거되는 함수를 만듭니다. 함수 템플릿으로서의 ">HTTP 트리거</abbr>** .
    
    + **를 선택합니다. <abbr title="Azure 함수에는 스토리지 계정이 필요하기 때문에 Azure에 프로젝트를 게시할 때 할당되거나 생성됩니다. HTTP 트리거는 Azure Storage 계정 연결 문자열을 사용하지 않습니다. 다른 모든 트리거 형식에는 유효한 Azure Storage 계정 연결 문자열이 필요합니다. 스토리지 계정 드롭다운의 ">스토리지 에뮬레이터</abbr>** .
        
    + **익명** 을 선택합니다. <abbr title="만들어진 함수를 모든 클라이언트에서 키를 제공하지 않고 트리거할 수 있습니다. 이 권한 부여 설정을 통해 새 함수를 쉽게 테스트할 수 있습니다.">권한 부여 수준</abbr> 선택합니다. (키 및 권한 부여에 대한 자세한 내용은 [권한 부여 키](functions-bindings-http-webhook-trigger.md#authorization-keys) 및 [HTTP 및 웹후크 바인딩](functions-bindings-http-webhook.md)을 참조하세요.)

    + **만들기** 를 선택합니다.
        
## <a name="3-rename-the-function"></a>3. 함수 이름 바꾸기

`FunctionName` 메서드 특성은 함수 이름을 설정하며, 생성되는 기본값은 `Function1`입니다. 프로젝트를 만들 때 도구를 사용하여 기본 함수 이름을 재정의할 수 없으므로 함수 클래스, 파일 및 메타데이터에 따라 더욱 적절한 이름을 생성해야 합니다.

1. **파일 탐색기** 에서 Function1.cs 파일을 마우스 오른쪽 단추로 클릭하고 이름을 *HttpExample.cs* 로 바꿉니다.

1. 코드에서 Function1 클래스의 이름을 'HttpExample'로 바꿉니다.

1. `HttpTrigger` 메서드 `Run`에서 `FunctionName` 메서드 특성을 `HttpExample`로 바꿉니다.


## <a name="4-run-the-function-locally"></a>4. 로컬에서 함수 실행

1. 함수를 실행하려면 Visual Studio에서 <kbd>F5</kbd>를 누릅니다.

1. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![Azure 로컬 런타임](../../includes/media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

1. 브라우저의 주소 표시줄에 HTTP 요청에 대한 URL을 붙여 넣습니다. **?name=<YOUR_NAME>** 쿼리 문자열을 이 URL에 추가하고 요청을 실행합니다. 

    ![브라우저의 localhost 함수 응답](../../includes/media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

1. 디버깅을 중지하려면 Visual Studio에서 <kbd>Shift</kbd>+<kbd>F5</kbd>를 누릅니다.

<br/>
<details>
<summary><strong>문제 해결</strong></summary>
 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다. 함수를 로컬로 실행하는 경우 권한 부여 수준이 적용되지 않습니다.
</details>

## <a name="5-publish-the-project-to-azure"></a>5. Azure에 프로젝트 게시

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.

1. **대상** 에서 **Azure** 를 선택합니다.

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Azure 대상 선택":::

1. **특정 대상** 에서 **Azure 함수 앱(Windows)** 을 선택합니다.

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Azure 함수 앱 선택":::

1. **함수 인스턴스** 에서 **새 Azure 함수 만들기...** 를 선택한 후 다음에 지정된 값을 사용합니다.

    + **이름** 에 대해 다음을 제공합니다. <abbr title="새 함수 앱을 고유하게 식별하는 이름을 사용합니다. 이 이름을 수락하거나 새 이름을 입력합니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다.">전역적으로 고유한 이름</abbr>
    
    + 드롭다운 목록에서 구독을 **선택합니다**.
    
    + 기존 항목 **선택** <abbr title="한 단위로 관리할 수 있는 관련 Azure 리소스에 대한 논리적 컨테이너.">리소스 그룹</abbr> 드롭다운 목록에서 또는 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다.
    
    + **Select** <abbr title="프로젝트를 사용량 요금제에서 실행되는 함수 앱에 게시하는 경우 함수 앱의 실행에 대한 비용만 지불합니다. 다른 호스팅 계획에는 비용이 더 많이 듭니다.">Consumption</abbr> 재생 유형 드롭다운에서 (자세한 내용은 [소비 계획](consumption-plan.md)을 참조하세요.)
    
    + **선택**  <abbr title="리소스가 할당된 특정 Azure 데이터 센터에 대한 지리적 참조. 사용 가능한 지역 목록은 [지역](https://azure.microsoft.com/regions/)을 참조하세요.">위치</abbr> 드롭다운에서
    
    + **를 선택합니다**. <abbr="Functions 런타임에는 Azure Storage 계정이 필요합니다. 새로 만들기를 선택하여 범용 스토리지 계정을 구성합니다. 스토리지 계정 요구 사항을 충족하는 기존 계정을 선택할 수도 있습니다.">Azure Storage</abbr> 드롭다운의 계정

    ![App Service 만들기 대화 상자](../../includes/media/functions-vstools-publish/functions-visual-studio-publish.png)

1. **만들기** 를 선택합니다. 

1. **함수 인스턴스** 에서 **패키지 파일에서 실행** 이 선택되어 있는지 확인합니다. 

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="프로필 만들기 완료":::

    <br/>
    <details>
    <summary><strong>이 설정의 역할은 무엇인가요?</strong></summary>
    **패키지 파일에서 실행** 을 사용할 경우 [Run-From-Package](run-functions-from-deployment-package.md) 모드가 설정된 상태에서 [Zip 배포](functions-deployment-technologies.md#zip-deploy)를 사용하여 함수 앱이 배포됩니다. 이는 성능이 향상되므로 함수 프로젝트에 권장되는 배포 방법입니다.    
    </details>   

1. **마침** 을 선택합니다.

1. 게시 페이지에서 **게시** 를 선택합니다.

1. 게시 페이지에서 함수 앱의 루트 URL을 검토합니다.

1. 게시 탭에서 **다음에서 관리<abbr title="를 선택합니다. 클라우드 탐색기를 통해 Visual Studio를 사용하여 사이트의 콘텐츠를 확인하고, 함수 앱을 시작 및 중지하고, Azure 및 Azure Portal에서 함수 앱 리소스를 직접 찾아볼 수 있습니다.">클라우드 탐색기</abbr>** .
    
    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="게시 성공 메시지":::
    

## <a name="6-test-your-function-in-azure"></a>6. Azure에서 함수 테스트

1. 클라우드 탐색기에서 새 함수 앱을 선택해야 합니다. 그렇지 않은 경우 구독, **App Services** 를 차례로 확장하고 새 함수 앱을 선택합니다.

1. 마우스 오른쪽 단추로 함수 앱을 클릭하고 **브라우저에서 열기** 를 선택합니다. 그러면 기본 웹 브라우저에서 함수 앱의 루트가 열리고 함수 앱이 실행 중임을 나타내는 페이지가 표시됩니다. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="함수 앱 실행 중":::

1. 브라우저의 주소 표시줄에서 **/api/HttpExample?name=Functions** 문자열을 기준 URL에 추가하고 요청을 실행합니다.

    HTTP 트리거 함수를 호출하는 URL은 다음과 같은 형식입니다.

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. 이 URL로 이동하면 다음 예제와 같이 함수에서 반환된 원격 GET 요청에 대한 브라우저의 응답이 표시됩니다.

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="브라우저의 함수 응답":::

## <a name="7-clean-up-resources"></a>7. 리소스 정리

[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]

## <a name="next-steps"></a>다음 단계

추가 방법을 알아보려면 다음 문서를 진행하세요. <abbr title="큐에 메시지를 만들 수 있도록 스토리지 큐에 함수를 연결하는 방법. 바인딩은 함수와 기타 리소스 간의 선언적 연결입니다. 입력 바인딩은 함수에 데이터를 제공합니다. 출력 바인딩은 함수에서 다른 리소스로 데이터를 제공합니다."> Azure Storage 큐 출력 바인딩</abbr> 함수 대상:

> [!div class="nextstepaction"]
> [함수에 Azure Storage 큐 바인딩 추가](functions-add-output-binding-storage-queue-vs.md)
