// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © Dabinlee

//@version=5
strategy("bst3일부보완",overlay=true,margin_long=100,initial_capital = 1000,process_orders_on_close = true,default_qty_type = strategy.percent_of_equity,default_qty_value = 100)

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

if longcond
    strategy.entry("롱", strategy.long)
    alert("롱",alert.freq_once_per_bar_close)
if exit 
    strategy.exit("롱", comment="롱종료",stop = long_stop_price)
    alert("롱종료",alert.freq_once_per_bar_close)
