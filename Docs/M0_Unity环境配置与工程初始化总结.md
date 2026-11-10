# M0：Unity 开发环境与工程初始化总结

## 1. 阶段概况

  项目           记录
  -------------- -----------------------------------------------------------
  项目名称       UnityGame
  阶段           M0：开发环境与工程初始化
  开发平台       Windows 11
  Unity Editor   6000.3.22f1（Unity 6.3）
  IDE            Visual Studio 2022 Community
  版本管理       Git + GitHub
  本地项目路径   `D:\Code\Unity_study\UnityGame`
  GitHub 仓库    `https://github.com/cy12131/UnityGame`
  主分支         `main`
  阶段结论       基础环境与远程版本管理已建立；M0.4 运行验证按用户决定跳过

## 2. 阶段目标

建立可用于后续 Unity 客户端开发的本地环境和工程基础，完成 Unity
工程初始化、基础目录规划、Git 版本管理及 GitHub 远程仓库连接。

本阶段不实现具体游戏玩法，也不引入 Codex 辅助开发流程。

## 3. 开发环境核查（M0.1）

### 3.1 已确认环境

  检查项               实际结果                               状态
  -------------------- -------------------------------------- --------
  操作系统             Windows 11                             已确认
  Unity Editor         6000.3.22f1                            已确认
  Git                  2.53.0.windows.3                       已确认
  Git 全局用户名       `cy12131`                              已确认
  Git 全局邮箱         已配置                                 已确认
  Visual Studio        Visual Studio 2022 Community           已确认
  Unity 开发工作负载   `Game development with Unity` 已安装   已确认

### 3.2 环境结论

现有 Unity、Visual Studio 和 Git 环境满足开始 Unity
客户端项目开发的基础要求。本阶段无需重新安装上述软件。

## 4. Unity 工程初始化（M0.2）

项目名称为 `UnityGame`，本地路径为：

`D:\Code\Unity_study\UnityGame`

规划的工程目录如下：

``` text
UnityGame/
├── Assets/
│   ├── Art/
│   ├── Animations/
│   ├── Audio/
│   ├── Prefabs/
│   ├── Scenes/
│   ├── Scripts/
│   │   ├── Core/
│   │   ├── Player/
│   │   ├── Combat/
│   │   ├── NPC/
│   │   ├── UI/
│   │   └── Common/
│   ├── ScriptableObjects/
│   ├── UI/
│   └── Editor/
├── Packages/
├── ProjectSettings/
├── Docs/
├── Tests/
├── .gitignore
└── README.md
```

### 4.1 Unity 序列化与版本控制设置

此前阶段指导要求设置：

-   Version Control Mode：`Visible Meta Files`
-   Asset Serialization Mode：`Force Text`

这两项用于让 Unity 资源元文件可见，并让场景、Prefab
等资源采用便于版本控制的文本序列化形式。

**状态说明：** 已按 M0.2
步骤进行工程配置，但用户没有在对话中逐项回报设置截图或最终值；因此本总结不将其标记为独立核验通过。后续如遇资源引用或
Git 合并问题，可再复核。

### 4.2 Visual Studio 外部脚本编辑器

此前阶段指导要求在 Unity Preferences \> External Tools 中选择 Visual
Studio 2022，并确认 Visual Studio Editor Package。

**状态说明：** 已纳入初始化配置步骤，但 M0.4
的联动验证被用户主动跳过，故不声称已验证脚本双击打开、IntelliSense 或
Unity 编译联动。

## 5. Git 与 GitHub 配置（M0.3）

### 5.1 Git 仓库

已在 Unity 项目目录中初始化 Git 仓库，并使用 `main` 作为主分支。

### 5.2 Git 忽略规则

项目采用 Unity 项目常见的忽略策略。以下生成目录不应纳入版本控制：

-   `Library/`
-   `Temp/`
-   `Obj/`
-   `Build/`、`Builds/`
-   `Logs/`
-   `UserSettings/`
-   IDE 临时文件和操作系统临时文件

以下项目文件应保留在版本控制中：

-   `Assets/`
-   `Packages/`
-   `ProjectSettings/`
-   资源对应的 `.meta` 文件
-   `Packages/packages-lock.json`

特别注意：不要随意删除 `.meta` 文件，否则可能破坏 Unity 资源 GUID
引用关系。

### 5.3 远程仓库

  项目       结果
  ---------- --------------------------------------------
  远程仓库   `https://github.com/cy12131/UnityGame.git`
  远程名称   `origin`
  本地分支   `main`
  跟踪分支   `origin/main`
  首次推送   成功

用户提供的终端输出确认：

``` text
[new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

随后执行 `git remote -v`，确认 fetch 和 push 地址均指向上述 GitHub
仓库。

随后执行 `git status`，得到：

``` text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

因此，GitHub
远程连接、主分支跟踪关系及当时工作区干净状态均已得到终端输出确认。

### 5.4 Git 状态结论

**M0.3：通过。**

## 6. M0.4：首次运行验证

原计划包含：

-   创建 `M0EnvironmentCheck.cs`；
-   创建 `M0_EnvironmentCheck` 测试场景；
-   在 Unity Play 模式中检查 Console 输出；
-   验证 Visual Studio 2022 与 Unity 的脚本联动；
-   提交测试脚本和场景。

用户明确要求跳过这一步。

**状态：主动跳过，未执行，不计为通过。**

因此，目前没有证据证明 M0.4 所列的脚本编译、Play 模式日志和 IDE
联动测试已经完成。

## 7. 阶段验收汇总

  子阶段   内容                                状态
  -------- ----------------------------------- ----------------------------------
  M0.1     本地开发环境核查                    已完成
  M0.2     Unity 工程初始化与配置              已按流程推进；个别配置未独立复核
  M0.3     Git 与 GitHub 版本管理              已完成，远程推送成功
  M0.4     测试场景、脚本运行及 IDE 联动验证   用户主动跳过

### 阶段结论

M0 的主要目标------确认本地开发工具、建立 Unity 工程基础并完成 GitHub
远程版本管理------已经实现。由于 M0.4
被主动跳过，不能将本阶段描述为完成了完整的 Unity 运行链路验证。

后续 M1 学习过程中，将通过实际创建 C# 脚本、理解 Unity
组件机制和运行小型练习逐步验证开发链路。

## 8. 后续注意事项

1.  Unity 资源的 `.meta` 文件必须与资源一起进行版本管理。
2.  不要提交 `Library/`、`Temp/` 等本地生成目录。
3.  场景和 Prefab 的修改应通过 Unity Editor 完成并在运行时检查。
4.  后续每个阶段结束时，形成独立、完整的 Markdown 阶段总结。
5.  当前暂不采用 Codex
    辅助开发；除非用户之后明确提出，否则按正常学习与手动实践流程推进。

## 9. 下一阶段

**M1：C# 与 Unity 核心机制**

计划学习：

-   C# 面向对象与 C++ 的差异；
-   C# 值类型、引用类型与垃圾回收；
-   Unity 的 GameObject、Component、Transform；
-   MonoBehaviour 与 Unity 生命周期；
-   Inspector 序列化字段；
-   Scene、Prefab 与对象引用。

M1 将通过小型练习逐步验证 Unity 脚本编译与运行，不再重复 M0
的软件安装工作。
