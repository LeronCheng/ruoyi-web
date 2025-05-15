<script setup lang="ts">
import { defineAsyncComponent, computed, ref, watch, onMounted } from 'vue'
import { NButton, NInput, NTabs, NTabPane, NRadioGroup, NRadio, NRadioButton } from 'naive-ui'
// import Avatar from './Avatar.vue'

const props = defineProps<{
  loading: boolean
  genText: string
}>()

const emit = defineEmits<{
  (e: 'generate', text: string, model: string, color: string): void
  (e: 'render', text: string): void
  (e: 'update:genText', text: string): void
}>()

const Avatar = defineAsyncComponent(() => import('./Avatar.vue'))

const loading = computed(() => {
  return props.loading
})

const text = ref('')
const gen = ref('')
const selectedModel = ref('claude')
const selectedColor = ref('蓝色')
const showHistory = ref(false)

const colorOptions = [
  { label: '红色', value: '红色', bg: '#ffebee', dot: '#f44336' },
  { label: '橙色', value: '橙色', bg: '#fff3e0', dot: '#ff9800' },
  { label: '黄色', value: '黄色', bg: '#fffde7', dot: '#ffeb3b' },
  { label: '绿色', value: '绿色', bg: '#e8f5e9', dot: '#4caf50' },
  { label: '青色', value: '青色', bg: '#e0f7fa', dot: '#00bcd4' },
  { label: '蓝色', value: '蓝色', bg: '#e3f2fd', dot: '#2196f3' },
  { label: '紫色', value: '紫色', bg: '#f3e5f5', dot: '#9c27b0' }
]

