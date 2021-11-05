---
title: Azure AD Identity Governance를 사용하여 더 이상 리소스 액세스 권한이 없는 외부 사용자 검토 및 제거
description: 액세스 검토를 사용하여 파트너 조직의 멤버에서 액세스 제거 확장
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: ajburnle
ms.openlocfilehash: eef4702b7166dabb81cd2391d4acb8d8211b9648
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051917"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Azure AD(Azure Active Directory) Identity Governance를 사용하여 더 이상 리소스 액세스 권한이 없는 외부 사용자 검토 및 제거

이 문서에서는 외부 ID를 검토하고 더 이상 필요하지 않은 경우 Azure AD에서 제거할 수 있도록 외부 ID를 확인 및 선택할 수 있는 기능과 방법을 설명합니다. 클라우드를 사용하면 내부 또는 외부 사용자와 더 쉽게 협업할 수도 있습니다. Office 365를 채택한 조직에서는 사용자가 데이터, 문서 또는 디지털 작업 영역(예: Teams)에서 함께 작업할 때 외부 ID(게스트 포함)의 확산을 확인하기 시작합니다. 조직은 협업을 사용하는 것과 보안 및 거버넌스 요구 사항을 충족하는 것의 균형을 맞추어야 합니다. 이 작업에는 파트너 조직에 속하고 협업을 위해 테넌트에 초대된 외부 사용자를 평가하고 정리하는 작업과 더 이상 필요하지 않은 경우 Azure AD에서 외부 사용자를 제거하는 작업이 포함되어야 합니다.

>[!NOTE]
>Azure AD 액세스 검토를 사용하는 데 유효한 Azure AD Premium P2, Enterprise Mobility + Security E5 유료 또는 평가판 라이선스가 필요합니다. 자세한 내용은 [Azure Active Directory 버전](../fundamentals/active-directory-whatis.md)을 참조하세요.

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>테넌트의 외부 조직에서 사용자를 검토하는 이유는 무엇인가요?

대부분 조직에서 최종 사용자는 협업을 위해 비즈니스 파트너 및 공급업체를 초대하는 프로세스를 시작합니다. 협업의 필요성으로 인해 조직은 외부 사용자를 정기적으로 평가하고 증명하는 방법을 리소스 소유자와 최종 사용자에게 제공합니다. 종종 새 협업 파트너를 온보딩하는 프로세스가 계획되고 고려되지만, 대부분 협업은 명확한 종료 날짜가 없으므로 사용자에게 더 이상 액세스 권한이 필요하지 않은 시기가 항상 분명한 것은 아닙니다. 또한 기업은 ID 수명 주기 관리를 통해 Azure AD를 정리하고, 더 이상 조직의 리소스에 액세스할 필요가 없는 사용자를 제거합니다. 디렉터리에서 파트너와 공급업체의 관련 ID 참조만 유지하면 제거되지 않은 외부 사용자에 대한 액세스 권한을 직원이 실수로 선택하고 부여할 위험을 줄일 수 있습니다. 이 문서에서는 권장되는 사전 제안부터 사후 대응 및 정리 작업에 이르기까지 외부 ID를 관리하는 여러 가지 옵션을 안내합니다.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>권한 관리를 사용하여 액세스 권한 부여 및 취소

권한 관리 기능은 리소스에 액세스할 수 있는 [외부 ID의 자동화된 수명 주기](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users)를 가능하게 합니다. 권한 관리를 통해 액세스를 관리하는 프로세스와 절차를 설정하고 액세스 패키지를 통해 리소스를 게시하여 리소스에 대한 외부 사용자 액세스를 추적하면 문제 해결이 훨씬 덜 복잡해집니다. Azure AD에서 [권한 관리 액세스 패키지](entitlement-management-overview.md)를 통해 액세스를 관리하는 경우 조직은 자체 사용자뿐 아니라 파트너 조직 사용자의 액세스를 중앙에서 정의하고 관리할 수 있습니다. 권한 관리는 액세스 패키지의 승인 및 할당을 사용하여 외부 사용자가 액세스 권한을 요청하고 할당받은 위치를 추적합니다. 외부 사용자가 모든 할당을 손실한 경우 권한 관리는 테넌트에서 자동으로 이 외부 사용자를 제거할 수 있습니다. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>권한 관리를 통해 초대되지 않은 게스트 찾기

