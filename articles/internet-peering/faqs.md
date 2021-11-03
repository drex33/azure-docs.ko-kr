---
title: 인터넷 피어링 - FAQ
titleSuffix: Azure
description: 인터넷 피어링 - FAQ
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c4c58a0550d35575721a1d27ce7a572ac0e00e90
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024205"
---
# <a name="internet-peering---faqs"></a>인터넷 피어링 - FAQ

일반적인 질문에 대한 다음 정보를 검토할 수 있습니다.

**인터넷 피어링과 피어링 서비스 간의 차이점은 무엇인가요?**

피어링 서비스는 기업 고객을 위해 Microsoft에 엔터프라이즈급 공용 IP 연결을 제공하는 서비스입니다. 엔터프라이즈급 인터넷에는 Microsoft에 대한 높은 처리량 연결 및 HA 연결에 대한 중복성을 제공하는 ISP를 통한 연결이 포함됩니다. 또한 사용자 트래픽은 가장 가까운 Microsoft Edge에 대한 대기 시간에 최적화됩니다. 피어링 서비스는 파트너 이동 통신 사업자와의 피어링 연결을 기반으로 합니다. 파트너와의 피어링 연결은 Exchange 피어링이 아닌 직접 피어링이어야 합니다. 직접 피어링에는 로컬 및 지역 중복이 있어야 합니다.

**레거시 피어링이란?**

Azure PowerShell을 사용하여 설정된 피어링 연결은 Azure 리소스로 관리됩니다. 과거에 설정된 피어링 연결은 Azure 리소스로 관리하도록 변환을 선택할 수 있는 레거시 피어링으로 시스템에 저장됩니다.

**New-AzPeeringDirectConnectionObject가 호출되면 Microsoft 및 피어 디바이스에 제공되는 IP 주소는 무엇인가요?**

New-AzPeeringDirectConnectionObject cmdlet을 호출할 때 `/31` 주소 ( `a.b.c.d/31` ) 또는 `/30` 주소 ( `a.b.c.d/30` )가 입력 됩니다. 첫 번째 IP 주소 ( `a.b.c.d+0` )는 피어의 장치에 지정 되 고 두 번째 ip 주소 ( `a.b.c.d+1` )는 Microsoft 장치에 제공 됩니다.

**New-AzPeeringDirectConnectionObject cmdlet에서 MaxPrefixesAdvertisedIPv4 및 MaxPrefixesAdvertisedIPv6 매개 변수란 무엇인가요?**

MaxPrefixesAdvertisedIPv4 및 MaxPrefixesAdvertisedIPv6 매개 변수는 피어가 Microsoft에서 수락할 수 있는 최대 IPv4 및 IPv6 접두사 수를 나타냅니다. 이러한 매개 변수는 언제든지 수정할 수 있습니다.