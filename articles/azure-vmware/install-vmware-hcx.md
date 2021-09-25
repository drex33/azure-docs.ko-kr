---
title: Azure VMware 솔루션용 VMware HCX 설치
description: Azure VMware 솔루션 프라이빗 클라우드에 VMware HCX를 설치합니다.
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: e3db000661ea310c35d32d988db5cd2001290004
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600820"
---
# <a name="install-and-activate-vmware-hcx-in-azure-vmware-solution"></a>Azure VMware 솔루션에 VMware HCX 설치 및 활성화

VMware HCX Advanced 및 이와 관련된 Cloud Manager는 더 이상 Azure VMware 솔루션에 미리 배포되지 않습니다. 대신 Azure Portal 통해 추가 기능으로 설치합니다. 여전히 HCX Connector OVA를 다운로드하고 온-프레미스 vCenter에 가상 어플라이언스를 배포합니다. 

모든 버전의 VMware HCX는 25개의 사이트 페어링(온-프레미스에서 클라우드로 또는 클라우드에서 클라우드로)을 지원합니다.  기본값은 HCX Advanced이지만, 현재 공개 미리 보기로 있는 HCX Enterprise Edition 사용하도록 [지원 요청을](https://rc.portal.azure.com/#create/Microsoft.Support) 열 수 있습니다. 서비스를 일반적으로 사용할 수 있게 되면, 다음 단계를 결정하는 데 30일이 주어집니다. HCX Enterprise Edition 서비스를 해제하거나 옵트아웃할 수도 있지만 노드 비용의 일부로 HCX Advanced를 유지할 수도 있습니다.

다시 배포하지 않고 HCX Enterprise Edition에서 HCX Advanced로 다운그레이드하는 것이 가능합니다. 먼저 Enterprise 기능을 사용하지 않고 HCX 고급 구성 상태로 되돌려야 합니다. 다운그레이드하려는 경우 예약된 마이그레이션이 없고 RAV 및 [HCX MON(Mobility Optimized Networking)과](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) 같은 기능이 사용되지 않고 사이트 페어링이 3개 이하인지 확인합니다.

>[!TIP]
>포털에서 [HCX Advanced를 제거](#uninstall-hcx-advanced)할 수도 있습니다. HCX Advanced를 제거하는 경우, 진행 중인 마이그레이션이 없는지 확인합니다. HCX Advanced를 제거하면 HCX 가상 어플라이언스에서 점유하는 프라이빗 클라우드에 리소스가 반환됩니다.

이 방법 가이드에서는 다음을 수행하게 됩니다.

* Azure Portal를 통해 HCX Advanced 설치
* VMware HCX Connector OVA 다운로드 및 배포
* 라이선스 키를 사용하여 HCX Advanced 활성화


완료되면 마지막에 권장되는 다음 단계에 따라 이 시작 가이드의 단계를 계속 진행합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [HCX 설치 준비](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-A631101E-8564-4173-8442-1D294B731CEB.html)

- VMware HCX Enterprise를 사용하려면 [지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 통해 [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) 추가 항목을 사용하도록 설정했는지 확인합니다. Azure VMware Solution 12개월 평가판입니다.

- [VMware 블로그 시리즈 - 클라우드 마이그레이션](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)


## <a name="install-vmware-hcx-advanced"></a>VMware HCX Advanced 설치

1. Azure VMware Solution 프라이빗 클라우드에서 **관리** > **Add-ons** 를 선택합니다.

1. **HCX Workload Mobility** 에 대한 **시작하기** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-get-started.png" alt-text="HCX Workload Mobility의 시작하기 단추를 보여주는 스크린샷.":::

1. **사용 약관에 동의함** 확인란을 선택하고 **설치** 를 선택합니다.

   HCX Advanced를 설치하고 Cloud Manager를 구성하는 데 35분 정도 걸립니다. 설치되면 HCX 온-프레미스 커넥터 사이트 페어링에 필요한 HCX 관리자 URL 및 HCX 키가 **HCX를 사용하여 마이그레이션** 탭에 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-using-hcx-tab.png" alt-text="연결 아래에서 HCX 탭을 사용하여 마이그레이션을 보여주는 스크린샷.":::


## <a name="download-and-deploy-the-vmware-hcx-connector-ova"></a>VMware HCX Connector OVA 다운로드 및 배포 

이 단계에서는 VMware HCX Connector OVA 파일을 다운로드 한 다음, 온-프레미스 vCenter에 VMware HCX Connector를 배포합니다.

1. 브라우저 창을 열고, **cloudadmin\@vsphere.local** 사용자 자격 증명을 사용하여 `https://x.x.x.9` 443 포트에서 Azure VMware Solution HCX Manager에 로그인합니다.

1. **관리** > **시스템 업데이트** 아래에서, **다운로드 링크 요청** 을 선택합니다. 상자가 회색으로 표시되면 몇 초 동안 기다린 후 링크를 생성합니다.

1. 로컬 vCenter에 배포하는 VMware HCX Connector OVA 파일의 링크를 다운로드하거나 수신합니다.

1. 온-프레미스 vCenter에서 [OVF 템플릿](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)을 선택하여 VMware HCX Connector를 온-프레미스 vCenter에 배포합니다.

1. 다운로드한 OVA 파일로 이동하여 선택한 다음, **열기** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::

1. 이름 및 위치를 선택하고, VMware HCX Connector를 배포할 리소스 또는 클러스터를 선택합니다. 그런 다음, 세부 정보 및 필요한 리소스를 검토하고, **다음** 을 선택합니다.

1. 사용 조건을 검토하고, 필요한 스토리지 및 네트워크를 선택하고 **다음** 을 선택합니다.

1. 계획 상태에서 정의한 [VMware HCX 관리 네트워크 세그먼트](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments)를 선택합니다. 그런 후 **다음** 을 선택합니다.  

1. **템플릿 사용자 지정** 에서 필요한 모든 정보를 입력하고, **다음** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="템플릿을 사용자 지정하는 상자의 스크린샷" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. 확인한 다음, **마침** 을 선택하여 VMware HCX Connector OVA를 배포합니다.

   >[!IMPORTANT]
   >가상 어플라이언스를 수동으로 설정해야 합니다.  전원을 켜고 10~15분 정도 기다린 후에 다음 단계로 진행하세요.


## <a name="activate-vmware-hcx"></a>VMware HCX 활성화

VMware HCX Connector OVA 온-프레미스를 배포하고 어플라이언스를 시작했으면 활성화 준비가 완료된 것입니다. 먼저, Azure VMware 솔루션 포털에서 라이선스 키를 가져온 다음 VMware HCX Manager에서 활성화해야 합니다. 마지막으로, 배포된 각 온-프레미스 HCX 커넥터에 대한 키가 필요합니다.

1. Azure VMware Solution 프라이빗 클라우드에서 **관리** > **Add-ons** > **Migration using HCX** 를 선택합니다. **활성화 키** 를 복사합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/hcx-activation-key.png" alt-text="활성화 키를 보여주는 스크린샷.":::   

1. `https://HCXManagerIP:9443`에서 **관리자** 자격 증명을 사용하여 온-프레미스 VMware HCX Manager에 로그인합니다.  VMware HCX Manager IP 주소에 `9443` 포트 번호를 포함해야 합니다.

   >[!TIP]
   >VMware HCX Manager OVA 파일을 배포하는 중에 **관리자** 사용자 암호를 정의했습니다.

1. **라이선스** 에서 **HCX Advanced 키** 에 대한 키를 입력하고, **활성화** 를 선택합니다.

    >[!IMPORTANT]
    >VMware HCX Manager는 인터넷 액세스가 가능하거나 프록시가 구성되어 있어야 합니다.

1. **데이터 센터 위치** 에서, VMware HCX Manager를 온-프레미스에 설치할 가장 가까운 위치를 입력합니다. 그런 다음, **계속** 을 선택합니다.

1. **시스템 이름** 에서 이름을 수정하거나 기본값을 적용하고, **계속** 을 선택합니다.

1. **예, 계속** 을 선택합니다.

1. **vCenter에 연결** 에서, vCenter 서버의 FQDN 또는 IP 주소와 적절한 자격 증명을 입력한 다음, **계속** 을 선택합니다.

   >[!TIP]
   >vCenter Server는 VMware HCX Connector를 데이터 센터에 배포한 위치입니다.

1. **SSO/PSC 구성** 에서, Platform Services Controller의 FQDN 또는 IP 주소를 입력한 다음, **계속** 을 선택합니다.

   >[!NOTE]
   >일반적으로 vCenter FQDN 또는 IP 주소와 동일합니다.

1. 입력한 정보가 올바른지 확인하고 **다시 시작** 을 선택합니다.

   >[!NOTE]
   >다시 시작한 후 다음 단계를 진행하는 메시지가 표시될 때까지 약간 시간이 걸립니다.

서비스가 다시 시작되면 나타나는 화면에 vCenter가 녹색으로 표시됩니다. vCenter와 SSO 모두 이전 화면과 동일하게 적절한 구성 매개 변수를 포함하고 있어야 합니다.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="vCenter 상태가 녹색인 대시보드의 스크린샷" lightbox="media/tutorial-vmware-hcx/activation-done.png":::


## <a name="uninstall-hcx-advanced"></a>HCX Advanced 제거

포털에서 HCX Advanced를 제거할 수 있습니다. 그러면 기존 페어링 및 소프트웨어가 제거됩니다. 

>[!NOTE]
>HCX 가상 어플라이언스에서 사용하는 프라이빗 클라우드에 리소스를 반환하는 데 30분 정도 걸릴 수 있습니다. 

1. 진행 중인 마이그레이션이 없는지 확인하세요.

1. L2 확장이 더 이상 필요하지 않거나 네트워크가 대상에 "스트레치 해제"되었는지 확인합니다. 

1. MON을 사용하는 워크로드의 경우, 기본 게이트웨이를 제거했는지 확인합니다. 그렇지 않으면, 작업이 통신하거나 작동하지 않을 수 있습니다.  

1. Azure VMware Solution 프라이빗 클라우드에서 **관리** > **Add-ons** 를 선택합니다.

1. **HCX Workload Mobility** 에 대한 **시작하기** 를 선택하고 **삭제** 를 선택합니다.
   
1. **예** 를 입력하여 삭제를 확인하세요.

이 시점에서, HCX Advanced에는 더 이상 vCenter 플러그인이 없으며 필요한 경우 언제든지 다시 설치할 수 있습니다.


## <a name="next-steps"></a>다음 단계

다음 자습서를 계속 진행하여 VMware HCX Connector를 구성합니다.  VMware HCX Connector를 구성한 후에, 가상 머신(VM) 생성 및 마이그레이션을 위한 프로덕션 준비 환경을 사용할 수 있습니다. 


>[!div class="nextstepaction"]
>[Azure VMware Solution에서 VMware HCX 구성](configure-vmware-hcx.md)
