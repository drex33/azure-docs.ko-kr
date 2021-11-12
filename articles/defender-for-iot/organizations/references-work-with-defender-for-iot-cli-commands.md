---
title: Defender for IoT CLI 명령 작업
description: 이 문서에서는 센서 및 온-프레미스 관리 콘솔을 위한 Defender for IoT CLI 명령을 설명합니다.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 4561f0e9697826fff808d03e3bf38f0d546d7c8a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347183"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Defender for IoT CLI 명령 작업

이 문서에서는 센서 및 온-프레미스 관리 콘솔을 위한 CLI 명령을 설명합니다. 명령에 액세스할 수 있는 사용자는 다음과 같습니다.

- 관리자
- CyberX 
- 지원

CLI에서 작업을 시작하려면 터미널을 사용하여 연결합니다. 예를 들어 터미널 이름 `Putty` 및 `Support` 사용자로 연결합니다. 

## <a name="create-local-alert-exclusion-rules"></a>로컬 경고 제외 규칙 만들기

CLI에 다음 명령을 입력하여 로컬 경고 제외 규칙을 만들 수 있습니다.

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

경고 제외 규칙과 함께 사용할 수 있는 특성은 다음과 같습니다.

| attribute | Description |
|--|--|
| [-h] | 명령에 대한 도움말 정보를 인쇄합니다. |
| -n NAME | 만들 규칙의 이름입니다. |
| [-ts TIMES] | 규칙이 활성 상태인 시간 범위입니다. 다음과 같이 지정해야 합니다.<br />`xx:yy-xx:yy`<br />기간 사이에 쉼표를 사용하면 둘 이상의 기간을 정의할 수 있습니다. 예: `xx:yy-xx:yy, xx:yy-xx:yy` |
| [-dir DIRECTION] | 규칙이 적용되는 방향입니다. 다음과 같이 지정해야 합니다.<br />`both | src | dst` |
| [-dev DEVICES] | 규칙에 의해 제외될 디바이스의 IP 주소 및 주소 유형으로, 다음과 같이 지정됩니다.<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a ALERTS] | 규칙이 제외할 경고의 이름입니다.<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>로컬 경고 제외 규칙 추가

CLI에서 다음 명령을 입력하여 로컬 경고 제외 규칙을 추가할 수 있습니다.

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

여기서 사용되는 특성은 로컬 경고 제외 규칙 만들기 섹션에 설명된 특성과 동일합니다. 여기서는 특성이 기존 규칙에 적용된다는 점이 다릅니다.

## <a name="show-local-alert-exclusion-rules"></a>로컬 경고 제외 규칙 표시

다음 명령을 입력하여 기존 제외 규칙 목록을 표시합니다.

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>로컬 경고 제외 규칙 삭제

다음 명령을 입력하여 기존 경고 제외 규칙을 삭제할 수 있습니다.

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

경고 제외 규칙과 함께 사용할 수 있는 특성은 다음과 같습니다.

| attribute | Description|
| --------- | ---------------------------------- |
| -n NAME | 삭제할 규칙의 이름입니다. |

## <a name="sync-time-from-the-ntp-server"></a>NTP 서버에서 시간 동기화

지정된 NTP 서버에서 시간 동기화를 사용하거나 사용하지 않도록 설정할 수 있습니다.

### <a name="enable-ntp-sync"></a>NTP 동기화 사용

다음 명령을 입력하여 지정된 NTP 서버에서 시간을 주기적으로 검색합니다.

```azurecli-interactive
ntp enable IP
```

명령 내에서 정의할 수 있는 특성은 NTP 서버의 IP 주소입니다.

### <a name="disable-ntp-sync"></a>NTP 동기화 사용 안 함

다음 명령을 입력하여 지정된 NTP 서버와의 시간 동기화를 사용하지 않도록 설정합니다.

```azurecli-interactive
ntp disable IP
```

명령 내에서 정의할 수 있는 특성은 NTP 서버의 IP 주소입니다.

## <a name="network-configuration"></a>네트워크 구성

다음 표에서는 IoT 용 Microsoft Defender에 대 한 네트워크 옵션을 구성 하는 데 사용할 수 있는 명령을 설명 합니다.

