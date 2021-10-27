---
title: '자습서: Azure Active Directory에서 SAP SuccessFactors 쓰기 저장 구성 | Microsoft Docs'
description: Azure AD에서 SAP SuccessFactors로 특성 쓰기 저장을 구성하는 방법을 알아봅니다.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/11/2021
ms.author: chmutali
ms.openlocfilehash: 39bd92afa4de12eafce7bff9f73f6f43d6e18f42
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993514"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>자습서: Azure AD에서 SAP SuccessFactors로 특성 쓰기 저장 구성
이 자습서에서는 특성을 Azure AD에서 SAP SuccessFactors Employee Central로 쓰기 저장하는 단계를 보여 줍니다. 

## <a name="overview"></a>개요

특정 특성을 Azure Active Directory에서 SAP SuccessFactors Employee Central로 쓰도록 SAP SuccessFactors 쓰기 저장 앱을 구성할 수 있습니다. SuccessFactors 쓰기 저장 프로비저닝 앱은 값을 다음 Employee Central 특성에 할당하는 기능을 지원합니다.

* 회사 이메일
* 사용자 이름
* 회사 전화 번호(국가 번호, 지역 번호, 전화번호 및 내선 번호 포함)
* 회사 전화 번호 기본 플래그
* 휴대폰 번호(국가 번호, 지역 번호, 전화 번호 포함)
* 휴대폰 기본 플래그 
* custom01-custom15 사용자 특성
* loginMethod 특성

