🚨🚨 You can run localGPT on a pre-configured Virtual Machine. Make sure to use the code: PromptEngineering to get 50% off. I will get a small commision!

LocalGPT is an open-source initiative that allows you to converse with your documents without compromising your privacy. With everything running locally, you can be assured that no data ever leaves your computer. Dive into the world of secure, local document interactions with LocalGPT.

Features 🌟
Utmost Privacy: Your data remains on your computer, ensuring 100% security.
Versatile Model Support: Seamlessly integrate a variety of open-source models, including HF, GPTQ, GGML, and GGUF.
Diverse Embeddings: Choose from a range of open-source embeddings.
Reuse Your LLM: Once downloaded, reuse your LLM without the need for repeated downloads.
Chat History: Remembers your previous conversations (in a session).
API: LocalGPT has an API that you can use for building RAG Applications.
Graphical Interface: LocalGPT comes with two GUIs, one uses the API and the other is standalone (based on streamlit).
GPU, CPU & MPS Support: Supports multiple platforms out of the box, Chat with your data using CUDA, CPU or MPS and more!
Dive Deeper with Our Videos 🎥
Detailed code-walkthrough
Llama-2 with LocalGPT
Adding Chat History
LocalGPT - Updated (09/17/2023)
Technical Details 🛠️
By selecting the right local models and the power of LangChain you can run the entire RAG pipeline locally, without any data leaving your environment, and with reasonable performance.

ingest.py uses LangChain tools to parse the document and create embeddings locally using InstructorEmbeddings. It then stores the result in a local vector database using Chroma vector store.
run_localGPT.py uses a local LLM to understand questions and create answers. The context for the answers is extracted from the local vector store using a similarity search to locate the right piece of context from the docs.
You can replace this local LLM with any other LLM from the HuggingFace. Make sure whatever LLM you select is in the HF format.
This project was inspired by the original privateGPT.

Built Using 🧩
LangChain
HuggingFace LLMs
InstructorEmbeddings
LLAMACPP
ChromaDB
Streamlit
Environment Setup 🌍
📥 Clone the repo using git:
git clone https://github.com/PromtEngineer/localGPT.git
🐍 Install conda for virtual environment management. Create and activate a new virtual environment.
conda create -n localGPT python=3.10.0
conda activate localGPT
🛠️ Install the dependencies using pip
To set up your environment to run the code, first install all requirements:

pip install -r requirements.txt
Installing LLAMA-CPP :

LocalGPT uses LlamaCpp-Python for GGML (you will need llama-cpp-python <=0.1.76) and GGUF (llama-cpp-python >=0.1.83) models.

To run the quantized Llama3 model, ensure you have llama-cpp-python version 0.2.62 or higher installed.

If you want to use BLAS or Metal with llama-cpp you can set appropriate flags:

For NVIDIA GPUs support, use cuBLAS

# Example: cuBLAS
CMAKE_ARGS="-DLLAMA_CUBLAS=on" FORCE_CMAKE=1 pip install llama-cpp-python --no-cache-dir
For Apple Metal (M1/M2) support, use

# Example: METAL
CMAKE_ARGS="-DLLAMA_METAL=on"  FORCE_CMAKE=1 pip install llama-cpp-python --no-cache-dir
For more details, please refer to llama-cpp

Docker 🐳
Installing the required packages for GPU inference on NVIDIA GPUs, like gcc 11 and CUDA 11, may cause conflicts with other packages in your system. As an alternative to Conda, you can use Docker with the provided Dockerfile. It includes CUDA, your system just needs Docker, BuildKit, your NVIDIA GPU driver and the NVIDIA container toolkit. Build as docker build -t localgpt ., requires BuildKit. Docker BuildKit does not support GPU during docker build time right now, only during docker run. Run as docker run -it --mount src="$HOME/.cache",target=/root/.cache,type=bind --gpus=all localgpt.

Test dataset
For testing, this repository comes with Constitution of USA as an example file to use.

Ingesting your OWN Data.
Put your files in the SOURCE_DOCUMENTS folder. You can put multiple folders within the SOURCE_DOCUMENTS folder and the code will recursively read your files.

Support file formats:
LocalGPT currently supports the following file formats. LocalGPT uses LangChain for loading these file formats. The code in constants.py uses a DOCUMENT_MAP dictionary to map a file format to the corresponding loader. In order to add support for another file format, simply add this dictionary with the file format and the corresponding loader from LangChain.

DOCUMENT_MAP = {
    ".txt": TextLoader,
    ".md": TextLoader,
    ".py": TextLoader,
    ".pdf": PDFMinerLoader,
    ".csv": CSVLoader,
    ".xls": UnstructuredExcelLoader,
    ".xlsx": UnstructuredExcelLoader,
    ".docx": Docx2txtLoader,
    ".doc": Docx2txtLoader,
}
Ingest
Run the following command to ingest all the data.

If you have cuda setup on your system.

python ingest.py
You will see an output like this: Screenshot 2023-09-14 at 3 36 27 PM

Use the device type argument to specify a given device. To run on cpu

python ingest.py --device_type cpu
To run on M1/M2

