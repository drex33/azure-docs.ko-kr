---
title: Azure Active Directory 포털에서 그룹 목록 다운로드 | Microsoft Docs
description: Azure Active Directory의 Azure 관리 센터에서 그룹 속성 대량 다운로드
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 09/01/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5422cc8af391a6ee861b6969d881055490b41b4c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129986685"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Azure Active Directory에서 그룹 목록 대량 다운로드

Azure AD(Azure Active Directory) 포털을 사용하여 조직의 모든 그룹 목록을 쉼표로 구분된 값(CSV) 파일에 대량으로 다운로드할 수 있습니다.

## <a name="to-download-a-list-of-groups"></a>그룹 목록을 다운로드하려면 다음을 수행합니다.

1. 조직의 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD에서 **그룹** > **그룹 다운로드** 를 선택합니다.
1. **그룹 다운로드** 페이지에서 **시작** 을 선택하여 그룹이 나열된 CSV 파일을 받습니다.

   ![그룹 다운로드 명령은 모든 그룹 페이지에 있습니다.](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>다운로드 상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

[![대량 작업 결과 페이지에서 상태를 확인합니다.](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>대량 다운로드 서비스 제한

그룹 목록을 다운로드하는 각 대량 활동은 최대 1시간 동안 실행될 수 있습니다. 이를 통해 최소 30만 그룹 목록을 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 구성원 대량 제거](groups-bulk-remove-members.md)
- [그룹 구성원 다운로드](groups-bulk-download-members.md)
