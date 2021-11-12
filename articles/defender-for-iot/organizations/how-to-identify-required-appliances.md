---
title: 필수 어플라이언스 식별
description: 인증된 Defender for IoT 센서 및 온-프레미스 관리 콘솔의 하드웨어 및 가상 어플라이언스에 대해 알아봅니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 1b0f96ac19c014ff2cc89df10cae3f21b38211ff
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343594"
---
# <a name="identify-required-appliances"></a>필수 어플라이언스 식별

이 문서에서는 인증된 Defender for IoT 센서 어플라이언스의 정보를 제공합니다. IoT 용 Defender는 물리적 및 가상 어플라이언스에 배포할 수 있습니다.

여기에는 소프트웨어가 이미 설치된 인증된 *사전 구성된* 어플라이언스가 포함되며, 필수 소프트웨어를 다운로드하여 설치할 수 있고 구성되지 않은 인증된 어플라이언스가 포함됩니다.

또한 이 문서에서는 온-프레미스 관리 콘솔 어플라이언스에 대한 사양을 제공합니다. 온-프레미스 관리 콘솔은 미리 구성된 어플라이언스로 사용할 수 없습니다.

- 미리 구성된 센서를 구매하려면 [센서 어플라이언스](#sensor-appliances) 섹션에서 사용 가능한 모델을 검토한 다음 구매를 진행합니다.

- 자체 어플라이언스를 구매하려면 [센서 어플라이언스](#sensor-appliances) 섹션 및 [추가 인증 어플라이언스](#additional-certified-appliances) 섹션에서 사용할 수 있는 모델을 검토합니다. 어플라이언스를 취득한 후에는 소프트웨어를 다운로드하여 설치할 수 있습니다.

- 온-프레미스 관리 콘솔을 구매하려면 [온-프레미스 관리 콘솔 어플라이언스](#on-premises-management-console-appliance) 섹션의 정보를 검토합니다. 디바이스를 확보한 후에는 소프트웨어를 다운로드하여 설치할 수 있습니다.

여기에서 작업을 완료한 후에는 소프트웨어를 설치하고 네트워크를 설정할 수 있습니다.

## <a name="sensor-appliances"></a>센서 어플라이언스

Defender for IoT는 물리적 배포와 가상 배포를 모두 지원합니다.

### <a name="physical-sensors"></a>실제 센서

이 섹션에서는 사용할 수 있는 실제 센서 모델의 개요를 제공합니다. 미리 구성 된 소프트웨어 또는 미리 구성 되지 않은 구입 센서를 사용 하 여 센서를 구입할 수 있습니다.

| 배포 유형 | 회사 | Enterprise | SMB 랙 탑재| SMB 러기드 |
|--|--|--|--|--|
| 이미지 | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="기업 수준 모델."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="엔터프라이즈 수준 모델."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="SMB 수준 모델."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="SMB 러기드 수준 모델"::: |
| 모델 | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| 포트 모니터링 | 최대 15개의 RJ45 또는 8개의 OPT | 최대 8개의 RJ45 또는 6개의 OPT | 최대 4개의 RJ45 | 최대 5개의 RJ45 |
| 최대 대역폭 [1](#anchortext) | 3Gb/초 | 1Gb/초 | 200Mb/초 | 100Mb/초 |
| 최대 보호 디바이스 | 12,000 | 10000 | 1,000 | 800 |

공급업체 세부 정보는 [어플라이언스 사양](#appliance-specifications)을 참조하세요.

미리 구성된 센서 정보: Microsoft는 Arrow와 협력하여 미리 구성된 센서를 제공합니다. 미리 구성된 센서를 구입하려면 Arrow(주소: <hardware.sales@arrow.com>)에 문의합니다.

사용자 고유의 어플라이언스를 가져오는 방법: 여기에 설명된 지원되는 모델을 검토합니다. 어플라이언스를 구매한 후 **Defender for IoT** > **네트워크 센서 ISO** > **설치** 로 이동하여 소프트웨어를 다운로드합니다.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="네트워크 센서 ISO.":::

<a id="anchortext">1</a> 대역폭 용량은 프로토콜 배포에 따라 달라질 수 있습니다.

### <a name="virtual-sensors"></a>가상 센서

이 섹션에서는 사용할 수 있는 가상 센서에 대해 설명 합니다.

| 배포 유형 | 회사 | Enterprise | SMB |
|--|--|--|--|
| 최대 대역폭 | 2.5Gb/초 | 800Mb/초 | 160Mb/초 |
| 최대 보호 디바이스 | 12,000 | 10000 | 800 |

## <a name="on-premises-management-console-appliance"></a>온-프레미스 관리 콘솔 어플라이언스

관리 콘솔은 가상 배포로 사용할 수 있습니다.

| 배포 유형 | Enterprise |
|--|--|
| 어플라이언스 유형 | HPE DL20, VM |
| 관리 센서 수 | 최대 300 |

온-프레미스 관리 콘솔을 구매한 후 **Defender for IoT** > **온-프레미스 관리 콘솔** > **ISO 설치** 로 이동하여 ISO를 다운로드합니다.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="온-프레미스 관리 콘솔.":::

## <a name="appliance-specifications"></a>어플라이언스 사양

이 섹션에서는 다음 어플라이언스에 대한 하드웨어 사양을 설명합니다.

- 회사 배포: HPE ProLiant DL360

- 엔터프라이즈 배포: HPE ProLiant DL20

- SMB 배포: HPE ProLiant DL20

- 가상 어플라이언스 사양

## <a name="corporate-deployment-hpe-proliant-dl360"></a>회사 배포: HPE ProLiant DL360

| 구성 요소 | 기술 사양 |
|--|--|
| 섀시 | 1U 랙 서버 |
| 차원 | 42.9 x 43.46 x 70.7(cm)/1.69" x 17.11" x 27.83"(인치) |
| 무게 | 최대 16.27kg(35.86lb) |
| 프로세서 | Intel Xeon 실버 4215 R 3.2GHz, 11M 캐시, 8c/16T, 130W |
| 칩셋 | Intel C621 |
| 메모리 | 32GB = 2 x 16GB 2666MT/s DDR4 ECC UDIMM |
| 스토리지 | Hot-Plug 하드 드라이브의 6 x 1.2TB SAS 12G 엔터프라이즈 10K SFF(2.5인치) - RAID 5 |
| 네트워크 컨트롤러 | 온-보드: 2 x 1GB <br>온-보드: iLO 포트 카드 1GB <br>외부: 1 x HPE 이더넷 1-GB 4-포트 366FLR 어댑터 |
| 관리 | HPE iLO 고급 |
| 디바이스 액세스 | 두 개의 후면 USB 3.0<br>한 개의 전면 USB 2.0<br>단일 내부 USB 3.0 |
| 고급 | 2 x HPE 500W 플렉스 슬롯 플래티넘 핫 플러그 저 할로겐 전원 공급 장치 키트 |
| 랙 지원 | HPE 1U Gen10 SFF 쉬운 설치 레일 키트 |

### <a name="appliance-bom"></a>어플라이언스 BOM

| PN | Description | 수량 |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO 서버 | 1 |
| P19766-B21 | 유럽 - 다국어 지역화 | 1 |
| P24479-L21 | DL360 G10용 Intel Xeon-S 4215 R FIO 키트 | 1 |
| P24479-B21 | DL360 Gen10용 Intel Xeon-S 4215 R 키트 | 1 |
| P00922-B21 | HPE 16GB 2Rx8 PC4-2933Y-R 스마트 키트 | 2 |
| 872479-B21 | HPE 1.2TB SAS 10K SFF SC DS HDD | 6 |
| 811546-B21 | HPE 1GBE 4-p BASE-T I350 어댑터 | 1 |
| P02377-B21 | HPE Smart Hybrid Capacitor \_ 145mm 케이블 포함 | 1 |
| 804331-B21 | HPE Smart Array P408i-a SR Gen10 컨트롤러 | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T I350 어댑터 | 1 |
| 871244-B21 | HPE DL360 Gen10 고성능 팬 키트 | 1 |
| 865408-B21 | HPE 500W FS Plat 핫 플러그 ​​LH 전원 공급 장치 키트 | 2 |
| 512485-B21 | HPE iLO Adv 1-서버 라이선스 1년 지원 | 1 |
| 874543-B21 | HPE 1U Gen10 SFF 쉬운 설치 레일 키트 | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>엔터프라이즈 배포: HPE ProLiant DL20

| 구성 요소 | 기술 사양 |
|--|--|
| 섀시 | 1U 랙 서버 |
| 차원(높이 x 너비 x 깊이) | 4.32 x 43.46 x 38.22cm/1.70 x 17.11 x 15.05인치 |
| 무게 | 7.9kg/17.41lb |
| 프로세서 | Intel Xeon E-2234, 3.6GHz, 4C/8T, 71W |
| 칩셋 | Intel C242 |
| 메모리 | 2 x 16GB Dual Rank x8 DDR4-2666 |
| 스토리지 | 3 x 1TB SATA 6G Midline 7.2K SFF(2.5인치) – 스마트 어레이 P408i-a SR 컨트롤러가 있는 RAID 5 |
| 네트워크 컨트롤러 | 온-보드: 2 x 1GB <br>온-보드: iLO 포트 카드 1GB <br>외부: 1 x HPE 이더넷 1-GB 4-포트 366FLR 어댑터 |
| 관리 | HPE iLO 고급 |
| 디바이스 액세스 | 전면: 1 x USB 3.0, 1 x USB iLO 서비스 포트 <br>후면: 2 x USB 3.0 <br>내부: 1 x USB 3.0 |
| 고급 | 이중 핫 플러그 전원 공급 장치 500W |
| 랙 지원 | HPE 1U 짧은 마찰 레일 키트 |

### <a name="appliance-bom"></a>어플라이언스 BOM

| PN | 설명: 최첨단 | 수량 |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO 서버 | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO 서버 | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO 키트 | 1 |
| 879507-B21 | HPE 16GB 2Rx8 PC4-2666V-E STND 키트 | 2 |
| 655710-B21 | HPE 1TB SATA 7.2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser Kit | 1 |
| 665240-B21 | HPE 이더넷 1GB 4개 포트 366FLR 어댑터 | 1 |
| 782961-B21 | HPE 12W 스마트 스토리지 배터리 | 1 |
| 869081-B21 | HPE 스마트 배열 P408i-a SR G10 LH 컨트롤러 | 1 |
| 865408-B21 | HPE 500W FS Plat 핫 플러그 ​​LH 전원 공급 장치 키트 | 2 |
| 512485-B21 | HPE iLO Adv 1-서버 라이선스 1년 지원 | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS Enablement FIO 키트 | 1 |
| 775612-B21 | HPE 1U 짧은 마찰 레일 키트 | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB 배포: HPE ProLiant DL20

| 구성 요소 | 기술 사양 |
|--|--|
| 섀시 | 1U 랙 서버 |
| 차원(높이 x 너비 x 깊이) | 4.32 x 43.46 x 38.22cm/1.70 x 17.11 x 15.05인치 |
| 무게 | 7.88kg/17.37lb |
| 프로세서 | Intel Xeon E-2224, 3.4GHz, 4C, 71W |
| 칩셋 | Intel C242 |
| 메모리 | 1 x 8GB 듀얼 랭크 x8 DDR4-2666 |
| 스토리지 | 2 x 1TB SATA 6G 미드 라인 7.2K SFF(2.5인치) – RAID 1 및 스마트 어레이 P208i-a |
| 네트워크 컨트롤러 | 온-보드: 2 x 1GB <br>온-보드: iLO 포트 카드 1GB <br>외부: 1 x HPE 이더넷 1-GB 4-포트 366FLR 어댑터 |
| 관리 | HPE iLO 고급 |
| 디바이스 액세스 | 전면: 1 x USB 3.0, 1 x USB iLO 서비스 포트 <br>후면: 2 x USB 3.0 <br>내부: 1 x USB 3.0 |
| 고급 | 핫 플러그 전원 공급 장치 290W |
| 랙 지원 | HPE 1U 짧은 마찰 레일 키트 |

### <a name="appliance-bom"></a>어플라이언스 BOM

| PN | Description | 수량 |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO 키트 | 1 |
| 879505-B21 | HPE 8GB 1Rx8 PC4-2666V-E STND 키트 | 1 |
| 801882-B21 | HPE 1TB SATA 7.2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser Kit | 1 |
| 665240-B21 | HPE 이더넷 1GB 4개 포트 366FLR 어댑터 | 1 |
| 869079-B21 | HPE 스마트 배열 E208i-a SR G10 LH 컨트롤러 | 1 |
| P21649-B21 | HPE DL20 Gen10 plat 290 W FIO PSU 키트 | 1 |
| P06683-B21 | HPE DL20 Gen10 M. 2 SATA/LFF AROC 케이블 키트 | 1 |
| 512485-B21 | HPE iLO Adv 1-서버 라이선스 1년 지원 | 1 |
| 775612-B21 | HPE 1U 짧은 마찰 레일 키트 | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB 러기드: HPE Edgeline EL300

| 구성 요소 | 기술 사양 |
|--|--|
| 건설 | 알루미늄, 팬리스 및 방진 설계 |
| 차원(높이 x 너비 x 깊이) | 높이 200.5mm(7.9"), 너비 232mm(9.14"), 깊이 100mm(3.9") |
| 무게 | 4.91KG(10.83lbs.) |
| CPU | Intel Core i7-8650U(1.9GHz/4-core/15W) |
| 칩셋 | Intel® Q170 플랫폼 컨트롤러 허브 |
| 메모리 | 8GB DDR4 2133MHz 폭넓은 온도 SODIMM |
| Storage | 128GB 3ME3 폭넓은 온도 mSATA SSD |
| 네트워크 컨트롤러 | Intel® I219의 6x 기가비트 이더넷 포트 |
| 디바이스 액세스  | USB 4개: 전면 2개; 후면 2개; 내부 1개 |
| 전원 어댑터 | 250V/10A |
| 탑재 | 탑재 키트, Din 레일 |
| 작동 온도 | 0C ~ +70C  |
| 습도 | 10%~90%, 비응결 |
| Vibration | 0.3그램 10Hz~300Hz, 축당 15분 - Din 레일   |
| 충격 | 10G 10ms, half-sine, 각 축에 3개. (양 및 음의 펄스 모두) – Din 레일 |

### <a name="appliance-bom"></a>어플라이언스 BOM
| 제품 | Description |
|--|--|
| P25828-B21 | HPE Edgeline EL300 v2 수렴형 에지 시스템 |
| P25828-B21 B19 | HPE EL300 v2 수렴형 에지 시스템 |
| P25833-B21 | Intel Core i7-8650U(1.9GHz/4-코어/15W) HPE Edgeline EL300용 FIO 기본 프로세서 키트 |
| P09176-B21 | HPE Edgeline 8GB(1x8GB) 듀얼 랭크 x8 DDR4-2666 SODIMM WT CAS-19-19-19 등록된 메모리 FIO 키트 |
| P09188-B21 | HPE Edgeline 256GB SATA 6G 집약적 읽기 M. 2 2242 3yr Wty 와이드 온도 SSD |
| P04054-B21 | HPE Edgeline EL300 SFF에서 M.2 사용 키트 |
| P08120-B21 | HPE Edgeline EL300 12VDC FIO 전송 보드 |
| P08641-B21 | HPE Edgeline EL300 80W 12VDC 전원 공급 장치 |
| AF564A | HPE C13 - SI-32 IL 250V 10Amp 1.83m 전원 코드 |
| P25835-B21 | HPE EL300 v2 FIO 캐리어 보드 |
| R1P49AAE | HPE EL300 iSM Adv 3yr 24x7 Sup_Upd E-LTU |
| P08018-B21 선택 사항 | HPE Edgeline EL300 Low Profile Bracket 키트  |
| P08019-B21 선택 사항 | HPE Edgeline EL300 DIN 레일 탑재 키트 |
| P08020-B21 선택 사항 | HPE Edgeline EL300 벽 탑재 키트 |
| P03456-B21 선택 사항 | HPE Edgeline 1GbE 4-포트 TSN FIO 부속 카드 |

## <a name="virtual-appliance-specifications"></a>가상 어플라이언스 사양

### <a name="sensors"></a>센서

| 유형 | 회사 | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| 메모리 | 32GB | 32GB | 8GB |
| 스토리지 | 5.6TB | 1.8TB | 500GB |

### <a name="on-premises-management-console-appliance"></a>온-프레미스 관리 콘솔 어플라이언스

| 유형 | Enterprise |
|--|--|
| Description | 엔터프라이즈 배포 유형의 가상 어플라이언스 |
| vCPU | 8 |
| 메모리 | 32GB |
| 스토리지 | 1.8TB |

지원되는 하이퍼바이저: VMware ESXi 버전 5.0 이상, Hyper-V

## <a name="additional-certified-appliances"></a>추가 인증 어플라이언스

이 섹션에서는 Microsoft에서 인증했지만 미리 구성된 어플라이언스로 제공되지 않는 추가 어플라이언스에 대해 자세히 설명합니다.

| 배포 유형 | Enterprise |
|--|--|
| 이미지 | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="엔터프라이즈 배포 유형."::: |
| 모델 | Dell PowerEdge R340 XL |
| 포트 모니터링 | 최대 9개의 RJ45 또는 6개의 OPT |
| 최대 대역폭 [1](#anchortext2)| 1Gb/초 |
| 최대 보호 디바이스 | 10000 |

<a id="anchortext2">1</a> 대역폭 용량은 프로토콜 배포에 따라 달라질 수 있습니다.

어플라이언스를 구매한 후 **Defender for IoT** > **네트워크 센서 ISO** > **설치** 로 이동하여 소프트웨어를 다운로드합니다.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="네트워크 센서 ISO.":::

## <a name="next-steps"></a>다음 단계

[IoT용 Microsoft Defender 설치 정보](how-to-install-software.md)

[IoT용 Microsoft Defender 네트워크 설정 정보](how-to-set-up-your-network.md)
