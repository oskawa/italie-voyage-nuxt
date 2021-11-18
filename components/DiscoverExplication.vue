<template>
  <div id="explication">
    <div class="container">
      <div class="row">
        <div class="col-12 col-lg-5">
          <h2>Découvrez notre voyage</h2>
       
         <p v-html="data.texteDePresentation"></p>
        </div>
        <div class="offset-md-1 col-12 col-lg-6 test">
          <img src="/img/discover.png" alt="" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  data(){
    return{
      data:{}
    }
  },
  mounted(){
    axios
    .get('https://back-italie.herokuapp.com/index')
    .then((response) => {
          this.data = response.data;
        
         
        });
          console.log(this.data)
  },
};
</script>

<style lang="scss">
#explication {
  background-color: #1a1717;
  padding: 4.75rem 0;
  h2 {
    font-size: 30px;
    line-height: 121.4%;
    color: #ffffff;
  }
  p {

    font-size: 15px;
    line-height: 138.9%;
    /* or 21px */
    text-align: justify;

    color: #ffffff;
  }
  .test{
    position:relative;
    &:after{
      content:'';
      position: absolute;
      top:0;
      left: 0;
      right: 0;
      bottom: 0;
      background-image: url('/img/mask.svg');
      background-size: cover;
    }
  }
  
}
</style>