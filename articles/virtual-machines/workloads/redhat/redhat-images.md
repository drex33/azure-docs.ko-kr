---
title: Azure의 Red Hat Enterprise Linux 이미지 개요
description: Microsoft Azure의 Red Hat Enterprise Linux 이미지에 대해 알아봅니다.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 970e0931556a787b461b8c614b41fd88271bdf1c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469123"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux 이미지 개요

이 문서에서는 Azure Marketplace에서 사용할 수 있는 RHEL(Red Hat Enterprise Linux) 이미지와 명명 및 보존 정책을 설명합니다.

모든 RHEL 버전의 Red Hat 지원 정책에 대한 자세한 내용은 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata)를 참조하세요. 가격 책정에 대한 자세한 내용은 [Azure 가격 계산기](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)를 참조하세요.

>[!IMPORTANT]
> Azure Marketplace에서 현재 사용할 수 있는 RHEL 이미지는 BYOS(Bring-Your-Own-Subscription) 또는 종량제 라이선스 모델을 지원합니다. BYOS와 종량제 라이선스 간 동적 전환은 [Azure 하이브리드 사용 혜택](../../linux/azure-hybrid-benefit-linux.md)을 통해 가능합니다.

>[!NOTE]
> Azure Marketplace의 RHEL 이미지와 관련하여 문제가 있는 경우 Microsoft에 지원 티켓을 제출하세요.

## <a name="view-images-available-in-azure"></a>Azure에서 사용 가능한 이미지 보기

Azure Marketplace에서 "Red Hat"을 검색하거나 Azure Portal UI에서 리소스를 만들 때 사용할 수 있는 RHEL 이미지의 하위 집합만 표시됩니다. Azure CLI, PowerShell 및 API를 사용하여 항상 사용 가능한 VM 이미지의 전체 세트를 구할 수 있습니다.

Azure에서 사용 가능한 Red Hat 이미지의 전체 세트를 보려면 다음 명령을 실행합니다.

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>명명 규칙

Azure에서 VM 이미지는 게시자, 제품, SKU 및 버전별로 구성됩니다. 게시자:제품:SKU:버전의 조합은 이미지 URN이며 사용할 이미지를 고유하게 식별합니다.

예를 들어 `RedHat:RHEL:8-LVM:8.1.20200318`은 2020년 3월 18일에 빌드된 RHEL 8.1 LVM 분할 이미지를 나타냅니다.

다음은 RHEL 8.1 VM을 만드는 방법의 샘플입니다.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>"최신" 모니커

Azure REST API는 특정 버전 대신 버전에 대한 "최신" 모니커 사용을 허용합니다. "최신"을 사용하면 지정된 게시자, 제품 및 SKU에 대해 사용 가능한 최신 이미지가 프로비전됩니다.

예를 들어 `RedHat:RHEL:8-LVM:latest`는 사용 가능한 최신 RHEL 8 제품군 LVM 분할 이미지를 나타냅니다.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> 일반적으로 최신을 결정하는 버전의 비교는 [CompareTo 메서드](/dotnet/api/system.version.compareto#system_version_compareto_system_version_)의 규칙을 따릅니다.
이 이미지 버전 비교는 문자열이 아닌 [Version](/dotnet/api/system.version.-ctor) 개체로 값을 비교하여 수행됩니다.

## <a name="rhel-6-image-types"></a>RHEL 6 이미지 형식

>[!NOTE]
> 2020년 12월 30일부로 RHEL 6.10의 EOL(End Of Life)이 시작되었습니다. 지속적인 지원을 받으려면 연장된 수명 주기 지원 단계의 일부로 ELS를 사용합니다. [Red Hat ELS 설명서](./redhat-extended-lifecycle-support.md)를 참조하세요.

RHEL 6.x 이미지 형식이 다음 표에 나와 있습니다.

|게시자 | 제안 | SKU 값 | 버전 | 세부 정보
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 부 버전(예: 6.9) | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 6.9.2018010506) | 모든 표준 RHEL 6.x 이미지가 이 규칙을 따릅니다.
|RedHat | rhel-byos | rhel-raw69 | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 6.9.20181023) | 이 이미지는 RHEL 6.9 BYOS 이미지입니다.
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 6.8.2017053118) | 이 이미지는 SAP 애플리케이션용 RHEL 6.8 이미지입니다. SAP 애플리케이션 리포지토리와 기본 RHEL 리포지토리에 액세스할 수 있습니다.
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 6.7.2017053121) | 이 이미지는 RHEL 6.7 for SAP HANA 이미지입니다. SAP HANA 리포지토리와 기본 RHEL 리포지토리에 액세스할 수 있습니다.

