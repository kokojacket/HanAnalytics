<template>
  <section class="han_analytics">
    <header>
      <div class="main">
        <div class="logo">
          <img src="../public/favicon.ico">
          <span>Han Analytics</span>
        </div>
        <h2>简单优雅的Web分析</h2>
      </div>
    </header>
    <main>
      <header>
        <Alert>
          <AlertDescription>
            <p>· Han Analytics 是一个简单的网络分析跟踪器和仪表板，托管在被称为赛博菩萨的 Cloudflare 上,无成本稳定运行,每天可达10万次免费统计。</p>
            <p>· 域名、服务器、数据库 通通都不用! 托管在 Cloudflare Pages 上即可快速部署网站分析仪表板。</p>
            <p style="font-weight: bold;">· 开源地址: <a class="git-link" href="https://github.com/uxiaohan/HanAnalytics"
                target="_blank">Han-Analytics</a>
            </p>
          </AlertDescription>
        </Alert>
      </header>

      <section class="main">
        <div class="pb-5 grid md:grid-cols-2 sm:grid-cols-1 items-start">
          <div class="flex gap-[16px] pb-6">
            <div class="w-3/6">
              <Select :disabled="siteList.length < 1 || getDatasStatus" v-model="siteValue"
                @update:model-value="siteChangeFn">
                <SelectTrigger class="w-[218px]">
                  <SelectValue placeholder="选择站点" />
                </SelectTrigger>
                <SelectContent>
                  <SelectGroup>
                    <SelectLabel>Web Site</SelectLabel>
                    <SelectItem :value="i" v-for="i in siteList" :key="i">{{ i }}</SelectItem>
                  </SelectGroup>
                </SelectContent>
              </Select>
            </div>
            <div class="w-3/6">
              <Select :disabled="siteList.length < 1 || getDatasStatus" v-model="timeValue"
                @update:model-value="siteChangeFn">
                <SelectTrigger class="w-[218px]">
                  <SelectValue placeholder="选择周期" />
                </SelectTrigger>
                <SelectContent>
                  <SelectGroup>
                    <SelectLabel>Cycle Time</SelectLabel>
                    <SelectItem :value="i.value" v-for="i in timeList" :key="i.name">{{ i.name }}</SelectItem>
                  </SelectGroup>
                </SelectContent>
              </Select>
            </div>
          </div>
          <div
            class="flex justify-end text-center md:text-right line-clamp-1 [&>.views-item]:flex [&>.views-item]:flex-col [&>.views-item]:items-center md:[&>.views-item]:items-end [&>.views-item]:gap-4 [&>.views-item>span]:text-sm [&>.views-item>p]:text-3xl [&>.views-item>p]:line-clamp-1 [&>.views-item>p]:w-full">
            <div class="views-item w-full overflow-hidden">
              <span>Views</span>
              <div class="space-y-2 w-[50%]" v-if="!resData.views">
                <Skeleton class="h-4  w-[50%] ml-auto" />
                <Skeleton class="h-4" />
              </div>
              <p v-else>{{ resData.views }}</p>
            </div>
            <div class="views-item w-full overflow-hidden">
              <span>Visitors</span>
              <div class="space-y-2 w-[50%]" v-if="!resData.visitor">
                <Skeleton class="h-4  w-[50%] ml-auto" />
                <Skeleton class="h-4" />
              </div>
              <p v-else>{{ resData.visitor }}</p>
            </div>
            <div class="views-item w-full overflow-hidden">
              <span>Visits</span>
              <div class="space-y-2 w-[50%]" v-if="!resData.visit">
                <Skeleton class="h-4  w-[50%] ml-auto" />
                <Skeleton class="h-4" />
              </div>
              <p v-else>{{ resData.visit }}</p>
            </div>
          </div>
        </div>

        <div ref="echartsDOM" class="data-view"></div>

        <div class="pt-20 grid md:grid-cols-2 sm:grid-cols-1 gap-[16px]">
          <Card class="box-border flex flex-col w-full h-[460px] overflow-hidden">
            <CardHeader>
              <CardTitle>Pages</CardTitle>
            </CardHeader>
            <CardContent class="box-border pt-0 w-full h-full overflow-hidden">
              <ScrollArea class="box-border p-2 pt-0 h-full w-full pages-list" v-if="resData.path">
                <p class="page-item" v-for="(i, idx) in resData.path" :key="idx">
                  <span class="line-clamp-1">{{ i.name }}</span>
                  <span class="line-clamp-1">{{ i.value }}</span>
                </p>
              </ScrollArea>
              <div class="space-y-4 pt-8 w-full" v-else>
                <Skeleton class="h-6 w-60" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-100" />
                <Skeleton class="h-6 w-60" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-100" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-full" />
              </div>
            </CardContent>
          </Card>

          <Card class="box-border flex flex-col w-full h-[460px] overflow-hidden">
            <CardHeader>
              <CardTitle>Referrers</CardTitle>
            </CardHeader>
            <CardContent class="box-border pt-0 w-full h-full overflow-hidden">
              <ScrollArea class="box-border p-2 pt-0 h-full w-full pages-list" v-if="resData.referrer">
                <p class="page-item" v-for="(i, idx) in resData.referrer" :key="idx">
                  <img v-if="i.name" :src="getIconUrl(i.name)">
                  <a :href="i.name" target="_blank" rel="noopener noreferrer" class="line-clamp-1 cursor-pointer">
                    {{ i.name || '(None)' }}
                  </a>
                  <span class="line-clamp-1">{{ i.value }}</span>
                </p>
              </ScrollArea>
              <div class="space-y-4 pt-8 w-full" v-else>
                <Skeleton class="h-6 w-60" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-100" />
                <Skeleton class="h-6 w-60" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-100" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-full" />
              </div>
            </CardContent>
          </Card>
        </div>

        <div class="pt-6 grid xl:grid-cols-3 gap-[16px] md:grid-cols-2 sm:grid-cols-1">
          <Card class="box-border flex flex-col w-full h-[460px] overflow-hidden">
            <CardHeader>
              <CardTitle>Browsers</CardTitle>
            </CardHeader>
            <CardContent class="box-border pt-0 w-full h-full overflow-hidden">
              <ScrollArea class="box-border p-2 pt-0 h-full w-full pages-list" v-if="resData.soft">
                <p class="page-item" v-for="(i, idx) in resData.soft" :key="idx">
                  <span class="line-clamp-1">{{ i.name }}</span>
                  <span class="line-clamp-1">{{ i.value }}</span>
                </p>
              </ScrollArea>
              <div class="space-y-4 pt-8 w-full" v-else>
                <Skeleton class="h-6 w-60" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-100" />
                <Skeleton class="h-6 w-60" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-100" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-full" />
              </div>
            </CardContent>
          </Card>

          <Card class="box-border flex flex-col w-full h-[460px] overflow-hidden">
            <CardHeader>
              <CardTitle>OS</CardTitle>
            </CardHeader>
            <CardContent class="box-border pt-0 w-full h-full overflow-hidden">
              <ScrollArea class="box-border p-2 pt-0 h-full w-full pages-list" v-if="resData.os">
                <p class="page-item" v-for="(i, idx) in resData.os" :key="idx">
                  <span class="line-clamp-1">{{ i.name }}</span>
                  <span class="line-clamp-1">{{ i.value }}</span>
                </p>
              </ScrollArea>
              <div class="space-y-4 pt-8 w-full" v-else>
                <Skeleton class="h-6 w-60" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-100" />
                <Skeleton class="h-6 w-60" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-100" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-full" />
              </div>
            </CardContent>
          </Card>


          <Card class="box-border flex flex-col w-full h-[460px] overflow-hidden">
            <CardHeader>
              <CardTitle>Areas</CardTitle>
            </CardHeader>
            <CardContent class="box-border pt-0 w-full h-full overflow-hidden">
              <ScrollArea class="box-border p-2 pt-0 h-full w-full pages-list" v-if="resData.area">
                <p class="page-item" v-for="(i, idx) in resData.area" :key="idx">
                  <img :src="getAreaIcon(i.name)">
                  <span class="line-clamp-1">{{ i.code }}</span>
                  <span class="line-clamp-1">{{ i.value }}</span>
                </p>
              </ScrollArea>
              <div class="space-y-4 pt-8 w-full" v-else>
                <Skeleton class="h-6 w-60" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-100" />
                <Skeleton class="h-6 w-60" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-100" />
                <Skeleton class="h-6 w-80" />
                <Skeleton class="h-6 w-full" />
              </div>
            </CardContent>
          </Card>
        </div>
      </section>
    </main>
    <footer>
      <p><img src="./assets/svg/ing.svg"></p>
      <p>
        <a href="https://pages.cloudflare.com" target="_blank" rel="noopener noreferrer"><img
            src="./assets/svg/framework.svg"></a>
        <a href="https://www.cloudflare.com/zh-cn/application-services/products/cdn/" target="_blank"
          rel="noopener noreferrer"><img src="./assets/svg/cdn.svg"></a>
        <a href="https://vuejs.org" target="_blank" rel="noopener noreferrer"><img src="./assets/svg/web.svg"></a>
        <a href="https://api.vvhan.com" target="_blank"><img src="./assets/svg/surppot.svg"></a>
      </p>
    </footer>
  </section>
