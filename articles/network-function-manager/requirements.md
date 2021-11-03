---
title: Azure 네트워크 기능 관리자의 필수 구성 요소 및 요구 사항
description: 네트워크 기능 관리자에 대 한 요구 사항 및 필수 구성 요소에 대해 알아봅니다.
author: prmitt
ms.service: network-function-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8843c92943071a96c6e0e9340823a85d01b1c218
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102997"
---
# <a name="network-function-manager-prerequisites-and-requirements"></a>네트워크 기능 관리자 필수 구성 요소 및 요구 사항

이 문서는 Azure 네트워크 기능 관리자를 구성 하 고 사용 하는 데 필요한 필수 구성 요소 및 요구 사항을 이해 하는 데 도움이 됩니다.

## <a name="azure-stack-edge-pro-with-gpu-installed-and-activated"></a><a name="edge-pro"></a>Azure Stack Edge Pro with GPU가 설치 및 활성화됨

다음 필수 구성 요소가 있는지 확인 합니다.

* Azure 네트워크 기능 관리자 서비스는 Azure Stack Edge Pro 장치에서 사용 하도록 설정 됩니다.
* 네트워크 기능을 배포하기 전에 Azure Stack Edge Pro가 설치되고 활성화되었는지 확인합니다.
* Azure Stack Edge 리소스는 네트워크 기능 관리자 리소스에서 지 원하는 지역에 배포 되어야 합니다. 자세한 내용은 [지역 가용성](overview.md#regions)을 참조하세요.
* Azure Stack Edge Pro [빠른 시작](../databox-online/azure-stack-edge-gpu-quickstart.md) 및 [자습서](../databox-online/azure-stack-edge-gpu-deploy-checklist.md)의 모든 단계를 수행해야 합니다.
* Azure 관리 포털에서 Azure Stack Edge 리소스의 속성 섹션에 있는 장치 **상태가** **온라인** 인지 확인 합니다.

   :::image type="content" source="./media/overview/properties.png" alt-text="속성 창의 스크린샷" lightbox="./media/overview/properties.png":::

## <a name="partner-prerequisites"></a><a name="partner-prereq"></a>파트너 필수 조건

고객은 하나 이상의 Network Function Manager [파트너](partners.md) 중에서 선택하여 Azure Stack Edge 디바이스에 네트워크 기능을 배포할 수 있습니다. 네트워크 기능 관리자 파트너에 대 한 자세한 내용은 [파트너](partners.md) 문서를 참조 하세요.

각 파트너에는 Azure Stack Edge 디바이스에 네트워크 기능을 배포하기 위한 네트워킹 요구 사항이 있습니다. 다음 구성 작업을 완료하려면 네트워크 기능 파트너의 제품 설명서를 참조하세요.

* [다양한 포트에서 네트워크를 구성합니다](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
* [Azure Stack Edge 디바이스에서 컴퓨팅 네트워크를 사용하도록 설정합니다](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network).

## <a name="azure-account"></a><a name="account"></a>Azure 계정

Azure 네트워크 기능 관리자 파트너가 동일한 Azure 구독 ID를 사용 하도록 설정 하 여 Azure Marketplace에서 네트워크 기능을 배포 해야 합니다. Azure 구독 ID가 파트너와 등록 되는지 확인 합니다.

Azure Network Function Manager 서비스는 Network Function Manager **디바이스** 및 Network Function Manager **네트워크 기능** 리소스로 구성됩니다. 디바이스 및 네트워크 기능 리소스는 Azure 구독 내에 있습니다. Azure Stack Edge Pro 디바이스 및 Network Function Manager 리소스를 활성화하는 데 사용되는 Azure 구독 ID는 동일해야 합니다.

## <a name="port-requirements-and-firewall-rules"></a><a name="port-firewall"></a>포트 요구 사항 및 방화벽 규칙

Azure Stack Edge에서 실행되는 NFM(Network Function Manager) 서비스는 관리 트래픽이 네트워크 기능을 배포하려면 NFM 클라우드 서비스에 대한 아웃바운드 연결이 필요합니다. NFM은 Azure Stack Edge 서비스와 완전히 통합됩니다. [Azure Stack Edge](../databox-online/azure-stack-edge-gpu-system-requirements.md#networking-port-requirements) 디바이스의 네트워킹 포트 요구 사항과 방화벽 규칙을 검토합니다.  

네트워크 기능 파트너에는 파트너 관리 포털에 대한 트래픽을 관리하기 위한 방화벽 및 포트 구성 규칙의 다양한 요구 사항이 있습니다. 특정 요구 사항에 관해서는 네트워크 기능 파트너에 문의하세요.

## <a name="next-steps"></a>다음 단계

[자습서: 네트워크 함수 관리자 장치 리소스를 만듭니다](create-device.md).