## <a name="rhel-7-image-types"></a>RHEL 7 이미지 형식

RHEL 7.x 이미지의 경우 몇 가지 다른 이미지 형식이 있습니다. 다음 표에서는 제공되는 다양한 이미지 세트를 보여줍니다. 전체 목록을 보려면 Azure CLI 명령 `az vm image list --publisher redhat --all`을 사용하세요.

>[!NOTE]
> 달리 명시되지 않은 한 모든 이미지는 LVM으로 분할되며 일반 RHEL 리포지토리에 연결됩니다. 즉, 리포지토리는 EUS(확장 업데이트 지원)가 아니며 SAP용 업데이트 서비스(E4S)가 아닙니다. 앞으로는 LVM 분할 이미지만 게시할 예정이지만 이 결정에 대한 피드백도 환영합니다. 확장 업데이트 지원과 SAP용 업데이트 서비스에 대한 자세한 내용은 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata)를 참조하세요.

|게시자 | 제안 | SKU 값 | 버전 | 세부 정보
|----------|-------|------------|---------|--------
|RedHat | RHEL | 부 버전(예: 7.6) | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 7.6.2019102813) | 2019년 4월 전에 게시된 이미지는 표준 RHEL 리포지토리에 연결됩니다. 2019년 4월 후에 게시된 이미지는 특정 부 버전의 버전 잠금을 허용하기 위해 Red Hat의 EUS 리포지토리에 연결됩니다. 일반 리포지토리를 원하는 고객은 SKU 값에 7-LVM 또는 7-RAW가 있는 이미지를 사용해야 합니다(자세한 내용은 다음 참조). RHEL 7.7 이상의 이미지는 LVM으로 분할됩니다. 이 범주의 다른 모든 이미지는 원시 분할됩니다.
|RedHat | RHEL | 7-RAW | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 7.6.2019102813) | 이러한 이미지는 원시 분할됩니다(예: 논리적 볼륨이 추가되지 않음).
|RedHat | RHEL | 7-RAW-CI | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 7.6.2019072418) | 이러한 이미지는 원시 분할되며(예: 논리적 볼륨이 추가되지 않음) 프로비저닝을 위해 cloud-init를 사용합니다.
|RedHat | RHEL | 7-LVM | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 7.6.2019062414) | 이러한 이미지는 LVM으 분할됩니다.
|RedHat | rhel-byos | rhel-{lvm,raw} | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 7.7.20190819) | 이러한 이미지는 RHEL 7 BYOS 이미지입니다. 리포지토리에 연결되지 않으며 RHEL 프리미엄 요금을 청구하지 않습니다. RHEL BYOS 이미지에 관심이 있는 경우 [액세스를 요청](https://aka.ms/rhel-byos)하세요. SKU 값은 부 버전으로 끝나며 이미지가 원시인지 LVM 분할되는지 나타냅니다. 예를 들어 SKU 값, rhel-lvm77은 LVM 분할 RHEL 7.7 이미지를 나타냅니다.
|RedHat | RHEL | RHEL-SAP | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 7.6.2019071300) | 이러한 이미지는 RHEL for SAP 이미지입니다. SAP HANA 및 애플리케이션 리포지토리와 RHEL E4S 리포지토리에 액세스할 수 있는 권한이 있습니다. 청구에는 기본 계산 요금 외에 RHEL 프리미엄과 SAP 프리미엄이 포함됩니다.
|RedHat | RHEL | RHEL-SAP-HA | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 7.6.2019062320) | 이러한 이미지는 고가용성 및 업데이트 서비스를 갖춘 RHEL for SAP 이미지입니다. SAP HANA 및 애플리케이션 리포지토리, 고가용성 리포지토리 및 RHEL E4S 리포지토리에 액세스할 수 있는 권한이 있습니다. 청구에는 기본 컴퓨팅 요금 외에 RHEL 프리미엄, SAP 프리미엄 및 고가용성 프리미엄이 포함됩니다.
|RedHat | RHEL | RHEL-HA | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 7.6.2019062019) | 이러한 이미지는 고가용성 추가 항목에 액세스할 수 있는 권한도 있는 RHEL 이미지입니다. 고가용성 추가 항목 프리미엄으로 인해 RHEL 및 기본 컴퓨팅 요금 외에 약간의 추가 요금이 부과됩니다.
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 7.3.2017053118) | SAP 애플리케이션과 SAP HANA 리포지토리가 SAP 리포지토리로 결합되었기 때문에 이러한 이미지는 최신 상태가 아닙니다. 이러한 이미지는 RHEL for SAP Applications 이미지입니다. SAP 애플리케이션 리포지토리와 기본 RHEL 리포지토리에 액세스할 수 있습니다.
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 7.3.2018051421) | SAP 애플리케이션과 SAP HANA 리포지토리가 SAP 리포지토리로 결합되었기 때문에 이러한 이미지는 최신 상태가 아닙니다. 이러한 이미지는 RHEL for SAP HANA 이미지입니다. SAP HANA 리포지토리와 기본 RHEL 리포지토리에 액세스할 수 있습니다.

