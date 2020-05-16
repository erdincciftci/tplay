<template>
  <div>
    <div class="header-container" :style="{ background: headerbgString1 + colPalette[0] + headerbgString2 + colPalette[2] + headerbgString3 }">

      <header class="the-header">
        <div class="logo">
          <div
            class="post-thumbnail"
            :style="{ backgroundImage: 'url(' + loadedPost.thumbnail + ')' }"
          ></div>
          <div class='song'>   
            {{ loadedPost.title }}
       
             </div>
           <div class='artist'> by {{ loadedPost.author }} </div>
      
          
     
        </div>
          
        <div class="spacer"></div>
        <div class="navigation-items">
          <ul class="nav-list">
            <li class="nav-item"><nuxt-link to="/posts">Back to Songs</nuxt-link></li>
          </ul>
        </div>
      </header>
    </div>
    <div class="single-post-page"  :style="{ background: bodybgString1 + colPalette[2] + bodybgString2}">
      <section class="post">
      
   
        <ul class="list-unstyled">
          <li
            v-for="(item, index) in exampleData"
            v-bind:key="index"
            v-bind:class="[index % 2 == 0 ? 'listRed' : 'listBlue']"
          >
            {{ item }}
          </li>
        </ul>
      </section>
  
      <div class="container border">
        <div class="d-flex justify-content-center" id="p5Canvas"></div>
        <div class="message d-flex justify-content-center">
          {{ message }}
        </div>
            <section class="post-feedback">
        <p>
            <div>Last updated on {{ loadedPost.updatedDate | date }}</div>
        </p>
      </section>
      </div>
    </div>
  </div>
</template>

<script>
import * as Vibrant from "node-vibrant";

if (process.browser) {
  var radar = require("@/js/Radar.js");
}

export default {
  layout: "listening",
  asyncData(context) {
    if (context.payload) {
      return {
        loadedPost: context.payload.postData
      };
    }
    return context.app.$axios
      .$get("/posts/" + context.params.id + ".json")
      .then(data => {
        return {
          loadedPost: data,
          myLines: data.content.split(/\r\n|\r|\n/),
          otherLines: data.previewText.split(/\r\n|\r|\n/),
          imageVib: data.thumbnail
        };
      })
      .catch(e => context.error(e));
  },
  data() {
    return {
      message: "",
      colPalette: [],
      bgHeader1: '#DE1315',
      bgHeader2: '#641116',
       angle: '180',

    };
  },

  head: {
    title: "A Single Post"
  },
  // mounted() {
  //   const P5 = require("p5");
  //   new P5(radar.main);
  //   // NOTE: p5.jsからのコールバックを受け取る
  //   radar.setDelegate(this.callbackOnP5);
  // },

  mounted: function() {
    var imageSrc = this.imageVib;
    let savedColors = this.colPalette;
    Vibrant.from(imageSrc)
      .getPalette()
      .then(palette => {
        savedColors.push(palette.Vibrant.hex);
        console.log(savedColors[0]);
        savedColors.push(palette.Muted.hex);
        console.log(savedColors[1]);
        savedColors.push(palette.DarkVibrant.hex);
        console.log(savedColors[2]);
        savedColors.push(palette.DarkMuted.hex);
        console.log(savedColors[3]);
        savedColors.push(palette.LightVibrant.hex);
        console.log(savedColors[4]);
        savedColors.push(palette.LightMuted.hex);
        console.log(savedColors[5]);
      });
  },

  methods: {
    callbackOnP5: function(timeStr) {
      this.message = timeStr;
    }
  },

  computed: {
    exampleData: function() {
      let matchedLyrics = [];
      for (let i = 0; i < this.myLines.length; i++) {
        matchedLyrics.push(this.myLines[i]);
        try {
          matchedLyrics.push(this.otherLines[i]);
        } catch (e) {
          matchedLyrics.add("-");
        }
      }
      return matchedLyrics;
    },
    saveColors: function() {
      return this.savedColors;
    },
    headerbgString1() {
      return `linear-gradient(180deg, `;
    },
    headerbgString2() {
      return ` 0%, `;
    },
    headerbgString3() {
      return ` 100%)`;
    },
    bodybgString1() {
      return `linear-gradient(180deg, `;
    },
    bodybgString2() {
      return ` 0%, #212738 19%, #212738 100%)`;
    },
  
    // background: linear-gradient(180deg,#2e1961 0%, #212738 19%, #212738 100%);
    //return `linear-gradient(180deg, #DE1315 0%, #641116 100%)`;
  }
};
</script>

<style scoped>
.single-post-page {
  padding: 30px;
  
  text-align: center;
  box-sizing: border-box;
}

.post {
  width: 100%;
}

@media (min-width: 768px) {
  .post {
    width: 600px;
    margin: auto;
  }
}

.post-title {
  margin: 0;
}

.post-details {
  padding: 10px;
  box-sizing: border-box;
  border-bottom: 3px solid #ccc;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

@media (min-width: 768px) {
  .post-details {
    flex-direction: row;
  }
}

.post-detail {
  color: rgb(88, 88, 88);
  margin: 0 10px;
}
.post-feedback {
  color: white;
  text-decoration: none;
}
.post-feedback a {
  color: red;
  text-decoration: none;
}

.post-feedback a:hover,
.post-feedback a:active {
  color: salmon;
}

.pre-formatted {
  white-space: pre;
}

.listRed {
  font-family: Metropolis;
  font-style: normal;
  font-weight: bold;
  font-size: 32px;
  line-height: 40px;
  /* or 119% */
  color: #ffffff;
}

.listBlue {
  font-family: Metropolis;
  font-style: normal;
  font-weight: normal;
  font-size: 24px;
  line-height: 28px;
  padding-bottom: 32px;
  /* or 137% */
  color: #ff6b6b;
}


/* here starts the header code

 background: #DE1315;
  background: linear-gradient(180deg,#DE1315 0%,#641116 100%);
 */
 .post-thumbnail {
  width: 60px;
  height: 60px;
  background-position: center;
  background-size: cover;
}

 .song{
   font-family: Metropolis;
font-style: normal;
font-weight: 600;
font-size: 15px;
line-height: 20px;
margin-top: 8px;
/* or 133% */

display: flex;
align-items: center;

color: #FFFBF2;
   
 }
 .artist {
   font-family: Metropolis;
font-style: normal;
font-weight: 300;
font-size: 15px;
line-height: 20px;
/* or 133% */
color: rgba(255, 255, 255, 0.8);
 }

.header-container {
 
  height: 120px;
}

.the-header {
  width: 100%;
  position: fixed;
  display: flex;
  justify-content: space-around;
  align-items: center;
  z-index: 100;
  box-sizing: border-box;
  padding: 16px;
}

.logo {
  margin: 0 10px;
  font-size: 1.3rem;
}

.logo a {
  text-decoration: none;
  color: white;
}

.spacer {
  flex: 1;
}

.navigation-items {
  display: none;
  float: right;
}

@media (min-width: 768px) {
  .navigation-items {
    display: block;
  }
}

.nav-list {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
}

.nav-item {
  margin: 0 10px;
}

.nav-item a {
  text-decoration: none;
  color: white;
}

.nav-item a:hover,
.nav-item a:active,
.nav-item a.nuxt-link-active {
  color: red;
}
</style>
