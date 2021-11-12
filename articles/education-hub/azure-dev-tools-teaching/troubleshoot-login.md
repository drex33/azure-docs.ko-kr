---
title: Azure Dev Tools for Teaching 로그인 오류 해결
description: 학생이 Azure Dev Tools for Teaching에 로그인할 때 오류 메시지를 수신하는 경우 수행해야 하는 작업을 설명합니다.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 937a321a0edb51f0bab9168706977d9b798429e8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339690"
---
# <a name="troubleshooting-student-login-issues"></a>학생 로그인 문제 해결
Azure Dev Tools for Teaching에 액세스하려면 사용자에게 MSA(Microsoft 계정)가 있어야 합니다. 학생은 계정이 아직 MSA가 아니거나 MSA에 연결되지 않은 경우 MSA 만들기로 자동 이동됩니다. 도메인이 Active Directory와 연결된 경우 해당 도메인의 모든 계정은 이미 MSA로 간주됩니다.

학생이 로그인을 시도했으나 다음 오류 메시지를 수신하는 경우 아래에 설명된 해결 방법 중 하나를 사용합니다.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="로그인 오류 메시지" border="false":::

해결 방법은 두 가지로, 새 Microsoft 계정을 만들거나 기존 Microsoft 계정을 사용하는 것입니다.

## <a name="create-a-new-microsoft-account"></a>새 Microsoft 계정 만들기
### <a name="use-a-university-email-address"></a>대학 메일 주소 사용
대학 메일 주소(예: `John.Smith@university.edu`)를 사용하여 로그인하는 경우 해당 메일 주소로 Microsoft 계정을 만들어야 합니다. 계정 만들기는 무료이며 몇 분 밖에 시간이 걸리지 않습니다. Microsoft 계정이 있으면 하나의 사용자 이름과 암호를 사용하여 모든 Microsoft 제품에 자동으로 로그인할 수 있습니다.

### <a name="use-a-personal-email-address"></a>개인 메일 주소 사용
개인 메일 주소(예: `John.Smith@email.com`)를 사용하여 로그인하고 있으나 대학 메일 주소도 있는 경우 대학 메일 주소를 사용해 보세요. 이전에 개인 메일 주소를 사용하여 이 교육 기관 웹 스토어에 로그인했었거나 대학 메일 주소가 없는 경우 개인 메일 주소로 Microsoft 계정을 만들거나 연결해야 합니다.

## <a name="use-an-existing-microsoft-account"></a>기존 Microsoft 계정 사용
학생에게 기존 Microsoft 계정(예: Xbox)이 있는 경우 해당 계정을 Azure Dev Tools 계정에 연결할 수 있습니다.

1. [https://editor.swagger.io](https://account.microsoft.com) 로 이동합니다.
1. Microsoft 계정 자격 증명을 사용하여 로그인합니다.
1. 위쪽 리본 메뉴에서 **사용자 정보** 를 선택합니다.

1. **Microsoft에 로그인하는 방법 관리** 를 클릭합니다. 본인 확인을 요청하는 메시지가 표시됩니다. 보안 코드가 메일로 전송됩니다.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="로그인 관리" border="false":::

1. 메일로 전송된 보안 코드를 입력합니다.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="로그인 코드 입력" border="false":::

1. 계정에 **메일 추가** 를 클릭하고 대학 메일 주소를 입력합니다.
다음에 로그인할 때 대학 메일 주소를 사용하여 Azure Dev Tools for Teaching에 액세스할 수 있습니다.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="Microsoft에 로그인하는 방법 관리" border="false":::

## <a name="next-steps"></a>다음 단계
- [FAQ](program-faq.yml)

- [지원 옵션](program-support.md)
