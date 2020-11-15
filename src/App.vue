<template>
    <form
        v-on:submit.prevent="getGifs()"
        class="field-container"
        :style="datas.length == 0 ? 'margin-top: 50px;' : ''"
    >
        <img
            :style="
                datas.length == 0
                    ? 'width: 200px;'
                    : 'width: 70px; position:fixed; top: 0px; margin-left: -200px;'
            "
            src="./assets/gif.png"
            alt=""
            style="margin-bottom: -100px; transition: 0.15 ease"
        />
        <h1
            :style="datas.length == 0 ? '' : 'display:none;'"
            class="header"
            style="margin-bottom: -20px"
        >
            Search GIFs
        </h1>
        
        <dir class="input-container">
            <input
                type="text"
                v-model="q"
                placeholder="Type here to Search GIF"
                class="field"
                :style="q != '' ? 'width: 250px;' : ''"
            />
            <h1 :style="
                datas.length == 0
                    ? 'display: none;'
                    : 'width: 70px; position:fixed; top: 0; right: 0; cursor: pointer;'
            " @click="q = ''; datas = []; total = 0; offset = 0;"><i class='bx bx-window-close' ></i></h1>
        </dir>
    </form>
    <div class="images">
        <div class="image" v-for="data in datas" :key="data.id">
            <img
                class="img"
                :data-clipboard-text="data.images.downsized.url"
                :src="data.images.downsized.url"
                :alt="data.title"
            />
        </div>
    </div>
    <button v-if="total > 50" class="load-more" @click="loadmore()">Load More...</button>
</template>

<script>
import ClipboardJS from 'clipboard';
export default {
    name: 'App',
    data: () => {
        return {
            q: '',
            datas: [],
            total: 0,
            offset: 0,
        };
    },
    mounted() {
        let clipboard = new ClipboardJS('.img');

        clipboard.on('success', function (e) {
            
            e.clearSelection();
        });
    },
    methods: {
        getGifs() {
            this.axios
                .get(
                    `https://api.giphy.com/v1/gifs/search?api_key=yrHgruwkr8SeXdko7PUH62LNrbhWWD0W&q=${this.q}`
                )
                .then((data) => {
                    this.datas = data.data.data;
                    this.total = data.data.pagination.total_count;
                    this.offset = data.data.pagination.count
                })
                .catch((e) => {
                    console.log(e);
                });
        },
        loadmore() {
            this.axios
                .get(
                    `https://api.giphy.com/v1/gifs/search?api_key=yrHgruwkr8SeXdko7PUH62LNrbhWWD0W&q=${this.q}&offset=${this.offset}`
                )
                .then((data) => {
                    
                    this.datas.push(...data.data.data)
                    this.offset = this.offset + data.data.pagination.count
                    console.log(this.offset)
                })
                .catch((e) => {
                    console.log(e);
                });
        }
    },
};
</script>
<style lang="scss" src="./app.scss"></style>
