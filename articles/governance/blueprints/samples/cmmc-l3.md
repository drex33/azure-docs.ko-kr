---
title: CMMC 수준 3 청사진 샘플
description: CMMC 수준 3 청사진 샘플에 대한 개요입니다. 이 청사진 샘플은 고객이 특정 컨트롤을 평가하는 데 도움이 됩니다.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 2925acf75ada0bd950ca52e3fe63b9c97b5e34ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632332"
---
# <a name="cmmc-level-3-blueprint-sample"></a>CMMC 수준 3 청사진 샘플

CMMC 수준 3 청사진 샘플 청사진 샘플은 특정 [CMMC(Cybersecurity Maturity Model Certification) 프레임워크](https://www.acq.osd.mil/cmmc/index.html) 컨트롤을 평가하는 데 도움이 되는 [Azure Policy](../../policy/overview.md)를 사용하여 거버넌스 보호책을 제공합니다. 이 청사진은 고객이 CMMC 수준 3 제어를 구현해야 하는 Azure 배포 아키텍처에 대한 핵심 정책 세트를 배포하는 데 도움이 됩니다.

## <a name="control-mapping"></a>컨트롤 매핑

[Azure Policy 제어 매핑](../../policy/samples/cmmc-l3.md)은 이 청사진에 포함된 정책 정의 및 이러한 정책 정의가 CMMC 프레임워크의 **제어** 에 매핑되는 방법에 대한 세부 정보를 제공합니다. 아키텍처에 할당된 경우 리소스는 할당된 정책 정의를 준수하지 않는지 Azure Policy에서 평가됩니다. 자세한 내용은 [Azure Policy](../../policy/overview.md)를 참조하세요.

## <a name="deploy"></a>배포

Azure Blueprints CMMC 수준 3 청사진 샘플을 배포하려면 다음 단계를 수행해야 합니다.

> [!div class="checklist"]
> - 샘플에서 새 청사진 만들기
> - 샘플 사본을 **게시됨** 으로 표시
> - 기존 구독에 청사진 사본 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

### <a name="create-blueprint-from-sample"></a>샘플에서 청사진 만들기

먼저 샘플을 스타터로 사용하여 환경 내에 새 청사진을 만들어 청사진 샘플을 구현합니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. **시작** 페이지 왼쪽에서 _청사진 만들기_ 아래의 **만들기** 단추를 선택합니다.

1. _다른 샘플_ 아래에서 **CMMC 수준 3** 청사진 샘플을 찾고, **이 샘플 사용** 을 선택합니다.

1. 청사진 샘플의 _기본 사항_ 을 입력합니다.

   - **청사진 이름**: CMMC 수준 3 청사진 샘플 복사본에 대한 이름을 입력합니다.
   - **정의 위치**: 줄임표를 사용하고 샘플 사본을 저장할 관리 그룹을 선택합니다.

1. 페이지 위쪽의 _아티팩트_ 탭 또는 페이지 아래쪽의 **다음: 아티팩트** 를 선택합니다.

1. 청사진 샘플에 포함된 아티팩트 목록을 검토합니다. 아티팩트 대부분에 매개 변수는 나중에 정의할 것입니다. 청사진 샘플 검토가 끝나면 **초안 저장** 을 선택합니다.

### <a name="publish-the-sample-copy"></a>샘플 사본 게시

이제 작업 환경에 청사진 샘플 사본이 만들어졌습니다. 이 청사진 정의는 **초안** 모드로 만들어졌으며 먼저 **게시** 해야만 할당하고 배포할 수 있습니다. 청사진 샘플의 복사본을 환경 및 요구 사항에 맞게 사용자 지정할 수 있지만, 이러한 수정이 CMMC 수준 3 제어와 일치하지 않을 수 있습니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 페이지 맨 위에서 **청사진 게시** 를 선택합니다. 오른쪽의 새 페이지에서 청사진 샘플 사본의 **버전** 을 지정합니다. 이 속성은 나중에 수정할 경우에 유용합니다. "CMMC 수준 3 청사진 샘플에서 게시된 첫 번째 버전"과 같은 **변경 메모** 를 제공합니다. 그런 후 페이지 아래쪽의 **게시** 를 선택합니다.

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
|CMMC Level 3|정책 할당|게스트 구성 정책을 평가할 때 Arc 연결된 서버 포함|‘true’를 선택하면 Arc 연결된 머신마다 매달 청구되는 것에 동의하게 됩니다. 자세한 내용은 https://aka.ms/policy-pricing 을 방문하세요.|
|CMMC Level 3|정책 할당|Windows VM Administrators 그룹에서 제외되어야 하는 사용자 목록|관리자 로컬 그룹에서 제외해야 하는 사용자의 세미콜론으로 구분된 목록입니다. 예: Administrator; myUser1; myUser2|
|CMMC Level 3|정책 할당|Windows VM 관리자 그룹에 포함해야 하는 사용자 목록|관리자 로컬 그룹에 포함해야 하는 사용자의 세미콜론으로 구분된 목록입니다. 예: Administrator; myUser1; myUser2|
|CMMC Level 3|정책 할당|VM 에이전트 보고를 위한 Log Analytics 작업 영역 ID|VM 에이전트가 보고해야 하는 Log Analytics 작업 영역의 ID(GUID)|
|CMMC Level 3|정책 할당|허용된 타원 곡선 이름|타원 곡선 암호화 인증서에 허용된 곡선 이름 목록입니다.|
|CMMC Level 3|정책 할당|허용된 키 유형|허용된 인증서 키 유형의 목록입니다.|
|CMMC Level 3|정책 할당|Kubernetes 클러스터 Pod에 대한 호스트 네트워크 사용 허용|Pod에서 호스트 네트워크를 사용할 수 있는 경우 이 값을 true로 설정하고, 그렇지 않은 경우 false로 설정합니다.|
|CMMC Level 3|정책 할당|인증 정책 변경 감사|인증 정책이 변경될 때 감사 이벤트를 생성할지 여부를 지정합니다. 이 설정은 사용자 계정 또는 그룹에 부여된 도메인 수준 및 포리스트 수준 신뢰 및 권한의 변경을 추적하는 데 유용합니다.|
|CMMC Level 3|정책 할당|권한 부여 정책 변경 감사|사용자 권한 정책의 사용자 권한 할당 및 제거, 보안 토큰 개체 권한 변경, 리소스 특성 변경 및 파일 시스템 개체에 대한 중앙 액세스 정책 변경에 대한 감사 이벤트를 생성하는지 여부를 지정합니다.|
|CMMC Level 3|정책 할당|정책 효과: Virtual Machines에 대해 Azure Backup을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Cognitive Services 계정은 네트워크 액세스를 제한해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL Managed Instance는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure API for FHIR은 CMK(고객 관리형 키)를 사용하여 미사용 데이터를 암호화해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Front Door Service에 대해 WAF(Web Application Firewall)를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Cognitive Services 계정에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용하지 않아야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 적응형 네트워크 강화 권장 사항은 인터넷에 연결된 가상 머신에 적용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 구독에 둘 이상의 소유자를 할당해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 가상 머신에서 디스크 암호화를 적용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 심각도가 높은 경고에 대해 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Key Vault는 제거 보호를 사용하도록 설정되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL Server는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 함수 앱에 대해 원격 디버깅을 해제해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Key Vault용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Database for MariaDB에서 지역 중복 백업을 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: CORS에서 모든 도메인이 API for FHIR에 액세스하도록 허용하지 않아야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Windows 머신은 '보안 옵션 - 네트워크 보안'에 대한 요구 사항을 충족해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: WAF(Web Application Firewall)는 Application Gateway에 대해 지정된 모드를 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 키에 만료 날짜가 설정되어 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Monitor 로그 프로필은 범주 '쓰기,' '삭제' 및 '작업'에 대한 로그를 수집해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: API 앱의 일부로 사용되는 경우 'PHP 버전'이 최신 상태인지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Key Vault는 일시 삭제를 사용하도록 설정되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Cache for Redis에 대해 보안 연결만 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Database for PostgreSQL 서버에 대해 인프라 암호화를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: App Service용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Windows 머신은 '시스템 감사 정책 - 정책 변경'에 대한 요구 사항을 충족해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Cognitive Services 계정은 데이터 암호화를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 인터넷에서 SSH 액세스를 차단해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 연결되지 않은 디스크는 암호화되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 스토리지용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 스토리지 계정은 네트워크 액세스를 제한해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: CORS에서 모든 리소스가 API 앱에 액세스하도록 허용하지 않아야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 스토리지 계정에 Advanced Threat Protection 배포|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 계정 변수 자동화는 암호화되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: IoT Hub의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Database for MySQL 서버에 대해 인프라 암호화를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 특정 보안 작업의 활동 로그 경고가 있어야 함(Microsoft.Security/securitySolutions/delete)|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Windows 머신은 '보안 옵션 - 네트워크 액세스'에 대한 요구 사항을 충족해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Monitor는 모든 지역의 활동 로그를 수집해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: WAF(Web Application Firewall)는 Azure Front Door Service에 대해 지정된 모드를 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 스토리지 계정에 인프라 암호화가 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 머신에서 안전한 애플리케이션을 정의하기 위한 적응형 애플리케이션 제어를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Windows 머신은 '보안 옵션 - 사용자 계정 컨트롤'에 대한 요구 사항을 충족해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 웹앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 서버용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 구독에 최대 3명의 소유자를 지정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 구독에 보안 문제에 대한 연락처 이메일 주소가 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 스토리지 계정 퍼블릭 액세스를 허용하지 않아야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 가상 머신에서 취약성 평가 솔루션을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Kubernetes용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Key Vault에서 방화벽을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Application Gateway에 대해 WAF(Web Application Firewall)를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 이전 24개 암호의 재사용을 허용하는 Windows 머신 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Container Registry를 CMK(고객 관리형 키)로 암호화해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: PostgreSQL 유연한 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Container Registry 이미지의 취약성을 수정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Service Fabric 클러스터에는 ClusterProtectionLevel 속성이 EncryptAndSign으로 설정되어 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 머신에서 SQL Server용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Cognitive Services 계정은 고객 관리형 키로 데이터 암호화를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 함수 앱에 HTTPS를 통해서만 액세스 가능|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 스토리지 계정은 고객 관리형 키를 암호화에 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 웹앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 함수 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 웹앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: API 앱의 일부로 사용되는 경우 'Python 버전'이 최신 상태인지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 키는 지정된 암호화 유형 RSA 또는 EC여야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure 구독에는 활동 로그에 대한 로그 프로필이 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Kubernetes Service 클러스터의 운영 체제와 데이터 디스크는 모두 고객 관리형 키로 암호화해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure SQL Database 서버용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Data Explorer 미사용 암호화에는 고객 관리형 키를 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: RSA 암호화를 사용하는 키에는 지정된 최소 키 크기가 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Kubernetes 클러스터 Pod는 승인된 호스트 네트워크와 포트 범위만 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 시스템 업데이트를 머신에 설치해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Windows 머신은 '시스템 감사 정책 - 권한 사용'에 대한 요구 사항을 충족해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Stream Analytics 작업은 고객 관리형 키를 사용하여 데이터를 암호화해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: API 앱의 일부로 사용되는 경우 'Java 버전'이 최신 상태인지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 웹앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: API 앱에서 최신 TLS 버전을 사용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: API 앱을 실행하는 데 사용되는 경우 'HTTP 버전'이 최신 상태인지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Microsoft IaaSAntimalware 확장을 Windows Server에 배포해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 함수 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 가상 머신에 연결된 네트워크 보안 그룹에서 모든 네트워크 포트가 제한되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Security Center 표준 가격 책정 계층을 선택해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 암호 최소 길이를 14자로 제한하지 않는 Windows 머신 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 사용자 지정 RBAC 규칙의 사용 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL Server에 대한 감사가 사용되도록 설정되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 가상 머신에 Log Analytics 에이전트를 설치해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL Managed Instance에서 Advanced Data Security를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Kubernetes 서비스에서 RBAC(역할 기반 액세스 제어)를 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 가상 머신에 게스트 구성 확장이 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Security Center에서 누락된 Endpoint Protection 모니터링|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 활동 로그는 1년 이상 보존되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: PostgreSQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Cosmos DB 계정에 대한 Advanced Threat Protection 배포|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: App Services의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.ClassicNetwork/networkSecurityGroups/delete)|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete)|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.Network/networkSecurityGroups/delete)|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.Network/networkSecurityGroups/securityRules/delete)|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.Sql/servers/firewallRules/delete)|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 네트워크 보안 그룹을 사용하여 인터넷에 연결되지 않은 가상 머신을 보호해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 암호 복잡성 설정을 사용하도록 설정하지 않는 Windows 머신 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 컨테이너 레지스트리용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Data Box 작업에서 디바이스의 미사용 데이터에 대해 이중 암호화를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 보호 서명을 자동으로 업데이트하려면 Azure용 Microsoft Antimalware를 구성해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 특정 정책 작업의 활동 로그 경고가 있어야 함(Microsoft.Authorization/policyAssignments/delete)|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: MySQL 유연한 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 스토리지 계정은 신뢰할 수 있는 Microsoft 서비스의 액세스를 허용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: RSA 암호화를 사용하는 인증서에는 지정된 최소 키 크기가 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Container Registry는 무제한 네트워크 액세스를 허용해서는 안 됨|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: PostgreSQL 데이터베이스 서버에 대해 SSL 강제 연결을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 시스템이 할당한 관리 ID를 사용하여 게스트 구성 확장을 Azure 가상 머신에 배포해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure SQL Database에 대해 장기 지역 중복 백업을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: MySQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows 머신 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Windows 머신은 '사용자 권한 할당'에 대한 요구 사항을 충족해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 머신 보안 구성의 취약성을 수정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 함수 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 인터넷에서 RDP 액세스를 차단해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: passwd 파일 권한이 0644로 설정되지 않은 Linux 머신 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 서브넷을 네트워크 보안 그룹과 연결해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: MySQL 데이터베이스 서버에 대해 SSL 강제 연결을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 컨테이너 보안 구성의 취약성을 수정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: API Apps에 대해 원격 디버깅을 해제해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 암호가 없는 계정에서 원격 연결을 허용하는 Linux 머신 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Data Explorer에서 이중 암호화를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Virtual Machine Scale Sets에 Log Analytics 에이전트를 설치해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 웹앱에서 최신 TLS 버전을 사용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Data Explorer에서 디스크 암호화를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 네트워크 보안 그룹을 사용하여 인터넷에 연결되지 않은 가상 머신을 보호해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 암호가 없는 계정이 있는 Linux 머신 감사|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Azure Synapse 작업 영역은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 함수 앱에서 최신 TLS 버전을 사용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Kubernetes 서비스를 취약하지 않은 Kubernetes 버전으로 업그레이드해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 모든 인터넷 트래픽은 배포된 Azure Firewall을 통해 라우팅되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: Linux 머신은 Azure 보안 기준에 대한 요구 사항을 충족해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: MariaDB 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: SQL 데이터베이스의 취약성을 수정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 효과: 타원 곡선 암호화를 사용하는 키에는 지정된 곡선 이름이 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요.|
|CMMC Level 3|정책 할당|정책 평가에서 제외된 네임스페이스: Kubernetes 클러스터 Pod는 승인된 호스트 네트워크와 포트 범위만 사용해야 함|정책 평가에서 제외할 Kubernetes 네임스페이스의 목록입니다.|
|CMMC Level 3|정책 할당|App Services용 최신 Java 버전|App Services에 대해 지원되는 최신 Java 버전|
|CMMC Level 3|정책 할당|App Services에 지원되는 Linux용 최신 Python 버전|App Services에 대해 지원되는 최신 Python 버전|
|CMMC Level 3|정책 할당|선택 사항: Log Analytics 에이전트 배포를 감사하는 경우 범위에 추가할 지원되는 Linux OS를 포함하는 VM 이미지 목록|예제 값: `/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage`|
|CMMC Level 3|정책 할당|선택 사항: Log Analytics 에이전트 배포를 감사하는 경우 범위에 추가할 지원되는 Windows OS를 포함하는 VM 이미지 목록|예제 값: `/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage`|
|CMMC Level 3|정책 할당|Network Watcher를 사용하도록 설정해야 하는 영역 목록|지역에서 Network Watcher를 사용할 수 없는 경우 감사합니다.|
|CMMC Level 3|정책 할당|진단 로그를 사용해야 하는 리소스 종류 목록||
|CMMC Level 3|정책 할당|Pod가 호스트 네트워크 네임스페이스에 사용할 수 있는 허용 가능한 호스트 포트 범위의 최댓값|Pod가 호스트 네트워크 네임스페이스에 사용할 수 있는 허용 가능한 호스트 포트 범위의 최댓값입니다.|
|CMMC Level 3|정책 할당|키에 대한 최소 RSA 키 크기|RSA 키에 대한 최소 키 크기입니다.|
|CMMC Level 3|정책 할당|최소 RSA 키 크기 인증서|RSA 인증서에 대한 최소 키 크기입니다.|
|CMMC Level 3|정책 할당|Windows 웹 서버의 최소 TLS 버전|더 낮은 TLS 버전을 사용하는 Windows 웹 서버는 비준수로 평가됩니다.|
|CMMC Level 3|정책 할당|Pod가 호스트 네트워크 네임스페이스에 사용할 수 있는 허용 가능한 호스트 포트 범위의 최솟값|Pod가 호스트 네트워크 네임스페이스에 사용할 수 있는 허용 가능한 호스트 포트 범위의 최솟값입니다.|
|CMMC Level 3|정책 할당|모드 요구 사항|모든 WAF 정책에 필요한 모드입니다.|
|CMMC Level 3|정책 할당|모드 요구 사항|모든 WAF 정책에 필요한 모드입니다.|
|CMMC Level 3|정책 할당|Pod hostPath 볼륨에서 사용하도록 허용된 호스트 경로|Pod hostPath 볼륨에서 사용하도록 허용된 호스트 경로입니다. 모든 호스트 경로를 차단하려면 빈 경로 목록을 제공합니다.|
|CMMC Level 3|정책 할당|네트워크 액세스: 원격으로 액세스할 수 있는 레지스트리 경로|`winreg` 레지스트리 키의 ACL(액세스 제어 목록)에 나열된 사용자 또는 그룹과 관계없이 네트워크에서 액세스할 수 있는 레지스트리 경로를 지정합니다.|
|CMMC Level 3|정책 할당|네트워크 액세스: 원격으로 액세스할 수 있는 레지스트리 경로 및 하위 경로|`winreg` 레지스트리 키의 ACL(액세스 제어 목록)에 나열된 사용자 또는 그룹과 관계없이 네트워크에서 액세스할 수 있는 레지스트리 경로 및 하위 경로를 지정합니다.|
|CMMC Level 3|정책 할당|네트워크 액세스: 익명으로 액세스할 수 있는 공유|익명 사용자가 액세스할 수 있는 네트워크 공유를 지정합니다. 모든 사용자가 서버의 공유 리소스에 액세스하려면 먼저 인증을 받아야 하므로 이 정책 설정의 기본 구성은 거의 효과가 없습니다.|
|CMMC Level 3|정책 할당|네트워크 보안: Kerberos에 허용된 암호화 유형 구성|Kerberos에서 사용할 수 있는 암호화 유형을 지정합니다.|
|CMMC Level 3|정책 할당|네트워크 보안: LAN Manager 인증 수준|네트워크 로그온에 사용되는 시도/응답 인증 프로토콜을 지정합니다. 이 옵션은 클라이언트에서 사용하는 인증 프로토콜 수준, 협상된 세션 보안 수준 및 서버에서 수락한 인증 수준에 영향을 줍니다.|
|CMMC Level 3|정책 할당|네트워크 보안: LDAP 클라이언트 서명 필요|LDAP BIND 요청을 실행하는 클라이언트를 대신하여 요청된 데이터 서명 수준을 지정합니다.|
|CMMC Level 3|정책 할당|네트워크 보안: NTLM SSP 기반(보안 RPC 포함) 클라이언트에 대한 최소 세션 보안|NTLM SSP(보안 지원 공급자)를 사용하는 애플리케이션에 대해 클라이언트에서 허용하는 동작을 지정합니다. 인증 서비스가 필요한 애플리케이션에서 SSPI(SSP 인터페이스)를 사용합니다. 자세한 내용은 [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers)를 참조하세요.|
|CMMC Level 3|정책 할당|네트워크 보안: NTLM SSP 기반(보안 RPC 포함) 서버에 대한 최소 세션 보안|NTLM SSP(보안 지원 공급자)를 사용하는 애플리케이션에 대해 서버에서 허용하는 동작을 지정합니다. 인증 서비스가 필요한 애플리케이션에서 SSPI(SSP 인터페이스)를 사용합니다.|
|CMMC Level 3|정책 할당|App Services용 최신 PHP 버전|App Services에 대해 지원되는 최신 PHP 버전|
|CMMC Level 3|정책 할당|IoT Hub 진단 로그의 필수 보존 기간(일)||
|CMMC Level 3|정책 할당|Network Watcher에 대한 리소스 그룹의 이름|Network Watcher의 리소스 그룹 이름(예: NetworkWatcherRG)입니다. Network Watcher가 있는 리소스 그룹입니다.|
|CMMC Level 3|정책 할당|SQL Server에 대한 필수 감사 설정||
|CMMC Level 3|정책 할당|소프트웨어 기반 이중 암호화를 지원하는 Azure Data Box SKU|소프트웨어 기반 이중 암호화를 지원하는 Azure Data Box SKU 목록입니다.|
|CMMC Level 3|정책 할당|UAC: 기본 제공 관리자 계정에 대한 관리자 승인 모드|기본 제공 관리자 계정에 대한 관리자 승인 모드 동작을 지정합니다.|
|CMMC Level 3|정책 할당|UAC: 관리자 승인 모드에서 관리자에 대한 권한 상승 프롬프트 동작|관리자에 대한 권한 상승 프롬프트 동작을 지정합니다.|
|CMMC Level 3|정책 할당|UAC: 애플리케이션 설치 검색 및 권한 상승 확인|컴퓨터에 대한 애플리케이션 설치 검색 동작을 지정합니다.|
|CMMC Level 3|정책 할당|UAC: 관리자 승인 모드에서 모든 관리자 실행|컴퓨터에 대한 모든 UAC(사용자 계정 컨트롤) 정책 설정 동작을 지정합니다.|
|CMMC Level 3|정책 할당|원격 시스템에서 강제로 종료할 수 있는 사용자 및 그룹|네트워크의 원격 위치에서 컴퓨터를 종료하도록 허용되는 사용자 및 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|네트워크에서 이 컴퓨터에 대한 액세스가 거부된 사용자 및 그룹|네트워크를 통해 컴퓨터에 연결하는 것이 명시적으로 금지된 사용자 또는 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|로컬 로그온이 거부된 사용자 및 그룹|컴퓨터에 로그온하도록 명시적으로 허용되지 않은 사용자 및 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|일괄 작업으로서의 로그온이 거부된 사용자 및 그룹|일괄 작업(즉, 예약된 작업)으로 컴퓨터에 로그온하도록 명시적으로 허용되지 않은 사용자 및 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|서비스로서의 로그온이 거부된 사용자 및 그룹|프로세스를 서비스로 등록하도록 명시적으로 허용되지 않은 서비스 계정을 지정합니다.|
|CMMC Level 3|정책 할당|원격 데스크톱 서비스를 통한 로그온이 거부된 사용자 및 그룹|터미널 서비스/원격 데스크톱 클라이언트를 통해 컴퓨터에 로그온하도록 명시적으로 허용되지 않은 사용자 및 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|파일 및 디렉터리를 복원할 수 있는 사용자 및 그룹|백업된 파일 및 디렉터리를 복원할 때 파일, 디렉터리, 레지스트리 및 기타 영구 개체 권한을 무시하도록 허용되는 사용자 및 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|시스템을 종료할 수 있는 사용자 및 그룹|사용자 환경의 컴퓨터에 로컬로 로그온하는 사용자 및 그룹 중에서 시스템 종료 명령을 사용하여 운영 체제를 종료하도록 허용되는 대상을 지정합니다.|
|CMMC Level 3|정책 할당|로컬로 로그온할 수 있는 사용자 또는 그룹|컴퓨터에 연결하도록 허용되는 네트워크의 원격 사용자를 지정합니다. 원격 데스크톱 연결은 포함되지 않습니다.|
|CMMC Level 3|정책 할당|파일 및 디렉터리를 백업할 수 있는 사용자 또는 그룹|시스템을 백업하기 위한 파일 및 디렉터리 권한을 우회하도록 허용된 사용자 및 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|시스템 시간을 변경할 수 있는 사용자 또는 그룹|컴퓨터 내부 시계의 시간과 날짜를 변경하도록 허용되는 사용자 및 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|표준 시간대를 변경할 수 있는 사용자 또는 그룹|컴퓨터 표준 시간대를 변경하도록 허용되는 사용자 및 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|토큰 개체를 만들 수 있는 사용자 또는 그룹|중요한 데이터에 액세스하기 위한 상승된 권한을 제공할 수 있는 액세스 토큰을 만들도록 허용되는 사용자 및 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|로컬로 로그온할 수 있는 사용자 또는 그룹|컴퓨터에 대화형으로 로그온할 수 있는 사용자 또는 그룹을 지정합니다. 원격 데스크톱 연결 또는 IIS를 통해 로그온하려는 사용자에게 이 사용자 권한이 필요합니다.|
|CMMC Level 3|정책 할당|Remote Desktop Users|원격 데스크톱 서비스를 통해 로그온할 수 있는 사용자 또는 그룹|
|CMMC Level 3|정책 할당|감사 및 보안 로그를 관리할 수 있는 사용자 또는 그룹|파일 및 디렉터리의 감사 옵션을 변경하고 보안 로그를 지울 수 있는 사용자 및 그룹을 지정합니다.|
|CMMC Level 3|정책 할당|파일 또는 다른 개체의 소유권을 가져올 수 있는 사용자 또는 그룹|파일, 폴더, 레지스트리 키, 프로세스 또는 스레드의 소유권이 허용되는 사용자 및 그룹을 지정합니다. 이 사용자 권한은 개체를 보호하기 위해 지정된 모든 권한을 무시하고 지정된 사용자에게 소유권을 제공합니다.|

## <a name="next-steps"></a>다음 단계

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
