<p align="center">
    <img src="http://raw.pixeye.games/logo_framework.png" alt="Actors">
</p>

[![Join the chat at https://gitter.im/ActorsFramework/Lobby](https://img.shields.io/badge/gitter-join%20chat-green.svg?style=flat-square)](https://gitter.im/ActorsFramework/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Join the chat at https://discord.gg/ukhzx83](https://img.shields.io/badge/discord-join%20channel-brightgreen.svg?style=flat-square)](https://discord.gg/ukhzx83)
[![Twitter Follow](https://img.shields.io/badge/twitter-%40dimmPixeye-blue.svg?style=flat-square&label=Follow)](https://twitter.com/dimmPixeye)
[![license](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](https://github.com/dimmpixeyeUnity-Framework/master/LICENSE)

## Actors - Компонентно-ориентированный паттерн для Unity3d

ACTORS небольшой фреймворк созданный для Unit3d. Он используется для облегчения разделения данных от поведения без тонн кода. Фреймворк полагается на концепт использования monobehavior'а Unity3d, но без ненужных издержек.

### Обзор игрового кода :
```csharp
 public class ActorPlayer : Actor, ITick
{

	[FoldoutGroup("Setup")] public DataMove dataMove;
	 
    	     protected override void Setup()
		{
			Add(dataMove);
			Add<BehaviorInput>();
		}
 
}
```
```csharp
	[System.Serializable]

	public class DataMove : IData
	{
		public float x;
		public float y;

		public void Dispose()
		{
		}
	}
```	
```csharp
	public class BehaviorInput : Behavior, ITick
	{

		[Bind] private DataMove dataMove;


		public override void OnTick()
		{
			dataMove.x = Input.GetAxis("Horizontal");
			dataMove.y = Input.GetAxis("Vertical");
		}
	}
	
```

## Содержание

* Установка
    * <a href="#Installation">Загрузка</a>
    * <a href="#Overview">Обзор проекта</a>
    * <a href="#Setup">Установка проекта</a>
    * <a href="#Scenes Setup">Создание сцен</a>
* Basic stuff  
    * <a href="#Actors overview">Обзор акторов</a>
    * <a href="#Data component">Компонент данных (Data)</a>
    * <a href="#Behavior component">Компонент поведения (Behavior)</a>
    * <a href="#Signals">Сигналы</a>
    * <a href="#Interfaces overivew">Обзор интерфейсов</a>
    * <a href="#Processings">Обработчики (Processings)</a>  
        * <a href="#ProcessingSceneLoad">Как изменять сцену</a>
    * <a href="#Object pooling">Пул объектов (Object pooling)</a>
    * <a href="#Creating and destroying new objects">Создание и удаление новых объектов</a>
    * <a href="#Blueprints">Схемы (Blueprints)</a>
    * <a href="#Tags">Теги</a>
    * <a href="#ECS">ECS</a>

##  <a id="Installation"></a>Загрузка
Из источника
* Клонируйте этот репозиторий и откройте его как новый проект Unity3D.

## <a id="Overview"></a>Обзор проекта
Проект включает в себя несколько папок:
* [-]Common : Для использования встроенных библиотек, плагинов фреймворка.
* [0]Framework : Код фреймворка. Вам не нужно тут ничего трогать.
* [1]Source : Игровой код.
* [2]Content : Игровой контент: сцены, графика, аудио и т.д.
* [3]ThirdParty : Используемые вами библиотеки из Asset store.
* Plugins : Папка для плагинов unity3d.

##  <a id="Setup"></a>Установка проекта
Фреймворк очень полагается на аддитивные сцены в Unity3D. Что бы использовать его так как было задумано вам нужно сделать немного приготовлений.

1. Откройте сцену sceneKernel. Вы можете найти ее в Assets/[2]Content/Scenes
там вы найдете один transform в view названном [KERNEL] - это корневой объект всей вашей игры. Для удобства, я прикрепил к ней камеру ( но, если вам это не надо, вы можете так не делать ) 

2. Взгляните на скрипт StarterKernel, прикрепленный к [KERNEL]. Стратерами я называю скрипты, которые инициализируют вашу игру, библиотеки и сцены. 

В StarterKernel есть несколько переменных, которые вы можете настроить: 
* ScriptableObject Blueprints: Blueprint это контейнер для всех blueprint'ов в вашей игре. Вlueprint'ами я называю схемы, построенные на  ScriptableObject. 
* ScriptableObject DataGameSettings: Сюда можно добавить некторые настройки для вашей игры.
* ScriptableObject DataGameSessiom : Доска внутреигровой сессии. Изменяйте так, как вам нужно. 
* Pluggable: лист всех используемых доп. модулей. Pluggable это оболочка для различных внешних ассетов, которые вы хотите подключить к игре и фреймворку и инцициализировать при запуске. По умолчанию у вас стоит модуль называемый PlugableConsole, который добавляет в проект внутреигровую консоль отладки.

[![https://gyazo.com/1e79f2d6bf54c3762f35eab153cb0bfe](https://i.gyazo.com/1e79f2d6bf54c3762f35eab153cb0bfe.png)](https://gyazo.com/1e79f2d6bf54c3762f35eab153cb0bfe)

## <a id="Scenes Setup"></a>Создание сцены

Добавляйте новые сцены в игру с помощью File->New Scene command. Вам сообщат, что сцена будет сгенерирована отлично от нормальной сцены в unity3d. Сцена не будет иметь камеры и освещения. Все потому что вы добавляете их аддитивно из других сцен. ( по умолчанию из sceneKernel )

### Обзор сцены 
Все сцены содержат важные объекты, которые вам не нужно изменять.
* [Setup]: это корневой объект, для starter скриптов и любых обектов, связанных с настройками.
* [SCENE]: Это корневой объект для всех относящихся к игре объектов на сцене. Помещайте ваши уровни сюда.
* [SCENE]/Dynamic: это объект, содержащий в себе все игровый объекты, создаваемые в runtime режиме. Важно разделять создаваемые объекты от статических для облегчения процесса отладки.

### Starters
Starter это компонент monobehavior, который вы подключаете к объекту [Setup]. Starter управляет загрузкой и установкой сцены. Вы можете унаследоваться от компонента Starter что бы расширить его и добавить в него собственную логику. Например "startLevel1"

Переменные в starter:
* Factories: под factory, я подразумеваю ScriptableОbject, который используется для создания сложных объектов. (Например factory player - создает объект игрока + создает и устанавливает UI игрока: HP, атаки и т.д.)
* Scenes to keep: сюда добавьте названия сцен, которые вы хотите сохранить после закрытия сцены. Обычно вам нужно всегда хотеть сохранить sceneKernel.
* Scenes depend on: сцены, которые вы хотите добавить при старте этой сцены. Обычно вам всегда нужно добавлять sceneKernel.


[![https://gyazo.com/1285f0b0feb8ecb0495ca536aae25606](https://i.gyazo.com/1285f0b0feb8ecb0495ca536aae25606.png)](https://gyazo.com/1285f0b0feb8ecb0495ca536aae25606)


## <a id="Actors overview"></a>Обзор акторов
С этого момента мы готовы создавать новые gameobject'ы. 

### Monocached
Вместо использования Monobehavior'ов рекомендуется использовать классы Monocached.
Вы можете настроить время уничтожения и типы пулов для вашего объекта. Если пул назначен объект будет деактивирован и кэширован. Если пул не назначен, то объект будет уничтожен. Объединение в пулы это отличная концепция для объектов, которые постоянно создаются. Например для пуль.

### Акторы (Actors)
Базовый класс для всех ваших акторов в игре. Наследуется от Monocached. Это контейнер для ваших компонентов данных и поведения (data&behaviors). Когда вам нужно добавить новую игровую сущность наследуйте ее от класса Actor. Например: ActorPlayer : Actor. 
Акторы это единственные компоненты влияющие на игровую логику объектов.

#### Actor Setup example

```csharp
// inherit from Actor. Inherit from ITick interface to mark that this object must be updated.
  public class ActorPlayer : Actor, ITick
{
    // add serializable data classes to ActorPlayer so we can inspect them in the Inspector
    // Use [FoldoutGroup("Setup")] to make nice foldable groups of variables in the inspector
	[FoldoutGroup("Setup")] public DataMove dataMove;
	
        // Use protected override void Setup to initialize Actor.
        // Setup is used to add data into Actor's container and create behavior scripts for an Actor.
    	protected override void Setup()
		{
            // use Add(object) to add already created object into Actor's container. For example data.
			Add(dataMove);
            // use Add<T>() to create new object and add into Actor's container. For example behavior.
			Add<BehaviorInput>();
		}
 
}
```
[![https://gyazo.com/f3960af1f36566e3d114d97af9e3f0b6](https://i.gyazo.com/f3960af1f36566e3d114d97af9e3f0b6.png)](https://gyazo.com/f3960af1f36566e3d114d97af9e3f0b6)

Вам не нужно писать никакой логики внутри классов актора. Вместо этого используйте классы поведения (behavior). Обычно класс вашего актора должен быть похож на этот:
```csharp
	public class ActorHero : Actor, ITickFixed, ITick, ITickLate
	{
		[FoldoutGroup("Setup"), SerializeField]
		private DataBlueprint dataBlueprint;
		[FoldoutGroup("Setup"), HideInInspector, SerializeField]
		private DataAnimationState dataAnimationState;
		[FoldoutGroup("Setup"), SerializeField]
		private DataInput dataInput;
		[FoldoutGroup("Setup"), SerializeField]
		private DataHealth dataHealth;
		[FoldoutGroup("Setup"), SerializeField]
		private DataDepthRender dataDepthRender;



		protected override void Setup()
		{
			Add(dataBlueprint);
			Add(dataAnimationState);
			Add(dataDepthRender);
			Add(dataHealth);
			Add(dataInput);
		
			Add<DecorateDamageReturn>();
			Add<DecorateBloodFloor>();
			Add<DecorateDamageBlink>();
			Add<DecorateBloodSplats>();

			Add<BehaviorTurn>();
			Add<BehaviorMove>();
			Add<BehaviorRoll>();
			
			Add<BehaviorPlayerInput>();
			Add<BehaviorDamageble>();

		}
	}
```

### Метод Get
Когда вы работете с акторами/поведением используйте Get<T> вместо GetComponent<T>. Метод Get<T> попытается найти компонент внутри актора.
Пример:
```csharp
Get<DataMovement>().facing
```
Вы можете получать компоненты unity так же легко, просто добавив путь к дочернему transform
```csharp
labelScore = Get<TextMeshProUGUI>("anchor_left/label_score");
```
	 

## <a id="Data component"></a>Компонент данных
Компоненты данных это обычные, сериализуемые c# классы, наследованные от интерфейса IData. Все ваши игровые переменные хранятся в компонентах данных.
Одни и те же компоненты данных могут совместно использоваться различными поведениям.


### Data Setup example

```csharp
// Always put [System.Serializable] to all data components and be sure to inherit from IData
	[System.Serializable]
    // Inhe
	public class DataMove : IData
	{
		public float x;
		public float y;

		public void Dispose()
		{
		}
	}
```

#### Интерфейс ISetup
Иногда необходима настройка компонента данных после запуска приложения. Наследуйте компонент данных от интерфейса ISetup, чтобы предоставить дополнительную настройку. Когда они будут добавлены к актору он проверит все компоненты на наличие интерфейса ISetup и запустит их.
```csharp

	[System.Serializable]
	public class DataMove : IData, ISetup
	{
		public float x;
		public float y;

		public void Dispose()
		{
		}

		public void Setup(Actor actor)
		{
			x = actor.selfTransform.position.x;
		}
	}
 ```
 
## <a id="Behavior component"></a>Компонент поведения (Behavior)
Поведения это обычные классы c#, живущие только внутри акторов, которым нужны компоненты данных для работы. Компоненты поведения это рабочие лошадки акторов, ведь именно они определяют как акторы будет себя вести на сцене.

### Пример компонента поведения

```csharp

// Inherit from ITick to mark this behavior for updates
		public class BehaviorInput : Behavior, ITick
	{
        // use [Bind] attribute for lazy initialization from Actor
		[Bind] private DataMove dataMove;

        // Update analogue, populating dataMove variables.
		public override void OnTick()
		{
			dataMove.x = Input.GetAxis("Horizontal");
			dataMove.y = Input.GetAxis("Vertical");
		}
	}
 ```

## <a id="Signals"></a>Сигналы
Сигналы это система подписки/получаения сообщений, которая эффективно заменяет SendMessage Unity3d.
Существует 2 урованя диспетчера сигналов : локальный реализован внутри класса Actor. Глобальный можно использовать из ProcessingSignals.Default.

Как использовать сигналы:

1. Создайте новую структуру. Я предпочитаю называть их Signal*YourName*. Структура содержит все аргументы, которые вы хотите передать.
```csharp
public struct SignalCameraShake 
{
	public int strength;
}
```
2. Добавьте IReceive<T> к объекту, который будет принимать ваш сигнал. T это тип вашего сигнала.
Метод HandleSignal(T arg) будет добавлен в ваш скрипт. Это входная точка для вашего сигнала.
 
```csharp
  public class ProcessingShakeCamera : IDisposable, IMustBeWipedOut, IReceive<SignalCameraShake> 
  {
	public void HandleSignal(SignalCameraShake arg)
		{
			if (arg.strength == 0)
				tweenShakeAverage.Restart();
			else if (arg.strength == 1)
				tweenShakeStrong.Restart();
			else if (arg.strength == 2)
				tweenShakeVeryStrong.Restart();
		}
  }
```

3. Добавьте подписку к вашему диспетчеру сигналов.
```csharp
  public class ProcessingShakeCamera : IDisposable, IMustBeWipedOut, IReceive<SignalCameraShake> 
  {
	
	public ProcessingShakeCamera()
		{
		        // subscribe this object on global signal dispatcher.
			ProcessingSignals.Default.Add(this);
	        }
		
	public void HandleSignal(SignalCameraShake arg)
		{
			if (arg.strength == 0)
				tweenShakeAverage.Restart();
			else if (arg.strength == 1)
				tweenShakeStrong.Restart();
			else if (arg.strength == 2)
				tweenShakeVeryStrong.Restart();
		}
  }
```
 4. Добавьте функционал отписки
```csharp
  public class ProcessingShakeCamera : IDisposable, IMustBeWipedOut, IReceive<SignalCameraShake> 
  {
	
	public ProcessingShakeCamera()
		{
		        // subscribe this object on global signal dispatcher.
			ProcessingSignals.Default.Add(this);
	        }
		
	public void HandleSignal(SignalCameraShake arg)
		{
			if (arg.strength == 0)
				tweenShakeAverage.Restart();
			else if (arg.strength == 1)
				tweenShakeStrong.Restart();
			else if (arg.strength == 2)
				tweenShakeVeryStrong.Restart();
		}
		
		// We don't want object to recieve signals when it's destroyed.
			public void Dispose()
		{
		        // Unsubscribe 
			ProcessingSignals.Default.Remove(this);
		}
		
  }
 ``` 
  Обратите внимание, что вам не нужно реализовывать логику подписки/отписки, которая наследуется от класса behavior или actor.
  Просто добавьте интерфейс IReceive :
  ```csharp
  	public class DecorateDamageBlink : Behavior, IReceive<SignalDamage>
	{
	 
		public void HandleSignal(SignalDamage val)
		{
			Blink();
		}
	
	}
  
 ``` 


## <a id="Interfaces overivew"></a>**Обзор интерфейсов**
Фреймворк содержит в себе несколько интерфейсов для расширения функциональности сущностей.

### ITick
The framework use single monobehavior update for *ALL* entities. Because of that we don't use any Update methods in actors/behaviors. 
Instead we mark actors and behaviors with interfaces to define update type. Use ITick if you have code that needs to run per frame.
Фреймворк использует одно обновление monobehaviour для *ВСЕХ* сущностей. Поэтому мы не используем никаких методов обновления в акторах/поведениях. 
Вместо этого мы помечаем акторов и поведения интерфейсами для определения типа обновления. Используйте Tick, если у вас есть код, который должен выполняться каждый кадр.

```csharp
public class BehaviorExample : Behavior, ITick{
    public override void OnTick() { }
}
```
### ITickFixed
Используйте его, когда у вас есть код, который должен работать через определенное количество кадров.

```csharp
public class BehaviorExample : Behavior, ITickFixed{
    public override void OnTickFixed() { }
}
```
### ITickLate
Используйте его, когда у вас есть код, который должен работать после всех других обновлений.

```csharp
public class BehaviorExample : Behavior, ITickLate{
    public override void OnTickLate() { }
}
``` 

 ### IData
Используйте его, когда вы хотите отметить класс как контейнер данных. Помните, что вам нужно использовать атрибут [System.Serializable] для контейнеров данных.

```csharp
[System.Serializable]
public class DataExample : IData{
    public override void Dispose() { }
}
``` 

 ### ISetup
Иногда необходима настройка компонента данных после запуска приложения. Наследуйте компонент данных от интерфейса ISetup, чтобы предоставить дополнительную настройку. Когда они будут добавлены к актору он проверит все компоненты на наличие интерфейса ISetup и запустит их.
```csharp
[System.Serializable]    
public class DataRender: ISetup, IData
{
	public MaterialPropertyBlock matPropBlock;
	public int ID = 0;
 
	public void Setup(Actor actor)
	{
			var rend = actor.Get<SpriteRenderer>("view");
			matPropBlock = new MaterialPropertyBlock();
			rend.GetPropertyBlock(matPropBlock);
	}

	public void Dispose()
	{
		source = null;
	}
}
```

### IMustBeWipedOut
Интерфейс IMustBeWipedOut отмечает обработчики, которые должны быть удалены из Тулбокса при изменении сцены.
```csharp
  public class ProcessingShakeCamera : IDisposable, IMustBeWipedOut 
  {
  }
```

### IReceive<T>
Интерфейс IReceive<T> используется, когда вы хотите, чтобы объект получал сигнал с типом T от локального диспетчера сигналов. IReceive<T> обычно используется внутри акторов для местной связи.
```csharp
  public class ProcessingShakeCamera : IDisposable, IMustBeWiped, IReceive<SignalCameraShake> 
  {
	public void HandleSignal(SignalCameraShake arg)
		{
			if (arg.strength == 0)
				tweenShakeAverage.Restart();
			else if (arg.strength == 1)
				tweenShakeStrong.Restart();
			else if (arg.strength == 2)
				tweenShakeVeryStrong.Restart();
		}
  }
```

### IReceiveGlobal<T>
Интерфейс IReceiveGlobal<T> используется, когда вы хотите, чтобы объект получал сигнал с типом T от глобального диспетчера сигналов.
```csharp
  public class ProcessingShakeCamera : IDisposable, IMustBeWiped, IReceiveGlobal<SignalCameraShake> 
  {
	public void HandleSignal(SignalCameraShake arg)
		{
			if (arg.strength == 0)
				tweenShakeAverage.Restart();
			else if (arg.strength == 1)
				tweenShakeStrong.Restart();
			else if (arg.strength == 2)
				tweenShakeVeryStrong.Restart();
		}
  }
```
## <a id="Toolbox"></a>Toolbox
The toolbox is a singleton that contains all processings, global data and everything you want to get from global access.
Think of toolbox as a "global actor." 

To add a new instance of a class to a toolbox use Add method
Example:

```csharp
Toolbox.Add<ProcessingInputConnect>();
```

To get something from a toolbox use Get method
Example:

```csharp
data = Toolbox.Get<DataGameSession>();
```

## <a id="Processings"></a>Processings
Processing more known as "managers," "controllers." Processings are classes that can be used like systems in ECS or to do some global work. For example, camera follow script is a good candidate for processing script.

There are few predefined processings in the framework. You can find them in StarterKernel script. The best place to add your custom processings is Starter scripts or pluggables.

Processings must live only inside of a toolbox.

### ProcessingBase
Typically, processing should be inherited from ProcessingBase, but it's ok to use them without a base.
Processing base is required to use the script as an ECS system. Also, it automates routine of subscribing/unsubscribing for signal events.

```csharp
public class ProcessingGroupEnemies : ProcessingBase
```

### IMustBeWiped
The IMustBeWiped interface says to the toolbox that this processing must be destroyed when the scene changed. Usually, you would use it with all "local" processing scripts that are related to one scene only. Sometimes it's better to kill object and recreate it in the new scene.

```csharp
public class ProcessingGroupEnemies : ProcessingBase, IMustBeWiped
```
### IDisposable
Use IDisposable interface when you want to clean processing object before destroying it. 
```csharp
public class ProcessingShakeCamera : IDisposable, IMustBeWiped
{

	        private Tween twShakeFromShootCamera;
		private Tween twShakeAverage;
		private Tween twShakeStrong;
		private Tween twShakeVeryStrong;


		public ProcessingShakeCamera()
		{
			ProcessingSignals.Default.Add(this);
                }

	        public void Dispose()
		{
			ProcessingSignals.Default.Remove(this);
			twShakeFromShootCamera.Kill();
			twShakeAverage.Kill();
			twShakeStrong.Kill();
		}
}
``` 
Don't use IDisposable when inheriting from ProcessingBase. It's already included there, and you get virtual method
OnDispose to make all necessary cleaning.


### Updating processings
Don't forget to use ITick, ITickFixed, and ITickLate interfaces with processings you want to be updated per frame.
Use ProcessingUpdate.Default.Add to register this object as Tickable.
```csharp
ProcessingUpdate.Default.Add(this);
``` 

```csharp
// don't forget to mark type of update. Here we use ITickLate
public class ProcessingCameraFollow : ProcessingBase, ITickLate, IMustBeWiped{
	public ProcessingCameraFollow()
		{
			transformCamera = Camera.main.transform;
			// use ProcessingUpdate.Default.Add to register this object as Tickable. 
			// In our example it will be added as TickLate
			ProcessingUpdate.Default.Add(this);
		}
}
```

## Framework Processings
The framework wouldn't work without some predefined processings, and you should know about them as you will use them a lot.

### ProcessingUpdate
Important processing that controls *all* updates in game. When ProcessingUpdate is created it adds a ComponentUpdate monobehavior inside of [KERNEL] root object to get Unity Update methods. All actors,timers,processings should work from ProcessingUpdate.

While Monobehavior update method can be used only with inherited mono components, you can use ProcessingUpdate with *ANY* script.
You can do that in 2 steps:
1. Inherit from interaces you need.
* ITick for Update
* ITickFixed for FixedUpdate
* ITickLate for LateUpdate
2. Call  *ProcessingUpdate.Default.Add(this);*  in script, somewhere in initializing.
3. Normally, all updates are killed when scene changes but if you want to kill Update earlier call *ProcessingUpdate.Default.Remove(this);*


```csharp
public class MyCustomClass : ITick{

 public MyCustomClass(){
   ProcessingUpdate.Default.Add(this);
 }
 
 public void Tick(){
			 
 }

}
```

### <a id="ProcessingSceneLoad"></a>ProcessingSceneLoad
First, add all scenes you want inside of Build Settings window.
Than, generate scene names:

[![https://gyazo.com/8507135cf74cf0dd1c4b9db90363a6ad](https://i.gyazo.com/8507135cf74cf0dd1c4b9db90363a6ad.gif)](https://gyazo.com/8507135cf74cf0dd1c4b9db90363a6ad)

Now to change scene use ProcessingSceneLoad.To(int level) .


```csharp
// example of level with ID 2
int level = 2;
ProcessingSceneLoad.To(level)
```



## <a id="Object pooling"></a>Object pooling
Every time you create/destroy object memory is allocated. The more complex object is the bigger allocation will be. It's not a big deal to create the object once or several times, but when you need to spawn hundreds of objects, or you want to generate them rapidly, you want to use object pooling. You can find more info about pooling on [Unity3d site](https://unity3d.com/learn/tutorials/topics/scripting/object-pooling).

There are two types of pools in the Framework :
* For gameobjects - any game related game objects on a scene with monobehavior classes.
* For c# objects - any plain c# classes. For example timers.

### GameObject pool
The control of gameobject pool goes through the ProcessingGoPool script and PoolStash script.
Pools are predefined and included inside monocached objects. 
There are 4 types of gameobject pools:

* Pool.UI - for any UI related entities
* Pool.Projectiles - for any fx, small objects such as bullets.
* Pool.Entities - for gameobjects and actors
* Pool.Audio - for audiosource gameobjects

You can add more pool types through ProcessingGoPool if you want.

#### Making object poolable

Step one.
Choose your actor or monocached object in the inspector. Open Mono foldout group. Set pool time you want.

[![https://gyazo.com/1fc7c2a2e77a9aac8544a66712c11f01](https://i.gyazo.com/1fc7c2a2e77a9aac8544a66712c11f01.gif)](https://gyazo.com/1fc7c2a2e77a9aac8544a66712c11f01)

Thats all. For now on when you will try to destroy object it will be deactivated and send to desired pool instead.
In your scene you may notice [POOLS] object. This object holds all deactivated entities for you.

[![https://gyazo.com/246ca65b49467779f106e4482be4b4e1](https://i.gyazo.com/246ca65b49467779f106e4482be4b4e1.gif)](https://gyazo.com/246ca65b49467779f106e4482be4b4e1)

Step two. ( Optional )
In case you want to provide some particular logic when object spawned/despawned inherit your class from IPoolable interface.
It's needed most when you want to reset object states. You don't want your enemy to spawn with zero HP for example.

```csharp
public class ActorEnemy : Actor, ITick, IPoolable
```

Then add override methods to behaviors that are used with this Actor:

```csharp
protected override void OnSpawn(){}
```
```csharp
protected override void OnDespawn(){
}
```

If you override from Actor class don't forget to add base.OnSpawn()/OnDespawn()
You need to do that because of base Actor class loops through behaviors and pass OnSpawn/OnDespawn methods to them.
```csharp
protected override void OnSpawn(){
base.OnSpawn();
}
```
```csharp
protected override void OnDespawn(){
base.OnDespawn();
}
```
In this way, if you plan and design carefully, you can do pooling for even very complex objects.

### Pool Register component

If you added objects to the scene via edit mode and you want them to be part of the pooling routine add PoolRegister component.
I usually attach it to [Setup] object. That define pool nodes: set pool type, prefab of the object from Project ( not from the scene )
And a list of all prefab clones on the scene. In future, I plan to automate this routine.

[![https://gyazo.com/51f7661dc970da82aa48faac0feffdfc](https://i.gyazo.com/51f7661dc970da82aa48faac0feffdfc.png)](https://gyazo.com/51f7661dc970da82aa48faac0feffdfc)

### Temporary pool
You can make a special temporary pool container to work with later. Useful when you need to deactivate/activate specific group of entities in a lazy way.

```csharp
ProcessingGoPool.AddToTemp(Pool.Entities, gameobject);
```

```csharp
ProcessingGoPool.ReleaseTemp(Pool.Entities);
```

## <a id="Creating and destroying new objects"></a>Creating and destroying new objects

### Creating routine

To get all benefits of the pooling system and to be sure objects will be spawned in the right places I wrote some instantiate shortcuts.
In ANY script you want just use 
```csharp
var pool = Pool.Entities;
var obj = this.Populate(pool, prefab);
```
You can provide extra params as well:
```csharp
var pool = Pool.UI;
var obj = this.Populate(pool, prefab, Vector3.zero, Quaternion.identity, null, WorldParenters.Level);
```
You can spawn from Resources by providing string id name.

```csharp
var pool = Pool.UI;
var obj = this.Populate(pool, "myObject" , Vector3.zero, Quaternion.identity, null, WorldParenters.UI);
```
Parameteres that are used :
Pool - the type of pool. Use Pool.None if you don't want to spawn from pool.
Prefab or string id - the object you want to spawn. 
Position - the initial position. Vector3.Zero by default.
Rotation - the initial rotation. Quaternion.identity by default.
Parent - parent transform. Use it if you want to set special parent.
WorldParenters - WorldParenters.Level by default.  Created object is put inside of particular world container if no other parent is registered. 

By default it's dynamic object inside [SCENE] object.
[![https://gyazo.com/4d3346f3fe63bb626af6ab6884271146](https://i.gyazo.com/4d3346f3fe63bb626af6ab6884271146.png)](https://gyazo.com/4d3346f3fe63bb626af6ab6884271146) 

When you spawn from string ID ProcessingResources starts to work. It looks inside of Resources/Prefabs folder and tries to find the desired object. Than ProcessingResources caches it and provide it to the spawn logic. Next time it will give this object from the cache instead of looking again inside of ResourcesFolder.

[![https://gyazo.com/025bca594dca7fdd3e35465bff05cb10](https://i.gyazo.com/025bca594dca7fdd3e35465bff05cb10.png)](https://gyazo.com/025bca594dca7fdd3e35465bff05cb10)

### Destroying routine

To destroy an actor or monocached object use HandleDestroyGO() method.
```csharp
actor.HandleDestroyGO()
```
If the object belongs to pool it will be deactivated for further reuse. If the object doesn't belong to a pool, it will be destroyed.
You can delay destroy by adding destroy delay time in the Inspector.

[![https://gyazo.com/49613234129a49a1a9a923a863225618](https://i.gyazo.com/49613234129a49a1a9a923a863225618.gif)](https://gyazo.com/49613234129a49a1a9a923a863225618)

### OnBeforeDestroy

Use override of OnBeforeDestroy() method to provide some logic for and Actor before destroying. Useful for adding some effects related stuff. Also OnBeforeDestroy works well to reset data if object is pooled.

```csharp
protected override void OnBeforeDestroy()
```	

## Timers

Timers are great for making delayed actions. I strongly recommend to use them instead of coroutines for example if you need to make single delay or call several delayed actions.

There are two ways of using timers. You can create a new timer each time you need from timer pool or cache timer and reuse it.
Example of timer:
```csharp
Timer.Add(0.1f, actor.HandleDestroyGO); 
```

Set timer time and action. You can use lambda to define a complex action. The timer will be automatically recycled after playing.
```csharp
 Timer.Add(0.1f, ()=>
    {
      Debug.Log("Killed");
      actor.HandleDestroyGO();
    }); 
```

### AddID method

You may add ID to the timer. After this you will be able to sort timers and get the list of timers with the same ID. You can use any object for your ID.

```csharp
  Timer.Add(0.1f, actor.HandleDestroyGO).AddID(actor);
```

For example you may want find all timers of freezed actor and changed timescale.
```csharp
var timers = Timer.FindAllTimers(actor);
	 
	if (timers != null)
	 for (var i = 0; i < timers.Count; i++)
	    {
		timers[i].timeScale = 0.5f;
	    }
```
### Restart method

You can cache timer and reuse it in future. 
```csharp
 private Timer timerBlink;
 timerBlink = new Timer(BlinkFinish, 0.15f);
 
 void Blink(){
 timerBlink.Restart();
 }
```
You can change the time or even action while restarting

```csharp
 private Timer timerBlink;
 timerBlink = new Timer(BlinkFinish, 0.15f);
 
 void Blink(){
 timerBlink.Restart(10f);
 timerBlink.Restart(10f, AnotherAction );
 }
 void AnotherAction(){
 }
```

### Kill method
If you need to destroy a timer use kill method.
```csharp
timerBlink.Kill();
 ```
 Normally, you want to do it inside OnDispose method.
 ```csharp
 protected override void OnDispose()
  {
    timerBlink.Kill();
  }
 ```
 OnDispose method provided inside of behaviors by default.

## <a id="Blueprints"></a>Blueprints
Blueprints are scriptable objects that are used for defining common data for similar actors. 
Their Setup is similar to actors Setup.

### How to create a blueprint
Step 1.
Create a new script and inherit it from Blueprint. 
Step 2.
Add [CreateAssetMenu] tag with fileName and menuName.
Step 3.
Define data components you want and add them via Setup method to the blueprint container.

 ```csharp
	[CreateAssetMenu(fileName = "BlueprintCreature", menuName = "Blueprints/BlueprintCreature")]
	public class BlueprintCreature : Blueprint
	{
		[FoldoutGroup("Setup")]
		public DataCreature dataCreature;
		
		[FoldoutGroup("Setup")]
		public DataDeathAnimations dataDeathAnimations;


		public override void Setup()
		{
			Add(dataCreature);
			Add(dataDeathAnimations);
		}
	}
 ```
Step 4. Create a new blueprint object in Project.

[![https://gyazo.com/13c79e46e32bd94b19b1db89dac43306](https://i.gyazo.com/13c79e46e32bd94b19b1db89dac43306.gif)](https://gyazo.com/13c79e46e32bd94b19b1db89dac43306)

Step 5. Create a blueprint data wrapper for all actors ( you need to do that only once ) and add this data to all actors you need.

```csharp
	[Serializable]
	public class DataBlueprint : IData
	{
		public Blueprint blueprint;
 
		public void Dispose()
		{
 
			blueprint = null;
		}

		public T Get<T>() where T : class
		{
			return blueprint.Get<T>();
		}
	  
	}
  ```
 Step 6. Assign from the Inspector view a desired blueprint to the actor.
  
  [![https://gyazo.com/c6e52a666f9d2b0a6a3c005bcef9d18d](https://i.gyazo.com/c6e52a666f9d2b0a6a3c005bcef9d18d.gif)](https://gyazo.com/c6e52a666f9d2b0a6a3c005bcef9d18d)
  
 Step 7. Find Blueprints scriptable object inside of Resources folder and populate it with your new blueprint object.
You can automate this process by clicking "populate blueprints" in tools menu. In this case your blueprints should be in
Assets/[2]Content/Blueprints folder

[![https://gyazo.com/7472fbc529e2e2b9f58b8f35b09a7c18](https://i.gyazo.com/7472fbc529e2e2b9f58b8f35b09a7c18.gif)](https://gyazo.com/7472fbc529e2e2b9f58b8f35b09a7c18)

[![https://gyazo.com/35b1b3c0426abe14278afe0fa107c2b8](https://i.gyazo.com/35b1b3c0426abe14278afe0fa107c2b8.gif)](https://gyazo.com/35b1b3c0426abe14278afe0fa107c2b8)

### How to use blueprints in code ?
It's easy and straightforward : use get method inside your behaviors.

```csharp
// Get<DataBlueprint>() returns the blueprint wrapper.
// Get<DataWeapon> returns desired data from the blueprint.
var weaponData = Get<DataBlueprint>().Get<DataWeapon>();
```
  
### When to use blueprints ? 
All variables you add to your game objects cost something. For example, creating 1 000 000 objects with one int variable will
require about 4MB of memory. Scriptable objects are created only once and shared among your actor copies. For example, you want to add an audio sound variable to your monster object. Instead, you can use monster blueprint and define the audio variable there. In this case, no matter how much copies of monsters you have on the scene their audio variable will be created only once.
 
## <a id="Tags"></a>Tags
Tags are the glue for your game: You can identify your actors with tags or use them as arguments for your signals to check game logic. Tags are simple cont INT variables.

### How to add
Step 1. 
Create a new static script called Tag or what do you prefer. 
I prefer to use partial classes to divide my tags to different files.
Populate your tags with unique int ID. 
```csharp
public static partial class Tag
	{
		 public const int SignalStasisOn = 10001;
		 public const int SignalStasisOff = 10002;
        }
```	

```csharp
public static partial class Tag
	{
		 public const int WeaponGun = 9000;
		 public const int WeaponLaser = 9001;
        }
```	

[![https://gyazo.com/0eb286e0d8b2712b9b3aee03eaaec9c9](https://i.gyazo.com/0eb286e0d8b2712b9b3aee03eaaec9c9.png)](https://gyazo.com/0eb286e0d8b2712b9b3aee03eaaec9c9)

Step 2.
Add [TagField(categoryName = "YOURNAME")] before your const int. Use '/' to add tag in child group.
```csharp
	public static partial class Tag
	{
		[TagField(categoryName = "Weapons")] public const int WeaponGun = 9000;
		[TagField(categoryName = "Weapons/BigGuns")] public const int WeaponLaser = 9001;
	}
```
Step 3.
Add your tag to Actor. To do that use tags.Add(YOUR_TAG);
```csharp
public class ActorPlayer : Actor{
	protected override void Setup()
		{
			Add(dataAnimationState);
			Add(dataCurrentWeapon);
			// always add tags at the end of your Actor Setup.
			tags.Add(Tag.GroupPlayer);
		}
}
```
Step 4.
You can edit your tags in the Inspector view. To do that add int variable where you want and attach attribute
[TagFilter(typeof(TYPE_OF_CLASS_WHERE_TAGS))]

```csharp
public class ActorPlayer : Actor{
[TagFilter(typeof(Tag))] public int tag;
	protected override void Setup()
		{
			Add(dataAnimationState);
			Add(dataCurrentWeapon);
			// always add tags at the end of your Actor Setup.
			tags.Add(tag);
		}
}
```
[![https://gyazo.com/e3c0c4d009209b46df72975305a6e936](https://i.gyazo.com/e3c0c4d009209b46df72975305a6e936.gif)](https://gyazo.com/e3c0c4d009209b46df72975305a6e936) 
 
 ### ProcessingTags
 Actors have special processingTags component. 
 
 ```csharp
 // add one tag.
 tags.Add(tag);
```
 ```csharp
 // add as many tags as you want.
 tags.Add(tag, tag2, tag3);
```
 ```csharp
  // remove one tag.
 tags.Remove(tag);
```
 ```csharp
   // remove all similar tags.
 tags.RemovAll(tag);
```
 ```csharp
   // all tags must be included.
 bool valid = tags.ContainAll(tag,tag2);
```
 ```csharp
   // at least one tag must be included.
 bool valid = tags.ContainAny(tag,tag2);
```
```csharp
   // tag must be included.
 bool valid = tags.Contain(tag);
```
### How to use
You can add similar tags to the actor. It's useful in case when you have several actions with the same logic, and you want to validate something. 

 ```csharp
 // Add stun marker from the mighty hammer of doom.
 tags.Add(Tag.Stunned);
 // Add stun marker from falling off the tree.
 tags.Add(Tag.Stunned);
// remove effect caused by the mighty hammer of doom. 
 tags.Remove(Tag.Stunned);
 bool condition_stunned = tags.Contain(Tag.Stunned);  
```
In the example above condition_stunned will be true because we have added the same tag twice but deleted it only once.

## <a id="ECS"></a>ECS
Simple ECS pattern for working with actors. My approach can be used only with actor classes at the current moment and is far less
powerful than clean ECS approaches and it's used more for structuring than gaining performance boost.

### Processings ( aka systems )
I call all systems or global "controllers" as Processings.

When you need to activate ECS system inherit your processing from ProcessingBase

```csharp
public class ProcessingCameraFollow : ProcessingBase, ITick, IMustBeWiped
```
To use Processing you need to add it to the Toolbox. I usually add them via Starter scripts.

```csharp
	public class StarterLv1 : Starter
	{
		[FoldoutGroup("Setup"), SerializeField, HideInInspector]
		private DataLevel dataLevel;

		protected override void Setup()
		{
			Toolbox.Get<DataGameSession>().currentLevel = 1;

			Toolbox.Add<ProcessingGroupPlayers>();
			Toolbox.Add<ProcessingGroupEnemies>();

			Toolbox.Add<ProcessingSortDepth>();
			Toolbox.Add<ProcessingShakeCamera>();
			Toolbox.Add<ProcessingCameraFollow>();

			Toolbox.Add<ProcessingInputConnect>();
			Toolbox.Add<ProcessingMenuHome>();

		}
	}
```
Remember, you can inherit from Starter if needed.

### Processing groups
When a new Actor entity is added ProcessingEntities script decide in what groups of actors it should be placed.
The group is a list of actors that share a common filter.


```csharp
public class ProcessingCameraFollow : ProcessingBase, ITick, IMustBeWiped{

private Group groupPlayers;

}
```

### Filters
To populate your group you need to provide some filters. Think of a filter as a key lock,  if the key matches this lock - than an actor is added to the group. You can filter actors by Data component types or by int tags.

#### GroupBy filer
To populate a group add GroupBy attribute above the group variable.
All your groupby filters must be valid in order to add an actor to a group.
```csharp
public class ProcessingCameraFollow : ProcessingBase, ITick, IMustBeWiped{
[GroupBy(typeof(DataPlayer))]
private Group groupPlayers;
}
```
You can use several filters as well :

```csharp
public class ProcessingCameraFollow : ProcessingBase, ITick, IMustBeWiped{
[GroupBy(typeof(DataPlayer), typeof(DataKnight) )]
private Group groupPlayersKnights;
}
```

##### Using Tags instead of types
You don't have to use types of data components for filtering. Instead, you can use Tag.
A tag is a simple const int variable. It's very similar to GameObject tags in Unity3D but more powerful.
```csharp
// make a static Tag class and define all your const there.
	public static partial class Tag
	{
		[TagField(categoryName = "Groups")] public const int GroupPlayer = 2001;
		[TagField(categoryName = "Groups")] public const int GroupEnemy = 2002;
		[TagField(categoryName = "Groups")] public const int GroupPlayerSpawner = 2003;
		[TagField(categoryName = "Groups")] public const int GroupDragable = 2004;
		[TagField(categoryName = "Groups")] public const int GroupPlayerUI = 2005;
		[TagField(categoryName = "Groups")] public const int GroupBorders = 2006;
		[TagField(categoryName = "Groups")] public const int GroupCameraStartPosition = 2007;
		[TagField(categoryName = "Groups")] public const int GroupCounterAttacked = 2008;
	}
```

```csharp
public class ProcessingCameraFollow : ProcessingBase, ITick, IMustBeWipedOut{
[GroupBy(Tag.GroupPlayer)]
private Group groupPlayers;
}
```
```csharp
public class ActorPlayer : Actor{
	protected override void Setup()
		{
			Add(dataAnimationState);
			Add(dataCurrentWeapon);
			// always add tags at the end of your Actor Setup.
			tags.Add(Tag.GroupPlayer);
		}
}
```
#### GroupExclude filter

You can be more specific by adding a GroupExclude filter. If any of group exclude filter match than an actor can be no longer be in the group.

```csharp
public class ProcessingCameraFollow : ProcessingBase, ITick, IMustBeWipedOut{
[GroupBy(Tag.GroupPlayer)]
[GroupExclude(Tag.StateDead)]
private Group groupPlayersAlive;
}
```


### OnGroupChanged action
You can provide extra logic when group is changed ( a new actor is added or removed from the group )

 ```csharp
public class ProcessingCameraFollow : ProcessingBase, ITick, IMustBeWipedOut{
[GroupBy(Tag.GroupPlayer)]
private Group groupPlayers;

	public ProcessingCameraFollow()
	{
	   groupPlayers.OnGroupChanged += OnGroupPlayersChanged;
	}

	void OnGroupPlayersChanged()
		{	 
			 for(var i=0;i<groupPlayers.length;i++){
			    Debug.Log("Actor: " + groupPlayers.actors[i]);
			 } 
		}
}
```

### Updating your processing component
To update your processing inherit from ITick, ITIckFixed, ITickLate.
Use group.length to get the container length. Use group.actors[i] - to receive one of the group actors.

 ```csharp
public class ProcessingCameraFollow : ProcessingBase, ITick, IMustBeWipedOut{
[GroupBy(Tag.GroupPlayer)]
private Group groupPlayers;

	public ProcessingCameraFollow()
	{
	   groupPlayers.OnGroupChanged += OnGroupPlayersChanged;
	}

	void OnGroupPlayersChanged()
		{	 
			 for(var i=0;i<groupPlayers.length;i++){
			    Debug.Log("Actor: " + groupPlayers.actors[i]);
			 } 
		}
		
	public void Tick()
	{
	      for(var i=0;i<groupPlayers.length;i++){
			    DoSomething(groupPlayers.actors[i]);
	         } 
	 }
	 
	 void DoSomething(Actor a){
	 }
		
}
```
		


	
 
