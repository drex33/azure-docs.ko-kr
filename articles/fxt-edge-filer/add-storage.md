---
title: '자습서: Azure FXT Edge Filer 클러스터에 스토리지 추가'
description: Azure FXT Edge Filer에 대해 백 엔드 스토리지 및 클라이언트 측 pseudonamespace를 구성하는 방법
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: f3995ee02894c9cddb4f9941999b057bfeb1d260
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044972"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>자습서: 백 엔드 스토리지 추가 및 가상 네임스페이스 구성

이 자습서에서는 캐시용 백 에지 스토리지를 추가하는 방법과 클라이언트 측 가상 파일 시스템을 설정하는 방법을 설명합니다.

클러스터는 백 엔드 스토리지 시스템에 연결하여 데이터 클라이언트 요청에 액세스하고 변경 사항을 캐시에서 보다 더 영구적으로 저장합니다.

네임스페이스는 클라이언트 측 워크플로를 변경하지 않고 백 엔드 스토리지를 교환할 수 있는 클라이언트 측 의사 파일 시스템입니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * Azure FXT Edge Filer 클러스터에 백 엔드 스토리지를 추가하는 방법
> * 스토리지에 대한 클라이언트 측 경로를 정의하는 방법

## <a name="about-back-end-storage"></a>백 엔드 스토리지

Azure FXT Edge Filer 클러스터는 코어 파일러(*core filer*) 정의를 사용하여 백 엔드 스토리지 시스템을 FXT 클러스터에 연결합니다.

Azure FXT Edge Filer는 여러 가지 유명한 NAS 하드웨어 시스템과 호환되며 Azure Blob 또는 기타 클라우드 스토리지의 빈 컨테이너를 사용할 수 있습니다.

FXT 운영 체제가 클라우드 스토리지 볼륨의 모든 데이터를 완벽하게 관리할 수 있도록 추가할 때는 클라우드 스토리지 컨테이너가 비어 있어야 합니다. 컨테이너를 클러스터에 코어 파일러로 추가한 후에 기존 데이터를 클라우드 컨테이너로 옮길 수 있습니다.

제어판을 사용하여 코어 파일러를 시스템에 추가할 수 있습니다.

> [!NOTE]
>
> Amazon AWS나 Google Cloud Storage를 사용하려면 FlashCloud<sup>TM</sup> 기능 라이선스를 설치해야 합니다. Microsoft 담당자에게 라이선스 키를 문의한 다음, 레거시 구성 가이드의 지침에 따라 [기능 라이선스를 추가하거나 제거](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses)합니다.
>
> Azure Blob Storage에 대한 지원은 Azure FXT Edge Filer 소프트웨어 라이선스에 포함되어 있습니다.

코어 파일러 추가에 대한 자세한 내용은 클러스터 구성 가이드의 다음 섹션을 참조하세요.

