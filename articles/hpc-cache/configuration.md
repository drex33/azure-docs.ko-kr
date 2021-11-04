---
title: Azure HPC Cache 설정 구성
description: MTU, 사용자 지정 NTP 및 DNS 구성과 같은 캐시에 대한 추가 설정을 구성하는 방법과 Azure Blob 스토리지 대상에서 Express 스냅샷에 액세스하는 방법에 대해 설명합니다.
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: femila
ms.openlocfilehash: ad4dedc023dc6ba5f58e51c816d422acb3ab2c69
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019992"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>추가 Azure HPC Cache 설정 구성

Azure Portal의 **네트워킹** 페이지에는 여러 설정을 사용자 지정하는 옵션이 있습니다. 대부분의 사용자는 이러한 설정을 기본값에서 변경할 필요가 없습니다.

이 문서에서는 Azure Blob 스토리지 대상에 대한 스냅샷 기능을 사용하는 방법에 대해서도 설명합니다. 스냅샷 기능에 구성 가능한 설정이 없습니다.

설정을 보려면 Azure Portal에서 캐시의 **네트워킹** 페이지를 엽니다.

![Azure Portal에서 네트워킹 페이지의 스크린샷](media/networking-page.png)

> [!NOTE]
> 이 페이지의 이전 버전에는 캐시 수준 루트 Squash 설정이 포함되어 있지만 이 설정은 [클라이언트 액세스 정책](access-policies.md)으로 이동되었습니다.

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>MTU 값 조정
<!-- linked from troubleshoot-nas article -->

**MTU 크기** 레이블이 지정된 드롭다운 메뉴를 사용하여 캐시에 대한 최대 전송 단위 크기를 선택할 수 있습니다.

기본값은 1500바이트이지만 1400으로 변경할 수 있습니다.

> [!NOTE]
> 캐시의 MTU 크기를 낮추는 경우 캐시와 통신하는 클라이언트와 스토리지 시스템의 MTU 설정이 동일하거나 더 낮은 값이 있는지 확인합니다.

캐시 MTU 값을 낮추면 나머지 캐시 네트워크에서 패킷 크기 제한을 해결하는 데 도움이 될 수 있습니다. 예를 들어 일부 VPN은 전체 크기 1500바이트 패킷을 전송할 수 없습니다. VPN을 통해 전송되는 패킷의 크기를 줄이면 이 문제를 해결할 수 있습니다. 그러나 캐시 MTU 설정이 낮을수록 클라이언트 및 스토리지 시스템을 비롯하여 캐시와 통신하는 다른 구성 요소에도 낮은 MTU 설정이 있어야 통신 문제가 발생하지 않습니다.

다른 시스템 구성 요소에서 MTU 설정을 변경하지 않으려는 경우 캐시의 MTU 설정을 낮추어서는 안 됩니다. VPN 패킷 크기 제한 문제를 해결하는 다른 솔루션이 있습니다. 이 문제를 진단하고 해결하는 방법에 대한 자세한 내용은 NAS 문제 해결 문서에서 [VPN 패킷 크기 제한 조정](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)을 참조하세요.

[Azure VM에 대한 TCP/IP 성능 튜닝](../virtual-network/virtual-network-tcpip-performance-tuning.md)을 읽어 Azure 가상 네트워크의 MTU 설정에 대해 자세히 알아보세요.

## <a name="customize-ntp"></a>NTP 사용자 지정

캐시는 기본적으로 Azure 기반 시간 서버 time.windows.com을 사용합니다. 캐시에서 다른 NTP 서버를 사용하려면 **NTP 구성** 섹션에서 지정합니다. 정규화된 도메인 이름 또는 IP 주소를 사용합니다.

## <a name="set-a-custom-dns-configuration"></a>사용자 지정 DNS 구성 설정

> [!CAUTION]
> 필요하지 않은 경우 캐시 DNS 구성을 변경하지 마세요. 구성 실수는 심각한 결과를 초래할 수 있습니다. 구성에서 Azure 서비스 이름을 확인할 수 없는 경우 HPC Cache 인스턴스는 영구적으로 액세스할 수 없게 됩니다.
>
> 사용자 지정 DNS 구성을 설정하기 전에 Azure 담당자에게 문의하세요.

Azure HPC Cache는 안전하고 편리한 Azure DNS 시스템을 사용하도록 자동으로 구성됩니다. 그러나 일부 비정상적인 구성의 경우 캐시에서 Azure 시스템 대신 별도의 온-프레미스 DNS 시스템을 사용해야 합니다. **네트워킹** 페이지의 **DNS 구성** 섹션을 사용하여 이러한 종류의 시스템을 지정합니다.

