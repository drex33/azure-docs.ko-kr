---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 SKU | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA 사용할 수 있는 SKU에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKUs, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 5/13/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017, references_regions
ms.openlocfilehash: 3458c818a9d68d458f46196b40f6ede35248cdb9
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110189874"
---
# <a name="available-skus-for-hana-large-instances"></a>HANA 대규모 인스턴스에 사용할 수 있는 SKU

## <a name="baremetal-infrastructure-availability-by-region"></a>지역별 운영 체제 미설치 인프라 가용성

Rev 4.2*를 기반으로 하는 운영 체제 미설치 인프라(SAP HANA 워크로드용으로 인증됨) 서비스는 다음 지역에서 사용할 수 있습니다.
- 서유럽
- 북유럽
- 영역 지원 포함 독일 중서부
- 영역 지원 포함 미국 동부
- 미국 동부 2
- 미국 중남부
- 영역 지원 포함 미국 서부 2

Rev 3*을 기반으로 하는 운영 체제 미설치 인프라(SAP HANA 워크로드용으로 인증됨) 서비스는 다음 지역에서 가용성이 제한됩니다.
- 미국 서부
- 미국 동부 
- 오스트레일리아 동부 
- 오스트레일리아 남동부
- 일본 동부

## <a name="list-of-available-azure-large-instances"></a>사용 가능한 Azure 대규모 인스턴스 목록

다음은 사용 가능한 Azure 대규모 인스턴스(운영 체제 미설치 인프라 인스턴스라고도 함) 목록입니다.

> [!IMPORTANT]
> 목록에 있는 각 대규모 인스턴스 유형에 대한 HANA 인증 상태를 나타내는 첫 번째 열에 주의해야 합니다. 이 열은 문자 **S** 로 시작하는 Azure SKU에 대한 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)와 연관되어야 합니다.


