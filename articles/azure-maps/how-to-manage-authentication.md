---
title: Microsoft Azure Maps에서 인증 관리
titleSuffix: Azure Maps
description: Azure Maps 인증에 대해 알아봅니다. 어떤 방식을 어떤 시나리오에서 가장 효과적인지 알아봅니다. 포털을 사용하여 인증 설정을 확인하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/10/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
custom.ms: subject-rbac-steps
ms.openlocfilehash: d087daf38c455fd4d87ea571ff5f3a0ab9ad0527
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454780"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps의 인증 관리

Azure Maps 계정을 만들면 키와 클라이언트 ID가 생성됩니다. 키와 클라이언트 ID는 Azure AD(Azure Active Directory) 인증과 공유 키 인증을 지원하는 데 사용됩니다.

## <a name="view-authentication-details"></a>인증 정보 보기

 >[!IMPORTANT]
 >[공유 키 인증을 사용하여 Azure Maps를 호출](./azure-maps-authentication.md#shared-key-authentication)하는 경우 기본 키를 구독 키로 사용하는 것이 좋습니다. 키 롤링 변경 등의 시나리오에서 보조 키를 사용하는 것이 좋습니다. 자세한 내용은 [Azure Maps의 인증](./azure-maps-authentication.md)을 참조하세요.

Azure Maps 인증 세부 정보를 보려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal 메뉴로 이동합니다. **모든 리소스** 를 선택한 다음, Azure Maps 계정을 선택합니다.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/select-all-resources.png" alt-text="Azure Maps 계정 선택":::

3. 왼쪽 창의 **설정** 에서 **인증** 을 선택합니다.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/view-authentication-keys.png" alt-text="인증 세부 정보":::

## <a name="choose-an-authentication-category"></a>인증 범주 선택

애플리케이션 요구 사항에 따라 애플리케이션 보안을 위한 특정 경로가 있습니다. Azure AD는 다양한 인증 흐름을 지원하는 특정 인증 범주를 정의합니다. 애플리케이션에 가장 적합한 범주를 선택하려면 [애플리케이션 범주](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories)를 참조하세요.

> [!NOTE]
> 공유 키 인증을 사용하는 경우에도 카테고리 및 시나리오를 이해하면 애플리케이션 보안에 도움이 됩니다.

## <a name="choose-an-authentication-and-authorization-scenario"></a>인증 및 권한 부여 시나리오 선택

이 표에서는 Azure Maps의 일반적인 인증 및 권한 부여 시나리오를 간략하게 설명합니다. 링크를 사용하여 각 시나리오의 자세한 구성 정보를 알아봅니다.

> [!IMPORTANT]
> 프로덕션 애플리케이션의 경우 Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 Azure AD를 구현하는 것이 좋습니다.

| 시나리오                                                                                    | 인증 | 권한 부여 | 개발 활동 | 운영 활동 |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [신뢰할 수 있는 디먼/비대화형 클라이언트 애플리케이션](./how-to-secure-daemon-app.md)        | 공유 키     | 해당 없음           | 중간             | 높음               |
| [신뢰할 수 있는 디먼/비대화형 클라이언트 애플리케이션](./how-to-secure-daemon-app.md)        | Azure AD       | 높음          | 낮음                | 중간             |
| [대화형 Single-Sign-On을 사용하는 웹 단일 페이지 애플리케이션](./how-to-secure-spa-users.md) | Azure AD       | 높음          | 중간             | 중간             |
| [비대화형 Sign-On을 사용하는 웹 단일 페이지 애플리케이션](./how-to-secure-spa-app.md)      | Azure AD       | 높음          | 중간             | 중간             |
| [대화형 Single Sign-On을 사용하는 웹 애플리케이션](./how-to-secure-webapp-users.md)          | Azure AD       | 높음          | 높음               | 중간             |
| [IoT 디바이스/입력 제한 디바이스](./how-to-secure-device-code.md)                     | Azure AD       | 높음          | 중간             | 중간             |

## <a name="view-built-in-azure-maps-role-definitions"></a>기본 제공 Azure Maps 역할 정의 보기

기본 제공 Azure Maps 역할 정의를 보려면:

1. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.

2. **역할** 탭을 선택합니다.

3. 검색 상자에 **Azure Maps** 를 입력합니다.

결과에는 Azure Maps에 사용할 수 있는 기본 제공 역할 정의가 표시됩니다.

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-role-definitions.png" alt-text="기본 제공 Azure Maps 역할 정의 보기":::

## <a name="view-role-assignments"></a>역할 할당 보기

Azure Maps에 대한 액세스 권한이 부여된 사용자 및 앱을 보려면 **Access Control(IAM)** 로 이동합니다. 여기에서 **역할 할당** 을 선택하고 **Azure Maps** 를 기준으로 필터링합니다.

1. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.

2. **역할 할당** 탭을 선택합니다.

3. 검색 상자에 **Azure Maps** 를 입력합니다.

결과에는 현재 Azure Maps 역할 할당이 표시됩니다.

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-amrbac.png" alt-text="액세스 권한이 부여된 기본 제공 사용자 및 앱 보기":::

## <a name="request-tokens-for-azure-maps"></a>Azure Maps에 대한 토큰 요청

Azure AD 토큰 엔드포인트에서 토큰을 요청합니다. Azure AD 요청에서 다음 세부 정보를 사용합니다.

| Azure 환경      | Azure AD 토큰 엔드포인트             | Azure 리소스 ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure 퍼블릭 클라우드     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government 클라우드 | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

사용자 및 서비스 주체에 대한 Azure AD의 액세스 토큰 요청 방법에 관한 자세한 내용은 [Azure AD에 대한 인증 시나리오](../active-directory/develop/authentication-vs-authorization.md)를 참조하세요.  특정 시나리오를 보려면 [시나리오 표](./how-to-manage-authentication.md#choose-an-authentication-and-authorization-scenario)를 참조하세요.

## <a name="manage-and-rotate-shared-keys"></a>공유 키 관리 및 회전

Azure Maps 구독 키는 Azure Maps 계정의 루트 암호와 비슷합니다. 항상 구독 키를 보호해야 합니다. Azure Key Vault를 사용하여 키를 안전하게 관리하고 회전합니다. 액세스 키를 다른 사용자에게 배포하거나, 하드 코딩하거나, 다른 사용자가 액세스할 수 있는 일반 텍스트로 저장하지 않도록 합니다. 키가 손상되었을 수 있다고 생각되면 키를 회전합니다.

> [!NOTE]
> 가능한 경우 공유 키 대신 Azure AD를 사용하여 요청에 권한을 부여하는 것이 좋습니다. Azure AD는 공유 키보다 더 나은 보안을 제공하며 사용하기가 더 쉽습니다.

### <a name="manually-rotate-subscription-keys"></a>수동으로 구독 키 회전

Azure Maps 계정을 안전하게 유지하려면 구독 키를 주기적으로 회전하는 것이 좋습니다. 가급적 Azure Key Vault를 사용하여 액세스 키를 관리하십시오. Key Vault를 사용하지 않는 경우 키를 수동으로 회전해야 합니다.

키를 회전할 수 있도록 두 개의 구독 키가 할당됩니다. 두 개의 키가 있으면 애플리케이션이 프로세스 전체에서 Azure Maps에 대한 액세스 권한을 유지할 수 있습니다.

Azure Portal에서 Azure Maps 구독 키를 회전하려면 다음을 수행합니다.

1. Azure Maps 계정의 보조 키를 참조하도록 애플리케이션 코드를 업데이트하고 배포합니다.
2. [Azure Portal](https://portal.azure.com/)에서 Azure Maps 계정으로 이동합니다.
3. **설정** 에서 **인증** 을 선택합니다.
4. Azure Maps 계정에 대한 기본 키를 다시 생성하려면 기본 키 옆에 있는 **다시 생성** 단추를 선택합니다.
5. 애플리케이션 코드를 업데이트하여 새 기본 키를 참조하고 배포합니다.
6. 같은 방식으로 보조 키를 다시 생성합니다.

> [!WARNING]
> 모든 애플리케이션에서 동시에 키 중 하나만 사용하는 것이 좋습니다. 어떤 경우에는 키 1을 사용하고, 다른 경우에는 키 2를 사용하면 어떤 애플리케이션이 액세스 권한을 상실해야만 키를 회전할 수 있게 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Maps 계정에 대한 API 사용 현황 메트릭을 확인하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합하는 방법을 보여 주는 샘플을 살펴보세요.

> [!div class="nextstepaction"]
> [Azure AD 인증 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
