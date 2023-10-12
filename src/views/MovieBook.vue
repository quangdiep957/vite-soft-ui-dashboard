<template>


<!----movie site template---->

<header>
    <div class="popular-movie-slider">
      <base-image-download
      class="poster"
            :linkImg="movieHot.posterLink"
          ></base-image-download>
      <!-- <img src="https://imageio.forbes.com/blogs-images/scottmendelson/files/2014/10/2v00kg8.jpg?format=jpg&width=1200" class="poster"> -->
      
      <div class="popular-movie-slider-content">
        <p class="release">2023</p>
        <h2 class="movie-name">{{ movieHot.movieName }}</h2>
        <ul class="category">
          <p>Thể loại: </p>
          <li>{{ movieHot.categoryName }}</li>
        </ul>
        <p class="desc">{{ movieHot.content }}</p>
        
        <div class="movie-info">
          <i class="fa fa-clock-o"> &nbsp;&nbsp;&nbsp;<span>{{ movieHot.timeLine }} phút</span></i> 
          <i class="fa fa-volume-up"> &nbsp;&nbsp;&nbsp;<span>Ngôn ngữ: {{ movieHot.language }}</span></i>
          <i class="fa fa-circle"> &nbsp;&nbsp;&nbsp;<span>Imdb: <b>9.1/10</b></span></i>
        </div>
        
        <div class="movie-btns">
          <button @click="openPopupVideo"><i class="fa fa-play"></i> &nbsp; Trailer</button>
          <button class="read-more"><i class="fa fa-circle"></i> <i class="fa fa-circle"></i> <i class="fa fa-circle"></i>&nbsp; Xem thêm</button>
        </div>
        
      </div>
      
    </div>
    <!---slider--->
    
</header>

<section>
  
  <div class="movie-ticket-book">
    <div class="choose-date">
      <div class="popup-input popup-date">
            <label>Chọn ngày *</label>
            <el-date-picker
              v-model="fromdate"
              type="date"
              :placeholder="'Chọn ngày'"
              :size="size"
              ref="fromdate"
              @blur="checkEmptyFromDate()"
              :class="isEmptyFromDate ? 'isEmpty' : ''"
            />
       <div class="marker"></div>
      </div>
    </div>
    <button @click = "openShowView(movieHot)">Đặt vé</button>
  </div>
  <!---movie-ticket-book-->
  
  
  <div class="filter-search-box">
    
    <div class="filters-box">
      
      <div class="all-filters filters" @click="getListMovie('all')">
        Tất cả <i class="fa fa-angle-down"></i>
      </div> 
      
      <div class="date-filters filters" @click="getListMovie('now')">
       Hôm nay <i class="fa fa-angle-down"></i>
      </div> 
      
      <div class="category-filters filters" @click="getListMovie('category')">
        Thể loại <i class="fa fa-angle-down"></i>
      </div> 
      
      <div class="category-filters filters" @click="getListMovie('coming')">
        Coming soon
      </div> 
      
    </div>
    
    <div class="search-filters">
        <input type="text" placeholder="Search by name...">
        <i class="fa fa-search"></i>
      </div> 
    
    <div class="search-bar">
      <div class="bar"></div>
    </div>
    
  </div>
  <!----filter-search-box---->
  
  
  <div class="movie-card-section">
    
    <div class="card" v-for="item in dataSource" :key="item.movieCode"   @click="selectMovie(item)" >
      <base-image-download
            :linkImg="item.posterLink"
          ></base-image-download>
      <div class="card-content">
        <p class="movie-name">
          {{ item.movieName }}
        </p>
        
        <div class="movie-info">
          <p class="time">11:30 <span>14:45<span class="d3">3D</span> 16:05<span class="d3">3D</span></span> 18:40 21:00 23:15</p>
        </div>
      </div>
    </div>
  </div>
  <!---movie-card--->
  
  <div class="show">
    <div class="show-bar">
      <div class="bar"></div>
    </div>
     <button>Show more</button>
  </div>
    <!---bar--->
  
  
</section>

