---
title: Azure Service Fabric 릴리스
description: Azure Service Fabric의 릴리스 정보입니다. Service Fabric의 최신 기능과 향상된 기능에 관한 정보를 포함합니다.
ms.date: 04/13/2021
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 8404078943f9398df6604c78413bc3750f13d655
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729086"
---
# <a name="service-fabric-releases"></a>Service Fabric 릴리스

이 문서에서는 Service Fabric 런타임 및 SDK의 최신 릴리스와 업데이트에 대한 자세한 내용을 제공합니다.

다음 리소스를 사용할 수도 있습니다.
- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">문제 해결 가이드</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">이슈 추적</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">지원 옵션</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">지원되는 버전</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">코드 샘플</a>


## <a name="service-fabric-81"></a>Service Fabric 8.1

Service Fabric 런타임의 8.1 릴리스가 도구 및 SDK 업데이트와 함께 다양한 Azure 지역에 배포되기 시작했음을 발표하게 되어 기쁩니다. .NET SDK, Java SDK 및 Service Fabric 런타임의 업데이트는 웹 플랫폼 설치 관리자, NuGet 패키지 및 Maven 리포지토리를 통해 제공됩니다.

### <a name="key-announcements"></a>주요 알림
- 보조 복제본 지원이 추가됨
- **미리 보기** .NET 6.0 Service Fabric 애플리케이션에 대한 지원이 추가됨
- 애플리케이션 설명 업데이트에 대한 API 지원이 추가됨
- IPC 실패 및 프로세스 중단을 감지하기 위해 RA(재구성 에이전트)와 RAP(재구성 에이전트 프록시) 간에 주기적인 ping이 추가됨
- 컨테이너화되지 않은 애플리케이션의 활동성 및 준비 상태 프로브에 대한 지원이 추가됨
- 노드 용량 업데이트에 대한 클러스터 업그레이드가 영향을 미치지 않음

### <a name="service-fabric-81-releases"></a>Service Fabric 8.1 릴리스
| 릴리스 날짜 | 해제 | 추가 정보 |
|---|---|---|
| 2021년 7월 28일 | [Azure Service Fabric 8.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-1-release/ba-p/2594194)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_81.md)|
| 2021년 8월 13일 | [Azure Service Fabric 8.1 최초 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-1-first-refresh-release/ba-p/2638798) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_81CU1.md) |
| 2021 년 9 월 9 일 | [Azure Service Fabric 8.1 초 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-1-second-refresh-release/ba-p/2734904) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_81CU2.md) |
| 10 월 06 2021 | [Azure Service Fabric 8.1 세 번째 새로 고침 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-1-third-refresh-release/ba-p/2816117) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_81CU3.md) |


## <a name="service-fabric-80"></a>Service Fabric 8.0

Service Fabric 런타임의 8.0 릴리스가 도구 및 SDK 업데이트와 함께 다양한 Azure 지역에 배포되기 시작했음을 발표하게 되어 기쁩니다. .NET SDK, Java SDK 및 Service Fabric 런타임의 업데이트는 웹 플랫폼 설치 관리자, NuGet 패키지 및 Maven 리포지토리를 통해 제공됩니다.

### <a name="key-announcements"></a>주요 알림

- Windows용 .NET 5 지원 **일반 공급**
- [상태 비저장 NodeTypes](./service-fabric-stateless-node-types.md) **일반 공급**
- 상태 비저장 서비스 인스턴스 이동 가능
- 애플리케이션 매니페스트에서 매개 변수가 있는 DefaultLoad 추가 가능
- 싱글톤 복제본 업그레이드의 경우 - 애플리케이션 수준에서 정의할 몇 가지 클러스터 수준 설정 포함 가능
- 노드 태그를 기반으로 하는 스마트 배치 가능
- 클러스터 상태에 영향을 주는 비정상 노드의 백분율 임계값 정의 가능
- 로드된 최상위 서비스 쿼리 가능
- 새 오류 코드에 대한 새 간격 추가 가능
- 서비스 인스턴스를 완료된 것으로 표시 가능
- 자동 업그레이드를 위한 웨이브 기반 배포 모델 지원
- 컨테이너화된 애플리케이션에 대한 준비 상태 프로브 추가
- 기본적으로 UseSeparateSecondaryMoveCost를 true로 설정
- 해제해도 안전할 경우 즉시 참조를 해제하도록 StateManager 수정
- 사용자 비밀을 저장하는 동안 중앙 비밀 서비스 제거 차단

