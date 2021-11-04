---
title: 개별 VSS Azure 구독을 사용 하 여 디렉터리 테 넌 트 변경
description: Azure 구독을 사용 하 여 디렉터리 테 넌 트를 변경 합니다.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: f2d1bb309a841beac4149e48ed48dcfa21f27c34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102953"
---
# <a name="change-directory-tenants-with-your-azure-subscriptions"></a>Azure 구독을 사용 하 여 디렉터리 테 넌 트 변경  

조직에는 여러 Azure 크레딧 구독이 있을 수 있습니다. 조직에서 설정 하는 각 구독은 [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)와 연결 됩니다. (Azure AD)  

Azure AD는 직원 들이 내부 및 외부 리소스에 로그인 하 고 액세스 하는 데 도움이 되는 Microsoft의 클라우드 기반 id 및 액세스 관리 서비스입니다.  

작업 중인 Active Directory를 변경 하거나 [구독을 다른 Active Directory으로 전송](../../role-based-access-control/transfer-subscription.md)해야 할 수 있습니다.  

구독을 활성화 하는 경우 사용 하는 전자 메일을 기반으로 id가 만들어집니다. 해당 id는 조직의 Active Directory 테 넌 트와 연결 되거나 해당 id에 대해 새 디렉터리 테 넌 트가 만들어집니다. Azure Portal의 오른쪽 위에서 사용 중인 id를 볼 수 있습니다.  

![오른쪽 위 모서리에 id가 강조 표시 된 Azure Portal의 스크린샷](media/how-to-change-directory-tenants-visual-studio-azure/identity.png "로그인 된 id는 Azure Portal의 오른쪽 위 모퉁이에 있습니다.")  

여기에서 id를 전환 하거나 디렉터리를 전환할 수 있습니다. 특정 디렉터리에 액세스 하려면 id를 변경 해야 할 수 있습니다.  

로그인 하는 id가 여러 디렉터리와 연결 된 경우 "디렉터리 전환"을 선택 하 여 전환 합니다. 현재 id가 연결 된 디렉터리가 표시 됩니다.  

![Azure Portal에서 디렉터리 및 구독 창의 스크린샷](media/how-to-change-directory-tenants-visual-studio-azure/switch-directory.png "디렉터리 전환을 클릭 하 여 디렉터리를 전환 합니다. 원하는 디렉터리를 선택 합니다.")  

포털 내에서의 환경은 사용한 id와 연결 된 디렉터리에 따라 매우 다릅니다. 디렉터리 테 넌 트를 변경 하려면 관리자가 대상 디렉터리 내에서 사용자로 id를 추가 해야 합니다.  

## <a name="importance-of-changing-your-azure-active-directory-tenant"></a>Azure Active Directory 테 넌 트 변경의 중요도  

Visual Studio 라이선스를 통해 Azure 크레딧 구독을 설정 하는 경우 회사 전자 메일 또는 개인 전자 메일을 사용 하 여 id를 만들 수 있습니다.  

개인 계정을 사용 하 여 신용 구독을 설정 하는 경우 id 및 작업이 조직의 active directory에서 격리 됩니다. 배포 전에 테스트 하 고 배우는 자체 구독이 필요한 앱에서 작업 하 고 있다고 가정해 보겠습니다. 이제 조직의 작업 또는 참조에 액세스할 수 있어야 합니다. 디렉터리의 테 넌 트를 변경 하면 조직의 리소스에 액세스 하 고 사용자가 자신의 리소스에 액세스할 수 있습니다.  

다음은 구독을 변경 하거나 이전할 때 수행 되는 기본 단계를 보여 주는 간단한 다이어그램입니다.

![구독을 변경 하거나 전송 하는 방법을 보여 주는 다이어그램입니다.](media/how-to-change-directory-tenants-visual-studio-azure/change-diagram.png "구독을 변경 하거나 이전할 때 발생 하는 상황에 대 한 다이어그램입니다.")  

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

액세스 권한이 있는 위치 및 방법에 따라 로그인 한 자격 증명에 따라 표시 되는 내용이 결정 됩니다. 이 액세스는 조직의 계층 내에서 서로 다른 수준으로 지정할 수 있습니다. 디렉터리 수준, 구독 수준 또는 리소스 그룹 내에서 액세스 권한을 부여할 수 있습니다.  

![Azure 액세스 수준의 스크린샷](media/how-to-change-directory-tenants-visual-studio-azure/access-management.png "Azure에서 사용할 수 있는 액세스 수준입니다.")  

Access Control 내에서 액세스 수준을 확인 하 고 관리할 수 있습니다. 액세스 수준에 따라 다른 사용자의 구독에 대 한 액세스를 관리할 수도 있습니다.  

![Visual Studio Subscription access control 페이지의 스크린샷](media/how-to-change-directory-tenants-visual-studio-azure/access-control.png "구독에 대 한 액세스를 관리 합니다.")

## <a name="how-to-change-your-azure-directory-tenant"></a>Azure Directory 테 넌 트를 변경 하는 방법

다른 Active Directory에 액세스 하려면 필요한 사용 권한이 있는 활성 계정 및 디렉터리 테 넌 트를 변경 하는 액세스 권한이 필요 합니다. 액세스 하고자 하는 디렉터리 테 넌 트 내의 관리자는 다음과 같이 추가할 수 있습니다.

* 사용자
* 게스트  

추가 하 고 적절 한 사용 권한을 부여 하면 구독 내에서 디렉터리를 전환할 수 있습니다.  

1. 로그인하여 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 사용하려는 구독을 선택합니다.  
2. "디렉터리 변경" 선택  

    ![변경 디렉터리가 강조 표시 된 Contoso Enterprise 구독 페이지의 스크린샷](media/how-to-change-directory-tenants-visual-studio-azure/change-directory.png "디렉터리 변경을 선택 합니다.") 
3. 새 디렉터리를 선택 하는 상자가 나타납니다.  
4. "변경" 선택  

    > [!NOTE]
    > 대상 디렉터리에 대 한 액세스 권한이 없는 경우 표시 되지 않습니다. 구독에 대 한 디렉터리가 변경 된 후에는 성공 메시지가 표시 됩니다.  

    ![디렉터리 유효성 검사 변경 페이지의 스크린샷](media/how-to-change-directory-tenants-visual-studio-azure/change-button.png "드롭다운 목록에서 디렉터리를 선택 하 고 변경 단추를 클릭 합니다.")
5. 구독 페이지에서 "디렉터리 전환"을 선택 하 여 새 디렉터리에 액세스 합니다.  

  ![스위치 디렉터리가 강조 표시 된 구독 페이지의 스크린샷](media/how-to-change-directory-tenants-visual-studio-azure/switch-directories-outlined.png "디렉터리 전환을 클릭 하 여 새 디렉터리에 액세스 합니다.")

대상 디렉터리로 이동 하 고 관리자를 추가 하 여 대상 디렉터리에 액세스 하 고 테 넌 트를 변경할 수도 있습니다. 구독에 관리자를 추가 하는 방법에 대 한 [지침](/visualstudio/subscriptions/cloud-admin.md) 을 따르세요. 완료 되 면 관리자는 두 디렉터리에 대 한 액세스 권한을 가지 며 테 넌 트 디렉터리를 변경할 수 있습니다.  
