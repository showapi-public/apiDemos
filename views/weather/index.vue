<template>

    <div class="container">
        <div class="weather-side">
            <div class="weather-gradient"></div>
            <div class="date-container" @click="showDialog = true">
                <h2 class="date-dayname">{{weekday}}</h2>
                <span class="date-day">{{date}}</span>
                <i class="location-icon" data-feather="map-pin"></i>
                <span class="location">{{cityInfo.c3}}, {{cityInfo.c7}}</span>
            </div>
            <div class="weather-container"><img :src="now.weather_pic" alt="">
                <h1 class="weather-temp">{{now.temperature}}°C</h1>
                <h3 class="weather-desc">{{now.weather}}</h3>
            </div>
        </div>
        <div class="info-side">
            <div class="today-info-container">
                <div class="today-info">
                    <div class="precipitation"><span class="title">空气湿度</span><span class="value">{{now.sd}}</span>
                        <div class="clear"></div>
                    </div>
                    <div class="humidity"><span class="title">空气质量</span><span class="value" v-if="now.aqiDetail">{{now.aqiDetail.quality}}</span>
                        <div class="clear"></div>
                    </div>
                    <div class="wind"><span class="title">风力</span><span class="value">{{now.wind_power}}</span>
                        <div class="clear"></div>
                    </div>
                </div>
            </div>
            <div class="week-container">
                <ul class="week-list">
                    <li class="active"><img :src="f1.day_weather_pic" width="40px"><span class="day-name">星期{{['日','一','二','三','四','五','六'][dateInstance.getDay()]}}</span><span
                            class="day-temp">{{f1.day_air_temperature}}°C</span></li>
                    <li><img :src="f2.day_weather_pic" width="40px"><span class="day-name">星期{{['日','一','二','三','四','五','六'][dateInstance.getDay() +1]}}</span><span
                            class="day-temp">{{f2.day_air_temperature}}°C</span></li>
                    <li><img :src="f3.day_weather_pic" width="40px"><span class="day-name">星期{{['日','一','二','三','四','五','六'][dateInstance.getDay() +2]}}</span><span
                            class="day-temp">{{f3.day_air_temperature}}°C</span></li>
                    <li><img :src="f4.day_weather_pic" width="40px"><span class="day-name">星期{{['日','一','二','三','四','五','六'][dateInstance.getDay() +3]}}</span><span
                            class="day-temp">{{f4.day_air_temperature}}°C</span></li>
                    <div class="clear"></div>
                </ul>
            </div>
            <div class="location-container">
                <button class="location-button" @click="showDialog = true"><i
                        data-feather="map-pin"></i><span> 切换地点</span></button>
            </div>
        </div>


        <van-dialog v-model="showDialog" title="地名" show-cancel-button style="color:initial"
                    :before-close="beforeClose">
            <van-form>
                <van-field
                        v-model="area"
                        name="area"
                        label="地名"
                        placeholder="昆明"
                />
            </van-form>
        </van-dialog>
    </div>
</template>

<script>
    module.exports = {
        data: function () {
            return {
                weekday: new Date().toLocaleDateString('en-US', {
                    weekday: 'long',
                }),

                date: new Date().toLocaleDateString('en-US', {
                    year: 'numeric',
                    day: 'numeric',
                    month: 'short',
                }),
                who: 'world',
                cityInfo: {},
                now: {},
                f1: {},
                f2: {},
                f3: {},
                f4: {},
                dateInstance: new Date(),
                showDialog: false,
                area: ''
            }
        },
        methods: {
            // 获取当前所在地点
            getArea() {
                return axios.post('https://route.showapi.com/632-1').then(({city}) => {
                    this.area = city
                })
            },
            beforeClose(action, done) {
                if (action === 'cancel') {
                    done()
                } else {
                    this.getWeather().then(() => {
                        done()
                    }).catch(() => {
                        done(false)
                    })
                }
            },

            // 地名查天气
            getWeather() {
                return axios.post('https://route.showapi.com/9-2', {needMoreDay: 1, area: this.area}).then(res => {
                    this.cityInfo = res.cityInfo
                    this.now = res.now
                    this.f1 = res.f1,
                        this.f2 = res.f2,
                        this.f3 = res.f3,
                        this.f4 = res.f4


                })
            }
        },
        created() {
            this.getArea().then(this.getWeather)
        }
    }
</script>