const options = [
  {
    prompt: '番茄炒蛋怎么做',
    pptValue:
      '<svg width="1280" height="720" viewBox="0 0 1280 720" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"><rect width="1280" height="720" fill="#003366"/><!-- Title --><text x="640" y="100" font-family="Arial" font-size="48" font-weight="bold" fill="#FFFFFF" text-anchor="middle">经典家常菜:番茄炒蛋</text><text x="640" y="150" font-family="Arial" font-size="24" fill="#FFFFFF" text-anchor="middle">简单易学的高营养家常菜制作指南</text><rect x="80" y="200" width="1120" height="400" rx="10" ry="10" fill="#FFFFFF" fill-opacity="0.9"/><rect x="120" y="240" width="300" height="340" rx="5" ry="5" fill="#F5F5F5"/> <text x="270" y="270" font-family="Arial" font-size="24" font-weight="bold" fill="#003366" text-anchor="middle">食材准备</text><circle cx="150" cy="310" r="8" fill="#FF6B6B"/><text x="170" y="315" font-family="Arial" font-size="18" fill="#333333">番茄 2-3个 (约300g)</text><circle cx="150" cy="350" r="8" fill="#FFD166"/><text x="170" y="355" font-family="Arial" font-size="18" fill="#333333">鸡蛋 3-4个</text><circle cx="150" cy="390" r="8" fill="#06D6A0"/><text x="170" y="395" font-family="Arial" font-size="18" fill="#333333">葱花 适量</text><circle cx="150" cy="430" r="8" fill="#118AB2"/><text x="170" y="435" font-family="Arial" font-size="18" fill="#333333">盐 1/2茶匙</text><circle cx="150" cy="470" r="8" fill="#073B4C"/><text x="170" y="475" font-family="Arial" font-size="18" fill="#333333">糖 1/4茶匙(可选)</text><circle cx="150" cy="510" r="8" fill="#EF476F"/><text x="170" y="515" font-family="Arial" font-size="18" fill="#333333">食用油 2汤匙</text><rect x="500" y="240" width="660" height="340" rx="5" ry="5" fill="#F5F5F5"/><text x="780" y="270" font-family="Arial" font-size="24" font-weight="bold" fill="#003366" text-anchor="middle">制作步骤</text><rect x="520" y="300" width="40" height="40" rx="20" ry="20" fill="#003366"/> <text x="540" y="325" font-family="Arial" font-size="18" font-weight="bold" fill="#FFFFFF" text-anchor="middle">1</text><text x="580" y="325" font-family="Arial" font-size="18" fill="#333333">番茄切块，鸡蛋打散加少许盐</text><rect x="520" y="360" width="40" height="40" rx="20" ry="20" fill="#003366"/><text x="540" y="385" font-family="Arial" font-size="18" font-weight="bold" fill="#FFFFFF" text-anchor="middle">2</text><text x="580" y="385" font-family="Arial" font-size="18" fill="#333333">热锅凉油，倒入蛋液炒至凝固盛出</text><rect x="520" y="420" width="40" height="40" rx="20" ry="20" fill="#003366"/><text x="540" y="445" font-family="Arial" font-size="18" font-weight="bold" fill="#FFFFFF" text-anchor="middle">3</text><text x="580" y="445" font-family="Arial" font-size="18" fill="#333333">重新热油，爆香葱花，下番茄翻炒</text><rect x="520" y="480" width="40" height="40" rx="20" ry="20" fill="#003366"/><text x="540" y="505" font-family="Arial" font-size="18" font-weight="bold" fill="#FFFFFF" text-anchor="middle">4</text><text x="580" y="505" font-family="Arial" font-size="18" fill="#333333">番茄变软后加入盐、糖调味</text><rect x="520" y="540" width="40" height="40" rx="20" ry="20" fill="#003366"/><text x="540" y="565" font-family="Arial" font-size="18" font-weight="bold" fill="#FFFFFF" text-anchor="middle">5</text><text x="580" y="565" font-family="Arial" font-size="18" fill="#333333">倒入炒好的鸡蛋，翻炒均匀即可出锅</text><text x="640" y="680" font-family="Arial" font-size="16" fill="#FFFFFF" text-anchor="middle">烹饪时间: 约10分钟 | 难度: ★☆☆☆☆ | 适合人群: 所有年龄段</text></svg>',
    creatTime: '2025-05-15 16:19:11'
  },
  {
    prompt: '番茄炒蛋怎么种植',
    pptValue:
      '<svg width="1280" height="720" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"><!-- Background --><rect width="1280" height="720" fill="white"/><!-- Header Bar --><rect width="1280" height="80" fill="#003366"/><text x="50" y="50" font-family="Arial" font-size="32" font-weight="bold" fill="white">番茄高效种植指南</text><!-- Main Content Sections --><g><!-- Section 1: 选种准备 --><rect x="50" y="120" width="340" height="250" fill="#f8f8f8" rx="5" ry="5" stroke="#003366" stroke-width="2"/><text x="70" y="150" font-family="Arial" font-size="22" font-weight="bold" fill="#003366">1. 选种准备</text><text x="80" y="185" font-family="Arial" font-size="18" fill="#333">• 选择适合当地气候的品种</text><text x="80" y="215" font-family="Arial" font-size="18" fill="#333">• 优质种子发芽率在85%以上</text><text x="80" y="245" font-family="Arial" font-size="18" fill="#333">• 提前20-30天育苗</text><text x="80" y="275" font-family="Arial" font-size="18" fill="#333">• 温度控制在22-26°C</text><text x="80" y="305" font-family="Arial" font-size="18" fill="#333">• 育苗基质pH值5.5-6.5</text><!-- Icon for Section 1 --><circle cx="340" cy="150" r="25" fill="#003366"/><text x="340" y="158" font-family="Arial" font-size="22" font-weight="bold" fill="white" text-anchor="middle">1</text></g><g><!-- Section 2: 土壤条件 --><rect x="470" y="120" width="340" height="250" fill="#f8f8f8" rx="5" ry="5" stroke="#003366" stroke-width="2"/><text x="490" y="150" font-family="Arial" font-size="22" font-weight="bold" fill="#003366">2. 土壤条件</text><text x="500" y="185" font-family="Arial" font-size="18" fill="#333">• 疏松肥沃、排水良好</text><text x="500" y="215" font-family="Arial" font-size="18" fill="#333">• 每亩施有机肥2000-3000kg</text><text x="500" y="245" font-family="Arial" font-size="18" fill="#333">• 土壤pH值5.5-7.0</text><text x="500" y="275" font-family="Arial" font-size="18" fill="#333">• 避免连作，轮作间隔2-3年</text><text x="500" y="305" font-family="Arial" font-size="18" fill="#333">• 建议进行土壤消毒</text><!-- Icon for Section 2 --><circle cx="760" cy="150" r="25" fill="#003366"/><text x="760" y="158" font-family="Arial" font-size="22" font-weight="bold" fill="white" text-anchor="middle">2</text></g><g><!-- Section 3: 种植管理 --><rect x="890" y="120" width="340" height="250" fill="#f8f8f8" rx="5" ry="5" stroke="#003366" stroke-width="2"/><text x="910" y="150" font-family="Arial" font-size="22" font-weight="bold" fill="#003366">3. 种植管理</text><text x="920" y="185" font-family="Arial" font-size="18" fill="#333">• 株距40-50cm，行距70-80cm</text><text x="920" y="215" font-family="Arial" font-size="18" fill="#333">• 遮阳度30-50%（夏季种植）</text><text x="920" y="245" font-family="Arial" font-size="18" fill="#333">• 定期摘除侧芽</text><text x="920" y="275" font-family="Arial" font-size="18" fill="#333">• 主蔓绑缚或支架引导生长</text><text x="920" y="305" font-family="Arial" font-size="18" fill="#333">• 控制植株高度1.5-2米</text><!-- Icon for Section 3 --><circle cx="1180" cy="150" r="25" fill="#003366"/><text x="1180" y="158" font-family="Arial" font-size="22" font-weight="bold" fill="white" text-anchor="middle">3</text></g><g><!-- Section 4: 水肥管理 --><rect x="50" y="400" width="520" height="250" fill="#f8f8f8" rx="5" ry="5" stroke="#003366" stroke-width="2"/><text x="70" y="430" font-family="Arial" font-size="22" font-weight="bold" fill="#003366">4. 水肥管理</text><text x="80" y="465" font-family="Arial" font-size="18" fill="#333">• 保持土壤湿润但避免积水</text><text x="80" y="495" font-family="Arial" font-size="18" fill="#333">• 采用滴灌技术节约用水增产20%</text><text x="80" y="525" font-family="Arial" font-size="18" fill="#333">• 结果期追施钾肥提高果实品质</text><text x="80" y="555" font-family="Arial" font-size="18" fill="#333">• 生长期氮磷钾比例为1:0.5:1.2</text><text x="80" y="585" font-family="Arial" font-size="18" fill="#333">• 分次施肥：定植后、开花前、结果期</text><!-- Icon for Section 4 --><circle cx="325" cy="430" r="25" fill="#003366"/><text x="325" y="438" font-family="Arial" font-size="22" font-weight="bold" fill="white" text-anchor="middle">4</text><!-- Water-Fertilizer Chart --><rect x="340" y="460" width="200" height="150" fill="white" stroke="#003366" stroke-width="1"/><line x1="340" y1="535" x2="540" y2="535" stroke="#003366" stroke-width="1"/><line x1="340" y1="610" x2="540" y2="610" stroke="#003366" stroke-width="1"/><line x1="340" y1="460" x2="340" y2="610" stroke="#003366" stroke-width="1"/><!-- Chart Bars --><rect x="360" y="475" width="30" height="60" fill="#003366"/><rect x="410" y="495" width="30" height="40" fill="#4682B4"/><rect x="460" y="485" width="30" height="50" fill="#5CACEE"/><rect x="360" y="550" width="30" height="45" fill="#003366"/><rect x="410" y="565" width="30" height="30" fill="#4682B4"/><rect x="460" y="550" width="30" height="45" fill="#5CACEE"/><text x="375" y="470" font-family="Arial" font-size="12" fill="#333">苗期</text><text x="425" y="470" font-family="Arial" font-size="12" fill="#333">花期</text><text x="475" y="470" font-family="Arial" font-size="12" fill="#333">果期</text><text x="325" y="535" font-family="Arial" font-size="12" fill="#333">水分</text><text x="325" y="580" font-family="Arial" font-size="12" fill="#333">肥料</text></g><g><!-- Section 5: 病虫害防治 --><rect x="630" y="400" width="600" height="250" fill="#f8f8f8" rx="5" ry="5" stroke="#003366" stroke-width="2"/><text x="650" y="430" font-family="Arial" font-size="22" font-weight="bold" fill="#003366">5. 病虫害防治</text><!-- Risk Table --><rect x="650" y="450" width="550" height="180" fill="white" stroke="#003366" stroke-width="1"/><!-- Table Headers --><rect x="650" y="450" width="170" height="40" fill="#003366"/><rect x="820" y="450" width="170" height="40" fill="#003366"/><rect x="990" y="450" width="210" height="40" fill="#003366"/><text x="735" y="475" font-family="Arial" font-size="16" font-weight="bold" fill="white" text-anchor="middle">常见问题</text><text x="905" y="475" font-family="Arial" font-size="16" font-weight="bold" fill="white" text-anchor="middle">风险等级</text><text x="1095" y="475" font-family="Arial" font-size="16" font-weight="bold" fill="white" text-anchor="middle">防治措施</text><!-- Row 1 --><rect x="650" y="490" width="170" height="35" fill="white" stroke="#003366" stroke-width="1"/><rect x="820" y="490" width="170" height="35" fill="white" stroke="#003366" stroke-width="1"/><rect x="990" y="490" width="210" height="35" fill="white" stroke="#003366" stroke-width="1"/><text x="735" y="512" font-family="Arial" font-size="14" fill="#333" text-anchor="middle">晚疫病</text><rect x="860" y="500" width="90" height="15" fill="#FF0000" rx="5" ry="5"/><text x="1095" y="512" font-family="Arial" font-size="14" fill="#333" text-anchor="middle">避免湿度，喷洒杀菌剂</text><!-- Row 2 --><rect x="650" y="525" width="170" height="35" fill="white" stroke="#003366" stroke-width="1"/><rect x="820" y="525" width="170" height="35" fill="white" stroke="#003366" stroke-width="1"/><rect x="990" y="525" width="210" height="35" fill="white" stroke="#003366" stroke-width="1"/><text x="735" y="547" font-family="Arial" font-size="14" fill="#333" text-anchor="middle">粉虱</text><rect x="860" y="535" width="90" height="15" fill="#FFCC00" rx="5" ry="5"/><text x="1095" y="547" font-family="Arial" font-size="14" fill="#333" text-anchor="middle">黄板诱捕，生物农药</text><!-- Row 3 --><rect x="650" y="560" width="170" height="35" fill="white" stroke="#003366" stroke-width="1"/><rect x="820" y="560" width="170" height="35" fill="white" stroke="#003366" stroke-width="1"/><rect x="990" y="560" width="210" height="35" fill="white" stroke="#003366" stroke-width="1"/><text x="735" y="582" font-family="Arial" font-size="14" fill="#333" text-anchor="middle">叶霉病</text><rect x="860" y="570" width="90" height="15" fill="#FF0000" rx="5" ry="5"/><text x="1095" y="582" font-family="Arial" font-size="14" fill="#333" text-anchor="middle">通风，杀菌剂预防喷洒</text><!-- Row 4 --><rect x="650" y="595" width="170" height="35" fill="white" stroke="#003366" stroke-width="1"/><rect x="820" y="595" width="170" height="35" fill="white" stroke="#003366" stroke-width="1"/><rect x="990" y="595" width="210" height="35" fill="white" stroke="#003366" stroke-width="1"/><text x="735" y="617" font-family="Arial" font-size="14" fill="#333" text-anchor="middle">根结线虫</text><rect x="860" y="605" width="90" height="15" fill="#00CC00" rx="5" ry="5"/><text x="1095" y="617" font-family="Arial" font-size="14" fill="#333" text-anchor="middle">轮作，土壤消毒</text><!-- Legend for Risk Levels --><rect x="1005" y="430" width="15" height="15" fill="#FF0000" rx="2" ry="2"/><text x="1025" y="443" font-family="Arial" font-size="12" fill="#333">高风险</text><rect x="1075" y="430" width="15" height="15" fill="#FFCC00" rx="2" ry="2"/><text x="1095" y="443" font-family="Arial" font-size="12" fill="#333">中风险</text><rect x="1145" y="430" width="15" height="15" fill="#00CC00" rx="2" ry="2"/><text x="1165" y="443" font-family="Arial" font-size="12" fill="#333">低风险</text></g><!-- Footer --><rect x="0" y="680" width="1280" height="40" fill="#003366"/><text x="640" y="705" font-family="Arial" font-size="14" fill="white" text-anchor="middle">番茄种植技术指南 - 基于农业专家建议整理</text></svg>',
    creatTime: '2025-05-15 16:30:19'
  }
]