<footer>
  
  <div class="logo-box">
    <p class="logo">
      multi<span>flex</span>
    </p>
    <p><i class="fa fa-copyright"></i> 2001-2017, SIA Multiflex</p>
  </div>
  
  <ul>
    <li>main</li>
    <li>schedlues</li>
    <li>tickets</li>
    <li>news</li>
    <li>contact</li>
  </ul>


<div class="socail-box">
  <i class="fa fa-facebook-f"></i>
  <i class="fa fa-twitter"></i>
  <i class="fa fa-instagram"></i>
</div>
  
</footer>

<popup-video :videoLink="movieHot.trailerLink" ref="popup" v-if = "showpopupVideo" @close="showpopupVideo = false"></popup-video>
<popup-seat-cinema
    v-if="$store.state.isOpenPopupSeat"
    :nameMovie="movieNameSelected"
    :idMovie="movieIDSelected"
  ></popup-seat-cinema>  
</template>

<script>
import { fields } from "@/constants/constantsmoviegrids.js";
import { filterMovie } from "@/constants/constantsdefaults.js";
import { convertDateFormat } from "@/common/commonFunc";
import BaseImageDownload from "./components/BaseImageDownload.vue";
import PopupSeatCinema from "./popups/PopupSeatCinema.vue";
import PopupVideo from "./popups/PopupVideo.vue";
import {
  
} from "@element-plus/icons-vue";
export default {
  name: "MovieBook",
  
  setup() {
    return {};
  },
  components: {
    BaseImageDownload,
    PopupSeatCinema,
    PopupVideo
  },
  created() {
   // lấy danh sách phim
   let me = this;
   this.$store.state.isOpenPopupSeat = false;
    me.dataField = fields;
    this.$store.state.isShowLoading = true;
    this.$api
      .post("/Movie/GetListMovie", { TypeFilter: 0 })
      .then((data) => {
        me.dataSource = data;
        this.$store.state.isShowLoading = false;
        this.movieHot = data[0];
        this.movieSelect = data[0];
        this.videoLink = data[0].trailerLink;
      });
  },
  props:{
    
  },
  data() {
    return {
      movieIDSelected: "",
      movieNameSelected: "",
      movieHot:[],
      dataField: [],
      movieSelect:[],
      fromdate:new Date(),
      dataSource: [],
      itemsSelected: {},
      showpopupVideo:false,
      IsOpenPopuppayment:false,
      searchValue: "",
      rowSelected: "",
      checkDelete: false,
      openTrailers: [],
      openContexts: [],
      contentSelected: "",
      nameMovie: "",
      typeFilter: 1,
      isEmptyFromDate:'',
      videoLink:''
    };
  },
  methods: {
    selectMovie(item)
    {
      this.movieHot = item;

    },
    openPopupVideo() {
      this.showpopupVideo = true;
    },
    getAlterMovie(item) {
      this.rowSelected = item;
      this.$store.state.isOpenPopupAlterMovie = true;
    },
    getListMovie(type)
    {
      let param = {};
        switch (type) {
          case 'all':
          param = {
            TypeFilter: 0,
          }
            break;
        
          default:
            break;
    me.dataField = fields;
    this.$store.state.isShowLoading = true;
    this.$api
      .post("/Movie/GetListMovie", { TypeFilter: me.typeFilter })
      .then((data) => {
        me.dataSource = data;
        this.$store.state.isShowLoading = false;
        this.movieHot = data[0];
      });
        }

    },
    checkEmptyFromDate() {
      setTimeout(() => {
        this.isEmptyFromDate = !this.fromdate ? true : false;
        if (this.isEmptyFromDate) {
          this.$store.dispatch("showToast", this.$t("BlankStartDate"));
        }
      }, 100);
    },
    isShowMovie(item) {
      return (
        item.movieName.toLowerCase().includes(this.searchValue.toLowerCase()) ||
        item.movieCode.toLowerCase().includes(this.searchValue.toLowerCase())
      );
    },
    // lấy các màn 
    openShowView(item) {
      this.dataMovie = item
      this.movieIDSelected = item.movieID;
      this.movieNameSelected = item.movieName;
      this.$store.state.isOpenPopupSeat = true;
      console.log(item);
    },

    isOpenTrailer(id) {
      return this.openTrailers.find((x) => x == id);
    },

    openTrailer(id) {
      if (this.openTrailers.find((x) => x == id)) {
        this.openTrailers = this.openTrailers.filter((x) => x != id);
      } else {
        this.openTrailers.push(id);
      }
    },

    isOpenContext(id) {
      return this.openContexts.find((x) => x == id);
    },

    openContext(id) {
      if (this.openContexts.find((x) => x == id)) {
        this.openContexts = this.openContexts.filter((x) => x != id);
      } else {
        this.openContexts.push(id);
      }
    },

    async openPopup() {
      if (await this.$store.dispatch("checkRole", "admin")) {
        this.$store.state.IsOpenPopup = true;
      }
    },
    getRowSelected(item) {
      let me = this;
      this.rowSelected = item;

      this.$store.state.isOpenPopupDelete = true;
    },

    loadData() {
      let param = {
        TypeFilter: 0
      }
      this.getData(param)
    },
    getData(param)
    {
      let me = this;
      this.$store.state.isShowLoading = true;
      this.$api
        .post("/Movie/GetListMovie",)
        .then((data) => {
          me.dataSource = data;
          me.$store.state.isShowLoading = false;
        });
    },

    handelAlter() {
      this.$store.state.isOpenPopupAlterMovie = false;
      this.loadData();
    },
    setPopupPayment(data)
    {
      this.dataBooking = data
      this.$store.state.IsOpenPopupPayment = true;
    },
    openContent(name, content) {
      this.nameMovie = name;
      this.contentSelected = content;
      this.$store.state.isOpenPopupShowContent = true;
    },
  },
};
</script>
<style lang="scss">
/*-----css movie site -----*/

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  transition:all .2s;
}


