**What**: tcp_dma_copybreak sysctl

**Date**: 在内核 v3.13 中被移除

**Contact**: Dan Williams <dan.j.williams@intel.com>

**Description**:

以前是将套接字读取的大小的下限（以字节为单位），这些读取将被卸载到 DMA 复制引擎。由于在 DMA 传输期间，CPU 可能会触及缓冲区的一致性问题，已被移除。