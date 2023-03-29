# BST1_code
// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
//@dabinlee
//@version=5
strategy('BST1', default_qty_type =  strategy.percent_of_equity, default_qty_value = 100, overlay=true)

len1 = input(20)
len2 = input(40)
len3 = input(80)

ma_num = input.int(0, minval=0, maxval=5, title='0:sma 1:ema 2:rema 3:wma 4:vwma')
vwma_e(src, len) =>
    ta.ema(src * volume, len) / ta.ema(volume, len)
vwma_e2(src, len) =>
    vp = math.pow(volume, 3)
    ta.ema(src * vp, len) / ta.ema(vp, len)

mma(len) =>
    ma = close
    if ma_num == 0
        ma := ta.vwma(close, len)
        ma
    if ma_num == 1
        ma := ta.ema(close, len)
        ma
    if ma_num == 2
        ma := (ta.sma(close,len) + ta.rma(close, len))/2
        ma
    if ma_num == 3
        ma := ta.wma(close, len)
        ma
    if ma_num == 4
        ma := ta.vwma(close, len)
        ma
    if ma_num == 5
        ma := vwma_e(close, len)
        ma

    ma

ma1 = mma(len1)
ma2 = mma(len2)
ma3 = mma(len3)
//atr
L_multi = input.float(3, "multi", step = 0.1)
S_multi = input.float(4.5, "multi", step = 0.1)
atr = ta.atr(14)
limitP = high + atr*L_multi
stopP = low -atr*S_multi
plot(limitP, color=color.green)
plot(stopP, color=color.red)
//정배열 역배열
bull = ma1 > ma2 and ma2 > ma3
bear = ma1 < ma2 and ma2 < ma3

// long_cond= ta.crossover(ma1,ma2)
var exit = 0.0
var entry = 0.0

plot(ma1, title='line1', color=color.new(color.blue, 0))
ma2p = plot(ma2, title='line2', color=#b01010ff)
plot(ma3, title='line3', color=color.new(color.green, 0))

if bull and strategy.opentrades == 0
    strategy.entry("롱", strategy.long)
    if strategy.position_size ==0
        strategy.exit("손익절", "롱", stop = stopP, limit = limitP, comment_profit = "익절",comment_loss ="손절")

if bull and strategy.opentrades == 0
    alert("정배열", alert.freq_once_per_bar_close)
else if stopP
    alert("손절", alert.freq_once_per_bar_close)
else if limitP
    alert("익절", alert.freq_once_per_bar_close)