> [!NOTE]
> 이 앱은 SuccessFactors 인바운드 사용자 프로비저닝 통합 앱에 대한 종속성이 없습니다. [SuccessFactors를 온-프레미스 AD로](sap-successfactors-inbound-provisioning-tutorial.md) 프로비저닝 앱 또는 [SuccessFactors에서 Azure AD로](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 프로비저닝 앱과 독립적으로 구성할 수 있습니다.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 SuccessFactors 쓰기 저장 사용자 프로비저닝 솔루션이 적합한 경우는 다음과 같습니다.

* IT에서 관리하는 신뢰할 수 있는 특성(예: 이메일 주소, 전화 번호, 사용자 이름)을 SuccessFactors Employee Central에 쓰기 저장하려는 Microsoft 365를 사용하는 조직

## <a name="configuring-successfactors-for-the-integration"></a>통합을 위한 SuccessFactors 구성

모든 SuccessFactors 프로비저닝 커넥터에는 Employee Central OData API를 호출할 수 있는 올바른 권한이 있는 SuccessFactors 계정의 자격 증명이 필요합니다. 이 섹션에서는 SuccessFactors에서 서비스 계정을 만들고 적절한 권한을 부여하는 단계에 대해 설명합니다. 

* [SuccessFactors에서 API 사용자 계정 만들기/식별](#createidentify-api-user-account-in-successfactors)
* [API 권한 역할 만들기](#create-an-api-permissions-role)
* [API 사용자에 대한 권한 그룹 만들기](#create-a-permission-group-for-the-api-user)
* [권한 그룹에 권한 역할 부여](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>SuccessFactors에서 API 사용자 계정 만들기/식별
SuccessFactors 관리자 팀 또는 구현 파트너와 협력하여 OData API를 호출하는 데 사용할 SuccessFactors에서 사용자 계정을 만들거나 식별합니다. 이 계정의 사용자 이름 및 암호 자격 증명은 Azure AD에서 프로비저닝 앱을 구성할 때 필요합니다. 

### <a name="create-an-api-permissions-role"></a>API 권한 역할 만들기

1. Admin Center(관리 센터)에 액세스할 수 있는 권한이 있는 사용자 계정을 사용하여 SAP SuccessFactors에 로그인합니다.
1. *Manage Permission Roles(권한 역할 관리)* 를 검색한 다음, 검색 결과에서 **Manage Permission Roles** 를 선택합니다.

   ![Manage Permission Roles](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. Permission Role List(권한 역할 목록)에서 **Create New(새로 만들기)** 를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![새 권한 역할 만들기](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. 새 권한 역할에 대한 **Role Name(역할 이름)** 및 **Description(설명)** 을 추가합니다. 이름 및 설명은 API 사용 권한에 대한 역할임을 나타내야 합니다.

   > [!div class="mx-imgBorder"]
   > ![권한 역할 세부 정보](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. Permission settings(권한 설정) 아래에서 **Permission(권한)...** 을 클릭한 다음, 권한 목록을 아래로 스크롤하여 **Manage Integration Tools(통합 도구 관리)** 를 클릭합니다. **Allow Admin to Access to OData API through Basic Authentication(관리자가 기본 인증을 통해 OData API에 액세스할 수 있도록 허용)** 확인란을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![Manage Integration Tools](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. 동일한 상자에서 아래로 스크롤하여 **Employee Central API** 를 선택합니다. 아래와 같이 ODATA API를 사용하여 읽고 편집할 수 있는 권한을 추가합니다. 동일한 계정을 SuccessFactors에 쓰기 저장 시나리오에 사용하려면 편집 옵션을 선택합니다. 

   > [!div class="mx-imgBorder"]
   > ![읽기 쓰기 권한](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. **완료** 를 클릭합니다. **변경 내용 저장** 을 클릭합니다.

### <a name="create-a-permission-group-for-the-api-user"></a>API 사용자에 대한 권한 그룹 만들기

1. SuccessFactors Admin Center(관리 센터)에서 *Manage Permission Groups(권한 그룹 관리)* 를 검색한 다음, 검색 결과에서 **Manage Permission Groups** 를 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![Manage Permission Groups](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. Manage Permission Groups 창에서 **Create New(새로 만들기)** 를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![새 그룹 추가](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. 새 그룹에 대한 Group Name(그룹 이름)을 추가합니다. 그룹 이름은 API 사용자에 대한 그룹임을 나타내야 합니다.

   > [!div class="mx-imgBorder"]
   > ![권한 그룹 이름](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. 멤버를 그룹에 추가합니다. 예를 들어 People Pool(사용자 풀) 드롭다운 메뉴에서 **Username(사용자 이름)** 을 선택한 다음, 통합에 사용할 API 계정의 사용자 이름을 입력할 수 있습니다. 

   > [!div class="mx-imgBorder"]
   > ![그룹 멤버 추가](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. **Done(완료)** 을 클릭하여 권한 그룹 만들기를 완료합니다.

### <a name="grant-permission-role-to-the-permission-group"></a>권한 그룹에 권한 역할 부여

1. SuccessFactors Admin Center(관리 센터)에서 *Manage Permission Roles(권한 역할 관리)* 를 검색한 다음, 검색 결과에서 **Manage Permission Roles** 를 선택합니다.
1. **Permission Role List(권한 역할 목록)** 에서 API 사용 권한에 대해 만든 역할을 선택합니다.
1. **Grant this role to(이 권한을 부여할 대상)...** 아래에서 **Add(추가)...** 단추를 클릭합니다.
1. 드롭다운 메뉴에서 **Permission Group(권한 그룹)...** 을 선택한 다음, **Select(선택)...** 를 클릭하여 Groups(그룹) 창을 열어 위에서 만든 그룹을 선택합니다. 

   > [!div class="mx-imgBorder"]
   > ![권한 그룹 추가](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Permission Group(권한 그룹)에 부여된 Permission Role(권한 역할)을 검토합니다. 
   > [!div class="mx-imgBorder"]
   > ![권한 역할 및 그룹 세부 정보](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. **변경 내용 저장** 을 클릭합니다.

## <a name="preparing-for-successfactors-writeback"></a>SuccessFactors 쓰기 저장 준비

SuccessFactors 쓰기 저장 프로비저닝 앱은 Employee Central에서 이메일 및 전화 번호를 설정하는 데 특정 *코드* 값을 사용합니다. 이러한 *코드* 값은 특성 매핑 테이블에서 상수 값으로 설정되며 각 SuccessFactors 인스턴스마다 다릅니다. 이 섹션에서는 이러한 *코드* 값을 캡처하는 단계를 제공합니다.

   > [!NOTE]
   > 이 섹션의 단계를 완료하려면 SuccessFactors 관리자를 참여시키세요. 

### <a name="identify-email-and-phone-number-picklist-names"></a>이메일 및 전화 번호 선택 목록 이름 식별 

SAP SuccessFactors에서 *선택 목록* 은 사용자가 선택할 수 있는 구성 가능한 옵션 세트입니다. 다양한 유형의 이메일 및 전화 번호(예: 회사, 개인, 기타)가 선택 목록을 사용하여 표시됩니다. 이 단계에서는 이메일 및 전화 번호 값을 저장하도록 SuccessFactors 테넌트에 구성된 선택 목록을 식별합니다. 
 
1. SuccessFactors 관리 센터에서 *Manage business configuration(비즈니스 구성 관리)* 을 검색합니다. 

   > [!div class="mx-imgBorder"]
   > ![Manage business configuration](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. **HRIS Elements(HRIS 요소)** 아래에서 **emailInfo** 를 선택하고 **email-type(이메일 유형)** 필드에 대해 *Details(세부 정보)* 를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![이메일 정보 가져오기](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. **email-type** 세부 정보 페이지에서 이 필드와 연결된 선택 목록의 이름을 적어 둡니다. 기본적으로 **ecEmailType** 입니다. 그러나 사용자의 테넌트에서는 다를 수 있습니다. 

   > [!div class="mx-imgBorder"]
   > ![이메일 선택 목록 식별](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. **HRIS Elements** 아래에서 **phoneInfo** 를 선택하고 **phone-type(전화 유형)** 필드에 대해 *Details* 를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![전화 정보 가져오기](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. **phone-type** 세부 정보 페이지에서 이 필드와 연결된 선택 목록의 이름을 적어 둡니다. 기본적으로 **ecPhoneType** 입니다. 그러나 사용자의 테넌트에서는 다를 수 있습니다. 

   > [!div class="mx-imgBorder"]
   > ![전화 선택 목록 확인](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>emailType 상수 값 검색

1. SuccessFactors 관리 센터에서 *Picklist Center(선택 목록 센터)* 를 검색하여 엽니다. 
1. 이전 섹션에서 캡처한 이메일 선택 목록 이름(예: ecEmailType)을 사용하여 이메일 선택 목록을 찾습니다. 

   > [!div class="mx-imgBorder"]
   > ![이메일 유형 선택 목록 찾기](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. 활성 이메일 선택 목록을 엽니다. 

   > [!div class="mx-imgBorder"]
   > ![활성 이메일 유형 선택 목록 열기](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. 이메일 유형 선택 목록 페이지에서 *Business(회사)* 이메일 유형을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![Business 이메일 유형 선택](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. *Business* 이메일과 연결된 **Option ID(옵션 ID)** 를 적어 둡니다. 이는 특성 매핑 테이블에서 *emailType* 과 함께 사용할 코드입니다.

   > [!div class="mx-imgBorder"]
   > ![이메일 유형 코드 가져오기](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > 값을 복사할 때 쉼표 문자를 삭제하세요. 예를 들어 **Option ID** 값이 *8,448* 인 경우 Azure AD의 *emailType* 을 *8448* 상수 숫자(쉼표 문자 없음)로 설정합니다. 

### <a name="retrieve-constant-value-for-phonetype"></a>phoneType 상수 값 검색

1. SuccessFactors 관리 센터에서 *Picklist Center(선택 목록 센터)* 를 검색하여 엽니다. 
1. 이전 섹션에서 캡처한 전화 선택 목록 이름을 사용하여 전화 선택 목록을 찾습니다. 

   > [!div class="mx-imgBorder"]
   > ![전화 유형 선택 목록 찾기](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. 활성 전화 선택 목록을 엽니다. 

   > [!div class="mx-imgBorder"]
   > ![활성 전화 유형 선택 목록 열기](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. 전화 유형 선택 목록 페이지에서 **Picklist Values(선택 목록 값)** 아래에 나열된 여러 전화 유형을 검토합니다.

   > [!div class="mx-imgBorder"]
   > ![전화 유형 검토](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. *Business* 전화와 연결된 **Option ID** 를 적어 둡니다. 이는 특성 매핑 테이블에서 *businessPhoneType* 과 함께 사용할 코드입니다.

   > [!div class="mx-imgBorder"]
   > ![회사 전화 코드 가져오기](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. *휴대폰* 과 연결된 **옵션 ID** 를 적어 둡니다. 특성 매핑 테이블에서 *cellPhoneType* 과 함께 사용할 코드입니다.

   > [!div class="mx-imgBorder"]
   > ![휴대폰 코드 가져오기](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > 값을 복사할 때 쉼표 문자를 삭제하세요. 예를 들어 **Option ID** 값이 *10,606* 인 경우 Azure AD의 *cellPhoneType* 을 *10606* 상수 숫자(쉼표 문자 없음)로 설정합니다. 


## <a name="configuring-successfactors-writeback-app"></a>SuccessFactors 쓰기 저장 앱 구성

이 섹션에서는 다음 단계에 대해 설명합니다. 

* [프로비저닝 커넥터 앱 추가 및 SuccessFactors에 대한 연결 구성](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [특성 매핑 구성](#part-2-configure-attribute-mappings)
* [사용자 프로비저닝 사용 및 시작](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1부: 프로비저닝 커넥터 앱 추가 및 SuccessFactors에 대한 연결 구성

**SuccessFactors 쓰기 저장을 구성하려면 다음을 수행합니다.**

1. <https://portal.azure.com> 로 이동

2. 왼쪽 탐색 모음에서 **Azure Active Directory** 를 선택합니다.

3. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

4. **애플리케이션 추가** 를 선택하고 **모두** 범주를 선택합니다.

5. **SuccessFactors 쓰기 저장** 을 검색하고, 갤러리에서 해당 앱을 추가합니다.

6. 앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전** 을 선택합니다.

7. **프로비저닝** **모드** 를 **자동** 으로 변경합니다.

8. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리 사용자 이름** – 회사 ID가 추가된 SuccessFactors API 사용자 계정의 사용자 이름을 입력합니다. 이는 **username\@companyID** 형식입니다.

   * **관리자 암호 –** SuccessFactors API 사용자 계정의 암호를 입력합니다. 

   * **테넌트 URL –** SuccessFactors OData API 서비스 엔드포인트의 이름을 입력합니다. http 또는 https 없이 서버의 호스트 이름만 입력합니다. 이 값은 `api4.successfactors.com`과 같습니다.

   * **알림 메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.
    > [!NOTE]
    > Azure AD 프로비전 서비스는 프로비전 작업이 [격리](../app-provisioning/application-provisioning-quarantine-status.md) 상태가 되면 이메일 알림을 보냅니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 실패하면 SuccessFactors 자격 증명 및 URL이 유효한지 다시 확인합니다.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 자격 증명이 성공적으로 저장되면 **매핑** 섹션에 기본 매핑이 표시됩니다. 특성 매핑이 표시되지 않으면 페이지를 새로 고치세요.  

### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성

이 섹션에서는 사용자 데이터가 SuccessFactors에서 Active Directory로 이동하는 방식을 구성합니다.

1. [프로비저닝] 탭의 **매핑** 아래에서 **Azure Active Directory 사용자 프로비저닝** 을 클릭합니다.

1. **원본 개체 범위** 필드에서 특성 기반 필터 세트를 정의하여 쓰기 저장할 Azure AD의 사용자 세트를 선택할 수 있습니다. 기본 범위는 "Azure AD의 모든 사용자"입니다. 
   > [!TIP]
   > 프로비전 앱을 처음 구성하는 경우 특성 매핑 및 식을 테스트하고 확인하여 원하는 결과를 제공하는지 확인해야 합니다. **원본 개체 범위** 아래의 범위 지정 필터를 통해 Azure AD에서 소수의 테스트 사용자를 사용하여 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **대상 개체 작업** 필드는 **업데이트** 작업만 지원합니다.

1. **특성 매핑** 섹션 아래의 매핑 테이블에서 다음 Azure Active Directory 특성을 SuccessFactors에 매핑할 수 있습니다. 아래 표에는 쓰기 저장 특성을 매핑하는 방법에 대한 지침이 나와 있습니다. 

   | \# | Azure AD 특성 | SuccessFactors 특성 | 설명 |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | 기본적으로 이 특성은 일치하는 식별자입니다. employeeId 대신 personIdExternal과 동일한 값을 SuccessFactors에 저장할 수 있는 다른 Azure AD 특성을 사용할 수 있습니다.    |
   | 2 | mail | 이메일 | email 특성 원본을 매핑합니다. 테스트를 위해 userPrincipalName을 email에 매핑할 수 있습니다. |
   | 3 | 8448 | emailType | 이 상수 값은 회사 이메일과 연결된 SuccessFactors ID 값입니다. SuccessFactors 환경과 일치하도록 이 값을 업데이트합니다. 이 값을 설정하는 단계는 섹션 [emailType 상수 값 검색](#retrieve-constant-value-for-emailtype)을 참조하세요. |
   | 4 | true | emailIsPrimary | 이 특성을 사용하여 SuccessFactors에서 회사 이메일을 기본 이메일로 설정합니다. 회사 이메일이 기본 이메일이 아닌 경우 이 플래그를 false로 설정합니다. |
   | 5 | userPrincipalName | [custom01 – custom15] | **새 매핑 추가** 를 사용하면 필요에 따라 userPrincipalName 또는 Azure AD 특성을 SuccessFactors User 개체에서 사용할 수 있는 사용자 지정 특성에 쓸 수 있습니다.  |
   | 6 | 온-프레미스 SamAccountName | 사용자 이름 | **새 매핑 추가** 를 사용하면 필요에 따라 온-프레미스 samAccountName을 SuccessFactors 사용자 이름 특성에 매핑할 수 있습니다. [Azure AD Connect 동기화: 디렉터리 확장](../hybrid/how-to-connect-sync-feature-directory-extensions.md)을 사용하여 samAccountName을 Azure AD에 동기화합니다. 원본 드롭다운에서 *extension_yourTenantGUID_samAccountName* 으로 표시됩니다. |
   | 7 | SSO | loginMethod | SuccessFactors 테넌트가 [부분 SSO](https://apps.support.sap.com/sap/support/knowledge/en/2320766)에 대해 설정된 경우 [새 매핑 추가]를 사용하면 필요에 따라 loginMethod를 상수 값인 "SSO" 또는 "PWD"로 설정할 수 있습니다. |
   | 8 | telephoneNumber | businessPhoneNumber | 이 매핑을 사용하여 *telephoneNumber* 를 Azure AD에서 SuccessFactors 회사/직장 전화 번호로 이동할 수 있습니다. |
   | 9 | 10605 | businessPhoneType | 이 상수 값은 회사 전화와 연결된 SuccessFactors ID 값입니다. SuccessFactors 환경과 일치하도록 이 값을 업데이트합니다. 이 값을 설정하는 단계는 섹션 [phoneType 상수 값 검색](#retrieve-constant-value-for-phonetype)을 참조하세요. |
   | 10 | true | businessPhoneIsPrimary | 이 특성을 사용하여 회사 전화 번호에 대한 기본 플래그를 설정합니다. 유효한 값은 true 또는 false입니다. |
   | 11 | mobile | cellPhoneNumber | 이 매핑을 사용하여 *telephoneNumber* 를 Azure AD에서 SuccessFactors 회사/직장 전화 번호로 이동할 수 있습니다. |
   | 12 | 10606 | cellPhoneType | 이 상수 값은 휴대폰과 연결된 SuccessFactors ID 값입니다. SuccessFactors 환경과 일치하도록 이 값을 업데이트합니다. 이 값을 설정하는 단계는 섹션 [phoneType 상수 값 검색](#retrieve-constant-value-for-phonetype)을 참조하세요. |
   | 13 | false | cellPhoneIsPrimary | 이 특성을 사용하여 휴대폰 번호에 대한 기본 플래그를 설정합니다. 유효한 값은 true 또는 false입니다. |
   | 14 | [extensionAttribute1-15] | userId | 이 매핑을 사용하여 동일한 사용자에 대한 여러 고용 레코드가 있는 경우 SuccessFactors의 활성 레코드가 업데이트되도록 합니다. 자세한 내용은 [UserID를 사용하여 쓰기 저장 활성화](../app-provisioning/sap-successfactors-integration-reference.md#enabling-writeback-with-userid)를 참조하세요. |
 
1. 특성 매핑의 유효성을 검사하고 검토합니다. 
 
    >[!div class="mx-imgBorder"]
    >![쓰기 저장 특성 매핑](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. **저장** 을 클릭하여 매핑을 저장합니다. 다음으로, SuccessFactors 인스턴스에서 phoneType 코드를 사용하도록 JSON 경로 API 식을 업데이트합니다. 
1. **고급 옵션 표시** 를 선택합니다. 

    >[!div class="mx-imgBorder"]
    >![고급 옵션 표시](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. **SuccessFactors 특성 목록 편집** 을 클릭합니다. 

   > [!NOTE] 
   > Azure Portal에 **SuccessFactors 특성 목록 편집** 옵션이 표시되지 않으면 *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* URL을 사용하여 해당 페이지에 액세스합니다. 

1. 이 보기의 **API 식** 열에 커넥터에서 사용하는 JSON 경로 식이 표시됩니다. 
1. 사용자 환경에 해당하는 ID 값(*businessPhoneType* 및 *cellPhoneType*)을 사용하도록 회사 전화 및 휴대폰에 대한 JSON 경로 식을 업데이트합니다. 

    >[!div class="mx-imgBorder"]
    >![전화 JSON 경로 변경](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. **저장** 을 클릭하여 매핑을 저장합니다.

## <a name="enable-and-launch-user-provisioning"></a>사용자 프로비저닝 사용 및 시작

SuccessFactors 프로비저닝 앱 구성이 완료되면 Azure Portal에서 프로비저닝 서비스를 설정할 수 있습니다.

> [!TIP]
> 기본적으로 프로비전 서비스를 켜면 범위 내 모든 사용자의 프로비전 작업이 시작됩니다. 매핑 오류 또는 데이터 문제가 있는 경우 프로비저닝 작업이 실패하고 격리 상태로 전환될 수 있습니다. 이를 방지하기 위해 **원본 개체 범위** 필터를 구성하고 모든 사용자의 전체 동기화를 시작하기 전에 몇몇 테스트 사용자로 특성 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하고 원하는 결과를 제공하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태** 를 **켜기** 로 설정합니다.

1. **범위** 를 선택합니다. 다음 옵션 중 하나를 선택할 수 있습니다. 
   * **모든 사용자 및 그룹 동기화**: **매핑** -> **원본 개체 범위** 아래에 정의된 범위 지정 규칙에 따라 모든 사용자의 매핑된 특성을 Azure AD에서 SuccessFactors로 쓰기 저장하려면 이 옵션을 선택합니다. 
   * **할당된 사용자 및 그룹만 동기화**: **애플리케이션** -> **관리** -> **사용자 및 그룹** 메뉴 옵션에서 이 애플리케이션에 할당한 사용자만의 매핑된 특성을 쓰기 저장하려면 이 옵션을 선택합니다. 이러한 사용자는 **매핑** -> **원본 개체 범위** 아래에 정의된 범위 지정 규칙도 적용됩니다.

   > [!div class="mx-imgBorder"]
   > ![쓰기 저장 범위 선택](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > SuccessFactors 쓰기 저장 프로비저닝 앱은 "그룹 할당"을 지원하지 않습니다. "사용자 할당"만 지원합니다. 

1. **저장** 을 클릭합니다.

1. 이 작업을 수행하면 초기 동기화가 시작되며, 이 경우 동기화하는 데 걸리는 시간이 Azure AD 테넌트의 사용자 수 및 작업에 대해 정의된 범위에 따라 달라질 수 있습니다. 진행률 표시줄을 확인하여 동기화 주기의 진행 상황을 추적할 수 있습니다. 

1. 언제든지 Azure Portal에서 **프로비저닝 로그** 탭을 확인하여 프로비저닝 서비스에서 수행한 작업을 확인합니다. 프로비저닝 로그에는 프로비저닝 서비스에서 수행하는 모든 개별 동기화 이벤트가 나열됩니다. 

1. 초기 동기화가 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

   > [!div class="mx-imgBorder"]
   > ![프로비저닝 진행률 표시줄](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>지원되는 시나리오, 알려진 문제 및 제한 사항

SAP SuccessFactors 통합 참조 가이드의 [쓰기 저장 시나리오 섹션](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

* [Azure AD 및 SAP SuccessFactors 통합 참조에 대해 자세히 알아봅니다.](../app-provisioning/sap-successfactors-integration-reference.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [SuccessFactors와 Azure Active Directory 간에 Single Sign-On을 구성하는 방법을 알아봅니다.](successfactors-tutorial.md)
* [Azure Active Directory와 다른 SaaS 애플리케이션을 통합하는 방법을 알아봅니다.](tutorial-list.md)
* [프로비저닝 구성을 내보내고 가져오는 방법을 알아봅니다.](../app-provisioning/export-import-provisioning-configuration.md)