|이름|명령|Description|
|-----------|-------|-----------|
|Ping|`ping IP`| Defender for IoT 플랫폼 외부의 주소를 ping합니다.|
|Blink|`network blink`| 인터페이스 표시등이 깜박이게 하여 연결을 찾습니다. |
|네트워크 다시 구성 |`network edit-settings`| 네트워크 구성 매개 변수를 변경할 수 있습니다. |
|네트워크 설정 표시 |`network list`|네트워크 어댑터 매개 변수를 표시합니다. |
|네트워크 구성 유효성 검사 |`network validate` |출력 네트워크 설정을 표시합니다. <br /> <br />예를 들면 다음과 같습니다. <br /> <br />현재 네트워크 설정: <br /> interface: eth0 <br /> ip: 10.100.100.1 <br />subnet: 255.255.255.0 <br />default gateway: 10.100.100.254 <br />dns: 10.100.100.254 <br />monitor interfaces: eth1|
|인증서 가져오기 |`certificate import FILE` |HTTPS 인증서를 가져옵니다. \*.crt 파일로 이어지는 전체 경로를 지정해야 합니다. |
|날짜 표시 |`date` |호스트에서 GMT 형식으로 현재 날짜를 반환합니다. |

## <a name="network-capture-filter-configuration"></a>네트워크 캡처 필터 구성

관리자는 `network capture-filter` 명령을 사용하여 분석하지 않아도 되는 네트워크 트래픽을 제거할 수 있습니다. 포함 목록 또는 제외 목록을 사용하여 트래픽을 필터링할 수 있습니다.

```azurecli-interactive
network capture-filter
```

명령을 입력하면 다음 질문과 함께 메시지가 표시됩니다.

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

다음 구문에 따라 디바이스, 채널, 포트, 하위 집합을 추가할 수 있는 nano 파일을 열려면 `Y`를 선택합니다.

| attribute | Description |
|--|--|
| 1.1.1.1 | 이 디바이스의 모든 트래픽을 포함합니다. |
| 1.1.1.1,2.2.2.2 | 이 채널의 모든 트래픽을 포함합니다. |
| 1.1.1,2.2.2 | 이 서브넷의 모든 트래픽을 포함합니다. |

행을 삭제하여 인수를 구분합니다.

디바이스, 채널 또는 서브넷을 포함하는 경우 센서는 일반적으로는 처리되지 않는 포트와 트래픽을 포함하여 해당 인수에 유효한 모든 트래픽을 처리합니다.

그러고 나서 다음 질문이 표시됩니다.

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

다음 구문에 따라 디바이스, 채널, 포트, 하위 집합을 추가할 수 있는 nano 파일을 열려면 `Y`를 선택합니다.

| attribute | Description |
|--|--|
| 1.1.1.1 | 이 디바이스의 모든 트래픽을 제외합니다. |
| 1.1.1.1,2.2.2.2 | 이 채널의 모든 트래픽, 즉 두 디바이스 간의 모든 트래픽을 제외합니다. |
| 1.1.1.1,2.2.2.2,443 | 포트별로 이 채널의 모든 트래픽을 제외합니다. |
| 1.1.1 | 이 서브넷의 모든 트래픽을 제외합니다. |
| 1.1.1,2.2.2 | 서브넷 간의 모든 트래픽을 제외합니다. |

행을 삭제하여 인수를 구분합니다.

디바이스, 채널 또는 서브넷을 제외하면 센서는 해당 인수에 유효한 모든 트래픽을 제외합니다.

### <a name="ports"></a>포트

모든 트래픽에 대해 UDP 및 TCP 포트를 포함하거나 제외합니다.

>`502`: 단일 포트

>`502,443`: 두 포트

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>구성 요소

다음 질문이 표시됩니다.

>`In which component do you wish to apply this capture filter?`

옵션은  `all`, `dissector`, `collector`, `statistics-collector`, `rpc-parser` 또는 `smb-parser`입니다.

대부분의 사용 사례에서는 `all`을 선택합니다.

### <a name="custom-base-capture-filter"></a>사용자 지정 기본 캡처 필터

기본 캡처 필터는 구성 요소의 기준입니다. 예를 들어 이 필터는 구성 요소에 사용할 수 있는 포트를 결정합니다.

다음 모든 옵션에서 `Y`를 선택합니다. 모든 필터는 변경이 설정된 후 기준에 추가됩니다. 변경을 수행하면 변경 내용이 기존 기준을 덮어씁니다.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

1\.1.1과 같은 서브넷을 제외하도록 선택하는 경우:

- `internal`은 해당 서브넷만 제외합니다.

- `all-connected`는 해당 서브넷과 해당 서브넷에서 들어오고 나가는 모든 트래픽을 제외합니다.

`internal`을 사용하는 것이 좋습니다.

> [!NOTE]
> 선택 항목은 도구의 모든 필터에 사용되며, 세션에 종속되지 않습니다. 즉, 일부 필터에 `internal`을 선택하고 다른 필터에  `all-connected`를 선택할 수는 없습니다.

