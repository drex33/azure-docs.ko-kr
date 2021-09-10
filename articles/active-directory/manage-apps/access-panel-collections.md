---
title: Azure Active Directory에서 내 앱 포털에 대한 컬렉션 만들기 | Microsoft Docs
description: 내 앱 컬렉션을 사용하여 최종 사용자에게 더 간단한 내 앱 환경을 위해 내 앱 페이지를 사용자 지정할 수 있습니다. 별도의 탭을 사용하여 애플리케이션을 그룹으로 구성합니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: davidmu
ms.reviewer: lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 454f1d5513e7cee0535251ac6f6b02fd3b699643
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771403"
---
# <a name="create-collections-on-the-my-apps-portal"></a>내 앱 포털에서 컬렉션 만들기

사용자는 내 앱 포털을 사용하여 액세스 권한이 있는 클라우드 기반 애플리케이션을 보고 시작할 수 있습니다. 기본적으로 사용자가 액세스할 수 있는 모든 애플리케이션은 단일 페이지에 함께 나열됩니다. 사용자를 위해 이 페이지를 더 잘 구성하려면 Azure AD Premium P1 또는 P2 라이선스가 있는 경우 컬렉션을 설정할 수 있습니다. 컬렉션을 사용하여 관련된 애플리케이션(예: 작업 역할, 작업 또는 프로젝트별)을 함께 그룹화하고 별도의 탭에 표시할 수 있습니다. 컬렉션은 기본적으로 사용자가 이미 액세스할 수 있는 애플리케이션에 필터를 적용하므로 사용자는 컬렉션에서 해당 애플리케이션에 할당된 애플리케이션만 볼 수 있습니다.

> [!NOTE]
> 이 문서에서는 관리자가 컬렉션을 사용하도록 설정하고 만드는 방법을 설명합니다. 내 앱 포털 및 컬렉션을 사용하는 방법에 대한 최종 사용자 정보는 [컬렉션 액세스 및 사용](../user-help/my-applications-portal-workspaces.md)을 참조하세요.

## <a name="enable-the-latest-my-apps-features"></a>최신 내 앱 기능 사용

