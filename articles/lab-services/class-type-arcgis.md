---
title: Azure Lab Services를 사용 하 여 ArcMap\ArcGIS Desktop에 대 한 랩 설정 Microsoft Docs
description: ArcGIS를 사용 하 여 클래스에 대 한 랩을 설정 하는 방법을 알아봅니다.
author: nicolela
ms.topic: how-to
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: dad2beae5248d8f73ceb2dfa747d0c4f45333a80
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177197"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>ArcMap\ArcGIS Desktop에 대 한 랩 설정

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview)는 일종의 GIS(지리 정보 시스템)입니다.  ArcGIS은 지도 분석 및 ESRI ( [환경 시스템 연구 협회](https://www.esri.com/en-us/home) )에서 제공 하는 지리 데이터로 작업 하는 데 사용 됩니다.  ArcGIS Desktop에는 여러 응용 프로그램이 포함 되어 있지만이 문서에서는 ArcMap 사용을 위한 랩을 설정 하는 방법을 보여 줍니다.  [Arcmap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) 은 2d 맵을 만들고 편집 하 고 분석 하는 데 사용 됩니다.

## <a name="lab-configuration"></a>랩 구성

ArcMap 사용을 위한 랩을 설정 하려면 Azure 구독 및 랩 계정이 필요 합니다.  Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다.  새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정](tutorial-setup-lab-account.md)을 참조하세요.  기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

다음 표에서 설명하는 것과 같이 랩 계정 설정을 사용합니다.  Azure Marketplace 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Azure Marketplace 이미지 지정](./specify-marketplace-images.md)을 참조하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
|Marketplace 이미지| 랩 계정 내에서 사용 하기 위해 Windows 10 Pro 또는 Windows 10 Pro KN 이미지를 사용 하도록 설정 합니다.|

### <a name="licensing-server"></a>라이선스 서버

ArcGIS Desktop이 제공 하는 한 가지 유형의 라이선스는 [동시 사용 라이선스](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm)입니다.  이렇게 하려면 라이선스 서버에 ArcGIS 라이선스 관리자를 설치 해야 합니다.  라이선스 관리자는 동시에 실행할 수 있는 소프트웨어의 복사본 수를 추적 합니다.  서버에서 라이선스 관리자를 설정 하는 방법에 대 한 자세한 내용은 [라이선스 관리자 가이드](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm)를 참조 하십시오.

라이선스 서버는 일반적으로 온-프레미스 네트워크 또는 Azure 가상 컴퓨터에서 호스트 되는 Azure 가상 네트워크 내에 있습니다.  라이선스 서버를 설정한 후에는 [랩 계정](./tutorial-setup-lab-account.md)으로 [가상 네트워크를 피어](./how-to-connect-peer-virtual-network.md) 링 해야 합니다.  랩 VM과 라이선스 서버가 서로 액세스할 수 있도록 랩을 만들기 ‘전에’ 네트워크 피어링을 수행해야 합니다.

자세한 내용은 [라이선스 서버를 공유 리소스로 설정](how-to-create-a-lab-with-shared-resource.md)을 참조하세요.

### <a name="lab-settings"></a>랩 설정

ArcGIS Desktop에 사용 하도록 권장 되는 VM (가상 머신)의 크기는 학생 들이 사용 하는 응용 프로그램, 확장 및 특정 버전에 따라 달라 집니다.  또한 VM 크기는 학생이 수행 해야 하는 워크 로드에 따라 달라 집니다.  VM 크기를 식별 하는 데 도움이 필요 하면 [ArcGIS Desktop system 요구 사항](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) 을 참조 하세요.  잠재적 VM 크기를 확인 한 후에는 학생의 워크 로드를 테스트 하 여 적절 한 성능을 확보 하는 것이 좋습니다.

