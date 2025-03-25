### 房间人数

```c#
//方法
    GetInitialAttr_maxPlayers()
        -->
        return 4;
```

>1. 对`return 4;`右键使用IL编辑
>2. 将lcd.i4.4改为`lcd.i4`
>3. 旁边会出现数字0
>4. 将 0 更改为 x 就是房间人数

###   【天赋】商店刷新次数

```c#
//类
Star_Global_ShopRefresh
	-->
	//增加的次数
	allowedShopRefreshes = 1;
```

###  【天赋】商店商品数量

```c#
//类
Star_Global_ShopMoreItems
	-->
	//具体数量为 2*(3+x)
	base.player.shopAddedItems = 1;
```

###  【天赋】商店折扣

```c#
Star_Global_ShopDiscount
	-->
	BonusRatio = new float[] { 0.33f, 0.33f, 0.33f, 0.33f };
```

###  【拓展】商品、净化、刷新、出售一元购（x）丨不推荐

> 标注（x）就是未成功、或者暂时未尝试、或者不完全生效、或者其他的问题

```c#
//商品
Cost Gold
-->gold = amount
 	-->改为
 	gold = 1
    
    -->改为根据周目数结算
    gold = Math.Max((int)Math.Ceiling((-1.0 + Math.Sqrt((double)(1 + 8 * (NetworkedManagerBase<ZoneManager>.instance.loopIndex + 1)))) / 2.0), 1)
 
//净化，有两处
GetCleanseGoldCost
	-->改
	return 1;

//相关-还未验证
Shrine_AltarOfCleansing
-->UserCode_CmdCleanse__NetworkBehaviour__NetworkConnectionToClient
    -->
    int cost = Math.Max((int)Math.Ceiling((-1.0 + Math.Sqrt((double)(1 + 8 * (NetworkedManagerBase<ZoneManager>.instance.loopIndex + 1)))) / 2.0), 1);
	-->
    int cost2 = Math.Max((int)Math.Ceiling((-1.0 + Math.Sqrt((double)(1 + 8 * (NetworkedManagerBase<ZoneManager>.instance.loopIndex + 1)))) / 2.0), 1);
	
//刷新Refresh
GetRefreshGoldCost
	 -->改
	return 1;

	 -->改为根据周目数结算
	return Mathf.FloorToInt(Mathf.Sqrt((float)(NetworkedManagerBase<ZoneManager>.instance.loopIndex + 1)));

//出售sell
GetSellGold
-->
GetSellGold(DewPlayer)
    -->改
	return 1;

PropEnt_Merchant_Base
-->OnSell(DewPlayer
    //出售给钱，固定为x
    hero.owner.EarnGold(x);
    //同理可添加，出售给周目*2尘
    hero.owner.EarnDreamDus((NetworkedManagerBase<ZoneManager>.instance.loopIndex + 1) * 2);
          
UI_InGame_SkillButton_EditSkill.UpdateCostDisplayStatus
-->修改
this.costDisplay.SetupGold(this.skill.GetSellGold(DewPlayer.local), false, true);
-->       
this.costDisplay.SetupGold(1, false, true);
          
PropEnt_Merchant_Base.OnSell
-->修改
this.costDisplay.SetupGold(this.skill.GetSellGold(DewPlayer.local), false, true);
-->
int sellGold = skillTrigger.GetSellGold(activator);
-->
int sellGold = 1;
```

> 净化原文：
>
> ```c#
>   public int GetCleanseGoldCost(SkillTrigger **skill**)
>   {
>     return Mathf.RoundToInt(base.Evaluate((float)**skill**.level));
>   }
> 
>   // Token: 0x0600183C RID: 6204 RVA: 0x000138B3 File Offset: 0x00011AB3
>   public int GetCleanseGoldCost(Gem **gem**)
>   {
>     return Mathf.RoundToInt(this.gss.gemCleanseCostByQuality.Evaluate((float)**gem**.quality));
>   }
> ```

