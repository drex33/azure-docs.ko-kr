---
title: 포함 파일
description: 포함 파일
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 06/04/2021
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: 234d2510f115edf646ac490126e316a7dd3369c1
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019274"
---
MARS 에이전트는 Azure Active Directory, Azure Storage 및 Azure Backup 서비스 엔드포인트에 대한 액세스 권한이 필요합니다. 공용 IP 범위를 가져오는 방법은 [JSON 파일](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519&preserveview=true)을 참조하세요. Azure Backup ( `AzureBackup` ), Azure Storage ( `Storage` ) 및 Azure Active Directory ()에 해당 하는 ip에 대 한 액세스를 허용 `AzureActiveDirectory` 합니다. 또한 Windows 버전에 따라 운영 체제의 네트워크 연결 검사를 수행하려면 `www.msftconnecttest.com` 및 `www.msftncsi.com`에 액세스해야 합니다.

컴퓨터의 인터넷 액세스가 제한 된 경우 방화벽, 프록시 및 네트워크 설정에서 다음 Fqdn 및 공용 IP 주소에 대 한 액세스를 허용 하는지 확인 합니다.

### <a name="url-and-ip-access"></a>URL 및 IP 액세스

**FQDN**

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- `*.microsoft.com`
- `*.windowsazure.com`
- `*.microsoftonline.com`
- `*.windows.net`

**IP 주소**

- `20.190.128.0/18`
- `40.126.0.0/18`

미국 정부 고객인 경우 다음 URL에 대한 액세스 권한이 있는지 확인합니다.

- `www.msftncsi.com`
- `*.microsoft.com`
- `*.windowsazure.us`
- `*.microsoftonline.us`
- `*.windows.net`
- `*.usgovcloudapi.net`

위에 나열된 모든 URL 및 IP 주소에 대한 액세스는 포트 443에서 HTTPS 프로토콜을 사용합니다.

MARS 에이전트를 사용하여 Azure VM에서 파일 및 폴더를 백업하는 경우 Azure 가상 네트워크도 액세스를 허용하도록 구성해야 합니다. NSG(네트워크 보안 그룹)를 사용하는 경우 AzureBackup 서비스 태그를 사용하여 Azure Backup에 대한 아웃바운드 액세스를 허용하세요. Azure Backup 태그 외에도 Azure AD(AzureActiveDirectory) 및 Azure Storage(Storage)에 대해 유사한 [NSG 규칙](../articles/virtual-network/network-security-groups-overview.md#service-tags)을 만들어 인증 및 데이터 전송에 대한 연결을 허용해야 합니다.

Azure Backup 태그에 대한 규칙을 만들려면 다음 단계를 수행합니다.

1. **모든 서비스** 에서 **네트워크 보안 그룹** 으로 이동하여 네트워크 보안 그룹을 선택합니다.
1. **설정** 아래에서 **아웃바운드 보안 규칙** 을 선택합니다.
1. **추가** 를 선택합니다.
1. [보안 규칙 설정](../articles/virtual-network/manage-network-security-group.md#security-rule-settings)에 설명된 대로 새 규칙을 만드는 데 필요한 세부 정보를 모두 제공합니다.<br>옵션이 아래와 같이 설정되었는지 확인합니다.
   - **대상** 이 _서비스 태그_ 로 설정되어 있는지 확인
   - **대상 서비스 태그** 가 _AzureBackup_ 으로 설정되어 있는지 확인
1. **추가** 를 선택하여 새로 만든 아웃바운드 보안 규칙을 저장합니다.

마찬가지로, Azure Storage 및 Azure AD에 대한 NSG 아웃바운드 보안 규칙을 만들 수 있습니다. 서비스 태그에 대해 자세히 알아보려면 [가상 네트워크 서비스 태그](../articles/virtual-network/service-tags-overview.md)를 참조하세요.

### <a name="azure-expressroute-support"></a>Azure ExpressRoute 지원

공용 피어링(이전 회로에 사용 가능)을 사용하여 Azure ExpressRoute를 통해 데이터를 백업할 수 있습니다. 개인 피어링을 통한 Microsoft 피어링 백업은 지원되지 않습니다.

퍼블릭 피어링을 사용하려면 다음 도메인 및 주소에 포트 443에 대한 HTTPS 액세스 권한이 있는지 확인합니다.

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- `*.microsoft.com`
- `*.windowsazure.com`
- `*.microsoftonline.com`
- `*.windows.net`

**IP 주소**
- `20.190.128.0/18`
- `40.126.0.0/18`

Microsoft 피어링을 사용하려면 다음 서비스, 지역 및 관련 커뮤니티 값을 선택합니다.
- Azure Active Directory(12076:5060)
- Azure 지역(Recovery Services 자격 증명 모음의 위치에 따름)
- Azure Storage(Recovery Services 자격 증명 모음의 위치에 따름)

[ExpressRoute 라우팅 요구 사항](../articles/expressroute/expressroute-routing.md#bgp)에 대해 자세히 알아보세요.

>[!NOTE]
>공용 피어링은 새 회로에 사용되지 않습니다.


### <a name="private-endpoint-support"></a>프라이빗 엔드포인트 지원

이제 프라이빗 엔드포인트를 사용하여 데이터를 서버에서 Recovery Services 자격 증명 모음으로 안전하게 백업할 수 있습니다. 프라이빗 엔드포인트를 통해서는 Azure AD에 액세스할 수 없으므로 아웃바운드 액세스를 위해 Azure AD에 필요한 IP 및 FQDN을 별도로 허용해야 합니다.

MARS 에이전트를 사용하여 온-프레미스 리소스를 백업하는 경우 (백업할 리소스가 포함된) 온-프레미스 네트워크는 자격 증명 모음의 프라이빗 엔드포인트를 포함하는 Azure VNet과 피어링되어 있어야 합니다. 그러면 MARS 에이전트를 계속 설치하고 백업을 구성할 수 있습니다. 다만 백업을 위한 모든 통신은 피어링된 네트워크를 통해 수행되어야 합니다.

MARS 에이전트를 등록한 후에 자격 증명 모음의 프라이빗 엔드포인트를 제거하는 경우 컨테이너를 자격 증명 모음에 다시 등록해야 합니다. 보호를 중지할 필요는 없습니다. 자세한 내용은 [Azure Backup에 대 한 개인 끝점](../articles/backup/private-endpoints.md)을 참조 하세요.

### <a name="throttling-support"></a>제한 지원

| 기능 | 세부 정보 |
|---|---|
| 대역폭 제어 | 지원됨. MARS 에이전트에서 **속성 변경** 을 사용하여 대역폭을 조정합니다. |
| 네트워크 제한 | Windows Server 2008 R2, Windows Server 2008 SP2 또는 Windows 7을 실행하는 백업된 컴퓨터에는 사용할 수 없습니다. |
