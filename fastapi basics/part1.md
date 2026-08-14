

<details><summary >roadmap </summary>
  

<img width="1212" height="544" alt="image" src="https://github.com/user-attachments/assets/5f70e6be-3559-41df-b246-63c2607660fe" />



</details>



<details><summary>What is FastAPI & Why It's Perfect for AI Backends</summary>
<img width="1280" height="720" alt="What is FastAPI   Why It&#39;s Perfect for AI Backends 2-30 screenshot" src="https://github.com/user-attachments/assets/16fa83fe-e91f-4647-9fb7-caa57df688ef" />


<img width="1649" height="797" alt="image" src="https://github.com/user-attachments/assets/ff8f038d-bcd1-4dad-9329-c551dfb41e84" />

<img width="1280" height="720" alt="What is FastAPI   Why It&#39;s Perfect for AI Backends 2-19 screenshot" src="https://github.com/user-attachments/assets/3dfef2fc-27eb-4892-94b6-52cd7f62e587" />
<img width="1280" height="720" alt="What is FastAPI   Why It&#39;s Perfect for AI Backends 4-16 screenshot" src="https://github.com/user-attachments/assets/57fa9432-7ce4-4e91-bbdd-8b76412c1c99" />

</details>



<details><summary>Python Type Hints</summary>


<img width="1280" height="720" alt="Python Type Hints Explained for FastAPI Developers 1-18 screenshot" src="https://github.com/user-attachments/assets/a75c6d40-0bfc-4976-b410-c33e8803a12a" />
<img width="1280" height="720" alt="Python Type Hints Explained for FastAPI Developers 3-12 screenshot" src="https://github.com/user-attachments/assets/5245d4f7-4b39-4a00-a9e0-ea387a2c3892" />

<img width="1280" height="720" alt="Python Type Hints Explained for FastAPI Developers 1-46 screenshot" src="https://github.com/user-attachments/assets/5b2e540f-b359-431d-84c6-ec0e0ca5b3f6" />
<img width="1280" height="720" alt="Python Type Hints Explained for FastAPI Developers 2-11 screenshot" src="https://github.com/user-attachments/assets/2df6a9e3-45e0-47e2-987e-0d6f98b5b2c3" />
<img width="1280" height="720" alt="Python Type Hints Explained for FastAPI Developers 3-6 screenshot" src="https://github.com/user-attachments/assets/cc896193-15cb-4030-8499-256d3c36668c" />
<img width="1280" height="720" alt="Python Type Hints Explained for FastAPI Developers 3-54 screenshot" src="https://github.com/user-attachments/assets/56e30747-add4-4117-9015-213f07002873" />


</details>



<details><summary>
Async Python Explained for AI Backends — I/O vs CPU Bound</summary>

**Core Concept: I/O vs. CPU Bound**
* **I/O-Bound Work (Waiting):** Your code sends a request to an external service (like an *OpenAI* API, a vector database, or a regular database) and waits for a response (1:18, 1:34-1:46). **Async** is highly effective here because it allows the server to handle other requests while waiting, instead of sitting idle (1:51-1:58).
* **CPU-Bound Work (Computing):** Your processor is busy with active calculations (1:21). **Async does not help here** because the CPU is already fully occupied; in these cases, separate worker processes are needed to handle parallel computation (2:10-2:23).

**The Simple Rule for FastAPI**
* Ask yourself: **Is my code waiting for an external response or actively computing?** (2:53-2:56)
    * **Waiting:** Use `async def` (2:58).
    * **Computing:** Use a regular function (`def`) or a separate worker (3:00-3:02).

**Warning:** Never perform heavy CPU-intensive tasks inside an `async` function, as it will block your server and decrease performance (3:04-3:10).


<img width="1280" height="720" alt="Async Python Explained for AI Backends — I_O vs CPU Bound 0-27 screenshot" src="https://github.com/user-attachments/assets/ad79c709-dc6d-4631-b869-62a3284222eb" />
<img width="1280" height="720" alt="Async Python Explained for AI Backends — I_O vs CPU Bound 1-7 screenshot" src="https://github.com/user-attachments/assets/40a36700-3855-4036-9786-eef83111ec9e" />
<img width="1280" height="720" alt="Async Python Explained for AI Backends — I_O vs CPU Bound 1-11 screenshot" src="https://github.com/user-attachments/assets/82e367b3-7944-48fc-bf77-d685c5e829f2" />
<img width="1280" height="720" alt="Async Python Explained for AI Backends — I_O vs CPU Bound 1-34 screenshot" src="https://github.com/user-attachments/assets/368dd4d2-1256-4cbe-ab72-eeb050b986b4" />
<img width="1280" height="720" alt="Async Python Explained for AI Backends — I_O vs CPU Bound 2-2 screenshot" src="https://github.com/user-attachments/assets/7ded0863-9363-484a-96cf-238b40a3c595" />
<img width="1280" height="720" alt="Async Python Explained for AI Backends — I_O vs CPU Bound 2-30 screenshot" src="https://github.com/user-attachments/assets/0e72e34d-bee3-4b4e-8e67-01b631f3abb1" />
<img width="1280" height="720" alt="Async Python Explained for AI Backends — I_O vs CPU Bound 2-53 screenshot" src="https://github.com/user-attachments/assets/bd780dc4-9717-4617-b810-5150802f1899" />

