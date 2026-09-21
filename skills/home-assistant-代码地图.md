# home-assistant 代码地图

> home-assistant 项目仓库索引、路由树、域锚点、AI 对话链路部署层——队长分诊与各域 agent 定位及故障排查用

# home-assistant CODEMAP（自动生成骨架 + 人工锚点）

## 仓库索引

| 仓库 | 技术栈 | 端 |
| --- | --- | --- |
| app（uni-app+Vue3） | 记账/物品/空间/家庭 | 小程序前端 |
| backend（Django+DRF） | items/bill/space/users/chat 等 app | 后端 |
| manage-panel | 运维后台前端 | 前端-运维 |

## 路由树（接口→后端 app）

| 前缀 | include app |
| --- | --- |
|  | apps.users.urls |
|  | apps.family.urls |
|  | apps.account.urls |
|  | apps.items.urls |
|  | apps.wardrobe.urls |
|  | apps.travel.urls |
| )),
    path(mp | common.urls |
|  | chat.urls |
|  | apps.adminpanel.urls |
|  | apps.adminpanel.panel_urls |

## 页面路由（前端，主包 32 页）

index、index、email、register、forgot-password、index、index、ai、ai-templates、import、index、category、add、index、index、index、index、profile、index、export、theme、recycle-bin、statistics、annual、index、index、index、add、recommend、calendar、tryon、dashboard

- 分包 pages/family：create、manage、detail、detail、join
- 分包 pages/bill-detail：add、list、statistics、ai、index
- 分包 pages/category：manage、select、sub
- 分包 pages/item-detail：add、category
- 分包 pages/account-book：manage
- 分包 pages/settings：index
- 分包 pages/space：index、editor、three、three3d、storage
- 分包 pages/travel：plan

## 域锚点（人工维护）

| 域 | 前端锚点 | 后端锚点 |
| --- | --- | --- |
| 记账 | pages/bill/index.vue · pages/item/add.vue | apps/items/ · apps/bill/ |
| 3D 空间 | pages/space/three3d.vue（原生WebGL零库）· api/space.ts | apps/items/spaces_views.py |
| 登录/用户 | 登录页 · utils/request.ts(401跳登录) | apps/users/ |
| 运维后台 | manage-panel/src | admin 对接接口 |

## 约定
- 小程序 API 前缀：/api/miniprogram/
- 统一响应：{code,message,data}（WrappedResponseMixin）
- 401 全局跳登录（utils/request.ts）
- 发版版本号：app/src/utils/constants.ts APP_VERSION

## AI 对话链路（生产部署层，故障排查必查）

小程序 AI 对话的多模态/模型问题按四段链路定位：

```text
小程序前端 → www.pnpm.chat/api（nginx）→ 后端容器(8000) → sub2api(服务器8080) → 模型(GLM)
```

- **sub2api**：服务器 8080 的 AI 中转服务，管理面板 s2.pnpm.chat；渠道模型已配 GLM-5.3-flash（多模态可用）
- 图片类缺陷排查顺序：前端上传与展示 → 后端 content 组装（是否透传 image 块）→ sub2api 转发与模型路由 → 上游模型