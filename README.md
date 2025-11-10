# RAG-IGBench: Innovative Evaluation for RAG-based Interleaved Generation in Open-domain Question Answering

📖[Paper]() | 🤗[Huggingface](https://huggingface.co/datasets/Muyi13/RAG-IGBench)

🎉 RAG-IGBench has been accepted as a poster at NeurIPS 2025 Datasets and Benchmarks Track!

In real-world scenarios, providing user queries with visually enhanced responses can considerably benefit understanding and memory, underscoring the great value of interleaved image-text generation. 

Therefore, we present **Interleaved Generation** based on **Retrieval-Augmented Generation** (RAG-IG) and the corresponding **RAG-IGBench**, a thorough benchmark designed specifically to evaluate the task. By integrating MLLMs with the RAG paradigm, we achieve high-quality and semantically coherent image-text interleaved generation.

<div align=center><img src="assets/case_example.jpg"></div>

<div align=center><img src="assets/overview.jpg"></div>

The two figures above illustrate the RAG-IG pipeline and a representative portion of our experimental results. Specifically, we input the query along with retrieved documents and images into the MLLMs. Through detailed instructions, the MLLMs generate answers in markdown format, incorporating appropriate image indices. Subsequently, we replace these image indices with the corresponding images to produce the final output: a coherent answer where text and images are seamlessly interleaved.

To comprehensively evaluate diverse MLLMs on RAG-IG, we use metrics of three dimensions: text quality, image quality, and image-text consistency. We use ROUGE-1 for text quality evaluation, modified Edit Distance and Kendall Score for image quality evaluation, and CLIP Score and Alignment Score for image-text consistency evaluation. The details of our innovative metrics can be found in our paper.

The following two figures show the statistics of our RAG-IGBench:

<div align=center><img src="assets/stat_table.jpg"></div>

<div align=center><img src="assets/stat_figure.jpg"></div>

## TODO
- [x] Release the English version of the data.
- [x] Release the data and eval code of our Benchmark.

## 📚 Setup
We have hosted the RAG-IGBench dataset on Huggingface and provide two versions of the data: RAG_IG_CH and RAG_IG_EN. The Chinese version is sourced from the original social media platform, and AI translates the English version. The format of each line in `RAG_IG_CH.jsonl `and `RAG_IG_EN.json` is:
```
[
    {
        "id": "a number",
        "query": "user question",
        "documents": [
            "content of doc#1",
            "content of doc#2",
            ...
        ],
        "images":[
            [img_url, img_url, ...],     # images of doc#1
            [img_url, img_url, ...],      # images of doc#2
            ...
        ],
        "gt_raw_answer": "json str",      # model-generated original answer
        "gt_clean_answer": "answer str",      # cleaned markdown-format answer
        "category":[
            "Finance",      # topic of the query
            "what-is"       # one of "what-is", "how-to", "yes-or-no", and "head-to-head"
        ],
        "split": "train/dev"      # data split for training and evaluation
    }
]
```
Run the script `download_images.py` to check all the images. If you just want to evaluate MLLM, the download is not necessary. The images folder structure is:
```
INPUT_DIR
    |INPUT_FILE(data.json)
    |images
        |1                              # images folder for query 1
            |1                              # images folder for doc#1
                |IMG#1.jpg
                |IMG#2.jpg
                |...
            |2                              # images folder for doc#2
                |IMG#x.jpg
                |...
            |3                              # images folder for doc#3
        |2                              # images folder for query 2
            |...
        |3                              # images folder for query 3
            |...
        |..
```
## 🛠️ Usage
### Answer generation
We have to modify the inference code for various models to support interleaved multi-image inputs. We have prepared some testing scripts for currently available open-source and closed-source models in the `./model_generation` folder. The models we support now include **GPT4o**, **Claude**, **Gemini**, **LLaVa-OV**, **InternVL2.5**, **Qwen2/2.5VL**, **NVLM_D** and **Deepseekvl2**. For the evaluation of these models, you need to modify the model's path and the path to RAG-IGbench's dataset to generate answers. The test result will be saved in a JSONL file. For other models, you need to create your own script to generate answers. The generated answer should be stored with the key `'model_answer'` in the result JSONL file for evaluation.
### Evaluation
Use the `./score/eval.py` script to score the model's responses. What you need to provide is:
```
output_dir = ''              # Save the evaluation result
input_file = ''               # Model answer file
clip_model_path = ''          # The model used to calculate Clip-Score, default as CLIP-ViT-Large-Patch14 from OpenAi
embedding_model_path = ''     # The model used to calculate Alignment-Score, default as Conan-embedding
```
Finally, the evaluation result will be saved in a JSONL file and the final scores will be printed in stdout, or you can run `./score/scores.py` with the evaluation result file as input to get the final scores. 

## ✒️Citation

## 📄 License
![](https://img.shields.io/badge/Code%20License-Apache%202.0-green) ![](https://img.shields.io/badge/Data%20License-CC%20By%20NC%204.0-red)

Usage and License Notices: The data and code are intended and licensed for research use only. License: Attribution-NonCommercial 4.0 International It should abide by the policy of OpenAI: https://openai.com/policies/terms-of-use
