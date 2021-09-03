---
title: Azure VMware Solution에서 VM을 배포하는 콘텐츠 라이브러리 만들기
description: Azure VMware Solution 프라이빗 클라우드에서 VM을 배포하는 콘텐츠 라이브러리를 만듭니다.
ms.topic: how-to
ms.date: 06/28/2021
ms.openlocfilehash: ed98d48037df6cfb50c3c94bb6a7187097f5b0e9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567969"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Azure VMware Solution에서 VM을 배포하는 콘텐츠 라이브러리 만들기

콘텐츠 라이브러리는 라이브러리 항목 형식으로 콘텐츠를 저장하고 관리합니다. 단일 라이브러리 항목은 VM(가상 머신)을 배포하는 데 사용되는 파일로 구성됩니다.

이 문서에서는 vSphere 클라이언트에서 컨텐츠 라이브러리를 만든 다음 컨텐츠 라이브러리의 ISO 이미지를 사용하여 VM을 배포합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 NSX 세그먼트 및 관리형 DHCP 서비스가 필요합니다.  자세한 내용은 [Azure VMware Solution용 DHCP 구성](configure-dhcp-azure-vmware-solution.md)을 참조하세요.  

## <a name="create-a-content-library"></a>콘텐츠 라이브러리 만들기

1. 온-프레미스 vSphere 클라이언트에서 **메뉴** > **콘텐츠 라이브러리** 를 선택합니다.

   :::image type="content" source="media/content-library/vsphere-menu-content-libraries.png" alt-text="vSphere 클라이언트의 콘텐츠 라이브러리 메뉴 옵션을 보여 주는 스크린샷.":::

1. **추가** 를 선택하여 새 콘텐츠 라이브러리를 만듭니다.

   :::image type="content" source="media/content-library/create-new-content-library.png" alt-text="vSphere에서 새 콘텐츠 라이브러리를 만드는 방법을 보여 주는 스크린샷.":::

1. 이름을 지정하고, vCenter Server의 IP 주소를 확인하고, **다음** 을 선택합니다.

   :::image type="content" source="media/content-library/new-content-library-step-1.png" alt-text="새 콘텐츠 라이브러리의 이름과 vCenter Server IP를 보여 주는 스크린샷.":::

1. **로컬 콘텐츠 라이브러리** 를 선택하고 **다음** 을 선택합니다.

   :::image type="content" source="media/content-library/new-content-library-step-2.png" alt-text="새 콘텐츠 라이브러리에 대해 선택된 로컬 콘텐츠 라이브러리 옵션을 보여 주는 스크린샷.":::

1. 콘텐츠 라이브러리를 저장할 데이터 저장소를 선택한 후 **다음** 을 선택합니다.

   :::image type="content" source="media/content-library/new-content-library-step-3.png" alt-text="선택한 vsanDatastore 스토리지 위치를 보여 주는 스크린샷.":::

1. 콘텐츠 라이브러리 설정을 검토하고 **마침** 을 선택합니다.

   :::image type="content" source="media/content-library/new-content-library-step-4.png" alt-text="새 콘텐츠 라이브러리의 설정을 보여 주는 스크린샷.":::

## <a name="upload-an-iso-image-to-the-content-library"></a>콘텐츠 라이브러리에 ISO 이미지 업로드

콘텐츠 라이브러리를 만들었으므로, 이제 ISO 이미지를 추가하여 프라이빗 클라우드 클러스터에 VM을 배포할 수 있습니다. 

1. vSphere 클라이언트에서 **메뉴** > **콘텐츠 라이브러리** 를 선택합니다.

1. 새 ISO에 사용할 콘텐츠 라이브러리를 마우스 오른쪽 단추로 클릭하고 **항목 가져오기** 를 선택합니다.

1. 다음 중 하나를 수행하여 원본에 대한 라이브러리 항목을 가져온 다음, **가져오기** 를 선택합니다.
   1. URL을 선택하고 ISO를 다운로드할 URL을 입력합니다.

   1. 로컬 시스템에서 업로드할 **로컬 파일** 을 선택합니다.

   > [!TIP]
   > 선택 사항으로, 대상의 사용자 지정 항목 이름과 메모를 입력할 수 있습니다.

1. 라이브러리를 열고 **기타 유형** 탭을 선택하여 ISO가 성공적으로 업로드되었는지 확인합니다.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>프라이빗 클라우드 클러스터에 VM 배포

1. vSphere 클라이언트에서 **메뉴** > **호스트 및 클러스터** 를 선택합니다.

1. 왼쪽 패널에서 트리를 확장하고 클러스터를 선택합니다.

1. **작업** > **새 가상 머신** 을 선택합니다.

1. 마법사를 진행하고 원하는 설정을 수정합니다.

1. **새 CD/DVD 드라이브** > **클라이언트 디바이스** > **콘텐츠 라이브러리 ISO 파일** 을 선택합니다.

1. 이전 섹션에서 업로드한 ISO를 선택하고 **확인** 을 선택합니다.

1. 전원을 켜면 ISO가 탑재되도록 **연결** 확인란을 선택합니다.

1. **새 네트워크** > **드롭다운 선택** > **찾아보기** 를 선택합니다.

1. **논리 스위치(세그먼트)** 를 선택하고 **확인** 을 선택합니다.

1. 다른 하드웨어 설정을 수정하고 **다음** 을 선택합니다.

1. 설정을 검토하고 **마침** 을 선택합니다.


## <a name="next-steps"></a>다음 단계

Azure VMware Solution에서 콘텐츠 라이브러리를 만들어 VM을 배포했습니다. 이제 다음에 대해 알아볼 수 있습니다.

- [VM 워크로드를 프라이빗 클라우드로 마이그레이션](configure-vmware-hcx.md)
- [Azure VMware Solution에서 Azure 네이티브 서비스 통합](integrate-azure-native-services.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
