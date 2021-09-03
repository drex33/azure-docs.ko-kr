---
title: Azure CLI에서 Azure Active Directory 서비스 주체 만들기
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication Services에 인증할 애플리케이션 및 서비스 주체를 만듭니다.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 06/30/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: 5b25c0c5d140302d6ea8abbc3eeafaf47fb3b64b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473165"
---
# <a name="quickstart-authenticate-using-azure-active-directory-azure-cli"></a>빠른 시작: Azure Active Directory(Azure CLI)를 사용하여 인증

Azure ID SDK는 Azure SDK 패키지에 대해 Azure AD(Azure Active Directory) 토큰 인증 지원을 제공합니다. 최신 버전의 .NET, Java, Python, JavaScript용 Azure Communication Services SDK는 Azure ID 라이브러리와 통합되어 Azure Communication Services 요청의 권한 부여를 위해 OAuth 2.0 토큰을 획득하는 간단하고 안전한 방법을 제공합니다.

Azure ID SDK의 장점은 애플리케이션이 개발 환경 또는 Azure에서 실행되고 있는지와 관계없이 동일한 코드를 사용하여 여러 서비스에 걸쳐 인증할 수 있도록 지원한다는 점입니다. 

Azure ID SDK는 여러 가지 방법으로 인증할 수 있습니다. 개발에서는 등록된 애플리케이션에 연결된 서비스 주체를 사용하고, 테스트 및 개발에 적합한 환경 변수에 자격 증명이 저장됩니다.

## <a name="prerequisites"></a>필수 구성 요소

 - Azure CLI. [설치 가이드](/cli/azure/install-azure-cli)
 - 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free)

## <a name="setting-up"></a>설치

다른 Azure 리소스에 Active Directory를 사용하는 경우 관리 ID를 사용해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager SDK](../../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [앱 서비스](../../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>개발 환경에서 등록된 애플리케이션 인증

개발 환경에서 웹 브라우저를 통한 Single Sign-On 또는 로그인을 지원하지 않는 경우 등록된 애플리케이션을 사용하여 개발 환경에서 인증할 수 있습니다.

### <a name="creating-an-azure-active-directory-registered-application"></a>Azure Active Directory 등록된 애플리케이션 만들기

Azure CLI에서 등록된 애플리케이션을 만들려면 작업을 수행하려는 Azure 계정에 로그인해야 합니다. 해당 작업을 하기 위해 `az login` 명령을 사용하여 브라우저에 자격 증명을 입력합니다. CLI에서 Azure 계정에 로그인한 후에는 `az ad sp create-for-rbac` 명령을 호출하여 등록된 애플리케이션과 서비스 주체를 만들 수 있습니다.

다음 예에서는 Azure CLI를 사용하여 새 등록된 애플리케이션을 만듭니다.

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

`az ad sp create-for-rbac` 명령은 JSON 형식으로 서비스 주체 속성 목록을 반환합니다. 해당 값을 복사하면 다음 단계에 필요한 환경 변수를 만드는 데 사용할 수 있습니다.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> Azure 역할 할당을 전파하는 데 몇 분 정도 걸릴 수 있습니다.

#### <a name="set-environment-variables"></a>환경 변수 설정

Azure ID SDK는 런타임의 세 가지 환경 변수에서 값을 읽어 애플리케이션을 인증합니다. 다음 표에서는 각 환경 변수에 대해 설정할 값을 설명합니다.

| 환경 변수  | 값                                    |
| --------------------- | ---------------------------------------- |
| `AZURE_CLIENT_ID`     | 생성된 JSON의 `appId` 값    |
| `AZURE_TENANT_ID`     | 생성된 JSON의 `tenant` 값   |
| `AZURE_CLIENT_SECRET` | 생성된 JSON의 `password` 값 |

> [!IMPORTANT]
> 환경 변수를 설정한 후 콘솔 창을 닫았다가 다시 엽니다. Visual Studio 또는 다른 개발 환경을 사용하는 경우 새 환경 변수를 등록하기 위해 다시 시작해야 할 수 있습니다.

이러한 변수가 설정되면 코드에서 DefaultAzureCredential 개체를 사용하여 선택한 서비스 클라이언트에 인증할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인증에 대한 자세한 정보](../../concepts/authentication.md)

다음을 수행할 수도 있습니다.

- [Azure ID 라이브러리에 대한 자세한 정보](/dotnet/api/overview/azure/identity-readme)