| SAP HANA 인증됨 | 모델 | 총 메모리 | 메모리 DRAM | 메모리 Optane | 스토리지 | 가용성 |
| --- | --- | --- | --- | --- | --- | --- |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | Azure의 SAP HANA S96<br /> –  2 x Intel® Xeon® Processor E7-8890 v4 <br /> 48개 CPU 코어 및 96개 CPU 스레드 |  768 GB | 768 GB | --- | 3.0 TB | 사용 가능 |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | Azure의 SAP HANA S224<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112개 CPU 코어 및 224개 CPU 스레드 |  3.0 TB | 3.0 TB | --- | 6.3TB | 사용 가능 |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | Azure의 SAP HANA S224m<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112개 CPU 코어 및 224개 CPU 스레드 |  6.0 TB | 6.0 TB | --- | 10.5TB | 사용 가능 |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | Azure의 SAP HANA S224om<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112개 CPU 코어 및 224개 CPU 스레드 | 6.0 TB |  3.0 TB |  3.0 TB | 10.5TB | 사용 가능 |
| 아니요 | Azure의 SAP HANA S224oo<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112개 CPU 코어 및 224개 CPU 스레드 | 4.5TB |  1.5 TB |  3.0 TB | 8.4TB | 사용 가능 |
| 아니요 | Azure의 SAP HANA S224ooo<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112개 CPU 코어 및 224개 CPU 스레드 | 7.5TB |  1.5 TB |  6.0 TB | 12.7TB | 사용 가능 |
| 아니요 | Azure의 SAP HANA S224oom<br /> – 4 x Intel® Xeon® Platinum 8276 프로세서 <br /> 112개 CPU 코어 및 224개 CPU 스레드 | 9.0TB |  3.0 TB |  6.0 TB | 14.8TB | 사용 가능 |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | Azure S384에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  4.0 TB | 4.0 TB | --- | 16TB | 사용 가능 |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | Azure S384m에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  6.0 TB | 6.0 TB | --- | 18 TB |  사용 가능  |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | Azure S384xm에서 SAP HANA<br /> – 8 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 192 CPU 코어 및 384 CPU 스레드 |  8.0 TB | 8.0 TB | --- | 22 TB | 사용 가능 |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2411), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | Azure의 SAP HANA S448<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224개 CPU 코어 및 448개 CPU 스레드 | 6.0 TB |  6.0 TB |  --- | 10.5TB | 사용 가능(Rev 4.2에만 해당) |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2410), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | Azure의 SAP HANA S448m<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224개 CPU 코어 및 448개 CPU 스레드 | 12.0 TB |  12.0 TB |  --- | 18.9TB | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S448oo<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224개 CPU 코어 및 448개 CPU 스레드 | 9.0TB |  3.0 TB |  6.0 TB | 14.8TB  | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S448om<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224개 CPU 코어 및 448개 CPU 스레드 | 12.0 TB |  6.0 TB |  6.0 TB | 18.9TB  | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S448ooo<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224개 CPU 코어 및 448개 CPU 스레드 | 15.0TB |  3.0 TB |  12.0 TB | 23.2TB  | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S448oom<br /> – 8 x Intel® Xeon® Platinum 8276 프로세서 <br /> 224개 CPU 코어 및 448개 CPU 스레드 | 18.0TB |  6.0 TB |  12.0 TB | 27.4TB  | 사용 가능(Rev 4.2에만 해당) |
| YES <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | Azure의 SAP HANA S576m<br /> – 12 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 288 CPU 코어 및 576 CPU 스레드 |  12.0 TB | 12.0 TB | --- | 28 TB | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure S576xm에서 SAP HANA<br /> – 12 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 288 CPU 코어 및 576 CPU 스레드 |  18.0TB | 18.0 | --- |  41TB | 사용 가능 |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2409), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | Azure의 SAP HANA S672<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336개 CPU 코어 및 672개 CPU 스레드 | 9.0TB |  9.0TB |  --- | 14.7TB | 사용 가능(Rev 4.2에만 해당) |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2408), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | Azure의 SAP HANA S672m<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336개 CPU 코어 및 672개 CPU 스레드 | 18.0TB |  18.0TB |  --- | 27.4TB | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S672oo<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336개 CPU 코어 및 672개 CPU 스레드 | 13.5TB |  4.5TB |  9.0TB | 21.1TB  | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S672om<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336개 CPU 코어 및 672개 CPU 스레드 | 18.0TB |  9.0TB |  9.0TB | 27.4TB  | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S672ooo<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336개 CPU 코어 및 672개 CPU 스레드 | 22.5TB |  4.5TB |  18.0TB | 33.7TB  | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S672oom<br /> – 12 x Intel® Xeon® Platinum 8276 프로세서 <br /> 336개 CPU 코어 및 672개 CPU 스레드 | 27.0TB |  9.0TB |  18.0TB | 40.0TB  | 사용 가능(Rev 4.2에만 해당) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | Azure의 SAP HANA S768m<br /> – 16 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 384 CPU 코어 및 768 CPU 스레드 |  16.0 TB | 16.0 TB | -- | 36 TB | 사용 가능 |
| 아니요 | Azure S768xm에서 SAP HANA<br /> – 16 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 384 CPU 코어 및 768 CPU 스레드 |  24.0TB | 24.0TB | --- | 56TB | 사용 가능 |
|  YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2407), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | Azure의 SAP HANA S896<br /> – 16 x Intel® Xeon® Platinum 8276 프로세서 <br /> 448개 CPU 코어 및 896개 CPU 스레드 | 12.0 TB |  12.0 TB |  --- | 18.9TB | 사용 가능(Rev 4.2에만 해당) |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2406), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | Azure의 SAP HANA S896m<br /> – 16 x Intel® Xeon® Platinum 8276 프로세서 <br /> 448개 CPU 코어 및 896개 CPU 스레드 | 24.0TB | 24.0TB | -- | 35.8TB | 사용 가능 |
| 아니요 | Azure의 SAP HANA S896oo<br /> – 16 x Intel® Xeon® Platinum 8276 프로세서 <br /> 448개 CPU 코어 및 896개 CPU 스레드 | 18.0TB |  6.0 TB |  12.0 TB | 27.4TB  | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S896om<br /> – 16 x Intel® Xeon® Platinum 8276 프로세서 <br /> 448개 CPU 코어 및 896개 CPU 스레드 | 24.0TB |  12.0 TB |  12.0 TB | 35.8TB  | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S896ooo<br /> – 16 x Intel® Xeon® Platinum 8276 프로세서 <br /> 448개 CPU 코어 및 896개 CPU 스레드 | 30.0TB |  6.0 TB |  24.0TB | 44.3TB  | 사용 가능(Rev 4.2에만 해당) |
| 아니요 | Azure의 SAP HANA S896oom<br /> – 16 x Intel® Xeon® Platinum 8276 프로세서 <br /> 448개 CPU 코어 및 896개 CPU 스레드 | 36.0TB |  12.0 TB |  24.0TB | 52.7TB  | 사용 가능(Rev 4.2에만 해당) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | Azure의 SAP HANA S960m<br /> – 20 x Intel® Xeon® 프로세서 E7-8890 v4<br /> 480 CPU 코어 및 960 CPU 스레드 |  20.0 TB | 20.0 TB | -- | 46 TB | 사용 가능(Rev 4.2에만 해당) |


- CPU 코어 = 서버 단위 프로세서 합계의 비하이퍼 스레드 CPU 코어 합계
- CPU 스레드 = 서버 단위 프로세서 합계의 하이퍼 스레드된 CPU 코어에서 제공하는 컴퓨팅 스레드 합계. 대부분의 단위는 기본적으로 하이퍼 스레딩 기술을 사용하도록 구성됩니다.
- 공급업체 권고에 따라 S768m, S768xm 및 S960m은 SAP HANA 실행에 하이퍼 스레딩을 사용하도록 구성되지 않았습니다.