</template>


<script setup lang="ts">
import { ref, markRaw, onMounted } from 'vue'
import * as echarts from "echarts";
import { Skeleton } from '@/components/ui/skeleton'
import { ScrollArea } from '@/components/ui/scroll-area'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Select, SelectContent, SelectGroup, SelectItem, SelectLabel, SelectTrigger, SelectValue, } from '@/components/ui/select'
import { Alert, AlertDescription } from '@/components/ui/alert'


// 站点列表
const siteList = ref<Array<string>>([])
const siteValue = ref<string>('')
const timeList = [{ name: 'Today', value: 'today' }, { name: 'Last 24 hours', value: '1d' }, { name: 'Last 7 days', value: '7d' }, { name: 'Last 30 days', value: '30d' }, { name: 'Last 90 days', value: '90d' }]
const timeValue = ref<string>('today')
const getSiteList = async () => {
  const res = await fetch('/api', { method: 'POST', headers: { 'Content-Type': 'application/json', }, body: JSON.stringify({ type: 'list' }) })
  const { data } = await res.json()
  siteList.value = data.filter((i: any) => i != '自定义网站唯一标识' && i != 'Hello-HanAnalytics')
  siteValue.value = siteList.value[0]
  if (siteValue.value) getDatas()
}

// 站点切换事件
const siteChangeFn = () => getDatas()

