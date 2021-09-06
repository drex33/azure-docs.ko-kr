---
title: Azure Active Directory 포털에서 사용자 대량 삭제 | Microsoft Docs
description: Azure Active Directory의 Azure 관리 센터에서 사용자 대량 삭제
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 07/09/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdaa91a5ffed9e75666602490b4829a3a95e2782
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113597832"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Azure Active Directory에서 사용자 대량 삭제

Azure AD(Azure Active Directory) 포털에서 CSV(쉼표로 구분된 값) 파일을 사용하여 그룹 구성원을 대량 삭제하는 방식으로 다수의 구성원을 그룹에서 제거할 수 있습니다.

## <a name="csv-template-structure"></a>CSV 템플릿 구조

![CSV 파일에는 삭제할 사용자의 이름 및 ID가 포함되어 있습니다.](./media/users-bulk-delete/delete-csv-file.png)

다운로드된 CSV 템플릿의 행은 다음과 같습니다.

- **버전 번호**: 버전 번호가 포함된 첫 번째 행은 CSV 업로드에 포함되어야 합니다.
- **열 머리글**: `User name [userPrincipalName] Required`. 이전 버전의 템플릿은 다를 수 있습니다.
- **예제 행**: 템플릿에는 허용되는 값의 예가 포함되어 있습니다. `Example: chris@contoso.com` 예제 행을 제거하고 자체 항목으로 바꾸어야 합니다.

### <a name="additional-guidance"></a>추가 지침

- 템플릿의 처음 두 행은 제거하거나 수정하면 안 됩니다. 제거하거나 수정하면 템플릿을 처리할 수 없습니다.
- 필수 열이 먼저 나열됩니다.
- 템플릿에 새 열을 추가하지 마세요. 새로 추가하는 열은 무시되고 처리되지 않습니다.
- 새로운 변경 작업을 수행하기 전에 최신 버전의 CSV 템플릿을 다운로드합니다.

## <a name="to-bulk-delete-users"></a>사용자를 대량 삭제하려면 다음을 수행합니다.

1. 조직에서 사용자 관리자인 계정으로 [Azure AD 조직에 로그인](https://aad.portal.azure.com)합니다.
1. Azure AD에서 **사용자** > **대량 작업** > **대량 삭제** 를 선택합니다.
1. **사용자 대량 삭제** 페이지에서 **다운로드** 를 선택하여 CSV 템플릿의 최신 버전을 다운로드합니다.
1. CSV 파일을 연 후 삭제할 각 사용자에 대한 줄을 추가합니다. 유일하게 필요한 값은 **사용자 계정 이름** 입니다. 파일을 저장합니다.
1. **사용자 대량 삭제** 페이지의 **csv 파일 업로드** 에서 해당 파일을 찾습니다. 파일을 선택하고 제출을 클릭하면 CSV 파일의 유효성 검사가 시작됩니다.
1. 파일 콘텐츠의 유효성 검사가 완료되면 **파일 업로드 성공** 이 표시됩니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과하면 **제출** 을 선택하여 사용자를 삭제하는 Azure 대량 작업을 시작합니다.
1. 삭제 작업이 완료되면 대량 작업이 성공했다는 알림이 표시됩니다.

오류가 있는 경우 **대량 작업 결과** 페이지에서 결과 파일을 다운로드하고 볼 수 있습니다. 해당 파일에는 각 오류에 대한 이유가 포함되어 있습니다.

## <a name="check-status"></a>상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

   [![대량 작업 결과 페이지에서 삭제 상태를 확인합니다.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

다음으로, Azure Portal 또는 PowerShell을 사용하여 Azure AD 조직에 삭제한 사용자가 있는지 확인할 수 있습니다.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Azure Portal에서 삭제한 사용자 확인

1. 조직에서 사용자 관리자인 계정으로 Azure Portal에 로그인합니다.
1. 탐색 창에서 **Azure Active Directory** 를 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. **표시** 에서 **모든 사용자** 만 선택하여 삭제한 사용자가 더 이상 나열되지 않는지 확인합니다.

### <a name="verify-deleted-users-with-powershell"></a>PowerShell을 사용하여 삭제한 사용자 확인

다음 명령 실행:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

삭제한 사용자가 더 이상 나열되지 않는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [사용자 일괄 추가](users-bulk-add.md)
- [사용자 목록 다운로드](users-bulk-download.md)
- [사용자 대량 복원](users-bulk-restore.md)
