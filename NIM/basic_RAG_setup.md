# How to setup basic RAG pipeline using NIM?  

To setup a basic RAG pipeline we will need a LLM model and an embedding model with a database to store the documents. The prereqisites for this would be setting up ngc and logging into nvcr.io using docker which can be found in [here](./NIM_LLM_Setup.md).

Once you have ngc in your machine and you need to login into nvcr.io using docker.  

    sudo docker login nvcr.io

You will enter username as <mark>$oauthtoken</mark> with password as your <mark>NGC_API_KEY</mark>.

After logging into it successfully, make sure to set environment variables permanently so that would be applicable to all users in the machine. You can do that by opening the .bashrc file and add the following commands at the end and save the file.  

    export NGC_API_KEY=<NGC_API_KEY>
    export LOCAL_NIM_CACHE=~/.cache/nim

Now, you pull the images of the LLM and embedding model using the given docker commands.

    # gemma 2 2B model
    sudo docker run -it --gpus all --shm-size=16GB -e NGC_API_KEY=$NGC_API_KEY -e NIM_RELAX_MEM_CONSTRAINTS=1 -v "$LOCAL_NIM_CACHE:/opt/nim/.cache" -u $(id -u) -p 7300:8000 --name gemma nvcr.io/nim/google/gemma-2-2b-instruct:latest

    # embedding model
    sudo docker run -it --gpus all --shm-size=16GB -e NGC_API_KEY=$NGC_API_KEY -e NIM_RELAX_MEM_CONSTRAINTS=1 -v "$LOCAL_NIM_CACHE:/opt/nim/.cache" -u $(id -u) -p 7301:8000 --name embed nvcr.io/nim/nvidia/nv-embedqa-e5-v5:latest

Both the models are now available at 7300 and 7301 ports of your server respectively.

Finally, you can use the given [ipynb file](https://github.com/NVIDIA/GenerativeAIExamples/blob/main/RAG/notebooks/langchain/RAG_Langchain_with_Local_NIM.ipynb) which will use these 2 models and faiss_gpu as vectorDB which takes few URLs as chunks and save them. Please mention the URLs where the models have been hosted into the respective sections of the file.