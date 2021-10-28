---
title: Azure Active Directory에서의 애플리케이션 프로비저닝에 대해 알려진 문제
description: Azure Active Directory에서 자동화된 애플리케이션 프로비저닝을 사용할 때 알려진 문제에 대해 알아봅니다.
author: kenwith
ms.author: kenwith
manager: karenh444
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.reviewer: arvinh
ms.openlocfilehash: 6c790baf2dfc7c08306bdf96598e33e8bdc6b9c6
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991226"
---
# <a name="known-issues-for-application-provisioning-in-azure-active-directory"></a>Azure Active Directory에서의 애플리케이션 프로비저닝에 대해 알려진 문제
이 문서에서는 앱 프로비저닝을 사용할 때 알고 있어야 하는 알려진 문제에 대해 설명합니다. UserVoice에서 애플리케이션 프로비저닝 서비스에 대한 피드백을 제공하려면 [Azure AD(Azure Active Directory) 애플리케이션 프로비저닝 UserVoice](https://aka.ms/appprovisioningfeaturerequest)를 참조하세요. Microsoft에서는 서비스를 개선할 수 있도록 UserVoice를 면밀하게 모니터링하고 있습니다.

> [!NOTE]
> 이 문서가 알려진 문제에 대한 포괄적인 목록이 아닙니다. 나열되지 않은 문제를 알고 있는 경우 페이지의 맨 아래에 피드백을 제공하세요.

## <a name="authorization"></a>권한 부여 

#### <a name="unable-to-save"></a>저장할 수 없음

저장하려면 테넌트 URL, 비밀 토큰 및 알림 이메일을 입력해야 합니다. 이중 하나만 제공할 수는 없습니다. 

#### <a name="unable-to-change-provisioning-mode-back-to-manual"></a>프로비저닝 모드를 수동으로 다시 변경할 수 없음

프로비저닝을 처음 구성하면 프로비저닝 모드가 수동에서 자동으로 전환되었음을 알 수 있습니다. 다시 수동으로 변경할 수 없습니다. 그러나 UI를 통해 프로비저닝을 해제할 수 있습니다. UI에서 프로비저닝을 해제하는 것은 드롭다운을 수동으로 설정하는 것과 동일합니다.

## <a name="attribute-mappings"></a>특성 매핑 

#### <a name="attribute-samaccountname-or-usertype-not-available-as-a-source-attribute"></a>SamAccountName 또는 userType 특성을 원본 특성으로 사용할 수 없음

**SamAccountName** 및 **userType** 특성은 기본적으로 원본 특성으로 사용할 수 없습니다. 스키마를 확장하여 특성을 추가합니다. 스키마를 확장하여 사용 가능한 원본 특성의 목록에 특성을 추가할 수 있습니다. 자세한 내용은 [누락된 원본 특성](user-provisioning-sync-attributes-for-mapping.md)을 참조하세요. 

#### <a name="source-attribute-dropdown-missing-for-schema-extension"></a>스키마 확장에 대한 원본 특성 드롭다운이 없음

때로는 UI의 원본 특성 드롭다운에서 스키마에 대한 확장이 누락될 수 있습니다. 특성 매핑의 고급 설정으로 이동하여 특성을 수동으로 추가합니다. 자세한 내용은 [특성 매핑 사용자 지정](customize-application-attributes.md)을 참조하세요.

#### <a name="null-attribute-cant-be-provisioned"></a>Null 특성을 프로비저닝할 수 없음

Azure AD는 현재 Null 특성을 프로비저닝할 수 없습니다. 사용자 개체의 특성이 null이면 건너뜁니다. 

#### <a name="maximum-characters-for-attribute-mapping-expressions"></a>특성 매핑 식의 최대 문자 수

특성 매핑 식에는 최대 10,000자를 사용할 수 있습니다. 

#### <a name="unsupported-scoping-filters"></a>지원되지 않는 범위 지정 필터

디렉터리 확장 및 **appRoleAssignments**, **userType**, **accountExpires** 특성은 범위 지정 필터로 지원되지 않습니다.

#### <a name="multivalue-directory-extensions"></a>다중값 디렉터리 확장

특성 매핑 또는 범위 지정 필터에는 다중값 디렉터리 확장을 사용할 수 없습니다. 

## <a name="service-issues"></a>서비스 문제 

#### <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

- 암호 프로비저닝은 지원되지 않습니다. 
- 중첩 그룹 프로비저닝은 지원되지 않습니다. 
- 테넌트 크기 때문에 B2C 테넌트에 대한 프로비저닝은 지원되지 않습니다.
- 모든 클라우드에서 모든 프로비저닝 앱을 사용할 수 있는 것은 아닙니다. 예를 들어, 정부 클라우드에서는 아직 Atlassian을 사용할 수 없습니다. 앱 개발자들과 협력하여 앱을 모든 클라우드에 온보딩하기 위해 노력하고 있습니다.

#### <a name="automatic-provisioning-isnt-available-on-my-oidc-based-application"></a>내 OIDC 기반 애플리케이션에서 자동 프로비저닝을 사용할 수 없음

앱 등록을 만드는 경우 엔터프라이즈 앱의 해당 서비스 주체는 자동 사용자 프로비저닝을 사용하도록 설정되지 않습니다. 여러 조직에서 앱을 사용하려는 경우 갤러리에 앱을 추가하도록 요청하거나 프로비저닝을 위한 두 번째 비 갤러리 앱을 만들어야 합니다.

#### <a name="the-provisioning-interval-is-fixed"></a>프로비저닝 간격이 고정되어 있음

프로비저닝 주기 사이의 [시간](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)은 현재 구성할 수 없습니다. 

#### <a name="changes-not-moving-from-target-app-to-azure-ad"></a>변경 내용이 대상 앱에서 Azure AD로 이동하지 않음

앱 프로비저닝 서비스는 외부 앱에서 변경한 내용을 인식하지 못합니다. 따라서 롤백 동작이 수행되지 않습니다. 앱 프로비저닝 서비스는 Azure AD에서 변경된 내용을 따릅니다. 

#### <a name="switching-from-sync-all-to-sync-assigned-not-working"></a>모두 동기화에서 할당된 항목 동기화로 전환이 작동하지 않음

**모두 동기화** 에서 **할당된 항목 동기화** 로 범위를 변경한 후에는 다시 시작을 수행하여 변경 내용이 적용되도록 해야 합니다. UI에서 다시 시작할 수 있습니다.

#### <a name="provisioning-cycle-continues-until-completion"></a>프로비저닝 주기가 완료될 때까지 계속됨

프로비저닝 `enabled = off`를 설정하거나 **중지** 를 선택하면 현재 프로비저닝 주기가 완료될 때까지 계속 실행됩니다. 서비스는 프로비저닝을 설정할 때까지 이후 주기의 실행을 중지합니다.

#### <a name="member-of-group-not-provisioned"></a>그룹의 멤버가 프로비저닝되지 않음

그룹이 범위 내에 있고 멤버가 범위를 벗어난 경우 그룹은 프로비저닝됩니다. 범위를 벗어난 사용자는 프로비저닝되지 않습니다. 멤버가 다시 범위 내에 들어오면 서비스가 즉시 변경 내용을 검색하지 않습니다. 프로비저닝을 다시 시작하면 문제가 해결됩니다. 정기적으로 서비스를 다시 시작하여 모든 사용자가 적절히 프로비저닝되도록 하는 것이 좋습니다.

#### <a name="manager-isnt-provisioned"></a>관리자가 프로비저닝되지 않음

사용자 및 해당 관리자가 둘 다 프로비저닝 범위에 있는 경우 서비스에서 사용자를 프로비저닝한 다음, 관리자를 업데이트합니다. 사용자가 범위 내에 있고 관리자가 범위를 벗어난 경우 관리자 참조 없이 사용자를 프로비저닝합니다. 관리자가 범위 내에 들어오면 프로비저닝을 다시 시작하고 서비스가 모든 사용자를 다시 평가할 때까지 관리자 참조가 업데이트되지 않습니다. 

## <a name="on-premises-application-provisioning"></a>온-프레미스 애플리케이션 프로비저닝
다음 정보는 Azure AD ECMA 커넥터 호스트 및 온-프레임 애플리케이션 프로비저닝의 알려진 제한 사항의 최근 목록입니다.

### <a name="application-and-directories"></a>애플리케이션 및 디렉터리
다음 애플리케이션 및 디렉터리는 아직 지원되지 않습니다.

#### <a name="active-directory-domain-services-user-or-group-writeback-from-azure-ad-by-using-the-on-premises-provisioning-preview"></a>Active Directory Domain Services(온-프레미스 프로비저닝 미리 보기를 사용하여 Azure AD에서 사용자 또는 그룹 쓰기 저장)
   - Azure AD Connect가 사용자를 관리하는 경우 권한 원본은 온-프레미스 Azure AD입니다. 따라서 Azure AD에서는 사용자 특성을 변경할 수 없습니다. 이 미리 보기는 Azure AD Connect에서 관리하는 사용자의 권한 원본을 변경하지 않습니다.
   - Azure AD Connect 및 온-프레미스 프로비저닝을 사용하여 그룹 또는 사용자를 Active Directory Domain Services로 프로비저닝하려고 시도하면 루프가 생성되어 Azure AD Connect가 클라우드의 프로비저닝 서비스에서 변경한 사항을 덮어쓸 수 있습니다. Microsoft는 그룹 또는 사용자 쓰기 저장을 위한 전용 기능을 개발하고 있습니다. [이 웹 사이트](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/16887037-enable-user-writeback-to-on-premise-ad-from-azure)에서 UserVoice 피드백에 찬성하여 미리 보기의 상태를 추적합니다. 또는 Azure AD에서 Active Directory로의 사용자/그룹 쓰기 저장에 [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016)를 사용할 수 있습니다.

#### <a name="connectors-other-than-sql"></a>SQL 이외의 커넥터

   Azure AD ECMA 커넥터 호스트는 제네릭 SQL 커넥터에 대해 공식적으로 지원됩니다. 웹 서비스 커넥터 또는 사용자 지정 ECMA 커넥터와 같은 다른 커넥터를 사용할 수 있지만 *아직은 지원되지 않습니다*.

#### <a name="azure-ad"></a>Azure AD

   온-프레미스 프로비저닝을 사용하면 이미 Azure AD에 있는 사용자를 타사 애플리케이션으로 프로비저닝할 수 있습니다. *타사 애플리케이션에서 사용자를 디렉터리로 가져올 수 없습니다.* 고객은 네이티브 HR 통합, Azure AD Connect, Microsoft Identity Manager 또는 Microsoft Graph를 사용하여 사용자를 디렉터리로 가져와야 합니다.

### <a name="attributes-and-objects"></a>특성 및 개체 
다음 특성 및 개체는 지원되지 않습니다.
   - 다중값 특성
   - 참조 특성(예: 관리자)
   - 그룹.
   - 복합 앵커(예: ObjectTypeName+UserName)
   - 온-프레미스 애플리케이션이 Azure AD와 페더레이션되지 않는 경우도 있으며 로컬 암호가 필요합니다. 온-프레미스 프로비저닝 미리 보기는 Azure AD와 타사 애플리케이션 간의 *일회성 암호 프로비저닝 또는 암호 동기화* 를 지원하지 않습니다.
   - **export_password** 가상 특성, **SetPassword** 및 **ChangePassword** 작업은 지원되지 않습니다.

#### <a name="ssl-certificates"></a>SSL 인증서
   Azure AD ECMA 커넥터 호스트의 경우 현재 SSL 인증서가 Azure의 신뢰를 받거나 프로비저닝 에이전트가 사용되어야 합니다. 인증서 주체는 Azure AD ECMA 커넥터 호스트가 설치된 호스트 이름과 일치해야 합니다.

#### <a name="anchor-attributes"></a>앵커 특성
   Azure AD ECMA 커넥터 호스트는 현재 앵커 특성 변경(이름 바꾸기) 또는 대상 시스템을 지원하지 않으므로 여러 특성이 앵커 하나를 구성해야 합니다. 

#### <a name="attribute-discovery-and-mapping"></a>특성 검색 및 매핑
   대상 애플리케이션에서 지원하는 특성은 Azure Portal의 **특성 매핑** 에서 검색 및 표시됩니다. 새로 추가된 특성은 계속해서 검색됩니다. 특성 유형이 변경되고(예: 문자열에서 부울로) 특성이 매핑의 일부인 경우 Azure Portal에서는 유형이 자동으로 변경되지 않습니다. 고객이 매핑의 고급 설정으로 이동하여 특성 유형을 수동으로 업데이트해야 합니다.

## <a name="next-steps"></a>다음 단계
[프로비저닝 작동 방법](how-provisioning-works.md)
