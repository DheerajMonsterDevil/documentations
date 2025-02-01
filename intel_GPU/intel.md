# How to use INTEL GPUs for running processes?
There are several ways of using INTEL GPUs for running processes.  

To use CUDA in INTEL GPUs, we will need to use ZLUDA tool which will replace few INTEL files with NVIDIA files and makes CUDA believe that it is a NVIDIA GPU. Some people suggest OpenVino is also a similar tool to ZLUDA.  

The advised approach to run GPU-accelerated programs would to use tools like Intel oneAPI, OpenCL, Level Zero API, SYCL. These tools do not install any CUDA into our systems but can run the programs in GPUs.  

Let us look into how to setup Intel oneAPI for GPU-accelerated programs,  

## How to run using Intel oneAPI?  
**Step 1:** Install Intel oneAPI toolkit into your machines.  

For Linux,

    wget https://registrationcenter-download.intel.com/akdlm/irc_nas/19185/l_BaseKit_p_2021.4.0.305_offline.sh
    chmod +x l_BaseKit_p_2021.4.0.305_offline.sh
    sudo ./l_BaseKit_p_2021.4.0.305_offline.sh

run the above commands.


**Step 2:** Run this script,

    C:\Program Files (x86)\Intel\oneAPI\setvars.bat

For Linux, 

    source /opt/intel/oneapi/setvars.sh

**Step 3:** Run and check using this script which is written using DPC++ whether it is running on GPU.  

    #include <CL/sycl.hpp>
    #include <iostream>

    int main() {
        // Create a SYCL queue that automatically selects the best device
        cl::sycl::queue q(cl::sycl::default_selector{});

        // Get the device associated with the queue
        cl::sycl::device device = q.get_device();

        // Print the device name
        std::cout << "Running on device: " << device.get_info<cl::sycl::info::device::name>() << std::endl;

        // Your code here...

        return 0;
    }  

To compile the code,  

    dpcpp gpu_example.cpp -o gpu_example

To run the code,  

    ./gpu_example

You can even check if it is running in GPU using INTEL GPU tools.  