# webGPT: 本地大模型聊天前端界面

这是一个纯前端的聊天界面，设计用于连接和展示本地部署的大语言模型（LLM）的输出。它的核心特色是能够将模型的“思考过程”和“最终答案”分开展示，为用户提供更深入的洞察力。

## ✨ 功能特性

-   **清爽的聊天界面**: 模仿主流聊天应用的现代化、响应式设计。
-   **流式输出**: 实时显示模型生成的文本，提供流畅的交互体验。
-   **思考过程可视化**: 独有的“思考过程”区域，可以展示模型在生成最终答案之前的推理、计划或检索步骤。
-   **可折叠的思考过程**: 用户可以自由选择显示或隐藏AI的思考过程，保持界面整洁。
-   **易于配置**: 仅需修改 `index.html` 文件中的几个变量，即可连接到您自己的本地LLM服务。
-   **纯粹的 HTML/CSS/JS**: 无需复杂的构建过程或依赖库，直接在浏览器中打开即可使用。

## 🚀 工作原理

此前端依赖于一个特殊的后端输出格式。您需要让您的本地LLM在生成回复时，遵循以下模式：

`[您的思考过程文本...]<think>[您的最终答案文本...]`

前端会通过 `fetch` API 以流式方式接收文本。它会：
1.  将 `<think>` 标签之前的所有内容都实时渲染到“思考过程”区域。
2.  当接收到 `<think>` 标签后，它会停止向“思考过程”区域输出，并将之后的所有内容渲染到主答案区域。

## 🛠️ 如何配置和使用

这是一个纯前端项目，您需要先拥有一个正在运行的、兼容OpenAI API格式的本地大模型服务。

1. **下载**

2.  **修改API配置**
    用任何文本编辑器打开 `index.html` 文件，找到头部的 `<script>` 标签内的 `--- API 配置区域 ---`。

    ```javascript
    // --- API 配置区域 ---
    const OPENAI_API_KEY = "sk-xxxxxxxx"; // 你的API Key (如果需要)
    const BASE_URL = "[http://127.0.0.1:8000/v1](http://127.0.0.1:8000/v1)"; // 部署服务的IP地址与端口
    const API_PATH = "/chat/completions"; // chat completions endpoint

    const API_ENDPOINT = `${BASE_URL}${API_PATH}`;
    const MODEL_NAME = "your-model-name"; // 你在后端服务中指定的模型名称
    const SEPARATOR_TOKEN = "</think>"; // 用于分隔思考和答案的特殊标记

    // (重要) 头像图片的 URL
    // 请将下面的本地路径 C:/... 替换为网络URL或项目内的相对路径
    const USER_AVATAR_URL = 'path/to/your/user_avatar.png';
    const AI_AVATAR_URL = 'path/to/your/ai_avatar.png';
    // --- API 配置区域结束 ---
    ```

3.  **配置说明**:
    -   `OPENAI_API_KEY`: 填入您的LLM服务需要的API密钥。如果您的服务不需要密钥，可以留空或随意填写。
    -   `BASE_URL`: 替换为您本地LLM服务的 `IP地址:端口/v1`。例如 `http://localhost:8080/v1`。
    -   `MODEL_NAME`: 替换为您服务中加载的模型名称。
    -   `USER_AVATAR_URL` / `AI_AVATAR_URL`: **非常重要！** 默认的 `C:/Users/...` 路径在网页上无法工作。您需要：
        -   **方法A (推荐)**: 将您的头像图片（例如 `user.png`, `ai.png`）放在与 `index.html` 相同的文件夹下，然后将路径修改为相对路径：`'user.png'` 和 `'ai.png'`。
        -   **方法B**: 将图片上传到图床，然后使用完整的网络URL。

4.  **运行**
    直接用您的网页浏览器 (如 Chrome, Firefox, Edge) 打开 `index.html` 文件即可开始使用。

## 📝 后端模型要求

为了使“思考过程”功能正常工作，您的后端LLM服务必须进行相应调整，使其输出的流式文本包含您在前端定义的 `SEPARATOR_TOKEN`（默认为 `</think>`）。

一个简单的Python后端伪代码示例可能如下：

```python
# (这是一个后端伪代码示例, 不是本项目的一部分)
async def generate_response(prompt):
    thinking_steps = "第一步：分析用户问题。\n第二步：制定回答计划。"
    final_answer = "这是最终的回答。"

    # 首先，流式输出思考过程
    yield thinking_steps

    # 然后，输出分隔符
    yield "</think>"

    # 最后，流式输出最终答案
    yield final_answer
