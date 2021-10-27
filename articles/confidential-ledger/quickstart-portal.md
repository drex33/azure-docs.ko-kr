---
title: 빠른 시작 – Azure Portal을 사용하는 Microsoft Azure Confidential Ledger
description: Azure Portal을 통해 Microsoft Azure Confidential Ledger 사용 방법 알아보기
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/18/2021
ms.service: confidential-ledger
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 5db9227303ed6b409dfd1650fc3c3a409ac2ba90
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167823"
---
# <a name="quickstart-create-a-confidential-ledger-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 기밀 원장 만들기

Azure Confidential Ledger는 데이터를 그대로 유지해야 하는 중요한 데이터 로그 및 레코드에 대해 높은 무결성 저장소를 제공하는 클라우드 서비스입니다. Confidential Ledger에 대한 자세한 내용 및 기밀 원장에 저장할 수 있는 항목의 예는 [Microsoft Azure Confidential Ledger 정보](overview.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

이 빠른 시작에서는 [Azure Portal](https://portal.azure.com)을 사용하여 기밀 원장을 만듭니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-confidential-ledger"></a>기밀 원장 만들기

1. Azure Portal 메뉴 또는 홈페이지에서 **리소스 만들기** 를 선택합니다.

1. 검색 상자에 "Confidential Ledger"를 입력합니다.

1. 결과 목록에서 **Confidential Ledger** 를 선택합니다.

1. Confidential Ledger 섹션에서 **만들기** 를 선택합니다.

1. 기밀 원장 만들기 섹션에서 다음 정보를 제공합니다.
    - **이름**: 기밀 원장에 고유한 이름을 제공합니다.
    - **구독**: 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기***를 선택하고 리소스 그룹 이름을 입력합니다.
    - **위치**: 풀 다운 메뉴에서 위치를 선택합니다.
    - 다른 옵션은 기본값으로 그대로 둡니다.
   
1. **보안** 탭을 선택합니다.

1. 이제 "관리자" 역할이 있는 기밀 원장에 Azure AD 기반 또는 인증서 기반 사용자를 추가해야 합니다. 이 빠른 시작에서는 Azure AD 기반 사용자를 추가합니다. **+ AAD 기반 사용자 추가** 를 선택합니다.

1. Azure AD 기반 또는 인증서 기반 사용자를 추가해야 합니다. 오른쪽 창에서 이메일 주소를 검색합니다. 행을 선택한 다음, 창 하단에서 **선택** 을 선택합니다.

1. **원장 역할** 드롭다운 필드에서 **관리자** 를 선택합니다.

1. **검토 + 만들기** 를 선택합니다. 유효성 검사를 통과하면 **만들기** 를 선택합니다.

배포가 완료된 경우 **리소스로 이동** 을 선택합니다.

:::image type="content" source="./media/confidential-ledger-portal-quickstart.png" alt-text="ACL 포털 만들기 화면":::

아래에 나열된 두 개의 속성을 기록합니다.
- **Confidential Ledger 이름**: 이 예제에서는 "test-create-ledger-demo"입니다. 다른 단계에서 이 이름을 사용합니다.
- **원장 엔드포인트**: 예제에서 이 엔드포인트는 `https://test-create-ledger-demo.confidential-ledger.azure.net/`입니다. 

데이터 평면에서 기밀 원장으로 거래하려면 이러한 속성 이름이 필요합니다.
 
## <a name="clean-up-resources"></a>리소스 정리

다른 Confidential Ledger 문서는 이 빠른 시작을 기반으로 합니다. 후속 문서를 계속 진행하려는 경우 이러한 리소스를 그대로 유지하는 것이 좋습니다. 

더 이상 필요 없으면 리소스 그룹을 삭제하고 Confidential Ledger 및 관련 리소스를 삭제합니다. 포털을 통해 리소스 그룹을 삭제하려면:

1.  포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.

1.  **리소스 그룹 삭제** 를 선택합니다.

1.  **리소스 그룹 이름 입력** 상자에 리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Portal을 사용하여 Confidential Ledger를 만들었습니다. Confidential Ledger 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Microsoft Azure Confidential Ledger 개요](overview.md)
