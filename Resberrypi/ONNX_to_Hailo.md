
---

````markdown
# 🚀 Hailo AI Software Suite & YOLO Model Deployment Guide (Raspberry Pi)

This guide provides a complete step-by-step process for setting up the **Hailo AI Software Suite**, compiling a YOLO model (ONNX → HAR → HEF), and deploying it on a **Raspberry Pi AI HAT (Hailo-8L)**.


## 1. Prerequisites Installation

### 1.1 System Update and Dependency Installation

```bash
sudo apt update
sudo apt upgrade -y
````

### 1.2 Install Required Packages

```bash
sudo apt install -y python3-pip python3-virtualenv python3-tk \
                    graphviz libgraphviz-dev build-essential \
                    python3.10-dev dkms linux-headers-$(uname -r) \
                    wget git
```

#### Package Details

| Package                       | Purpose                                   |
| ----------------------------- | ----------------------------------------- |
| `python3-pip`                 | Python package manager                    |
| `python3-virtualenv`          | Virtual environment management            |
| `graphviz`, `libgraphviz-dev` | Model visualization and graph tools       |
| `build-essential`             | Compilation tools                         |
| `dkms`                        | Dynamic Kernel Module Support for drivers |
| `linux-headers-$(uname -r)`   | Kernel headers for compilation            |
| `wget`, `git`                 | Downloading and version control utilities |

### 1.3 Verify Python Installation

```bash
python3 --version
```

If Python 3.10 is **not** installed:

```bash
sudo apt install python3.10 python3.10-dev
```

---

## 2. Hailo AI Software Suite Installation

### 2.1 Create Working Directory

```bash
mkdir -p ~/Desktop/YOLO
cd ~/Desktop/YOLO
```

### 2.2 Download Hailo Software Suite

**Option 1: Using `wget` (recommended)**

```bash
wget <hailo_download_url>
```

**Option 2: Manual Download**

1. Visit [Hailo’s official website](https://hailo.ai/software-suite/)
2. Download the latest `.run` file (e.g., `hailo8_ai_sw_suite_2025-10.run`)
3. Move the file to `~/Desktop/YOLO/`

### 2.3 Rename and Make Executable

If the file contains special characters:

```bash
mv hailo8_ai_sw_suite_2025-10.run\?* hailo8_ai_sw_suite_2025-10.run
chmod +x hailo8_ai_sw_suite_2025-10.run
```

### 2.4 Run Installer

```bash
sudo ./hailo8_ai_sw_suite_2025-10.run
```

#### Installation Details

* Creates a virtual environment at:

  ```
  ~/Desktop/YOLO/hailo_ai_sw_suite/hailo_venv
  ```
* Installs:

  * **HailoRT (Runtime)**
  * **Hailo DFC (Dataflow Compiler)**
  * **Hailo Model Zoo**
  * Required Python dependencies

> ⚠️ **Expected Warnings (Safe to Ignore)**
>
> * Node.js version warnings (for DFC Studio GUI only)
> * GPU not found (CPU compilation is sufficient)

🕒 Installation Time: *10–20 minutes (depending on internet speed)*

### 2.5 Post-Installation Steps

Reboot to complete the setup:

```bash
sudo reboot
```

### 2.6 Activate Virtual Environment

```bash
cd ~/Desktop/YOLO
source hailo_ai_sw_suite/hailo_venv/bin/activate
```

### 2.7 Verify Installation

```bash
hailo --version
hailo --help
```

#### Common Commands

| Command          | Description                  |
| ---------------- | ---------------------------- |
| `hailo parser`   | Convert ONNX to HAR          |
| `hailo optimize` | Quantize model (FP32 → INT8) |
| `hailo compiler` | Compile HAR to HEF           |
| `hailortcli`     | Runtime testing CLI          |

---

## 3. Model Compilation Workflow

### 3.1 Prepare ONNX Model

Copy or download your ONNX model to the working directory:

```bash
cd ~/Desktop/YOLO
wget https://github.com/ultralytics/yolov5/releases/download/v7.0/yolov5s.onnx
```

### 3.2 Step 1: Parse ONNX → HAR

```bash
hailo parser onnx yolov5s.onnx --hw-arch hailo8l --har-path yolov5s.har
```

#### Parameters

| Parameter                | Description                                        |
| ------------------------ | -------------------------------------------------- |
| `yolov5s.onnx`           | Input ONNX model file                              |
| `--hw-arch hailo8l`      | Target hardware (Hailo-8L for Raspberry Pi AI HAT) |
| `--har-path yolov5s.har` | Output HAR file name                               |

#### Notes

During parsing:

* If prompted for end node recommendations → type **`y`**
* If asked to add NMS postprocessing → type **`y`**

This adds **Non-Maximum Suppression (NMS)** post-processing to YOLO output.

---

### 3.3 Step 2: Model Optimization (Quantization)

```bash
hailo optimize --hw-arch hailo8l --use-random-calib-set yolov5s.har
```

#### Process

* Converts FP32 weights → INT8 (quantization)
* Uses random synthetic calibration data
* Generates optimized HAR file: `yolov5s_optimized.har`

---

### 3.4 Step 3: Compile Optimized Model → HEF

```bash
hailo compiler --hw-arch hailo8l yolov5s_optimized.har
```

#### Output

* Maps operations to Hailo Neural Processing Core
* Produces executable `.hef` binary for deployment

---

## 4. File Transfer to Raspberry Pi

Once compilation is complete, transfer the generated `.hef` file to your Raspberry Pi.

```bash
scp <file_name> <ssh_username>@<raspberry_pi_ip>:~/
```

**Example:**

```bash
scp yolov5s.hef qss@192.168.1.213:~/
```

To transfer a directory recursively:

```bash
scp -r images qss@192.168.1.213:~/
```

---

## ✅ Summary

* Full setup of **Hailo AI Software Suite**
* Conversion pipeline: **ONNX → HAR → HEF**
* Deployment-ready model transfer for **Raspberry Pi (Hailo-8L)**

---


