---
title: MABS(Microsoft Azure Backup Server)에서 원격 분석 설정 관리
description: 이 문서에서는 MABS에서 원격 분석 설정을 관리하는 방법에 대한 정보를 제공합니다.
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: 353aec2b3bf03c7ce06d19db34a6b81f91d2ee12
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528117"
---
# <a name="manage-telemetry-settings"></a>원격 분석 설정 관리

>[!NOTE]
>이 기능은 MABS V3 UR2 이상에 적용됩니다.

이 문서에서는 MABS(Microsoft Azure Backup Server)에서 원격 분석(진단 및 유틸리티 데이터) 설정을 관리하는 방법에 대한 정보를 제공합니다.

기본적으로 MABS는 진단 및 연결 데이터를 Microsoft에 보냅니다. Microsoft는 이 데이터를 사용하여 Microsoft 제품 및 서비스의 품질, 보안 및 무결성을 제공하고 개선합니다.

관리자는 언제든지 이 기능을 끌 수 있습니다. 수집되는 데이터에 대한 자세한 내용은 [다음 섹션](#telemetry-data-collected)을 참조하세요.

## <a name="turn-onoff-telemetry-from-console"></a>콘솔에서 원격 분석 켜기/끄기

1. Microsoft Azure Backup Server 콘솔에서 **관리** 로 이동하고 상단 창에서 **옵션** 을 클릭합니다.
1. **옵션** 대화 상자에서 **진단 및 사용량 현황 데이터 설정** 을 선택합니다.

    ![콘솔 원격 분석 옵션](./media/telemetry/telemetry-options.png)

1. 표시된 옵션에서 진단 및 사용 현황 데이터 공유 기본 설정을 선택하고 **확인** 을 클릭합니다.

    >[!NOTE]
    >옵션을 선택하기 전에 [개인정보처리방침](https://privacy.microsoft.com/privacystatement)을 읽어 보시기 바랍니다.
    >- 원격 분석을 설정하려면 **예, Microsoft에 데이터를 보내겠습니다.** 를 선택합니다.
    >- 원격 분석을 해제하려면 **아니요, Microsoft에 데이터를 보내지 않겠습니다.** 를 선택합니다.

## <a name="telemetry-data-collected"></a>수집된 원격 분석 데이터

| 관련된 데이터 | 수집되는 데이터* |
| --- | --- |
| **설치** | 설치된 MABS 버전입니다. <br/><br/>설치된 MABS 업데이트 롤업 버전입니다. <br/><br/> 고유한 머신 식별자입니다. <br/><br/> MABS가 설치된 운영 체제입니다. <br/><br/> 고유한 클라우드 구독 식별자입니다.<br/><br/> MARS 에이전트 버전입니다.<br/><br/> 계층화된 스토리지의 사용 여부입니다. <br/><br/> 사용된 스토리지의 크기입니다. |
| **워크로드가 보호됨** | 워크로드 고유 식별자입니다. <br/><br/>백업 중인 워크로드의 크기입니다. <br/><br/>워크로드 유형 및 버전 번호입니다. <br/><br/>워크로드를 현재 MABS에서 보호하고 있는지 여부입니다. <br/><br/>워크로드가 보호되는 보호 그룹의 고유 식별자입니다.<br/><br/> 워크로드를 백업하는 위치로, 디스크/테이프 또는 클라우드입니다.|
| **작업** | 백업/복원 작업의 상태입니다. <br/><br/> 백업/복원된 데이터의 크기입니다. <br/><br/>백업/복원 작업이 실패하는 경우 실패 메시지입니다.<br/><br/> 복원 작업에 걸린 시간입니다.<br/><br/>백업/복원 작업이 실행된 워크로드의 세부 정보입니다. |
| **원격 분석 변경 상태** | 원격 분석 설정에 대한 상태 변경 세부 정보로, 사용 여부 및 시간입니다. |
| **MABS 콘솔 충돌 오류** | MABS 콘솔에 크래시가 있을 때 발생하는 오류에 대한 세부 정보입니다.|

## <a name="next-steps"></a>다음 단계

[워크로드 보호](./back-up-hyper-v-virtual-machines-mabs.md)