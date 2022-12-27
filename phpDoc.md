# phpDoc

[自動編寫程式](https://github.com/barryvdh/laravel-ide-helper)

## 通用註解寫法

### 註解範例（普通程式文件，類別文件，函數文件，變量定義文件）

```php
/**
 * XXXXX的文件
 *
 * 功能1： xxx
 * 功能2： xxx
 *
 * @file        $https://github.com/changgenglu/note/blob/master/phpDoc.md  $
 * @package     core
 * @author      Ivan <eatbreakfast2012@gmail.com>
 * @version     V1.0.0
 * @link        https://github.com/changgenglu/note/blob/master/phpDoc.md
 */
```

- `@file` 檔案位置：

- `@version` 版本：

- `@package` 是團隊事先定義好的，在 phpdocumentor 里同一 package 的文件可以給出跟蹤的鏈接。項目開發前需要對其定義。

- `@link` 行後面接的地址是程式開發文檔的地址

> 說明：以上自動更新版本及文件名需要設定 SVN，具體請自行搜尋 _'SVN 自動版本號'_

### 類別註解，使用如下幾個 tag

```php
/**
 * xxxxx類
 *
 * 功能1：完成xxxx
 * 功能2：完成xxxxx
 *
 * @author      Ivan <eatbreakfast2012@gmail.com>
 * @access      public
 * @abstract
 */
public class DemoClass {
 //code...
}
```

- `@access` (`public`|`private`) 標記類是私有的還是公用的。
- `@abstract`標記該類是個抽象類

### 類別屬性宣告註解

```php
public class DemoClass {

   /**
    * 當前頁碼
    *
    * @var integer
    */
    public $currentPageNumber;

   /**
    * 私有變量使用下劃線開頭
    *
    * @var string
    */
    private $_instance;
}
```

### 普通函數和類別中的函數註解

```php
/**
 * 獲得頭貼地址
 *
 * @author Ivan <eatbreakfast2012@gmail.com>
 *
 * @param string  $imageName  圖片檔案名
 * @param integer $size     檔案大小
 *
 * @return string
 */
function getAvatarUrl($imageName, $size = 80)
{
    return sprintf(SITE_URL . '/service/images/cropped_%s/'.$imageName, $size);
}

```

順序按照 author、param、return 來放，**區塊間空行**。

### 程式段落註解

段落註解和邏輯註解使用如下方式

```php
/**
 * 1 如果$_GET['do']等於buy,則購買條碼
 */
if($_GET['do'] == 'buy')
{
    // 1.1 驗證用戶提交變量是否合法
    if($_POST['strCodeNum'])
    {

    }
    // 1.2 驗證用戶提交的碼是否可以購買
    // 1.3 ..................
} // end if

/**
 * 2 如果$_GET['do']等於list,顯示用戶選擇的條碼
 */
if($_GET['do'] == 'list')
{
    // 2.1 驗證用戶提交變量是否合法
    if($_POST['strCodeNum'])
    {

    }
    // 2.2 驗證用戶提交的碼是否可以購買

    // 2.3 ..................
} // end if
```

## PHPdoc 規範

[WIKI 上的 PHPDoc](http://en.wikipedia.org/wiki/Phpdoc)

| 標記        | 用途                              | 描述                                                                     |
| ----------- | --------------------------------- | ------------------------------------------------------------------------ |
| @abstract   |                                   | 抽象類的變量和方法                                                       |
| @access     | public, private or protected      | 文檔的訪問、使用權限. @access private 表明這個文檔是被保護的。           |
| @author     | Ivan <eatbreakfast2012@gmail.com> | 文檔作者                                                                 |
| @copyright  | 名稱 時間                         | 文檔版權信息                                                             |
| @deprecated | version                           | 文檔中被廢除的方法                                                       |
| @deprec     |                                   | 同 @deprecated                                                           |
| @example    | /path/to/example                  | 文檔的外部保存的示例文件的位置。                                         |
| @exception  |                                   | 文檔中方法拋出的異常，也可參照 @throws.                                  |
| @global     | 類型：$globalvarname              | 文檔中的全局變量及有關的方法和函數                                       |
| @ignore     |                                   | 忽略文檔中指定的關鍵字                                                   |
| @internal   |                                   | 開發團隊內部信息                                                         |
| @link       | URL                               | 類似於 license 但還可以通過 link 找到文檔中的更多個詳細的信息            |
| @name       | 變量別名                          | 為某個變量指定別名                                                       |
| @magic      |                                   | phpdoc.de compatibility                                                  |
| @package    | 封裝包的名稱                      | 一組相關類、函數封裝的包名稱                                             |
| @param      | 如 $username 用戶名               | 變量含義註解                                                             |
| @return     | 如 返回 bool                      | 函數返回結果描述，一般不用在 void（空返回結果的）的函數中                |
| @see        | 如 Class Login()                  | 文件關聯的任何元素（全局變量，包括，頁面，類，函數，定義，方法，變量）。 |
| @since      | version                           | 記錄什麽時候對文檔的哪些部分進行了更改                                   |
| @static     |                                   | 記錄靜態類、方法                                                         |
| @staticvar  |                                   | 在類、函數中使用的靜態變量                                               |
| @subpackage |                                   | 子版本                                                                   |
| @throws     |                                   | 某一方法拋出的異常                                                       |
| @todo       |                                   | 表示文件未完成或者要完善的地方                                           |
| @var        | type                              | 文檔中的變量及其類型                                                     |
| @version    |                                   | 文檔、類、函數的版本信息                                                 |

PHPDoc 註解實例：

```php
<?php
 /**
  * start page for webaccess
  *
  * PHP version 5
  *
  * @category  PHP
  * @package   PSI_Web
  * @author    Michael Cramer <BigMichi1@users.sourceforge.net>
  * @copyright 2009 phpSysInfo
  * @license   http://opensource.org/licenses/gpl-2.0.php GNU General Public License
  * @version   SVN: $Id: class.Webpage.inc.php 412 2010-12-29 09:45:53Z Jacky672 $
  * @link      http://phpsysinfo.sourceforge.net
  */
  /**
  * generate the dynamic webpage
  *
  * @category  PHP
  * @package   PSI_Web
  * @author    Michael Cramer <BigMichi1@users.sourceforge.net>
  * @copyright 2009 phpSysInfo
  * @license   http://opensource.org/licenses/gpl-2.0.php GNU General Public License
  * @version   Release: 3.0
  * @link      http://phpsysinfo.sourceforge.net
  */
 class Webpage extends Output implements PSI_Interface_Output
 {
     /**
      * configured language
      *
      * @var String
      */
     private $_language;

     /**
      * configured template
      *
      * @var String
      */
     private $_template;

     /**
      * all available templates
      *
      * @var Array
      */
     private $_templates = array();

     /**
      * all available languages
      *
      * @var Array
      */
     private $_languages = array();

     /**
      * check for all extensions that are needed, initialize needed vars and read config.php
      */
     public function __construct()
     {
         parent::__construct();
         $this->_getTemplateList();
         $this->_getLanguageList();
     }

     /**
      * checking config.php setting for template, if not supportet set phpsysinfo.css as default
      * checking config.php setting for language, if not supported set en as default
      *
      * @return void
      */
     private function _checkTemplateLanguage()
     {
         $this->_template = trim(PSI_DEFAULT_TEMPLATE);
         if (!file_exists(APP_ROOT.'/templates/'.$this->_template.".css")) {
             $this->_template = 'phpsysinfo';
         }

         $this->_language = trim(PSI_DEFAULT_LANG);
         if (!file_exists(APP_ROOT.'/language/'.$this->_language.".xml")) {
             $this->_language = 'en';
         }
     }

     /**
      * get all available tamplates and store them in internal array
      *
      * @return void
      */
     private function _getTemplateList()
     {
         $dirlist = CommonFunctions::gdc(APP_ROOT.'/templates/');
         sort($dirlist);
         foreach ($dirlist as $file) {
             $tpl_ext = substr($file, strlen($file) - 4);
             $tpl_name = substr($file, 0, strlen($file) - 4);
             if ($tpl_ext === ".css") {
                 array_push($this->_templates, $tpl_name);
             }
         }
     }

     /**
      * get all available translations and store them in internal array
      *
      * @return void
      */
     private function _getLanguageList()
     {
         $dirlist = CommonFunctions::gdc(APP_ROOT.'/language/');
         sort($dirlist);
         foreach ($dirlist as $file) {
             $lang_ext = substr($file, strlen($file) - 4);
             $lang_name = substr($file, 0, strlen($file) - 4);
             if ($lang_ext == ".xml") {
                 array_push($this->_languages, $lang_name);
             }
         }
     }

     /**
      * render the page
      *
      * @return void
      */
     public function run()
     {
         $this->_checkTemplateLanguage();

         $tpl = new Template("/templates/html/index_dynamic.html");

         $tpl->set("template", $this->_template);
         $tpl->set("templates", $this->_templates);
         $tpl->set("language", $this->_language);
         $tpl->set("languages", $this->_languages);

         echo $tpl->fetch();
     }
 }
```