watch(
  () => props.genText,
  val => {
    gen.value = val
  },
  { immediate: true }
)

function onReRender() {
  emit('render', gen.value)
}

function onCase() {
  text.value = `番茄炒蛋怎么做`
  gen.value = `
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1280 720">
  <!-- 背景 -->
  <rect width="1280" height="720" fill="#ffffff"/>
  
  <!-- 顶部标题栏 -->
  <rect x="0" y="0" width="1280" height="80" fill="#003366"/>
  <text x="60" y="50" font-family="Arial, sans-serif" font-size="32" font-weight="bold" fill="#ffffff">下周重点任务</text>
  
  <!-- 内容区域 -->
  <g>
    <!-- 时间轴 -->
    <line x1="150" y1="200" x2="1130" y2="200" stroke="#dddddd" stroke-width="4"/>
    
    <!-- 时间节点1 -->
    <circle cx="250" cy="200" r="20" fill="#0066cc"/>
    <text x="250" y="170" font-family="Arial, sans-serif" font-size="18" text-anchor="middle" fill="#333333">3月4日前</text>
    <rect x="150" y="230" width="200" height="100" rx="5" ry="5" fill="#e6f2ff" stroke="#0066cc" stroke-width="2"/>
    <text x="250" y="270" font-family="Arial, sans-serif" font-size="20" font-weight="bold" text-anchor="middle" fill="#333333">V8盖板</text>
    <text x="250" y="300" font-family="Arial, sans-serif" font-size="18" text-anchor="middle" fill="#333333">专项改善会议</text>
    
    <!-- 时间节点2 -->
    <circle cx="600" cy="200" r="20" fill="#0066cc"/>
    <text x="600" y="170" font-family="Arial, sans-serif" font-size="18" text-anchor="middle" fill="#333333">本周内</text>
    <rect x="500" y="230" width="200" height="100" rx="5" ry="5" fill="#e6f2ff" stroke="#0066cc" stroke-width="2"/>
    <text x="600" y="270" font-family="Arial, sans-serif" font-size="20" font-weight="bold" text-anchor="middle" fill="#333333">负极盖板</text>
    <text x="600" y="300" font-family="Arial, sans-serif" font-size="18" text-anchor="middle" fill="#333333">紧急采购方案</text>
    
    <!-- 时间节点3 -->
    <circle cx="950" cy="200" r="20" fill="#0066cc"/>
    <text x="950" y="170" font-family="Arial, sans-serif" font-size="18" text-anchor="middle" fill="#333333">本周内</text>
    <rect x="850" y="230" width="200" height="100" rx="5" ry="5" fill="#e6f2ff" stroke="#0066cc" stroke-width="2"/>
    <text x="950" y="270" font-family="Arial, sans-serif" font-size="20" font-weight="bold" text-anchor="middle" fill="#333333">设备扫码系统</text>
    <text x="950" y="300" font-family="Arial, sans-serif" font-size="18" text-anchor="middle" fill="#333333">升级验证</text>
  </g>
  
  <!-- 底部KPI指标区域 -->
  <g>
    <rect x="150" y="400" width="300" height="180" rx="5" ry="5" fill="#f5f5f5" stroke="#dddddd" stroke-width="2"/>
    <text x="300" y="440" font-family="Arial, sans-serif" font-size="22" font-weight="bold" text-anchor="middle" fill="#003366">V8盖板改善</text>
    <rect x="180" y="460" width="240" height="30" rx="3" ry="3" fill="#e6f2ff"/>
    <text x="190" y="480" font-family="Arial, sans-serif" font-size="16" fill="#333333">• 召开专项会议</text>
    <rect x="180" y="500" width="240" height="30" rx="3" ry="3" fill="#e6f2ff"/>
    <text x="190" y="520" font-family="Arial, sans-serif" font-size="16" fill="#333333">• 确定改善方案</text>
    <circle cx="170" cy="430" r="15" fill="#ff9900"/>
    <text x="170" y="435" font-family="Arial, sans-serif" font-size="16" font-weight="bold" text-anchor="middle" fill="#ffffff">!</text>
    
    <rect x="490" y="400" width="300" height="180" rx="5" ry="5" fill="#f5f5f5" stroke="#dddddd" stroke-width="2"/>
    <text x="640" y="440" font-family="Arial, sans-serif" font-size="22" font-weight="bold" text-anchor="middle" fill="#003366">负极盖板采购</text>
    <rect x="520" y="460" width="240" height="30" rx="3" ry="3" fill="#e6f2ff"/>
    <text x="530" y="480" font-family="Arial, sans-serif" font-size="16" fill="#333333">• 确定供应商</text>
    <rect x="520" y="500" width="240" height="30" rx="3" ry="3" fill="#e6f2ff"/>
    <text x="530" y="520" font-family="Arial, sans-serif" font-size="16" fill="#333333">• 落实采购方案</text>
    <circle cx="510" cy="430" r="15" fill="#cc0000"/>
    <text x="510" y="435" font-family="Arial, sans-serif" font-size="16" font-weight="bold" text-anchor="middle" fill="#ffffff">!</text>
    
    <rect x="830" y="400" width="300" height="180" rx="5" ry="5" fill="#f5f5f5" stroke="#dddddd" stroke-width="2"/>
    <text x="980" y="440" font-family="Arial, sans-serif" font-size="22" font-weight="bold" text-anchor="middle" fill="#003366">设备扫码系统</text>
    <rect x="860" y="460" width="240" height="30" rx="3" ry="3" fill="#e6f2ff"/>
    <text x="870" y="480" font-family="Arial, sans-serif" font-size="16" fill="#333333">• 系统升级部署</text>
    <rect x="860" y="500" width="240" height="30" rx="3" ry="3" fill="#e6f2ff"/>
    <text x="870" y="520" font-family="Arial, sans-serif" font-size="16" fill="#333333">• 验证系统功能</text>
    <circle cx="850" cy="430" r="15" fill="#009933"/>
    <text x="850" y="435" font-family="Arial, sans-serif" font-size="16" font-weight="bold" text-anchor="middle" fill="#ffffff">✓</text>
  </g>
  
  <!-- 底部栏 -->
  <rect x="0" y="680" width="1280" height="40" fill="#f5f5f5"/>
  <text x="1220" y="705" font-family="Arial, sans-serif" font-size="14" text-anchor="end" fill="#666666">制作日期: 2025年4月1日</text>
</svg>
`
  emit('render', gen.value)
}

