---
title: 온-프레미스 관리 콘솔 활성화 및 설정
description: 관리 콘솔을 활성화하면 센서가 Azure에 등록되고, 정보를 온-프레미스 관리 콘솔에 보내고, 온-프레미스 관리 콘솔에서 연결된 센서에 대한 관리 작업을 수행합니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 77f2a62cce7da4f9faac62820a85f38c0dd06ce5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306228"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>온-프레미스 관리 콘솔 활성화 및 설정 

온-프레미스 관리 콘솔을 활성화하고 설정하면 다음이 수행됩니다.

- 연결된 센서를 통해 모니터링하는 네트워크 디바이스가 Azure 계정에 등록됩니다.

- 센서에서 정보를 온-프레미스 관리 콘솔에 보냅니다.

- 온-프레미스 관리 콘솔에서 연결된 센서에 대한 관리 작업을 수행합니다.

- SSL 인증서를 설치했습니다.

## <a name="sign-in-for-the-first-time"></a>처음으로 로그인

**관리 콘솔에 로그인하려면,**

1. 시스템 설치 중에 온-프레미스 관리 콘솔에 대해 받은 IP 주소로 이동합니다.
 
1. 시스템 설치 중에 온-프레미스 관리 콘솔에 대해 받은 사용자 이름과 암호를 입력합니다. 


