---
title: Azure 방화벽 FTP 지원
description: 기본적으로 활성 FTP는 Azure Firewall에서 사용하지 않도록 설정되어 있습니다. PowerShell, CLI 및 ARM 템플릿을 사용하여 이 기능을 사용하도록 설정할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 09/23/2021
ms.author: victorh
ms.openlocfilehash: 7aeb7c596f1b4ca286cc9fce22965418c7c61c85
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084403"
---
# <a name="azure-firewall-ftp-support"></a>Azure 방화벽 FTP 지원

FTP를 지원 하기 위해 방화벽은 다음과 같은 주요 측면을 고려해 야 합니다.
- FTP 모드 – 활성 또는 수동
- 클라이언트/서버 위치-인터넷 또는 인트라넷
- Flow 방향 (인바운드 또는 아웃 바운드)입니다. 

Azure 방화벽은 활성 및 수동 FTP 시나리오를 모두 지원 합니다. FTP 모드에 대 한 자세한 내용은 [ACTIVE ftp Vs PASSIVE ftp (](https://slacksite.com/other/ftp.html)영문)를 참조 하십시오. 

기본적으로 수동 FTP는 사용 하도록 설정 되어 있으며 ftp 포트 명령을 사용 하 여 FTP 바운스 공격 으로부터 보호 하기 위해 활성 FTP 지원을 사용 하지 않도록 설정 되어 있습니다. 

그러나 Azure PowerShell, Azure CLI 또는 Azure ARM 템플릿을 사용하여 배포할 때 활성 FTP를 사용하도록 설정할 수 있습니다. Azure 방화벽은 활성 및 수동 FTP를 동시에 지원할 수 있습니다. *activeftp* 는 Premium 및 표준 sku, 보안 허브 및 VNet 방화벽, 방화벽 정책 및 클래식 규칙을 사용 하는 경우 둘 다에 대해 사용 하도록 설정할 수 있는 방화벽 속성입니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

다음 표에서는 다양 한 FTP 시나리오를 지 원하는 데 필요한 구성을 보여 줍니다.


|방화벽 시나리오  |활성 FTP 모드   |수동 FTP 모드  |
|---------|---------|---------|
|VNet-VNet     |구성할 네트워크 규칙:<br>-원본 VNet에서 대상 IP 포트 21로 허용<br>-대상 IP 포트 20에서 원본 VNet으로 허용 |구성할 네트워크 규칙:<br>-원본 VNet에서 대상 IP 포트 21로 허용<br>-원본 VNet에서 대상 IP로 허용 \<Range of Data Ports>|
|아웃 바운드 VNet-인터넷<br><br>(VNet의 FTP 클라이언트, 인터넷 서버)      |지원 되지 않음 *|**사전 조건**: 다른 원본 IP 주소에서 데이터 및 제어 채널을 허용 하도록 FTP 서버를 구성 합니다. 또는 단일 공용 IP 주소를 사용 하 여 Azure 방화벽을 구성 합니다.<br><br>구성할 네트워크 규칙:<br>-원본 VNet에서 대상 IP 포트 21로 허용<br>-원본 VNet에서 대상 IP로 허용 \<Range of Data Ports> |
|인바운드 DNAT<br><br>(인터넷의 FTP 클라이언트, VNet의 서버)      |구성할 DNAT 규칙:<br>-DNAT 인터넷 원본에서 VNet IP 포트 21<br><br>구성할 네트워크 규칙:<br>-VNet IP 포트 20에서 인터넷 원본으로 허용 |**사전 조건**:<br>다른 원본 IP 주소에서 데이터 및 제어 채널을 허용 하도록 FTP 서버를 구성 합니다.<br><br>팁: Azure 방화벽은 규칙에서 제한 된 수의 DNAT 합니다. FTP 서버에서 데이터 채널의 작은 포트 범위를 사용 하도록 구성 하는 것이 중요 합니다.<br><br>구성할 DNAT 규칙:<br>-DNAT 인터넷 원본에서 VNet IP 포트 21<br>-인터넷 원본에서 VNet IP로의 DNAT \<Range of Data Ports> |

\* FTP 클라이언트가 인터넷의 FTP 서버에 연결 해야 하는 경우에는 활성 FTP가 작동 하지 않습니다. 활성 FTP는 ftp 서버에 데이터 채널에 사용할 IP 주소 및 포트를 알려 주는 FTP 클라이언트의 포트 명령을 사용 합니다. 이 포트 명령은 변경할 수 없는 클라이언트의 개인 IP 주소를 사용 합니다. Azure 방화벽을 통과 하는 클라이언트 쪽 트래픽은 인터넷 기반 통신에 사용 되므로 FTP 서버에서 포트 명령이 잘못 된 것으로 보입니다. 이는 클라이언트 쪽 NAT와 함께 사용 될 경우 활성 FTP에 대 한 일반적인 제한 사항입니다. 


## <a name="deploy-using-azure-powershell"></a>Azure PowerShell을 사용하여 배포

Azure PowerShell을 사용하여 배포하려면 `AllowActiveFTP` 매개 변수를 사용합니다. 자세한 내용은 [활성 FTP를 사용하여 방화벽 만들기](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)를 참조하세요.

## <a name="deploy-using-azure-cli"></a>Azure CLI를 사용하여 배포

Azure CLI를 사용하여 배포하려면 `--allow-active-ftp` 매개 변수를 사용합니다. 자세한 내용은 [az network firewall create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters)를 참조하세요. 

## <a name="deploy-azure-resource-manager-arm-template"></a>ARM (배포 Azure Resource Manager) 템플릿

ARM 템플릿을 사용하여 배포하려면 `AdditionalProperties` 필드를 사용합니다.

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
자세한 내용은 [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Firewall을 배포하는 방법을 알아보려면 [Azure PowerShell을 사용하여 Azure Firewall 배포 및 구성](deploy-ps.md)을 참조하세요.