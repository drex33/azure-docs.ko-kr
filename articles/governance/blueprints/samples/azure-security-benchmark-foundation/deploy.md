---
title: Azure Security Benchmark Foundation 청사진 샘플 배포
description: 청사진 아티팩트 매개 변수 세부 정보를 포함하여 Azure Security Benchmark Foundation 청사진 샘플에 대한 단계를 배포합니다.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 8b609adca8133fbe1320b9a02ef02b2dd4e61fbf
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132935506"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Azure Security Benchmark Foundation 청사진 샘플 배포

Azure Security Benchmark Foundation 청사진 샘플을 배포하려면 다음 단계를 수행해야 합니다.

> [!div class="checklist"]
> - 샘플에서 새 청사진 만들기
> - 샘플 사본을 **게시됨** 으로 표시
> - 기존 구독에 청사진 사본 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="create-blueprint-from-sample"></a>샘플에서 청사진 만들기

먼저 샘플을 스타터로 사용하여 환경 내에 새 청사진을 만들어 청사진 샘플을 구현합니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. **시작** 페이지 왼쪽에서 _청사진 만들기_ 아래의 **만들기** 단추를 선택합니다.

1. _기타 샘플_ 에서 **Azure Security Benchmark Foundation** 청사진 샘플을 찾고 **이 샘플 사용** 을 선택합니다.

1. 청사진 샘플의 _기본 사항_ 을 입력합니다.

   - **청사진 이름**: Azure Security Benchmark Foundation 청사진 샘플의 복사본에 대한 이름을 제공합니다.
   - **정의 위치**: 줄임표를 사용하고 샘플 사본을 저장할 관리 그룹을 선택합니다.

1. 페이지 위쪽의 _아티팩트_ 탭 또는 페이지 아래쪽의 **다음: 아티팩트** 를 선택합니다.

1. 청사진 샘플을 구성하는 아티팩트 목록을 검토합니다. 아티팩트 대부분에 매개 변수는 나중에 정의할 것입니다. 청사진 샘플 검토가 끝나면 **초안 저장** 을 선택합니다.

## <a name="publish-the-sample-copy"></a>샘플 사본 게시

이제 작업 환경에 청사진 샘플 사본이 만들어졌습니다. 이 청사진 정의는 **초안** 모드로 만들어졌으며 먼저 **게시** 해야만 할당하고 배포할 수 있습니다. 청사진 샘플의 사본을 환경 및 필요에 맞게 사용자 지정할 수 있지만, 해당 수정으로 Azure Security Benchmark Foundation 청사진에서 벗어날 수 있습니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다. 필터를 사용하여 청사진 샘플의 사본을 찾은 다음, 선택합니다.

