---
title: '자습서: 여러 서브넷의 AG에 대한 필수 구성 요소'
description: '이 자습서에서는 Azure VMs(Virtual Machines)의 SQL Server에 대해 여러 서브넷에서 Always On 가용성 그룹을 만들기 위한 필수 구성 요소를 구성하는 방법을 보여 줍니다. '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 4d1ec223a745f49518a7936bc85d40f470739c80
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132160023"
---
# <a name="tutorial-prerequisites-for-availability-groups-in-multiple-subnets-sql-server-on-azure-vms"></a>자습서: 여러 서브넷의 가용성 그룹에 대한 필수 구성 요소(Azure VM의 SQL Server) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> SQL Server VM을 동일한 Azure 가상 네트워크 내의 여러 서브넷에 만들면 Always On 가용성 그룹에 대한 Azure Load Balancer 또는 DNN(분산 네트워크 이름)이 필요하지 않습니다.

이 자습서에서는 [Azure VMs(Virtual Machines)의 SQL Server에 대한 Always On 가용성 그룹](availability-group-manually-configure-tutorial-multi-subnet.md)을 여러 서브넷에 만들기 위한 필수 구성 요소를 완료합니다. 이 자습서가 완료되면 도메인 컨트롤러가 두 개의 Azure 가상 머신에 있고, 두 개의 SQL Server VM이 여러 서브넷에 있으며, 하나의 스토리지 계정이 단일 리소스 그룹에 있습니다. 

**예상 시간**: 이 자습서에서는 Azure에서 여러 리소스를 만들고 완료하는 데 최대 30분이 걸릴 수 있습니다. 

다음 다이어그램에서는 이 자습서에서 배포하는 리소스를 보여 줍니다. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/multi-subnet-availability-group-diagram.png" alt-text="이 자습서에서 배포하는 리소스를 보여 주는 다이어그램":::


## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

- Azure 구독 [무료 Azure 계정을 열거나](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic) 또는 [Visual Studio 구독자 혜택을 활성화](/visualstudio/subscriptions/subscriber-benefits)할 수 있습니다.
- [SQL Server의 Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)에 대한 기본적인 이해 및 숙지 


## <a name="create-resource-group"></a>리소스 그룹 만들기

Azure Portal에서 리소스 그룹을 만들려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **+ 리소스 만들기** 를 선택하여 포털에서 새 리소스를 만듭니다.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-portal-plus.png" alt-text="+ 리소스 만들기를 선택하여 포털에서 새 리소스를 만듦":::

1. **Marketplace** 검색 상자에서 **리소스 그룹** 을 검색하고, Microsoft의 **리소스 그룹** 타일을 선택합니다. **리소스 그룹** 페이지에서 **만들기** 를 선택합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-resource-group-search.png" alt-text="Marketplace에서 리소스 그룹을 검색한 다음, 리소스 그룹을 만들도록 선택함":::

1. **리소스 그룹 만들기** 페이지에서 값을 입력하여 리소스 그룹을 만듭니다.
   1. 드롭다운에서 적절한 Azure 구독을 선택합니다. 
   1. 리소스 그룹 이름(예: **SQL-HA-RG**)을 제공합니다.
   1. 드롭다운에서 지역(예: **미국 서부 2**)을 선택합니다. 모든 후속 리소스도 이 위치에 배포해야 합니다. 
   1. **검토 + 만들기** 를 선택하여 리소스 매개 변수를 검토한 다음, **만들기** 를 선택하여 리소스 그룹을 만듭니다.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-resource-group-create-complete.png" alt-text="값을 입력하여 Azure Portal에서 리소스 그룹을 만듦":::


## <a name="create-network-and-subnets"></a>네트워크 및 서브넷 만들기

다음으로, 가상 네트워크와 세 개의 서브넷을 만듭니다. 자세한 내용은 [가상 네트워크 개요](../../../virtual-network/virtual-networks-overview.md)를 참조하세요. 

Azure Portal에서 가상 네트워크를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하여 **+ 만들기** 를 선택합니다.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="리소스 그룹에 새 리소스 만들기":::

1. **Marketplace** 검색 상자에서 **가상 네트워크** 를 검색하고, Microsoft의 **가상 네트워크** 타일을 선택합니다. **가상 네트워크** 페이지에서 **만들기** 를 선택합니다.  
1. **가상 네트워크 만들기** 페이지의 **기본 사항** 탭에 다음 정보를 입력합니다. 
   1. **프로젝트 세부 정보** 아래에서 적절한 Azure **구독** 및 이전에 만든 **리소스 그룹**(예: **SQL-HA-RG**)을 선택합니다. 
   1. **인스턴스 세부 정보** 아래에서 가상 네트워크 이름(예: **SQLHAVNET**)을 제공하고, 드롭다운에서 리소스 그룹과 동일한 지역을 선택합니다.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/03-create-vnet-basics.png" alt-text="이전에 만든 리소스 그룹을 선택한 다음, SQLHAVNET과 같은 가상 네트워크 이름을 제공함":::

1. **IP 주소** 탭에서 **기본** 서브넷을 선택하여 **서브넷 편집** 페이지를 엽니다. 도메인 컨트롤러 서브넷에 사용할 이름을 **DC-subnet** 으로 변경합니다. **저장** 을 선택합니다.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/04-create-vnet-ip-address-rename-default-subnet.png" alt-text="IP 주소 탭에서 기본 서브넷을 선택하여 서브넷 수정 페이지 열기. 도메인 컨트롤러 서브넷에 사용할 이름을 DC-subnet으로 변경. 저장 선택":::

1. **+ 서브넷 추가** 를 선택하여 첫 번째 SQL Server VM에 대한 추가 서브넷을 추가하고, 다음 값을 입력합니다. 
   1. **서브넷 이름** 값(예: **SQL-subnet-1**)을 제공합니다. 
   1. 가상 네트워크 주소 공간 내에서 고유한 서브넷 주소 범위를 제공합니다. 예를 들어 DC-subnet 주소 범위의 세 번째 8진수를 1로 반복할 수 있습니다. 
      - 예를 들어 **DC-subnet** 범위가 *10.38.0.0/24* 인 경우 **SQL-subnet-1** 에 대해 `10.38.1.0/24` IP 주소 범위를 입력합니다. 
      - 마찬가지로 **DC-subnet** IP 범위가 *10.5.0.0/24* 인 경우 새 서브넷에 대해 `10.5.1.0/24`를 입력합니다. 
   1. **추가** 를 선택하여 새 서브넷을 추가합니다. 

     :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/05-create-vnet-ip-address-add-sql-subnet-1.png" alt-text="DC-subnet IP 주소가 10.5.0.0이면 새 서브넷이 10.5.1.0이 되도록 첫 번째 서브넷의 이름(예: sql-subnet-1)을 지정한 다음, 세 번째 8진수를 1로 반복함":::