body{
  font-family: 'Roboto', sans-serif;
  overflow:hidden;
  overflow-y:scroll;
}


header{
  background:url('https://s3picturehouses.s3.eu-central-1.amazonaws.com/header/ph_15977496885f3bb9b84457c.jpg');
  background-size:cover;
  background-position:center;
  width:100%;
  height:550px;
  position:relative;
  padding-top:80px;
}


header:before{
  content:"";
  position:absolute;
  top:0;
  left:0;
  width:100%;
  height:100%;
  background:rgba(0,0,0,.75);
  box-shadow:inset 0 0 80px #000;
}


header nav{
  color:#fff;
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:10px 100px;
  position:fixed;
  top:0;
  left:0;
  z-index:99;
  width:100%;
  
}


header nav .fa{
  cursor:pointer;
  display:none;
}


header nav .fa:hover{
  color:#DB0000;
}

.nav{
  background:#fff;
  color:#000;
  transition:background .5s;
  
}

.navBlack{
  background:#000;
  color:#fff;
  transition:background .5s;
  
}

header nav .logo{
  font-weight:700;
  font-size:1.5em;
}

header nav .logo span{
  color:#DB0000;
}

header nav ul .marker{
  background:#DB0000;
  width:40px;
  height:2px;
  position:absolute;
  bottom:-5px;
  left:0;
  border-radius:20px;
}


header nav ul{
  display:flex;
  justify-content:center;
  align-items:center;
  gap:20px;
  text-transform:uppercase;
  list-style:none;
  position:relative;
}


header nav ul li{
  cursor:pointer;
}


header nav ul li span{
  display:flex;
  gap:8px;
  align-items:center;
}

header nav ul li img{
  width:35px;
  height:35px;
  border-radius:50%;
  object-fit:cover;
}


header .popular-movie-slider{
  color:#fff;
  display:flex;
  justify-content:center;
  align-items:start;
  gap:35px;
  padding:10px 100px;
  position:relative;
}

header .popular-movie-slider .poster{
  width:300px;
  height:400px;
  object-fit:cover;
  border-radius:10px;
}


header .popular-movie-slider .popular-movie-slider-content{
  line-height:25px;
}

header .popular-movie-slider .popular-movie-slider-content .movie-name{
  font-size:1.8em;
}


