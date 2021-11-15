---
title: Azure Functions 트리거 및 바인딩을 통해 ID 기반 연결 사용
ms.service: azure-functions
description: Azure Functions를 사용하여 Service Bus 큐에 연결할 때 연결 문자열 대신 ID 기반 연결을 사용하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 10/20/2021
ms.openlocfilehash: 5a3eb4dc6006d9072abac95b7940692e8af737a5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271268"
---
# <a name="tutorial-use-identity-based-connections-instead-of-secrets-with-triggers-and-bindings"></a>자습서: 트리거 및 바인딩을 통해 암호 대신 ID 기반 연결 사용

이 자습서에서는 함수 앱 설정에 저장된 암호 대신, 관리 ID를 사용하여 Azure Service Bus 큐에 연결하도록 Azure Functions를 구성하는 방법을 보여 줍니다. 이 자습서는 [이전 자습서]인 [정의의 기본 스토리지 암호 없이 함수 앱 만들기]에서 연결된 내용입니다. ID 기반 연결에 대해 자세히 알아보려면 [ID 기반 연결 구성](functions-reference.md#configure-an-identity-based-connection)을 참조하세요.

표시되는 절차는 기본적으로 모든 언어에 해당하지만 이 자습서에서는 현재 Windows의 C# 클래스 라이브러리 함수를 지원합니다. 

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Service Bus 네임스페이스 및 큐를 만듭니다.
> * 관리 ID를 사용하여 함수 앱 구성
> * 해당 ID에 Service Bus 큐에서 읽을 수 있는 권한을 부여하는 역할 할당 만들기
> * Service Bus 트리거를 사용하여 함수 앱을 만들고 배포합니다.
> * Service Bus에 대한 ID 기반 연결 확인

## <a name="prerequisite"></a>필수 조건

이전 자습서 완료: [ID 기반 연결을 사용하여 함수 앱 만들기][이전 자습서]

## <a name="create-a-service-bus-and-queue"></a>Service Bus 및 큐 만들기

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기(+)** 를 선택합니다.

1. **리소스 만들기** 페이지에서 **통합** > **Service Bus** 를 선택합니다.

1. **기본 정보** 페이지에서 다음 표를 사용하여 Service Bus 네임스페이스 구성합니다. 나머지 옵션은 기본값 상태로 둡니다.

    | 옵션      | 제안 값  | 설명      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용하여 만든 리소스 그룹입니다. |
    | **네임스페이스 이름** | 전역적으로 고유한 이름 | 함수를 트리거할 인스턴스의 네임스페이스입니다. 네임스페이스는 공개적으로 액세스할 수 있기 때문에 Azure에서 전역적으로 고유한 이름을 사용해야 합니다. 이름은 6~50자 사이여야 하며 영숫자와 대시만 포함하고 숫자로 시작할 수 없습니다. |
    | **[위치](https://azure.microsoft.com/regions/)** | myFunctionRegion | 함수 앱을 만든 지역입니다. |
    | **가격 책정 계층** | Basic | 기본 Service Bus 계층입니다. |

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

1. 새 Service Bus 네임스페이스에서 **+ 큐** 를 선택하여 큐를 추가합니다.

1. `myinputqueue`를 새 큐의 이름으로 입력하고 **만들기** 를 선택합니다.

이제 큐가 있으므로 함수 앱의 관리 ID에 역할 할당을 추가합니다.

## <a name="configure-your-service-bus-trigger-with-a-managed-identity"></a>관리 ID를 사용하여 Service Bus 트리거 구성

ID 기반 연결을 사용하여 Service Bus 트리거를 사용하려면 함수 앱에서 관리 ID에 **Azure Service Bus 데이터 받는 사람** 역할 할당을 추가해야 합니다. 이 역할은 관리 ID를 사용하여 Service Bus 네임스페이스를 트리거할 때 필요합니다. 사용자 고유의 계정을 이 역할에 추가할 수도 있습니다. 이렇게 하면 로컬 테스트 중에 Service Bus 네임스페이스에 연결할 수 있습니다.

> [!NOTE]
> ID 기반 연결을 사용하기 위한 역할 요구 사항은 서비스 및 서비스에 연결하는 방법에 따라 달라집니다. 요구 사항은 트리거, 입력 바인딩 및 출력 바인딩마다 다릅니다. 특정 역할 요구 사항에 대한 자세한 내용은 서비스에 대한 트리거 및 바인딩 설명서를 참조하세요.

1. 방금 만든 Service Bus 네임스페이스에서 **액세스 제어(IAM)** 를 선택합니다. 여기에서 리소스에 대한 액세스 권한이 있는 사용자를 보고 구성할 수 있습니다.  

1. **추가** 를 클릭하고 **역할 할당 추가** 를 선택합니다.

1. **Azure Service Bus 데이터 받는 사람** 을 검색한 후 선택하고 **다음** 을 클릭합니다.

1. **구성원** 탭의 **액세스 권한 할당 대상** 아래에서 **관리 ID** 를 선택합니다.

1. **구성원 선택** 을 클릭하여 **관리 ID 선택** 패널을 엽니다.

1. **구독** 이 이전에 리소스를 만든 구독인지 확인합니다.

1. **관리 ID** 선택기에서 **시스템 할당 관리 ID** 범주에서 **함수 앱** 을 선택합니다. "함수 앱" 레이블의 옆에는 사용자 할당 ID가 있는 구독의 앱 수를 나타내는 괄호로 묶은 숫자가 표시될 수 있습니다.

1. 앱이 입력 필드 아래의 목록에 표시됩니다. 표시되지 않으면 **선택** 상자를 사용하여 앱 이름으로 결과를 필터링할 수 있습니다.

1. 애플리케이션을 클릭합니다. **선택한 구성원** 섹션 아래로 이동됩니다. **선택** 을 클릭합니다.

1. **역할 할당 추가** 화면으로 돌아가서 **검토 + 할당** 을 클릭합니다. 구성을 검토하고 **검토 + 할당** 을 클릭합니다.

관리 ID를 사용하여 함수 앱에 Service Bus 네임스페이스에 대한 액세스 권한을 부여했습니다.

## <a name="connect-to-service-bus-in-your-function-app"></a>함수 앱에서 Service Bus에 연결

1. 포털에서 [이전 자습서]에서 만든 함수 앱을 검색하거나 **함수 앱** 페이지에서 찾아봅니다.

1. 함수 앱의 **설정** 에서 **구성** 을 선택합니다.

1. **애플리케이션 설정** 에서 **+ 새 애플리케이션 설정** 을 선택하여 다음 표에 나와 있는 새 설정을 만듭니다.

    | 이름      | 값  | 설명 |
    | ------------ | ---------------- | ----------- |
    | **ServiceBusConnection__fullyQualifiedNamespace** | <SERVICE_BUS_NAMESPACE>.servicebus.windows.net | 이 설정은 함수 앱을 Service Bus에 연결하여 비밀 대신 ID 기반 연결을 사용합니다. |

1. 두 설정을 만든 후 **저장** > **확인** 을 선택합니다.

관리 ID를 사용하여 Service Bus 네임스페이스에 연결하기 위한 함수 앱을 준비했으므로 Service Bus 트리거를 사용하는 새 함수를 로컬 프로젝트에 추가할 수 있습니다.

## <a name="add-a-service-bus-triggered-function"></a>Service Bus 트리거 함수 추가

1. 다음과 같이 `func init` 명령을 실행하여 지정된 런타임에 LocalFunctionProj 폴더에 함수 프로젝트를 만듭니다.

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. 프로젝트 폴더로 이동합니다.

    ```console
    cd LocalFunctionProj
    ```

1. 루트 프로젝트 폴더에서 다음 명령을 실행합니다.

    ```command
    dotnet remove package Microsoft.Azure.Webjobs.Extensions.ServiceBus
    dotnet add package Microsoft.Azure.Webjobs.Extensions.ServiceBus --prerelease
    ```

    이렇게 하면 Service Bus 확장 패키지의 기본 버전이 관리 ID를 지원하는 버전으로 바뀝니다.

1. 다음 명령을 실행하여 프로젝트에 Service Bus 트리거 함수를 추가합니다.

    ```csharp
    func new --name ServiceBusTrigger --template ServiceBusQueueTrigger 
    ```

    그러면 새 Service Bus 트리거에 대한 코드와 확장 패키지에 대한 참조가 추가됩니다. 이 트리거에 대한 Service Bus 네임스페이스 연결 설정을 추가해야 합니다.

1. 새 ServiceBusTrigger.cs 프로젝트 파일을 열고 `ServiceBusTrigger` 클래스를 다음 코드로 바꿉니다.

    ```csharp
    public static class ServiceBusTrigger
    {
        [FunctionName("ServiceBusTrigger")]
        public static void Run([ServiceBusTrigger("myinputqueue", 
            Connection = "ServiceBusConnection")]string myQueueItem, ILogger log)
        {
            log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
        }
    }
    ```

    이 코드 샘플은 큐 이름을 `myinputqueue`로 업데이트합니다. 이 이름은 앞에서 만든 큐와 같은 이름입니다. 또한 Service Bus 연결의 이름을 `ServiceBusConnection`으로 설정합니다. 이것은 포털에서 구성한 ID 기반 연결 `ServiceBusConnection__fullyQualifiedNamespace`에서 사용하는 Service Bus 네임스페이스입니다.

> [!NOTE]  
> 이제 `func start`를 사용하여 함수를 실행하려고 하면 오류가 표시됩니다. 로컬에서 정의된 ID 기반 연결이 없기 때문입니다. 함수를 로컬로 실행하려면 [이전 섹션](#connect-to-service-bus-in-your-function-app)에서 수행한 것처럼 `local.settings.json`에서 앱 설정 `ServiceBusConnection__fullyQualifiedNamespace`를 설정합니다. 또한 개발자 ID에 역할을 할당해야 합니다. 자세한 내용은 [ID 기반 연결을 사용하여 로컬 개발 설명서](./functions-reference.md#local-development-with-identity-based-connections)를 참조하세요.

## <a name="publish-the-updated-project"></a>업데이트된 프로젝트 게시

1. 다음 명령을 실행하여 배포 패키지에 필요한 파일을 로컬로 생성합니다.

    ```console
    dotnet publish --configuration Release
    ```

1. `\bin\Release\netcoreapp3.1\publish` 하위 폴더를 찾아본 후 해당 콘텐츠에서 .zip 파일을 만듭니다.

1. 다음 명령을 실행하여 `FUNCTION_APP_NAME`, `RESOURCE_GROUP_NAME` 및 `PATH_TO_ZIP` 매개 변수를 적절하게 대체하여 .zip 파일을 게시합니다.

    ```azurecli
    az functionapp deploy -n FUNCTION_APP_NAME -g RESOURCE_GROUP_NAME --src-path PATH_TO_ZIP
    ```

새 트리거로 함수 앱을 업데이트했으므로 ID를 사용하여 작동하는지 확인할 수 있습니다.

## <a name="validate-your-changes"></a>변경 내용 유효성 검사

1. 포털에서 `Application Insights`를 검색하고 **서비스** 에서 **Application Insights** 를 선택합니다.  

1. **Application Insights** 에서 명명된 인스턴스를 찾아보거나 검색합니다.

1. 인스턴스에서 **조사** 아래의 **라이브 메트릭** 을 선택합니다.

1. 이전 탭을 열어 두고 Azure Portal을 새 탭에서 엽니다. 새 탭에서 Service Bus 네임스페이스로 이동하고 왼쪽 블레이드에서 **큐** 를 선택합니다.

1. `myinputqueue`라는 큐를 선택합니다.

1. 왼쪽 메뉴에서 **Service Bus Explorer** 를 선택합니다.

1. 테스트 메시지를 보냅니다.

1. 열려 있는 **라이브 메트릭** 탭을 선택하고 Service Bus 큐 실행을 확인합니다.

축하합니다! 관리 ID를 사용하여 Service Bus 큐 트리거를 성공적으로 설정했습니다.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 ID 기반 연결을 사용하여 함수 앱을 만들었습니다.

다음 링크를 사용하여 ID 기반 연결을 사용하는 추가 Azure Functions에 대해 알아보세요.

- [Azure Functions의 관리 ID](../app-service/overview-managed-identity.md)
- [Azure Functions의 ID 기반 연결](./functions-reference.md#configure-an-identity-based-connection)
- [로컬 개발을 위한 함수 설명서](./functions-reference.md#local-development-with-identity-based-connections)

[이전 자습서]: ./functions-identity-based-connections-tutorial.md
