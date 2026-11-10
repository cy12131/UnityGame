# M1：C# 与 Unity 核心机制总结

## 1. 阶段概况

  ------------------------------------------------------------------------------------------------
  项目                                内容
  ----------------------------------- ------------------------------------------------------------
  项目                                UnityGame

  阶段                                M1：C# 与 Unity 核心机制

  Unity Editor                        6000.3.22f1

  开发平台                            Windows 11

  IDE                                 Visual Studio 2022 Community

  主要语言                            C#

  学习方式                            理论讲解为主，结合 C++ 概念对照

  阶段状态                            五个知识模块已完成理论讲解；实践任务按用户要求跳过或未安排
  ------------------------------------------------------------------------------------------------

## 2. 阶段目标

理解 Unity 客户端开发所依赖的 C# 基础和 Unity 核心对象模型，能够解释普通
C# 类与 MonoBehaviour 的区别、GameObject/Component/Transform
的关系、Unity 生命周期、Inspector 与序列化、Scene/Prefab
以及运行时对象管理。

本阶段不要求完成完整游戏功能，也不以代码练习数量作为阶段验收条件。

## 3. M1.1：C# 面向对象基础

通过 `PlayerStats` 示例学习 C#
类、对象、构造函数、封装、属性和方法，并与 C++ 对照。

``` csharp
public class PlayerStats
{
    public string PlayerName { get; private set; }
    public int Level { get; private set; }
    public int HP { get; private set; }

    public PlayerStats(string name, int level, int hp)
    {
        PlayerName = name;
        Level = level;
        HP = hp;
    }

    public void TakeDamage(int damage)
    {
        HP -= damage;
        if (HP < 0)
            HP = 0;
    }
}
```

学习要点： - C# Property：`get` 读取，`set` 设置，`private set`
限制外部修改。 - 值类型：常见如 `int`、`float`、`bool`、`struct`。 -
引用类型：常见如 `class`、`string`。 - class
变量赋值复制的是对象引用，不是对象实例本身。 - C# GC
管理托管对象；外部资源仍需适当释放。Unity 对象还涉及引擎侧生命周期。 -
`namespace` 用于组织类型并避免命名冲突。

**实践状态：** 原计划创建 `PlayerStats.cs`
并实现属性、构造函数、伤害、治疗和升级方法。用户明确要求跳过该实践任务。因此记录为"理论讲解完成；代码实践主动跳过"，不记为实践验收通过。

## 4. M1.2：GameObject、Component 与 Transform

### GameObject

GameObject 是 Unity
场景中的游戏对象容器，可代表玩家、NPC、敌人、宝箱、摄像机或其他实体。

### Component

Component 为 GameObject 提供具体能力。角色可以由多个组件组合：

``` text
Player (GameObject)
├── Transform
├── SpriteRenderer
├── Rigidbody2D
├── CapsuleCollider2D
├── Animator
├── PlayerController
└── PlayerCombat
```

理解组件组合思想：对象通过组合不同组件获得能力，而不是把所有功能都写在单个巨大类中。

### Transform

Transform 管理对象空间属性和层级关系： - Position：世界位置； -
Rotation：世界旋转； - Scale：缩放； - Local Position / Local Rotation /
Local Scale：相对于父对象的局部变换。

### 组件获取

`GetComponent<T>()` 可以从当前 GameObject 获取指定组件，例如：

``` csharp
Rigidbody2D rb = GetComponent<Rigidbody2D>();
```

如果组件不存在，查询结果可能为空，实际开发需要检查引用。

## 5. M1.3：MonoBehaviour 与 Unity 生命周期

### MonoBehaviour

MonoBehaviour 是 Unity 提供的组件基类。继承它的脚本可以挂载到
GameObject，并参与 Unity 生命周期管理。

简化继承关系：

``` text
UnityEngine.Object
    └── Component
        └── Behaviour
            └── MonoBehaviour
                └── PlayerController
```

### 普通 C# 类与 MonoBehaviour

  对比                              普通 C# 类         MonoBehaviour
  --------------------------------- ------------------ ---------------------------------
  是否继承 MonoBehaviour            否                 是
  创建方式                          通常使用 `new`     使用 Unity 组件机制
  是否可挂载 GameObject             否                 是
  是否自动收到 Unity 生命周期回调   否                 是
  生命周期管理                      普通托管对象规则   同时涉及 Unity 引擎对象生命周期

