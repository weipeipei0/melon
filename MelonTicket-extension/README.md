# Melon Ticket Extension

这是一个从零开始搭建的 Chrome MV3 扩展骨架，目标是逐步承载一套浏览器自动化流程，而不是把所有逻辑继续堆进单个 `content.js`。

## 当前边界

- `background/`
  - 集中管理共享设置和每个 tab 的运行状态。
- `shared/`
  - 共享常量、日志工具、消息封装。
- `content/detection/`
  - 只做页面识别，不做业务动作。
- `content/dom/`
  - 只放通用 DOM 查询和安全动作封装。
- `content/messaging/`
  - 只负责和 background 通信。
- `content/stages/`
  - 每个自动化阶段独立实现，统一由 stage runner 调度。
  - 商品页已拆成独立的 `product/` 阶段目录，用于顺序步骤执行。
- `popup/`
  - 用于启停和查看当前 tab 状态。

## 当前能力

- 激活码首绑单机授权
  - 未激活时 popup 只显示激活区
  - 激活成功后才显示全部功能配置
  - 插件更新后沿用同一设备标识继续使用
- 启用/禁用自动化
- 配置起始演唱会页面 URL
- 配置商品页日期 / 时间
  - 前端采用和旧 `Melon` 类似的月份 / 日期 / 时间下拉选择，并提供“不指定”选项。
- 配置轮询间隔
- popup 底部提供独立的启动 / 停止按钮
- 基于 URL 的基础页面识别
- 按页面类型进入对应 stage 占位模块
- 将当前 tab 状态上报给 background，并在 popup 中查看
- onestop 弹框验证码自动识别、输入、提交
  - 默认走云端 OCR：`https://api.nexuschat.top/melon-ocr/ocr-api/ocr/file`
  - 请求前自动校验 / 刷新短期 access token

## 云端服务

- 独立服务目录：`/Users/xuqihan/Desktop/code_study/Melon_Ticket/cloud-ocr`
- 独立环境变量前缀：`MELON_OCR_*`
- 管理页：`GET /console`
- 部署入口：
  - `docker compose up -d --build`
  - 或 `uvicorn app:app --host 0.0.0.0 --port 8080`

## 授权说明

- 激活码首次激活时会绑定当前机器的稳定设备 ID。
- 后台 OCR 请求必须带授权 token；token 过期时扩展会自动续期。
- 若激活码被撤销、过期或已绑定其他设备，扩展会锁定功能区并停止 OCR 请求。

## 后续迭代方式

后续每增加一个功能，优先按下面方式扩展：

1. 先补充页面检测规则或选择器证据。
2. 再把具体动作写进独立 stage 或独立 action 模块。
3. 如需复用旧 `Melon` 逻辑，只抽取最小必要片段，避免迁入整块冗余代码。