## <a name="rhel-8-image-types"></a>RHEL 8 이미지 형식

>[!NOTE]
> Red Hat은 Grubby를 사용하여 RHEL 8+에서 커널 명령줄 매개 변수를 구성할 것을 권장합니다. 자세한 내용은 [5장. 커널 명령줄 매개 변수 구성 Red Hat Enterprise Linux 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel)을 참조하세요.

RHEL 8 이미지 형식에 대한 세부 정보는 다음과 같습니다.

|게시자 | 제안 | SKU 값 | 버전 | 세부 정보
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 8.0.20191023) | 이러한 이미지는 표준 Red Hat 리포지토리에 연결된 RHEL 8 LVM 분할 이미지입니다.
|RedHat | RHEL | 8-gen2 | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 8.0.20191024) | 이러한 이미지는 표준 Red Hat 리포지토리에 연결된 Hyper-V 2세대 RHEL 8 LVM 분할 이미지입니다. Azure의 2세대 VM에 대한 자세한 내용은 [Azure의 2세대 VM 지원](../../generation-2.md)을 참조하세요.
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 8.1.2021012201) | 이러한 이미지는 RHEL for SAP Applications 이미지입니다. SAP 애플리케이션 리포지토리와 기본 RHEL 리포지토리에 액세스할 수 있습니다.
|RedHat | RHEL | RHEL-SAP-HA | RHEL 부 버전과 게시된 날짜를 연결한 값(예: 8.1.2021010602) | 이러한 이미지는 고가용성 및 업데이트 서비스를 갖춘 RHEL for SAP 이미지입니다. SAP 솔루션 및 애플리케이션 리포지토리, 고가용성 리포지토리 및 RHEL E4S 리포지토리에 액세스할 수 있는 권한이 있습니다. 청구에는 기본 컴퓨팅 요금 외에 RHEL 프리미엄, SAP 프리미엄 및 고가용성 프리미엄이 포함됩니다.

## <a name="rhel-extended-support-add-ons"></a>RHEL 확장 지원 추가 항목

### <a name="extended-life-cycle-support"></a>연장된 수명 주기 지원