### <a name="comments"></a>주석

 ```/var/cyberx/properties/cybershark.properties```에서 필터를 볼 수 있습니다.

- **statistics-collector**:  ```/var/cyberx/properties/net.stats.collector.properties```의  `bpf_filter property`

- **dissector**:  ```/var/cyberx/properties/cybershark.properties```의 `override.capture_filter`  속성

- **rpc-parser**:  ```/var/cyberx/properties/rpc-parser.properties```의 `override.capture_filter` 속성

- **smb-parser**:  ```/var/cyberx/properties/smb-parser.properties```의  `override.capture_filter`  속성

- **collector**:  ```/var/cyberx/properties/collector.properties```의 `general.bpf_filter`  속성

cyberx 사용자의 경우 다음 코드를 입력하여 기본 구성을 복원할 수 있습니다.

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>클라이언트 및 서버 호스트 정의

Defender for IoT가 클라이언트 및 서버 호스트를 자동으로 검색하지 않은 경우 다음 명령을 입력하여 클라이언트 및 서버 호스트를 설정합니다.

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

`directions` 명령에 다음 특성을 사용할 수 있습니다.

| attribute | Description |
|--|--|
| [-h] | 명령에 대한 도움말 정보를 인쇄합니다. |
| [--identifier IDENTIFIER] | 서버 식별자입니다. |
| [--port PORT] | 서버 포트입니다. |
| [--remove] | 목록에서 클라이언트 또는 서버 호스트를 제거합니다. |
| [--add] | 목록에 클라이언트 또는 서버 호스트를 추가합니다. |
| [--tcp] | 이 호스트와 통신할 때 TCP를 사용합니다. |
| [--udp] | 이 호스트와 통신할 때 UDP를 사용합니다. |

## <a name="system-actions"></a>시스템 작업
다음 표에서는 Defender for IoT 내에서 다양한 시스템 작업을 수행하는 데 사용할 수 있는 명령을 설명합니다.

|이름|코드|Description|
|----|----|-----------|
|날짜 표시|`date`|호스트에서 GMT 형식으로 현재 날짜를 반환합니다.|
|호스트 재부팅|`system reboot`|호스트 디바이스를 재부팅합니다.|
|호스트 종료|`system shutdown`|호스트를 종료합니다.|
|시스템 백업|`system backup`|즉시 백업(예약되지 않은 백업)을 시작합니다.|
|백업에서 시스템 복원|`system restore`|가장 최근의 백업에서 복원합니다.|
|백업 파일 나열|`system backup-list`|사용 가능한 백업 파일을 나열합니다.|
|모든 Defender for IoT 플랫폼 서비스 상태 표시|`system sanity`|모든 Defender for IoT 플랫폼 서비스의 현재 상태를 나열하여 시스템의 성능을 확인합니다.|
|소프트웨어 버전 표시|`system version`|시스템에서 현재 실행 중인 소프트웨어의 버전을 표시합니다.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>어플라이언스에 SSL 및 TLS 인증서 배포

다음 명령을 입력하여 SSL 및 TLS 엔터프라이즈 인증서를 CLI로 가져옵니다.

```azurecli-interactive
cyberx-xsense-certificate-import
```
이 도구를 사용하려면 인증서 파일을 디바이스에 업로드해야 합니다. WinSCP 또는 Wget과 같은 도구를 통해 이 작업을 수행할 수 있습니다. 

이 명령은 다음 입력 플래그를 지원합니다.

| 플래그 | 설명 |
|--|--|
| -H | 명령줄 도움말 구문을 표시합니다. |
| --crt | 인증서 파일(.crt 확장명)의 경로입니다. |
| --key | \*.key 파일입니다. 키 길이는 2,048비트 이상이어야 합니다. |
| --chain | 인증서 체인 파일의 경로입니다(선택 사항). |
| --pass | 인증서를 암호화하는 데 사용되는 암호입니다(선택 사항). |
| --passphrase-set | 기본값은 **False**, **unused** 입니다. <br />이전 인증서와 함께 제공된 이전 암호를 사용하려면 **True** 로 설정합니다(선택 사항). | 

도구를 사용하는 경우:

- 인증서 파일을 어플라이언스에서 읽을 수 있는지 확인합니다. 

- DNS 서버와 해당 IP 주소를 사용하여 IT 팀과 함께 어플라이언스 도메인(인증서에 표시된 대로)을 확인합니다. 
    
## <a name="see-also"></a>참고 항목

[Defender for IoT 센서 및 관리 콘솔 API](references-work-with-defender-for-iot-apis.md)
