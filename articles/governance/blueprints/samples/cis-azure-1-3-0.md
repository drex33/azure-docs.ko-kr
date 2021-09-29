---
title: CIS Microsoft Azure Foundations Benchmark v1.3.0 청사진 샘플
description: CIS Microsoft Azure Foundations Benchmark v1.3.0 청사진 샘플에 대한 개요입니다. 이 청사진 샘플은 고객이 특정 컨트롤을 평가하는 데 도움이 됩니다.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 56189a41cf6debcbd0421ddc3227b4fcc84f15ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599528"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>CIS Microsoft Azure Foundations Benchmark v1.3.0 청사진 샘플

CIS Microsoft Azure Foundations Benchmark v1.3.0 청사진 샘플은 특정 CIS Microsoft Azure Foundations Benchmark v1.3.0 권장 사항을 평가하는 데 도움이 되는 [Azure Policy](../../policy/overview.md)를 사용하여 거버넌스 보호책을 제공합니다. 이 청사진은 고객이 CIS Microsoft Azure Foundations Benchmark v1.3.0 권장 사항을 구현해야 하는 Azure 배포 아키텍처에 대한 핵심 정책 세트를 배포하는 데 도움이 됩니다.

## <a name="recommendation-mapping"></a>추천 매핑

[Azure Policy 권장 사항 매핑](../../policy/samples/cis-azure-1-3-0.md)은 이 청사진에 포함된 정책 정의 및 이러한 정책 정의가 CIS Microsoft Azure Foundations Benchmark v1.3.0의 **권장 사항** 에 매핑되는 방법에 대한 세부 정보를 제공합니다. 아키텍처에 할당된 경우 리소스는 할당된 정책 정의를 준수하지 않는지 Azure Policy에서 평가됩니다. 자세한 내용은 [Azure Policy](../../policy/overview.md)를 참조하세요.

## <a name="deploy"></a>배포

Azure Blueprints CIS Microsoft Azure Foundations Benchmark v1.3.0 청사진 샘플을 배포하려면 다음 단계를 수행해야 합니다.

> [!div class="checklist"]
> - 샘플에서 새 청사진 만들기
> - 샘플 사본을 **게시됨** 으로 표시
> - 기존 구독에 청사진 사본 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

### <a name="create-blueprint-from-sample"></a>샘플에서 청사진 만들기

먼저 샘플을 스타터로 사용하여 환경 내에 새 청사진을 만들어 청사진 샘플을 구현합니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. **시작** 페이지 왼쪽에서 _청사진 만들기_ 아래의 **만들기** 단추를 선택합니다.

1. _다른 샘플_ 아래에서 **CIS Microsoft Azure Foundations Benchmark v1.3.0** 청사진 샘플을 찾고, **이 샘플 사용** 을 선택합니다.

1. 청사진 샘플의 _기본 사항_ 을 입력합니다.

   - **청사진 이름**: CIS Microsoft Azure Foundations Benchmark 청사진 샘플의 복사본 이름을 입력합니다.
   - **정의 위치**: 줄임표를 사용하고 샘플 사본을 저장할 관리 그룹을 선택합니다.

1. 페이지 위쪽의 _아티팩트_ 탭 또는 페이지 아래쪽의 **다음: 아티팩트** 를 선택합니다.

1. 청사진 샘플에 포함된 아티팩트 목록을 검토합니다. 아티팩트 대부분에 매개 변수는 나중에 정의할 것입니다. 청사진 샘플 검토가 끝나면 **초안 저장** 을 선택합니다.

### <a name="publish-the-sample-copy"></a>샘플 사본 게시

