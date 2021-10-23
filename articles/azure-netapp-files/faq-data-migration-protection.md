---
title: 데이터 마이그레이션 및 Azure NetApp Files에 대 한 보호 Faq | Microsoft Docs
description: Azure NetApp Files 데이터 마이그레이션 및 보호에 대 한 Faq (질문과 대답)입니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: c6422b99087b8239ea6d21c0f5186edd5df1c247
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273732"
---
# <a name="data-migration-and-protection-faqs-for-azure-netapp-files"></a>데이터 마이그레이션 및 Azure NetApp Files에 대 한 보호 Faq

이 문서는 Azure NetApp Files 데이터 마이그레이션 및 보호에 대 한 Faq (질문과 대답)를 답변 합니다.

## <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>데이터를 Azure NetApp Files로 마이그레이션하려면 어떻게 할까요?
Azure NetApp Files는 NFS 및 SMB 볼륨을 제공합니다.  파일 기반 복사 도구를 사용하여 데이터를 서비스로 마이그레이션할 수 있습니다. 

NetApp은 SaaS 기반 솔루션인 [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)를 제공합니다. 이 솔루션을 사용하면 NFS 또는 SMB 데이터를 Azure NetApp Files NFS 내보내기 또는 SMB 공유에 복제할 수 있습니다. 

또한 다양한 무료 도구를 사용하여 데이터를 복사할 수 있습니다. NFS의 경우 [rsync](https://rsync.samba.org/examples.html)와 같은 워크로드 도구를 사용하여 원본 데이터를 Azure NetApp Files 볼륨으로 복사하고 동기화할 수 있습니다. SMB의 경우 워크로드 [robocopy](/windows-server/administration/windows-commands/robocopy)를 동일한 방식으로 사용할 수 있습니다.  이러한 도구는 파일 또는 폴더 권한도 복제할 수 있습니다. 

데이터를 온-프레미스에서 Azure NetApp Files로 마이그레이션하기 위한 요구 사항은 다음과 같습니다. 

- Azure NetApp Files를 대상 Azure 지역에서 사용할 수 있는지 확인합니다.
- 원본과 Azure NetApp Files 대상 볼륨 IP 주소 간의 네트워크 연결을 확인합니다. 온-프레미스와 Azure NetApp Files 서비스 간의 데이터 전송은 ExpressRoute를 통해 지원됩니다.
- 대상 Azure NetApp Files 볼륨을 만듭니다.
- 기본 설정 파일 복사 도구를 사용하여 원본 데이터를 대상 볼륨으로 전송합니다.

## <a name="where-does-azure-netapp-files-store-customer-data"></a>Azure NetApp Files는 고객 데이터를 어디에 저장하나요?   

기본적으로 데이터는 Azure NetApp Files 볼륨을 배포하는 지역 내에 유지됩니다. 그러나 [지역 간 복제](cross-region-replication-introduction.md)를 사용하여 볼륨 단위로 데이터를 사용 가능한 대상 지역에 복제하도록 선택할 수 있습니다.

## <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>다른 Azure 지역에서 Azure NetApp Files 볼륨의 복사본을 만들려면 어떻게 할까요?
    
Azure NetApp Files는 NFS 및 SMB 볼륨을 제공합니다.  모든 파일 기반 복사 도구를 사용하여 Azure 지역 간에 데이터를 복제할 수 있습니다. 

[지역 간 복제](cross-region-replication-introduction.md) 기능을 사용하면 한 지역의 Azure NetApp Files 볼륨(원본)에서 다른 지역의 다른 Azure NetApp Files 볼륨(대상)으로 데이터를 비동기식으로 복제할 수 있습니다.  [기존 볼륨의 스냅샷을 사용하여 새 볼륨을 만들](snapshots-restore-new-volume.md) 수도 있습니다.

NetApp은 SaaS 기반 솔루션인 [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service)를 제공합니다. 이 솔루션을 사용하면 NFS 또는 SMB 데이터를 Azure NetApp Files NFS 내보내기 또는 SMB 공유에 복제할 수 있습니다. 

또한 다양한 무료 도구를 사용하여 데이터를 복사할 수 있습니다. NFS의 경우 [rsync](https://rsync.samba.org/examples.html)와 같은 워크로드 도구를 사용하여 원본 데이터를 Azure NetApp Files 볼륨으로 복사하고 동기화할 수 있습니다. SMB의 경우 워크로드 [robocopy](/windows-server/administration/windows-commands/robocopy)를 동일한 방식으로 사용할 수 있습니다.  이러한 도구는 파일 또는 폴더 권한도 복제할 수 있습니다. 

Azure NetApp Files 볼륨을 다른 Azure 지역으로 복제하기 위한 요구 사항은 다음과 같습니다. 
- Azure NetApp Files를 대상 Azure 지역에서 사용할 수 있는지 확인합니다.
- 각 지역에서 VNet 간 네트워크 연결의 유효성을 검사합니다. 현재 VNet 간의 글로벌 피어링은 지원되지 않습니다.  VNet 간 연결은 ExpressRoute 회로에 연결하거나 S2S VPN 연결을 사용하여 설정할 수 있습니다. 
- 대상 Azure NetApp Files 볼륨을 만듭니다.
- 기본 설정 파일 복사 도구를 사용하여 원본 데이터를 대상 볼륨으로 전송합니다.

## <a name="is-migration-with-azure-data-box-supported"></a>Azure Data Box를 통한 마이그레이션이 지원되나요?

아니요. Azure Data Box는 현재 Azure NetApp Files를 지원하지 않습니다. 

## <a name="is-migration-with-azure-importexport-service-supported"></a>Azure Import/Export 서비스를 통한 마이그레이션이 지원되나요?

아니요. Azure Import/Export 서비스는 현재 Azure NetApp Files를 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계  

- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [네트워킹 FAQ](faq-networking.md)
- [보안 FAQ](faq-security.md)
- [성능 FAQ](faq-performance.md)
- [NFS FAQ](faq-nfs.md)
- [SMB FAQ](faq-smb.md)
- [용량 관리 FAQ](faq-capacity-management.md)
- [Azure NetApp Files 백업 FAQ](faq-backup.md)
- [응용 프로그램 복원 력 Faq](faq-application-resilience.md)
- [통합 Faq](faq-integration.md)
