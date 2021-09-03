---
title: Azure(큰 인스턴스)의 SAP HANA에서 HANA 백업 및 복원 | Microsoft Docs
description: HANA(대규모 인스턴스)에서 SAP HANA를 백업하고 복원하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/02/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b60ba5c169f660cd01f95f53fbc292a768f1b5e
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358942"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>HANA 대규모 인스턴스에서 SAP HANA 백업 및 복원

>[!IMPORTANT]
>이 문서는 SAP HANA 관리 설명서나 SAP Note를 대체하지 않습니다. 이 문서에서는 사용자가 SAP HANA 관리 및 작업 중에서도 특히 백업, 복원, 고가용성 및 재해 복구 토픽에 대해 전문 지식을 가졌다고 가정합니다. 이 문서에서는 SAP HANA Studio의 스크린샷이 표시됩니다. SAP 관리 도구 스크린의 콘텐츠, 구조 및 특성과 도구 자체는 릴리스할 SAP HANA 릴리스마다 변경될 수 있습니다.

이 문서에서는 HANA(대규모 인스턴스)(BareMetal Infrastructure라고도 함)에서 SAP HANA를 백업하고 복원하는 단계를 안내합니다. 

이 문서에 설명된 프로세스 중 일부가 간소화되었습니다. 자세하지 않으므로 작업 핸드북에 포함되지는 않습니다. 구성에 대한 작업 핸드북을 만들려면 특정 HANA 버전 및 릴리스를 사용하여 프로세스를 실행하고 테스트합니다. 그런 다음, 구성 프로세스를 문서화할 수 있습니다.

운영 데이터베이스에서 가장 중요한 측면 중 하나는 치명적인 이벤트로부터 해당 데이터베이스를 보호하는 것입니다. 자연재해부터 간단한 사용자 오류까지 다양한 원인으로 인해 관련 이벤트가 발생할 수 있습니다. 데이터베이스를 백업하면 누군가 중요한 데이터를 삭제하기 전과 같은 특정 시점으로 복원하는 기능을 통해 중요한 보호가 제공됩니다. 데이터베이스를 중단 전에 있었던 방식과 최대한 가까운 상태로 복원할 수 있습니다.

이러한 복원 기능을 구현하려면 다음과 같은 두 가지 유형의 백업을 수행해야 합니다.

- 데이터베이스 백업: 전체, 증분 또는 차등 백업
- 트랜잭션 로그 백업

애플리케이션 수준에서 전체 데이터베이스 백업을 수행하거나 스토리지 스냅샷을 사용하여 백업을 수행할 수 있습니다. 스토리지 스냅샷은 트랜잭션 로그 백업을 대체하지 않습니다. 트랜잭션 로그 백업은 데이터베이스를 특정 시점으로 복원하거나 이미 커밋된 트랜잭션에서 로그를 비우는 데 중요합니다. 스토리지 스냅샷은 데이터베이스의 롤포워드 이미지를 신속하게 제공하여 복구를 가속화할 수 있습니다. 

Azure(큰 인스턴스)의 SAP HANA는 두 가지 백업 및 복원 옵션을 제공합니다.

- 타사 데이터 보호 도구를 사용하여 백업을 만들 수 있습니다. 이 도구는 애플리케이션 일치 스냅샷을 만들 수 있거나, 지원 인터페이스를 사용하여 여러 세션을 사용하여 적절한 백업 위치로 스트림할 수 있어야 합니다. 사용 가능한 지원되는 여러 가지 도구는 다음과 같습니다. 고객 백업 기간 요구 사항을 충족하기 위해 프로젝트 팀과 도구 선택을 논의하고 계획해야 합니다. 그리고 프로젝트 단계 중에 백업 및 복원 절차를 테스트하는 것이 매우 중요합니다.
- 다음 챕터에 설명된 대로 Microsoft에서 제공하는 유틸리티를 통해 스토리지 스냅샷 백업을 사용할 수 있습니다.

>[!NOTE]
>HANA 2.0 SPS4 이전에는 다중 테넌트 데이터베이스 컨테이너 데이터베이스(테넌트 2개 이상)의 데이터베이스 스냅샷을 생성하는 데 해당 유틸리티가 지원되지 않았습니다.
SPS4 이상에서는 SAP가 이 스냅샷 기능을 완벽하게 지원합니다.



## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Azure(대규모 인스턴스)의 SAP HANA 스토리지 스냅샷 사용

Azure(큰 인스턴스)의 SAP HANA에 기반한 스토리지 인프라는 볼륨의 스토리지 스냅샷을 지원합니다. 볼륨의 백업 및 복원은 다음 사항을 고려하여 지원됩니다.