header .popular-movie-slider .popular-movie-slider-content .category{
  display:flex;
  gap:30px;
  text-transform:capitalize;
  margin:10px 0;
}

header .popular-movie-slider .popular-movie-slider-content .desc{
  font-size:.90em;
}

header .popular-movie-slider .popular-movie-slider-content .movie-info{
  display:flex;
  gap:30px;
  margin:25px 0;
}

header .popular-movie-slider .popular-movie-slider-content .movie-info .fa-circle{
  color:#DB0000; 
  font-size:.85em;
}

header .popular-movie-slider .popular-movie-slider-content .movie-info .fa span{
  font-family: 'Roboto', sans-serif;
  color:#fff;
  font-size:15px;
}


header .popular-movie-slider .popular-movie-slider-content .movie-btns{
  display:flex;
  gap:10px;
}


header .popular-movie-slider .popular-movie-slider-content .movie-btns button{
  width:200px;
  border:none;
  outline:none;
  padding:15px 0;
  border-radius:100px;
  font-size:1em;
  background:#DB0000;
  color:#fff;
  cursor:pointer;
  display:flex;
  justify-content:center;
  align-items:center;
  gap:2px;
}

header .popular-movie-slider .popular-movie-slider-content .movie-btns button .fa{
  font-size:.65em;
  color:#fff;
}


header .popular-movie-slider .popular-movie-slider-content .movie-btns .read-more{
  background:none;
}

header .popular-movie-slider .popular-movie-slider-content .movie-btns .read-more:hover{
  background:#000;
}

header .popular-movie-slider .popular-movie-slider-content .movie-btns button:hover{
  background:#000;
}


section .movie-ticket-book{
  background:#222;
  color:#fff;
  display:grid;
  grid-template-columns: auto auto auto;
  justify-content:space-between;
  align-items:center;
  padding:30px 100px;
}


section .movie-ticket-book .choose-date ,
section .movie-ticket-book .choose-time {
  position:relative
}

section .movie-ticket-book .choose-date .heading,
section .movie-ticket-book .choose-time .heading{
  text-transform:uppercase;
  font-weight:400;
}

section .movie-ticket-book .choose-date .wrapper,
section .movie-ticket-book .choose-time .wrapper{
  width: 300px;
}
section .movie-ticket-book .choose-date .carousel,
section .movie-ticket-book .choose-time .carousel{
  margin-top:15px;
  display:flex;
  justify-content:center;
  align-item:center;
  padding:10px 0;
}

section .movie-ticket-book .choose-date .carousel .owl-nav,
section .movie-ticket-book .choose-time .carousel
.owl-nav{
  color:#DB0000;
  position:absolute;
  width:99%;
  top:50%;
  left:-15px;
  transform:translate(0,-50%);
  font-size:1.5em;
  display:flex;
  justify-content:space-between;
}

.card{
  max-width: 350px !important;
}
section .movie-ticket-book .choose-date .carousel .card p:nth-child(1),
section .movie-ticket-book .choose-time .carousel .card p:nth-child(1){
  color:#888;
  font-size:.75em;
}


section .movie-ticket-book .choose-date .carousel .card p:nth-child(2),
section .movie-ticket-book .choose-time .carousel .card p:nth-child(2){
  color:#fff;
  font-size:1.2em;
  text-transform:uppercase;
}


section .movie-ticket-book .choose-date .wrapper .marker,
section .movie-ticket-book .choose-time .wrapper .marker{
  width:60px;
  height:2px;
  background:#DB0000;
  position:absolute;
  left:45%;
  transform:translate(-50%,0);
}



section .movie-ticket-book button{
  border:none;
  outline:none;
  height:50px;
  background:#DB0000;
  color:#fff;
  border-radius:50px;
  padding:10px 50px;
  cursor:pointer;
}

section .movie-ticket-book button:hover{
  background:#000;
}




section .filter-search-box{
  background:#111;
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:20px 100px;
  padding-top:50px;
  position:relative;
}


section .filter-search-box .filters-box{
  display:flex;
  gap:50px;
  color:#fff;
}



