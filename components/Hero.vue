<template>
  <div id="hero">
    <div class="container h-100">
      <div
        class="row h-100 align-items-center text-center justify-content-center"
      >
        <div class="col-12 col-lg-8">
          <h1>Un bel viaggio in tutta Italia</h1>
         <NuxtLink to="/interactive-map"><button>Découvrez les destinations</button></NuxtLink>
        
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {};
</script>

<style lang="scss">
#hero {
  height: 100vh;
  position: relative;
  background-image: url("/img/hero.png");
  background-repeat: no-repeat;
  background-position: center;
  background-size: cover;
  z-index: 1;
  &:after {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: linear-gradient(180deg, #1a1717 0%, rgba(0, 0, 0, 0.08) 55.73%);
    transform: rotate(180deg);
    z-index: -1;
  }
  h1 {
    margin-bottom: 120px;
    z-index: 5;
    font-size: 96px;
    line-height: 93.4%;
    color: #ffffff;
    transform: rotate(-0.43deg);
  }

  button {
    background: none;
    border: none;
    font-family: Permanent Marker;
    font-style: normal;
    font-weight: normal;
    color: #ffffff;
    position: relative;
    z-index: 9;
    &:before {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      background-image: url("/img/brush.svg");
      z-index: -1;
      background-repeat: no-repeat;
      background-size: 0 100%;
      transition: background-size 0.6s 0s;
    }
    &:hover {
      &:before {
        right: 0;
        bottom: 0;
        background-size: 40% 100%;
        left: 0;
      }
    }
  }
}
</style>