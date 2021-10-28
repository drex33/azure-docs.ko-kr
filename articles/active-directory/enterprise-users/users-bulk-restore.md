---
title: Azure Active Directory 포털에서 삭제된 사용자 대량 복원 | Microsoft Docs
description: Azure Active Directory의 Azure AD 관리 센터에서 삭제된 사용자 대량 복원
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0bcef1404830f8973514c19f2536f73a489f08b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985336"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Azure Active Directory에서 삭제된 사용자 대량 복원

Azure AD(Azure Active Directory)는 사용자 대량 복원 작업을 지원하고 사용자, 그룹 및 그룹 구성원의 목록 다운로드를 지원합니다.

## <a name="understand-the-csv-template"></a>CSV 템플릿 이해

CSV 템플릿을 다운로드하고 작성하여 Azure AD 사용자를 대량 복원할 수 있습니다. 다운로드한 CSV 템플릿은 다음 예제와 같이 표시됩니다.

![업로드용 스프레드시트 및 각 행과 열의 용도 및 값에 대한 설명](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>CSV 템플릿 구조

다운로드된 CSV 템플릿의 행은 다음과 같습니다.

- **버전 번호**: 버전 번호가 포함된 첫 번째 행은 CSV 업로드에 포함되어야 합니다.
- **열 머리글**: 열 머리글의 형식은 &lt;*항목 이름*&gt; [PropertyName] &lt;*필수 또는 비워 둠*&gt;입니다. `Object ID [objectId] Required`)을 입력합니다. 템플릿의 일부 이전 버전은 약간 다를 수 있습니다.
- **예제 행**: 템플릿에는 각 열에 대해 허용 가능한 값의 예제 행이 포함되어 있습니다. 예제 행을 제거하고 원하는 항목으로 바꾸어야 합니다.

### <a name="additional-guidance"></a>추가 지침

- 업로드 템플릿의 처음 두 행은 제거하거나 수정하면 안 됩니다. 이렇게 하면 업로드를 처리할 수 없습니다.
- 필수 열이 먼저 나열됩니다.
- 템플릿에 새 열을 추가하지 않는 것이 좋습니다. 새로 추가하는 열은 무시되고 처리되지 않습니다.
- 최대한 자주 최신 버전의 CSV 템플릿을 다운로드하는 것이 좋습니다.

## <a name="to-bulk-restore-users"></a>사용자 대량 복원 방법

1. Azure AD 조직에서 사용자 관리자인 계정으로 [Azure AD 조직에 로그인](https://aad.portal.azure.com)합니다.
1. Azure AD에서 **사용자** > **삭제됨** 을 선택합니다.
1. **삭제된 사용자** 페이지에서 **대량 복원** 을 선택하여 복원할 사용자 속성의 유효한 CSV 파일을 업로드합니다.

    ![삭제된 사용자 페이지에서 대량 복원 명령을 선택합니다.](./media/users-bulk-restore/bulk-restore.png)

1. CSV 템플릿을 열고 복원하려는 각 사용자에 대한 줄을 추가합니다. 유일하게 필요한 값은 **ObjectID** 입니다. 그런 다음 파일을 저장합니다.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="추가하려는 사용자를 나열하는 로컬 CSV 파일 선택":::

1. **대량 복원** 페이지의 **CSV 파일 업로드** 에서 해당 파일을 찾습니다. 파일을 선택하고 **제출** 을 클릭하면 CSV 파일의 유효성 검사가 시작됩니다.
1. 파일 콘텐츠의 유효성 검사가 완료되면 **파일 업로드 성공** 이 표시됩니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과하면 **제출** 을 선택하여 사용자를 복원하는 Azure 대량 작업을 시작합니다.
1. 복원 작업이 완료되면 대량 작업이 성공했다는 알림이 표시됩니다.

오류가 있는 경우 **대량 작업 결과** 페이지에서 결과 파일을 다운로드하고 볼 수 있습니다. 해당 파일에는 각 오류에 대한 이유가 포함되어 있습니다.

## <a name="check-status"></a>상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

[![대량 작업 결과 페이지에서 상태를 확인합니다.](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

다음으로, Azure Portal 또는 PowerShell을 사용하여 Azure AD 조직에서 복원한 사용자가 있는지 확인할 수 있습니다.

## <a name="view-restored-users-in-the-azure-portal"></a>Azure Portal에서 복원된 사용자 보기

1. 조직에서 사용자 관리자 계정으로 [Azure AD 관리 센터에 로그인](https://aad.portal.azure.com)합니다.
1. 탐색 창에서 **Azure Active Directory** 를 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. **표시** 에서 **모든 사용자** 를 선택하여 복원한 사용자가 나열되는지 확인합니다.

### <a name="view-users-with-powershell"></a>PowerShell로 사용자 보기

다음 명령 실행:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

복원한 사용자가 나열되는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 대량 가져오기](users-bulk-add.md)
- [사용자 대량 삭제](users-bulk-delete.md)
- [사용자 목록 다운로드](users-bulk-download.md)
