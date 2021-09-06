---
title: Azure의 SAP HANA (대규모 인스턴스)에서 HANA 쪽의 모니터링 및 문제 해결 | Microsoft Docs
description: SAP HANA에서 제공하는 리소스를 사용하여 Azure의 SAP HANA(대규모 인스턴스)를 모니터링하고 관련 문제를 해결하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/18/2021
ms.author: madhukan
ms.custom:
- H1Hack27Feb2017
- contperf-fy21q4
ms.openlocfilehash: 691f9390d291d63b1263ecbcd77d1aa7bed48167
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580965"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA 쪽에서 모니터링 및 문제 해결

이 문서에서는 SAP HANA에서 제공하는 리소스를 사용하여 Azure의 SAP HANA(대규모 인스턴스)를 모니터링하고 관련 문제를 해결하는 과정을 살펴보겠습니다. 

Azure의 SAP HANA(대규모 인스턴스)와 관련된 문제를 분석하려면 문제의 근본 원인을 좁혀야 합니다. SAP는 도움이 되는 설명서를 다수 게시했습니다. SAP HANA 성능과 관련된 FAQ는 다음 SAP Note에서 찾을 수 있습니다.

- [SAP 참고 사항 #2222200 – FAQ: SAP HANA 네트워크](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP 참고 사항 #2100040 – FAQ: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP 참고 사항 #199997 – FAQ: SAP HANA 메모리](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP 참고 사항 #200000 – FAQ: SAP HANA 성능 최적화](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP 참고 사항 #199930 – FAQ: SAP HANA I/O 분석](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP 참고 사항 #2177064 – FAQ: SAP HANA 서비스 다시 시작 및 충돌](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA 경고

첫째, 현재 SAP HANA 경고 로그를 확인합니다. SAP HANA Studio에서 **관리 콘솔: 경고: 표시: 모든 경고** 로 이동합니다. 이 탭은 최소/최대 임계값 설정 범위에 속하지 않는 값(사용 가능한 실제 메모리, CPU 사용률 등)에 대한 SAP HANA 경고를 모두 표시합니다. 기본적으로 검사는 15분마다 자동으로 새로 고쳐집니다.

![SAP HANA Studio에서 [관리 콘솔: 경고: 표시: 모든 경고]로 이동합니다.](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

임계값을 부적절하게 설정하여 트리거되는 경고는 기본값 또는 보다 적절한 임계값으로 다시 설정합니다.

![기본값 또는 보다 적절한 크기의 임계값으로 다시 설정](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

다음과 같은 경고는 CPU 리소스 문제를 의미할 수 있습니다.

- 호스트 CPU 사용량(경고 5)
- 가장 최근 저장 지점 작업(경고 28)
- 저장 지점 기간(경고 54)

다음을 통해 SAP HANA 데이터베이스에서 높은 CPU 사용량을 확인할 수 있습니다.

- 경고 5(호스트 CPU 사용량)는 현재 또는 과거 CPU 사용량에 발생합니다.
- 개요 화면에 표시된 CPU 사용량

![개요 화면에 표시된 CPU 사용량](./media/troubleshooting-monitoring/image3-cpu-usage.png)

로드 그래프는 높은 CPU 사용량 또는 과거의 높은 사용량을 표시할 수 있습니다.

![로드 그래프는 높은 CPU 사용량 또는 과거의 높은 사용량을 표시할 수 있습니다.](./media/troubleshooting-monitoring/image4-load-graph.png)

CPU 사용량이 많아서 트리거되는 경고는 다음과 같은 여러 가지 이유로 인해 발생할 수 있습니다.
- 특정 트랜잭션의 실행
- 데이터 로드
- 응답하지 않는 작업
- 장기 실행 SQL 문
- 잘못된 쿼리 성능(예: HANA 큐브의 BW 사용)

자세한 CPU 사용량 문제 해결 단계는 [SAP HANA 문제 해결: CPU 관련 원인 및 솔루션](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/4fbc915462db406aa2fe92b708b95189.html?q=%20SAP%20HANA%20Troubleshooting:%20CPU%20Related%20Causes%20and%20Solutions)을 참조하세요.

## <a name="operating-system-os"></a>OS(운영 체제)

Linux에서 중요한 SAP HANA 검사 항목은 THP(Transparent Huge Pages)를 사용하지 않도록 설정했는지 확인하는 것입니다. 자세한 내용은 [SAP Note #2131662 – SAP HANA 서버의 THP(Transparent Huge Pages)](https://launchpad.support.sap.com/#/notes/2131662)를 참조하세요.

Linux 명령, 즉 **cat /sys/kernel/mm/transparent\_hugepage/enabled** 를 통해 THP(Transparent Huge Pages)가 사용하도록 설정되었는지 확인할 수 있습니다.
- _always_ 가 괄호로 묶인 경우 THP(Transparent Huge Pages)가 사용됨을 의미합니다([always] madvise never).
- _never_ 가 괄호로 묶인 경우 THP(Transparent Huge Pages)가 비활성화됨을 의미합니다(always madvise [never])

다음 Linux 명령은 아무것도 반환하지 않습니다. **rpm -qa | grep ulimit.** _ulimit_ 가 표시되는 경우 즉시 설치를 제거합니다.

## <a name="memory"></a>메모리

SAP HANA 데이터베이스에 할당된 메모리의 양이 예상보다 높다는 사실을 확인할 수 있습니다. 다음 경고는 높은 메모리 사용량에 문제가 있는 경우 표시됩니다.

- 호스트 실제 메모리 사용량(경고 1)
- 이름 서버의 메모리 사용량(경고 12)
- 열 저장소 테이블의 총 메모리 사용량(경고 40)
- 서비스의 메모리 사용량(경고 43)
- 열 스토리지 테이블 중 기본 스토리지의 메모리 사용량(경고 45)
- 런타임 덤프 파일(경고 46)

자세한 메모리 문제 해결 단계는 [SAP HANA 문제 해결: 메모리 문제의 근본 원인](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/3a2ea5c4593b4b8d823b5b48152bd1d4.html)을 참조하세요.

## <a name="network"></a>네트워크

[SAP Note #2081065 - SAP HANA 네트워크 문제 해결](https://launchpad.support.sap.com/#/notes/2081065)을 참조하고, 이 SAP Note에 있는 네트워크 문제 해결 단계를 수행합니다.

1. 서버와 클라이언트 간의 왕복 시간을 분석합니다.
    - SQL 스크립트 [_HANA\_네트워크\_클라이언트_](https://launchpad.support.sap.com/#/notes/1969700)_를 실행합니다._
  
2. 노드 간 통신을 분석합니다.
    - SQL 스크립트 [_HANA\_네트워크\_서비스_](https://launchpad.support.sap.com/#/notes/1969700)_를 실행합니다._

3. Linux 명령 **ifconfig** 를 실행합니다(패킷 손실의 발생 여부는 출력에서 표시).
4. Linux 명령 **tcpdump** 를 실행합니다.

또한 오픈 소스 [IPERF](https://iperf.fr/) 도구(또는 유사한 기능)를 사용하여 실제 애플리케이션 네트워크 성능을 측정합니다.

자세한 문제 해결 단계는 [SAP HANA 문제 해결: 네트워킹 성능 및 연결 문제](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/a3ccdff1aedc4720acb24ed8826938b6.html?q=Networking%20Performance%20and%20Connectivity%20Problems)를 참조하세요.

## <a name="storage"></a>Storage

I/O 성능에 문제가 있다고 가정해 보겠습니다. 그러면 최종 사용자가 애플리케이션 또는 시스템 전체를 찾아보거나, 느리게 실행하거나, 응답하지 않거나, 응답을 중지할 수도 있습니다. SAP HANA Studio의 **볼륨** 탭에서 연결된 볼륨과 각 서비스에서 사용하는 볼륨을 확인할 수 있습니다.

![SAP HANA Studio의 [볼륨] 탭에서 연결된 볼륨 및 각 서비스에서 사용하는 볼륨을 확인할 수 있습니다.](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

화면 아래쪽([볼륨] 탭)에서 파일 및 I/O 통계와 같은 볼륨의 세부 정보를 볼 수 있습니다.

![화면 아래쪽에서 파일 및 I/O 통계와 같은 볼륨의 세부 정보를 볼 수 있습니다.](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

I/O 문제 해결 단계는 [SAP HANA 문제 해결: I/O 관련 근본 원인 및 솔루션](https://help.sap.com/viewer/4e9b18c116aa42fc84c7dbfd02111aba/2.0.05/en-US/dc6ff98fa36541e997e4c719a632cbd8.html?q=I%2FO%20Related%20Root%20Causes%20and%20Solutions)을 참조하세요. 디스크 관련 문제 해결 단계는 [SAP HANA 문제 해결: 디스크 관련 근본 원인 및 솔루션](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/474cb08a715c42fe9f7cc5efdc599959.html?q=Disk%20Related%20Root%20Causes%20and%20Solutions)을 참조하세요.

## <a name="diagnostic-tools"></a>진단 도구

HANA\_Configuration\_Minichecks를 통해 SAP HANA 상태 검사를 수행합니다. 이 도구는 SAP HANA Studio에서 이미 경고로 발생했어야 하는 잠재적으로 중요한 기술 문제를 반환합니다.

1. [SAP 참고 사항 #1969700 - SAP HANA에 대한 SQL 문 컬렉션](https://launchpad.support.sap.com/#/notes/1969700)을 참조하고 참고 사항에 연결된 SQL Statements.zip 파일을 다운로드합니다. 로컬 하드 드라이브에서 이.zip 파일을 저장합니다.

2. SAP HANA Studio의 **시스템 정보** 탭에서 **이름** 열을 마우스 오른쪽 단추로 클릭하고 **가져오기 SQL 문** 을 선택합니다.

    ![SAP HANA Studio의 [시스템 정보] 탭에서 [이름] 열을 마우스 오른쪽 단추로 클릭하고 [가져오기 SQL 문]을 선택합니다.](./media/troubleshooting-monitoring/image7-import-statements-a.png)

3. 로컬에 저장된 SQL Statements.zip 파일을 선택하고, 해당 SQL 문이 포함된 폴더를 가져옵니다. 이 시점에서 이러한 SQL 문으로 다른 여러 진단 검사를 실행할 수 있습니다.

    예를 들어 SAP HANA 시스템 복제 대역폭 요구 사항을 테스트하려면 **복제: 대역폭** 아래에서 **대역폭** 문을 마우스 오른쪽 단추로 클릭하고 SQL 콘솔에서 **열기** 를 선택합니다.

    입력 매개 변수(수정 섹션)을 변경한 다음 실행할 수 있도록 전체 SQL 문이 열립니다.

    ![입력 매개 변수(수정 섹션)을 변경한 다음 실행할 수 있도록 전체 SQL 문이 열립니다.](./media/troubleshooting-monitoring/image8-import-statements-b.png)

4. 또 다른 예제는 **복제: 개요** 에서 문을 마우스 오른쪽 단추로 클릭하는 것입니다. 상황에 맞는 메뉴에서 **실행** 을 선택합니다.

    ![또 다른 예제는 [복제: 개요]에서 문을 마우스 오른쪽 단추로 클릭하는 것입니다. 상황에 맞는 메뉴에서 [실행]을 선택합니다.](./media/troubleshooting-monitoring/image9-import-statements-c.png)

    문제 해결에 유용한 정보를 볼 수 있습니다.

    ![문제 해결에 유용한 정보를 볼 수 있습니다.](./media/troubleshooting-monitoring/image10-import-statements-d.png)

5. HANA\_Configuration\_Minichecks에 대해 동일한 작업을 수행하고 _C_(위험) 열에서 _X_ 표시를 확인합니다.

    샘플 출력:

    일반 SAP HANA 검사는 **HANA\_Configuration\_MiniChecks\_Rev102.01+1**.

    ![일반 SAP HANA 검사는 [HANA\_Configuration\_MiniChecks\_Rev102.01+1].](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

    현재 실행 중인 SAP HANA 서비스의 개요는 **HANA\_Services\_Overview**.

    ![현재 실행 중인 SAP HANA 서비스의 개요는 HANA\_Services\_Overview](./media/troubleshooting-monitoring/image12-services-overview.png)

    SAP HANA 서비스 정보(CPU, 메모리 등)는 **HANA\_Services\_Statistics**.

    ![SAP HANA 서비스 정보는 [HANA\_Services\_Statistics].](./media/troubleshooting-monitoring/image13-services-statistics.png)

    SAP HANA 인스턴스에 대한 일반적인 정보는 **HANA\_Configuration\_Overview\_Rev110+**.

    ![SAP HANA 인스턴스에 대한 일반적인 정보는 [HANA\_Configuration\_Overview\_Rev110+].](./media/troubleshooting-monitoring/image14-configuration-overview.png)

    SAP HANA 매개 변수를 확인하려면 **HANA\_Configuration\_Parameters\_Rev70+**.

    ![SAP HANA 매개 변수를 확인하려면 [HANA\_Configuration\_Parameters\_Rev70+].](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

## <a name="next-steps"></a>다음 단계

STONITH 디바이스를 사용하여 SUSE 운영 체제에서 고가용성을 설정하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [STONITH를 사용하여 SUSE에서 고가용성 설정](ha-setup-with-stonith.md)
