# Overview

This repository contains the code for generating the RAG Reasoning Dataset and fine-tuning and testing models on this instruction dataset. As the name implies, the RAG Reasoning Dataset contains tasks whose goal is to explain reasoning/rationale behind generating an answer using a retrieved document. 

More specifically, it contains three tasks. One task is determining whether a paragraph is relevant to a question and explaining why it is or not. Another one is about deciding how much the document supports the answer and justifying that answer. The other task is to tell whether the response covers the complete answer and explain the reason why it thinks that.

## Usage

The package requirements are in the first cells of each notebook. If you wish to use a different model, specify its name in the Load Model Section.

Download dataset [here](https://drive.google.com/file/d/1Q8O1IUOobKJPvFoHIhdS27Tq8uSNtMUl/view?usp=sharing).

## Dataset Creation Summary

The RAG Reasoning Dataset was created from the self-rag dataset which is an instruction dataset for predicting reflection tokens along with the answer. These reflection tokens predict whether a retrieved document is relevant, whether the answer is supported by the document, and whether the answer is useful in answering the question. The first step in generating this dataset was to extract all reflection tokens along with their respective pair of question, document, or answer. Then, the usefulness tokens were replaced with completeness tokens depicting how much the response answered the question, and these complete tokens are the following: Complete, Partially Complete, Incomplete. Before, the usefulness tokens were numbers from 1-5 which weren’t descriptive and hard to predict because their differences were not clear. On the other hand, the completeness tokens are not only words that hold more meaning than numbers but also their difference is obvious. The last step was to generate the reason why each reflection token was the correct answer by using Llama 3.

## Results

| Models | BLEU | Rouge-L | BERTScore |
| ------------- | ------------- | ------------- | ------------- |
| Base | 0.097 | 0.207 | 0.725 |
| Fine-tuned RAG Reasoning | 0.200 | 0.364 | 0.899 |
| Fine-tuned RAG Reasoning + Alapaca | 0.212 | 0.369 | 0.901 |

As expected, the results of this experiment show that the base model had much lower performance than the fine-tuned versions of the model in all metrics. Among the two fine-tuned models, the one that was also trained on the RAG Reasoning dataset along with the Alpaca dataset had slightly better results than the one fine-tuned without Alpaca. 

This difference was mostly because the fluency of the RAG Reasoning + Alpaca model was typically better, especially in questions not related to the ones in the RAG Reasoning dataset. Though, there were also times when the RAG Reasoning + Alpaca model answered correctly, whereas the RAG Reasoning model generated an incorrect answer. As for the base model, it sometimes responded correctly. Having said that, it never generated the response in the expected format, and it often didn’t mention the expected words. Other times, the base model didn’t answer the question directly and proceeded to talk about something similar. Sometimes, it didn’t generate any text at all.

The main takeaways of this study are the following:
* If you are doing a task that requires a specific set of answers or a certain format, fine-tuning a model might be a good idea because it will increase the chances of the model generating that type of answer or format.
* Increasing the variety of tasks, instructions, and outputs in your instruction dataset can improve the performance of your model. For instance, in this study, adding the Alpaca dataset to the training data of the model enhanced the performance of the model not only on other types of task but also on the tasks in the RAG Reasoning dataset.
