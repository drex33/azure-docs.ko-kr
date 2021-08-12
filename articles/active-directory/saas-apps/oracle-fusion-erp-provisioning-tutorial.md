---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 수행하도록 Oracle Fusion ERP 구성 | Microsoft Docs'
description: 사용자 계정을 Oracle Fusion ERP로 자동으로 프로비저닝 및 프로비저닝 해제를 하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: thwimmer
ms.openlocfilehash: d9eaeebf72e13a3995c12326a1799a53fdeabdef
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760267"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 수행하도록 Oracle Fusion ERP 구성

이 자습서에서는 사용자 및/또는 그룹을 Oracle Fusion ERP로 자동으로 프로비저닝 및 프로비저닝 해제를 하도록 Azure AD(Azure Active Directory)를 구성하기 위해 Oracle Fusion ERP 및 Azure AD에서 수행하는 단계를 보여 줍니다.

> [!NOTE]
>  이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 미리 보기로 제공됩니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [Oracle Fusion ERP 테넌트](https://www.oracle.com/applications/erp/)
* 관리자 권한이 있는 Oracle Fusion ERP의 사용자 계정

## <a name="assign-users-to-oracle-fusion-erp"></a>Oracle Fusion ERP에 사용자 할당 
Azure Active Directory는 '할당'이라는 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 애플리케이션에 할당된 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하려면 먼저 Oracle Fusion ERP에 액세스해야 하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정되면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 Oracle Fusion ERP에 할당할 수 있습니다.
 
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>사용자를 Oracle Fusion ERP에 할당하기 위한 주요 팁 

 * 단일 Azure AD 사용자를 Oracle Fusion ERP에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 Oracle Fusion ERP에 할당할 때 할당 대화 상자에서 유효한 애플리케이션 특정 역할(사용 가능한 경우)을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>프로비저닝을 수행하도록 Oracle Fusion ERP 설정

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 Oracle Fusion ERP를 구성하려면 먼저 Oracle Fusion ERP에서 SCIM 프로비저닝을 사용하도록 설정해야 합니다.

1. [Oracle Fusion ERP 관리 콘솔](https://cloud.oracle.com/sign-in)에 로그인합니다.

2. 왼쪽 위 모서리에서 Navigator(탐색기)를 클릭합니다. **Tools(도구)** 아래에서 **Security Console(보안 콘솔)** 을 선택합니다.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="Oracle Fusion ERP 관리 콘솔의 Navigator 페이지에 대한 스크린샷. Tools 및 Security Console이 강조 표시되어 있습니다." border="false":::

3. **Users(사용자)** 로 이동합니다.
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="Oracle Fusion ERP 관리 콘솔의 패널에 대한 스크린샷. Users 항목이 강조 표시되어 있습니다." border="false":::

4. Oracle Fusion ERP 관리 콘솔에 로그인하는 데 사용할 관리 사용자 계정에 대한 사용자 이름과 암호를 저장합니다. 이러한 값은 Azure Portal에 있는 Oracle Fusion ERP 애플리케이션에 대한 [프로비저닝] 탭의 **관리 사용자 이름** 및 **암호** 필드에 입력해야 합니다.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>갤러리에서 Oracle Fusion ERP 추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 Oracle Fusion ERP를 구성하려면 Azure AD 애플리케이션 갤러리에서 Oracle Fusion ERP를 관리형 SaaS 애플리케이션 목록에 추가해야 합니다.

**Azure AD 애플리케이션 갤러리에서 Oracle Fusion ERP를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory** 를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 창 위쪽에서 **새 애플리케이션** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Oracle Fusion ERP** 를 입력하고, 결과 패널에서 **Oracle Fusion ERP** 를 선택합니다.

    ![결과 목록의 Oracle Fusion ERP](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Oracle Fusion ERP에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 Oracle Fusion ERP에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
> [Oracle Fusion ERP Single Sign-On 자습서](oracle-fusion-erp-tutorial.md)에서 제공하는 지침에 따라 SAML 기반 Single Sign-On을 Oracle Fusion ERP에 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 사용자 프로비저닝과 별개로 구성할 수 있지만, 이러한 두 기능은 서로 보완적입니다.

> [!NOTE]
> Oracle Fusion ERP의 SCIM 엔드포인트에 대한 자세한 내용은 [Oracle Applications Cloud의 일반적인 기능에 대한 REST API](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)를 참조하세요.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Azure AD에서 Fuze에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Oracle Fusion ERP** 를 선택합니다.

    ![애플리케이션 목록의 Oracle Fusion ERP 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동 옵션이 호출된 프로비저닝 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래의 **테넌트 URL** 에서 `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/`을 입력합니다. 이전에 검색한 사용자 이름 및 암호를 **관리 사용자 이름** 및 **암호** 필드에 입력합니다. Azure AD와 Oracle Fusion ERP 간의 **연결 테스트** 를 클릭합니다. 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="관리자 자격 증명 섹션의 스크린샷. 연결 테스트 단추와 테넌트 URL, 관리 사용자 이름 및 관리자 암호에 대한 필드가 표시되어 있습니다." border="false":::

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 클릭합니다.

8. **매핑** 섹션 아래에서 **Azure Active Directory 사용자를 Oracle Fusion ERP에 동기화** 를 선택합니다.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="매핑 섹션의 스크린샷. 이름 아래에 Azure Active Directory 사용자를 Oracle Fusion ERP에 동기화가 표시되어 있습니다." border="false":::

9. **특성 매핑** 섹션에서 Azure AD에서 Oracle Fusion ERP로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Oracle Fusion ERP의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="특성 매핑 페이지의 스크린샷. 테이블에 Azure Active Directory 특성, Oracle Fusion ERP 특성 및 일치하는 우선 순위가 나열되어 있습니다." border="false":::

10. **매핑** 섹션 아래에서 **Azure Active Directory 그룹을 Oracle Fusion ERP에 동기화** 를 선택합니다.

    ![Oracle Fusion ERP 그룹 매핑](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. **특성 매핑** 섹션에서 Azure AD에서 Oracle Fusion ERP로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Oracle Fusion ERP의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Oracle Fusion ERP 그룹 특성](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Azure AD 프로비저닝 서비스를 Oracle Fusion ERP에 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 Oracle Fusion ERP에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

    이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상황을 모니터링하고, Oracle Fusion ERP의 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 작업 보고서에 대한 링크를 따를 수 있습니다.

    Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Oracle Fusion ERP는 해당 SCIM 엔드포인트에 대한 기본 인증만 지원합니다.
* Oracle Fusion ERP는 그룹 프로비저닝을 지원하지 않습니다.
* Oracle Fusion ERP의 역할은 Azure AD의 그룹에 매핑됩니다. Azure AD에서 Oracle Fusion ERP의 사용자에게 역할을 할당하려면 사용자를 Oracle Fusion ERP에서 역할 뒤에 명명된 원하는 Azure AD 그룹에 할당해야 합니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
