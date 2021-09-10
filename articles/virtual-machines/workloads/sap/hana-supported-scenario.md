---
title: Azure(대규모 인스턴스)의 SAP HANA 지원 시나리오 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)에 대해 지원되는 시나리오 및 해당 아키텍처 세부 정보를 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/19/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9ebd38cbff7d28515a7f60554a2ef755bdf6d04a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467761"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 큰 인스턴스의 지원되는 시나리오
이 문서에서는 HLI(HANA 대규모 인스턴스)에 대해 지원되는 시나리오 및 아키텍처 세부 정보를 설명합니다.

>[!NOTE]
>시나리오가 이 문서에서 언급되지 않은 경우 Microsoft 서비스 관리 팀에 문의하여 요구 사항을 확인하세요.
HLI 단위를 설정하기 전에 SAP 또는 서비스 구현 파트너와 함께 디자인이 유효한지 검사합니다.

## <a name="terms-and-definitions"></a>용어 및 정의
문서에 사용되는 용어 및 정의를 살펴보겠습니다.

- **SID**: HANA 시스템의 시스템 식별자
- **HLI**: HANA 대규모 인스턴스
- **DR**: 재해 복구
- **일반 DR**: DR 용도로만 사용되는 전용 리소스가 있는 시스템 설정
- **다목적 DR**: DR 이벤트에 대해 구성된 프로덕션 인스턴스와 함께 비프로덕션 환경을 사용하도록 구성된 DR 사이트 시스템 
- **단일 SID**: 하나의 인스턴스가 설치된 시스템
- **다중 SID**: 여러 인스턴스가 구성된 시스템으로, MCOS 환경이라고도 함
- **HSR**: SAP HANA 시스템 복제

## <a name="overview"></a>개요
HANA 대규모 인스턴스는 사용자가 비즈니스 요구 사항을 충족하는 데 도움이 되는 다양한 아키텍처를 지원합니다. 다음 섹션에서는 아키텍처 시나리오 및 구성 세부 정보를 다룹니다. 

파생되는 아키텍처 디자인은 전적으로 인프라 관점에서 나옵니다. HANA 배포에 대해서는 SAP 또는 구현 파트너에게 문의하세요. 자신의 시나리오가 이 문서에 나와 있지 않은 경우 Microsoft 계정 팀에 문의하여 아키텍처를 검토하고 자신에게 맞는 솔루션을 구하세요.

> [!NOTE]
> 이러한 아키텍처는 TDI(조정된 데이터 통합) 디자인을 완벽하게 따르며 SAP에서 지원됩니다.

이 문서에서는 지원되는 각 아키텍처의 두 구성 요소에 대해 자세히 설명합니다.

- 이더넷
- 스토리지

### <a name="ethernet"></a>이더넷

프로비전된 각 서버는 이더넷 인터페이스 집합으로 미리 구성됩니다. 각 HLI 단위에 구성된 이더넷 인터페이스는 다음과 같은 네 가지 유형으로 분류됩니다.

- **A**: 클라이언트 액세스에 사용되거나 클라이언트 액세스에 의해 사용됩니다.
- **B**: 노드 간 통신에 사용됩니다. 이 인터페이스는 요청하는 토폴로지에 관계없이 모든 서버에서 구성됩니다. 그러나 스케일 아웃 시나리오에만 사용됩니다.
- **C**: 노드와 스토리지 간 연결에 사용됩니다.
- **D**: STONITH 설정을 위한 노드와 iSCSI 디바이스 간 연결에 사용됩니다. 이 인터페이스는 HSR 설정이 요청된 경우에만 구성됩니다.  

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간|
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간|
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

HLI 단위에 구성된 토폴로지에 따라 인터페이스를 선택합니다. 예를 들어, 인터페이스 "B"는 스케일 아웃 토폴로지를 구성한 경우에 유용한 노드 간 통신을 위해 설정됩니다. 이 인터페이스는 단일 노드 스케일 업 구성에 사용되지 않습니다. 인터페이스 사용에 대한 자세한 내용은 이 문서 뒷부분에서 필요한 시나리오를 검토하세요. 