> 刷新原文：
>
> ```c#
> public int GetRefreshGoldCost(DewPlayer player)
> 	{
> 		if (this.remainingRefreshes[player.netId] <= 0)
> 		{
> 			return 99999;
> 		}
> 		float sum = 0f;
> 		foreach (MerchandiseData i in this._merchandises[player.netId])
> 		{
> 			sum += (float)i.price.gold;
> 		}
> 		return Mathf.RoundToInt(sum / (float)this._merchandises[player.netId].Length * 0.5f);
> ```

### 【天赋】金币掉落

```c#
Star_Global_KillGoldBonus
	-->
	BonusRatio = new float[] { 1.66f, 1.66f, 1.66f, 1.66f, 1.66f };
```
### 【天赋】梦尘掉落
```c#
Star_Global_KillDreamDustBonus
	-->
	GainedAmount = 10;
	GainChance = new float[] { 0.66f, 0.66f, 0.66f, 0.66f, 0.66f };
```

###  【天赋】治疗药水掉落

```c#
Star_Global_IncreasePotionDrop
	-->
	PotionChanceIncrease = new float[] { 6.66f, 6.66f, 6.66f };
```

###  【天赋】拆解额外梦尘

```c#
Star_Global_DismantleDreamDustBonus
	//拆解收益=基础倍率+额外倍率
	-->
	
	dismantleDreamDustMultiplier = 1.66f+ Star_Global_DismantleDreamDustBonus.BonusDreamDustRatio.GetClamped(base.level - 1);
	
	-->
	BonusDreamDustRatio = new float[] { 0.66f, 0.66f, 0.66f, 0.66f };
```

###  【天赋】净化返还梦尘

```c#
Star_Global_Cleanse
	-->
	DreamDustRefundMultiplier = new float[] { 1.11f, 1.11f, 1.11f };
```

###  【天赋】初始技能等级

```c#
Star_Global_StartSkill
	--> 实际等级=x-1
	SkillLevel = new int[] { 21, 21, 21 };
```

###  【拓展】开局给予随机技能/精华

```c#
Star_Global_StartSkill
-->OnStartInGame()
-->在 vector = Dew.GetGoodRewardPosition(vector, 1f); 右键编辑类
-->下一行添加随机精华
	//比如Gem_E_Predation是掠食精华
	List<string> list = new List<string> { "Gem_E_Predation" };
	Dew.CreateGem<Gem>(DewResources.GetByShortTypeName<Gem>(list[Star_Global_StartSkill.random.Next(list.Count)]), vector, 80, base.player, null);
-->顶部添加
	using System.Collections.Generic;
-->尾部添加（最后一个括号上方
	private static readonly global::System.Random random = new global::System.Random();
```

###  【拓展】开局给予指定技能/精华

```c#
//指定技能
Dew.CreateSkillTrigger<SkillTrigger>(DewResources.GetByShortTypeName<SkillTrigger>("St_L_Multishot"), vector, Star_Global_StartSkill.SkillLevel.*GetClamped*(base.level - 1), base.player, null);

//指定精华
Dew.CreateGem<Gem>(DewResources.GetByShortTypeName<Gem>("St_L_Multishot"), vector, Star_Global_StartSkill.SkillLevel.*GetClamped*(base.level - 1), base.player, null);
```

###  【角色】光系增伤率

```c#
Star_Global_LightAmp
	-->
	BonusAmpPerStack = new float[] { 0.4f, 0.4f, 0.4f, 0.4f };
```

###  【天赋】暗系增伤率

```c#
Star_Global_DarkAmp
	-->
	BonusAmpPerStack = new float[] { 0.4f, 0.4f, 0.4f, 0.4f };
```

###  【天赋】寒冷亲和

```c#
//受到寒冷状态怪物伤害减伤率
Star_Global_DecreaseDamageFromChilled
	-->
	DamageReduction = new float[] { 0.66f, 0.66f, 0.66f, 0.66f };
```


###  【天赋】燃烧增伤率

