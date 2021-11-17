---
title: OKTA 동기화 프로비저닝을 Azure AD Connect 기반 동기화로 마이그레이션하는 자습서
titleSuffix: Active Directory
description: 이 자습서에서는 OKTA 동기화 프로비저닝을 Azure AD Connect 기반 동기화로 마이그레이션하는 방법을 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 0902fbd761cb66eb5dbee4a5e3406ecba688c0b7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451486"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-ad-connect-based-synchronization"></a>자습서: OKTA 동기화 프로비저닝을 Azure AD Connect 기반 동기화로 마이그레이션

이 자습서에서는 조직에서 현재 OKTA에서 Azure AD(Azure Active Directory)로 사용자 프로비저닝을 마이그레이션하고 사용자 동기화 또는 유니버설 동기화를 Azure AD Connect에 마이그레이션하는 방법을 알아봅니다. 이 기능을 사용하면 Azure AD 및 Office 365를 추가로 프로비전할 수 있습니다.

동기화 플랫폼을 마이그레이션하는 것은 작은 변화가 아닙니다. 준비 모드에서 Azure AD Connect를 제거하거나 Azure AD 클라우드 프로비저닝 에이전트를 활성화하기 전에 이 문서에 언급된 프로세스의 각 단계를 사용자 환경에 대해 유효성을 검사해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

OKTA 프로비저닝에서 Azure AD로 전환하는 경우 두 가지 선택 항목이 있습니다. Azure AD Connect 서버 또는 Azure AD 클라우드 프로비저닝을 사용할 수 있습니다. 두 항목의 차이점을 이해하려면 [Microsoft 의 비교 문서](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync)를 참조하세요.

Azure AD 클라우드 프로비저닝은 유니버설 또는 사용자 동기화를 사용하는 OKTA 고객에게 가장 친숙한 마이그레이션 경로가 됩니다. 클라우드 프로비저닝 에이전트는 경량입니다. OKTA 디렉터리 동기화 에이전트와 같은 도메인 컨트롤러 또는 그 근처에 설치할 수 있습니다. 동일한 서버에 설치하지 마세요.

사용자를 동기화할 때 조직에서 다음 기술 중 일부를 활용해야 하는 경우 Azure AD Connect 서버를 사용합니다.

- 디바이스 동기화: 하이브리드 Azure AD 조인 또는 비즈니스용 Hello
- 통과 인증
- 150,000개 넘는 개체 지원
- 쓰기 저장에 대한 지원

>[!NOTE]
>Azure AD Connect 또는 Azure AD 클라우드 프로비저닝을 설치할 때는 모든 필수 구성 요소를 고려해야 합니다. 설치를 계속하기 전에 자세한 내용은 [Azure AD Connect 필수 구성 요소](../hybrid/how-to-connect-install-prerequisites.md)를 참조하세요.

## <a name="confirm-immutableid-attribute-synchronized-by-okta"></a>OKTA에서 동기화된 ImmutableID 특성 확인

ImmutableID는 동기화된 개체를 온-프레미스 개체에 연결하는 데 사용되는 핵심 특성입니다. OKTA는 온-프레미스 개체의 Active Directory objectGUID를 가져와 Base64로 인코딩된 문자열로 변환합니다. 그런 다음, 기본적으로 해당 문자열을 Azure AD의 ImmutableID 필드에 스탬프합니다.

Azure AD PowerShell에 연결하고 현재 ImmutableID 값을 검사할 수 있습니다. Azure AD PowerShell 모듈을 사용한 적이 없는 경우 다음 명령을 실행하기 전에 관리 PowerShell 세션에서 `Install-Module AzureAD`를 실행합니다.

```Powershell
Import-module AzureAD
Connect-AzureAD
```

모듈이 이미 있는 경우 최신 버전으로 업데이트하라는 경고가 표시될 수 있습니다(최신 버전이 만료된 경우).

