---
title: Azure NetApp Files에 대 한 네트워킹 Faq | Microsoft Docs
description: Azure NetApp Files 네트워킹에 대해 Faq (질문과 대답)를 답변 합니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-hchen
ms.author: b-hchen
ms.date: 11/08/2021
ms.openlocfilehash: 8e0f9fad47c0300980f49530058dd769921d571b
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487343"
---
# <a name="networking-faqs-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 네트워킹 Faq

이 문서에서는 Azure NetApp Files 네트워킹에 대해 Faq (질문과 대답)에 답변 합니다. 

## <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>NFS 또는 SMB의 데이터 경로는 인터넷을 통과하나요?  

아니요. NFS 또는 SMB의 데이터 경로는 인터넷을 통과하지 않습니다. Azure NetApp Files는 서비스를 사용할 수 있는 VNet(Azure Virtual Network)에 배포되는 Azure 네이티브 서비스입니다. Azure NetApp Files는 위임된 서브넷을 사용하고 VNet에서 직접 네트워크 인터페이스를 프로비전합니다. 

자세한 내용은 [Azure NetApp Files 네트워크 계획 지침](./azure-netapp-files-network-topologies.md)을 참조하세요.  

## <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>이미 만든 VNet을 Azure NetApp Files 서비스에 연결할 수 있나요?

예, 만든 VNet은 서비스에 연결할 수 있습니다. 

자세한 내용은 [Azure NetApp Files 네트워크 계획 지침](./azure-netapp-files-network-topologies.md)을 참조하세요.  

## <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>DNS FQDN 이름을 사용하여 Azure NetApp Files의 NFS 볼륨을 탑재할 수 있나요?

예, 필요한 DNS 항목을 만들기만 하면 됩니다. Azure NetApp Files는 프로비전된 볼륨에 대한 서비스 IP를 제공합니다. 

> [!NOTE] 
> Azure NetApp Files는 필요에 따라 서비스에 대한 추가 IP를 배포할 수 있습니다.  DNS 항목을 정기적으로 업데이트해야 할 수 있습니다.

## <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Azure NetApp Files 볼륨에 대해 고유한 내 IP 주소를 설정하거나 선택할 수 있나요?  

아니요. Azure NetApp Files 볼륨에 대한 IP는 동적으로 할당됩니다. 고정 IP 할당은 지원되지 않습니다. 

## <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp Files는 이중 스택(IPv4 및 IPv6) VNet을 지원하나요?

아니요, Azure NetApp Files는 현재 이중 스택 (IPv4 및 IPv6) 위임 된 서브넷에서 볼륨 배포를 지원 하지 않습니다. Azure NetApp Files 서비스에 대 한 위임 된 서브넷은 IPv4 전용 서브넷 이어야 합니다. 그러나 Azure NetApp Files는 이중 스택 서브넷 또는 (피어 링) VNet을 통해 IPv4를 통해 액세스할 수 있습니다.

## <a name="is-the-number-of-the-ip-addresses-using-azure-vmware-solutions-for-guest-os-mounts-limited-to-1000"></a>게스트 OS 탑재의 Azure VMware 솔루션을 사용 하는 IP 주소의 수가 1000 개로 제한 되나요?

아니요. Azure VMware 솔루션은 온-프레미스 시스템과 유사 하 게 작동 하는 ER 게이트웨이 뒤에 있습니다. AVS "Hosts" 및 "Guests"의 수가 Azure NetApp Files에 표시 되지 않으며 [1000 IP 주소 제한이](azure-netapp-files-resource-limits.md#resource-limits) 적용 되지 않습니다.
 
## <a name="next-steps"></a>다음 단계  

- [Microsoft Azure ExpressRoute FAQ](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network FAQ](../virtual-network/virtual-networks-faq.md)
- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [보안 FAQ](faq-security.md)
- [성능 FAQ](faq-performance.md)
- [NFS FAQ](faq-nfs.md)
- [SMB FAQ](faq-smb.md)
- [용량 관리 FAQ](faq-capacity-management.md)
- [데이터 마이그레이션 및 보호 FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files 백업 FAQ](faq-backup.md)
- [애플리케이션 복원력 FAQ](faq-application-resilience.md)
- [통합 FAQ](faq-integration.md)
