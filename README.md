# ⚡ Error Code Editor — 错误码编辑器

一款基于 Web 的 C/C++ 错误码定义辅助工具。可视化地编辑错误码，并一键生成 `#define` 或 `enum` 风格的头文件。

## 快速开始

直接用浏览器打开 `index.html` 即可运行，无需安装、无需服务器。

```bash
# 克隆仓库
git clone https://github.com/SZyooo/ErrCodeEditor.git

# 打开 index.html
start index.html   # Windows
open index.html    # macOS
```

## 功能特性

### 1. 整数类型选择
支持 C/C++ 标准的所有整数类型：`uint8_t` ~ `uint64_t`、`int8_t` ~ `int64_t`。

### 2. 模块位数 / 错误码位数
- 通过滑块分配模块占用的位数，剩余位数自动分配给错误码值
- 例如选择 `uint32_t`（32位），模块占 8 位，则错误码占 24 位（范围 0 ~ 16777215）

### 3. 定义方式切换
- **`#define`**：宏定义风格
- **`enum`**：枚举类型风格（生成 `err_code_t` 枚举）

### 4. 错误码管理
| 操作 | 说明 |
|------|------|
| **添加** | 在列表末尾追加新的错误码，自动递增 |
| **插入** | 在当前选中行之前插入新的错误码 |
| **删除** | 删除当前选中的错误码 |
| **编辑** | 表格内直接编辑名称、描述 |

### 5. 自动递增
- 勾选"自动"列：码值从前一个错误码值 +1 自动计算
- 取消勾选"自动"：手动输入固定码值
- 自动递增时会自动跳过已占用的码值

### 6. 码值防重复
- 手动输入的码值如果与已有码值冲突，会被拒绝并提示
- 自动递增的码值会自动跳过冲突值
- 冲突项在行首会有红色警示标记

### 7. 码值溢出检测
- 错误码值超出当前位数范围时，行首显示黄色警示标记

### 8. 生成 C 代码
- 选择输出头文件（File System Access API 或手动路径）
- 点击"生成代码并保存"自动写入文件
- 同一文件只需选择一次，后续直接生成

### 9. 配置导入/导出
- 导出当前配置为 JSON 文件
- 导入历史配置继续编辑

### 10. 命名模板（前缀/后缀）
- 在设置中配置全局命名前缀和后缀
- 错误码列表只编辑"正文"部分，完整名称自动拼接
- 例如：前缀 `ERR_` + 正文 `SUCCESS` → 生成 `ERR_SUCCESS`

### 11. 项目管理
- 选择 `projects/` 文件夹作为项目存储目录
- 支持：**新建项目**、**保存**、**另存为**、**删除**
- 项目以 JSON 文件存储在 `projects/` 目录中
- `projects/` 已加入 `.gitignore`，不会被提交到仓库
- 每个项目独立保存/加载，方便管理多个模块的错误码

### 12. 自动保存
- 当前编辑自动保存到浏览器 `localStorage`
- 关闭页面后重新打开，自动恢复上次编辑状态

## 生成的代码示例

### `#define` 风格

```c
#ifndef ERR_CODE_H
#define ERR_CODE_H

#include <stdint.h>

#define ERR_MODULE_BITS  (8)
#define ERR_CODE_BITS    (24)
#define ERR_MODULE_MASK  ((1U << ERR_MODULE_BITS) - 1)
#define ERR_CODE_MASK    ((1U << ERR_CODE_BITS) - 1)

#define ERR_CODE(mod, val)  ((((uint32_t)(mod)) << ERR_CODE_BITS) | ((uint32_t)(val) & ERR_CODE_MASK))
#define ERR_GET_MODULE(err) (((err) >> ERR_CODE_BITS) & ERR_MODULE_MASK)
#define ERR_GET_CODE(err)   ((err) & ERR_CODE_MASK)

#define MOD_ID  (1)

#define ERR_SUCCESS  ERR_CODE(MOD_ID, 0)  // 操作成功
#define ERR_FAIL     ERR_CODE(MOD_ID, 1)  // 操作失败

#endif // ERR_CODE_H
```

### `enum` 风格

```c
typedef enum {
    ERR_SUCCESS = ERR_CODE(MOD_ID, 0),  // 操作成功
    ERR_FAIL    = ERR_CODE(MOD_ID, 1),  // 操作失败
} err_code_t;
```

## 错误码结构

```
  模块编号 (N bits)     错误码值 (M bits)
┌────────────────────┬────────────────────┐
│    module_id       │    code_value      │
└────────────────────┴────────────────────┘
  total_bits = N + M

完整值 = (module_id << M) | code_value
```

## 浏览器兼容性

| 特性 | 兼容性 |
|------|--------|
| 基本编辑功能 | 所有现代浏览器 |
| File System Access API (直接保存) | Chrome 86+、Edge 86+ |
| 下载保存 (后备方案) | 所有浏览器 |

## 开发

本项目为纯静态单页应用，所有代码在 `index.html` 中。

```bash
# 无构建步骤，直接编辑 index.html 即可
```

## License

MIT
