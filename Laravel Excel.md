# Laravel Excel

> 使用套件：
>
> - maatwebsite/Excel 3.1
>
> 環境要求：
>
> - PHP `^7.0`
>
> - Laravel `^5.5`
>
> 參考資料：
>
> - [maatwebsite/Excel 3.1 使用教程 （导入篇）](https://learnku.com/articles/32400)
>
> - [maatwebsite/Excel 3.1 使用教程 （导出篇）](https://learnku.com/articles/32391)

## 安裝

```bash
composer require maatwebsite/excel
```

## excel 匯出

建立匯出文件，匯入匯出的業務盡量不要和原來的業務耦合

```bash
php artisan make:export UserExport --model=User
```

在 app 目錄下建立 Export 資料夾

- UserExport.php

```php
namespace App\Exports;

use App\User;
use Maatwebsite\Excel\Concerns\FromCollection;

class UsersExport implements FromCollection
{
    protected $data;

    public function __construct($data)
    {
        $this->data = $data;
    }

    // 陣列轉集合
    public function collection()
    {
        return new Collection($this->createData());
    }

    // 商業邏輯
    public function createData()
    {
      // to-do
    }
}
```

- UserController.php

```php
use App\Exports\UsersExport;
use Maatwebsite\Excel\Facades\Excel;
use App\Http\Controllers\Controller;

class UsersController extends Controller
{
    public function export()
    {
        return Excel::download(new UsersExport, 'users.xlsx');
    }
}
```

### 欄位格式化：設定儲存格格式

```php
use PhpOffice\PhpSpreadsheet\Style\NumberFormat;
use Maatwebsite\Excel\Concerns\WithColumnFormatting;

// 新增 WithColumnFormatting
class UsersExport implements FromCollection, WithColumnFormatting
{
    public function columnFormats(): array
    {
        return [
            'B' => NumberFormat::FORMAT_DATE_DDMMYYYY, // 日期
            'C' => NumberFormat::FORMAT_NUMBER_00, // 取到小數點第二位
        ];
    }
}
```

### 自動欄寬

```php
use Maatwebsite\Excel\Concerns\ShouldAutoSize;

// 新增 ShouldAutoSize
class UsersExport implements FromCollection, ShouldAutoSize
```

### 匯出多個工作表(sheet)

匯出多個表需要兩步驟，先組裝 sheet，後建立對應的 sheet 表。

```php
use Maatwebsite\Excel\Concerns\Exportable;
use Maatwebsite\Excel\Concerns\WithMultipleSheets;

// 新增 WithMultipleSheets
class UsersExport implements WithMultipleSheets
{
    use Exportable;

    protected $year;

    public function __construct(int $year)
    {
        $this->year = $year;
    }

    /**
     * @return array
     */
    public function sheets(): array
    {
        $sheets = [];

        for ($month = 1; $month <= 12; $month++) {
            // 不同的資料可以呼叫不同的方法
            $sheets[] = new UserPerMonthSheet($this->year, $month);
        }

        return $sheets;
    }
}
```

然後新增 UserPerMonthSheet Class

```php
namespace App\Exports;

use Maatwebsite\Excel\Concerns\FromQuery;
use Maatwebsite\Excel\Concerns\WithTitle;

class UserPerMonthSheet implements FromQuery, WithTitle
{
    private $month;
    private $year;

    public function __construct(int $year, int $month)
    {
        $this->month = $month;
        $this->year  = $year;
    }

    /**
     * @return Builder
     */
    public function query()
    {
        return User
            ::query()
            ->whereYear('created_at', $this->year)
            ->whereMonth('created_at', $this->month);
    }

    /**
     * sheet 工作表名稱
     * @return string
     */
    public function title(): string
    {
        return 'Month ' . $this->month;
    }
}
```

### 事件模組：設定儲存格高度、垂直置中、字體顏色、背景顏色

提供多種事件：BeforeExport, BeforeWriting, BeforeSheet, AfterSheet 等等，修改儲存格使用 AfterSheet

```php
namespace App\Exports;

use Maatwebsite\Excel\Concerns\WithEvents;
use Maatwebsite\Excel\Events\BeforeExport;
use Maatwebsite\Excel\Events\BeforeWriting;
use Maatwebsite\Excel\Events\BeforeSheet;

class UserExport implements WithEvents
{
    /**
     * 註冊事件
     * @return array
     */
    public function registerEvents(): array
    {
        return [
            AfterSheet::class  => function(AfterSheet $event) {
                // 作者
                $event->writer->setCreator('Patrick');
                // 列寬
                $event->sheet->getDelegate()->getColumnDimension('A')->setWidth(50);
                // 行高，$i 為資料行數
                for ($i = 0; $i<=1265; $i++) {
                    $event->sheet->getDelegate()->getRowDimension($i)->setRowHeight(50);
                }
                // 垂直置中
                $event->sheet->getDelegate()->getStyle('A1:K1265')->getAlignment()->setVertical('center');
                // 字體、顏色、背景，詳細設定查看 applyFromArray 方法
                $event->sheet->getDelegate()->getStyle('A1:K6')->applyFromArray([
                    'font' => [
                        'name' => 'Arial',
                        'bold' => true,
                        'italic' => false,
                        'strikethrough' => false,
                        'color' => [
                            'rgb' => '808080'
                        ]
                    ],
                    'fill' => [
                        'fillType' => 'linear', // 漸層效果
                        'rotation' => 45, // 漸層角度
                        'startColor' => [
                            'rgb' => '000000' // 起始顏色
                        ],
                        // 結束顏色
                        'endColor' => [
                            'argb' => 'FFFFFF'
                        ]
                    ]
                ]);
                // 合併儲存格 mergeCells('A1:B1')
                $event->sheet->getDelegate()->mergeCells('A1:B1');
            }
        ];
    }
}
```

## excel 匯入
