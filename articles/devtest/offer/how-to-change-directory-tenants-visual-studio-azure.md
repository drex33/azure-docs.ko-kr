---
title: 개별 VSS Azure 구독을 통해 디렉터리 테넌트 변경
description: Azure 구독을 통해 디렉터리 테넌트 변경
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 158dee487b8e0e8333f267a7b5e854093582345c
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132868550"
---
# <a name="change-directory-tenants-with-your-azure-subscriptions"></a>Azure 구독을 통해 디렉터리 테넌트 변경  

조직에는 여러 Azure 크레딧 구독이 있을 수 있습니다. 조직에서 설정하는 각 구독은 [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)연결됩니다. (Azure AD)  

Azure AD는 직원이 로그인하여 내부 및 외부 리소스에 액세스하는 데 도움이 되는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다.  

작업 중인 Active Directory를 변경하거나 [구독을 다른 Active Directory 로 전송해야 할 수 있습니다.](../../role-based-access-control/transfer-subscription.md)  

구독을 활성화할 때 사용하는 메일에 따라 ID가 만들어집니다. 해당 ID는 조직의 Active Directory 테넌트 또는 해당 ID에 대한 새 디렉터리 테넌트 중 하나와 연결됩니다. Azure Portal 오른쪽 위에서 사용하는 ID를 볼 수 있습니다.  

![오른쪽 위 모서리에 ID가 강조 표시된 Azure Portal 스크린샷.](media/how-to-change-directory-tenants-visual-studio-azure/identity.png "로그인한 ID는 Azure Portal 오른쪽 위 모서리에 있습니다.")  

여기에서 ID를 전환하거나 디렉터리를 전환할 수 있습니다. 특정 디렉터리에 액세스하려면 ID를 변경해야 할 수 있습니다.  

로그인한 ID가 여러 디렉터리에 연결된 경우 "디렉터리 전환"을 선택하여 전환합니다. 현재 ID가 연결된 디렉터리를 볼 수 있습니다.  

![Azure Portal 디렉터리 및 구독 창의 스크린샷.](media/how-to-change-directory-tenants-visual-studio-azure/switch-directory.png "디렉터리 전환을 클릭하여 디렉터리를 전환합니다. 원하는 디렉터리를 선택합니다.")  

포털 내의 환경은 사용한 ID와 연결된 디렉터리에 따라 크게 달라집니다. 디렉터리 테넌트 변경을 위해 관리자는 대상 디렉터리 내에서 사용자로 ID를 추가해야 합니다.  

## <a name="importance-of-changing-your-azure-active-directory-tenant"></a>Azure Active Directory 테넌트 변경의 중요성  

Visual Studio 라이선스를 통해 Azure 크레딧 구독을 설정하는 경우 업무용 이메일 또는 개인 메일을 사용하여 ID를 만들 수 있습니다.  

개인 계정을 사용하여 신용 구독을 설정하는 경우 ID와 업무는 조직의 Active Directory와 격리됩니다. 배포하기 전에 자체 구독을 테스트하고 학습해야 하는 앱에서 작업했다고 가정해 보겠습니다. 이제 조직의 작업 또는 참조에 액세스해야 합니다. 디렉터리의 테넌트 변경으로 조직의 리소스에 액세스하고 사용자의 리소스에 액세스할 수 있습니다.  

다음은 구독을 변경하거나 양도할 때 수행되는 기본 단계를 보여 주는 간단한 다이어그램입니다.

![구독 변경 또는 양도를 보여 주는 다이어그램.](media/how-to-change-directory-tenants-visual-studio-azure/change-diagram.png "구독을 변경하거나 양도할 때 발생하는 작업의 다이어그램입니다.")  

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

액세스 권한이 있는 위치와 방법은 로그인한 자격 증명에 따라 표시되는 내용을 결정합니다. 이 액세스 권한은 조직의 계층 구조 내의 다양한 수준에서 부여할 수 있습니다. 디렉터리 수준, 구독 수준 또는 리소스 그룹 내에서 액세스 권한을 부여할 수 있습니다.  

![Azure 액세스 수준 스크린샷.](media/how-to-change-directory-tenants-visual-studio-azure/access-management.png "Azure에서 사용할 수 있는 액세스 수준입니다.")  

Access Control 내에서 액세스 수준을 보고 관리할 수 있습니다. 액세스 수준에 따라 구독에 대한 다른 사용자의 액세스를 관리할 수도 있습니다.  

![Visual Studio 구독 액세스 제어 페이지의 스크린샷.](media/how-to-change-directory-tenants-visual-studio-azure/access-control.png "구독에 대한 액세스를 관리합니다.")

## <a name="how-to-change-your-azure-directory-tenant"></a>Azure Directory 테넌트 변경 방법

다른 Active Directory에 액세스하려면 디렉터리 테넌트 변경에 필요한 권한과 액세스 권한이 있는 활성 계정이 필요합니다. 액세스하려는 디렉터리 테넌트 내의 관리자는 다음으로 추가할 수 있습니다.

* 사용자
* 게스트  

추가되고 적절한 권한이 부여되면 구독 내에서 디렉터리를 전환할 수 있습니다.  

1. 로그인하여 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 사용하려는 구독을 선택합니다.  
2. "디렉터리 변경"을 선택합니다.  

    ![디렉터리 변경이 강조 표시된 Contoso Enterprise 구독 페이지의 스크린샷](media/how-to-change-directory-tenants-visual-studio-azure/change-directory.png "디렉터리 변경을 선택합니다.") 
3. 새 디렉터리를 선택하기 위한 상자가 표시됩니다.  
4. "변경" 선택  

    > [!NOTE]
    > 대상 디렉터리에 대한 액세스 권한이 없으면 표시되지 않습니다. 구독에 대한 디렉터리를 변경하면 성공 메시지가 표시됩니다.  

    ![디렉터리 변경 유효성 검사 페이지의 스크린샷.](media/how-to-change-directory-tenants-visual-studio-azure/change-button.png "드롭다운에서 디렉터리를 선택하고 변경 단추를 클릭합니다.")
5. 구독 페이지에서 "디렉터리 전환"을 선택하여 새 디렉터리에 액세스합니다.  

  ![디렉터리 전환이 강조 표시된 구독 페이지의 스크린샷.](media/how-to-change-directory-tenants-visual-studio-azure/switch-directories-outlined.png "디렉터리 전환을 클릭하여 새 디렉터리에 액세스합니다.")

대상 디렉터리로 이동한 후 관리자를 추가하여 대상 디렉터리에 액세스하고 테넌티를 변경할 수도 있습니다. 구독에 관리자를 추가하는 방법에 대한 [지침을](/visualstudio/subscriptions/cloud-admin) 따르세요. 이 작업이 완료되면 관리자는 두 디렉터리에 모두 액세스할 수 있으며 테넌트 디렉터리를 변경할 수 있습니다.  
