# KERNEL OVERVIEW
```
Sources:
https://source.android.com/docs/core/architecture/kernel
```

Android kernel is based on upstream Linux LTS kernel. LTS kernels are then combined with Android-specific patches to form what are known as **Android Common Kernels (ACKs)**.

Newer ACKs (version 5.4 and above) are also known as **GKI kernels**, which supports the separation of the hardware-agnostic generic core kernel code and GKI modules from the hardware-specific vendor modules