section .filter-search-box .filters-box .filters{
  cursor:pointer;
}


section .filter-search-box .search-bar{
  position:absolute;
  bottom:0;
  left:100px;
  width:83%;
  height:2px;
  background:#fff;
  border-radius:50px;
  overflow:hidden;
}

section .filter-search-box .search-bar .bar{
  width:10%;
  height:inherit;
  background:#DB0000;
}


section .filter-search-box .search-filters{
  color:#fff;
}

section .filter-search-box .search-filters input{
  border:none;
  outline:none;
  width:200px;
  padding:5px 10px;
  background:none;
  color:#fff;
  font-family: 'Roboto', sans-serif;
  font-size:1em;

}




section .movie-card-section{
  background:#111;
  padding:20px 100px;
  width:100%;
  display: grid;
  grid-template-columns:auto auto auto auto;
  gap:18px;
  padding-top:50px;
}


section .movie-card-section .card{
  margin-bottom:20px;
  color:#fff;
  text-transform:uppercase;
}

section .movie-card-section .card img{
  width:100%;
  height:350px;
  object-fit:cover;
  border-radius:10px;
  box-shadow:0 0 30px #000;
}

.card-content{
  background-color:  #111 !important;
}
section .movie-card-section .card .card-content .movie-name{
  font-weight:700;
  margin-top:10px;
}

section .movie-card-section .card .card-content .movie-info{
  font-size:.95em;
  margin-top:10px;
}


section .movie-card-section .card .card-content .movie-info span{
  color:#666;
}

section .movie-card-section .card .card-content .movie-info .d3{
  background:#DB0000;
  border-radius:50%;
  font-size:.50em;
  color:#fff;
  padding:3px;
  margin-left:3px;
}


section .show{
  display:flex;
  justify-content:space-between;
  align-items:center;
  gap:20px;
  width:100%;
  background:#111;
  padding:10px 100px;
  padding-bottom:40px;
}


section .show .show-bar{
  background:#fff;
  width:100%;
  height:3px;
  border-radius:50px;
  overflow:hidden;
}

section .show .show-bar .bar{
  width:10%;
  height:inherit;
  background:#DB0000;
  border-radius:50px;
}


section  .show button{
  border:none;
  outline:none;
  width:250px;
  padding:15px 0;
  font-size:1em;
  border-radius:50px;
  background:#DB0000;
  color:#fff;
  cursor:pointer;
}


section  .show button:hover{
  background:#000;
}



/*---footer------*/
footer{
  background:#222;
  color:#fff;
  padding:40px 100px;
  display: grid;
  grid-template-columns:auto auto auto;
  align-items:center;
}


footer .logo-box{
  font-size:.85em;
}


footer .logo-box .logo{
  font-size:1.8em;
  font-weight:700;
}

footer .logo-box .logo span{
    color:#DB0000;
}



footer ul {
  text-transform:uppercase;
  list-style:none;
  display:flex;
  justify-content:center;
  gap:20px;
}


footer ul li{
  cursor:pointer;
  color:#888;
}


footer ul li:nth-child(1){
  color:#fff;
}

footer ul li:hover{
  color:#fff;
}


footer .socail-box{
  display:flex;
  gap:10px;
  justify-content:end;
}

footer .socail-box .fa{
  border:2px solid #fff;
  border-radius:50%;
  width:40px;
  height:40px;
  display:flex;
  justify-content:center;
  align-items:center;
  cursor:pointer;
}

footer .socail-box .fa:hover{
  background:#fff;
  color:#DB0000;
}





@media (min-width: 1024px) and (max-width: 1080px) {
  
  section .movie-card-section{
  grid-template-columns:auto auto auto;
  gap:15px;
}
  
  
}





