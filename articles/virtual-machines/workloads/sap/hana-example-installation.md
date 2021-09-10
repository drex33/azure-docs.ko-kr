---
title: Azure(대규모 인스턴스)의 SAP HANA에 HANA 설치 | Microsoft Docs
description: Azure(대규모 인스턴스)의 SAP HANA에 HANA를 설치하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/4/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e4e7aec0422d3dfe5772748215c350073155a30
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111559791"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Azure의 SAP HANA(대규모 인스턴스)에 HANA 설치

이 문서에서는 Azure의 SAP HANA(대규모 인스턴스)(BareMetal Infrastructure라고도 함)에 HANA를 설치하는 방법을 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure의 SAP HANA(대규모 인스턴스)에 HANA를 설치하려면 먼저 다음을 수행합니다.

- SAP HANA(대규모 인스턴스)에 배포하려면 Microsoft에 모든 데이터를 제공합니다.
- Microsoft로부터 SAP HANA(대규모 인스턴스)를 받습니다.
- 온-프레미스 네트워크에 연결되는 Azure 가상 네트워크를 만듭니다.
- HANA(대규모 인스턴스)에 대한 ExpressRoute 회로를 동일한 Azure 가상 네트워크에 연결합니다.
- HANA(대규모 인스턴스)의 점프 박스로 사용하는 Azure Virtual Machine을 설치합니다.
- 점프 박스에서 HANA(대규모 인스턴스)로 연결할 수 있고 그 반대로도 연결할 수 있어야 합니다.
- 필요한 패키지와 패치가 모두 설치되어 있는지 확인합니다.
- 사용하고 있는 운영 체제에 HANA 설치에 대한 SAP 참고 사항 및 설명서를 읽습니다. 선택한 HANA 릴리스가 운영 체제 릴리스에서 지원되어야 합니다.

## <a name="download-the-sap-hana-installation-bits"></a>SAP HANA 설치 비트 다운로드

이제 HANA 설치 패키지를 점프 박스 가상 머신으로 다운로드하겠습니다. 이 예제에서 운영 체제는 Windows입니다.

HANA 대규모 인스턴스 유닛은 인터넷에 직접 연결되지 않습니다. SAP에서 HANA 대규모 인스턴스 가상 머신으로 설치 패키지를 직접 다운로드할 수 없습니다. 대신 패키지를 점프 박스 가상 머신에 다운로드합니다.

SAP Marketplace에 액세스할 수 있는 SAP S-사용자 또는 다른 사용자가 필요합니다.

