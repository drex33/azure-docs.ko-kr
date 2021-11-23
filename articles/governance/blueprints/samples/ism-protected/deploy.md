---
title: 오스트레일리아 정부 ISM PROTECTED 청사진 샘플 배포
description: 청사진 아티팩트 매개 변수 세부 정보를 포함하여 오스트레일리아 정부 ISM PROTECTED 청사진 샘플에 대한 단계를 배포합니다.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 305eb078ac3e65838ef4da52130d199e98e7a480
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132937358"
---
# <a name="deploy-the-australian-government-ism-protected-blueprint-sample"></a>오스트레일리아 정부 ISM PROTECTED 청사진 샘플 배포

Azure Blueprints ISM PROTECTED 청사진 샘플을 배포하려면 다음 단계를 수행해야 합니다.

> [!div class="checklist"]
> - 샘플에서 새 청사진 만들기
> - 샘플 사본을 **게시됨** 으로 표시
> - 기존 구독에 청사진 사본 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="create-blueprint-from-sample"></a>샘플에서 청사진 만들기

먼저 샘플을 스타터로 사용하여 환경 내에 새 청사진을 만들어 청사진 샘플을 구현합니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. **시작** 페이지 왼쪽에서 _청사진 만들기_ 아래의 **만들기** 단추를 선택합니다.

1. **ISM PROTECTED** 청사진 샘플을 _다른 샘플_ 아래에서 찾은 후, **이 샘플 사용** 을 선택합니다.

1. 청사진 샘플의 _기본 사항_ 을 입력합니다.

   - **청사진 이름**: ISM PROTECTED 청사진 샘플 사본의 이름을 지정합니다.
   - **정의 위치**: 줄임표를 사용하고 샘플 사본을 저장할 관리 그룹을 선택합니다.

1. 페이지 위쪽의 _아티팩트_ 탭 또는 페이지 아래쪽의 **다음: 아티팩트** 를 선택합니다.

1. 청사진 샘플을 구성하는 아티팩트 목록을 검토합니다. 아티팩트 대부분에 매개 변수는 나중에 정의할 것입니다. 청사진 샘플 검토가 끝나면 **초안 저장** 을 선택합니다.

## <a name="publish-the-sample-copy"></a>샘플 사본 게시

이제 작업 환경에 청사진 샘플 사본이 만들어졌습니다. 이 청사진 정의는 **초안** 모드로 만들어졌으며 먼저 **게시** 해야만 할당하고 배포할 수 있습니다. 청사진 샘플의 사본을 환경 및 필요에 맞게 사용자 지정할 수 있지만, 해당 수정으로 ISM PROTECTED 컨트롤에서 벗어날 수 있습니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 페이지 맨 위에서 **청사진 게시** 를 선택합니다. 오른쪽의 새 페이지에서 청사진 샘플 사본의 **버전** 을 지정합니다. 이 속성은 나중에 수정할 경우에 유용합니다. "ISM PROTECTED 청사진 샘플에서 게시된 첫 번째 버전"과 같은 **변경 메모** 를 제공합니다. 그런 후 페이지 아래쪽의 **게시** 를 선택합니다.

## <a name="assign-the-sample-copy"></a>샘플 사본 할당

청사진 샘플 사본이 **게시** 되면 저장된 관리 그룹 내의 구독에 할당할 수 있습니다. 이 단계에서는 청사진 샘플 사본의 배포를 고유하게 만들기 위해 매개 변수가 제공됩니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 청사진 정의 페이지 위쪽에서 **청사진 할당** 을 선택합니다.

