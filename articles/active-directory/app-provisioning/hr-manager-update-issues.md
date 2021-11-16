---
title: HR 프로비저닝과 관련된 관리자 업데이트 문제 해결
description: HR 프로비저닝과 관련된 관리자 업데이트 문제를 해결하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 21635b7c7df6639745916aecd207780e6ae5df52
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482844"
---
# <a name="troubleshoot-hr-manager-update-issues"></a>HR 관리자 업데이트 문제 해결

**적용 대상:**
* 온-프레미스 Active Directory 사용자 프로비저닝에 대한 Workday
* Azure Active Directory 사용자 프로비저닝에 대한 Workday
* 온-프레미스 Active Directory 사용자 프로비저닝에 대한 SAP SuccessFactors
* Azure Active Directory 사용자 프로비저닝에 대한 SAP SuccessFactors

## <a name="understanding-how-manager-reference-resolution-works"></a>관리자 참조 확인의 작동 방식 이해
Azure AD 프로비전 서비스는 Azure AD의 사용자-관리자 관계가 항상 HR 데이터와 동기화되도록 관리자 정보를 자동으로 업데이트합니다. *관리자 참조 확인* 이라는 프로세스를 사용하여 *관리자* 특성을 정확하게 업데이트합니다. 프로세스 세부 정보로 이동하기 전에 관리자 정보가 Azure AD 및 온-프레미스 Active Directory에 저장되는 방법을 이해하는 것이 중요합니다. 

* **온-프레미스 Active Directory** 에서 *manager* 특성은 AD에 있는 관리자 계정의 *dn(distinguishedName)* 을 저장합니다. 
* **Azure AD** 에서 *manager* 특성은 Azure AD의 DirectoryObject 탐색 속성입니다. Azure Portal에서 사용자 레코드를 확인하는 경우 Azure AD에 있는 관리자 레코드의 *displayName* 이 표시됩니다. 

*관리자 참조 확인* 은 2단계 프로세스입니다. 
* 1단계: *원본 앵커* 및 *대상 앵커* 로 참조되는 특성 쌍을 사용하여 관리자의 HR 원본 레코드를 관리자의 대상 계정 레코드와 연결합니다. 
* 2단계: 스키마에 정의된 관리자 참조 특성을 사용하여 대상의 관리자 특성을 필요한 형식으로 업데이트합니다. 

각 앱에 대한 기본 앵커 특성 및 참조 특성은 아래에 나와 있습니다. 

| 앱 이름 | 앵커 특성 | 사용자 프로필의 참조 특성 | 
|--|--|--| 
| Workday | WID | ManagerReference(관리자 레코드의 WID를 가리킴) |
| SAP SuccessFactors | personIdExternal | manager(관리자 레코드의 personIdExternal을 가리킴) |
| 온-프레미스 Active Directory | objectGUID | manager(관리자 레코드의 DN을 가리킴) |
| Azure AD | objectId | manager(관리자의 Azure AD 레코드를 가리킴) |

## <a name="prerequisites-for-successful-manager-update"></a>성공적인 관리자 업데이트를 위한 필수 구성 요소
*관리자 참조 확인* 이 성공적으로 작동하려면 다음 필수 구성 요소를 충족해야 합니다. 
* 위에 나열된 기본 원본 및 대상 앵커를 사용하도록 프로비저닝 앱을 구성해야 합니다. 이러한 앵커 및 참조 특성과 연결된 메타데이터 특성(데이터 형식, API 식)을 변경하지 마세요. 
* 관리자 특성과 연결된 API 식(Workday의 경우 XPATH 및 SuccessFactors의 경우 JSONPath)은 Null이 아닌 유효한 값으로 확인됩니다. 
   * Workday ManagerReference 기본 XPATH API 식: `wd:Worker/wd:Worker_Data/wd:Management_Chain_Data/wd:Worker_Supervisory_Management_Chain_Data[position()=1]/wd:Management_Chain_Data[last()=position()]/wd:Manager_Reference/wd:ID[@wd:type='WID']/text()`
   * SuccessFactors manager 기본 JSONPath API 식: `$.employmentNav.results[0].userNav.manager.empInfo.personIdExternal`
* 관리자 레코드도 프로비전 작업의 범위 내에 있어야 합니다. 
* 프로비저닝 앱에서 사용자 레코드를 처리하기 전에 관리자 레코드를 먼저 처리해야 합니다. 

## <a name="provision-on-demand-does-not-update-manager-attribute"></a>provision-on-demand에서 관리자 특성을 업데이트하지 않음
| | |
|--|--|
| **문제점** | 인바운드 프로비저닝 앱을 성공적으로 구성했습니다. provision-on-demand를 사용하여 동기화를 테스트하고 있습니다. 관리자 특성을 업데이트하지 않고, *"잘못된 값"* 오류 메시지가 표시됩니다.  |
| **원인** | 프로비전 작업에서 [성공적인 관리자 업데이트를 위한 필수 구성 요소](#prerequisites-for-successful-manager-update) 중 하나를 충족하지 않습니다.  |
| **해결 방법** | * 기본 관리자 특성 매핑을 변경한 경우 기본 매핑을 복원합니다. <br> * 관리자 레코드가 범위 내에 있고 관리자 API 식에서 유효한 값으로 확인하는지 확인합니다. <br> * provision-on-demand를 먼저 관리자의 레코드에 대해 실행한 다음, 사용자의 레코드에 대해 실행합니다.  |

## <a name="full-sync-does-not-update-manager-attribute"></a>전체 동기화에서 관리자 특성을 업데이트하지 않음
| | |
|--|--|
| **문제점** | 인바운드 프로비저닝 앱을 성공적으로 구성했습니다. 범위 지정 필터를 사용하여 특정 HR 레코드만 처리하고 있습니다. 관리자 확인이 일부 사용자에 대해 수행되지 않는 것을 확인할 수 있습니다.  |
| **원인** | 범위 지정 필터를 사용하는 경우 관리자 레코드가 범위 내에 있지 않을 가능성이 높습니다.  |
| **해결 방법** | 관리자 레코드를 범위에 추가하도록 범위 지정 필터를 업데이트합니다.  |

## <a name="next-steps"></a>다음 단계

* [Azure AD와 Workday 통합 시나리오 및 웹 서비스 호출에 대한 자세한 정보](workday-integration-reference.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](check-status-user-account-provisioning.md).