- 전체 데이터베이스 백업 대신 스토리지 볼륨 스냅샷을 자주 사용합니다.
- 스토리지 스냅샷이 /hana/data 볼륨을 통해 트리거되기 전에 스냅샷 도구(azacsnap)가 SAP HANA 스냅샷을 시작합니다. 이 SAP HANA 스냅샷은 스토리지 스냅샷을 복구한 후 최종 로그 복원을 위한 일관성 지점입니다. 
- HANA 스냅샷이 성공적이려면 활성 HANA 인스턴스가 필요합니다. HSR(HANA System Replication)을 사용한 시나리오에서 스토리지 스냅샷은 HANA 스냅샷을 수행할 수 없는 현재 보조 노드에서 지원되지 않습니다.
- 스토리지 스냅샷이 성공적으로 실행된 후 SAP HANA 스냅샷이 삭제됩니다.
- /hana/shared(/usr/sap 포함)와 같은 다른 볼륨은 데이터베이스 상호 작용 없이 언제든지 스냅샷으로 생성될 수 있습니다.

트랜잭션 로그 백업은 자주 사용되며 /hana/logbackups 볼륨 또는 Azure에 저장됩니다. 트랜잭션 로그 백업을 포함하는 /hana/logbackups 볼륨을 트리거하여 별도의 스냅샷을 생성할 수 있습니다. 이 경우 HANA 스냅샷을 실행할 필요가 없습니다. /hana/logbackup의 모든 파일이 “오프라인” 상태여서 일관되므로 언제든지 해당 파일을 다른 백업 위치에 백업하여 보관할 수도 있습니다.
데이터베이스를 특정 시점으로 복원해야 하는 경우 프로덕션 중단에 대비해서 azacsnap 도구는 데이터 스냅샷을 새 볼륨으로 복제하여 데이터베이스를 복구(기본 복원 방법)하거나 데이터베이스가 위치한 동일한 데이터 볼륨으로 스냅샷을 복원할 수 있습니다.

>[!NOTE]
> 이전 스냅샷(snaprevert)을 원래 데이터 볼륨으로 복원하면 새로 만든 스냅샷이 모두 삭제됩니다. 새로 만든 스냅샷의 데이터 포인트가 잘못될 것이므로 스토리지 시스템이 이 작업을 수행합니다. 항상 먼저 최신 스냅샷을 되돌리거나 스냅샷을 새 볼륨으로 훨씬 더 잘 복제합니다. 복제 프로세스에서는 아무것도 삭제되지 않습니다.


## <a name="storage-snapshot-considerations"></a>스토리지 스냅샷 고려 사항

>[!NOTE]
>스토리지 스냅샷은 HANA 대규모 인스턴스 단위에 할당된 스토리지 공간을 사용합니다. 스토리지 스냅샷을 예약하는 경우 다음과 같은 측면과 유지할 스토리지 스냅샷의 수를 고려해야 합니다. 

Azure(큰 인스턴스)의 SAP HANA에 대한 스토리지 스냅샷의 특정 역학에는 다음이 포함됩니다.

- 만들어진 시점에서 특정 스토리지 스냅샷은 스토리지를 적게 사용합니다.
- 데이터 콘텐츠가 변경되고 스토리지 볼륨에서 SAP HANA 데이터 파일의 콘텐츠가 변경되면 스냅샷은 원래 블록 콘텐츠와 데이터 변경 내용을 저장해야 합니다.
- 결과적으로 스토리지 스냅샷의 크기가 증가합니다. 스냅샷이 오래 존재할수록 스토리지 스냅샷은 커집니다.
- 스토리지 스냅샷의 수명이 흐르고 스토리지 스냅샷의 공간 소비가 증가함에 따라 SAP HANA 데이터베이스 볼륨은 더 이상 변경되지 않습니다.

Azure(큰 인스턴스)의 SAP HANA는 SAP HANA 데이터 및 로그 볼륨에 대해 고정된 볼륨 크기로 제공됩니다. 해당 볼륨의 스냅샷을 생성하면 볼륨 공간이 줄어듭니다. 그러려면 다음 작업을 수행해야 합니다.

- 스토리지 스냅샷을 예약할 시기를 결정합니다.
- 스토리지 볼륨의 공간 사용을 모니터링합니다. 
- 저장하는 스냅샷 수를 관리합니다. 

대량의 데이터를 가져오거나 HANA 데이터베이스에 대해 다른 중요한 변경을 수행할 때 스토리지 스냅샷을 사용하지 않도록 설정할 수 있습니다. 


