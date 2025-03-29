# NUMBER_ENGINIERING

---

# توضیحات کد

این کد یک اندیکاتور برای پلتفرم TradingView ایجاد می‌کند که به نام "تقریب و تا زدن عدد اعشاری VIPER" شناخته می‌شود. این اندیکاتور بر روی چارت قیمت نمایش داده می‌شود و برای تنظیم دقت اعشاری اعداد استفاده می‌شود.

## جزئیات کد

```pinescript
//@version=6
indicator('VIPER DECIMAL APPROXIMATION & TRUNCATE', overlay = true)
```
این خط نسخه Pine Script را مشخص می‌کند و نام اندیکاتور را تعیین می‌کند. همچنین مشخص می‌کند که این اندیکاتور روی چارت قیمت (overlay) قرار می‌گیرد.

### ۱. تنظیمات دقت اعشاری

```pinescript
var int CUSTOM_USER_DECIMAL_ADJUSTMENT = input.int(defval = 1, title = "USER CUSTOM DECIMALS ADJUSTMENT", tooltip = "Adjust decimal precision manually if needed", maxval = 64, minval = -16, step = 1)
```
در اینجا، یک ورودی برای تنظیم دقت اعشاری ایجاد می‌شود که می‌تواند بین -16 تا 64 متغیر باشد.

### ۲. تابع تقریب عدد اعشاری

```pinescript
VIPER_DECIMAL_APPROXIMATION_FUNCTION(NUMBER) =>
    var int DECIMALS = 0
    if NUMBER <= 10
        DECIMALS := 16 + CUSTOM_USER_DECIMAL_ADJUSTMENT
    else if NUMBER <= 99
        DECIMALS := 8 + CUSTOM_USER_DECIMAL_ADJUSTMENT
    else if NUMBER <= 999
        DECIMALS := 5 + CUSTOM_USER_DECIMAL_ADJUSTMENT
    else
        DECIMALS := 3 + CUSTOM_USER_DECIMAL_ADJUSTMENT
    [DECIMALS]
```
این تابع دقت اعشاری را بر اساس مقدار عدد ورودی محاسبه می‌کند. برای اعداد کوچک‌تر از 10، 16، و برای اعداد بزرگتر، دقت کمتری انتخاب می‌شود.

### ۳. تابع تا زدن اعداد

```pinescript
VIPER_NUMBER_TRUNCATE_FUNCTION(NUMBER, DECIMALS) =>
    var float FACTOR = na
    var float RESULT = na
    FACTOR := math.pow(10, DECIMALS + CUSTOM_USER_DECIMAL_ADJUSTMENT)
    RESULT := math.floor(math.ceil(NUMBER * FACTOR)) * math.pow(FACTOR, -1)
    [FACTOR, RESULT]
```
این تابع عدد ورودی را تا دقت مشخص شده (DECIMALS) تا می‌زند و همچنین عامل لازم برای این کار را محاسبه می‌کند.

### ۴. ورودی عدد (قیمت بسته شدن)

```pinescript
NUMBER = request.security(syminfo.tickerid, "1", close)
```
اینجا قیمت بسته شدن سهم مورد نظر به عنوان ورودی گرفته می‌شود.

### ۵. جدول نتایج

```pinescript
var table TBL = table.new(position = position.top_center, columns = 3, rows = 5, bgcolor = color.gray)
```
این خط یک جدول جدید ایجاد می‌کند که نتایج محاسبات در آن نمایش داده می‌شود. 

#### رنگ‌های جدول

رنگ‌های مختلف برای سطرها و عنوان‌ها تنظیم می‌شود تا خوانایی جدول بیشتر شود.

### ۶. نمایش نتایج

```pinescript
if barstate.islast
    // Merge top row for table title
    ...
    // Populate table with values
    ...
```
در این قسمت، در صورتی که بار نمودار آخرین بار باشد، مقادیر محاسبه‌شده در جدول نمایش داده می‌شوند، شامل مقادیر اصلی، دقت‌ها، و مقادیر تا شده.

---