```c#
//在不讨论保命的纯输出情况下，叠加燃烧伤害是最暴力的
Star_Global_FireAmp
	-->
	 FireAmp = new float[] { 0.5f, 0.5f, 0.5f, 0.5f, 0.5f };
```

###  【天赋】装备第一个精华提升品质等级

```c#
Star_Global_FirstGemUpgrade
	-->百分比
	BonusQuality = new int[] { 80, 80, 80};
```

###  【拓展】移除精华

```
SelectGemAndQuality
-->
```

###  【天赋】第二风（金身状态）持续时间及次数

```c#
Star_Global_FatalHitProtection
	-->
    //名刀司命的金身持续时间，以及复活后金身的持续时间
    InvulnerableTime = 3.5f;
```

###  【角色】韧性

```c#
Star_Global_Tenacity
	-->
    //韧性用于减免控制技能的持续时间
    //效果为100/（100+韧性)，也就是100韧性减少到50%，200韧性减少到33%
	BonusTenacity = new float[] { 66f, 66f, 66f };
```

###  【角色】元素抗性

```c#
Star_Global_ElementResistance
	-->
    //没有实际测试过，但应该是通用的100/（100+属性)
	DurationReduction = new float[] { 0.66f, 0.66f, 0.66f, 0.66f };
```

###  【角色】闪避冷却

```c#
Star_Global_DodgeAbilityCooldown
	-->
    //闪避冷却时间减少的百分比
	CooldownMultiplier = new float[] { 0.45f, 0.45f, 0.45f, 0.45f };
```

###  【角色】初始金币

```c#
Star_Global_StartGold
	-->
	GoldAmount = new int[] { 100, 100, 100 };
```

###  【角色】初始技能冷却

```c#
Star_Global_SkillHaste
	--> 
	BonusSkillHaste = new int[] { 33, 33, 33 };
```

###  【角色】初始额外生命

```c#
Star_Global_MaxHealth
	--> 默认最高是150f
	BonusHealth = new float[] { 150f, 150f, 150f, 150f };
```

###  【角色】初始额外攻击

```c#
Star_Global_AttackDamage
	--> 
	AttackDamageBonus = new int[] { 22, 22, 22, 22, 22 };
```

###  【角色】初始额外法强

```c#
Star_Global_AbilityPower
	--> 
	AbilityPowerBonus = new int[] { 33, 33, 33, 33, 33 };
```

###  【拓展】1.5倍攻击、法强

```c#
EntityStatus
-->CalculateStats()
	-->
    //攻击
	this.attackDamage *= 1f + this.bonusStats.attackDamagePercentage / 100f;
	-->1.5f + this.bonusStats.attackDamagePercentage / 67f;
    //法强
	this.abilityPower *= 1f + this.bonusStats.abilityPowerPercentage / 100f;
	-->1.5f + this.bonusStats.abilityPowerPercentage / 67f;
```


###  【角色】初始额外攻速

```c#
Star_Global_AttackSpeed
	--> 
	AttackSpeedBonus = new float[] { 111f, 111f, 111f, 111f, 111f };
```

###  【角色】初始护甲

```c#
Star_Global_Armor
	-->
    //效果为100/（100+护甲)，也就是100护甲减少到50%，200护甲减少到33%
	ArmorBonus = new int[] { 200, 200, 200, 200, 200 };
```


###  【角色】第四槽位

```c#
HeroSkill
--> GetMaxGemCount
--> 
	if (type == HeroSkillLocation.Q || type == HeroSkillLocation.W || type == HeroSkillLocation.E)
	{
		return 4;
	}
	if (type == HeroSkillLocation.R)
	{
		return 4;
	}
	return 4;
```

> 修改return后数字的方法和`房间人数`相同

###  【难度】基本修改位置

```c#
Dew.Core
	-->
	//类搜索
	DewDifficultySettings
	-->
	internal void ApplyDifficultyModifiers(Entity entity)
	{ 》标有难度的this.内容放在此处《
```


###  【难度】人口数量倍数

