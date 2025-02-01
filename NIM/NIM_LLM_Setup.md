# How to Setup LLMs in local using NIM services?  

Each NIM service is considered to be an independent container service with a LLM and Nemo is a framework which can be used to play around with NIM. So, for each NIM we can run one LLM to the specified port.  

To run a NIM service, first we will need to add [NGC CLI](https://org.ngc.nvidia.com/setup/installers/cli) into our machines which can be used to connect to NVIDIA Cloud and run any commands related NIM service. While setting the configurations, you will need the [personal API key](https://org.ngc.nvidia.com/setup/personal-keys) from NVIDIA Organization account and use default values provided while setting up.  

You can check if ngc is installed by running the below command,  

    ngc --version

From here, you can follow the given [NVIDIA's documentation](https://docs.nvidia.com/nim/large-language-models/latest/getting-started.html) on setting up a NIM service or you can continue following this to setup quickly.   

<!-- Once ngc is setup, you will need to login into nvcr.io hub using docker to get all repos of NVIDIA that supports your machines. You can login using below command,  

    docker login nvcr.io -->

You can find the list of all NIMs that support your machine using the command,  

    ngc registry image list --format_type csv nvcr.io/nim/*

## For any memory utilization issues of the container

You will have memory occupying issues because by default TensorRT-LLM occupies 95 percent of your machine memory and also offloads some amount to your CPU if needed. This is happening because it internally uses vLLM, where even though you don't use your model it will take up all that space just for KV cache purpose. We have two ways to change that:  

### Method 1 (recommended way):  

There are some environment variables which can be used to configure the docker containers. You can find the list of all environment variables that can be passed with the docker run command [here](https://docs.nvidia.com/nim/large-language-models/latest/configuration.html). Among these, i have used NIM_RELAX_MEM_CONSTRAINTS=1.  

### Method 2:  

You can always make changes in the code scripts provided by tensorRT-LLM in the docker container. I found the parameters that can be tweaked to configure the NIM service which can be found in the path CONTAINER_NAME:/opt/nim/llm/nim_llm_sdk/trtllm/utils.py.  

You cannot directly change this file, so you will have to copy the file from container to your server, make changes and copy it back. The following commands will be useful:  

    sudo docker cp CONTAINER_NAME:/opt/nim/llm/nim_llm_sdk/trtllm/utils.py utils.py
    sudo nano utils.py
    ## and then make changes (kv_cache_mem_utilization for memory utilization),
    sudo docker cp utils.py CONTAINER_NAME:/opt/nim/llm/nim_llm_sdk/trtllm/utils.py 