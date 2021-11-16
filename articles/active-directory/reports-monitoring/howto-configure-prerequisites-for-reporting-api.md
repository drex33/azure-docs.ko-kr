---
title: Azure Active Directory 보고 API의 필수 구성 요소 | Microsoft Docs
description: Azure AD Reporting API에 액세스하기 위한 필수 구성 요소에 대해 알아보기
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/21/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98899f175bb1f7cb39b06e75bbea220c356d9f0a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995125"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소

[Azure AD(Azure Active Directory) Reporting API](./concept-reporting-api.md)는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 여러 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다.

Reporting API는 [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) 를 사용하여 Web API에 대한 액세스 권한을 부여합니다.

reporting API에 액세스하도록 준비하려면 다음을 수행해야 합니다.

1. [역할 할당](#assign-roles)
2. [라이선스 요구 사항](#license-requirements)
3. [애플리케이션 등록](#register-an-application)
4. [권한 부여](#grant-permissions)
5. [구성 설정 수집](#gather-configuration-settings)

## <a name="assign-roles"></a>역할 할당

API를 통해 보고 데이터에 액세스하려면 다음 역할 중 하나를 할당받아야 합니다.

- 보안 Reader

- 보안 관리자

- 전역 관리자

## <a name="license-requirements"></a>라이선스 요구 사항

테넌트에 대한 로그인 보고서에 액세스하려면 Azure AD 테넌트에 연결된 Azure AD Premium 라이선스가 있어야 합니다. Azure AD 테넌트에 대한 로그인 보고서에 액세스하려면 Azure AD Premium P1(또는 그 이상) 라이선스가 필요합니다. 또는 디렉터리 유형이 Azure AD B2C인 경우 추가 라이선스 요구 사항 없이 API를 통해 로그인 보고서에 액세스할 수 있습니다. 


## <a name="register-an-application"></a>애플리케이션 등록

스크립트를 사용하여 보고 API에 액세스하는 경우에도 등록이 필요합니다. 등록하면 인증 호출에 필요한 **애플리케이션 ID** 가 제공되고, 이를 통해 코드에서 토큰을 받을 수 있습니다.

Azure AD 보고 API에 액세스하도록 디렉터리를 구성하려면 Azure AD 테넌트에서 **글로벌 관리자** 디렉터리 역할의 멤버이기도 한 Azure 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인해야 합니다.

> [!IMPORTANT]
> 이 같은 관리자 권한이 있는 자격 증명 하에서 실행 중인 애플리케이션은 매우 강력할 수 있으므로, 애플리케이션의 ID 및 비밀 자격 증명을 안전하게 보관해야 합니다.
> 

**Azure AD 애플리케이션을 등록하려면:**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory** 를 선택합니다.
   
    ![Azure Portal 메뉴에서 선택한 Azure Active Directory를 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **Azure Active Directory** 페이지에서 **앱 등록** 을 선택합니다.

    ![관리 메뉴에서 선택한 앱 등록을 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. **앱 등록** 페이지에서 **새 등록** 을 선택합니다.

    ![선택된 새 등록을 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. **애플리케이션 등록** 페이지:

    ![이 단계에서 값을 입력하는 애플리케이션 등록 페이지를 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. **이름** 텍스트 상자에서 `Reporting API application`을 입력합니다.

    b. **지원되는 계정 유형** 에서 **이 조직의 계정만** 을 선택합니다.

    다. **리디렉션 URL** 에서 **웹** 텍스트 상자를 선택하고 `https://localhost`를 입력합니다.

    d. **등록** 을 선택합니다. 


## <a name="grant-permissions"></a>권한 부여 

Azure AD 보고 API에 액세스하려면 앱에 다음 두 가지 권한을 부여해야 합니다.  

| API | 사용 권한 |
| --- | --- |
| Microsoft Graph | 디렉터리 데이터 읽기 |
| Microsoft Graph | 모든 감사 로그 데이터 읽기 |

![API 권한 창에서 권한 추가를 선택할 수 있는 위치를 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/api-permissions.png)

다음 섹션에서는 API 설정 단계를 나열합니다.

**API를 사용하도록 애플리케이션에 권한을 부여하려면:**


1. **API 권한** 을 선택한 다음, **권한 추가** 를 선택합니다.

    ![사용 권한 추가를 선택할 수 있는 API 권한 페이지를 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/add-api-permission.png)

2. **API 권한 요청** 페이지에서 **Microsoft Graph** 를 찾습니다.

    ![Azure Active Directory 그래프를 선택할 수 있는 요청 API 권한 페이지를 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/select-microsoft-graph-api.png)

3. **필수 권한** 페이지에서 **애플리케이션 사용 권한** 을 선택합니다. **디렉터리** 확인란을 선택한 다음, **Directory.ReadAll** 을 선택합니다. **AuditLog** 확인란을 선택한 다음, **AuditLog.Read.All** 을 선택합니다. **권한 추가** 를 선택합니다.

    ![애플리케이션 권한을 선택할 수 있는 요청 API 권한 페이지를 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/select-permissions.png)

4. **보고 API 애플리케이션 - API 권한** 페이지에서 **관리자 동의 부여** 를 선택합니다.

    ![관리자 동의 부여를 선택할 수 있는 보고 API 애플리케이션 API 권한 페이지를 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/grant-admin-consent.png)


## <a name="gather-configuration-settings"></a>구성 설정 수집 

이 섹션에서는 디렉터리에서 다음 설정을 가져오는 방법을 보여 줍니다.

- 도메인 이름
- 클라이언트 ID
- 클라이언트 암호 또는 인증서

Reporting API에 대한 호출을 구성하는 경우 이 값이 필요합니다. 인증서는 더 안전하므로 사용하는 것이 좋습니다.

### <a name="get-your-domain-name"></a>도메인 이름 가져오기

**도메인 이름을 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Active Directory** 를 선택합니다.
   
    ![스크린샷은 도메인 이름을 가져오기 위해 Azure Portal 메뉴에서 선택한 Azure Active Directory를 보여줍니다.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **Azure Active Directory** 페이지에서 **사용자 지정 도메인 이름** 을 선택합니다.

    ![Azure Active Directory에서 선택한 사용자 지정 도메인 이름을 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. 도메인 목록에서 도메인 이름을 복사합니다.


### <a name="get-your-applications-client-id"></a>애플리케이션의 클라이언트 ID 가져오기

**애플리케이션의 클라이언트 ID를 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory** 를 클릭합니다.
   
    ![스크린샷은 애플리케이션의 클라이언트 ID를 가져오기 위해 Azure Portal 메뉴에서 선택한 Azure Active Directory를 보여줍니다.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **앱 등록** 페이지에서 애플리케이션을 선택합니다.

3. 애플리케이션 페이지에서 **애플리케이션 ID** 로 이동하고 **복사하려면 클릭** 을 선택합니다.

    ![애플리케이션 ID를 복사할 수 있는 보고 API 애플리케이션 페이지를 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>애플리케이션의 클라이언트 비밀 가져오기

**애플리케이션의 클라이언트 비밀을 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory** 를 클릭합니다.
   
    ![스크린샷은 애플리케이션의 클라이언트 암호를 가져오기 위해 Azure Portal 메뉴에서 선택한 Azure Active Directory를 보여줍니다.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  **앱 등록** 페이지에서 애플리케이션을 선택합니다.

3.  **API 애플리케이션** 페이지에서 **인증서 및 암호** 를 선택하고 **클라이언트 암호** 섹션에서 **+ 새 클라이언트 암호** 를 클릭합니다. 

    ![클라이언트 암호를 추가할 수 있는 인증서 및 암호 페이지를 보여주는 스크린샷입니다.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

4. **클라이언트 암호 추가** 페이지에서 다음을 추가합니다.

    a. **설명** 텍스트 상자에서 `Reporting API`를 입력합니다.

    b. **만료** 로 **In 2 years**(2년)를 선택합니다.

    다. **저장** 을 클릭합니다.

    d. 키 값을 복사합니다.

### <a name="upload-the-certificate-of-your-application"></a>애플리케이션의 인증서 업로드

**인증서를 업로드하려면 다음을 수행합니다.**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Active Directory** 를 선택합니다.
   
    ![스크린샷은 인증서를 업로드하기 위해 Azure Portal 메뉴에서 선택한 Azure Active Directory를 보여줍니다.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **Azure Active Directory** 페이지에서 **앱 등록** 을 선택합니다.
3. 애플리케이션 페이지에서 애플리케이션을 선택합니다.
4. **인증서 및 비밀** 을 선택합니다.
5. **인증서 업로드** 를 선택합니다.
6. 파일 아이콘을 선택하고 인증서로 이동한 다음, **추가** 를 선택합니다.

    ![스크린샷은 인증서 업로드를 보여줍니다.](./media/howto-configure-prerequisites-for-reporting-api/upload-certificate.png)

## <a name="troubleshoot-errors-in-the-reporting-api"></a>보고 API의 오류 문제 해결

이 섹션에는 Microsoft Graph API를 사용하여 활동 보고서에 액세스하는 동안 발생할 수 있는 일반적인 오류 메시지와 해결 단계가 나와 있습니다.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>오류: Microsoft Graph에서 사용자 역할을 가져오지 못함

 그래프 탐색기를 사용하여 로그인할 때 오류가 발생하지 않도록 그래프 탐색기 UI의 두 로그인 단추를 사용하여 계정에 로그인합니다. 

![그래프 탐색기](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>오류: Microsoft Graph에서 Premium 라이선스 확인을 수행하지 못함 

Graph 탐색기를 사용하여 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 왼쪽 탐색에 있는 계정 아래에서 **권한 수정** 을 선택하고 **Tasks.ReadWrite** 및 **Directory.Read.All** 을 선택합니다. 

![권한 수정 UI](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>오류: 테넌트가 B2C가 아니거나 테넌트에 Premium 라이선스가 없음

로그인 보고서에 액세스하려면 Azure Active Directory P1(Premium 1) 라이선스가 필요합니다. 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 Azure AD P1 라이선스를 사용하여 테넌트에게 사용이 허가되었는지 확인합니다.

### <a name="error-the-allowed-roles-does-not-include-user"></a>오류: 허용된 역할에는 사용자가 포함되지 않았습니다. 

 감사 로그에 액세스하거나 API를 사용하여 로그인할 때 발생하는 오류를 방지합니다. 계정이 Azure Active Directory 테넌트에서 **보안 읽기 권한자** 또는  **보고서 읽기 권한자** 역할에 속하는지 확인합니다.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>오류: 애플리케이션에 AAD ‘디렉터리 데이터 읽기’ 권한이 없음 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>오류: 애플리케이션에 Microsoft Graph API '모든 감사 로그 데이터 읽기' 권한이 없음

[Azure Active Directory 보고 API에 액세스하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md)의 단계에 따라 애플리케이션이 적합한 권한 집합으로 실행되고 있는지 확인합니다. 

## <a name="next-steps"></a>다음 단계

* [인증서와 함께 Azure Active Directory reporting API를 사용하여 데이터 가져오기](tutorial-access-api-with-certificates.md).
* [감사 API 참조](/graph/api/resources/directoryaudit) 
* [로그인 활동 보고서 API 참조](/graph/api/resources/signin)
