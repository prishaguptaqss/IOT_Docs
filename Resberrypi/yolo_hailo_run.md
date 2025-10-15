# Running YOLO Models on Raspberry Pi using Hailo AI Hat

##  Overview

This guide explains how to run **YOLOv5** or **YOLOv8** object detection models on a **Raspberry Pi** equipped with the **Hailo AI Hat**.
You will use the compiled `.hef` model file for inference and execute a Python script to perform live camera detection.

---

## ⚙️ Prerequisites & Verification

Before proceeding, ensure that:

* The **Hailo SDK** and **Hailo Runtime** are installed and configured on your Raspberry Pi.
* The `.hef` model (e.g., `yolov5s.hef` or `yolov8s.hef`) is already compiled and available.
* Your camera module (CSI or USB) is properly connected.
* The Hailo device is recognized by running the verification command:

  ```bash
  hailortcli fw-control identify
  ```

  > This should display the connected Hailo device information and confirm it is operational.


---

## Steps to Run the YOLO Model

### **1. Navigate to the Project Directory**

Open a terminal on the Raspberry Pi and run:

```bash
cd ~/yolo_test/
```

---

### **2. Verify the Available .HEF File**

Check that the YOLO model file exists in the directory:

```bash
ls
```

Example output:

```
yolov5s.hef
yolov8_od.py
```

> The `.hef` file is the compiled model that will be executed by the Hailo AI chip.

---

### **3. Update the Model Path in the Script**

Open the file `yolov8_od.py` and update the function call with the correct `.hef` file:

```python
run_yolov8_hailo(model_path="yolov8s.hef", camera_id=0)
```

> Replace `"yolov8s.hef"` with your specific model file name (e.g., `"yolov5s.hef"` or `"yolov8m.hef"`).

---

### **4. Run the Detection Script**

Start the YOLO inference using:

```bash
python3 yolov8_od.py
```

You should see output similar to:

```
== YOLOv8 Live Detection (Hailo) ==
Hailo devices found: 1
Starting camera inference...
```

The live camera feed will open, showing real-time object detections.

---

##  Notes

* Ensure the Hailo AI Hat is correctly attached and recognized by the Raspberry Pi.
* You can experiment with different `.hef` files to compare performance (`yolov5s.hef`, `yolov8s.hef`, `yolov8m.hef`, etc.).
* To stop the script, press `Ctrl + C` or `q`.
* If you encounter device errors, recheck your Hailo installation or camera connection.

---
