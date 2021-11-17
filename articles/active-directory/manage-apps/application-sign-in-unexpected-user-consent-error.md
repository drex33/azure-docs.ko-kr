---
title: 애플리케이션에 대한 동의를 수행할 때 예기치 않은 오류
titleSuffix: Azure AD
description: 애플리케이션에 대한 동의 프로세스 도중 발생할 수 있는 오류 및 사용자가 할 수 있는 조치를 설명합니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bb824f9527619033467594a98310663c1113fdc
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620738"
---
# <a name="unexpected-error-when-performing-consent-to-an-application-in-azure-active-directory"></a>Azure Active Directory에서 애플리케이션에 대한 동의를 수행할 때 예기치 않은 오류가 발생했습니다.

이 문서에서는 애플리케이션에 대한 동의 프로세스 도중 발생할 수 있는 오류에 대해 설명합니다. 오류 메시지가 포함되지 않는 예기치 않은 동의 프롬프트의 문제를 해결하려는 경우 [Azure AD 인증 시나리오](../develop/authentication-vs-authorization.md)를 참조하세요.

Azure Active Directory와 통합되는 많은 애플리케이션을 작동시키기 위해 다른 리소스에 대한 사용 권한이 필요합니다. 또한 이러한 리소스가 Azure Active Directory와 통합되면 종종 일반적인 동의 프레임워크를 사용하여 액세스하기 위한 사용 권한을 요청합니다. 일반적으로 애플리케이션을 처음 사용할 때 발생하지만 이후에 애플리케이션을 사용할 때도 발생할 수 있는 동의 확인 프롬프트가 표시됩니다.

애플리케이션에 필요한 사용 권한에 대해 동의한 사용자에게는 특정 조건이 true여야 합니다. 이러한 조건이 충족되지 않으면 발생할 수 있는 오류는 다음과 같습니다.

## <a name="requesting-not-authorized-permissions-error"></a>권한 없는 사용 권한 요청 오류

* **AADSTS90093:** &lt;clientAppDisplayName&gt;은 사용 권한이 부여되지 않은 하나 이상의 사용 권한을 요청합니다. 사용자 대신 이 애플리케이션에 동의할 수 있는 관리자에게 문의합니다.
* **AADSTS90094:** &lt;clientAppDisplayName&gt;에는 관리자만 권한을 부여할 수 있는 조직의 리소스에 대한 액세스 권한이 필요합니다. 이 앱을 사용하려면 먼저 관리자에게 앱의 사용 권한을 부여하도록 요청하세요.

이 오류는 전역 관리자가 아닌 사용자가 관리자만이 부여할 수 있는 사용 권한을 요청하는 애플리케이션을 사용하려고 할 때 발생합니다. 해당 조직을 대신하여 애플리케이션에 대한 액세스 권한을 부여하는 관리자가 이 오류를 해결할 수 있습니다.

이 오류는 Microsoft가 권한 요청을 위험한 것으로 감지하기 때문에 사용자가 애플리케이션에 동의하지 못하도록 하는 경우에도 발생할 수 있습니다. 이 경우 감사 이벤트는 범주 "ApplicationManagement", 활동 유형 "애플리케이션에 동의" 및 상태 이유 "위험한 애플리케이션 감지됨"으로도 로깅됩니다.

이 오류가 발생할 수 있는 또 다른 시나리오는 사용자 할당이 애플리케이션에 필요하지만 관리자 동의가 제공되지 않은 경우입니다. 이 경우 관리자는 먼저 관리자 동의를 제공해야 합니다.

## <a name="policy-prevents-granting-permissions-error"></a>사용 권한 부여를 방지하는 정책 오류

* **AADSTS90093:**&lt;tenantDisplayName&gt;의 관리자는 사용 권한이 요청하는 &lt;앱의 이름&gt;을 부여하지 못하도록 방지하는 정책을 설정했습니다. 사용자 대신 이 앱에 대한 권한을 부여할 수 있는 &lt;tenantDisplayName&gt;의 관리자에게 문의하세요.

전역 관리자가 애플리케이션에 대해 동의하는 사용자의 기능을 해제한 다음, 관리자가 아닌 사용자가 동의를 필요로 하는 애플리케이션을 사용하려는 경우에 이 오류가 발생합니다. 해당 조직을 대신하여 애플리케이션에 대한 액세스 권한을 부여하는 관리자가 이 오류를 해결할 수 있습니다.

## <a name="intermittent-problem-error"></a>일시적인 문제 오류

* **AADSTS90090:** 로그인 프로세스에서 &lt;clientAppDisplayName&gt;에 부여하려고 한 권한을 기록하는 일시적인 문제가 발생했습니다. 나중에 다시 시도하세요.

