---
title: Azure AD 권한 관리를 사용하여 사용자 지정 Logic Apps 트리거
description: Azure Active Directory 권한 관리에서 사용자 지정 Logic Apps를 구성하고 사용하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: karenhoran
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 11/02/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdd706be5107558422971336cdb598c1148d6f83
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511670"
---
# <a name="trigger-custom-logic-apps-with-azure-ad-entitlement-management"></a>Azure AD 권한 관리를 사용하여 사용자 지정 Logic Apps 트리거


[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)를 사용하여 사용자 지정 워크플로를 자동화하고 앱과 서비스를 한 곳에서 연결할 수 있습니다. 사용자는 권한 관리와 Logic Apps를 통합하여 거버넌스 워크플로를 핵심 권한 관리 사용 사례 이상으로 확장할 수 있습니다.

그런 다음, 이러한 Logic Apps는 액세스 패키지를 부여하거나 요청하는 경우와 같은 권한 관리 사용 사례에 따라 실행되도록 트리거할 수 있습니다. 예를 들어 관리자는 사용자 지정 Logic App을 만들고 권한 관리에 연결할 수 있으므로 사용자가 액세스 패키지를 요청하면 사용자에게 타사 SAAS 앱(예: Salesforce)의 특정 특성이 할당되거나 사용자 지정 이메일이 보내지도록 하는 Logic App이 트리거됩니다.

Logic Apps와 통합할 수 있는 권한 관리 사용 사례는 다음과 같습니다.  

- 액세스 패키지가 요청되는 경우  

- 액세스 패키지 요청이 허용되는 경우  

- 액세스 패키지 할당이 만료되는 경우  

Logic Apps에 대한 이러한 트리거는 **규칙** 이라는 액세스 패키지 정책 내의 새 탭에서 제어됩니다. 또한 [카탈로그] 페이지의 **사용자 지정 확장** 탭에는 지정된 카탈로그에 대해 추가된 모든 Logic Apps가 표시됩니다. 이 문서에서는 권한 관리에서 논리 앱을 만들고, 카탈로그와 액세스 패키지에 추가하는 방법에 대해 설명합니다.

## <a name="create-and-add-a-logic-app-to-a-catalog-for-use-in-entitlement-management"></a>권한 관리에 사용할 논리 앱을 만들고 카탈로그에 추가 

**필수 역할:** 전역 관리자, Identity Governance 관리자, 카탈로그 소유자 또는 리소스 그룹 소유자 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. Azure Portal에서 **Azure Active Directory** 를 선택한 다음, **ID 거버넌스** 를 선택합니다. 

1. 왼쪽 메뉴에서 **카탈로그** 를 선택합니다. 

1. 왼쪽 메뉴에서 **사용자 지정 확장(미리 보기)** 을 선택합니다. 

1. 헤더 탐색 모음에서 **사용자 지정 확장 추가** 를 선택합니다.  

1. **기본 사항** 탭에서 사용자 지정 확장의 이름(추가하는 연결된 Logic App)과 워크플로에 대한 설명을 입력합니다. 이러한 필드는 앞으로 [카탈로그]의 **사용자 지정 확장** 탭에 표시됩니다. 

    ![사용자 지정 확장을 만드는 창](./media/entitlement-management-logic-apps/create-custom-extension.png)


1. 그런 다음, **세부 정보** 탭으로 이동합니다. 

1. '새 논리 앱 만들기' 필드에서 **예** 를 선택합니다. 그렇지 않고 기존 논리 앱을 사용하려면 **아니요** 를 선택하고 9단계로 이동합니다. '예'를 선택한 경우 아래 옵션 중 하나를 선택하고 9단계로 이동합니다. 

    1. 새 애플리케이션을 새 Logic App의 기반으로 사용하려면 **새 Azure AD 애플리케이션 만들기** 를 선택합니다. 또는
    
        ![논리 앱에 대한 새 앱을 선택하는 창](./media/entitlement-management-logic-apps/new-app-selection.png)

    1. 기존 애플리케이션을 새 Logic App의 기반으로 사용하려면 **기존 Azure AD 애플리케이션** 을 선택합니다.
    
        ![논리 앱에 대한 기존 앱을 선택하는 창](./media/entitlement-management-logic-apps/existing-app-selection.png)

    > [!Note]    
    > 나중에 논리 앱 디자이너에서 Logic App을 통해 수행하는 작업을 편집할 수 있습니다. 이렇게 하려면 **카탈로그** 의 **사용자 지정 확장** 탭에서 만든 Logic App을 선택합니다.  

1. 다음으로, **구독 ID**, **리소스 그룹**, **논리 앱 이름** 을 입력합니다. 

1. 그런 다음, **유효성 검사 및 만들기** 를 선택합니다. 

