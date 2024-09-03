# GASDocumentation

我对虚幻引擎5（Unreal Engine 5，简称UE5）的游戏玩法能力系统（Gameplay Ability System，简称GAS）插件的理解，以及一个简单的多人示例项目。这不是官方文档，该项目及我个人与Epic Games无关，我不保证这些信息的准确性。

这份文档的目标是解释GAS中的主要概念和类，并提供一些基于我的经验的额外评论。在社区用户中，有很多关于GAS的“部落知识”，我的目标是在这里分享我所有的知识。

示例项目和文档与虚幻引擎5.3（UE5）保持最新。虽然有针对旧版虚幻引擎的文档分支，但它们不再受支持，并且可能存在错误或过时的信息。请使用与您的引擎版本相匹配的分支。

[GASShooter](https://github.com/tranek/GASShooter) "GASShooter" 是一个展示使用Gameplay Ability System (GAS) 插件在多人第一人称射击/第三人称射击(FPS/TPS)游戏中应用高级技术的姊妹示例项目。这个项目旨在帮助开发者更好地理解和应用虚幻引擎5中的GAS，通过实际的示例代码和项目结构，展示如何在实际游戏开发中实现复杂的游戏机制。

确实，对于任何复杂的软件系统或插件，源代码始终是理解其工作原理和内部机制的最佳文档。对于虚幻引擎5（UE5）中的Gameplay Ability System（GAS）插件来说，也是如此。

<a name="table-of-contents"></a>
## 目录

> 1. [游戏玩法能力系统插件介绍](#intro)
> 1. [示例项目](#sp)
> 1. [使用GAS设置项目](#setup)
> 1. [概念](#concepts)  
>    4.1 [能力系统组件](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [复制模式](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [设置和初始化](#concepts-asc-setup)  
>    4.2 [游戏标签](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [响应游戏标签的变化](#concepts-gt-change)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2 [从插件 .ini 文件中加载游戏标签](#concepts-gt-loadfromplugin)  
>    4.3 [属性](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [属性定义](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [基础值与当前值](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [元属性](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [响应属性变化](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [派生属性](#concepts-a-derived)  
>    4.4 [属性集](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [属性集定义](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [属性集设计](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [具有单个属性的子组件](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [在运行时添加和移除属性集](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [物品属性（武器弹药）](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [物品上的普通浮点数](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [`物品上的属性集`](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [`物品上的 ASC`](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [定义属性](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [初始化属性](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [游戏效果](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [游戏效果定义](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [应用游戏效果](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [移除游戏效果](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [游戏效果修饰符](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [乘法和除法修饰符](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [修饰符上的游戏标签](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [堆叠游戏效果](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [授予的能力](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [游戏效果标签](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [免疫](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [游戏效果规格](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [由调用者设置](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [游戏效果上下文](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [修饰符幅度计算](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [游戏效果执行计算](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [向执行计算发送数据](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [由调用者设置](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [支持数据属性计算修饰符](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [支持数据临时变量计算修饰符](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [游戏效果上下文](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [自定义应用要求](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [费用游戏效果](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [冷却游戏效果](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [获取冷却游戏效果的剩余时间](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [监听冷却开始和结束](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [预测冷却](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [更改活动游戏效果的持续时间](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [在运行时创建动态游戏效果](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [游戏效果容器](#concepts-ge-containers)  
>    4.6 [游戏能力](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [游戏能力定义](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [复制策略](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [服务器尊重远程能力取消](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [直接复制输入](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [将输入绑定到 ASC](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [绑定输入而不激活能力](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [授予能力](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [激活能力](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [被动能力](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.2 [激活失败标签](#concepts-ga-activating-failedtags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [取消能力](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [获取活动能力](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [实例化策略](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [网络执行策略](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [能力标签](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [游戏能力规格](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [向能力传递数据](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [能力费用和冷却](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [能力升级](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [能力集](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [能力批处理](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [网络安全策略](#concepts-ga-netsecuritypolicy)  
>    4.7 [能力任务](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [能力任务定义](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [自定义能力任务](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [使用能力任务](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [根运动源能力任务](#concepts-at-rms)  
>    4.8 [游戏提示](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [游戏提示定义](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [触发游戏提示](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [本地游戏提示](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [游戏提示参数](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [游戏提示管理器](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [防止游戏提示触发](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [游戏提示批处理](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [手动 RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [单个 GE 上的多个 GC](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [游戏提示事件](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [游戏提示可靠性](#concepts-gc-reliability)  
>    4.9 [能力系统全局](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [预测](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [预测键](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [在能力中创建新预测窗口](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [预测生成演员](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [GAS 中预测的未来](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [网络预测插件](#concepts-p-npp)  
>    4.11 [目标](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [目标数据](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [目标演员](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [目标数据过滤器](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [游戏能力世界标记](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [游戏效果容器目标](#concepts-targeting-containers)  
> 1. [常见实现的能力和效果](#cae)  
>    5.1 [晕眩](#cae-stun)  
>    5.2 [冲刺](#cae-sprint)  
>    5.3 [瞄准](#cae-ads)  
>    5.4 [生命偷取](#cae-ls)  
>    5.5 [在客户端和服务器上生成随机数](#cae-random)  
>    5.6 [暴击](#cae-crit)  
>    5.7 [非堆叠游戏效果，但只有最大的幅度实际影响目标](#cae-nonstackingge)  
>    5.8 [在游戏暂停时生成目标数据](#cae-paused)  
>    5.9 [一键交互系统](#cae-onebuttoninteractionsystem)  
> 1. [调试 GAS](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [游戏调试器](#debugging-gd)  
>    6.3 [GAS 日志记录](#debugging-log)  
> 1. [优化](#optimizations)  
>    7.1 [能力批处理](#optimizations-abilitybatching)  
>    7.2 [游戏提示批处理](#optimizations-gameplaycuebatching)  
>    7.3 [能力系统组件复制模式](#optimizations-ascreplicationmode)  
>    7.4 [属性代理复制](#optimizations-attributeproxyreplication)  
>    7.5 [ASC 延迟加载](#optimizations-asclazyloading)  
> 1. [生活质量建议](#qol)  
>    8.1 [游戏效果容器](#qol-gameplayeffectcontainers)  
>    8.2 [将 Blueprint AsyncTasks 绑定到 ASC 委托](#qol-asynctasksascdelegates)  
> 1. [故障排除](#troubleshooting)  
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)  
>    9.2 [`ScriptStructCache` 错误](#troubleshooting-scriptstructcache)  
>    9.3 [动画蒙太奇未复制到客户端](#troubleshooting-replicatinganimmontages)  
>    9.4 [复制 Blueprint 演员时将 AttributeSets 设置为 nullptr](#troubleshooting-duplicatingblueprintactors)  
>    9.5 [未解析的外部符号 UEPushModelPrivate::MarkPropertyDirty(int,int)](#troubleshooting-unresolvedexternalsymbolmarkpropertydirty)  
>    9.6 [枚举名称现在由路径名表示](#troubleshooting-enumnamesarenowpathnames)  
> 1. [常见的 GAS 缩写](#acronyms)
> 1. [其他资源](#resources)  
>    11.1 [与 Epic Game 的 Dave Ratti 的问答](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [社区问题 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [社区问题 2](#resources-daveratti-community2)  
> 1. [GAS 更新日志](#changelog)  
>    * [5.3](#changelog-5.3)  
>    * [5.2](#changelog-5.2)  
>    * [5.1](#changelog-5.1)  
>    * [5.0](#changelog-5.0)  
>    * [4.27](#changelog-4.27)  
>    * [4.26](#changelog-4.26)  
>    * [4.25.1](#changelog-4.25.1)  
>    * [4.25](#changelog-4.25)  
>    * [4.24](#changelog-4.24)

         
<a name="intro"></a>
## 1. Intro to the GameplayAbilitySystem Plugin
From the [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html):
>游戏玩法能力系统是一个高度灵活的框架，用于构建您可能在角色扮演游戏（RPG）或多人在线战斗竞技场（MOBA）游戏中找到的能力（Abilities）和属性（Attributes）。您可以为游戏中的角色构建动作或被动能力，构建因这些动作而累积或减少各种属性的状态效果，实现“冷却”计时器或资源成本以调节这些动作的使用，改变能力及其效果的等级，激活粒子或声音效果等。简而言之，该系统可以帮助您设计、实现并高效地网络化游戏中的能力，无论是简单的跳跃还是您最喜欢的现代RPG或MOBA游戏中角色的能力集。

游戏玩法能力系统（GameplayAbilitySystem）插件是由Epic Games开发的，并随虚幻引擎（Unreal Engine）一同提供。它已经在如《Paragon》和《堡垒之夜》（Fortnite）等AAA级商业游戏中经过实战测试。


该插件为单人和多人游戏提供了即开即用的解决方案，用于：
* 该插件为单人和多人游戏提供了现成的解决方案，用于实现以下功能：([GameplayAbilities](#concepts-ga))
* 操控数值属性`Attributes` 属于actors ([Attributes](#concepts-a))
* 对角色应用状态效果。([GameplayEffects](#concepts-ge))
* 给角色应用 `GameplayTags`  ([GameplayTags](#concepts-gt))
* 生成视觉或声音效果 ([GameplayCues](#concepts-gc))
* 上述所有内容的复制

在多人游戏中，GAS 支持以下内容的 [客户端预测](#concepts-p)：
* 能力激活
* 播放动画蒙太奇
* 属性的变化
* 应用 `GameplayTags`
* 生成 `GameplayCues`
* 通过连接到 `CharacterMovementComponent` 的 `RootMotionSource` 函数进行移动。


**GAS 必须在 C++ 中设置**，但 `GameplayAbilities` 和 `GameplayEffects` 可以由设计师在 Blueprint 中创建。


当前 GAS 存在的问题：
* `GameplayEffect` 延迟调解（无法预测能力冷却时间，导致高延迟的玩家在低冷却能力的射速上比低延迟的玩家更慢）。
* 无法预测 `GameplayEffects` 的移除。然而，我们可以通过预测添加具有相反效果的 `GameplayEffects` 来有效地移除它们。但这并不总是合适或可行，仍然是一个问题。
* 缺乏模板、多人游戏示例和文档。希望这能有所帮助！

**[⬆ 回到顶部](#table-of-contents)**


<a name="sp"></a>
## 2. 示例项目
本文档附带了一个多人第三人称射击游戏示例项目，旨在帮助那些对GameplayAbilitySystem插件不熟悉但对Unreal Engine有所了解的人。用户应具备C++、Blueprints、UMG、Replication等UE中的中级知识。该项目提供了一个示例，展示如何使用 `AbilitySystemComponent` (`ASC`) 在 `PlayerState` 类上为玩家/AI控制的英雄，以及在 `Character` 类上为AI控制的仆从设置一个基本的第三人称射击游戏多人项目。

该项目的目标是保持简单，同时展示GAS的基础知识，并通过注释良好的代码演示一些常见的能力请求。由于其面向初学者的定位，该项目未展示如[预测投射物](#concepts-p-spawn)等高级主题。


展示的概念：
* `PlayerState` 与 `Character` 上的 `ASC`
* Replicated `Attributes`
* Replicated animation montages
* `GameplayTags`
* 在 `GameplayAbilities` 内部和外部应用和移除 `GameplayEffects`
* 应用由护甲减轻的伤害以改变角色的生命值
* `GameplayEffectExecutionCalculations`
* 晕眩效果
* 死亡与重生
* 从服务器上的能力生成 Actor（投射物）
* 通过瞄准和冲刺预测性地改变本地玩家的速度
* 不断消耗耐力以进行冲刺
* 使用法力施放能力
* 被动能力
* 堆叠 `GameplayEffects`
* 目标 Actor
* 在 Blueprint 中创建的 `GameplayAbilities`
* 在 C++ 中创建的 `GameplayAbilities`
* 每个 Actor 实例化的 `GameplayAbilities`
* 非实例化的 `GameplayAbilities`（跳跃）
* 静态 `GameplayCues`（FireGun 投射物碰撞粒子效果）
* Actor `GameplayCues`（冲刺和晕眩粒子效果）


英雄类具有以下能力：


| 能力                        | 输入绑定            | 预测性     | C++ / Blueprint | 描述                                                                                                                                                                  |
| -------------------------- | ------------------- | ---------- | --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Jump                       | 空格键              | 是         | C++             | 使英雄跳跃。                                                                                                                                                           |
| Gun                        | 左鼠标按钮          | 否         | C++             | 从英雄的枪中发射投射物。动画是预测性的，但投射物不是。                                                                                                                |
| Aim Down Sights            | 右鼠标按钮          | 是         | Blueprint       | 按住按钮时，英雄会走得更慢，摄像机会放大，以便更精确地射击。                                                                                                           |
| Sprint                     | 左Shift             | 是         | Blueprint       | 按住按钮时，英雄会跑得更快，但会消耗耐力。                                                                                                                             |
| Forward Dash               | Q                   | 是         | Blueprint       | 英雄向前冲刺，消耗耐力。                                                                                                                                               |
| Passive Armor Stacks       | 被动                | 否         | Blueprint       | 每4秒，英雄会获得一层护甲，最多可叠加4层。受到伤害时会移除一层护甲。                                                                                                  |
| Meteor                     | R                   | 否         | Blueprint       | 玩家选择一个位置，将陨石投放到敌人身上，造成伤害并使其晕眩。目标定位是预测性的，而生成陨石不是。                                                                     |


It does not matter if `GameplayAbilities` are created in C++ or Blueprint. A mixture of the two were used here for example of how to do them in each language.

Minions do not come with any predefined `GameplayAbilities`. The Red Minions have more health regen while the Blue Minions have higher starting health.

For `GameplayAbility` naming, I used the suffix `_BP` to denote the `GameplayAbility's` logic was created in Blueprint. The lack of suffix means the logic was created in C++.

`GameplayAbilities` 是否使用 C++ 或 Blueprint 创建并不重要。在这里，两者结合使用，以展示如何在每种语言中实现它们。

仆从(NPC?)没有预定义的 `GameplayAbilities`。红色仆从(NPC?)拥有更高的生命回复，而蓝色仆从则有更高的初始生命值。

在 `GameplayAbility` 的命名中，我使用后缀 `_BP` 来表示 `GameplayAbility` 的逻辑是使用 Blueprint 创建的。没有后缀则表示逻辑是使用 C++ 创建的。

**Blueprint 资产命名前缀**


| 前缀      | 资产类型          |
| ----------- | ------------------- |
| GA_         | GameplayAbility     |
| GC_         | GameplayCue         |
| GE_         | GameplayEffect      |

**[⬆ Back to Top](#table-of-contents)**

<a name="setup"></a>
## 3. 使用 GAS 设置项目
使用 GAS 设置项目的基本步骤：
1. 在编辑器中启用 GameplayAbilitySystem 插件
2. 编辑 `YourProjectName.Build.cs`，将 `"GameplayAbilities", "GameplayTags", "GameplayTasks"` 添加到 `PrivateDependencyModuleNames`
3. 刷新/重新生成你的 Visual Studio 项目文件
4. 从 4.24 到 5.2 版本，使用 [`TargetData`](#concepts-targeting-data) 时必须调用 `UAbilitySystemGlobals::Get().InitGlobalData()`。示例项目在 `UAssetManager::StartInitialLoading()` 中调用了此方法。从 5.3 开始，该方法将自动调用。有关更多信息，请参见 [`InitGlobalData()`](#concepts-asg-initglobaldata)。

这就是启用 GAS 所需的全部操作。接下来，将 [`ASC`](#concepts-asc) 和 [`AttributeSet`](#concepts-as) 添加到你的 `Character` 或 `PlayerState`，并开始创建 [`GameplayAbilities`](#concepts-ga) 和 [`GameplayEffects`](#concepts-ge) 吧！


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS Concepts

#### Sections

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 能力系统组件
`AbilitySystemComponent` (`ASC`) 是 GAS 的核心。它是一个 `UActorComponent`（[`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)），负责处理与系统的所有交互。任何希望使用 [`GameplayAbilities`](#concepts-ga)、拥有 [`Attributes`](#concepts-a) 或接收 [`GameplayEffects`](#concepts-ge) 的 `Actor` 都必须附加一个 `ASC`。这些对象都存在于 `ASC` 内，并由其管理和复制（`Attributes` 除外，它们由其 [`AttributeSet`](#concepts-as) 复制）。开发人员通常会对其进行子类化，但这并不是必须的。

附加了 `ASC` 的 `Actor` 被称为 `ASC` 的 `OwnerActor`。`ASC` 的物理表现 `Actor` 被称为 `AvatarActor`。`OwnerActor` 和 `AvatarActor` 可以是相同的 `Actor`，例如在 MOBA 游戏中的简单 AI 仆从的情况下。它们也可以是不同的 `Actor`，例如在 MOBA 游戏中由玩家控制的英雄的情况下，`OwnerActor` 是 `PlayerState`，而 `AvatarActor` 是英雄的 `Character` 类。大多数 `Actor` 会在自身上拥有 `ASC`。如果你的 `Actor` 将会重生并需要在重生之间保留 `Attributes` 或 `GameplayEffects`（例如 MOBA 中的英雄），那么 `ASC` 的理想位置是在 `PlayerState` 上。


**注意:** 如果你的 `ASC` 在 `PlayerState` 上，那么你需要增加 `PlayerState` 的 `NetUpdateFrequency`。`PlayerState` 的默认值非常低，这可能导致在客户端上 `Attributes` 和 `GameplayTags` 等变化发生之前出现延迟或感知到的滞后。请务必启用 [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)，Fortnite 使用了它。

如果 `OwnerActor` 和 `AvatarActor` 是不同的 `Actor`，它们都应该实现 `IAbilitySystemInterface`。这个接口有一个必须被重写的函数 `UAbilitySystemComponent* GetAbilitySystemComponent() const`，它返回一个指向其 `ASC` 的指针。`ASC` 通过查找此接口函数在系统内部相互交互。

`ASC` 在 `FActiveGameplayEffectsContainer ActiveGameplayEffects` 中保存其当前活动的 `GameplayEffects`。

`ASC` 在 `FGameplayAbilitySpecContainer ActivatableAbilities` 中保存其授予的 `Gameplay Abilities`。每当你计划迭代 `ActivatableAbilities.Items` 时，务必在循环上方添加 `ABILITYLIST_SCOPE_LOCK();` 以锁定列表，防止其在迭代期间（由于移除能力）发生变化。作用域内的每个 `ABILITYLIST_SCOPE_LOCK();` 都会增加 `AbilityScopeLockCount`，并在作用域结束时减少。当列表被锁定时，请勿尝试在 `ABILITYLIST_SCOPE_LOCK();` 作用域内移除能力（清除能力函数会在内部检查 `AbilityScopeLockCount`，以防止在列表被锁定时移除能力）。


<a name="concepts-asc-rm"></a>
### 4.1.1 复制模式
`ASC` 为复制 `GameplayEffects`、`GameplayTags` 和 `GameplayCues` 定义了三种不同的复制模式——`Full`、`Mixed` 和 `Minimal`。`Attributes` 由它们的 `AttributeSet` 进行复制。


| 复制模式           | 使用场景                                 | 描述                                                                                                                           |
| ------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full`             | 单人游戏                                | 每个 `GameplayEffect` 都会复制到每个客户端。                                                                                   |
| `Mixed`            | 多人游戏，玩家控制的 `Actors`           | `GameplayEffects` 只复制到拥有者客户端。只有 `GameplayTags` 和 `GameplayCues` 会复制到所有人。                                 |
| `Minimal`          | 多人游戏，AI 控制的 `Actors`            | `GameplayEffects` 不会复制给任何人。只有 `GameplayTags` 和 `GameplayCues` 会复制到所有人。                                      |


**注意:** `Mixed` 复制模式要求 `OwnerActor` 的 `Owner` 是 `Controller`。`PlayerState` 的 `Owner` 默认是 `Controller`，但 `Character` 的不是。如果在使用 `Mixed` 复制模式时 `OwnerActor` 不是 `PlayerState`，那么你需要在 `OwnerActor` 上使用有效的 `Controller` 调用 `SetOwner()`。

从 4.24 开始，`PossessedBy()` 现在将 `Pawn` 的拥有者设置为新的 `Controller`。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 设置和初始化
`ASCs` 通常在 `OwnerActor` 的构造函数中构建，并显式标记为可复制。**这必须在 C++ 中完成**。

```c++
AGDPlayerState::AGDPlayerState()
{
	// 创建能力系统组件，并将其设置为显式复制
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}

```

`ASC` 需要在服务器和客户端上使用其 `OwnerActor` 和 `AvatarActor` 进行初始化。你需要在 `Pawn` 的 `Controller` 设置之后（即在控制权移交后）进行初始化。单人游戏只需要关注服务器路径。

对于 `ASC` 位于 `Pawn` 上的玩家控制角色，我通常在服务器上的 `Pawn` 的 `PossessedBy()` 函数中进行初始化，并在客户端上的 `PlayerController` 的 `AcknowledgePossession()` 函数中进行初始化。


```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode 复制模式要求 ASC 的 Owner 的 Owner 是 Controller。
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

对于 `ASC` 位于 `PlayerState` 上的玩家控制角色，我通常在服务器上的 `Pawn` 的 `PossessedBy()` 函数中进行初始化，并在客户端上的 `Pawn` 的 `OnRep_PlayerState()` 函数中进行初始化。这可以确保 `PlayerState` 在客户端上存在。


```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// 在服务器上设置 ASC。客户端在 `OnRep_PlayerState()` 中执行此操作。
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI 不会有 PlayerControllers，所以我们可以在这里再次初始化以确保万无一失。对于有 PlayerControllers 的英雄来说，初始化两次也无妨。
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC for clients. Server does this in PossessedBy.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// Init ASC Actor Info for clients. Server will init its ASC when it possesses a new Actor.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

如果你收到错误信息 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`，那么说明你没有在客户端上初始化你的 `ASC`。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt"></a>
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html) 是以 `Parent.Child.Grandchild...` 形式注册到 `GameplayTagManager` 的分层名称。这些标签在分类和描述对象状态时非常有用。例如，如果一个角色被晕眩了，我们可以在晕眩期间给它一个 `State.Debuff.Stun` 的 `GameplayTag`。

你会发现自己逐渐用 `GameplayTags` 来替换以前使用布尔值或枚举处理的内容，并在对象是否具有某些 `GameplayTags` 上进行布尔逻辑判断。

通常情况下，当我们给一个对象添加标签时，如果它有 `ASC`，我们会将标签添加到它的 `ASC` 中，以便 GAS 可以与它们交互。`UAbilitySystemComponent` 实现了 `IGameplayTagAssetInterface`，提供了访问其拥有的 `GameplayTags` 的函数。

多个 `GameplayTags` 可以存储在一个 `FGameplayTagContainer` 中。与 `TArray<FGameplayTag>` 相比，更推荐使用 `GameplayTagContainer`，因为 `GameplayTagContainers` 添加了一些效率魔法。虽然标签是标准的 `FNames`，但它们可以高效地打包在 `FGameplayTagContainers` 中进行复制，前提是项目设置中启用了 `Fast Replication`。`Fast Replication` 要求服务器和客户端具有相同的 `GameplayTags` 列表。这通常不应该是个问题，因此你应该启用此选项。`GameplayTagContainers` 还可以返回一个 `TArray<FGameplayTag>` 供迭代使用。

存储在 `FGameplayTagCountContainer` 中的 `GameplayTags` 有一个 `TagMap`，其中存储了该 `GameplayTag` 的实例数量。`FGameplayTagCountContainer` 可能仍然包含 `GameplayTag`，但其 `TagMapCount` 为零。如果一个 `ASC` 仍然有一个 `GameplayTag`，你可能会在调试时遇到这种情况。任何 `HasTag()` 或 `HasMatchingTag()` 或类似的函数都会检查 `TagMapCount`，如果 `GameplayTag` 不存在或其 `TagMapCount` 为零，则返回 false。

`GameplayTags` 必须提前在 `DefaultGameplayTags.ini` 中定义。Unreal Engine Editor 提供了一个项目设置中的界面，允许开发者在不手动编辑 `DefaultGameplayTags.ini` 的情况下管理 `GameplayTags`。`GameplayTag` 编辑器可以创建、重命名、搜索引用并删除 `GameplayTags`。

![项目设置中的 GameplayTag 编辑器](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

搜索 `GameplayTag` 引用将调出编辑器中熟悉的 `Reference Viewer` 图表，显示所有引用 `GameplayTag` 的资产。然而，这不会显示任何引用 `GameplayTag` 的 C++ 类。

重命名 `GameplayTags` 会创建一个重定向，以便仍引用原始 `GameplayTag` 的资产可以重定向到新的 `GameplayTag`。如果可能的话，我更倾向于创建一个新的 `GameplayTag`，手动更新所有引用到新的 `GameplayTag`，然后删除旧的 `GameplayTag`，以避免创建重定向。

除了 `Fast Replication` 之外，`GameplayTag` 编辑器还提供了一个选项，用于填充常见的复制 `GameplayTags` 以进一步优化它们。

如果 `GameplayTags` 是从 `GameplayEffect` 添加的，它们会被复制。`ASC` 允许你添加不复制的 `LooseGameplayTags`，这些标签必须手动管理。示例项目使用了一个 `LooseGameplayTag` 用于 `State.Dead`，以便拥有客户端可以立即响应其健康值降至零时的情况。重生会手动将 `TagMapCount` 重新设置为零。在处理 `LooseGameplayTags` 时，建议仅手动调整 `TagMapCount`。最好使用 `UAbilitySystemComponent::AddLooseGameplayTag()` 和 `UAbilitySystemComponent::RemoveLooseGameplayTag()` 函数，而不是手动调整 `TagMapCount`。

在 C++ 中获取 `GameplayTag` 的引用：

```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

对于高级的 `GameplayTag` 操作，如获取父级或子级 `GameplayTags`，请查看 `GameplayTagManager` 提供的函数。要访问 `GameplayTagManager`，请包含 `GameplayTagManager.h` 并通过 `UGameplayTagManager::Get().FunctionName` 调用它。`GameplayTagManager` 实际上将 `GameplayTags` 存储为关系节点（父级、子级等），这样比常量字符串操作和比较处理速度更快。

`GameplayTags` 和 `GameplayTagContainers` 可以具有可选的 `UPROPERTY` 指定符 `Meta = (Categories = "GameplayCue")`，它在 Blueprint 中过滤标签，仅显示具有 `GameplayCue` 父标签的 `GameplayTags`。当你知道 `GameplayTag` 或 `GameplayTagContainer` 变量应该仅用于 `GameplayCues` 时，这非常有用。

或者，还有一个名为 `FGameplayCueTag` 的独立结构，它封装了一个 `FGameplayTag`，并且在 Blueprint 中自动过滤 `GameplayTags`，只显示那些具有 `GameplayCue` 父标签的标签。

如果你想在函数中过滤 `GameplayTag` 参数，请使用 `UFUNCTION` 指定符 `Meta = (GameplayTagFilter = "GameplayCue")`。函数中的 `GameplayTagContainer` 参数无法过滤。如果你希望编辑引擎以允许此操作，请查看 `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp` 中的 `SGameplayTagGraphPin::ParseDefaultValueData()` 如何调用 `FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);`，并将 `FilterString` 传递给 `SGameplayTagWidget` 在 `SGameplayTagGraphPin::GetListContent()` 中。而在 `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp` 中的 `GameplayTagContainer` 版本的这些函数不会检查元字段属性并传递过滤器。

示例项目广泛使用了 `GameplayTags`。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-change"></a>
### 4.2.1 响应 Gameplay Tags 的变化
`ASC` 提供了一个委托，用于在 `GameplayTags` 被添加或移除时触发。它接收一个 `EGameplayTagEventType`，可以指定仅在 `GameplayTag` 被添加/移除时触发，或在 `GameplayTag` 的 `TagMapCount` 发生任何变化时触发。


```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

回调函数具有一个用于 `GameplayTag` 和新的 `TagCount` 的参数。

```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-loadfromplugin"></a>
### 4.2.2 从插件 .ini 文件加载 Gameplay Tags
如果你创建了一个带有 `GameplayTags` 的插件，并且它有自己的 .ini 文件，你可以在插件的 `StartupModule()` 函数中加载该插件的 `GameplayTag` .ini 目录。

例如，以下是 Unreal Engine 附带的 CommonConversation 插件的实现方式：


```c++
void FCommonConversationRuntimeModule::StartupModule()
{
	TSharedPtr<IPlugin> ThisPlugin = IPluginManager::Get().FindPlugin(TEXT("CommonConversation"));
	check(ThisPlugin.IsValid());
	
	UGameplayTagsManager::Get().AddTagIniSearchPath(ThisPlugin->GetBaseDir() / TEXT("Config") / TEXT("Tags"));

	//...
}
```

这将查找目录 `Plugins\CommonConversation\Config\Tags`，并在引擎启动时加载其中包含 `GameplayTags` 的任何 .ini 文件到你的项目中（前提是插件已启用）。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attributes

<a name="concepts-a-definition"></a>
#### 4.3.1  Attribute Definition
`Attributes` 是由结构体 [`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html) 定义的浮点值。这些值可以表示从角色的健康值到角色的等级，再到药水的使用次数等任何内容。如果它是属于某个 `Actor` 的与游戏玩法相关的数值，你应该考虑使用 `Attribute` 来表示它。`Attributes` 通常应仅由 [`GameplayEffects`](#concepts-ge) 修改，以便 ASC 可以预测这些变化。


`Attributes` 是由 [`AttributeSet`](#concepts-as) 定义并存在其中。`AttributeSet` 负责复制被标记为可复制的 `Attributes`。有关如何定义 `Attributes`，请参阅 [`AttributeSets`](#concepts-as) 部分。

**提示:** 如果你不希望某个 `Attribute` 显示在编辑器的 `Attributes` 列表中，你可以使用 `Meta = (HideInDetailsView)` 属性指定符。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 BaseValue vs CurrentValue
一个 `Attribute` 由两个值组成——`BaseValue` 和 `CurrentValue`。`BaseValue` 是 `Attribute` 的永久值，而 `CurrentValue` 是 `BaseValue` 加上来自 `GameplayEffects` 的临时修改。例如，你的 `Character` 可能有一个移动速度的 `Attribute`，其 `BaseValue` 为 600 单位/秒。由于还没有任何 `GameplayEffects` 修改移动速度，因此 `CurrentValue` 也是 600 u/s。如果她获得了一个临时的 50 u/s 的移动速度增益，`BaseValue` 保持不变，仍然是 600 u/s，而 `CurrentValue` 现在是 600 + 50，总共 650 u/s。当移动速度增益失效时，`CurrentValue` 会恢复为 600 u/s 的 `BaseValue`。

初学者经常会将 `BaseValue` 与 `Attribute` 的最大值混淆，并试图将其视为最大值。这是一种错误的做法。可以改变或在能力或 UI 中引用的 `Attributes` 的最大值应该被视为单独的 `Attributes`。对于硬编码的最大值和最小值，有一种方法可以定义一个带有 `FAttributeMetaData` 的 `DataTable`，它可以设置最大值和最小值，但 Epic 在该结构体上方的注释将其称为“正在进行的工作”。有关更多信息，请参见 `AttributeSet.h`。为了避免混淆，我建议将可以在能力或 UI 中引用的最大值作为单独的 `Attributes`，而仅用于限制 `Attributes` 的硬编码最大值和最小值则定义为 `AttributeSet` 中的硬编码浮点数。在 [PreAttributeChange()](#concepts-as-preattributechange) 中讨论了对 `CurrentValue` 的变化进行限制，而在 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute) 中讨论了来自 `GameplayEffects` 的 `BaseValue` 变化的限制。

对 `BaseValue` 的永久性变化来自 `Instant` `GameplayEffects`，而 `Duration` 和 `Infinite` `GameplayEffects` 则改变 `CurrentValue`。周期性 `GameplayEffects` 被视为即时 `GameplayEffects`，并改变 `BaseValue`。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 Meta Attributes
一些 `Attributes` 被视为用于与其他 `Attributes` 交互的临时值占位符，这些被称为 `Meta Attributes`（元属性）。例如，我们通常将伤害定义为一个 `Meta Attribute`。与其让一个 `GameplayEffect` 直接改变我们的健康 `Attribute`，我们使用一个称为伤害的 `Meta Attribute` 作为占位符。这样一来，伤害值可以在 [`GameplayEffectExecutionCalculation`](#concepts-ge-ec) 中通过增益和减益进行修改，并且可以在 `AttributeSet` 中进一步处理，例如先从当前的护盾 `Attribute` 中减去伤害，然后再从健康 `Attribute` 中减去剩余部分。伤害 `Meta Attribute` 不会在 `GameplayEffects` 之间保留，并且每次都会被覆盖。`Meta Attributes` 通常不复制。

`Meta Attributes` 为诸如伤害和治疗等内容提供了一个良好的逻辑分离——“我们造成了多少伤害？”和“我们如何处理这些伤害？”。这种逻辑分离意味着我们的 `Gameplay Effects` 和 `Execution Calculations` 不需要知道目标如何处理伤害。继续我们的伤害示例，`Gameplay Effect` 决定造成多少伤害，而 `AttributeSet` 决定如何处理这些伤害。并非所有角色都具有相同的 `Attributes`，尤其是当你使用子类化的 `AttributeSets` 时。基础 `AttributeSet` 类可能只有一个健康 `Attribute`，但子类化的 `AttributeSet` 可能会添加一个护盾 `Attribute`。具有护盾 `Attribute` 的子类化 `AttributeSet` 会与基础 `AttributeSet` 类不同地分配所收到的伤害。

虽然 `Meta Attributes` 是一个很好的设计模式，但它们并非强制性的。如果你只对所有伤害实例使用一个 `Execution Calculation`，并且所有角色共享一个 `Attribute Set` 类，那么你可以在 `Execution Calculation` 内部处理健康、护盾等的伤害分配，并直接修改这些 `Attributes`。你只会牺牲一些灵活性，但这对你来说可能是可以接受的。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 Responding to Attribute Changes
要监听 `Attribute` 的变化以更新 UI 或其他游戏玩法，可以使用 `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`。此函数返回一个委托，当 `Attribute` 发生变化时，该委托会自动被调用。委托提供一个 `FOnAttributeChangeData` 参数，其中包含 `NewValue`、`OldValue` 和 `FGameplayEffectModCallbackData`。**注意:** `FGameplayEffectModCallbackData` 仅在服务器上设置。


```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

示例项目在 `GDPlayerState` 上绑定了 `Attribute` 值变化的委托，以更新 HUD 并在健康值降至零时响应玩家死亡。

示例项目中包含一个自定义 Blueprint 节点，将此功能封装在 `ASyncTask` 中。它在 `UI_HUD` UMG Widget 中用于更新健康值、法力值和耐力值。这个 `AsyncTask` 会一直存在，直到手动调用 `EndTask()`，我们在 UMG Widget 的 `Destruct` 事件中执行了这个操作。参见 `AsyncTaskAttributeChanged.h/cpp`。


![Listen for Attribute Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-derived"></a>
#### 4.3.5 Derived Attributes
To make an `Attribute` that has some or all of its value derived from one or more other `Attributes`, use an `Infinite` `GameplayEffect` with one or more `Attribute Based` or [`MMC`](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods). The `Derived Attribute` will update automatically when an `Attribute` that it depends on is updated.

The final formula for all the `Modifiers` on a `Derived Attribute` is the same formula for `Modifier Aggregators`. If you need calculations to happen in a certain order, do it all inside of an `MMC`.

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**注意:** 如果在 PIE 中使用多个客户端进行游戏，你需要在编辑器偏好设置中禁用 `Run Under One Process`，否则当独立的 `Attributes` 在第一个客户端之外的其他客户端上更新时，`Derived Attributes` 将不会更新。

在此示例中，我们有一个 `Infinite` `GameplayEffect`，它根据 `Attributes` `TestAttrB` 和 `TestAttrC` 通过公式 `TestAttrA = (TestAttrA + TestAttrB) * (2 * TestAttrC)` 派生出 `TestAttrA` 的值。每当任何一个 `Attributes` 更新其值时，`TestAttrA` 会自动重新计算其值。


![Derived Attribute Example](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 Attribute Set

<a name="concepts-as-definition"></a>
#### 4.4.1 Attribute Set Definition
`AttributeSet` 定义、保存并管理对 `Attributes` 的更改。开发者应该从 [`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html) 继承子类。在 `OwnerActor` 的构造函数中创建一个 `AttributeSet` 会自动将其注册到 `ASC`。**这必须在 C++ 中完成**。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 Attribute Set Design
一个 `ASC` 可以有一个或多个 `AttributeSets`。`AttributeSets` 占用的内存非常小，因此使用多少个 `AttributeSets` 是一个由开发者决定的组织性选择。

可以接受的是，你可以拥有一个大型的单一 `AttributeSet`，该 `AttributeSet` 被游戏中的每个 `Actor` 共享，并且仅在需要时使用其中的属性，忽略未使用的属性。

或者，你可以选择拥有多个 `AttributeSet`，它们代表 `Attributes` 的分组，并根据需要选择性地添加到你的 `Actors` 中。例如，你可以拥有一个用于健康相关 `Attributes` 的 `AttributeSet`，一个用于法力相关 `Attributes` 的 `AttributeSet`，等等。在 MOBA 游戏中，英雄可能需要法力，而仆从可能不需要。因此，英雄会获得法力 `AttributeSet`，而仆从则不会。

此外，`AttributeSets` 可以被子类化，作为另一种选择性选择 `Actor` 具有哪些 `Attributes` 的方式。`Attributes` 在内部被称为 `AttributeSetClassName.AttributeName`。当你对子类化 `AttributeSet` 时，父类的所有 `Attributes` 仍然会以父类的名称作为前缀。

虽然你可以拥有多个 `AttributeSet`，但你不应该在一个 `ASC` 上拥有多个相同类的 `AttributeSet`。如果你有多个相同类的 `AttributeSet`，它将不知道该使用哪个 `AttributeSet`，并且只会随机选择一个。


<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 具有独立属性的子组件
在 `Pawn` 上有多个可损坏组件的情况下，例如单独可损坏的护甲部件，我建议，如果你知道 `Pawn` 可能拥有的最大可损坏组件数量，则可以在一个 `AttributeSet` 上创建多个健康 `Attributes`——如 DamageableCompHealth0、DamageableCompHealth1 等，用于表示这些可损坏组件的逻辑“槽位”。在你的可损坏组件类实例中，分配可以由 `GameplayAbilities` 或 [`Executions`](#concepts-ge-ec) 读取的槽位编号 `Attribute`，以确定应将伤害应用于哪个 `Attribute`。拥有少于最大数量或没有可损坏组件的 `Pawns` 也没有问题。仅仅因为一个 `AttributeSet` 有一个 `Attribute`，并不意味着你必须使用它。未使用的 `Attributes` 只占用极少的内存。

如果你的子组件每个都需要许多 `Attributes`，潜在的子组件数量是无限的，子组件可以分离并被其他玩家使用（例如武器），或者由于任何其他原因导致这种方法不适合你，我建议你放弃使用 `Attributes`，转而在组件上存储普通的浮点数。参见 [物品属性](#concepts-as-design-itemattributes)。


<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 在运行时添加和移除 AttributeSets
`AttributeSets` 可以在运行时从 `ASC` 中添加和移除；然而，移除 `AttributeSets` 可能是危险的。例如，如果在客户端上移除 `AttributeSet` 早于服务器，并且 `Attribute` 的值变化被复制到客户端，`Attribute` 将找不到它的 `AttributeSet`，这将导致游戏崩溃。

在武器添加到库存时：

```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

在武器从库存中移除时：
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```
<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 物品属性（武器弹药）
有几种方法可以使用 `Attributes` 实现可装备物品（武器弹药、护甲耐久度等）。所有这些方法都将值直接存储在物品上。这对于在其生命周期内可以被多个玩家装备的物品是必要的。

> 1. 在物品上使用普通浮点数（**推荐**）
> 1. 在物品上使用单独的 `AttributeSet`
> 1. 在物品上使用单独的 `ASC`


<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 在物品上使用普通浮点数
与使用 `Attributes` 相比，可以在物品类实例上存储普通的浮点值。Fortnite 和 [GASShooter](https://github.com/tranek/GASShooter) 就是这样处理枪支弹药的。对于一把枪，直接在枪实例上存储最大弹夹容量、当前弹夹中的弹药量、备用弹药等，并将其作为复制的浮点数（`COND_OwnerOnly`）。如果武器共享备用弹药，你可以将备用弹药作为一个 `Attribute` 移动到角色的共享弹药 `AttributeSet` 中（重新装填能力可以使用 `Cost GE` 将备用弹药从角色的 `AttributeSet` 中拉到枪支的浮点数弹夹弹药中）。由于你没有使用 `Attributes` 来表示当前的弹夹弹药，你将需要重写 `UGameplayAbility` 中的一些函数，以检查并将消耗应用于枪上的浮点数。当授予能力时，将枪设置为 [`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec) 中的 `SourceObject`，这样你就可以在能力中访问授予能力的枪。

为了防止枪在自动射击期间复制弹药量并覆盖本地弹药量，可以在玩家拥有 `IsFiring` `GameplayTag` 时，在 `PreReplication()` 中禁用复制。本质上，你是在这里进行自己的本地预测。


```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

优点：
1. 避免了使用 `AttributeSets` 的限制（见下文）

限制：
1. 无法使用现有的 `GameplayEffect` 工作流程（如弹药使用的 `Cost GEs` 等）
1. 需要额外工作来重写 `UGameplayAbility` 上的关键函数，以检查并将弹药消耗应用于枪支的浮点数


<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 在物品上使用 `AttributeSet`
在物品上使用一个单独的 `AttributeSet`，并在将其添加到玩家库存时将其[添加到玩家的 `ASC`](#concepts-as-design-addremoveruntime) 是可行的，但它有一些主要的限制。我在 [GASShooter](https://github.com/tranek/GASShooter) 的早期版本中为武器弹药实现了这种方式。武器将其 `Attributes`（如最大弹夹容量、当前弹夹中的弹药量、备用弹药等）存储在一个属于武器类的 `AttributeSet` 中。如果武器共享备用弹药，你可以将备用弹药移动到角色的共享弹药 `AttributeSet` 中。当武器在服务器上被添加到玩家库存时，武器会将其 `AttributeSet` 添加到玩家的 `ASC::SpawnedAttributes` 中。然后服务器会将此信息复制到客户端。如果武器从库存中移除，它将从 `ASC::SpawnedAttributes` 中移除其 `AttributeSet`。

当 `AttributeSet` 存在于 `OwnerActor` 以外的其他地方（例如武器）时，最初你会在 `AttributeSet` 中遇到一些编译错误。解决方法是在 `BeginPlay()` 中而不是在构造函数中构建 `AttributeSet`，并在武器上实现 `IAbilitySystemInterface`（在将武器添加到玩家库存时设置指向 `ASC` 的指针）。


```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

你可以通过查看这个示例来看到它的实际应用
 [older version of GASShooter](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735).

优点：
1. 可以使用现有的 `GameplayAbility` 和 `GameplayEffect` 工作流程（如弹药使用的 `Cost GEs` 等）
1. 对于非常小的一组物品来说，设置简单

限制：
1. 你必须为每种武器类型创建一个新的 `AttributeSet` 类。由于 `ASC` 只能在功能上拥有一个 `AttributeSet` 类的实例，因为对 `Attribute` 的更改会在 `ASC` 的 `SpawnedAttributes` 数组中查找其 `AttributeSet` 类的第一个实例，因此相同 `AttributeSet` 类的其他实例会被忽略。
1. 由于每个 `AttributeSet` 类只能有一个 `AttributeSet` 实例，因此玩家的库存中每种类型的武器只能有一个。
1. 移除 `AttributeSet` 是危险的。在 GASShooter 中，如果玩家通过火箭自杀，玩家会立即从其库存中移除火箭发射器（包括从 `ASC` 中移除其 `AttributeSet`）。当服务器复制火箭发射器的弹药 `Attribute` 发生变化时，客户端的 `ASC` 上不再存在该 `AttributeSet`，导致游戏崩溃。


<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 在物品上使用 `ASC`
在每个物品上放置一个完整的 `AbilitySystemComponent` 是一种极端的方法。我个人没有这样做过，也没有在实际中见过。要使其正常工作可能需要大量的工程设计。

> 是否可行拥有多个相同所有者但不同化身的 AbilitySystemComponents（例如，在玩家和武器/物品/投射物上，所有者设置为 PlayerState）？
>
> 我首先看到的问题是需要在拥有的 actor 上实现 IGameplayTagAssetInterface 和 IAbilitySystemInterface。前者可能是可行的：只需从所有 ASC 聚合标签（但请注意 -HasAllMatchingGameplayTags 可能仅通过跨 ASC 聚合来满足。仅将这些调用转发给每个 ASC 并将结果 OR 在一起可能是不够的）。但后者更加棘手：哪个 ASC 是权威的？如果有人想应用 GE - 应该由哪个接收？也许你可以解决这些问题，但问题的这一方面将是最难的：所有者将有多个 ASC。
>
> 不过，在玩家和武器上使用单独的 ASC 是合理的。例如，区分描述武器的标签与描述玩家的标签。也许武器授予的标签也“应用”于所有者并且不涉及其他内容（例如，属性和 GE 是独立的，但所有者会聚合如上所述的已拥有的标签）。我确信这可能有效。但是，拥有多个相同所有者的 ASC 可能会变得棘手。

*Epic 的 Dave Ratti 对 [社区问题#6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) 的回答*

优点：
1. 可以使用现有的 `GameplayAbility` 和 `GameplayEffect` 工作流程（如弹药使用的 `Cost GEs` 等）
1. 可以重用 `AttributeSet` 类（每个武器的 ASC 上都有一个）

限制：
1. 未知的工程成本
1. 这是否真的可行？

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 Defining Attributes
**`Attributes` 只能在 C++ 中定义**，并且需要在 `AttributeSet` 的头文件中定义。建议在每个 `AttributeSet` 头文件的顶部添加以下宏块。这将自动为你的 `Attributes` 生成 getter 和 setter 函数。


```c++
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

A replicated health attribute would be defined like this:
一个复制的health属性定义如下：

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

还需要在头文件中定义 `OnRep` 函数：
```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

在 `AttributeSet` 的 .cpp 文件中，`OnRep` 函数应使用预测系统使用的 `GAMEPLAYATTRIBUTE_REPNOTIFY` 宏来填充：

```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

最后，需要将 `Attribute` 添加到 `GetLifetimeReplicatedProps` 中：

```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always` 指示 `OnRep` 函数在本地值已经等于从服务器复制下来的值时触发（由于预测的原因）。默认情况下，如果本地值与从服务器复制下来的值相同，则不会触发 `OnRep` 函数。

如果 `Attribute` 不进行复制，比如 `Meta Attribute`，那么可以跳过 `OnRep` 和 `GetLifetimeReplicatedProps` 的步骤。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 Initializing Attributes
有多种方法可以初始化 `Attributes`（将其 `BaseValue` 和相应的 `CurrentValue` 设置为某个初始值）。Epic 推荐使用即时 `GameplayEffect`。这也是示例项目中使用的方法。

参见示例项目中的 `GE_HeroAttributes` Blueprint，了解如何制作一个用于初始化 `Attributes` 的即时 `GameplayEffect`。此 `GameplayEffect` 的应用在 C++ 中进行。

如果在定义 `Attributes` 时使用了 `ATTRIBUTE_ACCESSORS` 宏，那么将在 `AttributeSet` 上为每个 `Attribute` 自动生成一个初始化函数，您可以在 C++ 中随时调用它。


```c++
// `InitHealth(float InitialValue)` 是为使用 `ATTRIBUTE_ACCESSORS` 宏定义的 `Health` 属性自动生成的函数。

AttributeSet->InitHealth(100.0f);
```

有关初始化 `Attributes` 的更多方法，请参见 `AttributeSet.h`。

**注意:** 在 4.24 之前，`FAttributeSetInitterDiscreteLevels` 无法与 `FGameplayAttributeData` 一起使用。它是在 `Attributes` 仍是原始浮点数时创建的，并且会抱怨 `FGameplayAttributeData` 不是“普通旧数据”(`POD`)。此问题已在 4.24 中修复 [https://issues.unrealengine.com/issue/UE-76557](https://issues.unrealengine.com/issue/UE-76557)。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)` 是 `AttributeSet` 中响应 `Attribute` 的 `CurrentValue` 变化的主要函数之一，且是在变化发生之前进行响应的理想位置。可以通过引用参数 `NewValue` 来限制传入的 `CurrentValue` 变化。

例如，为了限制移动速度的修改，示例项目是这样做的：

```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// Cannot slow less than 150 units/s and cannot boost more than 1000 units/s
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
`GetMoveSpeedAttribute()` 函数是由我们添加到 `AttributeSet.h` 中的宏块创建的（[定义属性](#concepts-as-attributes)）。

无论是使用 `Attribute` 设置器（由 `AttributeSet.h` 中的宏块定义的）（[定义属性](#concepts-as-attributes)），还是使用 [`GameplayEffects`](#concepts-ge)，任何对 `Attributes` 的更改都会触发此函数。

**注意:** 这里发生的任何限制不会永久更改 `ASC` 上的修饰符。它只会更改查询修饰符时返回的值。这意味着任何从所有修饰符重新计算 `CurrentValue` 的操作，如 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 和 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc)，都需要再次实现限制。

**注意:** Epic 对 `PreAttributeChange()` 的注释表明不应将其用于游戏事件，而应主要用于限制。Epic 推荐在 `Attribute` 更改时进行游戏事件处理的地方是 `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`（[响应属性更改](#concepts-a-changes)）。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)` 仅在 `instant` [`GameplayEffect`](#concepts-ge) 影响 `Attribute` 的 `BaseValue` 之后触发。这是当 `Attribute` 由于 `GameplayEffect` 发生变化时，进行更多 `Attribute` 操作的有效位置。

例如，在示例项目中，我们在这里从健康 `Attribute` 中减去最终的伤害 `Meta Attribute`。如果有一个盾牌 `Attribute`，我们会先从盾牌中减去伤害，然后再从健康中减去剩余部分。示例项目还使用此位置来应用命中反应动画、显示浮动的伤害数字，以及向击杀者分配经验和金币赏金。按照设计，伤害 `Meta Attribute` 始终通过 `instant` `GameplayEffect` 传递，而不会通过 `Attribute` 设置器。

其他仅由 `instant` `GameplayEffects` 更改 `BaseValue` 的 `Attributes`（如法力和耐力）也可以在这里限制其最大值对应的 `Attributes`。

**注意:** 当 `PostGameplayEffectExecute()` 被调用时，对 `Attribute` 的更改已经发生，但尚未复制回客户端，因此在此处限制值不会导致对客户端的两次网络更新。客户端只会在限制后收到更新。



**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)` 在为此集合中的 `Attribute` 创建 `Aggregator` 时触发。它允许自定义设置 [`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html)。`AggregatorEvaluateMetaData` 被 `Aggregator` 用于基于应用于 `Attribute` 的所有 [`Modifiers`](#concepts-ge-mods) 来评估 `CurrentValue`。默认情况下，`AggregatorEvaluateMetaData` 仅由 `Aggregator` 用于确定哪些 `Modifiers` 符合条件，例如 `MostNegativeMod_AllPositiveMods` 允许所有正向 `Modifiers`，但将负向 `Modifiers` 限制为最负的一个。这在 Paragon 中使用，用于仅允许最负的移动速度减缓效果应用于玩家，而不管他们同时受到多少个减缓效果，同时应用所有正向的移动速度增益。那些不符合条件的 `Modifiers` 仍然存在于 `ASC` 上，只是它们不会被聚合到最终的 `CurrentValue` 中。一旦条件发生变化，比如最负的 `Modifier` 过期，下一个最负的 `Modifier`（如果存在）就会符合条件。

在只允许最负的 `Modifier` 和所有正向 `Modifiers` 的示例中使用 `AggregatorEvaluateMetaData`：


```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

你的自定义 `AggregatorEvaluateMetaData` 资格筛选器应作为静态变量添加到 `FAggregatorEvaluateMetaDataLibrary` 中。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 Gameplay Effects

<a name="concepts-ge-definition"></a>
#### 4.5.1 Gameplay Effect Definition
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html) (`GE`) 是能力通过其改变自身或他人 [`Attributes`](#concepts-a) 和 [`GameplayTags`](#concepts-gt) 的载体。它们可以立即改变 `Attribute`，例如造成伤害或治疗，或施加长期的状态增益/减益效果，例如移动速度提升或眩晕。`UGameplayEffect` 类旨在成为一个**仅数据**类，用于定义单一的游戏效果。不要向 `GameplayEffects` 添加额外的逻辑。通常，设计师会创建许多 `UGameplayEffect` 的 Blueprint 子类。

`GameplayEffects` 通过 [`Modifiers`](#concepts-ge-mods) 和 [`Executions` (`GameplayEffectExecutionCalculation`)](#concepts-ge-ec) 来改变 `Attributes`。

`GameplayEffects` 有三种持续时间类型：`Instant`、`Duration` 和 `Infinite`。

此外，`GameplayEffects` 可以添加/执行 [`GameplayCues`](#concepts-gc)。`Instant` `GameplayEffect` 将在 `GameplayCue` `GameplayTags` 上调用 `Execute`，而 `Duration` 或 `Infinite` `GameplayEffect` 将在 `GameplayCue` `GameplayTags` 上调用 `Add` 和 `Remove`。

| Duration Type | GameplayCue Event | When to use                                                                                                                                                                                                                                |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant`     | Execute           | 用于对 `Attribute` 的 `BaseValue` 进行立即的永久更改。`GameplayTags` 不会被应用，甚至不会应用一帧。                                                                                                                    |
| `Duration`    | Add & Remove      | 用于暂时更改 `Attribute` 的 `CurrentValue` 并应用 `GameplayTags`，这些 `GameplayTags` 会在 `GameplayEffect` 到期或手动移除时被移除。持续时间在 `UGameplayEffect` 类/Blueprint 中指定。       |
| `Infinite`    | Add & Remove      | 用于暂时更改 `Attribute` 的 `CurrentValue` 并应用 `GameplayTags`，这些 `GameplayTags` 会在 `GameplayEffect` 被移除时被移除。它们永远不会自行到期，必须通过某个能力或 `ASC` 手动移除。 |

`Duration` 和 `Infinite` `GameplayEffects` 可以选择应用 `Periodic Effects`，该效果每 `X` 秒应用其 `Modifiers` 和 `Executions`，如其 `Period` 所定义的那样。`Periodic Effects` 在改变 `Attribute` 的 `BaseValue` 和 `Executing` `GameplayCues` 时被视为 `Instant` `GameplayEffects`。这些效果对持续伤害（DOT）类型的效果非常有用。**注意:** `Periodic Effects` 不能被 [预测](#concepts-p)。

`Duration` 和 `Infinite` `GameplayEffects` 在应用后如果不满足/满足其 `Ongoing Tag Requirements`，可以暂时关闭和重新开启。关闭 `GameplayEffect` 会移除其 `Modifiers` 和应用的 `GameplayTags` 的效果，但不会移除 `GameplayEffect`。重新开启 `GameplayEffect` 会重新应用其 `Modifiers` 和 `GameplayTags`。

如果你需要手动重新计算 `Duration` 或 `Infinite` `GameplayEffect` 的 `Modifiers`（例如你有一个 `MMC` 使用的数据不来自 `Attributes`），你可以使用 `UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)` 并使用 `UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()` 设置其已有的相同等级。基于支持 `Attributes` 的 `Modifiers` 会在那些支持的 `Attributes` 更新时自动更新。`SetActiveGameplayEffectLevel()` 的关键函数用于更新 `Modifiers`：


```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// Private function otherwise we'd call these three functions without needing to set the level to what it already is
UpdateAllAggregatorModMagnitudes(Effect);
```
`GameplayEffects` 通常不会被实例化。当一个能力或 `ASC` 想要应用一个 `GameplayEffect` 时，它会从 `GameplayEffect` 的 `ClassDefaultObject` 创建一个 [`GameplayEffectSpec`](#concepts-ge-spec)。成功应用的 `GameplayEffectSpecs` 然后会被添加到一个名为 `FActiveGameplayEffect` 的新结构中，这个结构是 `ASC` 在一个名为 `ActiveGameplayEffects` 的特殊容器结构中跟踪的内容。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-applying"></a>
#### 4.5.2 Applying Gameplay Effects
`GameplayEffects` 可以通过 [`GameplayAbilities`](#concepts-ga) 上的函数和 `ASC` 上的函数以多种方式应用，通常采用 `ApplyGameplayEffectTo` 的形式。这些不同的函数实际上是一些便利函数，最终都会调用 `UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()` 来对 `Target` 进行操作。

要在 `GameplayAbility` 之外应用 `GameplayEffects`，例如从一个投射物中，你需要获取 `Target` 的 `ASC` 并使用其中的一个函数来 `ApplyGameplayEffectToSelf`。

你可以通过绑定到 `ASC` 的委托来监听任何 `Duration` 或 `Infinite` `GameplayEffects` 何时被应用：

```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
The callback function:
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

服务器将始终调用此函数，无论复制模式如何。自主代理只会在 `Full` 和 `Mixed` 复制模式下为复制的 `GameplayEffects` 调用此函数。模拟代理只会在 `Full` [复制模式](#concepts-asc-rm) 下调用此函数。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-removing"></a>
#### 4.5.3 Removing Gameplay Effects
`GameplayEffects` 可以通过 [`GameplayAbilities`](#concepts-ga) 上的函数和 `ASC` 上的函数以多种方式移除，通常采用 `RemoveActiveGameplayEffect` 的形式。这些不同的函数实际上是一些便利函数，最终都会调用 `FActiveGameplayEffectsContainer::RemoveActiveEffects()` 来对 `Target` 进行操作。

要在 `GameplayAbility` 之外移除 `GameplayEffects`，你需要获取 `Target` 的 `ASC` 并使用其中的一个函数来 `RemoveActiveGameplayEffect`。

你可以通过绑定到 `ASC` 的委托来监听任何 `Duration` 或 `Infinite` `GameplayEffects` 何时从 `ASC` 中被移除：

```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
The callback function:
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

服务器将始终调用此函数，无论复制模式如何。自主代理只会在 `Full` 和 `Mixed` 复制模式下为复制的 `GameplayEffects` 调用此函数。模拟代理只会在 `Full` [复制模式](#concepts-asc-rm) 下调用此函数。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 Gameplay Effect Modifiers
`Modifiers` 会改变一个 `Attribute`，并且是 [预测性](#concepts-p) 改变 `Attribute` 的唯一方式。一个 `GameplayEffect` 可以有零个或多个 `Modifier`。每个 `Modifier` 负责通过指定的操作改变一个 `Attribute`。


| Operation  | Description                                                                                                         |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| `Add`      | 将结果添加到 `Modifier` 指定的 `Attribute` 中。使用负值进行减法。                     |
| `Multiply` | 将结果乘以 `Modifier` 指定的 `Attribute`。                                     |
| `Divide`   | 将结果除以 `Modifier` 指定的 `Attribute`。                                     |
| `Override` | 用结果覆盖 `Modifier` 指定的 `Attribute`。                                   |


`Attribute` 的 `CurrentValue` 是所有 `Modifiers` 加到其 `BaseValue` 上的聚合结果。`Modifiers` 是按照 `GameplayEffectAggregator.cpp` 中 `FAggregatorModChannel::EvaluateWithBase` 函数定义的公式进行聚合的。
:
```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

任何 `Override` 类型的 `Modifiers` 都会用最后应用的 `Modifier` 覆盖最终值，并且最后应用的 `Modifier` 优先级最高。


**注意：** 对于基于百分比的更改，请确保使用 `Multiply` 操作，以便它在加法之后发生。

**注意：** [预测](#concepts-p) 对于百分比变化存在问题。

有四种类型的 `Modifiers`：可缩放浮点（Scalable Float）、基于属性（Attribute Based）、自定义计算类（Custom Calculation Class）和调用者设置（Set By Caller）。它们都生成一些浮点值，然后根据其操作用于更改 `Modifier` 指定的 `Attribute`。


| `Modifier` Type            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Scalable Float`           | `FScalableFloats` 是一种结构，可以指向数据表，将变量作为行、级别作为列。可缩放浮点数将自动读取在能力当前级别（或在 [`GameplayEffectSpec`](#concepts-ge-spec) 中被覆盖的级别）指定表行的值。该值可以通过一个系数进一步调整。如果未指定数据表/行，它会将值视为 1，因此可以使用系数在所有级别硬编码一个单一值。 ![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png) |
| `Attribute Based`          | `Attribute Based` `Modifiers` 使用在 `Source`（创建 `GameplayEffectSpec` 的对象）或 `Target`（接收 `GameplayEffectSpec` 的对象）上支持的 `Attribute` 的 `CurrentValue` 或 `BaseValue`，并通过系数和系数之前和之后的加法进一步修改它。`Snapshotting` 意味着在创建 `GameplayEffectSpec` 时捕获支持的 `Attribute`，而没有快照意味着在应用 `GameplayEffectSpec` 时捕获 `Attribute`。 |
| `Custom Calculation Class` | `Custom Calculation Class` 为复杂的 `Modifiers` 提供了最大的灵活性。此 `Modifier` 采用 [`ModifierMagnitudeCalculation`](#concepts-ge-mmc) 类，并可以通过系数以及系数之前和之后的加法进一步操纵生成的浮点值。 |
| `Set By Caller`            | `SetByCaller` `Modifiers` 是在运行时由能力或创建 `GameplayEffectSpec` 的对象在 `GameplayEffectSpec` 上设置的值。例如，如果你希望根据玩家按住按钮的时间长短来设置伤害值，你可以使用 `SetByCaller`。`SetByCallers` 实质上是存在于 `GameplayEffectSpec` 上的 `TMap<FGameplayTag, float>`。`Modifier` 只是告诉 `Aggregator` 查找与提供的 `GameplayTag` 相关联的 `SetByCaller` 值。如果在 `GameplayEffectSpec` 上没有正确的 `GameplayTag` 的 `SetByCaller`，游戏将抛出运行时错误并返回值 0。这在 `Divide` 操作中可能会引发问题。有关如何使用 `SetByCallers` 的更多信息，请参阅 [`SetByCallers`](#concepts-ge-spec-setbycaller)。 |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 Multiply and Divide Modifiers
默认情况下，所有的 `Multiply` 和 `Divide` `Modifiers` 在乘以或除以 `Attribute` 的 `BaseValue` 之前都会先相加。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```
*摘自 `GameplayEffectAggregator.cpp`*

在这个公式中，`Multiply` 和 `Divide` `Modifiers` 的 `Bias` 值都是 `1`（`Addition` 的 `Bias` 值为 `0`）。所以它看起来会像这样：
:

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

这个公式会导致一些意想不到的结果。首先，这个公式在将修正值（modifiers）与 `BaseValue` 相乘或相除之前，会先将它们相加。大多数人可能会期望它们是先相乘或相除。例如，如果你有两个 `Multiply` 修正值为 `1.5`，大多数人可能会期望 `BaseValue` 被乘以 `1.5 x 1.5 = 2.25`。但实际上，它会将两个 `1.5` 相加，然后乘以 `BaseValue`，得到的结果是 `2`（`50% 增加 + 再一个 50% 增加 = 100% 增加`）。这就像 `GameplayPrediction.h` 中的一个示例，假设 `500` 的基础速度有一个 `10%` 的速度增益，最终结果是 `550`。再增加一个 `10%` 的速度增益，结果会变成 `600`。

其次，这个公式有一些未记录的规则，这些规则是在设计 Paragon 游戏时定下的，影响了 `Multiply` 和 `Divide` 修正值的相加公式。

`Multiply` 和 `Divide` 修正值的相加公式规则如下：
:
* `(No more than one value < 1) AND (Any number of values [1, 2))`
* `OR (One value >= 2)`

公式中的 `Bias` 基本上会减去 `[1, 2)` 范围内数字的整数部分。第一个 `Modifier` 的 `Bias` 会从开始的 `Sum` 值（在循环之前设置为 `Bias`）中减去，因此任何单个数值都能正常工作，并且单个值 `< 1` 也会在 `[1, 2)` 范围内正常工作。

以下是一些使用 `Multiply` 的示例：
- 修正值: `0.5`  
  `1 + (0.5 - 1) = 0.5`，正确

- 修正值: `0.5, 0.5`  
  `1 + (0.5 - 1) + (0.5 - 1) = 0`，错误，预期为 `1`？多个小于 `1` 的值在乘法修正中没有意义。Paragon 只会使用[`最大负值的修正值`](#cae-nonstackingge)，所以最多只有一个小于 `1` 的值会被用于乘以 `BaseValue`。

- 修正值: `1.1, 0.5`  
  `1 + (0.5 - 1) + (1.1 - 1) = 0.6`，正确

- 修正值: `5, 5`  
  `1 + (5 - 1) + (5 - 1) = 9`，错误，预期为 `10`。最终结果总是 `修正值的和 - 修正值的数量 + 1`。

许多游戏希望他们的 `Multiply` 和 `Divide` 修正值在应用到 `BaseValue` 之前可以一起进行乘除运算。要实现这一点，你需要**更改引擎代码**以修改 `FAggregatorModChannel::EvaluateWithBase()` 的逻辑。


```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	float Division = MultiplyMods(Mods[EGameplayModOp::Division], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 Gameplay Tags on Modifiers

`SourceTags` 和 `TargetTags` 可以为每个 `Modifier` 设置。它们的工作方式与 `GameplayEffect` 的 `Application Tag requirements` 类似。所以这些标签只在效果首次应用时被考虑。例如，对于周期性、无限的效果，它们只在效果首次应用时被考虑，但**不会**在每次周期执行时被考虑。

`Attribute Based` `Modifiers` 还可以设置 `SourceTagFilter` 和 `TargetTagFilter`。在确定作为 `Attribute Based` `Modifier` 源的属性的量值时，这些过滤器会用于排除某些对该属性的 `Modifiers`。那些源或目标不具备过滤器中所有标签的 `Modifiers` 会被排除。

详细来说：`GameplayEffects` 会捕获来源 `ASC` 和目标 `ASC` 的标签。来源 `ASC` 的标签在创建 `GameplayEffectSpec` 时捕获，目标 `ASC` 的标签在效果执行时捕获。当确定无限或持续效果的 `Modifier` 是否"符合条件"（即其 Aggregator 是否符合条件）且设置了这些过滤器时，捕获的标签会与过滤器进行比较。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 4.5.5 Stacking Gameplay Effects
`GameplayEffects` 默认情况下会应用新的 `GameplayEffectSpec` 实例，而不关心或考虑之前已经存在的 `GameplayEffectSpec` 实例。`GameplayEffects` 可以设置为叠加，这样在应用时不会添加新的 `GameplayEffectSpec` 实例，而是改变当前已有的 `GameplayEffectSpec` 的叠加次数。叠加仅适用于 `Duration` 和 `Infinite` 类型的 `GameplayEffects`。

叠加有两种类型：按来源聚合（Aggregate by Source）和按目标聚合（Aggregate by Target）。


| Stacking Type       | 描述                                                                                                                           |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Aggregate by Source | 每个 Source ASC 在 Target 上都有一个单独的堆叠实例。每个 Source 可以应用 X 数量的堆叠              |
| Aggregate by Target | 无论 Source 是什么，Target 上只有一个堆叠实例。每个 Source 可以应用一个堆叠，直到达到共享的堆叠上限。 |

堆叠还具有到期、持续时间刷新和周期重置的策略。在 `GameplayEffect` Blueprint 中，它们有有用的悬停提示。

示例项目包含一个自定义的 Blueprint 节点，用于监听 `GameplayEffect` 堆叠变化。HUD UMG 小部件使用它来更新玩家拥有的被动护甲堆叠数量。这个 `AsyncTask` 将一直存在，直到手动调用 `EndTask()`，我们在 UMG 小部件的 `Destruct` 事件中执行此操作。有关详细信息，请参阅 `AsyncTaskEffectStackChanged.h/cpp`。


![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 4.5.6 Granted Abilities
`GameplayEffects` 可以为 `ASCs` 授予新的 [`GameplayAbilities`](#concepts-ga)。只有 `Duration` 和 `Infinite` 类型的 `GameplayEffects` 能够授予能力。

一个常见的用例是，当你想强制另一名玩家执行某个动作，比如将他们从击退或拉动中移动时。你可以对他们应用一个 `GameplayEffect`，该效果会授予他们一个自动激活的能力（有关如何在授予能力时自动激活能力，请参阅 [Passive Abilities](#concepts-ga-activating-passive)），该能力会对他们执行所需的动作。

设计人员可以选择 `GameplayEffect` 授予的能力，授予它们的等级，绑定的 [输入](#concepts-ga-input) 以及授予能力的移除策略。


| Removal Policy             | Description                                                                                                                                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cancel Ability Immediately | 被授予的能力会在赋予它的 GameplayEffect 从目标身上移除时立即取消并移除。                          |
| Remove Ability on End      | 被授予的能力被允许完成，然后从目标身上移除。                                                                        |
| Do Nothing                 | 被授予的能力不受授予该能力的 GameplayEffect 从目标身上移除的影响。该目标将永久拥有此能力，直到它被手动移除为止。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 4.5.7 Gameplay Effect Tags
`GameplayEffects` 携带多个 [`GameplayTagContainers`](#concepts-gt)。设计人员将编辑每个类别的 `Added` 和 `Removed` `GameplayTagContainers`，并且结果将在编译时显示在 `Combined` `GameplayTagContainer` 中。`Added` 标签是此 `GameplayEffect` 添加的新标签，其父类先前没有这些标签。`Removed` 标签是父类具有但此子类不再包含的标签。


| Category                          | Description                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Gameplay Effect Asset Tags        | `GameplayEffect` 拥有的标签。它们本身不执行任何功能，仅用于描述 `GameplayEffect`。                                                                                                                                                                                                                |
| Granted Tags                      | 存在于 `GameplayEffect` 上的标签，同时也赋予给应用了该 `GameplayEffect` 的 `ASC`。当该 `GameplayEffect` 被移除时，这些标签也会从 `ASC` 中移除。这仅适用于 `Duration` 和 `Infinite` 类型的 `GameplayEffects`。                                                                                                                                                                                                                                                        |
| Ongoing Tag Requirements          |  一旦应用，这些标签决定了 `GameplayEffect` 是启用还是禁用。一个 `GameplayEffect` 即使被禁用也可以应用。如果由于不满足持续标签要求而使 `GameplayEffect` 被禁用，但随后这些要求得到了满足，`GameplayEffect` 将再次启用并重新应用其修饰符。这仅适用于 `Duration` 和 `Infinite` 类型的 `GameplayEffects`。 | 
| Application Tag Requirements      | 决定一个 `GameplayEffect` 是否可以应用于目标的标签。如果这些要求未被满足，该 `GameplayEffect` 将不会被应用。                                                                                                                                                                                                   |
| Remove Gameplay Effects with Tags | 目标上拥有这些标签中的任何一个的 `GameplayEffects`，如果这些标签在它们的 `Asset Tags` 或 `Granted Tags` 中，则当此 `GameplayEffect` 成功应用时，这些 `GameplayEffects` 将被从目标中移除.                                                                                                                                                                                            |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 Immunity
`GameplayEffects` 可以基于 [`GameplayTags`](#concepts-gt) 授予免疫效果，有效地阻止其他 `GameplayEffects` 的应用。虽然免疫效果可以通过其他方式实现，比如 `Application Tag Requirements`，但使用此系统可以提供一个委托 `UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`，当由于免疫阻止 `GameplayEffects` 时触发。

`GrantedApplicationImmunityTags` 检查来源 `ASC`（包括来源能力的 `AbilityTags`，如果有的话）是否具有任何指定的标签。这是一种根据标签为来自特定角色或来源的所有 `GameplayEffects` 提供免疫的方法。

`Granted Application Immunity Query` 检查传入的 `GameplayEffectSpec` 是否符合任何查询条件，以阻止或允许其应用。

这些查询在 `GameplayEffect` Blueprint 中有有用的悬停提示。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 Gameplay Effect Spec
[`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html) (`GESpec`) 可以被看作是 `GameplayEffects` 的实例化。它们持有对其代表的 `GameplayEffect` 类的引用、它们创建时的等级以及创建它们的对象。与 `GameplayEffects` 不同，`GameplayEffectSpecs` 可以在运行时自由创建和修改，而 `GameplayEffects` 应该由设计师在运行前创建。在应用 `GameplayEffect` 时，`GameplayEffectSpec` 是从 `GameplayEffect` 中创建的，实际应用到目标上的也是它。

`GameplayEffectSpecs` 通过 `UAbilitySystemComponent::MakeOutgoingSpec()` 从 `GameplayEffects` 中创建，该函数是 `BlueprintCallable` 的。`GameplayEffectSpecs` 不需要立即应用。通常会将 `GameplayEffectSpec` 传递给由能力创建的投射物，然后投射物可以在击中目标时将其应用。当 `GameplayEffectSpecs` 成功应用时，会返回一个新的结构体，称为 `FActiveGameplayEffect`。


Notable `GameplayEffectSpec` 内容:
* 该 `GameplayEffectSpec` 所创建自的 `GameplayEffect` 类。
* 该 `GameplayEffectSpec` 的等级。通常与创建 `GameplayEffectSpec` 的能力的等级相同，但可以不同。
* 该 `GameplayEffectSpec` 的持续时间。默认情况下与 `GameplayEffect` 的持续时间相同，但可以不同。
* 该 `GameplayEffectSpec` 的周期时间（适用于周期性效果）。默认情况下与 `GameplayEffect` 的周期时间相同，但可以不同。
* 该 `GameplayEffectSpec` 当前的堆叠计数。堆叠限制在 `GameplayEffect` 上。
* [`GameplayEffectContextHandle`](#concepts-ge-context) 告诉我们谁创建了该 `GameplayEffectSpec`。
* 由于快照机制，在 `GameplayEffectSpec` 创建时捕获的 `Attributes`。
* `DynamicGrantedTags` 是该 `GameplayEffectSpec` 授予目标的动态标签，除此之外还有 `GameplayEffect` 授予的 `GameplayTags`。
* `DynamicAssetTags` 是该 `GameplayEffectSpec` 附带的动态资产标签，除此之外还有 `GameplayEffect` 附带的 `AssetTags`。
* `SetByCaller` 的 `TMaps`。
**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCallers
`SetByCallers` 允许 `GameplayEffectSpec` 携带与 `GameplayTag` 或 `FName` 关联的浮点值。它们分别存储在 `GameplayEffectSpec` 上的 `TMap<FGameplayTag, float>` 和 `TMap<FName, float>` 中。这些值可以作为 `GameplayEffect` 的 `Modifiers` 使用，或者作为传递浮点值的一种通用方式。通常情况下，可以通过 `SetByCallers` 将在能力内生成的数值数据传递给 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 或 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc)。

| `SetByCaller` 使用 | 备注                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`        | 必须提前在 `GameplayEffect` 类中定义。只能使用 `GameplayTag` 版本。如果在 `GameplayEffect` 类中定义了一个，但 `GameplayEffectSpec` 没有相应的标签和浮点值对，游戏在应用 `GameplayEffectSpec` 时会发生运行时错误，并返回 0。这对 `Divide` 操作来说是一个潜在的问题。参见 [`Modifiers`](#concepts-ge-mods)。 |
| Elsewhere                    | 不需要提前定义。读取 `GameplayEffectSpec` 上不存在的 `SetByCaller` 时，可以返回开发人员定义的默认值，并可选择显示警告。                                                                                                                                                                                                                                        |

要在 Blueprint 中分配 `SetByCaller` 值，请使用所需版本的 Blueprint 节点（`GameplayTag` 或 `FName`）：

![Assigning SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

要在 Blueprint 中读取 `SetByCaller` 值，您需要在 Blueprint 库中创建自定义节点。

要在 C++ 中分配 `SetByCaller` 值，请使用您需要的函数版本（`GameplayTag` 或 `FName`）：


```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

要在 C++ 中读取 `SetByCaller` 值，请使用所需版本的函数（`GameplayTag` 或 `FName`）：


```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

我建议使用 `GameplayTag` 版本而不是 `FName` 版本。这可以防止在 Blueprint 中出现拼写错误。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 Gameplay Effect Context
[`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html) 结构体保存了 `GameplayEffectSpec` 的发起者和 [`TargetData`](#concepts-targeting-data) 的信息。这也是一个很好的结构体，可以通过子类化在 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) / [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)、[`AttributeSets`](#concepts-as) 和 [`GameplayCues`](#concepts-gc) 之间传递任意数据。

要子类化 `GameplayEffectContext`，步骤如下：

1. 子类化 `FGameplayEffectContext`
1. 重写 `FGameplayEffectContext::GetScriptStruct()`
1. 重写 `FGameplayEffectContext::Duplicate()`
1. 如果你的新数据需要被复制，重写 `FGameplayEffectContext::NetSerialize()`
1. 为你的子类实现 `TStructOpsTypeTraits`，就像父结构体 `FGameplayEffectContext` 那样
1. 在你的 [`AbilitySystemGlobals`](#concepts-asg) 类中重写 `AllocGameplayEffectContext()`，以返回你的子类的新对象

[GASShooter](https://github.com/tranek/GASShooter) 使用了一个子类化的 `GameplayEffectContext` 来添加 `TargetData`，可以在 `GameplayCues` 中访问，特别是针对霰弹枪，因为它可以命中多个敌人。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 Modifier Magnitude Calculation
[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html) (`ModMagCalc` 或 `MMC`) 是用于 `GameplayEffects` 中 [`Modifiers`](#concepts-ge-mods) 的强大类。它们的功能类似于 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)，但功能稍弱，最重要的是它们可以被 [预测](#concepts-p)。它们的唯一目的就是从 `CalculateBaseMagnitude_Implementation()` 返回一个浮点值。你可以在 Blueprint 和 C++ 中通过子类化和重写这个函数来实现自定义计算。


`MMCs` 可以用于任何持续时间的 `GameplayEffects` - `Instant`、`Duration`、`Infinite` 或 `Periodic`。

`MMCs` 的强大之处在于它们能够捕获 `GameplayEffect` 的 `Source` 或 `Target` 上任意数量的 `Attributes` 的值，并且可以完全访问 `GameplayEffectSpec` 来读取 `GameplayTags` 和 `SetByCallers`。`Attributes` 可以被快照或不被快照。被快照的 `Attributes` 在 `GameplayEffectSpec` 创建时被捕获，而未被快照的 `Attributes` 在 `GameplayEffectSpec` 应用时被捕获，并在 `Infinite` 和 `Duration` 类型的 `GameplayEffects` 中自动更新 `Attribute` 变化时的值。捕获 `Attributes` 时，会重新计算其在 `ASC` 上的 `CurrentValue`，但此重新计算**不会**运行 `AbilitySet` 中的 [`PreAttributeChange()`](#concepts-as-preattributechange)，因此任何限制都必须在此重新处理。


| Snapshot | Source or Target | Captured on `GameplayEffectSpec` | Automatically updates when `Attribute` changes for `Infinite` or `Duration` `GE` |
| -------- | ---------------- | -------------------------------- | -------------------------------------------------------------------------------- |
| Yes      | Source           | Creation                         | No                                                                               |
| Yes      | Target           | Application                      | No                                                                               |
| No       | Source           | Application                      | Yes                                                                              |
| No       | Target           | Application                      | Yes                                                                              |
| 快照   | 来源或目标 | 在 `GameplayEffectSpec` 上捕获 | 当 `Attribute` 变化时，是否会自动更新 `无限` 或 `持续` `GE` 
| 是     | 来源       | 创建时捕获                    | 否                                                       |
| 是     | 目标       | 应用时捕获                    | 否                                                       |
| 否     | 来源       | 应用时捕获                    | 是                                                       |
| 否     | 目标       | 应用时捕获                    | 是                                                       |


从 `MMC` 得到的结果浮点数可以在 `GameplayEffect` 的 `Modifier` 中通过系数以及系数前后的加法进一步修改。

一个示例 `MMC`，它捕获 `Target` 的魔法值 `Attribute`，并从中减去中毒效果的影响，减去的数量根据 `Target` 拥有的魔法值以及 `Target` 可能拥有的标签而变化：

```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef defined in header FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef defined in header FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Gather the tags from the source and target as that can affect which buffs should be used
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // Avoid divide by zero

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// Double the effect if the target has more than half their mana
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// Double the effect if the target is weak to PoisonMana
		Reduction *= 2;
	}
	
	return Reduction;
}
```

如果你没有在 `MMC` 构造函数中将 `FGameplayEffectAttributeCaptureDefinition` 添加到 `RelevantAttributesToCapture`，但尝试捕获 `Attributes`，你将会在捕获时遇到缺少 Spec 的错误。如果你不需要捕获 `Attributes`，那么你无需在 `RelevantAttributesToCapture` 中添加任何内容。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 Gameplay Effect Execution Calculation
[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html)（`ExecutionCalculation`，`Execution`（你经常会在插件的源码中看到这个术语），或者 `ExecCalc`）是 `GameplayEffects` 对 `ASC` 进行更改的最强大方式。与 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) 类似，这些可以捕获 `Attributes` 并可选择快照它们。不像 `MMCs`，这些可以更改多个 `Attribute`，并且本质上可以执行程序员希望的任何操作。其力量和灵活性的缺点是它们不能被[预测](#concepts-p)，并且必须在 C++ 中实现。

`ExecutionCalculations` 只能用于 `Instant` 和 `Periodic` `GameplayEffects`。任何带有“Execute”一词的内容通常指的是这两种类型的 `GameplayEffects`。

快照捕获在 `GameplayEffectSpec` 创建时捕获 `Attribute`，而不快照则在 `GameplayEffectSpec` 应用时捕获 `Attribute`。捕获 `Attributes` 会从现有的 `ASC` 修改中重新计算它们的 `CurrentValue`。此重新计算**不会**在 `AbilitySet` 中运行 [`PreAttributeChange()`](#concepts-as-preattributechange)，因此任何限制必须在此处再次进行。


| Snapshot | Source or Target | Captured on `GameplayEffectSpec` |
| -------- | ---------------- | -------------------------------- |
| Yes      | Source           | Creation                         |
| Yes      | Target           | Application                      |
| No       | Source           | Application                      |
| No       | Target           | Application                      |
| 快照      | 来源或目标      | 捕获于 `GameplayEffectSpec`         |
| 是        | 来源            | 创建时捕获                          |
| 是        | 目标            | 应用时捕获                          |
| 否        | 来源            | 应用时捕获                          |
| 否        | 目标            | 应用时捕获                          |


要设置 `Attribute` 的捕获，我们遵循Epic的ActionRPG示例项目中的模式，定义一个结构体来持有和定义如何捕获这些 `Attributes`，并在结构体的构造函数中创建其副本。对于每个 `ExecCalc` 都会有一个类似的结构体。**注意：** 每个结构体需要一个唯一的名称，因为它们共享相同的命名空间。使用相同名称的结构体会导致捕获 `Attributes` 时出现不正确的行为（通常会捕获错误的 `Attributes` 值）。

对于 `Local Predicted`、`Server Only` 和 `Server Initiated` [`GameplayAbilities`](#concepts-ga)，`ExecCalc` 只会在服务器端调用。

基于读取 `Source` 和 `Target` 上的多个属性，通过复杂公式计算接收到的伤害是 `ExecCalc` 最常见的示例。示例项目中包含了一个简单的 `ExecCalc` 用于计算伤害，它从 `GameplayEffectSpec` 的 [`SetByCaller`](#concepts-ge-spec-setbycaller) 读取伤害值，然后基于从 `Target` 捕获的护甲 `Attribute` 来减轻该值。详见 `GDDamageExecCalculation.cpp/.h`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 Sending Data to Execution Calculations
除了捕获 `Attributes` 之外，还有几种方法可以将数据发送到 `ExecutionCalculation`。

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller
任何设置在 `GameplayEffectSpec` 上的 [`SetByCallers`](#concepts-ge-spec-setbycaller) 可以直接在 `ExecutionCalculation` 中读取。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 Backing Data Attribute Calculation Modifier
如果你想将硬编码的数值传递给 `GameplayEffect`，可以使用 `CalculationModifier`，并将其中一个捕获的 `Attributes` 作为基础数据来传递。

在这个截图示例中，我们在捕获的 Damage `Attribute` 上加上 50。你也可以将其设置为 `Override`，只采用硬编码的数值。

![Backing Data Attribute Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

当 `ExecutionCalculation` 捕获 `Attribute` 时，会读取这个值。

```c++
float Damage = 0.0f;
// Capture optional damage value set on the damage GE as a CalculationModifier under the ExecutionCalculation
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 Backing Data Temporary Variable Calculation Modifier
如果你想将硬编码的数值传递给 `GameplayEffect`，可以使用 `CalculationModifier`，并使用 `Temporary Variable`（在 C++ 中也称为 `Transient Aggregator`）来传递这些数值。`Temporary Variable` 与一个 `GameplayTag` 相关联。

在这个截图示例中，我们通过 `Data.Damage` `GameplayTag` 向一个 `Temporary Variable` 添加了 50。

![Backing Data Temporary Variable Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

在你的 `ExecutionCalculation` 的构造函数中添加 `Temporary Variables` 作为基础数据：

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation` 使用类似于 `Attribute` 捕获函数的特殊捕获函数来读取此值。
```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 Gameplay Effect Context
您可以通过在 `GameplayEffectSpec` 上的自定义 [`GameplayEffectContext`](#concepts-ge-context) 将数据发送到 `ExecutionCalculation`。

在 `ExecutionCalculation` 中，您可以通过 `FGameplayEffectCustomExecutionParameters` 访问 `EffectContext`。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

如果您需要更改 `GameplayEffectSpec` 或 `EffectContext`：

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

在 `ExecutionCalculation` 中修改 `GameplayEffectSpec` 时要小心。请参阅 `GetOwningSpecForPreExecuteMod()` 的注释。

```c++
/** Non const access. Be careful with this, especially when modifying a spec after attribute capture. */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 Custom Application Requirement
[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html) (`CAR`) 类为设计师提供了比 `GameplayEffect` 上简单的 `GameplayTag` 检查更高级的控制，用于判断 `GameplayEffect` 是否可以应用。这些可以通过在 Blueprint 中覆盖 `CanApplyGameplayEffect()` 或在 C++ 中覆盖 `CanApplyGameplayEffect_Implementation()` 来实现。

`CARs` 的使用示例：
* `目标`需要拥有一定数量的 `属性`
* `目标`需要拥有一定数量的 `GameplayEffect` 堆叠

`CARs` 还可以执行更高级的操作，例如检查这个 `GameplayEffect` 的实例是否已经存在于 `目标` 上，并[更改现有实例的持续时间](#concepts-ge-duration)而不是应用一个新实例（在 `CanApplyGameplayEffect()` 中返回 false）。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 Cost Gameplay Effect
[`GameplayAbilities`](#concepts-ga) 有一个可选的 `GameplayEffect`，专门用作能力的消耗。消耗指的是 `ASC` 需要拥有多少 `属性` 才能激活 `GameplayAbility`。如果 `GA` 无法负担 `Cost GE`，那么它们将无法激活。这个 `Cost GE` 应该是一个带有一个或多个从 `Attributes` 中减去的 `Modifiers` 的 `Instant` `GameplayEffect`。默认情况下，`Cost GEs` 旨在预测，并且建议保持这种能力，这意味着不要使用 `ExecutionCalculations`。`MMCs` 是完全可以接受并且推荐用于复杂的成本计算的。

在开始时，你很可能会为每个有成本的 `GA` 创建一个独特的 `Cost GE`。一种更高级的技术是为多个 `GA` 重用一个 `Cost GE`，并只需使用 `GA` 特定的数据（成本值在 `GA` 上定义）修改从 `Cost GE` 创建的 `GameplayEffectSpec`。**这仅适用于 `Instanced` 能力。**

重用 `Cost GE` 的两种技术：

1. **使用 `MMC`。** 这是最简单的方法。创建一个 [`MMC`](#concepts-ge-mmc)，从你可以从 `GameplayEffectSpec` 中获取的 `GameplayAbility` 实例读取成本值。

```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

在这个示例中，成本值是我添加到 `GameplayAbility` 子类中的一个 `FScalableFloat`。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **重写 `UGameplayAbility::GetCostGameplayEffect()`**。重写此函数，并[在运行时创建一个 `GameplayEffect`](#concepts-ge-dynamic)，读取 `GameplayAbility` 上的成本值。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 Cooldown Gameplay Effect
[`GameplayAbilities`](#concepts-ga) 拥有一个专门设计的可选 `GameplayEffect`，用于作为技能的冷却时间。冷却时间决定了在激活技能后多长时间才能再次激活。如果 `GA` 仍处于冷却状态，它将无法激活。这个 `Cooldown GE` 应该是一个 `Duration` 类型的 `GameplayEffect`，没有 `Modifiers`，并且在 `GameplayEffect` 的 `GrantedTags` 中为每个 `GameplayAbility` 或每个技能槽分配一个独特的 `GameplayTag`（如果您的游戏有可互换的技能分配给共享冷却时间的槽位）作为 "`Cooldown Tag`"。`GA` 实际上检查的是 `Cooldown Tag` 的存在，而不是 `Cooldown GE` 的存在。默认情况下，`Cooldown GEs` 旨在进行预测，建议保持这种能力，即不要使用 `ExecutionCalculations`。`MMCs` 是完全可以接受并且鼓励用于复杂的冷却时间计算的。

初学者通常会为每个有冷却时间的 `GA` 创建一个独特的 `Cooldown GE`。一种更高级的技巧是为多个 `GA` 复用一个 `Cooldown GE`，并仅使用 `GA` 特定的数据（冷却时间和 `Cooldown Tag` 定义在 `GA` 上）修改从 `Cooldown GE` 创建的 `GameplayEffectSpec`。**这仅适用于 `Instanced` 能力。**

复用 `Cooldown GE` 的两种方法：

1. **使用 [`SetByCaller`](#concepts-ge-spec-setbycaller)。** 这是最简单的方法。将共享的 `Cooldown GE` 的持续时间设置为带有 `GameplayTag` 的 `SetByCaller`。在您的 `GameplayAbility` 子类中，定义一个浮点数 / `FScalableFloat` 来表示持续时间，一个 `FGameplayTagContainer` 来表示独特的 `Cooldown Tag`，以及一个临时的 `FGameplayTagContainer`，我们将用作返回指针，表示我们的 `Cooldown Tag` 与 `Cooldown GE` 的标签的联合。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

然后，重写 `UGameplayAbility::GetCooldownTags()` 方法，以返回我们定义的 `Cooldown Tags` 与任何现有的 `Cooldown GE` 标签的联合。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后，重写 `UGameplayAbility::ApplyCooldown()` 方法，将我们定义的 `Cooldown Tags` 注入到冷却 `GameplayEffectSpec` 中，并添加 `SetByCaller`。
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

在这张图片中，冷却时间的持续时间 `Modifier` 被设置为 `SetByCaller`，并带有 `Data Tag` 为 `Data.Cooldown`。在上面的代码中，`Data.Cooldown` 对应的是 `OurSetByCallerTag`。

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **使用一个 [`MMC`](#concepts-ge-mmc)。** 这个设置与上面提到的类似，不同之处在于，不是将冷却时间设置为 `SetByCaller`，而是在 `Cooldown GE` 和 `ApplyCooldown` 中，将持续时间设置为 `Custom Calculation Class`，并指向我们将创建的新的 `MMC`。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

然后重写 `UGameplayAbility::GetCooldownTags()` 函数，以返回我们定义的 `Cooldown Tags` 和任何现有的 `Cooldown GE` 标签的并集。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后，重写 `UGameplayAbility::ApplyCooldown()` 函数，将我们的 `Cooldown Tags` 注入到冷却的 `GameplayEffectSpec` 中。
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 Get the Cooldown Gameplay Effect's Remaining Time
```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

 return false;
}
```
**注意：** 在客户端查询冷却时间的剩余时间需要他们能够接收复制的 `GameplayEffects`。这将取决于其 `ASC` 的[复制模式](#concepts-asc-rm)。

#### 4.5.15.2 监听冷却开始和结束

要监听冷却开始，可以通过绑定 `AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf` 响应 `Cooldown GE` 的应用，或通过绑定 `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)` 监听 `Cooldown Tag` 的添加。我推荐监听 `Cooldown GE` 的添加，因为这样你还能访问应用它的 `GameplayEffectSpec`。通过这种方式，你可以确定 `Cooldown GE` 是本地预测的还是服务器的修正版本。

要监听冷却结束，可以通过绑定 `AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()` 响应 `Cooldown GE` 的移除，或通过绑定 `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)` 监听 `Cooldown Tag` 的移除。我推荐监听 `Cooldown Tag` 的移除，因为当服务器的修正 `Cooldown GE` 到来时，它会移除我们本地预测的 `Cooldown GE`，从而触发 `OnAnyGameplayEffectRemovedDelegate()`，即使我们仍处于冷却状态。`Cooldown Tag` 在预测的 `Cooldown GE` 被移除和服务器的修正 `Cooldown GE` 应用之间不会发生变化。

**注意：** 在客户端监听 `GameplayEffect` 的添加或移除需要它们能够接收复制的 `GameplayEffects`。这将取决于它们的 `ASC` 的[复制模式](#concepts-asc-rm)。

示例项目包括一个自定义的蓝图节点，用于监听冷却的开始和结束。HUD UMG Widget 使用它来更新陨石技能冷却剩余时间。这个 `AsyncTask` 将一直存在，直到手动调用 `EndTask()`，我们在 UMG Widget 的 `Destruct` 事件中完成了这一操作。参见 [`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp)。

![监听冷却变化 BP 节点](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

##### 4.5.15.3 预测冷却

当前冷却无法真正被预测。我们可以在本地预测的 `Cooldown GE` 应用时启动 UI 冷却计时器，但 `GameplayAbility` 的实际冷却时间与服务器的冷却时间剩余时间相关联。根据玩家的延迟，本地预测的冷却可能会结束，但 `GameplayAbility` 在服务器上仍处于冷却状态，这将阻止 `GameplayAbility` 在服务器的冷却结束前立即重新激活。

示例项目通过在本地预测冷却开始时将陨石技能的 UI 图标变灰，然后在服务器的修正 `Cooldown GE` 到来时启动冷却计时器来处理这一情况。

这种做法的一个游戏后果是，高延迟的玩家在短冷却技能上的开火率低于低延迟玩家，使他们处于不利地位。Fortnite 通过其武器使用自定义记录系统而不是冷却 `GameplayEffects` 来避免这种情况。

允许真正的预测冷却（玩家可以在本地冷却结束时激活 `GameplayAbility`，但服务器仍处于冷却中）是 Epic 希望在未来的 [GAS 迭代中实现](#concepts-p-future) 的功能。

**[⬆ 返回顶部](#table-of-contents)**

#### 4.5.16 更改活动的 Gameplay Effect 持续时间

要更改 `Cooldown GE` 或任何 `Duration` `GameplayEffect` 的剩余时间，我们需要更改 `GameplayEffectSpec` 的 `Duration`，更新其 `StartServerWorldTime`，更新其 `CachedStartServerWorldTime`，更新其 `StartWorldTime`，并使用 `CheckDuration()` 重新检查持续时间。在服务器上执行此操作并将 `FActiveGameplayEffect` 标记为脏数据将把更改复制到客户端。

**注意：** 这涉及使用 `const_cast`，可能不是 Epic 的预期更改持续时间的方法，但到目前为止似乎效果不错。

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 Creating Dynamic Gameplay Effects at Runtime
创建动态的 `GameplayEffects` 在运行时是一个高级话题，你不应该经常需要这样做。

只有 `Instant` 类型的 `GameplayEffects` 可以在运行时通过 C++ 从头创建。`Duration` 和 `Infinite` 类型的 `GameplayEffects` 不能在运行时动态创建，因为当它们进行复制时，会查找不存在的 `GameplayEffect` 类定义。要实现这种功能，你应该像在编辑器中正常操作一样，先创建一个原型 `GameplayEffect` 类。然后在运行时根据需要自定义 `GameplayEffectSpec` 实例。

在运行时创建的 `Instant` 类型 `GameplayEffects` 也可以在[本地预测](#concepts-p)的 `GameplayAbility` 中调用。然而，目前尚不清楚动态创建是否会有副作用。

##### Examples

示例项目创建了一个 `Instant` 类型的 `GameplayEffect`，用于在角色被击杀时将金币和经验点数发送回击杀者。这是在 `AttributeSet` 中实现的。

```c++
// Create a dynamic instant Gameplay Effect to give the bounties
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

第二个示例展示了在本地预测的 `GameplayAbility` 内创建的一个运行时 `GameplayEffect`。请自行承担风险使用（参见代码中的注释）！


```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// Create the GE at runtime.
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // Only instant works with runtime GE.

		// Add a simple scalable float modifier, which overrides MyAttribute with 42.
		// In real world applications, consume information passed via TriggerEventData.
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// Apply the GE.

		// Create the GESpec here to avoid the behavior of ASC to create GESpecs from the GE class default object.
		// Since we have a dynamic GE here, this would create a GESpec with the base GameplayEffect class, so we
		// would lose our modifiers. Attention: It is unknown, if this "hack" done here can have drawbacks!
		// The spec prevents the GE object being collected by the GarbageCollector, since the GE is a UPROPERTY on the spec.
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // "new", since lifetime is managed by a shared ptr within the handle
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 游戏效果容器（Gameplay Effect Containers）
Epic 的 [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) 实现了一种名为 `FGameplayEffectContainer` 的结构。这些结构不在原版的 GAS 中，但对于包含 `GameplayEffects` 和 [`TargetData`](#concepts-targeting-data) 非常有用。它自动化了一些操作，比如从 `GameplayEffects` 创建 `GameplayEffectSpecs` 以及在其 `GameplayEffectContext` 中设置默认值。在 `GameplayAbility` 中创建一个 `GameplayEffectContainer` 并将其传递给生成的投射物非常简单明了。我选择不在包含的示例项目中实现 `GameplayEffectContainers`，以展示在原版 GAS 中如何在没有它们的情况下工作，但我强烈建议研究它们并考虑将其添加到您的项目中。

要访问 `GameplayEffectContainers` 中的 `GESpecs` 以进行诸如添加 `SetByCallers` 等操作，请分解 `FGameplayEffectContainer` 并按其在 `GESpecs` 数组中的索引访问 `GESpec` 引用。这需要您提前知道要访问的 `GESpec` 的索引。

![SetByCaller with a GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainers` 还包含一种可选的高效 [目标选择](#concepts-targeting-containers) 方法。

**[⬆ 返回顶部](#table-of-contents)**


<a name="concepts-ga"></a>
### 4.6 Gameplay Abilities

<a name="concepts-ga-definition"></a>
#### 4.6.1 游戏能力定义
[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html) (`GA`) 是游戏中一个 `Actor` 可以执行的任何动作或技能。多个 `GameplayAbility` 可以同时激活，例如奔跑和射击。这些能力可以用 Blueprint 或 C++ 创建。

`GameplayAbilities` 示例：
* 跳跃
* 奔跑
* 射击
* 每隔 X 秒被动格挡一次攻击
* 使用药水
* 开门
* 收集资源
* 建造建筑

不应使用 `GameplayAbilities` 实现的内容：
* 基本移动输入
* 一些与 UI 的交互 - 不要使用 `GameplayAbility` 来从商店购买物品。

这些并不是规则，只是我的建议。您的设计和实现可能有所不同。

`GameplayAbilities` 提供默认功能来修改属性变化的程度或改变 `GameplayAbility` 的功能性。

`GameplayAbilities` 根据 [`Net Execution Policy`](#concepts-ga-net) 在拥有者客户端和/或服务器上运行，但不在模拟代理上运行。`Net Execution Policy` 决定了 `GameplayAbility` 是否会在本地进行[预测](#concepts-p)。它们包含 [可选的花费和冷却时间 `GameplayEffects`](#concepts-ga-commit) 的默认行为。`GameplayAbilities` 使用 [`AbilityTasks`](#concepts-at) 来处理需要一段时间的操作，例如等待事件、等待属性变化、等待玩家选择目标或使用 `Root Motion Source` 移动 `Character`。**模拟客户端不会运行 `GameplayAbilities`**。相反，当服务器运行能力时，任何需要在模拟代理上视觉显示的内容（如动画蒙太奇）将通过 `AbilityTasks` 或 [`GameplayCues`](#concepts-gc) 进行复制或远程过程调用（RPC），用于播放声音和粒子效果等外观内容。

所有的 `GameplayAbilities` 都会覆盖其 `ActivateAbility()` 函数以实现您的游戏逻辑。可以在 `EndAbility()` 中添加附加逻辑，该逻辑将在 `GameplayAbility` 完成或取消时运行。

简单 `GameplayAbility` 的流程图：
![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)

更复杂的 `GameplayAbility` 流程图：
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

复杂的能力可以通过多个 `GameplayAbilities` 进行实现，这些能力之间可以相互激活、取消等。

<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 复制策略
不要使用此选项。该名称具有误导性，且您不需要使用它。[`GameplayAbilitySpecs`](#concepts-ga-spec) 默认情况下从服务器复制到拥有者客户端。如上所述，**`GameplayAbilities` 不会在模拟代理上运行**。它们使用 `AbilityTasks` 和 `GameplayCues` 来复制或 RPC 视觉变化到模拟代理。Epic 的 Dave Ratti 已表示希望在未来[移除此选项](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)。

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 服务器尊重远程能力取消
此选项通常会引发问题。它意味着如果客户端的 `GameplayAbility` 由于取消或自然完成而结束，无论服务器上的版本是否完成，都会强制其结束。后一个问题尤为重要，特别是对于高延迟玩家使用的本地预测 `GameplayAbilities`。通常情况下，您会希望禁用此选项。

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 直接复制输入
设置此选项将始终将输入按压和释放事件复制到服务器。Epic 建议不要使用此选项，而是依赖现有输入相关 [`AbilityTasks`](#concepts-at) 中内置的 `Generic Replicated Events`，如果您的 [输入绑定到您的 `ASC`](#concepts-ga-input) 上。


Epic's comment:
```c++
/** Direct Input state replication. These will be called if bReplicateInputDirectly is true on the ability and is generally not a good thing to use. (Instead, prefer to use Generic Replicated Events). */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 绑定输入到 ASC
`ASC` 允许您将输入动作直接绑定到它，并在授予 `GameplayAbilities` 时将这些输入分配给它们。分配给 `GameplayAbilities` 的输入动作在按下时会自动激活这些 `GameplayAbilities`，前提是满足 `GameplayTag` 要求。分配的输入动作是使用内置 `AbilityTasks` 响应输入所必需的。

除了用于激活 `GameplayAbilities` 的输入动作外，`ASC` 还接受通用的 `Confirm` 和 `Cancel` 输入。这些特殊输入由 `AbilityTasks` 使用，用于确认诸如 [`Target Actors`](#concepts-targeting-actors) 等操作或取消它们。

要将输入绑定到 `ASC`，首先必须创建一个将输入动作名称转换为字节的枚举。枚举名称必须与项目设置中使用的输入动作名称完全匹配。`DisplayName` 无关紧要。

以下是来自示例项目的代码示例：

```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```
如果您的 `ASC` 位于 `Character` 上，那么在 `SetupPlayerInputComponent()` 中包含绑定到 `ASC` 的函数：

```c++
// Bind to AbilitySystemComponent
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

如果您的 `ASC` 位于 `PlayerState` 上，在 `SetupPlayerInputComponent()` 内部可能会存在潜在的竞态条件，此时 `PlayerState` 可能尚未复制到客户端。因此，我建议在 `SetupPlayerInputComponent()` 和 `OnRep_PlayerState()` 中尝试绑定输入。仅依靠 `OnRep_PlayerState()` 是不够的，因为在 `PlayerState` 复制到客户端之前，`Actor` 的 `InputComponent` 可能为空，而这可能是在 `PlayerController` 告诉客户端调用 `ClientRestart()` 之前发生的，后者会创建 `InputComponent`。示例项目展示了如何在两个位置尝试绑定输入，并使用一个布尔变量来控制绑定过程，以确保输入仅被绑定一次。

**注意：** 在示例项目中，枚举中的 `Confirm` 和 `Cancel` 与项目设置中的输入操作名称（`ConfirmTarget` 和 `CancelTarget`）不匹配，但我们在 `BindAbilityActivationToInputComponent()` 中提供了它们之间的映射。这些是特殊情况，因为我们提供了映射，它们不必匹配，但也可以匹配。枚举中的所有其他输入必须与项目设置中的输入操作名称匹配。

对于只能通过一个输入激活的 `GameplayAbilities`（它们将始终存在于相同的“槽位”中，如MOBA游戏中的情况），我更倾向于在我的 `UGameplayAbility` 子类中添加一个变量，用于定义它们的输入。然后在授予能力时，我可以从 `ClassDefaultObject` 中读取该输入。

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 绑定输入而不激活能力
如果您不希望 `GameplayAbilities` 在按下输入时自动激活，但仍希望绑定它们以便在 `AbilityTasks` 中使用，您可以在 `UGameplayAbility` 子类中添加一个新的布尔变量 `bActivateOnInput`，其默认值为 `true`，并重写 `UAbilitySystemComponent::AbilityLocalInputPressed()`。


```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// Consume the input if this InputID is overloaded with GenericConfirm/Cancel and the GenericConfim/Cancel callback is bound
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// Invoke the InputPressed event. This is not replicated here. If someone is listening, they may replicate the InputPressed event to the server.
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// Ability is not active, so try to activate it
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 Granting Abilities 授予能力
将 `GameplayAbility` 授予 `ASC` 会将其添加到 `ASC` 的 `ActivatableAbilities` 列表中，只要满足 [`GameplayTag` 要求](#concepts-ga-tags)，就可以随时激活该 `GameplayAbility`。

我们在服务器上授予 `GameplayAbilities`，然后自动将 [`GameplayAbilitySpec`](#concepts-ga-spec) 复制到拥有该能力的客户端。其他客户端/模拟代理不会接收到 `GameplayAbilitySpec`。

示例项目在 `Character` 类上存储了一个 `TArray<TSubclassOf<UGDGameplayAbility>>`，该数组会在游戏开始时被读取并授予：


```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// Grant abilities, but only on the server	
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->bCharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->bCharacterAbilitiesGiven = true;
}
```

在授予这些 `GameplayAbilities` 时，我们使用 `UGameplayAbility` 类、能力等级、绑定的输入以及 `SourceObject`（即谁将此 `GameplayAbility` 授予了该 `ASC`）创建了 `GameplayAbilitySpecs`。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 Activating Abilities 激活能力
如果 `GameplayAbility` 分配了一个输入操作，它将在按下输入并且满足其 `GameplayTag` 要求时自动激活。不过，这不一定总是期望的激活方式。`ASC` 提供了四种其他激活 `GameplayAbilities` 的方法：通过 `GameplayTag`、`GameplayAbility` 类、`GameplayAbilitySpec` 句柄以及通过事件。通过事件激活 `GameplayAbility` 允许你[随事件传递数据负载](#concepts-ga-data)。


```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec, const FGameplayEventData* GameplayEventData);
```
要通过事件激活 `GameplayAbility`，必须在 `GameplayAbility` 中设置其 `Triggers`。分配一个 `GameplayTag` 并选择一个 `GameplayEvent` 选项。要发送事件，使用函数 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。通过事件激活 `GameplayAbility` 允许你传递包含数据的负载。

`GameplayAbility` 的 `Triggers` 还允许你在 `GameplayTag` 被添加或移除时激活 `GameplayAbility`。

**注意：** 在 Blueprint 中通过事件激活 `GameplayAbility` 时，必须使用 `ActivateAbilityFromEvent` 节点。

**注意：** 当 `GameplayAbility` 应该终止时，不要忘记调用 `EndAbility()`，除非你有一个将始终运行的 `GameplayAbility`，如被动能力。

**本地预测** `GameplayAbilities` 的激活顺序：
1. **拥有的客户端** 调用 `TryActivateAbility()`
2. 调用 `InternalTryActivateAbility()`
3. 调用 `CanActivateAbility()`，并返回 `GameplayTag` 要求是否满足，`ASC` 是否能负担费用，`GameplayAbility` 是否不在冷却中，以及是否没有其他实例当前激活
4. 调用 `CallServerTryActivateAbility()` 并传递它生成的 `Prediction Key`
5. 调用 `CallActivateAbility()`
6. 调用 `PreActivate()`，Epic 称之为“样板初始化内容”
7. 最终调用 `ActivateAbility()` 激活能力

**服务器** 接收 `CallServerTryActivateAbility()`
1. 调用 `ServerTryActivateAbility()`
2. 调用 `InternalServerTryActivateAbility()`
3. 调用 `InternalTryActivateAbility()`
4. 调用 `CanActivateAbility()`，并返回 `GameplayTag` 要求是否满足，`ASC` 是否能负担费用，`GameplayAbility` 是否不在冷却中，以及是否没有其他实例当前激活
5. 如果成功，调用 `ClientActivateAbilitySucceed()`，通知客户端更新其 `ActivationInfo`，确认其激活已由服务器确认，并广播 `OnConfirmDelegate` 委托。这与输入确认不同。
6. 调用 `CallActivateAbility()`
7. 调用 `PreActivate()`，Epic 称之为“样板初始化内容”
8. 最终调用 `ActivateAbility()` 激活能力

如果服务器在任何时候激活失败，它将调用 `ClientActivateAbilityFailed()`，立即终止客户端的 `GameplayAbility` 并撤销任何预测更改。

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 被动能力
要实现自动激活并持续运行的被动 `GameplayAbilities`，重写 `UGameplayAbility::OnAvatarSet()`，它在 `GameplayAbility` 被授予并设置 `AvatarActor` 时自动调用，然后调用 `TryActivateAbility()`。

我建议在自定义 `UGameplayAbility` 类中添加一个 `bool`，用于指定 `GameplayAbility` 是否应该在授予时激活。示例项目为其被动护甲叠加能力做了这一设置。

被动 `GameplayAbilities` 通常具有 `Net Execution Policy` 为 `Server Only`。

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (bActivateAbilityOnGranted)
	{
		ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epic 将此函数描述为启动被动能力以及执行类似 `BeginPlay` 操作的正确位置。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-activating-failedtags"></a>
##### 4.6.4.2 激活失败标签

能力有默认的逻辑来告诉你为什么能力激活失败。要启用此功能，你必须设置与默认失败情况对应的`GameplayTags`。

将这些标签（或你自己的命名规范）添加到你的项目中：

```
+GameplayTagList=(Tag="Activation.Fail.BlockedByTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.CantAffordCost",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.IsDead",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.MissingTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.Networking",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.OnCooldown",DevComment="")
```

然后将它们添加到 [`GASDocumentation\Config\DefaultGame.ini`] 中：
(https://github.com/tranek/GASDocumentation/blob/master/Config/DefaultGame.ini#L8-L13):
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
ActivateFailIsDeadName=Activation.Fail.IsDead
ActivateFailCooldownName=Activation.Fail.OnCooldown
ActivateFailCostName=Activation.Fail.CantAffordCost
ActivateFailTagsBlockedName=Activation.Fail.BlockedByTags
ActivateFailTagsMissingName=Activation.Fail.MissingTags
ActivateFailNetworkingName=Activation.Fail.Networking
```

现在，每当一个技能激活失败时，相应的 `GameplayTag` 将包含在输出日志消息中，或者在 `showdebug AbilitySystem` HUD 上可见。

```
LogAbilitySystem: Display: InternalServerTryActivateAbility. Rejecting ClientActivation of Default__GA_FireGun_C. InternalTryActivateAbility failed: Activation.Fail.BlockedByTags
LogAbilitySystem: Display: ClientActivateAbilityFailed_Implementation. PredictionKey :109 Ability: Default__GA_FireGun_C
```

![Activation Failed Tags Displayed in showdebug AbilitySystem](https://github.com/tranek/GASDocumentation/raw/master/Images/activationfailedtags.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 Canceling Abilities
要从内部取消一个 `GameplayAbility`，可以调用 `CancelAbility()`。这将调用 `EndAbility()` 并将其 `WasCancelled` 参数设置为 true。

要从外部取消一个 `GameplayAbility`，`ASC` 提供了几个函数：


```c++
/** Cancels the specified ability CDO. */
void CancelAbility(UGameplayAbility* Ability);	

/** Cancels the ability indicated by passed in spec handle. If handle is not found among reactivated abilities nothing happens. */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** Cancel all abilities with the specified tags. Will not cancel the Ignore instance */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities regardless of tags. Will not cancel the ignore instance */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities and kills any remaining instanced abilities */
virtual void DestroyActiveState();
```

**Note:** I have found that `CancelAllAbilities` doesn't seem to work right if you have a `Non-Instanced` `GameplayAbilities`. It seems to hit the `Non-Instanced` `GameplayAbility` and give up. `CancelAbilities` can handle `Non-Instanced` `GameplayAbilities` better and that is what the Sample Project uses (Jump is a non-instanced `GameplayAbility`). Your mileage may vary.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 Getting Active Abilities 获取激活的能力

初学者经常问：“如何获取激活的能力？”可能是为了设置变量或取消它。由于多个 `GameplayAbility` 可以同时激活，因此没有一个“唯一的激活能力”。相反，你必须在 `ASC` 的 `ActivatableAbilities` 列表中搜索（ASC 拥有的被授予的 `GameplayAbilities`），并找到匹配的 [`Asset` 或 `Granted` `GameplayTag`](#concepts-ga-tags)。

`UAbilitySystemComponent::GetActivatableAbilities()` 返回一个 `TArray<FGameplayAbilitySpec>`，供你进行迭代。

`ASC` 还提供了一个辅助函数，接受一个 `GameplayTagContainer` 作为参数，帮助你进行搜索，而不是手动遍历 `GameplayAbilitySpecs` 列表。`bOnlyAbilitiesThatSatisfyTagRequirements` 参数将仅返回满足其 `GameplayTag` 要求并且可以立即激活的 `GameplayAbilitySpecs`。例如，你可能有两个基本攻击的 `GameplayAbilities`，一个使用武器，一个使用赤手，正确的能力将根据是否装备了武器（设置 `GameplayTag` 要求）而激活。有关更多信息，请参阅 Epic 对该函数的注释。


```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

一旦你找到所需的 `FGameplayAbilitySpec`，你可以调用 `IsActive()` 来检查它是否处于激活状态。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 Instancing Policy 实例化策略
`GameplayAbility` 的 `Instancing Policy` 决定了该 `GameplayAbility` 在激活时是否以及如何实例化。


| `实例化策略`            | 描述                                                                                          | 适用场景示例                                                                                                                                                                                                                                                                                                                                                                                                       |
| ----------------------- | ----------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 每个 Actor 实例化        | 每个 `ASC` 只有一个 `GameplayAbility` 实例，并在多次激活间重复使用。                            | 这可能是你使用最多的 `实例化策略`。它适用于任何能力，并在多次激活之间保持持久性。设计人员负责在每次激活之间手动重置任何需要重置的变量。                                                                                                                                                                                                                                                                                             |
| 每次执行实例化          | 每次激活 `GameplayAbility` 时，都会创建一个新的 `GameplayAbility` 实例。                        | 这种 `GameplayAbilities` 的优点是每次激活时变量都会重置。由于每次激活都会生成新的 `GameplayAbilities`，因此它们的性能比 `每个 Actor 实例化` 更差。Sample Project 中没有使用这种策略。                                                                                                                                                                                                                                               |
| 非实例化               | `GameplayAbility` 在其 `ClassDefaultObject` 上运行。不会创建任何实例。                          | 这是三种策略中性能最佳的，但它在功能上最受限制。`非实例化` 的 `GameplayAbilities` 不能存储状态，这意味着不能使用动态变量，也不能绑定 `AbilityTask` 的委托。最适合使用在简单且频繁使用的能力上，例如 MOBA 或 RTS 中的小兵基础攻击。Sample Project 中的跳跃 `GameplayAbility` 就是 `非实例化` 的。                                                                                                          |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-net"></a>

#### 4.6.8 Net Execution Policy 网络执行策略
`GameplayAbility` 的 `网络执行策略` 决定了谁来运行该 `GameplayAbility` 以及以何种顺序运行。

| `Net Execution Policy` | Description                                                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Local Only`           | `GameplayAbility` 仅在拥有客户端上运行。这对于只进行本地化装饰性更改的能力可能很有用。单人游戏应使用 `Server Only`。                                     |
| `Local Predicted`      | `Local Predicted` 的 `GameplayAbilities` 首先在拥有客户端上激活，然后在服务器上激活。服务器版本将纠正客户端预测错误的任何内容。详见 [Prediction](#concepts-p)。 |
| `Server Only`          | `GameplayAbility` 仅在服务器上运行。被动的 `GameplayAbilities` 通常为 `Server Only`。单人游戏应使用此选项。                                                                  |
| `Server Initiated`     | `Server Initiated` 的 `GameplayAbilities` 首先在服务器上激活，然后在拥有客户端上激活。我个人很少使用这些。   |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 Ability Tags
`GameplayAbilities` 自带 `GameplayTagContainers` 及内置逻辑。这些 `GameplayTags` 都不会被复制（replicated）。


| `GameplayTag Container`     | 描述                                                                                                                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ability Tags`              | `GameplayAbility` 所拥有的 `GameplayTags`。这些仅用于描述 `GameplayAbility`。                                                                              |
| `Cancel Abilities with Tag` | 当此 `GameplayAbility` 激活时，其他拥有这些 `GameplayTags` 的 `GameplayAbilities` 将被取消。                                                   |
| `Block Abilities with Tag`  | 当此 `GameplayAbility` 激活时，其他拥有这些 `GameplayTags` 的 `GameplayAbilities` 将无法激活。                                          |
| `Activation Owned Tags`     | 当此 `GameplayAbility` 激活时，这些 `GameplayTags` 会赋予 `GameplayAbility` 的拥有者。请注意，这些标签不会被同步。                                                    |
| `Activation Required Tags`  | 只有当拥有者拥有**所有**这些 `GameplayTags` 时，此 `GameplayAbility` 才能被激活。                                                                                                |
| `Activation Blocked Tags`   | 只要拥有者拥有**任意**这些 `GameplayTags`，此 `GameplayAbility` 就无法被激活。                                                                                                  |
| `Source Required Tags`      | 只有当 `Source` 拥有**所有**这些 `GameplayTags` 时，此 `GameplayAbility` 才能被激活。`Source` 的 `GameplayTags` 只有在 `GameplayAbility` 由事件触发时才会被设置。 |
| `Source Blocked Tags`       | 只要 `Source` 拥有**任意**这些 `GameplayTags`，此 `GameplayAbility` 就无法被激活。`Source` 的 `GameplayTags` 只有在 `GameplayAbility` 由事件触发时才会被设置。   |
| `Target Required Tags`      | 只有当 `Target` 拥有**所有**这些 `GameplayTags` 时，此 `GameplayAbility` 才能被激活。`Target` 的 `GameplayTags` 只有在 `GameplayAbility` 由事件触发时才会被设置。 |
| `Target Blocked Tags`       | 只要 `Target` 拥有**任意**这些 `GameplayTags`，此 `GameplayAbility` 就无法被激活。`Target` 的 `GameplayTags` 只有在 `GameplayAbility` 由事件触发时才会被设置。   |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 Gameplay Ability Spec

`GameplayAbilitySpec` 是在 `GameplayAbility` 被授予后存在于 `ASC` 上的结构，它定义了可激活的 `GameplayAbility`，包括 `GameplayAbility` 的类、等级、输入绑定以及必须与 `GameplayAbility` 类分开的运行时状态。

当 `GameplayAbility` 在服务器上被授予时，服务器会将 `GameplayAbilitySpec` 同步到拥有客户端，以便她可以激活它。

激活 `GameplayAbilitySpec` 会根据 `GameplayAbility` 的 `Instancing Policy` 创建一个实例（或对于 `Non-Instanced` 的 `GameplayAbilities` 不会创建实例）。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 Passing Data to Abilities

`GameplayAbilities` 的一般模式是 `Activate->Generate Data->Apply->End`。有时你需要处理已有的数据。GAS 提供了几种将外部数据传递给 `GameplayAbilities` 的方法：


| 方法                                              | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 通过事件激活 `GameplayAbility`                   | 使用包含数据负载的事件激活 `GameplayAbility`。事件的负载会从客户端复制到服务器，用于本地预测的 `GameplayAbilities`。对于不适合现有变量的任意数据，可以使用两个 `Optional Object` 或 [`TargetData`](#concepts-targeting-data) 变量。缺点是这会阻止你通过输入绑定来激活能力。要通过事件激活 `GameplayAbility`，必须在 `GameplayAbility` 中设置 `Triggers`。分配一个 `GameplayTag` 并选择一个 `GameplayEvent` 选项。要发送事件，请使用函数 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。 |
| 使用 `WaitGameplayEvent` `AbilityTask`           | 使用 `WaitGameplayEvent` `AbilityTask` 告诉 `GameplayAbility` 在激活后监听一个带有负载数据的事件。事件负载和发送过程与通过事件激活 `GameplayAbilities` 相同。缺点是事件不会被 `AbilityTask` 复制，因此应仅用于 `Local Only` 和 `Server Only` 的 `GameplayAbilities`。你可能需要编写自己的 `AbilityTask` 来复制事件负载。                                                                                                                                                                                                                                                                                                                                                                         |
| 使用 `TargetData`                                | 自定义 `TargetData` 结构是一种在客户端和服务器之间传递任意数据的好方法。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 在 `OwnerActor` 或 `AvatarActor` 上存储数据      | 使用存储在 `OwnerActor`、`AvatarActor` 或其他可以获取引用的对象上的复制变量。这种方法最灵活，并且可以与通过输入绑定激活的 `GameplayAbilities` 一起使用。然而，它不能保证在使用时数据会通过复制同步。你必须提前确保这一点——这意味着如果你设置了一个复制变量，然后立即激活 `GameplayAbility`，由于潜在的包丢失，接收端的执行顺序不一定得到保证。                                                                                                                                                                                                                                                                                                                                                     |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-commit"></a>

#### 4.6.12 Ability Cost and Cooldown 能力的消耗与冷却

`GameplayAbilities` 具备可选的消耗和冷却功能。消耗是指 `ASC` 必须拥有的一定数量的 `Attributes` 才能激活 `GameplayAbility`，这是通过一个 `Instant GameplayEffect`（[`Cost GE`](#concepts-ge-cost)）实现的。冷却则是指防止 `GameplayAbility` 在冷却结束前重新激活的计时器，它通过一个 `Duration GameplayEffect`（[`Cooldown GE`](#concepts-ge-cooldown)）实现。

在 `GameplayAbility` 调用 `UGameplayAbility::Activate()` 之前，它会先调用 `UGameplayAbility::CanActivateAbility()`。这个函数会检查拥有的 `ASC` 是否能够支付消耗 (`UGameplayAbility::CheckCost()`) 并确保 `GameplayAbility` 不在冷却状态 (`UGameplayAbility::CheckCooldown()`)。

在 `GameplayAbility` 调用 `Activate()` 之后，它可以在任何时候通过 `UGameplayAbility::CommitAbility()` 来提交消耗和冷却，这个函数会调用 `UGameplayAbility::CommitCost()` 和 `UGameplayAbility::CommitCooldown()`。如果不需要同时提交消耗和冷却，设计师可以选择分别调用 `CommitCost()` 或 `CommitCooldown()`。提交消耗和冷却时会再次调用 `CheckCost()` 和 `CheckCooldown()`，这是 `GameplayAbility` 与之相关的最后失败机会。在 `GameplayAbility` 激活后，拥有的 `ASC` 的 `Attributes` 可能会发生变化，导致在提交时无法满足消耗要求。如果在提交时预测键(`Prediction Key`)有效，则可以[本地预测](#concepts-p)提交消耗和冷却。

请参阅 [`CostGE`](#concepts-ge-cost) 和 [`CooldownGE`](#concepts-ge-cooldown) 了解实现细节。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-leveling"></a>

#### 4.6.13 Leveling Up Abilities
有两种常见的方法来提升能力等级：

| 升级方法                                  | 描述                                                                                                                                                                                                               |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 撤销并重新授予新的等级                    | 从`ASC`中撤销（移除）`GameplayAbility`，并在服务器上重新授予下一级别。这会在`GameplayAbility`处于活动状态时终止它。                                                                                             |
| 增加`GameplayAbilitySpec`的等级           | 在服务器上找到`GameplayAbilitySpec`，增加其等级，并将其标记为脏数据，以便同步到拥有客户端。此方法不会在`GameplayAbility`处于活动状态时终止它。                                                               |


两种方法的主要区别在于你是否希望在升级时取消活跃的`GameplayAbilities`。根据你的`GameplayAbilities`，你很可能会使用这两种方法。我建议在你的`UGameplayAbility`子类中添加一个`bool`变量，用于指定使用哪种方法。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-sets"></a>

#### 4.6.14 Ability Sets
`GameplayAbilitySets` 是方便使用的 `UDataAsset` 类，用于保存输入绑定和角色启动时的 `GameplayAbilities` 列表，并包含授予这些 `GameplayAbilities` 的逻辑。子类还可以包括额外的逻辑或属性。Paragon 每个英雄都有一个 `GameplayAbilitySet`，其中包含了他们所有的 `GameplayAbilities`。

根据我目前的了解，我认为这个类是没有必要的。示例项目在 `GDCharacterBase` 及其子类中处理了 `GameplayAbilitySets` 的所有功能。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-batching"></a>

#### 4.6.15 Ability Batching
传统的 `Gameplay Ability` 生命周期至少涉及两个或三个从客户端到服务器的RPC调用。


1. `CallServerTryActivateAbility()`
1. `ServerSetReplicatedTargetData()` (Optional)
1. `ServerEndAbility()`

如果一个 `GameplayAbility` 在一帧内以一个原子组的方式执行所有这些操作，我们可以优化此工作流程，将所有两个或三个RPC合并为一个RPC。`GAS` 将这种RPC优化称为 `Ability Batching`（能力批处理）。使用 `Ability Batching` 的常见例子是使用击中扫描的枪支。击中扫描的枪支激活后，进行直线追踪，将[`TargetData`](#concepts-targeting-data)发送到服务器，并在一帧内以一个原子组的方式结束能力。[GASShooter](https://github.com/tranek/GASShooter) 示例项目展示了这种技术在击中扫描枪支中的应用。

半自动枪是最佳案例，它将 `CallServerTryActivateAbility()`、`ServerSetReplicatedTargetData()`（子弹命中结果）和 `ServerEndAbility()` 合并为一个RPC，而不是三个RPC。

全自动/连发枪则在第一颗子弹发射时，将 `CallServerTryActivateAbility()` 和 `ServerSetReplicatedTargetData()` 合并为一个RPC，而不是两个RPC。每发接下来的子弹都会各自发送一个 `ServerSetReplicatedTargetData()` RPC。最后，当枪停止射击时，`ServerEndAbility()` 会作为单独的RPC发送。这种情况是最坏的情况，因为我们在第一颗子弹上只节省了一个RPC，而不是两个。这种情况下，也可以通过使用[`Gameplay Event`](#concepts-ga-data)激活能力的方式来实现，这会将子弹的 `TargetData` 与 `EventPayload` 一起从客户端发送到服务器。后一种方法的缺点是 `TargetData` 必须在能力之外生成，而批处理方法是在能力内部生成 `TargetData`。

`Ability Batching` 默认在 [`ASC`](#concepts-asc) 上是禁用的。要启用 `Ability Batching`，需要重写 `ShouldDoServerAbilityRPCBatch()` 并返回 true：


```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

现在 `Ability Batching` 已经启用，在激活你希望进行批处理的能力之前，你必须首先创建一个 `FScopedServerAbilityRPCBatcher` 结构体。这个特殊的结构体会尝试在其作用域内批处理后续的任何能力。一旦 `FScopedServerAbilityRPCBatcher` 超出其作用域，激活的能力将不会尝试批处理。`FScopedServerAbilityRPCBatcher` 通过在每个可以批处理的函数中插入特殊代码来工作，这些代码会拦截RPC的发送请求，并将消息打包到批处理结构中。当 `FScopedServerAbilityRPCBatcher` 超出作用域时，它会自动在 `UAbilitySystemComponent::EndServerAbilityRPCBatch()` 中RPC这个批处理结构到服务器。服务器在 `UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)` 中接收批处理RPC。`BatchInfo` 参数将包含标志，指示能力是否应该结束、激活时是否按下了输入键，以及是否包含了 `TargetData`。这是一个很好的函数，可以设置断点来确认你的批处理是否正常工作。或者，使用cvar `AbilitySystem.ServerRPCBatching.Log 1` 来启用特殊的能力批处理日志记录。

这个机制只能在C++中完成，并且只能通过它们的 `FGameplayAbilitySpecHandle` 激活能力。


```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```

GASShooter 复用了同一个批处理的 `GameplayAbility` 来处理半自动和全自动枪械，这些枪械从不直接调用 `EndAbility()`（由一个本地专用的能力在能力外部处理，该能力管理玩家输入并根据当前的开火模式调用批处理能力）。由于所有的RPC必须发生在 `FScopedServerAbilityRPCBatcher` 的作用域内，我提供了 `EndAbilityImmediately` 参数，以便控制/管理本地专用的能力能够指定该能力是否应该批处理 `EndAbility()` 调用（适用于半自动），或者不批处理 `EndAbility()` 调用（适用于全自动），并且 `EndAbility()` 调用将在稍后的某个时间点通过单独的RPC进行。

GASShooter 暴露了一个蓝图节点，用于允许批处理能力，本地专用的能力可以使用该节点来触发批处理能力。

![Activate Batched Ability](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 Net Security Policy
`GameplayAbility` 的 `NetSecurityPolicy` 决定了一个能力应在网络的哪个位置执行。它为防止客户端尝试执行受限能力提供了保护。

| `NetSecurityPolicy`     | Description                                                                                                                                        |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ClientOrServer`        | 无安全要求。客户端或服务器都可以自由触发该能力的执行和终止。                                                |
| `ServerOnlyExecution`   | 服务器将忽略客户端请求执行此能力的请求。客户端仍然可以请求服务器取消或终止此能力。                             |
| `ServerOnlyTermination` | 服务器将忽略客户端请求取消或终止此能力的请求。客户端仍然可以请求执行此能力。                                   |
| `ServerOnly`            | 服务器控制该能力的执行和终止。服务器将忽略客户端的任何请求。                                                    |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 Ability Tasks

<a name="concepts-at-definition"></a>

### 4.7.1 Ability Task Definition

`GameplayAbilities` 只能在一个帧内执行。这本身并不提供太多灵活性。为了执行需要随时间发生的操作或响应稍后在某个时间点触发的委托，我们使用称为 `AbilityTasks` 的潜在操作。

GAS 自带许多开箱即用的 `AbilityTasks`：


GAS 自带许多开箱即用的 `AbilityTasks`：

* 用于通过 `RootMotionSource` 移动角色的任务
* 播放动画蒙太奇的任务
* 响应 `Attribute` 更改的任务
* 响应 `GameplayEffect` 更改的任务
* 响应玩家输入的任务
* 以及更多其他任务

`UAbilityTask` 的构造函数强制执行了一个硬编码的全局最大值，即最多允许 1000 个 `AbilityTasks` 同时运行。设计 `GameplayAbilities` 时需要牢记这一点，特别是在具有数百个角色同时存在于世界中的游戏（如 RTS 游戏）中。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-definition"></a>

### 4.7.2 Custom Ability Tasks

通常情况下，你将需要创建自定义的 `AbilityTasks`（使用 C++）。示例项目包含了两个自定义的 `AbilityTasks`：

1. `PlayMontageAndWaitForEvent` 是默认的 `PlayMontageAndWait` 和 `WaitGameplayEvent` `AbilityTasks` 的组合。它允许动画蒙太奇通过 `AnimNotifies` 将游戏事件发送回启动它们的 `GameplayAbility`。使用此任务可以在动画蒙太奇的特定时间点触发动作。
2. `WaitReceiveDamage` 监听 `OwnerActor` 接收伤害。当英雄受到伤害时，被动护甲堆叠的 `GameplayAbility` 会移除一层护甲。

`AbilityTasks` 由以下部分组成：
* 一个静态函数，用于创建 `AbilityTask` 的新实例
* 当 `AbilityTask` 完成其目的时广播的委托
* 一个 `Activate()` 函数，用于启动其主要任务，绑定到外部委托等
* 一个 `OnDestroy()` 函数，用于清理，包括解除绑定的外部委托
* 任何绑定到的外部委托的回调函数
* 成员变量和任何内部辅助函数

**注意:** `AbilityTasks` 只能声明一种类型的输出委托。所有输出委托必须为此类型，无论它们是否使用参数。对于未使用的委托参数，传递默认值。

`AbilityTasks` 只在运行拥有 `GameplayAbility` 的客户端或服务器上运行；然而，可以通过在 `AbilityTask` 构造函数中设置 `bSimulatedTask = true;`，重写 `virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`，并设置任何成员变量为可复制，使 `AbilityTasks` 在模拟客户端上运行。这只在少数情况下有用，如移动 `AbilityTasks`，你不想复制每一个移动变化，而是模拟整个移动 `AbilityTask`。所有的 `RootMotionSource` `AbilityTasks` 都这样做了。参见 `AbilityTask_MoveToLocation.h/.cpp` 作为示例。

如果你在 `AbilityTask` 构造函数中设置 `bTickingTask = true;` 并重写 `virtual void TickTask(float DeltaTime);`，则 `AbilityTasks` 可以 `Tick`。当你需要在帧间平滑插值值时，这非常有用。参见 `AbilityTask_MoveToLocation.h/.cpp` 作为示例。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-using"></a>

### 4.7.3 Using Ability Tasks
创建并激活一个 `AbilityTask` 的示例（摘自 `GDGA_FireGun.cpp`）：

```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

在Blueprint中，我们只需使用为`AbilityTask`创建的Blueprint节点。我们不需要手动调用`ReadyForActivation()`，因为该函数会由`Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`自动调用。`K2Node_LatentGameplayTaskCall`还会自动调用`BeginSpawningActor()`和`FinishSpawningActor()`，如果它们存在于你的`AbilityTask`类中（参见`AbilityTask_WaitTargetData`）。再次强调，`K2Node_LatentGameplayTaskCall`的这些自动处理仅限于Blueprint。在C++中，我们必须手动调用`ReadyForActivation()`、`BeginSpawningActor()`和`FinishSpawningActor()`。

![Blueprint WaitTargetData AbilityTask](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

要手动取消一个`AbilityTask`，只需在Blueprint中（称为`Async Task Proxy`）或在C++中对`AbilityTask`对象调用`EndTask()`即可。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-rms"></a>

### 4.7.4 Root Motion Source Ability Tasks 根运动源能力任务

GAS 提供了使用 `Root Motion Sources` 的 `AbilityTasks`，这些任务通过与 `CharacterMovementComponent` 相结合，用于在一段时间内移动 `Characters`，实现诸如击退、复杂跳跃、拉拽和冲刺等效果。

**注意:** 预测 `RootMotionSource` 的 `AbilityTasks` 在引擎版本 4.19 和 4.25+ 中工作正常。然而，在引擎版本 4.20-4.24 中存在预测问题；不过这些 `AbilityTasks` 仍然能够在多人游戏中完成它们的功能，只是会有一些轻微的网络校正，并且在单人游戏中能够完美运行。如果需要，可以将 [预测修复](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c) 从 4.25 版本中挑选出来，应用于定制的 4.20-4.24 引擎。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 Gameplay Cues

<a name="concepts-gc-definition"></a>

#### 4.8.1 Gameplay Cue 定义
`GameplayCues`（简称 `GC`）用于执行与游戏玩法无关的内容，如音效、粒子效果、摄像机震动等。`GameplayCues` 通常会被复制（除非明确在本地 `Executed`、`Added` 或 `Removed`）并预测。

我们通过将对应的 `GameplayTag`（必须以 `GameplayCue.` 为父名称）和事件类型（`Execute`、`Add` 或 `Remove`）发送到 `ASC` 的 `GameplayCueManager` 来触发 `GameplayCues`。`GameplayCueNotify` 对象和其他实现了 `IGameplayCueInterface` 的 `Actors` 可以根据 `GameplayCue` 的 `GameplayTag`（`GameplayCueTag`）订阅这些事件。

**注意:** 再次强调，`GameplayCue` 的 `GameplayTags` 需要以 `GameplayCue` 作为父 `GameplayTag` 开始。例如，一个有效的 `GameplayCue` `GameplayTag` 可能是 `GameplayCue.A.B.C`。

`GameplayCueNotifies` 分为两类：`Static` 和 `Actor`。它们响应不同的事件，不同类型的 `GameplayEffects` 可以触发它们。重写相应的事件以实现你的逻辑。


| `GameplayCue` 类别                                                                                                                   | 事件              | `GameplayEffect` 类型    | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` 或 `Periodic`  | 静态 `GameplayCueNotify` 在 `ClassDefaultObject` 上操作（即无实例），非常适合一次性效果，如命中冲击效果。                                                                                                                                                                                                                                                                                                                                                              |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` 或 `Remove` | `Duration` 或 `Infinite` | Actor 类型的 `GameplayCueNotify` 在 `Add` 时生成一个新实例。由于这些是实例化的，它们可以在一定时间内执行操作，直到被 `Remove`。这些非常适合循环播放的声音和粒子效果，当支持的 `Duration` 或 `Infinite` 类型的 `GameplayEffect` 被移除或手动调用 `Remove` 时，这些效果会被移除。这些还带有选项，可以管理同时允许 `Add` 的实例数量，这样多个相同效果的应用只会启动一次声音或粒子效果。 |


`GameplayCueNotifies` 理论上可以响应任何事件，但通常我们按照上述方式使用它们。


**注意:** 使用 `GameplayCueNotify_Actor` 时，确保勾选 `Auto Destroy on Remove`，否则后续调用 `Add` 该 `GameplayCueTag` 时将无法正常工作。

当使用 `ASC` 的 [Replication Mode](#concepts-asc-rm) 设置为非 `Full` 模式时，`Add` 和 `Remove` 的 `GC` 事件会在服务器玩家（监听服务器）上触发两次——一次是应用 `GE` 时触发，另一次是通过 "Minimal" 的 `NetMultiCast` 发送给客户端。然而，`WhileActive` 事件依然只会触发一次。所有事件在客户端上只会触发一次。

示例项目中包括了用于眩晕和冲刺效果的 `GameplayCueNotify_Actor`，以及用于 FireGun 弹道命中的 `GameplayCueNotify_Static`。这些 `GC` 可以通过 [本地触发](#concepts-gc-local) 而不是通过 `GE` 进行复制来进一步优化。我在示例项目中展示了初学者使用它们的方法。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-trigger"></a>

#### 4.8.2 触发 Gameplay Cues

从 `GameplayEffect` 内部，当它成功应用时（未被标签或免疫阻止），填写所有应触发的 `GameplayCues` 的 `GameplayTags`。

![通过GameplayEffect触发GameplayCue](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` 提供了在 Blueprint 中 `Execute`、`Add` 或 `Remove` `GameplayCues` 的节点。

![通过GameplayAbility触发GameplayCue](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

在 C++ 中，你可以直接调用 `ASC` 上的函数（或在你的 `ASC` 子类中将其暴露给 Blueprint）：

```c++
/** GameplayCues can also come on their own. These take an optional effect context to pass through hit result, etc */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Add a persistent gameplay cue */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Remove a persistent gameplay cue */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** Removes any GameplayCue added on its own, i.e. not as part of a GameplayEffect. */
void RemoveAllGameplayCues();
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-local"></a>

#### 4.8.3 Local Gameplay Cues
从 `GameplayAbilities` 和 `ASC` 中触发 `GameplayCues` 的公开函数默认是被复制的。每个 `GameplayCue` 事件都是一个多播 RPC。这可能会导致大量的 RPC 调用。GAS 还限制了每个网络更新中最多两个相同的 `GameplayCue` RPC。我们可以通过使用本地 `GameplayCues` 来避免这种情况。本地 `GameplayCues` 只在各个客户端上 `Execute`、`Add` 或 `Remove`。

可以使用本地 `GameplayCues` 的场景：
* 弹道物体的碰撞
* 近战攻击的碰撞
* 从动画蒙太奇中触发的 `GameplayCues`

你应该在 `ASC` 子类中添加的本地 `GameplayCue` 函数：


```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

如果一个 `GameplayCue` 是本地 `Added` 的，那么它也应该本地 `Removed`。如果它是通过复制 `Added` 的，那么它也应该通过复制 `Removed`。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-parameters"></a>

#### 4.8.4 Gameplay Cue 参数

`GameplayCues` 会接收一个 `FGameplayCueParameters` 结构体作为参数，包含额外的信息。如果你从 `GameplayAbility` 或 `ASC` 中的函数手动触发 `GameplayCue`，那么你必须手动填充传递给 `GameplayCue` 的 `GameplayCueParameters` 结构体。如果 `GameplayCue` 是由 `GameplayEffect` 触发的，那么以下变量会自动填充到 `GameplayCueParameters` 结构体中：

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude（如果 `GameplayEffect` 在 `GameplayCue` 标签容器上方的下拉菜单中为幅度选择了 `Attribute`，并且有相应的 `Modifier` 影响该 `Attribute`）

`GameplayCueParameters` 结构体中的 `SourceObject` 变量在手动触发 `GameplayCue` 时，可能是传递任意数据到 `GameplayCue` 的一个不错的位置。

**注意：** 参数结构体中的一些变量如 `Instigator` 可能已经存在于 `EffectContext` 中。`EffectContext` 还可以包含一个 `FHitResult`，用于指定在世界中生成 `GameplayCue` 的位置。继承 `EffectContext` 可能是传递更多数据到 `GameplayCues` 的一个好方法，特别是那些由 `GameplayEffect` 触发的 `GameplayCues`。

有关更多信息，请参阅 [`UAbilitySystemGlobals`](#concepts-asg) 中填充 `GameplayCueParameters` 结构体的三个函数。它们是虚函数，因此你可以重写它们以自动填充更多信息。


```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-manager"></a>


#### 4.8.5 Gameplay Cue Manager

默认情况下，`GameplayCueManager` 会扫描整个游戏目录中的 `GameplayCueNotifies` 并在游戏运行时将它们加载到内存中。我们可以通过在 `DefaultGame.ini` 中设置路径来更改 `GameplayCueManager` 扫描的路径。



```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

我们确实希望 `GameplayCueManager` 扫描并找到所有的 `GameplayCueNotifies`；然而，我们并不希望它在游戏启动时异步加载每一个 `GameplayCueNotify`。这样做会将每个 `GameplayCueNotify` 及其引用的声音和粒子效果都加载到内存中，无论它们是否在某个关卡中使用。在像 Paragon 这样的大型游戏中，这可能会导致数百兆字节的不必要资源占用内存，并在启动时造成卡顿和游戏冻结。

一种替代方案是在游戏中仅在 `GameplayCues` 被触发时异步加载它们。这可以缓解不必要的内存使用和异步加载所有 `GameplayCue` 时可能导致的游戏硬性冻结问题，代价是在游戏过程中首次触发某个特定 `GameplayCue` 时可能会出现效果延迟。如果使用 SSD，这种潜在的延迟几乎不存在。我没有在 HDD 上进行测试。如果在 UE 编辑器中使用此选项，首次加载 `GameplayCues` 时可能会出现轻微的卡顿或冻结，尤其是在编辑器需要编译粒子系统时。在发布版本中，这不是问题，因为粒子系统已经编译完成。

首先，我们必须子类化 `UGameplayCueManager` 并告诉 `AbilitySystemGlobals` 类在 `DefaultGame.ini` 中使用我们自定义的 `UGameplayCueManager` 子类。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

在我们的 `UGameplayCueManager` 子类中，重写 `ShouldAsyncLoadRuntimeObjectLibraries()` 函数。


```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-prevention"></a>

#### 4.8.6 防止 Gameplay Cues 触发

有时我们不希望 `GameplayCues` 被触发。例如，如果我们阻挡了一次攻击，我们可能不希望播放与伤害 `GameplayEffect` 相关的命中效果，或者希望播放一个自定义效果。我们可以在 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 中通过调用 `OutExecutionOutput.MarkGameplayCuesHandledManually()` 来实现这一点，然后手动将我们的 `GameplayCue` 事件发送到 `Target` 或 `Source` 的 `ASC`。

如果你永远不希望任何 `GameplayCues` 在特定的 `ASC` 上触发，你可以设置 `AbilitySystemComponent->bSuppressGameplayCues = true;`。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 Gameplay Cue Batching
Each `GameplayCue` triggered is an unreliable NetMulticast RPC. In situations where we fire multiple `GCs` at the same time, there are a few optimization methods to condense them down into one RPC or save bandwidth by sending less data.

<a name="concepts-gc-batching-manualrpc"></a>

##### 4.8.7.1 手动 RPC

假设你有一把射出八发子弹的霰弹枪。这意味着会有八次轨迹和命中 `GameplayCues`。 [GASShooter](https://github.com/tranek/GASShooter) 采用了一种懒惰的方法，将它们组合成一个 RPC，通过将所有轨迹信息存入 [`EffectContext`](#concepts-ge-ec) 作为 [`TargetData`](#concepts-targeting-data)。虽然这减少了 RPC 的次数（从八个减少到一个），但在一次 RPC 中仍然会发送大量数据（约 500 字节）。一种更优化的方法是使用自定义结构体发送 RPC，在其中有效地编码命中位置，或者你可以使用一个随机种子号来在接收端重现/近似命中位置。客户端然后会解包这个自定义结构体并将其转换为 [本地执行的 `GameplayCues`](#concepts-gc-local)。

实现方法如下：
1. 声明一个 `FScopedGameplayCueSendContext`。这会抑制 `UGameplayCueManager::FlushPendingCues()` 的调用，直到它超出范围为止，意味着所有的 `GameplayCues` 都会被排队，直到 `FScopedGameplayCueSendContext` 超出范围。
2. 重写 `UGameplayCueManager::FlushPendingCues()`，将可以一起批处理的 `GameplayCues` 合并到你的自定义结构体中，并通过 RPC 将其发送给客户端。
3. 客户端接收自定义结构体，并将其解包为本地执行的 `GameplayCues`。

当你需要为 `GameplayCues` 提供特定参数而这些参数不适合 `GameplayCueParameters` 提供的内容时，并且你不想将它们添加到 `EffectContext` 中（如伤害数值、暴击指示器、护盾破碎指示器、致命击打指示器等），你也可以使用这种方法。

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>

##### 4.8.7.2 在一个 GE 上的多个 GCs

所有附加在 `GameplayEffect` 上的 `GameplayCues` 都会在一个 RPC 中发送。默认情况下，`UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()` 将在不考虑 `ASC` 的 `Replication Mode` 的情况下，通过不可靠的 NetMulticast 发送整个 `GameplayEffectSpec`（但会转换为 `FGameplayEffectSpecForRPC`）。这可能会占用大量带宽，具体取决于 `GameplayEffectSpec` 中的内容。我们可以通过设置 cvar `AbilitySystem.AlwaysConvertGESpecToGCParams 1` 来优化这一点。这样会将 `GameplayEffectSpecs` 转换为 `FGameplayCueParameter` 结构体，并通过 RPC 发送，而不是整个 `FGameplayEffectSpecForRPC`。这可能会节省带宽，但也会丢失一些信息，具体取决于 `GESpec` 如何转换为 `GameplayCueParameters` 以及你的 `GCs` 需要了解的内容。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-events"></a>


#### 4.8.8 Gameplay Cue Events
`GameplayCues` 会响应特定的 `EGameplayCueEvents`：

| `EGameplayCueEvent` | 描述                                                                                                                                                                                                                                                                                                                                                  |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `OnActive`          | 当 `GameplayCue` 被激活（添加）时调用。                                                                                                                                                                                                                                                                                                                   |
| `WhileActive`       | 当 `GameplayCue` 处于活动状态时调用，即使它实际上并不是刚刚应用的（例如，正在进行中）。这不是 `Tick`！当 `GameplayCueNotify_Actor` 被添加或变得相关时，它像 `OnActive` 一样只调用一次。如果需要 `Tick()`，请使用 `GameplayCueNotify_Actor` 的 `Tick()`，毕竟它是一个 `AActor`。 |
| `Removed`           | 当 `GameplayCue` 被移除时调用。响应此事件的 Blueprint `GameplayCue` 函数是 `OnRemove`。                                                                                                                                                                                                                                                                |
| `Executed`          | 当 `GameplayCue` 被执行时调用：即时效果或周期性 `Tick()`。响应此事件的 Blueprint `GameplayCue` 函数是 `OnExecute`。                                                                                                                                                                                                                                   |


使用 `OnActive` 处理 `GameplayCue` 开始时发生的任何事件，但如果晚到者错过也没关系。使用 `WhileActive` 处理 `GameplayCue` 中的持续效果，晚到者也能看到。例如，如果你有一个 `GameplayCue` 用于 MOBA 游戏中一个塔楼结构爆炸，你会将初始的爆炸粒子系统和爆炸声音放在 `OnActive` 中，而将任何残留的持续火焰粒子或声音放在 `WhileActive` 中。在这种情况下，晚到者不需要重新播放 `OnActive` 中的初始爆炸，但你会希望他们能看到爆炸后地面上持久的、循环的火焰效果，这些效果在 `WhileActive` 中处理。`OnRemove` 应该清理在 `OnActive` 和 `WhileActive` 中添加的任何内容。每次 Actor 进入 `GameplayCueNotify_Actor` 的相关范围时，`WhileActive` 都会被调用。每次 Actor 离开 `GameplayCueNotify_Actor` 的相关范围时，`OnRemove` 都会被调用。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-reliability"></a>

#### 4.8.9 Gameplay Cue 可靠性

一般来说，`GameplayCues` 被认为是不可靠的，因此不适用于直接影响游戏玩法的内容。

**已执行的 `GameplayCues`：** 这些 `GameplayCues` 通过不可靠的多播应用，并且总是不可靠的。

**从 `GameplayEffects` 应用的 `GameplayCues`：**
* 自主代理可靠地接收 `OnActive`、`WhileActive` 和 `OnRemove`  
`FActiveGameplayEffectsContainer::NetDeltaSerialize()` 调用 `UAbilitySystemComponent::HandleDeferredGameplayCues()` 来调用 `OnActive` 和 `WhileActive`。`FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()` 发出 `OnRemove` 调用。
* 模拟代理可靠地接收 `WhileActive` 和 `OnRemove`  
`UAbilitySystemComponent::MinimalReplicationGameplayCues` 的复制调用 `WhileActive` 和 `OnRemove`。`OnActive` 事件由不可靠的多播调用。

**没有 `GameplayEffect` 的 `GameplayCues`：**
* 自主代理可靠地接收 `OnRemove`  
`OnActive` 和 `WhileActive` 事件由不可靠的多播调用。
* 模拟代理可靠地接收 `WhileActive` 和 `OnRemove`  
`UAbilitySystemComponent::MinimalReplicationGameplayCues` 的复制调用 `WhileActive` 和 `OnRemove`。`OnActive` 事件由不可靠的多播调用。

如果你需要在 `GameplayCue` 中的某些内容是“可靠”的，那么从 `GameplayEffect` 中应用它，并使用 `WhileActive` 添加特效，使用 `OnRemove` 移除特效。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asg"></a>

### 4.9 Ability System Globals

[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) 类持有关于 GAS 的全局信息。大多数变量可以从 `DefaultGame.ini` 中设置。通常，你不需要与这个类互动，但你应该了解它的存在。如果你需要对子类化诸如 [`GameplayCueManager`](#concepts-gc-manager) 或 [`GameplayEffectContext`](#concepts-ge-context) 之类的东西，你必须通过 `AbilitySystemGlobals` 来实现。

要对子类化 `AbilitySystemGlobals`，请在 `DefaultGame.ini` 中设置类名：

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>

#### 4.9.1 InitGlobalData()

在 UE 4.24 到 5.2 之间，需要调用 `UAbilitySystemGlobals::Get().InitGlobalData()` 才能使用 [`TargetData`](#concepts-targeting-data)，否则你会遇到与 `ScriptStructCache` 相关的错误，且客户端将从服务器断开连接。这个函数在项目中只需要调用一次。Fortnite 从 `UAssetManager::StartInitialLoading()` 调用它，而 Paragon 从 `UEngine::Init()` 调用它。我发现将其放在 `UAssetManager::StartInitialLoading()` 是一个不错的选择，如示例项目所示。我认为这是一段模板代码，你应该将其复制到你的项目中，以避免 `TargetData` 的问题。从 5.3 开始，它会自动调用。

如果在使用 `AbilitySystemGlobals` 的 `GlobalAttributeSetDefaultsTableNames` 时遇到崩溃，你可能需要像 Fortnite 一样在 `AssetManager` 或 `GameInstance` 中稍后调用 `UAbilitySystemGlobals::Get().InitGlobalData()`。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 预测

GAS 默认支持客户端预测；然而，它并不预测所有内容。GAS 中的客户端预测意味着客户端不需要等待服务器的许可来激活 `GameplayAbility` 和应用 `GameplayEffects`。它可以“预测”服务器会允许它这样做，并预测将 `GameplayEffects` 应用到哪些目标上。然后服务器在客户端激活后经过网络延迟时间运行 `GameplayAbility`，并告诉客户端他在预测中是否正确。如果客户端在任何预测中出错，它将“回滚”其“错误预测”的更改以匹配服务器。

GAS 相关预测的权威来源是插件源代码中的 `GameplayPrediction.h`。

Epic 的思维方式是只预测你“能成功”的内容。例如，Paragon 和 Fortnite 不预测伤害。它们很可能使用 [`ExecutionCalculations`](#concepts-ge-ec) 进行伤害计算，这些计算本身无法预测。这并不是说你不能尝试预测某些东西，比如伤害。如果你做到并且效果很好，那当然很好。

> ... 我们也不完全支持一种“预测所有内容：无缝且自动”的解决方案。我们仍然认为玩家预测最好保持在最低限度（意思是：预测你能成功的最少量内容）。

*Dave Ratti 在 Epic 新的 [网络预测插件](#concepts-p-npp) 中的评论*

**预测的内容：**
> * 能力激活
> * 触发事件
> * GameplayEffect 应用：
>   * 属性修改（例外：执行操作目前无法预测，只有属性修饰符）
>   * GameplayTag 修改
> * Gameplay Cue 事件（包括来自预测性 gameplay effect 和其自身的事件）
> * 动作
> * 移动（内置于 UE 的 UCharacterMovement）

**未预测的内容：**
> * GameplayEffect 移除
> * GameplayEffect 周期性效果（例如 dot 计时）

*来自 `GameplayPrediction.h`*

虽然我们可以预测 `GameplayEffect` 的应用，但不能预测 `GameplayEffect` 的移除。我们可以通过预测相反效果来解决这个限制。例如，如果我们预测移动速度减慢 40%，我们可以通过应用 40% 的移动速度增益来预测性地移除它。然后同时移除这两个 `GameplayEffects`。这并不适用于所有场景，仍然需要支持预测 `GameplayEffect` 移除。Dave Ratti 表示希望在 [GAS 的未来版本](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) 中加入这一功能。

由于我们无法预测 `GameplayEffects` 的移除，因此无法完全预测 `GameplayAbility` 的冷却时间，也没有反向 `GameplayEffect` 的解决方法。服务器的复制 `Cooldown GE` 将存在于客户端，任何尝试绕过这一点（例如使用 `Minimal` 复制模式）都会被服务器拒绝。这意味着延迟较高的客户端需要更长时间才能告知服务器进入冷却状态，并接收服务器的 `Cooldown GE` 的移除。这意味着延迟较高的玩家将比延迟较低的玩家有更低的射速，给低延迟玩家带来劣势。Fortnite 通过使用自定义记账来避免这个问题，而不是使用 `Cooldown GEs`。

关于预测伤害，我个人不推荐这样做，尽管它是大多数人在开始使用 GAS 时最先尝试的内容之一。我特别不推荐尝试预测死亡。虽然你可以预测伤害，但这很棘手。如果你错误地预测了伤害应用，玩家会看到敌人的生命值跳回去。如果你尝试预测死亡，这尤其会显得尴尬和令人沮丧。比如你错误地预测了一个 `Character` 的死亡，它开始变成 ragdoll，但当服务器修正时，它又停止 ragdoll 并继续对你开火。

**注意：** `Instant` `GameplayEffects`（如 `Cost GEs`）可以在你自己身上无缝预测，预测对其他角色的 `Instant` `Attribute` 更改会在其属性中显示一个简短的异常或“闪烁”。预测的 `Instant` `GameplayEffects` 实际上被视为 `Infinite` `GameplayEffects`，以便在错误预测时可以回滚。当服务器的 `GameplayEffect` 被应用时，可能会存在两个相同的 `GameplayEffect`，导致 `Modifier` 被应用两次或不应用一段时间。最终它会自行纠正，但有时闪烁对玩家来说是显而易见的。

GAS 的预测实现试图解决的问题：
> 1. “我可以这样做吗？” 基本的预测协议。
> 2. “撤销” 当预测失败时如何撤销副作用。
> 3. “重做” 如何避免重新播放我们在本地预测的副作用，同时这些副作用也被服务器复制。
> 4. “完整性” 如何确保我们/确实/预测了所有副作用。
> 5. “依赖关系” 如何管理依赖的预测和预测事件链。
> 6. “覆盖” 如何预测性地覆盖服务器复制/拥有的状态。

*来自 `GameplayPrediction.h`*


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-key"></a>

#### 4.10.1 Prediction Key 预测键

GAS 的预测基于 `Prediction Key` 的概念，这是一个整数标识符，客户端在激活 `GameplayAbility` 时生成。

* 客户端在激活 `GameplayAbility` 时生成一个预测键。这是 `Activation Prediction Key`。
* 客户端通过 `CallServerTryActivateAbility()` 将这个预测键发送给服务器。
* 客户端将这个预测键添加到它应用的所有 `GameplayEffects` 中，只要预测键仍然有效。
* 客户端的预测键超出范围。相同 `GameplayAbility` 中进一步的预测效果需要一个新的 [Scoped Prediction Window](#concepts-p-windows)。

* 服务器从客户端接收预测键。
* 服务器将这个预测键添加到它应用的所有 `GameplayEffects` 中。
* 服务器将预测键复制回客户端。

* 客户端从服务器接收带有预测键的复制 `GameplayEffects`。如果任何复制的 `GameplayEffects` 与客户端使用相同预测键应用的 `GameplayEffects` 匹配，则预测是正确的。目标上将暂时存在两个 `GameplayEffect` 的副本，直到客户端移除其预测的副本。
* 客户端从服务器接收到预测键。这是 `Replicated Prediction Key`。这个预测键现在被标记为过时。
* 客户端移除所有使用现在过时的复制预测键创建的 `GameplayEffects`。服务器复制的 `GameplayEffects` 将继续存在。任何客户端添加的且没有从服务器接收到匹配复制版本的 `GameplayEffects` 都是预测错误的。

预测键在 `GameplayAbilities` 中的原子指令组“窗口”中从激活预测键的激活开始保证有效。你可以把它看作在一个帧内仅有效。任何来自延迟操作 `AbilityTasks` 的回调将不再具有有效的预测键，除非 `AbilityTask` 有一个内置的同步点，这会生成一个新的 [Scoped Prediction Window](#concepts-p-windows)。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-windows"></a>

#### 4.10.2 在能力中创建新的预测窗口

要在 `AbilityTasks` 的回调中预测更多操作，我们需要创建一个新的 Scoped Prediction Window 和新的 Scoped Prediction Key。这有时被称为客户端和服务器之间的同步点。一些 `AbilityTasks`（如所有与输入相关的任务）具有内置功能来创建新的 scoped prediction window，这意味着 `AbilityTasks` 的回调中的原子代码有一个有效的 scoped prediction key 可供使用。其他任务，如 `WaitDelay` 任务，则没有内置代码来为其回调创建新的 scoped prediction window。如果你需要在 `AbilityTask`（如 `WaitDelay`）之后预测操作，而该任务没有内置代码来创建 scoped prediction window，我们必须手动使用 `WaitNetSync` `AbilityTask` 并选择 `OnlyServerWait` 选项来实现。当客户端遇到带有 `OnlyServerWait` 的 `WaitNetSync` 时，它会基于 `GameplayAbility` 的激活预测键生成一个新的 scoped prediction key，将其 RPC 发送到服务器，并将其添加到它应用的任何新的 `GameplayEffects` 中。当服务器遇到带有 `OnlyServerWait` 的 `WaitNetSync` 时，它会等待从客户端接收新的 scoped prediction key 后再继续。这个 scoped prediction key 的行为与激活预测键相同——应用于 `GameplayEffects` 并复制回客户端以标记为过时。scoped prediction key 有效直到超出范围，这意味着 scoped prediction window 已关闭。因此，再次强调，只有原子操作，而不是延迟操作，才能使用新的 scoped prediction key。

你可以创建尽可能多的 scoped prediction windows。

如果你希望将同步点功能添加到自定义的 `AbilityTasks` 中，请查看输入相关任务如何实质性地将 `WaitNetSync` `AbilityTask` 代码注入其中。

**注意：** 使用 `WaitNetSync` 时，这会阻止服务器的 `GameplayAbility` 继续执行，直到它从客户端收到消息。这可能会被恶意用户利用，他们通过破解游戏故意延迟发送新的 scoped prediction key。虽然 Epic 只是偶尔使用 `WaitNetSync`，但如果这是你的关注点，建议你考虑构建一个新的 `AbilityTask` 版本，该版本具有自动继续而不依赖客户端的延迟。

示例项目在 Sprint `GameplayAbility` 中使用 `WaitNetSync`，每次应用耐力消耗时都会创建一个新的 scoped prediction window，以便我们可以预测它。理想情况下，我们希望在应用消耗和冷却时间时有一个有效的预测键。

如果你有一个预测的 `GameplayEffect` 在拥有客户端上播放了两次，你的预测键已经过时，你正在经历“重做”问题。你通常可以通过在应用 `GameplayEffect` 之前放置一个带有 `OnlyServerWait` 的 `WaitNetSync` `AbilityTask` 来解决此问题，以创建一个新的 scoped prediction key。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-spawn"></a>

#### 4.10.3 Predictively Spawning Actors 预测性地生成 Actors

在客户端上预测性地生成 `Actors` 是一个高级话题。GAS 默认并不提供处理这种情况的功能（`SpawnActor` `AbilityTask` 仅在服务器上生成 `Actor`）。关键概念是在客户端和服务器上都生成一个复制的 `Actor`。

如果 `Actor` 只是装饰性的或不具备任何游戏玩法目的，简单的解决方案是重写 `Actor` 的 `IsNetRelevantFor()` 函数，以限制服务器向拥有客户端复制。拥有客户端将拥有其本地生成的版本，而服务器和其他客户端将拥有服务器复制的版本。

```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

如果生成的 `Actor` 影响游戏玩法，比如需要预测伤害的投射物，那么你需要使用超出本文档范围的高级逻辑。查看 Epic Games 的 GitHub 上 UnrealTournament 如何预测性地生成投射物。他们在拥有客户端上仅生成了一个虚拟投射物，并与服务器的复制投射物同步。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-future"></a>

#### 4.10.4 Future of Prediction in GAS GAS 中的预测未来

`GameplayPrediction.h` 表示，未来可能会添加预测 `GameplayEffect` 移除和周期性 `GameplayEffects` 的功能。

Epic 的 Dave Ratti [表达了兴趣](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) 解决预测冷却时间的 `latency reconciliation` 问题，从而减少高延迟玩家与低延迟玩家之间的差距。

Epic 的新 [`Network Prediction` 插件](#concepts-p-npp) 预计将像之前的 `CharacterMovementComponent` 一样与 GAS 完全互操作。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-p-npp"></a>

#### 4.10.5 Network Prediction Plugin 网络预测插件

Epic 最近开始了一项替换 `CharacterMovementComponent` 的新 `Network Prediction` 插件的计划。该插件仍处于非常早期的阶段，但可以在 Unreal Engine GitHub 上获得非常早期的访问权限。目前还不清楚它会在哪个未来版本的引擎中首次推出实验性测试版。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 目标选择

<a name="concepts-targeting-data"></a>
#### 4.11.1 目标数据

[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html) 是一个用于网络传递的通用目标数据结构。`TargetData` 通常包含 `AActor`/`UObject` 引用、`FHitResults` 以及其他通用的位置信息/方向信息/起点信息。然而，你可以通过子类化它来放入几乎任何你想要的数据，以简单的方式 [在客户端和服务器之间传递数据](#concepts-ga-data)。基本结构 `FGameplayAbilityTargetData` 不应该直接使用，而是应该进行子类化。`GAS` 提供了一些预定义的 `FGameplayAbilityTargetData` 结构，位于 `GameplayAbilityTargetTypes.h`。

`TargetData` 通常由 [`Target Actors`](#concepts-targeting-actors) 生成或 **手动创建**，然后通过 [`EffectContext`](#concepts-ge-context) 被 [`AbilityTasks`](#concepts-at) 和 [`GameplayEffects`](#concepts-ge) 使用。由于在 `EffectContext` 中，[`Executions`](#concepts-ge-ec)、[`MMCs`](#concepts-ge-mmc)、[`GameplayCues`](#concepts-gc) 以及 [`AttributeSet`](#concepts-as) 后端的函数可以访问 `TargetData`。

我们通常不会直接传递 `FGameplayAbilityTargetData`，而是使用 [`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html)，它内部有一个指向 `FGameplayAbilityTargetData` 的 TArray 指针。这个中间结构提供了对 `TargetData` 的多态支持。

一个从 `FGameplayAbilityTargetData` 继承的示例：

```c++
USTRUCT(BlueprintType)
struct MYGAME_API FGameplayAbilityTargetData_CustomData : public FGameplayAbilityTargetData
{
    GENERATED_BODY()
public:

    FGameplayAbilityTargetData_CustomData()
    { }

    UPROPERTY()
    FName CoolName = NAME_None;

    UPROPERTY()
    FPredictionKey MyCoolPredictionKey;

    // This is required for all child structs of FGameplayAbilityTargetData
    virtual UScriptStruct* GetScriptStruct() const override
    {
    	return FGameplayAbilityTargetData_CustomData::StaticStruct();
    }

	// This is required for all child structs of FGameplayAbilityTargetData
    bool NetSerialize(FArchive& Ar, class UPackageMap* Map, bool& bOutSuccess)
    {
	    // The engine already defined NetSerialize for FName & FPredictionKey, thanks Epic!
        CoolName.NetSerialize(Ar, Map, bOutSuccess);
        MyCoolPredictionKey.NetSerialize(Ar, Map, bOutSuccess);
        bOutSuccess = true;
        return true;
    }
}

template<>
struct TStructOpsTypeTraits<FGameplayAbilityTargetData_CustomData> : public TStructOpsTypeTraitsBase2<FGameplayAbilityTargetData_CustomData>
{
	enum
	{
        WithNetSerializer = true // This is REQUIRED for FGameplayAbilityTargetDataHandle net serialization to work
	};
};
```
For adding the target data to a handle:
```c++
UFUNCTION(BlueprintPure)
FGameplayAbilityTargetDataHandle MakeTargetDataFromCustomName(const FName CustomName)
{
	// Create our target data type, 
	// Handle's automatically cleanup and delete this data when the handle is destructed, 
	// if you don't add this to a handle then be careful because this deals with memory management and memory leaks so its safe to just always add it to a handle at some point in the frame!
	FGameplayAbilityTargetData_CustomData* MyCustomData = new FGameplayAbilityTargetData_CustomData();
	// Setup the struct's information to use the inputted name and any other changes we may want to do
	MyCustomData->CoolName = CustomName;
	
	// Make our handle wrapper for Blueprint usage
	FGameplayAbilityTargetDataHandle Handle;
	// Add the target data to our handle
	Handle.Add(MyCustomData);
	// Output our handle to Blueprint
	return Handle
}
```

为了获取值，需要进行类型安全检查，因为从句柄的目标数据中获取值的唯一方法是使用通用的 C/C++ 强制转换，这 **不是** 类型安全的，可能导致对象切片和崩溃。对于类型检查，有多种方法（你可以按照自己的方式），但以下是两种常见的方法：

- **Gameplay Tag(s)**: 你可以使用子类层次结构，在特定的代码架构功能发生时，知道可以强制转换为基类类型，并获取其 gameplay tag(s)，然后与这些标签进行比较以进行继承类的强制转换。
  
- **Script Struct 和 Static Structs**: 你可以直接进行类比较（这可能涉及大量的 IF 语句或创建一些模板函数），以下是执行此操作的示例，但基本上你可以从任何 `FGameplayAbilityTargetData` 中获取脚本结构（这是一种作为 `USTRUCT` 的优势，并要求任何继承的类在 `GetScriptStruct` 中指定结构类型），并比较是否是你要找的类型。以下是使用这些函数进行类型检查的示例：

```c++
UFUNCTION(BlueprintPure)
FName GetCoolNameFromTargetData(const FGameplayAbilityTargetDataHandle& Handle, const int Index)
{   
    // NOTE, there is two versions of this '::Get(int32 Index)' function; 
    // 1) const version that returns 'const FGameplayAbilityTargetData*', good for reading target data values 
    // 2) non-const version that returns 'FGameplayAbilityTargetData*', good for modifying target data values
    FGameplayAbilityTargetData* Data = Handle.Get(Index); // This will valid check the index for you 
    
    // Valid check we have something to use, null data means nothing to cast for
    if(Data == nullptr)
    {
       	return NAME_None;
    }
    // This is basically the type checking pass, static_cast does not have type safety, this is why we do this check.
    // If we don't do this then it will object slice the struct and thus we have no way of making sure its that type.
    if(Data->GetScriptStruct() == FGameplayAbilityTargetData_CustomData::StaticStruct())
    {
        // Here is when you would do the cast because we know its the correct type already
        FGameplayAbilityTargetData_CustomData* CustomData = static_cast<FGameplayAbilityTargetData_CustomData*>(Data);    
        return CustomData->CoolName;
    }
    return NAME_None;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-actors"></a>

#### 4.11.2 Target Actors

`GameplayAbilities` 使用 [`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html) 与 `WaitTargetData` `AbilityTask` 来可视化和捕捉来自世界的目标信息。`TargetActors` 可以选择性地使用 [`GameplayAbilityWorldReticles`](#concepts-targeting-reticles) 来显示当前的目标。确认后，目标信息作为 [`TargetData`](#concepts-targeting-data) 返回，然后可以传递到 `GameplayEffects` 中。

`TargetActors` 基于 `AActor`，因此它们可以具有任何类型的可见组件来表示 **在哪里** 和 **如何** 定位目标，例如静态网格或 decal。静态网格可以用来可视化角色将建造的物体的位置。Decals 可以用来显示地面的效果区域。示例项目使用 [`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html) 和地面的 decal 来表示 Meteor 能力的伤害范围。它们也可以不显示任何东西。例如，对于瞬间追踪目标的 hitscan 武器（如在 [GASShooter](https://github.com/tranek/GASShooter) 中使用的那样），显示任何东西都没有意义。

`TargetActors` 使用基本的 trace 或碰撞重叠来捕获目标信息，并根据 `TargetActor` 实现将结果转换为 `FHitResults` 或 `AActor` 数组到 `TargetData`。`WaitTargetData` `AbilityTask` 通过其 `TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` 参数确定何时确认目标。当 **不** 使用 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant` 时，`TargetActor` 通常在 `Tick()` 上执行 trace/overlap 并根据其实现更新其位置到 `FHitResult`。尽管它在 `Tick()` 上执行 trace/overlap，但由于未复制，它通常不会造成太大问题，并且你通常不会同时运行多个（虽然你可以运行多个）`TargetActor`。只需注意，它使用 `Tick()`，一些复杂的 `TargetActors` 可能会在 `Tick()` 上做很多事情，例如 GASShooter 中火箭发射器的辅助能力。尽管在 `Tick()` 上追踪对客户端非常响应，但如果性能影响过大，你可以考虑降低 `TargetActor` 的 tick 率。在 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant>` 的情况下，`TargetActor` 会立即生成，产生 `TargetData`，然后销毁。`Tick()` 不会被调用。


| `EGameplayTargetingConfirmation::Type` | When targets are confirmed                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`                              | 目标确认瞬间发生，无需特殊逻辑或用户输入来决定何时“发射”。                                                                                                                                                                                                                                                                                                           |
| `UserConfirmed`                        | 当用户确认目标时，目标才会被确认。这可以通过将能力绑定到 `Confirm` 输入或调用 `UAbilitySystemComponent::TargetConfirm()` 实现。`TargetActor` 也会响应绑定的 `Cancel` 输入或调用 `UAbilitySystemComponent::TargetCancel()` 来取消目标确认。                                                                        |
| `Custom`                               | GameplayTargeting 能力负责决定目标数据何时准备好，通过调用 `UGameplayAbility::ConfirmTaskByInstanceName()` 实现。`TargetActor` 也会响应 `UGameplayAbility::CancelTaskByInstanceName()` 以取消目标确认。                                                                                                        |
| `CustomMulti`                          | GameplayTargeting 能力负责决定目标数据何时准备好，通过调用 `UGameplayAbility::ConfirmTaskByInstanceName()` 实现。`TargetActor` 也会响应 `UGameplayAbility::CancelTaskByInstanceName()` 以取消目标确认。数据生成后不应结束 `AbilityTask`。                                                                                 |


不是每种 `EGameplayTargetingConfirmation::Type` 都被所有的 `TargetActor` 支持。例如，`AGameplayAbilityTargetActor_GroundTrace` 不支持 `Instant` 确认。

`WaitTargetData` `AbilityTask` 需要一个 `AGameplayAbilityTargetActor` 类作为参数，并在每次激活 `AbilityTask` 时生成一个实例，`AbilityTask` 结束时销毁 `TargetActor`。`WaitTargetDataUsingActor` `AbilityTask` 接受一个已经生成的 `TargetActor`，但在 `AbilityTask` 结束时仍会销毁它。这两种 `AbilityTasks` 的效率较低，因为它们要么每次使用时生成新的 `TargetActor`，要么需要新的 `TargetActor`。这些 `AbilityTasks` 对于原型设计很有用，但在生产环境中，你可能需要考虑优化，如果你有不断生成 `TargetData` 的情况，例如自动步枪。GASShooter 有一个自定义的 [`AGameplayAbilityTargetActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h) 子类和一个全新的 [`WaitTargetDataWithReusableActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask`，允许你在不销毁的情况下重用 `TargetActor`。

默认情况下，`TargetActors` 不会进行复制；然而，如果在你的游戏中显示本地玩家的目标位置是有意义的，你可以使其进行复制。它们包括与服务器通过 `WaitTargetData` `AbilityTask` 的 RPC 进行通信的默认功能。如果 `TargetActor` 的 `ShouldProduceTargetDataOnServer` 属性设置为 `false`，则客户端将在确认时通过 `CallServerSetReplicatedTargetData()` 将其 `TargetData` RPC 到服务器，`UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` 中调用。如果 `ShouldProduceTargetDataOnServer` 为 `true`，客户端将在 `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` 中发送一个通用确认事件 `EAbilityGenericReplicatedEvent::GenericConfirm` 的 RPC 到服务器，服务器在接收到 RPC 后进行跟踪或重叠检查，以在服务器上生成数据。如果客户端取消目标，它将在 `UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback()` 中发送一个通用取消事件 `EAbilityGenericReplicatedEvent::GenericCancel` 的 RPC 到服务器。正如你所看到的，`TargetActor` 和 `WaitTargetData` `AbilityTask` 上都有很多委托。`TargetActor` 响应输入以生成和广播 `TargetData` 准备、确认或取消委托。`WaitTargetData` 监听 `TargetActor` 的 `TargetData` 准备、确认和取消委托，并将这些信息传递回 `GameplayAbility` 和服务器。如果你将 `TargetData` 发送到服务器，你可能需要在服务器上进行验证，以确保 `TargetData` 看起来合理，以防止作弊。直接在服务器上生成 `TargetData` 可以完全避免此问题，但可能会导致拥有客户端的预测错误。

根据你使用的 `AGameplayAbilityTargetActor` 的具体子类，`WaitTargetData` `AbilityTask` 节点上会暴露不同的 `ExposeOnSpawn` 参数。一些常见的参数包括：


| Common `TargetActor` Parameters | Definition                                                                                                                                                                                                                                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Debug                           | 如果设置为 `true`，每当 `TargetActor` 执行跟踪时，它将在非发布版本中绘制调试跟踪/重叠信息。请记住，非 `Instant` 的 `TargetActors` 会在 `Tick()` 上执行跟踪，因此这些调试绘制调用也会在 `Tick()` 上发生。                                                        |
| Filter                          | [可选] 用于在跟踪/重叠发生时从目标中筛选（移除）`Actors` 的特殊结构。典型用例包括筛选掉玩家的 `Pawn`，要求目标属于特定类。有关更高级的用例，请参见 [Target Data Filters](#concepts-target-data-filters)。 |
| Reticle Class                   | [可选] `TargetActor` 将生成的 `AGameplayAbilityWorldReticle` 的子类。                                                                                                                                                                                                                                 |
| Reticle Parameters              | [可选] 配置你的 Reticles。请参见 [Reticles](#concepts-targeting-reticles)。                                                                                                                                                                                                                                        |
| Start Location                  | 跟踪应该从哪里开始的特殊结构。通常，这将是玩家的视角、武器枪口或 `Pawn` 的位置。                                                                                                                                                                          |


默认的 `TargetActor` 类中，`Actors` 只有在它们直接位于跟踪/重叠区域时才是有效目标。如果它们离开了跟踪/重叠区域（例如移动或你移开视线），它们将不再有效。如果你希望 `TargetActor` 记住最后一个有效目标，你需要在自定义 `TargetActor` 类中添加此功能。我将这些称为持久目标，因为它们会持续存在，直到 `TargetActor` 收到确认或取消，`TargetActor` 在其跟踪/重叠中找到新的有效目标，或者目标不再有效（被销毁）。GASShooter 为其火箭发射器的副能力的导向火箭目标使用了持久目标。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-target-data-filters"></a>

#### 4.11.3 Target Data Filters

使用 `Make GameplayTargetDataFilter` 和 `Make Filter Handle` 节点，你可以过滤掉玩家的 `Pawn` 或仅选择特定的类。如果你需要更高级的过滤，可以继承 `FGameplayTargetDataFilter` 并重写 `FilterPassesForActor` 函数。

```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** Returns true if the actor passes the filter and will be targeted */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

然而，这种方法不能直接用于 `Wait Target Data` 节点，因为它需要一个 `FGameplayTargetDataFilterHandle`。你必须创建一个新的自定义 `Make Filter Handle`，以接受自定义子类：

```c++
FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>

#### 4.11.4 游戏玩法能力世界准星（Gameplay Ability World Reticles）

[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html)（`Reticles`）用于可视化**你**在使用非`Instant`确认的[`TargetActors`](#concepts-targeting-actors)时正在瞄准的对象。`TargetActors` 负责所有 `Reticles` 的生成和销毁生命周期。由于 `Reticles` 是 `AActors`，它们可以使用任何类型的可视组件进行表示。

在 [GASShooter](https://github.com/tranek/GASShooter) 中，一个常见的实现是使用 `WidgetComponent` 来显示 UMG Widget 在屏幕空间（始终面向玩家的摄像机）。`Reticles` 本身并不知晓它们附着的 `AActor`，但你可以通过自定义 `TargetActor` 来添加这项功能。通常，`TargetActors` 会在每个 `Tick()` 更新 `Reticle` 的位置以匹配目标的位置。

GASShooter 使用 `Reticles` 显示火箭发射器次级能力的锁定目标。敌人上的红色指示器是 `Reticle`，而白色图像是火箭发射器的十字准星。
![GASShooter中的Reticles](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`Reticles` 提供了多个 `BlueprintImplementableEvents` 供设计师使用（这些事件旨在通过蓝图进行开发）：

- **OnReticleCreated**：当 `Reticle` 被生成时调用。
- **OnReticleDestroyed**：当 `Reticle` 被销毁时调用。
- **OnReticleUpdated**：当 `Reticle` 的位置或外观更新时调用。

这些事件允许设计师自定义 `Reticles` 在游戏中的行为和外观。

```c++
/** Called whenever bIsTargetValid changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** Called whenever bIsTargetAnActor changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticles` 可以选择使用 [`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html) 进行配置，这些参数由 `TargetActor` 提供。默认的结构体仅提供一个变量 `FVector AOEScale`。虽然你可以技术上对子类化这个结构体，但 `TargetActor` 只接受基本结构体。虽然这种做法可能略显短视，但如果你创建自己的自定义 `TargetActor`，你可以提供自己的自定义瞄准器参数结构体，并在生成时手动传递给你的 `AGameplayAbilityWorldReticles` 子类。

`Reticles` 默认情况下不会被复制，但如果对你的游戏有意义，可以使它们复制，以便显示其他玩家正在瞄准的对象。

使用默认的 `TargetActors` 时，`Reticles` 只会显示在当前有效的目标上。例如，如果你使用 `AGameplayAbilityTargetActor_SingleLineTrace` 进行目标追踪，`Reticle` 只会在敌人直接在追踪路径中时出现。如果你转移视线，敌人不再是有效目标，`Reticle` 将会消失。如果你希望 `Reticle` 保持在最后一个有效目标上，你需要自定义你的 `TargetActor` 以记住最后一个有效目标，并将 `Reticle` 保持在上面。我称这些为持久目标，因为它们将持续存在，直到 `TargetActor` 收到确认或取消，`TargetActor` 在其追踪/重叠中找到新的有效目标，或者目标不再有效（被销毁）。GASShooter 使用持久目标来进行其火箭发射器副能力的制导火箭瞄准。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-containers"></a>

#### 4.11.5 Gameplay Effect Containers Targeting

[`GameplayEffectContainers`](#concepts-ge-containers) 提供了一种可选且高效的方式来生成 [`TargetData`](#concepts-targeting-data)。这种瞄准发生在 `EffectContainer` 在客户端和服务器上应用时，效率高于 [`TargetActors`](#concepts-targeting-actors)，因为它在瞄准对象的 CDO 上运行（无需生成和销毁 `Actors`），但它缺乏玩家输入，发生得非常迅速，无需确认，无法取消，并且不能从客户端向服务器发送数据（在两者上生成数据）。这种方法适用于即时的追踪和碰撞重叠。Epic 的 [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) 包括了两种示例类型的容器瞄准 - 针对能力拥有者和从事件中提取 `TargetData`。它还在 Blueprint 中实现了一个示例，在玩家位置的某个偏移处进行即时球体追踪（由子 Blueprint 类设置）。你可以在 C++ 或 Blueprint 中子类化 `URPGTargetType` 来创建自己的瞄准类型。


**[⬆ Back to Top](#table-of-contents)**

<a name="cae"></a>

## 5. Commonly Implemented Abilities and Effects 常见的能力和效果



<a name="cae-stun"></a>

### 5.1 Stun

通常情况下，对于眩晕效果，我们希望做到以下几点：
- 取消目标角色的所有激活中的 `GameplayAbilities`。
- 阻止新的 `GameplayAbility` 激活。
- 在眩晕期间阻止角色移动。

示例项目中的 Meteor `GameplayAbility` 在击中目标时会施加眩晕效果。

为了取消目标的激活中的 `GameplayAbilities`，我们在眩晕 [`GameplayTag`](#concepts-gt-change) 被添加时调用 `AbilitySystemComponent->CancelAbilities()`。

为了阻止新的 `GameplayAbilities` 在眩晕期间激活，我们在 `GameplayAbilities` 的 [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags) 中添加眩晕 `GameplayTag`。

为了在眩晕时阻止角色移动，我们重写 `CharacterMovementComponent` 的 `GetMaxSpeed()` 函数，使其在角色拥有眩晕 `GameplayTag` 时返回 0。


**[⬆ Back to Top](#table-of-contents)**

<a name="cae-sprint"></a>

### 5.2 Sprint

示例项目提供了一个如何实现冲刺的例子——当按住 `Left Shift` 键时，角色会更快地移动。

更快的移动速度通过 `CharacterMovementComponent` 进行预测处理，并将标志通过网络发送给服务器。详情请查看 `GDCharacterMovementComponent.h/cpp`。

`GA` 处理响应 `Left Shift` 输入，指示 `CMC` 开始和停止冲刺，并在按住 `Left Shift` 时预测性地消耗体力。详情请查看 `GA_Sprint_BP`。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ads"></a>

### 5.3 Aim Down Sights

示例项目处理这个功能的方式与冲刺完全相同，但会减少移动速度而不是增加。

有关预测性减少移动速度的详细信息，请参阅 `GDCharacterMovementComponent.h/cpp`。

有关处理输入的详细信息，请参阅 `GA_AimDownSight_BP`。瞄准时没有体力消耗。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ls"></a>

### 5.4 Lifesteal

我在伤害的 [`ExecutionCalculation`](#concepts-ge-ec) 中处理吸血效果。`GameplayEffect` 上会有一个类似 `Effect.CanLifesteal` 的 `GameplayTag`。`ExecutionCalculation` 会检查 `GameplayEffectSpec` 是否包含 `Effect.CanLifesteal` 的 `GameplayTag`。如果存在该 `GameplayTag`，`ExecutionCalculation` 会 [创建一个动态的 `Instant` `GameplayEffect`](#concepts-ge-dynamic)，并将健康值作为修饰符应用回 `Source` 的 `ASC`。


```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-random"></a>

### 5.5 Generating a Random Number on Client and Server

有时你需要在 `GameplayAbility` 中生成一个“随机”数字，例如用于子弹的后坐力或散布。客户端和服务器都希望生成相同的随机数字。为此，我们必须在 `GameplayAbility` 激活时将 `random seed` 设置为相同。你将需要在每次激活 `GameplayAbility` 时设置 `random seed`，以防客户端预测错误导致随机数字序列与服务器的不同步。


| Seed Setting Method                                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Use the activation prediction key                                            | The `GameplayAbility` activation prediction key is an int16 guaranteed to be synchronized and available in both the client and server in the `Activation()`. You can set this as the `random seed` on both the client and the server. The downside to this method is that the prediction key always starts at zero each time the game starts and consistently increments the value to use between generating keys. This means each match will have the exact same random number sequence. This may or may not be random enough for your needs. |
| Send a seed through an event payload when you activate the `GameplayAbility` | Activate your `GameplayAbility` by event and send the randomly generated seed from the client to the server via the replicated event payload. This allows for more randomness but the client could easily hack their game to only send the same seed value every time. Also activating `GameplayAbilities` by event will prevent them from activating from the input bind.                                                                                                                                                                     |

| 种子设置方法                                                                  | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 使用激活预测键                                                                | `GameplayAbility` 的激活预测键是一个 int16，保证在客户端和服务器中的 `Activation()` 时都能同步且可用。你可以将其设置为客户端和服务器上的 `random seed`。此方法的缺点是预测键在每次游戏开始时总是从零开始，并且在生成键之间持续递增。这意味着每场比赛将拥有完全相同的随机数序列。这可能不够随机，具体取决于你的需求。                                                                                                                                            |
| 在激活 `GameplayAbility` 时通过事件有效载荷发送种子                           | 通过事件激活你的 `GameplayAbility` 并通过复制的事件有效载荷将客户端生成的随机种子发送到服务器。这允许更高的随机性，但客户端可能会轻松修改游戏以每次都发送相同的种子值。此外，通过事件激活 `GameplayAbilities` 将阻止它们通过输入绑定激活。                                                                                                                                                                                             |


如果你的随机偏差很小，大多数玩家不会注意到每场游戏的序列是相同的，这种情况下使用激活预测键作为 `random seed` 应该足够了。如果你正在进行更复杂的操作且需要防止黑客攻击，那么使用 `Server Initiated` `GameplayAbility` 可能更合适，在这种情况下，服务器可以生成预测键或随机种子，并通过事件有效载荷发送。


**[⬆ Back to Top](#table-of-contents)**

<a name="cae-crit"></a>

### 5.6 Critical Hits

我在伤害 [`ExecutionCalculation`](#concepts-ge-ec) 内处理暴击。`GameplayEffect` 上会有一个类似 `Effect.CanCrit` 的 `GameplayTag`。`ExecutionCalculation` 检查 `GameplayEffectSpec` 是否有这个 `Effect.CanCrit` `GameplayTag`。如果存在该 `GameplayTag`，`ExecutionCalculation` 将生成一个与暴击几率（从 `Source` 捕获的 `Attribute`）相对应的随机数，并在成功时添加暴击伤害（也从 `Source` 捕获的 `Attribute`）。由于我不预测伤害，所以不需要担心客户端和服务器上的随机数生成器同步，因为 `ExecutionCalculation` 仅在服务器上运行。如果你尝试使用 `MMC` 进行预测性伤害计算，你需要从 `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance` 中获取 `random seed` 的引用。

查看 [GASShooter](https://github.com/tranek/GASShooter) 如何处理爆头。概念相同，只不过它不依赖于随机数来决定几率，而是检查 `FHitResult` 骨骼名称。


**[⬆ Back to Top](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target

在《Paragon》中，减速效果不会叠加。每个减速实例会正常应用并跟踪其持续时间，但只有最大的减速效果会真正影响 `Character`。GAS 提供了这个场景的开箱即用的解决方案，使用 `AggregatorEvaluateMetaData`。有关详细信息和实现，请参见 [`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated)。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-paused"></a>

### 5.8 Generate Target Data While Game is Paused

如果在等待生成来自玩家的 [`TargetData`](#concepts-targeting-data) 的 `WaitTargetData` `AbilityTask` 时需要暂停游戏，我建议使用 `slomo 0` 代替暂停。


**[⬆ Back to Top](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>

### 5.9 One Button Interaction System

[GASShooter](https://github.com/tranek/GASShooter) 实现了一个一键互动系统，玩家可以按下或长按 'E' 键与可互动的物体进行交互，如复活玩家、打开武器箱、以及开关滑动门。

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging"></a>

## 6. Debugging GAS

调试GAS相关问题时，通常你可能会想了解以下内容：
> * "我的属性值是什么？"
> * "我拥有哪些游戏标签？"
> * "我当前拥有哪些游戏效果？"
> * "我有哪些能力被授予，哪些能力正在运行，哪些能力被阻止激活？"

GAS提供了两种在运行时回答这些问题的技术 - [`showdebug abilitysystem`](#debugging-sd) 和 [`GameplayDebugger`](#debugging-gd) 中的钩子。

**提示：** Unreal Engine 会优化 C++ 代码，这可能使得某些函数难以调试。在深入代码跟踪时，可能会遇到这种情况。如果将 Visual Studio 解决方案配置设置为 `DebugGame Editor` 仍然无法跟踪代码或检查变量，你可以通过使用 `UE_DISABLE_OPTIMIZATION` 和 `UE_ENABLE_OPTIMIZATION` 宏，或在 CoreMiscDefines.h 中定义的发布变体来禁用所有优化。这不能用于插件代码，除非你从源代码重新构建插件。根据函数的作用及其位置，这可能对内联函数不起作用。调试完成后，请务必删除这些宏！


```c++
UE_DISABLE_OPTIMIZATION
void MyClass::MyFunction(int32 MyIntParameter)
{
	// My code
}
UE_ENABLE_OPTIMIZATION
```

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-sd"></a>

### 6.1 showdebug abilitysystem

在游戏内控制台中输入 `showdebug abilitysystem`。此功能分为三个“页面”。所有三个页面都会显示你当前拥有的 `GameplayTags`。输入 `AbilitySystem.Debug.NextCategory` 以在页面之间循环。

第一个页面显示你所有 `Attributes` 的 `CurrentValue`：
![First Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

第二个页面显示你身上的所有 `Duration` 和 `Infinite` `GameplayEffects`，它们的堆叠数量、提供的 `GameplayTags` 和 `Modifiers`。
![Second Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

第三个页面显示授予你的所有 `GameplayAbilities`，它们是否当前正在运行、是否被阻止激活，以及当前运行的 `AbilityTasks` 的状态。
![Third Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

要在目标之间循环（通过绿色长方体围绕 Actor 标记），使用 `PageUp` 键或 `NextDebugTarget` 控制台命令转到下一个目标，使用 `PageDown` 键或 `PreviousDebugTarget` 控制台命令转到上一个目标。

**注意：** 为了使能力系统信息能够根据当前选择的调试 Actor 更新，你需要在 `AbilitySystemGlobals` 中设置 `bUseDebugTargetFromHud=true`，例如在 `DefaultGame.ini` 中：

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
bUseDebugTargetFromHud=true
```

**注意：** 为了使 `showdebug abilitysystem` 起作用，游戏模式中必须选择一个实际的 HUD 类。否则，命令会无法找到，返回“Unknown Command”。


**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-gd"></a>

### 6.2 Gameplay Debugger

GAS 增加了对 Gameplay Debugger 的功能支持。通过按下撇号 (') 键访问 Gameplay Debugger。按下数字小键盘上的 3 来启用 Abilities 类别。根据你安装的插件，类别可能会有所不同。如果你的键盘没有数字小键盘（例如笔记本电脑），你可以在项目设置中更改按键绑定。

当你想查看 **其他** `Characters` 的 `GameplayTags`、`GameplayEffects` 和 `GameplayAbilities` 时，使用 Gameplay Debugger。遗憾的是，它不会显示目标的 `Attributes` 的 `CurrentValue`。它会定位在你屏幕中心的 `Character`。你可以通过在编辑器中的 World Outliner 中选择它们或通过查看不同的 `Character` 再次按下撇号 (') 键来更改目标。当前检查的 `Character` 上方有最大的红色圆圈。

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)


**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-log"></a>

### 6.3 GAS Logging

GAS 源代码包含大量的日志语句，这些语句以不同的详细级别生成。你最可能看到的是 `ABILITY_LOG()` 语句。默认的详细级别是 `Display`，任何更高的详细级别默认不会在控制台中显示。

要更改日志类别的详细级别，可以在控制台中输入：
```
log [category] [verbosity]
```

For example, to turn on `ABILITY_LOG()` statements, you would type into your console:
```
log LogAbilitySystem VeryVerbose
```

To reset it back to default, type:
```
log LogAbilitySystem Display
```

To display all log categories, type:
```
log list
```

Notable GAS related logging categories:

| Logging Category          | Default Verbosity Level |
| ------------------------- | ----------------------- |
| LogAbilitySystem          | Display                 |
| LogAbilitySystemComponent | Log                     |
| LogGameplayCueDetails     | Log                     |
| LogGameplayCueTranslator  | Display                 |
| LogGameplayEffectDetails  | Log                     |
| LogGameplayEffects        | Display                 |
| LogGameplayTags           | Log                     |
| LogGameplayTasks          | Log                     |
| VLogAbilitySystem         | Display                 |

See the [Wiki on Logging](https://unrealcommunity.wiki/logging-lgpidy6i) for more information.

**[⬆ Back to Top](#table-of-contents)**

<a name="optimizations"></a>
## 7. 优化

<a name="optimizations-abilitybatching"></a>
### 7.1 能力批处理
[`GameplayAbilities`](#concepts-ga) 在激活、选择性地将 `TargetData` 发送到服务器并在同一帧结束时，可以 [批处理以将两个到三个 RPC 压缩为一个 RPC](#concepts-ga-batching)。这些类型的能力通常用于瞄准射击武器。

<a name="optimizations-gameplaycuebatching"></a>
### 7.2 Gameplay Cue 批处理
如果你同时发送许多 [`GameplayCues`](#concepts-gc)，考虑 [将它们批处理为一个 RPC](#concepts-gc-batching)。目标是减少 RPC 的数量（`GameplayCues` 是不可靠的 NetMulticasts），并尽可能少地发送数据。

<a name="optimizations-ascreplicationmode"></a>
### 7.3 AbilitySystemComponent 复制模式
默认情况下，[`ASC`](#concepts-asc) 处于 [`完全复制模式`](#concepts-asc-rm)。这将把所有 [`GameplayEffects`](#concepts-ge) 复制到每个客户端（对于单人游戏是可以的）。在多人游戏中，将玩家拥有的 `ASCs` 设置为 `混合复制模式`，将 AI 控制的角色设置为 `最小复制模式`。这将使应用于玩家角色的 `GEs` 仅复制给该角色的拥有者，而 AI 控制的角色上的 `GEs` 将不会复制给客户端。 [`GameplayTags`](#concepts-gt) 仍然会复制，`GameplayCues` 仍然会是不可靠的 NetMulticast 到所有客户端，无论 `Replication Mode` 如何。这将减少 `GEs` 复制时网络数据的数量，因为所有客户端不需要看到它们。

<a name="optimizations-attributeproxyreplication"></a>
### 7.4 属性代理复制
在像 Fortnite Battle Royale (FNBR) 这样的大型游戏中，有许多 [`ASCs`](#concepts-asc) 存在于始终相关的 `PlayerStates` 中，复制大量的 [`Attributes`](#concepts-a)。为了优化这个瓶颈，Fortnite 在 `PlayerState::ReplicateSubobjects()` 中禁用了 **模拟玩家控制代理** 的 `ASC` 及其 [`AttributeSets`](#concepts-as) 的复制。自主代理和 AI 控制的 `Pawns` 仍然根据其 [`Replication Mode`](#concepts-asc-rm) 完全复制。FNBR 使用了一个在玩家的 `Pawn` 上复制的代理结构来替代在始终相关的 `PlayerStates` 上复制 `Attributes`。当服务器的 `ASC` 上的 `Attributes` 发生变化时，它们也会在代理结构中发生变化。客户端从代理结构接收复制的 `Attributes` 并将更改推送回本地的 `ASC`。这使得 `Attribute` 复制可以使用 `Pawn` 的相关性和 `NetUpdateFrequency`。这个代理结构还复制了一小部分白名单的 `GameplayTags` 位掩码。此优化减少了网络上的数据量，并允许我们利用 `Pawn` 的相关性。AI 控制的 `Pawns` 其 `ASC` 在 `Pawn` 上，这已经利用了其相关性，因此不需要此优化。

> 我不确定随着服务器端优化（如复制图等）的完成，这是否仍然必要，这也不是最可维护的模式。

*Dave Ratti 来自 Epic 的 [社区问题 #3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) 的回答*

<a name="optimizations-asclazyloading"></a>
### 7.5 ASC 懒加载
Fortnite Battle Royale (FNBR) 在世界中有大量可受损的 `AActors`（树木、建筑物等），每个都拥有一个 [`ASC`](#concepts-asc)。这可能会增加内存成本。FNBR 通过仅在需要时（即当它们首次受到玩家伤害时）懒加载 `ASCs` 来优化此问题。这减少了整体内存使用，因为有些 `AActors` 可能在比赛中从未受到伤害。

**[⬆ Back to Top](#table-of-contents)**

<a name="qol"></a>
## 8. 生活质量建议

<a name="qol-gameplayeffectcontainers"></a>
### 8.1 Gameplay Effect 容器
[GameplayEffectContainers](#concepts-ge-containers) 将 [`GameplayEffectSpecs`](#concepts-ge-spec)、[`TargetData`](#concepts-targeting-data)、[简单目标选择](#concepts-targeting-containers) 和相关功能组合成易于使用的结构。这些对于将 `GameplayEffectSpecs` 传递给从能力生成的投射物，然后在稍后碰撞时应用它们非常有用。

<a name="qol-asynctasksascdelegates"></a>
### 8.2 Blueprint 异步任务绑定到 ASC 委托
为了提高设计师友好的迭代时间，特别是在为 UI 设计 UMG 小部件时，可以创建 Blueprint 异步任务（用 C++ 实现）来直接从 UMG Blueprint 图绑定到 `ASC` 上的常见更改委托。唯一的注意事项是它们必须手动销毁（例如，当小部件被销毁时），否则它们将永久驻留在内存中。样例项目包括三个 Blueprint 异步任务。

监听 `Attribute` 更改：

![监听属性变化 BP 节点](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

监听冷却时间变化：

![监听冷却时间变化 BP 节点](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

监听 `GE` 堆叠变化：

![监听 GameplayEffect 堆叠变化 BP 节点](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting"></a>
## 9. 故障排除

<a name="troubleshooting-notlocal"></a>
### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
你需要 [在客户端初始化 `ASC`](#concepts-asc-setup)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
### 9.2 `ScriptStructCache` 错误
你需要调用 [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
### 9.3 动画蒙太奇未复制到客户端
确保你在你的 [GameplayAbilities](#concepts-ga) 中使用的是 `PlayMontageAndWait` Blueprint 节点，而不是 `PlayMontage`。这个 [AbilityTask](#concepts-at) 会自动通过 `ASC` 复制蒙太奇，而 `PlayMontage` 节点则不会。

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting-duplicatingblueprintactors"></a>
### 9.4 复制 Blueprint Actors 时将 AttributeSets 设置为 nullptr
Unreal Engine 中存在一个 [错误](https://issues.unrealengine.com/issue/UE-81109)，会将你的类中的 `AttributeSet` 指针设置为 nullptr，特别是对于从现有 Blueprint Actor 类复制的 Blueprint Actor 类。有几个解决方法。我已经成功地避免在我的类中创建特定的 `AttributeSet` 指针（.h 文件中没有指针，构造函数中不调用 `CreateDefaultSubobject`），而是直接在 `PostInitializeComponents()` 中将 `AttributeSets` 添加到 `ASC` 中（样例项目中没有展示）。复制的 `AttributeSets` 仍会存在于 `ASC` 的 `SpawnedAttributes` 数组中。它看起来可能像这样：


```c++
void AGDPlayerState::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->AddSet<UGDAttributeSetBase>();
		// ... any other AttributeSets that you may have
	}
}
```

在这种情况下，你会使用 `ASC` 上的函数来读取和设置 `AttributeSet` 中的值，而不是 [（calling functions on the AttributeSet made from the macros）调用由宏生成的 `AttributeSet` 中的函数](#concepts-as-attributes)。


```c++
/** Returns current (final) value of an attribute */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** Sets the base value of an attribute. Existing active modifiers are NOT cleared and will act upon the new base value. */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

So the `GetHealth()` would look something like:

```c++
float AGDPlayerState::GetHealth() const
{
	if (AbilitySystemComponent)
	{
		return AbilitySystemComponent->GetNumericAttribute(UGDAttributeSetBase::GetHealthAttribute());
	}

	return 0.0f;
}
```

设置（初始化）健康 `Attribute` 的代码示例如下：


```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

提醒一下，`ASC` 每个 `AttributeSet` 类最多只会期望有一个 `AttributeSet` 对象。

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-unresolvedexternalsymbolmarkpropertydirty"></a>

### 9.5 Unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)

If you get a compiler error like:

```
error LNK2019: unresolved external symbol "__declspec(dllimport) void __cdecl UEPushModelPrivate::MarkPropertyDirty(int,int)" (__imp_?MarkPropertyDirty@UEPushModelPrivate@@YAXHH@Z) referenced in function "public: void __cdecl FFastArraySerializer::IncrementArrayReplicationKey(void)" (?IncrementArrayReplicationKey@FFastArraySerializer@@QEAAXXZ)
```

这是因为尝试在 `FFastArraySerializer` 上调用 `MarkItemDirty()`。我在更新 `ActiveGameplayEffect` 时遇到过这种情况，比如在更新冷却时间时。

```c++
ActiveGameplayEffects.MarkItemDirty(*AGE);
```

发生这种情况是因为 `WITH_PUSH_MODEL` 在多个地方被定义。`PushModelMacros.h` 将其定义为 0，而在多个地方将其定义为 1。`PushModel.h` 看到的是 1，而 `PushModel.cpp` 看到的是 0。

解决方案是在 `Build.cs` 文件中的 `PublicDependencyModuleNames` 中添加 `NetCore`。

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-enumnamesarenowpathnames"></a>

### 9.6 Enum names are now represented by path name

If you get a compiler warning like:

```
warning C4996: 'FGameplayAbilityInputBinds::FGameplayAbilityInputBinds': Enum names are now represented by path names. Please use a version of FGameplayAbilityInputBinds constructor that accepts FTopLevelAssetPath. Please update your code to the new API before upgrading to the next release, otherwise your project will no longer compile.
```

UE 5.1 deprecated using `FString` in the constructor for `BindAbilityActivationToInputComponent()`. Instead, we must pass in an `FTopLevelAssetPath`.

Old, deprecated way:
```c++
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

New way:
```c++
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

See `Engine\Source\Runtime\CoreUObject\Public\UObject\TopLevelAssetPath.h` for more info.

**[⬆ Back to Top](#table-of-contents)**

<a name="acronyms"></a>
## 10. Common GAS Acronyms  常见的GAS缩写

| 名称                                                                                                   | 缩写                |
|------------------------------------------------------------------------------------------------------- | ------------------- |
| AbilitySystemComponent                                                                                 | ASC                 |
| AbilityTask                                                                                            | AT                  |
| [Epic的动作角色扮演游戏示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG示例      |
| CharacterMovementComponent                                                                             | CMC                 |
| GameplayAbility                                                                                        | GA                  |
| GameplayAbilitySystem                                                                                  | GAS                 |
| GameplayCue                                                                                            | GC                  |
| GameplayEffect                                                                                         | GE                  |
| GameplayEffectExecutionCalculation                                                                     | ExecCalc, 执行计算  |
| GameplayTag                                                                                            | Tag, GT             |
| ModifierMagnitudeCalculation                                                                           | ModMagCalc, MMC     |


**[⬆ Back to Top](#table-of-contents)**

<a name="resources"></a>

## 11. 其他资源

* [官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* 源代码！
   * 尤其是 `GameplayPrediction.h`
* [Epic的Lyra示例项目](https://unrealengine.com/marketplace/en-US/learn/lyra)
* [Epic的动作角色扮演游戏示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) 有一个专门讨论GAS的文本频道 `#gameplay-ability-system`
   * 查看置顶消息
* [Dan 'Pan' 的GitHub资源库](https://github.com/Pantong51/GASContent)
* [SabreDartStudios 的YouTube视频](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)


<a name="resources-daveratti"></a>
### 11.1 Q&A With Epic Game's Dave Ratti

<a name="resources-daveratti-community1"></a>

#### 11.1.1 社区问题 1

[Dave Ratti 对 Unreal Slackers Discord 服务器社区关于GAS的提问的回答](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89):

1. 如何在`GameplayAbilities`之外或不依赖于`GameplayAbilities`创建按需的预测窗口？例如，当火箭弹击中敌人时，如何进行本地预测以处理伤害`GameplayEffect`？


> 预测键系统实际上并不适合这种用途。该系统的基本原理是客户端启动一个预测性操作，并通过一个键告知服务器，然后客户端和服务器都运行相同的操作，并将预测性的副作用与给定的预测键关联起来。例如，“我正在预测性地激活一个能力”或“我已经生成了目标数据，并将预测性地运行 WaitTargetData 任务之后的能力图部分”。
>
> 在这种模式下，预测键会“弹回”到服务器，然后通过 `UAbilitySystemComponent::ReplicatedPredictionKeyMap`（已复制属性）返回给客户端。一旦键从服务器复制回来，客户端就能够撤销所有本地预测的副作用（GameplayCues、GameplayEffects）：复制的版本*会存在*，如果不存在，那就是预测错误。准确知道何时撤销预测副作用是关键：如果过早撤销，你会看到缺口；如果过晚撤销，你会看到“重复”。（注意，这指的是有状态的预测，如基于持续时间的循环 GameplayCue。 “Burst” 类型的 GameplayCues 和瞬时 Gameplay Effects 从未被“撤销”或回滚。如果有预测键与它们关联，它们只是在客户端被跳过。）
>
> 进一步强调这一点：至关重要的是，预测行为不是由服务器自行完成的，而是只有在客户端指示时才会进行。因此，单纯地“按需创建一个键并告诉服务器以便我可以运行某些操作”是不够的，除非这个“某些操作”是服务器在接收到客户端指示后才会进行的。
>
> 回到原始问题：像“发射后忘记”那样的投射物。Paragon 和 Fortnite 都有使用 GameplayCues 的投射物演员类。然而，我们并不使用预测键系统来处理这些。相反，我们使用了非复制的 GameplayCues 概念。这些 GameplayCues 只是本地触发，并被服务器完全忽略。基本上，这些都是直接调用 UGameplayCueManager::HandleGameplayCue 的。它们不通过 UAbilitySystemComponent，因此不会进行预测键检查或提前返回。
>
> 非复制的 GameplayCues 的缺点是它们不会被复制。因此，投射物类/蓝图需要确保调用这些函数的代码路径在所有人上都能运行。我们有启动（在 BeginPlay 中调用）、爆炸、撞墙/角色等的 Cue。
>
> 这些事件已经在客户端生成，因此调用非复制的 GameplayCue 并不是大问题。复杂的蓝图可能很棘手，作者需要确保他们理解每个部分的运行位置。

2. 当使用 `WaitNetSync` `AbilityTask` 和 `OnlyServerWait` 创建局部预测窗口时，是否存在玩家通过延迟数据包到服务器来控制 `GameplayAbility` 时间的问题？

> 是的，这确实是一个有效的担忧。任何在服务器上运行并等待客户端“信号”的能力蓝图都可能容易受到延迟开关类型的漏洞。
>
> 在《Paragon》中，我们有一个类似于 `UAbilityTask_WaitTargetData` 的自定义目标任务。在这个任务中，我们设置了超时或“最大延迟”，我们会在客户端上等待即时目标模式。如果目标模式等待用户确认（按钮按下），则会被忽略，因为用户可以慢慢来。但对于那些即时确认目标的能力，我们会等待一段时间，然后要么 A) 在服务器端生成目标数据，要么 B) 取消能力。
>
> 对于 `WaitNetSync`，我们没有这样的机制，我们使用得非常少。
>
> 我不认为《Fortnite》使用了类似的东西。 Fortnite 中的武器能力是特殊处理的，批量处理到一个 Fortnite 特定的 RPC：一个 RPC 来激活能力、提供目标数据和结束能力。因此，武器能力在大逃杀模式中本质上不会受到影响。
>
> 我认为这可能是一个可以在系统范围内解决的问题，但我不认为我们会很快进行更改。对 `WaitNetSync` 进行修复以包括你提到的最大延迟可能是一个合理的任务，但同样——我们可能不会立即进行此更改。


3. Paragon 和 Fortnite 使用了哪种 `EGameplayEffectReplicationMode`，Epic 对于每种模式的推荐是什么？

> 两款游戏基本上对玩家控制的角色使用了 Mixed 模式，对 AI 控制的角色（AI 小兵、丛林怪物、AI Husk 等）使用了 Minimal 模式。我建议大多数人使用这种模式来进行多人游戏。越早在项目中设置这些模式越好。
>
> 《Fortnite》在优化方面进一步提高了效率。实际上，它并不会为模拟代理复制 `UAbilitySystemComponent`。该组件和属性子对象在拥有 Fortnite 玩家状态类的 `::ReplicateSubobjects()` 中被跳过。我们将从能力系统组件中复制的最少数据推送到角色本身的一个结构中（基本上是属性值的子集和一个在位掩码中复制的标签子集）。我们称之为“代理”。在接收方，我们将代理数据（在角色上复制）重新推送到玩家状态上的能力系统组件中。所以在 FNBR 中每个玩家都有一个 ASC，它只是不会直接复制：而是通过角色上的最小代理结构复制数据，然后在接收端路由回 ASC。这有一个优点，即 A) 数据集更小 B) 利用角色的相关性。
>
> 我不确定是否仍然需要在其他服务器端优化（如复制图等）之后使用这种模式，这种模式也不是最易维护的模式。


### 4. 由于我们无法预测 `GameplayEffects` 的移除（参见 `GameplayPrediction.h`），有没有什么策略可以减轻延迟对 `GameplayEffects` 移除的影响？例如，当移除移动速度减慢效果时，我们当前必须等待服务器复制 `GameplayEffect` 的移除，导致玩家角色位置的突然变化。

> 这是一个棘手的问题，我没有一个很好的答案。我们通常通过容差和平滑来规避这些问题。我完全同意能力系统与角色移动系统的精确同步问题不在一个很好的位置，这是我们希望解决的问题。
>
> 我曾经尝试过允许预测性移除 GEs，但在不得不继续进行之前没有解决所有边缘情况。不过这也不能解决所有问题，因为角色移动仍有一个内部保存的移动缓冲区，并不知道能力系统和可能的移动速度修饰符等。即使在无法预测 GE 移除的情况下，也可能会进入修正反馈循环。
>
> 如果你认为你有一个真正紧急的案例，你可以预测性地添加一个 GE 来抑制你的移动速度 GEs。我自己从未尝试过，但之前有过这样的理论。这可能有助于解决某些类别的问题。

### 5. 我们知道 `AbilitySystemComponent` 在 Paragon 和 Fortnite 中存在于 `PlayerState` 上，而在 Action RPG Sample 中存在于 `Character` 上。Epic 对于 `AbilitySystemComponent` 应该存在于何处以及其 `Owner` 应该是什么，有哪些内部规则、指南或建议？

> 一般来说，我会说任何不需要重新生成的对象应该将 Owner 和 Avatar actor 设置为相同的东西。像 AI 敌人、建筑物、世界道具等。
>
> 任何需要重新生成的对象应该将 Owner 和 Avatar 设置为不同的，以便在重新生成后不需要保存/重新创建/恢复 Ability System Component。PlayerState 是一个合乎逻辑的选择，因为它会复制到所有客户端（而 PlayerController 不会）。缺点是 PlayerStates 总是相关的，所以在 100 名玩家的游戏中可能会遇到问题（参见第 3 问中 FN 所做的说明）。

### 6. 是否可行在拥有相同 Owner 但不同 Avatars（例如，在 pawn 和武器/物品/投射物上，`Owner` 设置为 `PlayerState`）上有多个 `AbilitySystemComponents`？

> 我看到的第一个问题是如何在拥有者演员上实现 `IGameplayTagAssetInterface` 和 `IAbilitySystemInterface`。前者可能可行：只是从所有 ASC 中聚合标签（但要注意 - `HasAllMatchingGameplayTags` 可能仅通过跨 ASC 聚合来满足。仅仅转发调用到每个 ASC 并将结果 OR 在一起可能不够）。但后者则更棘手：哪个 ASC 是权威的？如果有人想要应用一个 GE，哪个 ASC 应该接收它？也许你可以解决这些问题，但这方面的问题将是最困难的：拥有者下面有多个 ASC。
>
> 不过，在 pawn 和武器上有单独的 ASCs 本身可能是有意义的。例如，区分描述武器的标签与描述拥有者 pawn 的标签。也许授予武器的标签也“应用”于拥有者而不是其他任何东西（例如，属性和 GE 是独立的，但拥有者将汇总拥有的标签，如上所述）。这可能会奏效，我相信。但拥有相同拥有者的多个 ASC 可能会变得棘手。



### 7. 是否有办法阻止服务器覆盖拥有客户端的本地预测能力的冷却时间？在高延迟场景中，这将允许拥有客户端在本地冷却时间到期后“尝试”再次激活能力，但在服务器上仍然在冷却中。到拥有客户端的激活请求通过网络到达服务器时，服务器可能已解除冷却，或者服务器可能能够排队激活请求以处理剩余的毫秒数。否则，具有较高延迟的客户端在重新激活能力之前的延迟较长，而具有较低延迟的客户端则较短。这在冷却时间非常短的能力（如基本攻击，冷却时间可能不到一秒钟）上尤为明显。如果没有办法阻止服务器覆盖本地预测能力的冷却时间，Epic 采用了什么策略来减轻高延迟对重新激活能力的影响？换句话说，Epic 是如何设计 Paragon 的基本攻击和其他能力的，以便高延迟玩家能够以与低延迟玩家相同的速度攻击或激活？

> 简短的回答是，没有办法阻止这一点，Paragon 确实遇到了这个问题。较高延迟的连接会导致基本攻击的射速较低。
>
> 我尝试通过添加“GE 调解”来解决这个问题，其中在计算 GE 持续时间时考虑了延迟。实质上是允许服务器消耗部分总 GE 时间，以便 GE 在客户端的实际时间与任何数量的延迟保持 100% 一致（尽管波动仍可能造成问题）。不过我从未能将其工作到一个可以发布的状态，项目进展很快，我们就没有完全解决这个问题。
>
> Fortnite 自行处理武器射速的记录：它不使用 GE 来管理武器的冷却时间。如果这是你游戏中的一个关键问题，我推荐这样做。



### 8. Epic 对 GameplayAbilitySystem 插件的路线图是什么？Epic 计划在 2019 年及以后添加哪些功能？

> 我们认为系统整体上已经相当稳定，目前没有人专门开发新的主要功能。偶尔会为 Fortnite 或 UDN/拉取请求进行修复和小改进，但目前就这些。
>
> 从长远来看，我认为我们最终会推出一个“V2”或进行一些重大更改。我们在编写此系统时学到了很多东西，感觉在很多方面做对了，也做错了。我很想有机会纠正这些错误，并改进上面提到的一些致命缺陷。
>
> 如果出现 V2，提供升级路径将是至关重要的。我们绝不会推出 V2 而让 Fortnite 永远停留在 V1：会有一些路径或程序可以尽可能地自动迁移，尽管仍然几乎肯定需要一些手动重建。
>
> 高优先级修复包括：
> * 更好地与角色移动系统互操作。统一客户端预测。
> * GE 移除预测（见问题 #4）
> * GE 延迟调解（见问题 #7）
> * 一般化的网络优化，如批量 RPC 和代理结构。主要是我们为 Fortnite 做的工作，但找到将其拆解为更通用形式的方法，至少使游戏能够更容易地编写自己的游戏特定优化。
>
> 我会考虑进行的一些更一般的重构更改：
> * 我希望从根本上考虑摆脱 GE 直接引用电子表格值的方式，而是让 GE 能够发出参数，这些参数可以由绑定到电子表格值的更高层次对象填充。目前模型的问题是 GE 因与曲线表行的紧密耦合而变得不可共享。我认为可以编写一个通用的参数化系统，并作为 V2 系统的基础。
> * 减少 UGameplayAbility 上的“策略”数量。我会删除 ReplicationPolicy 和 InstancingPolicy。Replication 在我看来几乎不需要，且容易引起混淆。InstancingPolicy 应该通过使 FGameplayAbilitySpec 成为可以被子类化的 UObject 来替代。这应该是“非实例化的能力对象”，具有事件并且可以蓝图化。UGameplayAbility 应该是“每次执行实例化”的对象。如果确实需要实例化，则可以选择实例化；否则，“非实例化”能力将通过新的 UGameplayAbilitySpec 对象来实现。
> * 系统应该提供更多的“中间层”构造，如“过滤的 GE 应用容器”（数据驱动哪些 GE 应用到哪些演员上，具有更高层次的游戏逻辑）、“重叠体积支持”（基于碰撞原语重叠事件应用“过滤的 GE 应用容器”等）。这些是每个项目最终都要以自己方式实现的构建块。确保正确实现这些并不简单，因此我认为我们应该更好地提供一些基本实现。
> * 总体而言，减少启动项目所需的样板代码。可能有一个单独的模块“Ex library”或其他模块，可以提供像被动能力或基本的击发武器等功能。该模块将是可选的，但可以让你快速启动。
> * 我希望将 GameplayCues 移动到一个与能力系统无关的单独模块。我认为这里有很多可以改进的地方。
>
> 这只是我个人的意见，而不是任何人的承诺。我认为最现实的做法是，随着新的引擎技术举措的出现，能力系统将需要进行更新，这是进行这类更改的时机。这些举措可能与脚本、网络或物理/角色移动有关。不过这些都是很远的事情，所以我无法提供承诺或时间表。


**[⬆ Back to Top](#table-of-contents)**

<a name="resources-daveratti-community2"></a>

#### 11.1.2 Community Questions 2
Community member [iniside](https://github.com/iniside)'s Q&A with Dave Ratti:

### 1. 是否计划支持解耦的固定滴答？我希望将游戏线程固定（如 30/60fps），而渲染线程可以自由运行。我问这个问题是为了了解未来是否可以实现这样的功能，以便对游戏玩法的工作方式做出一些假设。我主要是因为现在物理系统有了固定的异步滴答，这引发了关于系统未来可能会如何运作的问题。我不掩饰，如果能够有固定滴答的游戏线程而不固定引擎的其他部分的滴答率，将会非常棒。

> 目前没有计划解耦渲染帧率和游戏线程滴答帧率。我认为由于这些系统的复杂性和保持与以前引擎版本的向后兼容性，这种情况几乎不可能实现。
>
> 相反，我们的方向是拥有一个异步的“物理线程”，它以固定的滴答率运行，独立于游戏线程。需要以固定速率运行的东西可以在这里运行，而游戏线程/渲染可以像往常一样操作。
>
> 值得澄清的是，网络预测支持所谓的独立滴答和固定滴答模式。我的长期计划是保持独立滴答大致像现在的网络预测那样，在游戏线程上以可变帧率运行，没有“组/世界”预测，它只是经典的“客户端预测自己的角色和拥有的演员”模型。而固定滴答将利用异步物理功能，允许你预测非客户端控制/拥有的演员，如物理对象和其他客户端/角色/车辆等。

### 2. 是否有计划将网络预测与能力系统集成？例如，固定帧能力激活（即服务器在何时激活能力和执行任务的帧，而不是预测键）？

> 是的，计划是重写/移除能力系统的预测键，并用网络预测构造替代它们。NetworkPredictionExtras 中的 MockAbility 示例展示了这可能如何工作，但它们比 GAS 需要的更“硬编码”。
>
> 主要想法是移除 ASC 的 RPC 中的显式客户端->服务器预测键交换。不再有预测窗口或范围预测键。取而代之的是，一切都围绕网络预测帧进行。重要的是客户端和服务器对事件的发生时间达成一致。例如：
>
> * 何时激活/结束/取消能力
> * 何时应用/移除 Gameplay Effects
> * 属性值（属性在帧 X 的值）
>
> 我认为这可以在能力系统层面上通用地完成。但实际上使 UGameplayAbility 中的用户定义逻辑完全可回滚仍需要更多工作。我们可能会有一个完全可回滚的 UGameplayAbility 子类，并具有更有限的功能集或仅标记为回滚友好的能力任务。类似的东西。还有很多关于动画事件和根运动处理的影响。
>
> 希望有一个更明确的答案，但在触及 GAS 之前，确保基础设施正确是非常重要的。运动和物理必须稳固，然后才能改变更高层次的系统。



### 3. 是否计划将网络预测开发移到主分支？说实话，我真的很想查看最新的代码。不管它的状态如何。

> 我们正在朝这个方向努力。系统工作仍在 NetworkPrediction 中进行（参见 NetworkPhysics.h），基础的异步物理内容应该都可用（如 RewindData.h 等）。但我们在 Fortnite 中也有一些使用场景，显然不能公开。我们正在处理错误、性能优化等问题。
>
> 更多背景信息：在早期版本的工作中，我们非常关注“前端”部分——状态和模拟如何定义和编写。在这方面我们学到了很多。但随着异步物理内容的上线，我们更关注于让系统中的实际功能工作，虽然这意味着抛弃一些早期的抽象。目标是在实际功能工作之后回过头来重新统一这些东西。例如，回到“前端”，在我们现在正在工作的核心技术基础上完成最终版本。

### 4. 主分支上曾有一个用于发送 Gameplay Messages 的插件（看起来像 Event/Message Bus），但已被移除。是否有计划恢复它？考虑到游戏功能/模块化游戏插件，拥有一个通用的事件总线调度器将非常有用。

> 我认为你指的是 GameplayMessages 插件。这可能会在某个时候恢复——API 还没有真正确定，作者也不打算让它公开。我同意它对于模块化游戏设计会非常有用。但这不是我负责的领域，所以我没有更多信息。


### 5. 最近我在玩异步固定物理，结果很有前景。尽管如果将来有网络预测更新的话，我可能会先玩玩等待更新，因为要使它工作，我仍然需要将整个引擎设为固定刻度，而另一方面我尽量保持物理在33ms。这使得在30fps下体验不佳 (:。

我注意到有一些关于 Async CharacterMovementComponent 的工作，但不确定这是否会使用网络预测，或者这是一个独立的努力？

由于我注意到这一点，我也尝试实现我自己的异步运动在固定刻度率上，这工作还不错，但我还需要添加一个单独的插值更新。设置是将模拟刻度在单独的工作线程上以固定的33ms更新运行，进行计算，保存结果，并在游戏线程上进行插值以匹配当前的帧率。不是完美，但完成了工作。

我的问题是，未来是否会更容易设置这样的东西，因为有相当多的样板代码需要编写，（插值部分）并且单独对每个移动对象进行插值并不是特别高效。

异步功能真的很有趣，因为它允许你以固定的更新率运行游戏模拟（这将使固定线程不再必要）并且有更可预测的结果。这是未来计划中的内容吗？还是说更多地针对特定系统？据我记得，演员变换不会异步更新，蓝图也不是完全线程安全的。换句话说，这是计划在框架级别上支持的，还是每个游戏必须自己解决？

> **异步 CharacterMovementComponent**
>
> 这基本上是将 CharacterMovementComponent（CMC）移植到物理线程的早期原型/实验。我目前还不认为这是 CMC 的未来，但它可能会演变成那样。现在没有网络支持，所以这不是我真正关注的内容。参与者主要关注的是这个系统可能带来的输入延迟以及如何缓解。
>
> 目标是通过启用异步物理，您可以在变量刻度率下运行引擎，同时物理和“核心”游戏模拟可以以固定的速率运行（例如角色运动、车辆、GAS 等）。
>
> 目前需要设置的 cvars 是：  
> `p.DefaultAsyncDt=0.03333`  
> `p.RewindCaptureNumFrames=64`
>
> Chaos 确实提供了物理状态的插值（例如，推送回 UPrimitiveComponent 的变换，在游戏代码中可见）。现在有一个 cvar `p.AsyncInterpolationMultiplier`，可以控制它。如果您查看它，应该会看到物理体的平滑连续运动，而无需编写额外的代码。
>
> 如果您想插值非物理状态，目前仍然需要自己完成。示例可能是您希望在异步物理线程上更新（刻度）但在游戏线程上看到平滑连续插值的冷却时间，以便每个渲染帧更新冷却时间可视化。我们将来会解决这个问题，但目前还没有示例。
>
> 是的，所以这是目前系统的一个大问题。我们希望提供一个接口，让有经验的程序员可以用来最大化性能和安全性（编写“只需工作”的预测游戏代码，没有大量的危险和“你可以做但最好不要”）。例如，CharacterMovement 可能会做很多自定义工作以最大化性能，例如编写模板代码和进行批量更新，广泛使用，分解更新循环等。我们希望为这个用例提供一个良好的“低级”接口到异步线程和回滚系统。在这种情况下——这也是合理的——角色运动系统本身以自己的方式进行扩展。例如，提供一种蓝图自定义运动模式的方法，并提供一个线程安全的蓝图 API。
>
> 但我们认识到，这对于那些不需要自己“系统”的简单游戏对象来说是不够的。需要更符合 Unreal 的东西。例如，使用反射系统，拥有通用蓝图支持等。还有一些使用蓝图在其他线程上的示例（见 BlueprintThreadSafe 关键字和动画系统正在努力的方向）。所以我认为将来会有某种形式的解决方案。但我们还没有做到这一点。
>
> 我意识到您只是询问插值，但这是一般的答案：现在我们让您手动完成所有这些工作，如 NetSerialize、ShouldReconcile、Interpolate 等，但最终我们将提供一种方式，如果您只想使用反射系统，就不必手动编写这些内容。我们只是希望不强制所有人使用反射系统，因为这会引入其他限制，我们认为不想在系统的最低级别上接受。
>
> 然后，回到我之前说的 - 目前我们真的专注于让几个非常具体的示例工作并高效，然后我们将把注意力转回前端，做出易于使用和迭代的东西，减少样板代码等，以便其他人使用。


**[⬆ Back to Top](#table-of-contents)**

<a name="changelog"></a>

## 12. GAS 更新日志

这是一个GAS（Gameplay Ability System）重要更改的列表，包括修复、变更和新功能，汇编自官方Unreal Engine升级日志以及我遇到的未记录的更改。如果您发现了未列出的内容，请提交问题或拉取请求。

<a name="changelog-5.3"></a>
### 5.3

* **崩溃修复**: 修复了在无缝旅行后尝试应用Gameplay Cues时的崩溃问题。
* **崩溃修复**: 修复了使用Live Coding时，由GlobalAbilityTaskCount引起的崩溃问题。
* **崩溃修复**: 修复了在递归调用UAbilityTask_StartAbilityState等情况下，UAbilityTask::OnDestroy崩溃的问题。
* **错误修复**: 现在在子类中调用`Super::ActivateAbility`是安全的。之前，它会调用`CommitAbility`。
* **错误修复**: 增加了对不同类型的FGameplayEffectContext进行正确复制的支持。
* **错误修复**: FGameplayEffectContextHandle现在会在检索"Actors"之前检查数据是否有效。
* **错误修复**: 保留了Gameplay Ability System Target Data LocationInfo的旋转信息。
* **错误修复**: Gameplay Ability System现在仅在找到有效的PC时才停止搜索PC。
* **错误修复**: 在RemoveGameplayCue_Internal中使用现有的GameplayCueParameters（如果存在），而不是默认参数对象。
* **错误修复**: GameplayAbilityWorldReticle现在面向源Actor，而不是TargetingActor。
* **错误修复**: 如果事件数据通过GiveAbilityAndActivateOnce传递且能力列表被锁定，缓存触发事件数据。
* **错误修复**: 已添加对FInheritedGameplayTags的支持，以便其CombinedTags能立即更新，而不是等待保存。
* **错误修复**: 将ShouldAbilityRespondToEvent从仅客户端代码路径移动到服务器和客户端。
* **错误修复**: 修复了由于Curve Simplification导致FAttributeSetInitterDiscreteLevels在Cooked Builds中不起作用的问题。
* **错误修复**: 在GameplayAbility中设置CurrentEventData。
* **错误修复**: 确保MinimalReplicationTags在可能执行回调之前被正确设置。
* **错误修复**: 修复了ShouldAbilityRespondToEvent在实例化GameplayAbility上未被调用的问题。
* **错误修复**: Gameplay Cue Notify Actors在Child Actors上执行时不再在gc.PendingKill被禁用时泄漏内存。
* **错误修复**: 修复了GameplayCueManager中的问题，其中GameplayCueNotify_Actors可能由于哈希冲突而被“丢失”。
* **错误修复**: WaitGameplayTagQuery现在即使在Actor上没有Gameplay Tags时也会尊重其Query。
* **错误修复**: PostAttributeChange和AttributeValueChangeDelegates现在将具有正确的OldValue。
* **错误修复**: 修复了FGameplayTagQuery在结构体由本机代码创建时未显示正确的Query Description的问题。
* **错误修复**: 确保在使用Ability System时调用UAbilitySystemGlobals::InitGlobalData。之前，如果用户未调用它，Gameplay Ability System将无法正常工作。
* **错误修复**: 修复了从UGameplayAbility::EndAbility链接/取消链接动画层时的问题。
* **错误修复**: 更新了Ability System Component函数，以在使用之前检查Spec的能力指针。
* **新增**: 向FGameplayTagRequirements添加了GameplayTagQuery字段，以启用指定更复杂的要求。
* **新增**: 引入了FGameplayEffectQuery::SourceAggregateTagQuery，以增强SourceTagQuery。
* **新增**: 扩展了从控制台命令执行和取消Gameplay Abilities & Gameplay Effects的功能。
* **新增**: 添加了对Gameplay Ability Blueprints进行“审计”的能力，显示有关其开发和预期使用的信息。
* **变更**: OnAvatarSet现在在主要实例上调用，而不是在实例化的每个Actor的CDO上调用。
* **变更**: 允许在同一个Gameplay Ability Graph中同时使用Activate Ability和Activate Ability From Event。
* **变更**: AnimTask_PlayMontageAndWait现在有一个开关，允许在BlendOut事件后进行Completed和Interrupted。
* **变更**: ModMagnitudeCalc包装函数已声明为const。
* **变更**: FGameplayTagQuery::Matches现在对空查询返回false。
* **变更**: 更新了FGameplayAttribute::PostSerialize，以将包含的属性标记为可搜索名称。
* **变更**: 更新了GetAbilitySystemComponent，默认参数为Self。
* **变更**: 在AbilityTask_WaitTargetData中将函数标记为虚拟。
* **变更**: 删除了未使用的函数FGameplayAbilityTargetData::AddTargetDataToGameplayCueParameters。
* **变更**: 删除了遗留的GameplayAbility::SetMovementSyncPoint。
* **变更**: 从Gameplay任务和Ability系统组件中删除了未使用的复制标志。
* **变更**: 将一些Gameplay Effect功能移入可选组件。所有现有内容将在PostCDOCompiled期间自动更新以使用组件（如有必要）。

[查看详细更新](https://docs.unrealengine.com/5.3/en-US/unreal-engine-5.3-release-notes/)

<a name="changelog-5.2"></a>
### 5.2

* **错误修复**: 修复了`UAbilitySystemBlueprintLibrary::MakeSpecHandle`函数中的崩溃问题。
* **错误修复**: 修复了Gameplay Ability System中的逻辑问题，之前非控制的Pawn即使在服务器上本地生成（例如车辆），也会被视为远程。
* **错误修复**: 正确设置了被服务器拒绝的预测实例化能力的激活信息。
* **错误修复**: 修复了导致GameplayCues在远程实例上卡住的问题。
* **错误修复**: 修复了链式调用WaitGameplayEvent时发生的内存覆盖问题。
* **错误修复**: 在Blueprint中调用AbilitySystemComponent的`GetOwnedGameplayTags()`函数时，不再保留相同节点多次执行时的前一个调用返回值。
* **错误修复**: 修复了GameplayEffectContext复制对动态对象的引用的问题，这些引用永远不会被复制。
  * 这阻止了GameplayEffect调用`Owner->HandleDeferredGameplayCues(this)`，因为`bHasMoreUnmappedReferences`始终为true。
* **新增**: [Gameplay Targeting System](https://docs.unrealengine.com/en-US/gameplay-targeting-system-in-unreal-engine/) 是一种创建数据驱动的目标请求的方法。
* **新增**: 增加了对GameplayTag Queries的自定义序列化支持。
* **新增**: 添加了对派生FGameplayEffectContext类型的复制支持。
* **新增**: 资产中的Gameplay Attributes现在在保存时注册为可搜索的名称，允许在引用查看器中看到对这些属性的引用。
* **新增**: 为AbilitySystemComponent添加了一些基本的单元测试。
* **新增**: Gameplay Ability System Attributes现在尊重Core Redirects。这意味着你现在可以在代码中重命名Attribute Sets及其Attributes，并通过向DefaultEngine.ini中添加重定向条目，使其在用旧名称保存的资产中正确加载。
* **变更**: 允许通过代码更改Gameplay Effect Modifier的评估通道。
* **变更**: 从Gameplay Abilities插件中移除了先前未使用的变量`FGameplayModifierInfo::Magnitude`。
* **变更**: 移除了能力系统组件与智能对象实例标签之间的同步逻辑。

[查看详细更新](https://docs.unrealengine.com/5.2/en-US/unreal-engine-5.2-release-notes/)

<a name="changelog-5.1"></a>
### 5.1

* **错误修复**: 修复了复制的松散Gameplay标签未复制到所有者的问题。
* **错误修复**: 修复了AbilityTask的错误，之前可能会阻止能力及时进行垃圾收集。
* **错误修复**: 修复了基于标签激活的Gameplay Ability无法激活的问题。如果有多个Gameplay Ability监听该标签，而列表中的第一个无效或没有激活权限，就会发生此问题。
* **错误修复**: 修复了使用数据注册表的GameplayEffects在加载时发出警告的问题，并改进了警告文本。
* **错误修复**: 从UGameplayAbility中移除了错误地仅注册最后一个实例化能力的代码，这导致在Blueprint调试器中设置断点的问题。
* **错误修复**: 修复了如果在ApplyGameplayEffectSpecToTarget中调用EndAbility时Gameplay Ability System Ability卡住的问题。
* **新增**: 添加了支持Gameplay Effects添加被阻止的能力标签。
* **新增**: 添加了WaitGameplayTagQuery节点。一个基于UAbilityTask，另一个基于UAbilityAsync。此节点指定一个TagQuery，并根据配置，当查询变为真或假时，会触发其输出引脚。
* **新增**: 修改了AbilityTask调试的Console Variables，以在非发布版本中默认启用调试记录和日志打印（可根据需要进行热修复开/关）。
* **新增**: 现在可以将AbilitySystem.AbilityTask.Debug.RecordingEnabled设置为0以禁用，1以在非发布版本中启用，2以启用所有版本（包括发布版本）。
* **新增**: 可以使用AbilitySystem.AbilityTask.Debug.AbilityTaskDebugPrintTopNResults仅打印日志中的前N个结果（以避免日志垃圾）。
* **新增**: 使用STAT_AbilityTaskDebugRecording可以测试这些默认启用的调试更改的性能影响。
* **新增**: 添加了一个调试命令来过滤GameplayCue事件。
* **新增**: 向Gameplay Ability System添加了新的调试命令AbilitySystem.DebugAbilityTags、AbilitySystem.DebugBlockedTags和AbilitySystem.DebugAttribute。
* **新增**: 添加了一个Blueprint函数，用于获取Gameplay Attribute的调试字符串表示。
* **新增**: 添加了一个新的Gameplay Task资源重叠策略来取消现有任务。
* **变更**: 现在Ability Tasks应确保仅在执行必要的Ability指针操作后调用Super::OnDestroy，因为在调用后指针将被置为null。
* **变更**: 将FGameplayAbilitySpec/Def::SourceObject转换为弱引用。
* **变更**: 将Ability System Component引用在Ability Task中改为弱指针，以便垃圾收集器可以删除它。
* **变更**: 移除了冗余的枚举EWaitGameplayTagQueryAsyncTriggerCondition。
* **变更**: GameplayTasksComponent和AbilitySystemComponent现在支持注册的子对象API。
* **变更**: 添加了更好的日志记录，以指示为什么Gameplay Abilities无法激活。
* **变更**: 移除了AbilitySystem.Debug.NextTarget和PrevTarget命令，取而代之的是全局HUD NextDebugTarget和PrevDebugTarget命令。

[查看详细更新](https://docs.unrealengine.com/5.1/en-US/unreal-engine-5.1-release-notes/)

<a name="changelog-5.0"></a>
### 5.0

https://docs.unrealengine.com/5.0/en-US/unreal-engine-5.0-release-notes/

<a name="changelog-4.27"></a>
### 4.27

* **崩溃修复**: 修复了网络客户端在Actor执行使用恒定力量根运动任务的能力时崩溃的问题。
* **错误修复**: 修复了使用GameplayCues时编辑器加载时间的回归问题。
* **错误修复**: GameplayEffectsContainer的`SetActiveGameplayEffectLevel`方法在设置相同EffectLevel时将不再修改FastArray。
* **错误修复**: 修复了GameplayEffect混合复制模式中的边缘案例，其中未由网络连接明确拥有但通过`GetNetConnection`使用该连接的Actors未收到混合复制更新。
* **错误修复**: 修复了GameplayAbility的`EndAbility`类方法中的无限递归问题，该问题由从`K2_OnEndAbility`再次调用`EndAbility`引起。
* **错误修复**: 修复了在标签注册之前加载时GameplayTags Blueprint引脚被静默清除的问题。现在它们的行为与GameplayTag变量相同，可以通过项目设置中的ClearInvalidTags选项更改这两者的行为。
* **错误修复**: 改进了GameplayTag操作的线程安全性。
* **新增**: 将SourceObject暴露给GameplayAbility的`K2_CanActivateAbility`方法。
* **新增**: 引入了新的`FNativeGameplayTag`，使得可以创建一次性的本地标签，这些标签在模块加载和卸载时会正确注册和注销。
* **新增**: 更新了`GiveAbilityAndActivateOnce`以传入FGameplayEventData参数。
* **新增**: 改进了GameplayAbilities插件中的ScalableFloats，支持从新的数据注册系统动态查找曲线表。添加了ScalableFloat头文件，以便在能力插件之外更容易重用通用结构。
* **新增**: 添加了使用GameplayTag UI的代码支持，以便在其他编辑器自定义中使用，通过GameplayTagsEditorModule。
* **新增**: 修改了UGameplayAbility的PreActivate方法，以可选地接收触发事件数据。
* **新增**: 增强了在编辑器中过滤GameplayTags的支持，使用项目特定的过滤器。`OnFilterGameplayTag`提供了引用属性和标签源，因此可以根据请求标签的资产过滤标签。
* **新增**: 添加了在初始化后调用GameplayEffectSpec的类方法`SetContext`时保留原始捕获的SourceTags的选项。
* **新增**: 改进了注册GameplayTags的UI，以便从特定插件中注册。新的标签UI现在允许选择磁盘上的插件位置，用于新添加的GameplayTag源。
* **新增**: 在Sequencer中添加了一个新轨道，以允许在使用GameplayAbilitySystem构建的Actors上触发通知状态。像通知一样，GameplayCueTrack可以利用基于范围的事件或触发器事件。
* **变更**: 更改了GameplayCueInterface以按引用传递GameplayCueParameters结构。
* **优化**: 对加载和重新生成GameplayTag表进行了若干性能改进，以优化此选项。

[查看详细更新](https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/)

---

### 4.26

* **GAS插件**: 现在不再标记为测试版。
* **崩溃修复**: 修复了在没有有效标签源选择时添加Gameplay标签引发的崩溃。
* **崩溃修复**: 向消息添加了路径字符串参数，修复了UGameplayCueManager::VerifyNotifyAssetIsInValidPath中的崩溃。
* **崩溃修复**: 修复了在使用未检查的指针时在AbilitySystemComponent_Abilities中引发的访问违规崩溃。
* **错误修复**: 修复了在堆叠GameplayEffects时未重置额外实例效果的持续时间的问题。
* **错误修复**: 修复了导致CancelAllAbilities仅取消非实例化能力的问题。
* **新增**: 为Gameplay Ability提交函数添加了可选标签参数。
* **新增**: 向PlayMontageAndWait能力任务添加了StartTimeSeconds并改进了注释。
* **新增**: 向FGameplayAbilitySpec添加了标签容器“DynamicAbilityTags”。这些是与规格一起复制的可选能力标签。它们也被应用的Gameplay Effects捕获为源标签。
* **新增**: GameplayAbility的IsLocallyControlled和HasAuthority函数现在可以从Blueprint中调用。
* **新增**: Visual Logger现在仅在当前记录可视化日志数据时收集和存储有关即时Gameplay Effects的信息。
* **新增**: 在Blueprint节点中对Gameplay Attribute针脚添加了对重定向器的支持。
* **新增**: 添加了新功能，当根运动相关的能力任务结束时，它们将把运动组件的运动模式恢复到任务开始之前的运动模式。

[查看详细更新](https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_26/)

---

### 4.25.1

* **修复**: UE-92787 修复了保存包含Get Float Attribute节点的蓝图时崩溃的问题，当属性引脚内联设置时。
* **修复**: UE-92810 修复了生成具有内联更改的实例可编辑Gameplay标签属性的Actor时崩溃的问题。

---

### 4.25

* **修复**: 修复了`RootMotionSource` `AbilityTasks`的预测问题。
* **变更**: [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes)现在还接收旧的`Attribute`值。我们必须将其作为可选参数传递给我们的`OnRep`函数。以前，它通过读取属性值来尝试获取旧值。然而，如果从复制函数调用，旧值在达到SetBaseAttributeValueFromReplication之前已被丢弃，因此我们将获得新值。
* **新增**: 向`UGameplayAbility`添加了[`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy)。
* **崩溃修复**: 修复了在没有有效标签源选择时添加Gameplay标签引发的崩溃。
* **崩溃修复**: 移除了通过能力系统攻击者能够崩溃服务器的几种方式。
* **崩溃修复**: 现在在检查标签要求之前确保有一个GameplayEffect定义。
* **错误修复**: 修复了Gameplay标签类别未应用于Blueprint中函数参数的问题，如果它们是函数终止器节点的一部分。
* **错误修复**: 修复了在多个视口中Gameplay Effects的标签未复制的问题。
* **错误修复**: 修复了Gameplay Ability规格可能在循环触发能力时被InternalTryActivateAbility函数使无效的问题。
* **错误修复**: 更改了处理标签计数容器中Gameplay标签更新的方式。在删除Gameplay标签时推迟更新父标签时，现在会在父标签更新后调用相关的委托。这确保了标签表在委托广播时处于一致状态。
* **错误修复**: 现在在确认目标时，我们会在遍历生成的目标Actor数组之前先复制它，因为某些回调可能会修改该数组。
* **错误修复**: 修复了堆叠Gameplay Effects时，如果未重置额外实例的持续时间且调用者设置的持续时间会导致只有堆叠中的第一个实例正确设置持续时间的问题。堆叠中的所有其他GE规格将具有1秒的持续时间。添加了自动化测试以检测此情况。
* **错误修复**: 修复了处理Gameplay事件委托时可能修改Gameplay事件委托列表的问题。
* **错误修复**: 修复了GiveAbilityAndActivateOnce行为不一致的问题。
* **错误修复**: 重新排序了FGameplayEffectSpec::Initialize中的一些操作，以处理潜在的排序依赖性。
* **新增**: UGameplayAbility现在具有OnRemoveAbility函数。它遵循与OnGiveAbility相同的模式，仅在能力的主要实例或类默认对象上调用。
* **新增**: 显示被阻止的能力标签时，调试文本现在包括被阻止标签的总数。
* **新增**: 将UGameplayAbility::InternalServerTryActiveAbility重命名为UGameplayAbility::InternalServerTryActivateAbility。之前调用InternalServerTryActiveAbility的代码现在应调用InternalServerTryActivateAbility。
* **新增**: 继续使用过滤文本显示Gameplay标签，当添加或删除标签时。以前的行为会清除过滤器。
* **新增**: 在编辑器中添加新标签时，不会重置标签源。
* **新增**: 添加了查询能力系统组件以获取具有指定标签集的所有活动Gameplay Effects的功能。新函数名为GetActiveEffectsWithAllTags，可以通过代码或蓝图访问。
* **新增**: 当根运动相关的能力任务结束时，它们现在将运动组件的运动模式恢复到任务开始之前的运动模式。
* **新增**: 将SpawnedAttributes设为瞬态，以防止保存可能变

得陈旧和不正确的数据。添加了null检查以防止当前保存的陈旧数据传播。这可以防止与存储在SpawnedAttributes中的坏数据相关的问题。
* **API变更**: AddDefaultSubobjectSet已被弃用。应使用AddAttributeSetSubobject代替。
* **新增**: Gameplay Abilities现在可以指定播放蒙太奇的Anim Instance。

[查看详细更新](https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_25/)

<a name="changelog-4.24"></a>
### 4.24

* **错误修复**: 修复了蓝图节点`Attribute`变量在编译时重置为`None`的问题。
* **变更**: 需要调用 [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) 来使用 [`TargetData`](#concepts-targeting-data)，否则会出现 `ScriptStructCache` 错误并导致客户端从服务器断开连接。从4.24开始，建议在每个项目中都调用此方法，而在此之前它是可选的。
* **错误修复**: 修复了将`GameplayTag`设置器复制到之前未定义变量的蓝图时出现的崩溃问题。
* **改进**: `UGameplayAbility::MontageStop()`函数现在正确使用`OverrideBlendOutTime`参数。
* **错误修复**: 修复了组件上的`GameplayTag`查询变量在编辑时未被修改的问题。
* **新增**: 为`GameplayEffectExecutionCalculations`添加了支持针对“临时变量”的范围修饰符的功能，这些变量不需要由属性捕获支持。
  * 实现基本上使得可以创建由`GameplayTag`标识的聚合器，作为执行的一种方式，以便让执行暴露一个临时值以便使用范围修饰符进行操作；现在可以构建需要可操作值的公式，而这些值无需从源或目标捕获。
  * 使用方法：执行必须将标签添加到新的成员变量`ValidTransientAggregatorIdentifiers`中；这些标签会显示在范围修饰符计算修改器数组的底部，标记为临时变量—相关的详细信息自定义也会进行更新以支持此功能。
* **新增**: 添加了受限标签的质量改进。移除了受限`GameplayTag`源的默认选项。现在添加受限标签时不会重置源，这使得连续添加多个标签变得更容易。
* **改进**: `APawn::PossessedBy()`现在将`Pawn`的所有者设置为新的`Controller`。这很有用，因为 [混合复制模式](#concepts-asc-rm) 期望`Pawn`的所有者是`Controller`，如果`ASC`存在于`Pawn`上。
* **错误修复**: 修复了`FAttributeSetInitterDiscreteLevels`中POD（Plain Old Data）的错误。

[查看详细更新](https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_24/)

**[⬆ Back to Top](#table-of-contents)**
