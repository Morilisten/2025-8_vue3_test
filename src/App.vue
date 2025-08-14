<script setup>
import { ref, onMounted, computed } from 'vue'
import heartRed from '@/assets/heartRed.png'
import heartBlack from '@/assets/heartBlack.png'
import albums from '@/assets/albums.json'
import FavList from '@/components/FavList.vue'

// 任務1. 引入FavList組件到App.vue的aside中
// 如果使用pinia
// import { useFavoriteStore } from '@/stores/favorites'
// const favoriteStore = useFavoriteStore()

// 任務2. 顯示專輯資料
// 目前畫面中僅呈現defaultData
// 請將資料替換成`assets/albums.json`中的專輯資料
const albumData = ref([])
const defaultData = {
    id: 1,
    images: 'https://i.scdn.co/image/ab67616d00001e023e59f3e73b99ed248ab7bae2',
    name: 'Day & Night (feat. Jay Park)',
    artists: 'Lee Young Ji',
}

onMounted(() => {
    albumData.value = albums
})

// 任務3:開啟關閉側拉選單(收藏列表)
const asideToggle = ref(false)
const toggleAside = () => {
    asideToggle.value = !asideToggle.value
}

// 任務4.專輯資料可以被input搜尋
const search = ref('')
// 依名稱或歌手過濾
const items = computed(() => {
    const q = search.value.trim().toLowerCase()
    if (!q) return albumData.value
    return albumData.value.filter(
        (a) => (a.name || '').toLowerCase().includes(q) || (a.artists || '').toLowerCase().includes(q)
    )
})

// 任務5.加入我的收藏
// 不限定方式，如果不知道怎麼使用pinia可以用其他方式
const favorites = ref([])
const inFavorites = (album) => favorites.value.some((f) => f.id === album.id)

const toggleFav = (album) => {
    if (inFavorites(album)) {
        // 已收藏 → 取消
        favorites.value = favorites.value.filter((f) => f.id !== album.id)
    } else {
        // 未收藏 → 新增
        favorites.value.push(album)
        // 第一次加入時自動打開側欄
        if (!asideToggle.value) asideToggle.value = true
    }
}
const removeFav = (id) => {
    favorites.value = favorites.value.filter((f) => f.id !== id)
}
</script>

<template>
    <header>
        <div>
            <input type="search" v-model="search" placeholder="搜尋專輯/歌手" />
            <button @click="toggleAside" aria-label="收藏列表">
                <img :src="heartRed" alt="收藏列表" />
            </button>
        </div>
    </header>

    <main>
        <div class="card" v-for="album in items" :key="album.id">
            <img :src="album.images" :alt="album.name" />
            <div class="card_body">
                <h6>{{ album.name }}</h6>
                <p>{{ album.artists }}</p>
            </div>
            <div class="card_footer">
                <button
                    class="favoriteBtn"
                    @click="toggleFav(album)"
                    :aria-label="inFavorites(album) ? '取消收藏' : '收藏專輯'">
                    <img :src="inFavorites(album) ? heartRed : heartBlack" alt="收藏" />
                </button>
            </div>
        </div>
    </main>

    <aside class="fav-drawer" :class="{ open: asideToggle }" v-show="true">
        <FavList :list="favorites" @remove="removeFav" />
    </aside>
</template>

<style lang="scss">
button {
    width: 2rem;
    height: 2rem;
    border-radius: 2rem;
    padding: 0.2rem;
    img {
        width: 100%;
    }
}
</style>
<style lang="scss" scoped>
header {
    position: fixed;
    width: 100%;
    z-index: 2;
    background-color: #f9f9f9;
    > div {
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        width: 100%;
        max-width: 57rem;
        margin: auto;
        padding: 0.5rem;
        box-sizing: border-box;
    }
}
main {
    width: 100%;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: space-evenly;
    gap: 0.5rem;
    max-width: 57rem;
    margin: 3rem auto;
    padding: 0.5rem;
    box-sizing: border-box;
}
aside {
    width: 20rem;
    height: 100vh;
    position: fixed;
    overflow: hidden;
    overflow-y: scroll;
    z-index: 1;
    top: 0;
    right: -20rem;

    background-color: #2a2a2b;
    color: #f9f9f9;

    box-shadow: 0px 2px 3px rgba(0, 0, 0, 0.3);
    transition: right 1s;

    &.open {
        right: 0;
    }
}

.card {
    width: 12rem;
    padding: 0.75rem;
    border-radius: 0.5rem;
    background-color: #1a1a1a;
    color: #f9f9f9;

    &_body {
        height: 3.5rem;
    }
    &_footer {
        text-align: right;
        .favoriteBtn {
            background-color: #f9f9f9;
            filter: sepia(0);
            &:hover {
                filter: sepia(1);
            }
        }
    }
}
</style>
