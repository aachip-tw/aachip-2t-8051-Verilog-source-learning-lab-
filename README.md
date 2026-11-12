# AACHIP 2T 8051 學習實驗室

從 C 程式、8051 組合語言到 Verilog CPU 執行，使用 GTKWave 觀察 CLK、PC、ROM DATA 與周邊訊號，學習 AACHIP 2T 8051 的指令與硬體動作。

教材提供九個範例、Windows 批次操作選單、Native AOT C 編譯器、Python 組譯器、RTL 測試平台，以及 Yosys 一般邏輯合成流程。

## 快速開始

1. 在 GitHub 選擇 **Code → Download ZIP**，完整解壓縮。建議路徑：`C:\AACHIP_2T_8051_Peripherals`。
2. 安裝下表工具，確認命令可從命令提示字元執行。
3. 雙擊 `START_HERE.bat`，先選 **0** 檢查工具，再選 **1** 編譯與模擬全部九課。
4. 選 **3**，再輸入 **1**，執行 PortBlink 並開啟 GTKWave。

| 工具 | 用途 |
|---|---|
| Windows x64 | 執行內附的 AOT 編譯器與 BAT |
| Python 3.10 以上 | 組譯、課程建置與驗證腳本 |
| Icarus Verilog：`iverilog`、`vvp` | 編譯 RTL 與執行模擬 |
| GTKWave | 檢視 VCD 波形 |
| Yosys | 邏輯合成課程使用 |

可透過 OSS CAD Suite 提供 Icarus Verilog 與 Yosys；請將其 `bin` 資料夾加入 PATH。

教材已附 Windows x64 Native AOT 教學編譯器，執行課程不需安裝 AACHIP IDE、.NET Runtime 或 .NET SDK。

GTKWave 預設位置：

```text
C:\gtkwave-3.3.100-bin-win32\gtkwave\bin\gtkwave.exe
```

若安裝位置不同，可設定使用者環境變數 `GTKWAVE_EXE`；也可在命令提示字元設定後，從同一視窗啟動選單：

```bat
set "GTKWAVE_EXE=D:\gtkwave\bin\gtkwave.exe"
START_HERE.bat
```

## 選單功能

| 選項 | 功能 |
|---|---|
| 0 | 檢查所需工具 |
| 1 | 重新編譯與模擬全部九課 |
| 2 | 開啟全部已產生的課程波形 |
| 3 | 編譯、模擬並開啟指定課程 |
| 4 | 使用 Yosys 合成 8051 RTL |
| 5 | 執行 CPU 與周邊回歸驗證 |
| Q | 離開 |

每課資料夾也提供兩個入口：

- `RUN.bat`：重新編譯 C、產生 ROM、執行模擬並開啟波形。**修改 C 程式後使用這個入口。**
- `OPEN_WAVE.bat`：開啟上次成功產生的波形，不重新編譯或模擬。

## 九個學習範例

課程來源位於 `Learn-Embedded-C-with-8051-Microcontroller-master/`。

| 課程 | 學習內容 |
|---|---|
| 01.PortBlink | Port 輸出、延遲迴圈與 LED 切換 |
| 02.Blink_LED | LED 閃爍與輸出控制 |
| 03.LED_Pin | 單一 GPIO 腳位控制 |
| 04.StepperMotor | 步進馬達控制序列 |
| 05.LCD | LCD 4-bit 初始化、命令與字元傳輸 |
| 06.Switch | 開關輸入與輸出反應 |
| 07.OR_Logic | 開關輸入的 OR 邏輯 |
| 08.AND_Logic | 開關輸入的 AND 邏輯 |
| 09.Timer | Timer0 設定、溢位與重新裝載 |

預設採用快速模擬延遲，方便在短時間內觀察完整動作。實體馬達與 LCD 的電氣、驅動及等待時間須另依硬體規格設計。

## 從 C 到波形

