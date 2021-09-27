---
title: 앱 등록 만들기(CLI)
titleSuffix: Azure Digital Twins
description: 클라이언트 앱에 대한 인증 옵션으로 CLI를 사용하여 Azure AD 앱 등록을 만드는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 9/8/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6b769bbc1b3edb9a680fc6e2823a4e5eaeeaa201
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617757"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins-cli"></a>Azure Digital Twins에서 사용할 앱 등록 만들기(CLI)

[!INCLUDE [digital-twins-create-app-registration-selector.md](../../includes/digital-twins-create-app-registration-selector.md)]

Azure Digital Twins 인스턴스를 사용할 때 사용자 지정 클라이언트 앱 또는 [Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md)와 같은 샘플 등의 클라이언트 애플리케이션을 통해 해당 인스턴스와 상호 작용하는 것이 일반적입니다. 이러한 애플리케이션은 상호 작용하기 위해 Azure Digital Twins를 사용하여 인증해야 하며, 앱에서 사용할 수 있는 일부 [인증 메커니즘](how-to-authenticate-client.md)에는 [Azure AD(Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md) **앱 등록** 이 포함됩니다.

앱 등록이 모든 인증 시나리오에 필요한 것은 아닙니다. 그러나 앱 등록을 요구하는 인증 전략 또는 코드 샘플을 사용하는 경우 이 문서에서는 [Azure CLI](/cli/azure/what-is-azure-cli)를 사용하여 이를 설정하는 방법을 보여 줍니다. 또한 앱 등록을 사용하여 인증하기 위해 필요한 [중요한 값을 수집](#collect-important-values)하는 방법도 다룹니다.

## <a name="azure-ad-app-registrations"></a>Azure AD 앱 등록

[Azure AD(Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md)는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다. Azure AD에서 **앱 등록** 설정은 클라이언트 앱에 Azure Digital Twins에 대한 액세스 권한을 부여하는 한 가지 방법입니다.

이 앱 등록에서는 [Azure Digital Twins API](concepts-apis-sdks.md)에 대한 액세스 권한을 구성합니다. 이후에 클라이언트 앱은 등록의 **클라이언트 및 테넌트 ID 값** 을 사용하여 앱 등록에 대해 인증할 수 있으며, 그 결과 API에 대해 구성된 액세스 권한이 부여됩니다.

>[!TIP]
> 필요할 때마다 새 앱 등록을 설정 *하거나*, 한 번만 수행하고 이를 필요로 하는 모든 시나리오에서 공유될 단일 앱 등록을 설정하는 것이 좋습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-manifest"></a>매니페스트 만들기

먼저 앱 등록에서 Azure Digital Twins API에 액세스하는 데 필요한 특정 서비스 정보가 포함된 파일을 만듭니다. 나중에 앱 등록을 만들 때 이 파일을 전달하여 Azure Digital Twins 권한을 설정합니다.

컴퓨터에서 **manifest.json** 이라는 새 .json 파일을 만듭니다. 이 텍스트를 파일에 복사합니다.

```json
[
    {
        "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
        "resourceAccess": [
            {
                "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
                "type": "Scope"
            }
        ]
    }
]
```

정적 `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` 값은 앱 등록에서 Azure Digital Twins API에 액세스하는 데 필요한 Azure Digital Twins 서비스 엔드포인트의 리소스 ID입니다.
 
완료된 파일을 저장합니다.

### <a name="upload-to-cloud-shell"></a>Cloud Shell에 업로드

다음으로 앱 등록을 구성할 때 Cloud Shell 명령에서 액세스할 수 있도록 방금 만든 매니페스트 파일을 Cloud Shell에 업로드합니다.

파일을 업로드하려면 브라우저의 Cloud Shell 창으로 이동합니다. "파일 업로드/다운로드" 아이콘을 선택하고, "업로드"를 선택합니다.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Azure Cloud Shell의 스크린샷입니다. 업로드 아이콘이 강조 표시됩니다.":::

머신에서 **manifest.json** 파일로 이동하여 "열기"를 선택합니다. 그러면 파일이 Cloud Shell 스토리지의 루트에 업로드됩니다.

## <a name="create-the-registration"></a>등록 만들기

이 섹션에서는 Cloud Shell 명령을 실행하여 다음 설정으로 앱 등록을 만듭니다.
* 선택한 이름
* 기본 디렉터리(단일 테넌트)의 계정에만 사용 가능
* `http://localhost`의 웹 회신 URL
* Azure Digital Twins API에 대한 읽기/쓰기 권한

다음 명령을 실행하여 등록을 만듭니다.

```azurecli-interactive
az ad app create --display-name <app-registration-name> --available-to-other-tenants false --reply-urls http://localhost --native-app --required-resource-accesses "@manifest.json"
```

명령의 출력은 만든 앱 등록에 대한 정보입니다. 

## <a name="verify-success"></a>성공 확인

`az ad app create` 명령의 출력에서 다음 필드를 찾고 해당 값이 아래 스크린샷에 표시된 것과 일치하는지 확인하여 Azure Digital Twins 권한이 부여된 것을 확인할 수 있습니다.

:::image type="content" source="media/how-to-create-app-registration/cli-required-resource-access.png" alt-text="앱 등록 만들기 명령의 Cloud Shell 출력의 스크린샷입니다. 'requiredResourceAccess' 아래의 항목이 강조 표시됩니다. 'resourceAppId' 값은 0b07f429-9f4b-4714-9392-cc5e8e80c8b0이고, 'resourceAccess > id' 값은 4589bd03-58cb-4e6c-b17f-b580e39652f8입니다.":::

Azure Portal을 사용하여 앱 등록이 성공적으로 만들어졌는지도 확인할 수 있습니다. 포털 지침은 [성공 확인(포털)](how-to-create-app-registration-portal.md#verify-success)을 참조하세요.

## <a name="collect-important-values"></a>중요한 값 수집

다음으로 앱 등록을 사용하여 클라이언트 애플리케이션을 인증하기 위해 필요한 앱 등록에 대한 몇 가지 중요한 값을 수집합니다. 다음과 같은 값이 여기에 포함됩니다.
* **리소스 이름**
* **클라이언트 ID**
* **테넌트 ID**
* **클라이언트 암호**

Azure Digital Twins를 사용하기 위해 **리소스 이름** 은 `http://digitaltwins.azure.net`입니다.

다음 섹션에서는 다른 값을 찾는 방법을 설명합니다.

### <a name="collect-client-id-and-tenant-id"></a>클라이언트 ID 및 테넌트 ID 수집

인증에 앱 등록을 사용하려면 해당 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID** 를 제공해야 할 수 있습니다. 이 섹션에서는 필요할 때마다 저장하고 사용할 수 있도록 이러한 값을 수집합니다.

`az ad app create` 명령의 출력에서 이러한 값을 모두 찾을 수 있습니다.

애플리케이션(클라이언트) ID:

:::image type="content" source="media/how-to-create-app-registration/cli-app-id.png" alt-text="앱 등록 만들기 명령의 Cloud Shell 출력의 스크린샷입니다. appId 값이 강조 표시됩니다.":::

디렉터리(테넌트) ID:

:::image type="content" source="media/how-to-create-app-registration/cli-tenant-id.png" alt-text="앱 등록 만들기 명령의 Cloud Shell 출력의 스크린샷입니다. odata.metadata의 GUID 값이 강조 표시됩니다.":::

### <a name="collect-client-secret"></a>클라이언트 암호 수집

앱 등록을 위한 **클라이언트 암호** 를 만들려면 이전 섹션의 앱 등록 클라이언트 ID 값이 필요합니다. 다음 CLI 명령의 값을 사용하여 새 비밀을 만듭니다.

```azurecli-interactive
az ad app credential reset --id <client-ID> --append
```

이 명령에 선택적 매개 변수를 추가하여 자격 증명 설명, 종료 날짜 및 기타 세부 정보를 지정할 수도 있습니다. 명령과 해당 매개 변수에 대한 자세한 내용은 [az ad 앱 자격 증명 초기화 설명서](/cli/azure/ad/app/credential?view=azure-cli-latest&preserve-view=true#az_ad_app_credential_reset)를 참조하세요.

이 명령의 출력은 사용자가 만든 클라이언트 암호에 대한 정보입니다. 인증을 위해 클라이언트 암호가 필요할 때 사용할 `password` 값을 복사합니다.

:::image type="content" source="media/how-to-create-app-registration/cli-client-secret.png" alt-text="앱 등록 만들기 명령의 Cloud Shell 출력의 스크린샷입니다. 암호 값이 강조 표시됩니다.":::

>[!IMPORTANT]
>지금 값을 복사하고 다시 검색할 수 없으므로 안전한 장소에 저장해야 합니다. 나중에 값을 찾을 수 없는 경우 새 비밀을 만들어야 합니다.

## <a name="other-possible-steps-for-your-organization"></a>조직의 다른 가능한 단계

앱 등록을 성공적으로 설정하려면 조직이 구독 소유자/관리자의 추가 작업을 요구할 수 있습니다. 필요한 단계는 조직의 특정 설정에 따라 다를 수도 있습니다.

다음은 구독의 소유자 또는 관리자가 수행해야 하는 일반적인 잠재적 활동입니다.
* 앱 등록에 대한 관리자 동의를 부여합니다. 조직이 Azure AD에서 구독 내의 모든 앱 등록에 관해 **관리자 동의가 필요함** 을 전역적으로 활성화했을 수도 있습니다. 이 경우 소유자/관리자가 추가 위임된 권한 또는 애플리케이션 권한을 부여해야 할 수 있습니다.
* 등록을 위해 create 또는 update 명령에 `--set publicClient=true`를 추가하여 퍼블릭 클라이언트 액세스를 활성화합니다.
* `--reply-urls` 매개 변수를 사용하여 웹 및 데스크톱 액세스에 대한 특정 회신 URL을 설정합니다. `az ad` 명령과 함께 이 매개 변수를 사용하는 자세한 내용은 [az ad 앱 설명서](/cli/azure/ad/app?view=azure-cli-latest&preserve-view=true)를 참조하세요.
* `--oauth2-allow-implicit-flow` 매개 변수를 사용하여 암시적 OAuth2 인증 흐름을 허용합니다. `az ad` 명령과 함께 이 매개 변수를 사용하는 자세한 내용은 [az ad 앱 설명서](/cli/azure/ad/app?view=azure-cli-latest&preserve-view=true)를 참조하세요.

앱 등록 및 다양한 설정 옵션에 대한 자세한 내용은 [Microsoft ID 플랫폼에 애플리케이션 등록](/graph/auth-register-app-v2)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Digital Twins API를 사용하여 클라이언트 애플리케이션을 인증하는 데 사용할 수 있는 Azure AD 앱 등록을 설정했습니다.

다음으로 앱 등록을 사용하는 인증 메커니즘과 이를 사용하지 않는 인증 메커니즘에 대해 읽어보세요.
* [앱 인증 코드 쓰기](how-to-authenticate-client.md)