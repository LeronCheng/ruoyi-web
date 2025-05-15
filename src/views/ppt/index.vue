<script setup lang="ts">
import { defineAsyncComponent, ref, onMounted, watch, computed } from 'vue'
// import LoginTip from '@/views/user/LoginTip.vue'
import { useChat } from '../chat/hooks/useChat'
import { useAuthStore, useAppStore } from '@/store'
import { mlog, subModel } from '@/api'
const { addChat, updateChatSome } = useChat()

const appStore = useAppStore()
const authStore = useAuthStore()
const Sider = defineAsyncComponent(() => import('./components/Sider.vue'))
const MindMap = defineAsyncComponent(() => import('./components/MindMap.vue'))

const st = ref({ uuid: '1003', index: -1, chatType: 0, appId: '' })
const controller = ref<AbortController>()

// In a real app, these would be imported from actual APIs
// import { genMindMap } from '@/api/chat';
// import { isBlank } from '@/utils/is';

const text = ref('')
const genText = ref('')
const loading = ref(false)

const getCurrClass = computed<CSSProperties>(() => {
  if (appStore.theme == 'dark') {
    return {
      backgroundColor: '#232627'
    }
  } else {
    return {
      backgroundColor: '#fff'
    }
  }
})

// 监视 genText 的变化
async function onGenerate(text: string, model: string, color: string) {
  if (!text || text.trim() === '') {
    return
  }

  loading.value = true
  genText.value = '' // 确保在开始生成前清空
  const message = [
    {
      content: text
    }
  ]
  const inputs = {
    style: color,
    model: model,
    input: text
  }
  subModel({
    message,
    model: 'dify-ppt',
    uuid: st.value.uuid, //当前会话
    inputs,
    onMessage: d => {
      mlog('🐞消息', d)
      const str = JSON.parse(d.text)
      const newText = str.data.text
      genText.value = genText.value + newText
      // textRz.value.push(d.text)
    },
    onError: (e: any) => {
      mlog('onError', e)
    },
    // signal: controller.value.signal,
    kid: '',
    chatType: st.value.chatType,
    appId: st.value.appId
  }).finally(() => {
    loading.value = false
  })
  // .then(() => goFinish())
  // .catch(e => {
  //   if (e.message != 'canceled') textRz.value.push('\n' + t('mj.fail') + ':\n```\n' + (e.reason ?? JSON.stringify(e, null, 2)) + '\n```\n')
  //   goFinish()
  // })
  // try {
  //   // Call workflow API to generate sections
  //   const response = await fetch('http://192.168.50.83/v1/workflows/run', {
  //     method: 'POST',
  //     headers: {
  //       'Content-Type': 'application/json',
  //       Authorization: 'Bearer app-Mpvj48k6mIuttevNkNuezvgB'
  //     },
  //     body: JSON.stringify({
  //       inputs: {
  //         style: color,
  //         model: model,
  //         input: text
  //       },
  //       response_mode: 'streaming',
  //       user: 'a83fb08e-af48-444b-8b4b-8ac6e75d6cea'
  //     })
  //   })

  //   if (!response.ok) {
  //     throw new Error('Failed to generate sections')
  //   }

  //   // Handle SSE response
  //   const reader = response.body?.getReader()
  //   const decoder = new TextDecoder()
  //   let accumulatedOutput = ''

  //   if (!reader) {
  //     throw new Error('Failed to get response reader')
  //   }
  //   let isSVG = false
  //   while (true) {
  //     const { done, value } = await reader.read()
  //     if (done) break

  //     const chunk = decoder.decode(value)
  //     const lines = chunk.split('\n')
  //     // let perDatatext = ''
  //     for (const line of lines) {
  //       if (line.startsWith('data: ')) {
  //         const data = line.slice(6)
  //         // if (!perDatatext) {
  //         //   break
  //         // }
  //         try {
  //           // console.log(data)
  //           const parsed = JSON.parse(data)
  //           if (parsed.event === 'text_chunk') {
  //             accumulatedOutput = parsed.data.text

  //             if (!isSVG && accumulatedOutput.includes('<svg')) {
  //               const startIndex = accumulatedOutput.indexOf('<svg')
  //               accumulatedOutput = accumulatedOutput.slice(startIndex)
  //               isSVG = true
  //             }

  //             if (isSVG)
  //               // 只在数据完全获取后更新 genText
  //               genText.value = genText.value + accumulatedOutput
  //           }
  //         } catch (e) {
  //           // console.error('Error parsing SSE data:', e)
  //         }
  //       } else {
  //         console.log('Received:', line)
  //       }
  //     }
  //   }
  // } catch (error) {
  //   console.error('Error parsing SSE data:', e)
  // } finally {
  //   loading.value = false
  // }
}

function onRender(text: string) {
  // 防止 genText 被设置为空字符串
  if (!text || text.trim() === '') {
    return
  }

  genText.value = text
}
</script>

<template>
  <div v-if="!authStore.token" class="w-full h-full">
    <!-- <LoginTip /> -->
  </div>
  <div v-else class="w-full flex h-full" :style="getCurrClass">
    <Sider :genText="genText" :loading="loading" @generate="onGenerate" @render="onRender" />
    <MindMap :genText="genText" :loading="loading" />
  </div>
</template>

<style scoped></style>
