# 美国签证状态自动查询工具 (USA Visa Auto Status Check)

## 简介

本项目是一个 Python 脚本，旨在自动查询美国非移民签证在 CEAC 网站上的申请状态，并通过 Resend 服务发送邮件通知状态变化。脚本通过 GitHub Actions 定时运行。

**请注意：** 使用自动化工具查询 CEAC 网站可能违反其服务条款。请自行承担使用风险。本项目仅供学习和便利目的。

## 功能

*   通过 GitHub Actions 定时（默认每 10 分钟）自动运行查询脚本 ([check_visa.py](check_visa.py))。
*   访问 CEAC 网站查询指定的非移民签证申请状态。
*   使用 ddddocr 库识别验证码。
*   比较当前状态与上次记录的状态（存储在 `visa_state_*.json` 文件中，通过 GitHub Actions 缓存）。
*   当检测到签证状态或最后更新日期发生变化时，通过 Resend 服务发送邮件通知。
*   查询所需的个人信息和 API 密钥通过 GitHub Secrets 安全传递。

## 环境要求

*   Python 3.x
*   所需的 Python 库: `playwright`, `resend`, `ddddocr`, `Pillow` (作为 ddddocr 的依赖)

## 安装 (本地运行，可选)

1.  **克隆仓库:**
    ```bash
    git clone <repository-url>
    cd <repository-directory>
    ```

2.  **安装依赖:**
    ```bash
    python -m pip install --upgrade pip
    pip install playwright resend ddddocr Pillow
    playwright install chromium
    ```
3.  **设置环境变量 (本地运行):**
    在本地运行前，需要设置以下环境变量：
    *   `VISA_CASE_NUMBER`: 你的签证 Case Number。
    *   `PASSPORT_NUMBER`: 你的护照号码。
    *   `SURNAME`: 你的姓氏。
    *   `RESEND_API_KEY`: 你的 Resend API Key。
    *   `SENDER_ADDRESS`: 你在 Resend 验证过的发件邮箱地址。
    *   `RECIPIENT_EMAIL`: 接收通知的邮箱地址。

## GitHub Actions 配置 (Fork 后必需)

如果你 Fork 了这个仓库并希望使用 GitHub Actions 自动运行，你需要配置以下 GitHub Secrets：

1.  **导航到你的 Fork 仓库页面。**
2.  点击 **Settings** (设置)。
3.  在左侧菜单中，找到 **Secrets and variables**，然后点击 **Actions**。
4.  点击 **New repository secret** 按钮，添加以下 Secrets：
    *   `VISA_CASE_NUMBER`: 你的签证 Case Number (例如 `2023111010BEJ00XXX`)。
    *   `PASSPORT_NUMBER`: 你的护照号码。
    *   `SURNAME`: 你的姓氏 (拼音或英文，与申请时一致)。
    *   `RESEND_API_KEY`: 从 [Resend](https://resend.com/) 获取的 API Key，用于发送邮件通知。你需要注册 Resend 并验证你的发件域名或邮箱。
    *   `SENDER_ADDRESS`: 你在 Resend 中验证过的发件邮箱地址 (例如 `visa-bot@yourdomain.com`)。
    *   `RECIPIENT_EMAIL`: 接收签证状态更新通知的邮箱地址。

5.  **启用 Actions:** 确保你的仓库的 Actions 是启用的。你可能需要在 Actions 标签页手动触发一次 `workflow_dispatch` 事件来启动第一次运行，或者等待下一个计划时间 (`schedule`)。

## 使用方法 (通过 GitHub Actions)

1.  完成上述 **GitHub Actions 配置**。
2.  GitHub Actions 将根据 [.github/workflows/visa.yml](.github/workflows/visa.yml) 文件中的 `schedule` (默认为每 10 分钟) 或 `workflow_dispatch` (手动触发) 自动运行 [check_visa.py](check_visa.py) 脚本。
3.  如果签证状态或最后更新日期发生变化，配置的 `RECIPIENT_EMAIL` 将会收到来自 `SENDER_ADDRESS` 的邮件通知。
4.  你可以在仓库的 Actions 标签页查看运行日志。

## 免责声明

*   本工具仅为个人学习和便利目的创建，不保证查询结果的绝对准确性和实时性。请始终以 CEAC 官方网站的查询结果为准。
*   自动化查询可能违反 CEAC 网站的使用条款。用户需自行承担因使用此脚本而可能产生的所有风险和后果。开发者不对任何潜在的滥用或违规行为负责。
*   请合理设置查询频率（通过修改 [.github/workflows/visa.yml](.github/workflows/visa.yml) 中的 `cron` 表达式），避免对 CEAC 服务器造成过大负担。过于频繁的查询可能导致 IP 被暂时或永久封禁。
*   依赖的 OCR 库 (ddddocr) 对验证码的识别率并非 100%，可能导致查询失败或需要重试。

## 贡献

欢迎提交 Pull Requests 或提出 Issues。