직원에게 외부 사용자와 협업을 할 권한이 부여된 경우 해당 직원은 조직 외부 사용자를 원하는 만큼 초대할 수 있습니다. 검토할 개별 회사가 너무 많을 수 있거나 조직의 소유자 또는 스폰서가 없어서 외부 파트너를 찾고 회사에 맞게 조정된 동적 그룹으로 그룹화하고 이를 검토하는 것이 가능하지 않을 수 있습니다. Microsoft는 테넌트에서 외부 ID 사용을 분석하는 데 도움이 될 수 있는 샘플 PowerShell 스크립트를 제공합니다. 이 스크립트는 외부 ID를 열거하고 분류합니다. 스크립트를 사용하여 더 이상 필요하지 않을 수 있는 외부 ID를 식별하고 정리할 수 있습니다. 스크립트 출력의 일부로 스크립트 샘플은 추가 분석을 위해 식별된 그룹을 포함하고 Azure AD 액세스 검토를 사용하는 보안 그룹(덜 외부적인 파트너)을 자동으로 만드는 기능을 지원합니다.
스크립트는 [GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)에서 사용할 수 있습니다. 스크립트는 실행을 완료한 후 다음과 같은 외부 ID를 간단히 설명하는 HTML 출력 파일을 생성합니다.

- 더 이상 테넌트에 그룹 멤버 자격이 없음
- 테넌트에서 권한 있는 역할의 할당이 있음
- 테넌트에서 애플리케이션에 대한 할당이 있음

출력에는 각 외부 ID의 개별 도메인도 포함됩니다. 

>[!NOTE]
>위에서 참조된 스크립트는 Azure AD에서 그룹 멤버 자격, 역할 할당 및 애플리케이션 할당을 확인하는 샘플 스크립트입니다. 외부 사용자가 Azure AD 외부에서 받은 SharePoint(직접 멤버 자격 할당), Azure RBAC 또는 Azure DevOps와 같은 다른 할당이 애플리케이션에 있을 수 있습니다.

## <a name="review-resources-used-by-external-identities"></a>외부 ID에 사용되는 리소스 검토

Teams 또는 아직 권한 관리를 통해 관리되지 않는 기타 애플리케이션과 같은 리소스를 사용하는 외부 ID가 있는 경우 이 리소스에 대한 액세스도 정기적으로 검토하는 것이 좋습니다. Azure AD [액세스 검토](create-access-review.md)를 통해 리소스 소유자, 외부 ID 자체 또는 신뢰할 수 있는 다른 위임된 사용자가 지속적인 액세스가 필요했는지 여부를 입증하도록 하여 외부 ID의 액세스를 살펴볼 수 있습니다. 액세스 검토는 리소스를 대상으로 지정하고 리소스에 대한 액세스 권한이 있는 모든 사용자 또는 게스트 사용자만 중 하나로 범위가 지정된 검토 작업을 만듭니다. 그러면 검토자는 검토해야 하는 사용자가 조직의 직원을 포함한 모든 사용자인지 또는 외부 ID만인지 결과 목록에서 확인할 수 있습니다.

![그룹을 사용하여 액세스 검토](media/access-reviews-external-users/group-members.png)

리소스 소유자 중심의 검토 문화를 조성하면 외부 ID의 액세스를 관리하는 데 도움이 됩니다. 자신이 소유하는 정보의 액세스, 가용성 및 보안을 책임지는 리소스 소유자는 대부분의 경우 리소스 액세스와 관련된 의사 결정을 주도할 가장 좋은 대상 그룹이며 많은 외부 ID를 관리하는 스폰서 또는 중앙 IT보다는 리소스에 액세스하는 사용자에 더 가깝습니다.

## <a name="create-access-reviews-for-external-identities"></a>외부 ID의 액세스 검토 만들기

