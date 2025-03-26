# UVCVIDEO-WEBCAM-Linux

# UVCVIDEO Quirks - https://www.ideasonboard.org/uvc/faq/
UVC_QUIRK_STATUS_INTERVAL 	0x00000001 	Interpret the status endpoint bInterval value as a number of frame instead of an exponent value.

UVC_QUIRK_PROBE_MINMAX 	0x00000002 	Don't issue GET_MIN or GET_MAX request on the video probe and commit controls. Try to set this quirk if the device stalls the minimum and maximum video probe and comit requests.

UVC_QUIRK_PROBE_EXTRAFIELDS 	0x00000004 	Initialize read-only fields of the video probe and commit structure when negotiating formats with the camera. Try to set this quirk if the device stalls the set video probe and commit requests.

UVC_QUIRK_BUILTIN_ISIGHT 	0x00000008 	The device is an Apple iSight camera. Some versions of those cameras advertise UVC compatibility but use proprietary video data encapsulation that needs to be handled specifically.

UVC_QUIRK_STREAM_NO_FID 	0x00000010 	Don't use the Frame ID field from the video payload headers. UVC devices are required to toggle the FID bit at every frame, and the uvcvideo driver uses it by default to detect the start of a new frame. Try to set this quirk if the driver doesn't return any frame to applications after successfully starting the video stream.

UVC_QUIRK_IGNORE_SELECTOR_UNIT 	0x00000020 	Ignore UVC selector units. Selector units allow selecting the active input for multi-input video devices. Many webcams expose a selector unit with a single input, which is pointless by valid nonetheless. However, at least one camera exposes a selector unit but doesn't implement associated requests. The selector unit then has to be ingored. Try to set this quirk if VIDIOC_G_INPUT fail.

UVC_QUIRK_FIX_BANDWIDTH 	0x00000080 	Try to estimate the bandwidth required for uncompressed streams instead on relying on the value reported by the camera. See FAQ 7 for more information.

UVC_QUIRK_PROBE_DEF 	0x00000100 	Don't request the video probe and commit default values. Some cameras don't implement the GET_DEF request for the video probe and commit controls, or even completely crash when that request is received. The uvcvideo driver can then use the current values as default values.

UVC_QUIRK_RESTRICT_FRAME_RATE 	0x00000200 	Ignore all frame intervals reported by the device but the first one. This quirk is used for a specific device that reports buggy frame intervals, making the image severely underexposed when selected.

* echo 'options uvcvideo quirks=0x100' > /etc/modprobe.d/uvcvideo.conf

# USB autosuspend
http://www.ideasonboard.org/uvc/faq/#faq5<br>
https://www.linuxquestions.org/questions/slackware-14/webcam-often-not-seen-on-boot-4175722346/<br>
* echo -1 > /sys/module/usbcore/parameters/autosuspend

# Reset Webcam
v4l2-ctl --verbose --device /dev/video0 --set-ctrl=brightness=128 --set-ctrl=contrast=32 --set-ctrl=saturation=38 --set-ctrl=gain=63 --set-ctrl=sharpness=20

# Capture an image from Webcam
* v4l2-ctl --device $CAMERA --set-fmt-video=width=640,height=480,pixelformat=MJPG --stream-mmap --stream-to="imagename.jpg" --stream-count=1 &>/dev/null

*** Newer ffmpeg versions appears to consume a majority of bandwidth causing other USB devices to time out or become unresponsive.
* Depreciated:  ffmpeg -y -hide_banner -loglevel panic -f video4linux2 -s 640x480 -c:v mjpeg -i $CAMERA -ss 0:0:5 -frames 1 -update true "imagename.jpg"
