---
title: FAQ Azure NetApp Files 애플리케이션 볼륨 그룹 | Microsoft Docs
description: Azure NetApp Files 애플리케이션 볼륨 그룹에 대한 FAQ(질문과 대답)에 답변합니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-hchen
ms.author: b-hchen
ms.date: 11/19/2021
ms.openlocfilehash: 900be80a50ab5be11c331b8560cf0541a5418b1b
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484831"
---
# <a name="application-volume-group-faqs"></a>애플리케이션 볼륨 그룹 FAQ

이 문서에서는 Azure NetApp Files 애플리케이션 볼륨 그룹에 대한 FAQ(질문과 대답)에 답변합니다. 

## <a name="why-do-i-have-to-use-a-manual-qos-capacity-pool-for-all-of-my-volumes"></a>모든 볼륨에 수동 QoS 용량 풀을 사용해야 하는 이유는 무엇인가요?

수동 QoS 용량 풀은 SAP HANA 요구에 맞게 용량 및 처리량을 개별적으로 정의하는 가장 좋은 방법을 제공합니다. 예를 들어 로그 볼륨 또는 데이터 볼륨의 성능에 도달하는 오버 프로비저닝을 방지합니다. 또한 성능을 요구 사항에 맞는 값으로 유지하면서 로그 백업을 위해 더 큰 공간을 예약할 수 있습니다. 전반적으로 수동 QoS 용량 풀을 사용하면 가격이 절감됩니다.

> [!NOTE]
> 선택할 수 있는 목록에는 수동 QoS 용량 풀만 표시됩니다.

## <a name="will-all-the-volumes-be-provisioned-in-close-proximity-to-my-hana-servers"></a>모든 볼륨이 내 HANA 서버와 근접하게 프로비전될까요?

아니요. HANA 서버에 대해 만든 PPG(근접 배치 그룹)를 사용하면 최상의 대기 시간 및 처리량을 달성하기 위해 HANA 서버 가까이에 데이터, 로그 및 공유 볼륨이 생성됩니다. 그러나 로그 백업 및 데이터 백업 볼륨에는 짧은 대기 시간이 필요하지 않습니다. 보호 관점에서 이러한 백업 볼륨을 데이터, 로그 및 공유 볼륨과 동일한 위치에 저장하지 않는 것이 합리적입니다. 대신 백업 볼륨은 사용 가능한 충분한 공간과 처리량이 있는 지역 내의 다른 스토리지 위치에 배치됩니다.

## <a name="for-a-multi-host-sap-hana-system-will-the-shared-volume-be-resized-when-i-add-additional-hana-hosts"></a>다중 호스트 SAP HANA 시스템의 경우 HANA 호스트를 더 추가할 때 공유 볼륨의 크기가 다시 정해집니다.

아니요.  이 시나리오는 현재 크기를 수동으로 조정해야 하는 매우 적은 경우 중 하나입니다. SAP는 4개의 HANA 호스트마다 공유 볼륨의 크기를 1 x RAM으로 하는 것이 좋습니다. 첫 번째 SAP HANA 호스트의 일부로 공유 볼륨을 만들므로 이미 1 TB 크기로 크기가 정해져 있습니다. SAP HANA 위해 공유 볼륨의 크기를 적절하게 SAP HANA 두 가지 옵션이 있습니다.

* 필요한 미리 알고 있는 경우(예: 6개의 호스트) 초기 생성 중에 SAP HANA 애플리케이션 볼륨 그룹을 통해 1-TB 제안을 수정할 수 있습니다. 이 시점에서 6개의 호스트를 수용하도록 처리량(즉, QoS)을 늘릴 수도 있습니다.
* 언제든지 공유 볼륨을 편집하고 볼륨을 만든 후 크기 및 처리량을 개별적으로 변경할 수 있습니다. 볼륨 배치 그룹 내에서 또는 Azure 리소스 공급자 또는 GUI를 사용하여 볼륨에서 직접 수행할 수 있습니다.

## <a name="i-want-to-create-the-data-backup-volume-for-not-only-a-single-instance-but-for-more-than-one-sap-hana-database-how-can-i-do-this"></a>단일 인스턴스뿐만 아니라 두 개 이상의 SAP HANA 데이터베이스에 대한 데이터 백업 볼륨을 만들려고 합니다. 어떻게 해야 합니까?

로그백 및 데이터 백업 볼륨은 선택 사항이며 근접성이 필요하지 않습니다. 의도한 결과를 달성하는 가장 좋은 방법은 SAP HANA 위해 애플리케이션 볼륨 그룹에서 첫 번째 볼륨을 만들 때 데이터 백업 또는 로그 백업 볼륨을 제거하는 것입니다. 그런 다음 표준 볼륨 프로비저닝을 사용하고 요구 사항에 맞는 적절한 용량 및 처리량을 선택하여 고유한 볼륨을 독립된 단일 볼륨으로 만들 수 있습니다. 데이터 백업 볼륨을 나타내고 여러 SID에 사용되는 명명 규칙을 사용해야 합니다.

