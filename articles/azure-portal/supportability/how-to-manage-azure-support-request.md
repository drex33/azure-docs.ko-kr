---
title: Azure 지원 요청 관리
description: 지원 요청을 보는 방법 및 메시지를 보내고, 파일을 업로드 하 고, 옵션을 관리 하는 방법에 대해 알아봅니다.
tags: billing
ms.topic: how-to
ms.date: 11/10/2021
ms.openlocfilehash: c74a6245da9023889be151415bce72ba0129881c
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179926"
---
# <a name="manage-an-azure-support-request"></a>Azure 지원 요청 관리

[Azure 지원 요청을 만든](how-to-create-azure-support-request.md)후 [Azure Portal](https://portal.azure.com)에서 관리할 수 있습니다. [Azure 지원 티켓 REST API](/rest/api/support) 또는 [Azure CLI](/cli/azure/azure-cli-support-request)를 사용하여 프로그래밍 방식으로 요청을 만들고 관리할 수도 있습니다.

지원 요청을 관리 하려면 구독 수준에서 [소유자](../../role-based-access-control/built-in-roles.md#owner), [참가자](../../role-based-access-control/built-in-roles.md#contributor)또는 [지원 요청 참가자](../../role-based-access-control/built-in-roles.md#support-request-contributor) 역할이 있어야 합니다. 구독 없이 만든 지원 요청을 관리 하려면 [관리자](../../active-directory/roles/permissions-reference.md)여야 합니다.

## <a name="view-support-requests"></a>지원 요청 보기

**도움말 + 지원** >  **모든 지원 요청** 으로 이동하여 지원 요청의 세부 정보 및 상태를 볼 수 있습니다.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="모든 지원 요청":::

이 페이지에서 지원 요청을 검색, 필터링 및 정렬할 수 있습니다. 요청에 연결된 심각도 및 모든 메시지를 포함하여 세부 정보를 볼 수 있는 지원 요청을 선택합니다.

## <a name="send-a-message"></a>메시지 보내기

1. **모든 지원 요청** 페이지에서 지원 요청을 선택합니다.

1. **지원 요청** 페이지에서 **새 메시지** 를 선택합니다.

1. 메시지를 입력하고 **제출** 을 선택합니다.

## <a name="change-the-severity-level"></a>심각도 수준 변경

> [!NOTE]
> 최대 심각도 수준은 [지원 플랜](https://azure.microsoft.com/support/plans)에 따라 다릅니다.

1. **모든 지원 요청** 페이지에서 지원 요청을 선택합니다.

1. **지원 요청** 페이지에서 **변경** 을 선택합니다.

1. Azure Portal에는 요청이 지원 엔지니어에게 이미 할당되었는지 여부에 따라 다음 두 가지 화면 중 하나가 표시됩니다.

    - 요청이 할당되지 않은 경우 다음과 같은 화면이 표시됩니다. 새 심각도 수준을 선택한 다음 **변경** 을 선택합니다.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="새 심각도 수준 선택":::

    - 요청이 할당된 경우 다음과 같은 화면이 표시됩니다. **확인** 을 선택한 다음 [새 메시지](#send-a-message)를 만들어 심각도 수준에서 변경 사항을 요청합니다.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="새 심각도 수준을 선택할 수 없음":::

## <a name="allow-collection-of-advanced-diagnostic-information"></a>고급 진단 정보 수집 허용

지원 요청을 만들 때 **고급 진단 정보** 섹션에서 **예** 또는 **아니요** 를 선택할 수 있습니다. 이 옵션은 Azure 지원에서 잠재적으로 문제를 해결 하는 데 도움이 될 수 있는 Azure 리소스의 [로그 파일과](how-to-create-azure-support-request.md#advanced-diagnostic-information-logs) 같은 [진단 정보](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 를 수집할 수 있는지 여부를 결정 합니다. Azure Portal를 통해 사례를 만들고 허용할 수 있는 권한이 부여 된 경우에만 Azure 지원에서 고급 진단 정보에 액세스할 수 있습니다.

요청을 만든 후에 **고급 진단 정보** 선택 항목을 변경 하려면 다음을 수행 합니다.

1. **모든 지원 요청** 페이지에서 지원 요청을 선택합니다.

1. **지원 요청** 페이지에서 **고급 진단 정보** 를 확인 하 고 **변경** 을 선택 합니다.

1. **예** 또는 **아니요** 를 선택한 후 **확인** 을 선택하여 확인합니다.

    :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="진단 정보에 대한 권한 부여":::

## <a name="upload-files"></a>파일 업로드

파일 업로드 옵션을 사용하여 진단 파일 또는 지원 요청과 관련이 있다고 생각되는 다른 파일을 업로드할 수 있습니다.

1. **모든 지원 요청** 페이지에서 지원 요청을 선택합니다.

1. **지원 요청** 페이지에서 파일을 찾은 다음 **업로드** 를 선택합니다. 파일이 여러 개인 경우 프로세스를 반복합니다.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="파일 업로드":::.

### <a name="file-upload-guidelines"></a>파일 업로드 지침

파일 업로드 옵션을 사용할 때 다음 지침을 수행합니다.

- 개인 정보를 보호 하려면 업로드 시 개인 정보를 포함 하지 마세요.
- 파일 이름은 110자 이하여야 합니다.
- 둘 이상의 파일을 업로드할 수 없습니다.
- 파일은 4MB보다 클 수 없습니다.
- 모든 파일에는 *.docx* 또는 *.xlsx* 와 같은 파일 이름 확장명이 있어야 합니다. 다음 표에서는 업로드할 수 있는 파일 이름 확장명을 보여줍니다.

| 0-9, A-C    | D-G   | H-N         | O-Q   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | .har        | .odx  | .rar     | .uccapilog | .xlam   |
| .a          | .db   | .hwl        | .oft  | .rdl     | .uccplog   | .xlr    |
| .abc        | .DMP  | .ics        | .old  | .rdlc    | .udcx      | .xls    |
| .adm        | .do_  | .ini        | .one  | .re_     | .vb_       | .xlsb   |
| .aspx       | .doc  | .java       | .osd  | .remove  | .vbs_      | .xlsm   |
| .ATF        | .docm | .jpg        | .OUT  | .ren     | .vcf       | .xlsx   |
| .b          | .docx | .LDF        | .p1   | .rename  | .vsd       | .xlt    |
| .ba_        | .dotm | .letterhead | .pcap | .rft     | .wdb       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rpt     | .wks       | .xml    |
| .blg        | .dtsx | .log        | .pdf  | .rte     | .wma       | .xmla   |
| .CA_        | .eds  | .lpk        | .piz  | .rtf     | .wmv       | .xps    |
| .CAB        | .emf  | .manifest   | .pmls | .run     | .wmz       | .xsd    |
| .cap        | .eml  | .master     | .png  | .saz     | .wps       | .xsn    |
| .catx       | .emz  | .mdmp       | .potx | .sql     | .wpt       | .xxx    |
| .CFG        | .err  | .mof        | .ppt  | .sqlplan | .wsdl      | .z_     |
| .compressed | .etl  | .mp3        | .pptm | .stp     | .wsp       | .z01    |
| .Config     | .evt  | .mpg        | .pptx | .svclog  | .wtl       | .z02    |
| .cpk        | .evtx | .ms_        | .prn  | .tdb     | -          | .zi     |
| .cpp        | .EX   | .msg        | .psf  | .tdf     | -          | .zi_    |
| .cs         | .ex_  | .mso        | .pst  | .text    | -          | .zip    |
| .CSV        | .ex0  | .msu        | .pub  | .thmx    | -          | .zip_   |
| .cvr        | .FRD  | .nfo        | -     | .tif     | -          | .zipp   |
| -           | .gif  | -           | -     | .trc     | -          | .zipped |
| -           | .guid | -           | -     | .TTD     | -          | .zippy  |
| -           | .gz   | -           | -     | .tx_     | -          | .zipx   |
| -           | -     | -           | -     | .txt     | -          | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | .zzz    |

## <a name="close-a-support-request"></a>지원 요청 닫기

지원 요청을 닫려면 [메시지를 보내고](#send-a-message) 요청을 닫고 싶습니다.

## <a name="reopen-a-closed-request"></a>닫힌 요청 다시 열기

닫힌 지원 요청을 다시 열려면 요청을 자동으로 다시 여는 [새 메시지](#send-a-message)를 만듭니다.

## <a name="cancel-a-support-plan"></a>지원 플랜 취소

지원 계획을 취소하려면 [지원 계획 취소를 참조하세요.](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan)

## <a name="next-steps"></a>다음 단계

- 프로세스를 검토하여 [Azure 지원 요청을 만듭니다.](how-to-create-azure-support-request.md)
- [Azure 지원 티켓 REST API](/rest/api/support)대해 알아봅니다.
