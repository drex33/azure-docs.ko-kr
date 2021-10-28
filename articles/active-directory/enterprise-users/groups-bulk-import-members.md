---
title: 대량 업로드로 그룹 구성원 추가 또는 만들기 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory 관리 센터에서 대량으로 그룹 구성원을 추가합니다.
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 09/02/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: abccbbddc17c0f82d1e399d0ce1123b44938a830
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129986647"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Azure Active Directory에서 대량으로 그룹 구성원 추가

Azure AD(Azure Active Directory) 포털에서 CSV(쉼표로 구분된 값) 파일을 사용하여 그룹 구성원을 대량 가져오는 방식으로 다수의 구성원을 그룹에 추가할 수 있습니다.

## <a name="understand-the-csv-template"></a>CSV 템플릿의 이해

대량 업로드 CSV 템플릿을 다운로드하고 작성하여 대량으로 Azure AD 그룹 멤버를 추가합니다. CSV 템플릿은 다음 예와 비슷합니다.

![각 행과 열에 대한 용도와 값을 설명하는 업로드 및 콜아웃용 스프레드시트](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>CSV 템플릿 구조

다운로드된 CSV 템플릿의 행은 다음과 같습니다.

- **버전 번호**: 버전 번호가 포함된 첫 번째 행은 CSV 업로드에 포함되어야 합니다.
- **열 머리글**: 열 머리글의 형식은 &lt;*항목 이름*&gt; [PropertyName] &lt;*필수 또는 비워 둠*&gt;입니다. `Member object ID or user principal name [memberObjectIdOrUpn] Required`)을 입력합니다. 템플릿의 일부 이전 버전은 약간 다를 수 있습니다. 그룹 멤버 자격을 변경하는 경우 멤버 개체 ID와 사용자 계정 이름 중 원하는 식별자를 사용할 수 있습니다.
- **예제 행**: 템플릿에는 각 열에 대해 허용 가능한 값의 예제 행이 포함되어 있습니다. 예제 행을 제거하고 원하는 항목으로 바꾸어야 합니다.

### <a name="additional-guidance"></a>추가 지침

- 업로드 템플릿의 처음 두 행은 제거하거나 수정하면 안 됩니다. 이렇게 하면 업로드를 처리할 수 없습니다.
- 필수 열이 먼저 나열됩니다.
- 템플릿에 새 열을 추가하지 않는 것이 좋습니다. 새로 추가하는 열은 무시되고 처리되지 않습니다.
- 가능한 자주 최신 버전의 CSV 템플릿을 다운로드하는 것이 좋습니다.
- 파일을 성공적으로 업로드하려면 사용자의 UPN 또는 개체 ID를 두 개 이상 추가합니다.

## <a name="to-bulk-import-group-members"></a>그룹 구성원을 대량으로 가져오려면

1. 조직의 사용자 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 그룹 소유자는 또한 소유한 그룹의 구성원을 대량으로 가져올 수 있습니다.
1. Azure AD에서 **그룹** > **모든 그룹** 을 선택합니다.
1. 구성원을 추가할 그룹을 열고 **구성원** 을 선택합니다.
1. **구성원** 페이지에서 **구성원 가져오기** 를 선택합니다.
1. **그룹 구성원 대량 가져오기** 페이지에서 **다운로드** 를 선택하여 필요한 그룹 구성원 속성이 적용된 CSV 파일 템플릿을 가져옵니다.

    ![구성원 가져오기 명령은 그룹의 프로필 페이지에 있습니다.](./media/groups-bulk-import-members/import-panel.png)

1. CSV 파일을 열고 그룹으로 가져올 각 그룹 멤버에 대해 하나씩 줄을 추가합니다. 필요한 값은 **멤버 개체 ID** 또는 **사용자 계정 이름** 입니다. 그런 다음 파일을 저장합니다.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="CSV 파일에는 가져올 구성원의 이름 및 ID가 포함되어 있습니다.":::

1. **그룹 구성원 대량 가져오기** 페이지의 **csv 파일 업로드** 에서 해당 파일을 찾습니다. 파일을 선택하면 CSV 파일의 유효성 검사가 시작됩니다.
1. 파일 콘텐츠의 유효성 검사가 완료되면 대량 가져오기 페이지에 **파일 업로드 성공** 이 표시됩니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과하면 **제출** 을 선택하여 그룹으로 그룹 구성원을 가져오는 Azure 대량 작업을 시작합니다.
1. 가져오기 작업이 완료되면 대량 작업이 성공했다는 알림이 표시됩니다.

## <a name="check-import-status"></a>가져오기 상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

[![대량 작업 결과 페이지에서 상태를 확인합니다.](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

대량 작업 내의 각 줄 항목에 대한 자세한 내용을 보려면 **# Success**, **# Failure** 또는 **Total Requests** 열의 값을 선택합니다. 오류가 발생하면 실패 이유가 나열됩니다.

## <a name="bulk-import-service-limits"></a>대량 가져오기 서비스 제한

그룹 구성원 목록을 가져오는 각 대량 활동은 최대 1시간 동안 실행될 수 있습니다. 이를 통해 최대 40,000 명의 멤버 목록을 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 구성원 대량 제거](groups-bulk-remove-members.md)
- [그룹 구성원 다운로드](groups-bulk-download-members.md)
- [모든 그룹의 목록 다운로드](groups-bulk-download.md)
