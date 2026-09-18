# 《未寄出的森林》GitHub 上传与协作规范

本文档用于指导团队把 UE5 项目上传到 GitHub，并约定日常协作方式。

## 1. 仓库类型

建议使用：

```text
GitHub 私有仓库
+ Git LFS
+ GitHub Desktop / 命令行
+ 明确资源负责人制度
```

UE5 项目会包含大量二进制资源，例如 `.uasset`、`.umap`、贴图、音频、模型等。普通 Git 不适合直接管理这些大文件，因此必须启用 Git LFS。

## 2. 需要提交的内容

必须提交：

```text
ProjectName.uproject
Config/
Content/
Source/      如果项目使用 C++
Plugins/     如果使用项目内插件
.gitignore
.gitattributes
README.md
GITHUB_UPLOAD_GUIDE.md
```

其中 `ProjectName.uproject` 后续应替换为真实项目文件，例如：

```text
UnsentForest.uproject
```

## 3. 不要提交的内容

不要提交：

```text
Binaries/
DerivedDataCache/
Intermediate/
Saved/
.vs/
*.sln
*.opensdf
*.sdf
*.suo
*.user
*.userosscache
*.sln.docstates
```

这些文件通常由 Unreal Engine、Visual Studio 或本地缓存自动生成。提交它们会让仓库变大，也容易造成冲突。

## 4. 必须使用 Git LFS 管理的资源

以下资源必须交给 Git LFS 管理：

```text
*.uasset
*.umap
*.fbx
*.png
*.jpg
*.tga
*.exr
*.wav
*.mp3
*.mp4
*.mov
*.blend
*.psd
```

本仓库已经提供 `.gitattributes` 模板，用于声明这些文件类型走 LFS。

## 5. 本地建仓流程

如果当前文件夹还不是 Git 仓库，在项目根目录执行：

```bash
git init
git lfs install
git add .gitattributes .gitignore README.md GITHUB_UPLOAD_GUIDE.md
git commit -m "Initialize project docs and Git LFS rules"
```

等 UE5 项目创建完成后，再提交项目文件：

```bash
git add ProjectName.uproject Config Content Source Plugins
git commit -m "Add initial UE5 project"
```

如果暂时没有 `Source/` 或 `Plugins/`，可以不添加它们。

## 6. GitHub 远程仓库流程

在 GitHub 创建私有仓库后，把远程地址绑定到本地仓库：

```bash
git remote add origin https://github.com/你的组织或用户名/仓库名.git
git branch -M main
git push -u origin main
```

如果使用 GitHub Desktop，也可以选择：

```text
File -> Add Local Repository -> Publish repository -> 勾选 Private
```

发布前确认 `.gitattributes` 已经存在，否则 UE5 资源可能不会进入 LFS。

## 7. 日常协作流程

每次开始工作前：

```bash
git pull
```

完成一个清晰的小任务后：

```bash
git add .
git commit -m "Add pond letter puzzle"
git push
```

提交信息建议描述实际改动，例如：

```text
Add mailbox interaction blueprint
Update pond letter UI
Import paper forest textures
Fix theater camera switch
```

## 8. 团队协作规则

最重要的规则：

```text
不要多人同时修改同一个 .umap 或 .uasset 文件。
```

`.umap` 和 `.uasset` 是二进制文件，冲突后很难像代码一样合并。团队应提前分配资源负责人。

建议分工：

| 模块 | 建议负责人 | 说明 |
|---|---|---|
| 主地图 | 关卡负责人 | 管理主要关卡地图，整合所有资源 |
| 纸质景片机关 | 蓝图负责人 | 管理景片移动、灯光调整、视角切换 |
| 信件系统 | UI/叙事负责人 | 管理信件 UI、邮戳、文字和任务状态 |
| 美术资源 | 美术负责人 | 管理纸偶、植物、场景模型、材质 |
| 音效与反馈 | 音效负责人 | 管理蛙鸣、纸张声、灯光反馈和环境音 |
| 版本管理 | Git 负责人 | 检查提交、处理 LFS 和冲突问题 |

发生 `.uasset` 或 `.umap` 冲突时，不要随意合并。先确认应该保留谁的版本，再由负责人重新整合。

## 9. 推荐仓库初始结构

UE5 项目创建后，建议形成类似结构：

```text
项目根目录/
  Config/
  Content/
    UnsentForest/
      Blueprints/
      Maps/
      UI/
      Materials/
      Meshes/
      Characters/
      Letters/
      VFX/
      Audio/
      Data/
  Source/          如果使用 C++
  Plugins/         如果使用项目内插件
  UnsentForest.uproject
  README.md
  GITHUB_UPLOAD_GUIDE.md
  .gitignore
  .gitattributes
```

## 10. 上传前检查清单

首次上传前确认：

```text
[ ] 已安装 Git
[ ] 已安装 Git LFS
[ ] 仓库是 Private
[ ] .gitignore 已存在
[ ] .gitattributes 已存在
[ ] .uasset 和 .umap 已纳入 LFS
[ ] 没有提交 Binaries / Intermediate / Saved / DerivedDataCache
[ ] 团队已分配 .umap / .uasset 负责人
```

可以用以下命令检查 LFS 是否追踪资源：

```bash
git lfs track
git lfs ls-files
```

如果已经错误地把大资源作为普通 Git 文件提交，需要先暂停继续上传，由 Git 负责人统一处理历史记录。
