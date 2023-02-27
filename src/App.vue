<script setup lang="ts">
import { ref } from 'vue'
import { changeDpiDataUrl, dataURItoBlob, downloadFile } from './utils'

let ps = ref<null | HTMLCanvasElement>(null)

let width = ref(14.4)
let height = ref(19.2)
let fileName = ref('avatar')

const onChange = (e: Event | any) => {
  const file: File = e.target?.files[0]

  if(!file) return

  const fr = new FileReader()
  fr.readAsDataURL(file)

  fr.addEventListener('load', (e) => {
    const base64 = e.target?.result

    const img = document.createElement('img')
    img.src = base64 as string
  
    img.addEventListener('load', () => {
      const wPs = ps.value

      const tWidth = width.value
      const tHeight = height.value
    
      wPs!.width = tWidth
      wPs!.height = tHeight
    
      const ctx = wPs?.getContext('2d')
    
      ctx?.drawImage(img, 0, 0, tWidth, tHeight)

      const base64 = changeDpiDataUrl(wPs?.toDataURL('image/jpeg'), 300)

      const blob: any = dataURItoBlob(base64)
      blob!.name = fileName.value.trim() || 'avatar'

      downloadFile(blob)
    })
  })

}
</script>

<template>
  <div class="flex flex-col gap-4 items-center justify-center">
    <div>
      <label>宽:</label>
      <input type="text" v-model.number="width">
      <span>px</span>
    </div>
    <div>
      <label>高:</label>
      <input type="text" v-model.number="height">
      <span>px</span>
    </div>
    <div>
      <label>文件名:</label>
      <input type="text" v-model="fileName">
    </div>
    <canvas ref="ps" width="0" height="0"></canvas>
    <input type="file" @change="onChange">
  </div>
</template>

<style>
</style>
