---
title: Azure Stack Edge Pro FPGA 보안
description: 온-프레미스 및 클라우드에서 Azure Stack Edge Pro FPGA 디바이스, 서비스, 데이터를 보호하는 보안 및 개인 정보 보호 기능에 대해 설명합니다.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b2c6ee6e1bc494f0bef4a04b07115c400c8ce1ec
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112914052"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Azure Stack Edge 보안 및 데이터 보호

보안은 새로운 기술을 채택할 때, 특히 해당 기술이 기밀 또는 재산적 가치를 가지는 데이터에 사용되는 경우 중요한 관심사입니다. Azure Stack Edge를 사용하면 권한 있는 엔터티만 데이터를 확인, 수정 또는 삭제할 수 있습니다.

이 문서에서는 각 솔루션 구성 요소와 여기에 저장된 데이터를 보호하는 데 도움이 되는 Azure Stack Edge 보안 기능에 대해 설명합니다.

Azure Stack Edge는 상호 작용하는 다음 네 가지 기본 구성 요소로 이루어져 있습니다.

- **Azure에서 호스트된 Azure Stack Edge 서비스**. 디바이스 주문을 작성하고 디바이스를 구성한 다음, 완료까지 주문을 추적하는 데 사용되는 관리 리소스입니다.
- **Azure Stack Edge Pro FPGA 디바이스**. 온-프레미스 데이터를 Azure로 가져올 수 있도록 사용자에게 제공되는 전송 디바이스입니다.
- **디바이스에 연결된 클라이언트/호스트**. Azure Stack Edge Pro FPGA 디바이스에 연결하고 보호해야 하는 데이터를 포함하는 인프라 내 클라이언트입니다.
- **클라우드 스토리지**. Azure 클라우드 플랫폼에서 데이터가 저장되는 위치입니다. 이 위치는 일반적으로 사용자가 만드는 Azure Stack Edge 리소스에 연결된 스토리지 계정입니다.

## <a name="azure-stack-edge-service-protection"></a>Azure Stack Edge 서비스 보호

Azure Stack Edge 서비스는 Azure에서 호스트된 관리 서비스입니다. 이 서비스는 디바이스를 구성하고 관리하는 데 사용됩니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Azure Stack Edge 디바이스 보호

Azure Stack Edge 디바이스는 데이터를 로컬에서 처리한 다음, Azure에 전송하여 데이터 변환을 지원하는 온-프레미스 디바이스입니다. 사용자 디바이스는 다음 특징이 있습니다.

- Azure Stack Edge 서비스에 액세스하기 위해 활성화 키가 있어야 합니다.
- 디바이스 암호로 항상 보호됩니다.
- 잠긴 디바이스입니다. 디바이스 BMC 및 BIOS는 암호로 보호됩니다. BIOS는 제한된 사용자 액세스를 통해 보호됩니다.
- 보안 부팅을 사용할 수 있습니다.
- Windows Defender Device Guard를 실행합니다. Device Guard를 사용하면 코드 무결성 정책에서 정의된 신뢰할 수 있는 애플리케이션만 실행할 수 있습니다.

### <a name="protect-the-device-via-activation-key"></a>활성화 키를 통해 디바이스 보호

권한 있는 Azure Stack Edge 디바이스만 Azure 구독에서 직접 만든 Azure Stack Edge 서비스에 조인할 수 있습니다. 디바이스에 권한을 부여하려면 활성화 키를 사용하여 Azure Stack Edge 서비스에서 디바이스를 활성화해야 합니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

자세한 내용은 [활성화 키 가져오기](azure-stack-edge-deploy-prep.md#get-the-activation-key)를 참조하세요.

### <a name="protect-the-device-via-password"></a>암호를 통해 디바이스 보호

암호는 권한 있는 사용자만 데이터에 액세스할 수 있도록 보장합니다. Azure Stack Edge 디바이스는 잠긴 상태로 부팅됩니다.

다음을 할 수 있습니다.

- 브라우저를 통해 디바이스의 로컬 웹 UI에 연결한 다음, 암호를 입력하여 디바이스에 로그인합니다.
- HTTP를 통해 디바이스 PowerShell 인터페이스에 원격으로 연결합니다. 원격 관리는 기본적으로 켜져 있습니다. 그런 다음 디바이스 암호를 입력하여 디바이스에 로그인할 수 있습니다. 자세한 내용은 [Azure Stack Edge Pro FPGA 디바이스에 원격으로 연결](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)을 참조하세요.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 로컬 웹 UI를 사용하여 [암호를 변경](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access)합니다. 암호를 변경하는 경우 로그인 문제가 발생하지 않도록 모든 원격 액세스 사용자에게 알려야 합니다.

## <a name="protect-your-data"></a>데이터 보호

이 섹션에서는 전송 중인 데이터와 저장된 데이터를 보호하는 Azure Stack Edge Pro FPGA 보안 기능에 대해 설명합니다.

### <a name="protect-data-at-rest"></a>미사용 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256비트 암호화는 로컬 데이터를 보호하는 데 사용됩니다.


### <a name="protect-data-in-flight"></a>전송 중인 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>스토리지 계정을 통해 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- [스토리지 계정 키](azure-stack-edge-manage-shares.md#sync-storage-keys)를 정기적으로 순환하고 동기화하면 권한 없는 사용자로부터 스토리지 계정을 보호하는 데 도움이 됩니다.

## <a name="manage-personal-information"></a>개인 정보 관리

Azure Stack Edge 서비스는 다음 시나리오에서 개인 정보를 수집합니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

공유에 액세스하거나 삭제할 수 있는 사용자 목록을 보려면 [Azure Stack Edge Pro FPGA에서 공유 관리](azure-stack-edge-manage-shares.md)의 단계를 수행합니다.

자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인정보처리방침을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro FPGA 디바이스 배포](azure-stack-edge-deploy-prep.md)