1. 이전 단계를 반복하여 **SQL-subnet-2** 와 같은 이름의 두 번째 SQL Server VM에 대한 고유한 추가 서브넷 범위를 추가합니다. 세 번째 8진수를 1로 다시 반복할 수 있습니다. 
   - 예를 들어 **DC-subnet** IP 범위가 *10.38.0.0/24* 이고 **SQL-subnet-1** 이 *10.38.1.0/24* 인 경우 새 서브넷에 대해 `10.38.2.0/24`를 입력합니다.
   - 마찬가지로 **DC-subnet** IP 범위가 *10.5.0.0/24* 이고 **SQL-subnet-1** 이 *10.5.1.0/24* 인 경우 **SQL-subnet-2** 에 대해 `10.5.2.0/24` IP 주소 범위를 입력합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/06-create-vnet-ip-address-add-sql-subnet-2.png" alt-text="DC-subnet IP 주소가 10.38.0.0/24이면 새 서브넷이 10.38.2.0/24가 되도록 두 번째 서브넷의 이름(예: sql-subnet-2)을 지정한 다음, 세 번째 8진수를 2로 반복함":::

1. 두 번째 서브넷을 추가한 후 서브넷 이름과 범위를 검토합니다(IP 주소 범위는 이미지와 다를 수 있음). 모든 항목이 올바르게 표시되면 **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택하여 새 가상 네트워크를 만듭니다. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/07-create-vnet-ip-address.png" alt-text="두 번째 서브넷을 추가한 후 이미지 예와 같이 서브넷 이름과 범위를 검토함(IP 주소는 다를 수 있음). 모든 항목이 올바르게 표시되면 검토 + 만들기, 만들기를 차례로 선택하여 새 가상 네트워크를 만듦":::

   포털 대시보드가 반환되고 새 네트워크가 만들어지는 시점을 사용자에게 알립니다.


## <a name="create-domain-controllers"></a>도메인 컨트롤러 만들기

네트워크와 서브넷이 준비되면 가상 머신(또는 고가용성을 위해 선택적으로 두 개)을 만들고 이를 도메인 컨트롤러로 구성합니다. 

### <a name="create-dc-virtual-machines"></a>DC 가상 머신 만들기

Azure Portal에서 DC(도메인 컨트롤러) 가상 머신을 만들려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하여 **+ 만들기** 를 선택합니다.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="리소스 그룹에 새 리소스 만들기":::

1. **Marketplace** 검색 상자에서 **Windows Server** 를 검색합니다. 
1. Microsoft의 **Windows Server** 타일에서 **만들기** 드롭다운을 선택하고, **Windows Server 2016 Datacenter** 이미지를 선택합니다. 
1. **가상 머신 만들기** 페이지의 값을 입력하여 도메인 컨트롤러 VM(예: **DC-VM-1**)을 만듭니다. 필요에 따라 추가 VM(예: **DC-VM-2**)을 만들어 Active Directory Domain Services에 대한 고가용성을 제공합니다. 다음 표의 값을 사용하여 VM을 만듭니다.

    | **필드** | 값 |
    | --- | --- |
    | **구독** |*구독* |
    | **리소스 그룹** |SQL-HA-RG |
    | **가상 머신 이름** |첫 번째 도메인 컨트롤러: *DC-VM-1*</br>두 번째 도메인 컨트롤러 *DC-VM-2* |
    | **지역** |*리소스 그룹 및 가상 네트워크를 배포한 위치입니다.* |
    | **가용성 옵션** |가용성 영역 </br> *가용성 영역을 지원하지 않는 Azure 지역의 경우 가용성 집합을 대신 사용합니다. 새 가용성 집합을 만들고, 이 자습서에서 만든 모든 VM을 이 가용성 집합 내에 배치합니다.* |
    | **가용성 영역** |DC-VM-1에 대해 1을 지정합니다. </br> DC-VM-2에 대해 2를 지정합니다. |
    | **Size** |D2s_v3(2개 vCPU, 8GB RAM) |
    | **사용자 이름** |DomainAdmin |
    | **암호** |Contoso!0000 |
    | **퍼블릭 인바운드 포트** | *선택한 포트 허용* |
    | **인바운드 포트 선택** | *RDP(3389)* |
    | **OS 디스크 유형** | 프리미엄 SSD(로컬 중복 스토리지) |
    | **가상 네트워크** |SQLHAVNET |
    | **서브넷** |DC-subnet |
    | **공용 IP** |*VM과 동일한 이름(예: DC-VM-1 또는 DC-VM-2)* |
    | **NIC 네트워크 보안 그룹 추가**| Basic |
    | **퍼블릭 인바운드 포트**| 선택한 포트 허용 |
    | **인바운드 포트 선택**| RDP(3389) |
    | **부트 진단** |관리되는 스토리지 계정을 통해 사용하도록 설정합니다(권장). |

    가상 머신을 만들고 사용할 준비가 되면 Azure에서 알려줍니다. 


### <a name="configure-the-domain-controller"></a>도메인 컨트롤러 구성

DC 가상 머신이 준비되면 corp.contoso.com에 대한 도메인 컨트롤러를 구성합니다.

**DC-VM-1** 을 도메인 컨트롤러로 구성하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하여 **DC-VM-1** 컴퓨터를 선택합니다. 
1. **DC-VM-1** 페이지에서 **연결** 을 선택하여 원격 데스크톱 액세스를 위한 RDP 파일을 다운로드한 다음, 해당 파일을 엽니다.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/08-dc-vm-1-rdp-connect.png" alt-text="가상 머신에 연결":::

