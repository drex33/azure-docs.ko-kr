---
title: Azure | Microsoft Docs에서 지원되는 메인프레임 워크로드
description: Microsoft 파트너의 메인프레임 에뮬레이터 및 기타 서비스를 사용하여 Microsoft Azure를 사용하는 IBM Z 기반 시스템과 같은 메인프레임 워크로드를 다시 호스트합니다.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 05/09/2020
ms.topic: article
ms.openlocfilehash: bd698529c9a5457a05afd5e71c3cb3b3a9bba2bd
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871284"
---
# <a name="mainframe-workloads-supported-on-azure"></a>Azure에서 지원되는 메인프레임 워크로드

여기에 나열된 솔루션은 메인프레임 작업을 Microsoft Azure로 마이그레이션하는 데 도움이 될 수 있습니다. 비교적 쉽게 마이그레이션할 수 있는 워크로드도 있는 반면 기타 워크로드는 다시 호스팅 될 수 있는 레거시 시스템 software에 달려 있습니다. 

메인프레임 에뮬레이션 및 서비스에 대한 자세한 내용은 [Azure 메인프레임 마이그레이션 센터](https://azure.microsoft.com/migration/mainframe/)를 참조하세요.

## <a name="migrate-mainframe-closer-to-azure"></a>메인프레임을 Azure에 더 가깝게 마이그레이션

- [.net 컴파일러의 Asna visual RPG](https://asna.com/us/products/visual-rpg) Visual Studio 플러그 인을 통한 .NET Framework.
- [Asysco AMT COBOL](https://www.asysco.com/cobol/) 개발 환경(Unisys, IBM 메인프레임 및 기타 COBOL 언어(예: 마이크로 포커스 COBOL)).
- [ASYSCO AMT GO](https://www.asysco.com/amt-go/) 고성능 워크로드에 대한 클라우드 기반 배포 아키텍처.
- [Asysco AMT Transform](https://www.asysco.com/amt-transform/) 데이터, 코드, 스크립팅, 보안, 인터페이스 및 기타 메인프레임 아티팩트를 변환하는 데 사용.
- [Fujitsu NetCOBOL](https://www.fujitsu.com/global/products/software/developer-tool/netcobol/) 개발 및 통합 도구.
- [Micro Focus Visual COBOL](https://www.microfocus.com/products/visual-cobol/) 개발 및 통합 도구.
- [Micro Focus PL/I](https://www.microfocus.com/campaign/download/pli-modernization/) 메인프레임 PL/I 구문, 데이터 형식 및 동작을 지원하는 .NET 플랫폼을 위한 레거시 컴파일러.
- [Micro Focus Enterprise Server](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) 메인프레임 통합 플랫폼.
- [Modern Systems CTU (COBOL에서 유니버설)](https://modernsystems.com/automatic-cobol-to-java-conversion/) 개발 및 통합 도구.
- [NTT Data Enterprise COBOL](https://us.nttdata.com/en/digital/application-development-and-modernization) 개발 및 통합 도구.
- [NTT Open PL/I](https://us.nttdata.com/en/digital/application-development-and-modernization) 메인프레임 PL/I 구문, 데이터 형식 및 동작을 지원하는 .NET 플랫폼을 위한 레거시 컴파일러.
- [Raincode COBOL 컴파일러](https://www.raincode.com/products/cobol/) 개발 및 통합 도구.
- .NET 플랫폼을 위한 [Raincode PL/I 컴파일러](https://www.raincode.com/products/pli/) 메인프레임 PL/I 구문, 데이터 형식 및 동작 지원.
- Raincode ASM370 컴파일러 메인프레임 어셈블러 370 및 HLASM 구문용.

## <a name="deploy-an-emulation-environment-for-online-and-batch-processing"></a>온라인 및 일괄 처리를 위한 에뮬레이션 환경 배포

- [Asysco AMT GO](https://www.asysco.com/amt-go/) 배포 아키텍처는 CICS, IMS, TIP, HVTIP 및 기타 일반적인 메인프레임 환경을 지원.
- [Micro Focus Enterprise Server](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) 메인프레임 통합 플랫폼.
- [NTT 데이터 메인프레임 재호스팅 개발 환경](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) 네이티브 트랜잭션 처리 환경.
- [NTT 데이터 일괄 처리 환경](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf)(BPE)는 jcl 트랜잭션 기능을 포함합니다.
- .NET 및 Azure 플랫폼을 위한 [Raincode CICS](https://www.raincode.com/technical-landscape/cics/) 에뮬레이터.
- [Raincode JCL](https://www.raincode.com/products/jcl/) 플러그 호환 JCL 인터프리터.

## <a name="code-conversion"></a>코드 변환

- 소스 코드, 데이터, 일괄 처리, 일정, TP 모니터, 인터페이스, 보안, 관리 등을 포함하는 [Asysco](https://asysco.com/) 시스템 변환 기술입니다.
- [Asysco AMT Services](https://www.asysco.com/migration-services/) 인벤토리 및 분석, 디자인 교육, 최종 예행연습, 라이브 전환, 마이그레이션 후 지원 등 마이그레이션 프로젝트를 위한 엔드 투 엔드 서비스.
- [Blu Age](https://www.bluage.com/) 레거시 비즈니스 애플리케이션 및 데이터베이스 디지털화를 위한 도구.
- [Heirloom Computing](https://www.heirloomcomputing.com/tag/convert-cobol-to-java/) 메인프레임 COBOL, CICS 및 VSAM을 Java로 변환하는 서비스.
- [LzLabs Software Defined Mainframe](https://www.lzlabs.com/) 메인프레임 애플리케이션을 Linux 컴퓨터 또는 프라이빗, 공용 및 하이브리드 클라우드 환경으로 마이그레이션하기 위한 소프트웨어 컨테이너 관리.

## <a name="modernization-services"></a>현대화 서비스

Microsoft는 대규모 조직의 디자인, 빌드 및 솔루션 관리를 지원할 수 있는 GSIs(global system 통합자)와 협력합니다. 

- [Azure 메인프레임 마이그레이션 센터](https://azure.microsoft.com/migration/mainframe/)
