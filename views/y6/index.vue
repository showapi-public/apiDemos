<template>
    <div class="container">
        <van-form @submit="onSubmit">
            <van-field
                    required
                    v-model="num"
                    name="num"
                    label="手机号"
                    placeholder="输入要查询的手机号"
            ></van-field>

            <div style="margin: 1rem;">
                <van-button round block type="info" native-type="submit"
                            color="linear-gradient(to right, #4bb0ff, #6149f6)">
                    提交
                </van-button>
            </div>
        </van-form>

        <transition name="van-slide-up">
            <div v-show="name" style="text-align: center">{{prov}} {{city}} {{name}}</div>
        </transition>
    </div>

</template>

<script>
    module.exports = {
        data() {
            return {
                num: '',
                prov: '',
                city: '',
                name: '', // 运营商
            }
        },
        methods: {
            getInfo(payload) {
                return axios.get('https://route.showapi.com/6-1', {
                    params: {
                        ...payload
                    }
                }).then(res => {
                    console.log('thenres', res)
                    this.prov = res.prov
                    this.city = res.city
                    this.name = res.name
                }).catch(error => {
                    console.log('catcherror', error)
                    vant.Toast.fail('查询失败')
                })
            },
            onSubmit(values) {
                this.getInfo(values)
            },
        }
    }
</script>

<style>

    .container {
        padding-top: calc(1rem + var(--ion-safe-area-top));
    }


</style>

