---
title: Azure NetApp Files | 대한 보안 FAQ Microsoft Docs
description: Azure NetApp Files 보안에 대한 FAQ(질문과 대답)입니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-hchen
ms.author: b-hchen
ms.date: 10/11/2021
ms.custom: references_regions
ms.openlocfilehash: 19fa25ed6b1cb4e06de4a52bf4eff6f4de492169
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133483349"
---
# <a name="security-faqs-for-azure-netapp-files"></a>Azure NetApp Files 대한 보안 FAQ

이 문서에서는 Azure NetApp Files 보안에 대한 FAQ(질문과 대답)를 제공합니다.

## <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM과 스토리지 간의 네트워크 트래픽을 암호화할 수 있나요?

Azure NetApp Files 데이터 트래픽은 퍼블릭 엔드포인트를 제공하지 않고 데이터 트래픽이 고객 소유의 VNet 내에서 유지되기 때문에 기본적으로 안전합니다. 이동 중인 데이터는 기본적으로 암호화되지 않습니다. 그러나 Azure VM(NFS 또는 SMB 클라이언트 실행)에서 Azure NetApp Files로 이동하는 데이터 트래픽은 다른 Azure VM 간 트래픽만큼 안전합니다. 

NFSv3 프로토콜은 암호화를 지원하지 않으므로 이동 중인 데이터를 암호화할 수 없습니다. 그러나 NFSv4.1 및 SMB3 이동 중인 데이터 암호화를 선택적으로 사용하도록 설정할 수 있습니다. NFSv4.1 클라이언트와 Azure NetApp Files 볼륨 간의 데이터 트래픽은 AES-256 암호화를 사용하는 Kerberos를 통해 암호화할 수 있습니다. 자세한 내용은 [Azure NetApp Files에 대한 NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)을 참조하세요. SMB 3.0의 AES-CCM 알고리즘과 SMB 3.1.1 연결의 AES-GCM 알고리즘을 사용하여 SMB3 클라이언트와 Azure NetApp Files 볼륨 간 데이터 트래픽을 암호화할 수 있습니다. 자세한 내용은 [Azure NetApp Files에 대한 SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)를 참조하세요. 

## <a name="can-the-storage-be-encrypted-at-rest"></a>미사용 스토리지를 암호화할 수 있나요?

모든 Azure NetApp Files 볼륨은 FIPS 140-2 표준을 사용하여 암호화됩니다. 모든 키는 Azure NetApp Files 서비스에서 관리합니다. 

## <a name="is-azure-netapp-files-cross-region-replication-traffic-encrypted"></a>Azure NetApp Files 지역 간 복제 트래픽은 암호화되나요?

Azure NetApp Files 지역 간 복제는 TLS 1.2 AES-256 GCM 암호화를 사용하여 원본 볼륨과 대상 볼륨 간에 전송되는 모든 데이터를 암호화합니다. 이 암호화는 Azure NetApp Files 지역 간 복제를 포함하여 모든 Azure 트래픽에 대해 기본적으로 설정되는 [Azure MACSec 암호화](../security/fundamentals/encryption-overview.md)에 추가로 적용됩니다. 

## <a name="how-are-encryption-keys-managed"></a>암호 키를 관리하는 방법은 어떻게 되나요? 

Azure NetApp Files에 대한 키 관리는 서비스에서 처리합니다. 각 볼륨에 대해 고유한 XTS-AES-256 데이터 암호화 키가 생성됩니다. 암호화 키 계층 구조가 모든 볼륨 키를 암호화하고 보호하는 데 사용됩니다. 이러한 암호화 키는 암호화되지 않은 형식으로 표시되거나 보고되지 않습니다. 볼륨을 삭제하면 암호화 키가 즉시 삭제됩니다.

Azure Dedicated HSM을 사용하는 고객 관리형 키(Bring Your Own Key)에 대한 지원은 제어되는 기준에 근거하여 미국 동부, 미국 중남부, 미국 서부 2 및 US Gov 버지니아 지역에서 제공됩니다. [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com)에서 액세스를 요청할 수 있습니다. 용량을 사용할 수 있게 되면 요청이 승인됩니다.

## <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Azure NetApp Files 서비스 탑재 대상에 대한 액세스를 제어하도록 NFS 내보내기 정책 규칙을 구성할 수 있나요?

예, 단일 NFS 내보내기 정책에서 최대 5개의 규칙을 구성할 수 있습니다.

## <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Azure NetApp Files에서 Azure RBAC를 사용할 수 있나요?

예, Azure NetApp Files는 Azure RBAC 기능을 지원합니다. 기본 제공 Azure 역할과 함께 Azure NetApp Files에 대한 [사용자 지정 역할을 만들](../role-based-access-control/custom-roles.md) 수 있습니다. 

Azure NetApp Files 권한의 전체 목록은 [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp)에 대한 Azure 리소스 공급자 작업을 참조하세요.

## <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Azure NetApp Files에서 Azure 활동 로그를 지원하나요?

Azure NetApp Files는 Azure 네이티브 서비스입니다. Azure NetApp Files에 대한 모든 PUT, POST 및 DELETE API가 기록됩니다. 예를 들어 로그에는 스냅샷을 만든 사용자, 볼륨을 수정한 사용자 등과 같은 작업이 표시됩니다.

API 작업의 전체 목록은 [Azure NetApp Files REST API](/rest/api/netapp/)를 참조하세요.

## <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Azure NetApp Files에서 Azure 정책을 사용할 수 있나요?

예, [사용자 지정 Azure 정책](../governance/policy/tutorials/create-custom-policy-definition.md)을 만들 수 있습니다. 

그러나 Azure 정책(사용자 지정 명명 정책)은 Azure NetApp Files 인터페이스에서 만들 수 없습니다. [Azure NetApp Files 네트워크 계획 지침](azure-netapp-files-network-topologies.md#considerations)을 참조하십시오.

## <a name="when-i-delete-an-azure-netapp-files-volume-is-the-data-deleted-safely"></a>Azure NetApp Files 볼륨을 삭제하면 데이터가 안전하게 삭제되나요? 

Azure NetApp Files 볼륨의 삭제는 프로그래밍 방식으로 즉시 수행됩니다. 삭제 작업에는 미사용 데이터를 암호화하는 데 사용되는 키 삭제가 포함됩니다. 삭제 작업이 성공적으로 실행되면(Azure Portal 및 API와 같은 인터페이스를 통해) 삭제된 볼륨을 복구하는 시나리오에 대한 옵션은 제공되지 않습니다.


## <a name="next-steps"></a>다음 단계  

- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [네트워킹 FAQ](faq-networking.md)
- [성능 FAQ](faq-performance.md)
- [NFS FAQ](faq-nfs.md)
- [SMB FAQ](faq-smb.md)
- [용량 관리 FAQ](faq-capacity-management.md)
- [데이터 마이그레이션 및 보호 FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files 백업 FAQ](faq-backup.md)
- [애플리케이션 복원력 FAQ](faq-application-resilience.md)
- [통합 FAQ](faq-integration.md)