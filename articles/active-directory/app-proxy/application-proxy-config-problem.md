---
title: Azure Active Directory 애플리케이션 프록시 애플리케이션을 만들 때 문제 발생
description: Azure Active Directory 관리 포털에서 애플리케이션 프록시 애플리케이션을 만들 때 발생하는 문제를 해결하는 방법
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: a3f60e63d4fb5257d7d822a8e1a9326adf9eb62d
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108187314"
---
# <a name="problem-creating-an-application-proxy-application"></a>애플리케이션 프록시 애플리케이션을 만들 때 문제 발생 

다음은 새로운 애플리케이션 프록시 애플리케이션을 만들 때 발생하는 몇 가지 일반적인 문제입니다.

## <a name="recommended-documents"></a>권장되는 문서 

관리 포털을 통해 애플리케이션 프록시 애플리케이션을 만드는 방법에 대한 자세한 내용은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)를 참조하세요.

해당 문서의 단계에 따라 애플리케이션을 만드는 중 오류가 발생하면 오류 세부 정보에서 애플리케이션 문제 해결 방법에 대한 정보 및 제안 사항을 참조하세요. 대부분의 오류 메시지에는 제안 수정이 포함되어 있습니다. 

## <a name="specific-things-to-check"></a>확인할 항목

일반적인 오류를 방지하려면 다음을 확인합니다.

-   애플리케이션 프록시 애플리케이션을 만들 수 있는 권한을 가진 관리자여야 합니다.

-   내부 URL이 고유해야 합니다.

-   외부 URL이 고유해야 합니다.

-   URL이 http 또는 https로 시작하고 "/"로 끝나야 합니다.

-   URL은 IP 주소가 아닌 도메인 이름이어야 합니다.

애플리케이션을 만들 때 오류 메시지가 오른쪽 상단 모서리에 표시됩니다. 알림 아이콘을 선택하여 오류 메시지를 볼 수도 있습니다.

   ![알림 프롬프트](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>다음 단계
[Azure Portal에서 애플리케이션 프록시 사용](application-proxy-add-on-premises-application.md)