```text
main.c
  ↓ AACHIP Native AOT C Compiler
main.asm
  ↓ Python 8051 組譯器
main.bin / main.hex
  ↓ 產生 rom.mem，載入 Verilog 測試平台
8051 RTL 模擬
  ↓ Icarus Verilog
wave.vcd
  ↓ GTKWave
觀察 CPU 取指、指令執行與周邊輸出
```

產物集中在 `build/lessons/<課程名稱>/`。例如 PortBlink：

```text
build/lessons/01.PortBlink/
├─ main.asm                 組合語言
├─ main.bin                 原始 ROM 機器碼
├─ main.hex                 Intel HEX
├─ main.LIS / main.Lst      組譯列表
├─ main.Dis                 位址與機器碼對照
├─ rom.mem                  Verilog ROM 載入資料
├─ wave.vcd                 模擬波形
├─ detail.gtkw              波形訊號與顯示設定
├─ overview.gtkw            波形總覽設定
└─ result.json              課程驗證結果
```

文字輸出含 AACHIP 抬頭。BIN 與 HEX 保持可直接載入的格式，完整中文抬頭放在同名 `.bin.txt`、`.hex.txt`。為相容 GTKWave 3.3.100，VCD 使用英文註解，完整抬頭另存 `wave.vcd.txt`。

## 如何學習 2T 時序

先在 GTKWave 放大時間軸，再搭配組譯列表觀察：

| 訊號 | 觀察重點 |
|---|---|
| `clk`、`rst_n` | 時鐘週期與重置釋放 |
| `pc` | 程式計數器與跳轉目標 |
| `rom_addr`、`rom_data` | ROM 存取位址、opcode 與操作數 |
| `fetch`、`opcode`、`state` | 取指與指令執行流程 |
| `acc`、`sp`、`psw` | 暫存器及旗標變化 |
| `led`、`p0`～`p3` | 指令對輸出腳位的影響 |
| Timer、LCD、開關等訊號 | 各課周邊行為 |

**2T 不表示每條指令一律兩個 CLK。** 請選定指令，依核心執行流程數時脈；課程結果顯示的總 CLK 是測試完成前的累積值。

重置跳轉也必須以目前 BIN 為準。例如 `02 00 30` 是 `LJMP 0030h`，`02 00 03` 則是 `LJMP 0003h`；可搭配 `image_reset_bytes`、`image_reset_target` 與 PC 核對。

## 模擬、合成與驗證

九課已通過 RTL 功能模擬；組譯器以已知機器碼向量驗證全部 255 個有效基本 8051 opcode，另檢查資料格式及錯誤輸入。CPU／周邊回歸驗證與 Yosys 一般邏輯合成也已通過。

GTKWave 顯示 RTL 模擬的 VCD。Yosys 合成產物位於 `build/synthesis/`，包括網表、JSON 與紀錄。實際下載 FPGA 尚需板級頂層、ROM、腳位及時鐘約束，並完成佈局繞線與時序驗證。

## 進一步閱讀

- [完整操作手冊](docs/8051實驗操作手冊.md)
- [九課內容與驗證結果](docs/九個範例與驗證結果.md)
- [精簡 AOT 教學引擎](docs/精簡教學引擎.md)
- [學生閱讀指南](docs/學生閱讀指南.md)
- [8051 核心與組裝說明](docs/8051核心與組裝說明.md)
- [RTL 詳細中文說明](docs/RTL_詳細中文說明.md)
- [原有套件與介面說明](README_中文.md)

## 使用範圍與版權

AACHIP TECHNOLOGY CO LTD;TAIWAN 合合科技有限公司

本教材目前沿用[內部學習使用聲明](NOTICE_內部學習使用.txt)，僅供公司內部員工學習與訓練，未經事先書面許可不得對外散布或公開發布。儲存庫建議設為 **Private**，依授權邀請使用者；本教材未授予整套內容的開源授權。

原始範例及第三方內容的授權聲明請一併保留，包括[範例來源 LICENSE](Learn-Embedded-C-with-8051-Microcontroller-master/LICENSE)與[第三方執行庫聲明](tools/TeachingEngine/THIRD_PARTY_NOTICES.txt)。