1. 청사진 할당의 매개 변수 값을 다음과 같이 지정합니다.

   - 기본 사항

     - **구독**: 청사진 샘플 사본을 저장한 관리 그룹에 있는 하나 이상의 구독을 선택합니다. 둘 이상의 구독을 선택하면 입력한 매개 변수를 사용하여 각 구독의 할당이 생성됩니다.
     - **할당 이름**: 청사진의 이름에 따라 이 이름이 미리 채워집니다.
       필요에 따라 변경하거나 그대로 둡니다.
     - **위치**: 관리 ID를 만들 지역을 선택합니다. Azure Blueprints는 이 관리 ID를 사용하여 할당된 청사진의 모든 아티팩트를 배포합니다. 자세히 알아보려면 [Azure 리소스의 관리 ID](../../../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
     - **청사진 정의 버전**: 청사진 샘플 사본의 **게시됨** 버전을 선택합니다.

   - 할당 잠금

     환경에 맞는 청사진 잠금 설정을 선택합니다. 자세한 내용은 [청사진 리소스 잠금](../../concepts/resource-locking.md)을 참조하세요.

   - 관리 ID

     기본값 _시스템이 할당한_ 관리 ID 옵션을 유지합니다.

   - 아티팩트 매개 변수

     이 섹션에 정의된 매개 변수는 정의된 아티팩트에 적용됩니다. 이러한 매개 변수는 청사진 할당 중에 정의되므로 [동적 매개 변수](../../concepts/parameters.md#dynamic-parameters)입니다. 전체 목록 또는 아티팩트 매개 변수 및 해당 설명은 [아티팩트 매개 변수 테이블](#artifact-parameters-table)을 참조하세요.

1. 모든 매개 변수를 입력한 후에는 페이지 아래쪽에서 **할당** 을 선택합니다. 청사진 할당이 생성되고 아티팩트 배포가 시작됩니다. 배포에는 약 1 시간이 걸립니다. 배포의 상태를 확인하려면 청사진 할당을 엽니다.

> [!WARNING]
> Azure Blueprints 서비스 및 기본 제공 청사진 샘플은 **무료** 입니다. Azure 리소스는 [제품별로 가격이 책정](https://azure.microsoft.com/pricing/)됩니다. 이 청사진 샘플에서 배포되는 리소스를 실행하는 비용을 추정하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

## <a name="artifact-parameters-table"></a>아티팩트 매개 변수 테이블

다음 테이블은 청사진 아티팩트 매개 변수의 목록을 제공합니다.

|아티팩트 이름|아티팩트 형식|매개 변수 이름|Description|
|-|-|-|-|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|VM을 구성해야 하는 Log Analytics 작업 영역 ID|VM을 구성해야 하는 Log Analytics 작업 영역의 ID(GUID)입니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|진단 로그를 사용해야 하는 리소스 종류 목록|진단 로그 설정을 사용하도록 설정하지 않은 경우 감사할 리소스 종류의 목록입니다. 허용되는 값은 [Azure Monitor 리소스 로그 범주](../../../../azure-monitor/essentials/resource-logs-categories.md#supported-log-categories-per-resource-type)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Windows VM 관리자 그룹에서 제외해야 하는 사용자 목록|관리자 로컬 그룹에서 제외해야 하는 구성원의 세미콜론으로 구분된 목록입니다. 예: Administrator; myUser1; myUser2|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Windows VM 관리자 그룹에 포함해야 하는 사용자 목록|관리자 로컬 그룹에 포함해야 하는 구성원의 세미콜론으로 구분된 목록입니다. 예: Administrator; myUser1; myUser2|
|\[미리 보기\]: Linux VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포|정책 할당|Linux VMSS(VM Scale Sets)용 Log Analytics 작업 영역|이 작업 영역이 할당 범위를 벗어나는 경우 ‘Log Analytics 기여자’ 권한(또는 그와 유사한 권한)을 정책 할당의 Principal ID에 수동으로 부여해야 합니다.|
|\[미리 보기\]: Linux VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포|정책 할당|선택 사항: 범위에 추가할 Linux OS를 지원하는 VM 이미지 목록|빈 배열(\[\])을 사용하여 선택적 매개 변수가 없음을 나타낼 수 있습니다.|
|\[미리 보기\]: Linux VM용 Log Analytics 에이전트 배포|정책 할당|Linux VM용 Log Analytics 작업 영역|이 작업 영역이 할당 범위를 벗어나는 경우 ‘Log Analytics 기여자’ 권한(또는 그와 유사한 권한)을 정책 할당의 Principal ID에 수동으로 부여해야 합니다.|
|\[미리 보기\]: Linux VM용 Log Analytics 에이전트 배포|정책 할당|선택 사항: 범위에 추가할 Linux OS를 지원하는 VM 이미지 목록|빈 배열(\[\])을 사용하여 선택적 매개 변수가 없음을 나타낼 수 있습니다.|
|\[미리 보기\]: Windows VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포|정책 할당|Windows VMSS(VM Scale Sets)용 Log Analytics 작업 영역|이 작업 영역이 할당 범위를 벗어나는 경우 ‘Log Analytics 기여자’ 권한(또는 그와 유사한 권한)을 정책 할당의 Principal ID에 수동으로 부여해야 합니다.|
|\[미리 보기\]: Windows VMSS(VM Scale Sets)용 Log Analytics 에이전트 배포|정책 할당|선택 사항: 범위에 추가할 Windows OS를 지원하는 VM 이미지 목록|빈 배열(\[\])을 사용하여 선택적 매개 변수가 없음을 나타낼 수 있습니다.|
|\[미리 보기\]: Windows VM용 Log Analytics 에이전트 배포|정책 할당|Windows VM용 Log Analytics 작업 영역|이 작업 영역이 할당 범위를 벗어나는 경우 ‘Log Analytics 기여자’ 권한(또는 그와 유사한 권한)을 정책 할당의 Principal ID에 수동으로 부여해야 합니다.|
|\[미리 보기\]: Windows VM용 Log Analytics 에이전트 배포|정책 할당|선택 사항: 범위에 추가할 Windows OS를 지원하는 VM 이미지 목록|빈 배열(\[\])을 사용하여 선택적 매개 변수가 없음을 나타낼 수 있습니다.|
|스토리지 계정에 Advanced Threat Protection 배포|정책 할당|영향|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|SQL Server에 감사 배포|정책 할당|보존 기간 값(일)(0은 보존 기간에 제한이 없음을 표시) |보존 일 수(선택 사항, 지정하지 않는 경우 180일) |
|SQL Server에 감사 배포|정책 할당|SQL 서버 감사를 위한 스토리지 계정의 리소스 그룹 이름|감사에서는 데이터베이스 이벤트를 Azure Storage 계정의 감사 로그에 씁니다(스토리지 계정은 한 지역의 모든 서버에서 공유할 수 있는 SQL Server가 만들어진 각 지역에 만들어짐). 중요 - 적절한 감사 작업을 위해 리소스 그룹 또는 스토리지 계정을 삭제하거나 이름을 바꾸지 마세요.|
|네트워크 보안 그룹에 대한 진단 설정 배포|정책 할당|네트워크 보안 그룹 진단을 위한 스토리지 계정 접두사|이 접두사와 네트워크 보안 그룹 위치가 결합되어 스토리지 계정 이름이 구성됩니다.|
|네트워크 보안 그룹에 대한 진단 설정 배포|정책 할당|네트워크 보안 그룹 진단을 위한 스토리지 계정의 리소스 그룹 이름(있어야 함) |스토리지 계정이 생성될 리소스 그룹. 이 리소스 그룹은 이미 있어야 합니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|리소스 및 리소스 그룹에 허용된 위치|리소스를 배포할 때 조직에서 지정할 수 있는 Azure 위치 목록. 이 제공된 값은 정책 이니셔티브 내에서 ‘허용된 위치’ 정책에도 사용됩니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Virtual Machines에서 취약성 평가를 사용하도록 설정해야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Storage 계정에 대해 지역 중복 스토리지를 사용하도록 설정해야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|인터넷 연결 가상 머신에 대한 네트워크 보안 그룹 규칙을 강화해야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|웹 애플리케이션에 HTTPS를 통해서만 액세스 가능|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|함수 앱에 HTTPS를 통해서만 액세스 가능|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Azure SQL Database에 대해 장기 지역 중복 백업을 사용하도록 설정해야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Windows VM 관리자 그룹에서 제외된 사용자 목록|관리자 로컬 그룹에서 제외해야 하는 구성원의 세미콜론으로 구분된 목록입니다. 예: Administrator; myUser1; myUser2|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|VM을 구성해야 하는 Log Analytics 작업 영역 ID|VM을 구성해야 하는 Log Analytics 작업 영역의 ID(GUID)입니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|적응형 네트워크 강화 추천 사항은 인터넷에 연결된 가상 머신에 적용해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|구독에 둘 이상의 소유자를 할당해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|가상 머신에서 디스크 암호화를 적용해야 합니다.| 정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|함수 앱에 대해 원격 디버깅을 해제해야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.| 정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다. |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|선택 사항: 범위에 추가할 Windows OS를 지원하는 VM 이미지 목록|빈 배열([])을 사용하여 선택적 매개 변수가 없음을 나타낼 수 있습니다.
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|선택 사항: 범위에 추가할 Linux OS를 지원하는 VM 이미지 목록 |빈 배열([])을 사용하여 선택적 매개 변수가 없음을 나타낼 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|스토리지 계정에 대한 무제한 네트워크 액세스 감사|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|가상 머신에서 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|구독에 최대 3명의 소유자를 지정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|[미리 보기] Virtual Machines에서 취약성 평가를 사용하도록 설정해야 합니다.  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |  정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다. |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|HTTPS v2를 통해서만 함수 앱에 액세스할 수 있어야 함  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|취약성 평가 솔루션으로 취약성을 수정해야 합니다.  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Azure 구독에는 활동 로그에 대한 로그 프로필이 있어야 합니다.  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|진단 로그를 사용해야 하는 리소스 종류 목록|진단 로그 설정을 사용하도록 설정하지 않은 경우 감사할 리소스 종류의 목록입니다. 허용되는 값은 [Azure Monitor 리소스 로그 범주](../../../../azure-monitor/essentials/resource-logs-categories.md#supported-log-categories-per-resource-type)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|머신에 시스템 업데이트를 설치해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|App Service에 최신 TLS 버전을 사용해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Microsoft IaaSAntimalware 확장을 Windows Server에 배포해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|웹 애플리케이션은 HTTPS v2를 통해서만 액세스할 수 있어야 함  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|DDoS Protection 표준을 사용하도록 설정해야 합니다.  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다. |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다. |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|SQL 관리형 인스턴스에서 Advanced Data Security를 사용하도록 설정해야 합니다. |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Azure Security Center에서 누락된 엔드포인트 보호 모니터링  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|가상 머신에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다. |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다. |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|App Service에 HTTPS v2를 통해서만 액세스 가능 |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|웹 애플리케이션에 대해 원격 디버깅을 해제해야 함|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|머신 보안 구성의 취약성을 수정해야 합니다.  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다.|
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.  |정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|최근 암호 기억  | 암호 재사용 제한을 지정합니다. 암호를 반복하기 전에 사용자 계정에 대해 새 암호를 만들어야 하는 횟수를 지정합니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|최대 암호 사용 기간  | 사용자 계정 암호를 변경해야 하기 전에 경과할 수 있는 최대 일 수를 지정합니다. 값의 형식은 쉼표로 구분된 두 정수이며, 포괄 범위를 나타냅니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|최소 암호 사용 기간 |   사용자 계정 암호를 변경하기 전에 경과해야 하는 최소 일 수를 지정합니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|최소 암호 길이  | 사용자 계정 암호에 포함될 수 있는 최소 문자 수를 지정합니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|암호는 복잡성 조건을 만족해야 함|사용자 계정 암호가 복잡해야 하는지 여부를 지정합니다. 필요한 경우 복잡한 암호에는 사용자의 계정 이름 또는 전체 이름 일부가 포함되지 않아야 합니다. 6자 이상이어야 합니다. 대문자, 소문자, 숫자 및 영문자가 아닌 문자를 혼합하여 포함합니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|컨테이너 보안 구성의 취약성을 수정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|App Service에 대한 원격 디버깅을 해제해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|웹앱에서 최신 TLS 버전을 사용해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|인터넷 연결 가상 머신은 네트워크 보안 그룹과 함께 보호되어야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|함수 앱에서 최신 TLS 버전을 사용해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |
|\[미리 보기\]: 오스트레일리아 정부 ISM PROTECTED 컨트롤 감사 및 감사 요구 사항을 지원하도록 특정 VM 확장 배포|정책 할당|SQL 데이터베이스의 취약성을 수정해야 합니다.|정책의 영향에 대한 내용은 [Azure Policy의 영향 파악](../../../policy/concepts/effects.md)에서 찾을 수 있습니다. |

## <a name="next-steps"></a>다음 단계

지금까지 오스트레일리아 정부 ISM PROTECTED 청사진 샘플을 배포하는 단계를 살펴보았으므로 이제 다음 문서를 참조하여 청사진 및 컨트롤 매핑에 대해 알아보세요.

> [!div class="nextstepaction"]
> [ISM PROTECTED 청사진 - 개요](./index.md)
> [ISM PROTECTED 청사진 - 컨트롤 매핑](./control-mapping.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