1. 구성한 관리자 계정(**DomainAdmin**)과 암호(**Contoso!0000**)를 사용하여 RDP 세션에 연결합니다.
1. **서버 관리자** 대시보드를 열고(기본적으로 열려 있을 수 있음), **역할 및 기능 추가** 를 선택합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="서버 관리자 - 역할 추가":::

1. **서버 역할** 섹션으로 이동할 때까지 **다음** 을 선택합니다.
1. **Active Directory Domain Services** 및 **DNS 서버** 역할을 선택합니다. 메시지가 표시되면 이러한 역할에 필요한 기능을 더 추가합니다.

   > [!NOTE]
   > 정적 IP 주소가 없다는 경고가 표시됩니다. 구성을 테스트하는 경우 **계속** 을 선택합니다. 프로덕션 시나리오의 경우 Azure Portal에서 IP 주소를 고정으로 설정하거나 [PowerShell을 사용하여 도메인 컨트롤러 컴퓨터의 고정 IP 주소를 설정](/azure/virtual-network/virtual-networks-static-private-ip-arm-ps)합니다.
   >

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/10-add-roles.png" alt-text="역할 추가 대화 상자":::

1. **확인** 섹션에 도달할 때까지 **다음** 을 선택합니다. **필요한 경우 자동으로 대상 서버 다시 시작** 확인란을 선택합니다.
1. **설치** 를 선택합니다.
1. 기능 설치를 완료한 후에는 **서버 관리자** 대시보드로 돌아갑니다.
1. 왼쪽 창에서 새 **AD DS** 옵션을 선택합니다.
1. 노란색 경고 표시줄에서 **자세히** 링크를 선택합니다.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/11-ad-ds-more.png" alt-text="DNS 서버 VM의 AD DS 대화 상자":::
    
1. **모든 서버 작업 세부 정보** 대화 상자의 **작업** 열에서 **이 서버를 도메인 컨트롤러로 승격** 을 선택합니다.
1. **Active Directory Domain Services 구성 마법사** 에서 다음 값을 사용합니다.

    | **호출** | 설정 |
    | --- | --- |
    | **배포 구성** |**새 포리스트 추가**<br/> **루트 도메인 이름** = corp.contoso.com |
    | **도메인 컨트롤러 옵션** |**암호** = Contoso!0000<br/>**암호 확인** = Contoso!0000 |

1. **다음** 을 선택하여 마법사의 다른 페이지를 진행합니다. **필수 구성 요소 확인** 페이지에서 다음 메시지가 표시되는지 확인합니다. **모든 필수 구성 요소 검사를 마쳤습니다**. 해당하는 모든 경고 메시지를 검토할 수 있지만 설치를 계속할 수 있습니다.
1. **설치** 를 선택합니다. **DC-VM-1** 가상 머신이 자동으로 다시 부팅됩니다.

### <a name="identify-dns-ip-address"></a>DNS IP 주소 확인

DNS에 대한 주 도메인 컨트롤러를 사용합니다. 이렇게 하려면 주 도메인 컨트롤러에 사용되는 VM의 개인 IP 주소를 식별합니다. 

Azure Portal에서 VM의 개인 IP 주소를 확인하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하여 **DC-VM-1** 주 도메인 컨트롤러를 선택합니다. 
1. **DC-VM-1** 페이지의 **설정** 창에서 **네트워킹** 을 선택합니다. 
1. **NIC 개인 IP** 주소를 적어 둡니다. 이 IP 주소를 다른 가상 머신에 대한 DNS 서버로 사용합니다.  이미지 예에서 개인 IP 주소는 **10.38.0.4** 입니다. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-dc-vm-1-private-ip.png" alt-text="DC-VM-1 페이지의 설정 창에서 네트워킹을 선택한 다음, NIC 개인 IP 주소를 적어 둠. 이 IP 주소를 DNS 서버로 사용함":::

### <a name="configure-virtual-network-dns"></a>가상 네트워크 DNS 구성

첫 번째 도메인 컨트롤러가 만들어지고 DNS가 사용하도록 설정되면 이 VM을 DNS에 사용하도록 가상 네트워크를 구성합니다.

DNS에 대한 가상 네트워크를 구성하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하여 가상 네트워크(예: **SQLHAVNET**)를 선택합니다. 
1. **설정** 창 아래에서 **DNS 서버** 를 선택한 다음, **사용자 지정** 을 선택합니다. 
1. **IP 주소** 필드에서 이전에 확인한 개인 IP 주소(예: `10.38.0.4`)를 입력합니다. 
1. **저장** 을 선택합니다. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-identify-dns-ip-address.png" alt-text=" 설정 창 아래에서 DNS 서버를 선택한 다음, 사용자 지정을 선택함. IP 주소 필드에서 이전에 확인한 개인 IP 주소(예: 10.38.0.4)를 입력함":::


### <a name="configure-second-domain-controller"></a>두 번째 도메인 컨트롤러 구성

주 도메인 컨트롤러가 다시 부팅되면 필요에 따라 고가용성을 위해 두 번째 도메인 컨트롤러를 구성할 수 있습니다. 두 번째 도메인 컨트롤러를 구성하지 않으려면 이 단계를 건너뜁니다. 그러나 프로덕션 환경에서는 두 번째 도메인 컨트롤러를 사용하는 것이 좋습니다. 

기본 설정 DNS 서버 주소를 설정하고, 도메인에 조인한 다음, 보조 도메인 컨트롤러를 구성합니다. 

#### <a name="set-preferred-dns-server-address"></a>기본 설정 DNS 서버 주소 설정

먼저 기본 설정된 DNS 서버 주소를 변경합니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하여 두 번째 도메인 컨트롤러 컴퓨터(예: **DC-VM-2**)를 선택합니다. 
1. **DC-VM-2** 페이지에서 **연결** 을 선택하여 원격 데스크톱 액세스를 위한 RDP 파일을 다운로드한 다음, 해당 파일을 엽니다. 
1. 구성한 관리자 계정(**DomainAdmin**)과 암호(**Contoso!0000**)를 사용하여 RDP 세션에 연결합니다.
1. **네트워크 및 공유 센터** 를 열고, 네트워크 인터페이스를 선택합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/13-network-interface.png" alt-text="네트워크 인터페이스":::

