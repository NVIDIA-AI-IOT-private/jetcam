# JetCam

JetCam is an easy to use Python camera interface for NVIDIA Jetson.

*  Works with various USB and CSI cameras using Jetson's [Accelerated GStreamer Plugins](https://developer.download.nvidia.com/embedded/L4T/r32_Release_v1.0/Docs/Accelerated_GStreamer_User_Guide.pdf?uIzwdFeQNE8N-vV776ZCUUEbiJxYagieFEqUoYFM9XSf9tbslxWqFKnVHu8erbZZS20A7ADAIgmSQJvXZTb0LkuGl9GoD5HJz4263HcmYWZW0t2OeFSJKZOfuWZ-lF51Pva2DSDtu2QPs-junm7BhMB_9AMQRwExuDb5zIhf_o8PIbA4KKo)
*  Easily read images as ``numpy`` arrays with ``image = camera.read()``

*  Set the camera to ``running = True`` to attach callbacks to new frames

JetCam makes it easy to prototype AI projects in Python, especially within the Jupyter Lab programming environment installed in [JetCard](http://github.com/NVIDIA-AI-IOT/jetcard).

If you find an issue, please [let us know](../..//issues)!

## Setup

```bash
git clone https://github.com/NVIDIA-AI-IOT/jetcam
cd jetcam
sudo python3 setup.py install
```

> JetCam is tested against a system configured with the [JetCard](http://github.com/NVIDIA-AI-IOT/jetcard) setup.  Different system configurations may require additional steps.

## Usage

Below we show some usage examples.  You can find more in the [notebooks](notebooks).

### Create CSI camera

Call ``CSICamera`` to use a compatible CSI camera.  ``capture_width``, ``capture_height``, and ``capture_fps`` will control the capture shape and rate that images are aquired.  ``width`` and ``height`` control the final output shape of the image as returned by the ``read`` function.

```python
from jetcam.csi_camera import CSICamera

camera = CSICamera(width=224, height=224, capture_width=1080, capture_height=720, capture_fps=30)
```

### Create USB camera

Call ``USBCamera`` to use a compatible USB camera.  The same parameters as ``CSICamera`` apply, along with a parameter ``capture_device`` that indicates the device index.  You can check the device index by calling ``ls /dev/video*``.

```python
from jetcam.usb_camera import USBCamera

camera = USBCamera(capture_device=1)
```

### Read

Call ``read()`` to read the latest image as a ``numpy.ndarray`` of data type ``np.uint8`` and shape ``(224, 224, 3)``.  The color format is ``BGR8``.

```python
image = camera.read()
```

The ``read`` function also updates the camera's internal ``value`` attribute.

```python
camera.read()
image = camera.value
```

### Callback

You can also set the camera to ``running = True``, which will spawn a thread that acquires images from the camera.  These will update the camera's ``value`` attribute automatically.  You can attach a callback to the value using the [traitlets](https://traitlets.readthedocs.io/en/stable/api.html#callbacks-when-trait-attributes-change) library.  This will call the callback with the new camera value as well as the old camera value.

```python
camera.running = True

def callback(change):
    new_image = change['new']
    # do some processing...

camera.observe(callback, names='value')
```

## Cameras

### CSI Cameras

These cameras work with the [``CSICamera``](jetcam/csi_camera.py) class.  Try them out by following the example [notebook](notebooks/csi_camera/csi_camera.ipynb).

| Model | Infared | FOV | Resolution | Cost | 
|:-------|:-----:|:---:|:---:|:----:|
| [Raspberry Pi Camera V2](https://www.amazon.com/Raspberry-Pi-Camera-Module-Megapixel/dp/B01ER2SKFS/ref=sr_1_3?keywords=raspberry+pi+v2+camera&qid=1554831689&s=electronics&sr=1-3) |  | 62.2 | 3280x2464 | $25 | 
| [Raspberry Pi Camera V2 (NOIR)](https://www.amazon.com/RPi-Camera-V2-Official-Raspberry/dp/B07P7GBJTK/ref=sr_1_1_sspa?keywords=raspberry+pi+v2+camera&qid=1554831658&s=electronics&sr=1-1-spons&psc=1) | x | 62.2 |  3280x2464 | $31 | 
| [Arducam IMX219 CS lens mount](https://www.robotshop.com/en/arducam-8mp-sony-imx219-camera-module-cs-lens-2718-raspberry-pi.html?gclid=EAIaIQobChMIzMKg38bD4QIVrR6tBh3UoAdjEAYYCSABEgLg-_D_BwE) |   |  |  3280x2464 | $65 | 
| [Arducam IMX219 M12 lens mount](https://www.robotshop.com/en/arducam-8mp-sony-imx219-camera-module-m12-lens-ls40136-raspberry-pi.html) |   |  |  3280x2464 | $60 |
| [LI-IMX219-MIPI-FF-NANO](https://leopardimaging.com/product/li-imx219-mipi-ff-nano/) |   |   |  3280x2464 | $29 |
| [WaveShare IMX219-77](https://www.waveshare.com/IMX219-77-Camera.htm) |   | 77 |  3280x2464 | $19 |
| [WaveShare IMX219-77IR](https://www.waveshare.com/IMX219-77IR-Camera.htm) | x | 77 |  3280x2464 | $21 |
| [WaveShare IMX219-120](https://www.waveshare.com/IMX219-120-Camera.htm) |   | 120 |  3280x2464 | $20 |
| [WaveShare IMX219-160](https://www.waveshare.com/IMX219-160-Camera.htm) |   | 160 |  3280x2464 | $23 |
| [WaveShare IMX219-160IR](https://www.waveshare.com/IMX219-160IR-Camera.htm) | x | 160 |  3280x2464 | $25 |
| [WaveShare IMX219-200](https://www.waveshare.com/IMX219-200-Camera.htm) |   | 200 |  3280x2464 | $27 |

### USB Cameras

These cameras work with the [``USBCamera``](jetcam/usb_camera.py) class.  Try them out by following the example [notebook](notebooks/usb_camera/usb_camera.ipynb).

| Model | Infared | FOV | Resolution | Cost | 
|:-------|:-----:|:---:|:---:|:----:|
| [Logitech C270](https://www.amazon.com/Logitech-Widescreen-designed-Calling-Recording/dp/B004FHO5Y6) |  | 60 | 1280x720 | $18 | 

## See also

- [JetBot](http://github.com/NVIDIA-AI-IOT/jetbot) - An educational AI robot based on NVIDIA Jetson Nano

- [JetRacer](http://github.com/NVIDIA-AI-IOT/jetracer) - An educational AI racecar using NVIDIA Jetson Nano
- [JetCard](http://github.com/NVIDIA-AI-IOT/jetcard) - An SD card image for web programming AI projects with NVIDIA Jetson Nano
- [torch2trt](http://github.com/NVIDIA-AI-IOT/torch2trt) - An easy to use PyTorch to TensorRT converter
