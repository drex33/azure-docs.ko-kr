---
title: 뉴질랜드 ISM 제한 청사진 샘플
description: 뉴질랜드 ISM 제한 청사진 샘플에 대한 개요입니다. 이 청사진 샘플은 고객이 특정 컨트롤을 평가하는 데 도움이 됩니다.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: f64d14bc7fd2a3dd133f1f3fd4da6d5de39f9f01
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659724"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>뉴질랜드 ISM 제한 청사진 샘플

뉴질랜드 ISM 제한 청사진 샘플은 특정 [뉴질랜드 정보 보안 설명서](https://www.nzism.gcsb.govt.nz/) 제어를 평가하는 데 도움이 되는 [Azure Policy](../../policy/overview.md)를 사용하여 거버넌스 가드 레일을 제공합니다. 이 청사진은 고객이 뉴질랜드 ISM 제한 제어를 구현해야 하는 Azure 배포 아키텍처에 대한 핵심 정책 세트를 배포하는 데 도움이 됩니다.

## <a name="control-mapping"></a>컨트롤 매핑

[Azure Policy 제어 매핑](../../policy/samples/new-zealand-ism.md)은 이 청사진에 포함된 정책 정의 및 이러한 정책 정의가 뉴질랜드 정보 보안 설명서의 **제어** 에 매핑되는 방법에 대한 세부 정보를 제공합니다. 아키텍처에 할당된 경우 리소스는 할당된 정책 정의를 준수하지 않는지 Azure Policy에서 평가됩니다. 자세한 내용은 [Azure Policy](../../policy/overview.md)를 참조하세요.

## <a name="deploy"></a>배포

뉴질랜드 ISM 제한 청사진 샘플을 배포하려면 다음 단계를 수행해야 합니다.

> [!div class="checklist"]
> - 샘플에서 새 청사진 만들기
> - 샘플 사본을 **게시됨** 으로 표시
> - 기존 구독에 청사진 사본 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

### <a name="create-blueprint-from-sample"></a>샘플에서 청사진 만들기

먼저 샘플을 스타터로 사용하여 환경 내에 새 청사진을 만들어 청사진 샘플을 구현합니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. **시작** 페이지 왼쪽에서 _청사진 만들기_ 아래의 **만들기** 단추를 선택합니다.

1. _다른 샘플_ 아래에서 **뉴질랜드 ISM 제한** 청사진 샘플을 찾고, **이 샘플 사용** 을 선택합니다.

1. 청사진 샘플의 _기본 사항_ 을 입력합니다.

   - **청사진 이름**: 뉴질랜드 ISM 제한 청사진 샘플의 복사본 이름을 입력합니다.
   - **정의 위치**: 줄임표를 사용하고 샘플 사본을 저장할 관리 그룹을 선택합니다.

1. 페이지 위쪽의 _아티팩트_ 탭 또는 페이지 아래쪽의 **다음: 아티팩트** 를 선택합니다.

1. 청사진 샘플에 포함된 아티팩트 목록을 검토합니다. 아티팩트 대부분에 매개 변수는 나중에 정의할 것입니다. 청사진 샘플 검토가 끝나면 **초안 저장** 을 선택합니다.

### <a name="publish-the-sample-copy"></a>샘플 사본 게시

이제 작업 환경에 청사진 샘플 사본이 만들어졌습니다. 이 청사진 정의는 **초안** 모드로 만들어졌으며 먼저 **게시** 해야만 할당하고 배포할 수 있습니다. 청사진 샘플의 복사본을 환경 및 요구 사항에 맞게 사용자 지정할 수 있지만, 이러한 수정이 뉴질랜드 ISM 제한 제어와 일치하지 않을 수 있습니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 페이지 맨 위에서 **청사진 게시** 를 선택합니다. 오른쪽의 새 페이지에서 청사진 샘플 사본의 **버전** 을 지정합니다. 이 속성은 나중에 수정할 경우에 유용합니다. "뉴질랜드 ISM 제한 청사진 샘플에서 게시된 첫 번째 버전"과 같은 **변경 메모** 를 제공합니다. 그런 후 페이지 아래쪽의 **게시** 를 선택합니다.

### <a name="assign-the-sample-copy"></a>샘플 사본 할당

청사진 샘플 사본이 **게시** 되면 저장된 관리 그룹 내의 구독에 할당할 수 있습니다. 이 단계에서는 청사진 샘플 사본의 배포를 고유하게 만들기 위해 매개 변수가 제공됩니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 청사진 정의 페이지 위쪽에서 **청사진 할당** 을 선택합니다.

1. 청사진 할당의 매개 변수 값을 다음과 같이 지정합니다.

   - 기본 사항

     - **구독**: 청사진 샘플 사본을 저장한 관리 그룹에 있는 하나 이상의 구독을 선택합니다. 둘 이상의 구독을 선택하면 입력한 매개 변수를 사용하여 각 구독의 할당이 생성됩니다.
     - **할당 이름**: 청사진의 이름에 따라 이 이름이 미리 채워집니다.
       필요에 따라 변경하거나 그대로 둡니다.
     - **위치**: 관리 ID를 만들 지역을 선택합니다. Azure Blueprints는 이 관리 ID를 사용하여 할당된 청사진의 모든 아티팩트를 배포합니다. 자세히 알아보려면 [Azure 리소스의 관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
     - **청사진 정의 버전**: 청사진 샘플 사본의 **게시됨** 버전을 선택합니다.

   - 할당 잠금

     환경에 맞는 청사진 잠금 설정을 선택합니다. 자세한 내용은 [청사진 리소스 잠금](../concepts/resource-locking.md)을 참조하세요.

   - 관리 ID

     기본값 _시스템이 할당한_ 관리 ID 옵션을 유지합니다.

   - 아티팩트 매개 변수

     이 섹션에 정의된 매개 변수는 정의된 아티팩트에 적용됩니다. 이러한 매개 변수는 청사진 할당 중에 정의되므로 [동적 매개 변수](../concepts/parameters.md#dynamic-parameters)입니다. 전체 목록 또는 아티팩트 매개 변수 및 해당 설명은 [아티팩트 매개 변수 테이블](#artifact-parameters-table)을 참조하세요.

1. 모든 매개 변수를 입력한 후에는 페이지 아래쪽에서 **할당** 을 선택합니다. 청사진 할당이 생성되고 아티팩트 배포가 시작됩니다. 배포에는 약 1 시간이 걸립니다. 배포의 상태를 확인하려면 청사진 할당을 엽니다.

> [!WARNING]
> Azure Blueprints 서비스 및 기본 제공 청사진 샘플은 **무료** 입니다. Azure 리소스는 [제품별로 가격이 책정](https://azure.microsoft.com/pricing/)됩니다. 이 청사진 샘플에서 배포되는 리소스를 실행하는 비용을 추정하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

### <a name="artifact-parameters-table"></a>아티팩트 매개 변수 테이블

다음 테이블은 청사진 아티팩트 매개 변수의 목록을 제공합니다.

|아티팩트 이름|아티팩트 형식|매개 변수 이름|Description|
|-|-|-|-|
|뉴질랜드 ISM 제한됨|정책 할당|Windows VM 관리자 그룹에 포함해야 하는 사용자 목록|관리자 로컬 그룹에 포함해야 하는 사용자의 세미콜론으로 구분된 목록입니다. 예: Administrator; myUser1; myUser2|
|뉴질랜드 ISM 제한됨|정책 할당|Windows VM Administrators 그룹에서 제외되어야 하는 사용자 목록|관리자 로컬 그룹에서 제외해야 하는 사용자의 세미콜론으로 구분된 목록입니다. 예: Administrator; myUser1; myUser2|
|뉴질랜드 ISM 제한됨|정책 할당|Windows VM 관리자 그룹에서만 포함해야 하는 사용자 목록|필요한 모든 Administrators 로컬 그룹의 멤버를 세미콜론으로 구분한 목록입니다(예: Administrator; myUser1; myUser2).|
|뉴질랜드 ISM 제한됨|정책 할당|VM 에이전트 보고를 위한 Log Analytics 작업 영역 ID|VM 에이전트가 보고해야 하는 Log Analytics 작업 영역의 ID(GUID)|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Azure Front Door Service에 대해 WAF(Web Application Firewall)를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 적응형 네트워크 강화 권장 사항은 인터넷에 연결된 가상 머신에 적용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 구독에 둘 이상의 소유자를 할당해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 가상 머신에서 디스크 암호화를 적용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 함수 앱에 대해 원격 디버깅을 해제해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: WAF(Web Application Firewall)는 Application Gateway에 대해 지정된 모드를 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|Application Gateway에 대한 WAF 모드 요구 사항|Application Gateway 서비스에서 방지 또는 검색 모드를 사용하도록 설정해야 합니다.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|선택 사항: 배포 - Windows 가상 머신에서 사용하도록 종속성 에이전트 구성 정책에 대한 갤러리의 이미지에 추가되는 범위에 추가할 Windows OS를 지원하는 사용자 지정 VM 이미지 목록|게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)을 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Azure Cache for Redis에 대해 보안 연결만 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|Administrators 그룹에 지정한 멤버가 없는 Windows 머신 감사 정책을 평가할 때 Arc 연결 서버 포함|'true'를 선택하면 Arc 연결 머신마다 매월 청구되는 것에 동의하게 됩니다.|
|뉴질랜드 ISM 제한됨|정책 할당|선택 사항: [미리 보기]: Log Analytics 에이전트는 나열된 가상 머신 이미지에 대해 사용하도록 설정해야 함 정책에 대한 갤러리의 이미지에 추가되는 범위에 추가할 Windows OS를 지원하는 사용자 지정 VM 이미지 목록|게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)을 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|선택 사항: [미리 보기]: Log Analytics 에이전트는 나열된 가상 머신 이미지에 대해 사용하도록 설정해야 함 정책에 대한 갤러리의 이미지에 추가되는 범위에 추가할 Linux OS를 지원하는 사용자 지정 VM 이미지 목록|게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)을 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 스토리지 계정은 네트워크 액세스를 제한해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|선택 사항: 배포 - Windows 가상 머신 확장 집합에서 사용하도록 종속성 에이전트 구성 정책에 대한 갤러리의 이미지에 추가되는 범위에 추가할 Windows OS를 지원하는 사용자 지정 VM 이미지 목록|게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)을 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|Administrators 그룹에 추가 계정이 있는 Windows 머신 감사 정책을 평가할 때 Arc 연결 서버 포함|'true'를 선택하면 Arc 연결 머신마다 매월 청구되는 것에 동의하게 됩니다.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: WAF(Web Application Firewall)는 Azure Front Door Service에 대해 지정된 모드를 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|Azure Front Door Service에 대한 WAF 모드 요구 사항|Azure Front Door Service에서 방지 또는 검색 모드를 사용하도록 설정해야 합니다.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 머신에서 안전한 애플리케이션을 정의하기 위한 적응형 애플리케이션 제어를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 구독에 최대 3명의 소유자를 지정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: [미리 보기]: 스토리지 계정 퍼블릭 액세스를 허용하지 않아야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 가상 머신에서 취약성 평가 솔루션을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Application Gateway에 대해 WAF(Web Application Firewall)를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|보안 통신 프로토콜을 사용하지 않는 Windows 웹 서버 감사 정책을 평가할 때 Arc 연결 서버 포함|'true'를 선택하면 Arc 연결 머신마다 매월 청구되는 것에 동의하게 됩니다.|
|뉴질랜드 ISM 제한됨|정책 할당|Windows 웹 서버의 최소 TLS 버전|더 낮은 TLS 버전을 사용하는 Windows 웹 서버는 비준수로 평가됩니다.|
|뉴질랜드 ISM 제한됨|정책 할당|선택 사항: Log Analytics 에이전트는 가상 머신 확장 집합에서 나열된 가상 머신 이미지에 대해 사용하도록 설정해야 함 정책에 대한 갤러리의 이미지에 추가되는 범위에 추가할 Linux OS를 지원하는 사용자 지정 VM 이미지 목록|게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)을 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|선택 사항: Log Analytics 에이전트는 가상 머신 확장 집합에서 나열된 가상 머신 이미지에 대해 사용하도록 설정해야 함 정책에 대한 갤러리의 이미지에 추가되는 범위에 추가할 Windows OS를 지원하는 사용자 지정 VM 이미지 목록|게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../../policy/concepts/guest-configuration.md)을 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|Administrators 그룹에 지정한 멤버가 있는 Windows 머신 감사 정책을 평가할 때 Arc 연결 서버 포함|'true'를 선택하면 Arc 연결 머신마다 매월 청구되는 것에 동의하게 됩니다.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 함수 앱에 HTTPS를 통해서만 액세스 가능|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Azure 구독에는 활동 로그에 대한 로그 프로필이 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|진단 로그를 사용해야 하는 리소스 종류 목록||
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 시스템 업데이트를 머신에 설치해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: API 앱에서 최신 TLS 버전을 사용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Microsoft IaaSAntimalware 확장을 Windows Server에 배포해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Azure DDoS Protection 표준을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: SQL Managed Instance에서 Advanced Data Security를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Azure Security Center에서 누락된 Endpoint Protection 모니터링|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 활동 로그는 1년 이상 보존되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Windows Defender Exploit Guard를 사용하도록 설정되지 않은 Windows 머신 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|Windows Defender Exploit Guard를 사용하도록 설정되지 않은 Windows 머신 감사 정책을 평가할 때 Arc 연결 서버 포함|'true'를 선택하면 Arc 연결 머신마다 매월 청구되는 것에 동의하게 됩니다.|
|뉴질랜드 ISM 제한됨|정책 할당|Windows Defender Exploit Guard를 사용할 수 없는 Windows 머신에 대해 보고할 규정 준수 상태|Windows Defender Exploit Guard는 Windows 10/Windows Server 업데이트 1709부터만 사용할 수 있습니다. 이 값을 '비준수'로 설정하면 Windows Defender Exploit Guard를 사용할 수 없는 이전 버전(예: Windows Server 2012 R2)의 머신이 비준수로 표시됩니다. 이 값을 '준수'로 설정하면 이러한 머신이 준수로 표시됩니다.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 머신 보안 구성의 취약성을 수정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 컨테이너 보안 구성의 취약성을 수정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: API Apps에 대해 원격 디버깅을 해제해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 암호가 없는 계정에서 원격 연결을 허용하는 Linux 머신 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 암호가 없는 계정에서 원격 연결을 허용하는 Linux 머신 감사 정책을 평가할 때 Arc 연결 서버 포함|'true'를 선택하면 Arc 연결 머신마다 매월 청구되는 것에 동의하게 됩니다.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 웹앱에서 최신 TLS 버전을 사용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: Windows 머신은 '보안 설정 - 계정 정책'에 대한 요구 사항을 충족해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|Windows VM 로컬 계정에 암호 기록 적용|암호 재사용 제한, 즉 암호를 반복하기 전에 사용자 계정에 대한 새 암호를 만들어야 하는 횟수를 지정합니다.|
|뉴질랜드 ISM 제한됨|정책 할당|Windows 머신은 '보안 설정 - 계정 정책'에 대한 요구 사항을 충족해야 함 정책을 평가할 때 Arc 연결 서버 포함|'true'를 선택하면 Arc 연결 머신마다 매월 청구되는 것에 동의하게 됩니다.|
|뉴질랜드 ISM 제한됨|정책 할당|Windows VM 로컬 계정의 최대 암호 사용 기간|사용자 계정 암호를 변경해야 할 때까지 경과할 수 있는 최대 일 수를 지정합니다. 값 형식은 쉼표로 구분된 두 개의 정수입니다(포함 범위를 나타냄).|
|뉴질랜드 ISM 제한됨|정책 할당|Windows VM 로컬 계정의 최소 암호 사용 기간|사용자 계정 암호를 변경할 수 있을 때까지 경과해야 하는 최소 일 수를 지정합니다.|
|뉴질랜드 ISM 제한됨|정책 할당|Windows VM 로컬 계정의 최소 암호 길이|사용자 계정 암호에 포함될 수 있는 최소 문자 수를 지정합니다.|
|뉴질랜드 ISM 제한됨|정책 할당|암호는 Windows VM 로컬 계정의 복잡성 요구 사항을 충족해야 함|사용자 계정 암호가 복잡해야 하는지 여부를 지정합니다. 필요한 경우 복잡한 암호에는 사용자의 계정 이름 또는 전체 이름의 일부가 포함되지 않아야 합니다. 6자 이상이고, 대문자, 소문자, 숫자 및 알파벳이 아닌 문자를 혼합하여 사용해야 합니다.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 네트워크 보안 그룹을 사용하여 인터넷에 연결되지 않은 가상 머신을 보호해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 암호가 없는 계정이 있는 Linux 머신 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|암호가 없는 계정이 있는 Linux 머신 감사 정책을 평가할 때 Arc 연결 서버 포함|'true'를 선택하면 Arc 연결 머신마다 매월 청구되는 것에 동의하게 됩니다.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: 함수 앱에서 최신 TLS 버전을 사용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: [미리 보기]: 모든 인터넷 트래픽은 배포된 Azure Firewall을 통해 라우팅되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|뉴질랜드 ISM 제한됨|정책 할당|정책 효과: SQL 데이터베이스의 취약성을 수정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|

## <a name="next-steps"></a>다음 단계

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
