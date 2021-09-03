---
title: 'Azure AD Connect 단일 개체 동기화 '
description: 문제 해결을 위해 한 개체를 Active Directory에서 Azure AD로 동기화하는 방법을 알아봅니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/24/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dec171c299bdd8ea6c5989794539b6c79e7d3fe
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229692"
---
# <a name="azure-ad-connect-single-object-sync"></a>Azure AD Connect 단일 개체 동기화 

Azure AD Connect 단일 개체 동기화 도구는 Active Directory에서 Azure Active Directory으로 개별 개체를 동기화하는 데 사용할 수 있는 PowerShell cmdlet입니다. 생성된 보고서를 사용하여 개체 동기화 문제를 조사하고 문제를 해결할 수 있습니다. 

> [!NOTE]
> 이 도구는 Active Directory에서 Azure Active Directory로의 동기화를 지원합니다. Azure Active Directory에서 Active Directory로의 동기화는 지원하지 않습니다. 
>
> 이 도구는 개체 수정 추가 및 업데이트 동기화를 지원합니다. 개체 수정 삭제는 동기화는 지원되지 않습니다. 

## <a name="how-it-works"></a>작동 방식
단일 개체 동기화 도구에는 가져올 원본 커넥터와 파티션을 찾기 위한 입력으로 Active Directory 고유 이름이 필요합니다. Azure Active Directory로 변경 내용을 내보냅니다. 이 도구는 **provisioningObjectSummary** 리소스 종류와 비슷한 JSON 출력을 생성합니다. 

단일 개체 동기화 도구는 다음 단계를 수행합니다. 

 1. 개체(원본) 도메인(Active Directory Connector 및 파티션)이 동기화 범위 내에 있는지 확인합니다. 
 2. 개체(대상) 도메인(Azure Active Directory Connector 및 파티션)이 동기화 범위 내에 있는지 확인합니다. 
 3. 개체의 조직 구성 단위가 동기화 범위 내에 있는지 확인합니다. 
 4. 커넥터 계정 자격 증명을 사용하여 개체에 액세스할 수 있는지 확인합니다. 
 5. 개체의 형식이 동기화 범위 내에 있는지 확인합니다. 
 6. 그룹 필터링을 사용하도록 설정하는 경우 개체가 동기화 범위 내에 있는지 확인합니다. 
 7. Active Directory에서 Active Directory Connector 공간으로 개체를 가져옵니다. 
 8. Azure Active Directory에서 Azure Active Directory Connector 공간으로 개체를 가져옵니다. 
 9. Active Directory Connector 공간에서 개체를 동기화합니다. 
 10. Azure Active Directory Connector 공간에서 Azure Active Directory로 개체를 내보냅니다. 

JSON 출력 외에도 이 도구는 동기화 작업에 대한 모든 세부 정보를 포함하는 HTML 보고서를 생성합니다. HTML 보고서는 **<date>** 에 있습니다. 필요한 경우 이 HTML 보고서를 지원 팀과 공유하여 추가 문제 해결을 수행할 수 있습니다. 

HTML 보고서에는 다음이 포함됩니다. 

|탭|Description|
|-----|-----|
|단계|개체를 동기화하는 데 필요한 단계를 간략하게 설명합니다. 각 단계에는 문제 해결에 대한 세부 정보가 포함되어 있습니다. 가져오기, 동기화 및 내보내기 단계에는 이름, 다중값, 형식, 값, 값 추가, 값 삭제, 작업, 동기화 규칙, 매핑 유형 및 데이터 원본과 같은 추가 특성 정보가 포함됩니다.| 
|문제 해결 및 권장 사항|오류 코드 및 이유를 제공합니다. 오류 정보는 오류가 발생하는 경우에만 사용할 수 있습니다.| 
|수정된 속성|이전 값과 새 값을 표시합니다. 이전 값이 없거나 새 값이 삭제된 경우 해당 셀은 비어 있습니다. 다중값 특성의 경우 개수를 표시합니다. 특성 이름은 단계 탭: Azure Active Directory Connector 공간에서 Azure Active Directory로 개체 내보내기: 이름, 다중값, 유형, 값, 값 추가, 값 삭제, 작업, 동기화 규칙, 매핑 유형 및 데이터 원본 등의 특성에 대한 추가 세부 정보를 포함하는 특성 정보에 대한 링크입니다.| 
|요약|발생한 상황에 대한 개요와 원본 및 대상 시스템의 개체에 대한 식별자를 제공합니다.| 

## <a name="prerequisites"></a>필수 구성 요소 

단일 개체 동기화 도구를 사용하려면 다음을 사용해야 합니다.  
 - 2021년 3월 릴리스([1.6.4.0](reference-connect-version-history.md#1640)) 이상의 Azure AD Connect
 -  [PowerShell 5.0](/powershell/scripting/windows-powershell/whats-new/what-s-new-in-windows-powershell-50?view=powershell-7.1)

### <a name="run-the-single-object-sync-tool"></a>단일 개체 동기화 도구 실행 

단일 개체 동기화 도구를 실행하려면 다음 단계를 수행합니다. 

 1. [관리자 권한으로 실행] 옵션을 사용하여 Azure AD Connect 서버에서 새 Windows PowerShell 세션을 엽니다. 

 2. [실행 정책](/powershell/module/microsoft.powershell.security/set-executionpolicy)을 RemoteSigned 또는 Unrestricted로 설정합니다. 

 3. 동기화 작업이 실행되고 있지 않은지 확인한 다음, 동기화 스케줄러를 사용하지 않도록 설정합니다. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. AdSync 진단 모듈 가져오기 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. 단일 개체 동기화 cmdlet을 호출합니다. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. 동기화 스케줄러를 다시 사용하도록 설정합니다. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|단일 개체 동기화 입력 매개 변수|Description| 
|-----|----|
|DistinguishedName|필수 문자열 매개 변수입니다. </br></br>동기화 및 문제 해결이 필요한 Active Directory 개체의 고유 이름입니다.| 
|StagingMode|선택적 스위치 매개 변수입니다.</br></br>이 매개 변수를 사용하여 Azure Active Directory에 변경 내용을 내보내지 못하게 할 수 있습니다.</br></br>**참고**: cmdlet이 동기화 작업을 커밋합니다. </br></br>**참고**: Azure AD Connect 스테이징 서버는 Azure Active Directory로 변경 내용을 내보내지 않습니다.|
|NoHtmlReport|선택적 스위치 매개 변수입니다.</br></br>이 매개 변수를 사용하여 HTML 보고서 생성을 방지할 수 있습니다. 

## <a name="single-object-sync-throttling"></a>단일 개체 동기화 제한 

단일 개체 동기화 도구는 개체 동기화 문제를 조사하고 문제를 해결하기 위해 **고안되었습니다**. 스케줄러에 따라 실행되는 동기화 주기를 대체하기 위한 것이 **아닙니다**. Azure Active Directory에서 가져오기 및 Azure Active Directory로의 내보내기에는 제한 한도가 적용됩니다. 제한 한도에 도달하면 5분 후에 다시 시도하세요. 

## <a name="next-steps"></a>다음 단계
- [개체 동기화 문제 해결](tshoot-connect-objectsync.md)
- [동기화되지 않는 개체 문제 해결](tshoot-connect-object-not-syncing.md)
- [Azure AD Connect 개체 및 특성에 대한 엔드투엔드 문제 해결](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)