필요한 경우 추가 NIC 카드를 직접 정의할 수 있습니다. 그러나 기존 NIC의 구성은 변경할 수 ‘없습니다’.

>[!NOTE]
>실제 인터페이스 또는 결합에 해당하는 추가 인터페이스를 찾을 수도 있습니다. 사용 사례의 경우에는 앞에서 설명한 인터페이스만 고려해야 합니다. 다른 항목은 모두 무시하세요.

할당된 두 IP 주소가 있는 단위에 대한 배포는 다음과 같습니다.

- 이더넷 “A”에는 Microsoft에 제출한 서버 IP 풀 주소 범위 내에서 할당된 IP 주소가 있어야 합니다. 이 IP 주소는 OS(운영 체제)의 */etc/hosts* 디렉터리에서 유지 관리해야 합니다.

- 이더넷 “C”에는 NFS와의 통신에 사용되는 할당된 IP 주소가 있어야 합니다. 테넌트 내에서 인스턴스 간 트래픽을 허용하기 위해 *etc/hosts* 디렉터리에서 이 주소를 유지 관리할 필요가 *없습니다*.

HANA 시스템 복제 또는 HANA 스케일 아웃 배포의 경우 두 개의 할당된 IP 주소가 있는 블레이드 구성은 적합하지 않습니다. 할당된 IP 주소가 두 개만 있고 이러한 구성을 배포하려면 Azure 서비스 관리에서 SAP HANA에 문의하세요. 세 번째 VLAN의 세 번째 IP 주소를 할당할 수 있습니다. 세 개의 NIC 포트에 세 개의 할당된 IP 주소가 있는 HANA 대규모 인스턴스에는 다음 사용 규칙이 적용됩니다.

- 이더넷 “A”에는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 할당된 IP 주소가 있어야 합니다. 이 IP 주소는 OS의 *etc/hosts* 디렉터리에서 유지 관리하면 안 됩니다.

- 이더넷 “B”는 여러 인스턴스 간의 통신을 위해 *etc/hosts* 디렉터리에서 단독으로 유지 관리해야 합니다. HANA가 노드 간 구성에 사용하는 IP 주소로 스케일 아웃 HANA 구성에서 이러한 IP 주소를 유지 관리합니다.

- 이더넷 “C”에는 NFS 스토리지와의 통신에 사용되는 할당된 IP 주소가 있어야 합니다. 이 유형의 주소는 *etc/hosts* 디렉터리에서 유지 관리하면 안 됩니다.

- 이더넷 “D”는 Pacemaker용 STONITH 디바이스에 액세스하는 데만 사용해야 합니다. 이 인터페이스는 HANA 시스템 복제를 구성하고 SBD 기반 디바이스를 사용하여 운영 체제에서 자동 장애 조치(Failover)를 수행하려고 할 때 필요합니다.


### <a name="storage"></a>스토리지
스토리지는 요청된 토폴로지에 따라 미리 구성됩니다. 볼륨 크기와 탑재 지점은 서버 및 SKU 수, 구성된 토폴로지에 따라 달라집니다. 자세한 내용은 이 문서 뒷부분에서 필요한 시나리오를 검토하세요. 스토리지가 더 필요한 경우 1TB 단위로 구매할 수 있습니다.

>[!NOTE]
>탑재 지점 /usr/sap/\<SID>는 /hana/shared 탑재 지점의 바로 가기 링크입니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

다음 섹션의 아키텍처 다이어그램은 아래와 같은 표기법을 사용합니다.

