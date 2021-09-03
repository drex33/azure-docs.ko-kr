---
title: Arc 지원 서버를 사용하는 Azure 리소스에 대해 인증
description: 이 문서에서는 Arc 지원 서버에 대한 Azure Instance Metadata Service 지원과 비밀을 사용하여 Azure 리소스 및 로컬에 대해 인증하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: 76f7174792f751322545b1d30bb51476c5339e26
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389913"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Arc 지원 서버를 사용하는 Azure 리소스에 대해 인증

Azure Arc 지원 서버에서 직접 실행되는 애플리케이션 또는 프로세스는 관리 ID를 활용하여 Azure Active Directory 기반 인증을 지원하는 다른 Azure 리소스에 액세스할 수 있습니다. 애플리케이션은 Arc 지원 서버에 대해 시스템이 할당한 ID를 나타내는 [액세스 토큰](../../active-directory/develop/developer-glossary.md#access-token)을 가져와서 이를 '전달자' 토큰으로 사용하여 다른 서비스에 인증할 수 있습니다.

관리 ID에 대한 자세한 설명과 시스템이 할당한 ID와 사용자 할당 ID의 차이점은 [관리 ID 개요](../../active-directory/managed-identities-azure-resources/overview.md) 문서를 참조하세요.

이 문서에서는 서버가 시스템이 할당한 관리 ID를 사용하여 Azure [Key Vault](../../key-vault/general/overview.md)에 액세스하는 방법을 보여줍니다. 부트스트랩 역할을 하는 Key Vault를 사용하면 클라이언트 애플리케이션에서 비밀을 사용하여 Azure AD(Active Directory)로 보호되지 않는 리소스에 액세스할 수 있습니다. 예를 들어 IIS 웹 서버에서 사용하는 TLS/SSL 인증서를 Azure Key Vault에 저장하고 인증서를 Azure 외부의 Windows 또는 Linux 서버에 안전하게 배포할 수 있습니다.

## <a name="security-overview"></a>보안 개요

서버를 Arc 지원 서버에 온보딩하는 동안 Azure VM에 대해 수행되는 작업과 유사한 관리 ID를 사용하여 구성하기 위해 몇 가지 작업이 수행됩니다:

- Azure Resource Manager는 Arc 지원 서버에서 시스템이 할당한 관리 ID를 사용하도록 설정하라는 요청을 받습니다.

- Azure Resource Manager가 Azure AD에 서버 ID에 대한 서비스 주체를 만듭니다. 서비스 주체는 구독이 신뢰하는 Azure AD 테넌트에 생성됩니다.

- Azure Resource Manager는 서비스 주체 클라이언트 ID 및 인증서를 사용하여 [Windows](../../virtual-machines/windows/instance-metadata-service.md) 또는 [Linux](../../virtual-machines/linux/instance-metadata-service.md)에 대한 Azure IMDS(Instance Metadata Service) ID 엔드포인트를 업데이트하고 서버에서 ID를 구성합니다. 엔드 포인트는 잘 알려진 라우팅 불가능한 IP 주소를 사용하여 서버 내에서만 액세스 할 수 있는 REST 엔드 포인트입니다. 이 서비스는 Arc 지원 서버에 대한 메타데이터 정보의 하위 집합을 제공하여 관리 및 구성을 지원합니다.

관리 ID 사용 서버의 환경은 Windows Arc 지원 서버에서 다음 변수로 구성됩니다:

- **IMDS_ENDPOINT**: Arc 지원 서버의 IMDS 엔드포인트 IP 주소 `http://localhost:40342`입니다.

- **IDENTITY_ENDPOINT:** 서비스의 관리 ID `http://localhost:40342/metadata/identity/oauth2/token`에 해당하는 localhost 엔드포인트입니다.

서버에서 실행되는 코드는 Azure Instance Metadata Service 엔드포인트에서 토큰을 요청할 수 있으며, 서버 내에서만 액세스할 수 있습니다.

시스템 환경 변수 **IDENTITY_ENDPOINT** 는 애플리케이션에서 ID 엔드포인트를 발견하는 데 사용됩니다. 애플리케이션은 **IDENTITY_ENDPOINT** 및 **IMDS_ENDPOINT** 값을 검색하여 사용해야합니다. 모든 액세스 수준의 애플리케이션은 엔드포인트에 요청을 수행할 수 있습니다. 메타데이터 응답은 정상적으로 처리되며 컴퓨터의 모든 프로세스에 제공됩니다. 그러나 토큰 노출 요청이 있을 때 클라이언트는 더 높은 권한을 가진 사용자만 사용할 수 있는 데이터에 액세스 할 수 있음을 증명하는 비밀을 제공해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 관리 ID에 대한 이해.
- Arc 지원 서버에 서버를 연결하고 등록했습니다.
- 필요한 리소스 만들기 및 역할 관리 단계를 수행하기 위한 구독 또는 리소스 그룹에서 [소유자 그룹](../../role-based-access-control/built-in-roles.md#owner)**의 구성원입니다.
- 자격 증명을 저장하고 검색하기 위한 Azure Key Vault입니다. KeyVault에 Azure Arc ID 액세스를 할당합니다.

    - Key Vault 만들어지지 않은 경우 [Key Vault 만들기](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-)를 참조하세요.
    - 서버에서 사용하는 관리 ID로 액세스를 구성하려면 [Linux용 액세스 권한 부여](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) 또는 [Windows용 액세스 권한 부여](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access)를 참조하세요. 5단계에서는 Arc 지원 서버의 이름을 입력합니다. PowerShell을 사용하여 완료하려면 [PowerShell을 사용하여 액세스 정책 할당](../../key-vault/general/assign-access-policy-powershell.md)을 참조하세요.

## <a name="acquiring-an-access-token-using-rest-api"></a>REST API를 사용하여 액세스 토큰 획득

Azure 리소스 인증을 위해 시스템이 할당한 관리 ID를 얻고 사용하는 방법은 Azure VM에서 수행하는 방법과 유사합니다.

Arc 지원 Windows 서버의 경우 PowerShell을 사용하여 웹 요청을 호출하여 특정 포트의 로컬 호스트에서 토큰을 가져옵니다. IP 주소 또는 환경 변수 **IDENTITY_ENDPOINT** 를 사용하여 요청을 지정합니다.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

다음 응답은 반환되는 예시입니다:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="PowerShell을 사용하여 액세스 토큰을 성공적으로 검색했습니다.":::

Arc 지원 Linux 서버의 경우 Bash를 사용해서 웹 요청을 호출하여 특정 포트의 로컬 호스트에서 토큰을 가져옵니다. IP 주소 또는 환경 변수 **IDENTITY_ENDPOINT** 를 사용하여 다음 요청을 지정합니다. 이 단계를 완료하려면 SSH 클라이언트가 필요합니다.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

다음 응답은 반환되는 예시입니다:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Bash를 사용하여 액세스 토큰을 성공적으로 검색했습니다.":::

응답에는 Azure의 모든 리소스에 액세스하는 데 필요한 액세스 토큰이 포함되어 있습니다. Azure Key Vault 인증 구성을 완료하려면 [Windows를 사용하여 Key Vault 액세스](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) 또는 [Linux를 사용하여 Key Vault 액세스](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Key Vault에 대한 자세한 내용을 보려면 [Key Vault 개요](../../key-vault/general/overview.md)를 참조하세요.

- [PowerShell을 사용](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)하거나 [Azure CLI](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)를 사용하여 리소스에 관리 ID 액세스를 할당하는 방법을 알아봅니다.