---
title: Azure Automation 계정 문제 해결
description: 이 문서에서는 Azure 계정과 관련된 문제를 해결하는 방법을 설명합니다.
services: automation
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 33c4f3bab041356a1ad91e7ab9f12891f0d9c99d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488567"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Azure Automation 계정 문제 해결

이 문서에서는 Azure Automation 계정을 사용할 때 발생할 수 있는 문제에 대한 해결 방법을 설명합니다. Automation 계정에 대한 일반적인 정보는 [Azure Automation 계정 인증 개요](../automation-security-overview.md)를 참조하세요.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>시나리오: Automation 리소스 공급자를 구독에 등록할 수 없음

### <a name="issue"></a>문제

Automation 계정에서 업데이트 관리와 같은 관리 기능을 사용하면 다음과 같은 오류가 발생합니다.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>원인

Automation 리소스 공급자가 구독에 등록되어 있지 않습니다.

### <a name="resolution"></a>해결 방법

Automation 리소스 공급자를 등록하려면 Azure Portal에서 다음 단계를 수행합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

2. **구독** 으로 이동하여 구독을 선택합니다.   

3. **설정** 아래에서 **리소스 공급자** 를 선택합니다.

4. 리소스 공급자 목록에서 **Microsoft.Automation** 리소스 공급자가 등록되어 있는지 확인합니다.

5. 공급자가 나열되지 않으면 [리소스 공급자 등록 오류 해결](../../azure-resource-manager/templates/error-register-resource-provider.md)에서 설명한 대로 해당 공급자를 등록합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 문제가 해결되지 않으면 다음 채널 중 하나를 시도하여 추가 지원을 받습니다.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가의 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)에 연결합니다. 이는 Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하기 위한 공식 Microsoft Azure 계정입니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.
