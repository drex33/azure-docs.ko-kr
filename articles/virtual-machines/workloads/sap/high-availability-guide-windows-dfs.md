---
title: Windows DFS-N을 사용하여 SMB 기반 파일 공유에 대한 유연한 SAPMNT 공유 만들기를 지원합니다.
description: Windows DFS-N을 사용하여 Azure NetApp Files SMB 또는 Azure Files Premium SMB에 대한 SAP 관련 SAPMNT 명명 제한 해결
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: stmuelle
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/12/2021
ms.author: stmuelle
ms.openlocfilehash: ccf5d3f12d2530c6b790334713224007b0d8bb31
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386811"
---
# <a name="using-windows-dfs-n-to-support-flexible-sapmnt-share-creation-for-smb-based-file-share"></a>Windows DFS-N을 사용하여 SMB 기반 파일 공유에 대한 유연한 SAPMNT 공유 만들기 지원

## <a name="introduction"></a>소개

WSFC 기반 ASCS/SCS와 같은 SAP 인스턴스는 공유 드라이브에 SAP 파일을 설치해야 합니다. SAP는 이러한 파일을 호스트하는 클러스터 공유 디스크 또는 파일 공유 클러스터를 지원합니다.

![클러스터 공유 구성 스크린샷](media/virtual-machines-shared-sap-high-availability-guide/swpm-01.png)클러스터 공유 구성 옵션에 대한 SWPM 선택 화면

**Azure NetApp Files SMB를** 기반으로 설치하는 경우 파일 공유 클러스터 옵션을 선택해야 합니다. 후속 화면에서 파일 공유 호스트 이름을 지정해야 합니다.

![클러스터 공유 호스트 이름 선택 스크린샷](media/virtual-machines-shared-sap-high-availability-guide/swpm-02.png)클러스터 공유 호스트 이름 구성에 대한 SWPM 선택 화면

클러스터 공유 호스트 이름은 선택한 설치 옵션을 기반으로 합니다. Azure NetApp Files SMB의 경우 설치의 Active Directory에 NetApp 계정을 조인하는 데 사용됩니다. SAP 용어로 이 이름은 **SAPGLOBALHOST라고** 합니다.
SWPM은 내부적으로 **sapmnt를** 호스트 이름에 추가하여 **\\ \SAPGLOBALHOST\sapmnt** 공유를 생성합니다. 아쉽게도 **sapmnt는** NetApp 계정당 한 번만 만들 수 있습니다. 이는 제한적입니다. DFS-N을 사용하여 서로 다른 이름의 공유에 할당할 수 있는 가상 공유 이름을 만들 수 있습니다. SWPM에서 요구하는 대로 **sapmnt를** 공유 이름으로 사용하는 대신 **sapmnt-sid와** 같은 고유한 이름을 사용할 수 있습니다. 전역 전송 디렉터리에도 마찬가지입니다. trans는 전역 전송 디렉터리에서 예상되는 이름이기 때문에 **SAP DIR_TRANS** 프로필 매개 변수는 **DEFAULT입니다. PFL** 프로필을 조정해야 합니다.

예를 들어 DFS-N을 사용하여 다음 공유를 만들 수 있습니다.  

**\\ \ANF-670f.contoso.corp \\ d01-sapmnt를 가리키는 \contoso.local\sapmnt** **\\ \\ D01**  

**\\ \ANF-670f.contoso.corp erp-trans를 가리키는 \contoso.local\sapmnt \\ erp-trans** **\\ \\**  
**default의 DIR_TRANS = \\ \contoso.local\sapmnt\erp-trans입니다. PFL 프로필** 입니다.

## <a name="microsoft-dfs-n"></a>Microsoft DFS-N

DFS-N에 대한 소개 및 설치 지침은 [여기에서](/windows-server/storage/dfs-namespaces/dfs-overview) 찾을 수 있습니다.

DFS-N에 복원력을 추가하는 방법은 이 설명서의 끝에 설명되어 있습니다.

## <a name="setting-up-folder-targets-for-azure-netapp-files-smb"></a>Azure NetApp Files SMB의 폴더 대상 설정

 Azure NetApp Files SMB의 폴더 대상은 DFS-N을 사용하지 않고 [여기에](./high-availability-guide-windows-netapp-files-smb.md) 설명된 것과 동일한 방식으로 기술적으로 생성된 볼륨입니다. 
![anf-volumes-overview ](media/virtual-machines-shared-sap-high-availability-guide/anf-volumes.png) 기존 ANF 볼륨이 있는 포털 스크린샷

## <a name="configuring-dfs-n-for-sapmnt"></a>SAPMNT에 대한 DFS-N 구성

다음 시퀀스는 처음에 DFS-N을 구성하는 개별 단계를 보여줍니다. 

Windows 서버 시작 메뉴의 **Windows 관리 도구에서** **DFS 관리 콘솔을** 시작합니다.

![DFS-N 설정 시퀀스의 초기 화면](media/virtual-machines-shared-sap-high-availability-guide/dfs-setup-01.png)

이 화면에는 열린 DFS 화면이 표시됩니다.

