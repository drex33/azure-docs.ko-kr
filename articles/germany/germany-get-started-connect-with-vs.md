---
title: Visual Studio를 사용하여 Azure 독일에 연결 | Microsoft Docs
description: Visual Studio를 사용하여 Azure 독일에서 구독을 관리하는 방법에 대한 정보
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: f5749ed9d2080a92a33993d8131595359110b75e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122535443"
---
# <a name="connect-to-azure-germany-by-using-visual-studio"></a>Visual Studio를 사용하여 Azure 독일에 연결

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

개발자는 Visual Studio를 사용하여 솔루션을 빌드하는 동안 Azure 구독을 쉽게 관리합니다. 현재 Visual Studio 사용자 인터페이스에서는 Azure 독일에 대한 연결을 구성할 수 없습니다.

## <a name="visual-studio-2017-and-visual-studio-2019"></a>Visual Studio 2017 및 Visual Studio 2019

Visual Studio에서 Azure 독일에 연결하려면 구성 파일이 필요합니다. 이 파일이 있으면 Visual Studio는 글로벌 Azure 대신 Azure 독일에 연결합니다.

### <a name="create-a-configuration-file-for-azure-germany"></a>Azure 독일용 구성 파일 만들기

다음 콘텐츠로 *AadProvider.Configuration.json* 이라는 파일을 만듭니다.

```json
{
  "AuthenticationQueryParameters":null,
  "AsmEndPoint":"https://management.microsoftazure.de/",
  "Authority":"https://login.microsoftonline.de/",
  "AzureResourceManagementEndpoint":"https://management.microsoftazure.de/",
  "AzureResourceManagementAudienceEndpoints":["https://management.core.cloudapi.de/"],
  "ClientIdentifier":"872cd9fa-d31f-45e0-9eab-6e460a02d1f1",
  "EnvironmentName":"BlackForest",
  "GraphEndpoint":"https://graph.cloudapi.de",
  "MsaHomeTenantId":"f577cd82-810c-43f9-a1f6-0cc532871050",
  "NativeClientRedirect":"urn:ietf:wg:oauth:2.0:oob",
  "PortalEndpoint":"https://portal.core.cloudapi.de/",
  "ResourceEndpoint":"https://management.microsoftazure.de/",
  "ValidateAuthority":true,
  "VisualStudioOnlineEndpoint":"https://app.vssps.visualstudio.com/",
  "VisualStudioOnlineAudience":"499b84ac-1321-427f-aa17-267ca6975798"
}
```

### <a name="update-visual-studio-for-azure-germany"></a>Azure 독일용 Visual Studio 업데이트

1. Visual Studio를 닫습니다.
1. *%localappdata%\\.IdentityService\AadConfigurations* 에 *AadProvider.Configuration.json* 을 배치합니다. 이 폴더가 없으면 만듭니다.
1. Visual Studio를 시작하고 Azure 독일 계정 사용을 시작합니다.

> [!NOTE]
> 구성 파일을 사용하면 Azure 독일 구독에만 액세스할 수 있습니다. 현재 Visual Studio가 글로벌 Azure 대신 Azure 독일에 연결되어 있기 때문에 이전에 구성했지만 작동하지 않는 구독이 계속 표시됩니다. 글로벌 Azure에 연결하려면 파일을 제거합니다.
>

### <a name="revert-a-visual-studio-connection-to-azure-germany"></a>Azure 독일에 대한 Visual Studio 연결 되돌리기

Visual Studio에서 글로벌 Azure에 연결할 수 있도록 하려면 Azure 독일에 연결할 수 있도록 하는 구성 파일을 제거해야 합니다.

1. Visual Studio를 닫습니다.
1. *%localappdata%\.IdentityService\AadConfigurations* 폴더를 삭제하거나 이름을 바꿉니다.
1. Visual Studio를 다시 시작하고 글로벌 Azure 계정 사용을 시작합니다.

> [!NOTE]
> 이 구성을 되돌린 후에는 Azure 독일 구독에 더 이상 액세스할 수 없습니다.
>

## <a name="visual-studio-2015"></a>Visual Studio 2015

Visual Studio 2015에서 Azure 독일에 연결하려면 레지스트리를 변경해야 합니다. 이 레지스트리 키를 설정하면 Visual Studio가 글로벌 Azure 대신 Azure 독일에 연결합니다.

### <a name="update-visual-studio-2015-for-azure-germany"></a>Azure 독일용 Visual Studio 2015 업데이트

Visual Studio에서 Azure 독일에 연결할 수 있도록 하려면 레지스트리를 업데이트해야 합니다.

1. Visual Studio를 닫습니다.
1. *VisualStudioForAzureGermany.reg* 라는 텍스트 파일을 만듭니다.
1. *VisualStudioForAzureGermany.reg* 에 다음 텍스트를 복사하여 붙여넣습니다.

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"="https://login.microsoftonline.de/"
"adaluri"="https://management.microsoftazure.de"
"AzureRMEndpoint"="https://management.microsoftazure.de"
"AzureRMAudienceEndpoint"="https://management.core.cloudapi.de"
"EnableAzureRMIdentity"="true"
"GraphUrl"="graph.cloudapi.de"
"AadApplicationTenant"="f577cd82-810c-43f9-a1f6-0cc532871050"
```

1. 해당 파일을 저장한 다음 두 번 클릭하여 실행합니다. 파일을 레지스트리에 병합할지 묻는 메시지가 표시됩니다.
1. Visual Studio를 시작하고 Azure 독일 계정으로 [클라우드 탐색기](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer)를 사용합니다.

> [!NOTE]
> 이 레지스트리 키가 설정되면 Azure 독일 구독에만 액세스할 수 있습니다. 현재 Visual Studio가 글로벌 Azure 대신 Azure 독일에 연결되어 있기 때문에 이전에 구성했지만 작동하지 않는 구독이 계속 표시됩니다. 글로벌 Azure에 연결하려면 변경 사항을 되돌립니다.
>

### <a name="revert-a-visual-studio-2015-connection-to-azure-germany"></a>Azure 독일에 대한 Visual Studio 2015 연결 되돌리기

Visual Studio에서 글로벌 Azure에 연결할 수 있도록 하려면 Azure 독일에 연결할 수 있도록 하는 레지스트리 설정을 제거해야 합니다.

1. Visual Studio를 닫습니다.
1. *VisualStudioForAzureGermany_Remove.reg* 라는 텍스트 파일을 만듭니다.
1. *다음 텍스트를 복사하여 VisualStudioForAzureGermany_Remove.reg* 에 붙여넣습니다.

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
"AadInstance"=-
"adaluri"=-
"AzureRMEndpoint"=-
"AzureRMAudienceEndpoint"=-
"EnableAzureRMIdentity"=-
"GraphUrl"=-
```

1. 해당 파일을 저장한 다음 두 번 클릭하여 실행합니다. 파일을 레지스트리에 병합할지 묻는 메시지가 표시됩니다.
1. Visual Studio를 시작합니다.

> [!NOTE]
> 이 레지스트리 키를 되돌리면 Azure 독일 구독이 나타나지만 액세스할 수 없습니다. 안전하게 제거할 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

Azure 독일 연결에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [PowerShell을 사용하여 Azure 독일에 연결](./germany-get-started-connect-with-ps.md)
* [Azure CLI를 사용하여 Azure 독일에 연결](./germany-get-started-connect-with-cli.md)
* [Azure Portal을 사용하여 Azure 독일에 연결](./germany-get-started-connect-with-portal.md)