python ingest.py --device_type mps
Use help for a full list of supported devices.

python ingest.py --help
This will create a new folder called DB and use it for the newly created vector store. You can ingest as many documents as you want, and all will be accumulated in the local embeddings database. If you want to start from an empty database, delete the DB and reingest your documents.

Note: When you run this for the first time, it will need internet access to download the embedding model (default: Instructor Embedding). In the subsequent runs, no data will leave your local environment and you can ingest data without internet connection.

Ask questions to your documents, locally!
In order to chat with your documents, run the following command (by default, it will run on cuda).

python run_localGPT.py
You can also specify the device type just like ingest.py

python run_localGPT.py --device_type mps # to run on Apple silicon
This will load the ingested vector store and embedding model. You will be presented with a prompt:

> Enter a query:
After typing your question, hit enter. LocalGPT will take some time based on your hardware. You will get a response like this below. Screenshot 2023-09-14 at 3 33 19 PM

Once the answer is generated, you can then ask another question without re-running the script, just wait for the prompt again.

Note: When you run this for the first time, it will need internet connection to download the LLM (default: TheBloke/Llama-2-7b-Chat-GGUF). After that you can turn off your internet connection, and the script inference would still work. No data gets out of your local environment.

Type exit to finish the script.

Extra Options with run_localGPT.py
You can use the --show_sources flag with run_localGPT.py to show which chunks were retrieved by the embedding model. By default, it will show 4 different sources/chunks. You can change the number of sources/chunks

python run_localGPT.py --show_sources
Another option is to enable chat history. Note: This is disabled by default and can be enabled by using the --use_history flag. The context window is limited so keep in mind enabling history will use it and might overflow.

python run_localGPT.py --use_history
You can store user questions and model responses with flag --save_qa into a csv file /local_chat_history/qa_log.csv. Every interaction will be stored.

python run_localGPT.py --save_qa
Run the Graphical User Interface
Open constants.py in an editor of your choice and depending on choice add the LLM you want to use. By default, the following model will be used:

MODEL_ID = "TheBloke/Llama-2-7b-Chat-GGUF"
MODEL_BASENAME = "llama-2-7b-chat.Q4_K_M.gguf"
Open up a terminal and activate your python environment that contains the dependencies installed from requirements.txt.

Navigate to the /LOCALGPT directory.

Run the following command python run_localGPT_API.py. The API should being to run.

Wait until everything has loaded in. You should see something like INFO:werkzeug:Press CTRL+C to quit.

Open up a second terminal and activate the same python environment.

Navigate to the /LOCALGPT/localGPTUI directory.

Run the command python localGPTUI.py.

Open up a web browser and go the address http://localhost:5111/.

How to select different LLM models?
To change the models you will need to set both MODEL_ID and MODEL_BASENAME.

Open up constants.py in the editor of your choice.

Change the MODEL_ID and MODEL_BASENAME. If you are using a quantized model (GGML, GPTQ, GGUF), you will need to provide MODEL_BASENAME. For unquantized models, set MODEL_BASENAME to NONE

There are a number of example models from HuggingFace that have already been tested to be run with the original trained model (ending with HF or have a .bin in its "Files and versions"), and quantized models (ending with GPTQ or have a .no-act-order or .safetensors in its "Files and versions").

For models that end with HF or have a .bin inside its "Files and versions" on its HuggingFace page.

Make sure you have a MODEL_ID selected. For example -> MODEL_ID = "TheBloke/guanaco-7B-HF"
Go to the HuggingFace Repo
For models that contain GPTQ in its name and or have a .no-act-order or .safetensors extension inside its "Files and versions on its HuggingFace page.

Make sure you have a MODEL_ID selected. For example -> model_id = "TheBloke/wizardLM-7B-GPTQ"
Got to the corresponding HuggingFace Repo and select "Files and versions".
Pick one of the model names and set it as MODEL_BASENAME. For example -> MODEL_BASENAME = "wizardLM-7B-GPTQ-4bit.compat.no-act-order.safetensors"
Follow the same steps for GGUF and GGML models.

GPU and VRAM Requirements
Below is the VRAM requirement for different models depending on their size (Billions of parameters). The estimates in the table does not include VRAM used by the Embedding models - which use an additional 2GB-7GB of VRAM depending on the model.

Mode Size (B)	float32	float16	GPTQ 8bit	GPTQ 4bit
7B	28 GB	14 GB	7 GB - 9 GB	3.5 GB - 5 GB
13B	52 GB	26 GB	13 GB - 15 GB	6.5 GB - 8 GB
32B	130 GB	65 GB	32.5 GB - 35 GB	16.25 GB - 19 GB
65B	260.8 GB	130.4 GB	65.2 GB - 67 GB	32.6 GB - 35 GB
System Requirements
Python Version
To use this software, you must have Python 3.10 or later installed. Earlier versions of Python will not compile.

C++ Compiler
If you encounter an error while building a wheel during the pip install process, you may need to install a C++ compiler on your computer.

For Windows 10/11
To install a C++ compiler on Windows 10/11, follow these steps:

Install Visual Studio 2022.
Make sure the following components are selected:
Universal Windows Platform development
C++ CMake tools for Windows
Download the MinGW installer from the MinGW website.
Run the installer and select the "gcc" component.
NVIDIA Driver's Issues:
Follow this page to install NVIDIA Drivers.

Star History
Star History Chart

Disclaimer
This is a test project to validate the feasibility of a fully local solution for question answering using LLMs and Vector embeddings. It is not production ready, and it is not meant to be used in production. Vicuna-7B is based on the Llama model so that has the original Llama license.

Common Errors
Torch not compatible with CUDA enabled

Get CUDA version
nvcc --version
nvidia-smi
Try installing PyTorch depending on your CUDA version
   conda install -c pytorch torchvision cudatoolkit=10.1 pytorch
If it doesn't work, try reinstalling
   pip uninstall torch
   pip cache purge
   pip install torch -f https://download.pytorch.org/whl/torch_stable.html
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed

   pip install h5py
   pip install typing-extensions
   pip install wheel
Failed to import transformers

Try re-install
   conda uninstall tokenizers, transformers
   pip install transformers# localgpt
LocalGPT: Secure, Local Conversations with Your Documents
Step-by-Step Guide to Setup LocalGPT on Windows PC
19/11/2023
/

Blog, Generative AI
LocalGPT is a free tool that helps you talk privately with your documents. It keeps your information safe on your computer, so you can feel confident when working with your files.

Technically, LocalGPT offers an API that allows you to create applications using Retrieval-Augmented Generation (RAG). Seamlessly integrate LocalGPT into your applications and workflows to elevate the way you interact with documents. LocalGPT works on different systems right from the start, so you can talk to your data using CUDA for GPU, CPU, or MPS (Metal Performance Shaders). Choose the platform that fits your computer setup best.

In terms of document inference, LocalGPT has practical applications in:
1. Document Summarization:

   – LocalGPT can analyze and summarize lengthy documents, providing concise and coherent summaries. This is valuable for quickly extracting key information from reports, articles, or research papers.

2. Information Retrieval:

   – Users can engage in conversations with LocalGPT to retrieve specific information from documents. It can answer questions, provide insights, and guide users through the content of the documents.

3. Contextual Understanding:

   – LocalGPT excels in understanding the context within documents. It can maintain context over a series of queries, making it useful for users who want to explore or discuss topics within the document.

4. Language Translation Assistance:

   – LocalGPT can assist in translating content within documents, helping users understand or communicate information in different languages.

5. Data Exploration:

   – For datasets stored in document form, LocalGPT can be used to explore and analyze the data. Users can ask questions about the data, seek patterns, or request specific insights.

6. Document-Based Queries:

   – Users can pose queries related to the content of documents, and LocalGPT can provide relevant information or guide users to the sections of the document that address their queries.

7. Research Support:

   – Researchers can leverage LocalGPT for assistance in navigating through research papers, exploring related work, and obtaining contextually relevant information for their studies.

8. Document-Based Conversations:

   – Users can engage in natural language conversations with LocalGPT about the content of documents, enabling a dynamic interaction that goes beyond simple keyword searches.

9. Document Tagging and Categorization:

   – LocalGPT can assist in tagging or categorizing documents based on their content, making it easier for users to organize and manage large sets of documents.

10. Content Extraction:

    – LocalGPT can be used to extract specific details or data points from documents, streamlining the process of information extraction for various purposes.

These applications showcase how LocalGPT can enhance document inference tasks by providing natural language interaction and understanding within the context of the documents.

Setting Up Environment for LocalGPT 
1. Clone the Repository
git clone https://github.com/PromtEngineer/localGPT.git
2. Install Conda and Create a Virtual Environment
conda create -n localgpt_llama2_gpu python=3.10.0
conda activate localgpt_llama2_gpu
3. Install Dependencies
pip install -r requirements.txt
Make sure to run this command from inside the localGPT directory.

GPU Configuration for Windows PC
To install llama-cpp-python with cuBLAS support, execute the following commands:

set CMAKE_ARGS=-DLLAMA_CUBLAS=on FORCE_CMAKE=1
pip install llama-cpp-python==0.1.83 --no-cache-dir
Configure CUDA:
If torch is not using CUDA, follow these steps:

Validate CUDA usage:
python -c "import torch; print(torch.cuda.is_available())"
Uninstall torch packages:
pip uninstall torch torchvision torchaudio
Reinstall torch with CUDA support:
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
Validate CUDA usage again:
python -c "import torch; print(torch.cuda.is_available())"
Running LocalGPT:
Use Case 1 – Command Line Output:
Ingest data:
Run Command: python ingest.py --device_type cuda
Run LocalGPT Inference:
Run Command: python run_localGPT.py --device_type cuda
Use Case 2 – Web UI Output:
Ingest all files (if not done earlier):
Run Command: python ingest.py
Run LocalGPT API:
Run Command: python run_localGPT_API.py
Navigate to the LocalGPTUI directory:
Run Command: cd localGPTUI
Run the Flask web app:
Run Command: python localGPTUI.py
Open a web browser and go to the address http://localhost:5111/
