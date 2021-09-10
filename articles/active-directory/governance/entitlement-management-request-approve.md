---
title: 액세스 요청 승인 또는 거부 - Azure AD 자격 관리
description: 내 액세스 포털을 사용하여 Azure Active Directory 자격 관리에서 액세스 패키지에 대한 요청을 승인 또는 거부하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: f44390d042f12336c6556d05c4fe1332e822e068
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112963765"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 요청 승인 또는 거부

Azure AD 자격 관리를 사용하여 액세스 패키지에 대한 승인을 요구하는 정책을 구성하고 하나 이상의 승인자를 선택할 수 있습니다. 이 문서에서는 지정된 승인자가 액세스 패키지에 대한 요청을 승인 또는 거부하는 방법을 설명합니다.

## <a name="open-request"></a>요청 열기

액세스 요청을 승인하거나 거부하는 첫 번째 단계는 승인 보류 중인 액세스 요청을 찾아서 여는 것입니다. 액세스 요청을 여는 방법에는 두 가지가 있습니다.

**필수 역할:** 승인자

1. 요청을 승인 또는 거부하라고 요청하는 Microsoft Azure의 이메일을 찾습니다. 이메일 예는 다음과 같습니다.

    ![패키지 이메일에 액세스하는 요청 승인](./media/entitlement-management-shared/approver-request-email.png)

1. **요청 승인 또는 거부** 링크를 클릭하여 액세스 요청을 엽니다.

1. 내 액세스 포털에 로그인합니다.

이메일이 없는 경우 다음 단계에 따라 승인 대기 중인 액세스 요청을 찾을 수 있습니다.

1. 내 액세스 포털에 [https://myaccess.microsoft.com](https://myaccess.microsoft.com)으로 로그인합니다.  (미국 정부의 경우 내 액세스 포털 링크의 도메인은 `myaccess.microsoft.us`입니다.)

1. 왼쪽 메뉴에서 **승인** 을 클릭하면 승인 대기 중인 액세스 요청 목록이 표시됩니다.

1. **보류 중** 탭에서 요청을 찾습니다.

## <a name="view-requestors-answers-to-questions"></a>요청자의 질문에 대한 답변 보기

1. 내 액세스에서 **승인** 탭으로 이동합니다.

1. 승인하려는 요청으로 이동하여 **세부 정보** 를 클릭합니다. 결정을 내릴 준비가 되었으면 **승인** 또는 **거부** 를 클릭할 수도 있습니다.

1. **요청 세부 정보** 를 클릭합니다.

    ![내 액세스 포털 - 액세스 요청 - 요청 세부 정보 클릭](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. 요청자에 의해 제공되는 정보는 패널의 맨 아래에 있습니다.

    ![요청에 대한 세부 정보를 보여 주는 스크린샷](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. 요청자는 제공된 정보에 따라 요청을 승인하거나 거부할 수 있습니다. 지침은 요청 승인 또는 거부의 단계를 참조하세요.

## <a name="approve-or-deny-request"></a>요청 승인 또는 거부

승인 보류 중인 액세스 요청을 연 후 승인 또는 거부 결정을 내리는 데 도움이 되는 세부 정보를 볼 수 있습니다.

**필수 역할:** 승인자

1. **보기** 링크를 클릭하여 액세스 요청 창을 엽니다.

1. 액세스 요청에 대한 세부 정보를 보려면 **세부 정보** 를 클릭합니다.

    세부 정보에는 사용자 이름, 조직, 액세스 시작 날짜 및 종료 날짜, 제공된 경우 요청 제출 시, 요청이 만료되는 시간 등이 포함됩니다.

1. **승인** 또는 **거부** 를 클릭합니다.

1. 필요한 경우 이유를 입력합니다.

    ![요청을 수락하거나 거부하는 페이지가 스크린샷 화면에 표시됩니다.](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. **제출** 을 클릭하여 결정을 제출합니다.

    한 단계에서 여러 승인자를 사용하여 정책을 구성한 경우 하나의 승인자만 보류 중인 승인에 대한 결정을 내려야 합니다. 승인자가 액세스 요청에 대한 결정을 제출한 후 요청이 완료되고 다른 승인자가 요청을 승인하거나 거부하는 데 더 이상 사용할 수 없습니다. 다른 승인자는 내 액세스 포털에서 요청 결정 및 의사 결정자를 볼 수 있습니다.

    단계에서 구성된 승인자가 액세스 요청을 승인하거나 거부할 수 없는 경우 구성된 요청 기간이 지나면 요청이 만료됩니다. 액세스 요청이 만료되었으며 액세스 요청을 다시 제출해야 한다는 알림이 사용자에게 제공됩니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 액세스 요청](entitlement-management-request-access.md)
- [요청 프로세스 및 메일 알림](entitlement-management-process.md)