* 코어 파일러 추가를 위한 선택 및 준비에 대한 자세한 내용은 [코어 파일러 작업](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview)을 참조하세요.
* 자세한 필수 구성 요소 및 단계별 지침은 다음 문서를 읽어보세요.

  * [새 NAS 코어 파일러 추가](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [새 클라우드 코어 파일러 추가](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

코어 파일러를 추가한 후 코어 파일러 세부 정보 설정 페이지에서 설정을 업데이트할 수 있습니다.

## <a name="add-a-core-filer"></a>코어 파일러 추가

코어 파일러는 **코어 파일러** > **코어 파일러 관리** 설정 페이지에서 **만들기** 단추를 클릭하여 만듭니다.

![코어 파일러 관리 페이지의 코어 파일러 목록 위에서 만들기 단추 클릭](media/fxt-cluster-config/create-core-filer-button.png)

**새 코어 파일러 추가** 마법사는 백 엔드 스토리지에 연결하는 코어 파일러를 만드는 과정을 단계별로 안내합니다. 클러스터 구성 가이드에는 과정에 대한 단계별 설명이 있으며, NFS/NAS 스토리지 및 클라우드 스토리지(링크는 위에 있음)에 대해서는 다릅니다.

하위 작업은 다음 항목을 포함합니다.

* 코어 파일러의 유형(NAS 또는 클라우드)을 지정합니다.

  ![하드웨어 NAS 새 코어 파일러 마법사의 첫 번째 페이지입니다. "클라우드 코어 파일러" 에 대한 옵션은 비활성화되어 있고 누락된 라이선스에 대한 메시지가 표시되어 있습니다.](media/fxt-cluster-config/new-nas-1.png)

* 코어 파일러의 이름을 설정합니다. 클러스터 관리자가 어떤 스토리지 시스템을 나타내는지 이해하는 데 도움이 되는 이름을 선택합니다.

* NAS 코어 파일러에 대한 FQDN(정규화된 도메인 이름) 또는 IP를 제공합니다. FQDN은 모든 코어 파일러에 권장되며 SMB 액세스에 필요합니다.

* 캐시 정책 선택 - 마법사의 두 번째 페이지에는 새 코어 파일러에 사용 가능한 캐시 정책이 나열되어 있습니다. 자세한 내용은 [클러스터 구성 가이드의 캐시 정책 섹션](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)을 참조하세요.

  ![하드웨어 NAS 새 코어 파일러 마법사의 두 번째 페이지; 캐시 정책 드롭다운 메뉴가 열려 있고 몇 가지 비활성화된 옵션과 3개의 유효한 캐시 정책 옵션(바이패스, 읽기 캐싱, 읽기/쓰기 캐싱)이 보입니다.](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* 클라우드 스토리지의 경우, 다른 매개 변수 중 클라우드 서비스 및 액세스 자격 증명을 지정해야 합니다. 자세한 내용은 클러스터 구성 가이드의 [클라우드 서비스 및 프로토콜](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol)을 참조하세요.

  ![새 코어 파일러 마법사의 클라우드 코어 파일러 정보](media/fxt-cluster-config/new-core-filer-cloud3.png)
  
  이 클러스터에 대한 클라우드 액세스 자격 증명을 이미 추가한 경우에는 목록에 표시됩니다. **클러스터** > **클라우드 자격 증명** 설정 페이지에서 자격 증명을 업데이트 및 추가합니다.

마법사에서 필요한 설정을 모두 채운 후, **파일러 추가** 단추를 클릭하여 변경 내용을 제출합니다.

몇 분 후에 스토리지 시스템이 **대시보드** 코어 파일러 목록에 나타나고 코어 파일러 설정 페이지를 통해 액세스할 수 있습니다.

![파일러 세부 정보 보기가 펼쳐진 코어 파일러 관리 설정 페이지의 "Flurry-NAS" 코어 파일러](media/fxt-cluster-config/core-filer-in-manage-page.png)

이 의 스크린샷의 코어 파일러에는 vserver가 누락되었습니다. 코어 파일러를 vserver에 연결하고 클라이언트가 스토리지에 액세스할 수 있도록 접합을 만들어야 합니다. 이 단계는 아래 [네임스페이스 구성](#configure-the-namespace)에 설명되어 있습니다.

## <a name="configure-the-namespace"></a>네임스페이스 구성

Azure FXT Edge Filer 클러스터는 다양한 백 엔드 시스템에 저장된 데이터에 대한 클라이언트 액세스를 간소화하는 클러스터 네임스페이스(*cluster namespace*)라는 가상 파일 시스템을 만듭니다. 클라이언트는 가상 경로를 사용하여 파일을 요청하기 때문에 클라이언트 워크플로를 변경하지 않고도 스토리지 시스템을 추가하거나 교체할 수 있습니다.

클러스터 네임스페이스를 사용하면 클라우드 및 NAS 스토리지 시스템을 유사한 파일 구조로 표시할 수도 있습니다.

클러스터의 vserver는 네임스페이스를 유지하고 클라이언트에 콘텐츠를 제공합니다. 클러스터 네임스페이스를 만드는 단계는 2단계입니다.

1. vserver 만들기
1. 백 엔드 스토리지 시스템과 클라이언트 측 파일 시스템 경로 사이에 접합 설정

### <a name="create-a-vserver"></a>vserver 만들기

VServer는 클라이언트와 클러스터의 코어 파일러 간에 데이터가 흐르는 방식을 제어하는 가상 파일 서버입니다.

* VServer는 클라이언트 측 IP 주소를 호스트합니다.
* VServer는 네임스페이스를 만들고 클라이언트 쪽 가상 디렉터리 구조를 백 엔드 스토리지의 내보내기로 매핑하는 접합을 정의합니다.
* VServer는 코어 파일러 내보내기 정책 및 사용자 인증 시스템을 비롯한 파일 액세스 제어를 적용합니다.
* VServer는 SMB 인프라를 제공합니다.

클러스터 vserver 구성을 시작하기 전에 링크된 설명서를 읽고 네임스페이스 및 vserver를 이해하는 데 도움이 필요하면 Microsoft 담당자에게 문의하세요. VLAN을 사용하는 경우 vserver를 만들기 전에 [VLAN을 만듭니다](configure-network.md#adjust-network-settings).

클러스터 구성 가이드의 다음 섹션은 FXT vserver 및 글로벌 네임스페이스 기능을 익히는 데 유용합니다.

* [VServer 만들기 및 작업](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [글로벌 네임스페이스 사용](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [VServer 만들기](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

클러스터에는 하나 이상의 vserver가 필요합니다.

vserver를 새로 만들려면 다음 정보가 필요합니다.

* vserver에 설정할 이름

* vserver가 처리할 클라이언트 측 IP 주소의 범위

  vserver를 만들 때는 단일 범위의 연속 IP 주소를 제공해야 합니다. 나중에 **클라이언트 측 네트워크** 설정 페이지를 사용하여 주소를 더 추가할 수 있습니다.

* 네트워크에 VLAN이 있는 경우, 이 vserver에 사용할 VLAN

**VServer** > **VServer 관리** 설정 페이지를 사용하여 새 vserver를 만듭니다. 자세한 내용은 클러스터 구성 가이드의 [VServer 만들기](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)를 참조하세요.

![새 vserver 만들기 팝업 창](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>접합 만들기

*접합* 은 백 엔드 스토리지 경로를 클라이언트에 표시되는 네임스페이스로 매핑합니다.

이 시스템을 사용하면 클라이언트 탑재 지점에 사용되는 경로를 간소화하고 용량을 원활하게 확장할 수 있습니다. 하나의 가상 경로가 여러 코어 파일러의 스토리지를 수용할 수 있기 때문입니다.

![설정이 채워진 새 접합 추가 마법사 페이지](media/fxt-cluster-config/add-junction-full.png)

네임스페이스 접합 만들기에 대한 자세한 내용은 클러스터 구성 가이드에서 [**VServer** > **네임스페이스**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)를 참조하세요.

![접합에 대한 세부 정보를 보여주는 VServer > 네임스페이스 설정 페이지](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>내보내기 규칙 구성

vserver와 코어 파일러가 모두 있으면, 클라이언트가 코어 파일러 내보내기에서 파일에 액세스하는 방법을 제어하는 내보내기 규칙과 내부내기 정책을 사용자 지정해야 합니다.

먼저 **VServer** > **내보내기 규칙** 페이지를 사용하여 새 규칙을 추가하고 기본 정책을 수정하거나 나만의 사용자 지정 내보내기 정책을 만듭니다.

그런 다음, **VServer** > **내보내기 정책** 페이지를 사용하여 vserver를 통해 액세스 할 때 사용자 지정 정책을 코어 파일러의 내보내기에 적용합니다.

자세한 내용은 클러스터 구성 가이드 문서 [코어 파일러 내보내기에 대한 액세스 제어](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html)를 참조하세요.

## <a name="next-steps"></a>다음 단계

스토리지를 추가하고 클라이언트 측 네임스페이스를 구성한 후, 클러스터의 초기 설정을 완료합니다.

> [!div class="nextstepaction"]
> [클러스터의 네트워크 설정 구성](configure-network.md)
