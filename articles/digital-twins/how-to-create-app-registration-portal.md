---
title: 앱 등록 만들기(포털)
titleSuffix: Azure Digital Twins
description: 클라이언트 앱에 대한 인증 옵션으로 Azure Portal을 사용하여 Azure AD 앱 등록을 만드는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 9/8/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8350ff149b54ef8cf8f4061abd64df73dc688962
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653118"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins-portal"></a>Azure Digital Twins에서 사용할 앱 등록 만들기(포털)

[!INCLUDE [digital-twins-create-app-registration-selector.md](../../includes/digital-twins-create-app-registration-selector.md)]

Azure Digital Twins 인스턴스를 사용할 때 [클라이언트 앱 코딩](tutorial-code.md)에서 빌드된 사용자 지정 클라이언트 앱과 같은 클라이언트 애플리케이션을 통해 해당 인스턴스와 상호 작용하는 것이 일반적입니다. 이러한 애플리케이션은 상호 작용하기 위해 Azure Digital Twins를 사용하여 인증해야 하며, 앱에서 사용할 수 있는 일부 [인증 메커니즘](how-to-authenticate-client.md)에는 [Azure AD(Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md) **앱 등록** 이 포함됩니다.

앱 등록이 모든 인증 시나리오에 필요한 것은 아닙니다. 그러나 앱 등록을 요구하는 인증 전략 또는 코드 샘플을 사용하는 경우 이 문서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 이를 설정하는 방법을 보여 줍니다. 또한 앱 등록을 사용하여 인증하기 위해 필요한 [중요한 값을 수집](#collect-important-values)하는 방법도 다룹니다.

## <a name="azure-ad-app-registrations"></a>Azure AD 앱 등록

[Azure AD(Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md)는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다. Azure AD에서 **앱 등록** 설정은 클라이언트 앱에 Azure Digital Twins에 대한 액세스 권한을 부여하는 한 가지 방법입니다.

이 앱 등록에서는 [Azure Digital Twins API](concepts-apis-sdks.md)에 대한 액세스 권한을 구성합니다. 이후에 클라이언트 앱은 등록의 **클라이언트 및 테넌트 ID 값** 을 사용하여 앱 등록에 대해 인증할 수 있으며, 그 결과 API에 대해 구성된 액세스 권한이 부여됩니다.

>[!TIP]
> 필요할 때마다 새 앱 등록을 설정 *하거나*, 한 번만 수행하고 이를 필요로 하는 모든 시나리오에서 공유될 단일 앱 등록을 설정하는 것이 좋습니다.

## <a name="create-the-registration"></a>등록 만들기

Azure Portal에서 [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)로 이동하여 시작합니다. 이 링크를 사용하거나 포털 검색 창에서 찾을 수 있습니다. 서비스 메뉴에서 *앱 등록* 을 선택한 다음, *+ 새 등록* 을 선택합니다.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="'앱 등록' 페이지에서 새 등록을 만드는 단계를 보여주는 Azure Portal의 Azure AD 서비스 페이지 스크린샷.":::

뒤에 나오는 *애플리케이션 등록* 페이지에서 요청된 값을 입력합니다.
* **이름**: 등록과 연결할 Azure AD 애플리케이션 표시 이름
* **지원되는 계정 유형**: *이 조직 디렉터리의 계정만(기본 디렉터리만 - 단일 테넌트)* 선택
* **리디렉션 URI**: Azure AD 애플리케이션에 대한 *Azure AD 애플리케이션 회신 URL* `http://localhost`에 대해 *퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)* URI를 추가합니다.

완료되면 등록 단추를 선택합니다.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="설명된 값이 채워진 Azure Portal의 '애플리케이션 등록' 페이지 스크린샷.":::

등록 설정이 완료되면 포털에서 세부 정보 페이지로 리디렉션합니다.

## <a name="collect-important-values"></a>중요한 값 수집

다음으로 앱 등록을 사용하여 클라이언트 애플리케이션을 인증하기 위해 필요한 앱 등록에 대한 몇 가지 중요한 값을 수집합니다. 다음과 같은 값이 여기에 포함됩니다.
* **리소스 이름**
* **클라이언트 ID**
* **테넌트 ID**
* **클라이언트 암호**

Azure Digital Twins를 사용하기 위해 **리소스 이름** 은 `http://digitaltwins.azure.net`입니다.

다음 섹션에서는 다른 값을 찾는 방법을 설명합니다.

### <a name="collect-client-id-and-tenant-id"></a>클라이언트 ID 및 테넌트 ID 수집

**클라이언트 ID** 및 **테넌트 ID** 값은 Azure Portal의 앱 등록 세부 정보 페이지에서 수집할 수 있습니다.

:::image type="content" source="media/how-to-create-app-registration/client-id-tenant-id.png" alt-text="앱 등록에 대한 중요한 값을 보여주는 Azure Portal 보기의 스크린샷."  lightbox="media/how-to-create-app-registration/client-id-tenant-id.png":::

**사용자의** 페이지에 표시된 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID** 를 기록해 둡니다.

### <a name="collect-client-secret"></a>클라이언트 암호 수집

앱 등록을 위한 **클라이언트 암호** 를 설정하려면 Azure Portal의 앱 등록 페이지에서 시작합니다. 

1. 등록 메뉴에서 **인증서 및 암호** 를 선택한 다음 **+ 새 클라이언트 암호** 를 선택합니다.

    :::image type="content" source="media/how-to-create-app-registration/client-secret.png" alt-text="Azure AD 앱 등록과 '새 클라이언트 암호'에 대한 강조 표시를 보여 주는 Azure Portal의 스크린샷.":::

1. 설명 및 만료에 원하는 값을 입력하고 **추가** 를 선택합니다.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-create-app-registration/add-client-secret.png" alt-text="클라이언트 암호를 추가하는 동안의 Azure Portal 스크린샷.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. 클라이언트 암호가 만료 및 값 필드가 있는 **인증서 및 비밀** 페이지에 표시되는지 확인합니다. 

1. 나중에 사용할 수 있도록 **비밀 ID** 및 **값** 을 기록해 둡니다(복사 아이콘을 사용하여 클립보드에 복사할 수도 있음).

    :::image type="content" source="media/how-to-create-app-registration/client-secret-value.png" alt-text="클라이언트 암호 값을 복사하는 방법을 보여 주는 Azure Portal 스크린샷.":::

>[!IMPORTANT]
>다시 검색할 수 없으므로 지금 값을 복사하여 안전한 장소에 저장해야 합니다. 나중에 값을 찾을 수 없는 경우 새 비밀을 만들어야 합니다.

## <a name="provide-azure-digital-twins-api-permission"></a>Azure Digital Twins API 권한 제공

다음으로 기본 권한으로 만든 앱 등록을 Azure Digital Twins API에 구성합니다.

앱 등록에 대한 포털 페이지의 메뉴에서 *API 권한* 을 선택합니다. 다음 권한 페이지에서 *+ 권한 추가* 단추를 선택합니다.

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="'API 권한' 메뉴 옵션과 '권한 추가' 단추가 강조 표시된 Azure Portal의 앱 등록 스크린샷.":::

다음에 나오는 *API 권한 요청* 페이지에서 *내 조직에서 사용하는 API* 탭으로 전환하고 *Azure 디지털 트윈* 을 검색합니다. 검색 결과에서 _**Azure Digital Twins**_ 를 선택하여 Azure Digital Twins API에 대한 권한 할당을 계속합니다.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Azure Digital Twins를 보여주는 Azure Portal의 'API 권한 요청' 페이지 검색 결과 스크린샷.":::

>[!NOTE]
> 구독에 서비스의 이전 공개 미리 보기(2020년 7월 이전)에서 기존 Azure Digital Twins 인스턴스가 있는 경우 대신 _**Azure 스마트 공간 서비스**_ 를 검색하고 선택해야 합니다. 이는 동일한 API 집합의 이전 이름이며(*애플리케이션(클라이언트) ID* 는 위의 스크린샷에서와 동일), 이 단계를 벗어나 사용자 환경이 변경되지 않습니다.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Azure Portal의 Azure 스마트 공간 서비스를 표시하는 'API 권한 요청' 페이지 검색 결과의 스크린샷.":::

다음으로 이러한 API에 부여할 권한을 선택합니다. **읽기 (1)** 권한을 확장하고 *Read.Write* 라는 확인란을 선택하여 이 앱 등록 판독기 및 작성기 권한을 부여합니다.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Azure Portal의 Azure Digital Twins에 대한 'Read.Write' 권한 선택 및 'API 권한 요청' 페이지의 스크린샷.":::

완료되면 *권한 추가* 를 선택합니다.

### <a name="verify-success"></a>성공 확인

API 권한 페이지에서 읽기/쓰기 권한을 반영하는 Azure Digital Twins 항목이 있는지 확인합니다.

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Azure Digital Twins의 '읽기/쓰기 액세스'를 보여주는 Azure Portal의 Azure AD 앱 등록에 대한 API 권한의 스크린샷.":::

API 권한을 추가했을 때 Azure Digital Twins 정보를 사용하여 자동으로 업데이트된 앱 등록의 *manifest.json* 내에서 Azure Digital Twins에 대한 연결을 확인할 수도 있습니다.

이렇게 하려면 메뉴에서 **매니페스트** 를 선택하여 앱 등록의 매니페스트 코드를 확인합니다. 코드 창의 아래쪽으로 스크롤하여 `requiredResourceAccess` 아래에서 다음 필드 및 값을 찾습니다. 
* `"resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0"`
* `"resourceAccess"` > `"id": "4589bd03-58cb-4e6c-b17f-b580e39652f8"`

이러한 값은 아래 스크린샷에 나와 있습니다.

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Azure Portal의 Azure AD 앱 등록에 대한 매니페스트의 스크린샷.":::

이러한 값이 없는 경우 [API 권한 추가에 대한 섹션](#provide-azure-digital-twins-api-permission)의 단계를 다시 시도합니다.

## <a name="other-possible-steps-for-your-organization"></a>조직의 다른 가능한 단계

앱 등록을 성공적으로 설정하려면 조직이 구독 소유자/관리자의 추가 작업을 요구할 수 있습니다. 필요한 단계는 조직의 특정 설정에 따라 다를 수도 있습니다.

다음은 구독의 소유자/관리자가 수행해야 할 수 있는 일반적인 활동입니다. 이 작업 및 다른 작업은 Azure Portal의 [Azure AD 앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 페이지에서 수행할 수 있습니다.
* 앱 등록에 대한 관리자 동의를 부여합니다. 조직이 Azure AD에서 구독 내의 모든 앱 등록에 관해 *관리자 동의가 필요함* 을 전역적으로 활성화했을 수도 있습니다. 그런 경우 소유자/관리자는 앱 등록의 *API 권한* 페이지에서 회사에 대해 이 단추를 선택하여 앱 등록이 유효하도록 설정해야 합니다.

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="API 권한 아래에 '관리자 동의 부여' 단추를 표시하는 Azure Portal의 스크린샷.":::
  - 동의가 성공적으로 부여된 경우 Azure Digital Twins의 항목에는 _**(회사)** 에 대해 부여된_ *상태* 값이 표시되어야 합니다.
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="API 권한에 따라 회사에 부여된 관리자 동의를 보여주는 Azure Portal의 스크린샷.":::
* 퍼블릭 클라이언트 액세스 활성화
* 웹 및 데스크톱 액세스에 대한 특정 회신 URL 설정
* 암시적 OAuth2 인증 흐름 허용

앱 등록 및 다양한 설정 옵션에 대한 자세한 내용은 [Microsoft ID 플랫폼에 애플리케이션 등록](/graph/auth-register-app-v2)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Digital Twins API를 사용하여 클라이언트 애플리케이션을 인증하는 데 사용할 수 있는 Azure AD 앱 등록을 설정했습니다.

다음으로 앱 등록을 사용하는 인증 메커니즘과 이를 사용하지 않는 인증 메커니즘에 대해 읽어보세요.
* [앱 인증 코드 쓰기](how-to-authenticate-client.md)