```c#
//清醒梦人口过剩/LucidDream_Overpopulation : LucidDream)
	LucidDream_Overpopulation
		-->
		
		//开启人口过剩倍率
		-->OnCreate()
		maxAndSpawnedPopulationMultiplier = 1.5f;
		
		//正常人口倍率
		-->OnDestroyActor()
		maxAndSpawnedPopulationMultiplier = 1f;
```

> 或者用
>
> ```c#
> this.maxPopulationMultiplier *= 1f
> ```

### 【难度】有益节点倍率

```c#
this.beneficialNodeMultiplier *= 100f;
```

### 【拓展】治疗费用

```
Cost Shrine
-->GetCost
-->
return new Cost
		{
			gold = 0,
			healthPercentage = Mathf.RoundToInt(healthPercentage)
		};
```

###  【难度】有害节点倍率

```c#
this.harmfulNodeMultiplier *= 100f;
```

### 【难度】猎人扩散速度

```c#
this.hunterSpreadChance *= 1f;
```

###  【难度】治疗倍率

```c#
this.healRawMultiplier *= 0.5f;
```

> 0.5f在最低难度下也无法回血

###  【难度】设置灵魂距离

```c#
this.lostSoulDistance.Set(0,0);
```

###  【难度】怪物生命值倍数
```c#
this.enemyHealthPercentage *= 6.66f;
```

###  【难度】怪物攻击倍数
```c#
this.enemyPowerPercentage *= 3.33f;
```

###  【难度】怪物法强倍数
```c#
this.enemyPowerPercentage *= 3.33f;
```

###  【难度】怪物移速倍数
```c#
this.enemyMovementSpeedPercentage *= 1.66f;
```

###  【难度】怪物攻速倍数
```c#
this.enemyAttackSpeedPercentage *= 1.66f;
```

###  【难度】怪物技能CD倍数
```c#
this.enemyAbilityHasteFlat *= 1.66f;
```

###  【难度】怪物成长曲线
```c#
this.scalingFactor *= 1.005f;
```

###  【难度】怪物基础数值修改

```c#
//一般修改x即可，x默认100

// 怪物生命值百分比    
maxHealthPercentage = (healthMultiplier - 1f) * xf,

// 怪物攻击百分比
attackDamagePercentage = (damageMultiplier - 1f) * xf,

// 怪物法强百分比
abilityPowerPercentage = (damageMultiplier - 1f) * xf
```

###  【难度】特殊技能触发频率倍率

```c#
this.specialSkillChanceMultiplier = 6.66f;
```

###  【难度】怪物技能对玩家位置的采集率(越大越难)
```c#
this.positionSampleCount = 6;
```

###  【资源】星尘掉落量

```c#
//单份单次掉落量
this.gainedStardustAmountOffset = 100;
```

###  【资源】地图金币块价值

```c#
PropEnt_Stone_Gold 
-->OnDeath
-->float floatAmount = this.amountByZoneIndex.Evaluate((float)NetworkedManagerBase<ZoneManager>.instance.currentZoneIndex);
	-->
	//改为固定的掉落x金币
	float floatAmount = x;
```

###  【资源】地图梦尘块价值

```c#
PropEnt_Stone_DreamDust
-->OnDeath
-->float floatAmount = this.amountByZoneIndex.Evaluate((float)NetworkedManagerBase<ZoneManager>.instance.currentZoneIndex);
	-->
	//改为固定的掉落x梦尘
	float floatAmount = x;
```

###  【资源】地图金币块生成

```c#
RoomMod_GoldEverywhere
```

### 【BOSS】双boss

```c#
RoomMonsters
--> SpawnMonstersRoutine
--> rule.isBossSpawn
	//x就是数量，改法和改房间人数一样
	waves = x;
```
> 改不了就搜if (this.<rule>5__3.isBossSpawn)
> 
> 直到下面同时出现
> 
> ```c#
> this.<waves>5__4 
> 
> this.<hunterChance>5__5 
> 
> this.<mirageChance>5__6 
> ```
> 
> 对this.<waves>5__4 右键IL编辑
> 
> 改此处对`miniboss`部分也生效，无需再修改miniboss

