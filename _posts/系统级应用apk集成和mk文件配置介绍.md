

> Android源码目录下,应用的整体目录如下,根目录为Chat,文件夹中有如下结构：

![图一.png](http://upload-images.jianshu.io/upload_images/5734876-f2ee896cae8c9b27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> lib中存放的是so文件，打开lib可以看到

![图二.png](http://upload-images.jianshu.io/upload_images/5734876-3f5abd65f5c947eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Android.mk文件用来向编译系统描述你的源代码，内容如下：

```
PREBUILT_PATH := $(call my-dir)

LOCAL_PATH          := $(PREBUILT_PATH)
include $(CLEAR_VARS)

LOCAL_JNI_SHARED_LIBRARIES := libimagepipeline_chat  libjingle_peerconnection_videochat_so  libyuv_utils_videochat  libyuv
LOCAL_MODULE        := VideoChat
LOCAL_MODULE_OWNER  := qcom
LOCAL_DEX_PREOPT    := $($(LOCAL_MODULE)_DEX_PEROPT)
LOCAL_MODULE_TAGS   := optional
LOCAL_MULTILIB      := 32
LOCAL_MODULE_CLASS  := APPS
LOCAL_CERTIFICATE   := platform
LOCAL_MODULE_SUFFIX := .apk
LOCAL_SRC_FILES     := ./VideoChat.apk
LOCAL_SRC_FILES_32  := ./VideoChat.apk
include $(BUILD_PREBUILT)


LOCAL_PATH          := $(PREBUILT_PATH)
include $(CLEAR_VARS)
LOCAL_MODULE        := libjingle_peerconnection_videochat_so
LOCAL_MODULE_OWNER  := qcom
LOCAL_MODULE_TAGS   := optional
LOCAL_MODULE_CLASS  := SHARED_LIBRARIES
LOCAL_CERTIFICATE   := platform
LOCAL_MODULE_SUFFIX := .so
LOCAL_SRC_FILES     := ./lib/libjingle_peerconnection_videochat_so.so
LOCAL_MULTILIB := 32
LOCAL_32_BIT_ONLY := true
include $(BUILD_PREBUILT)

LOCAL_PATH          := $(PREBUILT_PATH)
include $(CLEAR_VARS)
LOCAL_MODULE        := libimagepipeline_chat
LOCAL_MODULE_OWNER  := qcom
LOCAL_MODULE_TAGS   := optional
LOCAL_MODULE_CLASS  := SHARED_LIBRARIES
LOCAL_CERTIFICATE   := platform
LOCAL_MODULE_SUFFIX := .so
LOCAL_SRC_FILES     := ./lib/libimagepipeline_chat.so
LOCAL_MULTILIB := 32
LOCAL_32_BIT_ONLY := true
include $(BUILD_PREBUILT)

LOCAL_PATH          := $(PREBUILT_PATH)
include $(CLEAR_VARS)
LOCAL_MODULE        := libyuv_utils_videochat
LOCAL_MODULE_OWNER  := qcom
LOCAL_MODULE_TAGS   := optional
LOCAL_MODULE_CLASS  := SHARED_LIBRARIES
LOCAL_CERTIFICATE   := platform
LOCAL_MODULE_SUFFIX := .so
LOCAL_SRC_FILES     := ./lib/libyuv_utils_videochat.so
LOCAL_MULTILIB := 32
LOCAL_32_BIT_ONLY := true
include $(BUILD_PREBUILT)

LOCAL_PATH          := $(PREBUILT_PATH)
include $(CLEAR_VARS)
LOCAL_MODULE        := libyuv
LOCAL_MODULE_OWNER  := qcom
LOCAL_MODULE_TAGS   := optional
LOCAL_MODULE_CLASS  := SHARED_LIBRARIES
LOCAL_CERTIFICATE   := platform
LOCAL_MODULE_SUFFIX := .so
LOCAL_SRC_FILES     := ./lib/libyuv.so
LOCAL_MULTILIB := 32
LOCAL_32_BIT_ONLY := true
include $(BUILD_PREBUILT)
```
>  LOCAL_JNI_SHARED_LIBRARIES 这样在编译的时候，NDK自动会把这个libxxx并不会打入apk中，只是放在系统的 /system/app/yourapk/lib/目录下

```
root@HeraFHD:/system/app/VideoChat # ls
VideoChat.apk
lib
root@HeraFHD:/system/app/VideoChat # cd lib/                                
root@HeraFHD:/system/app/VideoChat/lib # ls
arm
root@HeraFHD:/system/app/VideoChat/lib # cd arm/                            
root@HeraFHD:/system/app/VideoChat/lib/arm # ls
libimagepipeline_chat.so
libjingle_peerconnection_videochat_so.so
libyuv.so
libyuv_utils_videochat.so
```
