---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 수행하도록 Zscaler Beta 구성 | Microsoft Docs'
description: 사용자 계정을 Zscaler Beta로 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 809e400bd2dc8e5426670fde9cafd298c8e64ec8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045676"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 수행하도록 Zscaler Beta 구성

이 자습서에서는 사용자 및/또는 그룹을 Zscaler Beta로 자동으로 프로비전 및 프로비전 해제하도록 Azure AD를 구성하기 위해 Zscaler Beta 및 Azure AD(Azure Active Directory)에서 수행하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>


## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 설명한 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* Azure AD 테넌트
* Zscaler Beta 테넌트
* 관리자 권한이 있는 Zscaler Beta의 사용자 계정

> [!NOTE]
> Azure AD 프로비저닝 통합은 Zscaler Beta SCIM API를 사용합니다. 이 API는 Zscaler Beta 개발자가 Enterprise 패키지를 통해 계정에 사용할 수 있습니다.

## <a name="adding-zscaler-beta-from-the-gallery"></a>갤러리에서 Zscaler Beta 추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 Zscaler Beta를 구성하려면 먼저 Azure AD 애플리케이션 갤러리에서 Zscaler Beta를 관리형 SaaS 애플리케이션 목록에 추가해야 합니다.

**Azure AD 애플리케이션 갤러리에서 Zscaler Beta를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Zscaler Beta** 를 입력하고 결과 패널에서 **Zscaler Beta** 를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Zscaler Beta](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Zscaler Beta에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 "할당된" 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하려면 먼저 Zscaler Beta에 액세스해야 하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정되면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 Zscaler Beta에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>사용자를 Zscaler Beta에 할당하기 위한 주요 팁

* 단일 Azure AD 사용자를 Zscaler Beta에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 Zscaler Beta에 할당할 때 할당 대화 상자에서 유효한 애플리케이션 특정 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Zscaler Beta에 대한 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 Zscaler Beta에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
> [Zscaler Beta Single Sign-On 자습서](zscaler-beta-tutorial.md)에서 제공하는 지침에 따라 SAML 기반 Single Sign-On을 Zscaler Beta에 사용하도록 선택할 수도 있습니다. Single Sign-On과 자동 사용자 프로비저닝은 서로 보완적이지만, 별개로 구성할 수 있습니다.

> [!NOTE]
> 사용자 및 그룹을 프로비저닝하거나 프로비저닝 해제할 때 그룹 멤버 자격이 적절히 업데이트되도록 프로비저닝을 정기적으로 다시 시작하는 것이 좋습니다. 다시 시작하면 서비스에서 모든 그룹을 다시 평가하고 멤버 자격을 업데이트합니다.  

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Azure AD에서 Zscaler Beta에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Zscaler Beta** 를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Zscaler Beta** 를 선택합니다.

    ![애플리케이션 목록의 Zscaler Beta 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![범주별로 정렬된 탭 목록이 있으며, 제목이 ZScaler Beta - 프로비저닝/엔터프라이즈 애플리케이션입니다. 관리 범주의 프로비저닝 탭이 선택되어 있습니다.](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 모드 드롭다운 목록에서 자동 모드가 선택되어 있습니다. Zscaler Beta API에 연결하는 데 사용되는 관리자 자격 증명 필드 및 연결 테스트 단추가 있습니다.](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. **관리자 자격 증명** 섹션 아래에서 6단계에서 설명한 대로 Zscaler Beta 계정의 **테넌트 URL** 및 **비밀 토큰** 을 입력합니다.

6. **테넌트 URL** 및 **비밀 토큰** 을 가져오려면 Zscaler Beta 포털 사용자 인터페이스에서 **Administration(관리) > Authentication Settings(인증 설정)** 로 차례로 이동하여 **Authentication Type(인증 유형)** 아래에서 **SAML** 을 클릭합니다.

    ![Authentication Settings(인증 설정)의 Authentication Profile(인증 프로필)에서 선택한 Directory Type(디렉터리 유형)은 Hosted DB(호스팅된 DB)이고, 선택한 Authentication Type(인증 유형)은 SAML입니다.](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    **Configure SAML(SAML 구성)** 을 클릭하여 **Configuration SAML(SAML 구성)** 옵션을 엽니다.

    ![Configure SAML에서 Enable SAML Auto-Provisioning(SAML 자동 프로비저닝 사용) 및 Enable SCIM-Based Provisioning(SCIM 기반 프로비저닝 사용) 옵션이 선택되어 있습니다. Base URL(기준 URL) 및 Bearer Token(전달자 토큰) 텍스트 상자가 강조 표시되어 있습니다.](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    **Enable SCIM-Based Provisioning(SCIM 기반 프로비저닝 사용)** 을 선택하여 **Base URL(기준 URL)** 및 **Bearer Token(전달자 토큰)** 을 검색한 다음, 설정을 저장합니다. Azure Portal에서 **Base URL** 을 **테넌트 URL** 에 복사하고 **Bearer Token** 을 **비밀 토큰** 에 복사합니다.

7. 5단계에 표시된 필드가 채워지면 **연결 테스트** 를 클릭하여 Azure AD에서 Zscaler Beta에 연결할 수 있는지 확인합니다. 연결이 실패하면 Zscaler Beta 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![관리자 자격 증명에서 테넌트 URL 및 비밀 토큰 필드에 값이 있으며 연결 테스트 단추가 강조 표시되어 있습니다.](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. **알림 이메일** 필드에 프로비전 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 입력하고, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일 텍스트 상자가 비어 있고, 오류가 발생할 경우 이메일 알림 보내기 확인란이 선택 취소되어 있습니다.](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. **저장** 을 클릭합니다.

10. **매핑** 섹션 아래에서 **Azure Active Directory 사용자를 Zscaler Beta에 동기화** 를 선택합니다.

    ![Azure Active Directory 사용자를 Zscaler Beta에 동기화가 선택되어 사용하도록 설정되어 있습니다.](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. **특성 매핑** 섹션에서 Azure AD에서 Zscaler Beta로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Zscaler Beta의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![사용자 특성에 대한 특성 매핑 섹션에서 Active Directory 특성이 동기화되는 Zscalar Beta 특성 옆에 표시되어 있습니다. 특성 쌍 중 하나가 일치로 표시되어 있습니다.](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. **매핑** 섹션 아래에서 **Azure Active Directory 그룹을 Zscaler Beta로 동기화** 를 선택합니다.

    ![Azure Active Directory 그룹을 Zscaler Beta에 동기화가 선택되어 사용하도록 설정되어 있습니다.](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. **특성 매핑** 섹션에서 Azure AD에서 Zscaler Beta로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택된 특성은 업데이트 작업을 위해 Zscaler Beta의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![그룹 특성에 대한 특성 매핑 섹션에서 Active Directory 특성이 동기화되는 Zscalar Beta 특성 옆에 표시되어 있습니다. 특성 쌍 중 하나가 일치로 표시되어 있습니다.](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

15. Azure AD 프로비저닝 서비스를 Zscaler Beta에 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태가 표시되고, 켜기로 설정되어 있습니다.](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 Zscaler Beta에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![범위 드롭다운 목록이 표시되고, 할당된 사용자 및 그룹만 동기화가 선택되어 있습니다. 사용할 수 있는 다른 값은 모든 사용자 및 그룹 동기화입니다.](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![Zscaler Beta - 프로비저닝 위쪽의 저장 단추가 강조 표시되어 있습니다. 취소 단추도 있습니다.](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상황을 모니터링하고, Zscaler Beta의 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 작업 보고서에 대한 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png