이제 작업 환경에 청사진 샘플 사본이 만들어졌습니다. 이 청사진 정의는 **초안** 모드로 만들어졌으며 먼저 **게시** 해야만 할당하고 배포할 수 있습니다. 청사진 샘플의 복사본을 환경 및 요구 사항에 맞게 사용자 지정할 수 있지만, 이러한 수정이 CIS Microsoft Azure Foundations Benchmark v1.3.0 권장 사항과 일치하지 않을 수 있습니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 페이지 맨 위에서 **청사진 게시** 를 선택합니다. 오른쪽의 새 페이지에서 청사진 샘플 사본의 **버전** 을 지정합니다. 이 속성은 나중에 수정할 경우에 유용합니다. "CIS Microsoft Azure Foundations Benchmark 청사진 샘플에서 게시된 첫 번째 버전"과 같은 **변경 메모** 를 입력합니다. 그런 후 페이지 아래쪽의 **게시** 를 선택합니다.

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
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|사용이 승인된 가상 머신 확장 목록|세미콜론으로 구분된 가상 머신 확장 목록입니다. 전체 확장 목록을 보려면 Get-AzVMExtensionImage Azure PowerShell 명령을 사용합니다.|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL Managed Instance는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Azure Data Lake Store의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 가상 머신에서 디스크 암호화를 적용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Key Vault는 제거 보호를 사용하도록 설정되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: API 앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL Server는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 함수 앱에서 관리 ID를 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Key Vault용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 사용자 지정 구독 소유자 역할이 없어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 키에 만료 날짜가 설정되어 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: API 앱의 일부로 사용되는 경우 'PHP 버전'이 최신 상태인지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: App Service용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 웹앱에서 관리 ID를 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 인터넷에서 SSH 액세스를 차단해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 연결되지 않은 디스크는 암호화되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 스토리지용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 스토리지 계정은 네트워크 액세스를 제한해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Logic Apps의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: IoT Hub의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 함수 앱에서 FTPS만 요구해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 특정 보안 작업의 활동 로그 경고가 있어야 함(Microsoft.Security/securitySolutions/delete)|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 특정 보안 작업의 활동 로그 경고가 있어야 함(Microsoft.Security/securitySolutions/write)|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Batch 계정의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 구독에서 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 웹앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 웹앱에서 FTPS를 요구해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 서버용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 구독에 보안 문제에 대한 연락처 이메일 주소가 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 스토리지 계정 퍼블릭 액세스를 허용하지 않아야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Kubernetes용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: PostgreSQL 데이터베이스 서버에 대해 연결 제한을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 웹앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 머신에서 SQL Server용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 스토리지 계정은 고객 관리형 키를 암호화에 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 웹앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 함수 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 웹앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: API 앱의 일부로 사용되는 경우 'Python 버전'이 최신 상태인지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Virtual Machine Scale Sets의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Azure SQL Database 서버용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 이벤트 허브의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 시스템 업데이트를 머신에 설치해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: API 앱의 일부로 사용되는 경우 'Java 버전'이 최신 상태인지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL Server는 90일 이상의 감사 보존 기간으로 구성해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 웹앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: API 앱에서 최신 TLS 버전을 사용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 웹앱에서 인증을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 비밀에 만료 날짜가 설정되어 있어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: API 앱을 실행하는 데 사용되는 경우 'HTTP 버전'이 최신 상태인지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: API 앱에서 FTPS만 요구해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 함수 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL Server에 대한 감사가 사용되도록 설정되어야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL Managed Instance에서 Advanced Data Security를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Kubernetes 서비스에서 RBAC(역할 기반 액세스 제어)를 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Azure Security Center에서 누락된 Endpoint Protection 모니터링|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Search Services의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: App Services의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.Network/networkSecurityGroups/delete) |효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.Network/networkSecurityGroups/securityRules/delete) |효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.Network/networkSecurityGroups/securityRules/write) |효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.Network/networkSecurityGroups/write) |효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.Sql/servers/firewallRules/delete) |효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 특정 관리 작업의 활동 로그 경고가 있어야 함(Microsoft.Sql/servers/firewallRules/write) |효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 승인된 VM 확장만 설치해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 컨테이너 레지스트리용 Azure Defender를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: API 앱에서 관리 ID를 사용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: API 앱에서 인증을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 특정 정책 작업의 활동 로그 경고가 있어야 함(Microsoft.Authorization/policyAssignments/delete) |효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 특정 정책 작업의 활동 로그 경고가 있어야 함(Microsoft.Authorization/policyAssignments/write) |효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 함수 앱에서 인증을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Data Lake Analytics의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 스토리지 계정은 신뢰할 수 있는 Microsoft 서비스의 액세스를 허용해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Key Vault의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: PostgreSQL 데이터베이스 서버에 대해 SSL 강제 연결을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 함수 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 인터넷에서 RDP 액세스를 차단해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: MySQL 데이터베이스 서버에 대해 SSL 강제 연결을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 함수 앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: PostgreSQL 데이터베이스 서버에 대해 로그 검사점을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: PostgreSQL 데이터베이스 서버에 대해 로그 연결을 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: PostgreSQL 데이터베이스 서버에 대한 연결 끊김을 기록해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 웹앱에서 최신 TLS 버전을 사용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Service Bus의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: Azure Stream Analytics의 진단 로그를 사용하도록 설정해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 함수 앱에서 최신 TLS 버전을 사용해야 합니다.|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|정책 효과: 활동 로그가 있는 컨테이너를 포함하는 스토리지 계정은 BYOK로 암호화해야 함|효과에 대한 자세한 내용은 [https://aka.ms/policyeffects](../../policy/concepts/effects.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|가상 머신 확장 집합 진단 로그가 사용되는 경우 감사할 때 AKS 클러스터 포함||
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|App Services용 최신 Java 버전|App Services에 대해 지원되는 최신 Java 버전|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|App Services에 지원되는 Linux용 최신 Python 버전|App Services에 대해 지원되는 최신 Python 버전|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|Network Watcher를 사용하도록 설정해야 하는 영역 목록|전체 지역 목록을 보려면 Get-AzLocation PowerShell 명령을 실행합니다.|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|App Services용 최신 PHP 버전|App Services에 대해 지원되는 최신 PHP 버전|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|리소스 로그에 대한 필수 보존 기간(일)|리소스 로그에 대한 자세한 내용은 [https://aka.ms/resourcelogs](../../../azure-monitor/essentials/resource-logs.md)를 방문하세요. |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|Network Watcher에 대한 리소스 그룹의 이름|Network Watcher가 있는 리소스 그룹의 이름|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|정책 할당|SQL Server에 대한 필수 감사 설정||

## <a name="next-steps"></a>다음 단계

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
