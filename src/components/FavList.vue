<script setup>
import { computed } from 'vue'
// 如果使用pinia
// import { useFavoriteStore } from '@/stores/favorites'
// const favoriteStore = useFavoriteStore()

// 任務6. 移除收藏列表，不限定方式
const props = defineProps({
    list: {
        type: Array,
        default: () => [],
    },
})
const emit = defineEmits(['remove'])

const hasData = computed(() => props.list.length > 0)
const removeFav = (item) => {
    emit('remove', item.id) 
}
</script>

<template>
    <div class="favList">
        <div class="nodata" v-if="!hasData">還沒有加入收藏喔</div>

        <!-- 任務7-1. 沒有收藏列表顯示⬆️有收藏列表顯示⬇️-->
        <!-- 任務7-2. 顯示收藏列表-->
        <div class="container" v-else>
            <div class="list" v-for="item in list" :key="item.id">
                <img :src="item.images" :alt="item.name" />
                <div class="list_content">
                    <h6>{{ item.name }}</h6>
                    <p>{{ item.artists }}</p>
                </div>
                <div class="list_action">
                    <button @click="removeFav(item)" aria-label="移除收藏">X</button>
                </div>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.favList {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    gap: 0.5rem;
    .nodata {
        width: 100%;
        height: 10rem;
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .container {
        overflow: hidden;
        padding: 5rem 0.5rem 0.5rem 0.5rem;
    }
    .list {
        display: flex;
        flex-direction: row;
        align-items: center;
        gap: 0.5rem;
        margin-bottom: 0.5rem;
        img {
            width: 4rem;
        }
        &_content {
            flex: 0 1 calc(100% - 7.5rem);
            h6 {
                font-size: 0.8rem;
            }
            p {
                font-size: 0.75rem;
            }
        }
        &_action {
            display: flex;
            flex-direction: row;
            align-items: center;
            width: 2.5rem;
            button {
                background-color: transparent;
                color: rgb(175, 50, 119);
            }
        }
    }
}
</style>