// 获取数据
const resData = ref<any>({})
const getDatasStatus = ref<boolean>(false)
const getDatas = async () => {
  // 清空数据
  resData.value = {}
  // 获取数据
  getDatasStatus.value = true
  const res = await fetch('/api', { method: 'POST', headers: { 'Content-Type': 'application/json', }, body: JSON.stringify({ siteID: siteValue.value, time: timeValue.value }), })
  getDatasStatus.value = false
  const { data } = await res.json()
  resData.value = data
  renderEcharts(data.echarts_data.map((i: any) => `${i.name}${['today', '1d'].includes(timeValue.value) ? '点' : '日'}`), data.echarts_data.map((i: any) => `${i.value}`))
}

// 获取ICON
const getIconUrl = (url: string) => {
  if (!url) return 'https://icons.duckduckgo.com/ip3/none.ico'
  const _url = new URL(url)
  return `https://icons.duckduckgo.com/ip3/${_url.hostname}.ico`
}

// 获取Area ICON
const getAreaIcon = (code: string) => `${location.origin}/icon/${code}.png`

// 渲染图表
const echartsDOM = ref<HTMLCanvasElement>();
const canvasMain = ref<any>();
const renderEcharts = async (dateList: Array<any>, valueList: Array<any>) => {
  const option = {
    grid: { left: "0", right: "0", bottom: "0", top: "10", containLabel: true },
    xAxis: {
      type: "category",
      data: dateList,
      axisLabel: { color: "#959BAA" },
      axisLine: { lineStyle: { color: "rgba(255,255,255,0.56)", width: 2, type: "dashed" } }
    },
    yAxis: { type: "value", axisLabel: { color: "#959BAA" } },
    tooltip: { trigger: "axis" },
    series: [
      {
        data: valueList,
        type: "line",
        smooth: true,
        emphasis: {
          focus: "series",
          itemStyle: { borderWidth: 2 },
          areaStyle: {
            color: {
              colorStops: [
                { offset: 0, color: "#DAE4FF" },
                { offset: 1, color: "#ffffff" }
              ],
              x: 0,
              y: 0,
              x2: 0,
              y2: 1,
              type: "linear",
              global: false
            }
          }
        },
        lineStyle: {
          width: 2,
          color: {
            colorStops: [{ offset: 1, color: "#6F94F1" }],
            x: 0,
            y: 0,
            x2: 1,
            y2: 0,
            type: "linear",
            global: false
          }
        },
        showSymbol: false,
        areaStyle: {
          opacity: 1,
          color: {
            colorStops: [
              { offset: 0, color: "#DAE4FF" },
              { offset: 1, color: "#ffffff" }
            ],
            x: 0,
            y: 0,
            x2: 0,
            y2: 1,
            type: "linear",
            global: false
          }
        }
      }
    ]
  };
  canvasMain.value.setOption(option);
};

onMounted(() => {
  //   图表
  canvasMain.value = markRaw(echarts.init(echartsDOM.value, null, { renderer: "svg", useDirtyRect: true }));
  window.addEventListener("resize", canvasMain.value.resize);
  // 站点列表
  getSiteList()
})
</script>


<style scoped>
@import '@/assets/index.less';
</style>
