---
title: '자습서: Azure Stack Edge에 네트워크 기능 배포'
titleSuffix: Azure Network Function Manager
description: 이 자습서에서는 네트워크 기능을 관리형 애플리케이션으로 배포하는 방법에 대해 알아봅니다.
author: prmitt
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: prmitt
ms.openlocfilehash: 0e45f8db150505b323f45cd79eed40215b3a44f8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223332"
---
# <a name="tutorial-deploy-network-functions-on-azure-stack-edge-preview"></a>자습서: Azure Stack Edge에 네트워크 기능 배포(미리 보기)

이 자습서에서는 Azure Marketplace를 사용하여 Azure Stack Edge에 네트워크 기능을 배포하는 방법에 대해 알아봅니다. 네트워크 기능 관리자를 사용하면 Azure Stack Edge에서 간소화된 배포를 위한 Azure Managed Applications 환경을 사용할 수 있습니다.

> [!div class="checklist"]
> * [필수 구성 요소](overview.md#prereq) 확인
> * 네트워크 기능 만들기
> * 네트워크 기능 정보 확인

## <a name="prerequisites"></a>필수 조건

* 네트워크 기능 관리자용 디바이스 리소스를 만들었습니다. 이러한 단계를 완료하지 않은 경우 [디바이스 리소스를 만드는 방법](create-device.md)을 참조하세요.
* 디바이스의 **개요** 탭에서 다음 값이 있는지 확인합니다.
  * 프로비전 상태 = 성공함
  * 디바이스 상태 = 등록됨

## <a name="create-a-network-function"></a><a name="create"></a>네트워크 기능 만들기

1. [Azure 미리 보기 포털](https://aka.ms/AzureNetworkFunctionManager)에 로그인합니다.
1. 네트워크 기능을 배포하려는 **디바이스** 리소스로 이동하고 **+ 네트워크 기능 만들기** 를 선택합니다.

   :::image type="content" source="./media/deploy-functions/create-network-function.png" alt-text="+ 네트워크 기능 만들기의 스크린샷" lightbox="./media/deploy-functions/create-network-function.png":::
1. 드롭다운에서 사용하려는 **공급업체 SKU** 를 선택하고 **만들기** 를 클릭합니다.

   :::image type="content" source="./media/deploy-functions/select.png" alt-text="공급업체 SKU의 스크린샷" lightbox="./media/deploy-functions/select.png":::
1. 선택한 SKU에 따라 네트워크 기능 관리형 애플리케이션의 Marketplace 포털로 리디렉션됩니다.
 
   모든 네트워크 기능 파트너는 Azure Stack Edge에 네트워크 기능을 배포하기 위한 다양한 요구 사항을 갖습니다. 또한 모바일 패킷 코어 및 SD WAN 에지와 같은 일부 네트워크 기능을 수행하려면 네트워크 기능을 배포하기 전에 관리, LAN 및 WAN 포트를 구성하고 이러한 포트에 IP 주소를 할당해야 할 수 있습니다. 필요한 속성 및 Azure Stack Edge 디바이스 네트워크 구성을 파트너에게 확인합니다.
   
   > [!IMPORTANT]
   > 관리, LAN 또는 WAN 가상 네트워크 인터페이스의 고정 IP 주소를 지원하는 모든 네트워크 기능에 대해 특정 포트에 할당된 IP 주소 범위에서 처음 4개의 IP 주소를 사용하지 않도록 해야 합니다. 이러한 IP 주소는 Azure Stack Edge 서비스에 예약된 IP 주소입니다.
   >

1. Marketplace 포털의 단계에 따라 파트너 관리형 애플리케이션을 배포합니다. 관리형 애플리케이션을 성공적으로 프로비전한 후에는 리소스 그룹으로 이동하여 관리형 앱을 볼 수 있습니다. 네트워크 기능 리소스의 공급업체 프로비전 상태가 프로비전됨인지 확인하려면 관리되는 리소스 그룹으로 이동합니다. 이렇게 하면 네트워크 기능 배포에 성공했음을 확인하고 필요한 추가 구성을 네트워크 기능 파트너 관리 포털을 통해 프로비전할 수 있습니다. 네트워크 기능 파트너에게 네트워크 기능 관리자를 사용한 초기 배포 후의 관리 환경을 확인합니다.

### <a name="example"></a>예제

1. Marketplace에서 **Fusion Core - 5G packet core** 제품을 찾고 **만들기** 를 클릭하여 네트워크 기능 만들기를 시작합니다.

   :::image type="content" source="./media/deploy-functions/metaswitch.png" alt-text="Metaswitch 페이지의 스크린샷" lightbox="./media/deploy-functions/metaswitch.png":::
1. 기본 설정을 구성합니다.

   :::image type="content" source="./media/deploy-functions/basics-blade.png" alt-text="기본 설정의 스크린샷" lightbox="./media/deploy-functions/basics-blade.png":::
1. 관리 ID를 적용합니다. 자세한 내용은 [관리 ID](overview.md#managed-identity)를 참조하세요.

   :::image type="content" source="./media/deploy-functions/managed-identity.png" alt-text="관리 ID의 스크린샷" lightbox="./media/deploy-functions/managed-identity.png":::
1. Fusion Core VM의 관리, LAN 및 WAN 인터페이스의 IP 주소 정보를 입력합니다.

   :::image type="content" source="./media/deploy-functions/ip-settings.png" alt-text="Fusion Core VM의 관리, LAN 및 WAN 인터페이스의 스크린샷" lightbox="./media/deploy-functions/ip-settings.png":::
1. 5G 및 테스트 UE 구성에 대한 선택적 설정을 입력합니다.

   :::image type="content" source="./media/deploy-functions/5g-settings-blade.png" alt-text="5G의 스크린샷" lightbox="./media/deploy-functions/5g-settings-blade.png":::

   :::image type="content" source="./media/deploy-functions/test-blade.png" alt-text="테스트 UE 구성의 스크린샷" lightbox="./media/deploy-functions/test-blade.png":::
1. 유효성 검사가 통과하면 사용 약관에 동의하는 것입니다. 그런 다음 **만들기** 를 클릭하여 고객 리소스 그룹의 Fusion Core 관리형 애플리케이션 및 관리되는 리소스 그룹의 네트워크 기능 리소스 만들기를 시작합니다. 네트워크 기능 리소스를 보려면 관리되는 리소스 그룹 보기에서 **숨겨진 형식 표시** 상자를 선택해야 합니다.

   :::image type="content" source="./media/deploy-functions/managed-app-customer.png" alt-text="고객 리소스 그룹에 있는 관리형 앱의 스크린샷" lightbox="./media/deploy-functions/managed-app-customer.png":::

   :::image type="content" source="./media/deploy-functions/managed-resource-group.png" alt-text="관리되는 리소스 그룹에 있는 네트워크 기능의 스크린샷" lightbox="./media/deploy-functions/managed-resource-group.png":::

## <a name="next-steps"></a>다음 단계

공급업체 포털로 이동하여 네트워크 기능 구성을 완료합니다.
