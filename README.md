# 📱 Cell Phone Detector for Exams

## 📋 Description

Automatic cell phone detection system using artificial intelligence and an ESP32-CAM camera. This project is designed to prevent cheating during exams by detecting the presence of mobile phones and triggering a sound alert.

## ⚙️ Features

- ✅ Real-time object detection via YOLOv3
- ✅ Specific cell phone identification
- ✅ Sound alert (buzzer) upon detection
- ✅ Video streaming from ESP32-CAM
- ✅ Display detected objects with confidence level
- ✅ Visual interface with bounding boxes and labels
- ✅ Automatic HTTP request to ESP32 when phone detected

## 🛠️ Required Hardware

### Components
- **ESP32-CAM** (AI-Thinker model)
- **Active buzzer** (connected to GPIO 13)
- **USB cable** for programming
- **FTDI Adapter** (if needed for programming)
- **Computer** to run the Python detection script

### Specifications
- Camera resolution: up to 1600x1200 (UXGA)
- Connection: WiFi 2.4GHz
- Power supply: 5V
- Detection model: YOLOv3 (COCO dataset)

## 💻 Installation

### 1. ESP32-CAM Setup

#### Prerequisites
- Arduino IDE installed
- ESP32 board libraries installed in Arduino IDE

#### Steps
1. Open the provided Arduino code (`esp32cam.ino`) in Arduino IDE
2. Modify WiFi credentials:
```cpp
const char* WIFI_SSID = "your_ssid";
const char* WIFI_PASS = "your_password";
```
3. Select board: **Tools > Board > AI Thinker ESP32-CAM**
4. Upload the code to ESP32-CAM
5. Open Serial Monitor (115200 baud rate)
6. Note the IP address displayed after WiFi connection

### 2. Computer Setup

#### Python and Dependencies Installation

```bash
# Install required libraries
pip install numpy opencv-python opencv-contrib-python
```

#### Download YOLOv3 Files

You need three files to run the detection system:

1. **Configuration file**: `yolov31.cfg`
   - Download from: [YOLO GitHub - yolov3.cfg](https://github.com/pjreddie/darknet/blob/master/cfg/yolov3.cfg)
   - Rename to `yolov31.cfg`

2. **Model weights**: `yolov3.weights`
   - Download from: [YOLOv3 Weights (~237 MB)](https://pjreddie.com/media/files/yolov3.weights)

3. **Class names file**: `coco.names.txt`
   - Download from: [COCO Names](https://github.com/pjreddie/darknet/blob/master/data/coco.names)

#### Project Structure
```
project/
│
├── detection.py          # Python detection script
├── esp32cam.ino         # ESP32-CAM Arduino code
├── yolov31.cfg          # YOLOv3 configuration
├── yolov3.weights       # Pre-trained weights
└── coco.names.txt       # Object class names
```

## 🚀 Usage

### Starting the System

1. **Power on the ESP32-CAM**
   - Verify the buzzer is properly connected to GPIO 13
   - Wait for WiFi connection (check Serial Monitor)

2. **Update IP address in Python script**

Open `detection.py` and modify:
```python
url = 'http://YOUR_ESP32_IP/cam.jpg'
```
Example: `url = 'http://192.168.1.16/cam.jpg'`

Also update the alert URL:
```python
urllib.request.urlopen('http://YOUR_ESP32_IP/phone')
```

3. **Run the detection script**
```bash
python detection.py
```

### Operation

- The "ESP32-CAM Detection" window will open showing the live video feed
- Detected objects are highlighted with purple bounding boxes
- Object names and confidence percentages are displayed
- When a cell phone is detected:
  - A purple box appears around it labeled "CELL PHONE XX%"
  - An HTTP request is sent to the ESP32-CAM
  - The buzzer sounds for 1 second (1000 Hz tone)
  
### Controls

- **Press 'Q'**: Quit the application
- The video is automatically resized to 50% for better display

## 🔧 Configuration Parameters

### Detection Settings (in `detection.py`)

```python
whT = 320                # Input size for YOLO (320x320)
confThreshold = 0.5      # Confidence threshold (50%)
nmsThreshold = 0.3       # Non-maximum suppression threshold
```

**Adjusting parameters:**
- **Increase `confThreshold`** (e.g., 0.7) → Fewer false positives, might miss some detections
- **Decrease `confThreshold`** (e.g., 0.3) → More detections, but more false positives
- **Adjust `nmsThreshold`** (0.2-0.5) → Controls overlapping box elimination

### Camera Settings (in `esp32cam.ino`)

```cpp
config.frame_size = FRAMESIZE_UXGA;  // 1600x1200
config.jpeg_quality = 10;             // Quality (10-63, lower = better)
```

**Available frame sizes:**
- `FRAMESIZE_UXGA` - 1600x1200 (high quality, slower)
- `FRAMESIZE_SVGA` - 800x600 (balanced)
- `FRAMESIZE_VGA` - 640x480 (fast, lower quality)

## 🔍 How It Works

### Detection Process

1. **Image Capture**: ESP32-CAM captures frames and serves them via HTTP
2. **Image Processing**: Python script downloads and decodes the JPEG image
3. **Object Detection**: YOLOv3 neural network analyzes the image
4. **Classification**: Detected objects are classified using COCO dataset (80 classes)
5. **Phone Detection**: If "cell phone" class is detected with sufficient confidence
6. **Alert Trigger**: HTTP request sent to ESP32-CAM `/phone` endpoint
7. **Buzzer Activation**: ESP32 receives request and sounds the buzzer


## 📊 Detection Classes

The system can detect 80 different object classes from the COCO dataset, including:
- Cell phones (primary target)
- Laptops, keyboards, mice
- Books, bottles, bags
- People, chairs, tables
- And many more...

Only cell phone detection triggers the alarm.

## ⚠️ Troubleshooting

### Common Issues

**Problem**: Cannot connect to ESP32-CAM
- Check WiFi credentials
- Verify ESP32 is powered on
- Ensure computer and ESP32 are on the same network
- Check IP address in Serial Monitor

**Problem**: No image displayed
- Verify the URL in Python script matches ESP32 IP
- Check camera initialization in Serial Monitor
- Try reducing frame size in ESP32 code

**Problem**: Buzzer doesn't sound
- Verify buzzer connection to GPIO 13
- Check buzzer polarity (if polarized)
- Test with a simple Arduino sketch first

**Problem**: Low detection accuracy
- Adjust `confThreshold` parameter
- Improve lighting conditions
- Ensure camera is properly focused
- Try different frame sizes

**Problem**: Slow performance
- Reduce frame size on ESP32
- Increase JPEG quality number (lower quality, faster)
- Use a more powerful computer
- Reduce `whT` parameter (e.g., 256)

## 🔐 Security Considerations

- This system should be used in accordance with local privacy laws
- Inform exam participants about video surveillance
- Secure the WiFi network used by ESP32-CAM
- Consider encrypting the video stream for sensitive environments

## 📝 License

This project is provided as-is for educational purposes. Please ensure compliance with local regulations regarding surveillance and privacy.

## 🤝 Contributing

Improvements and suggestions are welcome! Potential enhancements:
- Multiple camera support
- Database logging of detections
- Web-based dashboard
- Email/SMS notifications
- Improved detection models (YOLOv5, YOLOv8)

## 📞 Support

For issues or questions:
1. Check the troubleshooting section
2. Verify all connections and configurations
3. Review Serial Monitor output for error messages

---

**Note**: This system is designed as a deterrent and monitoring tool. It should be used as part of a comprehensive exam security strategy, not as the sole anti-cheating measure.
