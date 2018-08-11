Code that can help to debug metadata_data_t struct used by qualcomm camera code.

It is located in QCamera2/stack/common/cam_intf.h

Main idea is to compare output from functions get_pointer_of() and get_size_of() from binary libmmcamera2_mct.so, (liboemcamera.so for older devices) with values defined in oss header.

To get started, generate the param_ids.h header using struct.py. This creates a FOR_EVERY_PARAM_ID macro read during compile time, which can be easily modified/regenerated later on.

```
#define FOR_EVERY_PARAM_ID(MACRO) \
    MACRO(CAM_INTF_META_HISTOGRAM) \
    MACRO(CAM_INTF_META_FACE_DETECTION) \
    MACRO(CAM_INTF_META_FACE_RECOG) \
```

Next build it and copy get_offsets binary to /system/vendor/lib on your device where libmmcamera2_mct.so located.

Run it and it will produce output like this 

```
BLOB: CAM_INTF_META_AUTOFOCUS_DATA index=19 pointer=8784 size=56
OSS : CAM_INTF_META_AUTOFOCUS_DATA index=19 pointer=8784 size=56
FIXME!!!
BLOB: CAM_INTF_META_CDS_DATA index=203 pointer=310378496 size=0
OSS : CAM_INTF_META_CDS_DATA index=203 pointer=8840 size=68
BLOB: CAM_INTF_PARM_UPDATE_DEBUG_LEVEL index=175 pointer=8908 size=4
OSS : CAM_INTF_PARM_UPDATE_DEBUG_LEVEL index=175 pointer=8908 size=4
```

* BLOB means values found in libmmcamera2_mct.so
* OSS means values from your current cam_intf.h
* FIXME means fields that different by offset or by size. Some BLOB values will be size=0 and big pointer, it means this field not used in blob (but thats *not* mean you have to remove it from oss HAL).
