<template>
    <div v-if="setted" class="weather-container" :class="(weather.main.temp <= 14)?'weather-cold weather-container':'weather-container'">
        <h1>Узнай погоду в любом городе</h1>
        <div class="search-box">
         <input type="text" 
         class="search-bar" 
         placeholder="Точное название города на русском/английском (Enter)"
         v-model="query"
         @keyup.enter="fetchWeather"
         />
        </div>
        <div v-if="typeof weather.main != 'undefined'" class="weather-wrap">
            <div class="location-box">
                <div class="location">{{weather.name}}, {{weather.sys.country}}</div>
                <div class="date">{{dataBuilder()}}</div>
            </div>
            <div class="weather-box">
                 <div class="temp">{{ Math.round(weather.main.temp)}}°C</div>
                <div class="weather">{{weather.weather[0].main}}</div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            api_key: '36b94826139b1854f33deea604a0f500',
            url_base: "https://api.openweathermap.org/data/2.5/",
            query: 'Москва',
            weather: {},
            setted: false,
        }
    },
    beforeMount () {
        this.fetchWeather();
    },
    methods: {
        dataBuilder() {
            let d = new Date();
            let months = [
            "Января", "Февраля", "Марта",
            "Апреля", "Мая", "Июня",
            "Июля", "Августа", "Сентября",
            "Октября", "Ноября", "Декабря"
            ];
            let days = ["Понедельник", "Вторник", "Среда",
                        "Четверг", "Пятница", "Суббота", "Воскресенье"]
            let day = days[d.getDay()];
            let date = d.getDate();
            let month = months[d.getMonth()];
            let year = d.getFullYear();
            return `${day} ${date} ${month} ${year}`
        },
        fetchWeather() {
            fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
                .then(res => {
                    return res.json();
                }).then(this.setResults)
            this.query = ''
        },
        setResults(result) {
            this.weather = result;
            this.setted = true;
        }
    },
}
</script>

<style scoped>
    .weather-container h1 {
        text-align: center;
        margin-bottom: 20px;
        font-size: 55px;
        text-shadow: 4px 6px rgba(0,0,0,.5)
    }
    .weather-container {
        background: no-repeat bottom;
        background-image: linear-gradient(to bottom, rgba(0, 0, 0, .25), rgba(0, 0, 0, .75)), url('../assets/warm-bg.jpg');
        background-size: cover;
        transition: 0.4s;
        min-height: 100vh;
        padding: 25px;
        position: relative;
        color: #fff;
    }
    .weather-cold {
        background-image: linear-gradient(to bottom, rgba(0, 0, 0, .25), rgba(0, 0, 0, .75)), url('../assets/cold-bg.jpg')
    }
    .search-box {
        width: 100%;
        margin-bottom: 30px;
    }
    .search-box .search-bar {
        display: block;
        width: 100%;
        padding: 15px;
        appearance: none;
        border: none;
        background-color: rgba(255, 255, 255, .8);
        border-radius: 0px 16px 0px 16px;
        transition: 0.4s;
        box-shadow: 0px 0px 8px rgba(0,0,0,.5);
        font-size: 32px;
        font-family: Arial, Helvetica, sans-serif;
        color: #f16794;
        text-shadow: 2px 1px rgba(0,0,0,.2)
    }
    .search-box .search-bar:focus {
        background-color: rgba(255, 255, 255, 0.75);
        box-shadow: 0px 0px 30px rgba(0,0,0,.5);
        outline: none;
    }
    .location-box .location {
        color: #fff;
        font-size: 32px;
        text-align: center;
        text-shadow: 4px 6px rgba(0,0,0,.25)
    }
    .location-box .date {
        color: #fff;
        font-size: 20px;
        font-weight: 300;
        font-style: italic;
        text-align: center;
    }
    .weather-box {
        text-align: center;
    }
    .weather-box .temp {
        display: inline-block;
        padding: 10px 25px;
        font-size: 102px;
        font-weight: 900;
        text-shadow: 3px 6px rgba(0,0,0,.25);
        background-color: rgba(255,255,255,.25);
        border-radius: 16px;
        margin: 30px 0;
        
        box-shadow: 3px 6px rgba(0,0,0,.25);
    }
    .weather-box .weather {
        font-size: 48px;
        font-weight: 700;
        color: #fff;
        text-shadow: 3px 6px rgba(0,0,0,.25);
    }
</style>