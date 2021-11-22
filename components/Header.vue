<template>
  <div>
    <header>
      <Slide right>
        <NuxtLink to="/"><p>Accueil</p></NuxtLink>

        <NuxtLink to="/interactive-map"><p>Carte Interactive</p></NuxtLink>
      </Slide>
      <div class="container">
        <div class="row text-end">
          <div class="col-12"></div>
        </div>
      </div>
    </header>
  </div>
</template>

<script>
import { Slide } from "vue-burger-menu";
export default {
   head() {
    return {
      title: "Voyage en Italie - Carte interactive",
      meta: [
        {
          name: "description",
          content:
            "Carte interactive de l'Italie pour accéder à tous les projets !",
          hid: "description",
        },
      ],
    };
  },
  components: {
    Slide,
  },
};
</script>

<style lang="scss">
#__layout {
  position: relative;
}
header {
  padding-top: 1rem;
  z-index: 10;
  position: absolute;
  width: 100%;
  left: 0;
  top: 0;
  .bm-menu {
    background-color: #d49566;
  }
  a {
    text-decoration: none;
  }
  p {
    color: white;
  }
  ul {
    list-style: none;
    padding: 0;
    display: inline-flex;
    li:first-child {
      margin-right: 1rem;
    }
  }
}
</style>