다음 섹션에서는 일반적인 권장 사항을 포함하여 이 스냅샷을 생성하는 방법에 관한 정보를 제공합니다.

- 하드웨어가 볼륨당 255개의 스냅샷을 유지할 수 있지만 이 숫자보다 낮게 유지하는 것이 좋습니다. 권장 구성은 250개 이하입니다.
- 스토리지 스냅샷을 수행하기 전에 여유 공간을 모니터링하고 추적합니다.
- 사용 가능한 공간에 따라 스토리지 스냅샷의 수를 줄입니다. 보관할 스냅샷의 수를 줄이거나 볼륨을 확장할 수 있습니다. 추가 스토리지는 1테라바이트 단위로 주문할 수 있습니다.
- SAP 플랫폼 마이그레이션 도구(R3load)를 사용하여 데이터를 SAP HANA로 이동하거나 백업에서 SAP HANA에 데이터를 복원하는 것과 같은 작업 중에는 /hana/data 볼륨에 대해 스토리지 스냅샷을 사용하지 않도록 설정합니다. 
- SAP HANA 테이블을 더 크게 재구성하는 동안 되도록이면 스토리지 스냅샷을 사용하지 말아야 합니다.
- 스토리지 스냅샷은 Azure의 SAP HANA(대규모 인스턴스)에 대한 DR 기능을 활용하기 위한 필수 조건입니다.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>셀프 서비스 스토리지 스냅샷을 사용하기 위한 필수 구성 요소

[Azure 애플리케이션 일치 스냅샷 도구란?](../../../azure-netapp-files/azacsnap-introduction.md) 설명서를 참조하세요.

이 도구를 구현하는 두 가지 방법이 있습니다. 
1)  데이터베이스 서버에서 로컬로
2)  “백업” VM에서 원격으로

백업 VM을 만드는 경우 최신 HANA 클라이언트가 해당 VM에 설치되어 있는지 확인합니다. 이 방법을 통해 azacsnap은 다른 VM에서 실행되는 HANA 인스턴스에 대한 원격 데이터베이스 연결을 열 수 있어야 합니다.
스토리지에 액세스할 수 있도록 Microsoft 지원 팀에서 SSH 키와 스토리지 사용자를 요청해야 합니다. 이 SSH 키와 사용자가 없으면 스냅샷을 만들 수 없습니다.

## <a name="download-and-set-up-azacsnap"></a>azacsnap 다운로드 및 설정

HANA(대규모 인스턴스)를 사용하여 스토리지 스냅샷을 설정하려면 먼저 [Azure 애플리케이션 일치 스냅샷 도구 시작](../../../azure-netapp-files/azacsnap-get-started.md)에 설명된 대로 azacsnap을 다운로드하고 설치합니다.

Azacsnap은 기본적으로 azacsnap이라는 사용자를 만듭니다. 다른 이름을 선호하는 경우 설치하는 동안 이 이름을 지정할 수 있습니다. 자세한 내용은 위 설명서를 참조하세요.

## <a name="subsequent-next-steps"></a>후속 다음 단계
Azacsnap의 설명서에 따라 다음을 수행합니다.

- [Azure Application Consistent Snapshot 도구 설치](../../../azure-netapp-files/azacsnap-installation.md)
- [Azure 애플리케이션 일치 스냅샷 도구 구성](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Azure Application Consistent Snapshot Tool 테스트](../../../azure-netapp-files/azacsnap-cmd-ref-test.md)
- [Azure Application Consistent Snapshot Tool을 사용한 백업](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)
- [Azure Application Consistent Snapshot 도구로 세부 정보 가져오기](../../../azure-netapp-files/azacsnap-cmd-ref-details.md)
- [Azure Application Consistent Snapshot 도구를 사용하여 삭제](../../../azure-netapp-files/azacsnap-cmd-ref-delete.md)
- [Azure Application Consistent Snapshot 도구를 사용하여 복원](../../../azure-netapp-files/azacsnap-cmd-ref-restore.md)
- [Azure 애플리케이션 일치 스냅샷 도구를 사용하여 재해 복구](../../../azure-netapp-files/azacsnap-disaster-recovery.md)
- [Azure 애플리케이션 일치 스냅샷 도구 문제 해결](../../../azure-netapp-files/azacsnap-troubleshoot.md)
- [Azure 애플리케이션 일치 스냅숏 도구 사용을 위한 팁과 요령](../../../azure-netapp-files/azacsnap-tips.md)


## <a name="next-steps"></a>다음 단계

[Azure 애플리케이션 일치 스냅샷 도구란?](../../../azure-netapp-files/azacsnap-introduction.md) 문서를 참조하세요.

  