1. 로그인한 후, [SAP Service Marketplace](https://support.sap.com/en/index.html)로 이동합니다. **소프트웨어 다운로드** > **설치 및 업그레이드** > **사전순 인덱스별** 을 선택합니다. 그런 다음, **H – SAP HANA 플랫폼 버전 아래에서** > **SAP HANA 플랫폼 버전 2.0** > **설치** 를 선택합니다. 다음 스크린샷에 표시된 것처럼 파일을 다운로드합니다.

   ![다운로드할 HANA 설치 파일의 스크린샷](./media/hana-installation/image16_download_hana.PNG)

2. 이 예제에서는 SAP HANA 2.0 설치 패키지를 다운로드했습니다. Azure 점프 박스 가상 머신에서 자동 압축 해제 아카이브를 디렉터리에 추출합니다(아래 그림 참조).

   ![자동 압축 해제 아카이브의 스크린샷](./media/hana-installation/image17_extract_hana.PNG)

3. 아카이브가 추출되면 추출하여 만든 디렉터리를 복사합니다(이 경우 51052030). HANA 대규모 인스턴스 유닛 /hana/shared 볼륨의 디렉터리를 만든 디렉터리로 복사합니다.

   > [!Important]
   > 설치 패키지를 루트 또는 부팅 LUN에 복사하지 마세요. 공간이 제한되어 있으며 다른 프로세스에서도 사용해야 합니다.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>HANA 큰 인스턴스 유닛에 SAP HANA 설치

1. SAP HANA를 설치하려면 루트 사용자로 로그인합니다. root만 SAP HANA를 설치할 수 있는 권한이 있습니다. /hana/shared에 복사한 디렉터리에 대한 권한을 설정합니다.

    ```
    chmod –R 744 <Installation bits folder>
    ```
    
    그래픽 사용자 인터페이스 설치를 사용하여 SAP HANA를 설치하려면 gtk2 패키지가 HANA(대규모 인스턴스)에 설치되어 있어야 합니다. 설치되어 있는지 확인하려면 다음 명령을 실행합니다.
    
    ```
    rpm –qa | grep gtk2
    ```

    (이후 단계에서는 그래픽 사용자 인터페이스를 사용하여 SAP HANA 설치를 보여 줍니다.)         

2. 설치 디렉터리로 이동한 다음, 하위 디렉터리인 HDB_LCM_LINUX_X86_64로 이동합니다. 

    해당 디렉터리에서 다음을 시작합니다.
    
    ```
    ./hdblcmgui 
    ```
3. 이제 설치를 위해 데이터를 제공하는 일련의 화면을 진행합니다. 이 예제에서는 SAP HANA 데이터베이스 서버 및 SAP HANA 클라이언트 구성 요소를 설치합니다. **SAP HANA 데이터베이스** 를 선택합니다.

    ![선택한 SAP HANA 데이터베이스를 사용하는 SAP HANA 수명 주기 관리 화면의 스크린샷](./media/hana-installation/image18_hana_selection.PNG)

4. **새 시스템 설치** 를 선택합니다.

    ![선택한 새 시스템 설치를 사용하는 SAP HANA 수명 주기 관리 화면의 스크린샷](./media/hana-installation/image19_select_new.PNG)

5. 설치할 수 있는 다른 몇 가지 구성 요소 중에서 선택합니다.

    ![추가 구성 요소 목록을 사용하는 SAP HANA 수명 주기 관리 화면의 스크린샷](./media/hana-installation/image20_select_components.PNG)

6. SAP HANA Client 및 SAP HANA Studio를 선택합니다. 강화 인스턴스도 설치합니다. 그런 다음, **단일 호스트 시스템** 을 선택합니다. 

    ![선택한 단일 호스트 시스템을 사용하는 SAP HANA 수명 주기 관리 화면의 스크린샷](./media/hana-installation/image21_single_host.PNG)

7. 다음으로, 일부 데이터를 제공합니다. 설치 경로는 /hana/shared 디렉터리를 사용합니다.

    ![정의할 시스템 속성 필드를 사용하는 SAP HANA 수명 주기 관리 화면의 스크린샷](./media/hana-installation/image22_provide_sid.PNG)

    > [!Important]
    > HANA SID(System ID)로 HANA 대규모 인스턴스 배포를 주문할 때 Microsoft에 제공한 것과 동일한 SID를 제공해야 합니다. 다른 SID를 선택하면 다른 볼륨의 액세스 권한 문제로 인해 설치가 실패합니다.

8. HANA 데이터 파일과 HANA 로그 파일의 위치를 제공합니다.

    ![데이터 및 로그 영역 필드를 사용하는 SAP HANA 수명 주기 관리 화면의 스크린샷](./media/hana-installation/image23_provide_log.PNG)

    > [!Note]
    > 시스템 속성을 정의할 때(두 개 화면 전에) 지정한 SID가 탑재 지점의 SID와 일치해야 합니다. 일치하지 않는 경우 뒤로 돌아가서 탑재 지점에 있는 값에 맞게 SID를 조정합니다.

9. 호스트 이름을 검토하고 필요에 따라 수정합니다. 

    ![호스트 이름을 사용하는 SAP HANA 수명 주기 관리 화면의 스크린샷](./media/hana-installation/image24_review_host_name.PNG)

10. 다음 HANA(대규모 인스턴스) 배포를 주문할 때 Microsoft에 제공한 데이터를 검색합니다. 

    ![정의할 시스템 관리자 필드를 사용하는 SAP HANA 수명 주기 관리 화면의 스크린샷](./media/hana-installation/image25_provide_guid.PNG)

    > [!Important]
    > 유닛 배포를 주문할 때 Microsoft에 제공한 **시스템 관리자 사용자 ID** 및 **사용자 그룹 ID** 를 제공합니다. 제공하지 않으면 HANA(대규모 인스턴스) 유닛에 SAP HANA를 설치할 수 없습니다.

11. 다음 두 화면은 여기에 표시되지 않습니다. 두 화면을 사용하면 SAP HANA 데이터베이스의 시스템 사용자 암호 및 Sapadm 사용자 암호를 제공할 수 있습니다. Sapadm 사용자 암호는 SAP HANA 데이터베이스 인스턴스의 일부로 설치되는 SAP Host Agent에 사용됩니다.

    암호를 정의한 후 확인 화면이 표시됩니다. 나열된 모든 데이터를 확인하고 설치를 계속합니다. 다음과 같이 설치 진행률을 보여주는 진행률 화면이 표시됩니다.

    ![설치 진행률 표시기를 사용하는 SAP HANA 수명 주기 관리 화면의 스크린샷](./media/hana-installation/image27_show_progress.PNG)

12. 설치가 완료되면 다음과 같은 화면이 표시됩니다.

    ![설치 완료를 표시하는 SAP HANA 수명 주기 관리 화면의 스크린샷](./media/hana-installation/image28_install_finished.PNG)

    이제 SAP HANA 인스턴스가 실행되어 사용할 준비가 되어야 합니다. SAP HANA Studio에서 연결할 수 있습니다. 최신 업데이트를 확인하고 적용해야 합니다.


## <a name="next-steps"></a>다음 단계

Azure의 SAP HANA(대규모 인스턴스) 고가용성 및 재해 복구에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure의 SAP HANA 대규모 인스턴스 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)