@media (min-width: 720px) and (max-width: 1023px) {
  
  header nav ul{
    display:none;
    position:absolute;
    right:0;
    top:40px;
    color:#fff;
    background:#000;
    width:100%;
    height:100vh;
    padding:20px 30px;
  }
  
  header nav .fa{
  cursor:pointer;
  display:block;
}
  
  
header .popular-movie-slider{
  padding:10px 50px;
}

header .popular-movie-slider .poster{
  width:250px;
  height:300px;
}
  


section .movie-ticket-book{
  grid-template-columns: auto;
  justify-content:center;
  align-items:center;
  padding:30px 50px;
}
  
  

  
  
section .movie-ticket-book .choose-date .wrapper,
section .movie-ticket-book .choose-time .wrapper{
  width: 30%;
  position:relative;
  left:50%;
  transform:translate(-50%,0);
}
section .movie-ticket-book .choose-date .carousel,
section .movie-ticket-book .choose-time .carousel{
  margin-top:15px;
  display:flex;
  justify-content:center;
  align-item:center;
  padding:10px 0;
}

section .movie-ticket-book .choose-date .carousel .owl-nav,
section .movie-ticket-book .choose-time .carousel
.owl-nav{
  color:#DB0000;
  position:absolute;
  width:110%;
  top:50%;
  left:-50%;
  transform:translate(0%,-50%);
  font-size:1.5em;
  display:flex;
  justify-content:space-between;
}



section .movie-ticket-book .choose-date .wrapper .marker,
section .movie-ticket-book .choose-time .wrapper .marker{
  width:60px;
  height:2px;
  background:#DB0000;
  position:absolute;
  left:45%;
  transform:translate(-50%,0);
}



section .movie-ticket-book button{
  width:20%;
  margin-top:20px;
  position:relative;
  left:50%;
  transform:translate(-50%,0);
}

  
 section .filter-search-box{
  padding:20px 50px;
}


section .filter-search-box .filters-box{
  gap:10px;
}
  
  
  
 
section .filter-search-box .search-bar{
  left:50px;
  width:85%;
}

 
section .movie-card-section{
  padding:20px 50px;
  grid-template-columns:auto auto auto;
}
  

section .movie-card-section .card img{
  width:100%;
  height:300px;
}


 
section .show{
  padding:10px 50px;
  padding-bottom:40px;
}
  
  
  
footer{
  padding:40px 50px;
  grid-template-columns:auto;
  justify-content:center;
  align-items:center;
  gap:30px;
}
  
  
}



@media (min-width: 0px) and (max-width: 719px) {
  
  
  header {
    height:auto;
    padding-bottom:30px;
  }
  header nav{
    padding:10px 20px;  
  }
  
  
  header nav ul{
    display:none;
    position:absolute;
    right:0;
    top:40px;
    color:#fff;
    background:#000;
    width:100%;
    height:100vh;
    padding:20px 30px;
  }
  
  header nav .fa{
  cursor:pointer;
  display:block;
}
  
  
header .popular-movie-slider{
  padding:10px 20px;
  display:block;
}

header .popular-movie-slider .poster{
  width:100%;
  height:300px;
  margin-bottom:20px;
}
  


section .movie-ticket-book{
  grid-template-columns: auto;
  justify-content:center;
  align-items:center;
  padding:30px 20px;
}
  
  

  

  
 section .filter-search-box{
  padding:20px 20px;
}


section .filter-search-box .filters-box{
  gap:10px;
}
  
  
  
 
section .filter-search-box .search-bar{
  left:20px;
  width:90%;
}

 
section .movie-card-section{
  padding:20px 20px;
  grid-template-columns:auto auto;
}
  

section .movie-card-section .card img{
  width:100%;
  height:300px;
}


 
section .show{
  padding:10px 20px;
  padding-bottom:40px;
}
  
  
  
footer{
  padding:40px 20px;
  grid-template-columns:auto;
  justify-content:center;
  align-items:center;
  gap:30px;
  font-size:.85em;
}
  
  
 section .filter-search-box{
  gap:20px;
  overflow-x:scroll;
}
  
  
 section .filter-search-box::-webkit-scrollbar {
  display: none;
}
  
  
section .filter-search-box .search-filters input{
  width:50px;
  padding:5px 10px;
}

  
  section .movie-ticket-book{
    padding:30px 0px;
  }
  
section .movie-ticket-book button{
 margin-top:10px;
}
  
}
</style>
