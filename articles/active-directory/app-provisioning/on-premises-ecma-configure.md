---
title: Azure AD ECMA 커넥터 호스트 구성
description: 이 문서에서는 Azure AD ECMA 커넥터 호스트를 구성하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 769b5a312572a2c10f55ee3efbb54191b4f13707
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449695"
---
# <a name="configure-the-azure-ad-ecma-connector-host-and-the-provisioning-agent"></a>Azure AD ECMA 커넥터 호스트 및 프로비저닝 에이전트를 구성합니다.

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. 기능에 대한 액세스를 요청하려면 [액세스 요청 양식](https://aka.ms/onpremprovisioningpublicpreviewaccess)을 사용하세요. 일반 공급을 준비하면서 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

이 문서에서는 Azure AD(Azure Active Directory) ECMA 커넥터 호스트와 프로비저닝 에이전트를 성공적으로 설치한 후 구성하는 방법에 대한 지침을 제공합니다.

이 흐름은 Azure AD ECMA 커넥터 호스트를 설치하고 구성하는 프로세스를 안내합니다. 

 ![설치 흐름을 보여 주는 다이어그램.](./media/on-premises-ecma-configure/flow-1.png)

설치 및 구성에 대한 자세한 내용은 다음을 참조하세요.
   - [Azure AD ECMA 커넥터 호스트의 필수 구성 요소](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
   - [Azure AD ECMA 커넥터 호스트 일반 SQL 커넥터 구성](on-premises-sql-connector-configure.md)
   
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA 커넥터 호스트 구성
Azure AD ECMA 커넥터 호스트 구성은 두 부분으로 진행됩니다.
    
   - **설정 구성**: Azure AD ECMA 커넥터 호스트에서 사용할 포트 및 인증서를 구성합니다. 이 단계는 ECMA 커넥터 호스트가 처음 시작될 때만 수행됩니다.
   - **커넥터 만들기**: 커넥터(예: SQL 또는 LDAP)를 만들어 Azure AD ECMA 커넥터 호스트가 데이터 원본으로 데이터를 내보내거나 가져올 수 있도록 합니다.

### <a name="configure-the-settings"></a>설정 구성
Azure AD ECMA 커넥터 호스트를 처음 시작하면 기본값 **8585** 가 입력된 포트 번호가 표시됩니다. 

 ![설정 구성을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-1.png)

미리 보기의 경우에는 자체 서명된 새 인증서를 생성해야 합니다.

 >[!NOTE]
 >이 미리 보기는 기간이 제한된 인증서를 사용합니다. 자동 생성된 인증서는 자체 서명됩니다. 신뢰할 수 있는 루트의 일부이며 SAN는 호스트 이름과 일치합니다.


### <a name="create-a-connector"></a>커넥터 만들기
이제 Azure AD ECMA 커넥터 호스트에서 사용할 커넥터를 만들어야 합니다. 이 커넥터를 사용하면 ECMA 커넥터 호스트에서 사용자가 만든 커넥터에 대한 데이터 원본으로 데이터를 내보낼 수 있습니다. 필요하면 데이터 가져오기에도 사용할 수 있습니다.

각 개별 커넥터에 대한 구성 단계는 더 길어지며 자체 문서에 제공됩니다.

커넥터를 만들고 구성하려면 [일반 SQL 커넥터](on-premises-sql-connector-configure.md)를 사용합니다. 이 커넥터는 Azure SQL Database 또는 Azure Database for MySQL 같은 Microsoft SQL 데이터베이스에서 작동합니다.

## <a name="establish-connectivity-between-azure-ad-and-the-azure-ad-ecma-connector-host"></a>Azure AD와 Azure AD ECMA 커넥터 호스트 간 연결 설정
다음 섹션에서는 온-프레미스 Azure AD ECMA 커넥터 호스트 및 Azure AD와의 연결을 설정하는 과정을 안내합니다.

### <a name="ensure-the-ecma2host-service-is-running"></a>ECMA2Host 서비스가 실행되고 있는지 확인
1. Azure AD ECMA 커넥터 호스트를 실행하는 서버에서 **시작** 을 선택합니다.
1. 상자에서 **run**, **services.msc** 를 차례로 입력합니다.
1. **서비스** 목록에서 **Microsoft ECMA2Host** 가 있고 실행 중인지 확인합니다. 그렇지 않은 경우 **시작** 을 선택합니다.

   ![서비스가 실행 중임을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-2.png)

### <a name="add-an-enterprise-application"></a>엔터프라이즈 애플리케이션 추가
1. 애플리케이션 관리자 권한으로 Azure Portal에 로그인합니다.
1. 포털에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** 으로 차례로 이동합니다.
1. **새 애플리케이션** 을 선택합니다.

   ![새 애플리케이션 추가를 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-4.png)
1. 갤러리에서 **온-프레미스 프로비저닝** 애플리케이션을 찾고 **만들기** 를 선택합니다.

### <a name="configure-the-application-and-test"></a>애플리케이션 구성 및 테스트
  1. 애플리케이션이 만들어지면 **프로비저닝** 페이지를 선택합니다.
  1. **시작** 을 선택합니다.
  
     ![시작을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-6.png)
  1. **프로비저닝** 페이지에서 **프로비저닝 모드** 를 **자동** 으로 변경합니다.

      ![모드 변경을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-7.png)
  1. **온-프레미스 연결** 섹션에서, 배포한 에이전트를 선택하고 **에이전트 할당** 을 선택합니다.
  
      ![에이전트 할당을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-8.png)</br>

      >[!NOTE]
      >에이전트를 추가한 후 등록이 완료될 때까지 10-20분 동안 기다립니다. 연결 테스트는 등록이 완료될 때까지 작동하지 않습니다.
      >
      >또는 서버에서 프로비저닝 에이전트를 다시 시작하여 에이전트 등록을 강제로 완료할 수 있습니다. 서버로 이동하고, Windows 검색 창에서 **서비스** 를 검색하고, **Azure AD Connect 프로비전 에이전트 서비스** 를 식별하고, 마우스 오른쪽 단추로 해당 서비스를 클릭하고, 다시 시작합니다.

  1. 10분 후 **관리자 자격 증명** 섹션에서 다음 URL을 입력합니다. `"connectorName"` 부분은 ECMA 호스트의 커넥터 이름으로 바꿉니다.

      |속성|값|
      |-----|-----|
      |테넌트 URL|https://localhost:8585/ecma2host_connectorName/scim|

  1. 커넥터를 만들 때 정의한 비밀 토큰 값을 입력합니다.
  1. **연결 테스트** 를 선택하고 1분 동안 기다립니다.
  
     ![연결 테스트를 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-5.png)

     >[!NOTE]
     >연결을 테스트하려면 에이전트를 할당한 후 10-20분 정도 기다려야 합니다. 등록이 완료되지 않은 경우에는 연결이 실패합니다.

  1. 연결 테스트가 성공하면 **저장** 을 선택합니다.</br>
  
     ![테스트 성공을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="configure-whos-in-scope-for-provisioning"></a>프로비저닝 범위에 있는 사용자 구성
이제 Azure AD와 통신하는 Azure AD ECMA 커넥터 호스트가 있으므로 프로비전 범위에 있는 사용자를 구성하기 위해 이동할 수 있습니다. 다음 섹션에서는 사용자의 범위를 지정하는 방법에 대한 정보를 제공합니다.

### <a name="assign-users-to-your-application"></a>애플리케이션에 사용자 할당
Azure AD를 사용하면 애플리케이션에 대한 할당을 기준으로 또는 특정 특성에 대해 필터링하여 프로비저닝해야 하는 사용자의 범위를 지정할 수 있습니다. 프로비저닝 범위에 있는 사용자를 결정하고 필요에 따라 범위 지정 규칙을 정의합니다. 자세한 내용은 [Azure Active Directory에서 앱에 대한 사용자 할당 관리](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)를 참조하세요.

### <a name="configure-your-attribute-mappings"></a>특성 매핑 구성
이제 Azure AD의 사용자 특성을 대상 애플리케이션의 특성에 매핑합니다. Azure AD 프로비저닝 서비스는 프로비저닝을 위한 SCIM 표준을 기반으로 합니다. 이 때문에, 드러나는 특성에 SCIM 네임스페이스가 있습니다. 다음 예제에서는 Azure AD의 **mail** 및 **objectId** 특성을, 애플리케이션의 **Email** 및 **InternalGUID** 특성에 매핑하는 방법을 보여 줍니다.

>[!NOTE]
>기본 매핑은 **userPrincipalName** 을 특성 이름 *PLACEHOLDER* 에 연결합니다. *PLACEHOLDER* 특성을 애플리케이션에 있는 특성으로 변경해야 합니다. 자세한 내용은 [원본 및 대상 시스템의 사용자 일치](customize-application-attributes.md#matching-users-in-the-source-and-target--systems)를 참조하세요.

|Azure AD의 특성 이름|SCIM의 특성 이름|대상 애플리케이션의 특성 이름|
|-----|-----|-----|
|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|Email|
|objectId|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:InternalGUID|InternalGUID|

### <a name="configure-attribute-mapping"></a>특성 매핑 구성
 1. Azure AD 포털의 **엔터프라이즈 애플리케이션** 에서 **프로비전** 페이지를 선택합니다.
 2. **시작** 을 선택합니다.
 3. **매핑** 을 펼치고, **Azure Active Directory 사용자 프로비저닝** 을 선택합니다.
 
    ![Azure Active Directory 사용자 프로비저닝을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-10.png)
 1. **새 매핑 추가** 를 선택합니다.
 
    ![새 매핑 추가를 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-11.png)
 1. 원본 및 대상 특성을 지정하고 **확인** 을 선택합니다.</br>
 
    ![특징 편집 창을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-12.png)


애플리케이션에서 Azure AD로 사용자 특성을 매핑하는 방법에 대한 자세한 내용은 [자습서 - Azure Active Directory에서 SaaS 애플리케이션에 대한 사용자 프로비저닝 특성 매핑 사용자 지정](customize-application-attributes.md)을 참조하세요.

### <a name="test-your-configuration-by-provisioning-users-on-demand"></a>사용자에 대해 주문형 프로비저닝을 수행하여 구성 테스트
구성을 테스트하기 위해 사용자의 주문형 프로비저닝을 사용할 수 있습니다. 주문형 사용자 프로비저닝에 대한 자세한 내용은 [주문형 프로비저닝](provision-on-demand.md)을 참조하세요.

 1. Single Sign-On 창으로 이동한 다음, 프로비저닝 창으로 돌아갑니다. 새 프로비저닝 개요 창에서 **주문형** 을 클릭합니다.
 1. [Azure Active Directory의 주문형 프로비저닝](provision-on-demand.md)에서 설명하는 대로 주문형 사용자 몇 명의 프로비저닝을 테스트합니다.
 
    ![프로비전 테스트를 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-13.png)

### <a name="start-provisioning-users"></a>사용자 프로비저닝 시작
 1. 주문형 프로비전이 성공하면 프로비저닝 구성 페이지로 돌아갑니다. 할당된 사용자 및 그룹으로만 범위가 설정되어 있는지 확인하고 프로비전 상태를 **켜기** 로 설정한 다음, **저장** 을 선택합니다.
 
    ![프로비저닝 시작을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-14.png)

1. 프로비전이 시작될 때까지 몇 분 정도 기다립니다. 최대 40분이 걸릴 수 있습니다. 프로비저닝 작업이 완료되면 다음 섹션에서 설명한 대로 프로비전 상태를 **끄기** 로 변경하고, **저장** 을 선택할 수 있습니다. 이 단계는 프로비저닝 서비스가 나중에 실행되지 않게 합니다.

### <a name="verify-users-were-successfully-provisioned"></a>사용자가 성공적으로 프로비저닝되었는지 확인
대기한 후 데이터 원본을 확인하여 새 사용자가 프로비저닝되고 있는지 확인합니다.

 ![사용자 프로비저닝의 확인을 보여 주는 스크린샷](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="monitor-your-deployment"></a>배포 모니터링

1. 프로비저닝 로그를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
1. Azure Monitor 통합을 사용하여 사용자 지정 경고, 대시보드 및 쿼리를 빌드합니다.
1. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. [격리 상태](https://github.com/MicrosoftDocs/azure-docs-pr/compare/application-provisioning-quarantine-status.md?expand=1)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

- [Azure AD ECMA 커넥터 호스트 필수 조건](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
- [일반 SQL 커넥터](on-premises-sql-connector-configure.md)
