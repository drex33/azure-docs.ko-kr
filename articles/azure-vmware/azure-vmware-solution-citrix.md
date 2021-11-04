---
title: Azure VMware Solution Citrix 배포
description: Azure VMware Solution VMware Citrix를 배포하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d3acd7ac8772a3d6e501b43f7277bcb57d8cd08
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052792"
---
# <a name="deploy-citrix-on-azure-vmware-solution"></a>Azure VMware Solution Citrix 배포

Citrix Virtual Apps 및 데스크톱 서비스는 Azure VMware Solution 지원합니다. Azure VMware Solution Azure 인프라에서 만든 vSphere 클러스터를 포함하는 클라우드 인프라를 제공합니다. Citrix Virtual Apps 및 데스크톱 서비스를 활용하여 온-프레미스 환경에서 vSphere를 사용하는 것과 동일한 방식으로 [VDA(가상 배달 에이전트)](https://www.citrix.com/downloads/xendesktop/components/xendesktop-and-xenapp-76-VDA.html) 워크로드를 프로비전하는 데 Azure VMware Solution 사용할 수 있습니다. 

[Citrix 가상 앱 및 데스크톱에 대해 자세히 알아보기](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/)

[배포 가이드](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-vmware-solution-avs-integration)

[솔루션 개요](https://www.citrix.com/content/dam/citrix/en_us/documents/solution-brief/citrix-virtual-apps-and-desktop-service-on-azure-vmware-solution.pdf)

**FAQ(Q&검토)**
 
- 17. 기존 Citrix 데스크톱 및 앱을 Azure VMware Solution 마이그레이션하거나 온-프레미스 및 Azure VMware Solution 기반 Citrix 워크로드로 구성된 하이브리드 환경을 운영할 수 있나요? 

    A. 예. 현재 사용하는 동일한 컴퓨터 이미지, 애플리케이션 패키지 및 프로세스를 사용할 수 있습니다. 마이그레이션을 위해 온-프레미스 및 Azure VMware Solution 기반 환경을 원활하게 연결할 수 있습니다.

- 17. Citrix를 Azure VMware Solution 내에서 독립 실행형 환경으로 배포할 수 있나요? 

    A. 예. 자유롭게 하이브리드 환경을 마이그레이션하거나 운영하거나 독립 실행형을 Azure VMware Solution 

- 17. Azure VMware Solution PVS와 MCS를 모두 지원하나요? 

    A. Yes 

- 17. Azure VMware Solution Citrix에서 GPU 기반 워크로드가 지원하나요? 

    A. 지금은 없습니다. 그러나 Microsoft Azure Citrix 워크로드는 사용 사례가 중요한 경우 GPU를 지원합니다. 

- 17. 온-프레임 Citrix 배포 또는 LTSR에서 Azure VMware Solution 지원되는가요?  

    A. 아니요.  Azure VMware Solution Citrix Virtual Apps 및 데스크톱 서비스 제품에서만 지원됩니다.  

- 17. 지원을 요청해야 Who? 

    A. 고객은 Citrix 지원 www.citrix.com/support 문의하여 도움을 받아야 합니다. 

- 17. Azure VMware Solution Citrix에서 Microsoft의 Azure Virtual Desktop 혜택을 사용할 수 있나요?
 
    A. 아니요. Azure Virtual Desktop 혜택은 네이티브 Microsoft Azure 워크로드에만 적용됩니다. Citrix Virtual Apps 및 Desktops 서비스는 네이티브 Azure 제품으로서 Azure VMware Solution 배포와 함께 Azure Virtual Desktop 혜택을 적용할 수 있습니다. 

- 17. Azure VMware Solution 사용하기 위해 Citrix Virtual Apps 및 Desktops 서비스를 어떻게 할까요? 구매하나요? 
    
    A. Citrix 파트너를 통해 또는 Azure Marketplace 직접 Citrix 제품을 구매할 수 있습니다. 
