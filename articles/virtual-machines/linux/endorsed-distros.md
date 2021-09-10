---
title: Azure 보증 Linux 배포판
description: Ubuntu, CentOS, Oracle 및 SUSE 관련 지침을 포함하여 Azure에서 Linux의 인증 배포를 수행하는 방법에 대해 알아봅니다.
services: virtual-machines
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: guybo
ms.openlocfilehash: b9262d512ed27419bab38b3ad26e1bae8cddee0d
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696192"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Azure 보증 Linux 배포판

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합: heavy_check_mark: 단일 확장 집합 

파트너는 Azure Marketplace에서 Linux 이미지를 제공합니다. Microsoft는 다양한 Linux 커뮤니티와 함께 작업하여 보증 배포 목록에 다양한 옵션을 추가합니다. Marketplace에서 제공하지 않는 배포판의 경우 [Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드](./create-upload-generic.md)의 지침에 따라 항상 고유한 Linux를 가져올 수 있습니다.

## <a name="supported-distributions-and-versions"></a>지원되는 배포판 및 버전

다음 표에서 Linux 배포판 및 Azure에서 지원 되는 버전을 나열합니다. 자세한 내용은 [Microsoft Azure의 Linux 이미지 지원](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)을 참조하세요.

Hyper-V 및 Azure용 LIS(Linux 통합 서비스) 드라이버는 Microsoft가 업스트림 Linux 커널로 직접 제공되는 커널 모듈입니다. 일부 LIS 드라이버는 기본적으로 배포 커널에 빌드됩니다. RHEL(Red Hat Enterprise)/CentOS를 기반으로 둔 이전 배포는 [Hyper-V 및 Azure용 Linux Integration Services 버전 4.2](https://www.microsoft.com/download/details.aspx?id=55106)에서 별도의 다운로드로 제공됩니다. 자세한 내용은 [Linux kernel 요구 사항](create-upload-generic.md#linux-kernel-requirements)을 참조하세요.

Azure Linux 에이전트는 Azure Marketplace 이미지에 이미 사전 설치되어 있으며 일반적으로 배포판의 패키지 리포지토리에서 제공합니다. 소스 코드는 [GitHub](https://github.com/azure/walinuxagent)에서 찾을 수 있습니다.

| 배포 | 버전 | 드라이버 | 에이전트 |
| --- | --- | --- | --- |
| Rogue Wave Software의 CentOS(이전의 OpenLogic) |CentOS 6.x, 7.x, 8.x |CentOS 6.3: [LIS 다운로드](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: 커널에 있음 |패키지: "WALinuxAgent"의 [리포지토리](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> CoreOS는 이제 2020년 5월 26일에 [수명이 종료](https://coreos.com/os/eol/)됩니다. |더 이상 사용할 수 없음 | | |
| Credativ의 Debian |8.x, 9.x, 10.x |커널에 있음 |패키지: "waagent"에서 리포지토리의 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
|Kinvolk의 Flatcar Container Linux| Pro, Stable, Beta| 커널에 있음 | wa-linux-agent가 이미 /usr/share/oem/bin/waagent에 설치되어 있습니다. |
| Oracle의 Oracle Linux |6.x, 7.x, 8.x |커널에 있음 |패키지: "WALinuxAgent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| [Red Hat의 Red Hat Enterprise Linux](../workloads/redhat/overview.md) |6.x, 7.x, 8.x |커널에 있음 |패키지: "WALinuxAgent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE의 SUSE Linux Enterprise |SLES/SLES for SAP 11.x, 12.x, 15.x <br/> [SUSE 퍼블릭 클라우드 이미지 수명 주기](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |커널에 있음 |패키지:<p> 11의 경우 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 리포지토리에 있음<br>12의 경우 "python-azure-agent" 아래의 "퍼블릭 클라우드" 모듈에 포함됨<br/>소스 코드: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| SUSE의 openSUSE |openSUSE Leap 15.x |커널에 있음 |패키지: "python-azure-agent"의 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Canonical의 Ubuntu |Ubuntu Server 및 Pro 16.x, 18.x, 20.x<p>Ubuntu 12.04 및 14.04에 대한 확장 지원에 대한 정보는 [Ubuntu 확장 보안 유지 관리](https://www.ubuntu.com/esm)에서 찾을 수 있습니다. |커널에 있음 |패키지: "WALinuxAgent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>이미지 업데이트 주기

Azure를 사용하려면 보증 Linux 배포판의 게시자가 분기별로 또는 더 빠른 주기로 최신 패치와 보안 픽스를 사용하여 Azure Marketplace의 이미지를 정기적으로 업데이트해야 합니다. Marketplace의 업데이트된 이미지는 새 버전의 이미지 SKU로 고객에게 자동으로 제공됩니다. Linux 이미지를 찾는 방법에 대한 자세한 내용은 [Azure Marketplace에서 Linux VM 이미지 찾기](./cli-ps-findimage.md)를 참조하세요.

## <a name="azure-tuned-kernels"></a>Azure-Tuned 커널

Azure는 다양한 보증 Linux 배포판을 사용하여 Azure Marketplace에 게시된 이미지를 최적화합니다. 이 협업의 한 가지 측면은 Azure 플랫폼에 최적화되고 Linux 배포판의 완전히 지원되는 구성 요소로 제공되는 "튜닝된" Linux 커널을 개발하는 것입니다. Azure-Tuned 커널에서는 배포판에서 제공하는 기본 또는 일반 커널에 비해 더 빠른 주기로(일반적으로 분기별로) 기능 및 성능 향상을 통합합니다.

대부분의 경우 이러한 커널은 Azure Marketplace의 기본 이미지에 미리 설치되어 있으므로 고객은 이러한 최적화된 커널의 이점을 즉시 얻을 수 있습니다. 이러한 Azure-Tuned 커널에 대한 자세한 내용은 다음 링크에서 확인할 수 있습니다.

- [CentOS Azure-Tuned 커널 - CentOS 가상화 SIG를 통해 사용 가능](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian 클라우드 커널 - Azure의 Debian 10 및 Debian 9 "backports" 이미지에서 사용할 수 있음](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [SLES Azure-Tuned 커널](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Ubuntu Azure-Tuned 커널](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)
- [Flatcar Container Linux Pro](https://azuremarketplace.microsoft.com/marketplace/apps/kinvolk.flatcar_pro)

## <a name="partners"></a>파트너

### <a name="coreos"></a>CoreOS

CoreOS는 2020년 5월 26일에 [수명이 종료](https://coreos.com/os/eol/)될 예정입니다.
Microsoft에는 CoreOS 사용자를 위한 2개의 마이그레이션 채널이 있습니다.

- Kinvolk의 Flatcar("Flatcar의 Flatcar Container Linux" 항목 참조)
- [Fedora Core OS](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/)(고객이 자신의 이미지를 업로드해야 함. [마이그레이션 설명서](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/) 참조)

### <a name="credativ"></a>credativ

[https://www.credativ.de/en/portfolio/support/open-source-support-center/](https://www.credativ.de/en/portfolio/support/open-source-support-center/)

credativ는 무료 소프트웨어를 사용하여 전문 솔루션의 개발 및 구현을 전문으로 하는 독립적인 컨설팅 및 서비스 회사입니다. 선도적인 오픈 소스 전문 회사인 credativ는 지원을 받는 많은 IT 부서로부터 국제적으로 인정을 받고 있습니다. credativ는 Microsoft와 함께 Debian 이미지를 준비하고 있습니다. 이 이미지 특별히 Azure에서 실행되도록 설계되었으며 플랫폼을 통해 쉽게 관리할 수 있습니다. 또한 credativ는 오픈소스 지원 센터를 통해 Azure용 Debian 이미지의 장기 유지 관리 및 업데이트를 지원합니다.

### <a name="kinvolk"></a>Kinvolk
[https://www.kinvolk.io/flatcar-container-linux/](https://www.kinvolk.io/flatcar-container-linux/)

Kinvolk는 Flatcar Container Linux를 지원하는 회사로, 컨테이너화된 애플리케이션에 대한 최소, 변경 불가, 자동 업데이트를 위해 기존 CoreOS 비전을 계속 사용합니다. 최소 배포판인 Flatcar에는 컨테이너를 배포하는 데 필요한 패키지만 포함되어 있습니다. 변경 불가 파일 시스템은 일관성과 보안을 보장하는 반면, 자동 업데이트 기능을 사용하면 최신 보안 픽스를 사용하여 항상 최신 상태로 유지할 수 있습니다. 

Flatcar Container Linux는 연중무휴 응답, 보안 및 기술 경고, 장기 지원 채널을 포함한 Azure에 최적화된 전용 이미지를 포함하는 선택적 상용 지원 구독을 제공하는 Kinvolk의 Linux 및 컨테이너 기술 전문가로 구성된 글로벌 팀이 지원합니다.


### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle의 전략은 퍼블릭 및 프라이빗 클라우드에 대해 광범위한 솔루션 포트폴리오를 제공하는 것입니다. 이 전략을 통해 고객은 Oracle 클라우드 및 다른 클라우드에 Oracle 소프트웨어를 배포하는 방법을 유연하게 선택할 수 있습니다. Oracle과 Microsoft의 파트너 관계를 통해 고객은 Oracle의 확실한 인증 및 지원으로 Microsoft 퍼블릭 및 프라이빗 클라우드에 Oracle 소프트웨어를 배포할 수 있습니다.  Oracle 퍼블릭 및 프라이빗 클라우드 솔루션에 대한 Oracle의 약속과 투자는 변하지 않습니다.

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat은 세계 최고의 오픈 소스 솔루션 공급업체로, Fortune지 선정 500대 기업의 90% 이상이 비즈니스 당면 과제를 해결하고, IT와 비즈니스 전략을 연결하고, 기술의 미래를 준비하도록 지원합니다. 이를 위해 Red Hat은 개방형 비즈니스 모델과 경제적이고 예측 가능한 구독 모델을 통해 안전한 솔루션을 제공합니다.

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server on Azure는 클라우드 컴퓨팅에 대해 우수한 안정성과 보안을 제공하는 검증된 플랫폼입니다. SUSE의 다양한 Linux 플랫폼은 Azure 클라우드 서비스와 자연스럽게 통합되어 쉽게 관리할 수 있는 클라우드 환경을 제공합니다. 1,800곳이 넘는 SUSE Linux Enterprise Server의 독립 소프트웨어 공급업체에서 9,200개 이상의 인증된 애플리케이션을 통해 SUSE는 데이터 센터에서 지원되는 실행 중인 작업이 확실히 Azure에 배포될 수 있도록 합니다.

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical 엔지니어링과 개방형 커뮤니티 거버넌스로 인해 소비자용 개인 클라우드 서비스를 비롯한 클라이언트, 서버 및 클라우드 컴퓨팅에서 Ubuntu가 성공할 수 있었습니다. Canonical은 전화에서 클라우드까지 적용되는 Ubuntu의 무료 통합 플랫폼에 대한 비전이 실현되도록 전화, 태블릿, TV 및 데스크톱에 대해 일관된 인터페이스 제품군을 제공합니다. 이러한 비전으로 인해 퍼블릭 클라우드 공급자에서 가전제품 제조업체에 이르는 다양한 기관에서 Ubuntu를 우선적으로 선택하고 있으며 개별 기술자 사이에서도 Ubuntu를 선호하게 되었습니다.

Canonical은 전 세계의 개발자 및 엔지니어링 센터와 함께 PC, 서버 및 핸드헬드 디바이스 업계에 Ubuntu 솔루션을 제공하기 위해 하드웨어 제조업체, 콘텐츠 공급자 및 소프트웨어 개발자와 유일하게 파트너 입장에 있습니다.