암호를 잊은 경우 **암호 복구** 옵션을 선택합니다. 암호를 복구하는 방법에 대한 지침은 [암호 복구](how-to-manage-the-on-premises-management-console.md#password-recovery)를 참조하세요.

## <a name="activate-the-on-premises-management-console"></a>온-프레미스 관리 콘솔 활성화

처음으로 로그인한 후에는 활성화 파일을 가져오고 업로드하여 온-프레미스 관리 콘솔을 활성화해야 합니다. 

**온-프레미스 관리 콘솔을 활성화하려면,**

1. 온-프레미스 관리 콘솔에 로그인합니다.

1. 화면 위쪽의 경고 알림에서 **작업 수행** 링크를 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/take-action.png" alt-text="화면 위쪽의 경고에서 작업 수행 링크 선택":::

1. [활성화] 팝업 화면에서 **Azure Portal** 링크를 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/azure-portal.png" alt-text="팝업 메시지에서 Azure Portal 링크 선택":::
 
1. 온-프레미스 관리 콘솔을 연결할 구독을 선택한 다음, **온-프레미스 관리 콘솔 활성화 파일 다운로드** 단추를 선택합니다. 활성화 파일이 다운로드됩니다.

   온-프레미스 관리 콘솔은 하나 이상의 구독에 연결할 수 있습니다. 활성화 파일은 선택한 모든 구독과, 다운로드 시점에 커밋된 디바이스 수에 연결됩니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/multiple-subscriptions.png" alt-text="온-프레미스 관리 콘솔을 온보딩할 여러 구독을 선택할 수 있습니다.":::

   구독을 아직 온보딩하지 않은 경우 [구독을 온보딩](how-to-manage-subscriptions.md#onboard-a-subscription)합니다.

   > [!Note]
   > 구독을 삭제할 경우 삭제된 구독과 관련된 모든 온-프레미스 관리 콘솔에 새 활성화 파일을 업로드해야 합니다.

1. **활성화** 팝업 화면으로 다시 이동하고, **파일 선택** 을 선택합니다.

1. 다운로드한 파일을 선택합니다.

초기 활성화 후 모니터링되는 디바이스 수가 온보딩 중에 정의된 커밋된 디바이스 수를 초과할 수 있습니다. 이 문제는 더 많은 센서를 관리 콘솔에 연결하는 경우에 발생합니다. 모니터링되는 디바이스 수와 커밋된 디바이스 수가 일치하지 않으면 관리 콘솔에 경고가 표시됩니다. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/device-commitment-update.png" alt-text="디바이스 커밋 경고가 표시되면 새 활성화 파일을 업로드해야 합니다.":::

이 경고가 표시되면 [새 활성화 파일](#activate-the-on-premises-management-console)을 업로드해야 합니다.

### <a name="activate-an-expired-license-versions-under-100"></a>만료된 라이선스(10.0 미만 버전) 활성화

10.0 이전 버전 사용자인 경우 라이선스가 만료될 수 있고 다음 경고가 표시됩니다. 

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/activation-popup.png" alt-text="라이선스가 만료되면 활성화 파일을 통해 라이선스를 업데이트해야 합니다.":::

**라이선스를 활성화하려면 다음을 수행합니다.**

1. [고객 지원팀](https://portal.azure.com/?passwordRecovery=true&Microsoft_Azure_IoT_Defender=canary#create/Microsoft.Support)에서 사례를 엽니다.

1. 지원은 활성화 ID 번호를 통해 제공됩니다.

1. 고객 지원팀에서 문자열 양식의 새 라이선스 정보를 제공합니다.

1. 사용 약관을 읽고 승인할 확인란을 선택합니다.

1. 제공된 공간에 문자열을 붙여넣습니다.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/add-license.png" alt-text="제공된 필드에 문자열을 붙여넣습니다.":::

1. **활성화** 를 선택합니다.

## <a name="set-up-a-certificate"></a>인증서 설정

관리 콘솔을 설치하면 자체 서명된 로컬 인증서가 생성됩니다. 이 인증서는 콘솔에 액세스하는 데 사용됩니다. 관리자가 처음으로 관리 콘솔에 로그인하면 해당 사용자에게 SSL/TLS 인증서를 온보딩하라는 메시지가 표시됩니다. 

다음 두 가지 수준의 보안을 사용할 수 있습니다.

- CA 서명된 인증서를 업로드하여 조직에서 요청하는 특정 인증서 및 암호화 요구 사항을 충족합니다.

- 관리 콘솔과 연결된 센서 간의 유효성 검사를 허용합니다. 유효성 검사는 인증서 해지 목록 및 인증서 만료 날짜에 대해 평가됩니다. *유효성 검사가 실패하면 관리 콘솔과 센서 간의 통신이 중지되고 콘솔에 유효성 검사 오류가 표시됩니다.* 이 옵션은 설치 후 기본적으로 사용하도록 설정됩니다.  

콘솔에서 지원하는 인증서의 유형은 다음과 같습니다.

- 프라이빗 및 엔터프라이즈 키 인프라(프라이빗 PKI)

- 공개 키 인프라(공개 PKI)

- 어플라이언스에서 로컬로 생성됨(로컬로 자체 서명됨) 

  > [!IMPORTANT]
  > 자체 서명된 인증서를 사용하지 않는 것이 좋습니다. 이 인증서는 안전하지 않으므로 테스트 환경에만 사용해야 합니다. 인증서 소유자의 유효성을 검사할 수 없으며 시스템 보안을 유지할 수 없습니다. 프로덕션 네트워크에는 이 옵션을 사용하지 마세요.

**인증서를 업로드하려면,**

1. 로그인 후 메시지가 표시되면 인증서 이름을 정의합니다.

1. CRT 및 키 파일을 업로드합니다.

1. 암호를 입력하고, 필요한 경우 PEM 파일을 업로드합니다.

CA 서명된 인증서가 업로드되면 화면을 새로 고쳐야 할 수도 있습니다.

**관리 콘솔과 연결된 센서 간의 유효성 검사를 사용하지 않도록 설정하려면,**

1. **다음** 을 선택합니다.

1. **시스템 수준 유효성 검사 사용** 토글을 해제합니다.

새 인증서, 지원되는 인증서 파일 및 관련 항목을 업로드하는 방법에 대한 자세한 내용은 [온-프레미스 관리 콘솔 관리](how-to-manage-the-on-premises-management-console.md)를 참조하세요.

## <a name="connect-sensors-to-the-on-premises-management-console"></a>센서를 온-프레미스 관리 콘솔에 연결

센서에서 정보를 온-프레미스 관리 콘솔에 보내는지와 온-프레미스 관리 콘솔에서 백업을 수행하고, 경고를 관리하고, 센서에 대한 다른 작업을 수행할 수 있는지 확인합니다. 이렇게 하려면 다음 절차에 따라 센서와 온-프레미스 관리 콘솔 간에 초기에 연결했는지 확인합니다.

IoT용 Microsoft Defender 센서를 온-프레미스 관리 콘솔에 연결하는 두 가지 옵션을 사용할 수 있습니다.

- 센서 콘솔에서 연결

- 터널링을 사용하여 연결

연결되면 이러한 센서를 사용하여 사이트를 설정해야 합니다.

### <a name="connect-sensors-to-the-on-premises-management-console-from-the-sensor-console"></a>센서 콘솔에서 센서를 온-프레미스 관리 콘솔에 연결

**센서 콘솔에서 센서를 온-프레미스 관리 콘솔에 연결하려면,**

1. 온-프레미스 관리 콘솔에서 **시스템 설정** 을 선택합니다.

1. **연결 문자열 복사** 를 수행합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-string.png" alt-text="센서에 대한 연결 문자열 복사":::

1. 센서에서 **시스템 설정** 으로 이동하고, **관리 콘솔에 연결** :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-to-management-console.png" border="false":::을 선택합니다.

1. 온-프레미스 관리 콘솔에서 복사한 연결 문자열을 **연결 문자열** 필드에 붙여넣습니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/paste-connection-string.png" alt-text="복사한 연결 문자열을 연결 문자열 필드에 붙여넣기":::

1. **연결** 을 선택합니다.

### <a name="connect-sensors-by-using-tunneling"></a>터널링을 사용하여 센서 연결

조직 센서와 온-프레미스 관리 콘솔 간의 보안 터널링 연결을 사용하도록 설정합니다. 이 설정은 조직 방화벽과의 상호 작용을 우회하여 공격 노출 영역을 줄입니다.

터널링을 사용하면 IP 주소 및 단일 포트(즉, 9000)에서 센서로 온-프레미스 관리 콘솔에 연결할 수 있습니다.

**온-프레미스 관리 콘솔에서 터널링을 설정하려면,**

- 온-프레미스 관리 콘솔에 로그인하고, 다음 명령을 실행합니다.

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

**센서에서 터널링을 설정하려면,**

1. 센서(network.properties)에서 9000 TCP 포트를 수동으로 엽니다. 포트가 열려 있지 않으면 센서에서 온-프레미스 관리 콘솔의 연결을 거부합니다.

2. 각 센서에 로그인하고, 다음 명령을 실행합니다.

   ```bash
   sudo cyberx-xsense-management-connect -ip <on-premises management console IP Address> -token < Copy the string that appears after the IP colon (:) from the Connection String field, Management Console Connection dialog box>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>사이트 설정

기본 기업 맵은 여러 수준의 지리적 위치에 따라 디바이스에 대한 전체 보기를 제공합니다.

조직 구조와 사용자 권한이 복잡한 경우 디바이스 보기가 필요할 수 있습니다. 이러한 경우 사이트 설정은 표준 사이트 또는 영역 구조 외에도 글로벌 조직 구조에 따라 결정될 수 있습니다.

이 환경을 지원하려면 조직의 사업부, 지역, 사이트 및 영역을 기반으로 하는 글로벌 비즈니스 토폴로지를 만들어야 합니다. 또한 액세스 그룹을 사용하여 이러한 엔터티에 대한 사용자 액세스 권한을 정의해야 합니다.

액세스 그룹을 사용하면 Defender for IoT 플랫폼에서 디바이스를 관리하고 분석하는 위치를 더 효율적으로 제어할 수 있습니다.

### <a name="how-it-works"></a>작동 방식

조직의 각 사이트에 대한 사업부 및 지역을 정의할 수 있습니다. 그런 다음, 네트워크에 있는 논리적 엔터티인 영역을 추가할 수 있습니다. 

영역당 하나 이상의 센서를 할당합니다. 5수준 모델은 조직의 구조를 반영하는 보호 시스템을 제공하는 데 필요한 유연성과 세분성을 제공합니다.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="센서 및 지역 관계를 보여 주는 다이어그램":::

사이트는 [기업 보기]를 사용하여 직접 편집할 수 있습니다. [기업 보기]에서 사이트를 선택하면 각 영역 옆에 열린 경고 수가 표시됩니다.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="베를린 데이터 오버레이가 있는 온-프레미스 관리 콘솔 맵의 스크린샷":::

**사이트를 설정하려면,**

1. 조직의 논리 구조를 반영하여 새 사업부를 추가합니다.

   1. [기업 보기]에서 **모든 사이트** > **사업부 관리** 를 차례로 선택합니다.

      :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-business-unit.png" alt-text="기업 보기 화면의 모든 사이트 드롭다운 메뉴에서 사업부 관리 선택":::

   1. 새 사업부 이름을 입력하고, **추가** 를 선택합니다.

1. 조직의 지역을 반영하는 새 지역을 추가합니다.

   1. [기업 보기]에서 **모든 지역** > **지역 관리** 를 차례로 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-regions.png" alt-text="기업 내 지역을 관리하기 위해 모든 지역, 지역 관리를 차례로 선택":::

   1. 새 지역 이름을 입력하고, **추가** 를 선택합니다.

1. 사이트를 추가합니다.

   1. [기업 보기]의 위쪽 표시줄에서 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false":::를 선택합니다. 커서가 더하기 기호( **+** )로 표시됩니다.

   1. **+** 를 새 사이트의 위치에 놓고, 이를 선택합니다. **새 사이트 만들기** 대화 상자가 열립니다.

      :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="새 사이트 만들기 보기의 스크린샷":::

   1. 새 사이트의 이름과 실제 주소를 정의하고, **저장** 을 선택합니다. 사이트 맵에 새 사이트가 표시됩니다.

4. [영역을 사이트에 추가](#create-enterprise-zones)합니다.

5. [센서를 연결](how-to-manage-individual-sensors.md#connect-a-sensor-to-the-management-console)합니다.

6. [센서를 사이트 영역에 할당](#assign-sensors-to-zones)합니다.

### <a name="delete-a-site"></a>사이트 삭제

사이트가 더 이상 필요하지 않은 경우 온-프레미스 관리 콘솔에서 사이트를 삭제할 수 있습니다.

**사이트를 삭제하려면,**

1. **사이트 관리** 창에서 사이트 이름이 포함된 막대에서 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false":::를 선택한 다음, **사이트 삭제** 를 선택합니다. 사이트를 삭제할 것인지 확인하는 확인 상자가 표시됩니다.

2. 확인 상자에서 **확인** 을 선택합니다.

## <a name="create-enterprise-zones"></a>기업 영역 만들기

영역은 다양한 특성에 따라 사이트 내의 디바이스를 그룹으로 나눌 수 있도록 하는 논리적 엔터티입니다. 예를 들어 생산 라인, 변전소, 사이트 영역 또는 디바이스 유형에 대한 그룹을 만들 수 있습니다. 영역은 조직에 적합한 특성을 기반으로 하여 정의할 수 있습니다.

사이트 구성 프로세스의 일부로 영역을 구성합니다.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="사이트 관리 영역 보기의 스크린샷":::

다음 표에서는 **사이트 관리** 창의 매개 변수에 대해 설명합니다.

| 매개 변수 | 설명 |
|--|--|
| 이름 | 센서의 이름입니다. 이 이름은 센서에서만 변경할 수 있습니다. 자세한 내용은 Defender for IoT 사용자 가이드를 참조하세요. |
| IP | 센서 IP 주소입니다. |
| 버전 | 센서 버전입니다. |
| 연결 | 센서 연결 상태입니다. 상태는 **연결됨** 또는 **연결 끊김** 일 수 있습니다. |
| 마지막 업그레이드 | 마지막 업그레이드 날짜입니다. |
| 업그레이드 진행률 | 진행률 표시줄에는 다음과 같은 업그레이드 프로세스의 상태가 표시됩니다.<br />- 패키지 업로드 중<br />- 설치 준비 중<br />- 프로세스 중지 중<br />- 데이터 백업 중<br />- 스냅샷 만드는 중<br />- 구성 업데이트 중<br />- 종속성 업데이트 중<br />- 라이브러리 업데이트 중<br />- 데이터베이스 패치 중<br />- 프로세스 시작 중<br />- 시스템 온전성 유효성 검사 중<br />- 유효성 검사 성공<br />- 성공<br />- 실패<br />- 업그레이드 시작됨<br />- 설치 시작 중ogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >업그레이드에 대한 자세한 내용은 [Microsoft 지원](https://support.microsoft.com/) 도움말을 참조하세요. |
| 디바이스 | 센서에서 모니터링하는 OT 디바이스의 수입니다. |
| 경고 | 센서에 대한 경고의 수입니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | 센서를 영역에 할당할 수 있습니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| 사이트에서 연결이 끊긴 센서를 삭제할 수 있습니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | 현재 영역에 연결되어 있는 센서의 수를 나타냅니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | 현재 영역에 연결되어 있는 OT 자산의 수를 나타냅니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | 영역에 할당된 센서에서 보낸 경고의 수를 나타냅니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | 영역에서 센서의 할당을 취소합니다. |

**영역을 사이트에 추가하려면,**

1. **사이트 관리** 창에서 사이트 이름이 포함된 막대에서 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false":::를 다음, **영역 추가** 를 선택합니다. **새 영역 만들기** 대화 상자가 표시됩니다.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="새 영역 만들기 보기의 스크린샷":::

1. 영역 이름을 입력합니다.

1. 사이트를 영역으로 나누는 데 사용한 특성을 명확하게 설명하는 새 영역에 대한 설명을 입력합니다.

1. **SAVE**(저장)를 선택합니다. 새 영역이 속한 사이트 아래의 **사이트 관리** 창에 해당 영역이 표시됩니다.

**영역을 편집하려면,**

1. **사이트 관리** 창에서 영역 이름이 포함된 막대에서 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false":::를 선택한 다음, **영역 편집** 을 선택합니다. **영역 편집** 대화 상자가 표시됩니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="영역 편집 대화 상자를 보여 주는 스크린샷":::

1. 영역 매개 변수를 편집하고, **저장** 을 선택합니다.

**영역을 삭제하려면:**

1. **사이트 관리** 창에서 영역 이름이 포함된 막대에서 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false":::를 선택한 다음, **영역 삭제** 를 선택합니다.

1. 확인 상자에서 **예** 를 선택합니다.

**연결 상태에 따라 필터링하려면,**

- 왼쪽 위 모서리에서 **연결** 옆에 있는 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false":::를 선택하고, 다음 옵션 중 하나를 선택합니다.

  - **모두**: 이 온-프레미스 관리 콘솔에 보고하는 모든 센서를 표시합니다.

  - **연결됨**: 연결된 센서만 표시합니다.

  - **연결 끊김**: 연결이 끊긴 센서만 표시합니다.

**업그레이드 상태에 따라 필터링하려면,**

- 왼쪽 위 모서리에서 **업그레이드 상태** 옆에 있는 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false":::를 선택하고, 다음 옵션 중 하나를 선택합니다.

  - **모두**: 이 온-프레미스 관리 콘솔에 보고하는 모든 센서를 표시합니다.

  - **유효함**: 유효함 업그레이드 상태의 센서를 표시 합니다.

  - **진행 중**: 업그레이드 중인 센서를 표시합니다.

  - **실패**: 업그레이드 프로세스가 실패한 센서를 표시합니다.

## <a name="assign-sensors-to-zones"></a>영역에 센서 할당

각 영역에 대해 로컬 트래픽 분석 및 경고를 수행하는 센서를 할당해야 합니다. 온-프레미스 관리 콘솔에 연결된 센서만 할당할 수 있습니다.

**센서를 할당하려면,**

1. **사이트 관리** 를 선택합니다. 할당되지 않은 센서는 대화 상자의 왼쪽 위 모서리에 표시됩니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="할당되지 않은 센서 보기의 스크린샷":::

1. **연결** 상태가 연결됨인지 확인합니다. 그렇지 않은 경우 연결에 대한 자세한 내용은 [센서를 온-프레미스 관리 콘솔에 연결](#connect-sensors-to-the-on-premises-management-console)을 참조하세요. 

1. 할당하려는 센서에 대해 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false":::을 선택합니다.

1. **센서 할당** 대화 상자에서 할당할 사업부, 지역, 사이트 및 영역을 선택합니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="센서 할당 보기의 스크린샷":::

1. **할당** 을 선택합니다.

**센서를 할당 취소하고 삭제하려면,**

1. 온-프레미스 관리 콘솔에서 센서의 연결을 끊습니다. 자세한 내용은 [센서를 온-프레미스 관리 콘솔에 연결](#connect-sensors-to-the-on-premises-management-console)을 참조하세요.

1. **사이트 관리** 창에서 센서를 선택하고, :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false":::을 선택합니다. 몇 분 후에 할당되지 않은 센서 목록에 센서가 표시됩니다.

1. 사이트에서 할당되지 않은 센서를 삭제하려면 할당되지 않은 센서 목록에서 센서를 선택하고, :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::를 선택합니다.

## <a name="see-also"></a>참고 항목

[센서 및 온-프레미스 관리 콘솔 문제 해결](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
