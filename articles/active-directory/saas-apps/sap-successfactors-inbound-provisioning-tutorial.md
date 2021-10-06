---
title: '자습서: AD 및 Azure AD에서 SuccessFactors 인바운드 프로비저닝 구성'
description: SuccessFactors로부터의 인바운드 프로비저닝을 구성하는 방법을 알아봅니다.
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: d4a1350999258b47db4a5188fa9eab92aa56e567
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604949"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>자습서: SAP SuccessFactors에서 Active Directory로 사용자 프로비저닝 구성 
이 자습서에서는 사용자를 SuccessFactors Employe Central에서 AD(Active Directory) 및 Azure AD로 프로비저닝하기 위해 수행해야 하는 단계를 보여 주며, 선택적으로 이메일 주소가 SuccessFactors에 쓰기 저장됩니다. 

>[!NOTE]
>SuccessFactors에서 프로비저닝하도록 하려는 사용자에게 온-프레미스 AD 계정과 선택적으로 Azure AD 계정이 필요한 경우 이 자습서를 사용합니다. SuccessFactors의 사용자에게 Azure AD 계정(클라우드 전용 사용자)만 필요한 경우 [SAP SuccessFactors에서 Azure AD로 사용자 프로비저닝 구성](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 자습서를 참조하세요. 


## <a name="overview"></a>개요

[Azure Active Directory 사용자 프로비저닝 서비스](../app-provisioning/user-provisioning.md)는 사용자의 ID 수명 주기를 관리하기 위해 [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)과 통합됩니다. 

Azure AD 사용자 프로비저닝 서비스에서 지원하는 SuccessFactors 사용자 프로비저닝 워크플로를 사용하면 다음과 같은 인적 자원 및 ID 수명 주기 관리 시나리오를 자동화할 수 있습니다.

* **새 직원 고용** - 새 직원이 SuccessFactors에 추가되면 사용자 계정이 Active Directory, Azure Active Directory 및 선택적으로 Microsoft 365 및 [Azure AD에서 지원하는 다른 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 자동으로 만들어지고, 이메일 주소가 SuccessFactors에 쓰기 저장됩니다.

* **직원 특성 및 프로필 업데이트** - 직원 레코드(예: 이름, 직위 및 관리자)가 SuccessFactors에서 업데이트되면 해당 사용자 계정이 Active Directory, Azure Active Directory 및 선택적으로 Microsoft 365 및 [Azure AD에서 지원하는 다른 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 자동으로 업데이트됩니다.

* **직원 해고** - 직원이 SuccessFactors에서 해고되면 해당 사용자 계정이 Active Directory, Azure Active Directory 및 선택적으로 Microsoft 365 및 [Azure AD에서 지원하는 다른 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 자동으로 사용하지 않도록 설정됩니다.

* **직원 재고용** - 직원이 SuccessFactors에서 다시 고용되면 해당 이전 계정이 Active Directory, Azure Active Directory 및 선택적으로 Microsoft 365 및 [Azure AD에서 지원하는 다른 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 기본 설정에 따라 자동으로 다시 활성화되거나 다시 프로비저닝될 수 있습니다.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 SuccessFactors에서 Active Directory로 사용자 프로비저닝 솔루션이 적합한 조직은 다음과 같습니다.

* SuccessFactors 사용자 프로비저닝에 맞게 미리 빌드된 클라우드 기반 솔루션이 필요한 조직

* 사용자를 SuccessFactors에서 Active Directory로 직접 프로비저닝해야 하는 조직

* [SuccessFactors EC(Employe Central)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)에서 가져온 데이터를 사용하여 사용자를 프로비저닝해야 하는 조직

* [SuccessFactors Employee Central(EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)에서 감지된 변경 정보만을 기반으로 하여 참가, 이동 및 탈퇴하는 사용자를 하나 이상의 Active Directory 포리스트, 도메인 및 OU에 동기화해야 하는 조직

* Microsoft 365를 이메일에 사용하는 조직

## <a name="solution-architecture"></a>솔루션 아키텍처

이 섹션에서는 일반적인 하이브리드 환경을 위한 엔드투엔드 사용자 프로비전 솔루션 아키텍처에 대해 설명합니다. 두 가지의 관련된 흐름이 있습니다.

* **신뢰할 수 있는 HR 데이터 흐름 – SuccessFactors에서 온-프레미스 Active Directory로:** 이 흐름에서는 작업자 이벤트(예: 새 고용, 이동, 해고)가 먼저 클라우드 SuccessFactors Employee Central에서 발생한 다음, 이벤트 데이터가 Azure AD 및 프로비저닝 에이전트를 통해 온-프레미스 Active Directory로 이동합니다. 이벤트에 따라 AD에서 만들기/업데이트/사용/사용 안 함 작업이 이루어질 수 있습니다.
* **이메일 쓰기 저장 흐름 - 온-프레미스 Active Directory에서 SuccessFactors로:** Active Directory에서 계정 만들기가 완료되면 Azure AD Connect 동기화를 통해 Azure AD와 동기화되고 이메일 특성이 SuccessFactors에 쓰기 저장될 수 있습니다.

  ![개요](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>엔드투엔드 사용자 데이터 흐름

1. HR 팀이 SuccessFactors Employee Central에서 작업자 트랜잭션(참가자/이동자/탈퇴자 또는 새 고용/이동/해고)을 수행합니다.
2. Azure AD 프로비저닝 서비스에서 SuccessFactors EC에서 예약된 ID 동기화를 실행하고, 온-프레미스 Active Directory와 동기화하기 위해 처리해야 하는 변경 내용을 식별합니다.
3. Azure AD Provisioning Service는 AD 계정 만들기/업데이트/사용/사용 안 함 작업이 포함된 요청 페이로드로 온-프레미스 Azure AD Connect Provisioning Agent를 호출합니다.
4. Azure AD Connect 프로비전 에이전트는 서비스 계정을 사용하여 AD 계정 데이터를 추가/업데이트합니다.
5. Azure AD Connect 동기화 엔진에서 델타 동기화를 실행하여 AD에서 업데이트를 가져옵니다.
6. Active Directory 업데이트는 Azure Active Directory와 동기화됩니다.
7. [SuccessFactors 쓰기 저장 앱](sap-successfactors-writeback-tutorial.md)이 구성되면 일치하는 사용된 특성에 따라 이메일 특성을 SuccessFactors에 쓰기 저장합니다.

## <a name="planning-your-deployment"></a>배포 계획

클라우드 HR 구동 사용자 프로비저닝을 SuccessFactors에서 AD로 수행하도록 구성하려면 다음과 같은 다양한 측면이 포함된 상당한 계획이 필요합니다.
* Azure AD Connect 프로비저닝 에이전트 설정 
* 배포할 AD 사용자 프로비저닝 앱에 대한 SuccessFactors 수
* 일치하는 ID, 특성 매핑, 변환 및 범위 지정 필터

이러한 항목에 대한 포괄적인 지침은 [클라우드 HR 배포 계획](../app-provisioning/plan-cloud-hr-provision.md)을 참조하세요. 지원되는 엔터티, 처리 세부 정보 및 다양한 HR 시나리오에 맞게 통합을 사용자 지정하는 방법에 대한 자세한 내용은 [SAP SuccessFactors 통합 참조](../app-provisioning/sap-successfactors-integration-reference.md)를 참조하세요. 

## <a name="configuring-successfactors-for-the-integration"></a>통합을 위한 SuccessFactors 구성

모든 SuccessFactors 프로비저닝 커넥터의 일반적인 요구 사항은 SuccessFactors OData API를 호출할 수 있는 올바른 권한이 있는 SuccessFactors 계정의 자격 증명이 필요하다는 것입니다. 이 섹션에서는 SuccessFactors에서 서비스 계정을 만들고 적절한 권한을 부여하는 단계에 대해 설명합니다. 

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

1. 동일한 권한 상자에서 **사용자 권한-> 직원 데이터** 로 이동하고 SuccessFactors 테넌트에서 서비스 계정을 읽을 수 있는 특성을 검토합니다. 예를 들어 SuccessFactors에서 *사용자 이름* 특성을 검색하려면 이 특성에 대해 "보기" 권한이 부여되어 있는지 확인합니다. 마찬가지로 보기 권한에 대한 각 속성을 검토합니다. 

    > [!div class="mx-imgBorder"]
    > ![직원 데이터 권한](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >이 프로비저닝 앱에서 검색하는 전체 특성 목록은 [SuccessFactors 특성 참조](../app-provisioning/sap-successfactors-attribute-reference.md)를 참조하세요.

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>사용자 프로비저닝을 SuccessFactors에서 Active Directory로 수행하도록 구성

이 섹션에서는 사용자 계정을 SuccessFactors에서 통합 범위 내의 각 Active Directory 도메인으로 프로비저닝하는 단계를 제공합니다.

* [프로비저닝 커넥터 앱을 추가하고 Provisioning Agent 다운로드](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [온-프레미스 프로비전 에이전트 설치 및 구성](#part-2-install-and-configure-on-premises-provisioning-agents)
* [SuccessFactors 및 Active Directory에 대한 연결 구성](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [특성 매핑 구성](#part-4-configure-attribute-mappings)
* [사용자 프로비저닝 사용 및 시작](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>1부: 프로비저닝 커넥터 앱을 추가하고 Provisioning Agent 다운로드

**프로비저닝을 SuccessFactors에서 Active Directory로 수행하도록 구성하려면 다음을 수행합니다.**

1. <https://portal.azure.com> 로 이동

2. 왼쪽 탐색 모음에서 **Azure Active Directory** 를 선택합니다.

3. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

4. **애플리케이션 추가** 를 선택하고 **모두** 범주를 선택합니다.

5. **SuccessFactors에서 Active Directory로 사용자 프로비저닝** 을 검색하고, 갤러리에서 해당 앱을 추가합니다.

6. 앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전** 을 선택합니다.

7. **프로비저닝** **모드** 를 **자동** 으로 변경합니다.

8. 표시된 정보 배너를 클릭하여 Provisioning Agent를 다운로드합니다. 
   >[!div class="mx-imgBorder"]
   >![에이전트 다운로드](./media/workday-inbound-tutorial/pa-download-agent.png "에이전트 다운로드 화면")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>2부: 온-프레미스 프로비전 에이전트 설치 및 구성

Active Directory 온-프레미스에 프로비저닝하려면 원하는 Active Directory 도메인에 대한 네트워크 액세스 권한이 있는 도메인 조인 서버에 프로비저닝 에이전트를 설치해야 합니다.

다운로드한 에이전트 설치 관리자를 서버 호스트로 전송하고 [설치 에이전트 섹션에](../cloud-sync/how-to-install.md) 나열된 단계에 따라 에이전트 구성을 완료합니다.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>3부: 프로비저닝 앱에서 SuccessFactors 및 Active Directory에 대한 연결 구성
이 단계에서는 Azure Portal에서 SuccessFactors 및 Active Directory와의 연결을 설정합니다. 

1. Azure Portal에서 [1부](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)에서 만든 [SuccessFactors에서 Active Directory로 사용자 프로비저닝] 앱으로 돌아갑니다.

1. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리 사용자 이름** – 회사 ID가 추가된 SuccessFactors API 사용자 계정의 사용자 이름을 입력합니다. 이는 **username\@companyID** 형식입니다.

   * **관리자 암호 –** SuccessFactors API 사용자 계정의 암호를 입력합니다. 

   * **테넌트 URL –** SuccessFactors OData API 서비스 엔드포인트의 이름을 입력합니다. http 또는 https 없이 서버의 호스트 이름만 입력합니다. 이 값은 **\<api-server-name\>.successfactors.com** 과 같습니다.

   * **Active Directory 포리스트 -** 에이전트에 등록된 Active Directory 도메인의 “이름”입니다. 드롭다운에서 프로비전을 위한 대상 도메인을 선택합니다. 이 값은 일반적으로 *contoso.com* 형태의 문자열입니다.

   * **Active Directory 컨테이너 -** 에이전트가 기본적으로 사용자 계정을 만드는 컨테이너 DN을 입력합니다.
        예제: *OU=Users,DC=contoso,DC=com*
        > [!NOTE]
        > 이 설정은 *parentDistinguishedName* 특성이 특성 매핑에 구성되지 않은 경우에만 사용자 계정 생성에 사용됩니다. 이 설정은 사용자 검색 또는 업데이트 작업에는 사용되지 않습니다. 전체 도메인 하위 트리는 검색 작업의 범위에 속합니다.

   * **알림 메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.
     > [!NOTE]
     > Azure AD 프로비전 서비스는 프로비전 작업이 [격리](../app-provisioning/application-provisioning-quarantine-status.md) 상태가 되면 이메일 알림을 보냅니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 실패하면 에이전트 설치 시 구성된 SuccessFactors 자격 증명과 AD 자격 증명이 유효한지 다시 확인합니다.
     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * 자격 증명이 성공적으로 저장되면 **매핑** 섹션에 **SuccessFactors 사용자를 온-프레미스 Active Directory에 동기화** 라는 기본 매핑이 표시됩니다.

### <a name="part-4-configure-attribute-mappings"></a>4부. 특성 매핑 구성

이 섹션에서는 사용자 데이터가 SuccessFactors에서 Active Directory로 이동하는 방식을 구성합니다.

1. [프로비저닝] 탭의 **매핑** 아래에서 **SuccessFactors 사용자를 온-프레미스 Active Directory에 동기화** 를 클릭합니다.

1. **원본 개체 범위** 필드에서 특성 기반 필터 세트를 정의하여 AD로 프로비저닝하는 범위에 포함할 SuccessFactors의 사용자 세트를 선택할 수 있습니다. 기본 범위는 "SuccessFactors의 모든 사용자"입니다. 예제 필터:

   * 예제: personIdExternal이 1000000-2000000(2000000 제외) 사이인 사용자에 대한 범위

      * 특성: personIdExternal

      * 연산자: REGEX 일치

      * 값: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 예제: 정규직 직원만 포함하고 비정규직 직원은 포함하지 않음

      * 특성: EmployeeID

      * 연산자: NULL이 아님

   > [!TIP]
   > 프로비전 앱을 처음 구성하는 경우 특성 매핑 및 식을 테스트하고 확인하여 원하는 결과를 제공하는지 확인해야 합니다. **원본 개체 범위** 아래의 범위 지정 필터를 통해 SuccessFactors에서 소수의 테스트 사용자를 사용하여 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

   > [!CAUTION] 
   > 프로비저닝 엔진의 기본 동작은 범위를 벗어나는 사용자를 사용하지 않도록 설정/삭제하는 것입니다. 이는 SuccessFactors를 AD에 통합하는 데 바람직하지 않을 수 있습니다. 이 기본 동작을 재정의하려면 범위를 [벗어난 사용자 계정 삭제 건너뛰기](../app-provisioning/skip-out-of-scope-deletions.md) 문서를 참조하세요.
  
1. **대상 개체 작업** 필드에서 Active Directory에서 수행할 작업을 전역적으로 필터링할 수 있습니다. **만들기** 및 **업데이트** 가 가장 일반적입니다.

1. **특성 매핑** 섹션에서 개별 SuccessFactors 특성이 Active Directory 특성에 매핑되는 방식을 정의할 수 있습니다.

     >[!NOTE]
     >애플리케이션에서 지원하는 SuccessFactors 특성의 전체 목록은 [SuccessFactors 특성 참조](../app-provisioning/sap-successfactors-attribute-reference.md)를 참조하세요.

1. 기존 특성 매핑을 클릭하여 업데이트하거나 화면 맨 아래에서 **새 매핑 추가** 를 클릭하여 새 매핑을 추가합니다. 개별 특성 매핑은 다음 속성을 지원합니다.

      * **매핑 유형**

         * **직접** – SuccessFactors 특성 값을 변경하지 않고 AD 특성에 씁니다.

         * **상수** - AD 특성에 고정적인 상수 문자열 값을 씁니다.

         * **식** – 하나 이상의 SuccessFactors 특성을 기반으로 하는 사용자 지정 값을 AD 특성에 쓸 수 있습니다. [자세한 내용은 식에 대한 이 문서를 참조하세요](../app-provisioning/functions-for-customizing-application-data.md).

      * **원본 특성** - SuccessFactors의 사용자 특성입니다.

      * **기본값** – 선택 사항입니다. 원본 특성의 값이 비어 있는 경우 매핑에서 이 값을 대신 씁니다.
            이 값을 비워두는 것이 가장 일반적인 구성입니다.

      * **대상 특성** – Active Directory의 사용자 특성입니다.

      * **이 특성을 사용하여 개체 일치** – SuccessFactors와 Active Directory 간에 사용자를 고유하게 식별하는 데 이 매핑을 사용할지 여부를 지정합니다. 이 값은 일반적으로 SuccessFactors의 Worker ID(작업자 ID) 필드에 설정되며, 일반적으로 Active Directory의 직원 ID 특성 중 하나에 매핑됩니다.

      * **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

      * **이 매핑 적용**

         * **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.

         * **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

1. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장** 을 클릭합니다.

속성 매핑 구성이 완료되면 [주문형 프로비저닝](../app-provisioning/provision-on-demand.md)을 사용하여 단일 사용자에 대한 프로비저닝을 테스트한 다음, [사용자 프로비저닝 서비스를 사용하고 시작](#enable-and-launch-user-provisioning)할 수 있습니다.

## <a name="enable-and-launch-user-provisioning"></a>사용자 프로비저닝 사용 및 시작

SuccessFactors 프로비저닝 앱 구성이 완료되고 [주문형 프로비저닝](../app-provisioning/provision-on-demand.md)을 사용하는 단일 사용자에 대한 프로비저닝을 확인하면 Azure Portal에서 프로비저닝 서비스를 설정할 수 있습니다.

> [!TIP]
> 기본적으로 프로비전 서비스를 켜면 범위 내 모든 사용자의 프로비전 작업이 시작됩니다. 매핑 오류 또는 SuccessFactors 데이터 문제가 있는 경우 프로비저닝 작업이 실패하고 격리 상태로 전환될 수 있습니다. 이를 방지하기 위해 **원본 개체 범위** 필터를 구성하고 모든 사용자의 전체 동기화를 시작하기 전에 [온디멘드 프로비저닝](../app-provisioning/provision-on-demand.md)을 사용하는 몇몇 테스트 사용자로 특성 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하고 원하는 결과를 제공하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **프로비저닝** 블레이드로 이동하고 **프로비저닝 시작** 을 클릭합니다.

1. 이 작업을 수행하면 초기 동기화가 시작되며, 이 경우 동기화하는 데 걸리는 시간이 SuccessFactors 테넌트의 사용자 수에 따라 달라질 수 있습니다. 진행률 표시줄을 확인하여 동기화 주기의 진행 상황을 추적할 수 있습니다. 

1. 언제든지 Azure Portal에서 **감사 로그** 탭을 확인하여 프로비전 서비스에서 수행한 작업을 확인합니다. 감사 로그에는 SuccessFactors에서 읽은 다음, Active Directory에 추가 또는 업데이트되는 사용자와 같이 프로비저닝 서비스에서 수행되는 모든 개별 동기화 이벤트가 나열됩니다. 

1. 초기 동기화가 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

   > [!div class="mx-imgBorder"]
   > ![프로비저닝 진행률 표시줄](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>다음 단계

* [인바운드 프로비저닝에 지원되는 SuccessFactors 특성에 대해 자세히 알아봅니다.](../app-provisioning/sap-successfactors-attribute-reference.md)
* [SuccessFactors에 이메일 쓰기 저장을 구성하는 방법을 알아봅니다.](sap-successfactors-writeback-tutorial.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [SuccessFactors와 Azure Active Directory 간에 Single Sign-On을 구성하는 방법을 알아봅니다.](successfactors-tutorial.md)
* [Azure Active Directory와 다른 SaaS 애플리케이션을 통합하는 방법을 알아봅니다.](tutorial-list.md)
* [프로비저닝 구성을 내보내고 가져오는 방법을 알아봅니다.](../app-provisioning/export-import-provisioning-configuration.md)