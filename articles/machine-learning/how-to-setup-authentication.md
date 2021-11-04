---
title: 인증 설정
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 다양한 리소스 및 워크플로에 대한 인증을 설정하고 구성하는 방법을 알아봅니다.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-js, contperf-fy21q2, subject-rbac-steps
ms.openlocfilehash: c83d578c364257ad8ecd229e49fdd0d35ec7a9c6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560780"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure Machine Learning 리소스 및 워크플로에 대한 인증 설정


Azure Machine Learning 작업 영역에 인증을 설정하는 방법을 알아봅니다. Azure Machine Learning 작업 영역 인증은 대부분 __Azure Active Directory__(Azure AD)를 기반으로 합니다. 일반적으로 작업 영역에 연결할 때는 네 가지 인증 워크플로를 사용할 수 있습니다.

* __대화형__: Azure Active Directory의 계정을 사용하여 직접 인증하거나 인증에 사용되는 토큰을 가져옵니다. 대화형 인증은 _실험 및 반복적인 개발_ 중에 사용됩니다. 대화형 인증을 사용하면 사용자별 기준으로 웹 서비스와 같은 리소스에 대한 액세스를 제어할 수 있습니다.

* __서비스 주체__: Azure Active Directory에 서비스 주체 계정을 만들고 이를 사용하여 인증하거나 토큰을 가져옵니다. 서비스 주체는 사용자 상호 작용 없이 서비스 _인증을 위해 자동화된 프로세스_ 가 필요한 경우에 사용됩니다. 예를 들어 학습 코드가 변경될 때마다 모델을 학습시키고 테스트하는 연속 통합 및 배포 스크립트가 있습니다.

* __Azure CLI 세션__: 활성 Azure CLI 세션을 사용하여 인증합니다. Azure CLI 인증은 _실험 및 반복 개발_ 중이거나 사전 인증된 세션을 사용하여 서비스의 _인증을 위한 자동화된 프로세스_ 가 필요한 경우에 사용됩니다. Python 코드에 자격 증명을 저장하지 않거나 사용자에게 인증을 요청하지 않고 로컬 워크스테이션에서 Azure CLI를 통해 Azure에 로그인할 수 있습니다. 마찬가지로, 서비스 사용자 ID를 사용하여 Azure CLI를 인증하는 동시에 연속 통합 및 배포 파이프라인의 일부로 동일한 스크립트를 다시 사용할 수 있습니다.

* __관리 ID__: _Azure Virtual Machine_ 에서 Azure Machine Learning SDK를 사용할 때 Azure에 관리 ID를 사용할 수 있습니다. 이 워크플로를 사용하면 Python 코드에 자격 증명을 저장하거나 사용자에게 인증 프롬프트를 표시하지 않아도 VM이 관리 ID를 사용하여 작업 영역에 연결할 수 있습니다. 또한 _모델을 학습시킬_ 때 관리 ID를 사용하여 작업 영역에 액세스하도록 Azure Machine Learning 컴퓨팅 클러스터를 구성할 수도 있습니다.

사용된 인증 워크플로에 관계없이 Azure 역할 기반 액세스 제어(Azure RBAC)는 리소스에 허용된 액세스 수준(권한 부여) 범위를 지정하는 데 사용됩니다. 예를 들어 관리 또는 자동화 프로세스는 컴퓨팅 인스턴스만들기 액세스 권한이 있지만, 사용 권한이 없고, 데이터 과학자는 사용 권한만 있고 삭제 또는 만들기 원한이 없을 수 있습니다. 자세한 내용은 [Azure Machine Learning 작업 영역 액세스 관리](how-to-assign-roles.md)를 참조하세요.

