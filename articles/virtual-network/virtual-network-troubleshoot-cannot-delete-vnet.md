---
title: Azure에서 가상 네트워크를 삭제할 수 없음 | Microsoft Docs
description: Azure에서 가상 네트워크를 삭제할 수 없는 문제를 해결하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b974af343907c98ebd7a318bc60a0e553a07a233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219354"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>문제 해결: Azure에서 가상 네트워크를 삭제하지 못함

Microsoft Azure에서 가상 네트워크를 삭제하려고 할 때 오류가 발생할 수 있습니다. 이 문서에서는 이 문제를 해결하는 데 도움이 되는 문제 해결 단계를 제공합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>문제 해결 지침 

1. [가상 네트워크에서 가상 네트워크 게이트웨이가 실행 중인지 확인](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network)합니다.
2. [가상 네트워크에서 애플리케이션 게이트웨이가 실행 중인지 확인](#check-whether-an-application-gateway-is-running-in-the-virtual-network)합니다.
3. [가상 네트워크에 Azure 컨테이너 인스턴스가 계속 있는지 확인](#check-whether-azure-container-instances-still-exist-in-the-virtual-network)합니다.
4. [가상 네트워크에서 Azure Active Directory Domain Service가 사용하도록 설정되어 있는지 확인](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network)합니다.
5. [가상 네트워크가 다른 리소스에 연결되어 있는지 확인](#check-whether-the-virtual-network-is-connected-to-other-resource)합니다.
6. [가상 네트워크에서 가상 컴퓨터가 여전히 실행 중인지 확인](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network)합니다.
7. [가상 네트워크가 마이그레이션 도중에 걸려 있는지 확인](#check-whether-the-virtual-network-is-stuck-in-migration)합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>가상 네트워크에서 가상 네트워크 게이트웨이가 실행 중인지 확인

가상 네트워크를 제거하려면 먼저 가상 네트워크 게이트웨이를 제거해야 합니다.

클래식 가상 네트워크의 경우 Azure Portal에서 클래식 가상 네트워크에 대한 **개요** 페이지로 이동합니다. 가상 네트워크에서 게이트웨이가 실행 중인 경우에는 **VPN 연결** 섹션에 게이트웨이의 IP 주소가 표시됩니다. 

![게이트웨이가 실행 중인지 확인](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

가상 네트워크의 경우 가상 네트워크에 대한 **개요** 페이지로 이동합니다. 가상 네트워크 게이트웨이에 대한 **연결된 디바이스** 를 확인합니다.

![Azure Portal에서 가상 네트워크에 연결된 장치 목록의 스크린샷. 가상 네트워크 게이트웨이가 목록에 강조 표시됩니다.](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

게이트웨이를 제거하려면 먼저 게이트웨이에서 **연결** 개체를 제거해야 합니다. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>가상 네트워크에서 애플리케이션 게이트웨이가 실행 중인지 확인

가상 네트워크에 대한 **개요** 페이지로 이동합니다. 애플리케이션 게이트웨이에 대한 **연결된 디바이스** 를 확인합니다.

![Azure Portal에서 가상 네트워크에 연결된 장치 목록의 스크린샷. 응용 프로그램 게이트웨이가 목록에 강조 표시됩니다.](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

애플리케이션 게이트웨이가 있는 경우 이를 제거해야 가상 네트워크를 삭제할 수 있습니다.

### <a name="check-whether-azure-container-instances-still-exist-in-the-virtual-network"></a>Azure 컨테이너 인스턴스가 가상 네트워크에 계속 있는지 확인

1. Azure Portal에서 리소스 그룹의 **개요** 페이지로 이동합니다.
1. 리소스 그룹의 리소스 목록에 대한 헤더에서 **숨겨진 형식 표시** 를 선택합니다. 네트워크 프로필 유형은 기본적으로 Azure Portal에서 숨겨집니다.
1. 컨테이너 그룹과 관련된 네트워크 프로필을 선택합니다.
1. **삭제** 를 선택합니다.

   ![숨겨진 네트워크 프로필 목록의 스크린샷입니다.](media/virtual-network-troubleshoot-cannot-delete-vnet/container-instances.png)

1. 서브넷 또는 가상 네트워크를 다시 삭제합니다.

이러한 단계를 수행해도 문제가 해결되지 않으면 다음 [Azure CLI 명령](../container-instances/container-instances-vnet.md#clean-up-resources)을 사용하여 리소스를 정리합니다. 

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>가상 네트워크에서 Azure Active Directory Domain Service가 사용하도록 설정되어 있는지 확인

Active Directory Domain Service가 사용하도록 설정되어 있고 가상 네트워크에 연결되어 있다면 이 가상 네트워크를 삭제할 수 없습니다. 

![Azure Portal의 Azure AD Domain Services 화면의 스크린샷. 가상 네트워크/서브넷에서 사용 가능한 필드가 강조 표시됩니다.](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

서비스를 사용하지 않도록 설정하려면 [Azure Portal을 사용하여 Azure Active Directory Domain Services 비활성화](../active-directory-domain-services/delete-aadds.md)를 참조하세요.

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>가상 네트워크가 다른 리소스에 연결되어 있는지 확인

서킷 링크, 연결 및 가상 네트워크 피어링이 있는지 확인합니다. 이러한 요소가 가상 네트워크를 삭제하지 못하는 원인일 수 있습니다. 

권장되는 삭제 순서는 다음과 같습니다.

1. 게이트웨이 연결
2. 게이트웨이
3. IP
4. 가상 네트워크 피어링
5. ASE(App Service Environment)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>가상 네트워크에서 가상 머신이 여전히 실행 중인지 확인

가상 네트워크에서 실행 중인 가상 컴퓨터가 없는지 확인합니다.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>가상 네트워크가 마이그레이션 도중에 걸려 있는지 확인

마이그레이션 상태에 걸려 있는 경우 가상 네트워크를 삭제할 수 없습니다. 다음 명령을 실행하여 마이그레이션 중단한 다음 가상 네트워크를 삭제합니다.

```azurepowershell
Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort
```

## <a name="next-steps"></a>다음 단계

- [Azure Virtual Network](virtual-networks-overview.md)
- [Azure Virtual Network FAQ(질문과 대답)](virtual-networks-faq.md)