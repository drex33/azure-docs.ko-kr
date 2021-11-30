---
title: 응용 프로그램 볼륨 그룹 Azure NetApp Files에 대 한 Faq | Microsoft Docs
description: 응용 프로그램 볼륨 그룹 Azure NetApp Files에 대 한 Faq (질문과 대답)입니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 11/19/2021
ms.openlocfilehash: a855aff8d54f953bc8bd73933189a71b6732699a
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271958"
---
# <a name="application-volume-group-faqs"></a>응용 프로그램 볼륨 그룹 Faq

이 문서에서는 Azure NetApp Files 응용 프로그램 볼륨 그룹에 대 한 Faq (질문과 대답)에 답변 합니다. 

## <a name="why-do-i-have-to-use-a-manual-qos-capacity-pool-for-all-of-my-volumes"></a>모든 볼륨에 수동 QoS 용량 풀을 사용 해야 하는 이유는 무엇 인가요?

수동 QoS 용량 풀은 SAP HANA 요구 사항에 맞게 용량 및 처리량을 개별적으로 정의 하는 최상의 방법을 제공 합니다. 과도 하 게 프로 비전 하 여 로그 볼륨이 나 데이터 볼륨 등의 성능에 도달 하지 않도록 방지 합니다. 또한 요구 사항에 맞는 값으로 성능을 유지 하면서 로그 백업에 대 한 더 많은 공간을 예약할 수 있습니다. 전반적으로, 수동 QoS 용량 풀을 사용 하면 가격 절감이 발생 합니다.

> [!NOTE]
> 수동 QoS 용량 풀만 선택할 목록에 표시 됩니다.

## <a name="will-all-the-volumes-be-provisioned-in-close-proximity-to-my-hana-servers"></a>모든 볼륨이 내 HANA 서버에 근접 하 게 프로 비전 되나요?

아니요. HANA 서버에 대해 만든 PPG (근접 배치 그룹)를 사용 하면 최상의 대기 시간 및 처리량을 얻기 위해 데이터, 로그 및 공유 볼륨이 HANA 서버 가까이 생성 됩니다. 그러나 로그 백업 및 데이터 백업 볼륨에는 짧은 대기 시간이 필요 하지 않습니다. 보호 측면에서 보면 이러한 백업 볼륨을 데이터, 로그 및 공유 볼륨과 동일한 위치에 저장 하지 않는 것이 좋습니다. 대신 백업 볼륨은 사용 가능한 공간 및 처리량이 충분 한 지역 내의 다른 저장소 위치에 배치 됩니다.

## <a name="for-a-multi-host-sap-hana-system-will-the-shared-volume-be-resized-when-i-add-additional-hana-hosts"></a>다중 호스트 SAP HANA 시스템의 경우 HANA 호스트를 더 추가할 때 공유 볼륨의 크기를 조정 하나요?

아니요.  이 시나리오는 현재 크기를 수동으로 조정 해야 하는 경우 중 하나입니다. SAP에서 4 개의 HANA 호스트 마다 공유 볼륨을 1 x RAM으로 크기를 조정 하는 것이 좋습니다. 첫 번째 SAP HANA 호스트의 일부로 공유 볼륨을 만들기 때문에 이미 1tb로 크기가 지정 되었습니다. SAP HANA에 대 한 공유 볼륨의 크기를 조정 하는 두 가지 옵션이 있습니다.

* 6 개의 호스트와 같이 필요에 대해 사전에 알고 있는 경우 SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 초기 생성 중에 1tb 제안을 수정할 수 있습니다. 이때 6 개의 호스트를 수용할 수 있도록 처리량 (즉, QoS)을 늘릴 수도 있습니다.
* 볼륨을 만든 후에는 항상 공유 볼륨을 편집 하 고 크기와 처리량을 개별적으로 변경할 수 있습니다. 볼륨 배치 그룹 내에서 또는 Azure 리소스 공급자나 GUI를 사용 하 여 볼륨에서 직접 수행할 수 있습니다.

## <a name="i-want-to-create-the-data-backup-volume-for-not-only-a-single-instance-but-for-more-than-one-sap-hana-database-how-can-i-do-this"></a>단일 인스턴스 뿐만 아니라 둘 이상의 SAP HANA 데이터베이스에 대 한 데이터 백업 볼륨을 만들려고 합니다. 이 작업을 수행 하려면 어떻게 해야 하나요?

로그 백업 및 데이터 백업 볼륨은 선택 사항이 며 근접 한 근접성이 필요 하지 않습니다. 원하는 결과를 얻기 위한 가장 좋은 방법은 SAP HANA의 응용 프로그램 볼륨 그룹에서 첫 번째 볼륨을 만들 때 데이터 백업 또는 로그 백업 볼륨을 제거 하는 것입니다. 그런 다음 표준 볼륨 프로 비전을 사용 하 고 요구 사항에 맞는 적절 한 용량 및 처리량을 선택 하 여 단일 독립 볼륨으로 볼륨을 만들 수 있습니다. 데이터 백업 볼륨을 나타내는 명명 규칙을 사용 하 고 여러 Sid에 사용 되어야 합니다.