###  【BOSS】双miniboss

```c#
RoomMonsters
--> SpawnMonstersRoutine
--> SpawnMiniBoss
	--> 
	settings.rule.wavesMax = 2;
	settings.rule.wavesMin = 2;
```

###  【BOSS】真·miniboss刷新
```c#
CombatRoom
-->搜索 
MiniBossCombatNodesVisited
	-->
	//原为0，推荐还是0
	//如果需要每个房间都有miniboss，那么请勿进光之碎片相关的房间
	this._visitedCombatNodesWithoutMiniBoss = 10000;

	-->//原为1
	this._visitedCombatNodesWithoutMiniBoss + 10000;
```

>  相关
>
> ```c#
> ZoneManager
> miniBossSpawnChanceByNonMiniBossCombatNodesVisited
> ```

###  【BOSS】每周目boss数量+1

```c#
//周目计数器-->loopIndex
if (rule.isBossSpawn)
{
	waves = NetworkedManagerBase<ZoneManager>.instance.loopIndex +1 ;
}
```

### 【BOSS】BOSS特殊状态
```c#
RoomMonsters
--> SpawnMonstersRoutine
--> rule.isBossSpawn
	-->
	
	//猎人化（更硬、更快）
	hunterChance = 100f;
	
	//幻想化（约4-5倍血量的盾+免控+90%减治疗）
	mirageChance = 100f;
```
###  【BOSS】幻想化减治疗
```c#
Se_MirageSkin_Delusion_Delusional
-->
VictimOntakenHealProcessor
    -->
    //x=0.8时，减少80%治疗
    float health_reduce_multiplier =xf;
```
    
###   【BOSS】对boss限伤

```c#
//对boss造成的单次伤害无法超过其最大生命值的10%
FinalDamageData
-->
FinalDamageData(DamageData data, float armorAmount, Entity victim)
-->
	if (victim as BossMonster != null)
	{
		//10%=0.1
		num = Mathf.Min(num, victim.Status.maxHealth *0.1f);
	}
```

###  【地图】本地图复活

```c#
ZoneManager
-->	<TryGetNodeIndexForNextGoal>g__GetScore|0
--> this.<>4__this.currentNodeIndex
	//score -= x实际是降低优先级，改成-x就是提高优先级，x越高越高
	--> score -= -10000f
```

> 推荐搭配`设置灵魂距离`食用

###  【地图】超级原地复活

```c#
GetNodeIndexSettings
--> allowedTypes
	--> 
	public WorldNodeType[] allowedTypes = new WorldNodeType[] {
	WorldNodeType.Start,
	WorldNodeType.Combat,
	WorldNodeType.Event,
	WorldNodeType.Merchant,
	WorldNodeType.Quest,
	WorldNodeType.Special,
	WorldNodeType.ExitBoss}
	
//修复boss房不能复活
Se_HeroKnockedOut
-->ActiveLogicUpdate
    -->
    //IL编辑-删除
    && NetworkedManagerBase<ZoneManager>.instance.currentNode.type != WorldNodeType.ExitBoss
```

### 【地图】提高怪物波次（x）

```c#
//忘记位置了，先忽略这条
public int wavesMin = 3;
public int wavesMax = 5;
```

### 【混沌】混沌必定为红

```c#
Shrine_Chaos
-->SetRandomRarity
	-->
	this.Networkrarity = xxx;
	全部改为
	this.Networkrarity = Rarity.Legendary;
	
	或，把if部分全去掉
	只留
	this.Networkrarity = Rarity.Legendary;
```

> | 颜色 | 对应             |
> | ---- | ---------------- |
> | 白   | Rarity.Common    |
> | 蓝   | Rarity.Rare      |
> | 紫   | Rarity.Epic      |
> | 红   | Rarity.Legendary |

### 【混沌】混沌提供提供的选择数量（x）

```c#
Shrine_Chaos
	-->
    numOfChoice = 7;
```

```c#
UI_InGame_FloatingWindow_Chaos
-->
UpdateItems()
-->
```

