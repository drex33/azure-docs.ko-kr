---
title: Azure HPC Cache 데이터 수집 - 수동 복사
description: Azure HPC Cache에서 cp 명령을 사용하여 Blob 스토리지 대상으로 데이터를 이동하는 방법입니다.
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: femila
ms.openlocfilehash: d3d93a767839d552791466d77be5ecbb9c50adf1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078525"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Azure HPC Cache 데이터 수집 - 수동 복사 방법

이 문서에서는 Azure HPC Cache와 함께 사용할 수 있도록 Blob 스토리지 컨테이너에 데이터를 수동으로 복사하는 방법에 대한 자세한 지침을 제공합니다. 다중 스레드 병렬 작업을 사용하여 복사 속도를 최적화합니다.

Azure HPC Cache에 사용하도록 Blob 스토리지로 데이터를 이동하는 방법에 대한 자세한 내용은 [Move data to Azure Blob storage](hpc-cache-ingest.md)(Azure Blob 스토리지로 데이터 이동)를 참조하세요.

## <a name="simple-copy-example"></a>단순 복사 예제

미리 정의된 파일 또는 경로 집합에 대해 백그라운드에서 한 번에 둘 이상의 복사 명령을 실행하여 클라이언트에 다중 스레드 복사본을 수동으로 만들 수 있습니다.

``cp`` Linux/UNIX 명령에는 소유권 및 mtime 메타데이터를 유지하기 위해 ``-p`` 인수가 포함됩니다. 아래의 명령에 이 인수를 추가하는 것은 선택 사항입니다. (인수를 추가하면 메타데이터를 수정하기 위해 클라이언트에서 대상 파일 시스템으로 보내는 파일 시스템 호출의 수가 증가합니다.)

간단한 다음 예제에서는 두 파일을 병렬로 복사합니다.

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

이 명령이 실행되면 `jobs` 명령에서 두 개의 스레드가 실행 중임을 표시합니다.

## <a name="copy-data-with-predictable-file-names"></a>예측 가능한 파일 이름을 사용하여 데이터 복사

파일 이름을 예측할 수 있는 경우 식을 사용하여 병렬 복사 스레드를 만들 수 있습니다.

예를 들어 디렉터리에 `0001`에서 `1000`까지 순차적으로 번호가 매겨진 1,000개의 파일이 있으면 다음 식을 사용하여 각각 100개의 파일을 복사하는 10개의 병렬 스레드를 만들 수 있습니다.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>비구조적 파일 이름을 사용하여 데이터 복사

파일 명명 구조를 예측할 수 없는 경우 파일을 디렉터리 이름별로 그룹화할 수 있습니다.

다음 예제에서는 전체 디렉터리를 수집하여 백그라운드 작업으로 실행되는 ``cp`` 명령으로 보냅니다.

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

파일이 수집되면 병렬 복사 명령을 실행하여 하위 디렉터리와 모든 해당 콘텐츠를 반복적으로 복사할 수 있습니다.

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>탑재 지점을 추가하는 경우

단일 대상 파일 시스템 탑재 지점에 대해 충분한 병렬 스레드가 있는 경우 더 많은 스레드를 추가해도 처리량이 늘어나지 않는 지점이 있습니다. (처리량은 데이터 형식에 따라 파일 수/초 또는 바이트 수/초로 측정됩니다.) 또는 과도한 스레딩으로 처리량이 저하되는 더 나쁜 상황이 발생하는 경우도 있습니다.

이 경우 동일한 원격 파일 시스템 탑재 경로를 사용하여 클라이언트 쪽 탑재 지점을 다른 Azure HPC Cache 탑재 주소에 추가할 수 있습니다.

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

클라이언트 쪽 탑재 지점을 추가하면 추가적인 복사 명령을 추가 `/mnt/destination[1-3]` 탑재 지점으로 포크하여 추가 병렬 처리를 수행할 수 있습니다.

예를 들어, 파일이 매우 큰 경우 고유한 대상 경로를 사용하도록 복사 명령을 정의하여 복사를 수행하는 클라이언트에서 더 많은 명령을 병렬로 보낼 수 있습니다.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

위의 예제에서 클라이언트 파일 복사 프로세스는 세 개의 대상 탑재 지점을 모두 대상으로 합니다.

## <a name="when-to-add-clients"></a>클라이언트를 추가하는 경우

마지막으로, 클라이언트 기능에 도달한 경우 더 많은 복사 스레드 또는 추가 탑재 지점을 추가해도 추가 파일 수/초 또는 바이트 수/초가 증가하지 않습니다. 이 경우 자체의 파일 복사 프로세스 집합을 실행할 동일한 탑재 지점 집합을 사용하여 다른 클라이언트를 배포할 수 있습니다.

예:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>매니페스트 파일 만들기

위의 방법(대상별 다중 복사 스레드, 클라이언트별 다중 대상, 네트워크 액세스 가능한 원본 파일 시스템별 다중 클라이언트)을 이해한 후에는 파일 매니페스트를 빌드한 후 다중 클라이언트에서 복사 명령과 함께 해당 파일 메니페스트를 사용하도록 추천하는 권장 사항을 고려해 보세요.

이 시나리오에서는 ``find`` UNIX 명령을 사용하여 파일 또는 디렉터리의 매니페스트를 만듭니다.

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

이 결과를 파일로 리디렉션합니다(`find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`).

그런 다음, BASH 명령을 통해 매니페스트를 반복하여 파일 수를 계산하고, 하위 디렉터리의 크기를 결정할 수 있습니다.

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

마지막으로, 클라이언트에 실제 파일 복사 명령을 만들어야 합니다.

4개의 클라이언트가 있는 경우 다음 명령을 사용합니다.

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

5개의 클라이언트가 있는 경우 다음과 같이 사용합니다.

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

6개의 클라이언트가 있는 경우 필요에 따라 추정합니다.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

`find` 명령에서 출력의 일부로 얻은 수준 4 디렉터리에 대한 경로 이름을 가진 *N* 개의 클라이언트 각각에 대해 하나씩 *N* 개의 결과 파일을 받게 됩니다.

각 파일을 사용하여 복사 명령을 작성합니다.

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

위의 명령은 각각 줄당 복사 명령이 포함된 *N* 개의 파일을 제공하며, 클라이언트에서 이 명령을 BASH 스크립트로 실행할 수 있습니다.

목표는 여러 클라이언트에서 동시에 클라이언트마다 이러한 스크립트의 여러 스레드를 병렬로 실행하는 것입니다.