1. [**Azure Portal**](https://portal.azure.com/)을 열고 사용자 관리자 또는 전역 관리자로 로그인합니다.

2. **Azure Active Directory** > **사용자 설정** 으로 이동합니다.

3. **사용자 기능 미리 보기** 에서 **사용자 기능 미리 보기 설정 관리** 를 선택합니다.

4. **사용자가 내 앱의 미리 보기 기능을 사용할 수 있음** 에서 다음 옵션 중 하나를 선택합니다.
   * **선택됨** - 특정 그룹에 대해 기능을 사용하도록 설정합니다. **그룹 선택** 옵션을 사용하여 기능을 사용하도록 설정할 그룹을 선택할 수 있습니다.  
   * **모두** - 모든 사용자에 대해 기능을 사용하도록 설정합니다.

> [!NOTE]
> 내 앱 포털을 열려면 사용자는 `https://myapps.microsoft.com` 링크 또는 `https://myapps.microsoft.com/contoso.com`와 같은 사용자 지정된 링크를 사용할 수 있습니다. 새로운 내 앱 환경을 사용하도록 설정하면, **업데이트된 내 애플리케이션 환경을 사용할 수 있습니다** 배너가 내 앱 페이지의 맨 위에 표시되고 사용자는 **사용해 보기** 를 선택하여 새 환경을 볼 수 있습니다. 새 환경 사용을 중지하려면 페이지의 맨 위에 있는 **새 환경 나가기** 배너에서 **예** 를 선택합니다.

## <a name="create-a-collection"></a>컬렉션 만들기

컬렉션을 만들려면 Azure AD Premium P1 또는 P2 라이선스가 있어야 합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 Azure AD Premium P1 또는 P2 라이선스를 사용하여 관리자로 로그인합니다.

2. **Azure Active Directory** > **엔터프라이즈 애플리케이션** 으로 이동합니다.

3. **관리** 에서 **컬렉션** 을 선택합니다.

4. **새 컬렉션** 을 선택합니다. **새 컬렉션** 페이지에서 컬렉션의 **이름** 을 입력합니다(이름에 "collection"을 사용하지 않는 것이 좋음). 그런 다음, **설명** 을 입력합니다.

   ![새 컬렉션 페이지](media/acces-panel-collections/new-collection.png)

5. **애플리케이션** 탭을 선택합니다. **+ 애플리케이션 추가** 를 선택한 다음, **애플리케이션 추가** 페이지에서 컬렉션에 추가하려는 모든 애플리케이션을 선택하거나 **검색** 상자를 사용하여 애플리케이션을 찾습니다.

   ![컬렉션에 애플리케이션 추가](media/acces-panel-collections/add-applications.png)

6. 애플리케이션 추가가 완료되면 **추가** 를 선택합니다. 선택한 애플리케이션 목록이 표시됩니다. 위쪽 화살표를 사용하여 목록에서 애플리케이션의 순서를 변경할 수 있습니다. 애플리케이션을 아래로 이동하거나 컬렉션에서 삭제하려면 **추가** 메뉴( **...** )를 선택합니다.

7. **소유자** 탭을 선택합니다. **+ 사용자 및 그룹 추가** 를 선택한 다음, **사용자 및 그룹 추가** 페이지에서 소유권을 할당하려는 사용자 또는 그룹을 선택합니다. 사용자 및 그룹 선택을 완료한 후 **선택** 을 선택합니다.

9. **사용자 및 그룹** 탭을 선택합니다. **+ 사용자 및 그룹 추가** 를 선택한 다음, **사용자 및 그룹 추가** 페이지에서 컬렉션을 할당하려는 사용자 또는 그룹을 선택합니다. 또는 **검색** 상자를 사용하여 사용자 또는 그룹을 찾을 수 있습니다. 사용자 및 그룹 선택을 완료한 후 **선택** 을 선택합니다.

   ![사용자 및 그룹을 추가합니다.](media/acces-panel-collections/add-users-and-groups.png)

11. **검토 + 생성** 를 선택합니다. 새 컬렉션의 속성이 표시됩니다.

> [!NOTE]
> 관리 컬렉션은 [내 앱 포털](https://myapps.microsoft.com)이 아닌 [Azure Portal](https://portal.azure.com)을 통해 관리됩니다. 예를 들어 사용자 또는 그룹을 소유자로 할당하는 경우 Azure Portal을 통해서만 컬렉션을 관리할 수 있습니다.

## <a name="view-audit-logs"></a>감사 로그 보기

감사 로그는 컬렉션 생성 최종 사용자 작업을 포함하여 내 앱 컬렉션 작업을 기록합니다. 내 앱에서 생성되는 이벤트는 다음과 같습니다.

* 관리 컬렉션 만들기
* 관리 컬렉션 수정
* 관리 컬렉션 삭제
* 셀프 서비스 애플리케이션 추가(최종 사용자)
* 셀프 서비스 애플리케이션 삭제(최종 사용자)

작업 섹션에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **감사 로그** 를 선택하여 [Azure Portal](https://portal.azure.com)에서 감사 로그에 액세스할 수 있습니다. **서비스** 의 경우 **내 앱** 을 선택합니다.

## <a name="get-support-for-my-account-pages"></a>내 계정 페이지에 대한 지원 받기

내 앱 페이지에서 사용자는 **내 계정** > **내 계정 보기** 를 선택하여 계정 설정을 열 수 있습니다. Azure AD **내 계정** 페이지에서 사용자는 보안 정보, 디바이스, 암호 등을 관리할 수 있습니다. 또한 Office 계정 설정에 액세스할 수 있습니다.

Azure AD 계정 페이지 또는 Office 계정 페이지에서 문제에 대한 지원 요청을 제출해야 하는 경우 다음 단계를 따르면 요청이 적절히 라우팅됩니다.

* **Azure AD "내 계정"** 페이지 문제의 경우 Azure Portal 내에서 지원 요청을 여세요. **Azure Portal** > **Azure Active Directory** > **새 지원 요청** 으로 이동합니다.

* **Office "내 계정"** 페이지 문제의 경우 Microsoft 365 관리 센터 내에서 지원 요청을 여세요. **Microsoft 365 관리 센터** > **지원** 으로 이동합니다.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory에서 애플리케이션에 대한 최종 사용자 환경](end-user-experiences.md)
