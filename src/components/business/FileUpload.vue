<script setup lang="ts">
import { ref, computed } from 'vue'
import { Upload, X, File, Image, FileText, Film, Music, Archive } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import { Card, CardContent } from '@/components/ui/card'

export interface UploadFile {
  id: string
  file: File
  name: string
  size: number
  type: string
  progress: number
  status: 'uploading' | 'success' | 'error'
  url?: string
}

interface Props {
  modelValue?: UploadFile[]
  accept?: string
  multiple?: boolean
  maxSize?: number // MB
  maxFiles?: number
}

const props = withDefaults(defineProps<Props>(), {
  modelValue: () => [],
  accept: '',
  multiple: true,
  maxSize: 10,
  maxFiles: 10,
})

const emit = defineEmits<{
  (e: 'update:modelValue', files: UploadFile[]): void
  (e: 'upload', file: UploadFile): void
  (e: 'remove', fileId: string): void
}>()

const isDragging = ref(false)
const fileInput = ref<HTMLInputElement | null>(null)

// 文件列表
const files = ref<UploadFile[]>(props.modelValue)

// 文件类型图标映射
const fileTypeIcons = {
  image: Image,
  video: Film,
  audio: Music,
  application: Archive,
  default: File,
}

// 获取文件图标
function getFileIcon(type: string) {
  const mainType = type.split('/')[0]
  return fileTypeIcons[mainType as keyof typeof fileTypeIcons] || fileTypeIcons.default
}

// 格式化文件大小
function formatSize(bytes: number): string {
  if (bytes < 1024) return bytes + ' B'
  if (bytes < 1024 * 1024) return (bytes / 1024).toFixed(1) + ' KB'
  return (bytes / (1024 * 1024)).toFixed(1) + ' MB'
}

// 点击选择文件
function handleClick() {
  fileInput.value?.click()
}

// 文件选择
function handleFileSelect(event: Event) {
  const input = event.target as HTMLInputElement
  if (input.files) {
    handleFiles(Array.from(input.files))
  }
}

// 拖拽开始
function handleDragStart(event: DragEvent) {
  event.preventDefault()
  isDragging.value = true
}

// 拖拽结束
function handleDragEnd(event: DragEvent) {
  event.preventDefault()
  isDragging.value = false
}

// 拖拽进入
function handleDragOver(event: DragEvent) {
  event.preventDefault()
  isDragging.value = true
}

// 拖拽离开
function handleDragLeave(event: DragEvent) {
  event.preventDefault()
  isDragging.value = false
}

// 拖拽放下
function handleDrop(event: DragEvent) {
  event.preventDefault()
  isDragging.value = false
  if (event.dataTransfer?.files) {
    handleFiles(Array.from(event.dataTransfer.files))
  }
}

// 处理文件
function handleFiles(fileList: File[]) {
  const newFiles: UploadFile[] = []

  for (const file of fileList) {
    // 检查文件数量
    if (files.value.length + newFiles.length >= props.maxFiles) {
      alert(`最多只能上传 ${props.maxFiles} 个文件`)
      break
    }

    // 检查文件大小
    if (file.size > props.maxSize * 1024 * 1024) {
      alert(`文件 ${file.name} 超过最大限制 ${props.maxSize} MB`)
      continue
    }

    const uploadFile: UploadFile = {
      id: Date.now().toString() + Math.random().toString(36).substr(2, 9),
      file,
      name: file.name,
      size: file.size,
      type: file.type,
      progress: 0,
      status: 'uploading',
    }

    newFiles.push(uploadFile)
  }

  files.value = [...files.value, ...newFiles]
  emit('update:modelValue', files.value)

  // 模拟上传
  newFiles.forEach(file => {
    emit('upload', file)
    simulateUpload(file.id)
  })
}

// 模拟上传进度
function simulateUpload(fileId: string) {
  const interval = setInterval(() => {
    const file = files.value.find(f => f.id === fileId)
    if (file && file.status === 'uploading') {
      file.progress += 20
      if (file.progress >= 100) {
        file.progress = 100
        file.status = 'success'
        file.url = `https://example.com/files/${file.name}`
        clearInterval(interval)
      }
    }
  }, 500)
}

// 移除文件
function removeFile(fileId: string) {
  files.value = files.value.filter(f => f.id !== fileId)
  emit('update:modelValue', files.value)
  emit('remove', fileId)
}
</script>

<template>
  <Card>
    <CardContent class="p-4">
      <!-- 上传区域 -->
      <div
        class="relative border-2 border-dashed rounded-lg p-8 text-center transition-colors"
        :class="isDragging ? 'border-primary bg-primary/5' : 'border-border hover:border-primary/50'"
        @dragstart="handleDragStart"
        @dragend="handleDragEnd"
        @dragover="handleDragOver"
        @dragleave="handleDragLeave"
        @drop="handleDrop"
      >
        <input
          ref="fileInput"
          type="file"
          :accept="accept"
          :multiple="multiple"
          class="hidden"
          @change="handleFileSelect"
        />

        <div class="space-y-3">
          <Upload class="h-12 w-12 mx-auto text-muted-foreground" />
          <div>
            <p class="text-sm font-medium text-foreground">
              拖拽文件到此处，或
              <button class="text-primary hover:underline" @click="handleClick">
                点击选择文件
              </button>
            </p>
            <p class="text-xs text-muted-foreground mt-1">
              支持 {{ accept || '所有' }} 格式，单个文件最大 {{ maxSize }} MB，最多 {{ maxFiles }} 个文件
            </p>
          </div>
        </div>
      </div>

      <!-- 文件列表 -->
      <div v-if="files.length > 0" class="mt-4 space-y-2">
        <div
          v-for="file in files"
          :key="file.id"
          class="flex items-center gap-3 p-3 border border-border rounded-md bg-muted/30"
        >
          <component
            :is="getFileIcon(file.type)"
            class="h-8 w-8 text-muted-foreground flex-shrink-0"
          />

          <div class="flex-1 min-w-0">
            <div class="flex items-center gap-2">
              <p class="text-sm font-medium text-foreground truncate">
                {{ file.name }}
              </p>
              <span
                v-if="file.status === 'success'"
                class="text-xs text-green-600"
              >
                上传成功
              </span>
              <span
                v-else-if="file.status === 'error'"
                class="text-xs text-red-600"
              >
                上传失败
              </span>
            </div>
            <p class="text-xs text-muted-foreground">
              {{ formatSize(file.size) }}
            </p>
            <div v-if="file.status === 'uploading'" class="mt-1">
              <div class="h-1 rounded-full bg-muted overflow-hidden">
                <div
                  class="h-full bg-primary transition-all duration-300"
                  :style="{ width: `${file.progress}%` }"
                />
              </div>
              <p class="text-xs text-muted-foreground mt-1">
                {{ file.progress }}%
              </p>
            </div>
          </div>

          <Button
            variant="ghost"
            size="sm"
            @click="removeFile(file.id)"
          >
            <X class="h-4 w-4" />
          </Button>
        </div>
      </div>
    </CardContent>
  </Card>
</template>
