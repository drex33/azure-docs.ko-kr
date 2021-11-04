---
title: Windows Server용 Automanage
description: Windows Server Azure Edition을 사용하는 Windows Server 기능에 대한 Azure Automanage 개요
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: efd5b643608b8d3de5cacbc1f810a9b1e9efb612
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501593"
---
# <a name="azure-automanage-for-windows-server"></a>Windows Server용 Azure Automanage

Windows Server용 Azure Automanage 특히 _Windows Server Azure Edition에_ 새로운 기능을 제공합니다.  이러한 기능은 다음과 같습니다.
- 핫 패치(미리 보기)
- SMB over QUIC
- Azure용 확장 네트워크

> [!IMPORTANT]
> 핫 패치는 현재 공개 미리 보기로 제공됩니다. 아래에 설명된 핫패치 기능을 사용하려면 옵트인 절차가 필요합니다.
> 이 미리 보기는 서비스 수준 약정 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows Server용 Automanage 기능은 다음 _Windows Server Azure Edition_ 이미지 중 하나 이상에서 찾을 수 있습니다. 

- Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경)
- Windows Server 2022 Datacenter: Azure Edition(Core)

기능은 이미지에 따라 다릅니다. 자세한 내용은 [시작하기](#getting-started-with-windows-server-azure-edition)를 참조하세요.

## <a name="automanage-for-windows-server-capabilities"></a>Windows Server용 Automanage 기능

### <a name="hotpatch-preview"></a>핫 패치(미리 보기)

핫패치는 다음 이미지의 공개 미리 보기에서 사용할 수 있습니다.

- Windows Server 2022 Datacenter: Azure Edition(Core)

핫패치를 사용하면 다시 부팅하지 않고 VM에 보안 업데이트를 적용할 수 있습니다.  또한 Windows Server용 Automanage는 핫패치의 온보딩, 구성 및 오케스트레이션을 자동화합니다.  자세히 알아보려면 [핫패치](automanage-hotpatch.md)를 참조하세요.  

### <a name="smb-over-quic"></a>SMB over QUIC

SMB over QUIC는 다음 이미지의 공개 미리 보기에서 사용할 수 있습니다.

- Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경)
- Windows Server 2022 Datacenter: Azure Edition(Core)

SMB overMUC는 통신, 모바일 디바이스 사용자 및 지점용 "SMB VPN"을 제공하여 인터넷과 같은 신뢰할 수 없는 네트워크를 통해 에지 파일 서버에 안전하고 신뢰할 수 있는 연결을 제공합니다. [SEALC는](https://datatracker.ietf.org/doc/rfc9000/) HTTP/3에서 사용되는 IETF 표준화된 프로토콜로, TLS 1.3을 사용하여 최대 데이터 보호를 위해 설계되었으며 사용하지 않도록 설정될 수 없는 암호화가 필요합니다. SMB는 일반적으로 사용자 환경이 변경되지 않는다는 의미로, 대개는 대개는 대개 을(를) 터널 내에서 작동합니다. 다중 채널, 서명, 압축, 지속적인 가용성 및 디렉터리 임대와 같은 SMB 기능은 정상적으로 작동합니다. 

또한, SMB over(SMB over MACHINE)는 [WINDOWS Server에 대한 Automanage Machine 모범 사례와](automanage-windows-server.md) 통합되어 있으며, 이를 통해 SMB가 더 쉽게 관리될 수 있습니다. ENCRYPTIONC는 인증서를 사용하여 암호화를 제공하며 조직에서는 복잡한 공개 키 인프라를 유지 관리하는 데 어려움을 겪는 경우가 많습니다. 자동 관리 머신 모범 사례는 인증서가 경고 없이 만료되지 않고, 대역폭을 통해 SMB가 최대 서비스 연속성을 위해 계속 사용하도록 설정되어 있는지 확인합니다.

자세한 내용은 [AUTOmanage 머신 모범 사례를](automanage-smb-over-quic.md)사용하여 [대개는 AUTOC를 통해](https://aka.ms/smboverquic) SMB를 참조하세요.
 

### <a name="extended-network-for-azure"></a>Azure용 확장 네트워크

Azure용 확장 네트워크는 다음 이미지에서 사용할 수 있습니다.

- Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경)
- Windows Server 2022 Datacenter: Azure Edition(Core)

Azure 확장 네트워크를 사용하면 온-프레미스 서브넷을 Azure로 확장하여 온-프레미스 가상 머신이 Azure로 마이그레이션할 때 원래 온-프레미스 개인 IP 주소를 유지할 수 있습니다. 자세히 알아보려면 [Azure 확장 네트워크](/windows-server/manage/windows-admin-center/azure/azure-extended-network)를 참조하세요.  


## <a name="getting-started-with-windows-server-azure-edition"></a>Windows Server Azure Edition으로 시작하기

먼저 사용하려는 Windows Server용 Automanage 기능을 고려한 다음, 이러한 모든 기능을 지원하는 해당 VM 이미지를 선택하는 것이 중요합니다.  일부 _Windows Server Azure Edition_ 이미지는 기능의 하위 집합만 지원합니다. 자세한 내용은 아래 표를 참조하세요.

### <a name="deciding-which-image-to-use"></a>사용할 이미지 결정 

|이미지|기능|
|--|--|
|Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경) | AZURE용 SMB, 확장된 네트워크 | 
| Windows Server 2022 Datacenter: Azure Edition(Core) | 핫패치, SMB over CPUC, Azure용 확장 네트워크 | 

### <a name="creating-a-vm"></a>VM 만들기

새 VM에서 Windows Server용 Automanage 기능 사용을 시작하려면 원하는 방법을 사용하여 Azure VM을 만들고 사용하려는 [기능](#getting-started-with-windows-server-azure-edition) 세트에 해당하는 _Windows Server Azure Edition_ 이미지를 선택합니다.  

> [!IMPORTANT]
> 일부 기능에는 VM을 만드는 동안 수행할 특정 구성 단계가 있으며 미리 보기에 있는 일부 기능에는 특정 옵트인 및 포털 보기 요구 사항이 있습니다.  VM에서 해당 기능을 사용하는 방법에 대한 자세한 내용은 위의 개별 기능 항목을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Automation에 대한 자세한 정보](automanage-virtual-machines.md)