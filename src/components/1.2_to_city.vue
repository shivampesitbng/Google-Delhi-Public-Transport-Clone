<template>
  <v-app class="to-city" style="background:#fff;">

    <v-navigation-drawer
      style=""
      temporary
      v-model="drawer"
      :mini-variant.sync="mini"
      light
      overflow
      absolute
    >
      <v-list class="pa-0">
        <v-list-tile avatar tag="div">
          <v-list-tile-avatar>
          </v-list-tile-avatar>
          <v-list-tile-content>
            <v-list-tile-title>
              RSRTC
            </v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
      <v-list class="pt-0" dense>
        <v-divider ></v-divider>
        <v-list-tile v-for="item in items" :key="item.title" >
          <v-list-tile-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title>{{ item.title }}</v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>

    <v-toolbar fixed class="aliceblue" dark  style="  background:#fff " >
      <v-toolbar-side-icon  style="flaot:left" @click.stop="drawer = !drawer" ></v-toolbar-side-icon>
        <div class="green--text">  <i style="font-size:2vh!important"class="material-icons md-18">&#xE061;</i></div>

		<input
            placeholder="TO CITY"
            id="testing"
            v-model="to_city"
            @keyup="search"
            ref='search'
            type="search"
            autofocus
			style="width:100%;margin-top:-7px;height:100%;color:#000;font-size:20px"

          >

        <div id="search">
          <svg style="width:24px;height:24px" viewBox="0 0 24 24">
            <path fill="#BFC9CA" d="M9.5,3A6.5,6.5 0 0,1 16,9.5C16,11.11 15.41,12.59 14.44,13.73L14.71,14H15.5L20.5,19L19,20.5L14,15.5V14.71L13.73,14.44C12.59,15.41 11.11,16 9.5,16A6.5,6.5 0 0,1 3,9.5A6.5,6.5 0 0,1 9.5,3M9.5,5C7,5 5,7 5,9.5C5,12 7,14 9.5,14C12,14 14,12 14,9.5C14,7 12,5 9.5,5Z" />
          </svg>
        </div>
        <div  @click="startSpeech" id="mic" v-ripple="{ class: 'grey--text' }">
          <svg style="width:24px;height:24px" viewBox="0 0 24 24" >
            <path fill="#616A6B" d="M12,2A3,3 0 0,1 15,5V11A3,3 0 0,1 12,14A3,3 0 0,1 9,11V5A3,3 0 0,1 12,2M19,11C19,14.53 16.39,17.44 13,17.93V21H11V17.93C7.61,17.44 5,14.53 5,11H7A5,5 0 0,0 12,16A5,5 0 0,0 17,11H19Z" />
          </svg>
        </div>
      </v-toolbar>
    </v-toolbar>

    <main  style="padding:0px">
    <v-container fluid>
	  <div class="preloader" v-if="show_p_bar_to_city" style="margin-top:-4px"></div>
      <v-snackbar
        :timeout="3000"
        :vertical="mode === 'vertical'"
        v-model="snackbar"
      >
        {{ text_snackbar }}
      </v-snackbar>

      <div @click="gps" v-ripple style="cursor:pointer">
        <div style="margin-top:15px">
          <p id="recenticon" >
            <svg style="width:24px;height:24px;margin-right:17px;font-weight:200;margin-top:10px;" >
              <path fill="#616A6B" d="M12,8A4,4 0 0,1 16,12A4,4 0 0,1 12,16A4,4 0 0,1 8,12A4,4 0 0,1 12,8M3.05,13H1V11H3.05C3.5,6.83 6.83,3.5 11,3.05V1H13V3.05C17.17,3.5 20.5,6.83 20.95,11H23V13H20.95C20.5,17.17 17.17,20.5 13,20.95V23H11V20.95C6.83,20.5 3.5,17.17 3.05,13M12,5A7,7 0 0,0 5,12A7,7 0 0,0 12,19A7,7 0 0,0 19,12A7,7 0 0,0 12,5Z" />
            </svg >
          </p>
          <span >
            <span   id="recent2" style="font-size:18px;" >Use Current Location</span>
            <h6 id="gps" >Using GPS</h6>
          </span>
        </div>`
      </div>

      <v-divider style="background:#616A6B;width:80%;margin-bottom:-7px;margin-top: -15px;height:2px"></v-divider>
      <p style="padding-left: 8.5vh;color:#BFC9CA;padding-top:4vh;font-size: 2.4vh;">RECENT SEARCHES</p>
      <v-divider style="background:none;width:80%;margin-bottom:-7px"></v-divider>


      <div v-show="show_recent" v-for="(city,c) in recent_copy" @click="select_to_city(city)" v-ripple style="cursor:pointer">
        <p id="recenticon" v-ripple="{ class: 'grey--text' }">
          <svg style="width:24px;height:24px;margin-right:20px;font-weight:200;margin-top:4px;margin-bottom:-6px" >
            <path fill="#616A6B" font-weight="200" d="M11,7V12.11L15.71,14.9L16.5,13.62L12.5,11.25V7M12.5,2C8.97,2 5.91,3.92 4.27,6.77L2,4.5V11H8.5L5.75,8.25C6.96,5.73 9.5,4 12.5,4A7.5,7.5 0 0,1 20,11.5A7.5,7.5 0 0,1 12.5,19C9.23,19 6.47,16.91 5.44,14H3.34C4.44,18.03 8.11,21 12.5,21C17.74,21 22,16.75 22,11.5A9.5,9.5 0 0,0 12.5,2Z" />
          </svg >
        </p>
        <div  id="recent" style="margin-top:10px;font-size:14px">
          <p  style="padding-top:0.5vh;height:100%;letter-spacing:0.3px">{{city.name}}</p>
        </div>
        <span v-if="c!=recent_copy.length-1">
          <v-divider style="width:80%;margin-top:-4px"></v-divider>
        </span>
      </div>


      <div v-for="(city,c) in cities" @click="select_to_city(city)" :class="city.class" v-ripple style="cursor:pointer">
        <p id="recenticon" v-ripple="{ class: 'grey--text' }">
          <!-- Marker icon by Icons8 -->
            <img class="icon icons8-Marker" width="24" height="24" style="width:24px;height:24px;margin-right:20px;font-weight:200;margin-top:4px"    src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAAwCAYAAABXAvmHAAACjElEQVRoQ+2Y8TEFQQzG8ypABagAFaACVIAKKIEKUAEqQAWoABWgAlTA/Gb2Zs7Ne5cvu3uPN+Sf++Oy2XzJl2x2RzbjMppx/+0fwE9nsHYGVs1s18z4zqcvGB/N7D19L9O3CvZaAHD6yMyWRK9ekj5giqQUAJE+b0U66gyZ2S/JSAmA7eQ8VCkRqAWI6xwjuQBw/ipnw541m2Z2F7WZAwDa3KYije7Xp08mAAGtZMkB8FDAec8xnF/zlNr/owD2Eu8je0R1qYcLdVEUwHOgVao+dPVoscvq4ggAuA99FLkxs9NWUW6Y2aGZbSmLE42kWogAwKEDwYE+CqgUPEuA3e0iAGhx645FIk+L7RP6vZeJezMja65EACjdR+nlOEYb7hO5G0UAfLrhMHk8r2brTwHgpJxzslCLQh/qSR/JgFLEFOiOA5IZyiv0QYqY05G535Nf20Y5iE4879N/MsG5QSQR2i/rvcg35uVxIkIhbluMEtMQRglGClciADBGf15xrZYpPEWm3SiACI1yYcj0YYMoAK6Pb7meiesW0guGpB4FgFG1G0kOdJR4pWDgkyUHwJDFLBdvgzAHwFBZCEc/pwYa4LWzwOjAhUlqnW1+5WYAG+oFR+HzcXqpU3S/6ZQAoCMRMW/A85x6TdFnWAxLCQA2q/HApUywE4GVAsCwckWc5IB8951koAaAXCoVUae0jXYDotxzu2uKqFMbQLQrZXedbhRqUKhtU7m1ybctpSXVBkA9MHIvTticURm6ZbXMcTZrA2APTlQy0T0fOG1xXnoyVKJfMkp49sedD1WKdugaaNtvv4OGLiledGrNQso+zWwvv/crRqcJIOpPWH+IIg47UbLgH0BJ9Gqs/QKWtG0xYp5DgAAAAABJRU5ErkJggg==">
        </p>
        <div  id="recent" style="margin-top:10px;font-size:14px">
          <p  style="padding-top:0.5vh;height:100%;letter-spacing:0.3px">{{city.name}}</p>
        </div>
        <span >
          <v-divider style="width:80%;margin-top:-4px"></v-divider>
        </span>
      </div>
    </v-container>
 </main>
  </v-app>
</template>

<script>
let recognition

import {mapGetters} from 'vuex'

export default {
  name: 'to-city',
  data () {
    return {
      show_p_bar_to_city : false,
      text: '',
      text_2:'',
      to_city : '',
      cities : [],
      city : {
        name: ''
      },
      recent_copy : [],
      err_msg:'',
      show_recent:true,
      min_dist_city : {},

      snackbar: false,
        y: 'top',
        x: null,
        mode: '',
        timeout: 6000,
        text_snackbar: '',

        drawer: null,
            items: [
              { title: 'About', icon: 'question_answer' }
            ],
            mini: false,
            right: null
    }
  },

  methods:{

    startSpeech () {
		this.$refs.search.focus()
      this.snackbar = true
      this.text_snackbar = 'Speak Now !'
      recognition.start()
      //this.text_2  = 'Speak now' // snackbar
    },
    //START_SPEECH ends

    search(){

      if(this.to_city == ''){
        this.cities = [];
        this.show_recent=true
        return;
      }else{
        this.show_p_bar_to_city = true
        //this.$Progress.start()
        this.$http.get('cities_&_stops/' + this.to_city[0].toUpperCase() + '.json')
        .then(response=>{
          this.cities = [];
          let cities=response.body
          for(let city in cities){
            this.city = {
              name:cities[city]
            }
            this.show_recent=false
            this.cities.push(this.city)
          //  this.$Progress.finish()
          this.show_p_bar_to_city = false
          }
          this.filter()
          return response.json()
        },response=>{
          //
        })
      }
    },
    //SEARCH ends

    filter(){
      for(let city in this.cities){
        if(this.cities[city].name.indexOf(this.to_city.toUpperCase()) != -1){
          this.cities[city].class = 'show_city'
          recognition.stop()
          this.snackbar = true
          this.text_snackbar = 'Match Found ! Click On Match To Proceed'
          //this.text = 'Match Found ! :) Click On Match To Proceed' //snackbar
          //this.text_2 = ''
        }else{
          this.cities[city].class = 'hide_city'
          this.snackbar = true
          this.text_snackbar = 'No Match Found ! Try Again'
          //this.text = 'No Match Found ! :( Try Again' //snackbar
          //this.text_2 = 'speak again'
        }
      }
    },
    //FILTER ends

    select_to_city(city){
	  this.$refs.search.focus()
      if(this.$store.state.from_city.name != city.name)
        this.$store.state.to_city = city
      else{
        this.err_msg = 'Source & Destination Cant be Same'
        //console.log("same source destination")

		this.snackbar = true
		this.text_snackbar = 'Source & Destination Cant be Same'

		return
      }


      //recent
      //_check duplicate
      this.$store.state.recent = JSON.parse(localStorage.getItem('recent'))
      for(let c in this.$store.state.recent){
        if(this.$store.state.recent[c].name == city.name){
          this.$store.state.recent.splice(c,1)
          break
        }
      }
      //_check duplicate ends

      //check len
      if(this.$store.state.recent != null){
        if(this.$store.state.recent.length == 10){
          this.$store.state.recent.splice(0,1)
        }
      }else{
        this.$store.state.recent = []
      }
      //check len ends

      this.$store.state.recent.push(city)
      localStorage.setItem('recent',JSON.stringify(this.$store.state.recent))
      //recent ends

      //check if -> Source & Destination are Same
      if(this.$store.state.from_city != null && this.$store.state.to_city != null){
        if(this.$store.state.from_city.name == this.$store.state.to_city.name){
          this.err_msg = 'Source & Destination Cant be Same'

		this.snackbar = true
		this.text_snackbar = 'Source & Destination Cant be Same'

          return
        }
      }
      //check if -> Source & Destination are Same ends

      localStorage.setItem('to_city',JSON.stringify(city))

      this.$router.push('/bus')
    },
    //SELECT_to_city ends

    get_recent(){
      this.$store.state.recent = JSON.parse(localStorage.getItem('recent'))
      if(this.$store.state.recent == null)
        this.$store.state.recent = []
      for(let city in this.$store.state.recent){
          this.recent_copy[this.$store.state.recent.length-1-city] = this.$store.state.recent[city]
      }
    },
    //GET_RECENT ends

    gps(){
      this.show_p_bar_to_city = true
    //  this.$Progress.start()
      if(navigator.geolocation){
        navigator.geolocation.getCurrentPosition(this.find_nearest)
      }else{
        //console.log("Cannot Fetch Your Current Location") //*** Snackbar
      }
    },
    //GPS ends

    find_nearest(position){

      this.$http.get('cities_&_stops_lat_&_lng.json')
      .then(response=>{
        let all_locs=response.body
        this.city_dist_arr = []
        //init min
        this.min_dist_city = {
          d:99999999999999999 ,
          name : ''
        }
        for(let loc in Object.keys(all_locs)){
          let k =  Object.keys(all_locs)[loc] // city

          this.haversine_formula(position.coords.latitude,position.coords.longitude,
            all_locs[k][0],all_locs[k][1], k)//call haversine
        }
        if(this.min_dist_city.name != ''){
      //    this.$Progress.finish()
          this.select_to_city(this.min_dist_city)
          this.show_p_bar_to_city = false
        }

        return response.json()
      })
      .then(response=>{
        //error
      })
    },
    //FIND_NEAREST ends

    haversine_formula(cur_lng, cur_lat, city_lng, city_lat, city_name){
      var rad = function(x) {
        return x * Math.PI / 180;
      }
      var lat = [cur_lng, city_lng]
      var lng = [cur_lat, city_lat]
      var R = 6378137;
      var dLat = (lat[1]-lat[0]) * Math.PI / 180;
      var dLng = (lng[1]-lng[0]) * Math.PI / 180;
      var a = Math.sin(dLat/2) * Math.sin(dLat/2) +
      Math.cos(lat[0] * Math.PI / 180 ) * Math.cos(lat[1] * Math.PI / 180 ) *
      Math.sin(dLng/2) * Math.sin(dLng/2);
      var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
      var d = R * c;
      var e =  Math.round(d);

      let temp_city_dist_obj = {
        d : e,
        name : city_name
      }


      //update min
      if(this.min_dist_city.d > temp_city_dist_obj.d){
          this.min_dist_city = {
            d: temp_city_dist_obj.d,
            name: temp_city_dist_obj.name
          }
      }

    }
    //HAVERSINE_FORMULA ends

  },
  // METHODS ends

  computed:{
    ...mapGetters([
      'from_city','recent'
    ])
  },
  //COMPUTED ends

  mounted(){
    this.$refs.search.focus()
    this.$store.state.flg2 = true
  },
  //MOUNTED ends

  beforeMount(){
    this.get_recent()
  },
  //BEFORE_MOUNT ends

  created() {
   recognition =  new webkitSpeechRecognition();
   recognition.continuous = true;
   recognition.interimResults = false;
   recognition.lang = 'en-US';

   recognition.onresult = event => {
       let interim_transcript = '';
       for (let i = event.resultIndex; i < event.results.length; ++i) {
           if (event.results[i].isFinal) {
               this.to_city = event.results[i][0].transcript;
               if(this.to_city[0] == ' '){
                //console.log("whitespace")
                this.to_city = this.to_city.slice(1)
               }
               this.search()
           } else {
               interim_transcript += event.results[i][0].transcript;
           }
       }
   };
 }
 //CREATED ends

}
</script>

<style scoped>
.show_city{
  display:block;
}
.hide_city{
  display:none;
}

.application>main>.container {

    padding-left: 0vh;
	padding-right: 0vh;
	padding-top: 10vh;
}

input::-webkit-input-placeholder {
    font-size: 20px;
    line-height: 3;
}
textarea:focus, input:focus{
    outline: none;
}

</style>
