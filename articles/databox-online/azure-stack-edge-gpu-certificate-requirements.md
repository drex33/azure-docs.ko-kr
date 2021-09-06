---
title: Azure Stack Edge Pro 인증서 요구 사항 및 문제 해결 | Microsoft Docs
description: Azure Stack Edge Pro 디바이스에서의 인증서 요구 사항과 인증서 오류 문제 해결에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 6071420216a4c3e365ca5c35e1bf59713974e679
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360545"
---
# <a name="certificate-requirements"></a>인증서 요구 사항

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro 디바이스에 인증서를 설치하기 전에 충족해야 하는 인증서 요구 사항을 설명합니다. 요구 사항은 PFX 인증서, 발급 기관, 인증서 주체 이름 및 주체 대체 이름, 지원되는 인증서 알고리즘과 관련이 있습니다.

## <a name="certificate-issuing-authority"></a>인증서 발급 기관

인증서 발급 요구 사항은 다음과 같습니다.

* 내부 인증 기관 또는 공용 인증 기관에서 인증서를 발급해야 합니다.

* 자체 서명된 인증서의 사용은 지원되지 않습니다.

* 인증서의 *발급 대상:* 필드는 루트 CA 인증서를 제외하고 *발급자:* 필드와 달라야 합니다.


## <a name="certificate-algorithms"></a>인증서 알고리즘

디바이스에서는 RSA(Rivest–Shamir–Adleman) 인증서만 지원됩니다. ECDSA(타원 곡선 디지털 서명 알고리즘) 인증서는 지원되지 않습니다.

RSA 공개 키가 포함된 인증서를 RSA 인증서라고 합니다. ECC(타원 곡선 암호화) 공개 키가 포함된 인증서를 ECDSA(타원 곡선 디지털 서명 알고리즘) 인증서라고 합니다.

인증서 알고리즘 요구 사항은 다음과 같습니다.

* 인증서는 RSA 키 알고리즘을 사용해야 합니다.

* Microsoft RSA/Schannel Cryptographic Provider의 RSA 인증서만 지원됩니다.

* 인증서 서명 알고리즘은 SHA1일 수 없습니다.

* 최소 키 크기는 4096입니다.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>인증서 주체 이름 및 주체 대체 이름

인증서는 다음 주체 이름 및 주체 대체 이름 요구 사항을 충족해야 합니다.

* 인증서의 SAN(주체 대체 이름) 필드에 있는 모든 네임스페이스를 포함하는 단일 인증서를 사용할 수 있습니다. 또는 각 네임스페이스에 대해 개별 인증서를 사용할 수 있습니다. 두 방법 모두 필요한 경우 Blob(Binary Large Object)과 같은 엔드포인트에 와일드카드를 사용해야 합니다.

* 주체 이름(주체 이름의 일반 이름)이 주체 대체 이름 확장에 있는 주체 대체 이름의 일부인지 확인합니다.

* 인증서의 SAN 필드에 있는 모든 네임스페이스를 포함하는 단일 와일드카드 인증서를 사용할 수 있습니다.

* 엔드포인트 인증서를 만드는 경우 다음 표를 사용합니다.

    |형식 |SN(주체 이름)  |SAN(주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 스토리지|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |로컬 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |두 엔드포인트에 대한 다중 SAN 단일 인증서|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |노드|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate는 하드 코딩되었습니다.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>PFX 인증서

Azure Stack Edge Pro 디바이스에 설치된 PFX 인증서는 다음 요구 사항을 충족해야 합니다.

* SSL 기관에서 인증서를 받으면 인증서에 대한 전체 서명 체인이 있는지 확인합니다.

* PFX 인증서를 내보낼 때 **가능한 경우 체인의 모든 인증서 포함** 옵션을 선택 했는지 확인합니다.

* Azure Stack Edge Pro에는 공개 키와 프라이빗 키가 모두 필요하기 때문에 엔드포인트, 로컬 UI, 노드, VPN 및 Wi-Fi에 PFX 인증서를 사용합니다. 프라이빗 키에는 로컬 컴퓨터 키 특성 집합이 있어야 합니다.

* 인증서의 PFX 암호화는 3DES여야 합니다. Windows 10 클라이언트 또는 Windows Server 2016 인증서 저장소에서 내보낼 때 사용되는 기본 암호화입니다. 3DES와 관련한 자세한 정보는 [Triple DES](https://en.wikipedia.org/wiki/Triple_DES)를 참조하세요.

* 인증서 PFX 파일의 *키 사용* 필드에는 유효한 *디지털 서명* 및 *KeyEncipherment* 값이 있어야 합니다.

* 인증서 PFX 파일의 *확장된 키 사용* 필드에는 *서버 인증(1.3.6.1.5.5.7.3.1)* 및 *클라이언트 인증(1.3.6.1.5.5.7.3.2)* 값이 있어야 합니다.

* Azure Stack 준비 상태 검사기 도구를 사용하는 경우 배포 시점에 모든 인증서 PFX 파일에 대한 암호가 동일해야 합니다. 자세한 정보는 [Azure Stack Hub 준비 상태 검사기 도구를 사용하여 Azure Stack Edge Pro의 인증서 만들기](azure-stack-edge-gpu-create-certificates-tool.md)를 참조하세요.

* 인증서 PFX에 대한 암호는 복잡한 암호여야 합니다. 이 암호는 배포 매개 변수로 사용되므로 기록해 둡니다.

* Microsoft RSA/Schannel Cryptographic Provider의 RSA 인증서만 사용합니다.

자세한 정보는 [프라이빗 키를 사용하여 PFX 인증서 내보내기](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 디바이스에 대한 인증서 만들기

    - [Azure PowerShell cmdlet](azure-stack-edge-gpu-create-certificates-powershell.md) 사용
    - [Azure Stack Hub 준비 상태 검사기 도구](azure-stack-edge-gpu-create-certificates-tool.md) 사용