테넌트의 리소스에 더 이상 액세스할 수 없는 사용자는 해당 조직에서 더 이상 작업하지 않는 경우 제거될 수 있습니다. 이 외부 ID를 차단하고 삭제하기 전에 해당 외부 사용자에게 연락하고 여전히 필요한 사용자의 프로젝트 또는 고정 액세스를 간과하지 않았는지 확인하는 것이 좋습니다. 테넌트의 리소스에 대한 액세스 권한이 없는 것을 확인한 모든 외부 ID를 멤버로 포함하는 그룹을 만드는 경우 액세스 검토를 사용하여 모든 외부 ID가 여전히 액세스 권한이 필요하거나 액세스 권한을 보유하고 있는지 또는 미래에도 액세스 권한이 필요할 것인지 여부를 직접 증명하도록 할 수 있습니다. 검토 과정에서 액세스 검토의 검토 작성자는 **승인 시 이유 필요** 기능을 사용하여 계속 액세스를 위한 근거를 제공하도록 요구할 수 있으며, 이를 통해 외부 사용자가 테넌트에서 계속 액세스해야 하는 위치와 방법을 알아볼 수 있습니다. 또한 설정 **검토자 메일에 대한 추가 콘텐츠** 기능을 사용하여 사용자가 응답하지 않으면 액세스 권한이 손실될 것이고 계속 액세스해야 하는 경우에는 근거가 필요함을 사용자에게 알릴 수 있습니다. 계속해서 외부 ID가 응답하지 않거나 계속 액세스하기 위한 유효한 이유를 제공하지 못하는 경우 액세스 검토를 통해 외부 ID를 **사용하지 않도록 설정하고 삭제** 하려는 경우에는 다음 섹션에 설명된 대로 사용 안 함 및 삭제 옵션을 사용할 수 있습니다.

![검토 범위를 게스트 사용자로만 제한](media/access-reviews-external-users/guest-users-only.png)

검토가 완료되면 **결과** 페이지에 모든 외부 ID가 제공한 응답의 개요가 표시됩니다. 자동으로 결과를 적용하고 액세스 검토를 통해 사용하지 않도록 설정하고 삭제하도록 선택할 수 있습니다. 또는 제공된 응답을 검토하고 사용자의 액세스 권한을 제거할지 아니면 결정하기 전에 사용자에게 연락하여 추가 정보를 얻을지 여부를 결정할 수 있습니다. 일부 사용자에게 아직 검토하지 않은 리소스에 대한 액세스 권한이 있는 경우에는 검색 과정에서 검토를 사용하고 다음 검토 및 증명 주기를 보강할 수 있습니다.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 외부 ID 사용 안 함 및 삭제

그룹 또는 애플리케이션과 같은 리소스에서 원치 않는 외부 ID를 제거하는 옵션 외에도, Azure AD 액세스 검토는 외부 ID가 테넌트에 로그인하는 것을 차단하고 30일 후에 테넌트에서 외부 ID를 삭제할 수 있습니다. **30일 동안 사용자 로그인을 차단한 후 테넌트에서 제거** 를 선택하면 검토는 30일 동안 “적용 중” 상태로 유지됩니다. 이 기간 동안 현재 검토 중인 설정, 결과, 검토자 또는 감사 로그를 볼 수 없거나 구성할 수 없습니다. 

![완료 시 설정](media/access-reviews-external-users/upon-completion-settings.png)

새 액세스 검토를 만들 때 “완료 시 설정” 섹션에서 **거부된 사용자에 적용할 작업** 에 대해 **30일 동안 사용자 로그인을 차단한 후 테넌트에서 제거** 를 정의할 수 있습니다.

이 설정을 사용하여 Azure AD 테넌트에서 외부 ID를 식별, 차단 및 삭제할 수 있습니다. 검토자가 계속 액세스를 검토하고 거부한 외부 ID는 보유하고 있는 리소스 액세스 권한 또는 그룹 멤버 자격에 관계없이 차단 및 삭제됩니다. 이 설정은 검토 중인 외부 사용자가 더 이상 리소스 액세스 권한을 보유하지 않고 테넌트에서 안전하게 제거될 수 있는지 유효성을 검사한 후 또는 사용자의 고정 액세스에 관계없이 사용자를 제거하려는 경우 마지막 단계로 사용하는 것이 가장 좋습니다. “사용 안 함 및 삭제” 기능은 외부 사용자를 먼저 차단하여 테넌트에 로그인하고 리소스에 액세스하는 기능을 제거합니다. 이 스테이지에서는 리소스 액세스가 취소되지 않으며, 외부 사용자를 다시 인스턴스화하려는 경우 로그온 기능을 다시 구성할 수 있습니다. 추가 작업이 없으면 차단된 외부 ID가 30일 후에 디렉터리에서 삭제되므로 계정 및 해당 액세스가 제거됩니다.

## <a name="next-steps"></a>다음 단계

- [액세스 검토 - Graph API](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)
- [권한 관리 - Graph API](/graph/api/resources/entitlementmanagement-root)