</details>





<details><summary>FastAPI Documentation</summary> 


<img width="1280" height="720" alt="FastAPI Documentation_ Done in Seconds_ 0-44 screenshot" src="https://github.com/user-attachments/assets/4651d2a5-f3bb-447f-a1b2-a7749be4d66a" />
<img width="1280" height="720" alt="FastAPI Documentation_ Done in Seconds_ 0-50 screenshot" src="https://github.com/user-attachments/assets/c2d12812-2d28-4271-9288-f240e64c28bc" />
<img width="1280" height="720" alt="FastAPI Documentation_ Done in Seconds_ 1-23 screenshot" src="https://github.com/user-attachments/assets/6e70dbbf-d174-4248-8a62-3b269b56e8c3" />
<img width="1280" height="720" alt="FastAPI Documentation_ Done in Seconds_ 1-43 screenshot" src="https://github.com/user-attachments/assets/45e45fc0-3b82-4c7d-8776-50e07f051b52" />
<img width="1280" height="720" alt="FastAPI Documentation_ Done in Seconds_ 2-20 screenshot" src="https://github.com/user-attachments/assets/dc7ea70d-282f-4105-bfc3-80c5f96dc438" />



</details>






<details><summary> REST API Design for AI: Routes, Resources & Reality</summary>
This video introduces **REST (Representational State Transfer)** as a core design principle for structuring APIs, specifically within the context of an *AI backend* (0:45).

### **Core Concepts of REST**
* **Resources:** The foundation of your API. Every piece of data you manage (e.g., users, documents, chat sessions) should be treated as a resource (0:58-1:09).
* **Routes:** These should be **plural nouns**, not verbs. They represent the URL path to a resource (e.g., `/documents`) (1:20-1:47).
* **HTTP Verbs:** The action to perform on a resource is defined by the method, not the URL. 
    * **GET:** Fetch/Read data (2:03)
    * **POST:** Create new resources (2:08)
    * **PUT:** Replace an existing resource (2:10)
    * **PATCH:** Update part of a resource (2:13)
    * **DELETE:** Remove a resource (2:15)

### **When to Use REST**
REST is highly effective for managing stateful resources like *document ingestion*, *user management*, and *session handling* (2:37-2:43). 

### **Limitations**
REST can feel awkward for certain advanced AI workflows, such as:
* **Streaming LLM responses** (2:48)
* **Long-running background jobs** (2:50)
* **Multi-step agentic workflows** (2:52)

These scenarios do not always fit the standard request-response model, and other patterns may be better suited for them in future modules of this course.




<img width="1280" height="720" alt="REST API Design for AI_ Routes, Resources   Reality 0-27 screenshot" src="https://github.com/user-attachments/assets/aa612eb8-ba3b-4285-89d7-9c9bb33b3135" />
<img width="1280" height="720" alt="REST API Design for AI_ Routes, Resources   Reality 0-56 screenshot" src="https://github.com/user-attachments/assets/9ff77534-d07e-47e1-86c4-db86bf74f5c1" />
<img width="1280" height="720" alt="REST API Design for AI_ Routes, Resources   Reality 1-20 screenshot" src="https://github.com/user-attachments/assets/6522f6c7-a999-470b-ab3d-7a4827955033" />
<img width="1280" height="720" alt="REST API Design for AI_ Routes, Resources   Reality 2-4 screenshot" src="https://github.com/user-attachments/assets/cd7ab96e-c977-4505-b4d5-4a9cacc91ec9" />
<img width="1280" height="720" alt="REST API Design for AI_ Routes, Resources   Reality 2-34 screenshot" src="https://github.com/user-attachments/assets/9c922f90-6150-40b3-8a0a-5ee69a4d5dff" />
</details>
