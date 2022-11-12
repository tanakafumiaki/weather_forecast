<template>
  <div class="hello">
  <!-- スマホ用 -->
  <div v-if="windowWidth < 768">
    <!-- <p>
      768px未満の画面で表示
    </p> -->
    <div class="card border-light mb-3 text-white bg-info" style="max-width: 90%; margin:0 auto;">
      <div class="row g-0 m-2">
        <div class="col-md-12">
          <div class="card-body">
            <div style="text-align:left;">
              <h5 class="card-title">
                Aria:
                <select
                v-model="selected"
                @change="search"
                :item="option"
                >
                    <option disabled value="">選択して下さい</option>
                    <option 
                      v-for="option in options" 
                      v-bind:value="option.name" 
                      v-bind:key="option.id"
                    >
                      {{ option.name }}
                    </option>
                </select>
              </h5>
              <br>
              <div>
                <div style="width: 50%; float:left;">
                  <div style="font-weight:bold;"><h1 class="card-title">{{infoCurrentTemp}}</h1></div><br>
                  <div>{{infoDate}}</div>
                </div>
                <div style="width: 50%; float:left;">
                  <div class="bgIconMobile sunnyMobile" v-if="isSunnyIcon" />
                  <div class="bgIconMobile rainyMobile" v-if="isRainyIcon" />
                  <div class="bgIconMobile snowMobile" v-if="isFogIcon" />
                  <div class="bgIconMobile fogMobile" v-if="isSnowIcon" />
                  <img alt="Vue logo" width="128" height="128" src="../assets/logo.png" v-if="selected === ''">
                </div>
              </div>
              <!-- <li>Max Temperature: {{infoMaxTemp}}</li><br>
              <li>Min Temperature: {{infoMinTemp}}</li> -->
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="bgMobile">
      <img alt="Tokyo View" class="ariaImageMobile" src="../assets/photo-tokyo-tower.jpeg" v-if="selected === 'Tokyo'">
      <img alt="NewYork View" class="ariaImageMobile" src="../assets/photo-NewYork.jpeg" v-if="selected === 'New York'">
      <img alt="London View" class="ariaImageMobile" src="../assets/photo-London.jpeg" v-if="selected === 'London'">
    </div>
  </div>
  <!-- PC用 -->
  <div v-else>
    <div class="card border-light mb-3 text-white bg-info" style="max-width: 40%; margin:0 auto;">
      <div class="row g-0 m-2">
        <div class="col-md-6">
          <div class="card-body">
            <ul style="text-align:left;">
              <li>
                <h5 class="card-title">
                  Aria:
                  <select
                  v-model="selected"
                  @change="search"
                  :item="option"
                  >
                      <option disabled value="">選択して下さい</option>
                      <option 
                        v-for="option in options" 
                        v-bind:value="option.name" 
                        v-bind:key="option.id"
                      >
                        {{ option.name }}
                      </option>
                  </select>
                </h5>
              </li><br>
              <li style="font-weight:bold;"><h1 class="card-title">{{infoCurrentTemp}}</h1></li><br>
              <li>{{infoDate}}</li><br>
              <!-- <li>Max Temperature: {{infoMaxTemp}}</li><br>
              <li>Min Temperature: {{infoMinTemp}}</li> -->
            </ul>
          </div>
        </div>
        <div class="col-md-6">
          <div class="bgIcon sunny" v-if="isSunnyIcon" />
          <div class="bgIcon rainy" v-if="isRainyIcon" />
          <div class="bgIcon snow" v-if="isFogIcon" />
          <div class="bgIcon fog" v-if="isSnowIcon" />
          <img alt="Vue logo" width="256" height="256" src="../assets/logo.png" v-if="selected === ''">
        </div>
      </div>
    </div>
    <div class="bg">
      <img alt="Tokyo View" class="ariaImage" src="../assets/photo-tokyo-tower.jpeg" v-if="selected === 'Tokyo'">
      <img alt="NewYork View" class="ariaImage" src="../assets/photo-NewYork.jpeg" v-if="selected === 'New York'">
      <img alt="London View" class="ariaImage" src="../assets/photo-London.jpeg" v-if="selected === 'London'">
    </div>
  </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'HelloWorld',
  data(){
    return{
      info: [],
      infoAria: '',
      infoCurrentTemp: '',
      infoMaxTemp: '',
      infoMinTemp: '',
      infoDate: '',
      infoCode: '',
      selected: '',
      isVisible: true,
      option: '',
      isSunnyIcon: false,
      isFogIcon: false,
      isRainyIcon: false,
      isSnowIcon: false,
      windowWidth: '',
      options: [
          { id: 1, name: 'Tokyo' },
          { id: 2, name: 'New York' },
          { id: 3, name: 'London' }
      ]
    }
  },
  mounted () {
    window.addEventListener('resize', this.resizeWindow)
  },
  methods: {
    resizeWindow () {
        this.windowWidth = window.innerWidth
    },
    search: function() {
      var url = 'https://api.open-meteo.com/v1/forecast?'
      var aria = ''
      var latitude = ''
      var longitude = ''
      var daily = '&daily=weathercode,temperature_2m_max,temperature_2m_min,precipitation_hours&current_weather=true'
      if (this.selected === 'Tokyo') {
        aria = '&timezone=Asia%2FTokyo'
        latitude = 'latitude=35.6785'
        longitude = '&longitude=139.6823'
      } else if (this.selected === 'New York') {
        aria = '&timezone=America%2FNew_York'
        latitude = 'latitude=40.71'
        longitude = '&longitude=-74.01'
      } else if (this.selected === 'London') {
        aria = '&timezone=Europe%2FLondon'
        latitude = 'latitude=51.5002'
        longitude = '&longitude=-0.1262'
      }
      url = url + latitude + longitude + daily + aria
      this.isVisible = false;
      this.isSunnyIcon = false;
      // console.log(url);
      axios.get(url)
      // axios.get('https://api.open-meteo.com/v1/forecast?latitude=35.6785&longitude=139.6823&daily=temperature_2m_max,temperature_2m_min,precipitation_hours&timezone=Asia%2FTokyo')
        .then(response => {
          this.info = response.data
          this.infoAria = response.data.timezone
          this.infoMaxTemp = response.data.daily.temperature_2m_max[0]
          this.infoMinTemp = response.data.daily.temperature_2m_min[0]
          this.infoDate = response.data.daily.time[0]
          this.infoCode = response.data.daily.weathercode[0]
          this.infoCurrentTemp = response.data.current_weather.temperature + '℃'
          // console.log(response.data)
          // console.log(response.data.timezone)
          // console.log(response.data.daily.temperature_2m_max[0])
          // console.log(response.data.daily.temperature_2m_min[0])
          // console.log(response.data.daily.time[0])
          // console.log(this.infoCode)
          if (this.infoCode === 0 | this.infoCode === 1 | this.infoCode === 2 | this.infoCode === 3) {
            this.isSunnyIcon = true;
            this.isFogIcon = false;
            this.isSnowIcon = false;
            this.isRainyIcon = false;
          } else if (this.infoCode === 45 | this.infoCode === 48 | this.infoCode === 51 
          | this.infoCode === 53 | this.infoCode === 55 | this.infoCode === 56 | this.infoCode === 57) {
            this.isSunnyIcon = false;
            this.isFogIcon = true;
            this.isSnowIcon = false;
            this.isRainyIcon = false;
          } else if (this.infoCode === 77) {
            this.isSunnyIcon = false;
            this.isFogIcon = false;
            this.isSnowIcon = true;
            this.isRainyIcon = false;
          } else {
            this.isSunnyIcon = false;
            this.isFogIcon = false;
            this.isSnowIcon = false;
            this.isRainyIcon = true;
          }
        })
        .catch(error => console.log(error))
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.bgIcon {
  border-radius: 50%;
  height: 256px;
  width: 256px;
  margin:0 auto;
}
.bgIconMobile {
  border-radius: 50%;
  height: 128px;
  width: 128px;
  margin:0 auto;
}
.ariaImage {
  width: 900px;
  height: 700px;
  /* margin: 0 auto; */
}
.bg {
      /*位置の設定*/
    /* width: 100%;
    height: 100%; */
    position: absolute;
    left: 280px;
    top: 0;
    /* left: 0; */
    z-index: -1;
}
.ariaImageMobile {
  width: 100%;
  height: 700px;
}
.bgMobile {
    position: absolute;
    top: 0;
    left: 0;
    z-index: -1;
}
.sunny {
  background-image: url("~@/assets/Sunny.jpeg");
}
.rainy {
  background-image: url("~@/assets/Rainy.jpeg");
}
.snow {
  background-image: url("~@/assets/Snow.jpeg");
}
.fog {
  background-image: url("~@/assets/Fog.jpeg");
}
.sunnyMobile {
  background-image: url("~@/assets/SunnyMobile.jpeg");
}
.rainyMobile {
  background-image: url("~@/assets/RainyMobile.jpeg");
}
.snowMobile {
  background-image: url("~@/assets/SnowMobile.jpeg");
}
.fogMobile {
  background-image: url("~@/assets/FogMobile.jpeg");
}
</style>