<style>
    @import url('https://fonts.googleapis.com/css?family=Montserrat:400,700,900&display=swap');

    :root {
        --gradient: linear-gradient(135deg, #72EDF2 10%, #5151E5 100%);
    }

    * {
        -webkit-box-sizing: border-box;
        box-sizing: border-box;
        line-height: 1.25em;
    }
    .clear {
        clear: both;
    }

    html {
        overflow: -moz-hidden-unscrollable;
        height: 100%;
    }
    body {
        margin: 0;
        width: calc(100vw + 18px);
        height: 100vh;
        font-family: 'Montserrat', sans-serif;
        background-color: #343d4b;
        display: -webkit-box;
        display: -ms-flexbox;
        display: flex;
        -webkit-box-align: center;
        overflow-x: hidden;
        -ms-flex-align: center;
        align-items: center;
        -webkit-box-pack: center;
        -ms-flex-pack: center;
        justify-content: center;
        -ms-overflow-style: none;


    }

    body::-webkit-scrollbar {
        display: none;
    }


    .container {
        border-radius: 25px;
        -webkit-box-shadow: 0 0 70px -10px rgba(0, 0, 0, 0.2);
        box-shadow: 0 0 70px -10px rgba(0, 0, 0, 0.2);
        background-color: #222831;
        color: #ffffff;
        height: 400px;
    }

    .weather-side {
        position: relative;
        height: 100%;
        border-radius: 25px;
        background-image: url("https://images.unsplash.com/photo-1559963110-71b394e7494d?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=675&q=80");
        width: 100%;
        -webkit-box-shadow: 0 0 20px -10px rgba(0, 0, 0, 0.2);
        box-shadow: 0 0 20px -10px rgba(0, 0, 0, 0.2);
        -webkit-transition: -webkit-transform 300ms ease;
        transition: -webkit-transform 300ms ease;
        -o-transition: transform 300ms ease;
        transition: transform 300ms ease;
        transition: transform 300ms ease, -webkit-transform 300ms ease;
        -webkit-transform: translateZ(0) scale(1.02) perspective(1000px);
        transform: translateZ(0) scale(1.02) perspective(1000px);
        float: left;
    }

    /*.weather-side:hover {*/
    /*    -webkit-transform: scale(1.1) perspective(1500px) rotateY(10deg);*/
    /*    transform: scale(1.1) perspective(1500px) rotateY(10deg);*/
    /*}*/

    .weather-gradient {
        position: absolute;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
        background-image: var(--gradient);
        border-radius: 25px;
        opacity: 0.8;
    }

    .date-container {
        position: absolute;
        top: 25px;
        left: 25px;
    }

    .date-dayname {
        margin: 0;
    }

    .date-day {
        display: block;
    }

    .location {
        display: inline-block;
        margin-top: 10px;
    }

    .location-icon {
        display: inline-block;
        height: 0.8em;
        width: auto;
        margin-right: 5px;
    }

    .weather-container {
        position: absolute;
        bottom: 25px;
        left: 25px;
    }

    .weather-icon.feather {
        height: 60px;
        width: auto;
    }

    .weather-temp {
        margin: 0;
        font-weight: 700;
        font-size: 4em;
    }

    .weather-desc {
        margin: 0;
    }

    .info-side {
        position: relative;
        float: left;
        width: 100%;
        height: 100%;
        padding-top: 25px;
    }

    .today-info {
        padding: 15px;
        margin: 0 25px 25px 25px;
        /* 	box-shadow: 0 0 50px -5px rgba(0, 0, 0, 0.25); */
        border-radius: 10px;
    }

    .today-info > div:not(:last-child) {
        margin: 0 0 10px 0;
    }

    .today-info > div .title {
        float: left;
        font-weight: 700;
    }

    .today-info > div .value {
        float: right;
    }

    .week-list {
        list-style-type: none;
        padding: 0;
        margin: 10px 35px;
        -webkit-box-shadow: 0 0 50px -5px rgba(0, 0, 0, 0.25);
        box-shadow: 0 0 50px -5px rgba(0, 0, 0, 0.25);
        border-radius: 10px;
    }

    .week-list > li {
        float: left;
        padding: 17px;
        cursor: pointer;
        -webkit-transition: 200ms ease;
        -o-transition: 200ms ease;
        transition: 200ms ease;
        border-radius: 10px;
    }

    .week-list > li:hover {
        -webkit-transform: scale(1.1);
        -ms-transform: scale(1.1);
        transform: scale(1.1);
        background: #fff;
        color: #222831;
        -webkit-box-shadow: 0 0 40px -5px rgba(0, 0, 0, 0.2);
        box-shadow: 0 0 40px -5px rgba(0, 0, 0, 0.2)
    }

    .week-list > li.active {
        background: #fff;
        color: #222831;
        border-radius: 10px;
    }

    .week-list > li .day-name {
        display: block;
        margin: 10px 0 0 0;
        text-align: center;
    }

    .week-list > li .day-icon {
        display: block;
        height: 30px;
        width: auto;
        margin: 0 auto;
    }

    .week-list > li .day-temp {
        display: block;
        text-align: center;
        margin: 10px 0 0 0;
        font-weight: 700;
    }

    .location-container {
        padding: 25px 35px;
    }

    .location-button {
        outline: none;
        width: 100%;
        -webkit-box-sizing: border-box;
        box-sizing: border-box;
        border: none;
        border-radius: 25px;
        padding: 10px;
        font-family: 'Montserrat', sans-serif;
        background-image: var(--gradient);
        color: #ffffff;
        font-weight: 700;
        -webkit-box-shadow: 0 0 30px -5px rgba(0, 0, 0, 0.25);
        box-shadow: 0 0 30px -5px rgba(0, 0, 0, 0.25);
        cursor: pointer;
        -webkit-transition: -webkit-transform 200ms ease;
        transition: -webkit-transform 200ms ease;
        -o-transition: transform 200ms ease;
        transition: transform 200ms ease;
        transition: transform 200ms ease, -webkit-transform 200ms ease;
    }

    .location-button:hover {
        -webkit-transform: scale(0.95);
        -ms-transform: scale(0.95);
        transform: scale(0.95);
    }

    .location-button .feather {
        height: 1em;
        width: auto;
        margin-right: 5px;
    }
</style>
