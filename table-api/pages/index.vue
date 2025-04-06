<script setup lang="ts">
import { h, resolveComponent } from 'vue'
import { getPaginationRowModel } from '@tanstack/vue-table'
import type { TableColumn } from '@nuxt/ui'
import type { Column } from '@tanstack/vue-table'

definePageMeta({
  title: 'Products List'
})

const UBadge = resolveComponent('UBadge')
const UButton = resolveComponent('UButton')
const UDropdownMenu = resolveComponent('UDropdownMenu')

const table = useTemplateRef('table')

const items = ref([
  { id: 1, name: 'Product A', category: 'Electronics', price: 100, stock: 50 },
  { id: 2, name: 'Product B', category: 'Clothing', price: 50, stock: 100 },
  { id: 3, name: 'Product C', category: 'Electronics', price: 200, stock: 20 },
  { id: 4, name: 'Product D', category: 'Home', price: 80, stock: 30 },
  { id: 5, name: 'Product E', category: 'Clothing', price: 70, stock: 80 },
  { id: 6, name: 'Product F', category: 'Electronics', price: 150, stock: 25 },
  { id: 7, name: 'Product G', category: 'Home', price: 120, stock: 15 },
  { id: 8, name: 'Product H', category: 'Clothing', price: 45, stock: 60 },
  { id: 9, name: 'Product I', category: 'Electronics', price: 250, stock: 10 },
  { id: 10, name: 'Product J', category: 'Home', price: 90, stock: 40 },
  { id: 11, name: 'Product K', category: 'Electronics', price: 180, stock: 35 },
  { id: 12, name: 'Product L', category: 'Home', price: 110, stock: 55 },
]);

// Function to generate column headers with sorting dropdowns
function getHeader(column: Column<any>, label: string) {
  const isSorted = column.getIsSorted()

  return h(
      UDropdownMenu,
      {
        content: {
          align: 'start'
        },
        'aria-label': 'Actions dropdown',
        items: [
          {
            label: 'Asc',
            type: 'checkbox',
            icon: 'i-lucide-arrow-up-narrow-wide',
            checked: isSorted === 'asc',
            onSelect: () => {
              if (isSorted === 'asc') {
                column.clearSorting()
              } else {
                column.toggleSorting(false)
              }
            }
          },
          {
            label: 'Desc',
            icon: 'i-lucide-arrow-down-wide-narrow',
            type: 'checkbox',
            checked: isSorted === 'desc',
            onSelect: () => {
              if (isSorted === 'desc') {
                column.clearSorting()
              } else {
                column.toggleSorting(true)
              }
            }
          }
        ]
      },
      () =>
          h(UButton, {
            color: 'neutral',
            variant: 'ghost',
            label,
            icon: isSorted
                ? isSorted === 'asc'
                    ? 'i-lucide-arrow-up-narrow-wide'
                    : 'i-lucide-arrow-down-wide-narrow'
                : 'i-lucide-arrow-up-down',
            class: '-mx-2.5 data-[state=open]:bg-(--ui-bg-elevated)',
            'aria-label': `Sort by ${isSorted === 'asc' ? 'descending' : 'ascending'}`
          })
  )
}

// Updated columns with enhanced sorting UI
const columns: TableColumn<any>[] = [
  {
    accessorKey: 'name',
    header: ({ column }) => getHeader(column, 'Product Name'),
  },
  {
    accessorKey: 'category',
    header: ({ column }) => getHeader(column, 'Category'),
  },
  {
    accessorKey: 'price',
    header: ({ column }) => getHeader(column, 'Price ($)'),
    cell: ({ row }) => {
      const price = Number.parseFloat(row.getValue('price'))
      return h('div', { class: 'text-right font-medium' }, `$${price}`)
    }
  },
  {
    accessorKey: 'stock',
    header: ({ column }) => getHeader(column, 'Stock'),
  },
];

const pagination = ref({
  pageIndex: 0,
  pageSize: 5
})

const sorting = ref([
  {
    id: 'name',
    desc: false
  }
])
</script>

<template>
  <div class="min-h-screen bg-[#2e2e2e] p-8">
    <div class="max-w-6xl mx-auto">
      <h1 class="text-3xl font-bold mb-6 text-white">Products List</h1>
      <div class="flex flex-col border-(--ui-border-accented)">
        <div class="flex flex-col px-2 py-3 flex-1 w-full">
        <UInput
            :model-value="table?.tableApi?.getColumn('name')?.getFilterValue() as string"
            class="max-w-sm"
            placeholder="Find by name"
            @update:model-value="table?.tableApi?.getColumn('name')?.setFilterValue($event)"
        />
        </div>
        <UTable
            ref="table"
            v-model:pagination="pagination"
            v-model:sorting="sorting"
            :columns="columns"
            :data="items"
            searchable
            search-placeholder="Search products..."
            :pagination-options="{
                getPaginationRowModel: getPaginationRowModel()
            }"
            sort-mode="all"
            class="bg-gray-700 rounded-lg"
        />
      </div>

    </div>
    <div class="flex justify-center border-t border-(--ui-border) pt-4">
      <UPagination
          :default-page="(table?.tableApi?.getState().pagination.pageIndex || 0) + 1"
          :items-per-page="table?.tableApi?.getState().pagination.pageSize"
          :total="table?.tableApi?.getFilteredRowModel().rows.length"
          @update:page="(p) => table?.tableApi?.setPageIndex(p - 1)"
      />
    </div>
  </div>
</template>

<style scoped>
</style>