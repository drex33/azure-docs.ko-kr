---
title: 구독을 찾을 수 없음 오류 - Azure Portal 로그인
description: Azure Portal에 로그인하는 동안 구독을 찾을 수 없음 오류가 발생하는 문제에 대한 솔루션을 제공합니다.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/28/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: a90285db3b95402581ba917cef0833e93da91f44
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425788"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal"></a>Azure Portal의 구독을 찾을 수 없음 로그인 오류

[Azure Portal](https://portal.azure.com/)에 로그인할 때 “구독을 찾을 수 없음” 오류 메시지가 표시될 수 있습니다. 본 문서는 이 문제에 대한 해결 방법을 제공합니다.

## <a name="symptom"></a>증상

[Azure Portal](https://portal.azure.com/)에 로그인할 때 “구독을 찾을 수 없음” 오류 메시지가 표시됩니다.

## <a name="cause"></a>원인

잘못된 디렉터리에서 선택했거나 사용자 계정에 충분한 권한이 없는 경우 이 문제가 발생합니다.

## <a name="solution"></a>해결 방법

### <a name="scenario-error-message-is-received-in-the-azure-portal"></a>시나리오: [Azure Portal](https://portal.azure.com)에서 오류 메시지가 표시됨

이 문제를 해결하려면

* 오른쪽 위에 있는 계정을 클릭하여 올바른 Azure 디렉터리를 선택했는지 확인합니다.

  ![Azure Portal 오른쪽 위에 있는 디렉터리를 선택합니다.](./media/no-subscriptions-found/directory-switch.png)
* 올바른 Azure 디렉터리를 선택했으나 여전히 오류 메시지가 표시되면 [계정에 소유자 역할을 할당](../../role-based-access-control/role-assignments-portal.md)합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).
