---
title: Azure VMware Solution by CloudSimple - VM 템플릿을 사용하여 Azure에서 가상 머신 만들기
description: CloudSimple 프라이빗 클라우드용 VMware 인프라에서 VM 템플릿을 사용하여 Azure에서 가상 머신을 만드는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cc382f0316f5f7f0d80d2d1fc5cf9612d623e754
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618293"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>VMware 인프라에서 VM 템플릿을 사용하여 Azure에서 가상 머신 만들기

CloudSimple 관리자가 구독에 대해 사용하도록 설정한 VMware 인프라에서 VM 템플릿을 사용하여 Azure Portal에서 가상 머신을 만들 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-cloudsimple-virtual-machine"></a>CloudSimple 가상 머신 만들기

1. **모든 서비스** 를 선택합니다.

2. **CloudSimple 가상 머신** 을 검색합니다.

3. **추가** 를 클릭합니다.

    ![CloudSimple 가상 머신 만들기](media/create-cloudsimple-virtual-machine.png)

4. 기본 정보를 입력하고 **다음:크기** 를 클릭합니다.

    > [!NOTE]
    > Azure에서 CloudSimple 가상 머신을 만들려면 VM 템플릿이 필요합니다.  이 VM 템플릿은 프라이빗 클라우드 vCenter에 있어야 합니다.  원하는 운영 체제 및 구성을 사용하여 vCenter UI에서 프라이빗 클라우드에 가상 머신을 만듭니다.  [Virtual Machine을 vSphere Web Client의 템플릿에 복제](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)의 지침을 따라 템플릿을 만듭니다.

    ![CloudSimple 가상 머신 만들기 - 기본 사항](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 필드 | 설명 |
    | ------------ | ------------- |
    | Subscription | 프라이빗 클라우드와 연결된 Azure 구독입니다.  |
    | 리소스 그룹 | VM이 할당될 리소스 그룹입니다. 기존 그룹을 선택하거나 새 그룹을 만들 수 있습니다. |
    | 이름 | VM을 식별하는 이름입니다.  |
    | Location | VM이 호스팅되는 Azure 지역입니다.  |
    | 프라이빗 클라우드 | 가상 머신을 만들려는 CloudSimple 프라이빗 클라우드입니다. |
    | Resource Pool | VM에 대해 매핑된 리소스 풀입니다. 사용 가능한 리소스 풀에서 선택합니다. |
    | vSphere 템플릿 | VM용 vSphere 템플릿입니다.  |
    | 사용자 이름 | VM 관리자의 사용자 이름입니다(Windows 템플릿의 경우).|
    | 암호 <br>암호 확인 | VM 관리자의 암호입니다(Windows 템플릿의 경우).  |

5. VM의 코어 수와 메모리 용량을 선택하고 **다음:구성** 을 클릭합니다. 이진 전환 또는 매개 변수화 없이 하드웨어 가상화를 필요로 하는 애플리케이션을 가상 머신에서 실행할 수 있도록 게스트 운영 체제에 전체 CPU 가상화를 노출하려면 이 확인란을 선택합니다. 자세한 내용은 [VMware 하드웨어 지원 가상화 공개](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html) VMware 문서를 참조하세요.

    ![CloudSimple 가상 머신 만들기 - 크기](media/create-cloudsimple-virtual-machine-size.png)

6. 다음 표에서 설명한 대로 네트워크 인터페이스와 디스크를 구성한 다음, **검토 + 만들기** 를 클릭합니다.

    ![CloudSimple 가상 머신 만들기 - 구성](media/create-cloudsimple-virtual-machine-configurations.png)

    네트워크 인터페이스에 대해 **네트워크 인터페이스 추가** 를 클릭하고, 다음 설정을 구성합니다.

    | 컨트롤 | 설명 |
    | ------------ | ------------- |
    | 이름 | 인터페이스를 식별하는 이름을 입력합니다.  |
    | 네트워크 | vSphere 프라이빗 클라우드에 구성된 분산 포트 그룹 목록에서 선택합니다.  |
    | 어댑터 | VM에 대해 구성된 사용 가능한 종류 목록에서 vSphere 어댑터를 선택합니다. 자세한 내용은 [가상 머신에 대한 네트워크 어댑터 선택](https://kb.vmware.com/s/article/1001805) VMware 기술 자료 문서를 참조하세요. |
    | 부팅 시 전원 켜기 | VM이 부팅되면 NIC 하드웨어를 사용하도록 설정할지 여부를 선택합니다. 기본값은 **사용** 입니다. |

    디스크의 경우 **디스크 추가** 를 클릭하고 다음 설정을 구성합니다.

    | 항목 | Description |
    | ------------ | ------------- |
    | 이름 | 디스크를 식별하는 이름을 입력합니다.  |
    | 크기 | 사용 가능한 크기 중 하나를 선택합니다.  |
    | SCSI 컨트롤러 | 디스크의 SCSI 컨트롤러를 선택합니다.  |
    | 모드 | 디스크에서 스냅샷에 참여하는 방법을 결정합니다. 다음 옵션 중 하나를 선택합니다. <br> - 독립적 영구: 디스크에 기록된 모든 데이터는 영구적으로 기록됩니다.<br> - 독립적 비영구: 전원을 끄거나 가상 머신을 다시 설정하면 디스크에 기록된 변경 내용이 삭제됩니다.  독립적 비영구 모드를 사용하면 항상 동일한 상태에서 VM을 다시 시작할 수 있습니다. 자세한 내용은 [VMware 설명서](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)를 참조하세요.

7. 유효성 검사가 완료되면 설정을 검토하고 **만들기** 를 클릭합니다. 변경하려면 상단의 탭을 클릭하거나 클릭합니다.

    ![CloudSimple 가상 머신 만들기 - 검토](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>CloudSimple 가상 머신의 목록 보기

1. **모든 서비스** 를 선택합니다.

2. **CloudSimple 가상 머신** 을 검색합니다.

3. 만들어진 프라이빗 클라우드를 선택합니다.

    ![CloudSimple 가상 머신의 목록](media/list-cloudsimple-virtual-machines.png)

CloudSimple 가상 머신의 목록에는 Azure Portal에서 만든 가상 머신이 있습니다.  매핑된 vCenter 리소스 풀의 프라이빗 클라우드 vCenter에 만들어진 가상 머신이 목록에 표시됩니다.  
