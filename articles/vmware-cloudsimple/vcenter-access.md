---
title: Azure VMware Solution by CloudSimple - vSphere 클라이언트에 액세스
description: 프라이빗 클라우드의 vCenter에 액세스하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a11567f18e2c62cfac67b6c5fde7e9c6ed1ce26a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618670"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>프라이빗 클라우드 vCenter 포털에 액세스

Azure Portal 또는 CloudSimple 포털에서 프라이빗 클라우드 vCenter 포털을 시작할 수 있습니다.  vCenter 포털을 사용하면 프라이빗 클라우드에서 VMware 인프라를 관리할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

vCenter 포털에 액세스하려면 네트워크 연결을 설정하고 DNS 이름 확인을 사용하도록 설정해야 합니다.  아래 옵션 중 하나를 사용하여 프라이빗 클라우드에 대한 네트워크 연결을 설정할 수 있습니다.

* [ExpressRoute를 사용하여 온-프레미스에서 CloudSimple로 연결](on-premises-connection.md)
* [CloudSimple 프라이빗 클라우드에 대한 VPN 연결 구성](set-up-vpn.md)

프라이빗 클라우드 VMware 인프라 구성 요소의 DNS 이름 확인을 설정하려면 [온-프레미스 워크스테이션에서 프라이빗 클라우드 vCenter 액세스에 대한 이름 확인을 위한 DNS 구성](on-premises-dns-setup.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-vcenter-from-azure-portal"></a>Azure Portal에서 vCenter에 액세스

Azure Portal에서 프라이빗 클라우드의 vCenter 포털을 시작할 수 있습니다.

1. **모든 서비스** 를 선택합니다.

2. **CloudSimple Services** 를 검색합니다.

3. 연결하려는 프라이빗 클라우드의 CloudSimple 서비스를 선택합니다.

4. **개요** 페이지에서 **VMware 프라이빗 클라우드 보기** 를 클릭합니다.

    ![CloudSimple 서비스 개요](media/cloudsimple-service-overview.png)

5. 프라이빗 클라우드 목록에서 프라이빗 클라우드를 선택하고 **vSphere 클라이언트 시작** 을 클릭합니다.

    ![vSphere 클라이언트 시작](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>CloudSimple 포털에서 vCenter에 액세스

CloudSimple 포털에서 프라이빗 클라우드의 vCenter 포털을 시작할 수 있습니다.

1. [CloudSimple 포털](access-cloudsimple-portal.md)에 액세스합니다.

2. **리소스** 에서 액세스하려는 프라이빗 클라우드를 선택하고 **vSphere 클라이언트 시작** 을 클릭합니다.

    ![vSphere 클라이언트 시작 - 리소스](media/cloudsimple-portal-resources-launch-vcenter.png)

3. 프라이빗 클라우드의 요약 화면에서 vCenter 포털을 시작할 수도 있습니다.

    ![vSphere 클라이언트 시작 - 요약](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드에 대한 VLAN/서브넷 만들기 및 관리](create-vlan-subnet.md)
* [VMware vCenter의 CloudSimple 프라이빗 클라우드 권한 모델](learn-private-cloud-permissions.md)