> [!IMPORTANT]
> S72, S72m, S144, S144m, S192 및 S192m SKU는 여전히 지원되지만 더 이상 구매할 수 없습니다.

선택한 특정 구성은 워크로드, CPU 리소스 및 원하는 메모리에 따라 달라집니다. OLTP 워크로드에서 OLAP 워크로드에 최적화된 SKU를 사용할 수 있습니다. 

SKU를 구분하는 두 가지 하드웨어 클래스는 다음과 같습니다.

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 및 S224m, S224oo, S224om, S224ooo, S224oom은 SKU의 "유형 I 클래스"라고 합니다.
- 다른 모든 SKU는 SKU의 "유형 II 클래스"라고 합니다.
- SAP 하드웨어 디렉터리에 아직 나열되지 않은 SKU에 관심이 있는 경우 Microsoft 계정 팀에 문의하여 자세한 정보를 받으세요. 

## <a name="tenant-considerations"></a>테넌트 고려 사항

HANA 대규모 인스턴스 스탬프 전체는 단일 고객 사용으로만 할당되지 않습니다. 이는 Azure에 배포된 네트워크 패브릭을 통해 연결된 컴퓨팅 및 스토리지 리소스 랙에도 적용됩니다. HANA 대규모 인스턴스 인프라는 Azure와 마찬가지로 다음 세 가지 수준에서 서로 격리된 서로 다른 고객 "테넌트"를 배포합니다.

- **네트워크**: HANA 대규모 인스턴스 스탬프 내에서 가상 네트워크를 통해 격리됩니다.
- **스토리지**: 스토리지 볼륨이 할당되고 테넌트 간 스토리지 볼륨을 격리하는 스토리지 가상 머신을 통해 격리됩니다.
- **Compute**: 서버 단위를 단일 테넌트에 전용 할당. 서버 장치에 대한 하드 분할 또는 소프트 분할이 없습니다. 테넌트 간 단일 서버 또는 호스트 단위의 공유 없음. 

서로 다른 테넌트 간의 HANA 대규모 인스턴스 장치 배포는 상호 간에 표시되지 않습니다. 서로 다른 테넌트에 배포된 HANA 대규모 인스턴스 장치는 HANA 대규모 인스턴스 스탬프 수준에서 서로 직접 통신 할 수 없습니다. 한 테넌트 내의 HANA 대규모 인스턴스 장치만 HANA 대규모 인스턴스 스탬프 수준에서 서로 통신할 수 있습니다.

대규모 인스턴스 스탬프에 배포된 테넌트는 청구를 위해 하나의 Azure 구독에 할당됩니다. 네트워크의 경우 동일한 Azure 등록 내에 있는 다른 Azure 구독의 가상 네트워크에서 액세스할 수 있습니다. 또한 동일한 Azure 지역의 또 다른 Azure 구독을 사용하여 배포할 경우 별도의 HANA 대규모 인스턴스 테넌트를 요청하기로 선택할 수 있습니다.

## <a name="sap-hana-on-hana-large-instances-vs-on-vms"></a>HANA 대규모 인스턴스와 VM의 SAP HANA

HANA 대규모 인스턴스에서 SAP HANA를 실행하는 것과 Azure에 배포된 VM에서 SAP HANA를 실행하는 것 사이에는 중요한 차이가 있습니다.

- Azure(큰 인스턴스)의 SAP HANA의 경우 가상화 계층이 없습니다. 기본적인 완전 복구 하드웨어의 성능을 얻을 수 있습니다.
- Azure와 달리 Azure(큰 인스턴스) 서버에서 SAP HANA는 특정 고객이 전용으로 사용할 수 있습니다. 서버 장치 또는 호스트가 하드 또는 소프트 분할될 가능성이 없습니다. 결과적으로 HANA 대규모 인스턴스 장치는 테넌트에 전체적으로 할당된 대로 사용되며, 이에 따라 사용자에게 제공됩니다. 서버를 다시 부팅하거나 종료해도 운영 체제 및 SAP HANA가 다른 서버에 자동으로 배포되지 않습니다. (유형 I 클래스 SKU의 경우, 유일한 예외는 서버에서 문제가 발생하고 다른 서버에서 다시 배포해야 하는 경우입니다.)
- Azure와 달리 최고의 가성비를 위해 호스트 프로세서 유형을 선택한 경우 Azure(큰 인스턴스)에서 SAP HANA에 대해 선택한 프로세서 유형은 Intel E7v3 및 E7v4 프로세서 제품군에서 최고의 성능을 보입니다.

## <a name="next-steps"></a>다음 단계
HANA 대규모 인스턴스의 크기 조정에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [HLI 크기](hana-sizing.md)