### <a name="service-fabric-80-releases"></a>Service Fabric 8.0 릴리스
| 릴리스 날짜 | 해제 | 추가 정보 |
|---|---|---|
| 2021년 4월 8일 | [Azure Service Fabric 8.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-release/ba-p/2260016)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80.md)|
| 2021년 5월 17일 | [Azure Service Fabric 8.0 첫 번재 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-first-refresh-release/ba-p/2362556) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80CU1.md) |
| 2021년 6월 17일 | [Azure Service Fabric 8.0 두 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-second-refresh-release/ba-p/2462979) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80CU2.md) |
| 2021년 7월 28일 | [Azure Service Fabric 8.0 세 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-third-refresh-release/ba-p/2594180) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80CU3.md) |


## <a name="previous-versions"></a>이전 버전

### <a name="service-fabric-72"></a>Service Fabric 7.2

#### <a name="key-announcements"></a>주요 알림

- **미리 보기**: [**Service Fabric 관리형 클러스터**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572)는 현재 퍼블릭 미리 보기로 제공됩니다. Service Fabric 관리 클러스터는 Service Fabric 클러스터를 구성하는 기본 리소스를 단일 ARM 리소스로 캡슐화하여 클러스터 배포와 관리를 간소화하려 합니다. 자세한 내용은 [Service Fabric 관리형 클러스터 개요](./overview-managed-cluster.md)를 참조하세요.
- **미리 보기**: [**인스턴스 수가 노드 수보다 큰 상태 비저장 서비스 지원**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)은 현재 퍼블릭 미리 보기로 제공됩니다. 배치 정책을 사용하면 노드에서 파티션의 상태 비저장 인스턴스를 여러 개 만들 수 있습니다.
- 이제 [**FO(FabricObserver) 3.0**](https://aka.ms/sf/fabricobserver)을 사용할 수 있습니다.
    - 이제 Linux와 Windows 클러스터에서 FabricObserver를 실행할 수 있습니다.
    - 이제 사용자 지정 관찰자 플러그인을 빌드할 수 있습니다. 자세한 내용과 코드는 [플러그인 추가 정보](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md)와 [샘플 플러그인 프로젝트](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin)를 참조하세요.
    - 이제 애플리케이션 매개 변수 업그레이드를 통해 관찰자 설정을 변경할 수 있습니다. 즉, 특정 관찰자 설정을 수정하기 위해 더는 FO를 다시 배포할 필요가 없습니다. [샘플](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates)을 참조하세요.
- [**Ubuntu 18.04 OneBox 컨테이너 이미지를 지원**](https://hub.docker.com/_/microsoft-service-fabric-onebox)합니다.
- **미리 보기**: [**Service Fabric 애플리케이션의 KeyVault 참조에서는 **버전이 지정된 비밀만 지원합니다**. 버전이 없는 비밀은 지원되지 않습니다.**](./service-fabric-keyvault-references.md)
- SF SDK를 사용하려면 새로운 .NET Framework 상태 비저장/상태 저장/작업자 프로젝트를 만들 수 있도록 최신 VS 2019 업데이트 16.7.6 또는 16.8 Preview 4가 필요합니다. 최신 VS 업데이트가 없으면 서비스 프로젝트를 만든 다음, 패키지 관리자를 사용하여 nuget.org에서 상태 저장/상태 비저장 프로젝트용 Microsoft.ServiceFabric.Services(버전 4.2.x)와 작업자 프로젝트용 Microsoft.ServiceFabric.Actors(버전 4.2.x)를 설치합니다.
- **RunToCompletion**: Service Fabric에서는 게스트 실행 파일의 완료될 때까지 실행 개념을 지원합니다. 이 업데이트를 사용하면 복제본이 완료될 때까지 실행되면 이 복제본에 할당된 클러스터 리소스가 해제됩니다.
- [**리소스 거버넌스 지원이 향상됨**](./service-fabric-resource-governance.md): CPU와 메모리 리소스에 대한 요청 및 한도 사양을 허용합니다.

#### <a name="service-fabric-72-releases"></a>Service Fabric 7.2 릴리스
| 릴리스 날짜 | 해제 | 추가 정보 |
|---|---|---|
| 2020년 10월 21일 | [Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 2020년 11월 9일 | [Azure Service Fabric 7.2 두 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 2020년 11월 10일  | Azure Service Fabric 7.2 세 번째 새 릴리스 | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2020년 12월 2일 | [Azure Service Fabric 7.2 네 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 2021년 1월 25일 | [Azure Service Fabric 7.2 다섯 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 2021년 2월 17일 | [Azure Service Fabric 7.2 여섯 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 2021년 3월 10일 | [Azure Service Fabric 7.2 일곱 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)


### <a name="service-fabric-71"></a>Service Fabric 7.1

현재 COVID-19 위기로 인해 고객이 직면한 문제를 고려하여 7.1을 사용할 수 있게 하지만, 자동 업그레이드를 받도록 설정된 클러스터를 자동으로 업그레이드하지는 않습니다. 예상치 못한 중단을 방지하기 위해 고객에게 가장 적절한 시기에 업그레이드를 적용할 수 있도록 추후 알림이 제공될 때까지 자동 업그레이드를 일시 중지합니다.

[Azure Portal](./service-fabric-cluster-upgrade-version-azure.md#manual-upgrades-with-azure-portal)이나 [Azure Resource Manager 배포](./service-fabric-cluster-upgrade-version-azure.md#resource-manager-template)를 통해 7.1로 업데이트할 수 있습니다.

자동 업그레이드가 사용으로 설정된 Service Fabric 클러스터는 표준 출시 프로시저를 재개하면 7.1 업데이트를 자동으로 받기 시작합니다. [Service Fabric 기술 커뮤니티 사이트](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)에서 표준 출시를 시작하기 전에 다른 알림을 제공합니다.
[여기](./service-fabric-versions.md)에는 6.5부터 7.1까지 주요 릴리스에 대한 지원 종료 날짜 업데이트도 게시되어 있습니다. 

#### <a name="key-announcements"></a>주요 알림

- [**Service Fabric 애플리케이션의 Service Fabric 관리 ID**](./concepts-managed-identity.md)의 **GA**
- [**Ubuntu 18.04 지원**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**미리보기 : 가상 머신 확장 집합 임시 OS 디스크 지원**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)**: 임시 OS 디스크는 로컬 가상 머신에 만든 스토리지이며, 원격 Azure Storage에 저장되지 않습니다. 기존 영구 OS 디스크보다 임시 OS 디스크는 다음과 같으므로 모든 Service Fabric 노드 형식(주 및 보조)에 권장됩니다.
      -  OS 디스크에 대한 읽기/쓰기 대기 시간 단축
      -  다시 설정/이미지로 다시 설치 노드 관리 작업을 더 빠르게 사용하도록 설정
      -  전체 비용 절감(디스크는 무료이며 추가 스토리지 비용이 발생하지 않음)
- [**주체 일반 이름별 Service Fabric 애플리케이션의 서비스 엔드포인트 인증서**](./service-fabric-service-manifest-resources.md) 선언 지원.
- [**컨테이너화된 서비스를 위한 상태 프로브 지원**](./probes-codepackage.md): 컨테이너화된 애플리케이션을 위한 활동성 프로브 메커니즘 지원. 활동성 프로브를 사용하면 컨테이너화된 애플리케이션의 활동성을 알릴 수 있으며, 적시에 응답하지 않으면 다시 시작됩니다. 
- [컨테이너](./service-fabric-containers-overview.md)와 [게스트 실행 파일](./service-fabric-guest-executables-introduction.md) 애플리케이션의 [**이니셜라이저 코드 패키지 지원**](./initializer-codepackages.md). 따라서 지정된 순서로 코드 패키지(예: 컨테이너)를 실행하여 서비스 패키지 초기화를 수행할 수 있습니다.
- **FabricObserver와 ClusterObserver** 는 SF 클러스터의 여러 측면과 관련된 Service Fabric 원격 분석을 캡처하는 상태 비저장 애플리케이션입니다. 이 두 애플리케이션은 모두 ApplicationInsights, EventSource 및 LogAnalytics에 대한 지원을 구현하여 풍부한 원격 분석을 캡처하기 위해 Windows 프로덕션 클러스터에 배포할 준비가 되었습니다.
    - [**FO(FabricObserver) 2.0**](https://github.com/microsoft/service-fabric-observer) - 모든 노드에서 실행되고 상태 이벤트를 생성하며 사용자가 구성한 리소스 사용 임계값에 도달하면 원격 분석을 내보냅니다. 이 릴리스에는 모니터링, 데이터 관리, 상태 이벤트 세부 정보, 구조적 원격 분석 등 여러 가지 향상된 기능이 포함되어 있습니다.
     - [**CO(ClusterObserver) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - 한 노드에서 실행되고 클러스터 수준 상태 원격 분석을 캡처합니다. 이 릴리스에서 ClusterObserver는 노드 상태를 모니터링하고 사용자가 지정한 기간보다 오랫동안 노드가 다운되었거나, 사용하지 않게 설정 중이거나, 사용하지 않게 설정된 상태이면 원격 분석을 내보냅니다.

#### <a name="improve-application-life-cycle-experience"></a>애플리케이션 수명 주기 환경 개선

- **[미리 보기: 요청 드레이닝](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** : 서비스 업그레이드 또는 노드 비활성화와 같은 계획된 서비스 유지 관리 중에 서비스가 연결을 정상적으로 드레이닝할 수 있습니다. 이 기능은 서비스 구성에서 인스턴스 닫기 연기 기간을 추가합니다. 계획된 작업 중에 SF는 검색에서 서비스의 주소를 제거한 다음, 서비스를 종료하기 전에 이 기간을 대기합니다.
- **[자동 하위 클러스터 검색 및 균형 조정](./cluster-resource-manager-subclustering.md)** : 하위 클러스터링은 배치 제약 조건이 서로 다른 서비스에 공통 [로드 메트릭](./service-fabric-cluster-resource-manager-metrics.md)이 있을 때 발생합니다. 여러 다른 노드 세트의 로드가 크게 다른 경우 배치 제약 조건으로 인해 Service Fabric 클러스터 리소스 관리자는 최상의 균형을 유지하더라도 클러스터가 균형이 맞지 않다고 간주합니다. 결과적으로 클러스터의 균형을 다시 맞추고 불필요한 서비스 이동을 유발할 수 있습니다(“불균형”을 크게 개선할 수 없기 때문). 이 릴리스부터 클러스터 리소스 관리자는 이제 해당 종류의 구성을 자동으로 검색하고, 이동을 통해 불균형을 수정할 수 있는 경우와 실질적으로 개선할 수 없으므로 상황을 그대로 두어야 하는 경우를 파악합니다.  
- [**보조 복제본의 다양한 이동 비용**](./service-fabric-cluster-resource-manager-movement-cost.md) : 보조 복제본에 별도의 이동 비용을 사용해야 하는지 정의하기 위해 일부 시나리오에서 추가 유연성을 제공하는 새로운 이동 비용 값 VeryHigh를 도입했습니다.
- 컨테이너화된 애플리케이션의 [**활동성 프로브**](./probes-codepackage.md) 메커니즘을 사용하도록 설정했습니다. 활동성 프로브를 사용하면 컨테이너화된 애플리케이션의 활동성을 알릴 수 있으며, 적시에 응답하지 않으면 다시 시작됩니다.
- [**서비스에 대해 완료될 때까지 실행/한 번 실행**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Image Store 개선 사항
 - Service Fabric 7.1에서는 **기본적으로 노드 간 파일 전송을 보호하기 위해 사용자 지정 전송** 을 사용합니다. SMB 파일 공유에 대한 종속성은 버전 7.1에서 제거됩니다. 보안 SMB 파일 공유는 고객이 기본값에서 옵트아웃하고 업그레이드하며 이전 버전으로 다운그레이드할 수 있도록 Image Store Service 복제본이 포함된 노드에 여전히 있습니다.
       
 #### <a name="reliable-collections-improvements"></a>신뢰할 수 있는 컬렉션 개선 사항

- [**신뢰할 수 있는 컬렉션을 사용한 상태 저장 서비스의 메모리 전용 저장 지원**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections): 신뢰할 수 있는 휘발성 컬렉션을 사용하면 대규모 중단 방지 내구성을 위해 데이터를 디스크에 보관할 수 있으며, 간헐적인 데이터 손실을 허용할 수 있는 복제된 캐시와 같은 워크로드에 사용할 수 있습니다. [신뢰할 수 있는 휘발성 컬렉션 제한 사항](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)에 따라 드물게 발생하는 쿼럼 손실을 처리하는 서비스의 지속성이 필요하지 않은 워크로드에 사용하는 것이 좋습니다.
- [**미리 보기: Service Fabric Backup 탐색기**](https://github.com/microsoft/service-fabric-backup-explorer): Service Fabric 상태 저장 애플리케이션의 신뢰할 수 있는 컬렉션 백업을 쉽게 관리하려면 Service Fabric Backup 탐색기를 사용하여 다음을 수행할 수 있습니다.
    - 신뢰할 수 있는 컬렉션의 콘텐츠를 감사하고 검토
    - 현재 상태를 일관된 보기로 업데이트
    - 신뢰할 수 있는 컬렉션의 현재 스냅샷 백업 만들기
    - 데이터 손상 수정
                 
#### <a name="service-fabric-71-releases"></a>Service Fabric 7.1 릴리스
| 릴리스 날짜 | 해제 | 추가 정보 |
|---|---|---|
| 2020년 4월 20일 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [릴리스 정보](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 2020년 6월 16일 | [Microsoft Azure Service Fabric 7.1 첫 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 2020년 7월 20일 | [Microsoft Azure Service Fabric 7.1 두 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 2020년 8월 12일 | [Microsoft Azure Service Fabric 7.1 세 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 2020년 9월 10일 | [Microsoft Azure Service Fabric 7.1 네 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 2020년 10월 7일 | Microsoft Azure Service Fabric 7.1 여섯 번째 새 릴리스 | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 2020년 11월 23일 | Microsoft Azure Service Fabric 7.1 여덟 번째 새 릴리스 | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7.0

이제 Azure Service Fabric 7.0을 사용할 수 있습니다. Azure Portal이나 Azure Resource Manager 배포를 통해 7.0으로 업데이트할 수 있습니다. 연휴 기간에 받은 릴리스에 관한 고객 피드백으로 인해 자동 업그레이드를 받도록 설정된 클러스터를 1월까지 자동으로 업데이트하지 않습니다.
1월에 표준 출시 프로시저를 재개하며, 자동 업그레이드가 사용으로 설정된 클러스터는 7.0 업데이트를 자동으로 받기 시작합니다. 출시가 시작되기 전에 다른 알림을 제공합니다.
이 정책을 고려하고 있음을 나타내기 위해 계획된 출시 날짜도 업데이트합니다. 향후 [릴리스 일정](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)에 관한 업데이트는 여기를 참조하세요.

#### <a name="key-announcements"></a>주요 알림
 - [**애플리케이션 비밀의 KeyVaultReference 지원(미리 보기)**](./service-fabric-keyvault-references.md): [관리 ID](./concepts-managed-identity.md)가 사용으로 설정된 Service Fabric 애플리케이션은 이제 Key Vault 비밀 URL을 환경 변수, 애플리케이션 매개 변수 또는 컨테이너 리포지토리 자격 증명으로 직접 참조할 수 있습니다. Service Fabric은 애플리케이션의 관리 ID를 사용하여 자동으로 비밀을 확인합니다. 
     
- **상태 비저장 서비스의 업그레이드 안전성 향상**: 애플리케이션 업그레이드 중에 가용성을 보장하기 위해 [상태 비저장 서비스의 최소 인스턴스 수](/dotnet/api/system.fabric.description.statelessservicedescription)를 사용 가능한 것으로 간주하도록 정의하는 새 구성을 도입했습니다. 이전에는 모든 서비스에 대해 이 값이 1이었으며 변경되지 않았습니다. 이 새로운 서비스별 안전 검사를 사용하면 애플리케이션 업그레이드, 클러스터 업그레이드 및 Service Fabric의 상태 및 안전 검사를 사용하는 기타 유지 관리 중에 서비스에서 최소한의 가동 인스턴스를 유지할 수 있습니다.
  
- [**사용자 서비스의 리소스 한도**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): 사용자는 Service Fabric 시스템 서비스의 리소스 고갈과 같은 시나리오를 방지하기 위해 노드의 사용자 서비스에 대한 리소스 한도를 설정할 수 있습니다. 
  
- 복제본 형식의 [**매우 높은 서비스 이동 비용**](./service-fabric-cluster-resource-manager-movement-cost.md). 이동 비용이 매우 높은 복제본은 클러스터에 다른 방법으로 수정할 수 없는 제약 조건 위반이 있는 경우에만 이동됩니다. “매우 높은” 이동 비용을 사용하는 것이 합당한 시기와 추가 고려 사항에 관한 추가 정보는 링크된 문서를 참조하세요.
  
-  **추가 클러스터 안전 검사**: 이 릴리스에서는 구성 가능한 시드 노드 쿼럼 안전 검사를 도입했습니다. 그러면 클러스터 수명 주기 및 관리 시나리오 중에 사용할 수 있어야 하는 시드 노드 수를 사용자 지정할 수 있습니다. 클러스터를 구성된 값 이하로 만드는 작업은 차단됩니다. 현재 기본값은 항상 시드 노드의 쿼럼입니다. 예를 들어, 7개의 시드 노드가 있으면 5개의 시드 노드 이하가 되는 작업은 기본적으로 차단됩니다. 이와 같은 변경으로 최소 안전 값은 6이 되므로, 한 번에 하나의 시드 노드만 다운될 수 있습니다.
   
- [**Service Fabric Explorer에서 백업 및 복원 서비스 관리**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)에 관한 지원이 추가되었습니다. 따라서 SFX 내에서 직접 백업 및 복원 서비스를 검색하고 백업 정책을 만들며 자동 백업을 사용으로 설정하고 임시 백업을 수행하며 복원 작업을 트리거하고 기존 백업을 찾아보는 등의 작업을 수행할 수 있습니다.

- [ **ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)의 가용성 발표: 이 도구를 사용하면 신뢰할 수 있는 컬렉션에 사용되는 형식이 롤링 애플리케이션 업그레이드 중에 현재 버전 및 이전 버전과 호환되는지 유효성을 검사하는 데 유용합니다. 그러면 누락되거나 호환되지 않는 형식으로 인한 업그레이드 실패나 데이터 손실 및 데이터 손상을 방지할 수 있습니다.

- [**보조 복제본에서 안정적인 읽기 사용**](./service-fabric-reliable-services-configuration.md#configuration-names-1): 안정적인 읽기를 사용하면 보조 복제본이 쿼럼 승인된 값을 반환하도록 제한됩니다.

또한 이 릴리스에는 다른 새 기능, 버그 수정, 지원 가능성, 안정성 및 성능 향상이 포함되어 있습니다. 전체 변경 내용 목록은 [릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)를 참조하세요.

#### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 릴리스

| 릴리스 날짜 | 해제 | 추가 정보 |
|---|---|---|
| 2019년 11월 18일 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020년 1월 30일 | [Azure Service Fabric 7.0 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020년 2월 6일 | [Azure Service Fabric 7.0 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020년 3월 2일 | [Azure Service Fabric 7.0 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 2020년 5월 6일 | [Azure Service Fabric 7.0 여섯 번째 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 2020년 10월 9일 | Azure Service Fabric 7.0 아홉 번째 새 릴리스 | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

이 릴리스에는 지원 가능성, 안정성, 성능 향상, 새로운 기능, 버그 수정 및 클러스터와 애플리케이션 수명 주기 관리를 용이하게 하는 개선 사항이 포함되어 있습니다.

> [!IMPORTANT]
> Service Fabric 6.5는 Visual Studio 2015에서 Service Fabric 도구를 지원하는 최종 릴리스입니다. 고객은 향후 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)로 전환하는 것이 좋습니다.

Service Fabric 6.5의 새로운 기능:

- Service Fabric Explorer에는 Image Store에 업로드한 애플리케이션을 검사하기 위한 [Image Store 뷰어](service-fabric-visualizing-your-cluster.md#image-store-viewer)가 포함되어 있습니다.

- [POA(패치 오케스트레이션 애플리케이션)](service-fabric-patch-orchestration-application.md) version [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0)에는 여러 자체 진단 개선 사항이 포함되어 있습니다. POA의 고객은 이 버전으로 전환하는 것이 좋습니다.

- 옵트아웃하지 않으면 Service Fabric 6.5 클러스터에 대해 [기본적으로 EventStore 서비스가 사용됩니다](service-fabric-visualizing-your-cluster.md#event-store).

- 상태 저장 서비스의 [복제본 수명 주기 이벤트](service-fabric-diagnostics-event-generation-operational.md#replica-events)가 추가되었습니다.

- 시드 노드가 비정상인 경우(*다운*, *제거됨* 또는 *알 수 없음*) 클러스터 수준 경고를 포함하여 [시드 노드 상태에 대한 가시성이 향상](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)됩니다.

- [Service Fabric 애플리케이션 재해 복구 도구](https://github.com/Microsoft/Service-Fabric-AppDRTool)를 사용하면 주 클러스터에 재해가 발생할 때 Service Fabric 상태 저장 서비스를 신속하게 복구할 수 있습니다. 주 클러스터의 데이터는 주기적인 백업과 복원을 사용하여 보조 대기 애플리케이션에서 지속해서 동기화됩니다.

- Visual Studio에서는 [Linux 기반 클러스터에 .Net Core 앱을 게시](service-fabric-how-to-publish-linux-app-vs.md)할 수 있습니다.

- [Azure Service Fabric CLI(SFCTL)](./service-fabric-cli.md)는 Azure에서 새 Linux 클러스터를 업그레이드하거나 만들 때 Service Fabric 6.5(및 이후 버전)에 자동으로 설치됩니다.

- [SFCTL](./service-fabric-cli.md)은 기본적으로 MacOS/Linux OneBox 클러스터에 설치됩니다.

자세한 내용은 [Service Fabric 6.5 릴리스 정보](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)를 참조하세요.

#### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 릴리스

| 릴리스 날짜 | 해제 | 추가 정보 |
|---|---|---|
| 2019년 6월 11일 | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019년 7월 2일 | [Azure Service Fabric 6.5 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019년 7월 29일 | [Azure Service Fabric 6.5 새 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure Service Fabric 6.5 새 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019년 10월 14일 | [Azure Service Fabric 6.5 새 릴리스](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [릴리스 정보](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 릴리스

| 릴리스 날짜 | 해제 |
|---|---|
| 2018년 11월 30일 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/) |
| 2018년 12월 12일 | [Windows 클러스터용 Azure Service Fabric 6.4 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2019년 2월 4일 | [Azure Service Fabric 6.4 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2019년 3월 4일 | [Azure Service Fabric 6.4 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2019년 4월 8일 | [Azure Service Fabric 6.4 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2019년 5월 2일 | [Azure Service Fabric 6.4 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
| 2019년 5월 28일 | [Azure Service Fabric 6.4 새 릴리스](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) |