이 오류는 일시적인 서비스 쪽 문제가 발생했음을 나타냅니다. 애플리케이션에 대한 동의하도록 다시 시도하여 문제를 해결할 수 있습니다.

## <a name="resource-not-available-error"></a>리소스 사용할 수 없음 오류

* **AADSTS65005:**&lt;clientAppDisplayName&gt; 앱은 사용할 수 없는 &lt;resourceAppDisplayName&gt; 리소스에 액세스하는 사용 권한을 요청했습니다.

애플리케이션 개발자에게 문의하세요.

## <a name="resource-not-available-in-tenant-error"></a>테넌트에서 리소스 사용할 수 없음 오류

* **AADSTS65005:** &lt;clientAppDisplayName&gt;은 &lt;tenantDisplayName&gt; 조직에서 사용할 수 없는 &lt;resourceAppDisplayName&gt; 리소스에 대한 액세스 권한을 요청합니다.

이 리소스를 사용할 수 있도록 하거나 &lt;tenantDisplayName&gt;의 관리자에게 문의합니다.

## <a name="permissions-mismatch-error"></a>권한 불일치 오류

* **AADSTS65005:** 앱이 &lt;resourceAppDisplayName&gt; 리소스에 액세스하기 위한 동의를 요청했습니다. 앱 등록 중에 앱이 미리 구성된 방법과 일치하지 않기 때문에 이 요청에 실패했습니다. 앱 공급업체에게 문의하세요.**

사용자가 동의하려는 애플리케이션이 조직의 디렉터리(테넌트)에서 찾을 수 없는 리소스 애플리케이션에 액세스하는 사용 권한을 요청하는 경우에 이러한 오류가 발생합니다. 이 상황이 발생할 수 있는 몇 가지 이유는 다음과 같습니다.

* 클라이언트 애플리케이션 개발자가 해당 애플리케이션을 잘못 구성하여 잘못된 리소스에 대한 액세스 권한을 요청했습니다. 이 경우에 애플리케이션 개발자는 이 문제를 해결하기 위해 클라이언트 애플리케이션의 구성을 업데이트해야 합니다.

* 대상 리소스 애플리케이션을 나타내는 서비스 주체는 조직에서 존재하지 않거나 이전에 있었지만 제거되었습니다. 이 문제를 해결하려면 클라이언트 애플리케이션이 이에 대한 권한을 요청할 수 있도록 조직에서 리소스 애플리케이션의 서비스 주체를 제공해야 합니다. 서비스 주체는 다음을 포함하여 애플리케이션의 유형에 따라 여러 가지 방법으로 프로비전할 수 있습니다.

* 리소스 애플리케이션의 구독 구입(게시된 Microsoft 애플리케이션)

* 리소스 애플리케이션에 대한 동의

* Azure Portal을 통해 애플리케이션 권한 부여

* Azure AD 애플리케이션 갤러리의 애플리케이션 추가

## <a name="risky-app-error-and-warning"></a>위험한 앱 오류 및 경고

* **AADSTS900941:** 관리자 동의가 필요합니다. 앱이 위험한 것으로 간주됩니다. (AdminConsentRequiredDueToRiskyApp)
* 이 앱은 위험할 수 있습니다. 이 앱을 신뢰하는 경우 관리자에게 액세스 권한을 부여하도록 요청하세요.
* **AADSTS900981:** 위험한 앱에 대한 관리자 동의 요청이 수신되었습니다. (AdminConsentRequestRiskyAppWarning)
* 이 앱은 위험할 수 있습니다. 이 앱을 신뢰하는 경우에만 계속합니다.

Microsoft에서 동의 요청이 위험할 수 있다고 판단하면 이러한 두 메시지가 모두 표시됩니다. 여러 가지 다른 요인 중에서 [확인된 게시자](../develop/publisher-verification-overview.md)가 앱 등록에 추가되지 않은 경우 이 문제가 발생할 수 있습니다. [관리자 동의 워크플로](configure-admin-consent-workflow.md)를 사용하지 않도록 설정하면 첫 번째 오류 코드 및 메시지가 최종 사용자에게 표시됩니다. 두 번째 코드 및 메시지는 관리자 동의 워크플로를 사용하도록 설정할 때 최종 사용자에게 표시되고 관리자에게도 표시됩니다.

최종 사용자는 위험한 것으로 감지된 앱에 대한 동의를 부여할 수 없습니다. 관리자는 앱을 평가하고 계속 진행할 수 있지만 매우 주의해야 합니다. 추가 검토 시 앱이 의심스러운 것으로 표시되면 동의 화면에서 Microsoft에 보고될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory에서 앱, 사용 권한 및 동의(v1 엔드포인트)](../develop/quickstart-register-app.md)<br>

[Azure Active Directory의 범위, 사용 권한 및 동의(v2.0 엔드포인트)](../develop/v2-permissions-and-consent.md)