###  【混沌】混沌奖励的类型

```c#
ChaosRewardType
```

###  【灵魂】提升层主灵魂的奖励

```c#
Shrine_BossSoul
Ai_RandomGemUpgrade
--> OnComplete
	-->
	// x就是倍率
	+= this.addedQuality * x;
	+= this.addedLevel * x;
```

###  【遗物】移除遗物任务

```c#
QuestManager.OnStartServer()
	-->删除
	this.DoArtifactStartServer(); 
```

###  【BOSS】层主1·癫狂掉率

```c#
//类搜索
Mon_Forest_BossDemon 
	(this.dropHerWorldChance * 10f)
	public float dropHysteriaChance = 0.1f;
```

###  【BOSS】层主3·她的世界掉落（x）

```c#
//类搜索
Mon_Sky_BossNyx
	(this.dropHerWorldChance * 10f)	
	public float dropHerWorldChance = 0.1f;
```

###  【UI】商店

```c#
UI_InGame_FloatingWindow_Shop
-->
UpdateMerchandise()
-->
this.grid.constraintCount = 3 + DewPlayer.local.shopAddedItems;
	-->改成
    //UI对 x作为列数 进行自适应
    //客机不生效
	this.grid.constraintCount = x;
```

###  【UI】开局公告

```c#
Forest_LoopCat_Spawner
-->
OnCreate
-->
		if (NetworkedManagerBase<ZoneManager>.instance.currentZoneIndex == 0)
		{
			NetworkedManagerBase<ChatManager>.instance.BroadcastChatMessage(new ChatManager.Message
			{
				type = ChatManager.MessageType.Raw,
				content = "bossrush v0.0.5 机制说明:\n每层获得300+200*周目数金币，500尘\n2周目开始减疗幅度会逐渐增加\n对boss造成的单次伤害无法超过其最大生命值的10%\n第一次攻击商人会免费刷新物品\n仇恨神龛不会出友伤诅咒，总是给予一个红色混沌\n埃尔的圣域会复活队友，世界破坏者释放不会被打断，神圣信仰杀一个怪即可叠满\n关注qq联机/改包教程群 1034195007 谢谢喵"
			});
		}
```
###  【技能】牢癫·增强

```c#
Se_L_Hysteria
-->
OnCreate
-->
在base.SetTimer(this.duration);前面一行
-->原文
    //减少移速
    base.DoSpeed(-50f);

-->加入
    //减去当前所有护甲（x）未实现
    base.DoArmorReduction(1f);
	//增加护甲x点
	base.DoArmorBoost(6666);

	//增加护甲 1.44的技能等级的次方，在24级时打到6000+护甲，并且护甲不超过6666
	base.DoArmorBoost(Math.Min(Mathf.Pow(1.44f, (float)base.skillLevel), 6666f));
	//增加护甲 1.79的技能等级的次方，在15级时打到6000+护甲，并且护甲不超过6666
	base.DoArmorBoost(Math.Min(Mathf.Pow(1.79f, (float)base.skillLevel), 6666f));

-->修改
    //持续时间增加 技能等级*0.6+周目*1.3
    base.SetTimer(this.duration + (float)base.skillLevel * 0.6f + (float)NetworkedManagerBase<ZoneManager>.instance.loopIndex * 1.3f);

-->删除
	//解除禁止释放技能
	this._handle.LockAllAbilitiesCast();

//攻击频率
Se_L_Hysteria
-->
ActiveLogicUpdate
-->
	//挥舞频率提高x倍
	float num = this.clawInterval / Mathf.Max(base.victim.Status.attackSpeedMultiplier * 6.66f, 1f);

//伤害减少，降低为6%
Ai_L_Hysteria_Claw
-->OnBeforeDispatchDamage
	-->添加
	dmg.ApplyReduction(0.922f);

//治疗效果
Ai_L_Hysteria_Claw
-->OnHit
-->
	//治疗效果提高x倍
healData.ApplyAmplification(this.healAmp * xf);
	//攻击叠血
	StatBonus statBonus = new StatBonus();
	statBonus.maxHealthFlat += (float)Mathf.FloorToInt(Mathf.Sqrt((float)base.skillLevel)) * 1f;
	base.info.caster.Status.AddStatBonus(statBonus);
    
```

