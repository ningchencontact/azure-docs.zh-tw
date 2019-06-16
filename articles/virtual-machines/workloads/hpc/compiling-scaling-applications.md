---
title: 調整 HPC 應用程式-Azure 虛擬機器 |Microsoft Docs
description: 了解如何調整 Azure Vm 上的 HPC 應用程式。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: e2e2476449f956361639e42e7c398e53e42d44ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66810108"
---
# <a name="scaling-hpc-applications"></a>調整 HPC 應用程式

最佳的向上延展及向外延展效能的 Azure 上的 HPC 應用程式需要效能微調和最佳化特定工作負載的實驗。 這一節和 VM 系列專屬頁面會提供一般指導方針來調整您的應用程式。

## <a name="compiling-applications"></a>編譯應用程式

雖然並非必要，編譯具有適當的最佳化旗標的應用程式提供最佳的向上延展效能 HB 和 HC 系列 Vm 上。

### <a name="amd-optimizing-cc-compiler"></a>AMD 最佳化 C /C++編譯器

AMD 最佳化 C /C++編譯器 (AOCC) 編譯器系統提供高層級的進階的最佳化，多執行緒處理，以及處理器支援，包括全域最佳化、 向量化、 程序間的分析，迴圈轉換，和產生程式碼。 AOCC 編譯器二進位碼檔案適合用於具有 GNU C 程式庫 (glibc) 2.17 版的 Linux 系統和更新版本。 包含 C 編譯器 suite /C++編譯器 (clang)、 Fortran 編譯器 (FLANG) 和 Clang （Dragon 蛋） 的 Fortran 前端。

### <a name="clang"></a>Clang

Clang 是 C， C++，和 Objective C 編譯器處理前置處理、 剖析、 最佳化、 程式碼產生、 組件，和連結。 Clang 支援`-march=znver1`旗標來啟用最佳程式碼產生和微調以 AMD 的 Zen 為主的 x86 架構。

### <a name="flang"></a>FLANG

FLANG 編譯器是 AOCC 套件 （加入 2018 年 4 月） 的最近和目前為發行前版本，讓開發人員下載和測試。 AMD Fortran 2008 為基礎，延伸 FLANG，GitHub 版本 (https://github.com/flangcompiler/flang) 。 FLANG 編譯器支援所有的 Clang 編譯器選項和 FLANG 特定編譯器選項的其他數目。

### <a name="dragonegg"></a>DragonEgg

DragonEgg 是取代 GCC 的最佳化工具和程式碼產生器與 LLVM 專案 gcc 外掛程式。 DragonEgg 隨附 AOCC gcc 4.8.x，適用於 x86 32/x86 64 目標經過測試，並已成功地使用各種 Linux 平台上。

GFortran 是負責前置處理、 剖析，並產生 GCC GIMPLE 中繼表示法 (IR) 的語意分析的 Fortran 程式實際的前端。 DragonEgg 是 GNU 外掛程式，將插入 GFortran 編譯流程。 它會實作 GNU 外掛程式 API。 使用外掛程式架構時，DragonEgg 會變成編譯器的驅動程式，推動編譯的不同階段。  完成後的下載和安裝指示，請 Dragon 蛋可以叫用使用： 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 編譯器
PGI Community Edition ver. 若要使用 AMD EPYC 確認 17。 資料流的 PGI 編譯版本確實提供了完整的記憶體頻寬，平台。 較新的 Community Edition 18.10 (11 月 2018) 應該同樣適用。 以下是範例 CLI 編譯器以最佳方式是以 Intel 編譯器：

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel 編譯器
Intel 編譯器 ver. 若要使用 AMD EPYC 確認 18。 以下是範例 CLI 以最佳方式是使用 Intel 編譯器的編譯器。

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 編譯器 
For HPC、 AMD 建議 7.3 或更新版本的 GCC 編譯器。 不建議使用較舊的版本，例如 4.8.5 隨附 RHEL/CentOS 7.4。 GCC 7.3 版，和更新版本中，將會傳遞 HPL、 HPCG 和 DGEMM 測試大幅提高效能。

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>調整應用程式 

下列建議適用於調整的效率、 效能和一致性的最佳應用程式：

* 處理程序釘選核心 0-59 使用循序的釘選方式 （而非自動平衡方法）。 
* 依據 Numa/Core/HwThread 繫結是優於預設繫結。
* 混合式平行應用程式 (OpenMP + MPI)，使用 4 個執行緒及每 CCX 1 MPI 順位。
* 純的 MPI 應用程式的試驗 1-4 MPI 每 CCX 排名為達最佳效能。
* 記憶體頻寬極端的區分大小寫一些應用程式可能會受益於使用的每個 CCX 核心數目減少。 對於這些應用程式中，使用 3 個或 2 個核心每 CCX 可能減少記憶體頻寬競爭的情況，產生較高的真實世界效能或更一致的延展性。 特別是，MPI Allreduce 可能受益於此。
* 針對更大的規模執行，建議使用 UD 或混合式 RC + UD 傳輸。 許多 MPI 程式庫/執行階段程式庫會在內部 （例如 UCX 或 MVAPICH2） 執行此動作。 請檢查您的傳輸設定大規模執行。

## <a name="next-steps"></a>後續步驟

深入了解[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 上。