普通业务类可以使用 `new` 创建；MonoBehaviour 组件应通过
`AddComponent<T>()` 或 Prefab 实例化等 Unity 机制创建，不能把普通 `new`
当作正常组件创建方式。

### 主要生命周期函数

  函数              主要用途
  ----------------- ------------------------------------------
  `Awake()`         组件自身初始化、获取组件引用
  `OnEnable()`      对象或组件启用时，常用于注册事件
  `Start()`         首次运行前的初始化
  `Update()`        每帧游戏逻辑
  `FixedUpdate()`   固定时间步长的物理相关更新
  `LateUpdate()`    当前帧 Update 之后的逻辑，例如摄像机跟随
  `OnDisable()`     组件禁用时清理或取消订阅
  `OnDestroy()`     对象销毁时执行清理

### Update 与 FixedUpdate

-   Update 按渲染帧调用，频率受帧率影响。
-   FixedUpdate 按固定时间步长调用，适合物理模拟。
-   一帧内 FixedUpdate 可能执行零次、一次或多次。
-   使用 `Time.deltaTime` 处理与帧率无关的时间增量。
-   常见实践是 Update 中读取输入，FixedUpdate 中执行 Rigidbody
    物理操作。

### 生命周期函数不是必需模板

所有生命周期函数均按需编写。没有对应需求时可以删除模板生成的空
Start/Update，也不需要手动调用这些回调。Unity
会在相应时机调度已实现的回调。

**实践状态：** 以理论讲解为主，没有安排强制代码实践任务。

## 6. M1.4：Inspector、序列化与对象引用

### Inspector

Inspector 显示当前选中 GameObject 的组件及其可编辑配置。脚本字段可以通过
Unity 序列化机制显示并保存。

### 字段序列化

常见规则： - 可序列化的 `public` 字段通常显示在 Inspector； - `private`
字段可通过 `[SerializeField]` 显示； - 普通 C# Property 默认不会被 Unity
序列化； - `static`、`const`、`readonly` 字段不按普通字段方式序列化； -
Unity 序列化支持的类型范围有限，Dictionary
通常不能直接按普通字段方式序列化； - 自定义普通 C# 类需要使用
`[System.Serializable]`，并由可序列化字段引用，才能在 Inspector
中显示其数据。

示例：

``` csharp
public class PlayerController : MonoBehaviour
{
    [SerializeField]
    private float moveSpeed = 5f;

    public float MoveSpeed => moveSpeed;
}
```

推荐优先采用
`private + [SerializeField]`，在保留封装的同时允许编辑器配置。

### 对象引用

可以在 Inspector 中配置对 GameObject、Transform 或其他 Component
的引用：

``` csharp
[SerializeField]
private Transform cameraTarget;
```

引用对象不等于复制对象，而是建立对象引用关系。

### Inspector 引用与 GetComponent

-   Inspector
    引用：由开发者在编辑器中拖拽指定，适合引用场景对象或预先配置的组件。
-   `GetComponent<T>()`：从指定 GameObject 查询组件，适合获取自身组件。
-   两种方式都可能得到空引用，实际开发需要校验引用有效性。

### 编辑模式与运行模式

编辑模式中修改并保存的场景或 Prefab 配置可以持久化。Play
模式中的运行时修改通常不会自动写回原始场景或 Prefab，停止 Play
后一般恢复进入 Play 前的编辑状态。

## 7. M1.5：Scene、Prefab 与对象管理

### Scene

Scene 是组织游戏内容的基本单位，可以包含
GameObject、Camera、Light、地图、角色、NPC、UI 等。Hierarchy
展示当前加载场景中的 GameObject 层级。

一个游戏可以包含多个 Scene，例如主菜单、村庄、地牢、战斗和结算场景。

### SceneManager

了解场景加载接口：

``` csharp
using UnityEngine.SceneManagement;

SceneManager.LoadScene("Dungeon");
SceneManager.LoadSceneAsync("Dungeon");
```

常见加载模式： - Single：加载新场景并替换当前场景； -
Additive：在当前已加载场景基础上追加加载场景。

### Prefab

Prefab 是可复用的 GameObject
模板，可以保存对象的组件配置、子对象层级和资源引用。

  概念              含义
  ----------------- ------------------------
  Prefab Asset      项目资源中的对象模板
  GameObject        实际游戏对象
  Prefab Instance   根据 Prefab 创建的实例

