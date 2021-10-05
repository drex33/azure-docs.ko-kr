---
title: Azure Portal을 사용한 Azure 가상 머신의 유지 관리 제어에 대한 개요
description: 유지 관리 제어로 Azure VM에 유지 관리가 적용되는 시점을 제어하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 47d99e87e788c833e5793a1e4e3b7d517d90909b
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534990"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>유지 관리 제어를 통한 플랫폼 업데이트 관리 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

유지 관리 제어를 사용하여 다시 부팅할 필요가 없는 플랫폼 업데이트를 관리합니다. Azure는 안정성, 성능, 보안을 향상시키거나 새로운 기능을 시작하기 위해 인프라를 자주 업데이트합니다. 대부분의 업데이트는 사용자에게 투명합니다. 게임, 미디어 스트리밍 및 금융 트랜잭션과 같은 일부 중요한 워크로드에서는 유지 관리를 위해 VM이 몇 초 동안 멈추거나 연결이 끊어지는 것을 허용할 수 없습니다. 유지 관리 제어는 플랫폼 업데이트를 기다렸다가 35일 롤링 기간 내에 적용할 수 있는 옵션을 제공합니다. 

유지 관리 제어를 사용하면 업데이트를 격리된 VM 및 Azure 전용 호스트에 적용할 시기를 결정할 수 있습니다.

유지 관리 제어를 사용하면 다음을 수행할 수 있습니다.
- 업데이트를 하나의 업데이트 패키지로 일괄 처리합니다.
- 업데이트를 적용하려면 최대 35일 동안 기다립니다. 
- 유지 관리 일정을 구성하여 플랫폼 업데이트를 자동화합니다.
- 유지 관리 구성은 구성 및 리소스 그룹에서 작동합니다. 

## <a name="limitations"></a>제한 사항

- VM은 [전용 호스트](./dedicated-hosts.md)에 있거나 [격리된 VM 크기](isolation.md)를 사용하여 만들어져야 합니다.
- 유지 관리 기간 기간은 월에 따라 다를 수 있으며, 사용자가 시작한 후 보류 중인 업데이트를 적용 하는 데 2 시간이 걸릴 수 있습니다.  
- 35일 후에는 업데이트가 자동으로 적용됩니다.
- 랙 수준 유지 관리 작업은 아직이 유지 관리 제어의 일부가 아닙니다.
- 사용자에게 **리소스 참가자** 액세스 권한이 있어야 합니다.

## <a name="management-options"></a>관리 옵션

다음 옵션 중 하나를 사용하여 유지 관리 구성을 만들고 관리할 수 있습니다.

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure Portal](maintenance-control-portal.md)

Azure Functions 샘플은 [유지 관리 제어 및 Azure Functions를 사용한 유지 관리 업데이트 예약](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 [유지 관리 및 업데이트](maintenance-and-updates.md)를 참조하세요.