이 문서에서는 다른 ArcGIS Desktop 확장이 사용 되지 않는다고 가정 하 고 [ArcMap의 버전 10.7.1](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm)에 대해 [ **중간** VM 크기](administrator-guide.md#vm-sizing) 를 사용 하는 것이 좋습니다.  그러나 클래스의 요구에 따라 **큰** 또는 **Small\Medium GPU (시각화)** VM 크기가 필요할 수 있습니다.  예를 들어 ArcGIS Desktop에 포함 된 [공간 분석가 확장](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) 은 성능 향상을 위해 gpu를 지원 하지만 gpu를 사용할 필요는 없습니다.

| 랩 설정 | 값 및 설명 |
| ------------ | ------------------ |
|Virtual Machine 크기| **보통**.  관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다.|  

### <a name="template-machine"></a>템플릿 머신

이 섹션의 단계에서는 템플릿 VM을 설정하는 방법을 보여 줍니다.

1.  템플릿 VM을 시작 하 고 RDP를 사용 하 여 컴퓨터에 연결 합니다.

2.  ESRI의 지침을 사용 하 여 ArcGIS 데스크톱 구성 요소를 다운로드 하 고 설치 합니다.  이러한 단계에는 동시 사용 라이선스에 라이선스 관리자를 할당 하는 작업이 포함 됩니다. 
    - [ArcGIS Desktop 설치 및 구성 소개](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  학생용 외부 백업 저장소를 설정 합니다.  학생은 할당 된 VM에 파일을 직접 저장할 수 있습니다. 이렇게 하면 모든 변경 내용이 세션 전체에 저장 되기 때문입니다.  그러나 학생 들이 다음과 같은 이유로 VM에서 외부에 있는 저장소에 작업을 백업 하는 것이 좋습니다.
    - 학생 들이 클래스 및 랩이 종료 된 후 해당 작업에 액세스할 수 있게 합니다.  
    - 학생이 VM을 잘못 된 상태로 가져오고 해당 이미지를 [다시 설정](how-to-set-virtual-machine-passwords.md#reset-vms)해야 하는 경우.

    ArcGIS를 사용 하면 각 학생이 각 작업 세션이 끝날 때 다음 파일을 백업 해야 합니다.

    - mxd 파일-프로젝트의 레이아웃 정보를 저장 합니다.
    - ArcGIS에 의해 생성 된 모든 데이터를 저장 하는 파일 geodatabases
    - 학생 들이 래스터 파일, 셰이프 파일, GeoTIFF 등을 사용 하 여 사용할 수 있는 기타 데이터

    백업 저장소에 OneDrive를 사용 하는 것이 좋습니다.  템플릿 VM에 OneDrive을 설정 하려면 [OneDrive 설치 및 구성](how-to-prepare-windows-template.md#install-and-configure-onedrive)문서의 단계를 따르세요. 

4.  마지막으로 템플릿 VM을 [게시](how-to-create-manage-template.md#publish-the-template-vm) 하 여 학생의 vm을 만듭니다.

### <a name="auto-shutdown-and-disconnect-settings"></a>자동 종료 및 설정 연결 끊기

랩의 [자동 종료 및 연결 끊기 설정은](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) 사용 하지 않을 때 학생의 VM이 종료 되도록 하는 데 도움이 됩니다.  이러한 설정은 해당 VM이 작업 중간에 종료 되지 않도록 학생이 수행 하는 작업 유형에 따라 설정 해야 합니다.  예를 들어 **가상 컴퓨터가 유휴 상태일 때 사용자 연결 끊기** 설정은 지정 된 시간 동안 마우스 또는 키보드 입력이 검색 되지 않은 후 RDP 세션에서 학생의 연결을 끊습니다.  이 설정은 장기 쿼리 실행 또는 렌더링 대기와 같이 학생이 적극적으로 마우스나 키보드를 사용 하지 않는 워크 로드에 충분 한 시간을 허용 해야 합니다.

ArcGIS의 경우 이러한 설정에 대해 다음 값을 권장 합니다.
- 가상 컴퓨터가 유휴 상태일 때 사용자 연결 끊기
    - 유휴 상태가 검색 된 후 30 분
- 사용자가 연결을 끊을 때 가상 머신 종료
    - 사용자 연결을 끊은 후 15 분

## <a name="cost"></a>Cost

이 클래스의 가능한 예상 비용을 살펴보겠습니다. 이러한 예상치에는 라이선스 서버를 실행하는 비용이 포함되지 않습니다. 25명의 학생이 있는 클래스를 예로 들겠습니다. 예약된 수업 시간은 20시간입니다. 예약된 수업 시간 외에도 숙제 또는 과제에 사용하도록 각 학생에게 10시간이 할당됩니다. 선택한 가상 컴퓨터 크기는 42 lab 단위인 **Medium** 입니다.

25 개 학생 \* (20 개의 예약 된 시간 + 10 할당량 시간) \* 42 랩 단위 * 0.01 usd/시간 = 315.00 USD

>[!IMPORTANT]
> 예상 비용은 예제 용도로만 사용됩니다.  가격 책정에 대한 최신 세부 정보는 [Azure Lab Services 가격](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.  

## <a name="next-steps"></a>다음 단계

다음 단계는 모든 랩 설정에 공통됩니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)