[ ![아키텍처 테이블 다이어그램](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

지원되는 시나리오는 다음과 같습니다.

* SID가 1개인 단일 노드
* 단일 노드 MCOS
* DR(일반)이 포함된 단일 노드
* DR(다목적)이 포함된 단일 노드
* STONITH가 있는 HSR
* DR(일반/다목적)이 있는 HSR 
* 호스트 자동 장애 조치(Failover)(1+1) 
* 대기 상태로 스케일 아웃
* 대기 없이 스케일 아웃
* DR을 사용하여 스케일 아웃

## <a name="single-node-with-one-sid"></a>SID가 1개인 단일 노드

이 토폴로지는 단일 SID가 있는 강화 구성의 단일 노드를 지원합니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![SID가 1개인 단일 노드](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | HANA 설치 | 
|/hana/data/SID/mnt00001 | 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 로그 파일 설치 | 
|/hana/logbackups/SID | 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.

## <a name="single-node-mcos"></a>단일 노드 MCOS

이 토폴로지는 여러 SID가 있는 강화 구성의 단일 노드를 지원합니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![단일 노드 MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID1 | SID1의 HANA 설치 | 
|/hana/data/SID1/mnt00001 | SID1의 데이터 파일 설치 | 
|/hana/log/SID1/mnt00001 | SID1의 로그 파일 설치 | 
|/hana/logbackups/SID1 | SID1의 다시 실행 로그 |
|/hana/shared/SID2 | SID2의 HANA 설치 | 
|/hana/data/SID2/mnt00001 | SID2의 데이터 파일 설치 | 
|/hana/log/SID2/mnt00001 | SID2의 로그 파일 설치 | 
|/hana/logbackups/SID2 | SID2의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보려면 [개요 및 아키텍처](./hana-overview-architecture.md)를 참조하세요.

## <a name="single-node-with-dr-using-storage-replication"></a>스토리지 복제를 사용하는 DR이 포함된 단일 노드
 
이 토폴로지는 하나 이상의 SID가 있는 강화 구성의 단일 노드를 지원합니다. DR 사이트에 대한 스토리지 기반 복제는 기본 SID에 사용됩니다. 다이어그램에서는 단일 SID 시스템만 기본 사이트에 표시되지만 MCOS 시스템도 지원됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![스토리지 복제를 사용하는 DR이 포함된 단일 노드](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | SID의 로그 파일 설치 | 
|/hana/logbackups/SID | SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보려면 [개요 및 아키텍처](./hana-overview-architecture.md)를 참조하세요.
- DR 사이트: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("HANA 설치에 필요"로 표시). 
- DR 사이트: 데이터, 로그 백업 및 공유 볼륨(“스토리지 복제”로 표시)이 프로덕션 사이트의 스냅샷을 통해 복제됩니다. 이러한 볼륨은 장애 조치(failover) 중에만 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치(failover) 프로시저](./hana-overview-high-availability-disaster-recovery.md)를 참조하세요.
- *SKU 유형 I 클래스* 에 대한 부팅 볼륨이 DR 노드에 복제됩니다.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>스토리지 복제를 사용하는 DR(다목적)이 포함된 단일 노드
 
이 토폴로지는 하나 이상의 SID가 있는 강화 구성의 단일 노드를 지원합니다. DR 사이트에 대한 스토리지 기반 복제는 기본 SID에 사용됩니다. 

다이어그램에서는 단일 SID 시스템만 기본 사이트에 표시되지만 다중 SID(MCOS) 시스템도 지원됩니다. DR 사이트에서 HLI 단위는 QA 인스턴스에 사용됩니다. 프로덕션 작업은 기본 사이트에서 실행됩니다. DR 장애 조치(또는 failover 테스트)를 수행하는 동안에는 DR 사이트의 QA 인스턴스가 제거됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![스토리지 복제를 사용하는 DR(다목적)이 포함된 단일 노드](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/shared/QA-SID | QA SID의 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID의 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID의 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보려면 [개요 및 아키텍처](./hana-overview-architecture.md)를 참조하세요.
- DR 사이트: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("HANA 설치에 필요"로 표시). 
- DR 사이트: 데이터, 로그 백업 및 공유 볼륨(“스토리지 복제”로 표시)이 프로덕션 사이트의 스냅샷을 통해 복제됩니다. 이러한 볼륨은 장애 조치(failover) 중에만 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치(failover) 프로시저](./hana-overview-high-availability-disaster-recovery.md)를 참조하세요. 
- DR 사이트: QA("QA 인스턴스 설치"로 표시)에 대한 데이터, 로그 백업, 로그 및 공유 볼륨이 QA 인스턴스 설치에 대해 구성됩니다.
- *SKU 유형 I 클래스* 에 대한 부팅 볼륨이 DR 노드에 복제됩니다.

## <a name="hsr-with-stonith-for-high-availability"></a>고가용성을 위한 STONITH가 있는 HSR
 
이 토폴로지는 HANA 시스템 복제 구성에 대해 2개의 노드를 지원합니다. 이 구성은 노드의 단일 HANA 인스턴스에 대해서만 지원됩니다. MCOS 시나리오는 지원되지 *않습니다*.

> [!NOTE]
> 2019년 12월부터 이 아키텍처는 SUSE 운영 체제에 대해서만 지원됩니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![고가용성을 위한 STONITH가 있는 HSR](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH에 사용 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH에 사용 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**보조 노드**|
|/hana/shared/SID | 보조 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 보조 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 보조 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 보조 SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보려면 [개요 및 아키텍처](./hana-overview-architecture.md)를 참조하세요.
- STONITH: STONITH 설치에 대해 SBD가 구성됩니다. 그러나 STONITH의 사용은 선택 사항입니다.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>스토리지 복제가 포함된 HSR 및 DR을 사용한 고가용성
 
이 토폴로지는 HANA 시스템 복제 구성에 대해 2개의 노드를 지원합니다. 일반 DR과 다목적 DR이 모두 지원됩니다. 이러한 구성은 노드의 단일 HANA 인스턴스에 대해서만 지원됩니다. MCOS 시나리오는 이러한 구성에서 지원되지 *않습니다*.

다이어그램에서 다목적 시나리오는 DR 사이트에 표시되며, 여기서 HLI 단위는 QA 인스턴스에 사용됩니다. 프로덕션 작업은 기본 사이트에서 실행됩니다. DR 장애 조치(또는 failover 테스트)를 수행하는 동안에는 DR 사이트의 QA 인스턴스가 제거됩니다. 

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![스토리지 복제가 포함된 HSR 및 DR을 사용한 고가용성](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH에 사용 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH에 사용 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트의 주 노드**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**기본 사이트의 보조 노드**|
|/hana/shared/SID | 보조 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 보조 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 보조 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 보조 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/shared/QA-SID | QA SID의 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID의 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID의 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보려면 [개요 및 아키텍처](./hana-overview-architecture.md)를 참조하세요.
- STONITH: STONITH 설치에 대해 SBD가 구성됩니다. 그러나 STONITH의 사용은 선택 사항입니다.
- DR 사이트: 주 노드 복제 및 보조 노드 복제에 *2개의 스토리지 볼륨 세트가 필요* 합니다.
- DR 사이트: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("HANA 설치에 필요"로 표시). 
- DR 사이트: 데이터, 로그 백업 및 공유 볼륨(“스토리지 복제”로 표시)이 프로덕션 사이트의 스냅샷을 통해 복제됩니다. 이러한 볼륨은 장애 조치(failover) 중에만 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치(failover) 프로시저](./hana-overview-high-availability-disaster-recovery.md)를 참조하세요. 
- DR 사이트: QA("QA 인스턴스 설치"로 표시)에 대한 데이터, 로그 백업, 로그 및 공유 볼륨이 QA 인스턴스 설치에 대해 구성됩니다.
- *SKU 유형 I 클래스* 에 대한 부팅 볼륨이 DR 노드에 복제됩니다.


## <a name="host-auto-failover-11"></a>호스트 자동 장애 조치(Failover)(1+1)
 
이 토폴로지는 호스트 자동 장애 조치(Failover) 구성의 두 노드를 지원합니다. 기본/작업자 역할의 노드 하나와 대기용으로 추가 노드가 있습니다. *SAP는 S/4 HANA에 대해서만 이 시나리오를 지원합니다.* 자세한 내용은 [OSS 참고 사항 2408419 - SAP S/4HANA - 다중 노드 지원](https://launchpad.support.sap.com/#/notes/2408419)을 참조하세요.



### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![호스트 자동 장애 조치(Failover)(1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 및 대기 노드**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |



### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- 대기: 볼륨 및 탑재 지점이 대기 단위의 HANA 인스턴스 설치에 대해 구성됩니다("HANA 설치에 필요"로 표시).
 

## <a name="scale-out-with-standby"></a>대기 상태로 스케일 아웃
 
이 토폴로지는 스케일 아웃 구성의 여러 노드를 지원합니다. 기본 역할의 노드 1개, 작업자 역할의 노드 1개 이상 및 대기용 노드 1개 이상이 있습니다. 그러나 기본 노드는 한 번에 하나만 있을 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![대기 상태로 스케일 아웃](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 노드, 작업자 노드 및 대기 노드에서**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


## <a name="scale-out-without-standby"></a>대기 없이 스케일 아웃
 
이 토폴로지는 스케일 아웃 구성의 여러 노드를 지원합니다. 기본 역할의 노드 1개, 작업자 역할의 노드 1개 이상이 있습니다. 그러나 기본 노드는 한 번에 하나만 있을 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![대기 없이 스케일 아웃](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 노드 및 작업자 노드에서**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.

## <a name="scale-out-with-dr-using-storage-replication"></a>스토리지 복제를 사용하는 DR이 있는 스케일 아웃
 
이 토폴로지는 DR이 있는 스케일 아웃의 여러 노드를 지원합니다. 일반 DR과 다목적 DR이 모두 지원됩니다. 이 다이어그램에는 단일 용도 DR만 표시됩니다. 대기 노드가 있거나 없는 상태로 이 토폴로지를 요청할 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![스토리지 복제를 사용하는 DR이 있는 스케일 아웃](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 노드**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
-  DR 사이트: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("HANA 설치에 필요"로 표시). 
- DR 사이트: 데이터, 로그 백업 및 공유 볼륨(“스토리지 복제”로 표시)이 프로덕션 사이트의 스냅샷을 통해 복제됩니다. 이러한 볼륨은 장애 조치(failover) 중에만 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치(failover) 프로시저](./hana-overview-high-availability-disaster-recovery.md)를 참조하세요. 
- *SKU 유형 I 클래스* 에 대한 부팅 볼륨이 DR 노드에 복제됩니다.


## <a name="single-node-with-dr-using-hsr"></a>HSR를 사용하는 DR이 포함된 단일 노드
 
이 토폴로지는 기본 SID에 대한 DR 사이트로의 HANA 시스템 복제를 포함하는 하나의 SID가 있는 스케일 업 구성의 단일 노드를 지원합니다. 다이어그램에서는 단일 SID 시스템만 기본 사이트에 표시되지만 다중 SID(MCOS) 시스템도 지원됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR를 사용하는 DR이 포함된 단일 노드](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI/HSR 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI/HSR 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 두 HLI 단위(기본 및 DR)에서 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | SID의 로그 파일 설치 | 
|/hana/logbackups/SID | SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보려면 [개요 및 아키텍처](./hana-overview-architecture.md)를 참조하세요.
- 기본 노드는 HANA 시스템 복제를 사용하여 DR 노드와 동기화됩니다. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md)는 ExpressRoute 회로를 함께 연결하여 지역 네트워크 간에 프라이빗 네트워크를 설정하는 데 사용됩니다.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>단일 노드 HSR에서 DR로(비용 최적화) 
 
 이 토폴로지는 단일 SID가 있는 강화 구성의 단일 노드를 지원합니다. DR 사이트에 대한 HANA 시스템 복제는 기본 SID에 사용됩니다. 다이어그램에서는 단일 SID 시스템만 기본 사이트에 표시되지만 다중 SID(MCOS) 시스템도 지원됩니다. DR 사이트에서 HLI 단위는 QA 인스턴스에 사용됩니다. 프로덕션 작업은 기본 사이트에서 실행됩니다. DR 장애 조치(또는 failover 테스트)를 수행하는 동안에는 DR 사이트의 QA 인스턴스가 제거됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![단일 노드 HSR에서 DR로(비용 최적화)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI/HSR 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI/HSR 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|/hana/shared/QA-SID | QA SID의 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID의 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID의 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보려면 [개요 및 아키텍처](./hana-overview-architecture.md)를 참조하세요.
- DR 사이트: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("DR 사이트의 PROD 인스턴스"로 표시). 
- DR 사이트: QA("QA 인스턴스 설치"로 표시)에 대한 데이터, 로그 백업, 로그 및 공유 볼륨이 QA 인스턴스 설치에 대해 구성됩니다.
- 기본 노드는 HANA 시스템 복제를 사용하여 DR 노드와 동기화됩니다. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md)는 ExpressRoute 회로를 함께 연결하여 지역 네트워크 간에 프라이빗 네트워크를 설정하는 데 사용됩니다.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>HSR을 사용한 고가용성 및 재해 복구 
 
 이 토폴로지는 로컬 지역의 고가용성을 위한 HANA 시스템 복제 구성에 대해 두 개의 노드를 지원합니다. DR의 경우 DR 지역의 세 번째 노드는 HSR(비동기 모드)을 사용하여 기본 사이트와 동기화됩니다. 

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR을 사용한 고가용성 및 재해 복구](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI/HSR 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI/HSR 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- DR 사이트: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("PROD DR 인스턴스"로 표시). 
- 기본 사이트 노드는 HANA 시스템 복제를 사용하여 DR 노드와 동기화됩니다. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md)는 ExpressRoute 회로를 함께 연결하여 지역 네트워크 간에 프라이빗 네트워크를 설정하는 데 사용됩니다.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>HSR을 사용한 고가용성 및 재해 복구(비용 최적화)
 
 이 토폴로지는 로컬 지역의 고가용성을 위한 HANA 시스템 복제 구성에 대해 두 개의 노드를 지원합니다. DR의 경우 DR 지역의 세 번째 노드는 HSR(비동기 모드)을 사용하여 기본 사이트와 동기화되지만 다른 인스턴스(예: QA)는 DR 노드에서 이미 실행되고 있습니다. 

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR을 사용한 고가용성 및 재해 복구(비용 최적화)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI/HSR 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI/HSR 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|/hana/shared/QA-SID | QA SID의 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID의 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID의 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- DR 사이트: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("PROD DR 인스턴스"로 표시). 
- DR 사이트: QA("QA 인스턴스 설치"로 표시)에 대한 데이터, 로그 백업, 로그 및 공유 볼륨이 QA 인스턴스 설치에 대해 구성됩니다.
- 기본 사이트 노드는 HANA 시스템 복제를 사용하여 DR 노드와 동기화됩니다. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md)는 ExpressRoute 회로를 함께 연결하여 지역 네트워크 간에 프라이빗 네트워크를 설정하는 데 사용됩니다.

## <a name="scale-out-with-dr-using-hsr"></a>HSR를 사용하는 DR이 있는 스케일 아웃
 
이 토폴로지는 DR이 있는 스케일 아웃의 여러 노드를 지원합니다. 대기 노드가 있거나 없는 상태로 이 토폴로지를 요청할 수 있습니다. 기본 사이트 노드는 HANA 시스템 복제(비동기 모드)를 사용하여 DR 사이트 노드와 동기화됩니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

[ ![HSR를 사용하는 DR이 있는 스케일 아웃](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트와 HLI/HSR 간 |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드와 스토리지 간 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트와 HLI/HSR 간 |
| b | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드와 스토리지 간 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 노드**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- DR 사이트: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다. 
- 기본 사이트 노드는 HANA 시스템 복제를 사용하여 DR 노드와 동기화됩니다. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md)는 ExpressRoute 회로를 함께 연결하여 지역 네트워크 간에 프라이빗 네트워크를 설정하는 데 사용됩니다.


## <a name="next-steps"></a>다음 단계

HANA 대규모 인스턴스 배포에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [SAP HANA(대규모 인스턴스) 배포](./hana-overview-infrastructure-connectivity.md)
