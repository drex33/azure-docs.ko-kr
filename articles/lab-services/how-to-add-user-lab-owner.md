---
title: Azure Lab Services의 랩에 소유자를 추가하는 방법
description: 이 문서에서는 관리자가 Azure Lab Services의 랩에 사용자를 소유자로 추가하는 방법을 보여 줍니다.
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 4b46dd08a3b9527cdfc7bdf766ee4976e67656d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89482672"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Azure Lab Services의 기존 랩에 소유자를 추가하는 방법
이 문서에서는 관리자가 기존 랩에 소유자를 추가하는 방법을 보여 줍니다.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>랩 계정의 읽기 권한자 역할에 사용자 추가
사용자를 기존 랩에 추가 소유자로 추가하려면 먼저 사용자에게 랩 계정에 대한 **읽기** 권한을 부여해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스** 를 선택합니다. **Lab Services** 를 검색한 다음 선택합니다.
3. 목록에서 **랩 계정** 을 선택합니다. 
2. **랩 계정 페이지** 의 왼쪽 메뉴에서 **IAM(액세스 제어)** 을 선택합니다. 
2. **IAM(액세스 제어)** 페이지의 툴바에서 **추가** 를 선택하고 **역할 할당 추가** 를 선택합니다.

    ![랩 계정에 대한 역할 할당 ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. **역할 할당 추가** 페이지에서 다음 단계를 수행합니다. 
    1. **역할** 에 대해 **읽기 권한자** 를 선택합니다. 
    2. 사용자를 선택합니다. 
    3. **저장** 을 선택합니다. 

        ![랩 계정의 읽기 권한자 역할에 사용자 추가 ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>랩의 소유자 역할에 사용자 추가

> [!NOTE]
> 사용자에게 랩에 대한 읽기 권한자 액세스 권한만 있는 경우 랩은 labs.azure.com에 표시되지 않습니다.

1. **랩 계정** 페이지로 돌아가서 왼쪽 메뉴에서 **모든 랩** 을 선택합니다.
2. 사용자를 소유자로 추가할 **랩** 을 선택합니다. 
    
    ![랩 선택 ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. **랩** 페이지의 왼쪽 메뉴에서 **IAM(액세스 제어)** 을 선택합니다.
4. **IAM(액세스 제어)** 페이지의 툴바에서 **추가** 를 선택하고 **역할 할당 추가** 를 선택합니다.
5. **역할 할당 추가** 페이지에서 다음 단계를 수행합니다. 
    1. **역할** 에 대해 **소유자** 를 선택합니다. 
    2. 사용자를 선택합니다. 
    3. **저장** 을 선택합니다. 

## <a name="next-steps"></a>다음 단계
사용자가 [랩 서비스 포털](https://labs.azure.com)에 로그인할 때 랩이 표시되는지 확인합니다.
