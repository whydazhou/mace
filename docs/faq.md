Frequently asked questions
==========================

Does the tensor data consume extra memory when compiled into C++ code?
----------------------------------------------------------------------
When compiled into C++ code, the tensor data will be mmaped by the system
loader. For the CPU runtime, the tensor data are used without memory copy.
For the GPU and DSP runtime, the tensor data are used once during model
initialization. The operating system is free to swap the pages out, however,
it still consumes virtual memory addresses. So generally speaking, it takes
no extra physical memory. If you are short of virtual memory space (this
should be very rare), you can use the option to load the tensor data from
data file (can be manually unmapped after initialization) instead of compiled
code.

Why is the generated static library file size so huge?
-------------------------------------------------------
The static library is simply an archive of a set of object files which are
intermediate and contain much extra information, please check whether the
final binary file size is as expected.

Why is the generated binary file (including shared library) size so huge?
-------------------------------------------------------------------------
When compiling the model into C++ code, the final binary may contains extra
debug symbols, they usually take a lot of space. Try to strip the shared
library or binary and make sure you are following best practices to reduce
the size of an ELF binary, including disabling C++ exception, disabling RTTI,
avoiding C++ iostream, hidden internal functions etc.
In most cases, the expected overhead should be less than
{model weights size in float32}/2 + 3MB.

OpenCL allocator failed with CL_OUT_OF_RESOURCES
------------------------------------------------
OpenCL runtime usually requires continuous virtual memory for its image buffer,
the error will occur when the OpenCL driver can't find the continuous space
due to high memory usage or fragmentation. Several solutions can be tried:

* Change the model by reducing its memory usage
* Split the Op with the biggest single memory buffer
* Change from armeabi-v7a to arm64-v8a to expand the virtual address space
* Reduce the memory consumption of other modules of the same process

Why is the performance worse than the official result for the same model?
-------------------------------------------------------------------------
The power options may not set properly, see `mace/public/mace_runtime.h` for
details.

Why is the UI getting poor responsiveness when running model with GPU runtime?
------------------------------------------------------------------------------
Try to set `limit_opencl_kernel_time` to `1`. If still not resolved, try to
modify the source code to use even smaller time intervals or changed to CPU
or DSP runtime.