## <a name="what-snapshot-policy-should-i-use-for-my-hana-volumes"></a>HANA 볼륨에 어떤 스냅샷 정책을 사용해야 하나요?  

이 질문은 SAP HANA 애플리케이션 볼륨 그룹과 직접 관련이 없습니다. 간단히 말해서, HANA 환경의 애플리케이션 일치 백업에 [AzAcSnap](azacsnap-introduction.md) 또는 Commvault와 같은 제품을 사용할 수 있습니다. HANA 데이터베이스의 일관된 백업에 Azure NetApp Files 기본 제공 스냅샷 정책으로 예약된 표준 스냅샷은 사용할 수 없습니다.

SAP HANA 환경의 스냅샷에 대한 일반적인 권장 사항은 다음과 같습니다.

* 데이터 볼륨 스냅샷을 면밀히 모니터링합니다. HANA는 변경률이 높은 경향이 있습니다. 스냅샷을 장기간 유지하면 용량 요구 사항이 증가할 수 있습니다. 사용된 용량과 할당된 용량을 모니터링해야 합니다.
* (로그 및 파일) 백업에 대한 스냅샷을 자동으로 만드는 경우 예측되지 않은 볼륨 증가를 방지하기 위해 해당 보존을 모니터링해야 합니다.

## <a name="the-deployment-failed-and-not-even-a-single-volume-was-created-why-is-that"></a>배포가 실패했으며 단일 볼륨도 생성되지 않았습니다. 왜 그럴까요?

이것이 일반적인 동작입니다. SAP HANA 애플리케이션 볼륨 그룹은 볼륨을 원자성 방식으로 프로비전합니다. 지정된 PPG에 요구 사항을 수용할 수 있는 충분한 리소스가 없기 때문에 일반적으로 배포가 실패합니다. Azure NetApp Files 팀은 이 상황을 조사하여 충분한 리소스를 제공할 것입니다.

## <a name="can-i-use-the-new-sap-hana-feature-of-multiple-partitions"></a>여러 파티션의 새로운 SAP HANA 기능을 사용할 수 있나요?

SAP HANA 대한 애플리케이션 볼륨 그룹은 여러 파티션에 대한 전용 포커스로 빌드되지 않았지만 입력을 조정하는 동안 SAP HANA 애플리케이션 볼륨 그룹을 사용할 수 있습니다.

여러 파티션에 대한 기본 사항은 다음과 같습니다.  

* 파티션이 여러 개이면 단일 SAP HANA 호스트가 두 개 이상의 볼륨을 사용하여 지속성을 저장합니다. 
* 여러 파티션이 다른 경로에 탑재되어야 합니다. 예를 들어 첫 번째 볼륨은 에 `/hana/data/SID/mnt00001` 있고 두 번째 볼륨에는 다른 경로( `/hana/data2/SID/mnt00001` )가 필요합니다. 이 결과를 얻으려면 명명 규칙을 수동으로 조정해야 합니다. 즉 `SID_DATA_MNT00001; SID_DATA2_MNT00001,...`입니다.
* 메모리는 용량 및 처리량의 크기를 SAP HANA 애플리케이션 볼륨 그룹의 핵심입니다. 따라서 파티션 수를 수용하도록 크기를 조정해야 합니다. 두 파티션의 경우 메모리의 50%만 사용해야 합니다. 세 파티션의 경우 메모리의 1/3 등을 사용해야 합니다. 

만들려는 각 호스트 및 각 파티션에 대해 SAP HANA 애플리케이션 볼륨 그룹을 다시 실행해야 합니다. 또한 위의 권장 사항을 충족하도록 명명 제안을 조정해야 합니다.

## <a name="why-is-1500-mibs-the-maximum-throughput-value-that-application-volume-group-for-sap-hana-proposes-for-the-data-volume"></a>SAP HANA 애플리케이션 볼륨 그룹이 데이터 볼륨에 대해 제안하는 최대 처리량 값이 1500 MiB인 이유는 무엇인가요?

NFSv4.1은 SAP HANA 지원되는 프로토콜입니다.  따라서 단일 볼륨을 탑재할 때 하나의 TCP/IP 세션이 지원됩니다. 단일 볼륨에 대해 단일 TCP 세션(즉, 단일 호스트에서)을 실행하는 경우 1500 MiB/s가 식별되는 일반적인 I/O 제한입니다. 따라서 SAP HANA 애플리케이션 볼륨 그룹은 실제로 달성할 수 있는 것보다 더 많은 처리량을 할당하지 않도록 방지합니다. 특히 더 큰 HANA 데이터베이스(예: 12 TiB)의 경우 더 많은 처리량이 필요한 경우 여러 파티션을 사용하거나 탑재 옵션을 사용해야 `nconnect` 합니다.