ELS(연장된 수명 주기 지원) 추가 항목은 EOL(수명 종료)에 도달한 릴리스에 대해 중요하고 보안 픽스를 사용하도록 설정하는 선택적 구독입니다. RHEL EUS에 대한 자세한 내용은 [Red Hat 설명서](https://access.redhat.com/support/policy/updates/errata#Extended_Life_Cycle_Support)에서 확인할 수 있습니다.

ELS는 현재 RHEL 6.10에만 사용할 수 있습니다. 종량제 이미지의 경우 [Red Hat ELS 설명서](./redhat-extended-lifecycle-support.md)의 단계에 따라 ELS를 사용하도록 설정할 수 있습니다.

이전 버전에서 실행 중인 경우 ELS를 사용하려면 먼저 RHEL 6.10으로 업그레이드해야 합니다.

### <a name="extended-update-support"></a>연장된 업데이트 지원

2019년 4월부터 기본적으로 EUS 리포지토리에 연결된 RHEL 이미지를 사용할 수 있습니다. RHEL EUS에 대한 자세한 내용은 [Red Hat 설명서](https://access.redhat.com/articles/rhel-eus)에서 확인할 수 있습니다.

EUS 리포지토리로 전환이 가능하며 지원됩니다. VM을 EUS로 전환하는 방법에 대한 지침과 EUS 지원 기간 종료 날짜에 대한 자세한 내용은 [RHEL EUS 및 버전 잠금 RHEL VM](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)을 참조하세요.

>[!NOTE]
> EUS는 RHEL Extras에서 지원되지 않습니다. 즉, 일반적으로 RHEL Extras 채널에서 사용할 수 있는 패키지를 설치하는 경우 EUS에서 이 작업을 수행할 수 없습니다. Red Hat 제품 수명 주기에 대한 자세한 내용은 [Red Hat Enterprise Linux Extras 수명 주기](https://access.redhat.com/support/policy/updates/extras/)를 참조하세요.

#### <a name="differentiate-between-regular-and-eus-images"></a>일반 이미지와 EUS 이미지 구분

EUS 리포지토리에 연결된 이미지를 사용하려는 고객은 SKU에 RHEL 부 버전 번호가 있는 RHEL 이미지를 사용해야 합니다.

예를 들어 다음과 같은 2개의 RHEL 7.4 이미지를 사용할 수 있음을 확인할 수 있습니다.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

이 경우 `RedHat:RHEL:7.6:7.6.2019102813`은 기본적으로 EUS 리포지토리에 연결됩니다. SKU 값은 7.4입니다. 그리고 `RedHat:RHEL:7-LVM:7.6.2019062414`는 기본적으로 비 EUS 리포지토리에 연결됩니다. SKU 값은 7-LVM입니다.

일반(비 EUS) 리포지토리를 사용하려면 SKU에 부 버전 번호가 없는 이미지를 사용합니다.

#### <a name="rhel-images-with-eus"></a>EUS를 사용한 RHEL 이미지

다음 표의 정보는 EUS 리포지토리에 연결된 RHEL 이미지에 적용됩니다.

>[!NOTE]
> 이 문서를 작성할 당시에는 RHEL 7.4 이상 부 버전에서만 EUS를 지원합니다. RHEL 7.3 이하에 대해서는 EUS가 더 이상 지원되지 않습니다.
>
> RHEL EUS 가용성에 대한 자세한 내용은 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata)를 참조하세요.

부 버전 |EUS 이미지 예제              |EUS 상태                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | 2019년 4월 이후에 게시된 이미지는 기본적으로 EUS입니다.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 2019년 6월 이후에 게시된 이미지는 기본적으로 EUS입니다. |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 2019년 5월 이후에 게시된 이미지는 기본적으로 EUS입니다. |
RHEL 8.0      |해당 없음                            | Red Hat에서는 EUS를 사용할 수 없습니다.                               |

### <a name="update-services-for-sap"></a>SAP용 업데이트 서비스

최신 RHEL for SAP 이미지는 Update Services for SAP Solutions 구독(E4S)에 연결됩니다. E4S에 대한 자세한 내용은 Red Hat [설명서](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)를 참조하세요.

#### <a name="rhel-images-with-e4s"></a>E4S를 사용한 RHEL 이미지

2019년 12월 후에 생성된 다음 제품의 이미지는 E4S 리포지토리에 연결됩니다.

* RHEL-SAP(RHEL for SAP)
* RHEL-SAP-HA(고가용성 및 업데이트 서비스를 갖춘 RHEL for SAP)

## <a name="other-available-offers-and-skus"></a>다른 사용 가능한 제품 및 SKU

사용 가능한 제품 및 SKU의 전체 목록은 위의 표에 나열된 것 이외에 추가 이미지를 포함할 수 있습니다. 예제는 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`입니다. 이러한 제품은 특정 마켓플레이스 솔루션에 대한 지원을 제공하는 데 사용할 수 있습니다. 또는 미리 보기 및 테스트용으로 게시할 수 있으며, 경고 없이 언제든지 변경 또는 제거될 수 있습니다. 현재 상태가 Microsoft 또는 Red Hat에서 공개적으로 문서화되지 않은 경우 이러한 제품을 사용하지 마세요.

## <a name="publishing-policy"></a>정책 게시

Microsoft 및 Red Hat은 특정 CVE(일반 취약성 및 노출)를 해결하기 위해 필요에 따라 또는 가끔 구성 변경 또는 업데이트를 위해 새로운 부 버전이 출시될 때 이미지를 업데이트합니다. CVE 픽스의 릴리스 또는 가용성 이후 영업일 3일 이내에 가능한 빨리 업데이트된 이미지를 제공하기 위해 노력합니다.

지정된 이미지 제품군의 현재 부 릴리스만 업데이트합니다. 최신 부 버전 릴리스로 이전 부 버전 업데이트를 중지합니다. 예를 들어 RHEL 7.6이 릴리스되어 RHEL 7.5 이미지는 더 이상 업데이트되지 않습니다.

>[!NOTE]
> RHEL 종량제 이미지에서 프로비전된 활성 Azure VM은 Azure RHUI에 연결되며 Red Hat에서 릴리스되고 Azure RHUI에 복제되는 대로 즉시 업데이트과 픽스를 받을 수 있습니다. 타이밍은 일반적으로 Red Hat의 공식 릴리스 후 24시간 이내입니다. 이러한 VM에는 업데이트를 가져오기 위해 새로 게시된 이미지가 필요하지 않습니다. 고객은 업데이트 시작 시점을 완전히 제어할 수 있습니다.

## <a name="image-retention-policy"></a>이미지 보존 정책

현재 정책은 이전에 게시된 모든 이미지를 유지하는 것입니다. 모든 종류의 문제를 일으키는 것으로 알려진 이미지를 제거할 권리를 보유합니다. 예를 들어 후속 플랫폼 또는 구성 요소 업데이트로 인한 잘못된 구성의 이미지는 제거될 수 있습니다. 제거될 수 있는 이미지는 이미지 제거 전 최대 30일 알림을 제공하도록 현재 Azure Marketplace 정책을 따릅니다.

## <a name="next-steps"></a>다음 단계

* Azure의 RHEL 이미지 전체 목록을 보려면 [Azure에서 사용 가능한 RHEL(Red Hat Enterprise Linux) 이미지](./redhat-imagelist.md)를 참조하세요.
* Azure Red Hat 업데이트 인프라에 대해 자세히 알아보려면 [Azure의 주문형 RHEL VM용 Red Hat 업데이트 인프라](./redhat-rhui.md)를 참조하세요.
* RHEL BYOS 제품에 대해 자세히 알아보려면 [Azure의 Red Hat Enterprise Linux BYOS 골드 이미지](./byos.md)를 참조하세요.
* 모든 RHEL 버전의 Red Hat 지원 정책에 대한 자세한 내용은 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata)를 참조하세요.
