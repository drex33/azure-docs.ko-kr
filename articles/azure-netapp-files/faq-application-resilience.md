---
title: Azure NetApp Files에 대 한 응용 프로그램 복원 력 Faq | Microsoft Docs
description: Azure NetApp Files 응용 프로그램 복원 력에 대 한 Faq (질문과 대답)를 답변 합니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-hchen
ms.author: b-hchen
ms.date: 10/11/2021
ms.openlocfilehash: cd327e0b3cb4273f5dfd7702ac4524c05189634d
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133482680"
---
# <a name="application-resilience-faqs-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 응용 프로그램 복원 력 Faq

이 문서에서는 응용 프로그램 복원 Azure NetApp Files에 대 한 Faq (질문과 대답)에 답변 합니다.

## <a name="what-do-you-recommend-for-handling-potential-application-disruptions-due-to-storage-service-maintenance-events"></a>저장소 서비스 유지 관리 이벤트로 인해 잠재적인 응용 프로그램 중단을 처리 하는 데 권장 되는 사항은 무엇 인가요?

Azure NetApp Files는 계획 된 유지 관리 (예: 플랫폼 업데이트, 서비스 또는 소프트웨어 업그레이드)가 발생할 수 있습니다. 파일 프로토콜 (NFS/SMB) 관점에서 볼 때 응용 프로그램이 이러한 이벤트 중에 일시적으로 발생할 수 있는 IO 일시 중지를 처리할 수 있으면 유지 관리 작업은 중단 되지 않습니다. I/o 일시 중지는 일반적으로 몇 초에서 30 초까지 짧게 단축 됩니다. NFS 프로토콜은 특히 강력 하며 클라이언트 서버 파일 작업은 정상적으로 계속 됩니다. 일부 응용 프로그램의 경우 30-45 초 동안 IO 일시 중지를 처리 하려면 조정이 필요할 수 있습니다. 따라서 저장소 서비스 유지 관리 이벤트를 처리 하는 응용 프로그램의 복원 력 설정을 알고 있어야 합니다. SMB 프로토콜을 활용 하는 휴먼 대화형 응용 프로그램의 경우 일반적으로 표준 프로토콜 설정으로 충분 합니다. 

## <a name="do-i-need-to-take-special-precautions-for-smb-based-applications"></a>SMB 기반 응용 프로그램에 대 한 특별 한 예방 조치를 취해야 하나요?

