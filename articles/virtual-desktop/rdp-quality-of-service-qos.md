---
title: Azure 가상 데스크톱에 대 한 QoS (서비스 품질) 구현
titleSuffix: Azure
description: Azure 가상 데스크톱에 대 한 QoS를 설정 하는 방법입니다.
author: gundarev
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: denisgun
ms.openlocfilehash: d82f885a2a1b527be292137049453efbe888f15e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181429"
---
# <a name="implement-quality-of-service-qos-for-azure-virtual-desktop"></a>Azure 가상 데스크톱에 대 한 QoS (서비스 품질) 구현

[관리 되는 네트워크에 대 한 RDP Shortpath](./shortpath.md) 는 원격 데스크톱 클라이언트와 세션 호스트 간에 직접 UDP 기반 전송을 제공 합니다. 관리 되는 네트워크에 대 한 RDP Shortpath는 RDP 데이터에 대 한 QoS (서비스 품질) 정책을 구성할 수 있습니다.
Azure Virtual Desktop의 QoS를 사용하면 네트워크 지연에 민감한 실시간 RDP 트래픽을 덜 중요한 트래픽 앞으로 "새치기"할 수 있습니다. 이러한 덜 중요한 트래픽의 예는 새 앱을 다운로드하는 경우가 될 수 있습니다. 이 경우 다운로드하는 데 드는 추가적인 1초는 그리 중요하지 않을 것입니다. QoS는 Windows 그룹 정책 개체를 사용하여 실시간 스트림의 모든 패킷을 식별한 후 표시하여, 네트워크에서 RDP 트래픽에 전용 대역폭 부분을 제공할 수 있도록 합니다.

이 문서에 설명된 문제가 발생하는 많은 사용자 그룹을 지원하는 경우 QoS를 구현해야 할 것입니다. 사용자가 많지 않은 중소기업은 QoS가 필요하지 않을 수 있지만, 이 경우에도 유용할 수 있습니다.

특정 형식의 QoS가 없으면 다음과 같은 문제가 나타날 수 있습니다.

* 지터 – RDP 패킷이 서로 다른 속도로 도착하여 시각적 및 오디오 결함을 초래할 수 있습니다.
* 패킷 손실 – 패킷이 삭제되어 재전송하는 데 추가 시간이 필요합니다.
* 지연된 RTT(왕복 시간) – 대상에 도달하는 데 오랜 시간이 걸리는 RDP 패킷 때문에 원격 애플리케이션의 입력 및 반응 간에 상당한 지연이 발생합니다.

이러한 문제를 해결하는 가장 덜 복잡한 방법은 내부적으로 및 인터넷으로의 데이터 연결 크기를 늘리는 것입니다. 이 방법은 비용이 많이 들지 않으므로 QoS는 대역폭을 보다 효율적으로 추가하는 대신, 리소스를 관리하는 방법을 제공합니다. 품질 문제를 해결하려면 먼저 QoS를 사용하고 필요한 경우에만 대역폭을 추가하는 것이 좋습니다.

QoS를 적용하려면 조직 전체에서 일관된 QoS 설정을 적용해야 합니다. QoS 우선 순위를 지원하지 않는 경로의 모든 부분은 고품질 RDP 세션을 저하시킬 수 있습니다.

## <a name="introduction-to-qos-queues"></a>QoS 큐 소개

QoS를 제공하려면 네트워크 디바이스에 트래픽을 분류하는 방법이 있어야 하고 RDP를 다른 네트워크 트래픽과 구별할 수 있어야 합니다.

네트워크 트래픽이 라우터로 들어가면 트래픽이 큐에 배치됩니다. QoS 정책이 구성되지 않은 경우에는 큐가 하나 뿐이며 모든 데이터는 동일한 우선 순위로 선입선출 방식으로 처리됩니다. 즉, RDP 트래픽이 추가 밀리초의 지연이 문제가 되지 않는 트래픽 뒤에서 중단될 수 있습니다.

QoS를 구현할 때 여러 정체 관리 기능(Cisco의 우선 순위 큐 및 [CBWFQ(Class-Based Weighted Fair Queueing)](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641)) 및 정체 방지 기능(예: [WRED(Weighted Random Early Detection))](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html) 중 하나를 사용하여 여러 큐를 정의합니다.

