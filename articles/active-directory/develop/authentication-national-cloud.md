---
title: Azure AD 인증 및 국가별 클라우드 | Azure
titleSuffix: Microsoft identity platform
description: 국가별 클라우드의 앱 등록 및 인증 엔드포인트에 관해 알아봅니다.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/03/2021
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: bc62e3a4ae32cf4b2d0a63dd15bdab24ac490d1e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564540"
---
# <a name="national-clouds"></a>국가별 클라우드

국가별 클라우드는 물리적으로 격리된 Azure 인스턴스입니다. 이러한 Azure 지역은 지리적 경계 내에서 데이터 상주, 주권 및 준수 요구 사항이 적용되도록 설계되었습니다.

글로벌 Azure 클라우드를 포함하여 Azure AD(Azure Active Directory)는 다음 국가별 클라우드에 배포됩니다.

- Azure Government
- Azure 중국 21Vianet
- Azure 독일([2021년 10월 29일에 종료](https://www.microsoft.com/cloud-platform/germany-cloud-regions)) [Azure 독일 마이그레이션](#azure-germany-microsoft-cloud-deutschland)에 대해 자세히 알아봅니다.

개별 국가 클라우드와 글로벌 Azure 클라우드는 클라우드 _인스턴스_ 입니다. 각 클라우드 인스턴스는 서로 별개이며 고유한 환경 및 _엔드포인트_ 를 포함합니다. 클라우드 관련 엔드포인트에는 OAuth 2.0 액세스 토큰 및 OpenID Connect ID 토큰 요청 엔드포인트와 앱 관리 및 배포에 대한 URL(예: Azure Portal)이 포함됩니다.

앱을 개발할 때 애플리케이션을 배포할 클라우드 인스턴스의 엔드포인트를 사용합니다.

## <a name="app-registration-endpoints"></a>앱 등록 엔드포인트

각 국가별 클라우드마다 별도의 Azure Portal이 있습니다. 국가별 클라우드에서 Microsoft ID 플랫폼과 애플리케이션을 통합하려면 환경별 각 Azure Portal에서 애플리케이션을 별도로 등록해야 합니다.

다음 표에는 각 국가별 클라우드에 대한 애플리케이션을 등록하는 데 사용되는 Azure AD 엔드포인트의 기본 URL이 나열되어 있습니다.

| 국가별 클라우드                          | Azure Portal 엔드포인트      |
| --------------------------------------- | -------------------------- |
| 미국 정부에 대한 Azure Portal          | `https://portal.azure.us`  |
| 21Vianet으로 운영되는 Azure Portal 중국 | `https://portal.azure.cn`  |
| Azure Portal(글로벌 서비스)           | `https://portal.azure.com` |

## <a name="application-endpoints"></a>애플리케이션 엔드포인트

Azure Portal에서 애플리케이션의 인증 엔드포인트를 찾을 수 있습니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. **Azure Active Directory** 를 선택합니다.
1. **관리** 에서 **앱 등록** 을 선택한 다음 최상위 메뉴에서 **엔드포인트** 를 선택합니다.

   **엔드포인트** 페이지가 표시되고 Azure AD 테넌트에 등록된 애플리케이션에 대한 인증 엔드포인트를 보여 줍니다.

   **애플리케이션 (클라이언트) ID** 와 함께 사용하는 인증 프로토콜과 일치하는 엔드포인트를 사용하여 애플리케이션에 해당하는 인증 요청을 만듭니다.

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 인증 엔드포인트

모든 국가별 클라우드는 각 환경의 사용자를 개별적으로 인증하며 별도의 인증 엔드포인트를 포함합니다.

다음 표에는 각 국가별 클라우드용 토큰을 얻는 데 사용되는 Azure AD 엔드포인트의 기본 URL이 나와 있습니다.

| 국가별 클라우드                      | Azure AD 인증 엔드포인트           |
| ----------------------------------- | ------------------------------------------ |
| 미국 정부의 Azure AD          | `https://login.microsoftonline.us`         |
| 21Vianet으로 운영되는 Azure AD China | `https://login.partner.microsoftonline.cn` |
| Azure AD(글로벌 서비스)           | `https://login.microsoftonline.com`        |

적절한 지역별 기본 URL을 사용하여 Azure AD 권한 부여 또는 토큰 엔드포인트에 대한 요청을 구성할 수 있습니다. 예를 들어 글로벌 Azure의 경우:

- 권한 부여 공통 엔드포인트는 `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`입니다.
- 토큰 공통 엔드포인트는 `https://login.microsoftonline.com/common/oauth2/v2.0/token`입니다.

단일 테넌트 애플리케이션의 경우 이전 URL의 "common"을 테넌트 ID 또는 이름으로 바꿉니다. 예제는 `https://login.microsoftonline.com/contoso.com`입니다.

## <a name="azure-germany-microsoft-cloud-deutschland"></a>Azure 독일(Microsoft Cloud Deutschland)

Azure 독일에서 애플리케이션을 마이그레이션하지 않은 경우 [Azure 독일에서 마이그레이션에 대한 Azure Active Directory 정보](/microsoft-365/enterprise/ms-cloud-germany-transition-azure-ad)에 따라 시작하세요.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

국가별 클라우드 환경에서 Microsoft Graph API를 호출하는 방법을 알아보려면 [국가별 클라우드 배포의 Microsoft Graph](/graph/deployments)로 이동하세요.

글로벌 Azure 클라우드의 일부 서비스 및 기능은 국가별 클라우드와 같은 다른 클라우드 인스턴스에서 사용할 수 없을 수도 있습니다.

지정된 클라우드 인스턴스에서 사용할 수 있는 서비스 및 기능을 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)을 참조하세요.

Microsoft ID 플랫폼을 사용하여 애플리케이션을 빌드하는 방법을 알아보려면 [인증 코드 흐름 자습서를 사용하여 SPA(단일 페이지 애플리케이션)](tutorial-v2-angular-auth-code.md)를 따릅니다. 특히 이 앱은 사용자를 로그인하고 Microsoft Graph API를 호출하는 액세스 토큰을 가져옵니다.

## <a name="next-steps"></a>다음 단계

[국가 클라우드 환경에서 MSAL(Microsoft Authentication Library)](msal-national-cloud.md)을 사용하는 방법에 대해 알아봅니다.

국가별 클라우드 설명서는 다음과 같습니다.

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure 독일(2021년 10월 29일에 종료)](../../germany/index.yml)
