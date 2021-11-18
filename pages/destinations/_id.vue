<template>
  <div id="destination">
    
    <div
      id="hero-destination"
      v-bind:style="{
        'background-image': 'url(' + projet.imagePrincipal + ')',
      }"
    >
      <div class="container">
        <div class="row">
          <div class="col-12 col-lg-4">
            <h1 class="text-center">{{ projet.titre }}</h1>
            <p class="text-end">{{ projet.date }}</p>
          </div>
        </div>
      </div>
    </div>
    <div id="texte">
      <div class="container">
        <div class="row">
          <div class="col-12 col-lg-10">
            <p v-html="projet.descriptif"></p>
          </div>
        </div>
      </div>
    </div>
   
 <CoolLightBox  :items="images" :index="indexun" :effect="'fade'" @close="index = null">
    </CoolLightBox>
    
    
    <div id="gallery">
     

      <div class="container">
        <div class="row">
          <div class="col-12">
            <div class="viewer">
              <div class="grid-sizer"></div>
              <div
                v-for="(image, indexsu) in projet.galery"
                :data-index="indexsu"
                :key="indexsu"
                :class="`box-${indexsu}`"
                class="item"
                @click="indexun = indexsu"
              >
                <img :src="`${image.url}`" />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CoolLightBox from "vue-cool-lightbox";
import "vue-cool-lightbox/dist/vue-cool-lightbox.min.css";

import axios from "axios";
if (process.browser) {
}
import Masonry from "masonry-layout";
import ImagesLoaded from "imagesloaded";
export default {
  components: {
    CoolLightBox,
  },
  data() {
    return {
      images: [],
      indexun: null,
      selector: ".viewer",
      options: {
        itemSelector: ".item",
        columnWidth: ".grid-sizer",
        percentPosition: true,
        gutter: 0,

        fitWidth: true,
      },
      id: this.$route.params.id,
      projet: {
        titre: "",
        date: "",
        descriptif: "",
        galery: null,
        imagePrincipal: null,
      },
    };
  },
  //   watch: {
  //     data() {
  //    this.$nextTick(() => this.loaded());
  //     },
  //   },
  mounted() {
    axios
      .get(`https://back-italie.herokuapp.com/destinations/${this.id}`)
      .then((response) => {
        this.projet = {
          titre: response.data.Titre,
          date: response.data.Date,
          descriptif: response.data.Descriptif,
          galery: response.data.Gallerie,
          imagePrincipal: response.data.imagePrincipale.formats.large.url,
        };
        this.projet.galery.forEach(element => {
          this.images.push(element.url);          
        });
        
        console.log(this.images);
        setTimeout(() => {
          this.loaded();
        }, 1.5);
        // this.loaded();
      });
  },
  methods: {
    async loaded() {
      await ImagesLoaded(this.selector, () => {
        this.$emit("masonry-images-loaded");
        let masonry = new Masonry(this.selector, this.options);
        this.$emit("masonry-loaded", masonry);
      });
     
    },
  },
};
</script>

<style lang="scss" scoped>
.viewer {
  width: 100% !important;
}

img {
  width: 100%;
  border-radius: 1%;
}
.box-0 {
  width: 50%;
}
#hero-destination {
  height: 610px;
  background-repeat: no-repeat;
  background-size: cover;
  position: relative;
  background-position: bottom;
  &:before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: -1px;
    background: linear-gradient(180deg, #1a1717 0%, rgba(0, 0, 0, 0.08) 55.73%);
    transform: rotate(180deg);
    z-index: 1;
  }

  .container {
    height: 100%;
    .row {
      height: 100%;
      align-items: center;
      justify-content: center;
    }
  }
  h1 {
    color: #f9f8f8;

    font-size: 100px;
    line-height: 121.4%;

    color: #c69761;

    transform: rotate(-6.36deg);
  }
  p {
    color: #f9f8f8;
  }
}
#texte {
  background-color: #1a1717;
  display: flex;
  margin-top: -3rem;
  z-index: 100;
  .container {
    z-index: 10;
  }

  p {
    color: #f9f8f8;

    font-size: 15px;
    line-height: 121.4%;
    /* or 18px */
    text-align: justify;
  }
  .row {
    justify-content: center;
  }
}

#gallery {
  padding-top: 3rem;
  background-color: #1a1717;
}
@media only screen and (max-width: 768px) {
  .grid-sizer {
    width: 100%;
  }
  .item {
    width: 100%;
    padding-bottom: 10px;
    padding-left: 0px;
  }
  .box-0 {
    width: 100%;
  }
}
@media only screen and (min-width: 769px) {
  .grid-sizer {
    width: 33%;
  }
  .item {
    width: 33%;
    padding-bottom: 10px;
    padding-left: 10px;
  }
  .box-0 {
    width: 66%;
  }
}
@media only screen and (min-width: 1200px) {
  .grid-sizer {
    width: 25%;
  }
  .item {
    width: 25%;
    padding-bottom: 10px;
    padding-left: 10px;
  }
  .box-0 {
    width: 50%;
  }
}
</style>