1. 페이지 맨 위에서 **청사진 게시** 를 선택합니다. 오른쪽의 새 페이지에서 청사진 샘플 사본의 **버전** 을 지정합니다. 이 속성은 나중에 수정할 경우에 유용합니다. "Azure Security Benchmark Foundation 청사진 샘플에서 게시된 첫 번째 버전"과 같은 **변경 메모** 를 제공합니다. 그런 후 페이지 아래쪽의 **게시** 를 선택합니다.

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
     - **위치**: 관리 ID를 만들 지역을 선택합니다.
     - Azure Blueprints는 이 관리 ID를 사용하여 할당된 청사진의 모든 아티팩트를 배포합니다.
       자세히 알아보려면 [Azure 리소스의 관리 ID](../../../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
     - **청사진 정의 버전**: 청사진 샘플 사본의 **게시됨** 버전을 선택합니다.

   - 할당 잠금

     환경에 맞는 청사진 잠금 설정을 선택합니다. 자세한 내용은 [청사진 리소스 잠금](../../concepts/resource-locking.md)을 참조하세요.

   - 관리 ID

     기본 _시스템 할당_ 관리 ID 옵션 또는 _사용자 할당_ ID 옵션 중 하나를 선택합니다.

   - 청사진 매개 변수

     이 섹션에 정의된 매개 변수는 일관성을 제공하기 위해 청사진 정의의 아티팩트 대부분에서 사용됩니다.

     - **리소스 및 리소스 그룹의 접두사**: 이 문자열은 모든 리소스 및 리소스 그룹 이름의 접두사로 사용됩니다.
     - **허브 이름**: 허브에 대한 이름
     - **로그 보존 기간(일)** : 로그가 보존되는 일 수입니다. '0'을 입력하면 로그가 무기한 유지됩니다.
     - **허브 배포**: 할당이 아키텍처의 허브 구성 요소를 배포하는지 여부를 지정하려면 'true' 또는 'false'를 입력합니다.
     - **허브 위치**: 허브 리소스 그룹의 위치
     - **대상 IP 주소**: 아웃바운드 연결에 대한 대상 IP 주소, 쉼표로 구분된 IP 주소 또는 IP 범위 접두사 목록
     - **Network Watcher 이름**: Network Watcher 리소스에 대한 이름
     - **Network Watcher 리소스 그룹 이름**: Network Watcher 리소스 그룹에 대한 이름
     - **DDoS Protection 사용**: 가상 네트워크에서 DDoS 보호를 사용할지 여부를 지정하려면 'true' 또는 'false'를 입력합니다.

     > [!NOTE]
     > Network Watcher가 이미 사용하도록 설정된 경우 기존 Network Watcher 리소스 그룹을 사용하는 것이 좋습니다. 또한 아티팩트 매개 변수 **Network Watcher 리소스 그룹 위치** 에 대한 기존 Network Watcher 리소스 그룹의 위치를 제공해야 합니다.

   - 아티팩트 매개 변수

     이 섹션에 정의된 매개 변수는 정의된 아티팩트에 적용됩니다. 이러한 매개 변수는 청사진 할당 중에 정의되므로 [동적 매개 변수](../../concepts/parameters.md#dynamic-parameters)입니다. 전체 목록 또는 아티팩트 매개 변수 및 해당 설명은 [아티팩트 매개 변수 테이블](#artifact-parameters-table)을 참조하세요.

1. 모든 매개 변수를 입력한 후에는 페이지 아래쪽에서 **할당** 을 선택합니다. 청사진 할당이 생성되고 아티팩트 배포가 시작됩니다. 배포에는 약 1 시간이 걸립니다. 배포의 상태를 확인하려면 청사진 할당을 엽니다.

> [!WARNING]
> Azure Blueprints 서비스 및 기본 제공 청사진 샘플은 **무료** 입니다. Azure 리소스는 [제품별로 가격이 책정](https://azure.microsoft.com/pricing/)됩니다. 이 청사진 샘플에서 배포되는 리소스를 실행하는 비용을 추정하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

## <a name="artifact-parameters-table"></a>아티팩트 매개 변수 테이블

다음 테이블은 청사진 매개 변수의 목록을 제공합니다.

|아티팩트 이름|아티팩트 형식|매개 변수 이름|Description|
|-|-|-|-|
|허브 리소스 그룹|리소스 그룹|리소스 그룹 이름|잠김 - 허브 이름으로 접두사 연결|
|허브 리소스 그룹|리소스 그룹|리소스 그룹 위치|잠김 - 허브 위치 사용|
|Azure Firewall 템플릿|Resource Manager 템플릿|Azure Firewall 개인 IP 주소||
|Azure Log Analytics 및 진단 템플릿|Resource Manager 템플릿|Log Analytics 작업 영역 위치|Log Analytics 작업 영역을 만들 위치입니다. Azure PowersShell에서 `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName`을 실행하여 사용 가능한 영역을 봅니다.|
|Azure Log Analytics 및 진단 템플릿|Resource Manager 템플릿|Azure Automation 계정 ID(선택 사항) |Automation 계정 리소스 ID, Log Analytics와 Automation 계정 간에 연결된 서비스를 만드는 데 사용됩니다.|
|Azure 네트워크 보안 그룹 템플릿|Resource Manager 템플릿|NSG 흐름 로그 사용|NSG 흐름 로그를 사용하거나 사용하지 않으려면 'true' 또는 'false'를 입력합니다.|
|Azure Virtual Network 허브 템플릿|Resource Manager 템플릿|가상 네트워크 주소 접두사|허브 가상 네트워크에 대한 가상 네트워크 주소 접두사|
|Azure Virtual Network 허브 템플릿|Resource Manager 템플릿|방화벽 서브넷 주소 접두사|허브 가상 네트워크에 대한 방화벽 서브넷 주소 접두사|
|Azure Virtual Network 허브 템플릿|Resource Manager 템플릿|베스천 서브넷 주소 접두사|허브 가상 네트워크에 대한 베스천 서브넷 주소 접두사|
|Azure Virtual Network 허브 템플릿|Resource Manager 템플릿|게이트웨이 서브넷 주소 접두사|허브 가상 네트워크에 대한 게이트웨이 서브넷 주소 접두사|
|Azure Virtual Network 허브 템플릿|Resource Manager 템플릿|관리 서브넷 주소 접두사|허브 가상 네트워크에 대한 관리 서브넷 주소 접두사|
|Azure Virtual Network 허브 템플릿|Resource Manager 템플릿|점프 상자 서브넷 주소 접두사|허브 가상 네트워크에 대한 점프 상자 서브넷 주소 접두사|
|Azure Virtual Network 허브 템플릿|Resource Manager 템플릿|서브넷 주소 이름(선택 사항)|허브 가상 네트워크에 배포할 서브넷 이름 배열(예: "subnet1", "subnet2")|
|Azure Virtual Network 허브 템플릿|Resource Manager 템플릿|서브넷 주소 접두사(선택 사항)|허브 가상 네트워크의 선택적 서브넷에 대한 IP 주소 접두사의 배열(예: "10.0.7.0/24", "10.0.8.0/24")|
|스포크 리소스 그룹|리소스 그룹|리소스 그룹 이름|잠김 - 스포크 이름으로 접두사 연결|
|스포크 리소스 그룹|리소스 그룹|리소스 그룹 위치|잠김 - 허브 위치 사용|
|Azure Virtual Network 스포크 템플릿|Resource Manager 템플릿|스포크 배포|할당이 아키텍처의 스포크 구성 요소를 배포하는지 여부를 지정하려면 'true' 또는 'false'를 입력합니다.|
|Azure Virtual Network 스포크 템플릿|Resource Manager 템플릿|허브 구독 ID|허브가 배포된 구독 ID, 기본값은 청사진 정의가 있는 구독입니다.|
|Azure Virtual Network 스포크 템플릿|Resource Manager 템플릿|스포크 이름|스포크의 이름|
|Azure Virtual Network 스포크 템플릿|Resource Manager 템플릿|Virtual Network 주소 접두사|스포크 가상 네트워크에 대한 Virtual Network 주소 접두사|
|Azure Virtual Network 스포크 템플릿|Resource Manager 템플릿|서브넷 주소 접두사|스포크 가상 네트워크에 대한 서브넷 주소 접두사|
|Azure Virtual Network 스포크 템플릿|Resource Manager 템플릿|서브넷 주소 이름(선택 사항)|허브 가상 네트워크에 배포할 서브넷 이름 배열(예: "subnet1", "subnet2")|
|Azure Virtual Network 스포크 템플릿|Resource Manager 템플릿|서브넷 주소 접두사(선택 사항)|스포크 가상 네트워크의 선택적 서브넷에 대한 IP 주소 접두사의 배열(예: "10.0.7.0/24", "10.0.8.0/24")|
|Azure Virtual Network 스포크 템플릿|Resource Manager 템플릿|스포크 배포|할당이 아키텍처의 스포크 구성 요소를 배포하는지 여부를 지정하려면 'true' 또는 'false'를 입력합니다.|
|Azure Network Watcher 템플릿|Resource Manager 템플릿|Network Watcher 위치|Network Watcher 리소스의 위치|
|Azure Network Watcher 템플릿|Resource Manager 템플릿|Network Watcher 리소스 그룹 위치|Network Watcher가 이미 사용하도록 설정된 경우 이 매개 변수 값은 기존 Network Watcher 리소스 그룹의 위치와 일치 **해야 합니다**.|

## <a name="troubleshooting"></a>문제 해결

`The resource group 'NetworkWatcherRG' failed to deploy due to the
following error: Invalid resource group location '{location}'. The Resource group already exists in
location '{location}'.` 오류가 발생하면 청사진 매개 변수 **Network Watcher 리소스 그룹 이름** 이 기존 Network Watcher 리소스 그룹 이름을 지정하고 아티팩트 매개 변수 **Network Watcher 리소스 그룹 위치** 가 기존 Network Watcher 리소스 그룹 위치를 지정하는지 확인합니다.

## <a name="next-steps"></a>다음 단계

지금까지 Azure Security Benchmark Foundation 청사진 샘플을 배포하는 단계를 살펴보았으므로 다음 문서를 참조하여 아키텍처에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Azure Security Benchmark Foundation 청사진 - 개요](./index.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
