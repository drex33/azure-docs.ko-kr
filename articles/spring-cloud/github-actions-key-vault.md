---
title: GitHub Actions에서 Key Vault를 사용하여 Azure Spring Cloud 인증
description: GitHub Actions를 사용하여 Azure Spring Cloud용 CI/CD 워크플로로 키 자격 증명 모음을 사용하는 방법
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: ca57e3a659d92faa3fd38c233c68067bfe0fcbd2
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567508"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>GitHub Actions에서 Key Vault를 사용하여 Azure Spring Cloud 인증

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

Key Vault는 키를 저장할 수 있는 안전한 장소입니다. Enterprise 사용자는 CI/CD 환경의 자격 증명을 제어 범위 내에 저장해야 합니다. 키 자격 증명 모음에서 자격 증명을 가져오는 키는 리소스 범위로 제한되어야 합니다.  전체 Azure 범위가 아니라 키 자격 증명 모음 범위에만 액세스할 수 있습니다. 이는 건물의 모든 문을 열 수 있는 마스터 키가 아닌 금고를 열 수 있는 키와 같습니다. 다른 키를 사용하여 키를 가져오는 방법이며, CICD 워크플로에서 유용합니다.

## <a name="generate-credential"></a>자격 증명 생성

Key Vault에 액세스하기 위한 키를 생성하려면 로컬 머신에서 아래 명령을 실행합니다.

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```

`--scopes` 매개 변수로 지정된 범위는 리소스에 대한 키 액세스를 제한합니다.  금고에만 액세스할 수 있습니다.

결과를 가지고:

```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```

그런 다음 [GitHub 리포지토리 설정 및 Azure로 인증](./how-to-github-actions.md#set-up-github-repository-and-authenticate)에 설명된 대로 GitHub **비밀** 에 결과를 저장합니다.

## <a name="add-access-policies-for-the-credential"></a>자격 증명에 대한 액세스 정책 추가

위에서 만든 자격 증명은 저장된 내용이 아니라 Key Vault에 대한 일반 정보만 가져올 수 있습니다.  Key Vault에 저장된 암호를 가져오려면 자격 증명에 대한 액세스 정책을 설정해야 합니다.

Azure Portal에서 **Key Vault** 대시보드로 이동하여 **액세스 제어** 메뉴를 선택한 다음 **역할 할당** 탭을 엽니다. **유형** 에 대해 **앱** 을, **범위** 에 대해 `This resource`를 선택합니다.  이전 단계에서 만든 자격 증명이 표시됩니다.

![액세스 정책 설정](./media/github-actions/key-vault1.png)

자격 증명 이름을 복사합니다(예: `azure-cli-2020-01-19-04-39-02`). **액세스 정책** 메뉴를 연 다음, **액세스 정책 추가** 링크를 선택합니다.  **템플릿** 에 대해 `Secret Management`를 선택하고 **보안 주체** 를 선택합니다. **보안 주체**/**선택** 입력 상자에 자격 증명 이름을 붙여 넣습니다.

![선택](./media/github-actions/key-vault2.png)

**액세스 정책 추가** 대화 상자에서 **추가** 단추를 선택한 다음, **저장** 을 선택합니다.

## <a name="generate-full-scope-azure-credential"></a>전체 범위 Azure 자격 증명 생성

건물의 모든 문을 여는 마스터 키입니다. 절차는 이전 단계와 유사하지만 여기서는 마스터 키를 생성하는 범위를 변경합니다.

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

다시, 결과는 다음과 같습니다.

```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```

전체 JSON 문자열을 복사합니다.  **Key Vault** 대시보드로 돌아갑니다. **비밀** 메뉴를 열고 **생성/가져오기** 단추를 선택합니다. 암호 이름(예: `AZURE-CREDENTIALS-FOR-SPRING`)을 입력합니다. JSON 자격 증명 문자열을 **값** 입력 상자에 붙여넣습니다. 값 입력 상자는 여러 줄 텍스트 영역이 아니라 한 줄 텍스트 필드임을 알 수 있습니다.  여기에 전체 JSON 문자열을 붙여넣을 수 있습니다.

![전체 범위 자격 증명](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>GitHub Actions에서 자격 증명 결합

CICD 파이프라인 실행 시 사용되는 자격 증명을 설정 합니다.

```console
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>다음 단계

* [Spring Cloud GitHub Actions](./how-to-github-actions.md)
