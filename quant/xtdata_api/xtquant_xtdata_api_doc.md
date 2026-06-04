# xtquant.xtdata API 文档

> 取行情、财务等数据的相关接口

---

## 目录

- [订阅与回调](#订阅与回调)
  - [subscribe_quote](#subscribe_quote)
  - [subscribe_whole_quote](#subscribe_whole_quote)
  - [unsubscribe_quote](#unsubscribe_quote)
  - [run](#run)
- [行情数据](#行情数据)
  - [get_market_data](#get_market_data)
  - [get_market_data_ex](#get_market_data_ex)
  - [get_local_data](#get_local_data)
  - [get_full_tick](#get_full_tick)
- [Level2 数据](#level2-数据)
  - [get_l2_quote](#get_l2_quote)
  - [get_l2_order](#get_l2_order)
  - [get_l2_transaction](#get_l2_transaction)
- [除权除息](#除权除息)
  - [get_divid_factors](#get_divid_factors)
- [财务数据](#财务数据)
  - [get_financial_data](#get_financial_data)
  - [download_financial_data](#download_financial_data)
- [合约与板块](#合约与板块)
  - [get_instrument_detail](#get_instrument_detail)
  - [get_instrument_type](#get_instrument_type)
  - [get_sector_list](#get_sector_list)
  - [get_stock_list_in_sector](#get_stock_list_in_sector)
  - [add_sector](#add_sector)
  - [remove_sector](#remove_sector)
- [指数权重](#指数权重)
  - [get_index_weight](#get_index_weight)
  - [download_index_weight](#download_index_weight)
- [交易日历](#交易日历)
  - [get_trading_dates](#get_trading_dates)
  - [get_holidays](#get_holidays)
  - [get_trading_calendar](#get_trading_calendar)
  - [get_trade_times](#get_trade_times)
- [数据下载](#数据下载)
  - [download_history_data](#download_history_data)
  - [download_sector_data](#download_sector_data)
- [可转债](#可转债)
  - [download_cb_data](#download_cb_data)
  - [get_cb_info](#get_cb_info)
- [工具函数](#工具函数)
  - [datetime_to_timetag](#datetime_to_timetag)
  - [timetag_to_datetime](#timetag_to_datetime)

---

## 订阅与回调

### subscribe_quote

```python
def subscribe_quote(stock_code, period='1d', start_time='', end_time='', count=0, callback=None)
```

订阅股票行情数据。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `stock_code` | str | 股票代码，如 `"000001.SZ"` |
| `start_time` | str | 开始时间，格式 `YYYYMMDD` / `YYYYMMDDhhmmss` / `YYYYMMDDhhmmss.milli`，如 `"20200427"`、`"20200427093000"`、`"20200427093000.000"`。若取某日全量历史数据，时间需要具体到秒，如 `"20200427093000"` |
| `end_time` | str | 结束时间，同 `start_time` |
| `count` | int | 数量，`-1` 为全部，`n` 为从结束时间向前数 `n` 个 |
| `period` | str | 周期：分笔 `"tick"`，分钟线 `"1m"` / `"5m"`，日线 `"1d"` |
| `callback` | callable | 订阅回调函数 `onSubscribe(datas)`，其中 `datas` 为 `{stock: [data1, data2, ...]}` 数据字典 |

#### Returns

> `int` — 订阅序号

---

### subscribe_whole_quote

```python
def subscribe_whole_quote(code_list, callback=None)
```

订阅全推数据。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `code_list` | list[str] | 市场代码列表，如 `["SH", "SZ"]` |
| `callback` | callable | 订阅回调函数 `onSubscribe(datas)`，其中 `datas` 为 `{stock1: data1, stock2: data2, ...}` 数据字典 |

#### Returns

> `int` — 订阅序号

---

### unsubscribe_quote

```python
def unsubscribe_quote(seq)
```

取消订阅。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `seq` | int | 订阅接口 `subscribe_quote` 返回的订阅号 |

---

### run

```python
def run()
```

阻塞线程接收行情回调。

---

## 行情数据

### get_market_data

```python
def get_market_data(
    field_list=[], stock_list=[], period='1d',
    start_time='', end_time='', count=-1,
    dividend_type='none', fill_data=True
)
```

获取历史行情数据。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `field_list` | list[str] | 行情数据字段列表，`[]` 为全部字段 |
| `stock_list` | list[str] | 股票代码，如 `"000001.SZ"` |
| `period` | str | 周期：分笔 `"tick"`，分钟线 `"1m"` / `"5m"`，日线 `"1d"` |
| `start_time` | str | 起始时间，如 `"20200101"`、`"20200101093000"` |
| `end_time` | str | 结束时间，如 `"20201231"`、`"20201231150000"` |
| `count` | int | 数量，`-1` 为全部，`n` 为从结束时间向前数 `n` 个 |
| `dividend_type` | str | 除权类型：`"none"`、`"front"`、`"back"`、`"front_ratio"`、`"back_ratio"` |
| `fill_data` | bool | 对齐时间戳时是否填充数据，仅对 K 线有效，分笔周期不对齐时间戳。为 `True` 时，以缺失数据的前一条数据填充（`open`/`high`/`low`/`close` 为前一条数据的 `close`，`amount`/`volume` 为 0，`settle`/`openInterest` 和前一条数据相同）；为 `False` 时，缺失数据所有字段填 `NaN` |

#### field_list 可选字段

**K 线可选字段：**

| 字段名 | 说明 |
|--------|------|
| `"time"` | 时间戳 |
| `"open"` | 开盘价 |
| `"high"` | 最高价 |
| `"low"` | 最低价 |
| `"close"` | 收盘价 |
| `"volume"` | 成交量 |
| `"amount"` | 成交额 |
| `"settle"` | 今结算 |
| `"openInterest"` | 持仓量 |

**分笔可选字段：**

| 字段名 | 说明 |
|--------|------|
| `"time"` | 时间戳 |
| `"lastPrice"` | 最新价 |
| `"open"` | 开盘价 |
| `"high"` | 最高价 |
| `"low"` | 最低价 |
| `"lastClose"` | 前收盘价 |
| `"amount"` | 成交总额 |
| `"volume"` | 成交总量 |
| `"pvolume"` | 原始成交总量 |
| `"stockStatus"` | 证券状态 |
| `"openInt"` | 持仓量 |
| `"lastSettlementPrice"` | 前结算 |
| `"askPrice1"` ~ `"askPrice5"` | 卖一价 ~ 卖五价 |
| `"bidPrice1"` ~ `"bidPrice5"` | 买一价 ~ 买五价 |
| `"askVol1"` ~ `"askVol5"` | 卖一量 ~ 卖五量 |
| `"bidVol1"` ~ `"bidVol5"` | 买一量 ~ 买五量 |

#### Returns

> 数据集，分笔数据和 K 线数据格式不同：
> - `period` 为 `"tick"` 时：`{stock1: value1, stock2: value2, ...}`，其中 `stock1, stock2, ...` 为合约代码，`value1, value2, ...` 为 `np.ndarray` 数据列表，按 `time` 增序排列
> - `period` 为其他 K 线周期时：`{field1: value1, field2: value2, ...}`，其中 `field1, field2, ...` 为数据字段，`value1, value2, ...` 为 `pd.DataFrame` 字段对应的数据，各字段维度相同，`index` 为 `stock_list`，`columns` 为 `time_list`

---

### get_market_data_ex

```python
def get_market_data_ex(
    field_list=[], stock_list=[], period='1d',
    start_time='', end_time='', count=-1,
    dividend_type='none', fill_data=True
)
```

获取历史行情数据（扩展版）。参数与 `get_market_data` 相同，返回格式略有差异，K 线数据返回 `{stock: pd.DataFrame}` 格式。

---

### get_local_data

```python
def get_local_data(
    field_list=[], stock_code=[], period='1d',
    start_time='', end_time='', count=-1,
    dividend_type='none', fill_data=True, data_dir='../userdata_mini/datadir'
)
```

获取本地数据（当前实现返回 `None`）。

---

### get_full_tick

```python
def get_full_tick(code_list)
```

获取盘口 tick 数据。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `code_list` | list[str] | `stock.market` 组成的股票代码列表 |

#### Returns

> `dict` — `{'stock.market': {dict}}`

---

## Level2 数据

### get_l2_quote

```python
def get_l2_quote(field_list=[], stock_code='', start_time='', end_time='', count=-1)
```

Level2 实时行情。

---

### get_l2_order

```python
def get_l2_order(field_list=[], stock_code='', start_time='', end_time='', count=-1)
```

Level2 逐笔委托。

---

### get_l2_transaction

```python
def get_l2_transaction(field_list=[], stock_code='', start_time='', end_time='', count=-1)
```

Level2 逐笔成交。

---

## 除权除息

### get_divid_factors

```python
def get_divid_factors(stock_code, start_time='', end_time='')
```

获取除权除息日及对应的权息。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `stock_code` | str | 股票代码 |
| `start_time` | str | 起始时间 |
| `end_time` | str | 结束时间 |

#### Returns

> `pd.DataFrame` — 数据集

---

## 财务数据

### get_financial_data

```python
def get_financial_data(stock_list, table_list=[], start_time='', end_time='', report_type='report_time')
```

获取财务数据。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `stock_list` | list[str] | 合约代码列表 |
| `table_list` | list[str] | 报表名称列表，`[]` 为全部表 |
| `start_time` | str | 起始时间 |
| `end_time` | str | 结束时间 |
| `report_type` | str | 时段筛选方式：`"announce_time"` / `"report_time"` |

#### table_list 可选表名

| 表名 | 对应数据库表 |
|------|-------------|
| `Balance` | `ASHAREBALANCESHEET` |
| `Income` | `ASHAREINCOME` |
| `CashFlow` | `ASHARECASHFLOW` |
| `Capital` | `CAPITALSTRUCTURE` |
| `HolderNum` | `SHAREHOLDER` |
| `Top10Holder` | `TOP10HOLDER` |
| `Top10FlowHolder` | `TOP10FLOWHOLDER` |
| `PershareIndex` | `PERSHAREINDEX` |

#### Returns

> ```
> field: list[str]
> date: list[int]
> stock: list[str]
> value: list[list[float]]
> ```

---

### download_financial_data

```python
def download_financial_data(stock_list, table_list=[], start_time='', end_time='')
```

下载财务数据。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `stock_list` | list[str] | 股票代码列表 |
| `table_list` | list[str] | 财务数据表名列表，`[]` 为全部表。可选范围：`['Balance','Income','CashFlow','Capital','Top10FlowHolder','Top10Holder','HolderNum','PershareIndex','PerShare']` |
| `start_time` | str | 开始时间，格式 `YYYYMMDD`，如 `"20200427"` |
| `end_time` | str | 结束时间，同上，若是未来某时刻会被视作当前时间 |

---

## 合约与板块

### get_instrument_detail

```python
def get_instrument_detail(stock_code)
```

获取合约信息。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `stock_code` | str | 股票代码，如 `"600000.SH"` |

#### Returns

> `dict` — 合约详细信息：
> - `ExchangeID` (str): 合约市场代码
> - `InstrumentID` (str): 合约代码
> - `InstrumentName` (str): 合约名称
> - `ProductID` (str): 合约的品种 ID（期货）
> - `ProductName` (str): 合约的品种名称（期货）
> - `CreateDate` (int/str): 上市日期（期货）
> - `OpenDate` (int/str): IPO 日期（股票）
> - `ExpireDate` (int): 退市日或者到期日
> - `PreClose` (double): 前收盘价格
> - `SettlementPrice` (double): 前结算价格
> - `UpStopPrice` (double): 当日涨停价
> - `DownStopPrice` (double): 当日跌停价
> - `FloatVolume` (double): 流通股本
> - `TotalVolume` (double): 总股本
> - `LongMarginRatio` (double): 多头保证金率
> - `ShortMarginRatio` (double): 空头保证金率
> - `PriceTick` (double): 最小变价单位
> - `VolumeMultiple` (int): 合约乘数（对期货以外的品种，默认是 1）
> - `MainContract` (int): 主力合约标记，1、2、3 分别表示第一主力合约、第二主力合约、第三主力合约
> - `LastVolume` (int): 昨日持仓量
> - `InstrumentStatus` (int): 合约停牌状态
> - `IsTrading` (bool): 合约是否可交易
> - `IsRecent` (bool): 是否是近月合约
> - `ProductTradeQuota` / `ContractTradeQuota` / `ProductOpenInterestQuota` / `ContractOpenInterestQuota`: 扩展信息

---

### get_instrument_type

```python
def get_instrument_type(stock_code)
```

判断证券类型。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `stock_code` | str | 股票代码，如 `"600000.SH"` |

#### Returns

> `dict{str: bool}` — `{类型名: 是否属于该类型}`

---

### get_sector_list

```python
def get_sector_list()
```

获取板块列表。

#### Returns

> `list[str]`

---

### get_stock_list_in_sector

```python
def get_stock_list_in_sector(sector_name)
```

获取板块成份股，支持客户端左侧板块列表中任意的板块，包括自定义板块。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `sector_name` | str | 板块名称 |

#### Returns

> `list`

---

### add_sector

```python
def add_sector(sector_name, stock_list)
```

增加自定义板块。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `sector_name` | str | 板块名称，如 `"我的自选"` |
| `stock_list` | list[str] | `stock.market` 组成的股票代码列表 |

---

### remove_sector

```python
def remove_sector(sector_name)
```

删除自定义板块。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `sector_name` | str | 板块名称，如 `"我的自选"` |

---

## 指数权重

### get_index_weight

```python
def get_index_weight(index_code)
```

获取某只股票在某指数中的绝对权重。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `index_code` | str | 指数名称 |

#### Returns

> `dict`

---

### download_index_weight

```python
def download_index_weight()
```

下载指数权重数据。

---

## 交易日历

### get_trading_dates

```python
def get_trading_dates(market, start_time='', end_time='', count=-1)
```

根据市场获取交易日列表。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `market` | str | 市场代码，如 `'SH'`、`'SZ'`、`'IF'`、`'DF'`、`'SF'`、`'ZF'` 等 |
| `start_time` | str | 起始时间，如 `'20200101'` |
| `end_time` | str | 结束时间，如 `'20201231'` |
| `count` | int | 数据个数，`-1` 为全部数据 |

#### Returns

> `list[long]` — 毫秒数的时间戳列表

---

### get_holidays

```python
def get_holidays()
```

获取节假日列表。

#### Returns

> `list[str]` — 8 位 int 型日期字符串

---

### get_trading_calendar

```python
def get_trading_calendar(market, start_time='', end_time='', tradetimes=False)
```

获取指定市场交易日历。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `market` | str | 市场 |
| `start_time` | str | 起始时间，如 `'20200101'` |
| `end_time` | str | 结束时间，如 `'20201231'` |
| `tradetimes` | bool | 是否包含日内交易时段 |

#### Returns

> - `tradetimes=False` 时：返回交易日列表 `list[str]`
> - `tradetimes=True` 时：返回交易日历字典 `dict`，键为日期，值为该日交易时段列表

---

### get_trade_times

```python
def get_trade_times(stockcode)
```

返回指定市场或者指定股票的交易时段。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `stockcode` | str | 市场或者 `代码.市场`，如 `'SH'` 或 `'600000.SH'` |

#### Returns

> 返回交易时段列表，每个元素为 `[开始时间, 结束时间, 交易类型]`：
> - 交易类型：`2` - 开盘竞价，`3` - 连续交易，`8` - 收盘竞价，`9` - 盘后定价

---

## 数据下载

### download_history_data

```python
def download_history_data(stock_code, period, start_time='', end_time='')
```

下载历史行情数据。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `stock_code` | str | 股票代码，如 `"000001.SZ"` |
| `period` | str | 周期：分笔 `"tick"`，分钟线 `"1m"` / `"5m"`，日线 `"1d"` |
| `start_time` | str | 开始时间，格式 `YYYYMMDD` / `YYYYMMDDhhmmss` / `YYYYMMDDhhmmss.milli`，如 `"20200427"`。若取某日全量历史数据，时间需要具体到秒，如 `"20200427093000"` |
| `end_time` | str | 结束时间，同上，若是未来某时刻会被视作当前时间 |

#### Returns

> `bool` — 是否成功

---

### download_sector_data

```python
def download_sector_data()
```

下载行业板块数据。

---

## 可转债

### download_cb_data

```python
def download_cb_data()
```

下载可转债数据。

---

### get_cb_info

```python
def get_cb_info(stockcode)
```

获取可转债信息。

---

## 工具函数

### datetime_to_timetag

```python
def datetime_to_timetag(datetime, format="%Y%m%d%H%M%S")
```

将日期时间字符串转换为毫秒时间戳。

---

### timetag_to_datetime

```python
def timetag_to_datetime(timetag, format)
```

将毫秒时间戳转换成日期时间字符串。

#### Parameters

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `timetag` | int | 时间戳毫秒数 |
| `format` | str | 时间格式 |

#### Returns

> `str`

---

## 别名

| 别名 | 原函数 |
|------|--------|
| `gmd` | `get_market_data` |
| `gmd2` | `get_market_data_ex` |
| `gmd3` | `get_market_data3` |
| `gld` | `get_local_data` |
| `t2d` | `timetag_to_datetime` |
| `gsl` | `get_stock_list_in_sector` |
| `get_stock_type` | `get_instrument_type` |
| `supply_history_data` | `download_history_data` |