Prefab 可用于玩家、NPC、敌人、宝箱、门和其他重复对象。

### Prefab Override 与 Variant

-   Prefab Instance 可以具有相对于源 Prefab 的覆盖配置；
-   Apply 将实例修改应用回 Prefab；
-   Revert 撤销实例覆盖；
-   Prefab Variant 可以在基础 Prefab 上形成具有差异的变体。

Prefab Variant 与继承有一定概念相似性，但它属于 Unity
资源层面的变体机制，不等同于 C# 类继承。

### Instantiate

运行时创建对象：

``` csharp
GameObject enemy = Instantiate(
    enemyPrefab,
    spawnPosition,
    Quaternion.identity
);
```

也可以指定父对象：

``` csharp
Instantiate(
    enemyPrefab,
    spawnPosition,
    Quaternion.identity,
    enemyContainer
);
```

### Destroy 与 SetActive

-   `Destroy(obj)`：请求 Unity
    销毁对象；实际销毁并非在调用代码的同一瞬间完成。
-   `SetActive(false)`：禁用对象，但对象仍然存在，可以重新启用。
-   两者用途不同，不应混淆。

### Transform 层级与 SetParent

``` csharp
weapon.transform.SetParent(player.transform, false);
```

第二个参数 `false`
表示不保持原世界变换，而是按新的父对象关系使用局部变换。默认参数为
`true`，尽可能保持世界变换。

### 对象池

初步了解 Object Pool： - 预先创建一批对象； - 需要时取出并启用； -
使用结束后禁用并归还； - 减少高频 Instantiate/Destroy 带来的额外开销。

对象池的具体实现安排在后续游戏系统开发阶段学习。

## 8. M1 阶段知识体系

``` text
C# 基础
  ├── 类、对象、封装
  ├── Property
  ├── 值类型与引用类型
  ├── GC
  └── Namespace

Unity 对象模型
  ├── GameObject
  ├── Component
  └── Transform

Unity 运行机制
  ├── MonoBehaviour
  ├── Awake / OnEnable / Start
  ├── Update / FixedUpdate / LateUpdate
  └── OnDisable / OnDestroy

编辑器与数据
  ├── Inspector
  ├── SerializeField
  ├── Unity Serialization
  └── Object Reference

场景与对象管理
  ├── Scene / Hierarchy
  ├── Prefab / Prefab Variant
  ├── Instantiate / Destroy
  ├── SetActive
  └── Transform Parent / Child
```

## 9. 阶段验收与实践状态

  模块                                    理论讲解   实践状态
  --------------------------------------- ---------- -----------------------------------
  M1.1 C# 面向对象基础                    已完成     用户主动跳过 PlayerStats 代码练习
  M1.2 GameObject、Component、Transform   已完成     以概念讲解为主
  M1.3 MonoBehaviour 与生命周期           已完成     以概念讲解为主
  M1.4 Inspector、序列化与对象引用        已完成     以概念讲解为主
  M1.5 Scene、Prefab 与对象管理           已完成     以概念讲解为主

### 阶段结论

M1
五个模块均已完成理论讲解。用户偏好先理解概念和机制，实践任务可以按需跳过，因此本阶段不声称已经完成完整代码实践或运行验证。

后续在 M2 的 2D 场景与角色控制开发中，将逐步把本阶段知识应用到实际 Unity
工程。

## 10. 下一阶段：M2 2D 场景与角色控制

计划内容： 1. Sprite 与 Sprite Editor； 2. Tileset 切片； 3. Tile
Palette 与 Tilemap； 4. Rigidbody2D； 5. Collider2D； 6. Input System；
7. Animator； 8. 摄像机跟随； 9. 场景交互。

目标是构建一个可探索的 2D RPG 场景，包含地图、玩家移动、碰撞和基础 NPC
交互。

## 11. 后续工程约定

-   Unity 项目使用 Windows 11 + Unity 6000.3.22f1 + Visual Studio 2022
    Community。
-   版本管理使用 Git + GitHub。
-   每个阶段完成后提供一份完整 Markdown 总结文档。
-   总结应记录实际完成内容、设计决策、实践与验证状态、遗留问题和下一阶段计划。
-   当前不主动引入 Codex 辅助开发流程，除非用户之后明确提出。
-   以就业能力为目标，主线为 Unity C# 客户端开发，并逐步与 C++ Linux
    游戏服务器结合。
