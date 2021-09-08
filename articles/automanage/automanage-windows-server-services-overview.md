---
title: Windows Server용 Automanage 서비스(미리 보기)
description: Windows Server Services용 Automanage 및 Windows Server Azure Edition의 기능 개요
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: 09b011d76a570aaed1a9ea8c0b9bdc74e80eca53
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772189"
---
# <a name="automanage-for-windows-server-services-preview"></a>Windows Server용 Automanage 서비스(미리 보기)

Windows Server Services용 Automanage는 특히 _Windows Server Azure Edition_ 에 새로운 기능을 제공합니다.  이러한 기능은 다음과 같습니다.
- 핫패치
- SMB over QUIC
- 확장 네트워크

> [!IMPORTANT]
> Windows Server Services용 Automanage는 현재 공개 미리 보기로 제공됩니다. 아래에 설명된 핫패치 기능을 사용하려면 옵트인 절차가 필요합니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!NOTE]
> 핫패치는 _Windows Server 2022 Datacenter: Azure Edition(Core) 미리 보기_ 에서 평가할 수 있습니다.  _Windows Server 2019 Datacenter: Azure Edition 미리 보기_ 의 핫패치는 더 이상 평가할 수 없습니다.

Windows Server용 Automanage 기능은 다음 _Windows Server Azure Edition_ 이미지 중 하나 이상에서 찾을 수 있습니다. 

- Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경)
- Windows Server 2022 Datacenter: Azure Edition(Core)

기능은 이미지에 따라 다릅니다. 자세한 내용은 [시작하기](#getting-started-with-windows-server-azure-edition)를 참조하세요.

## <a name="automanage-for-windows-server-capabilities"></a>Windows Server용 Automanage 기능

### <a name="hotpatch"></a>핫패치

핫패치는 다음 이미지의 공개 미리 보기에서 사용할 수 있습니다.

- Windows Server 2022 Datacenter: Azure Edition(Core)

핫패치를 사용하면 다시 부팅하지 않고 VM에 보안 업데이트를 적용할 수 있습니다.  또한 Windows Server용 Automanage는 핫패치의 온보딩, 구성 및 오케스트레이션을 자동화합니다.  자세히 알아보려면 [핫패치](automanage-hotpatch.md)를 참조하세요.  

### <a name="smb-over-quic"></a>SMB over QUIC

SMB over QUIC는 다음 이미지의 공개 미리 보기에서 사용할 수 있습니다.

- Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경)
- Windows Server 2022 Datacenter: Azure Edition(Core)

SMB over QUIC를 사용하면 VPN 없이 원격으로 작업할 때 QUIC 프로토콜을 통해 SMB 트래픽을 터널링하여 파일에 액세스할 수 있습니다.  자세히 알아보려면 [SMB over QUIC](/windows-server/storage/file-server/smb-over-quic)를 참조하세요.  

### <a name="azure-extended-network"></a>Azure 확장 네트워크

Azure 확장 네트워크는 다음 이미지에서 공개 미리 보기로 제공됩니다.

- Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경)
- Windows Server 2022 Datacenter: Azure Edition(Core)

Azure 확장 네트워크를 사용하면 온-프레미스 서브넷을 Azure로 확장하여 온-프레미스 가상 머신이 Azure로 마이그레이션할 때 원래 온-프레미스 개인 IP 주소를 유지할 수 있습니다. 자세히 알아보려면 [Azure 확장 네트워크](/windows-server/manage/windows-admin-center/azure/azure-extended-network)를 참조하세요.  


## <a name="getting-started-with-windows-server-azure-edition"></a>Windows Server Azure Edition으로 시작하기

먼저 사용하려는 Windows Server용 Automanage 기능을 고려한 다음, 이러한 모든 기능을 지원하는 해당 VM 이미지를 선택하는 것이 중요합니다.  일부 _Windows Server Azure Edition_ 이미지는 기능의 하위 집합만 지원합니다. 자세한 내용은 아래 표를 참조하세요.

### <a name="deciding-which-image-to-use"></a>사용할 이미지 결정 

|이미지|기능|
|--|--|
|Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경) | SMB over QUIC, 확장 네트워크 | 
| Windows Server 2022 Datacenter: Azure Edition(Core) | 핫패치, SMB over QUIC, 확장 네트워크 | 

### <a name="creating-a-vm"></a>VM 만들기

새 VM에서 Windows Server용 Automanage 기능 사용을 시작하려면 원하는 방법을 사용하여 Azure VM을 만들고 사용하려는 [기능](#getting-started-with-windows-server-azure-edition) 세트에 해당하는 _Windows Server Azure Edition_ 이미지를 선택합니다.  이러한 기능의 구성은 VM을 만드는 동안 필요할 수 있습니다. 개별 기능 토픽(예: [핫패치](automanage-hotpatch.md))에서 VM 구성에 대해 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Automation에 대한 자세한 정보](automanage-virtual-machines.md)