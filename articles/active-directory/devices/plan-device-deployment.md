---
title: Azure Active Directory 디바이스 배포 계획
description: 조직의 요구를 충족하는 Azure AD 디바이스 통합 전략을 선택합니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f2ae8a38305b74f2bccba55b02d98626b5afc1d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528224"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Azure Active Directory 디바이스 배포 계획

이 문서를 통해 디바이스를 Azure AD와 통합하는 방법을 평가하고, 구현 계획을 선택하고, 지원되는 디바이스 관리 도구에 대한 주요 링크를 제공할 수 있습니다.

사용자가 로그인하는 디바이스의 범위가 확장되고 있습니다. 조직은 데스크톱, 노트북, 휴대폰, 태블릿 및 기타 디바이스를 제공할 수 있습니다. 사용자는 고유한 디바이스 배열을 가져오고 다양한 위치에서 정보에 액세스할 수 있습니다. 이 환경에서 관리자로써의 직무는 모든 디바이스에서 조직 리소스를 안전하게 지키는 것입니다.

Azure AD(Azure Active Directory)를 사용하면 조직에서 디바이스 ID 관리를 통해 이러한 목표를 충족할 수 있습니다. 이제 Azure AD에서 디바이스를 가져오고 [Azure Portal](https://portal.azure.com/)의 중앙 위치에서 제어할 수 있습니다. 이를 통해 통합된 환경, 강화된 보안을 경험할 수 있으며, 새 디바이스를 구성하는 데 필요한 시간을 줄일 수 있습니다.

다음과 같은 여러 가지 방법으로 디바이스를 Azure AD에 통합할 수 있습니다.

* Azure AD에 [디바이스를 등록](concept-azure-ad-register.md)

* Azure AD에 [디바이스를 조인](concept-azure-ad-join.md)(클라우드 전용) 또는

* 온-프레미스 Active Directory 및 Azure AD의 디바이스 간에 [하이브리드 Azure AD 조인 만들기](concept-azure-ad-join-hybrid.md) 

## <a name="learn"></a>배우기

시작하기 전에 [디바이스 ID 관리 개요](overview.md)에 대해 잘 알고 있는지 확인하세요.

### <a name="benefits"></a>이점

디바이스에 Azure AD ID를 부여하면 다음과 같은 주요 이점이 제공됩니다.

* 생산성 향상 – Azure AD를 사용하면 사용자가 온-프레미스 및 클라우드 리소스에 대한 [원활한 SSO(로그온)](./azuread-join-sso.md)를 수행할 수 있으므로 어디에서든 생산성을 높일 수 있습니다.

* 보안 강화 – Azure AD 디바이스를 사용하여 디바이스 또는 사용자의 ID에 따라 리소스에 [조건부 액세스 정책](../conditional-access/require-managed-devices.md)을 적용할 수 있습니다. 조건부 액세스 정책은 [Azure AD ID 보호](../identity-protection/overview-identity-protection.md)를 사용하여 추가 보호를 제공할 수 있습니다. Azure AD에 디바이스를 조인하는 것은 [암호 없는 인증](../authentication/concept-authentication-passwordless.md) 전략으로 보안을 강화하기 위한 필수 구성 요소입니다.

* 사용자 환경 개선 – Azure AD의 디바이스 ID를 통해 사용자에게 개인 및 회사 디바이스 모두에서 조직의 클라우드 기반 리소스에 대한 손쉬운 액세스를 제공할 수 있습니다. 관리자는 모든 Windows 디바이스에서 통합된 환경에 대한 [Enterprise State Roaming](enterprise-state-roaming-overview.md)을 사용하도록 설정할 수 있습니다.

* 배포 및 관리 간소화 – 디바이스 ID 관리를 사용하면 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot), [대량 프로비전](/mem/intune/enrollment/windows-bulk-enroll) 및 [셀프 서비스: OOBE(첫 실행 경험)](../user-help/user-help-join-device-on-network.md)를 통해 Azure AD로 디바이스를 가져오는 프로세스가 간소화됩니다. [Microsoft Intune](/mem/intune/fundamentals/what-is-intune)과 같은 MDM(모바일 디바이스 관리) 도구 및 [Azure Portal](https://portal.azure.com/)에서 해당 ID를 사용하여 이러한 디바이스를 관리할 수 있습니다.

### <a name="training-resources"></a>학습 리소스

동영상: [디바이스 컨트롤을 사용한 조건부 액세스](https://youtu.be/NcONUf-jeS4)

FAQ: [Azure AD 디바이스 관리 FAQ](faq.yml) 및 [설정 및 데이터 로밍 FAQ](enterprise-state-roaming-faqs.yml) 

## <a name="plan-the-deployment-project"></a>배포 프로젝트 계획

사용자 환경에서 이 배포에 대한 전략을 결정하는 동안 조직의 요구 사항을 고려합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여](../fundamentals/active-directory-deployment-plans.md)시키고 프로젝트의 해당 관련자 역할이 명확하게 이해되도록 해야 합니다. 

이 계획의 경우 목록에 다음 관련자를 추가합니다.

| 역할| Description |
| - | - |
| 디바이스 관리자| 계획이 조직의 디바이스 요구 사항을 충족하는지 확인할 수 있는 디바이스 팀의 담당자입니다. |
| 네트워크 관리자| 네트워크 요구 사항을 충족하는지 확인할 수 있는 네트워크 팀의 담당자입니다. |
| 디바이스 관리 팀| 디바이스 인벤토리를 관리하는 팀입니다. |
| OS 특정 관리자 팀| 특정 OS 버전을 지원하고 관리하는 팀입니다. 예를 들어 Mac 또는 iOS 중심 팀이 있을 수 있습니다. |

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사용자와 사전에 소통합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

통합 방법의 초기 구성은 테스트 환경에 있거나 테스트 디바이스의 소그룹을 사용하는 것이 좋습니다. [파일럿에 대한 모범 사례](../fundamentals/active-directory-deployment-plans.md)를 참조하세요.

하이브리드 Azure AD 조인 배포는 간단하며, 최종 사용자 작업이 필요 없는 100% 관리자 작업입니다. 전체 조직을 대상으로 한 번에 사용하도록 설정하기 전에 [하이브리드 Azure AD 조인에 대해 제어된 유효성 검사](hybrid-azuread-join-control.md)를 수행할 수도 있습니다.

## <a name="choose-your-integration-methods"></a>통합 방법 선택

조직에서는 단일 Azure AD 테넌트에서 여러 디바이스 통합 방법을 사용할 수 있습니다. 목표는 Azure AD에서 디바이스를 안전하게 관리하는 데 적합한 방법을 선택하는 것입니다. 소유권, 디바이스 유형, 기본 대상 그룹 및 조직의 인프라를 비롯하여 이 결정을 주도하는 많은 매개 변수가 있습니다.

다음은 사용할 통합 방법을 결정하는 데 유용한 정보입니다.

### <a name="decision-tree-for-devices-integration"></a>디바이스 통합에 대한 의사 결정 트리

이 트리를 사용하여 조직이 소유한 디바이스에 대한 옵션을 결정할 수 있습니다. 

> [!NOTE]
> 개인 또는 BYOD(사용자 고유의 디바이스 가져오기) 시나리오는 이 다이어그램에 표시되지 않습니다. Azure AD 등록이 항상 발생합니다.

 ![의사 결정 트리](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>비교 행렬

iOS 및 Android 디바이스는 Azure AD만 등록할 수 있습니다. 다음 표에서는 Windows 클라이언트 디바이스에 대한 높은 수준의 고려 사항을 보여 줍니다. 개요로 사용한 다음, 다양한 통합 방법을 자세히 살펴봅니다.

| 고려 사항 | Azure AD 등록됨| Azure AD 조인| 하이브리드 Azure AD 조인 |
| - | - | - | - |
| **클라이언트 운영 체제**| | |  |
| Windows 10 디바이스| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
| Windows 하위 수준 디바이스(Windows 8.1 또는 Windows 7)| | | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
|**로그인 옵션**| | |  |
| 최종 사용자 로컬 자격 증명| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| |  |
| 암호| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
| 디바이스 PIN| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| |  |
| 비즈니스용 Windows Hello| | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
| FIDO 2.0 보안 키| | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator 앱(암호 없음)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
|**주요 기능**| | |  |
| 클라우드 리소스에 대한 SSO| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
| 온-프레미스 리소스에 대한 SSO| | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
| 조건부 액세스 <br> (디바이스를 준수 상태로 표시해야 함) <br> (MDM에서 관리해야 함)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)|![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
조건부 액세스 <br>(하이브리드 Azure AD 조인된 디바이스 필요)| | | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)
| Windows 로그인 화면에서 셀프 서비스 암호 재설정| | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
| Windows hello PIN 다시 설정| | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |
| 여러 디바이스에서 Enterprise State Roaming| | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD 등록 

등록된 디바이스는 종종 [Microsoft Intune](/mem/intune/enrollment/device-enrollment)으로 관리됩니다. 디바이스는 운영 체제에 따라 다양한 방법으로 Intune에 등록됩니다. 

Azure AD 등록 디바이스는 클라우드 리소스에 대한 SSO로 BYOD(사용자 고유 디바이스 가져오기) 및 회사 소유 디바이스를 지원하는 것을 제공합니다. 리소스에 대한 액세스는 디바이스 및 사용자에게 적용되는 Azure AD [조건부 액세스 정책](../conditional-access/require-managed-devices.md)을 기반으로 합니다.

### <a name="registering-devices"></a>디바이스 등록

등록된 디바이스는 종종 [Microsoft Intune](/mem/intune/enrollment/device-enrollment)으로 관리됩니다. 디바이스는 운영 체제에 따라 다양한 방법으로 Intune에 등록됩니다. 

BYOD 및 회사 소유 모바일 디바이스는 회사 포털 앱을 설치하는 사용자가 등록합니다.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

* [macOS](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)

디바이스를 등록하는 것이 조직에게 가장 적합한 경우 다음 리소스를 참조하세요.

* [Azure AD 등록 디바이스](concept-azure-ad-register.md)에 대한 개요입니다.

* 이 최종 사용자 설명서는 [조직의 네트워크에 개인 디바이스를 등록](../user-help/user-help-register-device-on-network.md)하는 방법에 대해 설명합니다.

## <a name="azure-ad-join"></a>Azure AD 조인

Azure AD 조인을 사용하면 Windows를 사용하여 클라우드 우선 모델로 전환할 수 있습니다. 디바이스 관리를 현대화하고 디바이스 관련 IT 비용을 줄이는 계획을 세우는 경우에 매우 편리합니다. Azure AD 조인은 Windows 10 디바이스에서 작동합니다. 새 디바이스에 대한 첫 번째 선택 사항으로 간주합니다.

그러나 조직의 네트워크에 있는 경우 [Azure AD 조인 디바이스는 온-프레미스 리소스에 SSO를 사용할 수 있으며](azuread-join-sso.md), 파일, 인쇄 및 기타 애플리케이션과 같은 온-프레미스 서버에 인증할 수 있습니다.

이것이 조직에게 가장 적합한 경우 다음 리소스를 참조하세요.

* [Azure AD 조인 디바이스](concept-azure-ad-join.md)에 대한 개요입니다.

* [Azure AD 조인 구현 계획](azureadjoin-plan.md)을 숙지합니다.

### <a name="provisioning-azure-ad-join-to-your-devices"></a>디바이스에 Azure AD 조인 프로비전

Azure AD 조인을 프로비전하려면 다음 방법을 사용합니다.

* 셀프 서비스: [Windows 10 첫 실행 환경](azuread-joined-devices-frx.md)

Windows 10 Professional 또는 Windows 10 Enterprise가 디바이스에 설치된 경우 환경은 기본적으로 회사 소유의 디바이스에 대한 설정 프로세스로 지정됩니다.

* [Windows OOBE(첫 실행 경험) 또는 Windows 설정](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [대량 등록](/mem/intune/enrollment/windows-bulk-enroll)

신중하게 [이러한 접근 방식을 비교](azureadjoin-plan.md)한 후 배포 절차를 선택합니다.

Azure AD 조인이 디바이스에 가장 적합한 솔루션임을 확인했고, 해당 디바이스는 이미 다른 상태에 있을 수 있습니다. 업그레이드 고려 사항은 다음과 같습니다.

| 현재 디바이스 상태| 원하는 디바이스 상태| 방법 |
| - | - | - |
| 온-프레미스 도메인 조인| Azure AD 조인| Azure AD에 조인하기 전에 온-프레미스 도메인에서 디바이스 조인 해제 |
| 하이브리드 Azure AD 조인| Azure AD 조인| Azure AD에 조인하기 전에 온-프레미스 도메인 및 Azure AD에서 디바이스 조인 해제 |
| Azure AD 등록됨| Azure AD 조인| Azure AD에 조인하기 전에 디바이스 등록 취소 |


## <a name="hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인

온-프레미스 Active Directory 환경을 사용하고, Azure AD에 Active Directory 도메인 조인 컴퓨터를 조인하려는 경우 하이브리드 Azure AD 조인으로 이를 수행할 수 있습니다. Windows 현재 및 Windows 하위 수준 디바이스를 비롯한 [광범위한 windows 디바이스](hybrid-azuread-join-plan.md)를 지원합니다.

대부분의 조직에는 도메인 조인 디바이스가 이미 있고 그룹 정책 또는 SCCM(System Center Configuration Manager)을 통해 관리됩니다. 이 경우 기존 투자를 활용하면서 이점을 얻기 위해 하이브리드 Azure AD 조인을 구성하는 것이 좋습니다.

하이브리드 Azure AD 조인이 조직에게 가장 적합한 경우 다음 리소스를 참조하세요.

* [하이브리드 Azure AD 조인 디바이스](concept-azure-ad-join-hybrid.md)에 대한 개요입니다.

* [하이브리드 Azure AD 조인 구현](hybrid-azuread-join-plan.md) 계획을 숙지합니다.

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>디바이스에 하이브리드 Azure AD 조인 프로비전

[ID 인프라를 검토합니다](hybrid-azuread-join-plan.md). Azure AD Connect는 다음을 위해 하이브리드 Azure AD 조인을 구성하는 마법사를 제공합니다.

* [페더레이션된 도메인](hybrid-azuread-join-federated-domains.md)

* [관리되는 도메인](hybrid-azuread-join-managed-domains.md)

필수 버전의 Azure AD Connect를 설치할 수 없는 경우 [수동으로 하이브리드 Azure AD 조인을 구성하는 방법](hybrid-azuread-join-manual.md)을 참조하세요. 

> [!NOTE] 
> 온-프레미스 도메인 조인 Windows 10 디바이스는 기본적으로 하이브리드 Azure AD 조인이 되기 위해 Azure AD에 자동 조인을 시도합니다. 이는 올바른 환경을 설정한 경우에만 성공합니다. 

하이브리드 Azure AD 조인이 디바이스에 가장 적합한 솔루션임을 확인했고, 해당 디바이스는 이미 다른 상태에 있을 수 있습니다. 업그레이드 고려 사항은 다음과 같습니다.

| 현재 디바이스 상태| 원하는 디바이스 상태| 방법 |
| - | - | - |
| 온-프레미스 도메인 조인| 하이브리드 Azure AD 조인| Azure AD Connect 또는 AD FS를 사용하여 Azure에 조인 |
| 온-프레미스 작업 그룹 조인 또는 신규| 하이브리드 Azure AD 조인| [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)으로 지원됩니다. 그렇지 않으면 하이브리드 Azure AD 조인 전에 디바이스가 온-프레미스 도메인에 조인되어 있어야 합니다. |
| Azure AD 가입| 하이브리드 Azure AD 조인| 온-프레미스 작업 그룹 또는 새 상태에 배치하는 Azure AD의 조인을 해제합니다. |
| Azure AD가 등록됨| 하이브리드 Azure AD 조인| Windows 버전에 따라 다릅니다. [다음 고려 사항을 확인하세요.](hybrid-azuread-join-plan.md) |

## <a name="manage-your-devices"></a>디바이스 관리

디바이스를 Azure AD에 등록하거나 조인한 후에는 [Azure Portal](https://portal.azure.com/)을 중앙 위치로 사용하여 디바이스 ID를 관리합니다. Azure Active Directory 디바이스 페이지에서 다음을 수행할 수 있습니다.

* [디바이스 설정 구성](device-management-azure-portal.md#configure-device-settings)
* Windows 디바이스를 관리하려면 로컬 관리자여야 합니다. [Azure AD는 Azure AD 조인 디바이스에 대해 이 멤버 자격을 업데이트](assign-local-admin.md)하고, 디바이스 관리자 역할을 사용하여 조인된 모든 디바이스에 관리자로 자동으로 추가합니다.

[부실 디바이스를 관리](manage-stale-devices.md)하여 환경을 정리하고 현재 디바이스를 관리하는 리소스에 집중하도록 합니다.

* [디바이스 관련 감사 로그 검토](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>지원되는 디바이스 관리 도구

관리자는 추가 디바이스 관리 도구를 사용하여 등록되고 조인된 디바이스를 보호하고 추가적으로 제어할 수 있습니다. 이러한 도구는 스토리지 암호화, 암호 복잡성, 소프트웨어 설치 및 소프트웨어 업데이트를 요구하는 것처럼 조직에 필요한 구성을 적용할 수 있는 수단을 제공합니다. 

통합 디바이스에 대해 지원 및 비지원되는 플랫폼을 검토합니다.

| 디바이스 관리 도구| Azure AD 등록됨| Azure AD 조인| 하이브리드 Azure AD 조인|
| - | - | - | - |
| [MDM(모바일 디바이스 관리)](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>예: Microsoft Intune| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| 
| [Microsoft Intune 및 Microsoft Endpoint Configuration Manager를 사용한 공동 관리](/mem/configmgr/comanage/overview) <br>(Windows 10 이상)| | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| 
| [그룹 정책](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Windows만 해당)| | | ![이러한 값에 대한 확인 표시](./media/plan-device-deployment/check.png)| 



 등록된 iOS 또는 Android 디바이스에 대한 디바이스 관리를 사용하거나 사용하지 않는 [Microsoft Intune MAM(모바일 애플리케이션 관리)](/mem/intune/apps/app-management)을 고려하는 것이 좋습니다.

 또한 관리자는 조직에서 Windows 운영 체제를 호스팅하는 [VDI(가상 데스크톱 인프라) 플랫폼을 배포](howto-device-identity-virtual-desktop-infrastructure.md)하여 관리를 간소화하고 리소스의 통합 및 중앙 집중화를 통해 비용을 절감할 수 있습니다. 

### <a name="troubleshoot-device-identities"></a>디바이스 ID 문제 해결

* [dsregcmd 명령을 사용하여 디바이스 문제 해결](troubleshoot-device-dsregcmd.md)

* [Azure Active Directory에서 엔터프라이즈 상태 로밍 설정 문제 해결](enterprise-state-roaming-troubleshooting.md)

도메인 가입 Windows 디바이스에 대한 하이브리드 Azure AD 조인을 완료할 때 문제가 발생하면 다음을 참조하세요.

* [Windows 최신 디바이스의 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-current.md)

* [Windows 하위 수준 디바이스에 대한 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>다음 단계

* [Azure AD 조인 구현 계획](azureadjoin-plan.md)
* [하이브리드 Azure AD 조인 구현 계획](hybrid-azuread-join-plan.md)
* [디바이스 ID 관리](device-management-azure-portal.md)
