# 发包脚本使用指南

Kode 项目提供了两套发包流程，用于不同的发布场景：

## 🚀 快速使用

### 开发版本发布 (测试用)
```bash
npm run publish:dev
```

### 正式版本发布
```bash
npm run publish:release
```

## 📦 发包策略

### 1. 开发版本 (`dev` tag)
- **目的**: 内部测试和预发布验证
- **版本格式**: `1.1.16-dev.1`, `1.1.16-dev.2`
- **安装方式**: `npm install -g @shareai-lab/kode@dev`
- **特点**:
  - 自动递增 dev 版本号
  - 不影响正式版本的用户
  - 可以快速迭代测试

### 2. 正式版本 (`latest` tag)
- **目的**: 面向最终用户的稳定版本
- **版本格式**: `1.1.16`, `1.1.17`, `1.2.0`
- **安装方式**: `npm install -g @shareai-lab/kode` (默认)
- **特点**:
  - 语义化版本控制
  - 严格的发布流程
  - 包含完整的测试和检查

## 🛠️ 脚本功能详解

### 开发版本发布 (`scripts/publish-dev.js`)

**自动化流程**:
1. ✅ 检查当前分支和工作区状态
2. 🔢 自动生成递增的 dev 版本号
3. 🔨 构建项目
4. 🔍 运行预发布检查
5. 📤 发布到 npm 的 `dev` tag
6. 🏷️ 创建 git tag
7. 🔄 恢复 package.json (不提交版本变更)

**使用场景**:
- 功能开发完成，需要内部测试
- PR 合并前的最终验证
- 快速修复验证

**安全特性**:
- 临时修改 package.json，发布后自动恢复
- 失败时自动回滚
- 不污染主分支版本号

### 正式版本发布 (`scripts/publish-release.js`)

**交互式流程**:
1. 🔍 检查分支 (建议在 main/master)
2. 🧹 确保工作区干净
3. 📡 拉取最新代码
4. 🔢 选择版本升级类型:
   - **patch** (1.1.16 → 1.1.17): 修复 bug
   - **minor** (1.1.16 → 1.2.0): 新功能
   - **major** (1.1.16 → 2.0.0): 破坏性变更
   - **custom**: 自定义版本号
5. ✅ 确认发布信息
6. 🧪 运行测试和类型检查
7. 🔨 构建项目
8. 📝 提交版本更新
9. 🏷️ 创建 git tag
10. 📤 发布到 npm (默认 `latest` tag)
11. 📡 推送到 git 仓库

**安全特性**:
- 交互式确认，避免误发布
- 测试失败时自动回滚版本号
- 完整的 git 历史记录

## 🎯 最佳实践

### 开发流程建议
```bash
# 1. 开发功能
git checkout -b feature/new-feature
# ... 开发代码 ...
git commit -am "feat: add new feature"

# 2. 发布开发版本测试
npm run publish:dev
# 安装测试: npm install -g @shareai-lab/kode@dev

# 3. 测试通过后合并到主分支
git checkout main
git merge feature/new-feature

# 4. 发布正式版本
npm run publish:release
```

### 版本号管理
- **开发版**: 基于当前正式版本自动递增
- **正式版**: 遵循 [语义化版本](https://semver.org/lang/zh-CN/) 规范
- **Git 标签**: 自动创建，格式 `v1.1.16`

### 标签管理
```bash
# 查看所有版本
npm view @shareai-lab/kode versions --json

# 查看 dev 版本
npm view @shareai-lab/kode@dev version

# 查看最新正式版本  
npm view @shareai-lab/kode@latest version
```

## 🔧 故障排除

### 常见问题

**发布失败怎么办？**
- 脚本会自动回滚 package.json
- 检查错误信息，修复后重新运行

**版本号冲突？**
- 开发版本会自动递增，不会冲突
- 正式版本发布前会检查是否已存在

**权限问题？**
- 确保已登录 npm: `npm whoami`
- 确保有包的发布权限

**Git 相关错误？**
- 确保有 git 推送权限
- 检查远程仓库配置: `git remote -v`

### 手动清理
```bash
# 如果发布过程中断，可能需要手动清理
git tag -d v1.1.16-dev.1  # 删除本地标签
git push origin :v1.1.16-dev.1  # 删除远程标签
```

## 📊 监控和分析

```bash
# 查看包下载统计
npm view @shareai-lab/kode

# 查看所有版本的详细信息
npm view @shareai-lab/kode versions --json

# 测试安装
npm install -g @shareai-lab/kode@dev
kode --version
```

---

通过这套双发包系统，你可以：
- 🚀 快速发布开发版本进行内部测试
- 🛡️ 安全发布正式版本给最终用户
- 📈 保持清晰的版本管理和发布历史
- ⚡ 自动化大部分重复操作，减少人为错误