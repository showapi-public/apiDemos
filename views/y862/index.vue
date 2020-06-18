<template>
    <div class="container">
        <van-form @submit="onSubmit">
            <van-field
                    required
                    v-model="carNumber"
                    name="carNumber"
                    label="车牌号"
                    placeholder="车牌号"
            ></van-field>
            <van-field
                    v-model="carCode"
                    name="carCode"
                    label="车架号"
                    placeholder="车架号后4/6位"

            ></van-field>
            <van-field
                    v-model="carEngineCode"
                    name="carEngineCode"
                    label="发动机号"
                    placeholder="发动机号后4/6位"

            ></van-field>
            <van-field
                    readonly
                    clickable
                    name="carType"
                    :value="carTypes[carType]"
                    label="车辆类型"
                    @click="showPicker = true"
            ></van-field>
            <van-popup v-model="showPicker" position="bottom">
                <van-picker
                        show-toolbar
                        :columns="columns"
                        @confirm="(value,index)=>{carType = value;showPicker = false;}"
                        @cancel="showPicker = false"
                ></van-picker>
            </van-popup>
            <div style="margin: 1rem;">
                <van-button round block type="info" native-type="submit"
                            color="linear-gradient(to right, #4bb0ff, #6149f6)">
                    提交
                </van-button>
            </div>
        </van-form>

        <div class="list">
            <div class="list-item" v-for="item in list">
                <p class="list-item-title">{{item.address}}</p>
                <p class="list-item-content">{{item.reason}}</p>
                <p class="list-item-content" style="display:flex;align-items: center;justify-content: space-between">
                    <time class="list-item-time">{{item.time}}</time>
                    <span>{{item.money}}</span>
                </p>

            </div>
        </div>

        <transition name="van-slide-up">
            <div v-show="msg" style="text-align: center;margin:0 1rem;">{{msg}}</div>
        </transition>
    </div>

</template>

<script>
    <!--    车辆违章-->

    const carTypes = {
        '大型汽车': '01',
        '小型汽车': '02',
        '使馆汽车': '03',
        '领馆汽车': '04',
        '境外汽车': '05',
        '外籍汽车': '06',
        '两三轮摩托车': '07',
        '轻便摩托车': '08',
        '使馆摩托车': '09',
        '境外摩托车': '11',
        '外籍摩托车': '12',
        '农用摩托车': '13',
        '拖拉机': '14',
        '挂车': '15',
        '教练汽车': '16',
        '教练摩托车': '17',
        '试验汽车': '18',
        '试验摩托车': '19',
        '临时入境汽车': '20',
        '临时入境摩托车': '21',
        '临时行驶车': '22',
        '公安警车': '23',
        '其他车型': '24'
    }
    module.exports = {
        data() {
            return {
                carTypes: carTypes,
                carNumber: '贵AH171R', // 车牌号
                carCode: '037572', // 车架号后4/6位
                carEngineCode: '366893', //发动机号
                carType: '',
                showPicker: false,
                columns: Object.keys(carTypes),
                msg: '',
                list: [],
                loading: false,
                finished: false,
            }
        },
        methods: {
            getInfo(payload) {
                return axios.get('https://route.showapi.com/862-1', {
                    params: {
                        ...payload

                    }
                }).then(res => {
                    console.log('thenres', res)
                    this.msg = res.msg
                    this.list = res.records
                }).catch(error => {
                    console.log('catcherror', error)
                    vant.Toast.fail(error.msg)
                })
            },
            onSubmit(values) {
                if (!this.carNumber) {
                    return vant.Toast.fail('必须输入车牌号')
                }
                this.getInfo(values)
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

