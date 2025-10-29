# Lab 1: ESP-IDF Setup และโปรเจกต์แรก

## วัตถุประสงค์
ติดตั้งและตรวจสอบการทำงานของ ESP-IDF พร้อมสร้างโปรเจกต์แรก

## เวลาที่ใช้
45 นาที

## ข้อกำหนดเบื้องต้น
- ESP32 Development Board
- USB Cable
- คอมพิวเตอร์ที่ติดตั้ง ESP-IDF แล้ว

## ขั้นตอนการทดลอง

### Step 1: ตรวจสอบการติดตั้ง ESP-IDF (10 นาที)

```bash
# 1. ตรวจสอบ ESP-IDF environment
echo $IDF_PATH

# 2. ตรวจสอบเวอร์ชัน
idf.py --version

# 3. ตรวจสอบ Python และ pip
python --version
pip --version

# 4. ตรวจสอบ toolchain
xtensa-esp32-elf-gcc --version
```

**ผลลัพธ์ที่คาดหวัง:**
```
ESP-IDF v5.x
Python 3.x.x
xtensa-esp32-elf-gcc version x.x.x
```

### Step 2: สร้างโปรเจกต์แรก (15 นาที)

```bash
# 1. สร้างโปรเจกต์ใหม่
idf.py create-project hello_esp32
cd hello_esp32

# 2. ดูโครงสร้างโปรเจกต์
ls -la
```

**โครงสร้างโปรเจกต์:**
```
hello_esp32/
├── CMakeLists.txt          # Build configuration
├── main/
│   ├── CMakeLists.txt      # Main component
│   └── hello_esp32.c       # Main source file
├── README.md               # Project documentation
└── sdkconfig               # Project configuration (after build)
```

### Step 3: ศึกษาไฟล์หลัก (10 นาที)

**ไฟล์ `main/hello_esp32.c`:**
```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

void app_main(void)
{
    printf("Hello, ESP32 World!\n");
    
    // Keep the program running
    while(1) {
        printf("ESP32 is running...\n");
        vTaskDelay(pdMS_TO_TICKS(1000)); // 1 second delay
    }
}
```

**ไฟล์ `CMakeLists.txt` (root):**
```cmake
cmake_minimum_required(VERSION 3.16)
include($ENV{IDF_PATH}/tools/cmake/project.cmake)
project(hello_esp32)
```

**ไฟล์ `main/CMakeLists.txt`:**
```cmake
idf_component_register(SRCS "hello_esp32.c"
                       INCLUDE_DIRS ".")
```

### Step 4: Build โปรเจกต์ (10 นาที)

```bash
# 1. กำหนด target chip
idf.py set-target esp32

# 2. Build โปรเจกต์
idf.py build
```

**ข้อความที่ควรเห็น:**
```
Project build complete. To flash, run:
  idf.py flash
or
  idf.py -p (PORT) flash
```

**ไฟล์ที่ถูกสร้าง:**
```
build/
├── bootloader/
├── partition_table/
├── hello_esp32.bin         # Main application binary
├── hello_esp32.elf         # ELF file with debug info
└── flash_args              # Flash arguments
```

## การแก้ไขปัญหาที่อาจเกิดขึ้น

### ปัญหา 1: ESP-IDF not found
```bash
# แก้ไข: Set up environment
source $HOME/esp/esp-idf/export.sh
# หรือ (Windows)
%USERPROFILE%\esp\esp-idf\export.bat
```

### ปัญหา 2: Permission denied
```bash
# Linux/macOS
sudo chmod 666 /dev/ttyUSB0
# หรือเพิ่ม user ใน dialout group
sudo usermod -a -G dialout $USER
```

### ปัญหา 3: Target not set
```bash
idf.py set-target esp32
```

## แบบฝึกหัด

### Exercise 1: แก้ไขข้อความ
แก้ไขไฟล์ `main/hello_esp32.c` ให้แสดงข้อความที่แตกต่าง:

```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_system.h"

void app_main(void)
{
    printf("=== My First ESP32 Project ===\n");
    printf("Chip model: %s\n", esp_get_idf_version());
    printf("Free heap: %d bytes\n", esp_get_free_heap_size());
    
    int counter = 0;
    while(1) {
        printf("Running for %d seconds\n", counter++);
        vTaskDelay(pdMS_TO_TICKS(1000));
    }
}
```

### Exercise 2: เพิ่ม Build Information
สร้างไฟล์ `main/build_info.h`:

```c
#ifndef BUILD_INFO_H
#define BUILD_INFO_H

#define PROJECT_NAME "Hello ESP32"
#define PROJECT_VERSION "1.0.0"
#define BUILD_DATE __DATE__
#define BUILD_TIME __TIME__

#endif
```

แก้ไข `main/hello_esp32.c`:

```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_system.h"
#include "build_info.h"

void app_main(void)
{
    printf("=== %s v%s ===\n", PROJECT_NAME, PROJECT_VERSION);
    printf("Built on: %s %s\n", BUILD_DATE, BUILD_TIME);
    printf("ESP-IDF Version: %s\n", esp_get_idf_version());
    printf("Chip: %s\n", CONFIG_IDF_TARGET);
    printf("Free heap: %d bytes\n", esp_get_free_heap_size());
    
    while(1) {
        printf("System is running...\n");
        vTaskDelay(pdMS_TO_TICKS(2000));
    }
}
```

### Exercise 3: การ Build แบบ Verbose
```bash
# Build with verbose output
idf.py -v build

# Clean และ build ใหม่
idf.py clean
idf.py build
```

## Checklist การทำงาน

- [x] ตรวจสอบ ESP-IDF environment สำเร็จ
- [x] สร้างโปรเจกต์ใหม่ได้
- [x] เข้าใจโครงสร้างโปรเจกต์
- [x] Build โปรเจกต์สำเร็จ
- [x] แก้ไขโค้ดและ build ใหม่ได้
- [x] เข้าใจไฟล์ CMakeLists.txt
- [x] ทำแบบฝึกหัดครบ

## คำถามทบทวหน

### 1. ไฟล์ที่จำเป็นสำหรับโปรเจกต์ ESP-IDF ขั้นต่ำ

| ไฟล์/โฟลเดอร์ | หน้าที่ |
|----------------|----------|
| `CMakeLists.txt` | กำหนดการ build (ระบุ source, include, dependencies) |
| `main/` | โฟลเดอร์หลักเก็บซอร์สโค้ดของผู้ใช้ |
| `main/CMakeLists.txt` | ระบุไฟล์ source ภายใน `main/` |
| `main/app_main.c` | จุดเริ่มต้นของโปรแกรม (เหมือน `main()` ใน C) |
| `sdkconfig` | เก็บค่าการตั้งค่าคอมไพล์ของโปรเจกต์ (สร้างหลัง `idf.py menuconfig`) |

---

### 2. ความแตกต่างระหว่าง `.bin` และ `.elf`

| ไฟล์ | รายละเอียด |
|-------|--------------|
| `hello_esp32.elf` | ไฟล์ผลลัพธ์ที่มีข้อมูลดีบัก (symbol, address mapping) ใช้กับ GDB หรือ debugger |
| `hello_esp32.bin` | ไฟล์ไบนารีสำหรับแฟลชลงบอร์ดจริง ไม่มีข้อมูลดีบัก |

> 🔧 **สรุป:**  
> - `.elf` → ใช้สำหรับดีบัก  
> - `.bin` → ใช้สำหรับแฟลชลงชิป

---

### 3. คำสั่ง `idf.py set-target`

ใช้สำหรับกำหนดชนิดของชิป (Target Chip) ให้ระบบ build ถูกต้อง เช่น:

```bash
idf.py set-target esp32
idf.py set-target esp32s3
idf.py set-target esp32c3

---

### 4. เนื้อหาในโฟลเดอร์ `build/`

หลังรันคำสั่ง `idf.py build` จะได้โฟลเดอร์ `build/` ที่มีไฟล์หลักดังนี้:

| ไฟล์/โฟลเดอร์ | หน้าที่ |
|----------------|----------|
| `hello_esp32.elf` | ไฟล์ผลลัพธ์พร้อมข้อมูลดีบัก |
| `hello_esp32.bin` | ไฟล์สำหรับแฟลชลงชิป |
| `bootloader/` | โค้ดบูทโหลดเดอร์ที่คอมไพล์แล้ว |
| `partition_table/` | ตารางพาร์ติชันของเฟิร์มแวร์ |
| `CMakeFiles/` | ไฟล์ควบคุมการ build |
| `flasher_args.json` | เก็บพารามิเตอร์สำหรับแฟลช เช่น address และ path |

---

### 5. การใช้ `vTaskDelay()` แทน `delay()`

| ฟังก์ชัน | ลักษณะการทำงาน | ผลกระทบต่อระบบ |
|-----------|----------------|----------------|
| `delay()` | หน่วงเวลาแบบ Busy-wait (CPU หยุดรอ) | ทำให้ระบบเสียเวลา CPU, ไม่เหมาะกับ Multitasking |
| `vTaskDelay()` | ปล่อยให้ Task อื่นทำงานระหว่างรอ | ประสิทธิภาพสูง, เหมาะกับระบบ RTOS |

## บทสรุป

ในแลปนี้คุณได้เรียนรู้:
- การตรวจสอบ ESP-IDF environment
- การสร้างโปรเจกต์ ESP-IDF
- โครงสร้างโปรเจกต์และไฟล์สำคัญ
- กระบวนการ build
- การแก้ไขปัญหาเบื้องต้น

**ขั้นตอนต่อไป:** Lab 2 - Hello World และ Serial Communication
