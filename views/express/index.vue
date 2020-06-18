<template>
    <div class="container">
        <van-search
                v-model="value"
                show-action
                shape="round"
                placeholder="请输入搜索单号"
                @search="onSearch"
        >
            <template #action>
                <div @click="onSearch">搜索</div>
            </template>
        </van-search>

        <van-steps direction="vertical" :active="0">
            <van-step v-for="item in list" :key="item.time">
                <p>{{item.context}}</p>
                <p>{{item.time}}</p>
            </van-step>

        </van-steps>
    </div>

</template>

<script>
    module.exports = {
        data() {
            return {
                value: '',
                list: []
            }
        },
        created() {
            this.getInfo()
        },
        methods: {
            getInfo() {
                return axios.get('https://route.showapi.com/64-19', {
                    params: {
                        nu: this.value,
                        com: 'auto',
                        asd: ''
                    }
                }).then(res => {
                    this.list = res.data
                })
            },
            onSearch(val) {
                this.getInfo()

            }
        }
    }
</script>

<style>

    .container {
        padding-left: 1rem;
        padding-right: 1rem;
        padding-top: calc(1rem + var(--ion-safe-area-top));
    }
</style>
