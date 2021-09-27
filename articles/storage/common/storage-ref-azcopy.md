---
title: azcopy | Microsoft Docs
description: 이 문서는 azcopy 명령에 관한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7153eb1ee29ea63a17e614f49f287945869c7a7e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680151"
---
# <a name="azcopy"></a>azcopy

AzCopy는 데이터를 Azure Storage 안이나 밖으로 이동하는 명령줄 도구입니다. AzCopy를 다운로드하고 스토리지 서비스에 인증 자격 증명을 제공하는 방법에 관해 알아보려면 [AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조하세요.

## <a name="synopsis"></a>개요

명령의 일반적인 형식은 `azcopy [command] [arguments] --[flag-name]=[flag-value]`입니다.

문제를 신고하거나 도구에 관해 자세히 알아보려면 [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy) 링크를 참조하세요.

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [자습서: AzCopy를 사용하여 클라우드 스토리지로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy 및 Blob Storage를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

## <a name="options"></a>옵션

**--cap-mbps**(float): 전송 속도(Mbps)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.

**--help**: azcopy의 도움말입니다.

**--output-type**(string): 명령의 출력 형식입니다. text, json을 선택할 수 있습니다. 기본값은 `text`입니다. (기본값 `text`)

**--trusted-microsoft-suffixes**(string): Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ‘ *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net’입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분합니다.

## <a name="see-also"></a>참조

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [azcopy bench](storage-ref-azcopy-bench.md)
- [azcopy copy](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy jobs](storage-ref-azcopy-jobs.md)
- [azcopy jobs clean](storage-ref-azcopy-jobs-clean.md)
- [azcopy jobs list](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs remove](storage-ref-azcopy-jobs-remove.md)
- [azcopy jobs resume](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobs show](storage-ref-azcopy-jobs-show.md)
- [azcopy list](storage-ref-azcopy-list.md)
- [azcopy login](storage-ref-azcopy-login.md)
- [azcopy logout](storage-ref-azcopy-logout.md)
- [azcopy make](storage-ref-azcopy-make.md)
- [azcopy remove](storage-ref-azcopy-remove.md)
- [azcopy sync](storage-ref-azcopy-sync.md)
