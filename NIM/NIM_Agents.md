# How is NVIDIA building it's agentic AI applications?  

NVIDIA uses langchain and llamaindex for tools to build agents. It is writing all the prompt templates to build agents in classes with async-await functions.

The blueprints where agents were implemented are Structured Report Generation, Document Research Assistant for Blog Creation, Traceability for Agentic AI, Code Documentation for Software Development, Build an AI Virtual Assistant, Vulnerability Analysis for Container Security, Build a Digital Human.  

Let us got through the Document Research Assistant for Blog Creation and observe how it is different from langchain's or llamaindex's agents :  

## Document Research Assistant for Blog Creation

In this [notebook](https://github.com/run-llama/llama_index/blob/main/docs/docs/examples/agent/nvidia_document_research_assistant_for_blog_creation.ipynb) we can observe that it is using llamaindex tools for agents and also llamaparse for chunk parsing into documents.  

In the *DocumentResearchAgent* class, we can see that there are events and a workflow. Events are considered to be data objects which hold information required for each step. Workflow is considered to be the method in which the agents has to run. Let us understand the workflow first :  

<!-- Workflows
Workflows are the orchestrated sequences of steps that define the overall process. In the code, the workflow is implemented by subclassing Workflow to create the DocumentResearchAgent. This workflow encapsulates the entire process from initial query to final blog post and is organized into distinct steps: -->

### formulate_plan (StartEvent → OutlineEvent):

**Role**: Receives the initial query (from a StartEvent), stores some context (like the original query and available tools), and uses an LLM to generate an outline for the blog post.  
**Output**: An OutlineEvent that contains the generated outline.

### formulate_questions (OutlineEvent → QuestionEvent):

**Role**: Takes the outline from the previous step, generates a list of simple, targeted questions to gather further factual data, and sends out these questions as individual QuestionEvent instances.  
**Output**: Emits multiple QuestionEvent objects (one for each question).

### answer_question (QuestionEvent → AnswerEvent):

**Role**: For each received question, this step uses a FunctionCallingAgent (which is initialized with external tools) to obtain an answer.  
**Output**: Produces an AnswerEvent containing both the original question and the corresponding answer.

### write_report (AnswerEvent → ReviewEvent):

**Role**: Once answers for all the questions are collected, this step compiles the information (along with the original outline) into a coherent blog post by prompting an LLM.  
**Output**: Results in a ReviewEvent that holds the complete blog post draft.

### review_report (ReviewEvent → StopEvent or QuestionEvent):

**Role**: Acts as a review phase. It assesses whether the blog post sufficiently answers the original query. If not, it generates a new set of questions (thus returning QuestionEvent objects for further refinement). If the post is satisfactory or if a review limit is reached, it emits a StopEvent with the final report.  
**Output**: Either issues more questions for further research or finalizes the process with a StopEvent.  

This is how a template for a multi-agent is built and it is the similar template for *FunctionCallingAgent* from llamaindex's [documentation](https://docs.llamaindex.ai/en/stable/examples/workflow/function_calling_agent/), *ToolCallingAgent* in langchain's [documentation](https://python.langchain.com/docs/tutorials/agents/). Instead of having multiple tools for it, we can simply use these with proper prompt templates. It is also the same procedure followed by langchain and llamaindex as well.