<template>
    <div class="container">
        <van-form @submit="onSubmit">
            <van-field
                    readonly
                    clickable
                    name="type"
                    :value="type"
                    label="查询方式"
                    @click="showPicker = true"
            ></van-field>
            <van-popup v-model="showPicker" position="bottom">
                <van-picker
                        show-toolbar
                        :columns="columns"
                        @confirm="(value,index)=>{type = value;showPicker = false;}"
                        @cancel="showPicker = false"
                ></van-picker>
            </van-popup>
            <van-field
                    v-show="type === '域名'"
                    required
                    v-model="domain"
                    name="domain"
                    label="域名"
                    placeholder="www.baidu.com"
            ></van-field>

            <van-field
                    v-show="type === 'ip'"
                    v-model="ip"
                    center
                    clearable
                    name="ip"
                    label="ip"
                    placeholder="116.4.201.181"
            >
                <template #button>
                    <van-button size="small" type="primary" native-type="button" @click="getMyIp">当前IP</van-button>
                </template>
            </van-field>

            <div style="margin: 1rem;">
                <van-button round block type="info" native-type="submit"
                            color="linear-gradient(to right, #4bb0ff, #6149f6)">
                    提交
                </van-button>
            </div>
        </van-form>


        <transition name="van-slide-up">
            <div v-show="msg" style="text-align: center;margin:0 1rem">{{msg}}</div>
        </transition>
    </div>

</template>

<script>

    module.exports = {
        data() {
            return {
                columns: ['域名', 'ip'],
                showPicker: false,
                type: 'ip',
                domain: '',
                ip: '',
                msg: '',
            }
        },
        methods: {
            getMyIp() {
                axios.get('https://route.showapi.com/632-1').then(res => {
                    this.ip = res.ip
                })
            },
            getInfo(url, payload) {
                return axios.get(url, {
                    params: {
                        ...payload

                    }
                }).then(res => {
                    const {country, region, city, ip, isp} = res
                    this.msg = `${country} ${region} ${city} ${isp} ${ip} `
                }).catch(error => {
                    vant.Toast.fail(error.remark)
                })
            },
            onSubmit(values) {
                console.log('submit', values)
                if (this.type === '域名') {
                    this.getInfo('https://route.showapi.com/20-2', values)
                }
                if (this.type === 'ip') {
                    this.getInfo('https://route.showapi.com/20-1', values)
                }

            },
        }
    }
</script>

<style>

    .container {
        padding-top: calc(1rem + var(--ion-safe-area-top));
    }

    .list-item {
        background: #edf2f7;
        border-radius: .5rem;
        padding: 1rem .5rem;
        margin-bottom: 1.5rem;
    }

    .list-item-title {
        margin-bottom: .5rem;
        font-weight: 700;
        font-size: 1.25rem;
    }

    .list-item-content {
        color: #969799;
    }

    .list-item-content:not(:last-of-type) {
        margin-bottom: .5rem;
    }

    .list-item-tag {
        border-radius: 9999px;
        background-color: #edf2f7 !important;
        padding: .75rem .25rem;

    }

</style>

