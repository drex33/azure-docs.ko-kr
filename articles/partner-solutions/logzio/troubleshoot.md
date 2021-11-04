---
title: Logz.io 문제 해결 - Azure 파트너 솔루션
description: Logz.io와의 Azure 통합 문제를 해결하는 방법에 대해 알아봅니다.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.openlocfilehash: 4322cdfead345aec836760199ce7d6f1f9654c59
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057517"
---
# <a name="troubleshoot-logzio-integration-with-azure"></a>Azure와 Logz.io 통합 문제 해결

이 문서에서는 Logz.io와의 Azure 통합 문제를 해결하는 방법에 대해 알아봅니다.

## <a name="owner-role-needed-to-create-resource"></a>리소스를 만드는 데 필요한 소유자 역할

Logz.io를 설정하려면 Azure 구독에서 [소유자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)을 할당받아야 합니다. 이 통합을 시작하기 전에 [액세스를 확인](../../role-based-access-control/check-access.md)하세요.

## <a name="single-sign-on-errors"></a>Single Sign-On 오류

SSO를 설정하는 동안 오류가 발생할 수 있습니다. SSO(Single Sign-On)를 설정하는 방법에 대한 자세한 내용은 [Logz.io Single Sign-On 설정](setup-sso.md)을 참조하세요.

### <a name="unable-to-save-single-sign-on-settings"></a>Single Sign-On 설정을 저장할 수 없음

이 오류는 Logz.io SAML(Security Assertion Markup Language) 식별자를 사용하는 다른 엔터프라이즈 애플리케이션이 있음을 의미합니다. 애플리케이션을 찾으려면 **기본 SAML 구성** 섹션에서 **편집** 을 선택합니다.

이 문제를 해결하려면 SAML을 사용하는 엔터프라이즈 애플리케이션을 사용하지 않도록 설정하거나 다른 엔터프라이즈 애플리케이션을 사용하여 Logz.io로 SAML SSO를 설정합니다. 애플리케이션에 필요한 설정이 있는지 확인합니다.

### <a name="application-not-shown-in-single-sign-on"></a>애플리케이션이 Single Sign-On에 표시되지 않음

_애플리케이션 ID_ 로 검색해 봅니다. 결과가 표시되지 않는 경우 애플리케이션의 SAML 설정을 확인합니다. 그리드는 올바른 SAML 설정이 있는 애플리케이션만 표시합니다. **식별자** 및 **회신 URL** 은 정확히 다음과 같이 표시되어야 합니다.

빈 텍스트 상자를 사용하여 **식별자** 및 **회신 URL** 에 대한 새 값을 추가할 수 있습니다.

다음 패턴을 사용하여 새 값을 추가합니다.

- **식별자**: `urn:auth0:logzio:<Application ID>`
- **회신 URL**: `https://logzio.auth0.com/login/callback?connection=<Application ID>`

:::image type="content" source="./media/troubleshoot/basic-saml-config.png" alt-text="기본 SAML 구성 설정.":::

## <a name="logs-not-being-sent-to-logzio"></a>로그가 Logz.io로 전송되지 않음

[Azure Monitor 리소스 로그 범주](../../azure-monitor/essentials/resource-logs-categories.md)에 나열된 리소스만 Logz.io에 로그를 전송합니다.

리소스가 로그를 Logz.io에 전송하는지 확인하려면 다음을 수행합니다.

1. 특정 리소스에 대한 [Azure 진단 설정](../../azure-monitor/essentials/diagnostic-settings.md)으로 이동합니다.
1. Logz.io 진단 설정이 있는지 확인합니다.

:::image type="content" source="./media/troubleshoot/diagnostics.png" alt-text="진단 설정.":::

## <a name="limit-reached-in-monitored-resources"></a>모니터링되는 리소스에서 제한에 도달함

Azure Monitor Diagnostics는 단일 리소스 또는 구독에서 최대 5개의 진단 설정을 지원합니다. 해당 제한에 도달하면 **모니터링되는 리소스** 에서 리소스가 **제한에 도달함** 을 표시합니다. Logz.io를 통해 모니터링을 추가할 수 없음

:::image type="content" source="./media/troubleshoot/limit-monitored-resources.png" alt-text="모니터링되는 리소스에 대한 제한에 도달했습니다.":::

## <a name="vm-extension-installation-failed"></a>VM 확장 설치 실패

VM(가상 머신)은 단일 Logz.io 계정(주 또는 하위)에서만 모니터링할 수 있습니다. 다른 계정에서 이미 모니터링하는 VM에 에이전트를 설치하려고 하면 다음과 같은 오류가 표시됩니다.

:::image type="content" source="./media/troubleshoot/vm-agent-fail.png" alt-text="가상 머신 에이전트를 설치하지 못했습니다.":::

## <a name="purchase-errors"></a>구매 오류

유효한 신용 카드가 Azure 구독에 연결되어 있지 않아 구매에 실패했습니다. 또는 결제 방법이 구독과 연결되어 있지 않습니다.

구매 오류를 해결하려면 다음을 수행합니다.

- 다른 Azure 구독을 사용합니다.
- 구독의 신용 카드나 결제 방법을 추가하거나 업데이트합니다. 자세한 내용은 [Azure에 대한 신용 카드 추가 또는 업데이트](../../cost-management-billing/manage/change-credit-card.md)를 참조하세요.

**작업 세부 정보** 를 선택하여 리소스의 배포 페이지에서 오류의 출력을 볼 수 있습니다.

```json
{
  "status": "Failed",
  "error": {
    "code": "BadRequest",
    "message": "{\"message\":\"Purchase has failed because we couldn't find a valid credit card nor
               a payment method associated with your Azure subscription. Please use a different
               Azure subscription or add\\\\update current credit card or payment method for this
               subscription and retry.\",\"code\":\"BadRequest\"}"
  }
}
```

## <a name="next-steps"></a>다음 단계

- Logz.io 통합을 [관리](manage.md)하는 방법을 알아봅니다.
- SSO에 대한 자세한 내용은 [Logz.io Single Sign-On 설정](setup-sso.md)을 참조하세요.