## <a name="what-snapshot-policy-should-i-use-for-my-hana-volumes"></a>HANA 볼륨에 어떤 스냅숏 정책을 사용 해야 하나요?  

이 질문은 SAP HANA의 응용 프로그램 볼륨 그룹과 직접 관련 되지 않습니다. 간단한 대답으로, HANA 환경에 대해 응용 프로그램 일치 백업에 [AzAcSnap](azacsnap-introduction.md) 또는 Commvault와 같은 제품을 사용할 수 있습니다. HANA 데이터베이스의 일관성 있는 백업을 위해 Azure NetApp Files 기본 제공 스냅숏 정책에서 예약한 표준 스냅숏은 사용할 수 없습니다.

SAP HANA 환경의 스냅숏에 대 한 일반적인 권장 사항은 다음과 같습니다.

* 데이터 볼륨 스냅숏을 면밀 하 게 모니터링 합니다. HANA는 높은 변경 률을 갖게 되는 경향이 있습니다. 스냅숏을 장기간 유지 하면 용량 요구가 늘어날 수 있습니다. 사용 된 용량과 할당 된 용량을 모니터링 해야 합니다.
* (로그 및 파일) 백업에 대 한 스냅숏을 자동으로 만드는 경우 해당 보존을 모니터링 하 여 예상치 못한 볼륨 증가를 방지 해야 합니다.

## <a name="the-deployment-failed-and-not-even-a-single-volume-was-created-why-is-that"></a>단일 볼륨이 생성 된 경우에도 배포에 실패 했습니다. 왜 그럴까요?

이는 정상적인 동작입니다. SAP HANA에 대 한 응용 프로그램 볼륨 그룹은 원자성 방식으로 볼륨을 프로 비전 합니다. 일반적으로 지정 된 PPG에는 요구 사항을 수용 하기에 충분 한 리소스가 없으므로 배포가 실패 합니다. Azure NetApp Files 팀은이 상황을 조사 하 여 충분 한 리소스를 제공 합니다.

## <a name="can-i-use-the-new-sap-hana-feature-of-multiple-partitions"></a>여러 파티션의 새로운 SAP HANA 기능을 사용할 수 있나요?

SAP HANA에 대 한 응용 프로그램 볼륨 그룹은 여러 파티션에서 전용 포커스를 사용 하 여 빌드되지 않았지만, 입력을 적용 하는 동안 SAP HANA에 대해 응용 프로그램 볼륨 그룹을 사용할 수 있습니다.

여러 파티션에 대 한 기본 사항은 다음과 같습니다.  

* 파티션이 여러 개이면 단일 SAP HANA 호스트가 지 속성을 저장 하는 데 둘 이상의 볼륨을 사용 하는 것을 의미 합니다. 
* 여러 파티션을 다른 경로에 탑재 해야 합니다. 예를 들어 첫 번째 볼륨은에 `/hana/data/SID/mnt00001` 있고 두 번째 볼륨은 다른 경로 ()를 필요로 합니다 `/hana/data2/SID/mnt00001` . 이러한 결과를 얻으려면 수동으로 명명 규칙을 조정 해야 합니다. 즉 `SID_DATA_MNT00001; SID_DATA2_MNT00001,...`입니다.
* Memory는 용량 및 처리량의 크기에 대 한 SAP HANA 응용 프로그램 볼륨 그룹의 키입니다. 따라서 파티션 수에 맞게 크기를 조정 해야 합니다. 두 파티션에 대해 50%의 메모리를 사용 해야 합니다. 3 개의 파티션에 대해 1/3의 메모리를 사용 해야 합니다. 

만들려는 각 호스트와 각 파티션에 대해 SAP HANA 응용 프로그램 볼륨 그룹을 다시 실행 해야 합니다. 위의 권장 사항을 충족 하도록 명명 제안을 조정 해야 합니다.

## <a name="why-is-1500-mibs-the-maximum-throughput-value-that-application-volume-group-for-sap-hana-proposes-for-the-data-volume"></a>1500 MiB/s의 최대 처리량 값 SAP HANA 응용 프로그램 볼륨 그룹에서 데이터 볼륨을 제안 하는 이유는 무엇 인가요?

NFSv 4.1은 SAP HANA에 대해 지원 되는 프로토콜입니다.  따라서 단일 볼륨을 탑재 하는 경우 하나의 TCP/IP 세션이 지원 됩니다. 단일 볼륨에 대해 단일 TCP 세션 (즉, 단일 호스트에서)을 실행 하는 경우 1500 s s/s가 식별 되는 일반적인 i/o 한계입니다. 따라서 SAP HANA의 응용 프로그램 볼륨 그룹은 현실적으로 달성할 수 있는 것 보다 더 많은 처리량을 할당 하는 것을 방지 합니다. 특히 더 큰 HANA 데이터베이스 (예: 12 TiB)의 경우 더 많은 처리량이 필요한 경우 여러 파티션을 사용 하거나 탑재 옵션을 사용 해야 합니다 `nconnect` .

