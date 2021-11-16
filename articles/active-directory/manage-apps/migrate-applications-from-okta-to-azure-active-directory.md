---
title: OKTA에서 Azure Active Directory로 애플리케이션을 마이그레이션하는 자습서
titleSuffix: Active Directory
description: OKTA에서 Azure Active Directory로 애플리케이션을 마이그레이션하는 방법을 알아봅니다.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 25724ab674879d460c11e52a8360cb2c76a23a26
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451619"
---
# <a name="tutorial-migrate-your-applications-from-okta-to-azure-active-directory"></a>자습서: OKTA에서 Azure Active Directory로 애플리케이션 마이그레이션

이 자습서에서는 애플리케이션을 OKTA에서 Azure AD(Azure Active Directory)로 마이그레이션하는 방법을 알아봅니다.

## <a name="create-an-inventory-of-current-okta-applications"></a>현재 OKTA 애플리케이션의 인벤토리 만들기

마이그레이션을 시작하기 전에 현재 환경 및 애플리케이션 설정을 문서화해야 합니다. OKTA API를 사용하여 중앙 위치에서 이 정보를 수집할 수 있습니다. API를 사용하려면 [Postman](https://www.postman.com/)과 같은 API 탐색기 도구가 필요합니다.

애플리케이션 인벤토리를 만들려면 다음 단계를 따릅니다.

1. Postman 앱을 설치합니다. 그런 다음, OKTA 관리 콘솔에서 API 토큰을 생성합니다.

1. API 대시보드의 **보안** 아래에서 **토큰** > **토큰 만들기** 를 선택합니다.

   ![토큰을 만드는 단추를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/token-creation.png)

1. 토큰 이름을 삽입한 다음, **토큰 만들기** 를 선택합니다.

   ![토큰 이름을 지정할 위치를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/token-created.png)

1. 토큰 값을 기록하고 저장합니다. **네, 이해했습니다.** 를 선택한 후에는 액세스할 수 없습니다.

   ![토큰 값 상자를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/record-created.png)

1. Postman 앱의 작업 영역에서 **가져오기** 를 선택합니다.

   ![가져오기 API를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/import-api.png)

1. **가져오기** 페이지에서 **링크** 를 선택합니다. 그런 다음, 다음 링크를 삽입하여 API를 가져옵니다. `https://developer.okta.com/docs/api/postman/example.oktapreview.com.environment`

   ![가져올 링크를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/link-to-import.png)

   >[!NOTE]
   >테넌트 값으로 링크를 수정하지 마세요.

1. **가져오기** 를 선택하여 계속합니다.

   ![다음 가져오기 페이지를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/next-import-menu.png)

1. API를 가져온 후 **환경** 선택을 **{yourOktaDomain}** 으로 변경합니다.

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png" alt-text="환경을 변경하는 방법을 보여 주는 스크린샷" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/change-environment.png":::

1. 눈 모양 아이콘을 선택하여 OKTA 환경을 편집합니다. 그런 다음 **편집** 을 선택합니다.

   ![OKTA 환경을 편집하는 방법을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/edit-environment.png)

1. **초기 값** 및 **현재 값** 필드에서 URL 및 API 키의 값을 업데이트합니다. 환경을 반영하도록 이름을 변경합니다. 그런 다음, 값을 저장합니다.

    ![API에 대한 값을 업데이트하는 방법을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/update-values-for-api.png)

1. [Postman에 API를 로드합니다](https://app.getpostman.com/run-collection/377eaf77fdbeaedced17).

1. **앱** > **목록 앱 받기** > **보내기** 를 선택합니다.

   이제 OKTA 테넌트에서 모든 애플리케이션을 인쇄할 수 있습니다. 목록은 JSON 형식입니다.

    ![OKTA 테넌트에서 애플리케이션 목록을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/list-of-applications.png)

이 JSON 목록을 복사하여 CSV 형식으로 변환하는 것이 좋습니다. [Konklone](https://konklone.io/json/)과 같은 공용 변환기를 사용할 수 있습니다. 또는 PowerShell의 경우 [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) 및 [ConvertTo-CSV](/powershell/module/microsoft.powershell.utility/convertto-csv)를 사용합니다.

CSV를 다운로드하여 나중에 참조할 수 있도록 OKTA 테넌트에서 애플리케이션의 레코드를 유지합니다.

## <a name="migrate-a-saml-application-to-azure-ad"></a>SAML 애플리케이션을 Azure AD로 마이그레이션

SAML 2.0 애플리케이션을 Azure AD로 마이그레이션하려면 먼저 애플리케이션 액세스를 위해 Azure AD 테넌트에서 애플리케이션을 구성합니다. 이 예제에서는 Salesforce 인스턴스를 변환합니다. [이 자습서](../saas-apps/salesforce-tutorial.md)에 따라 애플리케이션을 구성합니다.

마이그레이션을 완료하려면 OKTA 테넌트에서 검색된 모든 애플리케이션에 대한 구성 단계를 반복합니다.

1. [Azure AD 포털](https://aad.portal.azure.com)에서 **Azure Active Directory** > **Enterprise 애플리케이션** > **새 애플리케이션** 을 선택합니다.

   ![새 애플리케이션 목록을 보여주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/list-of-new-applications.png)

1. **Azure AD 갤러리** 에서 **Salesforce** 를 검색하고 애플리케이션을 선택한 다음, **만들기** 를 선택합니다.

   ![Azure AD 갤러리의 Salesforce 애플리케이션을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-application.png)

1. 애플리케이션을 만든 후 **SSO(Single Sign-On)** 탭에서 **SAML** 을 선택합니다.

   ![SAML 애플리케이션을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/saml-application.png)

1. **인증서(원시)** 및 **페더레이션 메타데이터 XML** 을 다운로드하여 Salesforce로 가져옵니다.

   ![페더레이션 메타데이터를 다운로드할 위치를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/federation-metadata.png)

1. Salesforce 관리 콘솔에서 **ID** > **Single Sign-On 설정** > **메타데이터 파일에서 새로 만들기** 를 선택합니다.

   ![Salesforce 관리 콘솔을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/salesforce-admin-console.png)

1. Azure AD 포털에서 다운로드한 XML 파일을 업로드합니다. 그런 다음 **만들기** 를 선택합니다.

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png" alt-text="XML 파일을 업로드할 위치를 보여 주는 스크린샷" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/upload-xml-file.png":::

1. Azure에서 다운로드한 인증서를 업로드합니다. 그런 다음, **저장** 을 선택하여 Salesforce에서 SAML 공급자를 만듭니다.

   ![Salesforce에서 SAML 공급자를 만드는 방법을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/create-saml-provider.png)

1. 다음 필드에 값을 기록합니다. Azure에서 이러한 값을 사용합니다.
   * **엔터티 ID**
   * **로그인 URL**
   * **로그아웃 URL**

   그런 다음, **메타데이터 다운로드** 를 선택합니다.

   ![Azure에서 사용하기 위해 기록해야 하는 값을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/record-values-for-azure.png)

1. Azure AD **Enterprise 애플리케이션** 페이지의 SAML SSO 설정에서 **메타데이터 파일 업로드** 를 선택하여 Azure AD 포털에 파일을 업로드합니다. 저장하기 전에 가져온 값이 기록된 값과 일치하는지 확인합니다.

   ![Azure AD에서 메타데이터 파일을 업로드하는 방법을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/upload-metadata-file.png)

1. Salesforce 관리 콘솔에서 **회사 설정** > **내 도메인** 을 선택합니다. **인증 구성** 으로 이동한 다음, **편집** 을 선택합니다.

    ![회사 설정을 편집하는 방법을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/edit-company-settings.png)

1. 로그인 옵션의 경우 이전에 구성한 새 SAML 공급자를 선택합니다. 그런 다음, **저장** 을 선택합니다.

    ![SAML 공급자 옵션을 저장할 위치를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/save-saml-provider.png)

1. Azure AD의 **엔터프라이즈 애플리케이션** 페이지에서 **사용자 및 그룹** 을 선택합니다. 그런 다음, 테스트 사용자를 추가합니다.

    ![추가된 테스트 사용자를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/add-test-user.png)

1. 구성을 테스트하려면 테스트 사용자 중 하나로 로그인합니다. Microsoft [앱 갤러리](https://aka.ms/myapps)로 이동한 다음, **Salesforce** 를 선택합니다.

    ![앱 갤러리에서 Salesforce를 여는 방법을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/test-user-sign-in.png)

1. 새로 구성된 IdP(ID 공급자)를 선택하여 로그인합니다.

    ![로그인할 위치를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/new-identity-provider.png)

    모든 것이 올바르게 구성된 경우 테스트 사용자는 Salesforce 홈페이지에 표시됩니다. 문제 해결 도움말은 [디버깅 가이드](../manage-apps/debug-saml-sso-issues.md)를 참조하세요.

1. **Enterprise 애플리케이션** 페이지에서 올바른 역할을 사용하여 나머지 사용자를 Salesforce 애플리케이션에 할당합니다.

    >[!NOTE]
    >나머지 사용자를 Azure AD 애플리케이션에 추가한 후에는 사용자가 연결을 테스트하여 액세스 권한이 있는지 확인해야 합니다. 다음 단계로 이동하기 전에 연결을 테스트합니다.

1. Salesforce 관리 콘솔에서 **회사 설정** > **내 도메인** 을 선택합니다.

1. **인증 구성** 에서 **편집** 을 선택합니다. 인증 서비스로 **OKTA** 에 대한 선택을 취소합니다.

    ![인증 서비스로 OKTA에 대한 선택을 취소할 위치를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/deselect-okta.png)

Salesforce는 이제 SSO용 Azure AD로 성공적으로 구성되었습니다.

## <a name="migrate-an-oidcoauth-20-application-to-azure-ad"></a>OIDC/OAuth 2.0 애플리케이션을 Azure AD로 마이그레이션

OIDC(OpenID Connect) 또는 OAuth 2.0 애플리케이션을 Azure AD로 마이그레이션하려면 먼저 Azure AD 테넌트에서 액세스하도록 애플리케이션을 구성합니다. 이 예제에서는 사용자 지정 OIDC 앱을 변환합니다.

마이그레이션을 완료하려면 OKTA 테넌트에서 검색된 모든 애플리케이션에 대한 다음 구성 단계를 반복합니다.

1. [Azure AD 포털](https://aad.portal.azure.com)에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** 을 차례로 선택합니다. **모든 애플리케이션** 에서 **새 애플리케이션** 을 선택합니다.

1. **자신만의 애플리케이션** 을 선택하세요. 표시되는 메뉴에서 OIDC 앱의 이름을 지정한 다음, **작업 중인 애플리케이션을 Azure AD와 통합하려면 등록** 을 선택합니다. 그런 다음 **만들기** 를 선택합니다.

   :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png" alt-text="OIDC 애플리케이션을 만드는 방법을 보여 주는 스크린샷" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/new-oidc-application.png":::

1. 다음 페이지에서 애플리케이션 등록의 테넌시를 설정합니다. 자세한 내용은 [Azure Active Directory의 테넌시](../develop/single-and-multi-tenant-apps.md)를 참조하세요.

   이 예제에서는 **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트)**  > **등록** 을 선택합니다.

   ![Azure AD 디렉터리 다중 테넌트를 선택하는 방법을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/multitenant-azure-ad-directory.png)

1. **앱 등록** 페이지의 **Azure Active Directory** 아래에서 새로 만든 등록을 엽니다.

   [애플리케이션 시나리오](../develop/authentication-flows-app-scenarios.md)에 따라 다양한 구성 작업이 필요할 수 있습니다. 대부분의 시나리오에는 앱 클라이언트 암호가 필요하므로 이러한 시나리오를 살펴보겠습니다.

1. **개요** 페이지에서 **애플리케이션(클라이언트) ID** 를 복사합니다. 애플리케이션에서 이 ID를 사용합니다.

   ![애플리케이션 클라이언트 ID를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/application-client-id.png)

1. 왼쪽에서 **인증서 및 비밀** 을 선택합니다. 그런 다음, **새 클라이언트 암호** 를 선택합니다. 클라이언트 암호의 이름을 지정하고 만료를 설정합니다.

   ![새 클라이언트 암호를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/new-client-secret.png)

1. 비밀의 값과 ID를 기록해 둡니다.

   >[!NOTE]
   >클라이언트 암호가 손실되면 검색할 수 없습니다. 대신 비밀을 다시 생성해야 합니다.

1. 왼쪽에서 **API 권한** 을 선택합니다. 그런 다음, 애플리케이션에 OIDC 스택에 대한 액세스 권한을 부여합니다.

1. **권한 추가** > **Microsoft Graph** > **위임된 권한** 을 선택합니다.

1. **OpenID 권한** 섹션에서 **이메일**, **openid** 및 **프로필** 을 선택합니다. 그런 다음 **권한 추가** 를 선택합니다.

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png" alt-text="Open ID 권한을 추가할 위치를 보여 주는 스크린샷" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/add-openid-permission.png":::

1. 사용자 환경을 개선하고 사용자 동의 프롬프트를 표시하려면 **테넌트 도메인 이름에 대한 관리자 동의 허용** 을 선택합니다. 그런 다음, **허용** 상태가 나타날 때까지 기다립니다.

    ![관리자 동의를 부여할 위치를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/grant-admin-consent.png)

1. 애플리케이션에 리디렉션 URI가 있는 경우 적절한 URI를 입력합니다. 회신 URL이 **인증** 탭을 대상으로 하는 경우 **플랫폼 추가** 및 **웹** 뒤에 적절한 URL을 입력합니다. **액세스 토큰** 및 **ID 토큰** 을 선택합니다. 그런 다음, **구성** 을 선택합니다.

    :::image type="content" source="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png" alt-text="토큰을 구성하는 방법을 보여 주는 스크린샷" lightbox="media/migrate-applications-from-okta-to-azure-active-directory/configure-tokens.png":::
    
    **인증** 메뉴의 **고급 설정** 및 **퍼블릭 클라이언트 흐름 허용** 아래에서 필요한 경우 **예** 를 선택합니다.

    ![퍼블릭 클라이언트 흐름을 허용하는 방법을 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/allow-client-flows.png)

1. OIDC 구성 애플리케이션에서 테스트하기 전에 애플리케이션 ID 및 클라이언트 암호를 가져옵니다. 이전 단계에 따라 클라이언트 ID, 비밀 및 범위와 같은 설정을 사용하여 애플리케이션을 구성합니다.

## <a name="migrate-a-custom-authorization-server-to-azure-ad"></a>사용자 지정 권한 부여 서버를 Azure AD로 마이그레이션

OKTA 권한 부여 서버는 [API를 노출하는](../develop/quickstart-configure-app-expose-web-apis.md#add-a-scope) 애플리케이션 등록에 일대일로 매핑됩니다.

기본 OKTA 권한 부여 서버는 Microsoft Graph 범위 또는 사용 권한에 매핑되어야 합니다.

![기본 OKTA 권한 부여를 보여 주는 스크린샷](media/migrate-applications-from-okta-to-azure-active-directory/default-okta-authorization.png)

## <a name="next-steps"></a>다음 단계

- [OKTA 페더레이션을 Azure AD로 마이그레이션](migrate-okta-federation-to-azure-active-directory.md)
- [OKTA 동기화 프로비저닝을 Azure AD Connect 기반 동기화로 마이그레이션](migrate-okta-sync-provisioning-to-azure-active-directory.md)
- [OKTA 로그온 정책을 Azure AD 조건부 액세스로 마이그레이션](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