1. **속성** 페이지를 엽니다. 
1. **인터넷 프로토콜 버전 4(TCP/IPv4)** 를 선택한 다음, **속성** 을 선택합니다.
1. **다음 DNS 서버 주소 사용** 을 선택한 다음, **기본 설정 DNS 서버** 에서 주 도메인 컨트롤러의 개인 IP 주소(예: `10.38.0.4`)를 지정합니다. 
1. **확인** 을 선택한 다음, **닫기** 를 선택하여 변경 내용을 커밋합니다. DNS IP 주소를 변경한 후에 원격 데스크톱 연결이 끊어지면 [Azure Portal](https://portal.azure.com)에서 가상 머신으로 이동하여 VM을 다시 시작합니다. 

### <a name="join-the-domain"></a>도메인 가입 

다음으로 **corp.contoso.com** 도메인에 조인합니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. **BUILTIN\DomainAdmin** 계정을 사용하여 가상 머신에 원격으로 연결합니다.
1. **서버 관리자** 를 열고, **로컬 서버** 를 선택합니다.
1. **작업 그룹** 을 선택합니다.
1. **컴퓨터 이름** 섹션에서 **변경** 을 선택합니다.
1. **도메인** 확인란을 선택하고, 텍스트 상자에서 **corp.contoso.com** 을 입력합니다. **확인** 을 선택합니다.
1. **Windows 보안** 팝업 대화 상자에서 기본 도메인 관리자 계정에 대한 자격 증명(**CORP\DomainAdmin**) 및 암호(**Contoso!0000**)를 지정합니다.
1. "corp.contoso.com 도메인 시작" 메시지가 표시되면 **확인** 을 선택합니다.
1. **닫기** 를 선택한 후 팝업 대화 상자에서 **지금 다시 시작** 을 선택합니다.


#### <a name="configure-domain-controller"></a>도메인 컨트롤러 구성 

서버가 도메인에 조인되면 이를 두 번째 도메인 컨트롤러로 구성할 수 있습니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. 아직 연결되지 않은 경우 보조 도메인 컨트롤러에 대한 RDP 세션을 열고, **서버 관리자 대시보드**(기본적으로 열려 있을 수 있음)를 엽니다.
1. 대시보드에서 **역할 및 기능 추가** 링크를 선택합니다.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="서버 관리자 - 역할 추가":::

1. **서버 역할** 섹션으로 이동할 때까지 **다음** 을 선택합니다.
1. **Active Directory Domain Services** 및 **DNS 서버** 역할을 선택합니다. 메시지가 표시되면 이러한 역할에 필요한 기능을 더 추가합니다.
1. 기능 설치를 완료한 후에는 **서버 관리자** 대시보드로 돌아갑니다.
1. 왼쪽 창에서 새 **AD DS** 옵션을 선택합니다.
1. 노란색 경고 표시줄에서 **자세히** 링크를 선택합니다.
1. **모든 서버 작업 세부 정보** 대화 상자의 **작업** 열에서 **이 서버를 도메인 컨트롤러로 승격** 을 선택합니다.
1. **배포 구성** 아래에서 **기존 도메인에 도메인 컨트롤러 추가** 를 선택합니다.
1. **선택** 을 클릭합니다.
1. 관리자 계정(**CORP.CONTOSO.COM\domainadmin**) 및 암호(**Contoso!0000**)를 사용하여 연결합니다.
1. **포리스트에서 도메인 선택** 에서 해당 도메인을 선택하고 **확인** 을 선택합니다.
1. **도메인 컨트롤러 옵션** 에서 기본값을 사용하고 DSRM 암호를 설정합니다.

    >[!NOTE]
    >**DNS 옵션** 페이지에서 이 DNS 서버에 대한 위임을 만들 수 없다고 경고할 수 있습니다. 비-프로덕션 환경에서 이 경고를 무시할 수 있습니다.
    >

1. 대화 상자가 **필수 구성 요소** 확인에 도달할 때까지 **다음** 을 선택합니다. **설치** 를 선택합니다.

서버가 구성 변경 내용을 완료한 후 서버를 다시 시작합니다.

### <a name="add-second-dc-ip-address-to-dns"></a>DNS에 두 번째 DC IP 주소 추가

두 번째 도메인 컨트롤러가 구성되면 이전과 동일한 단계에 따라 [VM의 개인 IP 주소를 확인](#identify-dns-ip-address)하고 리소스 그룹의 가상 네트워크에서 [개인 IP 주소를 보조 사용자 지정 DNS 서버로 추가](#configure-virtual-network-dns)합니다. Azure Portal에서 보조 DNS 서버가 추가되면 DNS 서비스의 중복성이 사용하도록 설정됩니다. 



## <a name="configure-domain-accounts"></a>도메인 계정 구성

도메인 컨트롤러가 구성되고 Azure Portal에서 DNS 서버가 설정되면 SQL Server를 설치하는 사용자와 SQL Server 서비스 계정에 대한 도메인 계정을 만듭니다. 

총 세 개의 계정, 두 SQL Server VM에 대한 하나의 설치 계정 및 각 SQL Server VM에 대한 서비스 계정을 구성합니다. 예를 들어 계정에 대해 다음 표의 값을 사용합니다.

|계정  | VM  |전체 도메인 이름  |설명   |
|---------|---------|---------|---------|
|설치    |모두| Corp\Install        |이 계정으로 VM에 로그인하여 클러스터 및 가용성 그룹을 구성합니다. |
|SQLSvc1     |SQL-VM-1 |Corp\SQLSvc1 | 이 계정을 첫 번째 SQL Server VM의 SQL Server 서비스에 사용합니다. |
|SQLSvc2     |SQL-VM2 |Corp\SQLSvc2| 이 계정을 두 번째 SQL Server VM의 SQL Server 서비스에 사용합니다.|

다음 단계에 따라 각 계정을 만듭니다. 

1. 주 도메인 컨트롤러 시스템(예: **DC-VM-1**)에 연결합니다. .
1. **서버 관리자** 에서 **도구** 를 선택한 후 **Active Directory 관리 센터** 를 선택합니다.
1. 왼쪽 창에서 **corp(로컬)** 을 선택합니다.
1. 오른쪽 **작업** 창에서 **새로 만들기** 를 선택한 후 **사용자** 를 선택합니다. 
1. 새 사용자 계정을 입력하고, 복잡한 암호를 설정합니다. 비-프로덕션 환경의 경우 사용자 계정이 만료되지 않도록 설정합니다.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/14-ad-dc-new-user.png" alt-text="Active Directory 관리 센터":::

1. **확인** 을 선택하여 사용자를 만듭니다.
1. 이러한 단계를 반복하여 세 개의 계정을 모두 만듭니다. 

### <a name="grant-installation-account-permissions"></a>설치 계정 권한 부여

계정이 만들어지면 해당 계정이 AD에서 개체를 만들 수 있도록 설치 계정에 필요한 도메인 권한을 부여합니다. 

권한을 설치 계정에 부여하려면 다음 단계를 수행합니다. 

1. 아직 열려 있지 않은 경우 **서버 관리자** 에서 **Active Directory 관리 센터** 를 엽니다. 
1. 왼쪽 창에서 **corp(로컬)** 를 선택합니다. 
1. 오른쪽 **작업** 창의 드롭다운에서 **corp(로컬)** 가 표시되는지 확인한 다음, 아래에서 **속성** 을 선택합니다.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/15-ad-dc-properties.png" alt-text="CORP 사용자 속성":::

1. **확장** 을 선택한 후 **보안** 탭에서 **고급** 단추를 선택합니다.
1. **corp 고급 보안 설정** 대화 상자에서 **추가** 를 선택합니다.
1. **보안 주체 선택** 을 선택하고, **CORP\Install** 을 검색한 다음, **확인** 을 선택합니다.
1. **모든 속성 읽기** 및 **컴퓨터 개체 만들기** 옆의 확인란을 선택합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/16-add-permissions.png" alt-text="Corp 사용자 권한":::

1. **확인** 을 선택한 후 **만들기** 를 선택합니다. **corp** 속성 창을 닫습니다.

이제 Active Directory 및 사용자 개체 구성을 완료했으므로 SQL Server VM을 만들 준비가 되었습니다. 

## <a name="create-sql-server-vms"></a>SQL Server VM 만들기

AD, DNS 및 사용자 계정이 구성되면 SQL Server VM을 만들 준비가 된 것입니다. 간단히 하기 위해 마켓플레이스의 SQL Server VM 이미지를 사용합니다. 

그러나 SQL Server VM을 만들기 전에 다음 디자인 결정을 고려합니다. 

**가용성 - 가용성 영역**   

가장 높은 수준의 중복성, 복원력 및 가용성을 위해 VM을 별도의 가용성 영역 내에 배포합니다. 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹이 있는 하나 이상의 데이터 센터로 구성됩니다. 아직 가용성 영역을 지원하지 않는 Azure 지역의 경우 가용성 집합을 대신 사용합니다. 모든 VM을 동일한 가용성 집합 내에 배치합니다. 

**스토리지 - Azure Managed Disks**   

가상 머신 스토리지에 대해 Azure Managed Disks를 사용합니다. Microsoft는 스토리지를 백그라운드에서 처리하는 SQL Server 가상 머신에 대한 Managed Disks를 권장합니다. 자세한 내용은 [Azure Managed Disks 개요](../../../virtual-machines/managed-disks-overview.md)를 참조하세요. 

**네트워크 - 프로덕션 환경의 개인 IP 주소**   

이 자습서에서는 가상 머신에 대해 공용 IP 주소를 사용합니다. 공용 IP 주소를 사용하면 인터넷을 통해 가상 머신에 직접 원격으로 연결할 수 있으므로 구성 단계가 좀 더 용이해집니다. 프로덕션 환경에서는 SQL Server 인스턴스 VM 리소스의 취약성을 줄이기 위해 개인 IP 주소만 권장됩니다.

**네트워크 - 서버당 단일 NIC**   

서버당 단일 NIC(클러스터 노드)를 사용합니다. Azure 네트워킹에는 물리적 중복성이 있으므로 Azure 가상 머신에 배포된 장애 조치(failover) 클러스터에는 추가 NIC가 필요하지 않습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결할 수 있다는 경고를 표시합니다. 장애 조치(failover) 클러스터가 Azure 가상 머신에 있는 경우 이 경고는 무시할 수 있습니다. 

VM을 만들려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하여 **+ 만들기** 를 선택합니다.
1. **Azure SQL** 을 검색하고, Microsoft의 **Azure SQL** 타일을 선택합니다. 
1. **Azure SQL** 페이지에서 **만들기** 를 선택한 다음, 드롭다운에서 **Windows Server 2016의 SQL Server 2016 SP2 Enterprise** 이미지를 선택합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/18-select-sql-vm-image.png" alt-text=" 포털의 Azure SQL 페이지에서 만들기를 선택한 다음, 드롭다운에서 Windows Server 2016의 SQL Server 2016 SP2 Enterprise 이미지를 선택함":::

두 SQL Server VM(예: **SQL-VM-1** 및 **SQL-VM-2**)을 모두 만들기 위해 다음 표를 사용하여 **가상 머신 만들기** 페이지에서 값을 입력합니다(IP 주소는 표의 예와 다를 수 있음). 

| Configuration | SQL-VM-1 | SQL-VM-2 |
| --- | --- | --- |
| 갤러리 이미지 |**Windows Server 2016의 SQL Server 2016 SP2 Enterprise** |**Windows Server 2016의 SQL Server 2016 SP2 Enterprise** |
| **VM 기본 사항** |**이름** = SQL-VM-1<br/>**사용자 이름** = DomainAdmin<br/>**암호** = Contoso!0000<br/>**구독** = 사용자 구독<br/>**리소스 그룹** = SQL-HA-RG<br/>**위치** = 해당 Azure 위치 |**이름** = SQL-VM-2<br/>**사용자 이름** = DomainAdmin<br/>**암호** = Contoso!0000<br/>**구독** = 사용자 구독<br/>**리소스 그룹** = SQL-HA-RG<br/>**위치** = 해당 Azure 위치 |
| **VM 크기** |**크기** = E2ds_v4(2개 vCPU, 16GB RAM)</br> |**크기** = E2ds_v4(2개 vCPU, 16GB RAM) |
| **VM 설정** |**가용성 옵션** = 가용성 영역<br/>**가용성 영역** = 1<br/>**퍼블릭 인바운드 포트** = 선택한 포트 허용<br/>**인바운드 포트 선택** = RDP(3389)<br/>**OS 디스크 유형** = 프리미엄 SSD(로컬 중복 스토리지)<br/>**가상 네트워크** = SQLHAVNET<br/>**서브넷** = SQL-subnet-1(10.38.1.0/24)<br/>**공용 IP 주소** = 자동으로 생성됨<br/>**NIC 네트워크 보안 그룹** = 기본<br/>**퍼블릭 인바운드 포트** = 선택한 포트 허용 <br/> **인바운드 포트 선택** = RDP(3389)<br/>**부팅 진단** = 관리되는 스토리지 계정을 통해 사용(권장)<br/>|**가용성 옵션** = 가용성 영역<br/>**가용성 영역** = 2<br/>**퍼블릭 인바운드 포트** = 선택한 포트 허용<br/>**인바운드 포트 선택** = RDP(3389)<br/>**OS 디스크 유형** = 프리미엄 SSD(로컬 중복 스토리지)<br/>**가상 네트워크** = SQLHAVNET<br/>**서브넷** = SQL-subnet-2(10.38.2.0/24)<br/>**공용 IP 주소** = 자동으로 생성됨<br/>**NIC 네트워크 보안 그룹** = 기본<br/>**퍼블릭 인바운드 포트** = 선택한 포트 허용 <br/> **인바운드 포트 선택** = RDP(3389)<br/>**부팅 진단** = 관리되는 스토리지 계정을 통해 사용(권장)<br/> |
| **SQL 서버 설정** |**SQL 연결** = 프라이빗(Virtual Network 내)<br/>**포트** = 1433<br/>**SQL 인증** = 사용 안 함<br/>**Azure Key Vault 통합** = 사용 안 함 <br/>**Storage 최적화** = 트랜잭션 처리<br/>**SQL 데이터** = 1,024GiB, 5,000IOPS, 200MB/초<br/>**SQL 로그** = 1,024GiB, 5,000IOPS, 200MB/초<br/>**SQL TempDb** = 로컬 SSD 드라이브 사용<br/>**자동화된 패치** = 일요일 2시<br/>**자동화된 백업** = 사용 안 함 |**SQL 연결** = 프라이빗(Virtual Network 내)<br/>**포트** = 1433<br/>**SQL 인증** = 사용 안 함<br/>**Azure Key Vault 통합** = 사용 안 함 <br/>**Storage 최적화** = 트랜잭션 처리<br/>**SQL 데이터** = 1,024GiB, 5,000IOPS, 200MB/초<br/>**SQL 로그** = 1,024GiB, 5,000IOPS, 200MB/초<br/>**SQL TempDb** = 로컬 SSD 드라이브 사용<br/>**자동화된 패치** = 일요일 2시<br/>**자동화된 백업** = 사용 안 함 |

<br/>

> [!NOTE]
> 제안된 이러한 컴퓨터 크기는 Azure Virtual Machines에서 가용성 그룹을 테스트하기 위한 것입니다. 최적화된 프로덕션 워크로드는 [Azure VM의 SQL Server에 대한 성능 모범 사례](./performance-guidelines-best-practices-checklist.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)의 크기 권장 사항을 참조하세요.
>

## <a name="configure-sql-server-vms"></a>SQL Server VM 구성

VM 만들기가 완료되면 보조 IP 주소를 각 VM에 추가하고 도메인에 조인하여 SQL Server VM을 구성합니다. 


### <a name="add-secondary-ips-to-sql-server-vms"></a>SQL Server VM에 보조 IP 추가

다중 서브넷 환경에서는 보조 IP 주소를 가용성 그룹 수신기에 사용할 각 SQL Server VM에 할당하고, Windows Server 2016 이하의 경우 보조 IP 주소를 클러스터 IP 주소에 대한 각 SQL Server VM에도 할당합니다. 이렇게 하면 단일 서브넷 환경의 요구 사항과 마찬가지로 Azure Load Balancer가 필요하지 않습니다.  

Windows Server 2016 및 이전 버전에서는 클러스터가 Windows Server 2019에 도입된 기본 DNN(분산 네트워크 이름) 대신 **클러스터 네트워크 이름** 을 사용하므로 추가 보조 IP 주소를 Windows 클러스터 IP에 사용할 각 SQL Server VM에 할당해야 합니다. DNN을 사용하면 CNO(클러스터 이름 개체)가 클러스터의 모든 노드에 대한 IP 주소에 자동으로 등록되므로 전용 Windows 클러스터 IP 주소가 필요하지 않습니다.

Windows Server 2016 및 이전 버전을 사용하는 경우 이 섹션의 단계에 따라 보조 IP 주소를 가용성 그룹 수신기 *및* 클러스터 *모두* 에 대한 각 SQL Server VM에 할당합니다. 

Windows Server 2019 이상을 사용하는 경우 VNN(가상 네트워크 이름)을 사용하여 클러스터를 구성하려는 경우를 제외하고는 가용성 그룹 수신기에 대한 보조 IP 주소만 할당하고 Windows 클러스터 IP를 할당하는 단계를 건너뜁니다. 이 경우 Windows Server 2016에서와 같이 두 IP 주소를 모두 각 SQL Server VM에 할당합니다. 

추가 보조 IP를 VM에 할당하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com/)에서 리소스 그룹으로 이동하여 첫 번째 SQL Server VM(예: **SQL-VM-1**)을 선택합니다. 
1. **설정** 창에서 **네트워킹** 을 선택한 다음, **네트워크 인터페이스** 를 선택합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/19-sql-vm-network-interface.png" alt-text="설정 창에서 네트워킹을 선택한 다음, 네트워크 인터페이스를 선택함":::

1. **네트워크 인터페이스** 페이지의 **설정** 창에서 **IP 구성** 을 선택한 다음, **+ 추가** 를 선택하여 추가 IP 주소를 추가합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/20-ip-configurations-add.png" alt-text="IP 구성":::

1. **IP 구성 추가** 페이지에서 다음을 수행합니다. 
   1. **이름** 을 Windows 클러스터 IP로 지정합니다(예: Windows 2016 이하의 경우 **windows-cluster-ip**). Windows Server 2019 이상을 사용하는 경우 이 단계를 건너뜁니다.  
   1. **할당** 을 **정적** 으로 설정합니다.
   1. SQL Server VM(**SQL-VM-1**)과 동일한 서브넷(**SQL-subnet-1**)에서 사용되지 않는 **IP 주소** 를 입력합니다(예: `10.38.1.10`). 
   1. **공용 IP 주소** 를 기본값인 **연결 끊기** 로 그대로 둡니다. 
   1. **확인** 을 선택하여 IP 구성 추가를 완료합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/21-add-ip-windows-cluster.png" alt-text="첫 번째 SQL Server VM의 서브넷에서 사용되는 IP 주소를 입력하여 클러스터 IP를 추가함":::

1. **+ 추가** 를 다시 선택하여 가용성 그룹 수신기(**availability-group-listener** 와 같은 이름 사용)에 대한 추가 IP 주소를 구성하고, **SQL-subnet-1** 에서 사용되지 않는 IP 주소(예: `10.38.1.11`)를 다시 지정합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/22-add-ip-ag-listener.png" alt-text="+ 추가를 다시 선택하여 SQL-subnet-1에서 사용되지 않는 IP 주소(예: 10.31.1.11)를 다시 사용하여 가용성 그룹 수신기(availability-group-listener와 같은 이름 사용)에 대한 추가 IP 주소를 구성함":::

1. 이러한 단계를 두 번째 SQL Server VM(예: **SQL-VM-2**)에 대해 다시 반복합니다. **SQL-subnet-2** 내에서 사용되지 않는 두 개의 보조 IP 주소를 할당합니다. 다음 표의 값을 사용하여 IP 구성을 추가합니다. 

   
    | **필드** | 입력 | 입력 | 
    | --- | --- | --- |
    | **이름** |windows-cluster-ip | availability-group-listener |
    | **Allocation** | 정적 | 정적 |
    | **IP 주소** | 10.38.2.10 | 10.38.2.11 | 
    |  |  |  |

이제 **corp.contoso.com** 에 조인할 준비가 되었습니다. 

### <a name="join-the-servers-to-the-domain"></a>서버를 도메인에 가입

두 개의 보조 IP 주소가 두 SQL Server VM에 할당되면 각 SQL Server VM을 **corp.contoso.com** 도메인에 조인합니다. 

corp.contoso.com 도메인에 조인하려면 보조 도메인 컨트롤러를 사용하여 [도메인에 조인](#join-the-domain)할 때 수행한 것과 동일한 단계를 SQL Server VM에 대해 수행합니다. 

각 SQL Server VM이 다시 시작될 때까지 기다린 후에 계정을 추가할 수 있습니다. 


## <a name="add-accounts"></a>계정 추가

각 VM에 관리자로 설치 계정을 추가하고, SQL Server 내의 설치 계정 및 로컬 계정에 권한을 부여하고, SQL Server 서비스 계정을 업데이트합니다. 

### <a name="add-install-account"></a>설치 계정 추가

두 SQL Server VM이 모두 도메인에 조인되면 **CORP\Install** 을 로컬 관리자 그룹의 멤버로 추가합니다.

>[!TIP]
> *도메인* 관리자 계정을 사용하여 로그인해야 합니다. 이전 단계에서 **BUILTIN** 관리자 계정을 사용했습니다. 이제 서버가 도메인에 속하므로 도메인 계정을 사용합니다. RDP 세션에서 *도메인*\\*사용자 이름*(예: **CORP\DomainAdmin**)을 지정합니다.

계정을 관리자로 추가하려면 다음 단계를 수행합니다. 

1. VM이 다시 시작될 때까지 기다린 후에 첫 번째 SQL Server VM에서 RDP 파일을 다시 시작하여 **CORP\DomainAdmin** 계정으로 **SQL-VM-1** 에 로그인합니다.
1. **서버 관리자** 에서 **도구** 를 선택한 다음, **컴퓨터 관리** 를 선택합니다.
1. **컴퓨터 관리** 창에서 **로컬 사용자 및 그룹** 을 확장한 후 **그룹** 을 선택합니다.
1. **Administrators** 그룹을 두 번 클릭합니다.
1. **Administrators 속성** 대화 상자에서 **추가** 단추를 선택합니다.
1. **CORP\Install** 사용자를 입력한 후 **확인** 을 선택합니다.
1. **확인** 을 선택하여 **Administrator 속성** 대화 상자를 닫습니다.
1. **SQL-VM-2** 에서 이러한 단계를 반복합니다. 

### <a name="add-account-to-sysadmin"></a>sysadmin에 계정 추가 

가용성 그룹을 구성하는 데 사용되는 설치 계정(CORP\install)은 각 SQL Server VM에서 **sysadmin** 고정 서버 역할의 일부여야 합니다. 

**sysadmin** 권한을 설치 계정에 부여하려면 다음 단계를 수행합니다. 

1. *\<MachineName\>\DomainAdmin* 계정(예: `SQL-VM-1\DomainAdmin`)을 사용하여 RDP(원격 데스크톱 프로토콜)를 통해 서버에 연결합니다.
1. SQL Server Management Studio를 열고 SQL Server의 로컬 인스턴스에 연결합니다.
1. **개체 탐색기** 에서 **보안** 을 선택합니다.
1. 마우스 오른쪽 단추로 **로그인** 을 클릭합니다. **새 로그인** 을 선택합니다.
1. **로그인 - 새로 만들기** 에서 **검색** 을 선택합니다.
1. **위치** 를 선택합니다.
1. 도메인 관리자 네트워크 자격 증명을 입력합니다.
1. 설치 계정(CORP\install)을 사용합니다.
1. **sysadmin** 고정 서버 역할의 구성원이 될 로그인을 설정합니다.
1. **확인** 을 선택합니다.
1. 두 번째 SQL Server VM(예: **SQL-VM-2**)에서 이러한 단계를 반복하고. 관련 컴퓨터 이름 계정(예: `SQL-VM-2\DomainAdmin`)을 사용하여 연결합니다. 


### <a name="add-system-account"></a>시스템 계정 추가 

이후 버전의 SQL Server에서 [NT AUTHORITY\SYSTEM] 계정에는 기본적으로 SQL Server에 대한 권한이 없으므로 권한을 수동으로 부여해야 합니다. 

[NT AUTHORITY\SYSTEM]을 추가하고 적절한 권한을 부여하려면 다음 단계를 수행합니다. 

1. *\<MachineName\>\DomainAdmin* 계정(예: `SQL-VM-1\DomainAdmin`)을 사용하여 RDP(원격 데스크톱 프로토콜)를 통해 첫 번째 SQL Server VM에 연결합니다.
1. SQL Server Management Studio를 열고 SQL Server의 로컬 인스턴스에 연결합니다.
1. 다음 T-SQL(Transact-SQL) 명령을 사용하여 각 SQL Server 인스턴스에서 `[NT AUTHORITY\SYSTEM]`에 대한 계정을 만듭니다. 

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. 각 SQL Server 인스턴스에서 `[NT AUTHORITY\SYSTEM]`에 다음 권한을 부여합니다.

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   이러한 권한을 부여하려면 다음 T-SQL(Transact-SQL) 명령을 사용합니다. 

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

1. 두 번째 SQL Server VM(예: **SQL-VM-2**)에서 이러한 단계를 반복하고. 관련 컴퓨터 이름 계정(예: `SQL-VM-2\DomainAdmin`)을 사용하여 연결합니다. 

### <a name="set-the-sql-server-service-accounts"></a>SQL Server 서비스 계정 설정

각 VM의 SQL Server 서비스는 전용 도메인 계정을 사용해야 합니다.  이전에 만든 도메인 계정(**SQL-VM-1** 의 경우 **Corp\SQLSvc1** 및 **SQL-VM-2** 의 경우 **Corp\SQLSvc2**)을 사용합니다. 

서비스 계정을 설정하려면 다음 단계를 수행합니다. 

1. *\<MachineName\>\DomainAdmin* 계정(예: `SQL-VM-1\DomainAdmin`)을 사용하여 RDP(원격 데스크톱 프로토콜)를 통해 첫 번째 SQL Server VM에 연결합니다.
1. **SQL Server 구성 관리자** 를 엽니다.
1. SQL Server 서비스를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
1. 계정(**Corp\SQLSvc1**) 및 암호를 제공합니다.
1. **적용** 을 선택하여 변경 내용을 커밋하고, SQL Server 서비스를 다시 시작합니다. 
1. 다른 SQL Server VM(SQL-VM-1)에서 이러한 단계를 반복하고, 컴퓨터 도메인 계정(예: `SQL-VM-2\DomainAdmin`)을 사용하여 로그인하고, 두 번째 서비스 계정(**Corp\SQLSvc2**)을 제공합니다. 


## <a name="create-azure-storage-account"></a>Azure Storage 계정 만들기

2노드 Windows Server 장애 조치(failover) 클러스터를 배포하려면 쿼럼을 설정하기 위해 세 번째 멤버가 필요합니다. Azure VM에서 클라우드 감시는 권장되는 쿼럼 옵션입니다. 클라우드 감시를 구성하려면 Azure Storage 계정이 필요합니다. 자세한 내용은 [장애 조치(failover) 클러스터용 클라우드 감시 배포](/windows-server/failover-clustering/deploy-cloud-witness)를 참조하세요.

포털에서 Azure Storage 계정을 만들려면 다음을 수행합니다.

1. 포털에서 **SQL-HA-RG** 리소스 그룹을 열고, **+ 만들기** 를 선택합니다.
1. **스토리지 계정** 을 검색합니다.
1. **스토리지 계정** 을 선택하고, **만들기** 를 선택하여 다음 값으로 구성합니다. 

    1. 구독, **SQL-HA-RG** 리소스 그룹을 차례로 선택합니다.
    1. 스토리지 계정에 대한 **스토리지 계정 이름** 을 입력합니다.
       Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 스토리지 계정 이름은 Azure 내에서 고유해야 합니다.    
    1. **지역** 을 선택합니다.
    1. **성능** 에 대해 **표준: 대부분의 시나리오에 권장(범용 v2 계정)** 을 선택합니다. Azure Premium Storage는 클라우드 감시에 지원되지 않습니다.
    1. **중복성** 에 대해 **LRS(로컬 중복 스토리지)** 를 선택합니다.
       장애 조치(failover) 클러스터링에서는 Blob 파일을 중재 지점으로 사용하므로 데이터를 읽을 때 일관성을 보장해야 합니다. 따라서 복제 유형에 대해 로컬 중복 스토리지를 선택해야 합니다. 
    1. **검토 + 만들기** 를 선택합니다.

## <a name="configure-the-firewall"></a>방화벽 구성

가용성 그룹 기능은 다음 TCP 포트를 통한 트래픽을 기반으로 합니다. 

- **SQL Server VM**: SQL Server의 기본 인스턴스의 경우 포트 1433
- **데이터베이스 미러링 엔드포인트:** 사용 가능한 포트 예제는 5022를 자주 사용합니다.

두 SQL Server VM 모두에서 이러한 방화벽 포트를 엽니다. 포트를 여는 방법은 사용하는 방화벽 솔루션에 따라 다르며, 이 섹션에서 제공하는 Windows 방화벽 예와 다를 수 있습니다. 

Windows 방화벽에서 이러한 포트를 열려면 다음 단계를 수행합니다. 

1. 첫 번째 SQL Server **시작** 화면에서 **고급 보안이 포함된 Windows 방화벽** 을 시작합니다.
1. 왼쪽 창에서 **인바운드 규칙** 을 선택합니다. 오른쪽 창에서 **새 규칙** 을 선택합니다.
1. **규칙 유형** 에 **포트** 를 선택합니다.
1. 포트에 대해 **TCP** 를 지정하고 적절한 포트 번호를 입력합니다. 다음 예제를 참조하십시오.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/17-firewall-tcp-ports.png" alt-text="SQL 방화벽":::

1. **새로 만들기** 를 선택합니다.
1. **작업** 페이지에서 **연결 허용** 을 선택하고, **다음** 을 선택합니다.
1. **프로필** 페이지에서 기본 설정을 그대로 적용하고 **다음** 을 선택합니다.
1. **이름** 페이지의 **이름** 텍스트 상자에서 규칙 이름(예: **SQL 인바운드**)을 지정한 다음, **마침** 을 선택합니다.
1. 두 번째 SQL Server VM에서 이 단계를 반복합니다.

## <a name="next-steps"></a>다음 단계

이제 필수 구성 요소가 구성되었으므로 여러 서브넷에서 [가용성 그룹 구성](availability-group-manually-configure-tutorial-multi-subnet.md)을 시작합니다. 

자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)