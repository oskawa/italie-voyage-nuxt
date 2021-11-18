<template>
  <div id="directaccess">
    <div class="container">
      <div class="row text-center">
        <div class="col-12">
          <h2>Accès direct <br />aux destinations</h2>
          <div id="destinations">
            <NuxtLink
              v-for="dest in data"
              :key="dest.id"
              :to="`/destinations/${dest.Identifiant}`"
              class="solo"
              v-bind:style="{
                'background-image':
                  'linear-gradient(black, black),url(' +
                  dest.imagePrincipale.formats.large.url +
                  ')',
              }"
            >
              <div class="layoutDestination">
                <h3 class="titre_destination">{{ dest.Titre }}</h3>
              </div>
            </NuxtLink>
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      data: [],
    };
  },
  mounted() {
    this.fetchDataCategory();
    document.body.style.overflow = "initial";
  },
  methods: {
    fetchDataCategory() {
      axios
        .get(`https://back-italie.herokuapp.com/destinations/`, {
          params: {
            _limit: 5,
          },
        })
        .then((response) => {
          this.data = response.data;
          console.log(this.data);
          console.log(this.data.imagePrincipale);
        });
    },
  },
};
</script>

<style lang="scss">
#directaccess {
  background-color: #1a1717;
  padding-bottom: 3rem;
  position: relative;
  a {
    text-decoration: none;
  }

  h2 {
    color: white;
    margin-bottom: 60px;
  }
  &:hover h3 {
  }
  #destinations {
    display: inline-flex;
    width: 100%;
    justify-content: space-between;
    .solo {
      width: 240px;
      height: 364px;
      background-size: cover;
      background-position: center;
      border-radius: 5px;
      position: relative;
      background-blend-mode: saturation;
      &:hover {
        background-blend-mode: exclusion;
      }
      .layoutDestination {
        position: absolute;
        width:100%;
        top: -5rem;
        height: 100%;
        visibility: hidden;
        transform:translateY(-100%);
         transition: transform 330ms ease-in-out;
         background-color: rgba(0,0,0,0.5);
      }
      &:hover .layoutDestination {
        top: 0;
        visibility: visible;
        transform:translateY(0)
      }
      h3 {
        font-size: 2rem;
        margin-top: 2rem;
        color: white;
      }
    }
  }
}
</style>