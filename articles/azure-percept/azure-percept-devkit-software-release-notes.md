---
title: Azure Percept DK 소프트웨어 릴리스 정보
description: Azure Percept DK 소프트웨어의 변경 사항에 대한 정보입니다.
author: EthanChangAED
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: 6f5e72f25d5b8b4060dd438faa0c8bfb40de5e00
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133485443"
---
# <a name="azure-percept-dk-software-release-notes"></a>Azure Percept DK 소프트웨어 릴리스 정보

이 페이지에서는 각 Azure Percept DK OS 및 펌웨어 릴리스에 대한 변경 사항 및 수정 사항에 대한 정보를 제공합니다.

업데이트 이미지를 다운로드하려면 [USB 케이블 업데이트에 대한 Azure Percept DK 소프트웨어 릴리스](./software-releases-usb-cable-updates.md) 또는 [OTA 업데이트에 대한 Azure Percept DK 소프트웨어 릴리스](./software-releases-over-the-air-updates.md)를 참조하세요.

## <a name="november-2111-release"></a>11월(2111) 릴리스

- 운영 체제
  - 최신 보안 수정.
  - 자동 패키지 업데이트를 사용하지 않도록 설정했습니다.
  - USB 디바이스 노드에 액세스하는 Azure Percept 컨테이너에 대한 사용자 권한을 설정합니다.

## <a name="september-2109-release"></a>9월(2109) 릴리스

- Wi-Fi:
  - hostapd.service가 지속적으로 다시 시도하고 다시 시작하지 않도록 자동 채널 선택 대신 고정 채널을 사용합니다.
- 설치 환경:
  - OOBE 서버 시스템 오류가 지역화됩니다.
  - IPv6 다중 라우팅 테이블을 사용하도록 설정합니다.
- 운영 체제
  - 최신 보안 수정.
  - Nginx 서비스는 루트가 아닌 사용자로 실행됩니다.


## <a name="july-2107-release"></a>7월(2017년) 릴리스

> [!IMPORTANT]
> 코드 서명 변경으로 인해 이 릴리스의 OTA(Over-The-Air) 패키지는 2106 릴리스를 실행하는 Azure Percept DK와만 호환됩니다. 현재 이전 SW 릴리스 버전을 실행 중인 Azure Percept DK 사용자의 경우 Microsoft는 USB 케이블을 통해 업데이트를 수행하거나 2107로 업데이트하기 전에 릴리스 2106으로 OTA 업데이트를 먼저 수행할 것을 권장합니다.

- Wi-Fi:
  - 설정이 완료된 후 Wi-Fi 액세스 지점이 종료되도록 보안을 강화합니다.
  - 개발 키트의 **설정** 단추를 누르면 개발 키트의 Wi-Fi 액세스 지점이 설정 환경 웹 서비스와 동기화되지 않을 수 있는 문제가 수정되었습니다.
  - Wi-Fi 액세스 지점 iptables 규칙을 개선하여 복원력을 높이고 불필요한 규칙을 제거했습니다.
  - 연결된 여러 Wi-Fi 네트워크의 우선 순위가 올바르게 지정되지 않는 문제가 수정되었습니다.
- 설치 환경:
  - 지원되는 지역에 대한 지역화를 추가하고 더 나은 가독성을 위해 텍스트를 업데이트했습니다.
  - 설정 환경이 때때로 로딩 페이지에서 멈추는 문제가 수정되었습니다.
- 일반 네트워킹:
  - IPv6이 유효한 DHCP 임대를 얻지 못하는 문제가 해결되었습니다.
- 운영 체제:
  - 보안 수정 사항입니다.

## <a name="june-2106-release"></a>6월(2016년) 릴리스

- OTA 에이전트에 대한 이미지 확인 메커니즘이 업데이트되었습니다.
- 설정 환경에 대한 UI 개선 및 버그 수정.

## <a name="may-2105-release"></a>5월(2015년) 릴리스

- CBL-Mariner OS에 대한 보안 업데이트.

## <a name="april-2104-release"></a>4월(2014년) 릴리스

- Azure Percept DK 스토리지가 가득 찰 수 있는 로그 회전 문제가 수정되었습니다.
- 설정 환경에서 Azure에 대한 TPM 기반 프로비저닝을 사용하도록 설정했습니다.
- 설정 환경 및 Wi-Fi 액세스 지점에 자동 시간 제한을 추가했습니다. 30분 후 또는 설정 환경 완료 후.
- Wi-Fi 액세스 지점 SSID가 "**scz-[xxx]** "에서 "**apd-[xxx]** "(으)로 변경되었습니다.

## <a name="next-steps"></a>다음 단계

- [업데이트 전략을 확인하는 방법](./how-to-determine-your-update-strategy.md)
