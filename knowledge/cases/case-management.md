# Good / Bad Cases 管理规范

> 更新日期：2026-04-05
> 存放位置：knowledge/cases/

---

## 一、核心规则

**每当用户确认某个生成结果为"好"或"坏"时，必须同时保存：**
1. **图片文件**：保存到对应案例文件夹
2. **Prompt 文本**：保存为 `prompt.md`，记录完整的 ImageGen prompt
3. **元数据**：保存为 `metadata.md`，记录来源、评价、关键要素

---

## 二、文件结构设计

```
knowledge/cases/
├── cases.md                        # Cases 总索引
├── good/                           # 好案例集合
│   ├── P1-封面/                    # 每个案例独立文件夹
│   │   ├── image.png               # 案例图片
│   │   ├── prompt.md               # 完整 ImageGen prompt
│   │   └── metadata.md             # 元数据（来源、评价、关键要素）
│   ├── P2-豆包/
│   │   ├── image.png
│   │   ├── prompt.md
│   │   └── metadata.md
│   ├── P4-ClaudeCode-V2/
│   │   ├── image.png
│   │   ├── prompt.md
│   │   └── metadata.md
│   └── ...
└── bad/                            # 坏案例集合
    ├── P3-豆包-V1/
    │   ├── image.png
    │   ├── prompt.md
    │   └── metadata.md
    ├── P4-ClaudeCode-V1/
    │   ├── image.png
    │   ├── prompt.md
    │   └── metadata.md
    └── ...
```

**设计原则：**
- 每个案例一个文件夹，文件夹名 = 案例名称（如 `P2-豆包`）
- 固定文件名：`image.png`、`prompt.md`、`metadata.md`
- 通过文件夹名直接关联图片和 prompt
- 支持同一案例的多个版本（如 `P4-ClaudeCode-V1/`、`P4-ClaudeCode-V2/`）
- 不再使用 `cover/`、`inner/` 子分类，直接在 good/bad 下按案例名组织

---

## 三、文件内容模板

### prompt.md
```markdown
# ImageGen Prompt

[完整的 ImageGen prompt 文本，可直接复制使用]
```

### metadata.md
```markdown
# 案例元数据

## 基本信息
- **来源**：[Post 名称 / 主题]
- **页面**：[P{N}-{页面主题}]
- **版本**：V{N}
- **日期**：YYYY-MM-DD
- **类型**：good / bad

## 用户评价
[用户的原话评价]

## 关键要素
### 成功要素（good case）
- ✅ [要素1]
- ✅ [要素2]

### 失败原因（bad case）
- ❌ [原因1]
- ❌ [原因2]

## 可复用模式
[提取可复用的 prompt 写法、结构、描述方式]

## 关联案例
- 参考：[其他相关案例文件夹名]
- 对比：[对比案例文件夹名]
```

---

## 四、命名规范

### 文件夹命名
- 格式：`P{N}-{简短主题}[-V{版本}]`
- 示例：
  - `P1-封面/`
  - `P2-豆包/`
  - `P4-ClaudeCode-V2/`
  - `P3-豆包-V1/`（bad case）

### 文件命名
- 固定使用：`image.png`、`prompt.md`、`metadata.md`
- 不使用版本号后缀，版本号体现在文件夹名中

---

## 五、执行流程

1. **用户确认好/坏** → 创建案例文件夹
2. **保存图片** → 复制到 `image.png`
3. **保存 Prompt** → 从 xhs-images 目录复制完整 prompt 到 `prompt.md`
4. **填写元数据** → 创建 `metadata.md`，记录来源、评价、关键要素
5. **更新 cases.md** → 添加新案例索引记录

---

## 六、迁移计划

将现有扁平结构的 cases 迁移到新结构：
- 扫描 `good/inner/`、`good/cover/` 和 `bad/` 下的所有 `.png` 文件
- 为每个图片创建对应文件夹
- 移动图片到 `image.png`
- 查找关联的 prompt（如果有）或标记为"待补充"
- 删除旧的 `good/inner/`、`good/cover/` 文件夹
- 更新 cases.md 索引

---

## 七、检查清单

每次保存案例时检查：
- [ ] 案例文件夹已创建（命名符合规范）
- [ ] `image.png` 已保存
- [ ] `prompt.md` 已保存完整 prompt
- [ ] `metadata.md` 已填写基本信息和用户评价
- [ ] cases.md 索引已更新

---

## 版本历史

| 日期 | 变更 |
|------|------|
| 2026-04-05 | 初版创建，建立按案例分文件夹的规范 |
