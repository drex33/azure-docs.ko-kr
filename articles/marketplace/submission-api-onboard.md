---
title: 파트너 센터 제출 API 온보딩 개요
description: 파트너 센터 제출 API 온보딩에 대한 개요입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: 5c6431fef8c91b41f19d27d19b59efca14986b7f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058754"
---
# <a name="partner-center-submission-api-onboarding"></a>파트너 센터 제출 API 온보딩

파트너 센터 제출 API를 사용하여 프로그래밍 방식으로 쿼리하고, 제출을 만들고, 상업용 Marketplace 제안을 게시합니다. 이 API는 계정이 많은 제품을 관리하고, 이러한 제품에 대한 제출 프로세스를 자동화하고 최적화하려는 경우에 유용합니다.

## <a name="api-prerequisites"></a>API 사전 요구 사항

파트너 센터 제출 API에는 몇 가지 프로그래밍 방식 자산이 필요합니다.

- Azure Active Directory 애플리케이션
- azure AD(Azure Active Directory) 액세스 토큰

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>1단계: 파트너 센터 제출 API를 사용하기 위한 필수 조건 완료

파트너 센터 제출 API를 호출하는 코드 작성을 시작하기 전에 다음 필수 구성을 완료해야 합니다.

- 사용자(또는 조직)는 Azure AD 디렉터리가 있어야 하고 디렉터리에 대한 [전역 관리자](../active-directory/roles/permissions-reference.md) 권한이 있어야 합니다. Microsoft 365 또는 Microsoft의 다른 비즈니스 서비스를 이미 사용하는 경우 이미 Azure AD 디렉터리가 있습니다. 그렇지 않으면 추가 비용 없이 파트너 센터 [새 Azure AD를 만들](manage-tenants.md#create-a-new-tenant) 수 있습니다.
- Azure [AD 애플리케이션을](manage-aad-apps.md) 파트너 센터 계정에 연결하고 테넌트 ID, 클라이언트 ID 및 키를 얻어야 합니다. Microsoft Store 제출 API 호출에 사용할 Azure AD 액세스 토큰을 가져오려면 이러한 토큰이 필요합니다.

#### <a name="associate-an-azure-ad-application-with-your-partner-center-account"></a>Azure AD 애플리케이션을 파트너 센터 계정에 연결

파트너 센터 제출 API를 사용하려면 Azure AD 애플리케이션을 파트너 센터 계정에 연결하고, 애플리케이션에 대한 테넌트 ID 및 클라이언트 ID를 검색하고, 키를 생성해야 합니다. Azure AD 애플리케이션은 파트너 센터 제출 API를 호출하려는 앱 또는 서비스를 나타냅니다. API에 전달할 Azure AD 액세스 토큰을 가져오려면 테넌트 ID, 클라이언트 ID 및 키가 필요합니다.

> [!NOTE]
> 이 작업은 한 번만 수행하면 됩니다. 테넌트 ID, 클라이언트 ID 및 키가 있으면 새 Azure AD 액세스 토큰을 만들어야 할 때마다 다시 사용할 수 있습니다.

1. 파트너 센터 조직의 파트너 센터 [계정을](manage-tenants.md) 조직의 Azure AD 디렉터리에 연결합니다.

1. 파트너 센터 **계정 설정** 섹션의 **사용자** 페이지에서 파트너 센터 계정에 대한 제출에 액세스하는 데 사용할 앱 또는 서비스를 나타내는 [Azure AD 애플리케이션을 추가합니다.](manage-aad-apps.md) 이 애플리케이션을 **관리자** 역할에 할당해야 합니다. 애플리케이션이 Azure AD 디렉터리에 아직 없는 경우 파트너 센터 [새 Azure AD 애플리케이션을 만듭니다.](manage-aad-apps.md#add-new-azure-ad-applications)

1. **사용자** 페이지로 돌아가서 Azure AD 애플리케이션의 이름을 선택하여 애플리케이션 설정으로 이동하고 **테넌트 ID** 및 **클라이언트 ID** 값을 복사합니다.

1. **새 키 추가를** 선택합니다. 다음 화면에서 **키** 값을 복사합니다. 이 페이지를 나가면 이 정보에 다시 액세스할 수 없습니다. 자세한 내용은 [Azure AD 애플리케이션 키 관리](manage-aad-apps.md#manage-keys-for-an-azure-ad-application)를 참조하세요.

### <a name="step-2-obtain-an-azure-ad-access-token"></a>2단계: Azure AD 액세스 토큰 가져오기

파트너 센터 제출 API에서 메서드를 호출하기 전에 먼저 Azure AD 액세스 토큰을 획득하여 API에서 각 메서드의 **권한 부여** 헤더에 전달해야 합니다. 액세스 토큰은 발급 후 60분 후에 만료됩니다. 그런 다음, 나중에 API를 호출할 때 사용할 수 있도록 새로 고칠 수 있습니다.

액세스 토큰을 얻으려면 [클라이언트 자격 증명을 사용한 서비스 간 호출](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)의 지침에 따라 `HTTP POST`를 `https://login.microsoftonline.com/<tenant_id>/oauth2/token` 엔드포인트로 보냅니다. 다음은 샘플 요청입니다.

```json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

POST URI의 tenant_id 값과 client_id 및 client_secret 매개 변수의 경우 이전 섹션의 파트너 센터 검색한 애플리케이션의 테넌트 ID, 클라이언트 ID 및 키를 지정합니다. 리소스 매개 변수에는 https://api.partner.microsoft.com을 지정해야 합니다.

### <a name="step-3-use-the-partner-center-submission-api"></a>3단계: 파트너 센터 제출 API 사용

Azure AD 액세스 토큰이 있으면 파트너 센터 제출 API에서 메서드를 호출합니다. 제출을 만들거나 업데이트하려면 일반적으로 파트너 센터 제출 API에서 특정 순서로 여러 메서드를 호출합니다. 각 시나리오 및 각 메서드의 구문에 대한 자세한 내용은 [Ingestion API](https://apidocs.microsoft.com/services/partneringestion/) swagger를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 위에서 설명한 대로 파트너 센터 제출 API 사용을 시작합니다.
