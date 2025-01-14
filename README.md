

# 介绍

一个 Python script，使用 OpenAI API 和群晖 Synology Chat 套件搭建了一个基于 ChatGPT-3.5 的聊天机器人。


使用说明
----

1.  在 Synology Chat 中请按照以下步骤添加聊天机器人：

    - 用有管理员权限的账户登录 Synology Chat。

    - 点击右上角你的头像，在菜单中选择「整合」(Integration)，点击「机器人」(Bots)。

    - 点击「+ 创建」按钮，然后选择「创建新机器人」。

    - 为您的机器人输入名称（例如：ChatGPT机器人）。点击「创建」。

    - 在创建的机器人详情页面，找到「传出 Webhook」部分，将 Webhook URL 设置为您在代码中设置的 URL（即 `http://your_server_ip:5005/webhook`， 其中 `your_server_ip` 应该是运行上述代码的机器的 IP 地址）。

    - 在机器人详情页面的「传入 Webhook」部分，将生成一个 Webhook URL 和一个 Token。请将这两个值复制并替换`gptchatbot.py`中相关变量：

    ```python
    INCOMING_WEBHOOK_URL = "your_incoming_webhook_url_here"
    OUTGOING_WEBHOOK_TOKEN = "your_outgoing_webhook_token_here"

    ```
    - 最后点击「确认」（OK）保存。

2. 在`https://platform.openai.com/account/api-keys`申请 OpenAI API 密钥，用你的 OpenAI API 密钥替换`gptchatbot.py`中的`openai.api_key`：
    

    ```python
    openai.api_key = "your_api_key_here"
    ```
    
3.  安装所需的库：

    在bash shell中运行：

    ```bash
    pip install openai requests flask
    ```
    或
    ```bash
    pip install -r requirements.txt 
    ```

4.  运行 Python 文件：

    在bash shell中运行：

    ```bash
    python gptchatbot.py
    ```

5. 在 Synology Chat 中与机器人进行对话。根据您的输入，机器人将使用OpenAI的 gpt-3.5-turbo 模型生成回复。



代码文件说明
------

1.  导入所需的库：
    
    *   `json`: 处理 JSON 数据格式
    *   `requests`: 发送 HTTP 请求
    *   `openai`: OpenAI 官方库，用于与 OpenAI API 交互
    *   `flask`: 构建 Web 应用程序


2.  设置 Synology Chat 机器人详细信息：用您的 Synology Chat 机器人的详细信息替换下面的占位符。

    ```python
    INCOMING_WEBHOOK_URL = "your_incoming_webhook_url_here"
    OUTGOING_WEBHOOK_TOKEN = "your_outgoing_webhook_token_here"
    ```

3.  创建一个用于存储内存中对话历史的字典 `conversation_history`。
    
4.  定义 `process_synology_chat_message(event)` 函数，处理从 Synology Chat 收到的消息：
    
    *   验证令牌是否有效。
    *   获取用户 ID 和消息文本。
    *   调用 `generate_gpt_response(user_id, text)` 函数生成 ChatGPT 响应。
    *   将响应发送回 Synology Chat。
5.  定义 `generate_gpt_response(user_id, message, max_conversation_length=10, refresh_keywords=None)` 函数，生成 ChatGPT 响应：
    
    *   根据给定的关键字刷新对话（默认为：`["new", "refresh", "00", "restart", "刷新", "新话题"]`）。
    *   维护与每个用户的对话历史记录。
    *   生成一个包含系统提示和聊天消息的 `messages` 列表。
    *   调用 OpenAI API，向其发送 `messages`，并接收生成的响应。
    *   如果响应完成，将响应添加到对话历史中并返回响应文本。
6.  定义 `handle_request(event)` 函数，处理传入的事件：
    
    *   检查事件是否为空。
    *   调用 `process_synology_chat_message(event)` 函数处理事件。
7.  使用 Flask 创建一个 Web 应用程序，并定义一个路由 `/webhook`，处理从 Synology Chat 发送的 POST 请求。
    
8.  在主函数中运行 Flask 应用程序。
    

注意事项
----

*   请确保您的 OpenAI API 密钥和 Synology Chat 机器人详细信息已正确填写。
    
*   确保您的服务器可以访问互联网，以便与 OpenAI API 进行通信。
    
*   本示例代码为了简化展示而直接在全局范围内设置了密钥和 URL。在实际生产环境中，您可能需要使用环境变量或配置文件来存储这些敏感信息。
    
*   请注意，向 OpenAI API 发送请求可能会产生费用。根据您的 API 使用情况，费用可能有所不同。请参阅 OpenAI 的[定价页面](https://openai.com/pricing)了解详细信息。
    
*   在生产环境中部署聊天机器人时，请确保遵循最佳安全实践，例如使用 HTTPS、验证令牌等。
    
*   本代码的对话历史存储在内存中。在实际生产环境中，您可能需要使用持久存储（例如数据库）来存储对话历史。
    
*   本示例代码中未实现对输入和输出的过滤和检查。在实际应用中，请确保对输入进行验证和过滤，以防止潜在的安全问题。
    

