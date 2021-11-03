---
title: Azure Monitor 통합 문서 사용자 스토리지 가져오기
description: 통합 문서 콘텐츠를 사용자의 스토리지에 저장하여 통합 문서를 보호하는 방법을 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: af32a91344d2201899757d806019f70eccd736ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072278"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>사용자 스토리지를 가져와 통합 문서 저장

보호하려는 쿼리 또는 비즈니스 논리가 있는 경우가 있습니다. 통합 문서는 통합 문서 콘텐츠를 사용자의 스토리지에 저장하여 통합 문서를 보호하는 옵션을 제공합니다. 그런 다음 Microsoft 관리 키를 사용하여 스토리지 계정을 암호화하거나 자체 키를 제공하여 암호화를 관리할 수 있습니다. [스토리지 서비스 암호화에 대한 Azure 설명서를 참조하세요.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>관리 ID를 사용하여 통합 문서 저장

1. 사용자의 스토리지에 통합 문서를 저장하려면 먼저 관리 ID를 만들어(모든 서비스 -> 관리 ID) 스토리지 계정에 대한 `Storage Blob Data Contributor` 액세스 권한을 부여해야 합니다. [관리 ID에 대한 Azure 설명서를 참조하세요.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![역할 할당 추가를 보여 주는 스크린샷](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. 새 통합 문서를 만듭니다.
3. **저장** 단추를 선택하여 새 통합 문서를 저장합니다.
4. `Save content to an Azure Storage Account` 옵션이 있습니다. 확인란을 선택하여 Azure Storage 계정에 저장합니다.

    ![* * 저장 * * 대화 상자를 보여 주는 스크린샷](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. 원하는 스토리지 계정 및 컨테이너를 선택합니다. 스토리지 계정 목록은 위에서 선택한 구독에서 가져온 것입니다.

    ![저장소 옵션을 사용 하 여 * * 저장 * * 대화 상자를 보여 주는 스크린샷](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. 그런 다음 **변경** 을 선택하여 이전에 만든 관리 ID를 선택합니다.

    [![ID 변경 대화 상자를 보여 주는 스크린샷](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. 스토리지 옵션을 선택한 후 **저장** 을 눌러 통합 문서를 저장합니다.

## <a name="limitations"></a>제한 사항

- 사용자 지정 스토리지에 저장하는 경우 개별 고정에 대시보드 자체의 보호되는 정보가 포함되므로 통합 문서의 개별 파트를 대시보드에 고정할 수 없습니다. 사용자 지정 스토리지를 사용하는 경우 통합 문서 자체에 대한 링크만 대시보드에 고정할 수 있습니다.
- 사용자 지정 스토리지에 저장한 통합 문서는 항상 사용자 지정 스토리지에 저장되며, 이는 해제할 수 없습니다. 다른 곳에 저장하려면 ‘다른 이름으로 저장’을 사용하여 복사본을 사용자 지정 스토리지에 저장하지 않도록 선택할 수 있습니다.
- Application Insights 리소스의 통합 문서는 ‘레거시’ 통합 문서이며 사용자 지정 스토리지를 지원하지 않습니다. Application Insights 리소스의 최신 통합 문서는 ‘...자세히’ 선택 항목입니다. 레거시 통합 문서에는 저장 시 구독 옵션이 없습니다.

   ![레거시 통합 문서를 보여 주는 스크린샷](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>다음 단계

- 통합 문서에서 [지도](workbooks-map-visualizations.md) 시각화를 만드는 방법을 알아봅니다.
- [통합 문서에서 그룹](../visualize/workbooks-groups.md)을 사용하는 방법을 알아봅니다.