## <a name="can-i-use-nconnect-as-a-mount-option"></a>탑재 옵션으로 를 사용할 수 `nconnect` 있나요?

Azure NetApp Files `nconnect` NFSv4.1을 지원하지만 다음 Linux OS 버전이 필요합니다.

* SLES 15SP2 이상
* RHEL 8.3 이상

탑재 옵션을 사용하는 경우 `nconnect` 읽기 제한은 최대 4500 MiB/s(Azure NetApp Files [대한 Linux NFS 탑재 옵션 모범 사례](performance-linux-mount-options.md)참조)이며 데이터 볼륨에 대해 제안된 처리량 제한을 그에 따라 조정해야 할 수 있습니다.

## <a name="how-can-i-understand-how-to-size-my-system-or-my-overall-system-landscape"></a>시스템 또는 전체 시스템 환경의 크기를 어떻게 파악할 수 있나요?

SAP Azure NetApp Files 크기 조정 전문가에게 문의하여 전체 SAP 시스템 크기 조정을 계획할 수 있습니다. 

각 시스템에 대해 제공해야 하는 중요한 정보에는 SID, 역할(프로덕션, 개발, 사전 프로덕션/QA), HANA 메모리, 스냅샷 예약(백분율), 로컬 스냅샷 보존 기간(일), 파일 기반 백업 수, 호스트 수가 있는 단일 호스트/다중 호스트 및 HSR(기본, 보조)이 포함됩니다.

일반적으로 프로덕션에 대해 100%, 사전 프로덕션 75%, 50% QA, 25% 개발, 프로덕션용 데이터 볼륨의 일일 변경률 30%, QA에 대한 일일 변경률 20%, 개발을 위한 일일 변경률 10%의 일반적인 부하 기여도를 가정합니다. 

데이터 백업은 250 MiB/s로 작성됩니다. 

이전에 HANA를 실행하여 시스템을 알고 있는 경우 이러한 일반적인 가정 대신 데이터를 제공할 수 있습니다. 

## <a name="ive-received-a-warning-message-not-enough-pool-capacity-what-can-i-do"></a>경고 메시지가 `"Not enough pool capacity"` 수신되었습니다. 어떻게 해야 하나요?
애플리케이션 볼륨 그룹은 HANA 메모리 입력에 따라 모든 볼륨의 용량 및 처리량 수요를 계산합니다. 용량 풀을 선택하면 용량 풀에 충분한 공간 또는 처리량이 남아 있는지 즉시 확인합니다. 

초기 **SAP HANA** 화면에서 이 메시지를 무시하고 **다음** 단추를 클릭하여 워크플로를 계속할 수 있습니다. 나중에 각 볼륨에 대해 제안된 값을 개별적으로 조정하여 모든 볼륨이 용량 풀에 맞도록 할 수 있습니다. 이 오류 메시지는 모든 볼륨이 용량 풀에 들어갈 때까지 각 개별 볼륨을 변경할 때 다시 나타납니다.

이 경고 메시지를 방지하기 위해 풀의 크기를 늘릴 수도 있습니다.

## <a name="why-is-the-hostid-for-example-00001-added-to-my-names-even-when-ive-removed-the-hostid-placeholder"></a>`hostid`자리 표시자를 제거한 경우에도 (예: 00001)이 내 이름에 추가되는 이유는 `{Hostid}` 무엇인가요?  

애플리케이션 볼륨 그룹에는 자리 표시자가 `{Hostid}` 이름의 일부여야 합니다. 제거되면 `hostid` 가 제공된 문자열에 자동으로 추가됩니다.

**검토 + 만들기를** 선택한 후 각 볼륨의 최종 이름을 볼 수 있습니다.

## <a name="how-long-does-it-take-to-create-a-volume-group"></a>볼륨 그룹을 만드는 데 얼마나 걸리나요?

볼륨 그룹을 만들 때는 여러 단계가 포함되며 모든 단계를 병렬로 수행할 수 있는 것은 아닙니다. 특히 지정된 위치(PPG)에 대한 첫 번째 볼륨 그룹을 만드는 경우 완료하는 데 최대 9-12분이 걸릴 수 있습니다. 후속 볼륨 그룹은 더 빠르게 만들어집니다.

## <a name="why-cant-i-edit-the-volume-group-description"></a>볼륨 그룹 설명을 편집할 수 없는 이유는 무엇인가요?

현재 구현에서 애플리케이션 볼륨 그룹은 볼륨 그룹의 초기 생성 및 삭제에만 중점을 두고 있습니다. 

## <a name="next-steps"></a>다음 단계  

* [SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA 애플리케이션 볼륨 그룹에 대한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [HSR에서 보조 데이터베이스로 SAP HANA 시스템의 볼륨 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용 하 여 SAP HANA 시스템용 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [응용 프로그램 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [응용 프로그램 볼륨 그룹 삭제](application-volume-group-delete.md)
* [응용 프로그램 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