예, 특정 SMB 기반 응용 프로그램은 SMB 투명 장애 조치 (Failover)가 필요 합니다. SMB 투명 장애 조치(Failover)를 사용하면 SMB 볼륨의 데이터를 저장 및 액세스하는 서버 애플리케이션에 대한 연결을 중단하지 않고도 Azure NetApp Files 서비스에서 유지 관리 작업을 수행할 수 있습니다. 특정 응용 프로그램에 대 한 SMB 투명 장애 조치 (Failover)를 지원 하기 위해 Azure NetApp Files는 이제 [Smb 지속적인 가용성 공유 옵션](azure-netapp-files-create-volumes-smb.md#continuous-availability)을 지원 합니다. 

## <a name="i-am-running-ibm-mq-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despite-using-the-nfs-protocol"></a>Azure NetApp Files에서 IBM MQ를 실행 하 고 있습니다. NFS 프로토콜을 사용 하는 경우에도 저장소 서비스 유지 관리 이벤트로 인 한 중단을 방지 하기 위해 수행할 수 있는 예방 조치는 무엇입니까?

IBM mq 데이터 및 로그가 Azure NetApp Files 볼륨에 저장 된 [공유 파일 구성에서 IBM mq 응용 프로그램](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=multiplatforms-sharing-mq-files)을 실행 하는 경우 저장소 서비스 유지 관리 이벤트 중에 복원 력을 향상 시키려면 다음과 같은 사항을 고려해 야 합니다.

* NFS v 4.1 프로토콜만 사용 해야 합니다.
* 고가용성을 위해 [공유 NFS v 4.1 볼륨을 사용 하는 IBM MQ 다중 인스턴스 구성을](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=manager-create-multi-instance-queue-linux)사용 해야 합니다. 
* [공유 NFS v 4.1 볼륨을 사용 하 여 IBM 다중 인스턴스 구성](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=multiplatforms-verifying-shared-file-system-behavior)의 기능을 확인 해야 합니다. 
* 대규모 다중 인스턴스 IBM MQ 구성 하나를 사용 하는 대신 스케일 아웃 IBM MQ 아키텍처를 구현 해야 합니다. 여러 IBM MQ 다중 인스턴스 쌍으로 메시지 처리 부하를 분산 하 여 각 MQ 다중 인스턴스 쌍이 메시지 수를 덜 처리 하므로 서비스 중단이 줄어들 수 있습니다.

> [!NOTE] 
> 각 MQ 다중 인스턴스 쌍이 처리 해야 하는 메시지 수는 특정 환경에 따라 달라 집니다. 필요한 MQ 다중 인스턴스 쌍 수 또는 확장 또는 축소 규칙의 수를 결정 해야 합니다.

수평 확장 아키텍처는 Azure Load Balancer 뒤에 배포 되는 여러 IBM MQ 다중 인스턴스 쌍으로 구성 됩니다. IBM MQ와 통신 하도록 구성 된 응용 프로그램은 Azure Load Balancer를 통해 IBM MQ 인스턴스와 통신 하도록 구성 됩니다. 공유 NFS 볼륨에서 IBM MQ와 관련 된 지원은 IBM에서 공급 업체 지원을 받아야 합니다.

## <a name="i-am-running-apache-activemq-with-leveldb-or-kahadb-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despite-using-the-nfs-protocol"></a>Azure NetApp Files에서 LevelDB 또는 KahaDB를 사용 하 여 Apache ActiveMQ를 실행 하 고 있습니다. *NFS* 프로토콜을 사용 하는 경우에도 저장소 서비스 유지 관리 이벤트로 인 한 중단을 방지 하기 위해 수행할 수 있는 예방 조치는 무엇입니까?

>[!NOTE]
> 이 섹션에는 Microsoft에서 더 이상 사용 하지 않는 사용 약관 *종속* 및 *마스터* 용어에 대 한 참조가 포함 되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

Apache activemq를 실행 하는 경우 [플러그형 Storage lockers를 사용 하 여 activemq 고가용성](https://www.openlogic.com/blog/pluggable-storage-lockers-activemq)을 배포 하는 것이 좋습니다. 

ActiveMQ 고가용성 (HA) 모델은 broker 인스턴스가 항상 온라인 상태이 고 메시지 트래픽을 처리할 수 있는지 확인 합니다. 가장 일반적인 두 가지 ActiveMQ HA 모델에는 네트워크를 통해 파일 시스템을 공유 하는 작업이 포함 됩니다. 이는 LevelDB 또는 KahaDB을 활성 및 수동 broker 인스턴스에 제공 하는 것입니다. 이러한 HA 모델에서는 "잠금" 이라는 LevelDB 또는 KahaDB 디렉터리의 파일에 대해 OS 수준 잠금을 가져오고 유지 관리 해야 합니다. 이 ActiveMQ HA 모델에는 몇 가지 문제가 있습니다. "종속 되지 않은" 상황이 발생할 수 있습니다. 여기서 "슬레이브"는 파일을 잠글 수 있음을 인식 하지 못합니다.  또한 인덱스 또는 저널 손상 및 궁극적으로 메시지 손실을 초래 하는 "마스터 마스터" 구성이 발생할 수 있습니다. 이러한 문제의 대부분은 ActiveMQ의 제어 범위를 벗어난 요인에서 기인 합니다. 예를 들어, 잘못 최적화 된 NFS 클라이언트는 로드 하는 동안 잠금이 만료 될 수 있으므로 장애 조치 (failover) 중에 "마스터 없음" 가동 중지 시간이 발생 합니다. 

이 HA 솔루션에서 발생 하는 대부분의 문제는 부정확 한 OS 수준 파일 잠금으로 인해 발생 하므로 ActiveMQ 커뮤니티에는 broker의 버전 5.7에 있는 [플러그형 저장소 보관 이라는 개념이 도입](https://www.openlogic.com/blog/pluggable-storage-lockers-activemq) 되었습니다. 이 접근 방식을 사용 하면 사용자가 OS 수준 파일 시스템 잠금과 달리 행 수준 JDBC 데이터베이스 잠금을 사용 하 여 공유 잠금의 다른 방법을 활용할 수 있습니다. ActiveMQ HA 아키텍처 및 배포에 대 한 지원 또는 컨설팅의 경우 [Perforce에서 OpenLogic에 문의](https://www.openlogic.com/contact-us)해야 합니다.

## <a name="i-am-running-apache-activemq-with-leveldb-or-kahadb-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despites-using-the-smb-protocol"></a>Azure NetApp Files에서 LevelDB 또는 KahaDB를 사용 하 여 Apache ActiveMQ를 실행 하 고 있습니다. *SMB* 프로토콜을 사용 하는 저장소 서비스 유지 관리 이벤트로 인 한 중단을 방지 하기 위해 수행할 수 있는 예방 조치는 무엇입니까?

일반적인 업계 권장 사항은 [CIFS/SMB에서 KahaDB 공유 저장소를 실행 하지 않는](https://www.openlogic.com/blog/activemq-community-deprecates-leveldb-what-you-need-know)것입니다. 정확한 잠금 상태를 유지 관리 하는 데 문제가 있는 경우 보다 안정적인 잠금 메커니즘을 제공할 수 있는 JDBC 플러그형 Storage 락커를 확인 하세요. ActiveMQ HA 아키텍처 및 배포에 대 한 지원 또는 컨설팅의 경우 [Perforce에서 OpenLogic에 문의](https://www.openlogic.com/contact-us)해야 합니다.

## <a name="next-steps"></a>다음 단계  

- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [네트워킹 FAQ](faq-networking.md)
- [보안 FAQ](faq-security.md)
- [성능 FAQ](faq-performance.md)
- [NFS FAQ](faq-nfs.md)
- [SMB FAQ](faq-smb.md)
- [용량 관리 FAQ](faq-capacity-management.md)
- [데이터 마이그레이션 및 보호 FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files 백업 FAQ](faq-backup.md)
- [통합 FAQ](faq-integration.md)

