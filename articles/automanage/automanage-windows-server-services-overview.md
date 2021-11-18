---
title: Windows Server용 Automanage
description: Windows server azure Edition을 사용 하는 Windows 서버 기능에 대 한 azure automanage 개요
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: febbd30bc342569f0a1e6dfb7b0de938cc461e65
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717382"
---
# <a name="azure-automanage-for-windows-server"></a>Windows Server용 Azure Automanage

Windows server에 대 한 Azure automanage는 _Windows Server Azure 버전_ 에 대 한 새로운 기능을 제공 합니다.  이러한 기능은 다음과 같습니다.
- Hotpatch (미리 보기)
- SMB over QUIC
- Azure 용 확장 네트워크

> [!IMPORTANT]
> Hotpatch은 현재 공개 미리 보기로 제공 됩니다. 아래에 설명된 핫패치 기능을 사용하려면 옵트인 절차가 필요합니다.
> 이 미리 보기는 서비스 수준 약정 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows Server용 Automanage 기능은 다음 _Windows Server Azure Edition_ 이미지 중 하나 이상에서 찾을 수 있습니다. 

- Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경)
- Windows Server 2022 Datacenter: Azure Edition(Core)

기능은 이미지에 따라 다릅니다. 자세한 내용은 [시작하기](#getting-started-with-windows-server-azure-edition)를 참조하세요.

## <a name="automanage-for-windows-server-capabilities"></a>Windows Server용 Automanage 기능

### <a name="hotpatch-preview"></a>Hotpatch (미리 보기)

핫패치는 다음 이미지의 공개 미리 보기에서 사용할 수 있습니다.

- Windows Server 2022 Datacenter: Azure Edition(Core)

핫패치를 사용하면 다시 부팅하지 않고 VM에 보안 업데이트를 적용할 수 있습니다.  또한 Windows Server용 Automanage는 핫패치의 온보딩, 구성 및 오케스트레이션을 자동화합니다.  자세히 알아보려면 [핫패치](automanage-hotpatch.md)를 참조하세요.  

### <a name="smb-over-quic"></a>SMB over QUIC

SMB over QUIC는 다음 이미지의 공개 미리 보기에서 사용할 수 있습니다.

- Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경)
- Windows Server 2022 Datacenter: Azure Edition(Core)

인터넷과 같은 신뢰할 수 없는 네트워크를 통해 edge 파일 서버에 안전 하 고 신뢰할 수 있는 연결을 제공 하는 기업, 모바일 장치 사용자 및 지점에 대 한 "SMB VPN"을 제공 합니다. 표준에는 TLS 1.3의 최대 데이터 보호를 위해 설계 되었으며 사용 하지 않도록 설정할 수 없는 [암호화가 필요한](https://datatracker.ietf.org/doc/rfc9000/) , HTTP/3에서 사용 되는 IETF 표준화 된 프로토콜이 있습니다. SMB는 일반적인 방식으로 작동 합니다. 즉, 사용자 환경이 변경 되지 않습니다. 다중 채널, 서명, 압축, 지속적인 가용성 및 디렉터리 임대와 같은 SMB 기능은 정상적으로 작동 합니다. 

또한 smb를 통한 smb는 [Windows 서버에 대 한 컴퓨터 모범 사례 자동 관리](automanage-windows-server.md) 와 통합 되어 있기 때문에 smb를 통한 smb 관리를 용이 하 게 해줍니다. 향상 된 인증서를 사용 하 여 암호화 및 조직에서 복잡 한 공개 키 인프라를 유지 관리 하는 경우가 자주 발생 합니다. 컴퓨터의 모범 사례 자동 관리에서는 경고가 발생 하지 않고 인증서가 만료 되지 않도록 하 고, SMB를 통한 SMB가 최대 서비스 연속성에 대해 사용 하도록 설정 되어 있는지 확인 합니다.

자세한 내용은 [자동 관리 컴퓨터 모범 사례를 사용 하 여](automanage-smb-over-quic.md)예를 들어 향상 된 [smb](/windows-server/storage/file-server/smb-over-quic) 및 smb를 통한 smb 관리를 참조 하세요.
 

### <a name="extended-network-for-azure"></a>Azure 용 확장 네트워크

Azure 용 확장 네트워크는 다음 이미지에서 제공 됩니다.

- Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경)
- Windows Server 2022 Datacenter: Azure Edition(Core)

Azure 확장 네트워크를 사용하면 온-프레미스 서브넷을 Azure로 확장하여 온-프레미스 가상 머신이 Azure로 마이그레이션할 때 원래 온-프레미스 개인 IP 주소를 유지할 수 있습니다. 자세히 알아보려면 [Azure 확장 네트워크](/windows-server/manage/windows-admin-center/azure/azure-extended-network)를 참조하세요.  


## <a name="getting-started-with-windows-server-azure-edition"></a>Windows Server Azure Edition으로 시작하기

먼저 사용하려는 Windows Server용 Automanage 기능을 고려한 다음, 이러한 모든 기능을 지원하는 해당 VM 이미지를 선택하는 것이 중요합니다.  일부 _Windows Server Azure Edition_ 이미지는 기능의 하위 집합만 지원합니다. 자세한 내용은 아래 표를 참조하세요.

### <a name="deciding-which-image-to-use"></a>사용할 이미지 결정 

|이미지|기능|
|--|--|
|Windows Server 2022 Datacenter: Azure Edition(데스크톱 환경) | Azure에 대 한 확장 된 네트워크와 Azure의 SMB | 
| Windows Server 2022 Datacenter: Azure Edition(Core) | Hotpatch, Azure 용 확장 된 네트워크, Azure에 대 한 SMB | 

### <a name="creating-a-vm"></a>VM 만들기

새 VM에서 Windows Server용 Automanage 기능 사용을 시작하려면 원하는 방법을 사용하여 Azure VM을 만들고 사용하려는 [기능](#getting-started-with-windows-server-azure-edition) 세트에 해당하는 _Windows Server Azure Edition_ 이미지를 선택합니다.  

> [!IMPORTANT]
> 일부 기능에는 VM을 만드는 동안 수행할 특정 구성 단계가 있으며 미리 보기에 있는 일부 기능에는 특정 옵트인 및 포털 보기 요구 사항이 있습니다.  VM에서이 기능을 사용 하는 방법에 대해 자세히 알아보려면 위의 개별 기능 항목을 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Automation에 대한 자세한 정보](automanage-virtual-machines.md)