###  【测试】癫狂强化后的dmg.ApplyReduction()效果测试

| dmg.ApplyReduction() | 实际伤害 |
| -------------------- | -------- |
| 0.922f               | 13点     |
| 0.5f                 | 85点     |
| 05f+仁慈             | 95点     |

###  【技能】圣域·增加复活机制

```c#
Ai_R_SanctuaryOfEl_Ground
-->
OnCreateSequenced
-->
//在 this.Network_desiredPosition = base.info.caster.agentPosition; 下一行添加
 		foreach (DewPlayer dewPlayer in DewPlayer.humanPlayers)
        {
            if (dewPlayer.hero != null && dewPlayer.hero.isKnockedOut)
            {
                Entity hero = dewPlayer.hero;
                hero.CreateAbilityInstance<Ai_ReviveHero>(base.info.caster.agentPosition, null, new CastInfo(hero, hero), delegate(Ai_ReviveHero a)
                {
                    a.reviveHealthMultiplier = 0.4f;
                });
            }
        }
```

> 改正报错：
>
> 1. 删除【protected unsafe override IEnumerator OnCreateSequenced()】方法声明中的`unsafe `
> 2. Ctrl+F搜索*readOnlySpan，三处都改为`readOnlySpan`，也就是去掉星号

###  【技能】箭雨·增强

```c#
Se_L_Multishot_Buff.OnCreate
-->在 base.SetTimer(this.duration); 前插入
    
    //基础+2，每2级攻击距离+1
	float rangeBonus = 3+(float)base.skillLevel / 2f;
	//每3级攻击距离+1
	float rangeBonus = (float)base.skillLevel / 3f;

	if (base.info.caster != null && base.info.caster.Ability.attackAbility != null)
	{
		base.info.caster.Ability.attackAbility.configs[0].castMethod._range += rangeBonus * 1f;
		base.info.caster.Ability.attackAbility.configs[1].castMethod._range += rangeBonus * 1f;
		base.info.caster.Ability.attackAbility.SyncCastMethodChanges(0);
		base.info.caster.Ability.attackAbility.SyncCastMethodChanges(1);
	}

Se_L_Multishot_Buff.OnDestroyActor
	-->在 base.OnDestroyActor(); 前插入
    //技能结束后恢复攻击距离
    if (base.info.caster != null && base.info.caster.Ability.attackAbility != null)
	{
        
    	//基础+2，每2级攻击距离+1
		float rangeBonus = 3+(float)base.skillLevel / 2f;
		//每3级攻击距离+1
		float rangeBonus = (float)base.skillLevel / 3f;
        
		base.info.caster.Ability.attackAbility.configs[0].castMethod._range -= rangeBonus;
		base.info.caster.Ability.attackAbility.configs[1].castMethod._range -= rangeBonus;
		base.info.caster.Ability.attackAbility.SyncCastMethodChanges(0);
		base.info.caster.Ability.attackAbility.SyncCastMethodChanges(1);
	}

Se_L_Multishot_Buff
-->IEnumerator
-->ShootArrowSequence(EventInfoAttackEffect
	-->
   	//并且每有一点额外攻击距离，伤害提高12% ——> 也就是每级提高4%伤害。
	a.strength = obj.strength * (1f + (float)this.skillLevel * 0.04f);
```

> 闪避相关的研究：
>
>  Ai_GenericDodge
> 
>  Star_Global_DodgeAbilityCooldown

 ###  生成地图

```c#
zonemanager
	-->generateworld函数负责（）
```


###  Boss事件

```c#
BossMonster
	//死亡事件
	OnDeath
```

###  随着等级限制技能极速（x）

```c#
this.abilityHaste = Mathf.Max(this.abilityHaste, this._level * 3);
```