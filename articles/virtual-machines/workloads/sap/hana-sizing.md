---
title: Azure(대규모 인스턴스)의 SAP HANA 크기 조정 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)에 대한 크기 조정에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4050a986c98b492f71383bd5c677d68288882611
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114404662"
---
# <a name="sizing"></a>크기 조정

이 문서에서는 HANA(대규모 인스턴스)의 크기 조정에 도움이 되는 정보를 살펴보겠습니다. 일반적으로 HANA(대규모 인스턴스)에 대한 크기 조정은 HANA에 대한 크기 조정과 다르지 않습니다. 

## <a name="moving-an-existing-system-to-sap-hana-large-instances"></a>기존 시스템을 SAP HANA(대규모 인스턴스)로 이동

기존에 배포된 시스템을 다른 RDBMS(관계형 데이터베이스 관리 시스템)에서 HANA로 이동하려고 한다고 가정해 보겠습니다. SAP는 기존 SAP 시스템에서 실행할 보고서를 제공합니다. 데이터베이스가 HANA로 이동되면 이러한 보고서는 데이터를 확인하고 HANA 인스턴스에 대한 메모리 요구 사항을 계산합니다. 

이러한 보고서를 실행하고 최신 패치 또는 버전을 가져오는 방법에 대한 자세한 내용은 다음 SAP Note를 참조하세요.

- [SAP Note #1793345 - HANA에서 SAP Suite에 대한 크기 조정](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite on HANA 및 S/4 HANA 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - FAQ: SAP BW on HANA 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - BW on HANA에 대한 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - BW on HANA에 대한 새 크기 조정 보고서](https://launchpad.support.sap.com/#/notes/2296290)

## <a name="sizing-greenfield-implementations"></a>그린필드 구현 크기 조정

구현을 처음부터 시작하는 경우 SAP Quick Sizer는 HANA를 기반으로 SAP 소프트웨어 구현의 메모리 요구 사항을 계산합니다.

## <a name="memory-requirements"></a>메모리 요구 사항

데이터 볼륨이 증가함에 따라 HANA에 대한 메모리 요구 사항도 증가합니다. 나중에 발생할 상황을 예측하는 데 도움이 되도록 현재 메모리 사용을 파악해야 합니다. 그러면 메모리 요구 사항에 따라 해당 요구 사항을 HANA 대규모 인스턴스 SKU 중 하나에 매핑할 수 있습니다.

## <a name="next-steps"></a>다음 단계

HANA(대규모 인스턴스)의 온보딩 요구 사항에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [온보딩 요구 사항](hana-onboarding-requirements.md)