function handleSelect(option: any) {
  text.value = option.prompt
  gen.value = option.pptValue
  emit('render', gen.value)
  showHistory.value = false
}

function onGenerate() {
  if (!text.value || text.value.trim() === '') {
    return
  }
  // 先清空生成的内容
  emit('update:genText', '')
  // 然后生成新内容
  emit('generate', text.value, selectedModel.value, selectedColor.value)
}

function onRender() {
  if (!gen.value || gen.value.trim() === '') {
    return
  }
  emit('render', gen.value)
}

// 在组件挂载时确保 gen.value 正确初始化
onMounted(() => {
  // 如果 gen 为空但 props.genText 有值，同步数据
  if ((!gen.value || gen.value.trim() === '') && props.genText) {
    gen.value = props.genText
  }
})
</script>

<template>
  <div class="w-[65%] border-r p-4 pt-2">
    <div class="flex space-x-2 items-center">
      <div style="width: 100%;">
        <div class="mb-4" style="display: flex;justify-content:space-between;align-items: center">
          <div>
            <span class="mr-2">选择模型：</span>
            <NRadioGroup v-model:value="selectedModel" name="model-type" class="flex gap-4">
              <NRadioButton value="claude">
                <div class="flex items-center">
                  <Avatar name="claude" :image-size="20" class="mr-2" />
                  <span>Claude</span>
                </div>
              </NRadioButton>
              <NRadioButton value="deepseek">
                <div class="flex items-center">
                  <Avatar name="deepseek" :image-size="20" class="mr-2" />
                  <span>Deepseek</span>
                </div>
              </NRadioButton>
            </NRadioGroup>
          </div>
          <div class="relative">
            <NButton @click="showHistory = !showHistory">历史记录</NButton>
            <div v-if="showHistory" class="absolute right-0 top-full mt-1 bg-white shadow-lg rounded-md p-2 z-10 w-64 max-h-80 overflow-y-auto">
              <ul class="divide-y divide-gray-200">
                <li v-for="(item, index) in options" :key="index" class="py-2 px-1 cursor-pointer hover:bg-gray-100" @click="handleSelect(item)">
                  <div class="font-medium text-gray-800 truncate">{{ item.prompt }}</div>
                  <div class="text-sm text-gray-500">{{ item.creatTime }}</div>
                </li>
              </ul>
            </div>
          </div>
        </div>
        <div class="mb-4">
          <span class="mr-2">选择颜色：</span>
          <NRadioGroup v-model:value="selectedColor" name="color-type" class="flex gap-2 flex-wrap">
            <NRadioButton v-for="color in colorOptions" :key="color.value" :value="color.value" class="!min-w-0 !w-auto px-3">
              <div class="flex items-center gap-1">
                <div class="w-3 h-3 rounded-full" :style="{ backgroundColor: color.dot }"></div>
                <span>{{ color.label }}</span>
              </div>
            </NRadioButton>
          </NRadioGroup>
        </div>
      </div>
    </div>

    <div class="py-2 flex items-center justify-between gap-1">
      <div class="text-nowrap">内容描述</div>
    </div>
    <NInput v-model:value="text" type="textarea" :autosize="{ minRows: 8, maxRows: 20 }" placeholder="请输入内容" class="mb-4" />
    <div class="mt-2 mb-2">
      <NButton :loading="loading" block secondary type="primary" @click="onGenerate">
        <template #icon>
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" class="text-lg">
            <path fill="currentColor" d="M20.5 11H19V7c0-1.1-.9-2-2-2h-4V3.5C13 2.12 11.88 1 10.5 1S8 2.12 8 3.5V5H4c-1.1 0-1.99.9-1.99 2v3.8H3.5c1.49 0 2.7 1.21 2.7 2.7s-1.21 2.7-2.7 2.7H2V20c0 1.1.9 2 2 2h3.8v-1.5c0-1.49 1.21-2.7 2.7-2.7s2.7 1.21 2.7 2.7V22H17c1.1 0 2-.9 2-2v-4h1.5c1.38 0 2.5-1.12 2.5-2.5S21.88 11 20.5 11z" />
          </svg>
        </template>生成PPT
      </NButton>
    </div>

    <div class="mt-6">
      <div class="flex flex-wrap justify-between items-center mb-2">
        <div class="flex items-center gap-1">
          <span>输出内容</span>
          <NButton text type="primary" @click="onCase">示例</NButton>
        </div>
        <NButton secondary size="tiny" type="primary" @click="onReRender">重新渲染</NButton>
      </div>
    </div>
    <NInput v-model:value="gen" placeholder="生成的HTML格式的PPT内容" :rows="16" type="textarea" />
  </div>
</template>

<style scoped></style> 