Azure 담당자에게 문의하거나 Microsoft 서비스 및 지원에 문의하여 사용자 지정 캐시 DNS 구성을 사용해야 하는지 여부를 확인합니다.

Azure HPC Cache에서 사용할 자체 온-프레미스 DNS 시스템을 구성하는 경우 로컬 DNS 서버에서 Azure 서비스 엔드포인트 이름을 직접 확인할 수 있는지 확인해야 합니다. DNS 서버가 공개 이름 확인에서 제한된 경우 HPC Cache가 작동하지 않습니다.

Azure HPC Cache에 사용하기 전에 DNS 구성이 이러한 항목을 성공적으로 확인할 수 있는지 확인합니다.

* ``*.core.windows.net``
* CRL(인증서 해지 목록) 다운로드 및 OCSP(온라인 인증서 상태 프로토콜) 확인 서비스 이 [Azure TLS 문서](../security/fundamentals/tls-certificate-changes.md)의 끝에 있는 [방화벽 규칙 항목](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me)에는 일부 목록이 제공되지만 Microsoft 기술 담당자에게 문의하여 모든 요구 사항을 이해해야 합니다.
* NTP 서버의 정규화된 도메인 이름(time.windows.com 또는 사용자 지정 서버)

캐시에 대해 사용자 지정 DNS 서버를 설정해야 하는 경우 제공된 필드를 사용합니다.

* **DNS 검색 도메인**(선택 사항) - 검색 도메인을 입력합니다(예: ``contoso.com``). 단일 값이 허용되거나 비워 둘 수 있습니다.
* **DNS 서버** - 최대 3개의 DNS 서버를 입력합니다. IP 주소를 기준으로 지정합니다.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

프로덕션 환경에서 사용하기 전에 테스트 캐시를 사용하여 DNS 설정을 확인하고 구체화하는 것이 좋습니다.

### <a name="refresh-storage-target-dns"></a>스토리지 대상 DNS 새로 고침

DNS 서버에서 IP 주소를 업데이트하는 경우 관련 NFS 스토리지 대상을 일시적으로 사용할 수 없게 됩니다. 스토리지 대상 보기 및 관리에서 사용자 지정 DNS 시스템 IP 주소를 업데이트하는 방법을 [읽어보세요.](manage-storage-targets.md#update-ip-address-custom-dns-configurations-only)

## <a name="view-snapshots-for-blob-storage-targets"></a>Blob 스토리지 대상에 대한 스냅샷 보기

Azure HPC Cache는 Azure Blob 스토리지 대상에 대한 스토리지 스냅샷을 자동으로 저장합니다. 스냅샷은 백 엔드 스토리지 컨테이너의 콘텐츠에 대한 빠른 참조 지점을 제공합니다.

스냅샷은 데이터 백업의 대체가 아니라 캐시된 데이터의 상태에 대한 정보를 포함하지 않습니다.

> [!NOTE]
> 이 스냅샷 기능은 NetApp 또는 Isilon 스토리지 소프트웨어에 포함된 스냅샷 기능과 다릅니다. 이러한 스냅샷 구현은 스냅샷을 만들기 전에 캐시에서 백 엔드 스토리지 시스템으로 변경 내용을 플러시합니다.
>
> 효율성을 위해 Azure HPC Cache 스냅샷은 변경 내용을 먼저 플러시하지 않고 Blob 컨테이너에 기록된 데이터만 기록합니다. 이 스냅샷은 캐시된 데이터의 상태를 나타내지 않으므로 최근 변경 내용을 포함하지 않을 수 있습니다.

이 기능은 Azure Blob 스토리지 대상에 대해서만 사용할 수 있으며 해당 구성을 변경할 수 없습니다.

스냅샷은 8시간(UTC 0:00, 08:00 및 16:00)마다 수행됩니다.

Azure HPC Cache는 새 항목으로 바뀔 때까지 매일, 매주 및 매월 스냅샷을 저장합니다. 스냅샷 보존 한도는 다음과 같습니다.

* 최대 20일간의 스냅샷
* 최대 8주간의 스냅샷
* 최대 3개월간의 스냅샷

탑재된 Blob 스토리지 대상의 루트에 있는 `.snapshot` 디렉터리에서 스냅샷에 액세스합니다.
