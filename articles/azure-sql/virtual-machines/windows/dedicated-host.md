---
title: Azure 전용 호스트에서 SQL Server VM 실행
description: Azure 전용 호스트에서 SQL Server VM을 실행하는 방법에 대해 알아보세요.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: pamela
ms.reviewer: mathoma
ms.openlocfilehash: a53b35ef7371d6608283374cfef1086cf9f0bbfd
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162223"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>Azure 전용 호스트에서 SQL Server VM 실행 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 SQL Server 가상 머신(VM)을 [Azure 전용 호스트](../../../virtual-machines/dedicated-hosts.md)로 사용하는 방법에 대한 자세한 정보를 제공합니다. Azure 전용 호스트에 대한 자세한 내용은 [Azure 전용 호스트 소개](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/) 블로그 게시물에서 확인할 수 있습니다. 

## <a name="overview"></a>개요
[Azure Dedicated Host](../../../virtual-machines/dedicated-hosts.md)는 하나의 Azure 구독 전용 물리적 서버(하나 이상의 가상 머신을 호스트)를 제공하는 서비스입니다. 전용 호스트는 리소스로 제공되는, Microsoft 데이터 센터에서 사용되는 것과 동일한 물리적 서버입니다. 지역, 가용성 영역 및 장애 도메인 내에서 전용 호스트를 프로비저닝할 수 있습니다. 그런 다음 사용자의 요구에 가장 적합한 구성으로 프로비저닝된 호스트에 직접 VM을 배치할 수 있습니다.

## <a name="limitations"></a>제한 사항

- 모든 VM 시리즈가 전용 호스트에서 지원되는 것은 아니며 VM 시리즈 가용성은 지역에 따라 다릅니다. 자세한 내용은 [Azure Dedicated Host 개요](../../../virtual-machines/dedicated-hosts.md)를 참조하세요.

## <a name="licensing"></a>라이선스

SQL Server VM을 Azure 전용 호스트에 설치할 때 두 가지 라이선스 옵션 중에서 선택할 수 있습니다. 

  - **SQL VM 라이선스**: 각 SQL Server VM 라이선스에 대해 개별적으로 요금을 지불하는 기존 라이선스 옵션입니다. 
  - **전용 호스트 라이선스**: Azure 전용 호스트에서 사용할 수 있는 새 라이선스 모델은 SQL Server 라이선스를 호스트 단위로 묶어서 요금을 지불합니다. 


기존 SQL Server 라이선스 사용에 대한 호스트 수준 옵션: 
  - SQL Server Enterprise Edition Azure 하이브리드 혜택(AHB)
    - SA 또는 구독 고객에게 제공됩니다.
    - 모든 사용 가능한 물리적 코어에 라이선스를 제공하고 무제한 가상화를 사용할 수 있습니다(호스트에서 지원되는 최대 vCPU까지 제한).
        - AHB를 Azure 전용 호스트에 적용하는 방법에 대한 자세한 내용은 [Azure 하이브리드 혜택 FAQ](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)를 참조하세요. 
  - 10월 1일 전에 취득한 SQL Server 라이선스
      - SQL Server Enterprise Edition은 호스트 수준 옵션과 VM별 라이선스 옵션이 있습니다. 
      - SQL Server Standard Edition은 VM별 라이선스 옵션만 있습니다. 
          - 자세한 내용은 [Microsoft 제품 약관](https://www.microsoft.com/licensing/product-licensing/products)을 참조하세요. 
  - SQL Server 전용 호스트 수준 옵션을 선택하지 않을 경우 다중 테넌트 VM처럼 개별 VM 수준에서 SQL Server AHB를 선택할 수 있습니다.



## <a name="provisioning"></a>프로비전  
SQL Server VM을 전용 호스트에 프로비저닝하는 작업은 다른 Azure 가상 머신과 다르지 않습니다. 여기에는 [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md), [Azure Portal](../../../virtual-machines/dedicated-hosts-portal.md), [Azure CLI](../../../virtual-machines/linux/dedicated-hosts-cli.md)를 사용할 수 있습니다.

기존 SQL Server VM을 전용 호스트에 추가하는 과정에서 전용 호스트가 잠시 중단되지만 데이터에는 영향을 미치지 않으므로 데이터 손실은 발생하지 않습니다. 그러나 시스템 데이터베이스를 포함한 모든 데이터베이스는 이동 전에 백업해야 합니다.

## <a name="virtualization"></a>가상화 

전용 호스트의 혜택 중 하나로 무제한 가상화가 있습니다. 예를 들어 64개 vCore에 대한 라이선스가 있지만 호스트가 128개 vCore를 가지도록 구성할 수 있습니다. vCore 개수는 두 배가 되지만 SQL Server 라이선스에서 지불해야 하는 요금의 절반만 지불하면 됩니다. 

사용자의 호스트이기 때문에 1:2 비율로 가상화를 설정할 수 있습니다. 

## <a name="faq"></a>FAQ

**Q: Azure 전용 호스트의 Windows Server/SQL Server 라이선스에서 Azure 하이브리드 혜택은 어떻게 적용되나요?**

A: 고객은 기존 Windows Server 및 SQL Server 라이선스의 가치를 소프트웨어 보증 또는 해당 구독 라이선스와 사용하여 Azure 하이브리드 혜택을 적용하고 Azure 전용 호스트를 저렴한 요금에 이용할 수 있습니다. Windows Server Datacenter 및 SQL Server Enterprise Edition 고객은 전체 호스트에 대한 라이선스를 구매하고 Azure 하이브리드 혜택을 사용할 경우 무제한 가상화를 이용할 수 있습니다(기본 서버의 물리적 용량에 따라 호스트에 원하는 만큼 Windows Server 가상 머신 배포).  Azure 전용 호스트의 모든 Windows Server 및 SQL Server 워크로드도 추가 요금 없이 Windows Server 및 SQL Server 2008/R2의 확장된 보안 업데이트가 제공됩니다. 

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.yml)
* [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
* [Azure VM의 SQL Server 새로운 내용](doc-changes-updates-release-notes-whats-new.md)