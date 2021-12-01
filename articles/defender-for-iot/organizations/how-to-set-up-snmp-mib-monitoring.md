---
title: SNMP MIB 모니터링 설정
description: SNMP를 사용하여 센서 상태 모니터링을 수행할 수 있습니다. 센서는 권한 있는 모니터링 서버에서 보낸 SNMP 쿼리에 응답합니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: fee36c0f2063bb8c68a9915a899f796bdbb2c66f
ms.sourcegitcommit: 8152290a8817d0882035f7f3f1fd56b80f87dcda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133312702"
---
# <a name="set-up-snmp-mib-monitoring"></a>SNMP MIB 모니터링 설정

SNMP(Simple Network Management Protocol)를 사용하여 센서 상태 모니터링을 수행할 수 있습니다. 센서는 권한 있는 모니터링 서버에서 보낸 SNMP 쿼리에 응답합니다. SNMP 모니터는 센서 OID를 주기적으로 폴링합니다(초당 최대 50회).

SNMP 지원 버전은 SNMP v2 또는 SNMP v3입니다. SNMP는 포트 161(SNMP)를 사용하여 UDP를 전송 프로토콜로 사용합니다.

SNMP 모니터링 구성을 시작하기 전에 방화벽에서 UDP 161 포트를 열어야 합니다.

## <a name="oids"></a>OID

| 관리 콘솔 및 센서 | OID | 형식 | Description |
|--|--|--|--|
| 어플라이언스 이름 | 1.3.6.1.2.1.1.5.0 | STRING | 온-프레미스 관리 콘솔의 어플라이언스 이름 |
| Vendor | 1.3.6.1.2.1.1.4.0 | STRING | Microsoft 지원(support.microsoft.com) |
| 플랫폼 | 1.3.6.1.2.1.1.1.0 | STRING | 센서 또는 온-프레미스 관리 콘솔 |
| 일련 번호 | 1.3.6.1.4.1.53313.1 |STRING | 라이선스에서 사용하는 문자열 |
| 소프트웨어 버전 | 1.3.6.1.4.1.53313.2 | STRING | Xsense 전체 버전 문자열 및 관리 전체 버전 문자열 |
| CPU 사용량 | 1.3.6.1.4.1.53313.3.1 | GAUGE32 | 0~100에 대한 표시 |
| CPU 온도 | 1.3.6.1.4.1.53313.3.2 | STRING | Linux 입력을 기반으로 0~100에 대한 섭씨 표시. 실제 물리적 온도 센서가 없는 모든 머신(예: VM)에서 "센서를 찾을 수 없음"이 반환됩니다.|
| 메모리 사용량 | 1.3.6.1.4.1.53313.3.3 | GAUGE32 | 0~100에 대한 표시 |
| 디스크 사용량 | 1.3.6.1.4.1.53313.3.4 | GAUGE32 | 0~100에 대한 표시 |
| 서비스 상태 | 1.3.6.1.4.1.53313.5  |STRING | 네 가지 중요한 구성 요소 중 하나가 다운된 경우 온라인 또는 오프라인 |
| 로컬/클라우드 연결 | 1.3.6.1.4.1.53313.6   |STRING | 이 어플라이언스의 활성화 모드: 클라우드 연결됨/로컬로 연결됨 |
| 라이선스 상태 | 1.3.6.1.4.1.53313.7  |STRING | 이 어플라이언스의 활성화 기간: 활성/만료 날짜/만료 |


   - 존재하지 않는 키는 null, HTTP 200으로 응답합니다.
    
   - 모든 아키텍처와 실제 센서에서 하드웨어 관련 MIB(CPU 사용량, CPU 온도, 메모리 사용량, 디스크 사용량)를 테스트해야 합니다. 가상 머신의 CPU 온도는 적용되지 않습니다.

연결 데이터 및 원시 데이터를 포함하여 센서가 수신하는 모든 SNMP 쿼리가 포함된 로그를 다운로드할 수 있습니다.

SNMP v2 상태 모니터링을 정의하려면 다음을 수행합니다.

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

2. **활성 검색** 창에서 **SNMP MIB 모니터링** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false":::을 선택합니다.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="SNMP 편집 창.":::

3. **허용된 호스트** 섹션에서 **호스트 추가** 를 선택하고 시스템 상태 모니터링을 수행하는 서버의 IP 주소를 입력합니다.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="허용된 호스트의 IP 주소 입력.":::

4. **인증** 섹션의 **SNMP v2 커뮤니티 문자열** 상자에 문자열을 입력합니다. SNMP 커뮤니티 문자열은 최대 32자의 영숫자(대문자, 소문자, 숫자) 조합을 사용할 수 있습니다. 공백은 허용되지 않습니다.

5. **저장** 을 선택합니다.

SNMP v3 상태 모니터링을 정의하려면 다음을 수행합니다.

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

2. **활성 검색** 창에서 **SNMP MIB 모니터링** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false":::을 선택합니다.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="SNMP 편집 창.":::

3. **허용된 호스트** 섹션에서 **호스트 추가** 를 선택하고 시스템 상태 모니터링을 수행하는 서버의 IP 주소를 입력합니다.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="허용된 호스트의 IP 주소 입력.":::

4. **인증** 섹션에서 다음 매개 변수를 설정합니다.

    | 매개 변수 | 설명 |
    |--|--|
    | **사용자 이름** | SNMP 사용자 이름은 최대 32자의 영숫자(대문자, 소문자, 숫자) 조합을 사용할 수 있습니다. 공백은 허용되지 않습니다. <br /> <br />SNMP v3 인증에 대한 사용자 이름은 시스템 및 SNMP 서버에서 구성해야 합니다. |
    | **암호** | 대/소문자 구분 인증 암호를 입력합니다. 인증 암호는 8~12자의 영숫자(대문자, 소문자, 숫자) 조합을 사용할 수 있습니다. <br /> <br/>SNMP v3 인증에 대한 사용자 이름은 시스템 및 SNMP 서버에서 구성해야 합니다. |
    | **인증 유형** | MD5 또는 SHA를 선택합니다. |
    | **암호화** | DES 또는 AES를 선택합니다. |
    | **비밀 키** | 키는 정확히 8자이어야 하며, 영숫자(대문자, 소문자, 숫자) 조합을 사용해야 합니다. |

5. **저장** 을 선택합니다.

## <a name="see-also"></a>참고 항목

[문제 해결 로그 내보내기](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