모듈을 설치한 후 가져오고 다음 단계에 따라 Azure AD 서비스에 연결합니다.

1. 최신 인증 창에서 전역 관리자 자격 증명을 입력합니다.

   ![import-module을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

1. 테넌트에 연결 후 ImmutableID 값에 대한 설정을 확인합니다. 표시된 예제에서는 objectGUID의 OKTA 기본값을 ImmutableID로 사용합니다.

   ![ImmutableID에 대한 objectGUID의 OKTA 기본값을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

1. objectGUID에서 Base64로의 온-프레미스 변환을 수동으로 확인하는 방법에는 여러 가지가 있습니다. 개별 유효성 검사의 경우 다음 예제를 사용합니다.

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'
   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![OKTA objectGUID를 ImmutableID로 수동으로 변경하는 방법을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="mass-validation-methods-for-objectguid"></a>objectGUID에 대한 대용량 유효성 검사 메서드

Azure AD Connect로 전환하기 전에 Azure AD의 ImmutableID 값이 온-프레미스 값과 정확히 일치하는지 확인하는 것이 중요합니다.

이 예제는 *모든* 온-프레미스 Azure AD 사용자를 잡고 이미 CSV 파일로 계산된 objectGUID 값 및 ImmutableID 값 목록을 내보냅니다.

1. 온-프레미스 도메인 컨트롤러의 PowerShell에서 다음 명령을 실행합니다.

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![도메인 컨트롤러 온-프레미스 명령을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

1. Azure AD PowerShell 세션에서 다음 명령을 실행하여 이미 동기화된 값을 수집합니다.

   ```powershell
   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![Azure AD PowerShell 세션을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   두 내보내기 모두 있으면 각 사용자에 대한 ImmutableID가 일치하는지 확인합니다.

   >[!IMPORTANT]
   >클라우드의 ImmutableID 값이 objectGUID 값과 일치하지 않는 경우 OKTA 동기화에 대한 기본값을 수정한 것입니다. ImmutableID 값을 확인하기 위해 다른 특성을 선택했을 수 있습니다. 다음 섹션으로 이동하기 전에 ImmutableID 값을 채우는 원본 특성을 식별하는 것이 중요합니다. OKTA 동기화를 비활성화하기 전에 OKTA가 동기화 중인 특성을 업데이트해야 합니다.

## <a name="install-azure-ad-connect-in-staging-mode"></a>준비 모드에서 Azure AD Connect 설치

원본 및 대상 목표 목록을 준비한 후에는 Azure AD Connect 서버를 설치해야 합니다. Azure AD Connect 클라우드 프로비저닝을 사용하도록 선택한 경우 이 섹션을 건너뜁니다.

1. 선택한 서버에 대해 [Azure AD Connect 다운로드 및 설치](../hybrid/how-to-connect-install-custom.md)를 계속합니다.

1. **사용자 식별** 페이지의 **Azure AD로 사용자를 식별하는 방법 선택** 에서 **특정 특성 선택** 옵션을 선택합니다. 그런 다음, OKTA 기본값을 수정하지 않은 경우 **mS-DS-ConsistencyGUID** 를 선택합니다.

   >[!WARNING]
   >이 페이지에서 가장 중요한 단계입니다. **다음** 을 선택하기 전에 원본 앵커에 대해 선택하는 특성이 기존 Azure AD 사용자를 *현재* 채우고 있는지 확인합니다. 잘못된 특성을 선택하는 경우 Azure AD Connect를 제거하고 다시 설치하여 이 옵션을 다시 선택해야 합니다.
   
   ![mS-DS-ConsistencyGuid를 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png)

1. **구성** 페이지에서 **준비 모드 사용** 확인란을 선택했는지 확인합니다. **설치** 를 선택합니다.

   ![준비 모드 사용 확인란을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

1. 구성이 완료되면 **끝내기** 를 선택합니다.

   준비 모드를 종료하기 전에 ImmutableID 값이 제대로 일치하는지 확인합니다.

1. 관리자 권한으로 **동기화 서비스** 를 엽니다.

   ![동기화 서비스 열기를 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

1. domain.onmicrosoft.com 커넥터 공간에 대한 **전체 동기화** 에 **흐름 업데이트가 있는 커넥터** 탭에 표시되는 사용자가 있는지 확인합니다.

   ![흐름 업데이트가 있는 커넥터 탭을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

1. 내보내기에서 보류 중인 삭제가 없는지 확인합니다. **커넥터** 탭을 선택한 다음, domain.onmicrosoft.com 커넥터 공간을 강조 표시합니다. 그런 다음, **커넥터 공간 검색** 을 선택합니다.

   ![커넥터 공간 검색 작업을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

1. **커넥터 공간 검색** 대화 상자에서 **범위** 드롭다운을 선택하고 **보류 중인 내보내기** 를 선택합니다.

   ![보류 중인 내보내기를 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

1. **삭제** 를 선택한 다음, **검색** 을 선택합니다. 모든 개체가 제대로 일치하면 **Deletes** 에 대해 일치하는 레코드가 0개 있어야 합니다. 삭제 보류 중인 개체와 해당 온-프레미스 값을 기록합니다.

   ![삭제된 일치 레코드를 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

1. **삭제** 를 선택 취소하고 **추가**, **수정** 및 검색을 차례로 선택합니다. 현재 OKTA를 통해 Azure AD에 동기화 중인 모든 사용자에 대한 업데이트 함수가 표시됩니다. OKTA가 현재 동기화되지 않지만 Azure AD Connect 설치 중에 선택한 OU(조직 구성 단위) 구조에 있는 새 개체를 추가합니다.

   ![새 개체 추가를 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

1. 업데이트를 두 번 클릭하면 Azure AD Connect가 Azure AD와 통신하는 내용이 표시됩니다.

1. Azure AD에 이미 있는 사용자에 대한 **add** 함수가 있는 경우 해당 온-프레미스 계정이 해당 클라우드 계정과 일치하지 않습니다. AD Connect에서 새 개체를 만들고 예기치 않은 새 추가를 기록한다고 결정했습니다. 준비 모드를 종료하기 전에 Azure AD에서 ImmutableID 값을 수정해야 합니다.

   이 예제에서 OKTA는 온-프레미스 값이 제대로 채워지지 않았더라도 **mail** 특성을 사용자 계정에 스탬프 처리했습니다. Azure AD Connect에서 John Smith의 계정을 사용하는 경우 **mail** 특성이 해당 개체에서 삭제됩니다.

   업데이트에 Azure AD에 예상되는 모든 특성이 여전히 포함되어 있는지 확인합니다. 여러 특성을 삭제하는 경우 준비 모드를 제거하기 전에 이러한 온-프레미스 AD 값을 수동으로 채워야 할 수 있습니다.

   ![온-프레미스 추가 값을 채우는 것을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >다음 단계를 계속하기 전에 모든 사용자 특성이 제대로 동기화되고 있는지 확인하고 **보류 중인 내보내기** 탭에 예상대로 표시합니다. 삭제된 경우 ImmutableID 값이 일치하고 사용자가 동기화를 위해 선택한 OU 중 하나에 있는지 확인합니다.

## <a name="install-azure-ad-cloud-sync-agents"></a>Azure AD 클라우드 동기화 에이전트 설치

원본 및 대상 목표 목록을 준비한 후에는 [Azure AD 클라우드 동기화 에이전트를 설치하고 구성](../cloud-sync/tutorial-single-forest.md)해야 합니다. Azure AD Connect 서버를 사용하도록 선택한 경우 이 섹션을 건너뜁니다.

## <a name="disable-okta-provisioning-to-azure-ad"></a>Azure AD에 OKTA 프로비저닝 사용 안 함

Azure AD Connect 설치가 확인되고 보류 중인 내보내기 순서가 올바른지 확인한 후에는 Azure AD에 OKTA 프로비저닝을 비활성화할 차례입니다.

1. OKTA 포털로 이동하고 **애플리케이션** 을 선택한 다음, 사용자를 Azure AD에 프로비전하는 데 사용되는 OKTA 앱을 선택합니다. **프로비저닝** 탭을 열고 **통합** 섹션을 선택합니다.

   ![OKTA의 통합 섹션을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

1. **편집** 을 선택하고 **API 통합 사용** 옵션의 선택을 취소하고 **저장** 을 선택합니다.

   ![OKTA에서 API 통합 사용 편집을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >Azure AD로 프로비전을 처리하는 Office 365 앱이 여러 개 있는 경우 모두 해제되어 있는지 확인합니다.

## <a name="disable-staging-mode-in-azure-ad-connect"></a>Azure AD Connect에서 준비 모드 사용 안 함

OKTA 프로비전을 비활성화하면 Azure AD Connect 서버에서 개체 동기화를 시작할 준비가 된 것입니다. Azure AD 클라우드 동기화 에이전트로 이동하도록 선택한 경우 이 섹션을 건너뜁니다.

1. 바탕 화면에서 설치 마법사를 다시 실행하고 **구성** 을 선택합니다.

   ![Azure AD Connect 서버를 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

1. **준비 모드 구성** 을 선택한 다음, **다음** 을 선택합니다. 전역 관리자 자격 증명을 입력합니다.

   ![준비 모드 구성 옵션을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

1. **준비 모드 사용** 옵션의 선택을 취소하고 **다음** 을 선택합니다.

   ![준비 모드 사용 옵션의 선택 취소를 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

1. **구성** 을 선택하여 계속합니다.

   ![구성 단추를 선택하는 것을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

1. 구성이 완료되면 관리자 권한으로 **동기화 서비스** 를 엽니다. domain.onmicrosoft.com 커넥터에서 **내보내기** 를 봅니다. 모든 추가, 업데이트 및 삭제가 예상대로 수행되는지 확인합니다.

   ![동기화 서비스 확인을 보여 주는 스크린샷](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

이제 Azure AD Connect 서버 기반 프로비저닝으로 성공적으로 마이그레이션되었습니다. Azure AD Connect의 기능 집합에 대한 업데이트 및 확장은 설치 마법사를 다시 실행하여 수행할 수 있습니다.

## <a name="enable-cloud-sync-agents"></a>클라우드 동기화 에이전트 사용

OKTA 프로비저닝을 비활성화하면 Azure AD 클라우드 동기화 에이전트에서 개체 동기화를 시작할 준비가 된 것입니다. [Azure AD 포털](https://aad.portal.azure.com/)로 돌아갑니다.

1. **구성** 프로필을 **사용** 으로 수정합니다.

1. 프로비저닝 메뉴로 돌아가서 **로그** 를 선택합니다.

1. 프로비저닝 커넥터가 적절하게 내부 개체를 업데이트했는지 평가합니다. 클라우드 동기화 에이전트는 비파괴입니다. 일치가 제대로 수행되지 않으면 업데이트가 실패합니다.

1. 사용자가 일치하지 않으면 필요한 업데이트를 수행하여 ImmutableID 값을 바인딩합니다. 그런 다음, 클라우드 프로비저닝 동기화를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

OKTA에서 Azure AD로의 마이그레이션에 대한 자세한 내용은 다음을 참조하세요.

- [Okta에서 Azure AD로 애플리케이션 마이그레이션](migrate-applications-from-okta-to-azure-active-directory.md)
- [Okta 페더레이션을 Azure AD 관리 인증으로 마이그레이션](migrate-okta-federation-to-azure-active-directory.md)
- [OKTA 로그온 정책을 Azure AD 조건부 액세스로 마이그레이션](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
