<script setup lang="ts" generic="T">
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from './ui/table'
import { Card } from './ui/card'
import { Input } from './ui/input'

interface Column<T> {
  key: keyof T
  title: string
  render?: (value: T[keyof T], row: T) => any
}

interface Props<T> {
  columns: Column<T>[]
  data: T[]
  searchKey?: keyof T
}

defineProps<Props<T>>()
</script>

<template>
  <Card class="p-4">
    <!-- 搜索框 -->
    <div v-if="searchKey" class="mb-4">
      <Input
        placeholder="搜索..."
        class="max-w-sm"
      />
    </div>

    <!-- 表格 -->
    <Table>
      <TableHeader>
        <TableRow>
          <TableHead v-for="col in columns" :key="String(col.key)">
            {{ col.title }}
          </TableHead>
        </TableRow>
      </TableHeader>
      <TableBody>
        <TableRow v-for="(row, idx) in data" :key="idx">
          <TableCell v-for="col in columns" :key="String(col.key)">
            <component
              v-if="col.render"
              :is="col.render(row[col.key], row)"
            />
            <span v-else>{{ row[col.key] }}</span>
          </TableCell>
        </TableRow>
      </TableBody>
    </Table>
  </Card>
</template>
