---
title: 'Azure AD Connect: Microsoft 365 리소스에 대한 기본 데이터 위치 구성'
description: Azure Active Directory Connect 동기화를 사용하여 Microsoft 365 사용자 리소스를 사용자에게 가깝게 배치하는 방법을 설명합니다.
author: billmath
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/09/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b709932114ad9e527b82a1457310ff01d2cff38
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768556"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-microsoft-365-resources"></a>Azure Active Directory Connect 동기화: Microsoft 365 리소스에 대한 기본 데이터 위치 구성
이 토픽의 목적은 Azure AD(Azure Active Directory) Connect 동기화에서 기본 데이터 위치에 대한 특성을 구성하는 방법을 안내하는 것입니다. 누군가 Microsoft 365에서 Multi-Geo 기능을 사용하는 경우 이 특성으로 사용자의 Microsoft 365 데이터의 지리적 위치를 지정합니다. *region(지역)* 과 *Geo(지역)* 라는 용어는 서로 바꿔 사용할 수 있습니다.

## <a name="supported-multi-geo-locations"></a>지원되는 다중 지역 위치
Azure AD Connect에서 지원하는 모든 지역 목록은 [Microsoft 365 다중 지역 가용성](/microsoft-365/enterprise/microsoft-365-multi-geo#microsoft-365-multi-geo-availability)을 참조하세요.

## <a name="enable-synchronization-of-preferred-data-location"></a>기본 데이터 위치의 동기화 사용
기본적으로 사용자를 위한 Microsoft 365 리소스는 Azure AD 테넌트와 같은 지역에 있습니다. 예를 들어 테넌트가 북아메리카에 있는 경우 사용자의 Exchange 사서함도 북아메리카에 있습니다. 다국적 조직의 경우 이 상황은 최적이 아닐 수 있습니다.

**preferredDataLocation** 특성을 설정하여 사용자의 지역을 정의할 수 있습니다. 사서함 및 OneDrive와 같은 사용자의 Microsoft 365 리소스를 사용자와 동일한 지역에 두고, 전체 조직에 대해 하나의 테넌트만 유지할 수 있습니다.

> [!IMPORTANT]
> 다중 지역은 현재 활성 기업 계약이 있고 최소 250개의 Microsoft 365 Services 구독이 있는 고객이 사용할 수 있습니다. 자세한 내용은 Microsoft 담당자에게 문의하세요.
>
> Azure AD Connect에서 지원하는 모든 지역 목록은 [Microsoft 365 다중 지역 가용성](/microsoft-365/enterprise/microsoft-365-multi-geo#microsoft-365-multi-geo-availability)을 참조하세요.




### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect 동기화 지원

Azure AD Connect는 1.1.524.0 이상 버전의 **User** 개체에 대한 **preferredDataLocation** 특성의 동기화를 지원합니다. 특히:

* Azure AD Connector의 **User** 개체 형식의 스키마가 **preferredDataLocation** 특성을 포함하도록 확장되었습니다. 특성의 형식은 단일 값 문자열입니다.
* 메타버스의 **Person** 개체 형식의 스키마가 **preferredDataLocation** 특성을 포함하도록 확장되었습니다. 특성의 형식은 단일 값 문자열입니다.

기본적으로 **preferredDataLocation** 은 동기화가 허용되지 않습니다. 이 기능은 대규모 조직을 대상으로 합니다. Windows Server 2019의 Active Directory 스키마에는 이 용도로 사용해야 하는 **msDS-preferredDataLocation** 특성이 있습니다. Active Directory 스키마를 업데이트하지 않았으며 업데이트할 수 없는 경우 사용자의 Microsoft 365 지역을 보유할 특성을 식별해야 합니다. 이러한 특성은 각 조직마다 다를 수 있습니다.

> [!IMPORTANT]
> Azure AD를 사용하면 Azure AD PowerShell을 사용하여 **클라우드 User 개체** 의 **preferredDataLocation** 특성을 직접 구성할 수 있습니다. **동기화된 User 개체** 에 이 특성을 구성하려면 Azure AD Connect를 사용해야 합니다.

동기화를 활성화하기 전에:

* Active Directory 스키마를 2019로 업그레이드하지 않은 경우 원본 특성으로 사용할 온-프레미스 Active Directory 특성을 결정합니다. **단일 값 문자열** 형식이어야 합니다.
* Azure AD PowerShell을 사용하여 Azure AD의 기존 **동기화된 User 개체** 에 **preferredDataLocation** 특성을 미리 구성한 경우, 특성 값을 온-프레미스 Active Directory의 해당 **User** 개체로 백포팅해야 합니다.

    > [!IMPORTANT]
    > 이 값을 백포팅하지 않으면 Azure AD Connect는 **preferredDataLocation** 특성에 대한 동기화가 활성화된 경우 Azure AD의 기존 속성 값을 제거합니다.

* 이제 두 개 이상의 온-프레미스 Active Directory User 개체에 원본 특성을 구성합니다. 이것을 나중에 확인하는 데 사용할 수 있습니다.

다음 섹션에서는 **preferredDataLocation** 특성의 동기화를 사용하도록 설정하는 단계를 제공합니다.

> [!NOTE]
> 이 단계는 단일 포리스트 토폴로지를 사용하고 사용자 지정 동기화 규칙이 없는 Azure AD 배포와 관련됩니다. 다중 포리스트 토폴로지, 사용자 지정 동기화 규칙이 구성되어 있거나 스테이징 서버가 있으면 이에 따라 단계를 조정해야 합니다.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1단계: 동기화 스케줄러 비활성화 및 진행 중인 동기화가 없는지 확인
의도치 않은 변경 내용을 Azure AD로 내보내지 않도록 하려면 동기화 규칙을 업데이트하는 동안 동기화가 수행되지 않도록 합니다. 기본 제공 동기화 스케줄러를 비활성화하려면

1. Azure AD Connect 서버에서 PowerShell 세션을 시작합니다.
2. `Set-ADSyncScheduler -SyncCycleEnabled $false` cmdlet을 실행하여 예약된 동기화를 사용하지 않게 설정합니다.
3. **시작** > **동기화 서비스** 로 이동하여 **Synchronization Service Manager** 를 시작합니다.
4. **작업** 탭을 선택하고 상태가 *진행 중* 인 작업이 없는지 확인합니다.

![Synchronization Service Manager 스크린샷](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>2단계: Active Directory에 대한 스키마 새로 고침
Active Directory 스키마를 2019로 업데이트하고 스키마 확장 전에 Connect를 설치한 경우 Connect 스키마 캐시에 업데이트된 스키마가 없습니다. 그런 다음, 마법사에서 스키마를 새로 고쳐 UI에 표시해야 합니다.

1. 바탕 화면에서 Azure AD Connect 마법사를 시작합니다.
2. **디렉터리 스키마 새로 고침** 옵션을 선택하고 **다음을** 클릭합니다.
3. Azure AD 자격 증명을 입력하고 **다음** 을 클릭합니다.
4. **디렉터리 스키마 새로 고침** 페이지에서 모든 포리스트가 선택되어 있는지 확인하고 **다음** 을 클릭합니다.
5. 완료되면 마법사를 닫습니다.

![연결 마법사의 디렉터리 스키마 새로 고침 스크린샷](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>3단계: 온-프레미스 Active Directory Connector 스키마에 원본 특성 추가
**이 단계는 Connect 버전 1.3.21 이하를 실행하는 경우에만 필요합니다. 1.4.18 이상인 경우 5단계로 건너뜁니다.**  
모든 Azure AD 특성을 온-프레미스 Active Directory 커넥터 공간으로 가져오지는 않습니다. 기본적으로 동기화되지 않은 특성을 사용하도록 선택한 경우 해당 특성을 가져와야 합니다. 가져온 특성 목록에 원본 특성을 추가하려면 다음을 수행합니다.

1. Synchronization Service Manager에 있는 **커넥터** 탭을 선택합니다.
2. 온-프레미스 Active Directory Connector를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
3. 팝업 대화 상자에서 **특성 선택** 탭으로 이동합니다.
4. 특성 목록에서 사용하도록 선택한 원본 특성이 선택되어 있는지 확인합니다. 특성이 표시되지 않으면 **모두 표시** 확인란을 선택합니다.
5. 저장하려면 **확인** 을 선택합니다.

!["특성" 목록이 강조 표시된 Synchronization Service Manager 및 속성 대화 상자를 보여주는 스크린샷.](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>4단계: Azure AD Connector 스키마에 **preferredDataLocation** 추가
**이 단계는 Connect 버전 1.3.21 이하를 실행하는 경우에만 필요합니다. 1.4.18 이상인 경우 5단계로 건너뜁니다.**  
기본적으로 **preferredDataLocation** 특성은 Azure AD Connector 공간으로 가져오지 않습니다. 가져온 속성 목록에 추가하려면 다음을 수행합니다.

1. Synchronization Service Manager에 있는 **커넥터** 탭을 선택합니다.
2. Azure AD Connector를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
3. 팝업 대화 상자에서 **특성 선택** 탭으로 이동합니다.
4. 목록에서 **preferredDataLocation** 특성을 선택합니다.
5. 저장하려면 **확인** 을 선택합니다.

![Synchronization Service Manager 및 속성 대화 상자의 스크린샷](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>5단계: 인바운드 동기화 규칙 만들기
인바운드 동기화 규칙은 온-프레미스 Active Directory의 원본 특성에서 메타버스로 특성 값이 흐르도록 허용합니다.

1. **시작** > **동기화 규칙 편집기** 로 이동하여 **동기화 규칙 편집기** 를 시작합니다.
2. **방향** 검색 필터를 **인바운드** 로 설정합니다.
3. 새 인바운드 규칙을 만들려면 **새 규칙 추가** 를 선택합니다.
4. **설명 탭** 아래에서 다음 구성을 제공합니다.

    | attribute | 값 | 세부 정보 |
    | --- | --- | --- |
    | 속성 | *이름 제공* | 예: "AD - User preferredDataLocation에서 인바운드" |
    | 설명 | *사용자 지정 설명 제공* |  |
    | 연결된 시스템 | *온-프레미스 Active Directory Connector 선택* |  |
    | 연결된 시스템 개체 유형 | **사용자** |  |
    | 메타버스 개체 유형 | **Person** |  |
    | 링크 형식 | **Join** |  |
    | 우선 순위 | *1-99 사이의 숫자 선택* | 1-99는 사용자 지정 동기화 규칙을 위해 예약되어 있습니다. 다른 동기화 규칙에서 사용하는 값은 선택하지 않습니다. |

5. 모든 개체를 포함하려면 **범위 지정 필터** 를 비워 둡니다. Azure AD Connect 배포에 따라 범위 지정 필터를 조정해야 할 수도 있습니다.
6. **변환 탭** 으로 이동하여 다음 변환 규칙을 구현합니다.

    | 흐름 형식 | 대상 특성 | 원본 | 한 번 적용 | 병합 종류 |
    | --- | --- | --- | --- | --- |
    |직접 | preferredDataLocation | 원본 특성 선택 | 선택 취소됨 | 업데이트 |

7. 인바운드 규칙을 만들려면 **추가** 를 선택합니다.

![인바운드 동기화 규칙 만들기 스크린샷](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>6단계: 아웃바운드 동기화 규칙 만들기
아웃바운드 동기화 규칙은 메타버스에서 Azure AD의 **preferredDataLocation** 특성으로 특성 값이 흐르도록 허용합니다.

1. **동기화 규칙 편집기** 로 이동합니다.
2. **방향** 검색 필터를 **아웃바운드** 로 설정합니다.
3. **새 규칙 추가** 를 선택합니다.
4. **설명 탭** 아래에서 다음 구성을 제공합니다.

    | attribute | 값 | 세부 정보 |
    | ----- | ------ | --- |
    | 속성 | *이름 제공* | 예: "Azure AD - User preferredDataLocation로 아웃바운드" |
    | Description | *설명 제공* ||
    | 연결된 시스템 | *Azure AD 커넥터에 선택* ||
    | 연결된 시스템 개체 유형 | **사용자** ||
    | 메타버스 개체 유형 | **Person** ||
    | 링크 형식 | **Join** ||
    | 우선 순위 | *1-99 사이의 숫자 선택* | 1-99는 사용자 지정 동기화 규칙을 위해 예약되어 있습니다. 다른 동기화 규칙에서 사용하는 값은 선택하지 않습니다. |

5. **범위 지정 필터** 탭으로 이동하여 다음 두 절이 있는 단일 범위 지정 필터 그룹을 추가합니다.

    | attribute | 연산자 | 값 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | 사용자 |
    | cloudMastered | NOTEQUAL | True |

    범위 지정 필터는 이 아웃바운드 동기화 규칙이 적용되는 Azure AD 개체를 결정합니다. 이 예에서는 "Out to Azure AD - User Identity" OOB(out-of-box) 동기화 규칙과 동일한 범위 지정 필터를 사용합니다. 온-프레미스 Active Directory와 동기화되지 않은 **User** 개체에 동기화 규칙이 적용되지 않도록 합니다. Azure AD Connect 배포에 따라 범위 지정 필터를 조정해야 할 수도 있습니다.

6. **변환** 탭으로 이동하여 다음 변환 규칙을 구현합니다.

    | 흐름 형식 | 대상 특성 | 원본 | 한 번 적용 | 병합 종류 |
    | --- | --- | --- | --- | --- |
    | 직접 | preferredDataLocation | preferredDataLocation | 선택 취소됨 | 업데이트 |

7. **추가** 를 클릭하여 아웃바운드 규칙을 만듭니다.

![아웃바운드 동기화 규칙 만들기 스크린샷](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>7단계: 전체 동기화 주기 실행
일반적으로 전체 동기화 주기가 필요합니다. 이는 Active Directory 및 Azure AD Connector 스키마에 새 특성을 추가하고 사용자 지정 동기화 규칙을 도입했기 때문입니다. Azure AD로 내보내기 전에 변경 내용을 확인합니다. 다음 단계를 사용하여 전체 동기화 주기를 구성하는 단계를 수동으로 실행하는 동안 변경 내용을 확인할 수 있습니다.

1. 온-프레미스 Active Directory Connector에서 **전체 가져 오기** 실행:

   1. Synchronization Service Manager에 있는 **커넥터** 탭으로 이동합니다.
   2. **온-프레미스 Active Directory Connector** 를 마우스 오른쪽 단추로 클릭하고 **실행** 을 선택합니다.
   3. 대화 상자에서 **전체 가져오기** 를 선택하고 **확인** 을 선택합니다.
   4. 작업이 완료될 때까지 기다립니다.

      > [!NOTE]
      > 원본 특성이 가져온 특성 목록에 이미 포함되어 있으면 온-프레미스 Active Directory Connector에서 전체 가져오기를 건너뛸 수 있습니다. 다시 말해, 이 문서 앞쪽의 2단계를 진행하는 동안 변경할 필요가 없습니다.

2. Azure AD 커넥터에 **전체 가져오기** 실행:

   1. **Azure AD Connector** 를 마우스 오른쪽 단추로 클릭하고 **실행** 을 선택합니다.
   2. 대화 상자에서 **전체 가져오기** 를 선택하고 **확인** 을 선택합니다.
   3. 작업이 완료될 때까지 기다립니다.

3. 기존 **User** 개체에 대한 동기화 규칙 변경 내용을 확인합니다.

   온-프레미스 Active Directory의 원본 특성과 Azure AD의 **preferredDataLocation** 을 각각의 커넥터 공간으로 가져왔습니다. 전체 동기화 단계를 진행하기 전에 온-프레미스 Active Directory Connector 공간에서 기존 **User** 개체에 대한 미리 보기를 수행합니다. 선택한 개체에 원본 특성이 채워져야 합니다. **preferredDataLocation** 이 메타버스에 채워진 성공적인 미리 보기는 동기화 규칙을 올바르게 구성했음을 나타내는 좋은 지표입니다. 미리 보기를 수행하는 방법에 대한 자세한 내용은 [변경 확인](how-to-connect-sync-change-the-configuration.md#verify-the-change)을 참조하세요.

4. 온-프레미스 Active Directory Connector에서 **전체 동기화** 실행:

   1. **온-프레미스 Active Directory Connector** 를 마우스 오른쪽 단추로 클릭하고 **실행** 을 선택합니다.
   2. 대화 상자에서 **전체 동기화** 를 선택하고 **확인** 을 선택합니다.
   3. 작업이 완료될 때까지 기다립니다.

5. Azure AD로의 **보류 중인 내보내기** 를 확인합니다.

   1. **Azure AD 커넥터** 를 마우스 오른쪽 단추로 클릭하고 **커넥터 공간 검색** 을 선택합니다.
   2. **커넥터 공간 검색** 대화 상자에서:

        a. **범위** 를 **보류 중인 내보내기** 로 설정합니다.<br>
        b. **추가, 수정 및 삭제** 를 포함한 세 개의 확인란을 모두 선택합니다.<br>
        다. 내보낼 변경 내용이 있는 개체의 목록을 보려면 **검색** 을 선택합니다. 지정된 개체에 대한 변경 내용을 검사하려면 해당 개체를 두 번 클릭합니다.<br>
        d. 필요한 변경 내용인지 확인합니다.

6. **Azure AD Connector** 에서 **내보내기** 실행

   1. **Azure AD Connector** 를 마우스 오른쪽 단추로 클릭하고 **실행** 을 선택합니다.
   2. **커넥터 실행** 대화 상자에서 **내보내기** 를 선택하고 **확인** 을 선택합니다.
   3. 작업이 완료될 때까지 기다립니다.

> [!NOTE]
> Azure AD 커넥터에 전체 동기화 단계가 포함되어 있지 않거나 Active Directory Connector에 내보내기 단계가 포함되어 있지 않을 수도 있습니다. 특성 값이 온-프레미스 Active Directory에서 Azure AD로만 흐르기 때문에 이러한 단계가 필요하지 않습니다.

## <a name="step-8-re-enable-sync-scheduler"></a>8단계: 동기화 스케줄러를 다시 사용하도록 설정
기본 제공 동기화 스케줄러를 다시 사용하도록 설정하려면

1. PowerShell 세션을 시작합니다.
2. `Set-ADSyncScheduler -SyncCycleEnabled $true` cmdlet을 실행하여 예약된 동기화를 다시 사용하도록 설정합니다.

## <a name="step-9-verify-the-result"></a>9단계: 결과 확인
구성을 확인하고 사용자를 위해 적용해야 합니다.

1. 사용자의 선택한 특성에 해당 지역을 추가합니다. 사용 가능한 지역 목록은 이 표에서 확인할 수 있습니다.  
![사용자에게 추가된 AD 특성 스크린샷](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. 특성을 Azure AD와 동기화될 때까지 기다립니다.
3. Exchange Online PowerShell을 사용하여 사서함 지역이 올바르게 설정되어 있는지 확인합니다.  
![Exchange Online PowerShell 스크린샷](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
테넌트가 이 기능을 사용할 수 있는 것으로 표시될 경우, 사서함은 올바른 지역으로 이동됩니다. 올바른 지역으로 이동되는지는 사서함이 있는 서버 이름을 확인하여 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

Microsoft 365의 다중 지역에 대한 자세한 정보:

* [Ignite의 다중 지역 세션](https://aka.ms/MultiGeoIgnite)
* [OneDrive의 다중 지역](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)
* [SharePoint Online의 다중 지역](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)

동기화 엔진의 구성 모델에 대한 자세한 정보:

* [선언적 프로비전 이해](concept-azure-ad-connect-sync-declarative-provisioning.md)에서 구성 모델에 대해 자세히 알아봅니다.
* [선언적 프로비전 식 이해](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)에서 식 언어에 대해 자세히 알아봅니다.

개요 항목:

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](how-to-connect-sync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)