간단히 비유하자면 QoS가 데이터 네트워크에 가상 "카풀 도로"를 만드는 것과 같습니다. 따라서 일부 데이터 형식은 지연이 절대 발생하지 않거나 거의 발생하지 않습니다. 이러한 도로를 만든 후에는 조직의 사용자에 대한 비즈니스 수준 환경을 제공하면서 동시에 상대적 크기를 조정하고 연결 대역폭을 훨씬 더 효과적으로 관리할 수 있습니다.

## <a name="qos-implementation-checklist"></a>QoS 구현 검사 목록

높은 수준에서 다음을 수행하여 QoS를 구현합니다.

1. [네트워크가 준비되었는지 확인](#make-sure-your-network-is-ready)
2. [관리 되는 네트워크용 RDP Shortpath 사용 하도록 설정 되어 있는지 확인](./shortpath.md) -QoS 정책은 역방향 연결 전송에 대해 지원 되지 않습니다.
3. 세션 호스트에서 [DSCP 마커 삽입 구현](#insert-dscp-markers)

QoS 구현 준비를 수행할 때는 다음 지침에 유의하세요.

* 세션 호스트에 대한 최단 경로가 가장 좋습니다.
* 프록시 또는 패킷 검사 디바이스와 같은 장애 요인은 권장되지 않습니다.

## <a name="make-sure-your-network-is-ready"></a>네트워크가 준비되었는지 확인

QoS 구현을 고려하는 경우 대역폭 요구 사항 및 기타 [네트워크 요구 사항](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context)을 미리 결정했어야 합니다.
  
네트워크의 트래픽 정체는 미디어 품질에 심각한 영향을 줍니다. 대역폭이 부족하면 성능이 저하되고 사용자 환경이 나빠질 수 있습니다. Azure Virtual Desktop 채택 및 사용이 증가함에 따라 [Log Analytics](./diagnostics-log-analytics.md)를 사용하여 문제를 식별한 다음, QoS 및 선택적 대역폭 추가를 사용하여 조정하는 것이 좋습니다.

### <a name="vpn-considerations"></a>VPN 고려 사항

QoS는 클라이언트와 세션 호스트 간의 모든 링크에서 구현될 경우에만 예상대로 작동합니다. 내부 네트워크에 QoS를 사용하고 사용자가 원격 위치에서 로그인하는 경우 내부 관리형 네트워크 내에서만 우선 순위를 지정할 수 있습니다. 원격 위치에서 VPN(가상 사설망)을 구현하여 관리형 연결을 수신할 수 있지만 기본적으로 VPN은 패킷 오버헤드를 추가하고 실시간 트래픽에서 지연을 발생합니다.

대륙에 걸쳐 있는 관리형 링크를 포함하는 글로벌 조직에서는 이러한 링크에 대한 대역폭이 LAN에 비해 제한되므로 QoS를 사용하는 것이 좋습니다.

## <a name="insert-dscp-markers"></a>DSCP 마커 삽입

GPO(그룹 정책 개체)를 통해 QoS를 구현하여 세션 호스트에 트래픽을 특정 유형으로 식별하는 DSCP 마커를 IP 패킷 헤더에 삽입하도록 지시할 수 있습니다. 이러한 마커를 인식하고 트래픽을 우선 순위가 높은 별도의 큐에 배치하도록 라우터 및 기타 네트워크 디바이스를 구성할 수 있습니다.

DSCP 마커는 우편 배달부에게 배달이 얼마나 긴급한지, 특급 우편으로 분류하는 것이 얼마나 적절한지를 나타내는 우편 스탬프와 비교할 수 있습니다. RDP 스트림에 우선 순위를 부여하도록 네트워크를 구성한 후에는 손실된 패킷과 지연 패킷이 현저하게 감소됩니다.

모든 네트워크 디바이스가 동일한 분류, 마커 및 우선 순위를 사용하는 경우 지연, 삭제된 패킷 및 지터를 줄이거나 제거할 수 있습니다. RDP 관점에서 필수 구성 단계는 패킷 분류 및 표시입니다. 그러나 엔드투엔드 QoS를 성공적으로 수행하려면 기본 네트워크 구성에 맞춰 RDP를 신중히 구성해야 합니다.
DSCP 값은 구성된 네트워크에 패킷 또는 스트림에 지정할 우선 순위를 알려 줍니다.

**EF(긴급 전달)** DSCP 클래스에 매핑되는 DSCP 값 46를 사용하는 것이 좋습니다.

### <a name="implement-qos-on-session-host-using-group-policy"></a>그룹 정책를 사용하여 세션 호스트에서 QoS 구현

그룹 정책 내에서 정책 기반 QoS(서비스 품질)를 사용하여 미리 정의된 DSCP 값을 설정할 수 있습니다.

도메인에 가입된 세션 호스트에 대한 QoS 정책을 만들려면 먼저 그룹 정책 관리가 설치된 컴퓨터에 로그인합니다. 그룹 정책 관리(시작을 선택하고 관리 도구를 가리킨 다음 그룹 정책 관리 선택)를 연 다음 다음 단계를 완료합니다.

1. 그룹 정책 관리에서 새 정책을 만들어야 하는 컨테이너를 찾습니다. 예를 들어 모든 세션 호스트 컴퓨터가 이름이 **"세션 호스트"** 인 OU에 있으면 새 정책은 세션 호스트 OU에 만들어야 합니다.

2. 적절한 컨테이너를 마우스 오른쪽 단추로 클릭하고 **이 도메인에서 GPO를 만들어 여기에 연결** 을 선택합니다.

3. **새 GPO** 대화 상자의 **이름** 상자에 새 그룹 정책 개체의 이름을 입력한 다음 **확인** 을 선택합니다.

4. 새로 만든 정책을 마우스 오른쪽 단추로 클릭한 다음 **편집** 을 선택합니다.

5. 그룹 정책 관리 편집기에서 **컴퓨터 구성**, **Windows 설정** 을 차례로 확장하고 **정책 기반 QoS** 를 마우스 오른쪽 단추로 클릭한 다음 **새 정책 만들기** 를 선택합니다.

6. **정책 기반 QoS** 대화 상자의 열기 페이지에서 **이름** 상자에 새 정책의 이름을 입력합니다. **DSCP 값 지정** 을 선택하고 값을 **46** 으로 설정합니다. **아웃바운드 제한 시간 지정** 을 선택 취소한 상태로 두고 **다음** 을 선택합니다.

7. 다음 페이지에서 **이 실행 파일 이름이 있는 애플리케이션만** 을 선택하고 이름 **svchost.exe** 를 입력한 후 **다음** 을 선택합니다. 이 설정은 정책에서 원격 데스크톱 서비스의 일치하는 트래픽의 우선 순위만 지정하도록 지시합니다.

8. 세 번째 페이지에서 **모든 원본 IP 주소** 와 **모든 대상 IP 주소** 가 둘 다 선택되어 있는지 확인하고 **다음** 을 선택합니다. 이러한 두 설정에서 패킷을 전송한 컴퓨터(IP 주소)와 패킷을 수신할 컴퓨터(IP 주소)에 관계없이 패킷이 관리되는지 확인합니다.

9. 4페이지의 **이 QoS 정책이 적용되는 프로토콜 선택** 드롭다운 목록에서 **UDP** 를 선택합니다.

10. **원본 포트 번호 지정** 제목 아래에서 **이 원본 포트 또는 범위** 를 선택합니다. 함께 제공되는 텍스트 상자에서 **3390** 을 입력합니다. **마침** 을 선택합니다.

사용자가 만든 새 정책은 세션 호스트 컴퓨터에서 그룹 정책이 새로 고쳐질 때까지 적용되지 않습니다. 그룹 정책은 정기적으로 자체적으로 새로 고침되지만 다음 단계를 수행하여 새로 고침을 즉시 강제로 수행할 수 있습니다.

1. 그룹 정책을 새로 고칠 각 세션 호스트에서 관리자로 명령 프롬프트를 엽니다(*관리자 권한으로 실행*).

1. 명령 프롬프트에 다음을 입력합니다.

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>PowerShell을 사용하여 세션 호스트에서 QoS 구현

아래 PowerShell cmdlet을 사용 하 여 관리 되는 네트워크용 RDP Shortpath 대 한 QoS를 설정할 수 있습니다.

```powershell
New-NetQosPolicy -Name "RDP Shortpath for managed networks" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>관련 문서

* [QoS(서비스 품질) 정책](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>다음 단계

* Azure Virtual Desktop의 대역폭 요구 사항에 대해 알아보려면 [Azure Virtual Desktop에 대한 RDP(원격 데스크톱 프로토콜) 대역폭 요구 사항 이해](rdp-bandwidth.md)를 참조하세요.
* Azure Virtual Desktop 네트워크 연결에 대한 자세한 내용은 [Azure Virtual Desktop 네트워크 연결 이해](network-connectivity.md)를 참조하세요.
