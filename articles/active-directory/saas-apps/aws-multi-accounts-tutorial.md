---
title: '자습서: 여러 계정 연결을 위해 Amazon Web Services와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure AD와 Amazon Web Services 간에 Single Sign-On을 구성하는 방법을 알아봅니다(레거시 자습서).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 90890843c5377f22647634fad158fb7e26df17f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288363"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>자습서: Amazon Web Services와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 AWS(Amazon Web Services)를 통합하는 방법에 대해 알아봅니다(레거시 자습서).

이렇게 통합하면 다음과 같은 이점이 있습니다.

- Azure AD에서 AWS에 액세스할 수 있는 사용자를 제어할 수 있습니다.
- 사용자가 SSO(Single Sign-On)를 사용하여 해당 Azure AD 계정으로 AWS에 자동으로 로그인되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

![AWS와 Azure AD 통합 다이어그램](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> 하나의 AWS 앱을 모든 AWS 계정에 연결하지 _않는_ 것이 좋습니다. 대신 [AWS와 Azure AD SSO 통합](./amazon-web-service-tutorial.md)을 사용하여 AWS 계정의 여러 인스턴스를 Azure AD에 있는 AWS 앱의 여러 인스턴스에 구성하는 것이 좋습니다.

다음과 같은 이유로 하나의 AWS 앱을 모든 AWS 계정에 연결하지 _않는_ 것이 좋습니다.

- 이 모델은 AWS 계정 및 해당 계정의 역할이 증가함에 따라 확장할 수 없으므로 AWS 계정 및 역할의 수가 적은 경우에만 이 방법을 사용합니다. 이 방법은 Azure AD 사용자 프로비저닝에서 AWS 역할 가져오기 기능을 사용하지 않으므로 역할을 수동으로 추가, 업데이트 또는 삭제해야 합니다.

- Microsoft Graph Explorer 방법을 사용하여 모든 역할을 앱에 패치해야 합니다. 매니페스트 파일 방식은 권장되지 않습니다.

- 단일 AWS 앱에 대해 1,200개 이하의 앱 역할이 추가된 후 앱에서 추가 작업을 수행하면 크기와 관련된 오류를 throw하기 시작한다고 고객이 보고하고 있습니다. 애플리케이션 개체에는 하드 크기 제한이 있습니다.

- 역할이 계정에 추가되면 해당 역할을 수동으로 업데이트해야 합니다. 이는 _추가_ 방법이 아니라 _대체_ 방법입니다. 또한 계정 수가 증가하는 경우 이는 계정 및 역할과의 _n_ &times; _n_ 관계가 됩니다.

- 모든 AWS 계정은 동일한 페더레이션 메타데이터 XML 파일을 사용합니다. 인증서를 롤오버할 때 모든 AWS 계정에서 인증서를 동시에 업데이트하는 것은 대규모 실습일 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

AWS와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독 Azure AD 구독이 없는 경우 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 얻을 수 있습니다.
- AWS SSO를 사용하도록 설정된 구독

> [!NOTE]
> 필요한 경우를 제외하고는 프로덕션 환경에서 이 자습서의 단계를 테스트하지 않는 것이 좋습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

AWS에서 SP 시작 및 IDP 시작 SSO를 지원합니다.

## <a name="add-aws-from-the-gallery"></a>갤러리에서 AWS 추가

AWS가 Azure AD에 통합되도록 구성하려면 갤러리의 AWS를 관리형 SaaS(Software as a Service) 앱 목록에 추가합니다.

1. Azure Portal에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 창에서 사용하려는 Azure AD 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Amazon Web Services** 를 입력합니다.
1. 결과 창에서 **Amazon Web Services** 를 선택한 다음, 앱을 추가합니다. 몇 초 후에 앱이 테넌트에 추가됩니다.

1. **속성** 창으로 이동한 다음, **개체 ID** 상자에 표시된 값을 복사합니다.

   ![속성 창의 개체 ID 상자에 대한 스크린샷](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 하여 AWS에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 AWS의 상대 사용자를 알고 있어야 합니다. 즉, Azure AD 사용자와 AWS의 동일한 사용자 간에 연결 관계를 설정해야 합니다.

AWS에서 Azure AD의 **사용자 이름** 값을 AWS **Username** 값으로 할당하여 연결 관계를 설정합니다.

AWS에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음을 수행합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
1. **[AWS SSO 구성](#configure-aws-sso)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 다음을 수행하여 Azure Portal에서 Azure AD SSO를 사용하도록 설정하고 AWS 애플리케이션에서 SSO를 구성합니다.

1. Azure Portal에 있는 **AWS(Amazon Web Services)** 애플리케이션 통합 페이지의 왼쪽 창에서 **Single Sign-On** 을 선택합니다.

   !["Single Sign-On" 명령의 스크린샷](common/select-sso.png)

1. **Single Sign-On 방법 선택** 창에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

   !["Single Sign-On 방법 선택" 창의 스크린샷](common/select-saml-option.png)

1. **SAML로 Single Sign-On 설정** 창에서 **편집** 단추(연필 아이콘)를 선택합니다.

   !["SAML로 Single Sign-On 설정" 창의 편집 단추에 대한 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 패널이 열립니다. 앱이 Azure와 사전 통합되어 있으므로 이 섹션을 건너뜁니다. **저장** 을 선택합니다.

   AWS 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이러한 특성의 값은 **애플리케이션 통합** 페이지의 **사용자 특성 및 클레임** 섹션에서 관리할 수 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 선택합니다.

   !["사용자 특성" 창의 편집 단추에 대한 스크린샷](common/edit-attribute.png)

1. **사용자 특성** 창의 **사용자 클레임** 섹션에서 다음 표의 값을 사용하여 SAML 토큰 특성을 구성합니다.

   | Name            | 원본 특성                                                            | 네임스페이스                                |
   | --------------- | --------------------------------------------------------------------------- | ---------------------------------------- |
   | RoleSessionName | user.userprincipalname                                                      | `https://aws.amazon.com/SAML/Attributes` |
   | 역할            | user.assignedroles                                                          | `https://aws.amazon.com/SAML/Attributes` |
   | SessionDuration | "900초(15분)에서 43,200초(12시간)까지의 값을 제공합니다." | `https://aws.amazon.com/SAML/Attributes` |

   a. **새 클레임 추가** 를 선택한 다음, **사용자 클레임 관리** 창에서 다음을 수행합니다.

   !["사용자 클레임" 창의 "새 클레임 추가" 및 "저장" 단추에 대한 스크린샷](common/new-save-attribute.png)

   !["사용자 클레임 관리" 창의 스크린샷](common/new-attribute-details.png)

   b. **이름** 상자에서 특성 이름을 입력합니다.

   c. **네임스페이스** 텍스트 상자에서 네임스페이스 값을 입력합니다.

   d. **원본** 에서 **특성** 을 선택합니다.

   e. **원본 특성** 드롭다운 목록에서 특성을 선택합니다.

   f. **확인**, **저장** 을 차례로 선택합니다.

   > [!NOTE]
   > Azure AD의 역할에 대한 자세한 내용은 [애플리케이션에 앱 역할 추가 및 토큰에서 받기](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)를 참조하세요.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 선택하여 페더레이션 메타데이터 XML을 다운로드한 다음, 컴퓨터에 저장합니다.

   !["SAML 서명 인증서" 창의 인증서 "페더레이션 메타데이터 XML" 다운로드 링크에 대한 스크린샷](common/metadataxml.png)

### <a name="configure-aws-sso"></a>AWS SSO 구성

1. 새 브라우저 창에서 관리자 권한으로 AWS 회사 사이트에 로그온합니다.

1. **AWS 홈** 아이콘을 선택합니다.

   !["AWS 홈" 아이콘의 스크린샷][11]

1. **AWS services**(AWS 서비스) 창의 **Security, Identity & Compliance**(보안, ID 및 규정 준수) 아래에서 **IAM (Identity & Access Management)** (IAM(ID 및 액세스 관리))을 선택합니다.

   !["AWS Services" 창의 "Identity and Access Management" 링크에 대한 스크린샷][12]

1. 왼쪽 창에서 **Identity Providers**(ID 공급자)를 선택한 다음, **Create Provider**(공급자 만들기)를 선택합니다.

   !["Create Provider" 단추의 스크린샷][13]

1. **Configure Provider**(공급자 구성) 페이지에서 다음을 수행합니다.

   !["Configure Provider" 창의 스크린샷][14]

   a. **Provider Type**(공급자 유형) 드롭다운 목록에서 **SAML** 을 선택합니다.

   b. **Provider Name**(공급자 이름) 상자에서 공급자 이름(예: _WAAD_)을 입력합니다.

   c. **Metadata Document**(메타데이터 문서) 상자 옆에 있는 **Choose File**(파일 선택)을 선택하여 다운로드한 페더레이션 메타데이터 XML 파일을 Azure Portal에 업로드합니다.

   d. **다음 단계** 를 선택합니다.

1. **Verify Provider Information**(공급자 정보 확인) 창에서 **Create(만들기)** 를 선택합니다.

   !["Verify Provider Information" 창의 스크린샷][15]

1. 왼쪽 창에서 **Roles**(역할)를 선택한 다음, **Create role**(역할 만들기)을 선택합니다.

   ![Roles 창의 "Create role" 단추에 대한 스크린샷][16]

   > [!NOTE]
   > 가져오는 역할에 대한 역할 ARN(Amazon 리소스 이름) 및 SAML 공급자 ARN을 결합한 길이는 240자 이하여야 합니다.

1. **Create role** 페이지에서 다음을 수행합니다.

   !["Create role" 페이지의 "SAML 2.0 federation(SAML 2.0 페더레이션)" 신뢰할 수 있는 엔터티 단추에 대한 스크린샷][19]

   a. **Select type of trusted entity**(신뢰할 수 있는 엔터티 유형 선택) 아래에서 **SAML 2.0 federation**(SAML 2.0 페더레이션)을 선택합니다.

   b. **Choose a SAML 2.0 Provider**(SAML 2.0 공급자 선택) 아래에서 이전에 만든 SAML 공급자(예: _WAAD_)를 선택합니다.

   다. **프로그래밍 및 AWS 관리 콘솔 액세스 허용** 을 선택합니다.

   d. 완료되면 **다음: 사용 권한** 을 클릭합니다.

1. 검색 상자에서 **Administrator Access**(관리자 액세스)를 입력하고, **AdministratorAccess** 확인란을 선택하고, **다음: 태그** 를 선택합니다.

   ![AdministratorAccess 정책이 선택된 "Policy name(정책 이름)" 목록의 스크린샷](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. **Add tags (optional)** (태그 추가(선택 사항)) 창에서 다음을 수행합니다.

   !["Add tags (optional)" 창의 스크린샷](./media/aws-multi-accounts-tutorial/config2.png)

   a. **Key**(키) 상자에서 키 이름(예: _Azureadtest_)을 입력합니다.

   b. **Value (optional)** (값(선택 사항)) 상자에서 `<accountname-aws-admin>` 형식의 키 값을 입력합니다. 계정 이름은 모두 소문자여야 합니다.

   c. 완료되면 **다음: 검토** 를 클릭합니다.

1. **Review**(검토) 창에서 다음을 수행합니다.

   !["Role name" 및 "Role description" 상자가 강조 표시된 Review 창의 스크린샷][34]

   a. **Role name**(역할 이름) 상자에서 `<accountname-aws-admin>` 형식의 값을 입력합니다.

   b. **Role name**(역할 설명) 상자에서 역할 이름에 사용한 값을 입력합니다.

   다. **Create role**(역할 만들기)을 선택합니다.

   d. 역할을 필요한 만큼 만들고, 이를 ID 공급자에 매핑합니다.

   > [!NOTE]
   > 마찬가지로 각각 다른 정책이 연결된 다른 역할(예: _accountname-finance-admin_, _accountname-read-only-user_, _accountname-devops-user_ 또는 _accountname-tpm-user_)을 만들 수 있습니다. 이러한 역할 정책은 나중에 각 AWS 계정의 요구 사항에 따라 변경할 수 있습니다. AWS 계정 전체에서 각 역할에 대해 동일한 정책을 유지하는 것이 좋습니다.

1. 다음 스크린샷과 같이 Amazon EC2(Amazon Elastic Compute Cloud) 속성 창 또는 IAM 대시보드의 AWS 계정에 대한 계정 ID를 확인해야 합니다.

   !["Identity and Access Management" 창에서 계정 ID가 표시되는 위치를 보여 주는 스크린샷](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Azure Portal에 로그인한 다음, **그룹** 으로 이동합니다.

1. 이전에 만든 IAM 역할과 동일한 이름을 사용하는 새 그룹을 만든 다음, 이러한 새 그룹 각각에 대한 **개체 ID** 상자에 있는 값을 확인합니다.

   ![새 그룹의 계정 세부 정보에 대한 스크린샷](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. 현재 AWS 계정에서 로그아웃한 다음, Azure AD를 사용하여 SSO를 구성하려는 다른 계정에 로그인합니다.

1. 모든 역할이 계정에 만들어지면 해당 계정의 **역할** 목록에 표시됩니다.

   ![각 역할의 이름, 설명 및 신뢰할 수 있는 엔터티를 보여 주는 역할 목록의 스크린샷](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

다음으로, 모든 계정에서 모든 역할에 대한 모든 역할 ARN 및 신뢰할 수 있는 엔터티를 캡처해야 합니다. Azure AD 애플리케이션을 사용하여 수동으로 매핑해야 합니다. 그러려면 다음을 수행합니다.

1. 각 역할을 선택하여 해당 역할 ARN 및 신뢰할 수 있는 엔터티 값을 복사합니다. 이는 Azure AD에서 만들 모든 역할에 필요합니다.

   ![역할 ARN 및 신뢰할 수 있는 엔터티에 대한 요약 창의 스크린샷](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. 모든 계정의 모든 역할에 대해 이전 단계를 반복한 다음, `<Role ARN>,<Trusted entities>` 형식으로 텍스트 파일에 저장합니다.

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 열고, 다음을 수행합니다.

   a. 테넌트에 대한 전역 관리자 또는 공동 관리자 자격 증명을 사용하여 Microsoft Graph Explorer 사이트에 로그인합니다.

   b. 역할을 만들 수 있는 충분한 권한이 필요합니다. **권한 수정** 을 선택합니다.

   ![Microsoft Graph Explorer 인증 창의 "권한 수정" 링크에 대한 스크린샷](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

   c. 권한 목록에서 다음 스크린샷에 표시된 권한이 아직 없는 경우 각 권한을 선택한 다음, **권한 수정** 을 선택합니다.

   ![적절한 권한이 강조 표시된 Microsoft Graph Explorer 권한 목록의 스크린샷](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

   d. Graph Explorer에 다시 로그인하고, 사이트 사용 조건에 동의합니다.

   e. 창의 위쪽에서 **GET** 메서드 및 **beta** 버전을 차례로 선택한 다음, 쿼리 상자에서 다음 중 하나를 입력합니다.

   - 테넌트에서 모든 서비스 주체를 가져오려면 `https://graph.microsoft.com/beta/servicePrincipals`를 사용합니다.
   - 여러 디렉터리를 사용하는 경우 기본 도메인이 포함된 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`를 사용합니다.

   ![Microsoft Graph Explorer 쿼리 "요청 본문" 창의 스크린샷](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

   f. 서비스 주체 목록에서 수정해야 하는 서비스 주체를 가져옵니다.

   또한 Ctrl+F를 선택하여 나열된 모든 서비스 주체에 대한 애플리케이션을 검색할 수 있습니다. 특정 서비스 주체를 가져오려면 아래와 같이 이전에 Azure AD 속성 창에서 복사한 서비스 주체 개체 ID를 쿼리에 포함합니다.

   `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

   ![개체 ID가 포함된 서비스 주체 쿼리를 보여 주는 스크린샷](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

   g. 서비스 주체 개체에서 appRoles 속성을 추출합니다.

   ![서비스 주체 개체에서 appRoles 속성을 추출하는 코드의 스크린샷](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

   h. 이제 애플리케이션에 대한 새 역할을 생성해야 합니다.

   i. 다음 JSON 코드는 appRoles 개체의 예제입니다. 애플리케이션에 역할을 추가할 유사한 개체를 만듭니다.

   ```
   {
   "appRoles": [
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "msiam_access",
           "displayName": "msiam_access",
           "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
           "isEnabled": true,
           "origin": "Application",
           "value": null
       },
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Admin,WAAD",
           "displayName": "Admin,WAAD",
           "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
           "isEnabled": true,
           "origin": "ServicePrincipal",
           "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
       },
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Auditors,WAAD",
           "displayName": "Auditors,WAAD",
           "id": "bcad6926-67ec-445a-80f8-578032504c09",
           "isEnabled": true,
           "origin": "ServicePrincipal",
           "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
       }    ]
   }
   ```

   > [!Note]
   > 새 역할은 패치 작업에 대한 _msiam_access_ 가 추가된 후에만 추가할 수 있습니다. 또한 역할은 조직의 요구에 따라 원하는 만큼 추가할 수 있습니다. Azure AD는 이러한 역할의 _값_ 을 SAML 응답의 클레임 값으로 보냅니다.

   j. Microsoft Graph Explorer에서 메서드를 **GET** 에서 **PATCH** 로 변경합니다. 이전 예제와 같이 appRoles 속성을 업데이트하여 서비스 주체 개체를 원하는 역할로 패치합니다. **쿼리 실행** 을 선택하여 패치 작업을 실행합니다. 성공 메시지에서 AWS 애플리케이션에 대한 역할이 만들어졌음을 확인합니다.

   ![메서드가 PATCH로 변경된 Microsoft Graph Explorer 창의 스크린샷](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. 서비스 주체에 더 많은 역할이 패치되면 사용자 및 그룹을 각 역할에 할당할 수 있습니다. 이 작업은 Azure Portal에서 AWS 애플리케이션으로 이동한 다음, 위쪽에서 **사용자 및 그룹** 탭을 선택하여 수행합니다.

1. 특정 역할을 그룹에 할당할 수 있도록 모든 AWS 역할에 대해 새 그룹을 만드는 것이 좋습니다. 이 일대일 매핑은 하나의 그룹이 하나의 역할에 할당됨을 의미합니다. 그런 다음, 해당 그룹에 속하는 멤버를 추가할 수 있습니다.

1. 그룹이 만들어지면 그룹을 선택하여 애플리케이션에 할당합니다.

   !["사용자 및 그룹" 창의 스크린샷](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

   > [!Note]
   > 그룹을 할당하는 경우 중첩 그룹은 지원되지 않습니다.

1. 역할을 그룹에 할당하려면 역할을 선택한 다음, **할당** 을 선택합니다.

   !["할당 추가" 창의 스크린샷](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

   > [!Note]
   > 역할이 할당되면 Azure Portal 세션을 새로 고쳐 해당 역할을 볼 수 있습니다.

### <a name="test-sso"></a>SSO 테스트

이 섹션에서는 Microsoft 내 앱 포털을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

내 앱에서 **AWS** 타일을 선택하면 역할을 선택할 수 있는 옵션이 포함된 AWS 애플리케이션 페이지가 열립니다.

![SSO 테스트를 위한 AWS 페이지의 스크린샷](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

SAML 응답에서도 클레임으로 전달된 역할을 확인할 수 있습니다.

![SAML 응답의 스크린샷](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

내 앱에 대한 자세한 내용은 [내 앱 포털에서 로그인하고 앱 시작](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)을 참조하세요.

## <a name="next-steps"></a>다음 단계

AWS가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. 자세한 내용은 [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법 알아보기](/cloud-app-security/proxy-deployment-aad)를 참조하세요.

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
