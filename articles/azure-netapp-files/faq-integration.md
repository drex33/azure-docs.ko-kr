---
title: Azure NetApp Files | 대한 통합 FAQ Microsoft Docs
description: Azure NetApp Files 다른 제품 또는 서비스를 사용하는 것에 대한 FAQ(질문과 대답)입니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: c48a35b4578488fdab5fc55cdca4e19d97a003d3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273760"
---
# <a name="integration-faqs-for-azure-netapp-files"></a>Azure NetApp Files 대한 통합 FAQ

이 문서에서는 Azure NetApp Files 다른 제품 또는 서비스를 사용하는 것에 대한 FAQ(질문과 대답)를 제공합니다.  

## <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>AVS(Azure VMware Solution)에서 Azure NetApp Files NFS 또는 SMB 볼륨을 사용할 수 있나요?

Azure NetApp Files NFS 볼륨은 AVS Windows VM 또는 Linux VM에서 탑재할 수 있습니다. Azure NetApp Files SMB 공유는 AVS Windows VM에서 매핑할 수 있습니다. 자세한 내용은 [Azure VMware Solution Azure NetApp Files]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md)참조하세요.  

## <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>AVS(Azure VMware Solution)에서 Azure NetApp Files NFS 또는 SMB 볼륨을 사용하도록 지원되는 지역은 무엇인가요?

*게스트 OS 탑재* 를 위해 AVS에서 Azure NetApp Files NFS 또는 SMB 볼륨을 사용하는 방식은 [모든 AVS 및 ANF 지원 지역](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware,netapp)에서 지원됩니다.

## <a name="does-azure-netapp-files-work-with-azure-policy"></a>Azure NetApp Files는 Azure Policy와 함께 작동하나요?

예. Azure NetApp Files는 자사 서비스입니다. Azure 리소스 공급자 표준을 완벽하게 준수합니다. 따라서 ‘사용자 지정 정책 정의’를 통해 Azure NetApp Files를 Azure Policy에 통합할 수 있습니다. Azure NetApp Files에 대한 사용자 지정 정책을 구현하는 방법에 대한 자세한 내용은 Microsoft Tech Community에서 [이제 Azure NetApp Files에 Azure Policy를 사용할 수 있음](https://techcommunity.microsoft.com/t5/azure/azure-policy-now-available-for-azure-netapp-files/m-p/2282258)을 참조하세요. 

## <a name="which-unicode-character-encoding-is-supported-by-azure-netapp-files-for-the-creation-and-display-of-file-and-directory-names"></a>파일 및 디렉터리 이름을 만들고 표시하기 위해 Azure NetApp Files에서 어떤 유니코드 문자 인코딩을 지원하나요?   

Azure NetApp Files는 NFS 및 SMB 볼륨 모두에 대해 UTF-8 유니코드 문자 인코딩 형식으로 인코딩된 파일 및 디렉터리 이름만 지원합니다.

UTF-8에서 지원되지 않는 비정규 문자 및 이모지와 같은 보조 문자 또는 서로게이트 쌍을 사용하는 이름으로 파일 또는 디렉터리를 만들려고 하면 작업이 실패합니다. 이 경우 Windows 클라이언트에서 "지정한 파일 이름이 잘못되었거나 너무 깁니다. 다른 파일 이름을 지정하세요."와 같은 오류가 표시될 수 있습니다. 

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
- [애플리케이션 복원력 FAQ](faq-application-resilience.md)
