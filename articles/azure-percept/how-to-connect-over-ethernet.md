---
title: 이더넷을 통해 Azure Percept DK에 연결
description: 이 가이드에서는 이더넷을 통해 연결할 때 Azure Percept DK 설정 환경으로 연결하는 방법을 보여 줍니다.
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 6ffc2a2d55bdad670c8323dfc57efad17dec1930
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227084"
---
# <a name="connect-to-azure-percept-dk-over-ethernet"></a>이더넷을 통해 Azure Percept DK에 연결

이 방법 가이드에서는 이더넷 연결을 통해 Azure Percept DK 설정 환경을 시작하는 방법을 알아봅니다. 이 가이드는 [빠른 시작: Azure Percept DK를 설정하고 첫 번째 AI 모델 배포](./quickstart-percept-dk-set-up.md) 가이드와 함께 제공됩니다. 아래에 설명된 각 옵션을 확인하고 사용자 환경에 가장 적합한 옵션을 선택합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Percept DK([여기에서 확인](https://go.microsoft.com/fwlink/?linkid=2155270))
- Wi-Fi 또는 이더넷 기능과 웹 브라우저를 지원하는 Windows, Linux 또는 OS X 기반 호스트 컴퓨터
- 네트워크 케이블

## <a name="identify-your-dev-kits-ip-address"></a>개발 키트의 IP 주소 식별

이더넷 연결을 통해 Azure Percept DK 설정 환경을 실행할 때 핵심적인 작업은 개발 키트의 IP 주소를 찾는 것입니다. 이 문서에서는 다음 세 가지 옵션을 다룹니다.
1. 네트워크 라우터에서
1. SSH를 통해
1. Nmap 도구를 통해

### <a name="from-your-network-router"></a>네트워크 라우터에서
개발 키트의 IP 주소를 식별하는 가장 빠른 방법은 네트워크 라우터에서 조회하는 것입니다.
1. 이더넷 케이블을 개발 키트에 연결하고 다른 쪽 끝을 라우터에 연결합니다.
1. Azure Percept DK를 켜도록 하겠습니다.
1. 네트워크 라우터에서 액세스 지침을 지정하는 스티커를 찾습니다.

    **라우터 스티커의 예는 다음과 같습니다.**

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-01.png" alt-text="네트워크 라우터의 예제 스티커":::

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-02.png" alt-text="네트워크 라우터의 또 다른 예제 스티커":::

1. 이더넷 또는 Wi-Fi에 연결된 컴퓨터에서 웹 브라우저를 엽니다.
1. 스티커에 있는 라우터의 브라우저 주소를 입력합니다.
1. 메시지가 표시되면 스티커에 있는 라우터의 이름과 암호를 입력합니다.
1. 라우터 인터페이스에서 내 디바이스(또는 라우터에 따라 유사한 옵션)를 선택합니다.
1. 디바이스 목록에서 Azure Percept 개발 키트를 찾습니다.
1. Azure Percept 개발 키트의 IP 주소를 복사합니다.

### <a name="via-ssh"></a>SSH를 통해
SSH를 통해 개발 키트에 연결하여 개발 키트의 IP 주소를 찾을 수 있습니다.

> [!NOTE]
> 개발 키트의 IP 주소를 식별할 때 SSH 방법을 사용하려면 개발 키트의 Wi-Fi 액세스 지점에 연결할 수 있어야 합니다. 가능하지 않은 경우에는 다른 방법 중 하나를 사용합니다.

1. 이더넷 케이블을 개발 키트에 연결하고 다른 쪽 끝을 라우터에 연결합니다.
1. Azure Percept DK의 전원을 켭니다.
1. SSH를 통해 개발 키트에 연결합니다. SSH를 통해 개발 키트에 연결하는 방법에 대한 자세한 지침은 [SSH를 통해 Azure Percept DK에 연결](./how-to-ssh-into-percept-dk.md)을 참조하세요.
1. 이더넷 로컬 네트워크 IP 주소를 나열하려면 SSH 터미널 창에 아래 명령을 입력합니다.

    ```bash
    ip a | grep eth1
    ```

    :::image type="content" source="media/how-to-connect-over-ethernet/ssh-local-network-address.png" alt-text="SSH 터미널에서 로컬 네트워크 IP를 식별하는 예제":::


1. 개발 키트의 IP 주소가 'inet' 다음에 표시됩니다. IP 주소를 복사합니다.

### <a name="using-the-nmap-tool"></a>Nmap 도구 사용
웹에 있는 무료 도구를 사용하여 개발 키트의 IP 주소를 식별할 수도 있습니다. 이러한 지침에서는 Nmap이라는 도구를 다룹니다.
1. 이더넷 케이블을 개발 키트에 연결하고 다른 쪽 끝을 라우터에 연결합니다.
1. Azure Percept DK의 전원을 켭니다.
1. 호스트 컴퓨터에서 플랫폼(Windows/Mac/Linux)에 필요한 [무료 Nmap 보안 스캐너](https://nmap.org/download.html)를 다운로드하여 설치합니다.
1. 컴퓨터의 "기본 게이트웨이"를 가져옵니다. [기본 게이트웨이를 찾는 방법](https://www.noip.com/support/knowledgebase/finding-your-default-gateway/)
1. Nmap 애플리케이션을 엽니다. 
1. *대상* 상자에 기본 게이트웨이를 입력하고 끝에 **/24** 를 추가합니다. *프로필* 을 **빠른 검사** 로 변경하고 **검색** 단추를 선택합니다.
    
    :::image type="content" source="media/how-to-connect-over-ethernet/nmap-tool.png" alt-text="Nmap 도구 입력 예제":::
 
1. 결과에서 디바이스 목록에서 **apd-xxxxxxxx** 와 유사한 Azure Percept 개발 키트를 찾습니다.
1. Azure Percept 개발 키트의 IP 주소를 복사합니다. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Azure Percept DK 설정 환경 시작
1. 이더넷 케이블을 개발 키트에 연결하고 다른 쪽 끝을 라우터에 연결합니다.
1. Azure Percept DK의 전원을 켭니다.
1. 웹 브라우저를 열고 개발 키트의 IP 주소 이름을 붙여넣습니다. 브라우저에서 설정 환경이 시작됩니다.

## <a name="next-steps"></a>다음 단계
- [설정 환경 완료](./quickstart-percept-dk-set-up.md)