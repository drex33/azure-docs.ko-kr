---
title: 프록시를 사용하여 센서 연결
description: 직접 인터넷에 액세스할 수 없는 프록시를 통해 센서와 통신 하도록 IoT 용 Microsoft Defender를 구성 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 0a6e2ef526985ca5e8bee208989310ed01f18267
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278800"
---
# <a name="connect-microsoft-defender-for-iot-sensors-without-direct-internet-access-by-using-a-proxy"></a>커넥트 프록시를 사용 하 여 인터넷에 직접 액세스 하지 않는 IoT 용 Microsoft Defender 센서 

이 문서에서는 직접 인터넷에 액세스할 수 없는 프록시를 통해 센서와 통신 하도록 IoT 용 Microsoft Defender를 구성 하는 방법을 설명 합니다. HTTP 터널링이 있고 연결에 HTTP CONNECT 명령을 사용하는 전달 프록시와 센서를 연결합니다. 여기에 제공된 지침에서는 오픈 소스 Squid 프록시를 사용하며 CONNECT를 지원하는 다른 프록시가 사용될 수 있습니다. 

프록시는 암호화된 SSL 터널을 사용하여 센서에서 서비스로 데이터를 전송합니다. 프록시는 데이터를 검사, 분석 또는 캐시하지 않습니다. 

다음 다이어그램에서는 IT 네트워크에 있는 프록시를 통해 클라우드로 온-클라우드의 IoT 센서에서 클라우드로의 Microsoft Defender로 이동 하는 데이터와 산업용 DMZ를 보여 줍니다.

:::image type="content" source="media/how-to-connect-sensor-by-proxy/cloud-access.png" alt-text="클라우드를 통해 센서를 프록시에 연결":::

## <a name="set-up-your-system"></a>시스템 설정

이 시나리오에서는 Ubuntu 18 서버에서 최신 버전의 [Squid](http://www.squid-cache.org/)를 설치하고 구성합니다.

> [!Note]
> IoT 용 Microsoft Defender는 Squid 또는 다른 프록시 서비스에 대 한 지원을 제공 하지 않습니다.

**Ubuntu 18 서버에 Squid 프록시를 설치하려면**:

1. 지정된 프록시 Ubuntu 머신에 로그인합니다.

1. 터미널 창을 시작합니다.
 
1. 다음 명령을 사용하여 소프트웨어를 최신 버전으로 업데이트합니다.

    ```bash
    sudo apt-get update 
    ```

1. 다음 명령을 사용하여 Squid 패키지를 설치합니다.

    ```bash
    sudo apt-get install squid 
    ```

1. `/etc/squid/squid.conf` 및 `/etc/squid/conf.d/`에 있는 Squid 구성 파일을 찾습니다.

1. 다음 명령을 사용하여 원본 파일의 백업을 만듭니다.

    ```bash
    sudo cp -v /etc/squid/squid.conf{,.factory}'/etc/squid/squid.conf' -> '/etc/squid/squid.conf.factory sudo nano /etc/squid/squid.conf
    ```

1. 텍스트 편집기에서 `/etc/squid/squid.conf` 파일을 엽니다.

1. `# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS`를 검색합니다.

1. `acl sensor1 src <sensor-ip>` 및 `http_access allow sensor1`을 파일에 추가합니다.

    :::image type="content" source="media/how-to-connect-sensor-by-proxy/add-lines.png" alt-text="텍스트에 다음 두 줄을 추가하고 파일을 저장":::

1. (선택 사항) 각 센서에 해당하는 추가 줄을 추가하여 더 많은 센서를 추가합니다.

1. 다음 명령을 사용하여 시작 시 Squid 서비스를 시작할 수 있습니다.

    ```bash
    sudo systemctl enable squid 
    ```

## <a name="set-up-a-sensor-to-use-squid"></a>Squid를 사용하도록 센서 설정

**Squid를 사용하도록 센서를 설정하려면**:

1. 센서에 로그인합니다.

1. **시스템 설정** > **네트워크** 로 이동합니다.

1. **프록시 사용** 을 선택합니다.

    :::image type="content" source="media/how-to-connect-sensor-by-proxy/enable-proxy.png" alt-text="센서 네트워크 구성 창에서 프록시 사용 선택":::

1. 프록시 주소를 입력합니다.

1. 포트를 입력합니다. 기본 포트는 `3128`입니다.

1. (선택 사항) 프록시 사용자와 암호를 입력합니다.

1. **저장** 을 선택합니다.

## <a name="see-also"></a>참고 항목

[구독 관리](how-to-manage-subscriptions.md).