## <a name="can-i-use-nconnect-as-a-mount-option"></a>`nconnect`탑재 옵션으로 사용할 수 있나요?

Azure NetApp Files는 `nconnect` nfsv 4.1을 지원 하지만 다음과 같은 LINUX OS 버전이 필요 합니다.

* SLES 15SP2 이상
* RHEL 8.3 이상

탑재 옵션을 사용 하는 경우 `nconnect` 읽기 제한은 최대 4500 MiB/s ( [Azure NetApp Files에 대 한 Linux NFS 탑재 옵션 모범 사례](performance-linux-mount-options.md)참조)와 데이터 볼륨에 대 한 제안 된 처리량 제한을 적절 하 게 조정 해야 할 수 있습니다.

## <a name="how-can-i-understand-how-to-size-my-system-or-my-overall-system-landscape"></a>내 시스템 또는 전체 시스템의 가로 크기를 조정 하는 방법을 어떻게 이해할 수 있나요?

SAP Azure NetApp Files 크기 조정 전문가에 게 문의 하 여 전체 SAP 시스템 크기 조정을 계획할 수 있습니다. 

각 시스템에 대해 제공 해야 하는 중요 한 정보에는 SID, 역할 (프로덕션, 개발, 사전 프로덕션/QA), HANA 메모리, 스냅숏 예약 (백분율), 로컬 스냅숏 보존 기간 (일), 파일 기반 백업 수, 호스트 수를 포함 하는 단일 호스트/다중 호스트 및 HSR (주, 보조)가 포함 됩니다.

일반적으로 프로덕션의 경우 100%, 75% 사전 프로덕션, 50% QA, 25% 개발, 30%의 일일 데이터 볼륨의 30% 일일 변경 률, QA의 20% 일일 변경 률, 개발에 대해 10% 일일 변경 률의 일반적인 부하 기여를 가정 합니다. 

데이터 백업은 250 MiB/s를 사용 하 여 작성 됩니다. 

시스템을 알고 있는 경우 (먼저 HANA를 실행 하 여) 이러한 일반적인 가정 대신 데이터를 제공할 수 있습니다. 

## <a name="ive-received-a-warning-message-not-enough-pool-capacity-what-can-i-do"></a>경고 메시지를 받았습니다 `"Not enough pool capacity"` . 어떻게 해야 하나요?
응용 프로그램 볼륨 그룹은 HANA 메모리의 입력에 따라 모든 볼륨의 용량 및 처리량 요청을 계산 합니다. 용량 풀을 선택 하면 즉시 용량 풀에 남아 있는 공간이 나 처리량이 충분 한지 확인 합니다. 

초기 **SAP HANA** 화면에서이 메시지를 무시 하 고 **다음** 단추를 클릭 하 여 워크플로를 계속 진행할 수 있습니다. 그리고 나중에 각 볼륨에 대해 제안 된 값을 개별적으로 조정 하 여 모든 볼륨을 용량 풀에 맞출 수 있습니다. 모든 볼륨이 용량 풀에 도달할 때까지 개별 볼륨을 변경 하면이 오류 메시지가 나타납니다.

이 경고 메시지를 방지 하려면 풀의 크기를 늘려야 할 수도 있습니다.

## <a name="why-is-the-hostid-for-example-00001-added-to-my-names-even-when-ive-removed-the-hostid-placeholder"></a>`hostid`자리 표시자를 제거한 경우에도 (예: 00001)가 내 이름에 추가 되는 이유는 무엇 `{Hostid}` 인가요?  

응용 프로그램 볼륨 그룹을 사용 하려면 자리 표시자를 이름에 포함 해야 합니다 `{Hostid}` . 제거 된 경우 `hostid` 가 제공 된 문자열에 자동으로 추가 됩니다.

**검토 + 만들기** 를 선택한 후 각 볼륨의 최종 이름을 확인할 수 있습니다.

## <a name="how-long-does-it-take-to-create-a-volume-group"></a>볼륨 그룹을 만드는 데 걸리는 시간

볼륨 그룹을 만들려면 여러 단계를 수행 해야 하며, 일부는 동시에 수행할 수 없습니다. 특히 지정 된 위치에 대 한 첫 번째 볼륨 그룹 (PPG)을 만드는 경우 완료 하는 데 최대 9-12 분이 걸릴 수 있습니다. 이후 볼륨 그룹을 더 빠르게 만들 수 있습니다.

## <a name="why-cant-i-edit-the-volume-group-description"></a>볼륨 그룹 설명을 편집할 수 없는 이유는 무엇입니까?

현재 구현에서 응용 프로그램 볼륨 그룹은 볼륨 그룹을 처음 생성 하 고 삭제 하는 데 중점을 두고 있습니다. 

## <a name="next-steps"></a>다음 단계  

* [SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹에 대 한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [HSR에서 SAP HANA 시스템의 볼륨을 보조 데이터베이스로 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용하여 dr 시스템으로 SAP HANA 시스템에 대한 볼륨 추가](application-volume-group-disaster-recovery.md)
* [애플리케이션 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [애플리케이션 볼륨 그룹 삭제](application-volume-group-delete.md)
* [애플리케이션 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