1. 사용자 지정 확장에 대한 요약을 검토하고, Logic App 설명선에 대한 세부 정보가 올바른지 확인합니다. 그런 다음 **만들기** 를 선택합니다.

    ![사용자 지정 확장 요약의 예](./media/entitlement-management-logic-apps/custom-extension-summary.png)

1. 연결된 Logic App에 대한 이 사용자 지정 확장은 이제 [카탈로그] 아래의 [사용자 지정 확장] 탭에 표시됩니다. 이는 액세스 패키지 정책에서 호출할 수 있습니다.


## <a name="edit-a-linked-logic-app"></a>연결된 Logic App 편집 

**필수 역할:** 전역 관리자, Identity Governance 관리자 또는 카탈로그 소유자 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. Azure Portal에서 **Azure Active Directory** 를 선택한 다음, **ID 거버넌스** 를 선택합니다. 

1. 왼쪽 메뉴에서 **카탈로그** 를 선택합니다. 

1. 왼쪽 메뉴에서 **사용자 지정 확장** 을 선택합니다. 

1. 여기에는 이 카탈로그에 추가한 모든 사용자 지정 확장(Logic Apps)가 표시됩니다. Logic App 워크플로를 편집하거나 새로 추가된 Logic App에 대한 워크플로를 만들려면 **엔드포인트** 아래에서 Logic App 사용자 지정 확장을 선택합니다. 그러면 논리 앱 디자이너가 열리고 워크플로를 만들 수 있습니다.  

 Logic App 워크플로를 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 Azure Logic Apps를 사용하여 자동화된 워크플로 만들기](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow)를 참조하세요.

## <a name="add-custom-extension-to-access-package-policy"></a>액세스 패키지 정책에 사용자 지정 확장 추가 

**필수 역할:** 전역 관리자, Identity Governance 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. Azure Portal에서 **Azure Active Directory** 를 선택한 다음, **ID 거버넌스** 를 선택합니다. 

1. 왼쪽 메뉴에서 **액세스 패키지** 를 선택합니다. 

1. 사용자 지정 확장(Logic App)을 새 액세스 패키지에 추가하려면 **새 액세스 패키지** 를 선택합니다. 또는 이미 만들어진 액세스 패키지 목록에서 사용자 지정 확장(Logic App)을 추가하려는 액세스 패키지를 선택합니다.  

    > [!NOTE]  
    > 액세스 패키지를 만드는 방법에 대한 자세한 내용은 [권한 관리에서 새 액세스 패키지 만들기](entitlement-management-access-package-create.md)를 참조하세요.  기존 액세스 패키지를 편집하는 방법에 대한 자세한 내용은 [Azure AD 권한 관리에서 액세스 패키지에 대한 요청 설정 변경](entitlement-management-access-package-request-policy.md#open-and-edit-an-existing-policy-of-request-settings)을 참조하세요. 

1. 액세스 패키지의 정책 설정에서 **규칙(미리 보기)** 탭으로 이동합니다. 

1. **경우** 아래의 메뉴에서 이 사용자 지정 확장(Logic App)에 대한 트리거로 사용할 액세스 패키지 이벤트를 선택합니다. 예를 들어 사용자가 액세스 패키지를 요청하는 경우에만 사용자 지정 확장 Logic App 워크플로를 트리거하려면 **요청이 만들어지는 경우** 를 선택합니다. 

1. **수행** 아래의 메뉴에서 액세스 패키지에 추가하려는 사용자 지정 확장(Logic App)을 선택합니다. '경우' 필드에서 선택한 이벤트가 발생하면 선택한 '수행' 작업이 실행됩니다.  

1. 사용자 지정 확장을 새 액세스 패키지에 추가하려면 **만들기** 를 선택합니다. 기존 액세스 패키지에 추가하려면 **업데이트** 를 선택합니다.

    ![액세스 패키지에 논리 앱 추가](./media/entitlement-management-logic-apps/add-logic-apps-access-package.png)

## <a name="troubleshooting-and-validation"></a>문제 해결 및 유효성 검사 

액세스 패키지 **수행** 옵션으로 호출하는 경우 사용자 지정 확장에서 연결된 Logic App을 올바르게 트리거했는지 확인하려면 Logic App 로그를 확인할 수 있습니다. 

특정 Logic App에 대한 개요 페이지에는 Logic App을 마지막으로 실행한 시간의 타임스탬프가 표시됩니다. 또한 연결된 사용자 지정 확장을 사용하는 리소스 그룹에 대한 리소스 그룹 개요는 올바르게 구성된 경우 해당 사용자 지정 확장의 이름을 개요에 표시합니다.  

## <a name="next-steps"></a>다음 단계