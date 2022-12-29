# Laravel 服務容器

> **參考資料**：
> [Laravel 學習筆記—— 神奇的服務容器
> ](https://learnku.com/articles/789/laravel-learning-notes-the-magic-of-the-service-container)
>
> 容器，字面上理解就是裝東西的東西。
> 常見的變量、物件屬性等都可以算是容器。
> 一個容器能夠裝什麼，全部取決於你對該容器的定義。
> 當然，有這樣一種容器，它存放的不是文本、數值，而是物件、物件的描述(類別、介面)或者是提供物件的回調，
> 通過這種容器，我們得以實現許多高級的功能，其中最常提到的，就是"解耦"、"依賴注入(DI)"。本文就從這裡開始。

## IoC 容器 laravel 的核心

Laravel 的核心就是一個 IoC 容器，根據官方文件，稱其為"服務容器"，顧名思義，該容器提供了整個框架中需要的一系列服務。作為初學者，很多人會在這一個概念上犯難，因此，我打算從一些基礎的內容開始講解，通過理解面向物件開發中依賴的產生和解決方法，來逐漸揭開"依賴注入"的面紗，逐漸理解這一神奇的設計理念。

## IoC 容器誕生的故事

超人和超能力，依賴的產生！
物件導向程式碼，有以下幾樣東西無時不刻的接觸：介面、類別還有物件。這其中，介面是類別的原型，一個類別必須要遵守其實現的介面；物件則是一個類別實例化後的產物，我們稱其為一個實例。當然這樣說肯定不利於理解，我們就實際的寫點中看不中用的代碼輔助學習。

> 怪物橫行的世界，總歸需要點超級人物來擺平。

我們把一個"超人"作為一個類別，

```php
class Superman {}
```

我們可以想像，一個超人誕生的時候肯定擁有至少一個超能力，這個超能力也可以抽象為一個物件，為這個物件定義一個描述他的類別吧。一個超能力肯定有多種屬性、(操作)方法，這個盡情的想像，但是目前我們先大致定義一個只有屬性的"超能力"，至於能幹啥，我們以後再豐富：

```php
class Power
{
    /**
    * 能力值
    */
    protected $ability;

    /**
     * 能力範圍或距離
     */
    protected $range;

    public function __construct($ability, $range) {
        $this->ability = $ability;
        $this->range = $range;
    }
}
```

這時候我們回過頭，修改一下之前的"超人"類別，讓一個"超人"創建的時候被賦予一個超能力：

```php
class Superman
{
    protected $power;

    public function __construct()
    {
        $this->power = new Power(999, 100);
    }

}
```

這樣的話，當我們創建一個"超人"實例的時候，同時也創建了一個"超能力"的實例，但是，我們看到了一點，"超人"和"超能力"之間不可避免的產生了一個依賴。

> 所謂"依賴"，就是"我若依賴你，少了你就沒有我"。

在一個貫徹物件導向程式碼的項目中，這樣的依賴隨處可見。少量的依賴並不會有太過直觀的影響，我們隨著這個例子逐漸鋪開，讓大家慢慢意識到，當依賴達到一個量級時，是怎樣一番噩夢般的體驗。當然，我也會自然而然的講述如何解決問題。

### 一堆亂麻——可怕的依賴

之前的例子中，超能力類別實例化後是一個具體的超能力，但是我們知道，超人的超能力是多元化的，每種超能力的方法、屬性都有不小的差異，沒法通過一種類別描述完全。我們現在進行修改，我們假設超人可以有以下多種超能力：

- 飛行，屬性有：飛行速度、持續飛行時間
- 蠻力，屬性有：力量值
- 能量彈，屬性有：傷害值、射擊距離、同時射擊個數

我們建立以下類別：

```php
class Flight
{
    protected $speed;
    protected $hold_time;
    public function __construct($speed, $hold_time) {}
}

class Force
{
    protected $force;
    public function __construct($force) {}
}

class Shot
{
    protected $atk;
    protected $range;
    protected $limit;
    public function __construct($atk, $range, $limit) {}
}

```

**這邊沒有詳細寫出 `__construct()` 這個構造函數的全部，只寫了需要傳遞的參數**

好了，這下我們的超人有點"忙"了。在超人初始化的時候，我們會根據需要來實例化其擁有的超能力嗎，大致如下：

```php
class Superman
{
    protected $power;

    public function __construct()
    {
        $this->power = new Fight(9, 100);
        // $this->power = new Force(45);
        // $this->power = new Shot(99, 50, 2);
        /*
        $this->power = array(
            new Force(45),
            new Shot(99, 50, 2)
        );
        */
    }

}
```

我們需要自己手動的在構造函數內(或者其他方法裡)實例化一系列需要的類別，這樣並不好。可以想像，假如需求變更(不同的怪物橫行地球)，需要更多的有針對性的 新的 超能力，或者需要 變更 超能力的方法，我們必須 重新改造 超人。換句話說就是，改變超能力的同時，我還得重新製造個超人。效率太低了！新超人還沒創造完成世界早已被毀滅。

> 這時，靈機一動的人想到：為什麼不可以這樣呢？超人的能力可以被隨時更換，只需要添加或者更新一個芯片或者其他裝置啥的(想到鋼鐵人沒)
> 這樣的話就不要整個重新來過了。

對，就是這樣的。

我們不應該手動在"超人"類別中固化了他的"超能力"初始化的行為，而轉由外部負責，由外部創造超能力模組、裝置或者芯片等(我們後面統一稱為"模組")，植入超人體內的某一個介面，這個介面是一個既定的，只要這個"模組"滿足這個介面的裝置都可以被超人所利用，可以提升、增加超人的某一種能力。這種由外部負責其依賴需求的行為，我們可以稱其為`控制反轉(IoC)`。

### 工廠模式，依賴轉移

當然，實現控制反轉的方法有幾種。在這之前，不如我們先了解一些好玩的東西。

> 我們可以想到，組件、工具(或者超人的模組)，是一種可被生產的玩意兒，生產的地方當然是"工廠(Factory)"，於是有人就提出了這樣一種模式：`工廠模式`。

工廠模式，顧名思義，就是一個類別所以依賴的外部事物的實例，都可以被一個或多個"工廠"創建的這樣一種開發模式，就是工廠模式。

我們為了給超人製造超能力模組，我們創建了一個工廠，它可以製造各種各樣的模組，且僅需要通過一個方法：

```php
class SuperModuleFactory
{
    public function makeModule($moduleName, $options) {
        switch ($moduleName) {
            case 'Fight':
                return new Fight($options[0], $options[1]);
            case 'Force':
                return new Force($options[0]);
            case 'Shot':
                return new Shot($options[0], $options[1], $options[2]);
        }
    }
}
```

這時候，超人創建之初就可以使用這個工廠！

```php
class Superman
{
protected $power;

    public function __construct()
    {
        // 初始化工廠
        $factory = new SuperModuleFactory;

        // 通過工廠提供的方法制造需要的模組
        $this->power = $factory->makeModule('Fight', [9, 100]);
        // $this->power = $factory->makeModule('Force', [45]);
        // $this->power = $factory->makeModule('Shot', [99, 50, 2]);
        /*
        $this->power = array(
            $factory->makeModule('Force', [45]),
            $factory->makeModule('Shot', [99, 50, 2])
        );
        */
    }

}
```

可以看得出，我們不再需要在超人初始化之初，去初始化許多第三方類別，只需初始化一個工廠類別，即可滿足需求。但這樣似乎和以前區別不大，只是沒有那麼多 new 關鍵字。其實我們稍微改造一下這個類別，你就明白，工廠類別的真正意義和價值了。

```php
class Superman
{
    protected $power;

    public function __construct(array $modules)
    {
        // 初始化工廠
        $factory = new SuperModuleFactory;

        // 通過工廠提供的方法制造需要的模組
        foreach ($modules as $moduleName => $moduleOptions) {
            $this->power[] = $factory->makeModule($moduleName, $moduleOptions);
        }
    }

}

// 建立超人
$superman = new Superman([
    'Fight' => [9, 100],
    'Shot' => [99, 50, 2]
]);
```

現在修改的結果令人滿意。現在，"超人"的創建不再依賴任何一個"超能力"的類別，我們如若修改了或者增加了新的超能力，只需要針對修改 `SuperModuleFactory` 即可。擴充超能力的同時不再需要重新編輯超人的類別文件，使得我們變得很輕鬆。但是，這才剛剛開始。

### 再進一步！IoC 容器的重要組成—— 依賴注入

由"超人"對"超能力"的依賴變成"超人"對"超能力模組工廠"的依賴後，對付小怪獸們變得更加得心應手。但這也正如你所看到的，依賴並未解除，只是由原來對多個外部的依賴變成了對一個"工廠"的依賴。假如工廠出了點麻煩，問題變得就很棘手。

> 其實大多數情況下，工廠模式已經足夠了。
> 工廠模式的缺點就是：介面未知(即沒有一個很好的契約模型，關於這個我馬上會有解釋)、產生物件類別型單一。
> 總之就是，還是不夠靈活。
> 雖然如此，工廠模式依舊十分優秀，並且適用於絕大多數情況。
> 不過我們為了講解後面的依賴注入，這裡就先誇大一下工廠模式的缺陷咯。

我們知道，超人依賴的模組，我們要求有統一的介面，這樣才能和超人身上的注入介面對接，最終起到提升超能力的效果。

事實上，我之前說謊了，不僅僅只有一堆小怪獸，還有更多的大怪獸。嘿嘿。額，這時候似乎工廠的生產能力顯得有些不足—— 由於工廠模式下，所有的模組都已經在工廠類別中安排好了，如果有新的、高級的模組加入，我們必須修改工廠類別(好比增加新的生產線)：

```php
class SuperModuleFactory
{
    public function makeModule($moduleName, $options){
        switch ($moduleName) {
            case 'Fight':
                return new Fight($options[0], $options[1]);
            case 'Force':
                return new Force($options[0]);
            case 'Shot':
                return new Shot($options[0], $options[1], $options[2]);
            // case 'more': .......
            // case 'and more': .......
            // case 'and more': .......
            // case 'oh no! its too many!': .......
        }
    }
}
```

噩夢般的感受！

> 其實靈感就差一步！你可能會想到更為靈活的辦法！對，下一步就是我們今天的主要配角—— DI (依賴注入)

由於對超能力模組的需求不斷增大，我們需要集合整個世界的高智商人才，一起解決問題，不應該僅僅只有幾個工廠壟斷負責。不過高智商人才們都非常自負，認為自己的想法是對的，創造出的超能力模組沒有統一的介面，自然而然無法被正常使用。這時我們需要提出一種契約，這樣無論是誰創造出的模組，都符合這樣的介面，自然就可被正常使用。

```php
interface SuperModuleInterface
{
    /*
     * 超能力啟動方法
     *
     * 任何一個超能力都得有該方法，並擁有一個參數
     * @param array $target 針對目標，可以是一個或多個，自己或他人
     */
    public function activate(array $target);
}
```

上文中，我們定下了一個介面(超能力模組的規範、契約)，所有被創造的模組必須遵守該規範，才能被生產。

> 其實，這就是 php 中 介面( interface ) 的用處和意義！
> 很多人覺得，為什麼 php 需要介面這種東西？難道不是 java 、 C# 之類別的語言才有的嗎？
> 這麼說，只要是一個正常的物件導向程式碼語言(雖然 php 可以程序式程式設計)，都應該具備這一特性。
> 因為一個 物件(object) 本身是由他的模板或者原型——類別(class)，經過實例化後產生的一個具體事物，
> 而有時候，實現同一種方法且不同功能(或特性)的時候，會存在很多的類別(class)，
> 這時候就需要有一個契約，讓大家編寫出可以被隨時替換卻不會產生影響的介面。
> 這種由程式碼語言本身提出的硬性規範，會增加更多優秀的特性。
>
> 雖然有些繞，但通過我們接下來的實例，大家會慢慢領會介面帶來的好處。

這時候，那些提出更好的超能力模組的高智商人才，遵循這個介面，創建了下述(模組)類別：

```php
/**
 *
 * X-超能量
 */
class XPower implements SuperModuleInterface
{
    public function activate(array $target) {
        //
    }
}

/**
 *
 * 終極炸彈
 */
class UltraBomb implements SuperModuleInterface
{
    public function activate(array $target)
    {
        //
    }
}
```

同時，為了防止有些"磚家"自作聰明，或者一些叛徒惡意搗蛋，不遵守契約胡亂製造模組，影響超人，我們對超人初始化的方法進行改造：

```php
class Superman
{
    protected $module;

    public function __construct(SuperModuleInterface $module)
    {
        $this->module = $module
    }

}
```

改造完畢！現在，當我們初始化"超人"類別的時候，提供的模組實例必須是一個 `SuperModuleInterface` 介面的實現。否則就會提示錯誤。

正是由於超人的創造變得容易，一個超人也就不需要太多的超能力，我們可以創造多個超人，並分別注入需要的超能力模組即可。這樣的話，雖然一個超人只有一個超能力，但超人更容易變多，我們也不怕怪獸啦！

> 現在有人疑惑了，你要講的 依賴注入 呢？
>
> 其實，上面講的內容，正是依賴注入。

什麼叫做依賴注入？

本文從開頭到現在提到的一系列依賴，只要不是由內部生產(比如初始化、構造函數 \_\_construct 中通過工廠方法、自行手動 new 的)，而是由外部以參數或其他形式註入的，都屬於依賴注入(DI)。是不是豁然開朗？事實上，就是這麼簡單。下面就是一個典型的依賴注入：

```php
// 超能力模组
$superModule = new XPower;

// 初始化一個超人，並注入一個超能力模组依賴
$superMan = new Superman($superModule);

```

關於依賴注入這個本文的主要配角，也就這麼多需要講的。理解了依賴注入，我們就可以繼續深入問題。慢慢走近今天的主角……

### 更為先進的工廠—— IoC 容器

剛剛列了一段代碼：

```php
$superModule = new XPower;

$superMan = new Superman($superModule);
```

讀者應該看出來了，手動的創建了一個超能力模組、手動的創建超人並註入了剛剛創建超能力模組。呵呵，手動。

現代社會，應該是高效率的生產，乾淨的現場，完美的自動化生產。

一群怪獸來了，如此低效率產出超人是不現實，我們需要自動化—— 最多一條指令，千軍萬馬來相見。我們需要一種高級的生產現場，我們只需要向生產現場提交一個腳本，工廠便能夠通過指令自動化生產。這種更為高級的工廠，就是工廠模式的昇華—— IoC 容器。

```php
class Container
{
    protected $binds;

    protected $instances;

    public function bind($abstract, $concrete)
    {
        if ($concrete instanceof Closure) {
            $this->binds[$abstract] = $concrete;
        } else {
            $this->instances[$abstract] = $concrete;
        }
    }

    public function make($abstract, $parameters = [])
    {
        if (isset($this->instances[$abstract])) {
            return $this->instances[$abstract];
        }

        array_unshift($parameters, $this);

        return call_user_func_array($this->binds[$abstract], $parameters);
    }

}
```

這時候，一個十分粗糙的容器就誕生了。現在的確很簡陋，但不妨礙我們進一步提升他。先著眼現在，看看這個容器如何使用吧！

```php
// 创建一個容器(后面称作超级工廠)
$container = new Container;

// 向該 超级工廠 添加 超人 的生產脚本
$container->bind('superman', function($container, $moduleName) {
    return new Superman($container->make($moduleName));
});

// 向該 超级工廠 添加 超能力模组 的生產脚本
$container->bind('xpower', function($container) {
return new XPower;
});

// 同上
$container->bind('ultrabomb', function($container) {
return new UltraBomb;
});

// ****************** 华丽丽的分割线 **********************
// 開始啟動生產
$superman_1 = $container->make('superman', 'xpower');
$superman_2 = $container->make('superman', 'ultrabomb');
$superman_3 = $container->make('superman', 'xpower');
// ...随意添加
```

看到沒？通過最初的 绑定(bind) 操作，我們向超級工廠註冊了一些生產腳本，這些生產腳本在生產指令下達之時便會執行。發現沒有？我們徹底的解除了超人與超能力模組的依賴關係，更重要的是，容器類別也絲毫沒有和他們產生任何依賴！我們通過註冊、綁定的方式向容器中添加一段可以被執行的回調(可以是匿名函數、非匿名函數、類別的方法)作為生產一個類別的實例的腳本，只有在真正的 生產(make) 操作被調用執行時，才會觸發。

這樣一種方式，使得我們更容易在創建一個實例的同時解決其依賴關係，並且更加靈活。當有新的需求，只需另外綁定一個"生產腳本"即可。

> 實際上，真正的 IoC 容器更為高級。
> 我們現在的例子中，還是需要手動提供超人所需要的模組參數，
> 但真正的 IoC 容器會根據類別的依賴需求，自動在註冊、綁定的一堆實例中搜尋符合的依賴需求，並自動注入到構造函數參數中去。
> Laravel 框架的服務容器正是這麼做的。
>
> 這種自動搜尋依賴需求的功能，是通過 `反射(Reflection)` 實現的，恰好的，php 完美的支持反射機制！
>
> [PHP 官方文件 - 反射](https://www.php.net/manual/zh/book.reflection.php)

現在，到目前為止，我們已經不再懼怕怪獸們了。高智商人才集思廣益，井井有條，根據介面契約創造規範的超能力模組。超人開始批量產出。最終，人人都是超人，你也可以是哦！

## 回歸正常世界。我們開始重新審視 laravel 的核心

現在，我們開始慢慢解讀 laravel 的核心。其實，laravel 的核心就是一個 IoC 容器，也恰好是我之前所說的高級的 IoC 容器。

可以說，laravel 的核心本身十分輕量，並沒有什麼很神奇很實質性的應用功能。很多人用到的各種功能模組比如 Route(路由)、Eloquent ORM(資料庫 ORM 组件)、Request and Response(乞求和響應)等等等等，實際上都是與核心無關的類別模組提供的，這些類別從註冊到實例化，最終被你所使用，其實都是 laravel 的服務容器負責的。

我們以大家最常見的 Route 類別作為例子。大家可能經常見到路由定義是這樣的：

```php
Route::get('/', function() {
    // bla bla bla...
});
```

實際上，Route 類別被定義在這個命名空間：Illuminate\Routing\Router，文件 vendor/laravel/framework/src/Illuminate/Routing/Router.php。

我們通過打開發現，這個類別的這一系列方法，如 get，post，any 等都不是靜態(static)方法，這是怎麼一回事兒？不要急，我們繼續。

服務提供者
我們在前文介紹 IoC 容器的部分中，提到了，一個類別需要綁定、註冊至容器中，才能被"製造"。

對，一個類別要被容器所能夠提取，必須要先註冊至這個容器。既然 laravel 稱這個容器叫做服務容器，那麼我們需要某個服務，就得先註冊、綁定這個服務到容器，那麼提供服務並綁定服務至容器的東西，就是服務提供者(ServiceProvider)。

雖然，綁定一個類別到容器不一定非要通過服務提供者(ServiceProvider)。

但是，我們知道，有時候我們的類別、模組會有需要其他類別和組件的情況，為了保證初始化階段不會出現所需要的模組和組件沒有註冊的情況，laravel 將註冊和初始化行為進行拆分，註冊的時候就只能註冊，初始化的時候就是初始化。拆分後的產物就是現在的服務提供者。

服務提供者主要分為兩個部分，register(注册)和 boot(引導、初始化)，具體參考官方文件。register 負責進行向容器註冊"腳本"，但要注意註冊部分不要有對未知事物的依賴，如果有，就要移步至 boot 部分。

正面
我們現在解答之前關於 Route 的方法為何能以靜態方法訪問的問題。實際上這個問題官方文件上有寫，簡單說來就是模擬一個類別，提供一個靜態魔術方法\_\_callStatic，並將該靜態方法對應到真正的方法上。

我們使用的 Route 類別實際上是 Illuminate\Support\Facades\Route 通過 class_alias() 函數創造的 别名 而已，這個類別被定義在文件 vendor/laravel/framework/src/Illuminate/Support/Facades/Route.php。

我們打開文件一看…… 誒？怎麼只有這麼簡單的一段代碼呢？

```php
namespace Illuminate\Support\Facades;

/**
 * @see \Illuminate\Routing\Router
 */
class Route extends Facade {

    /**
     * Get the registered name of the component.
     *
     * @return string
     */
    protected static function getFacadeAccessor() {
        return 'router';
    }
}
```

其實仔細看，會發現這個類別繼承了一個叫做 Facade 的類別，到這裡謎底差不多要解開了。

上述簡單的定義中，我們看到了 getFacadeAccessor 方法返回了一個 route，這是什麼意思呢？事實上，這個值被一個 ServiceProvider 註冊過，大家應該知道註冊了個什麼，當然是那個真正的路由類別！
