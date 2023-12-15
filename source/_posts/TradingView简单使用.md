---
title: TradingView简单使用
date: 2023-12-8 20:54:43
tags:
    - tradingView
categories:  [业务,插件]
   
---

使用的高级表格tradingView，官网需要公司资质下载专用的charting_library,datafeeds，这里在网上下载一个不是最新版本的
![Alt text](/img/trad_image.png)
<!-- more -->
index.html
```
  <script src="/public/charting_library/charting_library.min.js"></script>
  <script src="/public/datafeeds/udf/dist/bundle.js"></script>
  <script src="/public/datafeeds/udf/dist/polyfills.js"></script>
```
画布
```
<template>
  <div id="chart_container"></div>
</template>
```
配置,DataFeeds可以直接用线上的，也可以自定义
```
  new window.TradingView.widget({
    fullscreen: true, //全屏
    symbol: '000001',
    interval: 'D',  // 时间间隔
    timeframe: '48M', //时间范围 默认查询4年的
    container_id: "chart_container",
    timezone: "America/New_York",

    //	BEWARE: no trailing slash is expected in feed URL
    // datafeed: new window.Datafeeds.UDFCompatibleDatafeed("https://demo-feed-data.tradingview.com"),
    datafeed: DataFeeds,
    library_path: "charting_library/",
    locale: getParameterByName('lang') || "zh",
    theme: getParameterByName('theme'),
  });

  //本地语言环境，配色环境
function getParameterByName(name: string) {
  name = name.replace(/[\[]/, "\\[").replace(/[\]]/, "\\]");
  var regex = new RegExp("[\\?&]" + name + "=([^&#]*)"),
    results = regex.exec(location.search);
  return results === null ? "" : decodeURIComponent(results[1].replace(/\+/g, " "));
}
```

datafeed配置主要方法configurationData，onReady，searchSymbols，getBars，subscribeBars，unsubscribeBars

举一个例子，makeApiRequest为获取数据的接口
```
import dayjs from 'dayjs'
import { makeApiRequest } from './helpers'

// DatafeedConfiguration implementation
const configurationData = {
  // Represents the resolutions for bars supported by your datafeed
  supported_resolutions: ['D', '2D', '3D', 'W', '3W', 'M', '6M'],
  exchanges: [{ value: '000001', name: '股票代码', desc: '000001' }],
  symbols_types: [{ name: 'stock', value: 'stock' }]
}

// Obtains all symbols for all exchanges supported by CryptoCompare API
async function getAllSymbols() {
  const data = await makeApiRequest('getStockList')
  data.forEach((item) => {
    item.type = 'stock'
    item.description = item.name
  })
  return data
}

export default {
  onReady: (callback) => {
    console.log('[onReady]: Method call')
    setTimeout(() => callback(configurationData))
  },

  searchSymbols: async (userInput, ts_code, symbolType, onResultReadyCallback) => {
    console.log('[searchSymbols]: Method call')
    const symbols = await getAllSymbols()
    const newSymbols = symbols.filter((symbol) => {
      const isExchangeValid = ts_code === '' || symbol.symbol === ts_code
      const isFullSymbolContainsInput =
        symbol.ts_code.toLowerCase().indexOf(userInput.toLowerCase()) !== -1
      return isExchangeValid && isFullSymbolContainsInput
    })
    onResultReadyCallback(newSymbols)
  },

  resolveSymbol: async (
    symbolName,
    onSymbolResolvedCallback,
    onResolveErrorCallback,
    extension
  ) => {
    console.log('[resolveSymbol]: Method call', symbolName)
    const symbols = await getAllSymbols()
    const symbolItem = symbols.find(({ symbol }) => symbol === symbolName)
    if (!symbolItem) {
      console.log('[resolveSymbol]: Cannot resolve symbol', symbolName)
      onResolveErrorCallback('Cannot resolve symbol')
      return
    }
    // Symbol information object
    const symbolInfo = {
      ticker: symbolItem.ts_code,
      name: symbolItem.symbol,
      description: symbolItem.area + symbolItem.name,
      type: 'stock',
      session: '24x7',
      timezone: 'Etc/UTC',
      minmov: 1,
      pricescale: 100,
      has_intraday: false,
      visible_plots_set: 'ohlc',
      has_weekly_and_monthly: false,
      supported_resolutions: configurationData.supported_resolutions,
      volume_precision: 2,
      data_status: 'streaming',
      ts_code: symbolItem.ts_code
    }
    console.log('[resolveSymbol]: Symbol resolved', symbolName)
    onSymbolResolvedCallback(symbolInfo)
  },

  getBars: async (
    symbolInfo,
    resolution,
    rangeStartDate,
    rangeEndDate,
    onHistoryCallback,
    onErrorCallback,
    firstDataRequest
  ) => {
    //版本原因修改getBars
    // getBars: async (symbolInfo, resolution, periodParams, onHistoryCallback, onErrorCallback) => {
    const from = rangeStartDate
    const to = rangeEndDate
    // const { from, to, firstDataRequest } = periodParams
    console.log('[getBars]: Method call', symbolInfo, resolution, from, to)

    const urlParameters = {
      ts_code: symbolInfo.ts_code,
      start_date: dayjs(from * 1000).format('YYYYMMDD'),
      end_date: dayjs(to * 1000).format('YYYYMMDD')
    }
    const query = Object.keys(urlParameters)
      .map((name) => `${name}=${encodeURIComponent(urlParameters[name])}`)
      .join('&')
    try {
      const data = await makeApiRequest(`getHistaryData?${query}`)

      if (data.length === 0) {
        onHistoryCallback([], { noData: true })
        return
      }
      let bars = []
      data.forEach((bar) => {
        if (
          (dayjs(bar.trade_date).unix() >= from && dayjs(bar.trade_date).unix() < to) ||
          firstDataRequest
        ) {
          bars = [
            ...bars,
            {
              time: dayjs(bar.trade_date).unix() * 1000,
              low: bar.low,
              high: bar.high,
              open: bar.open,
              close: bar.close
            }
          ]
        }
      })

      console.log(`[getBars]: returned ${bars.length} bar(s)`)
      onHistoryCallback(bars, { noData: false })
    } catch (error) {
      console.log('[getBars]: Get error', error)
      onErrorCallback(error)
    }
  },

  subscribeBars: (
    symbolInfo,
    resolution,
    onRealtimeCallback,
    subscriberUID,
    onResetCacheNeededCallback
  ) => {
    console.log('[subscribeBars]: Method call with subscriberUID:', subscriberUID)
  },
  unsubscribeBars: (subscriberUID) => {
    console.log('[unsubscribeBars]: Method call with subscriberUID:', subscriberUID)
  }
}


```

这里后端用python + 开源的数据写的,具体启动查看源码：

具体源码[链接]https://github.com/Tianyi-miku/tradingview-demo