![DFS 네임스페이스 서버 정의](media/virtual-machines-shared-sap-high-availability-guide/dfs-setup-07.png)이 화면에서 DFS가 설치된 AD 조인 Windows Server를 선택해야 합니다.

![DFS 네임스페이스 공유 정의 ](media/virtual-machines-shared-sap-high-availability-guide/dfs-setup-08.png) 이 화면에서 네임스페이스 루트의 두 번째 부분 이름이 정의됩니다. 이 화면에서 **sapmnt는** SAP 명명 규칙의 일부인 제공되어야 합니다.

![DFS 네임스페이스 형식 정의](media/virtual-machines-shared-sap-high-availability-guide/dfs-setup-09.png)

이 단계에서는 네임스페이스 형식이 정의됩니다. 이 입력은 네임스페이스 루트의 첫 번째 부분 이름도 결정합니다. DFS는 도메인 기반 또는 독립 실행형 네임스페이스를 지원합니다. Windows 기반 설치에서 도메인 기반이 기본값입니다. 따라서 네임스페이스 서버의 설정은 도메인 기반이어야 합니다. 이 선택에 따라 도메인 이름은 네임스페이스 루트의 첫 번째 부분이 됩니다. 따라서 여기서 AD/도메인 이름은 **contoso.corp** 이므로 네임스페이스 루트는 **\\ \contoso.corp\sapmnt** 입니다.

네임스페이스 루트 아래에는 수많은 네임스페이스 폴더를 만들 수 있습니다. 각각은 폴더 대상을 가리킵니다. 폴더 대상의 이름을 자유롭게 선택할 수 있지만 네임스페이스 폴더의 이름은 유효한 SAP SID와 일치해야 합니다. 이렇게 하면 유효한 SWPM 규격 UNC 공유가 생성됩니다. 이 메커니즘은 SAP 전송 디렉터리를 제공하기 위해 trans-directory를 만드는 데도 사용됩니다.

![완료된 SAP 폴더를 사용하여 DFS 설치](media/virtual-machines-shared-sap-high-availability-guide/dfs-setup-11.png)스크린샷은 이러한 구성에 대한 예제를 보여줍니다.

## <a name="adding-additional-dfs-namespace-servers-to-increase-resiliency"></a>복원력을 높이기 위해 DFS 네임스페이스 서버 추가

도메인 기반 네임스페이스 서버를 설정하면 네임스페이스 서버를 쉽게 추가할 수 있습니다. 도메인 컨트롤러 간에 중요한 정보가 복제되는 Active Directories에서 중복성을 위해 여러 도메인 컨트롤러를 갖는 것과 유사하게, DFS-N에 대해 네임스페이스 서버를 더 추가하면 동일한 작업을 수행합니다. 이는 클러스터 노드 또는 독립 실행형 도메인 조인 서버에 대해 로컬로 도메인 컨트롤러에 허용됩니다. 이러한 역할을 사용하기 전에 DFS-N 역할을 설치해야 합니다.

**네임스페이스 루트** 를 **마우스 오른쪽** 단추로 클릭하면 **네임스페이스 서버 추가** 대화 상자가 열립니다.

![네임스페이스 서버 추가 대화 상자](media/virtual-machines-shared-sap-high-availability-guide/dfs-add-nss-07.png)

이 화면에서 네임스페이스 서버의 이름을 직접 지정할 수 있습니다. 또는 찾아보기 단추를 푸시하여 기존 서버를 나열할 수 있습니다.

![기존 네임스페이스 서버 개요](media/virtual-machines-shared-sap-high-availability-guide/dfs-add-nss-08.png)기존 네임스페이스 서버 개요입니다.

## <a name="adding-folders-to-azure-netapp-files-smb-based-namespace-root"></a>Azure NetApp Files SMB 기반 네임스페이스 루트에 폴더 추가

다음 시퀀스에서는 DFS-N에서 폴더를 만들고 폴더 대상에 할당하는 방법을 보여줍니다.

DFS 관리 콘솔에서 네임스페이스 루트를 마우스 오른쪽 단추로 클릭하고 새 폴더를 선택합니다. 

![DFS-N 폴더 추가 대화 상자 화면](media/virtual-machines-shared-sap-high-availability-guide/dfs-add-folder-05.png)

이 단계에서는 새 폴더 대화 상자를 엽니다. 이 경우 **P01과** 같은 유효한 SID를 제공하거나 전송 디렉터리를 만들려는 경우 **trans를** 사용합니다.

포털에서 폴더 대상으로 사용하려는 볼륨에 대한 탑재 지침을 얻고 위에 표시된 대로 UNC 이름을 복사하여 붙여넣습니다.

![포털의 ANF 탑재 지침](media/virtual-machines-shared-sap-high-availability-guide/dfs-add-folder-04.png)

![SAP 지형에 대한 폴더 설정의 스크린샷](media/virtual-machines-shared-sap-high-availability-guide/dfs-add-folder-08.png)

이 화면은 SAP 지형에 대한 폴더 설정을 예로 보여 줍니다.