Azure AD 조건부 액세스를 사용하여 각 인증 워크플로의 작업 영역에 대한 액세스를 추가로 제어하거나 제한할 수 있습니다. 예를 들어 관리자는 관리 디바이스에서만 작업 영역 액세스를 허용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [cli-version-info](../../includes/machine-learning-cli-version-1-only.md)]

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)을 만듭니다.
* Azure Machine Learning SDK를 설치하도록 [개발 환경을 구성](how-to-configure-environment.md)하거나 SDK가 이미 설치된 [Azure Machine Learning 컴퓨팅 인스턴스](concept-azure-machine-learning-architecture.md#compute-instance)를 사용합니다.

## <a name="azure-active-directory"></a>Azure Active Directory

작업 영역의 모든 인증 워크플로에는 Azure Active Directory가 사용됩니다. 사용자가 개별 계정을 사용하여 인증하도록 하려면 사용자에게 Azure AD의 계정이 있어야 합니다. 서비스 주체를 사용하려면 사용자가 Azure AD에 있어야 합니다. 관리 ID는 또한 Azure AD의 기능입니다. 

Azure AD에 대한 자세한 내용은 [Azure Active Directory 인증이란?](..//active-directory/authentication/overview-authentication.md)을 참조하세요.

Azure AD 계정을 만든 후 작업 영역에 대한 액세스 권한을 부여하는 방법 및 Azure Machine Learning의 기타 작업에 대한 자세한 내용은 [Azure Machine Learning 작업 영역 액세스 관리](how-to-assign-roles.md)를 참조하세요.

## <a name="configure-a-service-principal"></a>서비스 주체

SP(서비스 주체)를 사용하려면 먼저 SP를 만들고 여기에 작업 영역 액세스 권한을 부여해야 합니다. 또한 앞에서 설명한 것처럼 Azure 역할 기반 액세스 제어(Azure RBAC)는 액세스 제어를 위해 사용되므로, SP에 부여할 액세스 권한을 결정해야 합니다.

> [!IMPORTANT]
> 서비스 주체를 사용하는 경우 서비스 주체에 사용되는 __작업에 필요한 최소 액세스 권한__ 을 부여합니다. 예를 들어 웹 배포를 위해 액세스 토큰 읽기 목적으로만 사용되는 경우에는 서비스 주체에 소유자 또는 기여자 액세스 권한을 부여하지 않을 것입니다.
>
> 최소 권한을 부여하는 이유는 서비스 주체에 인증을 위해 암호가 사용되고, 이 암호가 자동화 스크립트의 일부로 저장될 수 있기 때문입니다. 암호가 유출되더라도 특정 작업에 필요한 최소 액세스 권한만 지정함으로써 SP 악용을 최소화할 수 있습니다.

SP를 만들고 작업 영역에 대해 액세스 권한을 부여하는 가장 쉬운 방법은 [Azure CLI](/cli/azure/install-azure-cli)를 사용하는 것입니다. 서비스 주체를 만들고 여기에 작업 영역에 대한 액세스 권한을 부여하려면 다음 단계를 따르세요.

> [!NOTE]
> 여기 표시된 단계를 모두 수행하려면 해당 구독에 대한 관리자여야 합니다.

1. Azure 구독을 인증합니다.

    ```azurecli-interactive
    az login
    ```

    CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. CLI가 브라우저를 열 수 없는 경우에는 사용자가 브라우저를 열고 명령줄의 지침을 따릅니다. 지침에 따라 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)으로 이동하고 인증 코드를 입력합니다.

    Azure 구독이 여러 개 있으면 `az account set -s <subscription name or ID>` 명령을 사용하여 구독을 설정할 수 있습니다. 자세한 내용은 [여러 Azure 구독 사용](/cli/azure/manage-azure-subscriptions-azure-cli)을 참조하세요.

    다른 인증 방법은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

1. 서비스 주체를 만듭니다. 다음 예제에서는 **ml-auth** 라는 SP를 만듭니다.

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    출력은 다음과 유사한 JSON입니다. 이 문서의 다른 단계에 필요하므로 `clientId`, `clientSecret` 및 `tenantId` 필드를 기록해 둡니다.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. 이전 단계에서 반환된 `clientId` 값을 사용하여 서비스 주체에 대한 세부 정보를 검색합니다.

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    다음 JSON은 이 명령의 출력에 대한 간단한 예제를 보여줍니다. 다음 단계에서 해당 값이 필요하므로 `objectId` 필드를 기록해 둡니다.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. SP가 Azure Machine Learning 작업 영역에 액세스하도록 허용합니다. 작업 영역 이름과 `-w` 및 `-g` 매개 변수에 대한 리소스 그룹 이름이 각각 필요합니다. `--user` 매개 변수의 경우 이전 단계의 `objectId` 값을 사용합니다. `--role` 매개 변수는 서비스 주체에 대해 액세스 역할을 설정하도록 허용합니다. 다음 예제에서는 **소유자** 역할에 SP가 할당됩니다. 

    > [!IMPORTANT]
    > 소유자 액세스 권한은 서비스 주체가 작업 영역에서 거의 모든 작업을 수행하도록 허용합니다. 이 문서에서는 액세스 권한을 부여하는 방법을 보여주기 위해 사용되었습니다. 프로덕션 환경에서는 의도한 역할을 수행하는 데 필요한 최소 액세스 권한을 서비스 주체에 부여하는 것이 좋습니다. 해당 시나리오에 필요한 액세스 권한이 포함된 사용자 지정 역할을 만드는 방법은 [Azure Machine Learning 작업 영역 액세스 관리](how-to-assign-roles.md)를 참조하세요.

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    이 호출은 성공 시 출력을 생성하지 않습니다.

## <a name="configure-a-managed-identity"></a>관리 ID 구성

> [!IMPORTANT]
> 관리 ID는 Azure Virtual Machine에서 Azure Machine Learning SDK를 사용할 때 또는 Azure Machine Learning 컴퓨팅 클러스터에서만 지원됩니다. 컴퓨팅 클러스터에서 관리 ID 사용은 현재 미리 보기 상태입니다.

### <a name="managed-identity-with-a-vm"></a>VM의 관리 ID

1. [VM에서 Azure 리소스에 대해 시스템에서 할당된 관리 ID](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)를 사용하도록 설정합니다.

1. [Azure portal](https://portal.azure.com)에서 작업영역을 선택한 다음,__Access Control (IAM)__ 를 선택합니다.
1. __추가__, __역할 할당 추가__ 를 선택하여 __역할 할당 추가 창__ 을 엽니다.
1. 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

    | 설정 | 값 |
    | ----- | ----- |
    | 역할 | 할당하려는 역할을 선택합니다. |
    | 다음에 대한 액세스 할당 | 관리 ID |
    | 멤버 | 이전에 만든 사용자 관리 ID입니다. |

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-page.png)

### <a name="managed-identity-with-compute-cluster"></a>컴퓨팅 클러스터의 관리 ID

자세한 내용은 [컴퓨팅 클러스터의 관리 ID 설정](how-to-create-attach-compute-cluster.md#managed-identity)을 참조하세요.

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>대화형 인증 사용

> [!IMPORTANT]
> 대화형 인증에는 브라우저가 사용되고 쿠키(타사 쿠키 포함)가 필요합니다. 쿠키를 사용하지 않도록 설정한 경우 “로그인할 수 없음”과 같은 오류가 표시될 수 있습니다. 이 오류는 [Azure AD 다단계 인증](../active-directory/authentication/concept-mfa-howitworks.md)을 사용하도록 설정한 경우에도 발생할 수 있습니다.

설명서 및 샘플의 대부분의 예제에는 대화형 인증이 사용됩니다. 예를 들어 SDK를 사용할 때는 UI 기반 인증 흐름에서 사용자에게 프롬프트를 자동으로 표시하는 두 가지 함수 호출이 있습니다.

* `from_config()` 함수를 호출하면 프롬프트가 표시됩니다.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    `from_config()` 함수는 작업 영역 연결 정보를 포함하는 JSON 파일을 찾습니다.

* 구독, 리소스 그룹 및 작업 영역 정보를 제공하기 위해 `Workspace` 생성자를 사용할 때도 대화형 인증이 요청됩니다.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> 여러 테넌트에 액세스할 수 있는 경우 해당 클래스를 가져오고 대상 테넌트를 명시적으로 정의해야 합니다. `InteractiveLoginAuthentication`에 대한 생성자를 호출하면 위의 호출과 비슷하게 로그인하라는 메시지가 표시됩니다.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Azure CLI를 사용할 때 `az login` 명령은 CLI 세션 인증을 위해 사용됩니다. 자세한 내용은 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.

> [!TIP]
> 이전에 Azure CLI를 사용하여 대화형으로 인증된 환경에서 SDK를 사용하는 경우 `AzureCliAuthentication` 클래스를 사용하고 CLI로 캐시된 자격 증명을 사용하여 작업 영역에 인증을 수행할 수 있습니다.
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>서비스 주체 인증 사용

서비스 주체를 사용해서 SDK에서 작업 영역에 인증하려면 `ServicePrincipalAuthentication` 클래스 생성자를 사용합니다. 서비스 공급자를 만들 때 표시된 값을 매개 변수로 사용합니다. `tenant_id` 매개 변수는 위의 `tenantId`에 매핑되고, `service_principal_id`는 `clientId`에 매핑되고, `service_principal_password`는 `clientSecret`에 매핑됩니다.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

이제 `sp` 변수는 SDK에서 직접 사용하는 인증 개체를 포함합니다. 일반적으로 다음 코드와 같이 환경 변수에서 위의 사용된 ID/암호를 저장하는 것이 좋습니다. 환경 변수에 저장하면 해당 정보가 GitHub 리포지토리에 실수로 체크 인되는 것을 방지할 수 있습니다.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Python에서 실행되고 SDK를 주로 사용하는 자동화 워크플로의 경우 대부분의 경우 인증에 이 개체를 그대로 사용할 수 있습니다. 다음 코드는 만든 인증 개체를 사용하여 작업 영역에서 인증을 합니다.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id",
                   resource_group="your-rg-name")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Azure CLI에서 서비스 주체 사용

Azure CLI 명령에 서비스 주체를 사용할 수 있습니다. 자세한 내용은 [서비스 주체를 사용하여 로그인](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal)을 참조하세요.

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>REST API에 서비스 주체 사용(미리 보기)

또한 서비스 주체를 사용하여 Azure Machine Learning [REST API](/rest/api/azureml/)(미리 보기)에 인증을 수행할 수 있습니다. 자동화 워크플로에서 헤드리스 인증에 대한 서비스 간 호출을 허용하는 Azure Active Directory [클라이언트 자격 증명 권한 부여 흐름](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)을 사용합니다. 이 예제는 Python 및 Node.js 모두에서 [ADAL 라이브러리](../active-directory/azuread-dev/active-directory-authentication-libraries.md)를 사용하여 구현되지만 OpenID Connect 1.0을 지원하는 오픈 소스 라이브러리를 사용할 수도 있습니다.

> [!NOTE]
> MSAL.js는 ADAL보다 최신 라이브러리이지만 MSAL.js에서 클라이언트 자격 증명을 사용하여 서비스 간 인증을 수행할 수 없습니다. 주로 특정 사용자와 연결된 대화형/UI 인증을 위한 클라이언트 쪽 라이브러리이기 때문입니다. REST API를 사용하여 자동화 워크플로를 빌드하려면 아래와 같이 ADAL을 사용하는 것이 좋습니다.

#### <a name="nodejs"></a>Node.js

Node.js를 사용하여 인증 토큰을 생성하려면 다음 단계를 사용합니다. 사용자 환경에서 `npm install adal-node`를 실행합니다. 그런 다음, 위의 단계에서 만든 서비스 주체의 `tenantId`, `clientId` 및 `clientSecret`를 다음 스크립트에서 일치하는 변수의 값으로 사용합니다.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

변수 `tokenResponse`는 토큰 및 관련 메타데이터(예: 만료 시간)를 포함하는 개체입니다. 토큰은 1시간 동안 유효하며, 동일하게 다시 호출하고 새 토큰을 검색하여 새로 고칠 수 있습니다. 다음 코드 조각은 샘플 응답입니다.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

`accessToken` 속성을 사용하여 인증 토큰을 페치합니다. 토큰을 사용하여 API 호출을 수행하는 방법에 대한 예제는 [REST API 설명서](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)를 참조하세요.

#### <a name="python"></a>Python

Python을 사용하여 인증 토큰을 생성하려면 다음 단계를 사용합니다. 사용자 환경에서 `pip install adal`를 실행합니다. 그런 다음, 위의 단계에서 만든 서비스 주체의 `tenantId`, `clientId` 및 `clientSecret`를 다음 스크립트에서 해당 변수의 값으로 사용합니다.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

변수 `token_response`는 토큰 및 관련 메타데이터(예: 만료 시간)를 포함하는 사전입니다. 토큰은 1시간 동안 유효하며, 동일하게 다시 호출하고 새 토큰을 검색하여 새로 고칠 수 있습니다. 다음 코드 조각은 샘플 응답입니다.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

`token_response["accessToken"]`를 사용하여 인증 토큰을 페치합니다. 토큰을 사용하여 API 호출을 수행하는 방법에 대한 예제는 [REST API 설명서](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)를 참조하세요.

#### <a name="java"></a>Java

Java에서 표준 REST 호출을 사용하여 전달자 토큰을 검색합니다.

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

위 코드가 `200 OK` 이외의 예외 및 상태 코드를 처리해야 하지만 다음 패턴을 보여줍니다. 

- 클라이언트 ID 및 비밀을 사용하여 프로그램에 액세스 권한이 필요한지 확인
- 테넌트 ID를 사용하여 `login.microsoftonline.com`이 확인해야 할 위치 지정
- Azure Resource Manager를 권한 부여 토큰의 소스로 사용

## <a name="use-managed-identity-authentication"></a>관리 ID 인증 사용

관리 ID로 구성된 VM 또는 컴퓨팅 클러스터에서 작업 영역에 인증을 수행하려면 `MsiAuthentication` 클래스를 사용합니다. 다음 예제는 이 클래스를 사용하여 작업 영역에 인증을 수행하는 방법을 보여줍니다.

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="use-conditional-access"></a>조건부 액세스 사용

관리자는 작업 영역에 로그인하는 사용자에 대해 [AZURE AD 조건부 액세스 정책](../active-directory/conditional-access/overview.md)을 적용할 수 있습니다. 예를 들어 2단계 인증을 요구하거나, 관리 디바이스에서만 로그인을 허용할 수 있습니다. 특히 Azure Machine Learning 작업 영역에 조건부 액세스를 사용하려면 Machine Learning Cloud 앱에 [조건부 액세스 정책을 할당](../active-directory/conditional-access/concept-conditional-access-cloud-apps.md)합니다.

## <a name="next-steps"></a>다음 단계

* [학습에 비밀 사용 방법](how-to-use-secrets-in-runs.md)
* [웹 서비스로 배포된 모델에 인증을 구성하는 방법](how-to-authenticate-web-service.md)
* [웹 서비스로 배포된 Azure Machine Learning 모델 사용](how-to-consume-web-service.md)
