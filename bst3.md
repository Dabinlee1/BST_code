// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © Dabinlee

//@version=5
strategy("bst3일부보완2",overlay=true,margin_long=100,initial_capital = 1000,process_orders_on_close = true,default_qty_type = strategy.percent_of_equity,default_qty_value = 100, pyramiding = 3, commission_value = 0.03)

rsi = ta.rsi(close,14)

longcond = ta.crossover(rsi,25)
rsi2 = ta.rsi(close,14)
exit = ta.rsi(close,14) >= 50 
long_trail_percent = input.float(2.0, "롱 트레일")/100
long_stop_price = 0.0
if strategy.position_size > 0 
    stop_value = close * (1-long_trail_percent*1)
    long_stop_price := math.max(stop_value, long_stop_price[1])
else
    long_stop_price := 0.0 // 롱 스탑을 무력화

if longcond and strategy.position_size < 2
    strategy.entry("롱", strategy.long)
if exit and strategy.position_size < 2
    if long_stop_price
        strategy.close("롱", comment="롱정리)

if longcond and strategy.position_size < 2
    alert("롱진입",alert.freq_once_per_bar_close)
if exit and strategy.position_size < 2
    alert("RSI50돌파",alert.freq_once_per_bar_close)
if exit and strategy.position_size < 2
        if long_stop_price
            alert("롱정리",alert.freq_once_per_bar_close)
