---
title: Azure 단추에 배포
description: 단추를 사용하여 GitHub 리포지토리에서 Azure Resource Manager 템플릿을 배포합니다.
ms.topic: conceptual
ms.date: 12/03/2021
ms.openlocfilehash: fabfc565693b8bf44c837db31d2f70507a8188a6
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133543781"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>배포 템플릿을 사용하여 GitHub 리포지토리에서 템플릿 배포

이 문서에서는 **Azure에 배포** 단추를 사용하여 GitHub 리포지토리에서 ARM JSON 템플릿을 배포하는 방법을 설명합니다. GitHub 리포지토리의 _README.md_ 파일에 바로 단추를 추가할 수 있습니다. 또는 리포지토리를 참조하는 웹 페이지에 단추를 추가할 수 있습니다. 이 메서드는 [Bicep 파일 를](../bicep/overview.md)지원하지 않습니다.

배포 범위는 템플릿 스키마에 따라 결정됩니다. 자세한 내용은 다음을 참조하세요.

- [resource groups](deploy-to-resource-group.md)
- [구독](deploy-to-subscription.md)
- [관리 그룹](deploy-to-management-group.md)
- [테넌트](deploy-to-tenant.md)

## <a name="use-common-image"></a>일반 이미지 사용

웹 페이지 또는 리포지토리에 단추를 추가하려면 다음 이미지를 사용합니다.

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

이미지는 다음과 같이 표시됩니다.

![Azure 단추에 배포](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>템플릿 배포용 URL 만들기

템플릿 URL을 만들려면 리포지토리의 템플릿에 대한 원시 URL로 시작합니다. 원시 URL을 보려면 **원시** 를 선택합니다.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="원시 선택":::

URL 형식은 다음과 같습니다.

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json
```

그런 다음 URL을 URL로 인코딩된 값으로 변환합니다. 온라인 인코더를 사용하거나 명령을 실행할 수 있습니다. 다음 PowerShell 예제에는 URL로 값을 인코딩하는 방법이 나와 있습니다.

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

URL을 인코딩할 때 예제 URL의 값은 다음과 같습니다.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json
```

각 링크는 동일한 기준 URL로 시작합니다.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

URL로 인코딩된 템플릿 링크를 기본 URL의 끝에 추가합니다.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json
```

사용자에게는 링크의 전체 URL이 있습니다.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

GitHub 리포지토리 대신 [Azure Repos Git](/azure/devops/repos/git/)를 사용하는 경우 **Azure에 배포** 단추를 계속 사용할 수 있습니다. 리포지토리가 공용인지 확인합니다. [항목 작업](/rest/api/azure/devops/git/items/get)을 사용하여 템플릿을 가져옵니다. 요청의 형식은 다음과 같아야 합니다.

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

이 요청 URL을 인코딩합니다.

## <a name="create-deploy-to-azure-button"></a>Azure 단추에 배포 만들기

마지막으로 링크와 이미지를 함께 배치합니다.

GitHub 리포지토리 또는 웹 페이지의 _README.md_ 파일에서 Markdown을 사용하여 단추를 추가하려면 다음을 사용합니다.

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json)
```

HTML의 경우 다음을 사용합니다.

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

Azure Repos Git의 경우 단추의 형식은 다음과 같습니다.

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>템플릿 배포

전체 솔루션을 테스트하려면 다음 단추를 선택합니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.storage%2Fstorage-account-create%2Fazuredeploy.json)

포털에는 매개 변수 값을 쉽게 입력할 수 있는 창이 표시됩니다. 매개 변수는 템플릿의 기본값으로 미리 채워져 있습니다. 템플릿에 정의된 카멜식 대/소문자 매개 변수 이름 *storageAccountType* 은 포털에 표시될 때 공백으로 구분된 문자열로 바뀝니다.

![포털을 사용하여 배포](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>다음 단계

- 템플릿에 대한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.